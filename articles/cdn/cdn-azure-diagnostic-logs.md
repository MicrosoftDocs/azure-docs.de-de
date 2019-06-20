---
title: Azure-Diagnoseprotokolle | Microsoft-Dokumentation
description: Kunden können die Protokollanalyse für Azure CDN aktivieren.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: a5fab3e2bf9908fa35cf5f5485df3116b7718d8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125884"
---
# <a name="azure-diagnostic-logs"></a>Azure-Diagnoseprotokolle

Mit Azure-Diagnoseprotokollen können Sie Daten der Basisanalyse anzeigen und diese an mindestens einem Ziel speichern, z. B.:

 - Azure-Speicherkonto
 - Azure Event Hubs
 - [Log Analytics-Arbeitsbereich](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Dieses Feature ist für CDN-Endpunkte für alle Tarife verfügbar. 

Mit Azure-Diagnoseprotokollen können Sie Metriken zur grundlegenden Nutzung von Ihrem CDN-Endpunkt auf verschiedene Quellkomponenten exportieren, um sie auf die gewünschte Weise zu verwenden. Sie können beispielsweise die folgenden Arten von Datenexport durchführen:

- Exportieren von Daten in Blobspeicher, Exportieren in eine CSV-Datei und Generieren von Diagrammen in Excel
- Exportieren von Daten nach Event Hubs und Korrelieren mit Daten von anderen Azure-Diensten
- Exportieren von Daten in Azure Monitor-Protokolle und Anzeigen von Daten im eigenen Log Analytics-Arbeitsbereich

Im folgenden Diagramm ist eine typische Datenansicht der CDN-Basisanalyse dargestellt.

![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Abbildung 1: Ansicht der CDN-Basisanalyse*

Weitere Informationen zu Diagnoseprotokollen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Aktivieren der Protokollierung über das Azure-Portal

Führen Sie die folgenden Schritte aus, um die Protokollierung mit der CDN-Basisanalyse zu aktivieren:

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wenn Sie CDN für Ihren Workflow noch nicht aktiviert haben, [Erstellen Sie ein Azure CDN-Profil und einen Endpunkt](cdn-create-new-endpoint.md), bevor Sie fortfahren.

1. Navigieren Sie im Azure-Portal zum **CDN-Profil**.

2. Suchen Sie im Azure-Portal nach einem CDN-Profil, oder wählen Sie eines aus Ihrem Dashboard. Wählen Sie dann den CDN-Endpunkt aus, für den Sie die Diagnoseprotokolle aktivieren möchten.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Wählen Sie im Abschnitt ÜBERWACHUNG die Option **Diagnoseprotokolle** aus.

   Die Seite **Diagnoseprotokolle** wird angezeigt.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Aktivieren der Protokollierung mit Azure Storage

Führen Sie die folgenden Schritte aus, um ein Speicherkonto zum Speichern der Protokolle zu verwenden:
    
1. Geben Sie für **Name** einen Namen für Ihre Diagnoseprotokolleinstellungen ein.
 
2. Wählen Sie **In einem Speicherkonto archivieren** und dann **CoreAnalytics**. 

2. Wählen Sie für **Beibehaltungsdauer (Tage)** die Anzahl der Aufbewahrungstage. Bei einer Aufbewahrung von 0 Tagen werden die Protokolle dauerhaft gespeichert. 

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Wählen Sie **Speicherkonto** aus.

    Sie Seite **Speicherkonto auswählen** wird angezeigt.

4. Wählen Sie in der Dropdownliste ein Speicherkontoname aus und klicken Sie auf **OK**.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Wenn Sie alle erforderlichen Diagnoseprotokolleinstellungen vorgenommen haben, wählen Sie **Speichern**.

### <a name="logging-with-azure-monitor"></a>Protokollierung mit Azure Monitor

Führen Sie die folgenden Schritte aus, um Azure Monitor zum Speichern der Protokolle zu verwenden:

1. Aktivieren Sie auf der Seite **Diagnoseprotokolle** die Option **An Log Analytics senden**. 

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Wählen Sie **Konfigurieren** aus, um die Azure Monitor-Protokollierung zu konfigurieren. 

   Die Seite **Log Analytics-Arbeitsbereiche** wird angezeigt.

    >[!NOTE] 
    >OMS-Arbeitsbereiche werden jetzt als Log Analytics-Arbeitsbereiche bezeichnet.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Wählen Sie **Neuen Arbeitsbereich erstellen** aus.

    Die Seite **Log Analytics-Arbeitsbereiche** wird angezeigt.

    >[!NOTE] 
    >OMS-Arbeitsbereiche werden jetzt als Log Analytics-Arbeitsbereiche bezeichnet.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/07_Create-new.png)

4. Geben Sie unter **Log Analytics-Arbeitsbereich** einen Log Analytics-Arbeitsbereichsnamen ein. Der Log Analytics-Arbeitsbereichsname muss eindeutig sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Leerzeichen und Unterstriche sind nicht zulässig. 

5. Wählen Sie unter **Abonnement** ein vorhandenes Abonnement aus. 

6. Erstellen Sie unter **Ressourcengruppe** eine neue Ressourcengruppe, oder wählen Sie eine vorhandene aus.

7. Wählen Sie für **Speicherort** einen Speicherort aus der Liste aus.

8. Wählen Sie **An Dashboard anheften** aus, wenn Sie die Protokollkonfiguration an Ihr Dashboard speichern möchten. 

9. Wählen Sie **OK** aus, um die Konfiguration abzuschließen.

10. Nach der Erstellung des Arbeitsbereichs wird wieder die Seite **Diagnoseprotokolle** angezeigt. Bestätigen Sie den Namen Ihres neuen Log Analytics-Arbeitsbereichs.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Wählen Sie **CoreAnalytics** und anschließend **Speichern**.

12. Um den neuen Log Analytics-Arbeitsbereich anzuzeigen, wählen Sie auf Ihrer CDN-Endpunkt-Seite die Option **Basisanalyse** aus.

    ![Portal – Diagnoseprotokolle](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Ihr Log Analytics-Arbeitsbereich ist nun bereit zur Datenprotokollierung. Zum Nutzen dieser Daten müssen Sie eine [Azure Monitor-Protokolllösung](#consuming-diagnostics-logs-from-a-log-analytics-workspace) verwenden, die später in diesem Artikel behandelt wird.

Weitere Informationen zu Protokolldatenverzögerungen finden Sie unter [Protokolldatenverzögerungen](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Aktivieren der Protokollierung mit PowerShell

Das folgende Beispiel zeigt, wie Sie Diagnoseprotokolle über die Azure PowerShell-Cmdlets aktivieren.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Aktivieren von Diagnoseprotokollen in einem Speicherkonto

1. Melden Sie sich an, und wählen Sie ein Abonnement aus:

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Geben Sie den folgenden Befehl ein, um Diagnoseprotokolle in einem Speicherkonto zu aktivieren:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Geben Sie den folgenden Befehl ein, um Diagnoseprotokolle in einem Log Analytics-Arbeitsbereich zu aktivieren:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Nutzen von Diagnoseprotokollen aus Azure Storage
In diesem Abschnitt werden das Schema der CDN-Basisanalyse und die Organisation innerhalb eines Azure-Speicherkontos beschrieben, und er enthält Beispielcode zum Herunterladen der Protokolle in einer CSV-Datei.

### <a name="using-microsoft-azure-storage-explorer"></a>Verwenden des Microsoft Azure Storage-Explorers
Bevor Sie im Azure-Speicherkonto auf die Basisanalysedaten zugreifen können, benötigen Sie zuerst ein Tool zum Zugreifen auf den Inhalt in einem Speicherkonto. Auf dem Markt sind verschiedene Tools erhältlich, aber wir empfehlen Ihnen die Verwendung des Microsoft Azure Storage-Explorers. Informationen zum Herunterladen des Tools finden Sie unter [Azure Storage-Explorer](https://storageexplorer.com/). Konfigurieren Sie nach dem Herunterladen und Installieren der Software die Verwendung desselben Azure-Speicherkontos, das als Ziel für die CDN-Diagnoseprotokolle konfiguriert wurde.

1.  Öffnen Sie den **Microsoft Azure Storage-Explorer**.
2.  Ermitteln Sie das Speicherkonto.
3.  Erweitern Sie den Knoten **Blobcontainer** unter diesem Speicherkonto.
4.  Wählen Sie den Container mit dem Namen *insights-logs-coreanalytics* aus.
5.  Im Bereich auf der rechten Seite werden Ergebnisse angezeigt. Es wird mit der ersten Ebene begonnen: *resourceId=* . Wählen Sie weiterhin jede Ebene aus, bis Sie die Datei *PT1H.json* gefunden haben. Eine Erläuterung des Pfads finden Sie unter [Blobpfadformat](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Jede *PT1H.json*-Datei des Blobs steht für die Analyseprotokolle für eine Stunde eines bestimmten CDN-Endpunkts oder dessen benutzerdefinierter Domäne.
7.  Das Schema der Inhalte dieser JSON-Datei wird im Abschnitt „Schema der Basisanalyseprotokolle“ beschrieben.


#### <a name="blob-path-format"></a>Blobpfadformat

Basisanalyseprotokolle werden stündlich generiert. Die Daten werden gesammelt und als JSON-Nutzlast in einem einzelnen Azure-Blob gespeichert. Da das Storage-Explorer-Tool den Schrägstrich (/) als Verzeichnistrennzeichen interpretiert und die Hierarchie anzeigt, wird der Pfad zum Azure-Blob so angezeigt, als gäbe es eine hierarchische Struktur, und stellt den Blobnamen dar. Für den Namen des Blobs wird die folgende Benennungskonvention verwendet:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beschreibung der Felder:**

|Wert|BESCHREIBUNG|
|-------|---------|
|Abonnement-ID    |ID des Azure-Abonnements im GUID-Format|
|Ressourcengruppenname |Name der Ressourcengruppe, der die CDN-Ressourcen angehören|
|Profile Name (Profilname) |Name des CDN-Profils|
|Endpoint Name (Endpunktname) |Name des CDN-Endpunkts|
|Jahr|  Vierstellige Jahresangabe, z.B. 2017|
|Month (Monat)| Zweistellige Monatsangabe. 01 = Januar bis 12 = Dezember|
|Day (Tag)|   Zweistellige Tagesangabe|
|PT1H.json| JSON-Datei, in der die Analysedaten gespeichert sind|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportieren der Basisanalysedaten in eine CSV-Datei

Um den Zugriff auf Basisanalysen zu erleichtern, wird Beispielcode für ein Tool bereitgestellt. Mit diesem Tool ist das Herunterladen der JSON-Dateien in einem flachen Dateiformat mit Kommas als Trennzeichen möglich. Diese Datei kann dann zum Erstellen von Diagrammen oder anderen Aggregationen verwendet werden.

Sie können das Tool wie folgt verwenden:

1.  Nutzen Sie den GitHub-Link: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Laden Sie den Code herunter.
3.  Befolgen Sie die Anleitung zum Kompilieren und Konfigurieren.
4.  Führen Sie das Tool aus.
5.  In der sich ergebenden CSV-Datei werden die Analysedaten in einer einfachen flachen Hierarchie angezeigt.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Nutzen von Diagnoseprotokollen aus einem Log Analytics-Arbeitsbereich
Azure Monitor ist ein Azure-Dienst, der Ihre cloudbasierten und lokalen Umgebungen überwacht, um die Verfügbarkeit und Leistung sicherzustellen. Er sammelt Daten, die von Ressourcen in Ihren cloudbasierten und lokalen Umgebungen sowie von anderen Überwachungstools generiert werden, um Analysen für mehrere Quellen zu ermöglichen. 

Für die Verwendung von Azure Monitor müssen Sie die [Protokollierung im Azure Log Analytics-Arbeitsbereich aktivieren](#enable-logging-with-azure-storage). Dies wurde weiter oben in diesem Artikel beschrieben.

### <a name="using-the-log-analytics-workspace"></a>Verwenden des Log Analytics-Arbeitsbereichs

 Im folgenden Diagramm ist die Architektur der Ein- und Ausgaben des Repositorys dargestellt:

![Log Analytics-Arbeitsbereich](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Abbildung 3: Log Analytics-Repository*

Sie können die Daten mithilfe von Verwaltungslösungen auf unterschiedliche Weise anzeigen. Sie erhalten die Verwaltungslösungen über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Sie können Überwachungslösungen aus Azure Marketplace installieren, indem Sie unten in jeder Lösung den Link **Jetzt herunterladen** auswählen.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Hinzufügen einer Azure Monitor-CDN-Überwachungslösung

Führen Sie die folgenden Schritte aus, um eine Azure Monitor-Überwachungslösung hinzuzufügen:

1.   Melden Sie sich mit Ihrem Azure-Abonnement beim Azure-Portal an, und navigieren Sie zu Ihrem Dashboard.
    ![Azure-Dashboard](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Klicken Sie unter **Marketplace** auf der Seite **Neu** auf **Überwachung und Verwaltung**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Wählen Sie auf der Seite **Überwachung und Verwaltung** die Option **Alle anzeigen**.

    ![Alle anzeigen](./media/cdn-diagnostics-log/15_See-all.png)

4. Suchen Sie im Suchfeld nach „CDN“.

    ![Alle anzeigen](./media/cdn-diagnostics-log/16_Search-for.png)

5. Wählen Sie **Azure CDN-Basisanalyse**. 

    ![Alle anzeigen](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Nachdem Sie **Erstellen** ausgewählt haben, werden Sie aufgefordert, einen neuen Log Analytics-Arbeitsbereich zu erstellen oder einen vorhandenen zu verwenden. 

    ![Alle anzeigen](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Wählen Sie den Arbeitsbereich aus, den Sie weiter oben erstellt haben. Anschließend müssen Sie ein Automation-Konto hinzufügen.

    ![Alle anzeigen](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Als Nächstes wird das Formular für das Automation-Konto angezeigt, das Sie ausfüllen müssen. 

    ![Alle anzeigen](./media/cdn-diagnostics-log/20_Automation.png)

9. Nachdem Sie das Automation-Konto erstellt haben, können Sie Ihre Lösung hinzufügen. Wählen Sie die Schaltfläche **Erstellen**.

    ![Alle anzeigen](./media/cdn-diagnostics-log/21_Ready.png)

10. Ihre Lösung wurde Ihrem Arbeitsbereich hinzugefügt. Wechseln Sie zurück in Ihr Dashboard im Azure-Portal.

    ![Alle anzeigen](./media/cdn-diagnostics-log/22_Dashboard.png)

    Wählen Sie den von Ihnen erstellten Log Analytics-Arbeitsbereich aus, um darauf zuzugreifen. 

11. Wählen Sie die Kachel **OMS-Portal**, um Ihre neue Lösung anzuzeigen.

    ![Alle anzeigen](./media/cdn-diagnostics-log/23_workspace.png)

12. Ihr Portal sollte jetzt wie folgt aussehen:

    ![Alle anzeigen](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Wählen Sie eine der Kacheln aus, um verschiedene Ansichten Ihrer Daten anzuzeigen.

    ![Alle anzeigen](./media/cdn-diagnostics-log/25_Interior-view.png)

    Sie können nach links oder rechts scrollen, um weitere Kacheln anzuzeigen, die einzelne Ansichten der Daten darstellen. 

    Wählen Sie eine der Kacheln aus, um weitere Details zu Ihren Daten anzuzeigen.

     ![Alle anzeigen](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Angebote und Tarife

Angebote und Tarife für Verwaltungslösungen finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Anpassen von Ansichten

Sie können die Ansichten für Ihre Daten anpassen, in dem Sie den **Ansicht-Designer** verwenden. Um mit dem Entwerfen zu beginnen, navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich und wählen Sie die Kachel **Ansicht-Designer**.

![Ansicht-Designer](./media/cdn-diagnostics-log/27_Designer.png)

Ziehen Sie die Diagrammtypen per Drag & Drop und geben Sie die Datendetails ein, die analysiert werden sollen.

![Ansicht-Designer](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Protokolldatenverzögerungen

Die folgende Tabelle zeigt die Verzögerungen bei Protokolldaten für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Akamai** und **Azure CDN Standard/Premium von Verizon**.

Microsoft-Protokolldatenverzögerungen | Verizon-Protokolldatenverzögerungen | Akamai-Protokolldatenverzögerungen
--- | --- | ---
Um eine Stunde verzögert | Um eine Stunde verzögert, und es dauert bis zu zwei Stunden, bis die Daten nach Abschluss der Verteilung auf die Endpunkte angezeigt werden. | Um 24 Stunden verzögert. Wenn die Daten vor mehr als 24 Stunden erstellt wurden, dauert es bis zu zwei Stunden, bis sie angezeigt werden. Falls die Daten vor Kurzem erstellt wurden, kann es bis zu 25 Stunden dauern, bis die Protokolle angezeigt werden.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnoseprotokolltypen für CDN-Basisanalyse

Microsoft bietet derzeit nur Basisanalyseprotokolle an, die Metriken mit HTTP-Antwortstatistiken und -Ausgangsstatistiken für die CDN-POPs/-Edges enthalten.

### <a name="core-analytics-metrics-details"></a>Details zu Basisanalysemetriken
Die folgende Tabelle zeigt eine Liste der in den wichtigsten Analyseprotokollen verfügbaren Metriken für **Azure CDN Standard von Microsoft**, **Azure CDN Standard von Akamai** und **Azure CDN Standard/Premium von Verizon**. Nicht alle Metriken sind für alle Anbieter verfügbar, aber die Unterschiede sind nur minimal. In dieser Tabelle ist jeweils auch angegeben, ob eine Metrik von einem Anbieter verfügbar ist. Die Metriken stehen nur für die CDN-Endpunkte zur Verfügung, für die Datenverkehr anfällt.


|Metrik                     | BESCHREIBUNG | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Gesamtzahl von Anforderungstreffern während dieses Zeitraums | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Anzahl aller Anforderungen, die zum HTTP-Code 2xx geführt haben (z.B. 200, 202) | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Anzahl aller Anforderungen, die zum HTTP-Code 3xx geführt haben (z.B. 300, 302) | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Anzahl aller Anforderungen, die zum HTTP-Code 4xx geführt haben (z.B. 400, 404) | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Anzahl aller Anforderungen, die zum HTTP-Code 5xx geführt haben (z.B. 500, 504) | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Anzahl aller anderen HTTP-Codes (außerhalb des Bereichs von 2xx bis 5xx) | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 200 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus206 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 206 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus302 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 302 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus304 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 304 geführt haben | Ja | Nein  |Ja |
| RequestCountHttpStatus404 | Anzahl aller Anforderungen, die zu einer Antwort mit dem HTTP-Code 404 geführt haben | Ja | Nein  |Ja |
| RequestCountCacheHit | Anzahl aller Anforderungen, die zu einem Cachetreffer geführt haben. Das Objekt wurde direkt vom POP für den Client bereitgestellt. | Ja | Ja | Nein  |
| RequestCountCacheMiss | Anzahl aller Anforderungen, die zu einem Cachefehler geführt haben. Ein Cachefehler bedeutet, dass das Objekt nicht auf dem POP gefunden wurde, der die geringste Entfernung zum Client aufweist, und daher vom Ursprungsserver abgerufen wurde. | Ja | Ja | Nein |
| RequestCountCacheNoCache | Anzahl aller Anforderungen eines Objekts, die aufgrund einer Benutzerkonfiguration im Edgebereich nicht zwischengespeichert wurden. | Ja | Ja | Nein |
| RequestCountCacheUncacheable | Anzahl aller Anforderungen von Objekten, für die durch die Header „Cache-Control“ und „Expires“ das Zwischenspeichern verhindert wird. Mit diesen Headern wird angegeben, dass keine Zwischenspeicherung auf einem POP oder durch den HTTP-Client erfolgen soll. | Ja | Ja | Nein |
| RequestCountCacheOthers | Anzahl aller Anforderungen mit einem Cachestatus, der durch die obigen Metriken nicht abgedeckt ist | Nein | Ja | Nein  |
| EgressTotal | Datenübertragung in ausgehender Richtung in GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Datenübertragung in ausgehender Richtung* für Antworten mit dem HTTP-Statuscode 2xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus3xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 3xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus4xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 4xx in GB | Ja | Ja | Nein  |
| EgressHttpStatus5xx | Datenübertragung in ausgehender Richtung für Antworten mit dem HTTP-Statuscode 5xx in GB | Ja | Ja | Nein |
| EgressHttpStatusOthers | Datenübertragung in ausgehender Richtung für Antworten mit anderen HTTP-Statuscodes in GB | Ja | Ja | Nein  |
| EgressCacheHit | Datenübertragung in ausgehender Richtung für Antworten, die direkt aus dem CDN-Cache auf den CDN-POPs/-Edges bereitgestellt wurden | Ja | Ja | Nein |
| EgressCacheMiss | Datenübertragung in ausgehender Richtung für Antworten, die nicht auf dem nächstgelegenen POP-Server gefunden und daher vom Ursprungsserver abgerufen wurden | Ja | Ja | Nein |
| EgressCacheNoCache | Datenübertragung in ausgehender Richtung für Objekte, die aufgrund einer Benutzerkonfiguration im Edgebereich nicht zwischengespeichert wurden | Ja | Ja | Nein |
| EgressCacheUncacheable | Datenübertragung in ausgehender Richtung für Objekte, für die durch den Header „Cache-Control“ bzw. „Expires“ das Zwischenspeichern verhindert wird. Mit diesen Headern wird angegeben, dass keine Zwischenspeicherung auf einem POP oder durch den HTTP-Client erfolgen soll. | Ja | Ja | Nein |
| EgressCacheOthers | Datenübertragungen in ausgehender Richtung für andere Cacheszenarien | Nein | Ja | Nein |

*Datenübertragung in ausgehender Richtung bezieht sich hier auf Datenverkehr, der von CDN-POP-Servern auf dem Client bereitgestellt wird.


### <a name="schema-of-the-core-analytics-logs"></a>Schema der Basisanalyseprotokolle 

Alle Protokolle werden im JSON-Format gespeichert, und jeder Eintrag enthält Zeichenfolgenfelder gemäß dem folgenden Schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Hierbei steht *time* für die Startzeit der Stundengrenze, für die die Statistik gemeldet wird. Wenn eine Metrik von einem CDN-Anbieter nicht unterstützt wird, ist anstelle eines double- oder integer-Werts ein NULL-Wert vorhanden. Mit diesem NULL-Wert wird das Fehlen einer Metrik angegeben. Dies ist ein anderer Wert als der Wert 0. Auf dem Endpunkt ist pro Domäne ein Satz mit diesen Metriken konfiguriert.

Beispiele für Eigenschaften:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Azure-Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Analysieren von Azure CDN-Verwendungsmustern](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics-REST-API](https://docs.microsoft.com/rest/api/loganalytics)







