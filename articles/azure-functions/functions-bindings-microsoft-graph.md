---
title: Microsoft Graph-Bindungen für Azure Functions
description: Erfahren Sie, wie Microsoft Graph-Trigger und -Bindungen in Azure Functions verwendet werden.
services: functions
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: f112bdf9eacf51852659ab49a5673b0c8bfb0e46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64511813"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph-Bindungen für Azure Functions

Dieser Artikel erläutert das Konfigurieren von und Arbeiten mit Microsoft Graph-Triggern und -Bindungen in Azure Functions. Mit diesen können Sie mithilfe von Azure Functions Daten, Einblicke und Ereignisse aus [Microsoft Graph](https://developer.microsoft.com/graph) bearbeiten.

Die Microsoft Graph-Erweiterung bietet die folgenden Bindungen:
- Eine [Autorisierungstoken-Eingabebindung](#token-input) ermöglicht Ihnen die Interaktion mit jeder Microsoft Graph-API.
- Eine [Excel-Tabelleneingabebindung](#excel-input) ermöglicht Ihnen, Daten aus Excel zu lesen.
- Eine [Excel-Tabellenausgabebindung](#excel-output) ermöglicht Ihnen, Excel-Daten zu ändern.
- Eine [OneDrive-Dateieingabebindung](#onedrive-input) ermöglicht Ihnen, Dateien von OneDrive zu lesen.
- Eine [OneDrive-Dateiausgabebindung](#onedrive-output) ermöglicht Ihnen, Dateien in OneDrive zu schreiben.
- Eine [Outlook-Nachrichtenausgabebindung](#outlook-output) ermöglicht Ihnen, E-Mails über Outlook zu senden.
- Mit einer Sammlung von [Microsoft Graph-Webhooktriggern und -bindungen](#webhooks) können Sie auf Ereignisse aus Microsoft Graph reagieren.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph-Bindungen befinden sich für Azure Functions Version 2.x derzeit in der Vorschauphase. In Functions Version 1.x werden sie nicht unterstützt.

## <a name="packages"></a>Pakete

Die Authentifizierungstoken-Eingabebindung wird im NuGet-Paket [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) bereitgestellt. Die anderen Microsoft Graph-Bindungen werden im Paket [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) bereitgestellt. Den Quellcode für die Pakete finden Sie im GitHub-Repository [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Einrichten der Erweiterungen

Microsoft Graph-Bindungen sind über _Bindungserweiterungen_ verfügbar. Bindungserweiterungen sind optionale Komponenten für die Azure Functions-Runtime. In diesem Abschnitt wird gezeigt, wie die Microsoft Graph- und die Authentifizierungstoken-Erweiterung eingerichtet werden.

### <a name="enabling-functions-20-preview"></a>Aktivieren von Functions 2.0 Preview

Bindungserweiterungen sind nur für Azure Functions 2.0 Preview verfügbar. 

Informationen dazu, wie Sie eine Functions-App so einrichten, dass sie die 2.0-Vorschauversion der Functions Runtime verwendet, finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](set-runtime-version.md).

### <a name="installing-the-extension"></a>Installieren der Erweiterung

Um eine Erweiterung über das Azure-Portal zu installieren, navigieren Sie zu einer Vorlage oder Bindung, die auf die Erweiterung verweist. Erstellen Sie eine neue Funktion, und wählen Sie auf dem Bildschirm für die Vorlagenauswahl das Szenario „Microsoft Graph“ aus. Wählen Sie eine der Vorlagen in diesem Szenario aus. Alternativ können Sie zur Registerkarte „Integration“ einer vorhandenen Funktion navigieren und eine der in diesem Artikel behandelten Bindungen auswählen.

In beiden Fällen wird eine Warnung angezeigt, die die zu installierende Erweiterung angibt. Klicken Sie auf **Installieren**, um die Erweiterung zu erhalten. Jede Erweiterung muss nur einmal pro Funktions-App installiert werden. 

> [!Note] 
> Der Installationsvorgang im Portal kann bis zu 10 Minuten eines Verbrauchstarifs aufbrauchen.

Bei Verwendung von Visual Studio können Sie die Erweiterungen abrufen, indem Sie [die weiter oben in diesem Artikel aufgeführten NuGet-Pakete](#packages) installieren.

### <a name="configuring-authentication--authorization"></a>Konfigurieren von Authentifizierung/Autorisierung

Für die in diesem Artikel beschriebenen Bindungen muss eine Identität verwendet werden. Dies ermöglicht Microsoft Graph, Berechtigungen zu erzwingen und Interaktionen zu überwachen. Die Identität kann ein Benutzer sein, der auf Ihre Anwendung zugreift, oder die Anwendung selbst. Um diese Identität zu konfigurieren, richten Sie [App Service-Authentifizierung/-Autorisierung](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) mit Azure Active Directory ein. Sie müssen auch alle Ressourcenberechtigungen anfordern, die in Ihren Funktionen benötigt werden.

> [!Note] 
> Die Microsoft Graph-Erweiterung unterstützt nur die Azure AD-Authentifizierung. Benutzer müssen sich mit einem Geschäfts-, Schul- oder Unikonto anmelden.

Wenn Sie das Azure-Portal verwenden, wird unter der Eingabeaufforderung zum Installieren der Erweiterung eine Warnung angezeigt. In der Warnung werden Sie aufgefordert, App Service-Authentifizierung/-Autorisierung zu konfigurieren und alle Berechtigungen anzufordern, die für die Vorlage oder Bindung erforderlich sind. Klicken Sie nach Bedarf auf **Azure AD jetzt konfigurieren** oder **Jetzt Berechtigungen hinzufügen**.



<a name="token-input"></a>
## <a name="auth-token"></a>Authentifizierungstoken

Die Authentifizierungstoken-Eingabebindung ruft ein Azure AD-Token für eine bestimmte Ressource ab und stellt es Ihrem Code als Zeichenfolge zur Verfügung. Bei der Ressource kann es sich um jede handeln, für die die Anwendung über Berechtigungen verfügt. 

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#auth-token---example)
* [Attribute](#auth-token---attributes)
* [Konfiguration](#auth-token---configuration)
* [Verwendung](#auth-token---usage)

### <a name="auth-token---example"></a>Authentifizierungstoken: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Authentifizierungstoken: C#-Skriptbeispiel

Im folgenden Beispiel werden Benutzerprofilinformationen abgerufen.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Tokeneingabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im C#-Skriptcode wird das Token verwendet, um einen HTTP-Aufruf zu Microsoft Graph auszuführen und das Ergebnis zurückzugeben:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Authentifizierungstoken: JavaScript-Beispiel

Im folgenden Beispiel werden Benutzerprofilinformationen abgerufen.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Tokeneingabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im JavaScript-Code wird das Token verwendet, um einen HTTP-Aufruf zu Microsoft Graph auszuführen und das Ergebnis zurückzugeben.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Authentifizierungstoken: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs).

### <a name="auth-token---configuration"></a>Authentifizierungstoken: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Token` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für das Autorisierungstoken verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Autorisierungstoken-Eingabebindung in Code](#token-input-code).|
|**type**||Erforderlich – muss auf `token` festgelegt sein.|
|**direction**||Erforderlich – muss auf `in` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**userId**|**UserId**  |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**Ressource**|**resource**|Erforderlich – eine Azure AD-Ressourcen-URL, für die das Token angefordert wird.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Authentifizierungstoken: Nutzung

Die Bindung selbst erfordert keine Azure AD-Berechtigungen, aber je nach Verwendung des Tokens müssen Sie möglicherweise zusätzliche Berechtigungen anfordern. Überprüfen Sie die Anforderungen der Ressource, auf die Sie mit dem Token zugreifen möchten.

In Code wird das Token immer als Zeichenfolge angegeben.

> [!Note]
> Bei der lokalen Entwicklung mit der Option `userFromId`, `userFromToken` oder`userFromRequest` kann das erforderliche Token [manuell abgerufen](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) und im Anforderungsheader `X-MS-TOKEN-AAD-ID-TOKEN` von einer aufrufenden Clientanwendung angegeben werden.


<a name="excel-input"></a>
## <a name="excel-input"></a>Excel-Eingabe

Diese Eingabebindung für eine Excel-Tabelle liest den Inhalt einer Excel-Tabelle, die in OneDrive gespeichert ist.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#excel-input---example)
* [Attribute](#excel-input---attributes)
* [Konfiguration](#excel-input---configuration)
* [Verwendung](#excel-input---usage)

### <a name="excel-input---example"></a>Excel-Eingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel-Eingabe: C#-Skriptbeispiel

In der folgenden *function.json*-Datei ist ein HTTP-Trigger mit einer Excel-Eingabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im folgenden C#-Skriptcode wird der Inhalt der angegebenen Tabelle gelesen und an den Benutzer zurückgegeben:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel-Eingabe: JavaScript-Beispiel

In der folgenden *function.json*-Datei ist ein HTTP-Trigger mit einer Excel-Eingabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im folgenden JavaScript-Code wird der Inhalt der angegebenen Tabelle gelesen und an den Benutzer zurückgegeben.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel-Eingabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-input---configuration"></a>Excel-Eingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Excel` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die Excel-Tabelle verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Excel-Tabelleneingabebindung in Code](#excel-input-code).|
|**type**||Erforderlich – muss auf `excel` festgelegt sein.|
|**direction**||Erforderlich – muss auf `in` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**userId**|**UserId**  |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**path**|**Path**|Erforderlich – der Pfad in OneDrive zur Excel-Arbeitsmappe.|
|**worksheetName**|**WorksheetName**|Das Arbeitsblatt, in dem die Tabelle zu finden ist.|
|**tableName**|**TableName**|Der Name der Tabelle. Wenn nicht angegeben, wird der Inhalt des Arbeitsblatts verwendet.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel-Eingabe: Nutzung

Diese Bindung erfordert folgende Azure AD-Berechtigungen:

|Resource|Berechtigung|
|--------|--------|
|Microsoft Graph|Benutzerdateien lesen|

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- string[][]
- Microsoft.Graph.WorkbookTable
- Benutzerdefinierte Objekttypen (unter Verwendung von struktureller Modellbindung)










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel-Ausgabe

Diese Excel-Ausgabebindung ändert den Inhalt einer Excel-Tabelle, die in OneDrive gespeichert ist.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#excel-output---example)
* [Attribute](#excel-output---attributes)
* [Konfiguration](#excel-output---configuration)
* [Verwendung](#excel-output---usage)

### <a name="excel-output---example"></a>Excel-Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel-Ausgabe: C#-Skriptbeispiel

Im folgende Beispiel werden einer Excel-Tabelle Zeilen hinzugefügt.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Excel-Ausgabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im folgenden C#-Skriptcode wird der Tabelle (die als einspaltig angenommen wird) anhand einer Eingabe aus der Abfragezeichenfolge eine neue Zeile hinzugefügt:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Excel-Ausgabe: JavaScript-Beispiel

Im folgende Beispiel werden einer Excel-Tabelle Zeilen hinzugefügt.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Excel-Ausgabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im folgenden JavaScript-Code wird der Tabelle (die als einspaltig angenommen wird) anhand einer Eingabe aus der Abfragezeichenfolge eine neue Zeile hinzugefügt.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel-Ausgabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs).

### <a name="excel-output---configuration"></a>Excel-Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Excel` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für das Autorisierungstoken verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Excel-Tabellenausgabebindung in Code](#excel-output-code).|
|**type**||Erforderlich – muss auf `excel` festgelegt sein.|
|**direction**||Erforderlich – muss auf `out` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**UserId** |**userId** |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**path**|**Path**|Erforderlich – der Pfad in OneDrive zur Excel-Arbeitsmappe.|
|**worksheetName**|**WorksheetName**|Das Arbeitsblatt, in dem die Tabelle zu finden ist.|
|**tableName**|**TableName**|Der Name der Tabelle. Wenn nicht angegeben, wird der Inhalt des Arbeitsblatts verwendet.|
|**updateType**|**UpdateType**|Erforderlich – die Art der Änderung, die an der Tabelle vorgenommen werden soll. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>update</code> – ersetzt den Inhalt der Tabelle in OneDrive.</li><li><code>append</code> – fügt die Nutzlast durch Erstellen neuer Zeilen am Ende der Tabelle in OneDrive hinzu.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel-Ausgabe: Nutzung

Diese Bindung erfordert folgende Azure AD-Berechtigungen:

|Resource|Berechtigung|
|--------|--------|
|Microsoft Graph|Vollzugriff auf Benutzerdateien|

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- Benutzerdefinierte Objekttypen (unter Verwendung von struktureller Modellbindung)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Dateieingabe

Die OneDrive-Dateieingabebindung liest den Inhalt einer in OneDrive gespeicherten Datei.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#file-input---example)
* [Attribute](#file-input---attributes)
* [Konfiguration](#file-input---configuration)
* [Verwendung](#file-input---usage)

### <a name="file-input---example"></a>Dateieingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Dateieingabe: C#-Skriptbeispiel

Im folgenden Beispiel wird eine Datei gelesen, die in OneDrive gespeichert ist.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer OneDrive-Dateieingabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im C#-Scriptcode wird die in der Abfragezeichenfolge angegebene Datei gelesen und ihre Länge protokolliert:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Dateieingabe: JavaScript-Beispiel

Im folgenden Beispiel wird eine Datei gelesen, die in OneDrive gespeichert ist.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer OneDrive-Dateieingabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Im folgenden JavaScript-Code wird die in der Abfragezeichenfolge angegebene Datei gelesen und ihre Länge zurückgegeben.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Dateieingabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-input---configuration"></a>Dateieingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `OneDrive` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die Datei verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer OneDrive-Dateieingabebindung in Code](#onedrive-input-code).|
|**type**||Erforderlich – muss auf `onedrive` festgelegt sein.|
|**direction**||Erforderlich – muss auf `in` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**userId**|**UserId**  |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**path**|**Path**|Erforderlich – der Pfad in OneDrive zur Datei.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Dateieingabe: Nutzung

Diese Bindung erfordert folgende Azure AD-Berechtigungen:

|Resource|Berechtigung|
|--------|--------|
|Microsoft Graph|Benutzerdateien lesen|

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- Byte[]
- Datenstrom
- Zeichenfolge
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Dateiausgabe

Die OneDrive-Dateiausgabebindung ändert den Inhalt einer in OneDrive gespeicherten Datei.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#file-output---example)
* [Attribute](#file-output---attributes)
* [Konfiguration](#file-output---configuration)
* [Verwendung](#file-output---usage)

### <a name="file-output---example"></a>Dateiausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Dateiausgabe: C#-Skriptbeispiel

Im folgenden Beispiel wird eine Datei gelesen, die in OneDrive gespeichert ist.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer OneDrive-Dateiausgabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode ruft Text aus der Abfragezeichenfolge ab und schreibt diesen in eine Textdatei („FunctionsTest.txt“ gemäß Definition im vorherigen Beispiel) im Stammverzeichnis des OneDrive des Aufrufers:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Dateiausgabe: JavaScript-Beispiel

Im folgenden Beispiel wird eine Datei gelesen, die in OneDrive gespeichert ist.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer OneDrive-Dateiausgabebindung definiert:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code ruft Text aus der Abfragezeichenfolge ab und schreibt diesen in eine Textdatei („FunctionsTest.txt“ gemäß Definition in der Konfiguration oben) im Stammverzeichnis des OneDrive des Aufrufers.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Dateiausgabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs).

### <a name="file-output---configuration"></a>Dateiausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `OneDrive` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die Datei verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer OneDrive-Dateiausgabebindung in Code](#onedrive-output-code).|
|**type**||Erforderlich – muss auf `onedrive` festgelegt sein.|
|**direction**||Erforderlich – muss auf `out` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**UserId** |**userId** |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**path**|**Path**|Erforderlich – der Pfad in OneDrive zur Datei.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Dateiausgabe: Nutzung

Diese Bindung erfordert folgende Azure AD-Berechtigungen:

|Resource|Berechtigung|
|--------|--------|
|Microsoft Graph|Vollzugriff auf Benutzerdateien|

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- Byte[]
- Datenstrom
- Zeichenfolge
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook-Ausgabe

Die Outlook-Nachrichtenausgabebindung sendet eine E-Mail-Nachricht über Outlook.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#outlook-output---example)
* [Attribute](#outlook-output---attributes)
* [Konfiguration](#outlook-output---configuration)
* [Verwendung](#outlook-output---usage)

### <a name="outlook-output---example"></a>Outlook-Ausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook-Ausgabe: C#-Skriptbeispiel

Im folgenden Beispiel wird eine E-Mail über Outlook gesendet.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Outlook-Nachrichtenausgabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Im C#-Skriptcode wird eine E-Mail vom Aufrufer an einen in der Abfragezeichenfolge angegebenen Empfänger gesendet:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Outlook-Ausgabe: JavaScript-Beispiel

Im folgenden Beispiel wird eine E-Mail über Outlook gesendet.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Outlook-Nachrichtenausgabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Im JavaScript-Code wird eine E-Mail vom Aufrufer an einen in der Abfragezeichenfolge angegebenen Empfänger gesendet:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Outlook-Ausgabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs).

### <a name="outlook-output---configuration"></a>Outlook-Ausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `Outlook` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die E-Mail-Nachricht verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Outlook-Nachrichtenausgabebindung in Code](#outlook-output-code).|
|**type**||Erforderlich – muss auf `outlook` festgelegt sein.|
|**direction**||Erforderlich – muss auf `out` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**userId**|**UserId**  |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook-Ausgabe: Nutzung

Diese Bindung erfordert folgende Azure AD-Berechtigungen:

|Resource|Berechtigung|
|--------|--------|
|Microsoft Graph|E-Mail als Benutzer senden|

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- Zeichenfolge
- Benutzerdefinierte Objekttypen (unter Verwendung von struktureller Modellbindung)






## <a name="webhooks"></a>Webhooks

Mit Webhooks können Sie auf Ereignisse in Microsoft Graph reagieren. Zur Unterstützung von Webhooks sind Funktionen erforderlich, die _Webhookabonnements_ erstellen, aktualisieren und auf diese reagieren. Für eine vollständige Webhooklösung ist eine Kombination aus den folgenden Bindungen erforderlich:
- Ein [Microsoft Graph-Webhooktrigger](#webhook-trigger) ermöglicht es Ihnen, auf einen eingehenden Webhook zu reagieren.
- Eine [Microsoft Graph-Webhookabonnement-Eingabebindung](#webhook-input) ermöglicht Ihnen das Auflisten und optionale Aktualisieren von vorhandenen Abonnements.
- Eine [Microsoft Graph-Webhookabonnement-Ausgabebindung](#webhook-output) ermöglicht Ihnen das Erstellen und Löschen von Webhookabonnements.

Für die eigentlichen Bindungen sind keine Azure AD-Berechtigungen erforderlich, aber Sie benötigen die entsprechenden Berechtigungen für den Ressourcentyp, auf den Sie reagieren möchten. Eine Liste der für jeden Ressourcentyp erforderlichen Berechtigungen finden Sie unter [Abonnementberechtigungen](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Weitere Informationen über Webhooks finden Sie unter [Arbeiten mit Webhooks in Microsoft Graph].





## <a name="webhook-trigger"></a>Webhooktrigger

Der Microsoft Graph-Webhooktrigger ermöglicht es einer Funktion, auf einen eingehenden Webhook von Microsoft Graph zu reagieren. Jede Instanz dieses Triggers kann auf einen Microsoft Graph Ressourcentyp reagieren.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#webhook-trigger---example)
* [Attribute](#webhook-trigger---attributes)
* [Konfiguration](#webhook-trigger---configuration)
* [Verwendung](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhooktrigger: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhooktrigger: C#-Skriptbeispiel

Im folgenden Beispiel werden Webhooks für eingehende Outlook-Nachrichten verarbeitet. Um einen Webhooktrigger zu verwenden, [erstellen Sie ein Abonnement](#webhook-output---example), und Sie können [das Abonnement aktualisieren ](#webhook-subscription-refresh), um zu verhindern, dass es abläuft.

In der *function.json*-Datei ist ein Webhooktrigger definiert:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

In dem C#-Skriptcode wird auf eingehende E-Mail-Nachrichten reagiert und werden die Texte der Nachrichten protokolliert, die der Empfänger gesendet hat und die „Azure Functions“ in der Betreffzeile enthalten:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Webhooktrigger: JavaScript-Beispiel

Im folgenden Beispiel werden Webhooks für eingehende Outlook-Nachrichten verarbeitet. Um einen Webhooktrigger zu verwenden, [erstellen Sie ein Abonnement](#webhook-output---example), und Sie können [das Abonnement aktualisieren ](#webhook-subscription-refresh), um zu verhindern, dass es abläuft.

In der *function.json*-Datei ist ein Webhooktrigger definiert:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

In dem JavaScript-Code wird auf eingehende E-Mail-Nachrichten reagiert und werden die Texte der Nachrichten protokolliert, die der Empfänger gesendet hat und die „Azure Functions“ in der Betreffzeile enthalten:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Webhooktrigger: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs).

### <a name="webhook-trigger---configuration"></a>Webhooktrigger: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `GraphWebHookTrigger` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die E-Mail-Nachricht verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Outlook-Nachrichtenausgabebindung in Code](#outlook-output-code).|
|**type**||Erforderlich – muss auf `graphWebhook` festgelegt sein.|
|**direction**||Erforderlich – muss auf `trigger` festgelegt sein.|
|**resourceType**|**ResourceType**|Erforderlich – die Graph-Ressource, für die diese Funktion auf Webhooks reagieren soll. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>#Microsoft.Graph.Message</code> – Änderungen an Outlook-Nachrichten.</li><li><code>#Microsoft.Graph.DriveItem</code> – Änderungen an OneDrive-Stammelementen.</li><li><code>#Microsoft.Graph.Contact</code> – Änderungen an persönlichen Kontakten in Outlook.</li><li><code>#Microsoft.Graph.Event</code> – Änderungen an Outlook-Kalenderelementen.</li></ul>|

> [!Note]
> Eine Funktions-App kann nur eine Funktion haben, die für einen bestimmten `resourceType`-Wert registriert ist.

### <a name="webhook-trigger---usage"></a>Webhooktrigger: Nutzung

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- Microsoft Graph SDK-Typen, die für den Ressourcentyp relevant sind, etwa `Microsoft.Graph.Message` oder `Microsoft.Graph.DriveItem`.
- Benutzerdefinierte Objekttypen (unter Verwendung von struktureller Modellbindung)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhookeingabe

Mit der Microsoft Graph-Webhookeingabebindung können Sie die Liste der von dieser Funktions-App verwalteten Abonnements abrufen. Die Bindung liest aus dem Speicher der Funktions-App, sodass sie keine anderen Abonnements betrifft, die außerhalb der App erstellt wurden.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#webhook-input---example)
* [Attribute](#webhook-input---attributes)
* [Konfiguration](#webhook-input---configuration)
* [Verwendung](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhookeingabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhookeingabe: C#-Skriptbeispiel

Im folgenden Beispiel werden alle Abonnements für den aufrufenden Benutzer abgerufen und danach gelöscht.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Abonnementeingabebindung und einer Abonnementausgabebindung definiert, in der die delete-Aktion verwendet wird:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode ruft die Abonnements ab und löscht sie:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Webhookeingabe: JavaScript-Beispiel

Im folgenden Beispiel werden alle Abonnements für den aufrufenden Benutzer abgerufen und danach gelöscht.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Abonnementeingabebindung und einer Abonnementausgabebindung definiert, in der die delete-Aktion verwendet wird:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code ruft die Abonnements ab und löscht sie:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Webhookeingabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-input---configuration"></a>Webhookeingabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `GraphWebHookSubscription` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die E-Mail-Nachricht verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Outlook-Nachrichtenausgabebindung in Code](#outlook-output-code).|
|**type**||Erforderlich – muss auf `graphWebhookSubscription` festgelegt sein.|
|**direction**||Erforderlich – muss auf `in` festgelegt sein.|
|**filter**|**Filter**| Bei Festlegung auf `userFromRequest` ruft die Bindung nur Abonnements im Besitz des aufrufenden Benutzers ab (nur gültig mit [HTTP-Trigger]).| 

### <a name="webhook-input---usage"></a>Webhookeingabe: Nutzung

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- string[]
- Benutzerdefinierte Objekttyparrays
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhookausgabe

Mit der Webhookabonnement-Ausgabebindung können Sie Webhookabonnements in Microsoft Graph erstellen, löschen und aktualisieren.

Dieser Abschnitt enthält die folgenden Unterabschnitte:

* [Beispiel](#webhook-output---example)
* [Attribute](#webhook-output---attributes)
* [Konfiguration](#webhook-output---configuration)
* [Verwendung](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhookausgabe: Beispiel

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhookausgabe: C#-Skriptbeispiel

Im folgenden Beispiel wird ein Abonnement erstellt. Sie können [das Abonnement aktualisieren](#webhook-subscription-refresh), um zu verhindern, dass es abläuft.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Abonnementausgabebindung definiert, in der die create-Aktion verwendet wird:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In dem C#-Scriptcode wird ein Webhook registriert, der diese Funktions-App benachrichtigt, wenn der aufrufende Benutzer eine Outlook-Nachricht empfängt:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Webhookausgabe: JavaScript-Beispiel

Im folgenden Beispiel wird ein Abonnement erstellt. Sie können [das Abonnement aktualisieren](#webhook-subscription-refresh), um zu verhindern, dass es abläuft.

In der *function.json*-Datei ist ein HTTP-Trigger mit einer Abonnementausgabebindung definiert, in der die create-Aktion verwendet wird:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In dem JavaScript-Code wird ein Webhook registriert, der diese Funktions-App benachrichtigt, wenn der aufrufende Benutzer eine Outlook-Nachricht empfängt:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhookausgabe: Attribute

Verwenden Sie in [C#-Klassenbibliotheken](functions-dotnet-class-library.md) das Attribut [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs).

### <a name="webhook-output---configuration"></a>Webhookausgabe: Konfiguration

Die folgende Tabelle gibt Aufschluss über die Bindungskonfigurationseigenschaften, die Sie in der Datei *function.json* und im Attribut `GraphWebHookSubscription` festlegen:

|Eigenschaft von „function.json“ | Attributeigenschaft |BESCHREIBUNG|
|---------|---------|----------------------|
|**name**||Erforderlich – der Variablenname, der im Funktionscode für die E-Mail-Nachricht verwendet wird. Weitere Informationen finden Sie unter [Verwenden einer Outlook-Nachrichtenausgabebindung in Code](#outlook-output-code).|
|**type**||Erforderlich – muss auf `graphWebhookSubscription` festgelegt sein.|
|**direction**||Erforderlich – muss auf `out` festgelegt sein.|
|**Identität**|**Identität**|Erforderlich – die Identität, die zum Ausführen der Aktion verwendet wird. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>userFromRequest</code> – nur gültig mit [HTTP-Trigger]. Verwendet die Identität des aufrufenden Benutzers.</li><li><code>userFromId</code> – verwendet die Identität eines zuvor angemeldeten Benutzers mit der angegebenen ID. Weitere Informationen finden Sie unter der <code>userId</code>-Eigenschaft.</li><li><code>userFromToken</code> – verwendet die Identität, die durch das angegebene Token dargestellt wird. Weitere Informationen finden Sie unter der <code>userToken</code>-Eigenschaft.</li><li><code>clientCredentials</code> – verwendet die Identität der Funktions-App.</li></ul>|
|**userId**|**UserId**  |Genau dann erforderlich, wenn _Identität_ auf `userFromId` festgelegt ist. Eine Benutzerprinzipal-ID, die einem zuvor angemeldeten Benutzer zugeordnet ist.|
|**userToken**|**UserToken**|Genau dann erforderlich, wenn _Identität_ auf `userFromToken` festgelegt ist. Ein gültiges Token für die Funktions-App. |
|**action**|**Aktion**|Erforderlich – gibt die Aktion an, die die Bindung durchführen soll. Es kann sich um einen der folgenden Werte handeln:<ul><li><code>create</code> – registriert ein neues Abonnement.</li><li><code>delete</code> – löscht ein angegebenes Abonnement.</li><li><code>refresh</code> – aktualisiert ein angegebenes Abonnement, damit es nicht abläuft.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Genau dann erforderlich, wenn die _Aktion_ auf `create` festgelegt ist. Gibt die Microsoft Graph-Ressource an, die auf Änderungen überwacht werden soll. Weitere Informationen finden Sie unter [Arbeiten mit Webhooks in Microsoft Graph]. |
|**changeType**|**ChangeType**|Genau dann erforderlich, wenn die _Aktion_ auf `create` festgelegt ist. Gibt den Typ der Änderung in der abonnierten Ressource an, der eine Benachrichtigung auslöst. Die unterstützten Werte sind: `created`, `updated`, `deleted`. Mehrere Werte können mit einer durch Trennzeichen getrennten Liste kombiniert werden.|

### <a name="webhook-output---usage"></a>Webhookausgabe: Nutzung

Die Bindung macht die folgenden Typen für .NET-Funktionen verfügbar:
- Zeichenfolge
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Aktualisierung von Webhookabonnements

Es gibt zwei Ansätze zum Aktualisieren von Abonnements:

- Verwenden der Anwendungsidentität zum Verarbeiten aller Abonnements. Dies erfordert die Zustimmung eines Azure Active Directory-Administrators. Dieser Ansatz kann in allen Sprachen verwendet werden, die von Azure Functions unterstützt werden.
- Verwenden der Identität für jedes Abonnement durch manuelles Binden jeder Benutzer-ID. Dies erfordert einigen benutzerdefinierten Code zum Durchführen der Bindung. Dieser Ansatz kann nur von .NET Funktionen verwendet werden.

Dieser Abschnitt enthält ein Beispiel für jeden dieser Ansätze:

* [Beispiel für Anwendungsidentität](#webhook-subscription-refresh---app-identity-example)
* [Beispiel für Benutzeridentität](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Aktualisierung von Webhookabonnements: Beispiel für Anwendungsidentität

Sehen Sie sich das sprachspezifische Beispiel an:

* [C#-Skript (.csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Aktualisierung über Anwendungsidentität: Beispiel für C#-Skript

Im folgenden Beispiel wird die Anwendungsidentität verwendet, um ein Abonnement zu aktualisieren.

In der *function.json*-Datei ist ein Timertrigger mit einer Abonnementeingabebindung und einer Abonnementausgabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode aktualisiert die Abonnements:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Aktualisierung über Anwendungsidentität: Beispiel für C#-Skript

Im folgenden Beispiel wird die Anwendungsidentität verwendet, um ein Abonnement zu aktualisieren.

In der *function.json*-Datei ist ein Timertrigger mit einer Abonnementeingabebindung und einer Abonnementausgabebindung definiert:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Der JavaScript-Code aktualisiert die Abonnements:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Aktualisierung von Webhookabonnements: Beispiel für Benutzeridentität

Im folgenden Beispiel wird die Benutzeridentität verwendet, um ein Abonnement zu aktualisieren.

In der *function.json*-Datei ist ein Timertrigger definiert und wird die Abonnementeingabebindung an den Funktionscode verzögert:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Der C#-Skriptcode aktualisiert die Abonnements und erstellt die Ausgabebindung im Code, wobei die Identität jedes Benutzers verwendet wird:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte für Azure Functions-Trigger und -Bindungen](functions-triggers-bindings.md)

[HTTP-Trigger]: functions-bindings-http-webhook.md
[Arbeiten mit Webhooks in Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
