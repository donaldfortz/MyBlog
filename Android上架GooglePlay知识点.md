# Choose singing key / 上传签名密钥

1. 首先点击 Choose singing key，会弹出以下弹窗：

   ![25-Play更改应用签名密钥-2-2](https://github.com/Jooezc/blog/assets/15683811/d0edafea-4336-4486-9c1f-b774e4e9d91b)

2. 选择 Use different singing key / 使用其他密钥，会弹出以下弹窗：

   ![25-Play更改应用签名密钥-3](https://github.com/Jooezc/blog/assets/15683811/10c54f1c-4109-40bf-b13d-5e99b4701035)

3. 按步骤下载私钥与jar工具，按Google 提供的命令行执行时有2个注意点：

   1. 命令行记得根据自己项目情况替换相应项
      ```
      /** 这是Google官方提供的命令行 */
      $ java -jar pepk.jar --keystore=foo.keystore --alias=foo --output=output.zip --include-cert --rsa-aes-encryption --encryption-key-path=/path/to/encryption_public_key.pem
      /** 这是我根据自己项目情况的修改 */
      $ java -jar pepk.jar --keystore=TG.jks --alias=key0 --output=output.zip --include-cert --rsa-aes-encryption --encryption-key-path=./encryption_public_key.pem
      ```
   2. 运行命令后还需要输入两次密码，分别是 key store password 与 key password

      ![image](https://github.com/Jooezc/blog/assets/15683811/4bb5a1d6-cc6b-4566-b40c-f6f8950a5350)

   3. 运行成功后运行路径下就会生产一个 output.zip ，这个就是我们要上传的密钥文件了
  
      ![image](https://github.com/Jooezc/blog/assets/15683811/bed09f1e-04e3-4442-8aba-e13c8d480d2d)


 4. 这里有个坑需要注意下：

    1. 如果你的 JDK 是在 [Oracle官网](https://www.oracle.com/java/technologies/downloads/) 下载的，会出现以下问题：
       
    ![image](https://github.com/Jooezc/blog/assets/15683811/2573a3f7-b85d-4f27-8f7b-a19a89c891e0)

    2. 查阅 [资料](https://stackoverflow.com/questions/76516045/cannot-find-any-provider-supporting-rsa-none-oaepwithsha1andmgf1padding-when-t) 发现，需要下载 [OpenJDK](https://jdk.java.net/21/) 才能正常导出 Google 所需要的密钥文件


