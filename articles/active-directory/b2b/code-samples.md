---
title: Code für B2B-Zusammenarbeit und PowerShell-Beispiele – Azure Active Directory | Microsoft-Dokumentation
description: Code- und PowerShell-Beispiele für die Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79352c2c82cae8fe77ef6313d74e60432c710ec0
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812507"
---
# <a name="azure-active-directory-b2b-collaboration-code-and-powershell-samples"></a>Azure Active Directory B2B-Zusammenarbeit: Code- und PowerShell-Beispiele

## <a name="powershell-example"></a>PowerShell-Beispiel
Sie können externe Benutzer per Masseneinladung zu einer Organisation einladen. Dabei werden E-Mail-Adressen verwendet, die Sie in einer CSV-Datei gespeichert haben.

1. Bereiten Sie die CSV-Datei vor. Erstellen Sie eine neue CSV-Datei und nennen Sie sie „invitations.csv“. In diesem Beispiel wird die Datei unter „C:\data“ gespeichert, und sie enthält die folgenden Informationen:
  
   NAME                  |  InvitedUserEmailAddress
   --------------------- | --------------------------
   Gmail B2B Invitee     | b2binvitee@gmail.com
   Outlook B2B invitee   | b2binvitee@outlook.com


2. Rufen Sie die neueste Azure AD-PowerShell-Version ab. Um die neuen Cmdlets zu verwenden, müssen Sie das aktualisierte Azure AD-PowerShell-Modul installieren, das Sie von der [Releaseseite des PowerShell-Moduls](https://www.powershellgallery.com/packages/AzureADPreview) herunterladen können.

3. Melden Sie sich bei Ihrem Mandanten an.

    ```powershell
    $cred = Get-Credential
    Connect-AzureAD -Credential $cred
    ```

4. Führen Sie das PowerShell-Cmdlet aus.

   ```powershell
   $invitations = import-csv C:\data\invitations.csv
   $messageInfo = New-Object Microsoft.Open.MSGraph.Model.InvitedUserMessageInfo
   $messageInfo.customizedMessageBody = "Hey there! Check this out. I created an invitation through PowerShell"
   foreach ($email in $invitations) {New-AzureADMSInvitation -InvitedUserEmailAddress $email.InvitedUserEmailAddress -InvitedUserDisplayName $email.Name -InviteRedirectUrl https://wingtiptoysonline-dev-ed.my.salesforce.com -InvitedUserMessageInfo $messageInfo -SendInvitationMessage $true}
   ```

Mit diesem Cmdlet wird eine Einladung an die E-Mail-Adressen in „invitations.csv“ gesendet. Weitere Funktionen dieses Cmdlets:
- Benutzerdefinierter Text in der E-Mail-Nachricht
- Einfügen eines Anzeigenamens für den eingeladenen Benutzer
- Senden von Nachrichten an Benutzer in Kopie oder Unterdrücken sämtlicher E-Mail-Nachrichten

## <a name="code-sample"></a>Codebeispiel
Hier wird veranschaulicht, wie Sie die Einladungs-API im reinen App-Modus aufrufen, um die Einlösungs-URL für die Ressource abzurufen, zu der Sie den B2B-Benutzer einladen möchten. Das Ziel ist es, eine benutzerdefinierte Einladungs-E-Mail zu senden. Die E-Mail kann über einen HTTP-Client zusammengestellt werden, sodass Sie das Aussehen der E-Mail anpassen und sie über die Graph-API senden können.

```csharp
namespace SampleInviteApp
{
    using System;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    class Program
    {
        /// <summary>
        /// Microsoft graph resource.
        /// </summary>
        static readonly string GraphResource = "https://graph.microsoft.com";
 
        /// <summary>
        /// Microsoft graph invite endpoint.
        /// </summary>
        static readonly string InviteEndPoint = "https://graph.microsoft.com/v1.0/invitations";
 
        /// <summary>
        ///  Authentication endpoint to get token.
        /// </summary>
        static readonly string EstsLoginEndpoint = "https://login.microsoftonline.com";
 
        /// <summary>
        /// This is the tenantid of the tenant you want to invite users to.
        /// </summary>
        private static readonly string TenantID = "";
 
        /// <summary>
        /// This is the application id of the application that is registered in the above tenant.
        /// The required scopes are available in the below link.
        /// https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/invitation_post
        /// </summary>
        private static readonly string TestAppClientId = "";
 
        /// <summary>
        /// Client secret of the application.
        /// </summary>
        private static readonly string TestAppClientSecret = @"";
 
        /// <summary>
        /// This is the email address of the user you want to invite.
        /// </summary>
        private static readonly string InvitedUserEmailAddress = @"";
 
        /// <summary>
        /// This is the display name of the user you want to invite.
        /// </summary>
        private static readonly string InvitedUserDisplayName = @"";
 
        /// <summary>
        /// Main method.
        /// </summary>
        /// <param name="args">Optional arguments</param>
        static void Main(string[] args)
        {
            Invitation invitation = CreateInvitation();
            SendInvitation(invitation);
        }
 
        /// <summary>
        /// Create the invitation object.
        /// </summary>
        /// <returns>Returns the invitation object.</returns>
        private static Invitation CreateInvitation()
        {
            // Set the invitation object.
            Invitation invitation = new Invitation();
            invitation.InvitedUserDisplayName = InvitedUserDisplayName;
            invitation.InvitedUserEmailAddress = InvitedUserEmailAddress;
            invitation.InviteRedirectUrl = "https://www.microsoft.com";
            invitation.SendInvitationMessage = true;
            return invitation;
        }
 
        /// <summary>
        /// Send the guest user invite request.
        /// </summary>
        /// <param name="invitation">Invitation object.</param>
        private static void SendInvitation(Invitation invitation)
        {
            string accessToken = GetAccessToken();
 
            HttpClient httpClient = GetHttpClient(accessToken);
 
            // Make the invite call. 
            HttpContent content = new StringContent(JsonConvert.SerializeObject(invitation));
            content.Headers.Add("ContentType", "application/json");
            var postResponse = httpClient.PostAsync(InviteEndPoint, content).Result;
            string serverResponse = postResponse.Content.ReadAsStringAsync().Result;
            Console.WriteLine(serverResponse);
        }
 
        /// <summary>
        /// Get the HTTP client.
        /// </summary>
        /// <param name="accessToken">Access token</param>
        /// <returns>Returns the Http Client.</returns>
        private static HttpClient GetHttpClient(string accessToken)
        {
            // setup http client.
            HttpClient httpClient = new HttpClient();
            httpClient.Timeout = TimeSpan.FromSeconds(300);
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
            httpClient.DefaultRequestHeaders.Add("client-request-id", Guid.NewGuid().ToString());
            Console.WriteLine(
                "CorrelationID for the request: {0}",
                httpClient.DefaultRequestHeaders.GetValues("client-request-id").Single());
            return httpClient;
        }
 
        /// <summary>
        /// Get the access token for our application to talk to microsoft graph.
        /// </summary>
        /// <returns>Returns the access token for our application to talk to microsoft graph.</returns>
        private static string GetAccessToken()
        {
            string accessToken = null;
 
            // Get the access token for our application to talk to microsoft graph.
            try
            {
                AuthenticationContext testAuthContext =
                    new AuthenticationContext(string.Format("{0}/{1}", EstsLoginEndpoint, TenantID));
                AuthenticationResult testAuthResult = testAuthContext.AcquireTokenAsync(
                    GraphResource,
                    new ClientCredential(TestAppClientId, TestAppClientSecret)).Result;
                accessToken = testAuthResult.AccessToken;
            }
            catch (AdalException ex)
            {
                Console.WriteLine("An exception was thrown while fetching the token: {0}.", ex);
                throw;
            }
 
            return accessToken;
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
    }
}
```


## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)

