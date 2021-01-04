---
title: 'Verschieben von Blob Storage-Daten mit SSIS-Connectors: Team Data Science-Prozess'
description: Erfahren Sie, wie Sie Daten mithilfe von SQL Server Integration Services Feature Pack für Azure in oder aus Azure Blob Storage verschieben.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322005"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Verschieben Sie Daten in und aus Azure Blob Storage mithilfe von SSIS-Connectors.
Das [SQL Server Integration Services Feature Pack für Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) bietet Komponenten zum Herstellen einer Verbindung mit Azure, Übertragen von Daten zwischen Azure und lokalen Datenquellen und Verarbeiten von in Azure gespeicherten Daten.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Nachdem Kunden lokale Daten in die Cloud verschoben haben, können sie über sämtliche Azure-Dienste darauf zugreifen, um die ganze Leistungsfähigkeit der Azure-Technologiepalette zu nutzen. Die Daten können anschließend z. B. in Azure Machine Learning oder auf einem HDInsight-Cluster verwendet werden.

Beispiele für die Verwendung dieser Azure-Ressourcen finden sich in den exemplarischen Vorgehensweisen zu [SQL](sql-walkthrough.md) und [HDInsight](hive-walkthrough.md).

Eine Beschreibung gängiger Szenarien, in denen SQL Server Integration Services (SSIS) zum Erfüllen geschäftlicher Anforderungen in hybriden Datenintegrationsszenarien zum Einsatz kommt, finden Sie im Blog [Doing more with SQL Server Integration Services Feature Pack for Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) .

> [!NOTE]
> Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Erste Schritte mit Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-dotnet.md) und [Azure-Blobdienst](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der in diesem Artikel beschriebenen Aufgaben benötigen Sie ein eingerichtetes Azure-Abonnement und Azure Storage-Konto. Um Daten hoch- und herunterladen zu können, benötigen Sie den Namen Ihres Azure Storage-Kontos und den Kontoschlüssel.

* Informationen zum Einrichten eines **Azure-Abonnements** finden Sie unter [Kostenlose 1-Monat-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Anleitungen zum Erstellen eines **Speicherkontos** und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure Storage-Konten](../../storage/common/storage-account-create.md).

Zum Verwenden der **SSIS-Connectors** müssen Sie Folgendes herunterladen:

* **SQL Server 2014 oder 2016 Standard (oder höher)** : Die Installation umfasst SQL Server Integration Services.
* **Microsoft SQL Server 2014 oder 2016 Integration Services Feature Pack für Azure** : Diese Connectors können auf den Seiten [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) bzw. [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) heruntergeladen werden.

> [!NOTE]
> SSIS wird mit SQL Server installiert, ist aber nicht in der Express-Version enthalten. Informationen dazu, welche Anwendungen in verschiedenen Editionen von SQL Server enthalten sind, finden Sie unter [SQL Server-Editionen](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/).
> 
> 

Schulungsmaterialien für SSIS finden Sie unter [Praktische Schulung für SSIS](https://www.microsoft.com/sql-server/training-certification)

Informationen zur Inbetriebnahme von SSIS zum Erstellen von ETL-Paketen (Extrahieren, Transformieren, Laden) finden Sie im [SSIS-Tutorial: Erstellen eines einfachen ETL-Pakets](/sql/integration-services/ssis-how-to-create-an-etl-package).

## <a name="download-nyc-taxi-dataset"></a>Herunterladen des Datasets „NYC Taxi Trips“
Das hier beschriebene Beispiel verwendet ein öffentlich verfügbares Dataset mit dem Namen [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/). Das Dataset besteht aus ca. 173 Millionen Taxifahrten in New York im Jahr 2013. Es gibt zwei Arten von Daten: Daten zu Fahrten und Daten zum Fahrpreis. Für jeden Monat gibt es jeweils eine Datei, sodass wir insgesamt 24 Dateien haben, die unkomprimiert jeweils ungefähr 2 GB groß sind.

## <a name="upload-data-to-azure-blob-storage"></a>Hochladen von Daten in Azure Blob Storage
Um Daten mithilfe des SSIS Feature Packs aus lokalem Speicher in Azure Blob Storage zu verschieben, verwenden wir eine Instanz der nachstehend gezeigten [**Aufgabe zum Hochladen von Azure-Blobs**](/sql/integration-services/control-flow/azure-blob-upload-task):

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Die Parameter dieser Aufgabe werden nachfolgend beschrieben:

| Feld | BESCHREIBUNG |
| --- | --- |
| **AzureStorageConnection** |Gibt einen vorhandenen Azure Storage-Verbindungs-Manager an oder erstellt einen neuen, der auf ein Azure Storage-Konto verweist, das dorthin zeigt, wo die Blobdateien gehostet werden. |
| **BlobContainer** |Gibt den Namen des Blobcontainers an, der die hochgeladenen Dateien als Blobs enthält. |
| **BlobDirectory** |Gibt das Blobverzeichnis an, in dem die hochgeladene Datei als Blockblob gespeichert wird. Das Blobverzeichnis ist eine virtuelle hierarchische Struktur. Wenn das Blob bereits vorhanden ist, wird es ersetzt. |
| **LocalDirectory** |Gibt das lokale Verzeichnis mit den hochzuladenden Dateien an. |
| **FileName** |Gibt einen Namensfilter an, um Dateien mit dem angegebenen Namensmuster auszuwählen. Beispielsweise enthält „MySheet\*.xls\*“ Dateien wie „MySheet001.xls“ und „MySheetABC.xlsx“. |
| **TimeRangeFrom/TimeRangeTo** |Gibt einen Zeitbereichsfilter an. Dateien, die nach *TimeRangeFrom* und vor *TimeRangeTo* geändert wurden, sind eingeschlossen. |

> [!NOTE]
> Die Anmeldeinformationen für **AzureStorageConnection** müssen ordnungsgemäß sein, und der **BlobContainer** muss vorhanden sein, bevor die Übertragung versucht wird.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Herunterladen von Daten aus Azure Blob Storage
Verwenden Sie eine Instanz von [Task zum Herunterladen von Azure-Blobs](/sql/integration-services/control-flow/azure-blob-download-task), um Daten mit SSIS aus Azure Blob Storage in den lokalen Speicher herunterzuladen.

## <a name="more-advanced-ssis-azure-scenarios"></a>Erweiterte Azure/SSIS-Szenarien
Das SSIS Feature Pack ermöglicht komplexere zu verarbeitende Abläufe, indem diese Aufgaben in einem Paket zusammengeführt werden. Beispielsweise können die Blobdaten direkt in einen HDInsight-Cluster übertragen werden, dessen Ausgabe zunächst zurück in ein Blob und anschließend in einen lokalen Speicher heruntergeladen werden kann. SSIS kann mithilfe eines zusätzlichen SSIS-Connectors Hive- und Pig-Aufträge auf einem HDInsight-Cluster anwenden:

* Verwenden Sie zum Anwenden eines Hive-Skripts auf einen Azure HDInsight-Cluster mit SSIS die [Hive-Aufgabe in Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Verwenden Sie zum Anwenden eines Pig-Skripts auf einen Azure HDInsight-Cluster mit SSIS die [Pig-Aufgabe in Azure HDInsight](/sql/integration-services/control-flow/azure-hdinsight-pig-task).