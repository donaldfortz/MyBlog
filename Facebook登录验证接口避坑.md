# FB 登陆服务端验证

1. 前端在接入FB登陆时，从Facebook获取到token，传给服务端进行验证，服务端会调用以下接口：
   ```
   const debugResp = await axios.get<DebugTokenResponse>("https://graph.facebook.com/debug_token", {
        params: {
            access_token: accessToken,
            input_token: inputToken,
        },
    });
   ```
   1. 这里需要注意 input_token 为前端传过来的Facebook 登陆 token
   2. access_token 是通过命令行生成的：
      ```
      curl -X GET "https://graph.facebook.com/oauth/access_token
        ?client_id={your-app-id}
        &client_secret={your-app-secret}
        &grant_type=client_credentials"
      ```
   3. 如果命令行的方式无法跑通，可以用 postman 发请求：

      ![image](https://github.com/Jooezc/blog/assets/15683811/d3f6928d-33f3-4f92-a398-ede20baa5912)

   
# [参考文档](https://developers.facebook.com/docs/facebook-login/guides/access-tokens/#apptokens)
