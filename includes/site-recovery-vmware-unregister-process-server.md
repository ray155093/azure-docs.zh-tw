取消註冊處理序伺服器的步驟，隨著其與設定伺服器的連線狀態而有所不同。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>取消註冊處於已連線狀態的處理序伺服器

1. 以系統管理員身分遠端登入處理序伺服器。
2. 啟動 [控制台] 並開啟 [程式集] > [解除安裝程式]
3. 解除安裝名為 **Microsoft Azure Site Recovery Configuration/Process Server** 的程式
4. 完成步驟 3 後，您就可以解除安裝 **Microsoft Azure Site Recovery Configuration/Process Server 相依項目**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>取消註冊處於已中斷連線狀態的處理序伺服器

> [!WARNING]
> 如果沒辦法恢復處理序伺服器安裝所在的虛擬機器，使用下列步驟應很有用。

1. 以系統管理員身分登入設定伺服器。
2. 開啟系統管理命令提示字元並瀏覽至目錄 `%ProgramData%\ASR\home\svsystems\bin`。
3. 現在執行命令。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 這將會從系統清除處理序伺服器的詳細資料。


<!--HONumber=Feb17_HO1-->


