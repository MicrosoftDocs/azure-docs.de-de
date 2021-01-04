---
title: Bereitstellen von Vorlagen mit Cloud Shell
description: In diesem Artikel verwenden Sie den Azure Resource Manager und Azure PowerShell, um Ressourcen in Azure bereitzustellen. Die Ressourcen sind in einer Azure Resource Manager-Vorlage definiert.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d0b519955cb877f5fb9640cc0cf95a898e1743ef
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681291"
---
# <a name="deploy-arm-templates-from-cloud-shell"></a>Bereitstellen von ARM-Vorlagen über Cloud Shell

Mit [Cloud Shell](../../cloud-shell/overview.md) können Sie Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereitstellen. Sie können entweder eine remote gespeicherte oder eine im lokalen Speicherkonto von Cloud Shell gespeicherte ARM-Vorlage bereitstellen.

Den Umfang der Bereitstellung legen Sie selbst fest. In diesem Artikel wird gezeigt, wie Sie eine Bereitstellung in einer Ressourcengruppe durchführen.

## <a name="deploy-remote-template"></a>Bereitstellen einer Remotevorlage

Um eine externe Vorlage bereitzustellen, geben Sie den URI der Vorlage genau wie bei jeder anderen externen Bereitstellung an. Die externe Vorlage kann sich in einem GitHub-Repository oder einem externen Speicherkonto befinden.

1. Öffnen Sie die Cloud Shell-Eingabeaufforderung.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell öffnen":::

1. Führen Sie die folgenden Befehle aus, um die Vorlage bereitzustellen:

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Bereitstellen einer lokalen Vorlage

Zum Bereitstellen einer lokalen Vorlage müssen Sie zunächst die Vorlage in das Speicherkonto hochladen, das mit Ihrer Cloud Shell-Sitzung verbunden ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie Ihre Cloud Shell-Ressourcengruppe aus. Namensmuster: `cloud-shell-storage-<region>`.

   ![Auswählen der Ressourcengruppe](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Wählen Sie das Speicherkonto für Ihre Cloud Shell-Instanz aus.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Speicherkonto auswählen":::

1. Klicken Sie auf **Dateifreigaben**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Dateifreigaben auswählen":::

1. Wählen Sie die Standarddateifreigabe für Cloud Shell aus. Die Dateifreigabe weist das Namensformat `cs-<user>-<domain>-com-<uniqueGuid>` auf.

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Standarddateifreigabe":::

1. Fügen Sie ein neues Verzeichnis für Ihre Vorlagen hinzu. Wählen Sie dieses Verzeichnis aus.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Hinzufügen von Verzeichnissen":::

1. Wählen Sie die Option **Hochladen**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Vorlage hochladen":::

1. Suchen Sie Ihre Vorlage, und laden Sie sie hoch.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Auswählen einer Vorlage":::

1. Öffnen Sie die Cloud Shell-Eingabeaufforderung.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Cloud Shell öffnen":::

1. Navigieren Sie zum Verzeichnis **clouddrive**. Navigieren Sie zu dem Verzeichnis, das Sie zum Speichern der Vorlagen hinzugefügt haben.

1. Führen Sie die folgenden Befehle aus, um die Vorlage bereitzustellen:

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Bereitstellungsbefehlen finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md) und [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
- Informationen zum Anzeigen einer Änderungsvorschau vor der Bereitstellung einer Vorlage finden Sie unter [Was-wäre-wenn-Vorgang bei der Bereitstellung von ARM-Vorlagen](template-deploy-what-if.md).
