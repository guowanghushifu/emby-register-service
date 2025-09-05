### 这个工具是我使用Google Gemini 2.5写出来的，只是能用的水平，方便分享自己的emby服务给朋友去注册
### 使用方法
1. 进入代码目录自己构建出docker镜像： `docker build -t emby-register-service .` 然后参考下面的示例编写docker-compose.yml。 如果你不想自己从代码构建，也可以用我构建好的镜像 guowanghushifu/emby-register-service
2. 找台安装了python的机器执行 `python3 -c "import secrets; print(secrets.token_hex(32) + '\n')"` 生成签名的密钥填到 FLASK_SECRET_KEY 环境变量
3. 修改docker-compose.yml中的其他环境变量
- PUBLIC_ACCESS_URL: 你这个docker的公网访问地址，如果非标准端口，一起写上，例如 https://your-domain.com:18080
- ADMIN_PASSWORD：你的管理员密码，不要用弱密码
- EMBY_SERVER_URL：EMBY服务器的地址，如果非标准端口，一起写上，例如 https://emby.your-domain.com:8920
- EMBY_API_KEY：和EMBY服务器交互的时候需要用到的API，去emby服务器管理面板里面申请
- COPY_FROM_USER_ID：从模板用户复制emby参数，这里填写他的ID，看如下说明

先去emby里面创建一个模板用户，注意不要给他管理权限!!!，把这个用户的各项参数配置好，例如他是否能看所有的库，是否能转码，是否能删除文件等；然后在控制台用户管理那里点这个用户，浏览器地址栏会显示这样的一串（举例）：https://emby.your-domain.com:8920/web/index.html#!/users/user?userId=a22935174ac24711aa54f84999999⁠ ，把userId= 这后面的这串代码a22935174ac24711aa54f84999999拷贝出来，写到 COPY_FROM_USR_ID 这个环境变量

4. 浏览器访问 PUBLIC_ACCESS_URL 填写管理员密码就可以创建token，分发给需要注册的用户了

### docker-compose.yml 示例
```
services:
    emby-register-service:
        ports:
            - 18080:5000
        volumes:
            - ./data:/app/data
        container_name: my-emby-register-app
        environment:
            - FLASK_SECRET_KEY=54a7a4e7d286d13dbf610f14677d11290dede4eb8f0f20f01f3b57b109530f8d
            - PUBLIC_ACCESS_URL=https://your-reg-domain.com
            - ADMIN_PASSWORD=your_admin_password
            - EMBY_SERVER_URL=https://emby.your-domain.com:8920
            - EMBY_API_KEY=your_api_key
            - COPY_FROM_USER_ID=your_template_user_id
        restart: unless-stopped
        image: emby-register-service
        # image: guowanghushifu/emby-register-service
```

### Token管理界面
![PixPin_2025-06-08_16-24-41.png](https://image.dooo.ng/c/2025/06/08/68454b033e0d3.webp)

### 用户注册界面
![PixPin_2025-06-08_16-25-49.png](https://image.dooo.ng/c/2025/06/08/68454b02d33ec.webp)

