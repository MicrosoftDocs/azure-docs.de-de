---
title: Kopieren von Daten aus einer lokalen SQL Server-Datenbank nach Azure mithilfe von PowerShell
description: Dieses PowerShell-Skript kopiert Daten aus einer SQL Server-Datenbank in eine Azure Blob Storage-Instanz.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: bc10017da8561cda09658ec9380cc82095f3ef94
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632231"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-sql-server-to-azure"></a>Verwenden von PowerShell zum Erstellen einer Data Factory-Pipeline zum Kopieren von SQL Server-Daten in Azure

Dieses PowerShell-Beispielskript erstellt eine Pipeline in Azure Data Factory, die Daten aus einer SQL Server-Datenbank in eine Azure Blob Storage-Instanz kopiert.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- **SQL Server** . In diesem Beispiel verwenden Sie eine SQL Server-Datenbank als **Quelldatenspeicher** .
- **Azure Storage-Konto** . In diesem Beispiel verwenden Sie Azure Blob Storage als **Ziel-/Senkendatenspeicher** . Wenn Sie kein Azure Storage-Konto haben, finden Sie im Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) Schritte zum Erstellen eines Azure Storage-Kontos.
- **Selbstgehostete Integration Runtime** . Laden Sie die MSI-Datei aus dem [Download Center](https://www.microsoft.com/download/details.aspx?id=39717) herunter, und führen Sie sie aus, um eine selbstgehostete Integration Runtime auf Ihrem Computer zu installieren.  

### <a name="create-sample-database-in-sql-server"></a>Erstellen einer Beispieldatenbank in SQL Server
1. Erstellen Sie in der SQL Server-Datenbank eine Tabelle namens **emp** . Verwenden Sie dazu das folgende SQL-Skript:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Fügen Sie einige Beispieldaten in die Tabelle ein:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Beispielskript

> [!IMPORTANT]
> Dieses Skript erstellt JSON-Dateien, die Data Factory-Entitäten (verknüpften Dienst, Dataset und Pipeline) auf der Festplatte im Ordner „c:\“ definieren.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können Sie den folgenden Befehl ausführen, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Führen Sie den folgenden Befehl aus, um die Data Factory aus der Ressourcengruppe zu entfernen:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Erstellen einer Data Factory. |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Verschlüsselt Anmeldeinformationen in einem verknüpften Dienst und generiert eine neue Definition für einen verknüpften Dienst mit den verschlüsselten Anmeldeinformationen
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Erstellt einen verknüpften Dienst in der Data Factory. Ein verknüpfter Dienst verbindet einen Datenspeicher oder ein Compute mit einer Data Factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Erstellt ein Dataset in der Data Factory. Ein Dataset stellt die Eingabe/Ausgabe für eine Aktivität in einer Pipeline dar. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Erstellt eine Pipeline in der Data Factory. Eine Pipeline enthält eine oder mehrere Aktivitäten zur Ausführung eines bestimmten Vorgangs. In dieser Pipeline kopiert eine Kopieraktivität in einem Azure Blob Storage Daten von einem Speicherort an einen anderen. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Erstellt eine Ausführung für die Pipeline. Soll heißen, führt die Pipeline aus. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ruft Details zur Ausführung der Aktivität (Aktivitätsausführung) in der Pipeline ab.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).

Zusätzliche PowerShell-Skriptbeispiele für Azure Data Factory finden Sie unter [Azure PowerShell-Beispiele für Azure Data Factory](../samples-powershell.md).