---
title: Speichern von Berichten in Power BI-Arbeitsbereichssammlungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Berichte in Power BI-Arbeitsbereichssammlungen speichern. Um dies erfolgreich durchzuführen, benötigen Sie die entsprechenden Berechtigungen.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: b61abee3382697d50b9a18de763c8a4d01e1ccba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701836"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Speichern von Berichten in Power BI-Arbeitsbereichssammlungen

Erfahren Sie, wie Sie Berichte in Power BI-Arbeitsbereichssammlungen speichern. Damit Sie Berichte erfolgreich speichern können, benötigen Sie die entsprechenden Berechtigungen.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

In Power BI-Arbeitsbereichssammlungen können Sie vorhandene Berichte bearbeiten und speichern. Sie können auch einen neuen Bericht erstellen und als neuen Bericht speichern, um einen Bericht zu erstellen.

Zum Speichern eines Berichts müssen Sie zunächst ein Token für den entsprechenden Bericht mit den richtigen Bereichen erstellen:

* Zum Aktivieren von „Speichern“ ist der Bereich „Report.ReadWrite“ erforderlich.
* Zum Aktivieren von „Speichern unter“ sind die Bereiche „Report.Read“ und „Workspace.Report.Copy“ erforderlich.
* Zum Aktivieren von „Speichern“ und „Speichern unter“ sind die Bereiche „Report.ReadWrite“ und „Workspace.Report.Copy“ erforderlich.

Um jeweils die richtigen Schaltflächen für „Speichern/Speichern unter“ im Dateimenü zu aktivieren, müssen Sie beim Einbetten des Berichts die richtige Berechtigung in der Einbettungskonfiguration angeben:

* models.Permissions.ReadWrite
* models.Permissions.Copy
* models.Permissions.All

> [!NOTE]
> Ihr Zugriffstoken benötigt auch die entsprechenden Bereiche. Weitere Informationen finden Sie unter [Authentifizieren und Autorisieren mit Power BI Embedded](app-token-flow.md#scopes).

## <a name="embed-report-in-edit-mode"></a>Einbetten von Berichten im Bearbeitungsmodus

Nehmen wir an, dass Sie einen Bericht im Bearbeitungsmodus in Ihre App einbetten möchten. Sie übergeben einfach nur die richtigen Eigenschaften an die Einbettungskonfiguration und rufen „powerbi.embed()“ auf. Geben Sie Berechtigungen und einen viewMode (Ansichtsmodus) an, damit im Bearbeitungsmodus die Schaltflächen „Speichern“ und „Speichern unter“ angezeigt werden. Weitere Informationen finden Sie unter [Embed configuration details](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details) (Details der Einbettungskonfiguration).

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
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

Jetzt wird ein Bericht im Bearbeitungsmodus in Ihre App eingebettet.

## <a name="save-report"></a>Bericht speichern

Nach dem Einbetten des Berichts im Bearbeitungsmodus mit dem richtigen Token und den richtigen Berechtigungen können Sie den Bericht über das Dateimenü oder über JavaScript speichern:

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>Speichern unter

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
> Ein neuer Bericht wird erst nach *Speichern unter* erstellt. Nach dem Speichern wird in der Canvas weiterhin der alte Bericht im Bearbeitungsmodus und nicht der neue Bericht angezeigt. Betten Sie den neuen Bericht ein, der erstellt wurde. Das Einbetten des neuen Berichts erfordert ein neues Zugriffstoken, da diese pro Bericht erstellt werden.

Sie müssen dann den neuen Bericht nach *Speichern unter* laden. Das Laden des neuen Berichts ähnelt dem Einbetten eines beliebigen Berichts.

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

## <a name="see-also"></a>Weitere Informationen

[Erste Schritte mit dem Beispiel](get-started-sample.md)  
[Einbetten eines Berichts](embed-report.md)  
[Erstellen eines neuen Berichts aus einem Dataset](create-report-from-dataset.md)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)

