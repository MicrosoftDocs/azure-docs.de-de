---
title: 'Leitfaden für Predictive Maintenance für Luft-und Raumfahrt: Team Data Science-Prozess'
description: Ein technischer Leitfaden für die Lösungsvorlage für Predictive Maintenance für Luft- und Raumfahrt, Versorgungsunternehmen und Transport.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 3edeee8f41c806c90f32208c0c4f174c76ba38d0
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321981"
---
# <a name="technical-guide-to-the-solution-template-for-predictive-maintenance-in-aerospace"></a>Technischer Leitfaden für die Lösungsvorlage für Predictive Maintenance für Luft- und Raumfahrt

> [!Important]
> Dieser Artikel ist veraltet. Die Diskussion über „Predictive Maintenance in Aerospace“ ist weiterhin relevant, aber aktuelle Informationen finden Sie unter [Solution Overview for Business Audiences](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace) (Lösungsübersicht für Geschäftskunden).


Lösungsvorlagen sollen den Prozess der Erstellung einer E2E-Demo beschleunigen. Durch das Bereitstellen einer Vorlage werden für Ihr Abonnement die erforderlichen Komponenten bereitgestellt und anschließend die Beziehungen dafür eingerichtet. Außerdem wird die Datenpipeline mit Beispieldaten einer Datengeneratoranwendung gestartet. Diese laden Sie auf Ihren lokalen Computer herunter und installieren sie, nachdem Sie die Lösungsvorlage bereitgestellt haben. Sobald die Datenpipeline mit den Daten der Generatoranwendung gefüllt ist, wird mit dem Erstellen von Machine Learning-Vorhersagen begonnen, die im Power BI-Dashboard visualisiert werden können.

Im Zuge der Bereitstellung führen Sie mehrere Schritte aus, bei denen Sie die Anmeldeinformationen für die Lösung einrichten. Notieren Sie sich die während der Bereitstellung angegebenen Anmeldeinformationen, z.B. Lösungsname, Benutzername und Kennwort, oder zeichnen Sie sie anderweitig auf. 


Für diesen Artikel gelten die folgenden Zielsetzungen:
- Beschreiben der Referenzarchitektur und der Komponenten unter Ihrem Abonnement
- Veranschaulichen, wie Sie die Beispieldaten durch Ihre eigenen Daten ersetzen 
- Veranschaulichen, wie Sie die Lösungsvorlage ändern  

## <a name="overview"></a>Übersicht
![Architektur für vorbeugende Wartung](./media/predictive-maintenance-technical-guide/predictive-maintenance-architecture.png)

Wenn Sie die Lösung bereitstellen, aktiviert sie Azure-Dienste wie Event Hub, Stream Analytics, HDInsight, Data Factory und Machine Learning. Das Architekturdiagramm veranschaulicht, wie die Lösungsvorlage für „Predictive Maintenance for Aerospace“ aufgebaut ist. Sie können diese Dienste im Azure-Portal untersuchen, indem Sie im Lösungsvorlagendiagramm, das bei der Bereitstellung der Lösung erstellt wurde, darauf klicken. (Eine Ausnahme ist HDInsight, da dieser Dienst bedarfsgesteuert bereitgestellt wird, wenn die zugehörigen Pipelineaktivitäten ausgeführt werden müssen und anschließend gelöscht werden.)
Laden Sie eine [Version des Diagramms in voller Größe](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png) herunter.

In den folgenden Abschnitten werden die Komponenten der Lösung beschrieben.

## <a name="data-source-and-ingestion"></a>Datenquelle und Erfassung
### <a name="synthetic-data-source"></a>Synthetische Datenquelle
Die für diese Vorlage verwendete Datenquelle wird mit einer heruntergeladenen Desktopanwendung generiert. Sie führen diese Anwendung nach dem erfolgreichen Bereitstellen der Vorlage lokal aus.

Wählen Sie zum Zugreifen auf die Anweisungen zum Herunterladen und Installieren dieser Anwendung im Lösungsvorlagendiagramm den ersten Knoten für den Datengenerator für Predictive Maintenance. Die Anweisungen befinden sich in der Eigenschaftenleiste. Diese Anwendung fügt dem [Azure Event Hub](#azure-event-hub)-Dienst Datenpunkte bzw. Ereignisse hinzu, die für den gesamten weiteren Lösungsablauf verwendet werden. Die Datenquelle ist von öffentlich zugänglichen Daten aus dem [Datenrepository der NASA](https://c3.nasa.gov/dashlink/resources/139/) (und dort konkret aus dem [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)) abgeleitet.

Die Ereignisgenerierungsanwendung füllt Azure Event Hub nur, solange sie auf dem Computer ausgeführt wird.  

### <a name="azure-event-hub"></a>Azure Event Hub  
Der [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)-Dienst nimmt die von der synthetischen Datenquelle bereitgestellten Dateneingaben als Empfänger entgegen.

## <a name="data-preparation-and-analysis"></a>Datenvorbereitung und Analyse  
### <a name="azure-stream-analytics"></a>Azure Stream Analytics
Verwenden Sie [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/), um nahezu in Echtzeit verfügbare Analysen zum Eingabedatenstrom über den [Azure Event Hub](#azure-event-hub)-Dienst bereitzustellen. Anschließend veröffentlichen Sie die Ergebnisse in einem [Power BI](https://powerbi.microsoft.com)-Dashboard und archivieren alle unformatierten eingehenden Ereignisse des [Azure Storage](https://azure.microsoft.com/services/storage/)-Diensts zur späteren Verarbeitung durch den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)-Dienst.

### <a name="hdinsight-custom-aggregation"></a>Benutzerdefinierte Aggregation mit HDInsight
Führen Sie (von Azure Data Factory orchestrierte) [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skripts mit HDInsight aus, um die von der Azure Stream Analytics-Ressource archivierten Ereignisse zu aggregieren.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Treffen Sie mithilfe des (von Azure Data Factory orchestrierten) [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Diensts basierend auf den erhaltenen Daten Vorhersagen über die Restlebensdauer eines bestimmten Flugzeugtriebwerks. 

## <a name="data-publishing"></a>Veröffentlichung der Daten
### <a name="azure-sql-database"></a>Azure SQL-Datenbank
Mit [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) speichern Sie die von Azure Machine Learning empfangenen Vorhersagen, die dann im [Power BI](https://powerbi.microsoft.com)-Dashboard genutzt werden.

## <a name="data-consumption"></a>Nutzung der Daten
### <a name="power-bi"></a>Power BI
Zeigen Sie mit [Power BI](https://powerbi.microsoft.com) ein Dashboard an, das die von [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bereitgestellten Aggregationen und Warnungen sowie die Vorhersagen zur Restlebensdauer enthält, die von [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) erstellt und in [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) gespeichert wurden.

## <a name="how-to-bring-in-your-own-data"></a>Einbringen Ihrer eigenen Daten
Dieser Abschnitt beschreibt, wie Sie Ihre eigenen Daten zu Azure übertragen und in welchen Bereichen die in diese Architektur eingebrachten eigenen Daten Änderungen erfordern.

Es ist unwahrscheinlich, dass Ihr Dataset mit dem für diese Vorlage verwendeten Dataset aus dem [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) übereinstimmt. Daher ist es entscheidend, dass Sie Ihre Daten und die Anforderungen genau verstehen. Nur dann können Sie diese Vorlage so abändern, dass sie mit Ihren eigenen Daten ordnungsgemäß funktioniert. 

In den folgenden Abschnitten werden die Teile der Vorlage erläutert, die beim Verwenden eines neuen Datasets geändert werden müssen.

### <a name="azure-event-hub"></a>Azure Event Hub
Azure Event Hub ist sehr allgemein angelegt. Daten können im CSV-Format oder im JSON-Format an den Hub gesendet werden. Im Azure Event Hub wird keine spezielle Verarbeitung ausgeführt, aber es ist wichtig, dass Sie die an ihn übermittelten Daten wirklich verstehen.

In diesem Dokument wird nicht beschrieben, wie Sie Daten erfassen. Es ist jedoch einfach, Ereignisse oder Daten über die Event Hub-APIs an Azure Event Hub zu senden.

### <a name="azure-stream-analytics"></a><a name="azure-stream-analytics-1"></a>Azure Stream Analytics
Führen Sie mit der Azure Stream Analytics-Ressource Analysen in Quasi-Echtzeit durch. Die Datenströme werden gelesen, und Daten können an eine beliebige Anzahl von Quellen ausgegeben werden.

Bei der Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt besteht die Azure Stream Analytics-Abfrage aus vier Unterabfragen, die jeweils Ereignisse aus dem Azure Event Hub-Dienst nutzen und Ausgabedaten für vier verschiedene Speicherorte erzeugen. Diese Ausgabedaten bestehen aus drei Power BI-Datasets und einem Azure Storage-Speicherort.

Sie finden die Azure Stream Analytics-Abfrage, indem Sie folgende Schritte ausführen:

* Stellen Sie eine Verbindung mit dem Azure-Portal her.
* Suchen Sie die Stream Analytics-Aufträge ![Stream Analytics-Symbol](./media/predictive-maintenance-technical-guide/icon-stream-analytics.png), die beim Bereitstellen der Lösung generiert wurden ( *z. B.* **maintenancesa02asapbi** und **maintenancesa02asablob** für die Lösung „Predictive Maintenance“).
* Wählen Sie Folgendes aus:
  
  * ***INPUTS** _ zum Anzeigen der Abfrageeingabe _ * **QUERY** _ zum Anzeigen der Abfrage selbst _ * **OUTPUTS** _ zum Anzeigen der verschiedenen Ausgaben

Informationen zum Aufbau von Azure Stream Analytics-Abfragen finden Sie auf MSDN unter [Stream Analytics-Abfragereferenz](/stream-analytics-query/stream-analytics-query-language-reference) .

In dieser Lösung geben die Abfragen drei Datasets mit nahezu in Echtzeit gewonnenen Analyseinformationen über den eingehenden Datenstrom an ein Power BI-Dashboard aus, das als Teil der Lösungsvorlage zur Verfügung gestellt wird. Da diesen Abfragen ein implizites Wissen über das Format der eingehenden Daten zugrunde liegt, müssen sie entsprechend Ihrem eigenen Datenformat geändert werden.

Die Abfrage im zweiten Stream Analytics-Auftrag _ *maintenancesa02asablob* * gibt einfach nur alle [Event Hub](https://azure.microsoft.com/services/event-hubs/)-Ereignisse an [Azure Storage](https://azure.microsoft.com/services/storage/) aus und muss deshalb unabhängig von Ihrem Datenformat nicht geändert werden. Die Ereignisinformationen werden in jedem Fall vollständig in den Speicher übertragen.

### <a name="azure-data-factory"></a>Azure Data Factory
Der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) -Dienst koordiniert die Verschiebung und Verarbeitung von Daten. In der Lösungsvorlage für „Predictive Maintenance for Aerospace“ besteht die Data Factory aus drei [Pipelines](../../data-factory/concepts-pipelines-activities.md), die die Daten mit verschiedenen Technologien verschieben und verarbeiten.  Greifen Sie auf Ihre Data Factory zu, indem Sie in dem Lösungsvorlagendiagramm, das bei der Bereitstellung der Lösung erstellt wurde, unten auf den Knoten für Data Factory klicken. Fehler unter Ihren Datasets sind entstanden, weil die Data Factory vor dem Start des Datengenerators bereitgestellt wurde. Diese Fehler können ignoriert werden und verhindern nicht, dass Ihre Data Factory ordnungsgemäß funktioniert.

![Data Factory-Datasetfehler](./media/predictive-maintenance-technical-guide/data-factory-dataset-error.png)

Dieser Abschnitt erläutert die erforderlichen [Pipelines und Aktivitäten](../../data-factory/concepts-pipelines-activities.md) in der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Hier folgt die Diagrammansicht der Lösung.

![Azure Data Factory](./media/predictive-maintenance-technical-guide/azure-data-factory.png)

Zwei der Pipelines dieser Factory enthalten [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skripts, die zum Partitionieren und Aggregieren der Daten verwendet werden. Wenn angemerkt, werden die Skripte in dem bei der Einrichtung erstellten [Azure Storage](https://azure.microsoft.com/services/storage/)-Konto abgelegt. Dabei wird folgender Speicherort verwendet: maintenancesascript\\\\script\\\\hive\\\\ (oder https://[Lösungsname].blob.core.windows.net/maintenancesascript).

Ähnlich wie bei [Azure Stream Analytics](#azure-stream-analytics-1)-Abfragen liegt den [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skripts ein implizites Wissen über das Format der eingehenden Daten zugrunde. Diese Skripts müssen daher gemäß Ihrem Datenformat geändert werden.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Diese [Pipeline](../../data-factory/concepts-pipelines-activities.md) enthält eine einzige Aktivität: eine [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md)-Aktivität, die mithilfe von [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) ein [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skript ausführt, um die während des [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Auftrags in [Azure Storage](https://azure.microsoft.com/services/storage/) abgelegten Daten zu partitionieren.

Das [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skript für diese Partitionierungsaufgabe heißt * **AggregateFlightInfo.hql** _.

#### <a name="_mlscoringpipeline"></a>_MLScoringPipeline*
Diese [Pipeline](../../data-factory/concepts-pipelines-activities.md) enthält verschiedene Aktivitäten, deren Endergebnis die bewerteten Vorhersagen aus dem [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment sind, das dieser Lösungsvorlage zugeordnet ist.

Zu den enthaltenen Aktivitäten zählen:

* Eine [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md)-Aktivität, die mithilfe von [HDInsightLinkedService](/previous-versions/azure/dn893526(v=azure.100)) ein [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skript ausführt, um Aggregationen und die Featureentwicklung durchzuführen. Beides wird für das [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment benötigt.
  Das [Hive](/archive/blogs/uk_faculty_connection/getting-started-with-microsoft-big-data-hive-hdinsight-jump-start)-Skript für diese Partitionierungsaufgabe heißt * **PrepareMLInput.hql** _.
  _ Eine [Copy](/previous-versions/azure/dn835035(v=azure.100))-Aktivität, die die Ergebnisse aus der [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md)-Aktivität in ein einzelnes [Azure Storage](https://azure.microsoft.com/services/storage/)-Blob verschiebt, auf das die [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100))-Aktivität zugreift.
* Eine [AzureMLBatchScoring](/previous-versions/azure/dn894009(v=azure.100))-Aktivität ruft das [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment auf. Dies bewirkt, dass die Ergebnisse in einem einzelnen [Azure Storage](https://azure.microsoft.com/services/storage/)-Blob abgelegt werden.

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Diese [Pipeline](../../data-factory/concepts-pipelines-activities.md) enthält eine einzige Aktivität: eine [Copy](/previous-versions/azure/dn835035(v=azure.100))-Aktivität, die die Ergebnisse des [Azure Machine Learning](#azure-machine-learning)-Experiments aus * **MLScoringPipeline** _ in die bei der Installation der Lösungsvorlage bereitgestellte [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) verschiebt.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Das für diese Lösungsvorlage verwendete [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment gibt die Restlebensdauer (Remaining Useful Life, RUL) für ein Flugzeugtriebwerk an. Dieses Experiment ist von dem hier genutzten Dataset abhängig und muss für die von Ihnen eingebrachten Daten angepasst oder ersetzt werden.

## <a name="monitor-progress"></a>Überwachen des Fortschritts
Nach dem Start des Daten-Generators füllt sich die Pipeline mit Daten, und die verschiedenen Komponenten der Lösung beginnen, im Anschluss an die von der Data Factory aufgerufenen Befehle in Aktion zu treten. Es gibt zwei Möglichkeiten zum Überwachen der Pipeline.

_ Einer der Stream Analytics-Aufträge schreibt die unformatierten eingehenden Daten in Blobspeicher. Wenn Sie auf dem Bildschirm, auf dem Sie die Lösung erfolgreich bereitgestellt haben, auf die Komponente Blob Storage und anschließend im rechten Bereich auf „Öffnen“ klicken, gelangen Sie zum [Azure-Portal](https://portal.azure.com/). Klicken Sie dort auf „Blobs“. Im nächsten Bereich sehen Sie eine Liste mit Containern. Klicken Sie auf **maintenancesadata**. Im nächsten Bereich befindet sich der Ordner **rawdata**. Innerhalb des Ordners „rawdata“ befinden sich Ordner mit Namen wie „hour=17“ und „hour=18“. Das Vorhandensein dieser Ordner zeigt an, dass die unformatierten Daten auf Ihrem Computer erstellt und im Blobspeicher gespeichert wurden. In diesen Ordnern sollten CSV-Dateien mit Größen in MB angezeigt werden.
* Der letzte Schritt der Pipeline ist das Schreiben von Daten (z. B. Prognosen aus Machine Learning) in die SQL-Datenbank. Möglicherweise müssen Sie bis zu drei Stunden warten, bis die Daten in Azure SQL-Datenbank angezeigt werden. Die Menge der in Ihrer SQL-Datenbank verfügbaren Daten kann unter anderem über das [Azure-Portal](https://portal.azure.com/) überwacht werden. Suchen Sie im linken Bereich nach SQL-DATENBANKEN :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-SQL-databases.png" border="false":::, und klicken Sie darauf. Suchen Sie Ihre Datenbank **pmaintenancedb** , und klicken Sie darauf. Klicken Sie auf der nächsten Seite unten auf VERWALTEN.
   
    ![Symbol „Verwalten“](./media/predictive-maintenance-technical-guide/icon-manage.png)
   
    Hier können Sie auf „Neue Abfrage“ klicken und dann die Anzahl der Zeilen (z. B. „select count(*)“ aus PMResult) abfragen. Wenn Ihre Datenbank wächst, nimmt auch die Anzahl der Zeilen in der Tabelle zu.

## <a name="power-bi-dashboard"></a>Power BI-Dashboard

Richten Sie ein Power BI-Dashboard ein, um Ihre Daten aus Azure Stream Analytics zu visualisieren („heiße Daten“) und die Batchvorhersageergebnisse aus Azure Machine Learning anzuzeigen („kalte Daten“).

### <a name="set-up-the-cold-path-dashboard"></a>Einrichten des Dashboards für kalte Daten
Bei der Datenpipeline für kalte Daten besteht das Ziel darin, die vorhergesagte Restlebensdauer jedes Flugzeugtriebwerks abzurufen, nachdem ein Flug oder Flugzyklus abgeschlossen ist. Das Vorhersageergebnis wird alle drei Stunden aktualisiert, um Vorhersagen für die Flugzeugtriebwerke zu erhalten, die in den letzten drei Stunden einen Flug beendet haben.

Power BI stellt eine Verbindung mit einer Azure SQL-Datenbank als Datenquelle her, denn dort werden die Vorhersageergebnisse gespeichert. 

Hinweis: 
1.    Nach dem Bereitstellen Ihrer Lösung wird alle drei Stunden eine Vorhersage in der Datenbank angezeigt. Die PBIX-Datei, die Teil des Downloads für den Generator ist, enthält einige Seedingdaten, sodass Sie das Power BI-Dashboard sofort erstellen können. 
2.    Die Voraussetzung für diesen Schritt ist das Herunterladen und Installieren der kostenlosen Software [Power BI Desktop](/power-bi/fundamentals/desktop-get-the-desktop).

In den folgenden Schritten wird erklärt, wie Sie die PBIX-Datei mit der SQL-Datenbank verbinden, die beim Bereitstellen der Lösung in Betrieb genommen wurde und die die Daten (z. B. Vorhersageergebnisse) für die Visualisierung enthält.

1. Rufen Sie die Anmeldeinformationen für die Datenbank ab.
   
   Sie benötigen **den Namen des Datenbankservers, den Namen der Datenbank, den Benutzernamen und das Kennwort** , bevor Sie die nächsten Schritte ausführen können. Sie finden diese Informationen anhand der folgenden Schritte.
   
   * Sobald **Azure SQL-Datenbank** in Ihrem Lösungsvorlagendiagramm grün dargestellt wird, klicken Sie auf diese Option und anschließend auf **Öffnen**.
   * Es wird eine neue Registerkarte im Browser oder ein neues Browserfenster mit der Azure-Portalseite angezeigt. Klicken Sie im linken Bereich auf **Ressourcengruppen** .
   * Wählen Sie das Abonnement aus, das Sie zum Bereitstellen der Lösung verwenden, und wählen Sie dann **IhrLösungsname\_Ressourcengruppe** aus.
   * Klicken Sie im daraufhin angezeigten Popupbereich auf das Symbol :::image type="icon" source="./media/predictive-maintenance-technical-guide/icon-sql.png" border="false":::, um auf Ihre Datenbank zuzugreifen. Neben diesem Symbol wird der Name Ihrer Datenbank angezeigt (z.B. **pmaintenancedb** ), und der **Name des Datenbankservers** wird unter der Eigenschaft „Servername“ aufgelistet. Er sollte etwa wie folgender Name aussehen: **IhrLösungsname.database.windows.net**.
   * Der **Benutzername** und das **Kennwort** für Ihre Datenbank sind identisch mit dem Benutzernamen und dem Kennwort, die Sie sich zuvor bei der Bereitstellung der Lösung notiert haben.
2. Aktualisieren Sie die Datenquelle der Berichtsdatei für kalte Daten mit Power BI Desktop.
   
   * Doppelklicken Sie in dem Ordner, in dem Sie die heruntergeladene Generatordatei entzippt haben, auf die Datei **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Wenn beim Öffnen der Datei Warnmeldungen angezeigt werden, ignorieren Sie sie. Klicken Sie über der Datei auf **Abfragen bearbeiten**.
     
     ![Abfragen bearbeiten](./media/predictive-maintenance-technical-guide/edit-queries.png)
   * Sie sehen die beiden Tabellen **RemainingUsefulLife** und **PMResult**. Wählen Sie die erste Tabelle aus, und klicken Sie rechts im Bereich **Abfrageeinstellungen** unter **ANGEWENDETE SCHRITTE** neben **Quelle** auf ![Symbol für Abfrageeinstellungen](./media/predictive-maintenance-technical-guide/icon-query-settings.png). Ignorieren Sie alle angezeigten Warnmeldungen.
   * Ersetzen Sie **Server** und **Datenbank** im Ausklappfenster durch Ihre eigenen Server- und Datenbanknamen, und klicken Sie anschließend auf **OK**. Geben Sie beim Servernamen unbedingt den Port 1433 an ( **IhrLösungsname.database.windows.net, 1433** ). Lassen Sie im Feld „Datenbank“ **Pmaintenancedb** unverändert. Ignorieren Sie die auf dem Bildschirm angezeigten Warnmeldungen.
   * Im nächsten Ausklappfenster werden im linken Bereich zwei Optionen angezeigt: **Windows** und **Datenbank**. Klicken Sie auf **Datenbank** , und geben Sie **Benutzername** und **Kennwort** ein. (Hierbei handelt es sich um den Benutzernamen und das Kennwort, die Sie beim ersten Bereitstellen der Lösung und beim erstmaligen Erstellen einer Azure SQL-Datenbank eingegeben haben.) Aktivieren Sie unter **_Wählen Sie die Ebene aus, auf die diese Einstellungen anzuwenden sind:_ *_ die Option für die Datenbankebene. Klicken Sie dann auf _* Verbinden**.
   * Klicken Sie auf die zweite Tabelle **PMResult** und dann rechts im Bereich **Abfrageeinstellungen** unter **ANGEWENDETE SCHRITTE** neben **Quelle** auf ![Navigationssymbol](./media/predictive-maintenance-technical-guide/icon-navigation.png). Aktualisieren Sie die Server- und Datenbanknamen wie in den Schritten zuvor, und klicken Sie auf „OK“.
   * Wenn die vorherige Seite wieder angezeigt wird, schließen Sie das Fenster. Es wird eine Meldung angezeigt. Klicken Sie auf **Übernehmen**. Klicken Sie zum Schluss auf die Schaltfläche **Speichern** , um die Änderungen zu speichern. Für Ihre Power BI-Datei wurde nun eine Verbindung mit dem Server eingerichtet. Wenn Ihre Visualisierungen leer sind, stellen Sie sicher, dass Sie die Auswahl für die Visualisierungen aufheben, um alle Daten zu visualisieren. Klicken Sie dazu in der rechten oberen Ecke der Legenden auf das Radierersymbol. Klicken Sie auf die Schaltfläche zum Aktualisieren, damit neue Daten in den Visualisierungen widergespiegelt werden. Anfangs sehen Sie in den Visualisierungen nur die Seedingdaten, weil die Data Factory planmäßig alle 3 Stunden aktualisiert wird. Wenn Sie die Daten nach drei Stunden aktualisieren, sehen Sie in den Visualisierungen neue Vorhersagen.
3. (Optional) Veröffentlichen des Dashboards für kalte Daten in [Power BI online](https://www.powerbi.com/). Für diesen Schritt benötigen Sie ein Power BI-Konto (oder ein Geschäfts-, Schul- oder Unikonto).
   
   * Klicken Sie auf **Veröffentlichen** . Nach einigen Sekunden wird durch eine Meldung mit einem grünen Häkchen bestätigt, dass die Veröffentlichung in Power BI erfolgreich war. Klicken Sie auf den Link unter „PredictiveMaintenanceAerospace.pbix in Power BI öffnen“. Ausführliche Anweisungen finden Sie unter [Veröffentlichen aus Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Klicken Sie zum Erstellen eines neuen Dashboards im linken Bereich neben dem Abschnitt **Dashboards** auf das Symbol **+** . Geben Sie für dieses neue Dashboard den Namen „Demo für vorbeugende Wartung“ ein.
   * Wenn Sie den Bericht geöffnet haben, klicken Sie auf ![PIN-Symbol](./media/predictive-maintenance-technical-guide/icon-pin.png), um alle Visualisierungen an Ihr Dashboard anzuheften. Ausführliche Informationen finden Sie unter [Anheften einer Kachel an ein Power BI-Dashboard aus einem Bericht](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Wechseln Sie zur Dashboardseite, passen Sie die Größe und Position der Visualisierungen an, und bearbeiten Sie deren Titel. Ausführliche Anweisungen zum Bearbeiten der Titel finden Sie unter [Bearbeiten einer Kachel – Größe ändern, verschieben, umbenennen, anheften, löschen, Link hinzufügen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Nachfolgend sehen Sie ein Beispieldashboard, an das einige Visualisierungen für kalte Daten angeheftet sind.  Abhängig davon, wie lange Sie Ihren Datengenerator ausführen, unterscheiden sich die Zahlen in Ihren Visualisierungen unter Umständen.
     <br/>
     ![Endansicht](./media/predictive-maintenance-technical-guide/final-view.png)
     <br/>
   * Um eine Aktualisierung der Daten zu planen, zeigen Sie auf das Dataset **PredictiveMaintenanceAerospace** , klicken Sie auf die ![Auslassungspunkte](./media/predictive-maintenance-technical-guide/icon-elipsis.png), und wählen Sie anschließend **Aktualisierung planen** aus.
     <br/>
     > [!NOTE]
     > Wenn eine Warnmeldung angezeigt wird, klicken Sie auf **Anmeldeinformationen bearbeiten** , und vergewissern Sie sich, dass die Anmeldeinformationen für die Datenbank mit den in Schritt 1 angegebenen Informationen identisch sind.
     <br/>
     ![Aktualisierung planen](./media/predictive-maintenance-technical-guide/schedule-refresh.png)
     <br/>
   * Erweitern Sie den Abschnitt **Aktualisierung planen** . Aktivieren Sie „Halten Sie Ihre Daten aktuell“.
     <br/>
   * Legen Sie einen geeigneten Zeitplan für die Aktualisierung fest. Weitere Informationen finden Sie unter [Aktualisieren von Daten in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Einrichten des Dashboards für heiße Daten
In den folgenden Schritten wird erklärt, wie Sie die Datenausgabe aus den beim Bereitstellen der Lösung generierten Stream Analytics-Aufträgen visualisieren. Zum Ausführen der folgenden Schritte benötigen Sie ein Konto für [Power BI online](https://www.powerbi.com/) . Wenn Sie über kein Konto verfügen, können Sie [eines erstellen](https://powerbi.microsoft.com/pricing).

1. Fügen Sie die Power BI-Ausgabe in Azure Stream Analytics hinzu.
   
   * Folgen Sie den Anweisungen unter [Azure Stream Analytics und Power BI: Ein Dashboard mit Analyse und Echtzeitsichtbarkeit von Streamingdaten](../../stream-analytics/stream-analytics-power-bi-dashboard.md), um die Ausgabe des Azure Stream Analytics-Auftrags an Ihr Power BI-Dashboard einzurichten.
   * Die Azure Stream Analytics-Abfrage hat die drei Ausgaben **aircraftmonitor** , **aircraftalert** und **flightsbyhour**. Sie können die Abfrage anzeigen, indem Sie auf die Registerkarte „Abfrage“ klicken. Für jede dieser Tabellen müssen Sie ASA eine Ausgabe hinzufügen. Stellen Sie beim Hinzufügen der ersten Ausgabe ( **aircraftmonitor** ) sicher, dass **Ausgabealias** , **Datasetname** und **Tabellenname** identisch sind ( **aircraftmonitor** ). Wiederholen Sie die Schritte zum Hinzufügen von Ausgaben für **aircraftalert** und **flightsbyhour**. Nachdem Sie alle drei Ausgabetabellen hinzugefügt und den ASA-Auftrag gestartet haben, sollte eine Bestätigungsmeldung angezeigt werden. („Der Stream Analytics-Auftrag "maintenancesa02asapbi" wurde erfolgreich gestartet.“)
2. Melden Sie sich bei [Power BI online](https://www.powerbi.com)
   
   * Im Abschnitt „Datasets“ des linken Bereichs in „Mein Arbeitsbereich“ sollten die * **DATASET** _-Namen _*aircraftmonitor**, **aircraftalert** und **flightsbyhour** angezeigt werden. die Sie zuvor in den Power BI-Ausgabeeinstellungen des Azure Stream Analytics-Auftrags definiert haben. Das Dataset **flightsbyhour** wird möglicherweise aufgrund der Natur der zugrunde liegende SQL-Abfrage nicht zur gleichen Zeit wie die anderen zwei Datasets angezeigt. Es sollte jedoch nach einer Stunde angezeigt werden.
   * Stellen Sie sicher, dass der Bereich * **Visualisierungen** _ geöffnet ist und auf der rechten Seite des Bildschirms angezeigt wird.
3. Sobald Daten in Power BI einströmen, können Sie mit dem Visualisieren des Datenstroms beginnen. Nachfolgend sehen Sie ein Beispieldashboard, an das einige Visualisierungen für heiße Daten angeheftet sind. Auf Basis der entsprechenden Datasets können Sie weitere Dashboardkacheln erstellen. Abhängig davon, wie lange Sie Ihren Datengenerator ausführen, unterscheiden sich die Zahlen in Ihren Visualisierungen unter Umständen.

    ![Dashboardansicht](media/predictive-maintenance-technical-guide/dashboard-view.png)

1. Es folgen die Schritte zum Erstellen einer der oben genannten Kacheln: Fleet View of Sensor 11 vs. Threshold 48.26:
   
   _ Klicken Sie im linken Bereich im Abschnitt „Datasets“ auf das Dataset **aircraftmonitor**.
   * Klicken Sie auf das Symbol **Liniendiagramm** .
   * Klicken Sie im Bereich **Felder** auf **Verarbeitet** , sodass das Feld im Bereich **Visualisierungen** unter „Achse“ angezeigt wird.
   * Klicken Sie auf „s11“ und „s11alert\_alert“, sodass beide unter „Werte“ angezeigt werden. Klicken Sie auf den kleinen Pfeil neben **s11** und **s11\_alert** , und ändern Sie „Summe“ in „Durchschnitt“.
   * Klicken Sie oben auf **SPEICHERN** , und nennen Sie den Bericht „Flugzeugmonitor“. Der Bericht „Flugzeugmonitor“ wird links im Bereich **Navigator** im Abschnitt **Berichte** angezeigt.
   * Klicken Sie in der oberen rechten Ecke dieses Liniendiagramms auf **Visualisierung anheften**. Möglicherweise wird ein Fenster „An das Dashboard anheften“ angezeigt, in dem Sie ein Dashboard auswählen können. Wählen Sie „Predictive Maintenance-Demo“ aus, und klicken Sie dann auf „Anheften“.
   * Zeigen Sie mit dem Mauszeiger auf diese Kachel auf dem Dashboard, klicken Sie in der oberen rechten Ecke auf das Symbol „Bearbeiten“, und ändern Sie den Titel in „Flottenansicht von Sensor 11 im Vergleich zu Schwellenwert 48,26“ und den Untertitel in „Flottendurchschnitt im Zeitverlauf“.

## <a name="delete-your-solution"></a>Löschen der Lösung
Stellen Sie sicher, dass Sie den Datengenerator beenden, wenn Sie die Lösung nicht aktiv nutzen, da das Ausführen des Datengenerators höhere Kosten verursacht. Löschen Sie die Lösung, wenn Sie sie nicht verwenden. Beim Löschen Ihrer Lösung werden alle dafür in Ihrem Abonnement bereitgestellten Komponenten gelöscht. Klicken Sie zum Löschen der Lösung im linken Bereich der Lösungsvorlage auf deren Namen und dann auf **Löschen**.

## <a name="cost-estimation-tools"></a>Kostenschätzungstools
Es stehen zwei Tools zur Verfügung, mit denen Sie die Gesamtkosten zum Ausführen der Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt im Rahmen Ihres Abonnements ermitteln können:

* [Microsoft Azure-Kostenschätzungstool (online)](https://azure.microsoft.com/pricing/calculator/)
* [Microsoft Azure-Kostenschätzungstool (Desktopversion)](https://www.microsoft.com/download/details.aspx?id=43376)