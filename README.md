# <a name="azure-technical-documentation-contributor-guide"></a>Azure 技術文件參與者指南
您已經找到 GitHub 存放庫，此處存放發佈於 [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) 之 Azure 技術文件的來源。

此儲存機制內也有相關指引，可協助您參與編輯我們的技術文件。 如需參與者指南中的文章清單，請參閱[索引](contributor-guide/contributor-guide-index.md)。

## <a name="contribute-to-azure-documentation"></a>參與編輯 Azure 文件
感謝您對於 Azure 文件感到興趣！

* [參與方法](#ways-to-contribute)
* [管理辦法](#code-of-conduct)
* [關於您對 Azure 內容的參與](#about-your-contributions-to-azure-content)
* [儲存機制組織方式](#repository-organization)
* [使用 GitHub、Git 和此儲存機制](#use-github-git-and-this-repository)
* [如何使用 Markdown 來格式化主題](#how-to-use-markdown-to-format-your-topic)
* [其他資源](#more-resources)
* [所有參與者指南文章的索引](contributor-guide/contributor-guide-index.md)(會開啟新頁面)

## <a name="ways-to-contribute"></a>參與方法
您可以將更新提交到 [Azure 文件](https://docs.microsoft.com/azure)，如下所示︰

* 您可以在 GitHub 使用者介面輕鬆地參與技術文件。 在此存放庫中尋找文章，或瀏覽 [https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) 上的文章，然後按一下其中的連結以前往文章的 GitHub 來源。
* 如果要大幅變更現有文章、新增或變更圖片或是參與編輯新文章，您還需要先取用其儲存機制、安裝 Git Bash 和 MarkdownPad，並學習一些 Git 命令才行。

## <a name="code-of-conduct"></a>管理辦法
此專案採用 [Microsoft 開放原始碼管理辦法](https://opensource.microsoft.com/codeofconduct/)。 如需詳細資訊，請參閱[管理辦法常見問題集](https://opensource.microsoft.com/codeofconduct/faq/)，如有任何其他問題或意見請連絡 [opencode@microsoft.com](mailto:opencode@microsoft.com)。

## <a name="about-your-contributions-to-azure-content"></a>關於您對 Azure 內容的參與
### <a name="minor-corrections"></a>小幅度修正
您在此儲存機制中針對文件和程式碼範例所提交的小幅度修正或釐清，將受到 [docs.microsoft.com 使用規定](https://docs.microsoft.com/legal/termsofuse)的約束。

### <a name="larger-submissions"></a>較大範圍的提交內容
如果您提交提取要求是為了對文件和程式碼範例進行全新或大幅度的變更，而且您不是 Microsoft 的員工，我們會在 GitHub 中傳送註解，要求您提交線上版的參與授權合約 (Contribution License Agreement, CLA)： 您必須先填寫線上表單，我們才會接受您的提取要求。

## <a name="repository-organization"></a>儲存機制組織方式
azure-docs 存放庫中的內容會遵循 https://docs.microsoft.com/azure 上文件的組織方式。 此儲存機制包含兩個根資料夾：

### <a name="articles"></a>\articles
*\articles* 資料夾包含格式化為 Markdown 檔的文件文章，其副檔名為 *.md*。 文章通常會依 Azure 服務分入群組。

文件需要遵守嚴格的檔案命名守則，如需詳細資訊，請參閱[我們的檔案命名指引](contributor-guide/file-names-and-locations.md)。

*\articles* 資料夾內有 *\media* 資料夾可供存放根目錄文章媒體檔，而在 \media 資料夾內則是存有各文章內圖片的子資料夾。  每個服務資料夾內都含有不同的媒體資料夾來存放文章。 文章圖片資料夾的名稱和文章檔案的名稱相同，但不會有 *.md* 副檔名。

### <a name="includes"></a>\includes
您也可以建立特定的內容區段，以便重複地用來加入一個或多個文章之中。 請參閱[技術內容中使用的自訂延伸模組](contributor-guide/custom-markdown-extensions.md)。

### <a name="markdown-templates"></a>\markdown templates
此資料夾包含標準的 Markdown 範本，而且此範本具有文章所需的基本 Markdown 格式。

### <a name="contributor-guide"></a>\contributor-guide
此資料夾包含屬於參與者指南的文章。

## <a name="use-github-git-and-this-repository"></a>使用 GitHub、Git 和此儲存機制
如需如何參與、如何使用 GitHub UI 參與小型變更，以及如何取用和複製儲存機制以進行更大幅度參與的相關資訊，請參閱[在 GitHub 中安裝和設定編寫工具](contributor-guide/tools-and-setup.md)。

如果您安裝了 GitBash 並選擇在本機工作，[用來建立新文章或更新現有文章的 Git 命令](contributor-guide/git-commands-for-master.md)中已列出了相關的步驟，以說明如何在本機建立新的工作分支、如何進行變更，以及如何將變更提交回主要分支。

### <a name="branches"></a>分支
對於您所建立的本機工作分支，建議您將變更的內容限縮於特定的範圍之內。 每個分支都應侷限在單一概念/文章，以簡化工作流程，並降低合併時發生衝突的可能性。  以下是新分支適合的工作範圍：

* 新文章 (和相關圖片)
* 對文章的拼字和文法進行編輯。
* 對為數眾多的一組文章套用單一格式變更 (例如新的著作權頁尾)。

## <a name="how-to-use-markdown-to-format-your-topic"></a>如何使用 Markdown 來格式化主題
此儲存機制中的所有文章都使用 GitHub 風格的 Markdown。  其資源清單如下。

* [Markdown 基本概念](https://help.github.com/articles/markdown-basics/)
* [可列印的 Markdown 速查表](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>文章中繼資料
文章中繼資料可啟用某些功能，例如作者屬性、投稿人屬性、階層連結、文章說明和 SEO 最佳化，以及 Microsoft 用來評估內容效能的報告。 因此，中繼資料非常重要！ [這裡的指引能協助您確認中繼資料的製作方式是否正確](contributor-guide/article-metadata.md)。

### <a name="labels"></a>標籤
系統會指派自動化標籤去提取要求，以幫助我們管理提取要求工作流程，並協助讓您知道您的提取要求做了些什麼︰

* 相關的參與授權合約
  * 不需要 cla︰變更幅度相當小，而且不需要您登入 CLA。
  * 需要 cla︰變更幅度相對較大，需要您登入 CLA。
  * cla 簽章︰ 參與者簽署 CLA，因此提取要求可繼續進行作業以供檢閱。
* 要件標籤︰例如 PnP、現代應用程式、TDC 等標籤，可幫助依據需要檢閱提取要求的內部組織將提取要求分類。
* 傳送變更給作者︰會通知作者已暫止提取要求。

## <a name="more-resources"></a>其他資源
如需所有指引主題，請參閱[參與者指南索引](contributor-guide/contributor-guide-index.md)。



<!--HONumber=Feb17_HO2-->


