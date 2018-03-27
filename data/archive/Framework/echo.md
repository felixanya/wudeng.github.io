# echo


e = echo.New()

## Server
e.Run()

## Middleware

Logger server log
Recover reover from panics anywhere in the chain
CORSWithConfig

### [JWT](https://jwt.io/): json web token

JWT是JSON风格轻量级的授权和身份认证规范，可实现无状态、分布式的Web应用授权。很方便的解决跨域授权问题。
因为跨域无法共享cookie。

* Header
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
* Payload
  - registered: iss (issuer), exp (expiration time), sub (subject), aud (audience), nbf (not before), iat (issued at), jti (JWT ID)
  - public: [IANA JSON Web Token Registry](https://www.iana.org/assignments/jwt/jwt.xhtml)
  - private
```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
* Signature
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

三部分由点分开的字符串。

`Authorization: Bearer <token>`


Authorization : Basic base64(username:password)

SWT: Simple Web Token
SAML: Security Assertion Markup Language Tokens (XML)

HTTP认证：https://www.iana.org/assignments/http-authschemes/http-authschemes.xhtml

## json

struct Dog {
  Name string     `json:"name"`
  Type string     `json:"type"`
}

方法一：最快
dog = Dog{}
b, err := ioutil.ReadAll(c.Request().Body)
json.Unmarshal(b, &dog)

方法二：其次
dog = Dog{}
json.NewDecoder(c.Request().Body).Decode(&dog)

方法三：最慢
dog = Dog{}
c.Bind(&dog)


Group

## 请求参数

要注意区分Post和Get请求。表单数据一般是Post：
values := c.FormParams()
values.Get("id")
server_ids = values["server_ids[]"]
比如checkbox类型的数组数据需要用这种方式来获取。ajax传过来的时候自动带了[]后缀。


src/main/main.go

export GOPATH=`pwd`
go install main
./bin/main

* https://echo.labstack.com
* http://go-echo.org/
