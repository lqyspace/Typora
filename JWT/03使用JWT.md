[toc]

## 1、引入依赖

```maven
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>4.4.0</version>
</dependency>
```

官网地址：https://github.com/auth0/java-jwt?tab=readme-ov-file

## 2、使用

```java
public class JWTTest {
    public static void main(String[] args) {
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.SECOND, 90);

        HashMap<String, Object> map = new HashMap<>();
        String token = JWT.create()
//                .withHeader(map) // header  这个可以不写
                .withClaim("userId", 21) // payload
                .withClaim("userName", "xiaochen")
                .withExpiresAt(calendar.getTime()) // 指定过期时间
                .sign(Algorithm.HMAC256("hdfauifgbafbaiufbeaibaiubuiabfia")); // 签名
        System.out.printf(token);
        test(token);
    }

    public static void test(String token){
        // 创建验证对象
        JWTVerifier verify = JWT.require(Algorithm.HMAC256("hdfauifgbafbaiufbeaibaiubuiabfia")).build();
        // 验证token
        DecodedJWT jwt = verify.verify(token);
        System.out.println(jwt.getClaim("userId").asInt());
        System.out.println(jwt.getClaim("userName").asString());
        System.out.printf("过期时间："+jwt.getExpiresAt());
    }
}
```

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIxLCJ1c2VyTmFtZSI6InhpYW9jaGVuIiwiZXhwIjoxNzMwNjMxMzM4fQ.8F5kGqTRDgxXITJ_8rGv6qgIaWxuo3m3ePl2jrN_nJ421
xiaochen
过期时间：Sun Nov 03 18:55:38 CST 2024
```

## 3、常见的异常信息

- SignatureVerificationException   签名不一致异常
- TokenExpiredException  令牌过期异常
- AlgorithmMismatchException  算法不匹配异常
- InvalidClaimException  失效的payload异常