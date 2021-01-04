---
title: Azure Key Vault als Event Grid-Quelle
description: Beschreibt die Eigenschaften und das Schema, die für Azure Key Vault-Ereignisse im Azure Event Grid verfügbar sind
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 605502794f2f3aa4f4edd14b49efda5003b91146
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460407"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault als Event Grid-Quelle

Dieser Artikel enthält Informationen zu den Eigenschaften und zum Schema für Ereignisse in [Azure Key Vault](../key-vault/index.yml). Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

### <a name="available-event-types"></a>Verfügbare Ereignistypen

Ein Azure Key Vault Konto generiert die folgenden Ereignistypen:

| Vollständiger Ereignisname | Anzeigename des Ereignisses | BESCHREIBUNG |
| ---------- | ----------- |---|
| Microsoft.KeyVault.CertificateNewVersionCreated | Certificate New Version Created (Neue Zertifikatversion erstellt) | Wird ausgelöst, wenn ein neues Zertifikat oder eine neue Zertifikatversion erstellt wird. |
| Microsoft.KeyVault.CertificateNearExpiry | Certificate Near Expiry (Zertifikat läuft bald ab) | Wird ausgelöst, wenn die aktuelle Version des Zertifikats abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.CertificateExpired | Zertifikat abgelaufen | Wird ausgelöst, wenn das Zertifikat abgelaufen ist. |
| Microsoft.KeyVault.KeyNewVersionCreated | Key New Version Created (Neue Schlüsselversion erstellt) | Wird ausgelöst, wenn ein neuer Schlüssel oder eine neue Schlüsselversion erstellt wird. |
| Microsoft.KeyVault.KeyNearExpiry | Key Near Expiry (Schlüssel läuft bald ab) | Wird ausgelöst, wenn die aktuelle Version eines Schlüssels demnächst abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.KeyExpired | Schlüssel abgelaufen | Wird ausgelöst, wenn ein Schlüssel abgelaufen ist. |
| Microsoft.KeyVault.SecretNewVersionCreated | Secret New Version Created (Neue Version des Geheimnisses erstellt) | Wird ausgelöst, wenn ein neues Geheimnis oder eine neue Geheimnisversion erstellt wird. |
| Microsoft.KeyVault.SecretNearExpiry | Secret Near Expiry (Geheimnis läuft demnächst ab) | Wird ausgelöst, wenn die aktuelle Version eines Geheimnisses demnächst abläuft. (Das Ereignis wird 30 Tage vor dem Ablaufdatum ausgelöst.) |
| Microsoft.KeyVault.SecretExpired | Secret Expired (Geheimnis abgelaufen) | Wird ausgelöst, wenn ein Geheimnis abgelaufen ist. |
| Microsoft.KeyVault.VaultAccessPolicyChanged | Geänderte Tresorzugriffsrichtlinie | Wird ausgelöst, wenn eine Zugriffsrichtlinie für Key Vault geändert wird. Enthält ein Szenario, in dem das Key Vault-Berechtigungsmodell in/von Azure RBAC geändert wird.  |

### <a name="event-examples"></a>Ereignisbeispiele

Das folgende Beispiel zeigt das Schema für **Microsoft.KeyVault.SecretNewVersionCreated**:

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | BESCHREIBUNG |
| ---------- | ----------- |---|
| id | Zeichenfolge | Die ID des Objekts, das dieses Ereignis ausgelöst hat. |
| vaultName | Zeichenfolge | Der Schlüsseltresorname des Objekts, das dieses Ereignis ausgelöst hat. |
| objectType | Zeichenfolge | Der Typ des Objekts, das dieses Ereignis ausgelöst hat. |
| objectName | Zeichenfolge | Der Name des Objekts, das dieses Ereignis ausgelöst hat. |
| version | Zeichenfolge | Die Version des Objekts, das dieses Ereignis ausgelöst hat. |
| nbf | number | Das Anfangsdatum in Sekunden seit 1970-01-01t00:00:00Z für das Objekt, das dieses Ereignis ausgelöst hat. |
| exp | number | Das Ablaufdatum in Sekunden seit 1970-01-01t00:00:00Z des Objekts, das dieses Ereignis ausgelöst hat. |

## <a name="tutorials-and-how-tos"></a>Tutorials und Anleitungen
|Titel  |BESCHREIBUNG  |
|---------|---------|
| [Überwachen von Key Vault-Ereignissen mit Azure Event Grid](../key-vault/general/event-grid-overview.md) | Übersicht über die Integration von Key Vault in Event Grid. |
| [Tutorial: Erstellen und Überwachen von Key Vault-Ereignissen mit Event Grid](../key-vault/general/event-grid-logicapps.md) | Erfahren Sie, wie Sie Event Grid-Benachrichtigungen für Key Vault einrichten. |


## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
* Weitere Informationen zu Key Vault finden Sie unter [Was ist Azure Key Vault?](../key-vault/general/overview.md)

