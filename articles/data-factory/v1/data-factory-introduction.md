---
title: Einführung in Data Factory, einen Datenintegrationsdienst
description: 'Hier erfahren Sie, worum es sich bei Azure Data Factory handelt: Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten orchestriert und automatisiert.'
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 101de32aea47bb3dd3c8a04434993c8614939c37
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495564"
---
# <a name="introduction-to-azure-data-factory"></a>Einführung in Azure Data Factory 
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-introduction.md)
> * [Version 2 (aktuelle Version)](../introduction.md)

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [Einführung in Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Was ist Azure Data Factory?
Wie werden vorhandene Daten in Zeiten von Big Data in Unternehmen genutzt? Ist es möglich, in der Cloud generierte Daten zu erweitern, indem Referenzdaten von lokalen Datenquellen oder anderen unterschiedlichen Datenquellen verwendet werden? 

Ein Spiele-Unternehmen erfasst beispielsweise Protokolle, die von Spielen in der Cloud erstellt werden. Das Unternehmen möchte diese Protokolle analysieren, um Erkenntnisse zu Vorlieben von Kunden, zu demografischen Daten, zum Nutzungsverhalten usw. zu erhalten. Außerdem möchte es Upselling- und Cross-Selling-Möglichkeiten ermitteln, mitreißende neue Features entwickeln, um das Unternehmenswachstum voranzutreiben, und die Benutzerfreundlichkeit für Kunden erhöhen. 

Zum Analysieren dieser Protokolle benötigt das Unternehmen die Referenzdaten (beispielsweise Informationen zu den Kunden, zum Spiel und zur Marketingkampagne), die in einem lokalen Datenspeicher gespeichert sind. Daher möchte das Unternehmen Protokolldaten aus dem Clouddatenspeicher und Referenzdaten aus dem lokalen Datenspeicher erfassen. 

Als Nächstes sollen die Daten mit Hadoop in der Cloud (Azure HDInsight) verarbeitet werden. Das Unternehmen möchte die Ergebnisdaten in einem Data Warehouse in der Cloud (beispielsweise Azure Synapse Analytics) oder in einem lokalen Datenspeicher wie SQL Server veröffentlichen. Dieser Workflow soll einmal pro Woche ausgeführt werden. 

Das Unternehmen benötigt eine Plattform, auf der ein Workflow erstellt werden kann, mit dem das Erfassen von Daten aus Datenspeichern lokal oder in der Cloud möglich ist. Außerdem soll es möglich sein, Daten mit vorhandenen Compute Services wie Hadoop zu transformieren und zu verarbeiten und die Ergebnisse in einem Datenspeicher lokal oder in der Cloud zu veröffentlichen, damit sie von BI-Anwendungen genutzt werden können. 

![Übersicht über Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory ist die optimale Plattform für diese Szenarien. Es ist ein *cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und -transformationen zu orchestrieren und zu automatisieren*. Mit Azure Data Factory können Sie die folgenden Aufgaben durchführen: 

- Erstellen und Planen von datengesteuerten Workflows (so genannte Pipelines), die Daten aus unterschiedlichen Datenspeichern erfassen können

- Verarbeiten oder Transformieren der Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning

-  Veröffentlichen von Ausgabedaten für Datenspeicher wie Azure Synapse Analytics für die Nutzung durch BI-Anwendungen (Business Intelligence)  

Dies ist eher eine EL- (Extract-and-Load) und TL-Plattform (Transform-and-Load) als eine traditionelle ETL-Plattform (Extract-Transform-and-Load). Die Transformationen verarbeiten Daten mit Compute Services, anstatt abgeleitete Spalten hinzuzufügen, die Anzahl von Zeilen zu zählen, Daten zu sortieren usw. 

Die Daten, die derzeit in Azure Data Factory von Workflows genutzt und erstellt werden, sind *Zeitsegmentdaten* (stündlich, täglich, wöchentlich usw.). Eine Pipeline kann beispielsweise unter Umständen einmal täglich Eingabedaten lesen, Daten verarbeiten und Ausgabedaten erstellen. Sie können einen Workflow auch nur einmal ausführen.  
  

## <a name="how-does-it-work"></a>Wie funktioniert dies? 
Die Pipelines (datengesteuerten Workflows) in Azure Data Factory führen in der Regel die folgenden drei Schritte aus:

![Drei Phasen von Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Herstellen einer Verbindung und Sammeln von Daten
In Unternehmen sind verschiedene Typen von Daten in unterschiedlichen Quellen gespeichert. Der erste Schritt bei der Erstellung eines Informationssystems für die Produktion umfasst das Herstellen der Verbindung mit allen erforderlichen Daten- und Verarbeitungsquellen. Dies sind beispielsweise die Quellen SaaS-Dienste (Software-as-a-Service), Dateifreigaben, FTP und Webdienste. Verschieben Sie die Daten anschließend zur weiteren Verarbeitung an einen zentralen Ort.

Ohne Data Factory müssen Unternehmen benutzerdefinierte Komponenten für die Datenverschiebung erstellen oder benutzerdefinierte Dienste schreiben, um diese Datenquellen und die Verarbeitung zu integrieren. Das Integrieren und Verwalten dieser Systeme ist teuer und aufwändig. Außerdem fehlen hierfür häufig für Unternehmen geeignete Überwachungs- und Warnfunktionen sowie die Steuerungsmöglichkeiten eines vollständig verwalteten Diensts.

Mit Data Factory können Sie die Kopieraktivität in einer Datenpipeline nutzen, um Daten sowohl aus lokalen als auch aus Cloud-Quelldatenspeichern zur weiteren Analyse in einen zentralen Datenspeicher in der Cloud zu verschieben. 

Beispielsweise können Sie Daten in Azure Data Lake Store sammeln und später transformieren, indem Sie einen Azure Data Lake Analytics-Computedienst verwenden. Oder sammeln Sie Daten in Azure Blob Storage, und transformieren Sie sie später mit einem Azure HDInsight Hadoop-Cluster.

### <a name="transform-and-enrich"></a>Transformieren und Erweitern
Wenn Daten in einem zentralen Datenspeicher in der Cloud gespeichert sind, können Sie sie mit Compute Services wie HDInsight Hadoop, Spark, Data Lake Analytics oder Machine Learning verarbeiten oder übertragen. Darauf folgt das Erstellen transformierter Daten nach einem verwaltbaren und gesteuerten Zeitplan, um Produktionsumgebungen mit vertrauenswürdigen Daten zu versorgen. 

### <a name="publish"></a>Veröffentlichen 
Stellen Sie transformierte Daten aus der Cloud auf lokalen Quellen bereit, z.B. SQL Server. Alternativ dazu können Sie sie auch auf Ihren Cloudspeicherquellen belassen, damit sie mit BI-Anwendungen und Analysetools sowie anderen Anwendungen genutzt werden können.

## <a name="key-components"></a>Wichtige Komponenten
Ein Azure-Abonnement kann über mindestens eine Azure Data Factory-Instanz (oder Data Factory) verfügen. Azure Data Factory besteht aus vier Hauptkomponenten. Zusammen stellen sie die Plattform dar, auf der Sie datengesteuerte Workflows mit Schritten zum Verschieben und Transformieren von Daten zusammenstellen können. 

### <a name="pipeline"></a>Pipeline
Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline ist eine Gruppe mit Aktivitäten. Gemeinsam führen die Aktivitäten einer Pipeline eine Aufgabe durch. 

Eine Pipeline kann beispielsweise eine Gruppe mit Aktivitäten enthalten, die Daten aus einem Azure-Blob erfasst, und anschließend eine Hive-Abfrage in einem HDInsight-Cluster ausführen, um die Daten zu partitionieren. Der Vorteil hierbei ist, dass die Pipeline Ihnen das Verwalten der Aktivitäten als Gruppe ermöglicht – anstatt für jede Aktivität einzeln. Beispielsweise können Sie die Pipeline bereitstellen und planen, anstatt voneinander unabhängige Aktivitäten zu planen. 

### <a name="activity"></a>Aktivität
Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Aktivitäten definieren die Aktionen, die Sie auf Ihre Daten anwenden. Beispielsweise können Sie eine Kopieraktivität verwenden, um Daten zwischen zwei Datenspeichern zu kopieren. Auf ähnliche Weise können Sie eine Hive-Aktivität verwenden. Eine Hive-Aktivität führt eine Hive-Abfrage in einem Azure HDInsight-Cluster aus, um Ihre Daten zu transformieren oder zu analysieren. Data Factory unterstützt zwei Arten von Aktivitäten: Datenverschiebungen und Datentransformationen.

### <a name="data-movement-activities"></a>Datenverschiebungsaktivitäten
Die Kopieraktivität in Data Factory kopiert die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher. Daten aus beliebigen Quellen können in beliebige Senken geschrieben werden. Wählen Sie einen Datenspeicher aus, um zu erfahren, wie Daten in diesen bzw. aus diesem Speicher kopiert werden. Data Factory unterstützt die folgenden Datenspeicher:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Weitere Informationen finden Sie unter [Transformieren von Daten in Azure Data Factory](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Datentransformationsaktivitäten
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Weitere Informationen finden Sie unter [Transformieren von Daten in Azure Data Factory](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Benutzerdefinierte .NET-Aktivitäten
Erstellen Sie eine benutzerdefinierte .NET-Aktivität, falls Sie Daten in einen bzw. aus einem Datenspeicher verschieben müssen, der von der Kopieraktivität nicht unterstützt wird, oder Daten mit Ihrer eigenen Logik transformieren möchten. Informationen zum Erstellen und Verwenden einer benutzerdefinierten Aktivität finden Sie unter [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Datasets
Eine Aktivität verwendet keine oder mehrere Datasets als Eingaben und erzeugt mindestens ein Dataset als Ausgabe. Bei Datasets handelt es sich um Datenstrukturen in den Datenspeichern. Diese Strukturen zeigen bzw. verweisen auf die Daten, die Sie in Ihren Aktivitäten verwenden möchten (z.B. Ein- oder Ausgaben). 

Ein Azure-Blob-Dataset kann beispielsweise den Blobcontainer und -ordner in Azure Blob Storage angeben, aus dem die Pipeline die Daten lesen soll. Oder ein Azure SQL-Tabellendataset gibt die Tabelle an, in die die Aktivität die Ausgabedaten schreibt. 

### <a name="linked-services"></a>Verknüpfte Dienste
Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Sie können sich dies wie folgt vorstellen: Ein verknüpfter Dienst definiert die Verbindung mit der Datenquelle, und ein Dataset stellt die Struktur der Daten dar. 

So gibt etwa ein verknüpfter Azure Storage-Dienst die Verbindungszeichenfolge an, mit der eine Verbindung mit einem Azure Storage-Konto hergestellt wird. Ein Azure-Blobdataset gibt den Blobcontainer und den Ordner an, der die Daten enthält.   

Verknüpfte Dienste werden in Data Factory aus zwei Gründen verwendet:

* Sie stellen einen *Datenspeicher* dar, der beispielsweise eine SQL Server-Datenbank, eine Oracle-Datenbank, eine Dateifreigabe oder ein Azure Blob Storage-Konto enthalten kann (aber nicht darauf beschränkt ist). Eine Liste mit unterstützten Datenspeichern finden Sie im Abschnitt [Datenverschiebungsaktivitäten](#data-movement-activities) .

* Sie stellen eine *Computeressource* dar, die die Ausführung einer Aktivität hosten kann. So wird beispielsweise die HDInsightHive-Aktivität in einem HDInsight-Hadoop-Cluster ausgeführt. Eine Liste mit unterstützten Compute-Umgebungen finden Sie unter [Datentransformationsaktivitäten](#data-transformation-activities).

### <a name="relationship-between-data-factory-entities"></a>Beziehung zwischen Data Factory-Entitäten

![Diagramm: Data Factory, ein Clouddaten-Integrationsdienst – Grundlegende Konzepte](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Unterstützte Regionen
Derzeit können Data Factorys in den Regionen „USA, Westen“, „USA, Osten“ und „Europa, Norden“ erstellt werden. Eine Data Factory kann aber auf Datenspeicher und Compute Services in anderen Azure-Regionen zugreifen, um Daten zwischen Datenspeichern zu verschieben oder Daten mithilfe von Computediensten zu verarbeiten.

In Azure Data Factory selbst werden keine Daten gespeichert. Sie können datengesteuerte Workflows erstellen, um die Verschiebung der Daten zwischen [unterstützten Datenspeichern](#data-movement-activities) zu orchestrieren. Außerdem können Sie Daten verarbeiten, indem Sie [Compute Services](#data-transformation-activities) in anderen Regionen oder in einer lokalen Umgebung verwenden. Außerdem können Sie mit programmgesteuerten und UI-basierten Mechanismen [Workflows überwachen und verwalten](data-factory-monitor-manage-pipelines.md).

Data Factory ist nur in den Regionen „USA, Westen“, „USA, Osten“ und „Europa, Norden“ verfügbar. Der Dienst für die Datenverschiebung in Data Factory ist aber [global](data-factory-data-movement-activities.md#global) in mehreren Regionen verfügbar. Wenn sich ein Datenspeicher hinter einer Firewall befindet, werden die Daten stattdessen von einem in der lokalen Umgebung installierten [Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md) verschoben.

Nehmen wir beispielsweise an, dass Ihre Compute-Umgebungen wie Azure HDInsight-Cluster und Azure Machine Learning in der Region „Europa, Westen“ angeordnet sind. Sie können eine Azure Data Factory-Instanz in „Europa, Norden“ erstellen und verwenden. Anschließend können Sie sie nutzen, um Aufträge in Ihren Compute-Umgebungen in „Europa, Westen“ zu planen. Es dauert ein paar Millisekunden, bis Data Factory den Auftrag in Ihrer Compute-Umgebung ausgelöst hat. Die Dauer für die Ausführung des Auftrags in Ihrer Compute-Umgebung ändert sich aber nicht.

## <a name="get-started-with-creating-a-pipeline"></a>Erste Schritte beim Erstellen einer Pipeline
Verwenden Sie diese Tools oder APIs, um Datenpipelines in Azure Data Factory zu erstellen: 

- Visual Studio
- PowerShell
- .NET API
- REST-API
- Azure Resource Manager-Vorlage

Informationen zum Erstellen von Data Factorys mit Datenpipelines erhalten Sie anhand von Schritt-für-Schritt-Anleitungen in den folgenden Tutorials:

| Lernprogramm | BESCHREIBUNG |
| --- | --- |
| [Kopieren von Daten aus Blob Storage in SQL-Datenbank mithilfe von Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Erstellen Sie eine Data Factory mit einer Pipeline, mit der Daten aus Blob Storage in SQL-Datenbank verschoben werden. |
| [Tutorial: Build your first pipeline to transform data using Hadoop cluster](data-factory-build-your-first-pipeline.md) (Tutorial: Erstellen der ersten Pipeline zum Verarbeiten von Daten mithilfe eines Hadoop-Clusters) |Erstellen Sie Ihre erste Azure Data Factory mit einer Datenpipeline für die Datenverarbeitung, indem ein Hive-Skript in einem Azure HDInsight-Cluster (Hadoop) ausgeführt wird. |
| [Move data between an on-premises data store and a cloud data store by using Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) (Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway) |Erstellen Sie eine Data Factory mit einer Pipeline, die Daten aus einer SQL Server-Datenbank in ein Azure-Blob verschiebt. Im Rahmen der exemplarischen Vorgehensweise installieren und konfigurieren Sie das Datenverwaltungsgateway auf Ihrem Computer. |
