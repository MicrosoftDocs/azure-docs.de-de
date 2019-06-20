---
title: Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Verwenden des Tools AdlCopy zum Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be66fd51b37c0e62b2b757a88ee1db9319b2093a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878833"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Verwenden von DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Verwenden von AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 bietet das Befehlszeilentool [AdlCopy](https://aka.ms/downloadadlcopy), mit dem Sie Daten aus den folgenden Quellen kopieren können:

* Aus Azure Storage-Blobs in Data Lake Storage Gen1. Sie können AdlCopy nicht verwenden, um Daten aus Data Lake Storage Gen1 in Azure Storage-Blobs zu kopieren.
* Zwischen zwei Azure Data Lake Storage Gen1-Konten.

Sie können das AdlCopy-Tool in zwei verschiedenen Modi:

* **Eigenständig**, wobei das Tool Ressourcen von Data Lake Storage Gen1 verwendet, um die Aufgabe auszuführen.
* **Mit einem Data Lake Analytics-Konto**, wobei die Einheiten, die Ihrem Data Lake Analytics-Konto zugewiesen sind, zum Ausführen des Kopiervorgangs verwendet werden. Möglicherweise möchten diese Option verwenden, wenn Sie die Kopie in einer vorhersagbaren Weise ausführen möchten.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Azure Storage-Blobscontainer** mit einigen Daten.
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Lake Analytics-Konto (optional)** – Anweisungen zum Erstellen eines Data Lake Analytics-Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
* **AdlCopy-Tool**. Installieren Sie das AdlCopy-Tool über [https://aka.ms/downloadadlcopy](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Syntax des AdlCopy-Tools
Verwenden Sie die folgende Syntax, um mit dem AdlCopy-Tool zu arbeiten.

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

Die Parameter in der Syntax werden nachfolgend beschrieben:

| Option | BESCHREIBUNG |
| --- | --- |
| `Source` |Gibt den Speicherort der Quelldaten im Azure-Speicherblob an. Bei der Quelle kann es sich um einen Blobcontainer, ein Blob oder ein anderes Data Lake Storage Gen1-Konto handeln. |
| Dest |Gibt das Data Lake Storage Gen1-Ziel für das Kopieren an. |
| SourceKey |Gibt den Speicherzugriffsschlüssel für die Azure-Speicherblobquelle an. Dies ist nur erforderlich, wenn die Quelle ein Blobcontainer oder ein Blob ist. |
| Konto |**Optional**. Verwenden Sie diese Option, wenn Sie das Azure Data Lake Analytics-Konto verwenden möchten, um den Kopierauftrag auszuführen. Wenn Sie die Option „/Account“ in der Syntax verwenden, aber kein Data Lake Analytics-Konto angeben, verwendet „AdlCopy“ ein Standardkonto zum Ausführen des Auftrags. Wenn Sie diese Option verwenden, müssen Sie außerdem die Quelle (Azure Storage Blob) und das Ziel (Azure Data Lake Storage Gen1) als Datenquellen für das Data Lake Analytics-Konto hinzufügen. |
| Units |Gibt die Anzahl der Data Lake Analytics-Einheiten an, die für den Kopierauftrag verwendet werden. Diese Option ist erforderlich, wenn Sie mithilfe der Option **/Account** das Data Lake Analytics-Konto angeben. |
| Muster |Gibt ein RegEx-Muster an, das festlegt, welche Blobs oder Dateien kopiert werden sollen. Beim Abgleich für AdlCopy muss die Groß-/Kleinschreibung übereinstimmen. Dies wird als Standardmuster verwendet, wenn zum Kopieren aller Elemente kein Muster angegeben wurde. Das Angeben mehrerer Dateimuster wird nicht unterstützt. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Verwenden von AdlCopy (als eigenständiges Tool) zum Kopieren von Daten aus einem Azure Storage-Blob
1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in dem „AdlCopy“ installiert ist, in der Regel `%HOMEPATH%\Documents\adlcopy`.
2. Führen Sie den folgenden Befehl aus, um ein bestimmtes Blob aus dem Quellcontainer in einen Data Lake Storage Gen1-Ordner zu kopieren:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Beispiel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >Die oben angegebene Syntax gibt die Datei an, die in einen Ordner im Data Lake Storage Gen1-Konto kopiert werden soll. Das AdlCopy-Tool erstellt einen Ordner, falls der angegebene Ordnername nicht vorhanden ist.

    Sie werden aufgefordert, die Anmeldeinformationen für das Azure-Abonnement einzugeben, dem Ihr Data Lake Storage Gen1-Konto zugeordnet ist. Eine Ausgabe ähnlich der folgenden wird angezeigt.

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. Sie können auch alle Blobs aus einem Container mit folgendem Befehl in das Data Lake Storage Gen1-Konto kopieren:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Beispiel:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Beim Kopieren aus einem Azure Blob Storage-Konto wird der Kopierauftrag unter Umständen auf der Blob Storage-Seite gedrosselt. Dies beeinträchtigt die Leistung Ihres Kopierauftrags. Weitere Informationen zu den Einschränkungen von Azure Blob Storage finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md) unter „Speichergrenzwerte“.

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Verwenden von AdlCopy (als eigenständiges Tool) zum Kopieren von Daten aus einem anderen Data Lake Storage Gen1-Konto
Sie können AdlCopy auch verwenden, um Daten von einem Data Lake Storage Gen1-Konto in ein anderes zu kopieren.

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in dem „AdlCopy“ installiert ist, in der Regel `%HOMEPATH%\Documents\adlcopy`.
2. Führen Sie den folgenden Befehl aus, um eine bestimmte Datei aus einem Data Lake Storage Gen1-Konto in ein anderes zu kopieren.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Beispiel:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > Die oben stehende Syntax gibt die Datei an, die in einen Ordner im Data Lake Storage Gen1-Zielkonto kopiert werden soll. Das AdlCopy-Tool erstellt einen Ordner, falls der angegebene Ordnername nicht vorhanden ist.
   >
   >

    Sie werden aufgefordert, die Anmeldeinformationen für das Azure-Abonnement einzugeben, dem Ihr Data Lake Storage Gen1-Konto zugeordnet ist. Eine Ausgabe ähnlich der folgenden wird angezeigt.

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. Der folgende Befehl kopiert alle Dateien aus einem bestimmten Ordner im Data Lake Storage Gen1-Quellkonto in einen Ordner im Data Lake Storage Gen1-Zielkonto.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Wenn Sie AdlCopy als eigenständiges Tool verwenden, wird die Kopie auf gemeinsam genutzten, von Azure verwalteten Ressourcen ausgeführt. Die Leistung in dieser Umgebung ist abhängig von der Systemlast und den verfügbaren Ressourcen. Dieser Modus eignet sich am besten für kleine Ad-hoc-Übertragungen. Wenn Sie AdlCopy als eigenständiges Tool verwenden, müssen keine Parameter optimiert werden.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Verwenden von AdlCopy (mit einem Data Lake Analytics-Konto) zum Kopieren von Daten
Sie können mit Ihrem Data Lake Analytics-Konto auch den AdlCopy-Auftrag zum Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1 ausführen. In der Regel verwenden Sie diese Option, wenn die zu verschiebenden Daten im GB- und TB-Bereich liegen, und Sie einen besseren und vorhersagbareren Leistungsdurchsatz wünschen.

Um Ihr Data Lake Analytics-Konto mit AdlCopy zum Kopieren von Daten aus einem Azure Storage-Blob zu verwenden, müssen Sie die Quelle (Azure Storage-Blob) als Datenquelle für Ihr Data Lake Analytics-Konto hinzufügen. Informationen zum Hinzufügen von zusätzlichen Datenquellen zu Ihrem Data Lake Analytics-Konto finden Sie unter [Verwalten der Datenquellen des Data Lake Analytics-Kontos](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Wenn Sie Daten unter Verwendung eines Data Lake Analytics-Kontos aus einem Azure Data Lake Storage Gen1-Quellkonto kopieren, müssen Sie das Data Lake Storage Gen1-Konto nicht dem Data Lake Analytics-Konto zuordnen. Die Anforderung, den Quellspeicher dem Data Lake Analytics-Konto zuzuordnen, gilt nur, wenn es sich bei der Quelle um ein Azure Storage-Konto handelt.
>
>

Führen Sie den folgenden Befehl aus, um mithilfe eines Data Lake Analytics-Kontos aus einem Azure Storage-Blob in ein Data Lake Storage Gen1-Konto zu kopieren:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Beispiel:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

Führen Sie den folgenden Befehl aus, um mithilfe eines Data Lake Analytics-Kontos alle Dateien aus einem bestimmten Ordner im Data Lake Storage Gen1-Quellkonto in einen Ordner im Data Lake Storage Gen1-Zielkonto zu kopieren:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Wenn Sie Datenmengen im Terabytebereich kopieren und AdlCopy mit Ihrem eigenen Azure Data Lake Analytics-Konto verwenden, erhalten Sie eine höhere und besser vorhersagbare Leistung. Bei den Parametern empfiehlt sich die Optimierung der Anzahl zu verwendender Azure Data Lake Analytics-Einheiten für den Kopierauftrag. Wenn Sie die Einheitenanzahl erhöhen, erhöht sich auch die Leistung Ihres Kopierauftrags. Jede zu kopierende Datei kann maximal eine Einheit verwenden. Wenn die Anzahl von Einheiten die Anzahl zu kopierender Dateien übersteigt, erhöht sich die Leistung nicht weiter.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Verwenden von AdlCopy zum Kopieren von Daten mithilfe des Musterabgleichs
In diesem Abschnitt erfahren Sie, wie Sie AdlCopy verwenden, um Daten mithilfe eines Musterabgleichs aus einem Data Lake Storage Gen1-Quellkonto (im Beispiel unten wird Azure Storage Blob verwendet) in ein Data Lake Store-Zielkonto zu kopieren. Sie können diese Schritte z.B. verwenden, um alle Dateien mit der Erweiterung „CSV“ aus dem Quellblob in das Ziel zu kopieren.

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in dem „AdlCopy“ installiert ist, in der Regel `%HOMEPATH%\Documents\adlcopy`.
2. Führen Sie den folgenden Befehl aus, um alle Dateien mit der Erweiterung „CSV“ aus einem bestimmten Blob des Quellcontainers in einen Data Lake Storage Gen1-Ordner zu kopieren:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Beispiel:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Abrechnung
* Bei Verwendung des Tools AdlCopy als eigenständiges Programm werden Ihnen Ausgangskosten für das Verschieben von Daten berechnet, wenn sich das Azure Storage-Quellkonto nicht in derselben Region wie das Data Lake Storage Gen1-Konto befindet.
* Bei Verwendung des AdlCopy-Tools mit Ihrem Data Lake Analytics-Konto gelten standardmäßige [Data Lake Analytics-Abrechnungsraten](https://azure.microsoft.com/pricing/details/data-lake-analytics/) .

## <a name="considerations-for-using-adlcopy"></a>Überlegungen zur Verwendung von AdlCopy
* AdlCopy (für Version 1.0.5) unterstützt das Kopieren von Daten aus Quellen, die zusammen mehrere Tausend Dateien und Ordner aufweisen. Wenn beim Kopieren eines umfangreichen Datasets jedoch Probleme auftreten, können Sie die Dateien und Ordner in verschiedene Unterordner verteilen und den Pfad zu diesen Unterordnern als Quelle angeben.

## <a name="performance-considerations-for-using-adlcopy"></a>Überlegungen zur Leistung bei Verwendung von AdlCopy

AdlCopy unterstützt das Kopieren von Daten mit Tausenden von Dateien und Ordnern. Sollten beim Kopieren eines großen Datasets jedoch Probleme auftreten, können Sie die Dateien/Ordner auf kleinere Unterordner verteilen. AdlCopy wurde für Ad-hoc-Kopien konzipiert. Wenn Sie wiederholt Daten kopieren möchten, sollten Sie die Verwendung von [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in Betracht ziehen, da diese Lösung eine umfassende Verwaltung rund um Kopiervorgänge bietet.

## <a name="release-notes"></a>Versionshinweise
* 1.0.13: Wenn Sie Daten über mehrere AdlCopy-Befehle in dasselbe Azure Data Lake Storage Gen1-Konto kopieren möchten, müssen Sie Ihre Anmeldeinformationen nicht mehr für jede Ausführung neu eingeben. Diese Informationen werden nun von Adlcopy über mehrere Ausführungen zwischengespeichert.

## <a name="next-steps"></a>Nächste Schritte
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
