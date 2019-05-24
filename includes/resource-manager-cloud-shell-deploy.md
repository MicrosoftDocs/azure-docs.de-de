---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66129029"
---
## <a name="deploy-template-from-cloud-shell"></a>Bereitstellen der Vorlage über Cloud Shell

Sie können Ihre Vorlage mithilfe von [Cloud Shell](../articles/cloud-shell/overview.md) bereitstellen. Um eine externe Vorlage bereitzustellen, geben Sie den URI der Vorlage genau wie bei jeder anderen externen Bereitstellung an. Um eine lokale Vorlage bereitzustellen, müssen Sie die Vorlage zuerst in das Speicherkonto für Ihre Cloud Shell-Instanz laden. In diesem Abschnitt wird beschrieben, wie Sie die Vorlage in Ihr Cloud Shell-Konto laden und als lokale Datei bereitstellen. Falls Sie Cloud Shell noch nicht verwendet haben, finden Sie unter [Übersicht über Azure Cloud Shell](../articles/cloud-shell/overview.md) Informationen zum Einrichten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie Ihre Cloud Shell-Ressourcengruppe aus. Namensmuster: `cloud-shell-storage-<region>`.

   ![Auswählen der Ressourcengruppe](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Wählen Sie das Speicherkonto für Ihre Cloud Shell-Instanz aus.

   ![Auswählen des Speicherkontos](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Wählen Sie **Blobs**aus.

   ![Auswählen von Blobs](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Wählen Sie **+ Container** aus.

   ![Hinzufügen eines Containers](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Geben Sie einen Namen und eine Zugriffsebene für Ihren Container ein. Die Beispielvorlage in diesem Artikel enthält keine vertraulichen Informationen, Sie können also anonymen Lesezugriff zulassen. Klicken Sie auf **OK**.

   ![Angeben von Containerwerten](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Wählen Sie den erstellten Container aus.

   ![Auswählen des neuen Containers](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Wählen Sie die Option **Hochladen**.

   ![Blob hochladen](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Suchen Sie Ihre Vorlage, und laden Sie sie hoch.

   ![Hochladen der Datei](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Wählen Sie die Vorlage aus, nachdem sie hochgeladen wurde.

   ![Auswählen der neuen Vorlage](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Kopieren Sie die URL.

   ![Kopieren der URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Öffnen Sie die Eingabeaufforderung.

   ![Öffnen von Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
