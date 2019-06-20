---
title: 'Azure Backup: Erstellen von Recovery Services-Tresoren mit der REST-API'
description: Verwalten von Sicherungs- und Wiederherstellungsvorgängen der Azure-VM-Sicherung mit der REST-API
services: backup
author: pvrk
manager: shivamg
keywords: REST-API; Azure-VM-Sicherung; Azure-VM-Wiederherstellung;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60646706"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Erstellen eines Azure Recovery Services-Tresors mit der REST-API

Die Schritte zum Erstellen eines Azure Recovery Services-Tresors mit der REST-API sind in der Dokumentation unter [Vaults – Create Or Update](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) (Tresore – Erstellen oder Aktualisieren) beschrieben. Wir nutzen dieses Dokument als Referenz zum Erstellen eines Tresors mit dem Namen „testVault“ in der Region „West US“ (USA, Westen).

Verwenden Sie den folgenden *PUT*-Vorgang, um einen Azure Recovery Services-Tresor zu erstellen oder zu aktualisieren.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Erstellen einer Anforderung

Zum Erstellen der *PUT*-Anforderung ist der `{subscription-id}`-Parameter erforderlich. Wenn Sie über mehrere Abonnements verfügen, lesen Sie [Verwenden mehrerer Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest): Sie definieren zusammen mit dem `api-version`-Parameter einen `{resourceGroupName}` und `{vaultName}` für Ihre Ressourcen. In diesem Artikel wird `api-version=2016-06-01` verwendet.

Die folgenden Header sind erforderlich:

| Anforderungsheader   | BESCHREIBUNG |
|------------------|-----------------|
| *Content-Type:*  | Erforderlich. Legen Sie diese Option auf `application/json` fest. |
| *Authorization:* | Erforderlich. Legen Sie diese Option auf ein gültiges `Bearer` [Zugriffstoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) fest. |

Weitere Informationen zum Erstellen der Anforderung finden Sie unter [Komponenten einer REST-API-Anforderung/Antwort](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Erstellen des Anforderungstexts

Die folgenden allgemeinen Definitionen werden verwendet, um einen Anforderungstext zu erstellen:

|NAME  |Erforderlich  |Typ  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|eTag     |         |   string      |  Optionales ETag       |
|location     |  true       |string         |   Speicherort von Ressourcen      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Eigenschaften des Tresors       |
|sku     |         |  [sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Gibt den eindeutigen Systembezeichner für jede Azure-Ressource an.     |
|tags     |         | Object        |     Ressourcentags    |

Beachten Sie, dass der Tresorname und Ressourcengruppenname im PUT-URI bereitgestellt werden. Im Anforderungstext wird der Standort definiert.

## <a name="example-request-body"></a>Beispiel für Anforderungstext

Der folgende Beispieltext wird verwendet, um in „West US“ (USA, Westen) einen Tresor zu erstellen. Geben Sie den Standort an. Die SKU lautet immer „Standard“.

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Antworten

Es gibt zwei erfolgreiche Antworten für den Vorgang, um einen Recovery Services-Tresor zu erstellen oder zu aktualisieren:

|NAME  |Type  |BESCHREIBUNG  |
|---------|---------|---------|
|200 – OK     |   [Tresor](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 – Erstellt     | [Tresor](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Erstellt      |

Weitere Informationen zu REST-API-Antworten finden Sie unter [Verarbeiten der Antwortnachricht](/rest/api/azure/#process-the-response-message).

### <a name="example-response"></a>Beispielantwort

Die komprimierte Antwort *201 – Erstellt* aus dem vorherigen Beispielanforderungstext zeigt, dass eine *id* zugewiesen wurde und *provisioningState* auf *Succeeded* festgelegt ist:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie eine Sicherungsrichtlinie zum Sichern einer Azure-VM in diesem Tresor](backup-azure-arm-userestapi-createorupdatepolicy.md).

Weitere Informationen zu den Azure-REST-APIs finden Sie in den folgenden Dokumenten:

- [Azure Recovery Services-Anbieter – REST-API](/rest/api/recoveryservices/)
- [Erste Schritte mit der Azure-REST-API](/rest/api/azure/)
