## 建立事件中心

1. 登入 [Azure 傳統入口網站][]，並按一下畫面底部的 [新增]。

2. 依序按一下 [**應用程式服務**]、[**服務匯流排**]、[**事件中樞**] 及 [**快速建立**]。

	![][1]

3. 為您的事件中樞輸入名稱、選取所需的區域，然後按一下 [**建立新的事件中樞**]。

	![][2]

4. 如果您未明確選取指定區域中現有的命名空間，入口網站會為您建立命名空間 (通常是**事件中樞名稱-ns**)。按一下該命名空間 (在此範例中為 **eventhub-ns**)。

	![][3]

5. 按一下頁面底部的 [連接資訊]。按一下 [複製] 按鈕 (如下圖所示)，將 **RootManageSharedAccessKey** 連接字串複製到剪貼簿。儲存此連接字串，以便稍後在本教學課程中使用。

	![][4]

現已建立事件中心，並具有傳送與接收事件所需的連接字串。

[1]: ./media/event-hubs-create-event-hub/create-event-hub1.png
[2]: ./media/event-hubs-create-event-hub/create-event-hub2.png
[3]: ./media/event-hubs-create-event-hub/create-event-hub3.png
[4]: ./media/event-hubs-create-event-hub/create-conn-str1.png

[Azure 傳統入口網站]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0824_2016-->