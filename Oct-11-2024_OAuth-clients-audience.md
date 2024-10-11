> In OAuth, audiences refer to the specific resources or services that an OAuth client is authorized to access on behalf of a user or themselves (in the case of machine-to-machine authentication).

# 简介
audiences 定义 OAuth client 被许可访问的资源。假如有以下两个受保护 API：
```
https://api-server/v1/users
https://api-server/v1/orders
```
auth server（https://auth.example.com） 可以颁发以下 access token，其中，aud 字段定义许可访问的 API：
```
{
  "iss": "https://auth.example.com",
  "sub": "user123",
  "aud": "https://api-server/v1/users",
  "exp": 1670000000,
  "iat": 1660000000,
  "scope": "read write"
}
```
aud 可以定义为列表：
```
{
  "iss": "https://auth.example.com",
  "sub": "user123",
  "aud": [
      "https://api-server/v1/users",
      "https://api-server/v1/orders
  ],
  "exp": 1670000000,
  "iat": 1660000000,
  "scope": "read write"
}
```

一个简单的 API 请求流程为：
1. gateway 检查 access token 是否合法（使用token颁发时的secret），是否过期，是否已经吊销（通过黑名单机制）
2. API client 检查 token 的 aud 是否包含待访问 api。

# 为什么需要 aud？
保护资源安全（资源的最前端的 API）。通过 aud，auth server 可以根据配置，或者 auth request 的参数设置，颁发特定 aud 的 token。这样，即便 token 被不合理的使用，比如恶意脚本，也只能
访问 aud 定义范围的资源，而非全部。

