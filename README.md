My Calibre WebServer
====================
A simple books library management website. Use Calibre as backend.

See a running instance of my books:

https://www.talebook.org

簡單的圖書管理系統
===================
這是一個基於Calibre的簡單的圖書管理系統，支持**在線閱讀**。主要特點是：
* 由於Calibre自帶的網頁太醜太難用，於是獨立編寫了一個。
* 為了網友們更方便使用，開發了多用戶功能，支持~~豆瓣~~（已廢棄）、QQ和微博登錄。
* 藉助[Readium.js](https://github.com/readium/readium-js-viewer) 庫，支持了網頁在線閱讀電子書。
* 支持從百度百科、豆瓣搜索並導入書籍基礎信息。

部署比較簡答，可以參考[安裝文檔](document/INSTALL.zh_CN.md)

或者，直接訪問我的在線書庫，有大約兩萬本藏書。https://www.talebook.org


感謝oldiy添加Docker一鍵部署，可以查看[my-calibre-webserver-docker](https://hub.docker.com/r/oldiy/my-calibre-webserver-docker)
