---
title: Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln ruhender Daten in ISEs
description: Erstellen und verwalten Sie Ihre eigenen Verschlüsselungsschlüssel in Azure Logic Apps, um ruhende Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) zu schützen.
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: 0057a4671dbc63bf53bafa8d2d742d4edcda1e5e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741047"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Einrichten von kundenseitig verwalteten Schlüsseln zum Verschlüsseln von ruhenden Daten für Integrationsdienstumgebungen (Integration Service Environment, ISE) in Azure Logic Apps

Azure Logic Apps nutzt Azure Storage zum Speichern und automatischen [Verschlüsseln von ruhenden Daten](../storage/common/storage-service-encryption.md). Diese Verschlüsselung schützt Ihre Daten und unterstützt Sie beim Einhalten der Sicherheits- und Complianceanforderungen Ihrer Organisation. Azure Storage verwendet standardmäßig von Microsoft verwaltete Schlüssel, um Ihre Daten zu verschlüsseln. Weitere Informationen zur Funktionsweise der Azure Storage-Verschlüsselung finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md) und [Azure-Datenverschlüsselung ruhender Daten](../security/fundamentals/encryption-atrest.md).

Wenn Sie eine [Integrationsdienstumgebung (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) zum Hosten Ihrer Logik-Apps erstellen und mehr Kontrolle über die von Azure Storage verwendeten Verschlüsselungsschlüssel haben möchten, können Sie mit [Azure Key Vault](../key-vault/general/overview.md) Ihren eigenen Schlüssel einrichten, verwenden und verwalten. Diese Funktion wird auch als „Bring Your Own Key“ (BYOK) bezeichnet, und Ihr Schlüssel wird als „vom Kunden verwalteter Schlüssel“ bezeichnet.

In diesem Thema erfahren Sie, wie Sie Ihren eigenen Verschlüsselungsschlüssel einrichten und angeben, der verwendet wird, wenn Sie Ihre ISE mithilfe der Logic Apps-REST-API erstellen. Die allgemeinen Schritte zum Erstellen einer ISE mithilfe der Logic Apps-REST-API finden Sie unter [Erstellen einer Integrationsdienstumgebung (Integration Service Environment, ISE) mithilfe der Logic Apps-REST-API](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Überlegungen

* Zum aktuellen Zeitpunkt ist die Unterstützung von kundenseitig verwalteten Schlüsseln für eine ISE nur in den folgenden Azure-Regionen verfügbar: „USA, Westen 2“, „USA, Osten“ und „USA, Süden-Mitte“

* Sie können einen vom Kunden verwalteten Schlüssel *nur beim Erstellen der ISE* angeben, nicht später. Sie können diesen Schlüssel nach dem Erstellen Ihrer ISE nicht mehr deaktivieren. Zurzeit wird das Rotieren eines vom Kunden verwalteten Schlüssels für eine ISE nicht unterstützt.

* Zur Unterstützung von kundenseitig verwalteten Schlüsseln muss die [systemseitig zugewiesene oder die benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) Ihrer ISE aktiviert sein. Mit dieser Identität kann Ihre ISE den auf geschützte Ressourcen wie virtuelle Computer und andere Systeme oder Dienste, die sich innerhalb eines virtuellen Azure-Netzwerks befinden oder damit verbunden sind, authentifizieren. Auf diese Weise müssen Sie sich nicht mit Ihren Anmeldeinformationen anmelden.

* Derzeit müssen Sie zum Erstellen einer ISE, die kundenseitig verwaltete Schlüssel unterstützt und beide verwalteten Identitätstypen aktiviert hat, die Logic Apps-REST-API über eine HTTPS PUT-Anforderung aufrufen.

* Sie müssen dem [Schlüsseltresor Zugriff auf die verwaltete Identität Ihrer ISE gewähren](#identity-access-to-key-vault), aber das Timing hängt davon ab, welche verwaltete Identität Sie verwenden.

  * **Systemseitig zugewiesene verwaltete Identität**: Innerhalb von *30 Minuten nach* dem Senden der HTTPS PUT-Anforderung zum Erstellen Ihrer ISE müssen Sie dem [Schlüsseltresor Zugriff auf die verwaltete Identität Ihrer ISE gewähren](#identity-access-to-key-vault). Andernfalls tritt bei der ISE-Erstellung ein Fehler auf und ein Berechtigungsfehler wird ausgegeben.

  * **Benutzerseitig zugewiesene verwaltete Identität**: Vor dem Senden der HTTPS PUT-Anforderung zum Erstellen Ihrer ISE [gewähren Sie dem Schlüsseltresor Zugriff auf die verwaltete Identität Ihrer ISE ](#identity-access-to-key-vault).

## <a name="prerequisites"></a>Voraussetzungen

* Die gleichen [Voraussetzungen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) und [Anforderungen zum Aktivieren des Zugriffs für die ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) wie bei der ISE-Erstellung über das Azure-Portal

* Ein Azure-Schlüsseltresor mit aktivierten Eigenschaften **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen)

  Weitere Informationen zum Aktivieren dieser Eigenschaften finden Sie unter [Übersicht über die Azure Key Vault-Funktion für vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) und [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md). Wenn Sie noch keine Erfahrung mit [Azure Key Vault](../key-vault/general/overview.md) haben, informieren Sie sich, wie Sie einen Schlüsseltresor über das [Azure-Portal](../key-vault/general/quick-create-portal.md), mithilfe der [Azure-Befehlszeilenschnittstelle](../key-vault/general/quick-create-cli.md) oder per [Azure PowerShell](../key-vault/general/quick-create-powershell.md) erstellen.

* Ein Schlüssel in Ihrem Schlüsseltresor, der mit diesen Eigenschaftswerten erstellt wurde:

  | Eigenschaft | Wert |
  |----------|-------|
  | **Schlüsseltyp** | RSA |
  | **RSA-Schlüsselgröße** | 2048 |
  | **Aktiviert** | Ja |
  |||

  ![Erstellen des vom Kunden verwalteten Verschlüsselungsschlüssels](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Weitere Informationen finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md) und unter dem Azure PowerShell-Befehl [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Ein Tool, mit dem Sie Ihre ISE durch Aufrufen der Logic Apps-REST-API mit einer HTTPS PUT-Anforderung erstellen können. Beispielsweise können Sie [Postman](https://www.getpostman.com/downloads/) verwenden oder eine Logik-App erstellen, die diese Aufgabe ausführt.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Erstellen der ISE mit Schlüsseltresor und Unterstützung für verwaltete Identitäten

Um Ihre ISE durch Aufrufen der Logic Apps-REST-API zu erstellen, führen Sie diese HTTPS PUT-Anforderung aus:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Version 2019-05-01 der Logic Apps-REST-API erfordert, dass Sie Ihre eigene HTTPS PUT-Anforderung für ISE-Connectors ausführen.

Der Bereitstellungsvorgang dauert in der Regel maximal zwei Stunden. Gelegentlich kann die Bereitstellung bis zu vier Stunden dauern. Wählen Sie zum Überprüfen des Bereitstellungsstatus im [Azure-Portal](https://portal.azure.com) auf Ihrer Azure-Symbolleiste das Benachrichtigungssymbol aus, um den Benachrichtigungsbereich zu öffnen.

> [!NOTE]
> Wenn die Bereitstellung fehlschlägt oder Sie Ihre ISE löschen, kann es bis zu einer Stunde dauern, bis Azure Ihre Subnetze freigibt. Daher müssen Sie möglicherweise warten, bevor Sie diese Subnetze in einer anderen ISE wiederverwenden können.
>
> Wenn Sie Ihr virtuelles Netzwerk löschen, dauert es in der Regel bis zu zwei Stunden, bis Azure Ihre Subnetze freigibt, dieser Vorgang kann aber auch länger dauern. 
> Stellen Sie beim Löschen virtueller Netzwerke sicher, dass keine Ressourcen mehr verbunden sind. 
> Beachten Sie hierzu die Anleitung [Löschen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Anforderungsheader

Schließen Sie die folgenden Eigenschaften in den Anforderungsheader ein:

* `Content-type`: Legen Sie diesen Eigenschaftswert auf `application/json` fest.

* `Authorization`: Legen Sie diesen Eigenschaftswert auf das Bearertoken für den Kunden fest, der Zugriff auf das gewünschte Azure-Abonnement bzw. die Ressourcengruppe hat.

### <a name="request-body"></a>Anforderungstext

Aktivieren Sie im Anforderungstext die Unterstützung für diese zusätzlichen Elemente, indem Sie die entsprechenden Informationen in der ISE-Definition bereitstellen:

* Die verwaltete Identität, die Ihre ISE zum Zugreifen auf Ihren Schlüsseltresor verwendet
* Ihr Schlüsseltresor und der vom Kunden verwaltete Schlüssel, den Sie verwenden möchten

#### <a name="request-body-syntax"></a>Syntax des Anforderungstexts

Hier ist die Syntax des Anforderungstexts, in der die Eigenschaften für das Erstellen der ISE beschrieben werden:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Beispiel für Anforderungstext

In diesem Beispielanforderungstext werden die Beispielwerte gezeigt:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Obgleich sich das Timing je nach der von Ihnen verwendeten verwalteten Identität unterscheidet, müssen Sie dem [Schlüsseltresor Zugriff auf die verwaltete Identität Ihrer ISE gewähren](#identity-access-to-key-vault).

* **Systemseitig zugewiesene verwaltete Identität**: Innerhalb von *30 Minuten nach* dem Senden der HTTPS PUT-Anforderung zum Erstellen Ihrer ISE müssen Sie Ihrem Schlüsseltresor eine Zugriffsrichtlinie für die systemseitig zugewiesene verwaltete Identität Ihrer ISE hinzufügen. Andernfalls tritt bei der Erstellung Ihrer ISE ein Fehler auf, und Sie erhalten einen Berechtigungsfehler.

* **Benutzerseitig zugewiesene verwaltete Identität**: Vor dem Senden der HTTPS PUT-Anforderung zum Erstellen Ihrer ISE fügen Sie Ihrem Schlüsseltresor eine Zugriffsrichtlinie für die benutzerseitig zugewiesene verwaltete Identität Ihrer ISE hinzu.

Für diese Aufgabe können Sie entweder den Azure PowerShell-Befehl [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) verwenden oder die folgenden Schritte im Azure-Portal ausführen:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren Azure-Schlüsseltresor.

1. Wählen Sie im Menü des Schlüsseltresors die Option **Zugriffsrichtlinien** > **Zugriffsrichtlinie hinzufügen** aus, z. B.:

   ![Hinzufügen einer Zugriffsrichtlinie für eine vom System zugewiesene verwaltete Identität](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Nachdem der Bereich **Zugriffsrichtlinie hinzufügen** geöffnet wurde, führen Sie die folgenden Schritte aus:

   1. Wählen Sie diese Optionen aus:

      | Einstellung | Werte |
      |---------|--------|
      | **Liste „Anhand einer Vorlage konfigurieren (optional)“** | Schlüsselverwaltung |
      | **Schlüsselberechtigungen** | - **Schlüsselverwaltungsvorgänge:** Get, List <p><p>- **Kryptografische Vorgänge:** Unwrap Key, Wrap Key |
      |||

      ![Wählen Sie „Schlüsselverwaltung“ > „Schlüsselberechtigungen“ aus.](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Wählen Sie unter **Prinzipal auswählen** die Option **Nichts ausgewählt** aus. Nachdem der Bereich **Prinzipal** geöffnet wurde, suchen Sie im Suchfeld nach Ihrer ISE, und wählen Sie sie aus. Wählen Sie nach Abschluss **Auswählen** > **Hinzufügen** aus.

      ![Auswählen Ihrer ISE zur Verwendung als Prinzipal](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Wenn Sie im Bereich **Zugriffsrichtlinien** fertig sind, wählen Sie **Speichern** aus.

Weitere Informationen finden Sie unter [Authentifizieren bei Key Vault](../key-vault/general/authentication.md) und [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Key Vault](../key-vault/general/overview.md)