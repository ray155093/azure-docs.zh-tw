### <a name="prepare-for-push-install-on-windows-machines"></a>準備在 Windows 機器上推送安裝

1. 確定 Windows 機器和處理序伺服器之間有網路連線。
2. 建立可以由處理序伺服器用來存取機器的帳戶。 帳戶應該具備系統管理員權限 (本機或網域)，並且僅用於推送安裝和代理程式更新。

   > [!NOTE]
   > 如果您未使用網域帳戶，您必須停用本機電腦上的遠端使用者存取控制。 您可以在 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 登錄機碼下新增 DWORD **LocalAccountTokenFilterPolicy** 並將其值設為 1，以停用遠端使用者存取控制。 以下是從命令提示字元執行相同作業的命令 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。
   >
   >
2. 在您想要保護之機器的 Windows 防火牆上，選取 [允許應用程式或功能通過防火牆] 。 啟用 [檔案及印表機共用] 和 [Windows Management Instrumentation]。 對於隸屬於網域的機器，您可以使用 GPO 設定防火牆設定。

   ![防火牆設定](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. 新增您在 CSPSConfigtool 中建立的帳戶。
    - 登入您的組態伺服器。
    - 開啟 **cspsconfigtool.exe**。 (它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾之下)
    - 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
    - 加入您所建立的帳戶。 新增帳戶之後，當您啟用機器的複寫時，必須提供認證。


<!--HONumber=Jan17_HO3-->


