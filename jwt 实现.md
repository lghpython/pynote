## jwt 实现

jwt: json web token, 应用于用户登入验证

djangorestframework-jwt 本质就是调用pyjwt

应用场景: 前后端分离、app开发、微信小程序

优点: 不用在服务端保存token

### 1. jwt实现原理

#### jwt的三段初始数据

1. 第一段 HEADER: 字典形式保存算法和类型,如下

   ``` json
   {
     "alg": "HS256", 
     "typ": "JWT"
   }
   ```

2. 第二段 PAYLOAD: 字典保存用户数据(可反解不保存敏感信息)

   ```json
   {
     "id": "1234567890",
     "name": "John Doe",
     "exp": 1516239022
   }
   ```

   

3. 第三段SIGNATURE: 为前两段base64url加密密文+ 盐(可选添加的自定义字符串),

   

#### 三段拼接 

```
	HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload) + "." +
  salt
) secret base64 encoded
```

1. header生成密文: json转换字符串,base64url加密( 可解密)
2. payload生成密文: json转换字符串,base64url加密( 可解密)
3. signature生成密文: header密文 、payload密文、自定义字符串 以‘ . ’ 拼接; 以header声明的加密类型加密默认HS256加密(不可解密),再做base64 加密 
4. 将上面生成的三段密文,以 ‘ . ’拼接生成token

``` 
# jwt.io 生成密文 以自定义123456789为例
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.S2ZL7D-D3VeduQ44Cy2qLRFxHV43gRGSZtlfJ2MJ57g

```

### 2. 验证token流程

1. 分割token: 得到header_segment、payload_segment、crypto_segment

   ```python
   jwt_token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.qvR5IfFi55hETIq5tfvMDsslBCgtsQNceILdb2MVuV0"
   signing_input, crypto_segment = jwt_token.rsplit(b'.', 1)
   header_segment, payload_segment = signing_input.split(b'.', 1)
   
   ```

   

2. 对header_segment、payload_segment解密得到header、payload

3. 对第三段 base64url 解密得到signature,对合法性做校验

   - 从得到的header中得到加密算法(默认算法:HS256)
   - 点号拼接header_segment、payload_segment 、盐
   - 使用算法进行加密,与得到的signature密文做比较

### 3. 代码实现

- 安装

  ``` python
  pip3 install pyjwt
  ```

- 生成token实现

  ``` python
  
  import jwt
  import datetime
  from jwt import exceptions
  SALT = '3987t5kjkdua9p30^%^%@*#&*#43&*#DHF&*#*H845)'
  def generate_token():
      headers = {
          'typ': 'jwt',
          'alg': 'HS256'
      }
      payload = {
          'user_id': 1, # 自定义用户id
          'username': 'xxx', # 自定义用户名
          'exp': datetime.datetime.utcnow() + datetime.timedelta(minutes=5) # 超时时间
      }
      token = jwt.encode(payload=payload, key=SALT, algorithm="HS256", headers=headers).decode('utf-8')
      return token
  if __name__ == '__main__':
      token = generate_token()
      print(token)
  ```

- 校验token实现

  ```python
  
  import jwt
  import datetime
  from jwt import exceptions
  def get_payload(token):
      """
      根据token获取payload
      :param token:
      :return:
      """
      try:
          # 从token中获取payload【不校验合法性】
          # unverified_payload = jwt.decode(token, None, False)
          # print(unverified_payload)
          # 从token中获取payload【校验合法性】
          verified_payload = jwt.decode(token, SALT, True)
          return verified_payload
      except exceptions.ExpiredSignatureError:
          print('token已失效')
      except jwt.DecodeError:
          print('token认证失败')
      except jwt.InvalidTokenError:
          print('非法的token')
  if __name__ == '__main__':
      token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.qvR5IfFi55hETIq5tfvMDsslBCgtsQNceILdb2MVuV0"
      payload = get_payload(token)
  
  ```


### 4. 基于jwt和drf实现认证

> 使用到的模块与方法:
>
> - pyjwt库的jwt模块: 
>   - encode(payload=payload, key=salt, algorithm="HS256", headers=headers) 
>   - decode(token, salt,True)   # 后面两参数可选, 表示是否加盐
> - djangorestframework库的rest_framework模块:
>   - rest_framework.authentication.BaseAuthentication
>   - rest_framework.exceptions.AuthenticationFailed

#### 代码实现(views.py中调用生成token的方法)

```python
from api.utils import jwt_gen
from api.extensions.auth import JwtQueryParamsAuthentication

class ProLoginView(APIView):
    '''用户登入'''
    authentication_classes = []  # 优先级高于setting中的REST_FRAMEWORK

    def post(self, request, *args, **kwargs):
        usr = request.data.get('username')
        pwd = request.data.get('password')
        user_object = models.UserInfo.objects.filter(username=usr, password=pwd).first()
        if not user_object:
            return Response({'code': 1000, 'error': '用户名错误或密码错误'})

        payload = {
            'user_id': user_object.id,  # 自定义用户ID
            'username': user_object.username,  # 自定义用户名
        }
        token = jwt_gen.gen_token(payload)

        return Response({'code': 1003, 'data': token})


class ProOrderView(APIView):
    '''用户登入, 在setting中设置authentication_classes'''
    # authentication_classes = [JwtQueryParamsAuthentication, ]

    def get(self, request, *args, **kwargs):
        return Response("列表订单")
```

#### 验证token

> 新建extensions/auth.py 创建继承BaseAuthentication的类重写authenticate方法,验证token

```python

from rest_framework.authentication import BaseAuthentication
from rest_framework.exceptions import AuthenticationFailed
from jwt import exceptions
import jwt
from django.conf import settings


class JwtQueryParamsAuthentication (BaseAuthentication):

    def authenticate(self, request):
        token = request.query_params.get("token")
        salt = settings.SECRET_KEY
        payload = None
        try:
            payload = jwt.decode(token, salt, True)
        except exceptions.ExpiredSignatureError:
            raise AuthenticationFailed('token已失效')
        except jwt.DecodeError:
            raise AuthenticationFailed('token认证失败')
        except jwt.InvalidTokenError:
            raise AuthenticationFailed('非法的token')

        return (payload, token)
```

#### 生成token()

> 新建utils/jwt_gen.py文件 创建gen_token()方法调用 jwt.encode()方法生成token返回

```python
# 新建utils/jwt_gen.py文件 创建gen_token方法
import datetime
import jwt
from jwt_project.settings import SECRET_KEY


def gen_token(payload, timeout = 1):
    salt = SECRET_KEY

    headers = {
        'typ': 'jwt',
        'alg': 'HS256'
    }
    # 构造payload
    payload['exp']= datetime.datetime.utcnow() + datetime.timedelta(minutes=timeout)  # 超时时间

    token = jwt.encode(payload=payload, key=salt, algorithm=headers.alg, headers=headers).decode('utf-8')

    return token
```

#### setting.py中设置REST_FRAMEWORK默认token验证类

```python
REST_FRAMEWORK = {"authentication_classes":['api.extensions.auth.JwtQueryParamsAuthentication' ,]}
```

