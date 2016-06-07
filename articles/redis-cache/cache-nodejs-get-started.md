<properties
	pageTitle="如何搭配使用 Azure Redis 快取與 Node.js | Microsoft Azure"
	description="開始搭配使用 Azure Redis 快取與 Node.js 和 node_redis。"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取 Microsoft 所管理的專用安全 Redis 快取。從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何開始搭配使用 Azure Redis 快取與 Node.js。如需搭配使用 Azure Redis 快取與 Node.js 的另一個範例，請參閱[在 Azure 網站上使用 Socket.IO 建置 Node.js 聊天應用程式](../app-service-web/web-sites-nodejs-chat-app-socketio.md)。


## 必要條件

安裝 [node\_redis](https://github.com/mranney/node_redis)：

    npm install redis

本教學課程使用 [node\_redis](https://github.com/mranney/node_redis)，但是您可以使用列在 [http://redis.io/clients](http://redis.io/clients) 的任何 Node.js 用戶端。

## 在 Azure 上建立 Redis 快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 擷取主機名稱和存取金鑰

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 啟用非 SSL 端點

有些 Redis 用戶端不支援 SSL，且預設會[停用新的 Azure Redis 快取執行個體的非 SSL 連接埠](cache-configure.md#access-ports)。在本文撰寫當下，[node\_redis](https://github.com/mranney/node_redis) 用戶端不支援 SSL。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## 在快取中加入項目並擷取該項目

	  var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

Output:

	OK
	value


## 後續步驟

- [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。

<!---HONumber=AcomDC_0601_2016-->