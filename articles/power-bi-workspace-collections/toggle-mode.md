---
title: Wechseln zwischen Ansichts- und Bearbeitungsmodus für Berichte in Power BI-Arbeitsbereichssammlungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie zwischen Ansichts- und Bearbeitungsmodus für Berichte in Power BI-Arbeitsbereichssammlungen wechseln.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: cab5d067387fcffe4f62b94e06960afe47d5f00c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702690"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Wechseln zwischen Ansichts- und Bearbeitungsmodus für Berichte in Power BI-Arbeitsbereichssammlungen

Erfahren Sie, wie Sie zwischen Ansichts- und Bearbeitungsmodus für Berichte in Power BI-Arbeitsbereichssammlungen wechseln.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="creating-an-access-token"></a>Erstellen eines Zugriffstokens

Sie müssen ein Zugriffstoken erstellen, mit dem Sie einen Bericht sowohl anzeigen als auch bearbeiten können. Zum Bearbeiten und Speichern eines Berichts benötigen Sie die Tokenberechtigung **Report.ReadWrite**. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen](app-token-flow.md).

> [!NOTE]
> Damit können Sie einen vorhandenen Bericht bearbeiten und die Änderungen speichern. Wenn Sie auch die Funktion **Speichern unter** unterstützen möchten, müssen Sie zusätzliche Berechtigungen angeben. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren mit Power BI Embedded](app-token-flow.md#scopes).

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Einbettungskonfiguration

Sie müssen Berechtigungen und einen viewMode (Ansichtsmodus) angeben, damit im Bearbeitungsmodus die Schaltfläche „Speichern“ angezeigt wird. Weitere Informationen finden Sie unter [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Details der Einbettungskonfiguration).

Beispielsweise in JavaScript:

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

Dadurch wird angegeben, dass der Bericht im Ansichtsmodus eingebettet wird, basierend auf der Festlegung von **viewMode** auf **models.ViewMode.View**.

## <a name="view-mode"></a>Ansichtsmodus

Sie können den folgenden JavaScript-Code zum Wechsel in den Ansichtsmodus verwenden, wenn Sie sich im Bearbeitungsmodus befinden.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>Bearbeitungsmodus

Sie können den folgenden JavaScript-Code zum Wechsel in den Bearbeitungsmodus verwenden, wenn Sie sich im Ansichtsmodus befinden.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>Weitere Informationen

[Erste Schritte mit dem Beispiel](get-started-sample.md)  
[Einbetten eines Berichts](embed-report.md)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git-Repository](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git-Repository](https://github.com/Microsoft/PowerBI-Node)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)
