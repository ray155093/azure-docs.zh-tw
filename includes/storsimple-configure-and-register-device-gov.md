<!--author=SharS last changed: 02/22/16-->

### <a name="to-configure-and-register-the-device"></a>設定和註冊裝置
1. 存取 StorSimple 裝置序列主控台上的 Windows PowerShell 介面。 如需相關指示，請參閱 [使用 PuTTY 來連接至裝置序列主控台](#use-putty-to-connect-to-the-device-serial-console) 。 **請務必確實依照此程序，否則將無法存取主控台。**
2. 在開啟的工作階段中，按 Enter 鍵一次以取得命令提示字元。 
3. 系統將提示您選擇想要為裝置設定的語言。 指定語言，然後按 Enter 鍵。 
   
    ![StorSimple 設定和註冊裝置 1](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice1-gov-include.png)
4. 在顯示的序列主控台功能表中，選擇選項 1 以使用完整存取權進行登入。 
   
    ![StorSimple 註冊裝置 2](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice2-gov-include.png)
5. 執行下列步驟，為裝置設定最小的必要網路設定。
   
   > [!IMPORTANT]
   > 這些設定步驟必須在裝置的主動控制器上執行。 序列主控台功能表會在橫幅訊息中指出控制站狀態。 如果您未連接到主動控制器，請中斷連線，然後連接到主動控制器。
   > 
   > 
   
   1. 在命令提示字元中，輸入您的密碼。 預設裝置密碼是 **Password1**。
   2. 輸入以下命令：
      
        `Invoke-HcsSetupWizard`
   3. 安裝精靈將協助您設定裝置的網路設定。 請提供下列資訊： 
      
      * 適用於 DATA 0 網路介面的 IP 位址
      * 子網路遮罩
      * 閘道器
      * 適用於主要 DNS 伺服器的 IP 位址
      * 適用於主要 NTP 伺服器的 IP 位址
      
      > [!NOTE]
      > 您可能需要等候幾分鐘，以套用子網路遮罩和 DNS 設定。 
      > 
      > 
   4. 選擇性設定 Web Proxy 伺服器。
      
      > [!IMPORTANT]
      > 雖然 Web Proxy 設定是選用的，但請注意，如果您使用 Web Proxy，就只能在此處設定它。 如需詳細資訊，請參閱 [設定裝置的 Web Proxy](../articles/storsimple/storsimple-configure-web-proxy.md)。 
      > 
      > 
6. 按 Ctrl + C 來結束安裝精靈。
7. 安裝更新，如下所示：
   
   1. 使用下列 Cmdlet 在兩個控制器上設定 IP：
      
      `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`
   2. 在命令提示字元下，執行 `Get-HcsUpdateAvailability`。 您應該會在更新可用時收到通知。
   3. 執行 `Start-HcsUpdate`。 您可以在任何模式中執行此命令。 更新會套用至第一個控制器，控制器會容錯移轉，然後更新會套用至其他控制器。
      
      您可以執行 `Get-HcsUpdateStatus` 以監視更新進度。    
      
      下列範例輸出顯示更新進行中。
      
      ````
      Controller0>Get-HcsUpdateStatus
      RunInprogress       : True
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   : 
      ````
      
      下列範例輸出指出更新已完成。
      
      ````
      Controller1>Get-HcsUpdateStatus
      
      RunInprogress       : False
      LastHotfixTimestamp : 4/13/2015 10:56:13 PM
      LastUpdateTimestamp : 4/13/2015 10:35:25 PM
      Controller0Events   :
      Controller1Events   :
      
      It may take up to 11 hours to apply all the updates, including the Windows Updates.
8. After all the updates are successfully installed, run the following cmdlet to confirm that the software updates were applied correctly:
   
     `Get-HcsSystem`
   
    You should see the following versions:
   
   * HcsSoftwareVersion: 6.3.9600.17491
   * CisAgentVersion: 1.0.9037.0
   * MdsAgentVersion: 26.0.4696.1433
9. Run the following cmdlet to confirm that the firmware update was applied correctly:
   
    `Start-HcsFirmwareCheck`.
   
     The firmware status should be **UpToDate**.
10. Run the following cmdlet to point the device to the Microsoft Azure Government portal (because it points to the public Azure classic portal by default). This will restart both controllers. We recommend that you use two PuTTY sessions to simultaneously connect to both controllers so that you can see when each controller is restarted.
    
     `Set-CloudPlatform -AzureGovt_US`
    
    You will see a confirmation message. Accept the default (**Y**).
11. Run the following cmdlet to resume setup:
    
     `Invoke-HcsSetupWizard`
    
     ![Resume setup wizard](./media/storsimple-configure-and-register-device-gov/HCS_ResumeSetup-gov-include.png)
    
    When you resume setup, the wizard will be the Update 1 version (which corresponds to version 17469). 
12. Accept the network settings. You will see a validation message after you accept each setting.
13. For security reasons, the device administrator password expires after the first session, and you will need to change it now. When prompted, provide a device administrator password. A valid device administrator password must be between 8 and 15 characters. The password must contain three of the following: lowercase, uppercase, numeric, and special characters.
    
    <br/>![StorSimple register device 5](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice5_gov-include.png)
14. The final step in the setup wizard registers your device with the StorSimple Manager service. For this, you will need the service registration key that you obtained in [Step 2: Get the service registration key](#step-2-get-the-service-registration-key). After you supply the registration key, you may need to wait for 2-3 minutes before the device is registered.
    
    > [!NOTE]
    > You can press Ctrl + C at any time to exit the setup wizard. If you have entered all the network settings (IP address for Data 0, Subnet mask, and Gateway), your entries will be retained.
    > 
    > 
    
    ![StorSimple registration progress](./media/storsimple-configure-and-register-device-gov/HCS_RegistrationProgress-gov-include.png)
15. After the device is registered, a Service Data Encryption key will appear. Copy this key and save it in a safe location. **This key will be required with the service registration key to register additional devices with the StorSimple Manager service.** Refer to [StorSimple security](../articles/storsimple/storsimple-security.md) for more information about this key.
    
    ![StorSimple register device 7](./media/storsimple-configure-and-register-device-gov/HCS_RegisterYourDevice7_gov-include.png)    
    
    > [!IMPORTANT]
    > To copy the text from the serial console window, simply select the text. You should then be able to paste it in the clipboard or any text editor. 
    > 
    > DO NOT use Ctrl + C to copy the service data encryption key. Using Ctrl + C will cause you to exit the setup wizard. As a result, the device administrator password will not be changed and the device will revert to the default password.
    > 
    > 
16. Exit the serial console.
17. Return to the Azure Government Portal, and complete the following steps:
    
    1. Double-click your StorSimple Manager service to access the **Quick Start** page.
    2. Click **View connected devices**.
    3. On the **Devices** page, verify that the device has successfully connected to the service by looking up the status. The device status should be **Online**.
       
        ![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov/HCS_DeviceOnline-gov-include.png) 
       
        If the device status is **Offline**, wait for a couple of minutes for the device to come online. 
       
        If the device is still offline after a few minutes, then you need to make sure that your firewall network was configured as described in [networking requirements for your StorSimple device](../articles/storsimple/storsimple-system-requirements.md). 
       
        Verify that port 9354 is open for outbound communication as this is used by the service bus for StorSimple Manager service-to-device communication.



<!--HONumber=Nov16_HO3-->


