---
title: 'Schnellstart: C#-Clientbibliothek für den plastischen Reader'
titleSuffix: Azure Cognitive Services
description: In dieser Schnellstartanleitung erstellen Sie eine Web-App von Grund auf neu und fügen die Funktion der API für den plastischen Reader hinzu.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cef5aaae58797e38745b3f5164c171581a005562
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371964"
---
[Plastischer Reader](https://www.onenote.com/learningtools) ist ein inklusiv konzipiertes Tool, das bewährte Techniken implementiert, um das Leseverständnis von Leseanfängern, Sprachenlernenden und Personen mit Lernunterschieden, wie z. B. Dyslexie, zu verbessern. Sie können den plastischen Reader in Ihren Anwendungen verwenden, um Text zu isolieren, den Fokus zu verbessern, Bilder für häufig verwendete Wörter anzuzeigen, Wortarten hervorzuheben, markierten Text laut vorzulesen, Wörter und Ausdrücke in Echtzeit zu übersetzen und vieles mehr.

In dieser Schnellstartanleitung erstellen Sie eine Web-App von Grund auf neu und integrieren den plastischen Reader mithilfe der Clientbibliothek für den plastischen Reader. Ein vollständiges Arbeitsbeispiel für diesen Schnellstart finden Sie auf [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](../../how-to-create-immersive-reader.md) für die Einrichtung. Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Beispielprojekteigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.

## <a name="create-a-web-app-project"></a>Erstellen eines Web-App-Projekts

Erstellen Sie mit der Vorlage für ASP.NET Core-Webanwendungen mit dem integrierten Model View Controller und ASP.NeT Core 2.1 ein neues Projekt in Visual Studio. Geben Sie dem Projekt den Namen „QuickstartSampleWebApp“.

![Neues Projekt: C#](../../media/quickstart-csharp/1-createproject.png)

![Konfigurieren des neuen Projekts: C#](../../media/quickstart-csharp/2-configureproject.png)

![Neue ASP.NET Core-Webanwendung: C#](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

### <a name="configure-authentication-values"></a>Konfigurieren der Authentifizierungswerte

Klicken Sie im _Projektmappen-Explorer_ mit der rechten Maustaste auf das Projekt, und wählen Sie anschließend **Geheimen Benutzerschlüssel verwalten** aus. Damit wird eine Datei namens _secrets.json_ geöffnet. Diese Datei ist nicht in die Quellcodeverwaltung eingecheckt. [Hier](/aspnet/core/security/app-secrets?preserve-view=true&tabs=windows&view=aspnetcore-3.1)erhalten Sie weitere Informationen. Ersetzen Sie den Inhalt der Datei _secrets.json_ durch Folgendes. Verwenden Sie dabei die Werte, die beim Erstellen der Ressource des plastischen Readers angegeben wurden.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Installieren des Active Directory-NuGet-Pakets

Der folgende Code verwendet Objekte aus dem NuGet-Paket **Microsoft.IdentityModel.Clients.ActiveDirectory** , sodass Sie einen Verweis auf dieses Paket in Ihrem Projekt hinzufügen müssen.

Öffnen Sie die NuGet-Paket-Manager-Konsole über **Tools > NuGet-Paket-Manager > Paket-Manager-Konsole** , und führen Sie den folgenden Befehl aus:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Aktualisieren des Controllers zum Abrufen des Tokens 

Öffnen Sie _Controllers\HomeController.cs_ , und fügen Sie nach den _using_ -Anweisungen am Anfang der Datei den folgenden Code ein:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Anschließend konfigurieren Sie den Controller zum Abrufen der Azure AD-Werte aus _secrets.json_. Fügen Sie am Anfang der Klasse _HomeController_ nach ```public class HomeController : Controller {``` den folgenden Code ein:

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Hinzufügen von Beispielinhalten
Öffnen Sie zunächst _Views\Shared\Layout.cshtml_. Fügen Sie vor der Zeile ```</head>``` den folgenden Code hinzu:

```html
@RenderSection("Styles", required: false)
```

Nun fügen Sie dieser Web-App Beispielinhalte hinzu. Öffnen Sie _Views\Home\Index.cshtml_ , und ersetzen Sie den gesamten automatisch generierten Code durch dieses Beispiel:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Beachten Sie, dass der gesamte Text über das Attribut **lang** verfügt, das die Sprachen des Texts beschreibt. Dieses Attribut unterstützt den plastischen Reader dabei, relevante Sprach- und Grammatikfeatures bereitzustellen.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>Hinzufügen von JavaScript zum Behandeln des Startens des plastischen Readers

Die Bibliothek des plastischen Readers stellt unter anderem Funktionen zum Starten des plastischen Readers sowie zum Rendern von Schaltflächen des plastischen Readers bereit. [Hier](../../reference.md)erhalten Sie weitere Informationen.

Fügen Sie am Ende von _Views\Home\Index.cshtml_ den folgenden Code hinzu:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

Wählen Sie auf der Menüleiste **Debuggen > Debuggen starten** aus, oder drücken Sie **F5** , um die Anwendung zu starten.

In Ihrem Browser sollte Folgendes angezeigt werden:

![Beispiel-App: C#](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Starten des plastischen Readers

Wenn Sie auf die Schaltfläche „Plastischer Reader“ klicken, sehen Sie, dass der plastische Reader mit dem Inhalt auf der Seite gestartet wurde.

![Plastischer Reader: C#](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit dem [SDK für Plastischer Reader](https://github.com/microsoft/immersive-reader-sdk) und der [zugehörigen Referenz](../../reference.md) vertraut.