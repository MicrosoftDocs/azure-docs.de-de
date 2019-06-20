---
title: Verwenden von Azure Key Vault mit verwalteten Anwendungen | Microsoft-Dokumentation
description: Zeigt die Verwendung von Zugriffsgeheimnissen in Azure Key Vault bei der Bereitstellung von verwalteten Anwendungen
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 55410250ccd4dfceac8ac9ae5b81d4736de0d91a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588235"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Zugreifen auf das Geheimnis im Schlüsseltresor bei der Bereitstellung von Azure Managed Applications

Wenn Sie einen zu schützenden Wert (z.B. ein Kennwort) während der Bereitstellung als Parameter übergeben müssen, können Sie den Wert aus einer Instanz von [Azure Key Vault](../key-vault/key-vault-whatis.md) abrufen. Um bei der Bereitstellung verwalteter Anwendungen auf den Schlüsseltresor zuzugreifen, müssen Sie Zugriff auf den Dienstprinzipal des **Applianceressourcenanbieters** gewähren. Der Managed Applications-Dienst verwendet diese Identität, um Vorgänge auszuführen. Um einen Wert während der Bereitstellung erfolgreich aus einem Schlüsseltresor abzurufen, muss der Dienstprinzipal auf den Schlüsseltresor zugreifen können.

Dieser Artikel beschreibt, wie Sie den Schlüsseltresor zum Arbeiten mit verwalteten Anwendungen konfigurieren.

## <a name="enable-template-deployment"></a>Aktivieren der Vorlagenbereitstellung

1. Wählen Sie im Portal Ihren Schlüsseltresor aus.

1. Klicken Sie auf **Zugriffsrichtlinien**.   

   ![Zugriffsrichtlinien auswählen](./media/key-vault-access/select-access-policies.png)

1. Wählen Sie **Klicken Sie, um erweiterte Zugriffsrichtlinien anzuzeigen.** aus.

   ![Erweiterte Zugriffsrichtlinien anzeigen](./media/key-vault-access/advanced.png)

1. Wählen Sie **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** aus. Wählen Sie anschließend **Speichern** aus.

   ![Aktivieren der Vorlagenbereitstellung](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Hinzufügen des Diensts als Mitwirkender

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** .

   ![„Zugriffssteuerung“ auswählen](./media/key-vault-access/access-control.png)

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

   ![„Hinzufügen“ auswählen](./media/key-vault-access/add-access-control.png)

1. Wählen Sie **Mitwirkender** für die Rolle aus. Suchen Sie nach dem **Applianceressourcenanbieter**, und wählen Sie ihn in den verfügbaren Optionen aus.

   ![Nach Anbieter suchen](./media/key-vault-access/search-provider.png)

1. Wählen Sie **Speichern** aus.

## <a name="reference-key-vault-secret"></a>Verweisen auf Schlüsseltresorgeheimnis

Um ein Geheimnis aus einem Schlüsseltresor an eine Vorlage in Ihrer verwalteten Anwendung zu übergeben, müssen Sie eine [verknüpfte Vorlage](../azure-resource-manager/resource-group-linked-templates.md) verwenden und in den Parametern für die verknüpfte Vorlage auf den Schlüsseltresor verweisen. Geben Sie die Ressourcen-ID des Schlüsseltresors und den Namen des Geheimnisses an.

```json
"resources": [{
  "apiVersion": "2015-01-01",
  "name": "linkedTemplate",
  "type": "Microsoft.Resources/deployments",
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"
          },
          "secretName": "<secret-name>"
        }
      },
      "adminLogin": { "value": "[parameters('adminLogin')]" },
      "sqlServerName": {"value": "[parameters('sqlServerName')]"}
    }
  }
}],
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben Ihren Schlüsseltresor so konfiguriert, dass während der Bereitstellung einer verwalteten Anwendung darauf zugegriffen werden kann.

* Informationen zum Übergeben eines Werts aus einem Schlüsseltresor als Vorlagenparameter finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](../azure-resource-manager/resource-manager-keyvault-parameter.md).
* Beispiele zu verwalteten Anwendungen finden Sie unter [Sample projects for Azure managed applications](sample-projects.md) (Beispielprojekte für verwaltete Azure-Anwendungen).
* Informationen zum Erstellen einer UI-Definitionsdatei für eine verwaltete Anwendung finden Sie unter [Erste Schritte mit „CreateUiDefinition“](create-uidefinition-overview.md).