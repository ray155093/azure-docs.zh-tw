---
title: "從 Windows、Linux、Unix 或 OS X 搭配使用 SSH 與 HDInsight (Hadoop) | Microsoft Docs"
description: " 您可以使用安全殼層 (SSH) 存取 HDInsight。 本文件提供從 Windows、Linux、Unix 或 OS X 用戶端搭配使用 SSH 與 HDInsight 的資訊."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 37409ad3f50cdd4a7a384c96a57a35ef8c83fb8f


---
# <a name="use-ssh-with-hdinsight-hadoop-from-windows-linux-unix-or-os-x"></a>從 Windows、Linux、Unix 或 OS X 搭配使用 SSH 與 HDInsight (Hadoop)

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows、Linux、Unix、OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[安全殼層 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 可讓您使用命令列介面登入以 Linux 為基礎的 HDInsight 叢集和執行命令。 本文件提供 SSH 的基本資訊和搭配使用 SSH 與 HDInsight 的特定資訊。

## <a name="what-is-ssh"></a>什麼是 SSH？

SSH 是一種密碼編譯網路通訊協定，它能讓您透過不安全的網路與遠端伺服器安全地通訊。 SSH 可用來提供安全的命令列供您登入遠端伺服器， 亦即本例中的 HDInsight 叢集前端節點或邊緣節點。

您也可以使用 SSH 來建立通道，將網路流量從用戶端傳送到 HDInsight 叢集。 透過通道，您可以存取 HDInsight 叢集上不直接向網際網路公開的服務。 如需搭配使用 SSH 通道與 HDInsight 的詳細資訊，請參閱[搭配使用 SSH 通道與 HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="ssh-clients"></a>SSH 用戶端

許多作業系統透過 `ssh` 和 `scp` 命令列公用程式提供 SSH 用戶端功能。

* __ssh__︰一般的 SSH 用戶端，可用來建立遠端命令列工作階段及建立通道。
* __scp__︰一種公用程式，可使用 SSH 通訊協定在本機或遠端系統之間複製檔案。

根據記錄，Windows 要等到 Windows 10 Anniversary Edition 才提供 SSH 用戶端。 這個版本的 Windows 包含 Bash on Windows 10 開發人員功能，其提供 `ssh`、`scp` 和其他 Linux 命令。 如需使用 Bash on Windows 10 的詳細資訊，請參閱 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)。

如果您使用 Windows 但沒有 Bash on Windows 10 的存取權限，我們推薦下列 SSH 用戶端︰

* [Git For Windows](https://git-for-windows.github.io/)︰提供 `ssh` 和 `scp` 命令列公用程式。
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)︰提供圖形化的 SSH 用戶端。
* [MobaXterm](http://mobaxterm.mobatek.net/)︰提供圖形化的 SSH 用戶端。
* [Cygwin](https://cygwin.com/)︰提供 `ssh` 和 `scp` 命令列公用程式。

> [!NOTE]
> 本文件中的步驟假設您擁有 `ssh` 命令的存取權限。 如果您使用 puTTY 或 MobaXterm 之類的用戶端，請參閱該產品的文件以取得對等的命令和參數。

## <a name="ssh-authentication"></a>SSH 驗證

SSH 連線可以透過密碼或[公開金鑰加密 (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography)來進行驗證。 使用金鑰是最安全的選擇，因為它不像密碼一樣容易暴露在許多攻擊的風險之下。 儘管如此，金鑰的建立和管理比使用密碼來得複雜。

使用公開金鑰加密牽涉到建立「公開」和「私密」金鑰組。

* **公開金鑰**會載入 HDInsight 叢集的節點，或任何您要搭配公開金鑰加密使用的其他服務上。

* **私密金鑰**是使用 SSH 用戶端登入時提供給 HDInsight 叢集，藉以驗證身分的資訊。 保護此私密金鑰。 不要共用它。

    您可以為私密金鑰建立複雜密碼來增加安全性。 在使用金鑰之前，您必須先提供此複雜密碼。

### <a name="create-a-public-and-private-key"></a>建立公用和私密金鑰

`ssh-keygen` 公用程式是建立與 HDInsight 搭配使用之公用和私密金鑰組最簡單的方式。 在命令列中，使用下列命令來建立與 HDInsight 搭配使用的新金鑰組：

> [!NOTE]
> 如果您使用 MobaXTerm 或 puTTY 之類的 GUI SSH 用戶端，請參閱用戶端的文件以了解如何產生金鑰。

    ssh-keygen -t rsa -b 2048

系統會提示您輸入下列資訊：

* 檔案位置：預設位置為 `~/.ssh/id_rsa`。

* 選用的複雜密碼︰如果您輸入複雜密碼，在進行 HDInsight 叢集驗證時就必須再次輸入。

> [!IMPORTANT]
> 複雜密碼是私密金鑰的密碼。 每當您使用私密金鑰來進行驗證時，必須先提供複雜密碼才能使用金鑰。 即便是有人取得您的私密金鑰，如果沒有複雜密碼依然無法使用。
>
> 不過如果您忘記複雜密碼，將無法重設或復原。

命令完成後，將會產生兩個新檔案︰

* __id\_rsa__：該檔案含有私密金鑰。

    > [!WARNING]
    > 您必須限制該檔案的存取權限，以防止他人在未經授權的情況下存取公用金鑰保護的服務。

* __id\_rsa.pub__：該檔案含有公開金鑰。 在建立 HDInsght 叢集時，您會使用這個檔案。

    > [!NOTE]
    > 誰擁有「公開」的存取權限並不重要。 從本質來說，公開金鑰只有驗證私密金鑰的作用。 諸如 SSH 伺服器等服務，會在您使用私密金鑰來進行驗證時使用公開金鑰來驗證您的身分。

## <a name="configure-ssh-on-hdinsight"></a>在 HDInsight 上設定 SSH

在建立以 Linux 為基礎的 HDInsight 叢集時，您必須提供「SSH 使用者名稱」和「密碼」或「公開金鑰」。 在叢集建立期間，這些資訊可用來在 HDInsight 叢集節點上建立登入。 密碼或公開金鑰可用來保護使用者帳戶。

如需在叢集建立期間設定 SSH 的詳細資訊，請參閱下列文件︰

* [使用 Azure 入口網站建立 HDInsight](hdinsight-hadoop-create-linux-clusters-portal.md)
* [使用 Azure CLI 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [使用 Azure PowerShell 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [使用 Azure Resource Manager 範本建立 HDInsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [使用 .NET SDK 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [使用 REST 建立 HDInsight](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>其他 SSH 使用者

雖然您可以在叢集建立後將其他 SSH 使用者加入叢集，不過我們不建議這種做法。

* 您必須手動將新的 SSH 使用者加入叢集中的每個節點。

* 新 SSH 使用者的 HDInsight 存取權限與預設使用者相同。 您無法在 HDInsight 中根據 SSH 使用者帳戶限制資料或作業的存取權限。

若要限制每位使用者的存取權限，您必須使用已加入網域的 HDInsight 叢集。 已加入網域的 HDInsight 叢集使用 Active Directory 來控制叢集資源的存取權限。

透過已加入網域的 HDInsight 叢集，您可以在使用 SSH 連線後使用 Active Directory 進行驗證。 多位使用者可以使用 SSH 連線，然後在連線後驗證其 Active Directory 帳戶。 如需詳細資訊，請參閱[已加入網域的 HDInsight 叢集](#domainjoined)。

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> 連接 HDInsight

雖然 HDInsight 叢集中的所有節點都會執行 SSH 伺服器，不過您只能透過公用網際網路連接前端節點或邊緣節點。

* 若要連接前端節點，請使用 `CLUSTERNAME-ssh.azurehdinsight.net`其中 __CLUSTERNAME__ 是 HDInsight 叢集的名稱。 連接連接埠 22 (SSH 的預設連接埠) 會連接主要前端節點。 連接埠 23 則會連接次要前端節點。

* 若要連接到邊緣節點，請使用 `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`其中 __EDGENAME__ 是邊緣節點的名稱，__CLUSTERNAME__ 是 HDInsight 叢集的名稱。 連接邊緣節點時，請使用連接埠 22。

以下範例示範如何使用 SSH 使用者名稱 __sshuser__ 連接 __myhdi__ 叢集的前端節點和邊緣節點。 邊緣節點名為 __myedge__。

| 作法... | 目的... |
| ----- | ----- |
| 連接主要前端節點 | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| 連接次要前端節點 | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| 連接到邊緣節點 | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

如果您使用密碼來保護 SSH 帳戶，系統會提示您輸入密碼。

如果您使用公開金鑰來保護 SSH 帳戶，可能需要使用 `-i` 參數來指定對應之私密金鑰的路徑。 以下範例示範如何使用 `-i` 參數：

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>連接其他節點

背景工作角色節點和 Zookeeper 節點無法直接從叢集外部存取，不過可以從叢集前端節點或邊緣節點存取。 以下是完成這項作業的一般步驟：

1. 使用 SSH 連接前端或邊緣節點：

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 從 SSH 到前端或邊緣節點的連線，使用 `ssh` 命令來連接叢集中的背景工作角色節點︰

        ssh sshuser@wn0-myhdi

    若要擷取叢集中的背景工作角色節點清單，請參閱[使用 Ambari REST API 管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文件中示範如何擷取叢集節點之完整網域名稱的範例。

如果您使用密碼保護 SSH 帳戶，系統會要求您輸入密碼，隨後再建立連線。

如果您使用 SSH 金鑰來驗證使用者帳戶，請務必確認本機環境已針對 SSH 代理程式轉送進行設定。

> [!IMPORTANT]
> 以下步驟假設您使用 Linux/UNIX 架構系統，並使用 Bash on Windows 10 進行操作。 如果這些步驟不適用於您的系統，您可能需要參閱 SSH 用戶端的文件。

1. 使用文字編輯器開啟 `~/.ssh/config`。 如果該檔案不存在，您可以藉由在命令列中輸入 `touch ~/.ssh/config` 加以建立。

2. 將下列項目新增至檔案。 將 *CLUSTERNAME* 取代為 HDInsight 叢集的名稱。

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    這個項目會為您的 HDInsight 叢集設定 SSH 代理程式轉送。

3. 從終端機使用下列命令，測試 SSH 代理程式轉送：

        echo "$SSH_AUTH_SOCK"

    此命令會傳回類似以下文字的資訊：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    如果未傳回任何資訊，表示 `ssh-agent` 未執行。 如需安裝及設定 `ssh-agent` 的步驟，請參閱[透過 ssh 使用 ssh-agent (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) 的代理程式啟動指令碼資訊，或參閱 SSH 用戶端文件。

4. 一旦確認 **ssh-agent** 正在執行，請使用下列項目將您的 SSH 私密金鑰新增至代理程式：

        ssh-add ~/.ssh/id_rsa

    如果您的私密金鑰儲存在不同的檔案，請將 `~/.ssh/id_rsa` 取代為檔案的路徑。

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>已加入網域的 HDInsight

[已加入網域的 HDInsight](hdinsight-domain-joined-introduction.md) 會整合 Kerberos 與 HDInsight 中的 Hadoop。 由於 SSH 使用者不是 Active Directory 網域使用者，因此除非您通過 Active Directory 驗證，否則將無法執行 Hadoop 命令。 請使用以下步驟來向 Active Directory 驗證 SSH 工作階段：

1. 使用 SSH 連接加入網域的 HDInsight 叢集，如[連接 HDInsight](#connect) 一節所述。 例如，以下命令會使用名為 __sshuser__ 的 SSH 帳戶連接名為 __myhdi__ 的 HDInsight 叢集。

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 使用以下命令來利用網域使用者和密碼進行驗證︰

        kinit

     出現提示時，請輸入網域使用者的網域使用者名稱和密碼。

    如需設定已加入網域 HDInsight 叢集之網域使用者的詳細資訊，請參閱[設定已加入網域的 HDInisight 叢集](hdinsight-domain-joined-configure.md)。

使用 `kinit` 命令完成驗證後，您現在就可以使用 `hdfs dfs -ls /` 或 `hive` 等 Hadoop 命令了。

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH 通道

SSH 可用來建立通道以將本機要求 (例如 Web 要求) 傳送到 HDInsight 叢集。 要求便會路由至要求的資源，彷彿要求是在 HDInsight 叢集前端節點上產生。

> [!IMPORTANT]
> SSH 通道是存取某些 Hadoop 服務之 Web UI 的必要項目。 例如，[作業記錄] UI 或 [資源管理員] UI 都只能使用 SSH 通道存取。

如需建立及使用 SSH 通道的詳細資訊，請參閱[使用 SSH 通道來存取 Ambari Web UI、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="next-steps"></a>後續步驟

您已經了解如何使用 SSH 金鑰進行驗證，接著請了解如何在 HDInsight 上搭配使用 MapReduce 和 Hadoop。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Jan17_HO3-->


