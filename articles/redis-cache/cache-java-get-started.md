<properties
   pageTitle="如何搭配使用 Azure Redis 快取與 Java | Microsoft Azure"
	description="開始搭配使用 Azure Redis 快取與 Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis 快取可讓您存取 Microsoft 所管理的專用 Redis 快取。從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何搭配使用 Azure Redis 快取與 Java。

## 必要條件

[Jedis](https://github.com/xetorthio/jedis) - Redis 的 Java 用戶端

本教學課程使用 Jedis，但是您可以使用列在 [http://redis.io/clients](http://redis.io/clients) 的任何 Java 用戶端。

## 在 Azure 上建立 Redis 快取

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## 擷取主機名稱和存取金鑰

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 啟用非 SSL 端點

有些 Redis 用戶端不支援 SSL，且預設會[停用新的 Azure Redis 快取執行個體的非 SSL 連接埠](cache-configure.md#access-ports)。在本文撰寫當下，[Jedis](https://github.com/xetorthio/jedis) 用戶端不支援 SSL。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## 在快取中加入項目並擷取該項目

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## 後續步驟

- [啟用快取診斷](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)，以[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)您快取的健全狀況。
- 閱讀官方 [Redis 文件](http://redis.io/documentation)。

<!---HONumber=AcomDC_0601_2016-->