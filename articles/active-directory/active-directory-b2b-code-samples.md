---
title: "Azure Active Directory B2B 共同作業程式碼與 PowerShell 範例 | Microsoft Docs"
description: "Azure Active Directory B2B 共同作業的程式碼與 PowerShell 範例"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/08/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 1287a44fcf450023d4544202bd5db51dc99768ab


---


# <a name="azure-active-directory-b2b-collaboration-code-and-powershell-samples"></a>Azure Active Directory B2B 共同作業程式碼與 PowerShell 範例

## <a name="code-sample"></a>程式碼範例
此範例示範如何以僅限應用程式模式呼叫邀請 API，以取得您要邀請 B2B 使用者存取之資源的兌換 URL。 目標是傳送自訂邀請電子郵件。 您將會看到如何使用 HTTP 用戶端來編寫電子郵件，因此您可以自訂其外觀並透過圖形 API 傳送。

```
namespace SampleInviteApp
{
    using System.Globalization;
    using System;
    using System.Net;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.Graph;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    class Program
    {
        /// <summary>
        ///  Authentication endpoint to get token.
        /// </summary>
        static readonly string EstsLoginEndpoint = "https://login.microsoftonline.com";
        /// <summary>
        /// Initialize this to Tenant you want to invite users to.
        /// </summary>
        private static readonly string TenantID = "";
        /// <summary>
        /// Client id of the app.
        /// </summary>
        private static readonly string TestAppClientId = "";
        /// <summary>
        /// Client secret of the app.
        /// </summary>
        private static readonly string TestAppClientSecret = @"";
        /// <summary>
        /// Admin user principal name
        /// </summary>
        private static readonly string InviterUserPrincipalName = "";
        static void Main(string[] args)
        {
            CreateInvitation();
        }
        private static void CreateInvitation()
        {
            string accessToken = null;
            string GraphResource = string.Format("", "graph.microsoft.com");
            // Get the token for our app to talk to graph.
            try
            {
                AuthenticationContext testAuthContext = new AuthenticationContext(string.Format("{0}/{1}", EstsLoginEndpoint, TenantID));
                AuthenticationResult testAuthResult = testAuthContext.AcquireToken(
                    GraphResource,
                    new ClientCredential(TestAppClientId, TestAppClientSecret));;
                accessToken = testAuthResult.AccessToken;
            }
            catch (AdalException ex)
            {
                Console.WriteLine("An exception was thrown: {0}.", ex);
                throw;
            }
            // setup http client.
            HttpClient httpClient = new HttpClient();
            httpClient.Timeout = TimeSpan.FromSeconds(300);
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            httpClient.DefaultRequestHeaders.Add("client-request-id", Guid.NewGuid().ToString());
            Console.WriteLine("CorrelationID for the request: {0}", httpClient.DefaultRequestHeaders.GetValues("client-request-id").Single());
            var inviteEndPoint = string.Format("https://graph.microsoft.com/beta/invitations");
            // Set the invitation object.
            Invitation invitation = new Invitation();
            invitation.InvitedUserDisplayName = "displayName";
            invitation.InvitedUserEmailAddress = "Give the invitee email";
            invitation.InviteRedirectUrl = "https://www.microsoft.com";
            invitation.SendInvitationMessage = false;
            // Make the invite call. Your app needs to have User.ReadWrite.All or Directory.ReadWrite.All scope to invite
            HttpContent content = new StringContent(JsonConvert.SerializeObject(invitation));
            content.Headers.Add("ContentType", "application/json");
            var postResponse = httpClient.PostAsync(inviteEndPoint, content).Result;
            string serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
            // Build the mail message. Here we are including the server response from inviation request, but you can customize it.
            // To send the email you need Mail.Send scope.
            var emailEndPoint = string.Format("https://graph.microsoft.com/beta/users/{0}/sendMail", InviterUserPrincipalName);
            Email mail = new Email();
            mail.message = new Message();
            mail.message.Subject = "Inviation Email";
            mail.message.Body = new ItemBody();
            mail.message.Body.Content = serverResponse;
            Recipient recipient = new Recipient();
            recipient.EmailAddress = new EmailAddress();
            recipient.EmailAddress.Address = invitation.InvitedUserEmailAddress;
            mail.recipients = new List<Recipient>();
            mail.recipients.Add(recipient);
            // Make the call to send email.
            content = new StringContent(JsonConvert.SerializeObject(mail));
            Console.WriteLine(content);
            content.Headers.Add("ContentType", "application/json");
            postResponse = httpClient.PostAsync(emailEndPoint, content).Result;
            serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
        }
        /// <summary>
        /// Invitation class.
        /// </summary>
        public class Invitation
        {
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserDisplayName { get; set; }
            /// <summary>
            /// Gets or sets display name.
            /// </summary>
            public string InvitedUserEmailAddress { get; set; }
            /// <summary>
            /// Gets or sets a value indicating whether Invitation Manager should send the email to InvitedUser.
            /// </summary>
            public bool SendInvitationMessage { get; set; }
            /// <summary>
            /// Gets or sets invitation redirect URL
            /// </summary>
            public string InviteRedirectUrl { get; set; }
        }
        /// <summary>
        /// The email message.
        /// </summary>
        public class Email
        {
            public Message message;
            public List<Recipient> recipients;
        }
    }
}
```

## <a name="powershell-example"></a>PowerShell 範例
下列範例將逐步說明如何從您儲存在 .CSV 檔案中的電子郵件地址大量邀請外部使用者。

1. 準備 .CSV 檔案 建立新的 CSV 檔案並將它命名為 invitations.csv。 在此範例中，檔案是儲存在 C:\data。 您的 CSV 檔案看起來可能像下面這樣：

  ```
    InvitedUserEmailAddress
    user1@contoso.com
    user2@outlook.com
    user3@gmail.com
    user4@yahoo.com
    ```

2. 取得最新的 Azure AD PowerShell 若要使用新 Cmdlet，您必須安裝更新的 Azure AD PowerShell 模組 (可從 [Powershell 模組的發行頁面](https://www.powershellgallery.com/packages/AzureADPreview)下載)。

3. 登入您的租用戶

    ```
    Connect-AzureAd and login
    ```

4. 執行 PowerShell Cmdlt

    ```
    $Invitations = import-csv C:\data\invitations.csv
    foreach ($email in $invitations) {New-AzureADMSInvitation -InvitedUserEmailAddress $email.InvitedUserEmailAddress -InviteRedirectUrl http://microsoft.com -SendInvitationMessage $true}
  ```

這會傳送邀請給 invitations.csv 中的電子郵件地址。 此 Cmdlet 的其他功能包括自訂電子郵件訊息中的文字、加入受邀使用者的顯示名稱、傳送訊息給副本收件者，或一併抑制電子郵件訊息。

## <a name="next-steps"></a>後續步驟

請瀏覽有關 Azure AD B2B 共同作業的其他文章：

* [何謂 Azure AD B2B 共同作業？](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 共同作業使用者屬性](active-directory-b2b-user-properties.md)
* [將 B2B 共同作業使用者新增至角色](active-directory-b2b-add-guest-to-role.md)
* [委派 B2B 共同作業邀請](active-directory-b2b-delegate-invitations.md)
* [動態群組與 B2B 共同作業](active-directory-b2b-dynamic-groups.md)
* [設定適用於 B2B 共同作業的 SaaS 應用程式](active-directory-b2b-configure-saas-apps.md)
* [B2B 共同作業使用者權杖](active-directory-b2b-user-token.md)
* [B2B 共同作業使用者宣告對應](active-directory-b2b-claims-mapping.md)
* [Office 365 外部共用](active-directory-b2b-o365-external-user.md)
* [B2B 共同作業目前的限制](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


