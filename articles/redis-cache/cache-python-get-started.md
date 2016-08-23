<properties
	pageTitle="如何搭配使用 Azure Redis 快取與 Python | Microsoft Azure"
	description="開始搭配使用 Azure Redis 快取與 Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

本主題說明如何搭配使用 Azure Redis 快取與 Python。


## 必要條件

安裝 [redis-py](https://github.com/andymccurdy/redis-py)。


## 在 Azure 上建立 Redis 快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 擷取主機名稱和存取金鑰

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 啟用非 SSL 端點

有些 Redis 用戶端不支援 SSL，且預設會[停用新的 Azure Redis 快取執行個體的非 SSL 連接埠](cache-configure.md#access-ports)。在本文撰寫當下，[redis-py](https://github.com/andymccurdy/redis-py) 用戶端不支援 SSL。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## 在快取中加入項目並擷取該項目


	>>> import redis
	>>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
	      port=6380, db=0, password='<key>', ssl=True)
	>>> r.set('foo', 'bar')
	True
	>>> r.get('foo')
	b'bar'


將 `<name>` 取代為您的快取名稱，並將 `key` 取代為您的存取金鑰。


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0817_2016-->