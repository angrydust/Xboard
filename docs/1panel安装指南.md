当然可以。以下是增强了步骤性的部署教程：

# 1panel 部署教程

本文将介绍如何使用 1panel 快速部署 Xboard。

## 安装部署

### 步骤 1：安装 1panel

1. 执行以下命令安装 1panel：

    ```
    curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh && sudo bash quick_start.sh
    ```

2. 安装完成后，登录 1panel 进行环境的安装。

### 步骤 2：安装应用

1. 打开应用商店，安装以下应用：

    - ☑️ OpenResty 任意版本 （<span style="color:yellow">安装时需要勾选 "端口外部访问" 来打开防火墙</span>>
    - ☑️ MySQL 5.7.\* （arm 架构可以选择 mariadb 进行代替）

    <span style="color:yellow">⚠️ ：安装过程中配置默认即可。</span>

### 步骤 3：添加站点

1. 在 1panel 面板中，选择“网站”并点击“创建网站”，然后选择“反向代理”。
2. 在 “主域名” 中填写你指向服务器的域名，
3. 在 “代号” 中填写 `xboard`
4. 在 “在代理地址” 中填写 `127.0.0.1:7001`，
5. 最后点击“创建”按钮。
6. 点击刚创建的网站的 "配置" > "反向代理" > "源文" 修改反向代理规则为以下内容：

    ```
    location ^~ / {
        proxy_pass http://127.0.0.1:7001;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Real-PORT $remote_port;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header Scheme $scheme;
        proxy_set_header Server-Protocol $server_protocol;
        proxy_set_header Server-Name $server_name;
        proxy_set_header Server-Addr $server_addr;
        proxy_set_header Server-Port $server_port;
        proxy_cache off;
    }
    ```

### 步骤 4：创建数据库

1. 在 1panel 面板中，选择“数据库”并点击“创建数据库”。
2. 在“名称”中填写 `xboard`。
3. 在“用户”中填写 `xboard`。
4. 在“权限”中选择“所有人(%)”。
5. 最后点击“创建”按钮。
6. 记住数据库账号密码进行下一步

### 步骤 5：安装 Xboard

1. 通过 SSH 登录到服务器后，访问站点路径如：`/opt/1panel/apps/openresty/openresty/www/sites/xboard/index`。
2. 如果系统没有安装 git，请执行以下命令安装 git：

    - Ubuntu/Debian：

        ```
        apt update
        apt install -y git
        ```

    - CentOS/RHEL：

        ```
        yum update
        yum install -y git
        ```

3. 在站点目录中执行以下命令从 Github 克隆到当前目录：

    ```
    git clone -b  docker-compose --depth 1 https://github.com/angrydust/Xboard ./
    ```

4. 执行以下命令安装 Xboard：

    ```
    docker compose run -it --rm xboard php artisan xboard:install
    ```

5. 根据提示输入上述创建的数据库账号密码，选择使用内置 redis 完成安装。  
   执行这条命令之后，会返回你的后台地址和管理员账号密码（你需要记录下来）。  
   你需要执行下面的“启动 Xboard”步骤之后才能访问后台。

### 步骤 6：启动 Xboard

在站点目录中执行以下命令：

```
docker compose up -d
```

🎉： 到这里，你已经可以通过域名访问你的站点了。

⚠️： 请务必开启防火墙防止7001端口暴露到公网当中。

## 更新

1. 通过 SSH 登录到服务器后，访问站点路径如：`/opt/1panel/apps/openresty/openresty/www/sites/xboard/index`，然后在站点目录中执行以下命令：

    ```
    docker compose down xboard
    docker compose pull 
    docker compose up -d
    ```

🎉： 在此，你已完成 Xboard 的更新。

## 注意

-   启用 webman 后做的任何代码修改都需要重启生效。
