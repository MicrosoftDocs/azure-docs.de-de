---
title: Erste Schritte mit einem Beispiel
description: Dieser Artikel enthält eine Einführung in das Beispiel für die ersten Schritte mit Power BI-Arbeitsbereichssammlungen.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 6cf78bf9c83df0a1255c14499bbd2036a2be5ed6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64714508"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Beispiel für die ersten Schritte mit Power BI-Arbeitsbereichssammlungen

Mit **Microsoft Power BI-Arbeitsbereichssammlungen** können Sie Power BI-Berichte direkt in webbasierte oder mobile Anwendungen einbinden. Dieser Artikel enthält eine Einführung in das Beispiel für die ersten Schritte mit **Power BI-Arbeitsbereichssammlungen**.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Bevor wir fortfahren, sollten Sie die folgenden Ressourcen speichern: Diese Ressourcen sind hilfreich, wenn Sie Power BI-Berichte in die Beispiel-App (und auch in Ihre eigenen Apps) integrieren.

* [Beispiel für eine Arbeitsbereichs-Web-App](https://go.microsoft.com/fwlink/?LinkId=761493)
* [API-Referenz für Power BI-Arbeitsbereichssammlungen](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI .NET SDK](https://go.microsoft.com/fwlink/?LinkId=746472) (verfügbar über NuGet)
* [Beispiel für die Einbettung von JavaScript-Berichten](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Bevor Sie das Beispiel zu den ersten Schritten mit Power BI-Arbeitsbereichssammlungen konfigurieren und ausführen können, müssen Sie mindestens eine **Arbeitsbereichssammlung** in Ihrem Azure-Abonnement erstellen. Informationen zum Erstellen einer **Arbeitsbereichssammlung** im Azure-Portal finden Sie unter [Erste Schritte mit Power BI-Arbeitsbereichssammlungen](get-started.md).

## <a name="configure-the-sample-app"></a>Konfigurieren der Beispiel-App

Richten wir zunächst Schritt für Schritt die Visual Studio-Entwicklungsumgebung ein, um auf die Komponenten zuzugreifen, die zum Ausführen der Beispiel-App erforderlich sind.

1. Laden Sie das Beispiel [Power BI-Arbeitsbereichssammlungen – Integrate a report into a web app](https://go.microsoft.com/fwlink/?LinkId=761493) (Integrieren eines Berichts in eine Web-App) von GitHub herunter, und extrahieren Sie den Download.
2. Öffnen Sie **PowerBI embedded.sln** in Visual Studio. Möglicherweise müssen Sie den Befehl **Update-Package** in der NuGet-Paket-Manager-Konsole ausführen, um die Pakete zu aktualisieren, die in dieser Lösung verwendet werden.
3. Erstellen Sie die Projektmappe.
4. Führen Sie die Konsolen-App **ProvisionSample** aus. In der Beispiel-App-Konsole stellen Sie einen Arbeitsbereich bereit und importieren eine PBIX-Datei.
5. Wählen Sie zum Bereitstellen eines neuen **Arbeitsbereichs** Option 1, **Collection management**, und dann Option 6, **Provision a new Workspace**.
6. Um einen neuen **Bericht** zu importieren, wählen Sie Option 2, **Report management**, und Option 3, **Import PBIX Desktop file into a workspace**, aus.

7. Geben Sie den Namen Ihrer **Arbeitsbereichssammlung** und den **Zugriffsschlüssel** ein. Sie finden diese Angaben im **Azure-Portal**. Weitere Informationen zum Abrufen Ihres **Zugriffsschlüssels**finden Sie unter [Anzeigen von Power BI-API-Zugriffsschlüsseln](get-started.md#view-power-bi-api-access-keys) in „Erste Schritte mit Microsoft Power BI Embedded“.

    ![Zugriffsschlüssel im Azure-Portal](media/get-started-sample/azure-portal.png)
8. Kopieren und speichern Sie die neu erstellte **Arbeitsbereichs-ID** , um sie später in diesem Artikel zu verwenden. Nach der Erstellung ist die **Arbeitsbereichs-ID** im **Azure-Portal** zu finden.

    ![Arbeitsbereichs-ID im Azure-Portal](media/get-started-sample/workspace-id.png)
9. Zum Importieren einer PBIX-Datei in Ihren **Arbeitsbereich** wählen Sie Option **6. Importieren Sie die PBIX-Desktopdatei in einen vorhandenen Arbeitsbereich**. Falls Sie keine PBIX-Datei parat haben, können Sie das [PBIX-Beispiel „Retail Analysis“](https://go.microsoft.com/fwlink/?LinkID=780547) herunterladen.
10. Wenn Sie dazu aufgefordert werden, geben Sie einen Anzeigenamen für Ihr **Dataset**ein.

Sie sollten in etwa folgende Rückgabe erhalten:

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> Wenn Ihre PBIX-Datei DirectQuery-Verbindungen enthält, führen Sie Option 7 aus, um die Verbindungszeichenfolgen zu aktualisieren.

Jetzt haben Sie einen Power BI-PBIX-Bericht in Ihren **Arbeitsbereich** importiert. Im nächsten Abschnitt erfahren Sie, wie Sie die Beispiel-Web-App für die ersten Schritte in **Power BI-Arbeitsbereichssammlungen** ausführen.

## <a name="run-the-sample-web-app"></a>Ausführen der Beispiel-Web-App

Das Beispiel für die Web-App ist eine Beispielanwendung, die die in den **Arbeitsbereich** importierten Berichte rendert. So konfigurieren Sie das Web-App-Beispiel

1. Klicken Sie in der Visual Studio-Projektmappe **PowerBI-embedded** mit der rechten Maustaste auf die Webanwendung **EmbedSample**, und wählen Sie **Als Startprojekt festlegen** aus.
2. Bearbeiten Sie in **web.config** in der Webanwendung **EmbedSample** die **appSettings**: **AccessKey**, **WorkspaceCollection**-Name und **WorkspaceId**.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Führen Sie die Webanwendung **EmbedSample** aus.

Nach dem Ausführen der Webanwendung **EmbedSample** enthält der Navigationsbereich auf der linken Seite ein Menü **Berichte**. Erweitern Sie zum Anzeigen des importierten Berichts den Eintrag **Berichte**, und klicken Sie auf einen Bericht. Wenn Sie das [PBIX-Beispiel „Retail Analysis“](https://go.microsoft.com/fwlink/?LinkID=780547) importiert haben, sieht die Beispiel-Web-App wie folgt aus:

![Beispiel für die linke Navigationsleiste in der Beispielanwendung](media/get-started-sample/sample-left-nav.png)

Nachdem Sie auf einen Bericht geklickt haben, sieht die Webanwendung **EmbedSample** in etwa wie folgt aus:

![In Anwendung angezeigter Beispielbericht](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>Untersuchen des Beispielcodes

Bei dem Beispiel für **Power BI-Arbeitsbereichssammlungen** handelt es sich um ein Beispiel einer Web-App, das zeigt, wie Sie **Power BI**-Berichte in Ihre App integrieren. Dabei werden die bewährten Methoden anhand eines MVC-Entwurfsmusters (Model-View-Controller) veranschaulicht. In diesem Abschnitt werden Teile des Beispielcodes hervorgehoben, die Sie innerhalb der Web-App-Projektmappe **PowerBI-embedded** untersuchen können. Das MVC-Muster (Model View Controller) trennt die Modellierung der Domäne, die Präsentation und die Aktionen basierend auf der Benutzereingabe in drei separate Klassen: „Model“, „View“ und „Control“. Weitere Informationen zu MVC finden Sie unter [Learn About ASP.NET](https://www.asp.net/mvc) (Informationen zu ASP.NET).

Der Beispielcode für **Microsoft Power BI-Arbeitsbereichssammlungen** ist wie folgt unterteilt. Jeder Abschnitt enthält den Dateinamen in der Projektmappe „PowerBI-embedded.sln“, damit Sie den Code im Beispiel leicht finden können.

> [!NOTE]
> Dieser Abschnitt ist eine Zusammenfassung des Beispielcodes, die zeigt, wie der Code geschrieben wurde. Laden Sie die Projektmappe „PowerBI-embedded.sln“ in Visual Studio, um das vollständige Beispiel anzuzeigen.

### <a name="model"></a>Modell

Das Beispiel umfasst ein **ReportsViewModel** und ein **ReportViewModel**.

**ReportsViewModel.cs:** Entspricht Power BI-Berichten.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs:** Entspricht einem Power BI-Bericht.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>Verbindungszeichenfolge

Die Verbindungszeichenfolge muss folgendes Format aufweisen:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Die Verwendung allgemeiner Server- und Datenbankattribute ist nicht möglich. Beispiel:  Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>Sicht

In der **Ansicht** wird die Anzeige von Power BI-**Berichten** und eines einzelnen Power BI-**Berichts** verwaltet.

**Reports.cshtml:** Durchlaufen Sie **Model.Reports**, um einen **ActionLink** zu erstellen. Der **ActionLink** setzt sich wie folgt zusammen:

| Teil | BESCHREIBUNG |
| --- | --- |
| Titel |Der Name des Berichts. |
| QueryString |Ein Link zur Berichts-ID. |
```cshtml
<div id="reports-nav" class="panel-collapse collapse">
    <div class="panel-body">
        <ul class="nav navbar-nav">
            @foreach (var report in Model.Reports)
            {
                var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                <li class="@reportClass">
                    @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                </li>
            }
        </ul>
    </div>
</div>
```
„Report.cshtml“: Legen Sie das **Model.AccessToken** und den Lambdaausdruck für **PowerBIReportFor** fest.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Controller

**DashboardController.cs:** Erstellt einen PowerBIClient, der ein **App-Token** übergibt. Ein JSON Web Token (JWT) wird auf der Grundlage des **Signaturschlüssels** generiert, um die **Anmeldeinformationen** zu erhalten. Die **Anmeldeinformationen** werden zum Erstellen einer Instanz von **PowerBIClient** verwendet. Sobald Sie über eine Instanz von **PowerBIClient** verfügen, können Sie GetReports() und GetReportsAsync() aufrufen.


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
public ActionResult Reports()
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

        var viewModel = new ReportsViewModel
        {
            Reports = reportsResponse.Value.ToList()
        };

        return PartialView(viewModel);
    }
}
```

Task\<ActionResult> Report(string reportId)

```csharp
public async Task<ActionResult> Report(string reportId)
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
        var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
        var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

        var viewModel = new ReportViewModel
        {
            Report = report,
            AccessToken = embedToken.Generate(this.accessKey)
        };

        return View(viewModel);
    }
}
```

### <a name="integrate-a-report-into-your-app"></a>Integrieren eines Berichts in Ihre App

Sobald Sie über einen **Bericht** verfügen, können Sie den Power BI-**Bericht** per **IFrame** einbetten. Hier sehen Sie einen Codeausschnitt aus „powerbi.js“ im Beispiel für **Microsoft Power BI-Arbeitsbereichssammlungen**.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>Filtern von in die Anwendung eingebetteten Berichten

Sie können einen eingebetteten Bericht mit einer URL-Syntax filtern. Fügen Sie hierfür der iFrame-SRC-URL mit dem angegebenen Filter den Abfragezeichenfolgen-Parameter **$filter** mit dem Operator **eq** hinzu. Dies ist die Syntax der Filterabfrage:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName} darf keine Leerzeichen oder Sonderzeichen enthalten. Für {fieldValue} wird ein einzelner Kategoriewert akzeptiert.  

## <a name="see-also"></a>Weitere Informationen

[Häufige Szenarien für Microsoft Power BI-Arbeitsbereichssammlungen](scenarios.md)  
[Authenticating and authorizing in Power BI Workspace Collections (Authentifizieren und Autorisieren in Power BI-Arbeitsbereichssammlungen)](app-token-flow.md)  
[Einbetten eines Berichts](embed-report.md)  
[Erstellen eines neuen Berichts aus einem Dataset](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)
