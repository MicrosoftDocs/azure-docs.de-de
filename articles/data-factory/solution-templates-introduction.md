---
title: Übersicht über Vorlagen für Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe einer vordefinierten Vorlage schnell den Einstieg in Azure Data Factory finden.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60786931"
---
# <a name="templates"></a>Vorlagen

Vorlagen sind vordefinierte Azure Data Factory-Pipelines, die Ihnen den schnellen Einstieg in Azure Data Factory ermöglichen. Vorlagen sind nützlich, wenn Sie noch nicht mit Data Factory vertraut sind und schnell die ersten Schritte ausführen möchten. Diese Vorlagen verringern den Entwicklungsaufwand für das Erstellen von Datenintegrationsprojekten und verbessern dadurch die Produktivität der Entwickler.

## <a name="create-data-factory-pipelines-from-templates"></a>Erstellen von Data Factory-Pipelines anhand von Vorlagen

Sie können mit dem Erstellen einer Data Factory-Pipeline aus einer Vorlage beginnen. Dabei haben Sie die folgenden zwei Möglichkeiten:

1.  Wählen Sie auf der Seite „Übersicht“ die Option **Pipeline aus Vorlage erstellen** aus, um den Vorlagenkatalog zu öffnen.

    ![Vorlagenkatalog über die Seite „Übersicht“ öffnen](media/solution-templates-introduction/templates-intro-image1.png)

1.  Wählen Sie im Ressourcen-Explorer auf der Registerkarte „Autor“ das Pluszeichen ( **+** ) aus, und wählen Sie dann **Pipeline aus Vorlage** aus, um den Vorlagenkatalog zu öffnen.

    ![Vorlagenkatalog über die Registerkarte „Autor“ öffnen](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Vorlagenkatalog

![Der Vorlagenkatalog](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Integrierte Data Factory-Vorlagen

Data Factory verwendet Azure Resource Manager-Vorlagen für das Speichern von Data Factory-Pipelinevorlagen. Im [offiziellen GitHub-Repository für Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/templates) werden alle Resource Manager-Vorlagen zusammen mit der für die integrierten Data Factory-Vorlagen verwendeten Manifestdatei angezeigt. Die von Microsoft bereitgestellten vordefinierten Vorlagen umfassen unter anderem die folgenden Elemente:

-   Kopiervorlagen:

    -   [Massenkopieren aus der Datenbank](solution-template-bulk-copy-with-control-table.md)
    
    -   [Kopieren neuer Dateien basierend auf LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Kopieren von Dateien aus mehreren Containern zwischen dateibasierten Speichern](solution-template-copy-files-multiple-containers.md)

    -   [Deltakopie aus Datenbank](solution-template-delta-copy-with-control-table.md)

    -   Kopieren aus \<Quelle\> in \<Ziel\>

        -   Aus Amazon S3 in Azure Data Lake Store Gen 2

        -   Aus Google Big Query in Azure Data Lake Store Gen 2

        -   Aus HDF in Azure Data Lake Store Gen 2

        -   Aus Netezza in Azure Data Lake Store Gen 1

        -   Aus einer lokalen SQL Server-Instanz in Azure SQL-Datenbank

        -   Aus einer lokalen SQL Server-Instanz in Azure SQL Data Warehouse

        -   Aus einer lokalen Oracle-Instanz in Azure SQL Data Warehouse

-   SSIS-Vorlagen

    -   Planen der Azure-SSIS Integration Runtime für die Ausführung von SSIS-Paketen

-   Transformationsvorlagen

    -   [ETL mit Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Meine Vorlagen

Sie können auch eine Pipeline als Vorlage speichern, indem Sie auf der Registerkarte „Pipeline“ die Option **Als Vorlage speichern** auswählen.

![Eine Pipeline als Vorlage speichern](media/solution-templates-introduction/templates-intro-image4.png)

Sie können die als Vorlagen gespeicherten Pipelines im Vorlagenkatalog im Abschnitt **Meine Vorlagen** anzeigen. Sie können sie auch im Ressourcen-Explorer im Abschnitt **Vorlagen** anzeigen.

![Meine Vorlagen](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Um das Feature „Meine Vorlagen“ verwenden zu können, müssen Sie die Git-Integration aktivieren. Azure DevOps-Git- und GitHub werden unterstützt.
