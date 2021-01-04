---
title: Bereitstellen von Ressourcen mit REST-API und Vorlagen
description: Verwenden Sie Azure Resource Manager und Resource Manager-REST-API, um Ressourcen in Azure bereitzustellen. Die Ressourcen werden in einer Resource Manager-Vorlage definiert.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: d1c8a365153007d3337d922bc163ba3767eeddc9
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675418"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Bereitstellen von Ressourcen mit ARM-Vorlagen und der Resource Manager-REST-API

In diesem Artikel wird erläutert, wie Ihre Ressourcen mithilfe der Azure Resource Manager-REST-API und von Azure Resource Manager-Vorlagen (ARM-Vorlagen) in Azure bereitgestellt werden.

Sie können entweder Ihre Vorlage in den Anforderungstext einschließen oder eine Verknüpfung zu einer Datei erstellen. Bei einer Datei kann es sich um eine lokale Datei oder eine externe Datei handeln, die über einen URI verfügbar ist. Wenn sich Ihre Vorlage in einem Speicherkonto befindet, können Sie den Zugriff auf die Vorlage beschränken und während der Bereitstellung ein SAS-Token (Shared Access Signature) angeben.

## <a name="deployment-scope"></a>Bereitstellungsumfang

Sie können als Ziel für Ihre Bereitstellung eine Ressourcengruppe, ein Azure-Abonnement, eine Verwaltungsgruppe oder einen Mandanten verwenden. Abhängig vom Umfang der Bereitstellung verwenden Sie unterschiedliche Befehle.

* Für die Bereitstellung in einer **Ressourcengruppe** verwenden Sie [Bereitstellungen – Erstellen](/rest/api/resources/deployments/createorupdate). Die Anforderung wird gesendet an:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

* Für die Bereitstellung in einem **Abonnement** verwenden Sie [Bereitstellungen – Erstellen im Abonnementbereich](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Die Anforderung wird gesendet an:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Weitere Informationen zu Bereitstellungen auf Abonnementebene finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](deploy-to-subscription.md).

* Für die Bereitstellung in einer **Verwaltungsgruppe** verwenden Sie [Bereitstellungen – Erstellen im Verwaltungsgruppenbereich](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Die Anforderung wird gesendet an:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Weitere Informationen zu Bereitstellungen auf Verwaltungsgruppenebene finden Sie unter [Erstellen von Ressourcen auf der Verwaltungsgruppenebene](deploy-to-management-group.md).

* Für die Bereitstellung in einem **Mandanten** verwenden Sie [Bereitstellungen – Erstellen oder Aktualisieren im Mandantenbereich](/rest/api/resources/deployments/createorupdateattenantscope). Die Anforderung wird gesendet an:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Weitere Informationen zu Bereitstellungen auf Mandantenebene finden Sie unter [Erstellen von Ressourcen auf der Mandantenebene](deploy-to-tenant.md).

Die Beispiele in diesem Artikel verwenden Ressourcengruppenbereitstellungen.

## <a name="deploy-with-the-rest-api"></a>Bereitstellen mit der REST-API

1. Legen Sie [allgemeine Parameter und Header](/rest/api/azure/) fest, einschließlich Authentifizierungstoken.

1. Wenn eine Bereitstellung in einer Ressourcengruppe erfolgen soll, die nicht vorhanden ist, erstellen Sie zunächst die Ressourcengruppe. Geben Sie Ihre Abonnement-ID, den Namen der neuen Ressourcengruppe und den Speicherort für Ihre Lösung an. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   Mit dem Anforderungstext ähnlich dem folgenden:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Vor dem Bereitstellen der Vorlage können Sie die Änderungen, die von der Vorlage an Ihrer Umgebung vorgenommen werden, in der Vorschau anzeigen. Überprüfen Sie anhand des [„Was-wäre-wenn“-Vorgangs](template-deploy-what-if.md), ob die Vorlage die erwarteten Änderungen vornimmt. „Was-wäre-wenn“ überprüft auch die Vorlage auf Fehler.

1. Geben Sie zum Bereitstellen einer Vorlage Ihre Abonnement-ID, den Namen der Ressourcengruppe und den Namen der Bereitstellung im Anforderungs-URI an.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   Geben Sie im Anforderungstext einen Link zu Ihrer Vorlage und Parameterdatei an. Weitere Informationen zur Parameterdatei finden Sie unter [Erstellen einer Resource Manager-Parameterdatei](parameter-files.md).

   Beachten Sie, dass **mode** auf **Incremental** festgelegt ist. Legen Sie zum Ausführen einer vollständigen Bereitstellung **mode** auf **Complete** fest. Gehen Sie bei Verwendung des Modus „Complete“ sehr umsichtig vor, da Sie versehentlich Ressourcen löschen können, die nicht in Ihrer Vorlage enthalten sind.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Wenn Sie den Antwort- und/oder den Anforderungsinhalt protokollieren möchten, fügen Sie `debugSetting` in die Anforderung ein.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Sie können das Speicherkonto so einrichten, das ein SAS-Token (Shared Access Signature) verwendet wird. Weitere Informationen finden Sie unter [Delegieren des Zugriffs mit einer SAS (Shared Access Signature)](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Wenn Sie einen vertraulichen Wert für einen Parameter (z.B. ein Kennwort) angeben müssen, fügen Sie den Wert einem Schlüsseltresor hinzu. Rufen Sie den Schlüsseltresor während der Bereitstellung wie im vorherigen Beispiel gezeigt ab. Weitere Informationen finden Sie unter [Übergeben sicherer Werte während der Bereitstellung](key-vault-parameter.md).

1. Anstatt für die Vorlage und die Parameter eine Verknüpfung mit Dateien zu erstellen, können Sie diese auch in den Anforderungstext einschließen. Im folgenden Beispiel ist der Anforderungstext mit der inline angegebenen Vorlage und den Parametern dargestellt:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Rufen Sie den Status der Vorlagenbereitstellung mit [Deployments - Get](/rest/api/resources/deployments/get) ab.

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>„Deployment name“ (Bereitstellungsname)

Sie können Ihrer Bereitstellung einen Namen wie `ExampleDeployment` geben.

Bei jeder Ausführung einer Bereitstellung wird dem Bereitstellungsverlauf der Ressourcengruppe ein Eintrag mit dem Bereitstellungsnamen hinzugefügt. Wenn Sie eine andere Bereitstellung ausführen und denselben Namen vergeben, wird der vorherige Eintrag durch die aktuelle Bereitstellung ersetzt. Wenn Sie eindeutige Einträge im Bereitstellungsverlauf beibehalten möchten, müssen Sie jeder Bereitstellung einen eindeutigen Namen geben.

Um einen eindeutigen Namen zu erstellen, können Sie eine Zufallszahl zuweisen. Sie können auch einen Datumswert hinzufügen.

Wenn Sie gleichzeitige Bereitstellungen in derselben Ressourcengruppe mit dem gleichen Bereitstellungsnamen ausführen, wird nur die letzte Bereitstellung abgeschlossen. Alle Bereitstellungen mit dem gleichen Namen, die noch nicht abgeschlossen wurden, werden durch die letzte Bereitstellung ersetzt. Wenn Sie z. B. eine Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung mit dem Namen `newStorage` ausführen, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, wird nur ein Speicherkonto bereitgestellt. Das resultierende Speicherkonto hat den Namen `storage2`.

Führen Sie jedoch eine Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage1` bereitstellt, und führen Sie direkt nach dem Abschluss eine andere Bereitstellung mit dem Namen `newStorage` aus, die ein Speicherkonto mit dem Namen `storage2` bereitstellt, erhalten Sie zwei Speicherkonten. Eines hat den Namen `storage1` und das andere den Namen `storage2`. Es ist jedoch nur ein Eintrag im Bereitstellungsverlauf vorhanden.

Wenn Sie für jede Bereitstellung einen eindeutigen Namen angeben, können Sie diese ohne Konflikt gleichzeitig ausführen. Wenn Sie eine Bereitstellung namens `newStorage1` ausführen, die ein Speicherkonto namens `storage1` bereitstellt, und gleichzeitig eine andere Bereitstellung namens `newStorage2` ausführen, die ein Speicherkonto namens `storage2` bereitstellt, erhalten Sie zwei Speicherkonten und zwei Einträge im Bereitstellungsverlauf.

Geben Sie jeder Bereitstellung einen eindeutigen Namen, um Konflikte mit gleichzeitigen Bereitstellungen zu vermeiden und eindeutige Einträge im Bereitstellungsverlauf zu gewährleisten.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Rollback zu einer erfolgreiche Bereitstellung, wenn ein Fehler auftritt, finden Sie unter [Rollback bei Fehler zu erfolgreicher Bereitstellung](rollback-on-error.md).
- Wenn Sie angeben möchten, wie Ressourcen behandelt werden sollen, die in der Ressourcengruppe enthalten sind, aber nicht in der Vorlage definiert wurden, lesen Sie die Informationen unter [Azure Resource Manager-Bereitstellungsmodi](deployment-modes.md).
- Informationen zum Arbeiten mit asynchronen REST-Vorgängen finden Sie unter [Nachverfolgen asynchroner Vorgänge in Azure](../management/async-operations.md).
- Weitere Informationen zu Vorlagen finden Sie unter [Verstehen der Struktur und Syntax von ARM-Vorlagen](template-syntax.md).

