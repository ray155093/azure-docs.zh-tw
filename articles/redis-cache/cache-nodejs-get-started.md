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
	ms.date="08/16/2016"
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

本教學課程使用 [node\_redis](https://github.com/mranney/node_redis)。如需使用其他 Node.js 用戶端的範例，請參閱列在 [Node.js Redis 用戶端](http://redis.io/clients#nodejs)之 Node.js 用戶端的個別文件。

## 在 Azure 上建立 Redis 快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 擷取主機名稱和存取金鑰

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## 使用 SSL 安全地連接到快取

[node\_redis](https://github.com/mranney/node_redis) 的最新組建提供了使用 SSL 連接到 Azure Redis 快取的支援。下列範例示範如何使用 6380 的 SSL 端點連接到 Azure Redis 快取。以您的快取名稱取代 `<name>`，並以先前[擷取主機名稱和存取金鑰](#retrieve-the-host-name-and-access-keys)一節中所述的主要或次要金鑰取代 `<key>`。

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## 在快取中加入項目並擷取該項目

下列範例示範如何連接到 Azure Redis 快取執行個體，以及儲存和擷取快取中的項目。如需更多搭配使用 Redis 與 [node\_redis](https://github.com/mranney/node_redis) 用戶端的範例，請參閱 [http://redis.js.org/](http://redis.js.org/)。

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

輸出：

	OK
	value


## 後續步驟

- [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。

<!---HONumber=AcomDC_0817_2016-->