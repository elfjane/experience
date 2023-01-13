# PHP BI & cron install

[TOC]

###### tags: `BI`, `Cron`

---
## BI install
### BI 環境
* 自製 framework
* php 8.0 
* nginx
### 安裝前端 BI 方法
1. 安裝 [docker](https://docs.docker.com/desktop/install/linux-install/)
2. 安裝 [docker-compose](https://docs.neos.io/cms/installation-development-setup/docker-and-docker-compose-setup)

3. 建立一個目錄
```bash
mkdir -p ~/docker_file/bloxmith_bi
```
4. git 環境目錄內有 bloxmith_bi_tools/docker_file/bloxmith_bi 檔案複製到 ~/docker_file/bloxmith_bi 目錄內，裡面檔案已經提供自動更新下載 git 檔案，更換參數，後面進行更參數即可。檔案結構說明：


| 檔案                         | 說明                                               |
| ---------------------------- | -------------------------------------------------- |
| docker-compose.yml           | docker-compose yml 設定檔                          |
| bloxmith.env                 | 自動佈屬需要的環境參數                             |
| default.conf                 | nginx 的設定參數                                   |
| entrypoint.sh                | docker-compose up 後會自動執行的程序               |
| php-build/Dockerfile         | Dockerfile自動佈屬環境 把需要的相關套件自動佈屬    |
| php-build/exclude_bmbi.list  | rsync 程序使用時，自動過應不必要的檔案以免曝露在外 |
| php-build/Smarty-2.6.28.tar.gz  | php 需要的 views 框架 |


5. docker-compose.yml 額外參數變更詳解

| 參數                  | 說明                                                                                           | 範例                                        |
| --------------------- | ---------------------------------------------------------------------------------------------- | ------------------------------------------- |
| BLOXMITH_API_PATH     | PHP API 程式碼目錄 (不需要更動)                                                                | /bloxmith_bi                                |
| BLOXMITH_GIT_URL      | git 網址                                                                                       | gitlab.com/pumpkinstudio/blockraidersserver |
| BLOXMITH_GIT_BRANCH   | branch 名稱 (不需要更動)                                                                       | BIAPI                                       |
| BLOXMITH_GIT_ACCOUNT  | token account 目前是使用智子的帳號產生的token                                                  | BLOXMITH_GIT_ACCOUNT                        |
| BLOXMITH_GIT_PASSWORD | token 產生方法請看 [此處](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html) | glpat-J9e5zd87dFVHDUpx_yGf                  |

6. bloxmith.env 參數主要在自動化安裝時，自動佈屬時，會使用的參數，參數說明：


| 參數                                          | 說明                                     | type                                                     | 範例                                        |
| --------------------------------------------- | ---------------------------------------- | -------------------------------------------------------- | ------------------------------------------- |
| BLOXMITH_CONFIG_MYSQL_PDO_HOST                | MySQL Host                               | str                                                      | 192.168.181.131                             |
| BLOXMITH_CONFIG_MYSQL_PDO_PORT                | MySQL Port                               | int                                                      | 3306                                        |
| BLOXMITH_CONFIG_MYSQL_PDO_DATABASE            | MySQL Database                           | string                                                   | bloxmith_bi                                 |
| BLOXMITH_CONFIG_MYSQL_PDO_USER                | MySQL User                               | string                                                   | bloxmith                                    |
| BLOXMITH_CONFIG_MYSQL_PDO_PASSWD              | MySQL Password                           | string                                                   | 1234                                        |
| BLOXMITH_CONFIG_MYSQL_TIME_ZONE               | MySQL Timezone                           | string                                                   | +0:00                                       |
| BLOXMITH_CONFIG_WEB_DEBUG_MODE                | 是否進行除錯                             | bool                                                     | true                                        |
| BLOXMITH_CONFIG_SQL_DEBUG_MODE                | 資料庫是否進行除錯                       | boot                                                     | true                                        |
| BLOXMITH_CONFIG_WEB_TIME_ZONE                 | 後台預設時區                             | string                                                   | UTC                                         |
| BLOXMITH_CONFIG_WEB_BASE_PATH                 | 程式路徑                                 | string                                                   | /var/www/web/bloxmith_bi                    |
| BLOXMITH_CONFIG_BLOXMITH_SSO_CLIENT_ID        | SSO client ID                            | string                                                   | php_Operation                               |
| BLOXMITH_CONFIG_BLOXMITH_SSO_CLIENT_SECRET    | SSO client secret                        | string                                                   | aabbccxxx                                   |
| BLOXMITH_CONFIG_BLOXMITH_SSO_SCOPES           | SSO SCOPES                               | string                                                   | openid profile email SSO                    |
| BLOXMITH_CONFIG_BLOXMITH_SSO_REDIRECT_URI     | SSO Redirect URI                         | http://bloxmith_bi.elfjane.com/admin/member/bloxmith_log |                                             |
| BLOXMITH_CONFIG_BLOXMITH_SSO_URL              | SSO URL                                  | string                                                   | https://sso.bloxmithdev.com/                |
| BLOXMITH_CONFIG_BLOXMITH_SSO_SEARCH_LIMIT     | SSO Search Limit                         | int                                                      | 20                                          |
| BLOXMITH_CONFIG_BLOXMITH_SSO_PHP_URL          | SSO PHP API URL                          | string                                                   | http://bloxmith_api.elfjane.com/            |
| BLOXMITH_CONFIG_BLOXMITH_SSO_JAVA_VERSION     | SSO JAVA API Version                     | string                                                   | 0.2.1                                       |
| BLOXMITH_CONFIG_BLOXMITH_SSO_JAVA_URL         | SSO JAVA API URL                         | string                                                   | https://raidersapi.bloxmithdev.com/         |
| BLOXMITH_CONFIG_WEB_IMG                       | img URL 切換網址，可以把圖檔導到不同 URL | string                                                   | /                                           |
| BLOXMITH_CONFIG_BLOXMITH_GAME_ANDROID_VERSION | 設定android game api 版本號              | string                                                   | 0.3.1                                       |
| BLOXMITH_CONFIG_BLOXMITH_GAME_ANDROID_URL     | android game api url                     | string                                                   | https://android.raidersapi.bloxmithdev.com/ |
| BLOXMITH_CONFIG_BLOXMITH_COUNT_TIMEZONE       | 統計時統計時區                           | string                                                   | 'Asia/Taipei'                               |

7. 修改 default 27 行 server_name 改為域名
主要為了防止被 SEO 搜尋到
```bash=
server {
    listen       80 default_server;
    server_name  _;

    if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})") {
        set $year $1;
        set $month $2;
        set $day $3;
    }
     
    error_log  /var/log/nginx/error/error.log;
    access_log /var/log/nginx/access/access-$year-$month-$day.log combined;

    root /var/www/web/html;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}

server {
    listen       80;
    server_name  elfjane.bloxmith.com;

    if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})") {
        set $year $1;
        set $month $2;
        set $day $3;
    }
     
    error_log  /var/log/nginx/error/error.log;
    access_log /var/log/nginx/access/access-$year-$month-$day.log combined;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    index index.php index.html index.htm;
    root /var/www/web/bloxmith_bi/web;
    #location / {
    #    root   /usr/share/nginx/html;
    #    index  index.html index.htm;
    #}

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }

    location /admin {
        rewrite ^/admin/(.+)/(.+) /index.php?mod=$1&cl=$2 last;
    }

    location /authentication/callback.html {
        rewrite ^/authentication/callback.html /index.php?mod=member&cl=bloxmith_login last;
    }
}

```
8. 啟動
```bash
# 啟動後會自動執行 entrypoint.sh 
docker-compose up -d
```

### 不停機更新方法
```bash
# 進入 docker 資料夾執行該指令
docker-compose exec php /entrypoint.sh
```

---
## Cron install
自動統計程式相關
### Cron 環境
* alpine 3.17
* shell 
* cron
### 安裝前端 Cron 方法
1. 安裝 [docker](https://docs.docker.com/desktop/install/linux-install/)
2. 安裝 [docker-compose](https://docs.neos.io/cms/installation-development-setup/docker-and-docker-compose-setup)

3. 建立一個目錄
```bash
mkdir -p ~/docker_file/bloxmith_cron
```
4. git 環境目錄內有 bloxmith_bi_tools/docker_file/bloxmith_cron 檔案複製到 ~/docker_file/bloxmith_cron 目錄內，裡面檔案已經提供自動更新下載 git 檔案，更換參數，後面進行更參數即可。檔案結構說明：


| 檔案               | 說明                                                |
| ------------------ | --------------------------------------------------- |
| docker-compose.yml | docker-compose yml 設定檔                           |
| crontab.txt        | crontab 排程檔                                      |
| Dockerfile         | 建置 Dockerfile 方法                                |
| entrypoint.sh      | docker-compose up 後會自動執行的程序                |
| script_5min.sh     | 自動統計程序，目前提供5分鐘活耀統計與每小時活耀統計 |

5. docker-compose.yml 額外參數變更詳解

| 參數              | 說明                     | 範例         |
| ----------------- | ------------------------ | ------------ |
| BLOXMITH_CRON_URL | 呼叫的 自動統計 URL 網址 | /bloxmith_bi |
6. 啟動
```bash
# 啟動後會自動執行 entrypoint.sh 
docker-compose up -d
```
