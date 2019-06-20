---
title: Erstellen eines neuen Berichts aus einem Dataset in Power BI-Arbeitsbereichssammlungen | Microsoft-Dokumentation
description: Berichte zu Power BI-Arbeitsbereichssammlungen können nun aus einem Dataset in Ihrer eigenen Anwendung erstellt werden.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: e7499345f03e3deedb8972b0d51e8e676cb6c982
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64683453"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Erstellen eines neuen Berichts aus einem Dataset in Power BI-Arbeitsbereichssammlungen

Berichte zu Power BI-Arbeitsbereichssammlungen können nun aus einem Dataset in Ihrer eigenen Anwendung erstellt werden.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Die Authentifizierungsmethode ähnelt der Methode zum Einbetten von Berichten. Sie basiert auf Zugriffstoken, die für ein Dataset spezifisch sind. Token, die für PowerBI.com verwendet werden, werden vom Azure Active Directory (AAD) ausgestellt. Token für Power BI-Arbeitsbereichssammlungen werden von Ihrer eigenen App ausgestellt.

Beim Erstellen eines Embedded-Berichts sind die ausgestellten Token für ein bestimmtes Dataset bestimmt. Token sollten der Einbettungs-URL auf dem gleichen Element zugeordnet sein, um sicherzustellen, dass jedes ein eindeutiges Token hat. Um einen Embedded-Bericht zu erstellen, müssen die Bereiche *Dataset.Read und Workspace.Report.Create* im Zugriffstoken bereitgestellt werden.

## <a name="create-access-token-needed-to-create-new-report"></a>Erstellen von Zugriffstoken, die zum Erstellen des neuen Berichts erforderlich sind

Power BI-Arbeitsbereichssammlungen verwenden einen Einbettungstoken, bei dem es sich um HMAC-signierte JSON-Webtoken handelt. Die Token werden mit dem Zugriffsschlüssel aus Ihrer Power BI-Arbeitsbereichssammlung signiert. Einbettungstoken werden standardmäßig verwendet, um schreibgeschützten Zugriff auf einen Bericht zum Einbetten in eine Anwendung bereitzustellen. Einbettungstoken werden für einen bestimmten Bericht ausgegeben und sollten einer Einbettungs-URL zugeordnet werden.

Zugriffstoken sollten auf dem Server erstellt werden, da die Zugriffsschlüssel zum Signieren/Verschlüsseln von Token verwendet werden. Informationen zum Erstellen eines Zugriffstoken finden Sie unter [Authenticating and authorizing with Power BI Workspace Collections (Authentifizieren und Autorisieren mit Power BI-Arbeitsbereichssammlungen)](app-token-flow.md). Sie können sich auch über die [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)-Methode informieren. Das folgende Beispiel zeigt Ihnen das Vorgehen mit dem .NET SDK für Power BI.

In diesem Beispiel geben wir unsere Dataset-ID an, zu der wir den neuen Bericht erstellen möchten. Wir müssen auch die Bereiche für *Dataset.Read und Workspace.Report.Create* hinzufügen.

Die *PowerBIToken-Klasse* erfordert die Installation des [Power BI Core-NuGut-Pakets](https://www.nuget.org/packages/Microsoft.PowerBI.Core/).

**NuGet-Paketinstallation**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C#-Code**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>Erstellen eines neuen leeren Berichts

Zum Erstellen eines neuen Berichts muss die Erstellungskonfiguration bereitgestellt werden. Dazu gehören Zugriffstoken, embedURL und datasetID, auf deren Basis wir den Bericht erstellen möchten. Hierzu müssen Sie das NuGet-[Power BI JavaScript-Paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/) installieren. Der Wert für „embedUrl“ lautet einfach https://embedded.powerbi.com/appTokenReportEmbed.

> [!NOTE]
> Mit dem [JavaScript Report Embed Sample](https://microsoft.github.io/PowerBI-JavaScript/demo/) (Beispiel zur Berichtseinbettung mit JavaScript) können Sie die Funktionalität testen. Es enthält außerdem Codebeispiele für die verschiedenen Vorgänge, die verfügbar sind.

**NuGet-Paketinstallation**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript-Code**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

Beim Aufrufen von *powerbi.createReport()* wird im *div*-Element eine leere Canvas im Bearbeitungsmodus angezeigt.

![Neuer leerer Bericht](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>Speichern von neuen Berichten

Der Bericht wird erst dann erstellt, wenn Sie den Vorgang **Speichern unter** aufrufen. Dies kann über das Menü „Datei“ oder JavaScript erfolgen.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> Ein neuer Bericht wird erst nach dem Aufruf von **Speichern unter** erstellt. Nach dem Speichern wird in der Canvas weiterhin das Dataset im Bearbeitungsmodus und nicht der Bericht angezeigt. Sie müssen den neuen Bericht wie jeden anderen Bericht neu laden.

![Menü „Datei“ > „Speichern unter“](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>Laden des neuen Berichts

Zur Interaktion mit dem neuen Bericht müssen Sie ihn in gleicher Weise einbetten, wie die Anwendung einen regulären Bericht einbettet, d.h. ein neues Token muss speziell für den neuen Bericht ausgestellt werden, und rufen Sie dann die Einbettungsmethode auf.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>Automatisches Speichern und Laden eines neuen Berichts mithilfe des „saved“-Ereignisses

Zum Automatisieren des „Speichern unter“-Vorgangs und anschließenden Laden des neuen Berichts können Sie das gespeicherte Ereignis verwenden. Dieses Ereignis wird ausgelöst, wenn der Speichervorgang abgeschlossen ist, und es gibt ein JSON-Objekt zurück, das die neue ReportId, den Berichtsnamen und die alte reportId (falls vorhanden) enthält, sowie die Information, ob ein „Speichern unter“- oder „Speichern“-Vorgang stattgefunden hat.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

Um den Vorgang zu automatisieren, können Sie auf das gespeicherte Ereignis lauschen, die neue ReportId übernehmen, das neue Token erstellen und den neuen Bericht damit einbetten.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>Weitere Informationen

[Erste Schritte mit dem Beispiel](get-started-sample.md)  
[Speichern von Berichten](save-reports.md)  
[Einbetten eines Berichts](embed-report.md)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI Core-NuGut-Paket](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript-Paket](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)
