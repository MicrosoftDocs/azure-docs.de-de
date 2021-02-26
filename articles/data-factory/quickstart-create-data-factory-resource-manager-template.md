---
title: Erstellen einer Azure Data Factory-Instanz mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage)
description: Erstellen Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure Data Factory-Instanz.
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: f3e76bf16b702adb26183209d36189a53b695c40
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373843"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Schnellstart: Erstellen einer Azure Data Factory-Instanz mithilfe einer ARM-Vorlage

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuelle Version](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure Data Factory erstellen. Die in dieser Data Factory erstellte Pipeline **kopiert** Daten aus einem Ordner in einen anderen Ordner in Azure Blob Storage. Ein Tutorial zum **Transformieren** von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Daten mit Spark transformieren](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Dieser Artikel enthält keine ausführliche Einführung in den Data Factory-Dienst. Eine Einführung in den Azure Data Factory-Dienst finden Sie unter [Einführung in Azure Data Factory](introduction.md).

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-subscription"></a>Azure-Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

### <a name="create-a-file"></a>Erstellen von Dateien

Öffnen Sie einen Text-Editor (etwa **Editor**), und erstellen Sie eine Datei mit dem Namen **emp.txt** und folgendem Inhalt:

```emp.txt
John, Doe
Jane, Doe
```

Speichern Sie die Datei im Ordner **C:\ADFv2QuickStartPSH**. (Erstellen Sie den Ordner, falls er noch nicht vorhanden ist.)

## <a name="review-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

In der Vorlage werden Azure-Ressourcen definiert:

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): Definieren eines Speicherkontos
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories): Erstellen einer Azure Data Factory-Instanz
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Erstellen eines verknüpften Azure Data Factory-Diensts
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets): Erstellen eines Azure Data Factory-Datasets
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): Erstellen einer Azure Data Factory-Pipeline

Weitere Azure Data Factory-Vorlagenbeispiele finden Sie im [Schnellstartvorlagenkatalog](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt ein Azure Data Factory-Konto, ein Speicherkonto und einen Blobcontainer.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Bereitstellen einer ADF-ARM-Vorlage":::

    Sofern nicht anders angegeben, verwenden Sie die Standardwerte, um die Azure Data Factory-Ressourcen zu erstellen:

    - **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    - **Ressourcengruppe**: Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und wählen Sie dann **OK** aus.
    - **Region**: Wählen Sie einen Standort aus.  Beispiel: *USA, Osten*
    - **Data Factory-Name**: Verwenden Sie den Standardwert.
    - **Standort**: Verwenden Sie den Standardwert.
    - **Speicherkontoname:** Verwenden Sie den Standardwert.
    - **Blobcontainer**: Verwenden Sie den Standardwert.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Wählen Sie **Zu Ressourcengruppe wechseln** aus.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Ressourcengruppe":::

2.  Vergewissern Sie sich, dass Ihre Azure Data Factory-Instanz erstellt wurde.
    1. Der Azure Data Factory-Name hat das Format „datafactory\<uniqueid\>“.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Data Factory-Beispiel":::

2. Vergewissern Sie sich, dass Ihr Speicherkonto erstellt wurde.
    1. Der Speicherkontoname hat das Format „storage\<uniqueid\>“.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Speicherkonto":::

3. Wählen Sie den Namen des erstellten Speicherkontos und anschließend die Option **Container** aus.
    1. Wählen Sie auf der Seite **Container** den erstellten Blobcontainer aus.
        1. Der Blobcontainername hat das Format „blob\<uniqueid\>“.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Blobcontainer":::

### <a name="upload-a-file"></a>Hochladen einer Datei

1. Wählen Sie auf der Seite **Container** die Option **Hochladen** aus.

2. Wählen Sie im rechten Bereich das Feld **Dateien** aus, navigieren Sie zu der zuvor erstellten Datei **emp.txt**, und wählen Sie sie aus.

3. Erweitern Sie die Überschrift **Erweitert**.

4. Geben Sie im Feld *In Ordner hochladen* den Namen **input** ein.

5. Wählen Sie die Schaltfläche **Hochladen**. Daraufhin sollten in der Liste die Datei **emp.txt** und der Status des Uploads angezeigt werden.

6. Wählen Sie das Symbol **Schließen** (das **X**) aus, um die Seite **Blob hochladen** zu schließen.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Hochladen der Datei in den Eingabeordner":::

Lassen Sie die Seite „Container“ geöffnet, da Sie dort am Ende dieses Schnellstarts die Ausgabe überprüfen können.

### <a name="start-trigger"></a>Starten des Triggers

1. Navigieren Sie zur Seite **Data Factorys**, und wählen Sie die von Ihnen erstellte Data Factory aus. 

2. Wählen Sie die Kachel **Erstellen und überwachen** aus. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Erstellen und überwachen":::

2. Wählen Sie die Registerkarte **Ersteller** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: aus.

3. Wählen Sie die erstellte Pipeline „ArmtemplateSampleCopyPipeline“ aus.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM-Vorlagenpipeline":::

4. Wählen Sie **Trigger hinzufügen** > **Trigger Now** (Jetzt auslösen) aus.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Trigger":::

5. Wählen Sie im rechten Bereich unter **Pipelineausführung** die Option **OK** aus.

### <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

1. Wählen Sie die Registerkarte **Überwachen** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: aus.

2. Sie sehen die Aktivitätsausführungen, die der Pipelineausführung zugeordnet sind. In dieser Schnellstartanleitung verfügt die Pipeline nur über eine Aktivität vom Typ „Kopieren“. Aus diesem Grund wird eine Ausführung für diese Aktivität angezeigt.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Erfolgreiche Ausführung":::

### <a name="verify-the-output-file"></a>Überprüfen der Ausgabedatei

Die Pipeline erstellt automatisch einen Ausgabeordner im Blobcontainer Anschließend wird die Datei „emp.txt“ aus dem Eingabe- in den Ausgabeordner kopiert. 

1. Wählen Sie im Azure-Portal auf der Seite **Container** die Option **Aktualisieren** aus, um den Ausgabeordner anzuzeigen. 

2. Wählen Sie in der Ordnerliste **output** aus.

3. Überprüfen Sie, ob die Datei **emp.txt** in den Ausgabeordner kopiert wurde. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Ausgabe":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die im Rahmen dieser Schnellstartanleitung erstellten Ressourcen können auf zwei Arten bereinigt werden. Sie können die [Azure-Ressourcengruppe](../azure-resource-manager/management/delete-resource-group.md) einschließlich aller darin enthaltenen Ressourcen löschen. Falls die anderen Ressourcen erhalten bleiben sollen, löschen Sie nur die Data Factory, die Sie in diesem Tutorial erstellt haben.

Wenn Sie eine Ressourcengruppe löschen, werden alle Ressourcen einschließlich enthaltener Data Factorys gelöscht. Führen Sie den folgenden Befehl aus, um die gesamte Ressourcengruppe zu löschen: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Wenn Sie nur die Data Factory und nicht die gesamte Ressourcengruppe löschen möchten, führen Sie den folgenden Befehl aus: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe einer ARM-Vorlage eine Azure Data Factory-Instanz erstellt und die Bereitstellung überprüft. Weitere Informationen zu Azure Data Factory und Azure Resource Manager finden Sie in den folgenden Artikeln:

- [Azure Data Factory-Dokumentation](index.yml)
- Lesen Sie weitere Informationen zu [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Weitere [Azure Data Factory-ARM-Vorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)