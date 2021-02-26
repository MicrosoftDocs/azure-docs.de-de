---
title: 'Data Factory-Tutorial: Erste Datenpipeline '
description: In diesem Azure Data Factory-Tutorial erfahren Sie, wie Sie eine Data Factory erstellen und planen, die Daten unter Verwendung eines Hive-Skripts in einem Hadoop-Cluster verarbeitet.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 7f1de53e20614ca66c91735ce462da5a194d1836
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377226"
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Erstellen Ihrer ersten Pipeline zum Transformieren von Daten mithilfe eines Hadoop-Clusters
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager: Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, helfen Ihnen die Informationen unter [Schnellstart: Erstellen einer Data Factory und Pipeline mit dem .NET SDK](../quickstart-create-data-factory-dot-net.md) weiter.

In diesem Tutorial erstellen Sie Ihre erste Azure Data Factory mit einer Datenpipeline. Die Pipeline transformiert Eingabedaten durch Ausführen des Hive-Skripts in einem Azure HDInsight (Hadoop)-Cluster in Ausgabedaten.  

Dieser Artikel enthält eine Übersicht und Voraussetzungen für das Tutorial. Wählen Sie nach dem Erfüllen der Voraussetzungen eines der folgenden Tools/SDKs aus, um das Tutorial durchzuarbeiten: Visual Studio, PowerShell, Resource Manager-Vorlage, REST-API. Wählen Sie eine der Optionen in der Dropdownliste am Anfang oder die Links am Ende dieses Artikels, um das Tutorial mithilfe einer der folgenden Optionen auszuführen.    

## <a name="tutorial-overview"></a>Übersicht über das Tutorial
In diesem Tutorial führen Sie die folgenden Schritte aus:

1. Eine **Data Factory**. Eine Data Factory kann mindestens eine Datenpipeline enthalten, die Daten verschiebt und verarbeitet.

    In diesem Tutorial erstellen Sie eine Pipeline in der Data Factory.
2. Erstellen einer **Pipeline** Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen (z.B. Kopieraktivität, HDInsight Hive-Aktivität). Dieses Beispiel verwendet die HDInsight-Hive-Aktivität, die ein Hive-Skript für einen HDInsight-Hadoop-Cluster ausführt. Das Skript erstellt zunächst eine Tabelle, die auf die Webprotokoll-Rohdaten in Azure Blob Storage verweist, und partitioniert die Rohdaten dann nach Jahr und Monat.

    In diesem Tutorial verwendet die Pipeline die Hive-Aktivität zum Transformieren von Daten durch Ausführen einer Hive-Abfrage in einem Azure HDInsight Hadoop-Cluster.
3. Erstellen von **verknüpften Diensten**. Sie erstellen einen verknüpften Dienst, um einen Datenspeicher oder einen Computedienst mit der Data Factory zu verknüpfen. Ein Datenspeicher wie Azure Storage hält Ein-/Ausgabedaten von Aktivitäten in der Pipeline. Ein Computedienst (etwa ein HDInsight-Hadoop-Cluster) verarbeitet/transformiert Daten.

    In diesem Tutorial erstellen Sie zwei verknüpfte Dienste: **Azure Storage** und **Azure HDInsight**. Der mit Azure Storage verknüpfte Dienst verknüpft ein Azure Storage-Konto, das die Eingabe-/Ausgabedaten enthält, mit der Data Factory. Der mit Azure HDInsight verknüpfte Dienst verknüpft einen Azure HDInsight-Cluster, der zum Transformieren von Daten verwendet wird, mit der Data Factory.
3. Erstellen von **Datasets** für Eingabe und Ausgabe Ein Eingabedataset stellt die Eingabe für eine Aktivität in der Pipeline dar, und ein Ausgabedataset stellt die Ausgabe für die Aktivität dar.

    In diesem Tutorial legen die Eingabe- und Ausgabedatasets Speicherorte der Eingabe- und Ausgabedaten in Azure Blob Storage fest. Der mit Azure Storage verknüpfte Dienst gibt an, welches Azure Storage-Konto verwendet werden soll. Ein Eingabedataset gibt an, wo sich die Eingabedateien befinden, ein Ausgabedataset gibt an, wo die Ausgabedateien platziert werden.


Eine ausführliche Übersicht über Azure Data Factory finden Sie im Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).

Die Diagrammansicht der Beispiel-Data Factory, die Sie in diesem Tutorial erstellen, finden Sie **hier** . **MyFirstPipeline** weist eine Aktivität vom Typ „Hive“ auf, die das **AzureBlobInput**-Dataset als Eingabe verbraucht und das **AzureBlobOutput**-Dataset als Ausgabe erstellt.

![Diagrammansicht im Data Factory-Tutorial](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


In diesem Tutorial enthält der Ordner **inputdata** des Azure-Blobcontainers **adfgetstarted** eine Datei namens „input.log“. Diese Protokolldatei enthält Einträge für drei Monate: Januar, Februar und März 2016. Hier sind die Beispielzeilen für jeden Monat in der Eingabedatei.

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Wenn die Datei von der Pipeline mit HDInsight-Hive-Aktivität verarbeitet wird, führt die Aktivität ein Hive-Skript auf dem HDInsight-Cluster aus, das Eingabedaten nach Jahr und Monat partitioniert. Das Skript erstellt drei Ausgabeordner, die eine Datei mit Einträgen aus jedem Monat enthalten.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

Von den obigen Beispielzeilen wird die erste (mit „2016-01-01“) in die Datei „000000_0“ im Ordner „month=1“ geschrieben. Auf ähnliche Weise wird die zweite Zeile in die Datei im Ordner „month=2“ geschrieben und die dritte Zeile in die Datei im Ordner „month=3“.  

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Tutorial beginnen, müssen folgende Voraussetzungen erfüllt sein:

1. **Azure-Abonnement** – Wenn Sie über kein Azure-Abonnement verfügen, können Sie in wenigen Minuten ein kostenloses Testversionskonto einrichten. Im Artikel [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) erfahren Sie, wie Sie ein kostenloses Testkonto erhalten.
2. **Azure Storage** – Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) . Notieren Sie sich nach Erstellung des Speicherkontos den **Kontonamen** und den **Zugriffsschlüssel**. Informationen zum Abrufen von Speicherkonto-Zugriffsschlüsseln finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).
3. Laden Sie die Hive-Abfragedatei (**HQL**) unter [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql) herunter, und überprüfen Sie sie. Diese Abfrage transformiert die Eingabedaten in Ausgabedaten.
4. Laden Sie die Beispieleingabedatei (**input.log**) unter [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log) herunter, und überprüfen Sie sie.
5. Erstellen Sie einen Blobcontainer namens **adfgetstarted** in Azure Blob Storage.
6. Laden Sie im Container **adfgetstarted** die Datei **partitionweblogs.hql** in den Ordner **script** hoch. Verwenden Sie Tools wie etwa den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/).
7. Laden Sie im Container **adfgetstarted** die Datei **input.log** in den Ordner **inputdata** hoch.

Nach dem Erfüllen der Voraussetzungen wählen Sie eines der folgenden Tools/SDKs aus, um das Tutorial auszuführen:

- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Resource Manager: Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
- [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)

Visual Studio bietet eine grafische Benutzeroberfläche zum Erstellen von Data Factorys. Die Optionen PowerShell, Resource Manager-Vorlage und REST-API bieten jedoch Skripting-/Programmiermethoden zum Erstellen von Data Factorys.

> [!NOTE]
> Die Datenpipeline in diesem Tutorial transformiert Eingabedaten in Ausgabedaten. Sie kopiert keine Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Kopieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Ausführliche Informationen finden Sie unter [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md).
