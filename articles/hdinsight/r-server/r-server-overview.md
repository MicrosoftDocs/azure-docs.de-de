---
title: Einführung in ML Services in Azure HDInsight
description: Erfahren Sie, wie Sie ML Services in HDInsight zum Erstellen von Anwendungen für Big Data-Analysen verwenden.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 6fecd2278ddad4de3a2cff9035d60083101e873e
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393835"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Worum handelt es sich bei ML Services in Azure HDInsight?

Microsoft Machine Learning Server ist als Bereitstellungsoption bei der Erstellung von HDInsight-Clustern in Azure verfügbar. Der Clustertyp, der diese Option bietet, heißt **ML Services**. Diese Funktion ermöglicht Datenanalysten, Statistikern und R-Programmierern bei Bedarf den Zugriff auf skalierbare, verteilte Analysemethoden in HDInsight.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

ML Services in HDInsight bietet die neuesten Funktionen für die R-basierte Analyse praktisch beliebig großer Datasets, die in Azure Blob Storage oder Data Lake Store geladen wurden. Da ML Services-Cluster auf Open-Source-R basieren, stehen Ihnen bei der Erstellung R-basierter Anwendungen alle über 8.000 Open-Source-R-Pakete zur Verfügung. Die Routinen in ScaleR, dem Big Data-Analysepaket von Microsoft, stehen ebenfalls zur Verfügung.

Der Edgeknoten eines Clusters ist ein praktischer Ort für die Verbindungsherstellung mit dem Cluster und die Ausführung Ihrer R-Skripts. Mit einem Edgeknoten haben Sie die Möglichkeit, die parallelisierten verteilten Funktionen von ScaleR in allen Kernen der Edgeknotenserver auszuführen. Außerdem können Sie sie in allen Knoten des Clusters ausführen, indem Sie Hadoop MapReduce von ScaleR oder Apache Spark-Computekontexte verwenden.

Die Modelle oder Vorhersagen, die sich aus der Analyse ergeben, können für die lokale Verwendung heruntergeladen werden. Sie können auch an anderer Stelle in Azure operationalisiert werden, insbesondere über den [Azure Machine Learning Studio](https://studio.azureml.net)-[Webdienst](../../machine-learning/studio/publish-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Erste Schritte mit ML Services in HDInsight

Um in Azure HDInsight einen ML Services-Cluster zu erstellen, wählen Sie beim Erstellen eines HDInsight-Clusters im Azure-Portal den Clustertyp **ML Services** aus. Der Typ des ML Services-Clusters bezieht ML Server auf den Datenknoten des Clusters und auf einem Edgeknoten ein, der als Landezone für ML Services-basierte Analysen dient. Eine Anleitung zum Erstellen eines Clusters finden Sie unter [Erste Schritte mit ML Services in HDInsight](r-server-get-started.md).

## <a name="why-choose-ml-services-in-hdinsight"></a>Gründe für die Wahl von ML Services in HDInsight

ML Services in HDInsight bietet folgende Vorteile:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>KI-Innovation von Microsoft und Open-Source

  ML Services beinhaltet hochgradig skalierbare, verteilte Algorithmen, etwa [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) und [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package), die auf Datenvolumen oberhalb der Größe des Arbeitsspeichers angewandt werden können und sich in verteilter Form auf einer Vielzahl von Plattformen ausführen lassen. Erfahren Sie mehr über die Sammlung der angepassten [R-Pakete](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) und [Python-Pakete](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) von Microsoft, die im Lieferumfang des Produkts enthalten sind.
  
  ML Services verbindet diese Microsoft-Innovationen mit Beiträgen aus der Open-Source-Community (R-, Python- und KI-Toolkits) auf einer einzelnen Plattform auf Unternehmensniveau. Jedes Open-Source-Paket für maschinelles Lernen in R oder Python funktioniert parallel mit jeder proprietären Innovation von Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Einfache, sichere und hochgradig skalierbare Operationalisierung und Verwaltung

  Unternehmen, die auf traditionelle Paradigmen und Umgebungen bauen, investieren viel Zeit und Mühe in die Operationalisierung. Dies führt zu überhöhten Kosten und Verzögerungen wie der Übersetzungszeit für Modelle, Iterationen, um sie gültig und aktuell zu halten, behördlichen Genehmigungen und der Verwaltung von Berechtigungen durch Operationalisierung.

  ML Services bietet [Operationalisierung](https://docs.microsoft.com/machine-learning-server/what-is-operationalization) auf Unternehmensniveau: Nach der Fertigstellung eines Modells für maschinelles Lernen können Webdienst-APIs mit wenigen Klicks erstellt werden. Diese [Webdienste](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) werden auf einem Serverraster in der Cloud gehostet und können mit Branchenanwendungen integriert werden. Durch die Möglichkeit der Bereitstellung in einem elastischen Raster können Sie nahtlos mit den Anforderungen Ihres Unternehmens skalieren, sowohl für Batch- als auch für Echtzeitbewertung. Anweisungen finden Sie unter [Operationalisieren von ML Services in HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Zentrale Features von ML Services in HDInsight

Die folgenden Features sind in ML Services in HDInsight enthalten.

| Featurekategorie | BESCHREIBUNG |
|------------------|-------------|
| R-fähig | [R-Pakete](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) für in R erstellte Lösungen mit einer Open-Source-Distribution von R und einer Laufzeitinfrastruktur für die Skriptausführung. |
| Python-fähig | [Python-Module](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) für in Python erstellte Lösungen mit einer Open-Source-Distribution von Python und einer Laufzeitinfrastruktur für die Skriptausführung.
| [Vortrainierte Modelle](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Für visuelle Analyse und Textempfindungsanalyse, bereit zur Bewertung der Daten, die Sie zur Verfügung stellen. |
| [Bereitstellen und Nutzen](r-server-operationalize.md) | Operationalisieren Sie Ihren Server, und stellen Sie Lösungen als Webdienst bereit. |
| [Remoteausführung](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Starten Sie Remotesitzungen auf ML Services-Clustern in Ihrem Netzwerk von Ihrer Clientarbeitsstation aus. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Datenspeicherungsoptionen für ML Services in HDInsight

Standardspeicher für das HDFS-Dateisystem von HDInsight-Clustern können entweder einem Azure Storage-Konto oder Azure Data Lake Storage zugeordnet werden. Durch diese Zuordnung wird sichergestellt, dass alle Daten, die während einer Analyse in den Clusterspeicher hochgeladen werden, persistent gemacht werden und selbst nach der Löschung des Clusters noch verfügbar sind. Es gibt verschiedene Tools für die Datenübertragung in die von Ihnen ausgewählte Speicheroption, einschließlich der portalbasierten Hochladefunktion für das Speicherkonto und des [AzCopy](../../storage/common/storage-use-azcopy.md)-Hilfsprogramms.

Unabhängig von der verwendeten primären Speicheroption haben Sie die Möglichkeit, während des Clusterbereitstellungsvorgangs den Zugriff auf zusätzliche Blobspeicher und Data Lake-Speicher zu ermöglichen. Informationen zum Hinzufügen von Zugriff auf zusätzliche Konten finden Sie unter [Erste Schritte mit ML Services in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started). Weitere Informationen zur Verwendung von mehreren Speicherkonten finden Sie unter [Azure Storage-Optionen für ML Services in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage).

Außerdem können Sie [Azure Files](../../storage/files/storage-how-to-use-files-linux.md) als Speicheroption für den Edgeknoten wählen. Mit Azure Files können Sie eine Dateifreigabe, die in Azure Storage erstellt wurde, für das Linux-Dateisystem bereitstellen. Weitere Informationen zu diesen Datenspeicheroptionen für ML Services in HDInsight-Clustern finden Sie unter [Azure Storage-Lösungen für ML Services in HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Zugreigen auf ML Services-Edgeknoten

Sie können über einen Browser eine Verbindung mit ML Server auf dem Edgeknoten herstellen. R Server wird standardmäßig bei der Clustererstellung installiert. Weitere Informationen finden Sie unter [Erste Schritte mit ML Services in HDInsight](r-server-get-started.md). Sie können auch über die Befehlszeile eine Verbindung mit Clusteredgeknoten herstellen, indem Sie mithilfe von SSH/PuTTY auf die R-Konsole zugreifen.

## <a name="develop-and-run-r-scripts"></a>Entwickeln und Ausführen von R-Skripts

Für von Ihnen geschriebene und ausgeführte R-Skripts können zusätzlich zu den parallelisierten und verteilten Routinen in der ScaleR-Bibliothek mehr als 8000 Open-Source-R-Pakete verwendet werden. Im Allgemeinen wird ein Skript, das mit ML Services auf dem Edgeknoten ausgeführt wird, im R-Interpreter auf diesem Knoten ausgeführt. Ausgenommen hiervon sind die Schritte, die eine ScaleR-Funktion mit einem Rechenkontext aufrufen müssen, der auf Hadoop MapReduce (RxHadoopMR) oder Spark (RxSpark) festgelegt ist. In diesem Fall wird die Funktion über diejenigen Daten(aufgabe)knoten des Clusters verteilt ausgeführt, die den Daten zugeordnet sind, auf die verwiesen wird. Weitere Informationen zu den verschiedenen Optionen für Berechnungskontexte finden Sie unter [Computekontextoptionen für ML Services in HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operationalisieren eines Modells

Nach Abschluss der Datenmodellierung können Sie das Modell operationalisieren, um in Azure oder lokal Vorhersagen über neue Daten treffen zu können. Dieser Prozess wird als Bewertung bezeichnet. Eine Bewertung kann in HDInsight, Azure Machine Learning oder lokal ausgeführt werden.

### <a name="score-in-hdinsight"></a>Bewertung in HDInsight

Schreiben Sie für die Bewertung in HDInsight eine R-Funktion, mit der Ihr Modell aufgerufen wird, um Vorhersagen für eine neue Datendatei zu treffen, die Sie in Ihr Speicherkonto geladen haben. Speichern Sie die Vorhersagen dann wieder im Speicherkonto. Sie können diese Routine bedarfsgesteuert auf dem Edgeknoten Ihres Clusters oder mithilfe eines geplanten Auftrags ausführen.

### <a name="score-in-azure-machine-learning-aml"></a>Bewertung in Azure Machine Learning (AML)

Verwenden Sie für die Bewertung mit Azure Machine Learning das Open-Source-Azure Machine Learning-R-Paket namens [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html), um Ihr Modell als Azure-Webdienst zu veröffentlichen. Der Einfachheit halber ist dieses Paket auf dem Edgeknoten vorinstalliert. Verwenden Sie als Nächstes die Funktionen in Azure Machine Learning zum Erstellen einer Benutzeroberfläche für den Webdienst, und rufen Sie den Webdienst dann nach Bedarf für die Bewertung auf.

Wenn Sie diese Option auswählen, müssen Sie zur Verwendung mit dem Webdienst alle ScaleR-Modellobjekte in entsprechende Open-Source-Modellobjekte umwandeln. Verwenden Sie für diese Umwandlung ScaleR-Koersionsfunktionen wie `as.randomForest()` für ensemblebasierte Modelle.

### <a name="score-on-premises"></a>Lokale Bewertung

Um nach der Erstellung Ihres Modells eine lokale Bewertung durchzuführen, können Sie das Modell in R serialisieren, herunterladen, deserialisieren und anschließend für die Bewertung neuer Daten verwenden. Sie können die Bewertung für neue Daten durchführen, indem Sie den weiter oben unter [Bewertung in HDInsight](#score-in-hdinsight) beschriebenen Ansatz verwenden oder [Webdienste](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) nutzen.

## <a name="maintain-the-cluster"></a>Verwalten des Clusters

### <a name="install-and-maintain-r-packages"></a>Installieren und Verwalten von R-Paketen

Die meisten R-Pakete, die Sie verwenden, sind auf dem Edgeknoten erforderlich, da ein Großteil der Schritte Ihrer R-Skripts dort ausgeführt wird. Um auf dem Edgeknoten zusätzliche R-Pakete zu installieren, verwenden Sie in R die `install.packages()`-Methode.

Wenn Sie nur Routinen aus der ScaleR-Bibliothek auf den Cluster anwenden, müssen Sie in der Regel keine zusätzlichen R-Pakete auf den Datenknoten installieren. Sie benötigen aber unter Umständen zusätzliche Pakete, um die Verwendung der **rxExec**- oder **RxDataStep**-Ausführung auf den Datenknoten zu unterstützen.

In solchen Fällen können die zusätzlichen Pakete nach dem Erstellen des Clusters mithilfe einer Skriptaktion installiert werden. Weitere Informationen finden Sie unter [Verwalten von ML Services in HDInsight-Clustern](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Ändern der Apache Hadoop MapReduce-Speichereinstellungen

Ein Cluster kann dahin gehend geändert werden, dass er die Speichermenge, die ML Services bei der Ausführung eines MapReduce-Auftrags zur Verfügung steht, ändern kann. Verwenden Sie zum Ändern eines Clusters die Apache Ambari-Benutzeroberfläche, die über das Blatt des Clusters im Azure-Portal verfügbar ist. Informationen zum Zugriff auf die Ambari-Benutzeroberfläche für Ihren Cluster finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md).

Sie können den für ML Services zur Verfügung stehenden Speicher ändern, indem Sie Hadoop-Switches beim Aufruf von **RxHadoopMR** wie folgt verwenden:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Skalieren des Clusters

Ein vorhandener ML Services-Cluster in HDInsight kann über das Portal zentral hoch- oder herunterskaliert werden. Durch das vertikale Skalieren erzielen Sie die zusätzliche Kapazität, die Sie für umfangreichere Verarbeitungsaufgaben benötigen, oder Sie können einen im Leerlauf befindlichen Cluster wieder herunterskalieren. Eine Anleitung zum Skalieren eines Clusters finden Sie unter [Verwalten von HDInsight-Clustern](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Warten des Systems

Die Wartung zur Anwendung von Betriebssystempatches und anderer Updates wird auf den zugrundeliegenden Linux-VMs in einem HDInsight-Cluster außerhalb der Arbeitszeiten durchgeführt. Normalerweise wird die Wartung jeden Montag und Donnerstag um 3:30 Uhr (Ortszeit der VM) durchgeführt. Die Updates werden so geplant, dass jeweils maximal ein Viertel des Clusters betroffen ist.

Da die Hauptknoten redundant sind und nicht alle Datenknoten betroffen sind, werden alle Aufträge, die während dieses Zeitraums ausgeführt werden, unter Umständen verlangsamt. Sie sollten aber trotzdem vollständig ausgeführt werden. Jegliche benutzerdefinierte Software oder lokalen Daten, über die Sie ggf. verfügen, werden über diese Wartungsereignisse hinweg erhalten – sofern kein schwerwiegender Fehler auftritt, der eine Neuerstellung des Clusters erfordert.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-Optionen für ML Services in HDInsight

Der Linux-Edgeknoten auf einem HDInsight-Cluster stellt die Landezone für R-basierte Analysen dar. Neuere Versionen von HDInsight bieten eine Standardinstallation von RStudio Server als browserbasierte IDE auf dem Edgeknoten. Die Verwendung von RStudio Server als IDE für die Entwicklung und Ausführung von R-Skripts kann erheblich produktiver sein als die ausschließliche Verwendung der R-Konsole.

Darüber hinaus können Sie eine Desktop-IDE installieren und sie verwenden, um mithilfe eines Remote-MapReduce- oder Spark-Rechenkontexts auf den Cluster zuzugreifen. Zu den Optionen gehören [R Tools für Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS) von Microsoft, RStudio und das auf Eclipse basierende [StatET](http://www.walware.de/goto/statet) von Walware.

Außerdem können Sie nach dem Herstellen einer Verbindung über SSH oder PuTTY durch Eingabe von **R** an der Linux-Eingabeaufforderung auf die R-Konsole auf dem Edgeknoten zugreifen. Wenn Sie die Konsolenschnittstelle verwenden, ist es sinnvoll, einen Texteditor für die R-Skriptentwicklung in einem anderen Fenster auszuführen und Abschnitte Ihres Skripts nach Bedarf auszuschneiden und in die R-Konsole einzufügen.

## <a name="pricing"></a>Preise

Die Preise für einen ML Services-HDInsight-Cluster sind ähnlich strukturiert wie die Preise für andere HDInsight-Clustertypen. Sie basieren auf der Größe der zugrundeliegenden VMs des Clusters sowie der Daten und Edgeknoten. Hinzu kommt eine stündliche Zusatzgebühr pro Kern. Weitere Informationen finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von ML Services in HDInsight-Clustern finden Sie unter den folgenden Themen:

* [Erste Schritte mit ML Services-Clustern in HDInsight](r-server-get-started.md)
* [Compute context options for ML Services on HDInsight](r-server-compute-contexts.md) (Computekontextoptionen für ML Services in HDInsight)
* [Speicheroptionen für ML Services-Cluster in HDInsight](r-server-storage.md)
