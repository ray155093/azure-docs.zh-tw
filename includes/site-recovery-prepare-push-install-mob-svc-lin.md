### <a name="prepare-for-push-install-on-linux-servers"></a>準備在 Linux 伺服器上推送安裝

1. 確定 Linux 機器和處理序伺服器之間有網路連線。
2. 建立可以由處理序伺服器用來存取機器的帳戶。 帳戶應該是來源 Linux 伺服器上的 **root** 使用者 (此帳戶僅用於推送安裝)。
3. 檢查來源 Linux 伺服器上的 `/etc/hosts` 檔案包含將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。
4. 在您想要複寫的機器上安裝最新的 openssh、openssh 伺服器、openssl 封裝。
5. 請確定 SSH 已啟用且正在連接埠 22 上執行。
6. 在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證，如下所示：
  - 以 **root** 的身分登入。
  - 在 /etc/ssh/sshd_config 檔案中，尋找以 **PasswordAuthentication** 開頭的行。
  - 取消該行的註解並將值從 **no** 變更為 **yes**。
   6.4 尋找以 **Subsystem** 為開頭的行並取消其註解。

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. 新增您在 CSPSConfigtool 中建立的帳戶。

    - 登入您的組態伺服器。
    - 開啟 **cspsconfigtool.exe**。 (它會是桌面上可用的捷徑，位於 %ProgramData%\home\svsystems\bin 資料夾之下)
    - 在 [管理帳戶] 索引標籤中，按一下 [新增帳戶]。
    - 加入您所建立的帳戶。 新增帳戶之後，當您啟用機器的複寫時，必須提供認證。


<!--HONumber=Jan17_HO3-->


