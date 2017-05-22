---
title: "適用於 PostgreSQL 的 Azure 資料庫的連線庫 | Microsoft Docs"
description: "列出用戶端程式在連接到適用於 PostgreSQL 的 Azure 資料庫時可使用的每個程式庫或驅動程式。"
keywords: "azure 雲端 postgresql postgres"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫的連線庫
本主題將列出用戶端程式在連接到適用於 PostgreSQL 的 Azure 資料庫時可使用的每個程式庫或驅動程式。

## <a name="client-interfaces"></a>用戶端介面
大部分連接到 PostgreSQL 伺服器的語言用戶端程式庫都是外部專案，而且是獨立發佈的。 Windows、Linux 和 Mac 平台均支援這些程式庫。 以下列出一些熱門的用戶端驅動程式。
| **語言** | **用戶端介面** | **其他資訊** | **下載** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | DB API 2.0 相容 | [下載](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | 資料庫擴充功能 | [安裝](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm 封裝](https://www.npmjs.com/package/pg) | 單純的 JavaScript 非封鎖用戶端 | [安裝](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Type 4 JDBC 驅動程式 | [下載](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Ruby 介面 | [下載](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | 單純的 Go postgres 驅動程式 | [安裝](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | ADO.NET 資料提供者 | [下載](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | ODBC 驅動程式 | [下載](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | 主要的 C 語言介面 | 已包括 |
| C++ | [libpqxx](http://pqxx.org/) | 新樣式的 C++ 介面 | [下載](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>後續步驟
- 如需服務的概觀，請參閱[適用於 PostgreSQL 的 Azure 資料庫概觀](overview.md)。
- 如需伺服器的詳細資訊，請參閱[適用於 PostgreSQL 的 Azure 資料庫伺服器](concepts-servers.md)。
- 若要建立第一部 PostgreSQL 伺服器，請參閱[在 Azure 入口網站中建立適用於 PostgreSQL 的 Azure 資料庫](quickstart-create-server-database-portal.md)。

