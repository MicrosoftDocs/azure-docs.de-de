---
title: Tätigen eines Telefonanrufs über Twilio (.NET) | Microsoft Docs
description: Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen. In .NET geschriebene Codebeispiele.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521120"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure
In diesem Leitfaden wird veranschaulicht, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die resultierende Anwendung fordert den Benutzer auf, einen Anruf mit der angegebenen Nummer und Nachricht zu tätigen, wie im folgenden Screenshot dargestellt.

![Azure-Anrufformular mit Twilio und ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Voraussetzungen
Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Erwerben Sie ein Twilio-Konto, und rufen Sie ein Authentifizierungstoken bei der [Twilio-Konsole][twilio_console] ab. Für die ersten Schritte mit Twilio registrieren Sie sich unter [https://www.twilio.com/try-twilio][try_twilio]. Sie können die Preise unter [https://www.twilio.com/pricing][twilio_pricing] einsehen. Informationen zu der von Twilio bereitgestellten API finden Sie unter [https://www.twilio.com/voice/api][twilio_api].
2. Fügen Sie Ihrer Webrolle die *Twilio .NET-Bibliothek* hinzu. Siehe **Hinzufügen der Twilio-Bibliotheken zu Ihrem Webrollen-Projekt** weiter unten in diesem Artikel.

Sie sollten mit der Erstellung einer einfachen [Webrolle in Azure][azure_webroles_get_started] vertraut sein.

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Erstellen eines Webformulars für einen Anruf
<a id="use_nuget"></a>So fügen Sie dem Webrollenprojekt die Twilio-Bibliotheken hinzu:

1. Öffnen Sie Ihre Projektmappe in Visual Studio.
2. Klicken Sie mit der rechten Maustaste auf **References**.
3. Klicken Sie auf **Manage NuGet Packages**.
4. Klicken Sie auf **Online**.
5. Geben Sie in das Onlinesuchfeld *twilio* ein.
6. Klicken Sie auf **Install** für das Twilio-Paket.

Der folgende Code erstellt ein Web-Formular zur Eingabe der Benutzerdaten für den Anruf. In diesem Beispiel wird eine ASP.NET-Webrolle mit dem Namen **TwilioCloud** erstellt.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Erstellen des Codes für den Anruf
Der folgende Code wird aufgerufen wenn der Benutzer das Formular abschließt und generiert die Anrufnachricht und führt den Anruf aus. In diesem Beispiel wird der Code im oncklick-Ereignishandler der Schaltfläche im Formular ausgeführt. (Ersetzen Sie die Platzhalterwerte `accountSID` und `authToken` im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

Der Anruf wird ausgeführt, und der Twilio-Endpunkt, die API-Version und der Anrufstatus werden angezeigt. Der folgende Screenshot zeigt die Ausgabe bei einer Beispielausführung.

![Azure-Anrufantwort mit Twilio und ASP.NET][twilio_dotnet_basic_form_output]

Weitere Informationen zu TwiML finden Sie unter [https://www.twilio.com/docs/api/twiml][twiml]. Weitere Informationen zu &lt;Say&gt; und anderen Twilio-Verben finden Sie unter [https://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a name="next-steps"></a><a id="nextsteps"></a>Nächste Schritte
Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio in einer ASP.NET-Webrolle in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

* Anstelle eines Web-Formulars könnten Sie Azure Blob Storage oder eine Azure SQL-Datenbank-Instanz zum Speichern von Telefonnummern und Anruftexten verwenden. Informationen zur Verwendung von Blobs in Azure finden Sie unter [Verwenden des Azure Blob Storage-Diensts in .NET][howto_blob_storage_dotnet]. Informationen zur Verwendung von SQL-Datenbank finden Sie unter [Verwenden von Azure SQL-Datenbank in .NET-Anwendungen][howto_sql_azure_dotnet].
* Sie können `RoleEnvironment.getConfigurationSettings` verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, statt diese Werte fest in Ihr Formular einzuprogrammieren. Informationen zur `RoleEnvironment`-Klasse finden Sie unter [Microsoft.WindowsAzure.ServiceRuntime-Namespace][azure_runtime_ref_dotnet].
* Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security][twilio_docs_security].
* Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a><a name="seealso"></a>Weitere Informationen
* [Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md