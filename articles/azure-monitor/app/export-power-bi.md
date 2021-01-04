---
title: Exportieren nach Power BI aus Azure Application Insights | Microsoft-Dokumentation
description: Analytics-Abfragen können in Power BI angezeigt werden.
ms.topic: conceptual
ms.date: 08/10/2018
ms.openlocfilehash: 1e88d60cd9a9d0e6267998237b33a361b12ffd84
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186999"
---
# <a name="feed-power-bi-from-application-insights"></a>Datenimport nach Power BI aus Application Insights
[Power BI](https://www.powerbi.com/) ist eine Suite aus Unternehmenstools, mit denen Sie Daten analysieren und Informationen teilen können. Auf jedem Gerät stehen leistungsfähige Dashboards zur Verfügung. Sie können Daten aus vielen Quellen kombinieren, z.B. Analytics-Abfragen aus [Azure Application Insights](./app-insights-overview.md).

Es gibt drei Methoden zum Exportieren von Application Insights-Daten nach Power BI:

* [**Exportieren von Analytics-Abfragen**](#export-analytics-queries). Dies ist die bevorzugte Methode. Schreiben Sie eine beliebige Abfrage, und exportieren Sie sie in Power BI. Sie können diese Abfrage zusammen mit anderen Daten in einem Dashboard anordnen.
* [**Fortlaufender Export und Azure Stream Analytics**](./export-stream-analytics.md). Diese Methode ist hilfreich, wenn Sie Ihre Daten über längere Zeiträume speichern möchten. Wenn Sie keine erweiterte Datenaufbewahrungsanforderung erfüllen müssen, verwenden Sie die Methode zum Exportieren von Analytics-Abfragen. Für fortlaufenden Export und Stream Analytics fallen mehr Einrichtungsaufgaben und zusätzlicher Speichermehraufwand an.
* **Power BI-Adapter**. Die Gruppe von Diagrammen ist vordefiniert, aber Sie können auch eigene Abfragen aus anderen Quellen hinzufügen.

> [!NOTE]
> Der Power BI-Adapter ist nun **veraltet**. Die vordefinierte Diagramme für diese Lösung werden durch statische nicht bearbeitbare Abfragen mit Daten aufgefüllt. Sie haben nicht die Möglichkeit, diese Abfragen zu bearbeiten, und abhängig von bestimmten Eigenschaften Ihrer Daten ist es möglich, dass die Verbindung mit Power BI erfolgreich ist, aber keine Daten aufgefüllt werden. Dies liegt an Ausschlusskriterien, die in der hartcodierten Abfrage festgelegt werden. Während diese Lösung für einige Kunden noch funktionieren kann, wird aufgrund der mangelnden Flexibilität des Adapters die Verwendung der Funktionalität [**Analytics-Abfrage exportieren**](#export-analytics-queries) empfohlen.

## <a name="export-analytics-queries"></a>Exportieren von Analytics-Abfragen
Mit dieser Route können Sie eine beliebige Analytics-Abfrage schreiben oder aus Verwendungstrichtern exportieren und dann in ein Power BI-Dashboard exportieren. (Sie können dem mit dem Adapter erstellten Dashboard Elemente hinzufügen.)

### <a name="one-time-install-power-bi-desktop"></a>Einmalig: Installieren von Power BI Desktop
Zum Importieren Ihrer Application Insights-Abfrage verwenden Sie die Desktopversion von Power BI. Diese können Sie dann im Web oder in Ihrem Power BI-Cloudarbeitsbereich veröffentlichen. 

Installieren Sie [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

### <a name="export-an-analytics-query"></a>Exportieren einer Analytics-Abfrage
1. [Öffnen Sie Analytics, und schreiben Sie Ihre Abfrage](../log-query/log-analytics-tutorial.md).
2. Testen und optimieren Sie die Abfrage, bis Sie mit den Ergebnissen zufrieden sind. Stellen Sie sicher, dass die Abfrage ordnungsgemäß in Analytics ausgeführt wird, bevor Sie sie exportieren.
3. Wählen Sie im Menü **Exportieren** die Option **Power BI (M)** . Speichern Sie die Textdatei.
   
    ![Screenshot von Analytics mit hervorgehobenem Menü „Exportieren“](./media/export-power-bi/analytics-export-power-bi.png)
4. Klicken Sie in Power BI Desktop auf **Daten abrufen** > **Leere Abfrage**. Klicken Sie dann im Abfrage-Editor unter **Ansicht** auf **Erweiterter Editor**.

    Fügen Sie das exportierte M-Sprachskript in den erweiterten Editor ein.

    ![Screenshot von Power BI Desktop mit hervorgehobenem erweiterten Editor](./media/export-power-bi/power-bi-import-analytics-query.png)

5. Unter Umständen müssen Sie Anmeldeinformationen angeben, um für Power BI den Zugriff auf Azure zuzulassen. Verwenden Sie die Option **Organisationskonto**, um sich mit Ihrem Microsoft-Konto anzumelden.
   
    ![Screenshot des Power BI-Dialogfelds für Abfrageeinstellungen](./media/export-power-bi/power-bi-import-sign-in.png)

    Wenn Sie die Anmeldeinformationen überprüfen müssen, verwenden Sie den Menübefehl **Datenquelleneinstellungen** im Abfrage-Editor. Sie müssen unbedingt Ihre Anmeldeinformationen für Azure verwenden, die sich von denen für Power BI möglicherweise unterscheiden.
6. Wählen Sie eine Visualisierung für Ihre Abfrage aus, und wählen Sie die Felder für X-Achse, Y-Achse und Segmentierungsdimension.
   
    ![Screenshot der Visualisierungsoptionen von Power BI Desktop](./media/export-power-bi/power-bi-analytics-visualize.png)
7. Veröffentlichen Sie den Bericht in Ihrem Power BI-Cloudarbeitsbereich. Von hier aus können Sie eine synchronisierte Version in andere Webseiten einbetten.
   
    ![Screenshot von Power BI Desktop mit hervorgehobener Schaltfläche „Veröffentlichen“](./media/export-power-bi/publish-power-bi.png)
8. Aktualisieren Sie den Bericht in bestimmten Abständen manuell, oder richten Sie auf der Seite mit den Optionen eine geplante Aktualisierung ein.

### <a name="export-a-funnel"></a>Exportieren eines Trichters
1. [Erstellen Sie Ihren Trichter.](./usage-funnels.md)
2. Wählen Sie **Power BI** aus.

   ![Screenshot der Power BI-Schaltfläche](./media/export-power-bi/button.png)

3. Klicken Sie in Power BI Desktop auf **Daten abrufen** > **Leere Abfrage**. Klicken Sie dann im Abfrage-Editor unter **Ansicht** auf **Erweiterter Editor**.

   ![Screenshot von Power BI Desktop mit hervorgehobener Schaltfläche „Leere Abfrage“](./media/export-power-bi/blankquery.png)

   Fügen Sie das exportierte M-Sprachskript in den erweiterten Editor ein. 

   ![Screenshot von Power BI Desktop mit hervorgehobenem erweiterten Editor](./media/export-power-bi/advancedquery.png)

4. Wählen Sie Elemente aus der Abfrage und eine Trichtervisualisierung aus.

   ![Screenshot der Trichtervisualisierungsoptionen von Power BI Desktop](./media/export-power-bi/selectsequence.png)

5. Geben Sie einen aussagekräftigen Titel an, und veröffentlichen Sie den Bericht in Ihrem Power BI-Cloudarbeitsbereich. 

   ![Screenshot von Power BI Desktop mit hervorgehobenem Titel](./media/export-power-bi/changetitle.png)

## <a name="troubleshooting"></a>Problembehandlung

Es können Fehler in Bezug auf die Anmeldeinformationen oder die Größe des Datasets auftreten. Hier finden Sie einige Informationen zum Beheben dieser Fehler.

### <a name="unauthorized-401-or-403"></a>Nicht autorisiert (401 oder 403)
Dieser Fall kann eintreten, wenn Ihr Aktualisierungstoken nicht aktualisiert wurde. Probieren Sie diese Schritte aus, um sicherzustellen, dass Sie weiterhin Zugriff haben:

1. Melden Sie sich beim Azure-Portal an, und stellen Sie sicher, dass Sie auf die Ressource zugreifen können.
2. Versuchen Sie, die Anmeldeinformationen für das Dashboard zu aktualisieren.
3. Versuchen Sie, den Cache von Power BI Desktop zu löschen.


   Falls Sie Zugriff haben und die Aktualisierung der Anmeldeinformationen das Problem nicht behebt, erstellen Sie ein Supportticket.

### <a name="bad-gateway-502"></a>Ungültiges Gateway (502)
Dies wird normalerweise durch eine Analytics-Abfrage verursacht, die zu viele Daten zurückgibt. Versuchen Sie es mit einem kleineren Zeitbereich für die Abfrage. 

Wenn das Verkleinern des von der Analytics-Abfrage stammenden Datasets Ihre Anforderungen nicht erfüllt, sollten Sie erwägen, mithilfe der [API](https://dev.applicationinsights.io/documentation/overview) ein größeres Dataset abzurufen. So wird der M-Query-Export zum Verwenden der API konvertiert:

1. Erstellen Sie einen [API-Schlüssel](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).
2. Aktualisieren Sie das M-Skript von Power BI, das Sie aus Analytics exportiert haben, indem Sie die Azure Resource Manager-URL durch die Application Insights-API ersetzen.
   * Ersetzen Sie **https:\//management.azure.com/subscriptions/...**
   * durch **https:\//api.applicationinsights.io/beta/apps/...**
3. Aktualisieren Sie schließlich die Anmeldeinformationen in „basic“, und verwenden Sie Ihren API-Schlüssel.

**Vorhandenes Skript**
 ```
 Source = Json.Document(Web.Contents("https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups//providers/microsoft.insights/components//api/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```
**Aktualisiertes Skript**
 ```
 Source = Json.Document(Web.Contents("https://api.applicationinsights.io/beta/apps/<APPLICATION_ID>/query?api-version=2014-12-01-preview",[Query=[#"csl"="requests",#"x-ms-app"="AAPBI"],Timeout=#duration(0,0,4,0)]))
 ```

## <a name="about-sampling"></a>Informationen zur Stichprobenerstellung (Sampling)
Abhängig von der Menge der Daten, die von Ihrer Anwendung gesendet werden, sollten Sie ggf. die Funktion für die adaptive Stichprobenerstellung verwenden, bei der nur ein prozentualer Anteil der Telemetriedaten gesendet wird. Dasselbe gilt, wenn Sie die Stichprobenerstellung entweder im SDK oder bei der Erfassung manuell festgelegt haben. [Erfahren Sie mehr über Sampling](./sampling.md).

## <a name="power-bi-adapter-deprecated"></a>Power BI-Adapter (veraltet)
Mit dieser Methode wird für Sie ein vollständiges Dashboard mit Telemetriedaten erstellt. Das anfängliche Dataset ist vordefiniert, aber Sie können weitere Daten hinzufügen.

### <a name="get-the-adapter"></a>Abrufen des Adapters
1. Melden Sie sich bei [Power BI](https://app.powerbi.com/) an.
2. Öffnen Sie **Daten abrufen** ![Screenshot des GetData-Symbols in der unteren linken Ecke](./media/export-power-bi/001.png) und dann **Dienste**.

    ![Screenshot der Schaltfläche „Abrufen“ im Fenster „Dienste“](./media/export-power-bi/002.png)

3. Wählen Sie **Jetzt abrufen** unter Application Insights aus.

   ![Screenshots für das Abrufen aus der Application Insights-Datenquelle](./media/export-power-bi/003.png)
4. Geben Sie die Details Ihrer Application Insights-Ressource an, und **melden Sie sich dann an**.

    ![Screenshot des Fensters „Connect to Application Insights“ (Verbindung mit Application Insights herstellen)](./media/export-power-bi/005.png)

     Sie finden diese Informationen im Bereich „Application Insights-Übersicht“:

     ![Screenshot für das Abrufen aus der Application Insights-Datenquelle](./media/export-power-bi/004.png)

5. Öffnen Sie die neu erstellte Application Insights Power BI-App.

6. Warten Sie ein oder zwei Minuten, bis die Daten importiert wurden.

    ![Screenshot des Power BI-Adapters](./media/export-power-bi/010.png)

Sie können das Dashboard bearbeiten und die Application Insights-Diagramme mit den Diagrammen anderer Quellen und mit Analytics-Abfragen kombinieren. Es gibt einen Katalog mit visuellen Elementen, in dem weitere Diagramme bereitgestellt werden. Jedes dieser Diagramme verfügt über Parameter, die Sie festlegen können.

Nach dem anfänglichen Import werden das Dashboard und die Berichte täglich aktualisiert. Sie können den Aktualisierungszeitplan im Dataset steuern.

## <a name="next-steps"></a>Nächste Schritte
* [Power BI – Informationen](https://www.powerbi.com/learning/)
* [Analytics-Tutorial](../log-query/log-analytics-tutorial.md)