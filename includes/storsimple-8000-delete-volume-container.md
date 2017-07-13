<!--author=alkohli last changed: 01/13/17-->

如果磁碟區容器有相關聯的磁碟區，請先將那些磁碟區離線。 請遵循 [使磁碟區離線](../articles/storsimple/storsimple-manage-volumes.md#take-a-volume-offline)中的步驟進行。 磁碟機離線後，才可以刪除它們。 當磁碟區容器沒有相關聯的磁碟區時，請將磁碟區容器刪除。 執行下列程序可將磁碟區容器刪除。

#### 刪除磁碟區容器
<a id="to-delete-a-volume-container" class="xliff"></a>
1. 移至 StorSimple 裝置管理員服務，然後按一下 [裝置]。 選取並按一下裝置，然後移至 [設定] > [管理] > [磁碟區容器]。

    ![磁碟區容器刀鋒視窗](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. 從表格式的磁碟區容器清單中，選取您想要刪除的磁碟區容器，以滑鼠右鍵按一下 [...]，然後選取 [刪除]。

    ![將磁碟區容器刪除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. 磁碟區容器沒有相關聯的磁碟區時，才能刪除它。 當系統提示您進行確認時，請檢閱並選取指出刪除磁碟區容器影響的核取方塊。 按一下 [刪除] 即可將磁碟區容器刪除。

    ![確認刪除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

磁碟區容器清單會加以更新，來反映已刪除的磁碟區容器。

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


