---
title: 'Freigeben außerhalb Ihrer Organisation (ARM-Vorlage): Schnellstartanleitung zu Azure Data Share'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Azure Data Share und einer Azure Resource Manager-Vorlage (ARM-Vorlage) Daten für Kunden und Partner freigeben.
author: mumian
ms.author: jgao
ms.service: data-share
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/19/2020
ms.openlocfilehash: 5abe92120c8b822ac86ced90658869a0858d4ff4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487686"
---
# <a name="quickstart-share-data-using-azure-data-share-and-arm-template"></a>Schnellstart: Freigeben von Daten mithilfe von Azure Data Share und einer ARM-Vorlage

Hier erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine neue Azure Data Share-Instanz anhand eines Azure-Speicherkontos einrichten. Außerdem wird beschrieben, wie Sie mit dem Freigeben von Daten für Kunden und Partner außerhalb Ihrer Azure-Organisation beginnen. Eine Liste der unterstützten Datenspeicher finden Sie unter [Unterstützte Datenspeicher in Azure Data Share](./supported-data-stores.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-data-share-share-storage-account/).

:::code language="json" source="~/quickstart-templates/101-data-share-share-storage-account/azuredeploy.json":::

Die folgenden Ressourcen sind in der Vorlage definiert:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts):
* [Microsoft.Storage/storageAccounts/blobServices/containers](/azure/templates/microsoft.storage/storageaccounts/blobservices/containers)
* [Microsoft.DataShare/accounts](/azure/templates/microsoft.datashare/accounts)
* [Microsoft.DataShare/accounts/shares](/azure/templates/microsoft.datashare/accounts/shares)
* [Microsoft.Storage/storageAccounts/providers/roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [Microsoft.DataShare/accounts/shares/dataSets](/azure/templates/microsoft.datashare/accounts/shares/datasets)
* [Microsoft.DataShare/accounts/shares/invitations](/azure/templates/microsoft.datashare/accounts/shares/invitations)
* [Microsoft.DataShare/accounts/shares/synchronizationSettings](/azure/templates/microsoft.datashare/accounts/shares/synchronizationsettings)

Diese Vorlage führt folgende Aufgaben aus:

* Erstellen eines Speicherkontos und eines Containers zur Verwendung als Datenquelle für die Datenfreigabe.
* Erstellen eines Data Share-Kontos und einer Datenfreigabe.
* Erstellen einer Rollenzuweisung, um der Quellressource der Datenfreigabe die Rolle „Storage-Blobdatenleser“ zuzuweisen. Siehe [Rollen und Anforderungen für Azure Data Share](./concepts-roles-permissions.md).
* Hinzufügen eines Datasets zur Datenfreigabe.
* Hinzufügen von Empfängern zur Datenfreigabe.
* Aktivieren eines Momentaufnahmezeitplans für die Datenfreigabe.

Diese Vorlage wurde zu Lernzwecken erstellt. In der Praxis verfügen Sie in der Regel über einige Daten in einem vorhandenen Speicherkonto. Die Rollenzuweisung müsste erstellt werden, bevor Sie eine Vorlage oder ein Skript zum Erstellen des Datasets ausführen. Beim Bereitstellen der Vorlage kann die folgende Fehlermeldung angezeigt werden:

```plaintext
"Missing permissions for DataShareAcccount on resource 'subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>' (Code: 5006)"
```

Der Grund hierfür ist, dass die Bereitstellung versucht, das Dataset zu erstellen, bevor die Azure-Rollenzuweisung abgeschlossen ist. Die Bereitstellung kann trotz dieser Fehlermeldung erfolgreich sein. Sie können die Schritte im Abschnitt [Überprüfen der bereitgestellten Ressourcen](#review-deployed-resources) trotzdem durchführen.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie das folgende Image aus, um sich bei Azure anzumelden und die Vorlage zu öffnen.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-share-share-storage-account%2Fazuredeploy.json)
1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein:

    * **Abonnement** : Wählen Sie ein Azure-Abonnement zum Erstellen der Datenfreigabe und anderer Ressourcen aus.
    * **Ressourcengruppe** : Klicken Sie auf **Neu erstellen** , um eine neue Ressourcengruppe zu erstellen, oder wählen Sie eine vorhandene Ressourcengruppe aus.
    * **Standort** : Wählen Sie einen Standort für die Ressourcengruppe aus.
    * **Projektname** : Geben Sie einen Projektnamen ein.  Der Projektname wird verwendet, um Ressourcennamen zu generieren.  Weitere Informationen finden Sie in den Variablendefinitionen in der vorherigen Vorlage.
    * **Standort** : Wählen Sie einen Standort für die Ressourcen aus.  Sie können den gleichen Standort verwenden wie für die Ressourcengruppe.
    * **Einladungs-E-Mail** : Geben Sie die E-Mail-Adresse für die Anmeldung bei Azure des Empfängers der Datenfreigabe ein.  Ein E-Mail-Alias funktioniert nicht.

    Übernehmen Sie für den Rest der Einstellungen die Standardwerte.
1. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** und anschließend **Kaufen** aus.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie das Data Share-Konto, das Sie erstellt haben.
1. Wählen Sie im Menü auf der linken Seite die Option **Send Shares** (Freigaben senden) aus.  Das Speicherkonto sollte angezeigt werden.
1. Wählen Sie Speicherkonto aus.  Unter **Details** werden die Synchronisierungseinstellungen angezeigt, die Sie in der Vorlage konfiguriert haben.

    ![Synchronisierungseinstellungen des Azure Data Share-Speicherkontos](./media/share-your-data-arm/azure-data-share-storage-account-synchronization-settings.png)
1. Wählen Sie oben die Option **Einladungen** aus. Die E-Mail-Adresse, die Sie beim Bereitstellen der Vorlage angegeben haben, sollte angezeigt werden. Der **Status** sollte **Ausstehend** lauten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie eine Azure Data Share-Instanz erstellen und Empfänger einladen. Fahren Sie mit dem Tutorial [Akzeptieren und Empfangen von Daten](subscribe-to-data-share.md) fort, um mehr darüber zu erfahren, wie ein Datenconsumer eine Datenfreigabe akzeptieren und empfangen kann.
