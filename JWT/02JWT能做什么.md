[toc]

## 1、JWT的结构是什么

> 令牌组成

- 标头(header)
- 有效负载(payload)
- 签名(Signature)
- 因此，JWT的表示通常是：xxxx.yyyy.zzzz    Header.Payload.Signature

> Header

- 标头通常由两部分组成：令牌的类型（即JWT）和所使用的签名算法，例如HMAC SHA256和RSA。它会使用Base64编码组成JWT结构的第一部分
- 注意：Base64是一种编码，也就是说，它可以被翻译回原来的样子。它并不是一种加密过程。

```json
{
    "alg": "HS256",
    "typ": "JWT"
}
```

> Payload

- 令牌的第二部分是有效负载，其中包含声明。声明是有关实体（通常是用户）和其他数据的声明。同样的，它会使用Base64编码组成JWT的第二部分。

```json
{
    "sub": "1234567",
    "name": "John Doe",
    "admin": true
}
```

> 注意: payload中不要包含敏感信息

> Signature

- 前面两部份都是使用Base64进行编码的，即前端可以解开里面的信息。Signature需要使用编码后的header和payload以及我们提供的一个密钥，然后使用header中指定的签名算法（HS256）进行签名。签名的作用是保证JWT没有被篡改过。

  HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret);

> 签名的目的

最后一步签名的过程，实际上是对头部和负载的内容进行签名，防止内容被篡改。如果有人对头部以及负载的内容进行修改的话，在进行编码，最后加上之前的签名组合成新的JWT的话，那么服务器就会判断出新的头部和负载所形成的签名和JWT附带上的签名是不一样的。如果要对新的头部和负载进行签名，在不知道服务器加密时使用的密钥，那么得出来的签名就一定是不一样的。

> 信息安全问题

由于Base64编码是可逆的，因此在JWT中的负载不应该包含任何敏感的数据。

JWT还经常用于设计用户认证和授权系统，甚至实现Web的单点登录。

![image-20241103175507919](https://fastly.jsdelivr.net/gh/lqyspace/mypic@master/img1/202411031755020.png)