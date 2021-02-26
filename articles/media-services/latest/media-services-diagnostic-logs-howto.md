---
title: Überwachen von Diagnoseprotokollen über Azure Monitor
description: In diesem Artikel wird das Weiterleiten und Anzeigen von Diagnoseprotokollen über Azure Monitor veranschaulicht.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d9252a5fdf7b1c68c1bb64bd22ede8640184a0c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953953"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Überwachen von Media Services-Diagnoseprotokollen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) ermöglicht Ihnen die Überwachung von Metriken und Diagnoseprotokollen, die Ihnen zu verstehen helfen, wie sich Ihre Anwendungen verhalten. Eine ausführliche Beschreibung dieser Funktion und Argumente für die Verwendung der Azure Media Services-Metriken und -Diagnoseprotokolle finden Sie unter [Überwachen von Media Services-Metriken und -Diagnoseprotokollen](media-services-metrics-diagnostic-logs.md).

Dieser Artikel zeigt Ihnen, wie Sie Daten an das Speicherkonto weiterleiten und dann die Daten anzeigen können.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md)
- Siehe [Überwachen von Media Services-Metriken und -Diagnoseprotokollen](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Weiterleiten von Daten an das Speicherkonto über das Portal

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
1. Navigieren Sie zu Ihrem Media Services-Konto, und klicken Sie auf **Diagnoseeinstellungen** unter **Überwachen**. Es wird eine Liste aller Ressourcen in Ihrem Abonnement eingeblendet, die mithilfe von Azure Monitor Überwachungsdaten generieren.

    ![Screenshot des Abschnitts „Überwachung“ mit Hervorhebung der Option „Diagnoseeinstellungen“](media/media-services-diagnostic-logs/logs01.png)

1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   Eine Diagnoseeinstellung für eine Ressource ist eine Definition, *welche* Überwachungsdaten von einer bestimmten Ressource weitergeleitet werden und *wo* diese Überwachungsdaten landen sollen.

1. Geben Sie im eingeblendeten Abschnitt der Einstellung einen **Namen**, und aktivieren Sie das Kontrollkästchen **In einem Speicherkonto archivieren**.

    Wählen Sie das Speicherkontos aus, an das die Protokolle gesendet werden sollen, und drücken Sie auf **OK**.
1. Aktivieren Sie alle Kontrollkästchen unter **Protokoll** und **Metrik**. Je nach Ressourcentyp wird Ihnen nur eine dieser Optionen angeboten. Diese Kontrollkästchen steuern, welche Kategorien von Protokoll- und Metrikdaten, die für den jeweiligen Ressourcentyp verfügbar sind, an das ausgewählte Ziel, in diesem Fall ein Speicherkonto, gesendet werden.

   ![Abschnitt „Diagnoseeinstellungen“](media/media-services-diagnostic-logs/logs02.png)
1. Legen Sie den Schieberegler **Aufbewahrung (Tage)** auf 30 fest. Mit diesem Schieberegler legen Sie die Anzahl von Tagen fest, die Überwachungsdaten im Speicherkonto aufbewahrt werden sollen. Azure Monitor löscht automatisch Daten, die älter als die angegebene Anzahl von Tagen sind. Bei einer Aufbewahrungsdauer von 0 Tagen werden die Daten dauerhaft gespeichert.
1. Klicken Sie auf **Speichern**.

Zu Ihrer Ressource gehörige Überwachungsdaten werden nun in das Speicherkonto übertragen.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Weiterleiten von Daten an das Speicherkonto mithilfe der Azure CLI

Um das Speichern von Diagnoseprotokollen in einem Speicherkonto zu aktivieren, führen Sie den Azure CLI-Befehl `az monitor diagnostic-settings` wie folgt aus:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Beispiel:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Anzeigen von Daten im Speicherkonto über das Portal

Wenn Sie die vorherigen Schritte ausgeführt haben, wurde begonnen, die Daten in Ihr Speicherkonto zu übertragen.

Unter Umständen müssen Sie bis zu fünf Minuten warten, bis das Ereignis im Speicherkonto erscheint.

1. Navigieren Sie im Portal auf der linken Navigationsleiste zum Abschnitt **Speicherkonten**.
1. Bestimmen Sie das Speicherkonto, das Sie im vorherigen Abschnitt erstellt haben, und klicken Sie darauf.
1. Klicken Sie auf **Blobs** und dann auf den Container mit der Bezeichnung **insights-logs-keydeliveryrequests**. Dieser Container enthält Ihre Protokolle. Überwachungsdaten werden anhand der Ressourcen-ID in Container und dann nach Datum und Uhrzeit unterteilt.
1. Navigieren Sie zur Datei „PT1H.json“, indem Sie auf die Container für Ressourcen-ID, Datum und Uhrzeit klicken. Klicken Sie auf die Datei „PT1H.json“ und dann auf **Herunterladen**.

 Sie können jetzt das JSON-Ereignis anzeigen, das im Speicherkonto gespeichert wurde.

### <a name="examples-of-pt1hjson"></a>Beispiele für PT1H.json

#### <a name="clear-key-delivery-log"></a>Übermittlungsprotokoll für unverschlüsselten Schlüssel

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Übermittlungsprotokoll für verschlüsselten Widevine-Schlüssel

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="see-also"></a>Weitere Informationen

* [Azure Monitor-Metriken](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor-Diagnoseprotokolle](../../azure-monitor/platform/platform-logs-overview.md)
* [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>Nächste Schritte

[Überwachen von Metriken](media-services-metrics-howto.md)
