
部署方法
===========
## 環境
常見Linux環境均可部署；主要是依賴Python2.7（calibre依賴該版本）、Sqlite。這裡選擇 Ubuntu 16.04.5 LTS 進行部署

## 部署目錄
* /data/books/: 作為書庫目錄
* /data/release/www/calibre.talebook.org/: 作為代碼目錄

## 安裝依賴包
```
sudo apt-get install python2.7 calibre python-pip nginx-extras unzip supervisor sqlite3 git
sudo pip install social-auth-app-tornado social-auth-storage-sqlalchemy "tornado<6.0" Baidubaike jinja
```

## 部署代碼
注意：如果要修改訪問域名，可以不調整代碼目錄，只調整nginx中的配置即可。
各個目錄的配置項，都可以在配置文件```webserver/settings.py```找到，可以根據自己的需求進行調整。
```
mkdir -p /data/log/
mkdir -p /data/release/www/calibre.talebook.org/
mkdir -p /data/books/{library,extract,upload,convert,progress}
cd /data/release/www/calibre.talebook.org/
git clone https://github.com/talebook/calibre-webserver.git

```

創建基礎書庫和DB
===========

## 創建書庫
請事先準備30本書籍。
使用以下命令創建書庫：
```
calibredb add --library-path=/data/books/library/  -r  書籍目錄
```

或者可以從github下載talebook.org的書庫（非常非常大，會很慢）
```
git clone https://github.com/talebook/talebook-library.git /data/books/library
```

## 創建DB
執行以下命令，創建程序DB。
```
python /data/release/www/calibre.talebook.org/calibre-webserver/server.py --syncdb
```


配置Kindle推送功能
============
## 使用QQ郵箱推送
進入[QQ郵箱網址](http://service.mail.qq.com/cgi-bin/help?subtype=1&&no=1001256&&id=28), 申請SMTP賬號，用於給Kindle推送。

填寫到```webserver/settings.py```配置文件中下述字段裡：
```
'smtp_server'                      : "smtp.talebook.org",
'smtp_username'                    : "sender@talebook.org",
'smtp_password'                    : "password",
```

配置用戶登錄功能
=============
根據自己的訴求，可以配置為個人自用，或者允許網友使用社交網站賬號登錄。

## 配置自動登錄（個人自用）
如果只是用於個人書籍、不會提供互聯網服務的話，可以配置自動登錄，免去社交網站的用戶登錄。
在```webserver/settings.py```中找到 __auto_login__ 選項，將其設置為1。
```
'auto_login': 1
```

## 申請社交網站應用賬號（多用戶使用）
在配置文件```webserver/settings.py```中，可以看到有相關的配置信息：

### 允許微博登錄
進入[微博開發者網址](http://open.weibo.com/developers), 申請微博登錄服務賬號，填寫到配置中。
```
'SOCIAL_AUTH_WEIBO_KEY'            : '',
'SOCIAL_AUTH_WEIBO_SECRET'         : '',
```

### 允許QQ登錄
進入[QQ互聯登錄網址](https://connect.qq.com/), 申請QQ登錄服務賬號，填寫到配置中。
```
'SOCIAL_AUTH_QQ_KEY'               : '',
'SOCIAL_AUTH_QQ_SECRET'            : '',
```


啟動服務
=============
## 配置supervisord
如果前面過程中，修改過代碼目錄路徑，那麼將 ``conf/supervisor/calibre-webserver.conf`` 中的路徑調整一下，放到 ``/etc/supervisor/conf.d/`` 中。

啟動命令如下：
```
sudo supervisorctl reload all
sudo supervisorctl restart all
```

## 配置NGINX
將 ``conf/nginx/talebook.org`` 中的域名修改為自己的網站域名，並放置到nginx的配置目錄中。

啟動命令如下：
```
sudo nginx -s start
```

訪問
===============
* 打開 http://web_server_ip:8000/ 測試python啟動是否正常；
* 打開 https://web_server_ip/ 測試nginx啟動是否正常


問題排查
===============
* supervisord啟動失敗

如果有調整過supervisord裡面的配置（例如端口、目錄），一定要執行```sudo supervisorctl reload all```重新讀取配置，不然是不會生效的，可能會導致啟動失敗。

如果提示```calibre:tornado-8000: ERROR(spawn error)```，那麼說明環境沒配置正確。
請打開日誌文件```/data/log/calibre-webserver.log```查看原因，重點查看最後一次出現Traceback報錯，關注其中```Traceback (most recent call last)```提示的錯誤原因。

* 網站能打開，但是提示```500: internal server error```

這種情況，一般是服務運行時出現異常，常見原因有目錄權限沒有配置正常、數據庫沒創建好、或者觸發了某個代碼BUG。

請打開日誌文件```/data/log/calibre-webserver.log```查看原因，重點查看最後一次出現Traceback報錯，關注其中```Traceback (most recent call last)```提示的錯誤原因，並提issue聯繫開發者排查。


