當不再需要某個連接至虛擬機器的資料磁碟時，卸離此資料磁碟很簡單。 將磁碟中斷連結會將它從虛擬機器中移除，但不會從 Azure 儲存體帳戶中刪除。

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連接至相同或其他虛擬機器。  

> [!NOTE]
> 若要將作業系統磁碟中斷連結，您必須先刪除虛擬機器。
>

## <a name="find-the-disk"></a>尋找磁碟
如果您不知道磁碟的名稱，或想要先驗證它再卸離，請遵循下列步驟。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [虛擬機器] ，然後選取適當的 VM。

3. 按一下沿著虛擬機器儀表板左側邊緣 [設定] 底下的 [磁碟]。

 虛擬機器儀表板會列出所有已連結之磁碟的名稱和類型。 例如，此畫面會顯示虛擬機器及一個作業系統 (OS) 磁碟和一個資料磁碟：

    ![尋找資料磁碟](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>卸離磁碟
1. 從 Azure 入口網站中，按一下 [虛擬機器]，然後按一下擁有您想要中斷連結之資料磁碟的虛擬機器名稱。

2. 按一下沿著虛擬機器儀表板左側邊緣 [設定] 底下的 [磁碟]。

3. 按一下您想要中斷連結的磁碟。

  ![識別要中斷連結的磁碟](./media/howto-detach-disk-windows-linux/disklist.png)

4. 從命令列中，按一下 [中斷連結]。

  ![找出中斷連結命令](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. 在確認對話方塊中，按一下 [是] 以將磁碟中斷連結。

  ![確認將磁碟中斷連結](./media/howto-detach-disk-windows-linux/confirmdetach.png)

磁碟仍留在儲存體中，但不再連接至虛擬機器。
