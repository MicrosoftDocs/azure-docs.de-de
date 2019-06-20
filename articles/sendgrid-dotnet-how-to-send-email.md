---
title: Verwenden des E-Mail-Diensts SendGrid (.NET) | Microsoft Docs
description: Erfahren Sie, wie Sie E-Mails mit dem SendGrid-E-Mail-Dienst in Azure senden. Die Codebeispiele wurden in C# geschrieben und verwenden die .NET-API.
services: ''
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
ms.openlocfilehash: 91d28802b4af23da5b8060fa7c8f9a7e843a7dab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444878"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Senden von E-Mails in Azure mit SendGrid
## <a name="overview"></a>Übersicht
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C\# geschrieben und unterstützen .NET Standard 1.3. Folgende Szenarien werden beschrieben: Schreiben einer E-Mail, Senden einer E-Mail, Hinzufügen von Anhängen und Aktivieren von verschiedenen Einstellungen für E-Mail und Nachverfolgung. Weitere Informationen zu SendGrid und zum Senden von E-Mails finden Sie im Abschnitt [Nächste Schritte][Next steps].

## <a name="what-is-the-sendgrid-email-service"></a>Was ist der SendGrid-E-Mail-Dienst?
SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Dies sind häufige Anwendungsfälle für SendGrid:

* Automatisches Senden von Empfangs- oder Kaufbestätigungen an Kunden
* Verwalten von Verteilerlisten zum Senden von monatlichen Flyern und Werbeangeboten
* Sammeln von Echtzeitmetriken beispielsweise zu gesperrten E-Mails oder zur Kundenbindung
* Weiterleitung von Kundenanfragen
* Verarbeitung eingehender E-Mails

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com) oder im GitHub-Repository in der [C#-Bibliothek][sendgrid-csharp] für SendGrid.

## <a name="create-a-sendgrid-account"></a>Erstellen eines SendGrid-Kontos
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Verweisen auf die SendGrid .NET-Klassenbibliothek
Der einfachste Weg, die SendGrid-API abzurufen und Ihre Anwendung mit allen Abhängigkeiten zu konfigurieren, ist das [SendGrid NuGet-Paket](https://www.nuget.org/packages/Sendgrid) . NuGet ist eine Visual Studio-Erweiterung, die in Microsoft Visual Studio 2015 und höher enthalten ist und die Installation und Aktualisierung von Bibliotheken und Tools erleichtert.

> [!NOTE]
> Wenn Sie NuGet installieren möchten und eine ältere Version von Visual Studio ausführen als Visual Studio 2015, besuchen Sie [https://www.nuget.org](https://www.nuget.org) und klicken Sie auf die Schaltfläche **Install NuGet** (NuGet installieren).
>
>

Gehen Sie folgendermaßen vor, um das SendGrid NuGet-Paket in Ihrer Anwendung zu installieren:

1. Klicken Sie auf **Neues Projekt**, und wählen Sie eine **Vorlage** aus.

   ![Erstellen eines neuen Projekts][create-new-project]
2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

   ![SendGrid NuGet-Paket][SendGrid-NuGet-package]
3. Suchen Sie nach **SendGrid**, und wählen Sie in der Ergebnisliste das Element **SendGrid** aus.
4. Wählen Sie die letzte stabile Version des NuGet-Pakets aus der Versionsdropdownliste aus, um mit dem Objektmodell und den APIs arbeiten zu können, die in diesem Artikel gezeigt werden.

   ![SendGrid-Paket][sendgrid-package]
5. Klicken Sie auf **Installieren** , um die Installation abzuschließen, und schließen Sie dann das Dialogfeld.

Die .NET-Klassenbibliothek von SendGrid heißt **SendGrid**. Sie enthält die folgenden Namespaces:

* **SendGrid** zur Kommunikation mit der SendGrid-API.
* **SendGrid.Helpers.Mail** für Hilfsmethoden zum einfachen Erstellen von SendGridMessage-Objekten, die angeben, wie E-Mails gesendet werden sollen.

Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf den E-Mail-Dienst SendGrid zugreifen möchten, die folgenden Namespace-Codedeklarationen hinzu:

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Gewusst wie: Erstellen einer E-Mail
Mit dem **SendGridMessage** -Objekt können Sie eine E-Mail-Nachricht erstellen. Nachdem das Nachrichtenobjekt erstellt wurde, können Sie Eigenschaften und Methoden festlegen, beispielsweise den E-Mail-Absender, den E-Mail-Empfänger, den Betreff und den Text der E-Mail.

Das folgende Beispiel zeigt, wie ein vollständig ausgefülltes E-Mail-Objekt erstellt wird:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Weitere Informationen über die vom Typ **SendGrid** unterstützen Eigenschaften und Methoden finden Sie unter [sendgrid-csharp][sendgrid-csharp] auf GitHub.

## <a name="how-to-send-an-email"></a>Gewusst wie: Senden einer E-Mail
Nach der Erstellung einer E-Mail-Nachricht können Sie diese über die SendGrid-API senden. Alternativ können Sie die [integrierte .NET-Bibliothek][NET-library] verwenden.

Beim Senden von E-Mails müssen Sie Ihren SendGrid-API-Schlüssel angeben. Ausführliche Informationen zum Konfigurieren von API-Schlüsseln finden Sie in der [Dokumentation][documentation] zu SendGrid-API-Schlüsseln.

Sie können diese Anmeldeinformationen im Azure-Portal speichern, indem Sie auf „Anwendungseinstellungen“ klicken und die Schlüssel-Wert-Paare unter „App-Einstellungen“ hinzufügen.

 ![App-Einstellungen in Azure][azure_app_settings]

 Anschließend können Sie wie folgt darauf zugreifen:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Die folgenden Beispiele zeigen, wie eine E-Mail-Nachricht unter Verwendung der SendGrid-Web-API mit einer Konsolenanwendung gesendet wird.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>Gewusst wie: Senden einer E-Mail aus einer ASP .NET Core-API mithilfe der MailHelper-Klasse

Das nachstehende Beispiel kann verwendet werden, um aus der ASP .NET Core-API mithilfe der `MailHelper`-Klasse des `SendGrid.Helpers.Mail`-Namespace eine einzelne E-Mail an mehrere Personen zu senden. Für dieses Beispiel wird ASP .NET Core 1.0 verwendet. 

In diesem Beispiel wurde der API-Schlüssel in der Datei `appsettings.json` gespeichert. Dieser kann über das Azure-Portal überschrieben werden, wie in den obigen Beispielen gezeigt.

Die Inhalte der Datei `appsettings.json` sollten in etwa wie folgt aussehen:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Zunächst müssen wir den nachstehenden Code zur Datei `Startup.cs` des .NET Core-API-Projekts hinzufügen. Dies ist erforderlich, damit wir unter Verwendung der Abhängigkeitsinjektion im API-Controller aus der Datei `appsettings.json` auf den `SENDGRID_API_KEY` zugreifen können. Die `IConfiguration`-Schnittstelle kann im Konstruktor des Controllers injiziert werden, nachdem sie in der nachstehenden `ConfigureServices`-Methode hinzugefügt wurde. Der Inhalt der Datei `Startup.cs` sieht nach dem Hinzufügen des erforderlichen Codes in etwa so aus:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Im Controller können wir nach Injektion der `IConfiguration`-Schnittstelle die `CreateSingleEmailToMultipleRecipients`-Methode der `MailHelper`-Klasse dazu verwenden, um eine einzelne E-Mail an mehrere Empfänger zu senden. Die Methode akzeptiert einen zusätzlichen booleschen Parameter namens `showAllRecipients`. Mit diesem Parameter kann gesteuert werden, ob E-Mail-Empfänger die E-Mail-Adressen der weiteren Empfänger im Abschnitt „An“ der E-Mail-Kopfzeile sehen können. Der Beispielcode für den Controller sollte in etwa so aussehen: 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Gewusst wie: Hinzufügen einer Anlage
Sie können einer Nachricht Anhänge hinzufügen, indem Sie die Methode **AddAttachment** aufrufen und mindestens den Dateinamen und den Base64-codierten Inhalt angeben, den Sie anfügen möchten. Sie können mehrere Anhänge anfügen, indem Sie diese Methode einmal für jede Datei aufrufen, die Sie anhängen möchten, oder indem Sie die Methode **AddAttachments** verwenden. Das folgende Beispiel veranschaulicht, wie ein Anhang in einer Nachricht eingefügt wird:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Gewusst wie: Verwenden von E-Mail-Einstellungen zum Aktivieren von Fußzeilen sowie für Nachverfolgung und Analysen
SendGrid bietet zusätzliche E-Mail-Funktionen durch Einstellungen für E-Mail und Nachverfolgung. Diese Einstellungen können zu einer E-Mail-Nachricht hinzugefügt werden, um bestimmte Funktionen wie Klickprotokollierung, Google Analytics, Abonnementnachverfolgung usw. zu aktivieren. Eine vollständige Liste der Apps finden Sie unter [Settings (Filters)][settings-documentation] (Einstellungen [Filter]).

Apps können mithilfe von Methoden, die als Teil der **SendGridMessage**-Klasse implementiert wurden, auf **SendGrid**-E-Mails angewendet werden. Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und die Klickprotokollierung:

Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile und die Klickprotokollierung:

### <a name="footer-settings"></a>Einstellungen für die Fußzeile
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klickprotokollierung
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste
SendGrid bietet verschiedene APIs und Webhooks, die Sie zur Nutzung zusätzlicher Funktionen in Ihrer Azure-Anwendung einsetzen können. Weitere Informationen finden Sie in der [SendGrid API Reference][SendGrid API documentation] (SendGrid-API-Referenz).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

* SendGrid C\#-Bibliotheksrepository: [sendgrid-csharp][sendgrid-csharp]
* SendGrid-API-Dokumentation: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[cloudbasierter E-Mail-Dienst]: https://sendgrid.com/solutions
[transaktionale E-Mail-Übermittlung]: https://sendgrid.com/use-cases/transactional-email

