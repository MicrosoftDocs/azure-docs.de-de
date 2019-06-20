---
title: Ausführen mehrerer Aktionen über eine Azure IoT Central-Regel | Microsoft-Dokumentation
description: Führen Sie mehrere Aktionen über eine einzelne IoT Central-Regel aus, und erstellen Sie wiederverwendbare Aktionsgruppen, die Sie von mehreren Regeln aus ausführen können.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517206"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Gruppieren mehrerer Aktionen für die Ausführung über eine oder mehrere Regeln

*Dieser Artikel gilt für Ersteller und Administratoren.*

In Azure IoT Central erstellen Sie Regeln, um Aktionen auszuführen, wenn eine Bedingung erfüllt wird. Regeln basieren auf der Gerätetelemetrie oder Ereignissen. Sie können dem Betreiber beispielsweise eine Benachrichtigung zukommen lassen, wenn ein Gerät einen Temperaturschwellenwert überschreitet. In diesem Artikel wird die Verwendung von *Aktionsgruppen* in [Azure Monitor](../azure-monitor/overview.md) zum Anfügen mehrerer Aktionen an eine IoT Central-Regel erläutert. Sie können eine Aktionsgruppe an mehrere Regeln anfügen. Eine [Aktionsgruppe](../azure-monitor/platform/action-groups.md) ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Anwendung mit nutzungsbasierter Bezahlung
- Ein Azure-Konto und -Abonnement zum Erstellen und Verwalten von Azure Monitor-Aktionsgruppen

## <a name="create-action-groups"></a>Erstellen von Aktionsgruppen

Sie können [Aktionsgruppen im Azure-Portal erstellen und verwalten](../azure-monitor/platform/action-groups.md) oder eine [Azure Resource Manager-Vorlage](../azure-monitor/platform/action-groups-create-resource-manager-template.md) verwenden.

Eine Aktionsgruppe hat folgende Funktionen:

- Senden von Benachrichtigungen (z.B. per E-Mail oder SMS) oder Tätigen von Sprachanrufen
- Ausführen von Aktionen, z.B. das Aufrufen eines Webhooks

Auf dem folgenden Screenshot ist eine Aktionsgruppe dargestellt, die Benachrichtigungen per E-Mail und SMS versendet und einen Webhook aufruft:

![Aktionsgruppe](media/howto-use-action-groups/actiongroup.png)

Wenn Sie eine Aktionsgruppe in einer IoT Central-Regel verwenden möchten, muss diese sich im selben Azure-Abonnement wie die IoT Central-Anwendung befinden.

## <a name="use-an-action-group"></a>Verwenden einer Aktionsgruppe

Erstellen Sie zunächst eine Telemetrie- oder Ereignisregel, um eine Aktionsgruppe in Ihrer IoT Central-Anwendung zu verwenden. Klicken Sie auf **Azure Monitor-Aktionsgruppen**, wenn Sie eine Aktion zur Regel hinzufügen:

![Aktion auswählen](media/howto-use-action-groups/chooseaction.png)

Wählen Sie eine Aktionsgruppe aus Ihrem Azure-Abonnement aus:

![Aktionsgruppe auswählen](media/howto-use-action-groups/chooseactiongroup.png)

Wählen Sie **Speichern** aus. Die Aktionsgruppe wird nun in der Liste der Aktionen angezeigt, die ausgeführt werden sollen, wenn die Regel ausgelöst wird:

![Gespeicherte Aktionsgruppe](media/howto-use-action-groups/savedactiongroup.png)

In der folgenden Tabelle werden die Informationen zusammengefasst, die an die unterstützten Aktionstypen gesendet werden:

| Aktionstyp | Ausgabeformat |
| ----------- | -------------- |
| E-Mail       | IoT Central-Standardvorlage für E-Mails |
| sms         | Azure IoT Central-Warnung: ${applicationName} - "${ruleName}" triggered on "${deviceName}" at ${triggerDate} ${triggerTime} (${applicationName} - "${ruleName}" wurde für "${deviceName}" um ${triggerDate} ${triggerTime} ausgelöst.) |
| Sprache       | Azure I.O.T Central-Warnung: rule "${ruleName}" triggered on device "${deviceName}" at ${triggerDate} ${triggerTime}, in application ${applicationName} (Regel "${ruleName}" wurde auf dem Gerät "${deviceName}" um ${triggerDate} ${triggerTime}, in der Anwendung ${applicationName} ausgelöst.) |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

Der folgende Text ist ein Beispiel für eine SMS einer Aktionsgruppe:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Der folgende JSON-Code stellt ein Beispiel für eine Webhooknutzlastaktion dar:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Sie wissen nun, wie Sie Ihre Aktionsgruppen mit Regeln verwenden können. Es wird empfohlen, sich im Anschluss mit der [Verwaltung von Geräten](howto-manage-devices.md) auseinanderzusetzen.
