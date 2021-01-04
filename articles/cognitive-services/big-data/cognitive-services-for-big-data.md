---
title: Cognitive Services für Big Data
description: Hier erfahren Sie, wie Sie Azure Cognitive Services für große Datasets mit Python, Java und Scala nutzen. Cognitive Services für Big Data ermöglicht die direkte Einbettung intelligenter, kontinuierlich optimierter Modelle in Apache Spark&trade; und SQL-Berechnungen.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.date: 07/09/2020
ms.author: marhamil
ms.openlocfilehash: aaade03edbbb109656fb7371a063cdc2512c5a20
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461627"
---
# <a name="azure-cognitive-services-for-big-data"></a>Azure Cognitive Services für Big Data

![Azure Cognitive Services für Big Data](media/cognitive-services-big-data-overview.svg)

Azure Cognitive Services für Big Data ermöglicht es Benutzern, mehrere Terabyte an Daten mithilfe von [Apache Spark&trade;](/dotnet/spark/what-is-spark) durch Cognitive Services zu leiten. Mit Cognitive Services für Big Data lassen sich ganz einfach umfangreiche intelligente Anwendungen mit einem beliebigen Datenspeicher erstellen.

Cognitive Services für Big Data ermöglicht die direkte Einbettung intelligenter, kontinuierlich optimierter Modelle in Apache Spark&trade; und SQL-Berechnungen. Dank dieser Tools müssen sich Entwickler nicht mehr um sämtliche Netzwerkdetails kümmern und können sich stattdessen auf die Erstellung intelligenter, verteilter Anwendungen konzentrieren.

## <a name="features-and-benefits"></a>Features und Vorteile

Von Cognitive Services für Big Data können Dienste aus einer beliebigen Region auf der Welt sowie [Cognitive Services in Containern](../cognitive-services-container-support.md) verwendet werden. Container unterstützen Bereitstellungen mit geringer oder ohne Konnektivität sowie mit Antworten mit äußerst geringer Wartezeit. Cognitive Services in Containern können lokal, direkt auf den Workerknoten Ihres Spark-Clusters oder auf einem externen Orchestrator wie Kubernetes ausgeführt werden.

## <a name="supported-services"></a>Unterstützte Dienste

[Cognitive Services](../index.yml) bietet Zugriffsmöglichkeiten über APIs und SDKs und unterstützt Entwickler bei der Entwicklung intelligenter Anwendungen ohne KI- oder Data Science-Kenntnisse. Mit Cognitive Services können Sie Anwendungen entwickeln, die sehen, hören, sprechen, verstehen und vernünftige Entscheidungen treffen können. Zur Verwendung von Cognitive Services muss Ihre Anwendung Daten über das Netzwerk an den Dienst senden. Nach Empfang der Daten gibt der Dienst eine intelligente Antwort zurück. Für Big Data-Workloads stehen folgende Dienste zur Verfügung:

### <a name="vision"></a>Bildanalyse

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Maschinelles Sehen](../computer-vision/index.yml "Maschinelles Sehen")| Über den Dienst für maschinelles Sehen haben Sie Zugriff auf erweiterte Algorithmen für die Bildverarbeitung und die Rückgabe von Informationen. |
|[Gesichtserkennung](../face/index.yml "Gesicht")| Der Gesichtserkennungsdienst ermöglicht den Zugriff auf erweiterte Algorithmen zur Gesichtserkennung, wodurch die Ermittlung von Gesichtsmerkmalen sowie die Gesichtserkennung ermöglicht wird. |

### <a name="speech"></a>Spracheingabe

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Speech-Dienst](../speech-service/index.yml "Speech-Dienst")|Der Speech-Dienst bietet Zugriff auf Features wie Spracherkennung, Sprachsynthese und Sprachübersetzung sowie Sprecherüberprüfung und -identifikation.|

### <a name="decision"></a>Entscheidung

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Anomalieerkennung](../anomaly-detector/index.yml "Anomalieerkennung") | Der Anomalieerkennungsdienst (Vorschauversion) ermöglicht die Überwachung und Erkennung von Anomalien in Ihren Zeitreihendaten.|

### <a name="language"></a>Sprache

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Textanalyse](../text-analytics/index.yml "Textanalyse")| Der Textanalysedienst ermöglicht die Verarbeitung natürlicher Sprache in unformatiertem Text für Standpunktanalyse, Schlüsselbegriffserkennung und Sprachenerkennung.|

### <a name="search"></a>Suchen,

|Service Name|Dienstbeschreibung|
|:-----------|:------------------|
|[Bing-Bildersuche](/azure/cognitive-services/bing-image-search "Bing-Bildersuche")|Der Dienst „Bing-Bildersuche“ gibt eine Liste von Bildern zurück, die für die Abfrage des Benutzers als relevant eingestuft wurden.|

## <a name="supported-programming-languages-for-cognitive-services-for-big-data"></a>Unterstützte Programmiersprachen für Cognitive Services für Big Data

Cognitive Services für Big Data basiert auf Apache Spark. Apache Spark ist eine Bibliothek für die verteilte Verarbeitung, die unter anderem die Sprachen Java, Scala, Python und R unterstützt. Aktuell werden folgende Sprachen unterstützt:

### <a name="python"></a>Python

Im Namespace `mmlspark.cognitive` von [Microsoft ML für Apache Spark](https://aka.ms/spark) wird eine PySpark-API bereitgestellt. Weitere Informationen finden Sie unter der [Python Developer-API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/pyspark/mmlspark.cognitive.html). Verwendungsbeispiele finden Sie in den [Python-Beispielen](samples-python.md).

### <a name="scala-and-java"></a>Scala und Java

Im Namespace `com.microsoft.ml.spark.cognitive` von [Microsoft ML für Apache Spark](https://aka.ms/spark) wird eine Scala- und Java-basierte Spark-API bereitgestellt. Weitere Informationen finden Sie unter der [Scala Developer-API](https://mmlspark.blob.core.windows.net/docs/1.0.0-rc1/scala/index.html#package). Verwendungsbeispiele finden Sie in den [Scala-Beispielen](samples-scala.md).

## <a name="supported-platforms-and-connectors"></a>Unterstützte Plattformen und Connectors

Zur Verwendung von Cognitive Services für Big Data wird Apache Spark benötigt. Cognitive Services für Big Data wird von verschiedenen Apache Spark-Plattformen unterstützt.

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) ist eine Apache Spark-basierte Analyseplattform, die für die Microsoft Azure-Clouddienstplattform optimiert ist. Sie ermöglicht die Einrichtung mit nur einem Klick und bietet optimierte Workflows sowie einen interaktiven Arbeitsbereich für die Zusammenarbeit von Datenspezialisten, Data Engineers und Business Analysts.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

[Azure Synapse Analytics](/azure/databricks/data/data-sources/azure/synapse-analytics) ist ein für Unternehmen konzipiertes Data Warehouse mit MPP (Massive Parallel Processing). Mit Synapse Analytics können Sie schnell komplexe Abfragen für mehrere Petabytes an Daten ausführen. Azure Synapse Analytics bietet verwaltete Spark-Pools zum Ausführen von Spark-Aufträgen mit intuitiver Jupyter Notebook-Schnittstelle.

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

[Azure Kubernetes Service (AKS)](../../aks/index.yml) orchestriert Docker-Container und verteilte Anwendungen in großem Umfang. AKS ist ein verwaltetes Kubernetes-Angebot, das die Verwendung von Kubernetes in Azure vereinfacht. Kubernetes ermöglicht eine differenzierte Steuerung der Skalierung, der Wartezeit und des Netzwerks von Cognitive Services. Sollten Sie allerdings noch nicht mit Apache Spark vertraut sein, empfiehlt sich die Verwendung von Azure Databricks oder Azure Synapse Analytics.

### <a name="data-connectors"></a>Datenconnectors

Wenn Sie über einen Spark-Cluster verfügen, muss als Nächstes eine Verbindung mit Ihren Daten hergestellt werden. Apache Spark bietet ein breites Spektrum an Datenbankconnectors. Diese Connectors ermöglichen Anwendungen die Verwendung umfangreicher Datasets unabhängig von deren Speicherort. Weitere Informationen zu unterstützten Datenbanken und Connectors finden Sie in der [Liste unterstützter Datenquellen für Azure Databricks](/azure/databricks/data/data-sources/).

## <a name="concepts"></a>Konzepte

### <a name="spark"></a>Spark

[Apache Spark&trade;](http://spark.apache.org/) ist eine vereinheitlichte Engine zur Verarbeitung umfangreicher Daten, deren Framework für die Parallelverarbeitung zur Steigerung der Leistung von Big Data- und Analyseanwendungen beiträgt. Spark kann ohne Änderung des Kernanwendungscodes sowohl als Batch- als auch als Datenstrom-Verarbeitungssystem genutzt werden.

Die Grundlage von Spark bildet der Datenrahmen: eine tabellarische Sammlung von Daten, die auf die Apache Spark-Workerknoten verteilt sind. Ein Spark-Datenrahmen ist mit einer Tabelle in einer relationalen Datenbank oder mit einem Datenrahmen in R/Python vergleichbar, lässt sich aber unbegrenzt skalieren. Datenrahmen können auf der Grundlage zahlreicher Quellen erstellt werden. Hierzu zählen unter anderem strukturierte Datendateien, Tabellen in Hive oder externe Datenbanken. Wenn sich Ihre Daten in einem Spark-Datenrahmen befinden, haben Sie folgende Möglichkeiten:

   - Ausführen SQL-ähnlicher Berechnungen wie Verknüpfungs- und Filtervorgänge für Tabellen
   - Anwenden von Funktionen auf umfangreiche Datasets mit MapReduce-ähnlicher Parallelität
   - Anwenden von verteiltem maschinellem Lernen für Apache Spark
   - Anreichern Ihrer Daten mit einsatzbereiten intelligenten Diensten mithilfe von Cognitive Services für Big Data

### <a name="microsoft-machine-learning-for-apache-spark-mmlspark"></a>Microsoft Machine Learning für Apache Spark (MMLSpark)

[Microsoft Machine Learning für Apache Spark](https://mmlspark.blob.core.windows.net/website/index.html#install) (MMLSpark) ist eine auf Apache Spark basierende Open-Source-Bibliothek für verteiltes maschinelles Lernen (ML). Cognitive Services für Big Data ist in diesem Paket enthalten. Darüber hinaus enthält MMLSpark auch noch einige weitere ML-Tools für Apache Spark wie etwa LightGBM, Vowpal Wabbit, OpenCV und LIME. MMLSpark ermöglicht die Erstellung leistungsstarker Vorhersage- und Analysemodelle auf der Grundlage einer beliebigen Spark-Datenquelle.

### <a name="http-on-spark"></a>HTTP in Spark

Cognitive Services für Big Data ist ein Beispiel für die Integration intelligenter Webdienste mit Big Data. Webdienste werden von zahlreichen Anwendungen auf der ganzen Welt genutzt, und die meisten Dienste kommunizieren über das Hypertext Transfer-Protokoll (HTTP). HTTP steht in Spark zur Verfügung, um im großen Umfang mit *beliebigen* Webdiensten arbeiten zu können. Mit HTTP in Spark können Daten im Terabytebereich über einen beliebigen Webdienst übergeben werden. Diese Technologie wird im Hintergrund für den Betrieb von Cognitive Services für Big Data genutzt.

## <a name="developer-samples"></a>Entwicklerbeispiele

- [Anleitung: Predictive Maintenance mit Cognitive Services für Big Data](recipes/anomaly-detection.md)
- [Anleitung: Intelligente Erkundung von Kunstwerken mit Cognitive Services für Big Data](recipes/art-explorer.md)

## <a name="blog-posts"></a>Blogbeiträge

- [Weitere Informationen zur Funktionsweise von Cognitive Services in Apache Spark&trade;](https://azure.microsoft.com/blog/dear-spark-developers-welcome-to-azure-cognitive-services/)
- [Schützen von Schneeleoparden mit Deep Learning und maschinellem Sehen in Spark](http://www.datawizard.io/2017/06/27/saving-snow-leopards-with-deep-learning-and-computer-vision-on-spark/)
- [Microsoft Research-Podcast: MMLSpark, Grundlage für AI for Good mit Mark Hamilton](https://blubrry.com/microsoftresearch/49485070/092-mmlspark-empowering-ai-for-good-with-mark-hamilton/)
- [Akademisches Whitepaper: Intelligente Microservices im großen Stil](https://arxiv.org/abs/2009.08044)

## <a name="webinars-and-videos"></a>Webinare und Videos

- [Azure Cognitive Services in Spark: Cluster mit eingebetteten intelligenten Diensten](https://databricks.com/session/the-azure-cognitive-services-on-spark-clusters-with-embedded-intelligent-services)
- [Spark Summit-Keynote: Skalierbare AI for Good-Lösung](https://databricks.com/session_eu19/scalable-ai-for-good)
- [Cognitive Services für Big Data in Cosmos DB](https://medius.studios.ms/Embed/Video-nc/B19-BRK3004?latestplayer=true&l=2571.208093)
- [Blitzgespräch zu „Intelligente Microservices im großen Stil“](https://www.youtube.com/watch?v=BtuhmdIy9Fk&t=6s)

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Cognitive Services für Big Data](getting-started.md)
- [Einfache Python-Beispiele](samples-python.md)
- [Einfache Scala-Beispiele](samples-scala.md)