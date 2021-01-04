---
title: Diagnostizieren von Leistungsproblemen mit Azure Application Insights | Microsoft-Dokumentation
description: Tutorial zum Suchen und Diagnostizieren von Leistungsproblemen in Ihrer Anwendung mithilfe von Azure Application Insights
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 06/15/2020
ms.custom: mvc
ms.openlocfilehash: df7cfff7d5bf1b89f88105f79c072d1d7e731b31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005485"
---
# <a name="find-and-diagnose-performance-issues-with-azure-application-insights"></a>Suchen und Diagnostizieren von Leistungsproblemen mit Azure Application Insights

Azure Application Insights erfasst die Telemetriedaten Ihrer Anwendung, um die Funktion und die Leistung zu analysieren.  Sie können mit diesen Informationen ggf. Probleme identifizieren oder Verbesserungen für die Anwendung entwerfen, die eine breite Benutzergruppe erreichen.  In diesem Tutorial lernen Sie den Prozess kennen, in dem die Leistung der Serverkomponenten der Anwendung und die Perspektive des Clients analysiert wird.  Folgendes wird vermittelt:

> [!div class="checklist"]
> * Ermitteln der Leistung von serverseitigen Vorgängen
> * Analysieren von Servervorgängen zur Grundursachenermittlung von Leistungseinbußen
> * Ermitteln der langsamsten clientseitigen Vorgänge
> * Analysieren der Details der Seitenansichten mit der Abfragesprache


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit den folgenden Workloads:
    - ASP.NET und Webentwicklung
    - Azure-Entwicklung
- Stellen Sie eine .NET-Anwendung in Azure bereit, und [aktivieren Sie das Application Insights SDK](../app/asp-net.md).
- [Aktivieren Sie Application Insights Profiler](../app/profiler.md#installation) für Ihre Anwendung.

## <a name="log-in-to-azure"></a>Anmelden an Azure
Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="identify-slow-server-operations"></a>Identifizieren langsamer Servervorgänge
Application Insights erfasst die Leistungsdetails der verschiedenen Vorgänge in Ihrer Anwendung. Durch Identifizieren der Vorgänge mit der längsten Dauer können Sie potenzielle Probleme diagnostizieren oder die aktuelle Entwicklung bestmöglich auf die Verbesserung der allgemeinen Leistung der Anwendung ausrichten.

1. Klicken Sie auf **Application Insights**, und wählen Sie anschließend Ihr Abonnement aus.  
1. Um den Bereich **Leistung** zu öffnen, klicken Sie entweder im Menü **Untersuchen** auf die Option **Leistung** oder auf das Diagramm **Serverantwortzeit**.

    ![Leistung](media/tutorial-performance/1-overview.png)

2. Der Bereich **Leistung** zeigt die Anzahl und durchschnittliche Dauer der einzelnen Anwendungsvorgänge an.  Mit diesen Informationen können Sie die Vorgänge ermitteln, die die größten Auswirkungen auf Benutzer haben. In diesem Beispiel sind **GET Customers/Details** und **GET Home/Index** aufgrund der relativ hohen Dauer und der Anzahl der Aufrufe auffällige Vorgänge.  Andere Vorgänge dauern möglicherweise länger, wurden aber nur selten aufgerufen. Der Effekt einer Verbesserung wäre daher minimal.  

    ![Panel für die Leistung auf dem Server](media/tutorial-performance/2-server-operations.png)

3. Das Diagramm zeigt derzeit die durchschnittliche Dauer der ausgewählten Vorgänge im Zeitverlauf an. Sie können zum 95. Perzentil wechseln, um die Leistungsprobleme zu ermitteln. Fügen Sie die für Sie interessanten Vorgänge hinzu, indem Sie sie an das Diagramm anheften.  Dies verdeutlicht, dass es einige Spitzen gibt, die eine Untersuchung wert sind.  Isolieren Sie sie weiter, indem Sie das Zeitfenster des Diagramms verkleinern.

    ![Anheftvorgänge](media/tutorial-performance/3-server-operations-95th.png)

4.  Im Leistungsbereich auf der rechten Seite sehen Sie die Verteilung der Dauer der verschiedenen Anforderungen für den ausgewählten Vorgang.  Reduzieren Sie das Fenster, um etwa beim 95. Perzentil zu beginnen. Auf der Insights-Karte „3 Hauptabhängigkeiten“ sehen Sie auf einen Blick, dass die externen Abhängigkeiten wahrscheinlich zu einer langsamen Ausführung der Transaktionen beitragen.  Klicken Sie auf die Schaltfläche mit der Anzahl von Beispielen, um eine Liste der Beispiele anzuzeigen. Anschließend können Sie ein beliebiges Beispiel auswählen, um Transaktionsdetails anzuzeigen.

5.  Sie sehen auf einen Blick, dass der Aufruf der Azure Table-Instanz „Fabrikamaccount“ am meisten zur Gesamtdauer der Transaktion beiträgt. Darüber hinaus können Sie erkennen, dass eine Ausnahme zu einem Fehler geführt hat. Sie können auf ein beliebiges Element in der Liste klicken, um seine Details auf der rechten Seite anzuzeigen. [Weitere Informationen zur Oberfläche zur Transaktionsdiagnose](../app/transaction-diagnostics.md)

    ![Vollständige Details zum Vorgang](media/tutorial-performance/4-end-to-end.png)
    

6.  Der [**Profiler**](../app/profiler-overview.md) unterstützt Sie bei der Diagnose auf Codeebene: Er zeigt den tatsächlichen Code, der für den Vorgang ausgeführt wurde, und den Zeitaufwand für die einzelnen Schritte an. Einige Vorgänge haben möglicherweise keine Ablaufverfolgung, da der Profiler in regelmäßigen Abständen ausgeführt wird.  Im Laufe der Zeit sollten weitere Vorgänge aber Ablaufverfolgungen erhalten.  Um den Profiler für den Vorgang zu starten, klicken Sie auf **Profiler-Ablaufverfolgungen**.
5.  Die Ablaufverfolgung zeigt die einzelnen Ereignisse für jeden Vorgang an, damit Sie die Grundursache für die Dauer des gesamten Vorgangs diagnostizieren können.  Klicken Sie auf eines der obersten Beispiele, die am längsten dauern.
6.  Klicken Sie auf **Langsamster Pfad**, um den Pfad der Ereignisse zu markieren, die am meisten zur Gesamtdauer des Vorgangs beitragen.  In diesem Beispiel können Sie sehen, dass der langsamste Aufruf von der Methode *FabrikamFiberAzureStorage.GetStorageTableData* stammt. Der Teil, der den Großteil der Zeit beansprucht, ist die Methode *CloudTable.CreateIfNotExist*. Wenn diese Codezeile bei jedem Aufruf der Funktion ausgeführt wird, werden unnötige Netzwerkaufruf- und CPU-Ressourcen verbraucht. Die beste Möglichkeit zum Korrigieren des Codes besteht darin, diese Zeile in eine Startmethode einzufügen, die nur einmal ausgeführt wird.

    ![Profilerdetails](media/tutorial-performance/5-hot-path.png)

7.  Der **Leistungstipp** oben im Bildschirm belegt die Einschätzung, dass die hohe Dauer der Wartezeit geschuldet ist.  Klicken Sie auf den Link **Warten**, um die Dokumentation zu den verschiedenen Ereignistypen aufzurufen.

    ![Leistungstipp](media/tutorial-performance/6-perf-tip.png)

8.   Für eine weiterführende Analyse können Sie auf **Ablaufverfolgung herunterladen** klicken und die Ablaufverfolgung herunterladen. Sie können diese Daten mit [PerfView](https://github.com/Microsoft/perfview#perfview-overview) anzeigen.

## <a name="use-logs-data-for-server"></a>Verwenden von Protokolldaten für den Server
 Protokolle bieten eine umfangreiche Abfragesprache, mit der Sie alle von Application Insights erfassten Daten analysieren können. Sie können damit tiefgehende Analysen von Anforderungs- und Leistungsdaten ausführen.

1. Kehren Sie zum Bereich mit den Vorgangsdetails zurück, und klicken Sie auf das ![Protokollsymbol](media/tutorial-performance/app-viewinlogs-icon.png)**In Protokollen anzeigen (Analytics)** .

2. Protokolle werden mit einer Abfrage für jede der Ansichten im Bereich geöffnet.  Sie können diese Abfragen ohne weitere Veränderungen ausführen oder sie Ihren Anforderungen entsprechend ändern.  Die erste Abfrage zeigt die Dauer dieses Vorgangs im Zeitverlauf an.

    ![Protokollabfrage](media/tutorial-performance/7-request-time-logs.png)


## <a name="identify-slow-client-operations"></a>Identifizieren langsamer Clientvorgänge
Zusätzlich zum Ermitteln von optimierbaren Serverprozessen kann Application Insights die Perspektive des Clientbrowsers analysieren.  So können Sie nicht nur mögliche Verbesserungen an Clientkomponenten, sondern auch Probleme mit verschiedenen Browsern oder verschiedenen Standorten identifizieren.

1. Um eine Zusammenfassung der Browserleistung anzuzeigen, gehen Sie folgendermaßen vor: Klicken Sie unter **Untersuchen** auf **Browser** und dann auf **Browserleistung**, oder klicken Sie unter **Untersuchen** auf **Leistung**, und wechseln Sie mithilfe der Schaltfläche „Server/Browser“ oben rechts zur Registerkarte **Browser**. Dies ist eine grafische Zusammenfassung der verschiedenen Telemetrien Ihrer Anwendung aus der Perspektive des Browsers.

    ![Browserzusammenfassung](media/tutorial-performance/8-browser.png)

2. Wählen Sie mindestens einen Vorgangsnamen aus, klicken Sie unten rechts auf die blaue Schaltfläche „Beispiele“, und wählen Sie einen Vorgang aus. Damit wird eine Übersicht über die vollständigen Transaktionsdetails geöffnet, und auf der rechten Seite können Sie die **Seitenansichtseigenschaften** anzeigen. Hier können Sie die Details zum Client anzeigen, der die Seite anfordert, z.B. den Browsertyp und dessen Speicherort. Diese Informationen können Ihnen dabei helfen, zu bestimmen, ob Leistungsprobleme mit bestimmten Clienttypen zu tun haben.

    ![Seitenansicht](media/tutorial-performance/9-page-view-properties.png)

## <a name="use-logs-data-for-client"></a>Verwenden von Protokolldaten für den Client
Auf die gleiche Weise wie bei den für die Serverleistung gesammelten Daten stellt Application Insights alle Clientdaten für tiefgehende Analysen mithilfe von Protokollen zur Verfügung.

1. Kehren Sie zur Browserzusammenfassung zurück, und klicken Sie auf das ![Protokollsymbol](media/tutorial-performance/app-viewinlogs-icon.png) **In Protokollen anzeigen (Analytics)** .

2. Protokolle werden mit einer Abfrage für jede der Ansichten im Bereich geöffnet. Die erste Abfrage zeigt die Dauer für verschiedene Seitenansichten im Zeitverlauf an.

    ![Protokollabfrage](media/tutorial-performance/10-page-view-logs.png)

## <a name="next-steps"></a>Nächste Schritte
Nun, da Sie wissen, wie Sie Laufzeitausnahmen ermitteln, können Sie mit dem nächsten Tutorial fortfahren. Dort erfahren Sie, wie Sie im Falle von Fehlern Warnungen erstellen.

> [!div class="nextstepaction"]
> [Ausgeben von Warnungen zur Anwendungsintegrität](./tutorial-alert.md)

