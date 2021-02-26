---
title: Einführung in ML Services in Azure HDInsight
description: Erfahren Sie, wie Sie ML Services in HDInsight zum Erstellen von Anwendungen für Big Data-Analysen verwenden.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943918"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Worum handelt es sich bei ML Services in Azure HDInsight?

Microsoft Machine Learning Server ist als Bereitstellungsoption bei der Erstellung von HDInsight-Clustern in Azure verfügbar. Der Clustertyp, der diese Option bietet, heißt **ML Services**. Mit dieser Funktion haben Sie bedarfsgesteuerten Zugriff auf anpassbare verteilte Analysemethoden in HDInsight.

ML Services in HDInsight verfügt über die aktuellen Funktionen für R-basierte Analysen jedes Umfangs. Die Datasets können in Azure-Blobspeicher oder Data Lake Storage geladen werden. Für Ihre R-basierten Anwendungen können die Open-Source-Pakete der Serie 8000+ von R verwendet werden. Die Routinen in ScaleR, dem Big Data-Analysepaket von Microsoft, stehen ebenfalls zur Verfügung.

Der Edgeknoten ist ein praktischer Ort für die Verbindungsherstellung mit dem Cluster und die Ausführung Ihrer R-Skripts. Über den Edgeknoten können Sie die parallelisierten verteilten ScaleR-Funktionen übergreifend für die Kerne des Servers ausführen. Außerdem können Sie sie auf allen Knoten des Clusters ausführen, indem Sie Hadoop MapReduce von ScaleR verwenden. Sie können auch Apache Spark-Computekontexte nutzen.

Die Modelle oder Vorhersagen, die sich aus der Analyse ergeben, können für die lokale Verwendung heruntergeladen werden. Sie können auch an anderer Stelle in Azure operationalisiert (`operationalized`) werden. Genauer gesagt per [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) und [Webdienst](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Erste Schritte mit ML Services in HDInsight

Wählen Sie zum Erstellen eines ML Services-Clusters in HDInsight den Clustertyp **ML Services** aus. Beim Clustertyp „ML Services“ ist ML Server auf den Datenknoten enthalten, und es wird ein Edgeknoten verwendet. Der Edgeknoten dient als Zielzone für ML Services-basierte Analysen. Unter [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) finden Sie eine exemplarische Vorgehensweise zum Erstellen des Clusters.

## <a name="why-choose-ml-services-in-hdinsight"></a>Gründe für die Wahl von ML Services in HDInsight

ML Services in HDInsight bietet folgende Vorteile:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>KI-Innovation von Microsoft und Open-Source

  ML Services umfasst einen verteilten Satz mit Algorithmen, z. B. [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) und [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package), der stark angepasst werden kann. Mit diesen Algorithmen können Datengrößen verarbeitet werden, die größer als der physische Speicher sind. Darüber hinaus werden sie auf verteilte Weise auf vielen verschiedenen Plattformen ausgeführt. Erfahren Sie mehr über die Sammlung der angepassten [R-Pakete](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) und [Python-Pakete](/machine-learning-server/python-reference/introducing-python-package-reference) von Microsoft, die im Lieferumfang des Produkts enthalten sind.
  
  ML Services verbindet diese Microsoft-Innovationen mit Beiträgen aus der Open-Source-Community (R-, Python- und KI-Toolkits). Alles auf einer zentralen Plattformen, die für Unternehmen geeignet ist. Jedes Open-Source-Paket für maschinelles Lernen in R oder Python funktioniert parallel mit jeder proprietären Innovation von Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Einfache, sichere und hochgradig skalierbare Operationalisierung und Verwaltung

  Unternehmen, die auf traditionelle Paradigmen und Umgebungen bauen, investieren viel Zeit und Mühe in die Operationalisierung. Diese Aktion führt zu überhöhten Kosten und zu Verzögerungen, einschließlich der Übersetzungszeit, in Bezug auf Modelle, Iterationen (um sie gültig und aktuell zu halten), behördliche Genehmigungen und die Berechtigungsverwaltung.

  ML Services ermöglicht eine [Operationalisierung](/machine-learning-server/what-is-operationalization) auf Unternehmensniveau. Nachdem ein Machine Learning-Modell abgeschlossen wurde, benötigen Sie nur wenige Klicks, um Webdienst-APIs zu generieren. Diese [Webdienste](/machine-learning-server/operationalize/concept-what-are-web-services) werden auf einem Serverraster in der Cloud gehostet und können mit Branchenanwendungen integriert werden. Durch die Möglichkeit der Bereitstellung in einem elastischen Raster können Sie nahtlos mit den Anforderungen Ihres Unternehmens skalieren, sowohl für Batch- als auch für Echtzeitbewertung. Anweisungen finden Sie unter [Operationalisieren von ML Services in HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Der Clustertyp „ML Services“ in HDInsight wird nur in HDInsight 3.6 unterstützt. HDInsight 3.6 wird voraussichtlich am 31. Dezember 2020 eingestellt.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Zentrale Features von ML Services in HDInsight

Die folgenden Features sind in ML Services in HDInsight enthalten.

| Featurekategorie | BESCHREIBUNG |
|------------------|-------------|
| R-fähig | [R-Pakete](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) für in R erstellte Lösungen mit einer Open-Source-Distribution von R und einer Laufzeitinfrastruktur für die Skriptausführung. |
| Python-fähig | [Python-Module](/machine-learning-server/python-reference/introducing-python-package-reference) für in Python erstellte Lösungen mit einer Open-Source-Distribution von Python und einer Laufzeitinfrastruktur für die Skriptausführung.
| [Vortrainierte Modelle](/machine-learning-server/install/microsoftml-install-pretrained-models) | Für visuelle Analyse und Textempfindungsanalyse, bereit zur Bewertung der Daten, die Sie zur Verfügung stellen. |
| [Bereitstellen und Nutzen](r-server-operationalize.md) | Führen Sie für Ihren Server einen `Operationalize`-Vorgang aus, und stellen Sie Lösungen als Webdienst bereit. |
| [Remoteausführung](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starten Sie Remotesitzungen auf ML Services-Clustern in Ihrem Netzwerk von Ihrer Clientarbeitsstation aus. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Datenspeicherungsoptionen für ML Services in HDInsight

Der Standardspeicher für das HDFS-Dateisystem kann ein Azure Storage-Konto oder Azure Data Lake Storage sein. Die Daten, die während der Analyse in den Clusterspeicher hochgeladen werden, werden dauerhaft gespeichert. Sie stehen daher auch nach dem Löschen des Clusters zur Verfügung. Die Datenübertragung an den Speicher kann mit verschiedenen Tools durchgeführt werden. Zu diesen Tools gehören beispielsweise die Uploadfunktion des Speicherkontos im Portal und das Hilfsprogramm AzCopy.

Sie können den Zugriff auf weiteren Blobspeicher und Data Lake Storage während der Clustererstellung aktivieren. Sie sind bei der Nutzung nicht auf die primäre Speicheroption beschränkt.  Weitere Informationen zur Verwendung von mehreren Speicherkonten finden Sie unter [Azure Storage-Optionen für ML Services in HDInsight](./r-server-storage.md).

Außerdem können Sie Azure Files als Speicheroption für den Edgeknoten wählen. Azure Files ermöglicht die Nutzung von in Azure Storage erstellten Dateifreigaben für das Linux-Dateisystem. Weitere Informationen finden Sie unter [Azure Storage-Lösungen für ML Services in HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Zugreigen auf ML Services-Edgeknoten

Sie können über einen Browser oder SSH/PuTTY eine Verbindung mit ML Server auf dem Edgeknoten herstellen. Die R-Konsole wird standardmäßig während der Clustererstellung installiert.  

## <a name="develop-and-run-r-scripts"></a>Entwickeln und Ausführen von R-Skripts

Für Ihre R-Skripts können alle Open-Source-Pakete der Serie 8000+ von R verwendet werden. Sie können auch die parallelisierten und verteilten Routinen aus der ScaleR-Bibliothek verwenden. Auf dem Edgeknoten ausgeführte Skripts werden über den R-Interpreter des Knotens ausgeführt. Eine Ausnahme stellen die Schritte dar, bei denen ScaleR-Funktionen mit einem Computetext vom Typ MapReduce (RxHadoopMR) oder Spark (RxSpark) aufgerufen werden. Die Funktionen werden auf verteilte Weise auf den Datenknoten ausgeführt, die den Daten zugeordnet sind. Weitere Informationen zu den Kontextoptionen finden Sie unter [Computekontextoptionen für ML Services in HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Ausführen eines `Operationalize`-Vorgangs für ein Modell

Führen Sie nach Abschluss der Datenmodellierung für das Modell einen `operationalize`-Vorgang aus, um in Azure oder lokal Vorhersagen über neue Daten treffen zu können. Dieser Prozess wird als Bewertung bezeichnet. Eine Bewertung kann in HDInsight, Azure Machine Learning oder lokal ausgeführt werden.

### <a name="score-in-hdinsight"></a>Bewertung in HDInsight

Schreiben Sie eine R-Funktion, um die Bewertung in HDInsight durchzuführen. Die Funktion ruft Ihr Modell auf, um Vorhersagen für eine neue Datendatei zu treffen, die Sie in Ihr Speicherkonto geladen haben. Speichern Sie die Vorhersagen dann wieder im Speicherkonto. Sie können diese Routine bedarfsgesteuert auf dem Edgeknoten Ihres Clusters oder mithilfe eines geplanten Auftrags ausführen.

### <a name="score-in-azure-machine-learning-aml"></a>Bewertung in Azure Machine Learning (AML)

Verwenden Sie für die Bewertung mit Azure Machine Learning das Open-Source-Azure Machine Learning-R-Paket namens [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/), um Ihr Modell als Azure-Webdienst zu veröffentlichen. Der Einfachheit halber ist dieses Paket auf dem Edgeknoten vorinstalliert. Verwenden Sie als Nächstes die Funktionen in Azure Machine Learning zum Erstellen einer Benutzeroberfläche für den Webdienst, und rufen Sie den Webdienst dann nach Bedarf für die Bewertung auf. Konvertieren Sie anschließend ScaleR-Modellobjekte in gleichwertige Open-Source-Modellobjekte, die für den Webdienst verwendet werden können. Verwenden Sie für diese Umwandlung ScaleR-Koersionsfunktionen wie `as.randomForest()` für ensemblebasierte Modelle.

### <a name="score-on-premises"></a>Lokale Bewertung

Gehen Sie wie folgt vor, um nach der Erstellung Ihres Modells eine lokale Bewertung durchzuführen: Serialisieren Sie das Modell in R, laden Sie es herunter, deserialisieren Sie es, und verwenden Sie es anschließend für die Bewertung neuer Daten. Sie können die Bewertung für neue Daten durchführen, indem Sie den weiter oben unter „Bewertung in HDInsight“ beschriebenen Ansatz verwenden oder [Webdienste](/machine-learning-server/operationalize/concept-what-are-web-services) nutzen.

## <a name="maintain-the-cluster"></a>Verwalten des Clusters

### <a name="install-and-maintain-r-packages"></a>Installieren und Verwalten von R-Paketen

Die meisten R-Pakete, die Sie verwenden, sind auf dem Edgeknoten erforderlich, da ein Großteil der Schritte Ihrer R-Skripts dort ausgeführt wird. Um auf dem Edgeknoten zusätzliche R-Pakete zu installieren, verwenden Sie in R die `install.packages()`-Methode.

Falls Sie nur ScaleR-Bibliotheksroutinen verwenden, benötigen Sie normalerweise keine zusätzlichen R-Pakete. Sie benötigen unter Umständen zusätzliche Pakete für die Ausführung von **rxExec** oder **RxDataStep** auf den Datenknoten.

Die zusätzlichen Pakete können nach dem Erstellen des Clusters mit einer Skriptaktion installiert werden. Weitere Informationen finden Sie unter [Verwalten von ML Services in HDInsight-Clustern](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändern der Apache Hadoop MapReduce-Speichereinstellungen

Der für ML Services verfügbare Arbeitsspeicher kann geändert werden, wenn ein MapReduce-Auftrag ausgeführt wird. Verwenden Sie zum Ändern eines Clusters die Apache Ambari-Benutzeroberfläche für Ihren Cluster. Eine Anleitung zur Ambari-Benutzeroberfläche finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md).

Sie können den verfügbaren Arbeitsspeicher für ML Services ändern, indem Sie im **RxHadoopMR**-Aufruf Hadoop-Switches verwenden:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Skalieren des Clusters

Ein vorhandener ML Services-Cluster in HDInsight kann über das Portal zentral hoch- oder herunterskaliert werden. Durch das Hochskalieren schaffen Sie zusätzliche Kapazität für umfangreichere Verarbeitungsaufgaben. Sie können die Skalierung für einen Cluster rückgängig machen, wenn er nicht benötigt wird. Eine Anleitung zum Skalieren eines Clusters finden Sie unter [Verwalten von HDInsight-Clustern](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Warten des Systems

Die Wartung des Betriebssystems wird auf den zugrunde liegenden Linux-VMs eines HDInsight-Clusters außerhalb der Geschäftszeiten durchgeführt. Normalerweise erfolgt die Wartung immer montags und donnerstags um 3:30 Uhr (Ortszeit der VM). Updates wirken sich jeweils nur auf maximal ein Viertel des Clusters aus.

Während der Wartung kann es für aktive Aufträge zu einer Verlangsamung kommen. Sie sollten aber trotzdem vollständig ausgeführt werden. Jegliche benutzerdefinierte Software oder lokalen Daten, über die Sie ggf. verfügen, werden über diese Wartungsereignisse hinweg beibehalten – sofern kein schwerwiegender Fehler auftritt, der eine Neuerstellung des Clusters erfordert.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-Optionen für ML Services in HDInsight

Der Linux-Edgeknoten auf einem HDInsight-Cluster stellt die Landezone für R-basierte Analysen dar. Neuere Versionen von HDInsight verfügen über eine browserbasierte IDE von RStudio auf dem Edgeknoten. RStudio Server weist in Bezug auf die Entwicklung und Ausführung eine höhere Produktivität als die R-Konsole auf.

Eine Desktop-IDE verfügt per MapReduce- oder Spark-Remote-Computekontext über Zugriff auf den Cluster. Beispiele für Optionen: [R Tools für Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) von Microsoft, RStudio und die auf Eclipse basierende Anwendung StatET von Walware

Greifen Sie auf die R-Konsole auf dem Edgeknoten zu, indem Sie an der Eingabeaufforderung **R** eingeben. Bei Nutzung der Konsolenoberfläche ist es ratsam, R-Skripts in einem Text-Editor zu entwickeln. Sie können die Abschnitte Ihres Skripts dann je nach Bedarf ausschneiden und in die R-Konsole einfügen.

## <a name="pricing"></a>Preise

Die Preise für einen ML Services-HDInsight-Cluster sind ähnlich strukturiert wie für andere HDInsight-Clustertypen. Sie basieren auf der Größe der zugrunde liegenden VMs auf den Namens-, Daten- und Edgeknoten. Hinzu kommt eine stündliche Zusatzgebühr pro Kern. Weitere Informationen finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von ML Services in HDInsight-Clustern finden Sie in den folgenden Artikeln:

* [Ausführen eines R-Skripts in einem ML Services-Cluster in Azure HDInsight mithilfe von RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Compute context options for ML Services on HDInsight](r-server-compute-contexts.md) (Computekontextoptionen für ML Services in HDInsight)
* [Speicheroptionen für ML Services-Cluster in HDInsight](r-server-storage.md)