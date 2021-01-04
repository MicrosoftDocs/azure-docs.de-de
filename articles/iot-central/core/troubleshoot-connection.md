---
title: Problembehandlung bei Geräteverbindungen mit Azure IoT Central | Microsoft-Dokumentation
description: Problembehandlung, warum keine Daten von Ihren Geräten in IoT Central angezeigt werden
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 2bbf400840c968587de3a0a0951d28c7c35b210f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990889"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Behandeln von Problemen, warum Daten von Ihren Geräten nicht in Azure IoT Central angezeigt werden

Dieses Dokument hilft Geräteentwicklern herauszufinden, warum die Daten, die ihre Geräte an IoT Central senden, in der Anwendung möglicherweise nicht angezeigt werden.

Es gibt zwei Hauptbereiche, die untersucht werden müssen:

- Probleme mit der Gerätekonnektivität
  - Authentifizierungsprobleme, wenn das Gerät z. B. ungültige Anmeldeinformationen besitzt
  - Probleme mit der Netzwerkkonnektivität
  - Gerät nicht genehmigt oder blockiert
- Probleme mit der Form der Gerätenutzdaten

Dieser Leitfaden zur Problembehandlung konzentriert sich auf Probleme mit der Gerätekonnektivität und der Form der Gerätenutzdaten.

## <a name="device-connectivity-issues"></a>Probleme mit der Gerätekonnektivität

Dieser Abschnitt hilft Ihnen festzustellen, ob Ihre Daten IoT Central erreichen.

Sofern dies noch nicht erledigt ist, installieren Sie das Tool `az cli` und die Erweiterung `azure-iot`.

Informationen zur Installation von `az cli` finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Führen Sie zum [Installieren](/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation) der Erweiterung `azure-iot` den folgenden Befehl aus:

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> Wenn Sie zum ersten Mal einen Erweiterungsbefehl ausführen, werden Sie möglicherweise aufgefordert, die `uamqp`-Bibliothek zu installieren.

Wenn Sie die Erweiterung `azure-iot` installiert haben, starten Sie Ihr Gerät, um zu sehen, ob die von ihm gesendeten Nachrichten ihren Weg zu IoT Central finden.

Verwenden Sie die folgenden Befehle, um sich in dem Abonnement anzumelden, in dem sich Ihre IoT Central-Anwendung befindet:

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

Verwenden Sie den folgenden Befehl, um die vom Gerät gesendeten Telemetriedaten zu überwachen:

```cmd/bash
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Wenn das Gerät erfolgreich mit IoT Central verbunden wurde, sehen Sie eine Ausgabe ähnlich der folgenden:

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Um die Eigenschaftsaktualisierungen zu überwachen, die Ihr Gerät mit IoT Central austauscht, verwenden Sie den folgenden Vorschaubefehl:

```cmd/bash
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Wenn das Gerät erfolgreich Eigenschaftsaktualisierungen sendet, wird eine Ausgabe ähnlich der folgenden angezeigt:

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Wenn Sie sehen, dass Daten in Ihrem Terminal angezeigt werden, dann gelangen die Daten bis zu Ihrer IoT Central-Anwendung.

Wenn nach einigen Minuten keine Daten angezeigt werden, versuchen Sie, die Taste `Enter` oder `return` auf Ihrer Tastatur zu drücken, falls die Ausgabe blockiert ist.

Wenn auf Ihrem Terminal immer noch keine Daten angezeigt werden, ist es wahrscheinlich, dass Ihr Gerät Probleme mit der Netzwerkverbindung hat oder Daten nicht ordnungsgemäß an IoT Central sendet.

### <a name="check-the-provisioning-status-of-your-device"></a>Überprüfen des Bereitstellungsstatus Ihres Geräts

Wenn Ihre Daten nicht auf dem Monitor angezeigt werden, überprüfen Sie den Bereitstellungsstatus Ihres Geräts, indem Sie den folgenden Befehl ausführen:

```cmd/bash
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Die folgende Ausgabe zeigt ein Beispiel für ein Gerät, das für die Verbindung blockiert ist:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Gerätebereitstellungsstatus | BESCHREIBUNG | Mögliche Entschärfung |
| - | - | - |
| Bereitgestellt | Kein sofort erkennbares Problem. | – |
| Registriert | Das Gerät hat noch keine Verbindung mit IoT Central hergestellt. | Überprüfen Sie Ihre Geräteprotokolle auf Konnektivitätsprobleme. |
| Blockiert | Das Gerät ist für die Verbindung mit IoT Central blockiert. | Das Gerät kann sich nicht mit der IoT Central-Anwendung verbinden. Heben Sie die Blockierung des Geräts in IoT Central auf, und versuchen Sie es erneut. Weitere Informationen finden Sie unter [Geräte blockieren](concepts-get-connected.md#device-status-values). |
| Nicht genehmigt | Das Gerät ist nicht genehmigt. | Das Gerät ist nicht für die Verbindung mit der IoT Central-Anwendung zugelassen. Genehmigen Sie das Gerät in IoT Central, und versuchen Sie es erneut. Weitere Informationen finden Sie unter [Geräte genehmigen](concepts-get-connected.md#device-registration). |
| Nicht zugeordnet | Das Gerät ist keiner Gerätevorlage zugeordnet. | Ordnen Sie das Gerät einer Gerätevorlage zu, sodass IoT Central weiß, wie die Daten zu analysieren sind. |

Erfahren Sie mehr über [Gerätestatuscodes](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Fehlercodes

Wenn Sie immer noch nicht diagnostizieren können, warum Ihre Daten nicht in `monitor-events` angezeigt werden, besteht der nächste Schritt darin, nach Fehlercodes zu suchen, die von Ihrem Gerät gemeldet werden.

Starten Sie eine Debugsitzung auf Ihrem Gerät, oder erfassen Sie Protokolle von Ihrem Gerät. Prüfen Sie auf alle Fehlercodes, die das Gerät meldet.

Die folgenden Tabellen zeigen die häufigsten Fehlercodes und mögliche Maßnahmen zur Entschärfung.

Wenn Sie Probleme in Bezug auf Ihren Authentifizierungsfluss feststellen:

| Fehlercode | BESCHREIBUNG | Mögliche Entschärfung |
| - | - | - |
| 400 | Der Anforderungstext ist nicht gültig. Er kann z. B. nicht analysiert oder das Objekt nicht überprüft werden. | Stellen Sie sicher, dass Sie die richtigen Anforderungen im Rahmen des Nachweisflusses senden, oder verwenden Sie ein Geräte-SDK. |
| 401 | Das Autorisierungstoken kann nicht überprüft werden. Es ist z. B. abgelaufen oder gilt nicht für die URI der Anforderung. Dieser Fehlercode wird auch im Rahmen des TPM-Nachweisvorgangs an Geräte zurückgegeben. | Stellen Sie sicher, dass Ihr Gerät über die richtigen Anmeldeinformationen verfügt. |
| 404 | Die Device Provisioning Service-Instanz oder eine Ressource (z. B. eine Registrierung) ist nicht vorhanden. | [Beantragen Sie ein Ticket beim Kundensupport](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | Gemäß RFC7232 entspricht das `ETag` in der Anforderung nicht dem `ETag` der vorhandenen Ressource. | [Beantragen Sie ein Ticket beim Kundensupport](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Vorgänge werden vom Dienst gedrosselt. Informationen zu bestimmten Dienstgrenzwerten finden Sie unter [Grenzwerte für den IoT Hub Device Provisioning-Dienst](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Verringern Sie die Nachrichtenhäufigkeit, teilen Sie die Verantwortlichkeiten auf mehrere Geräte auf. |
| 500 | Interner Fehler. | [Beantragen Sie ein Ticket beim Kundensupport](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um zu sehen, ob dieser Ihnen weiterhelfen kann. |

## <a name="payload-shape-issues"></a>Probleme mit der Form der Nutzdaten

Wenn Sie festgestellt haben, dass Ihr Gerät Daten an IoT Central sendet, besteht der nächste Schritt darin, sicherzustellen, dass Ihr Gerät Daten in einem gültigen Format sendet.

Es gibt zwei Hauptkategorien von häufigen Problemen, die dazu führen, dass Gerätedaten nicht in IoT Central angezeigt werden:

- Nicht übereinstimmende Gerätevorlage für Gerätedaten:
  - Nicht übereinstimmende Benennungen, z. B. Tippfehler oder Probleme bei der Groß-/Kleinschreibung.
  - Nicht modellierte Eigenschaften, bei denen das Schema nicht in der Gerätevorlage definiert ist.
  - Schemakonflikt, z. B. ein Typ, der in der Vorlage als `boolean` definiert ist, aber die Daten sind Zeichenfolgen.
  - Derselbe Telemetriename ist in mehreren Schnittstellen definiert, aber das Gerät ist nicht konform mit IoT Plug & Play.
- Die Datenform stellt ungültigen JSON-Code dar. Weitere Informationen finden Sie unter [Telemetrie-, Eigenschaften- und Befehlsnutzlasten](concepts-telemetry-properties-commands.md).

Um zu ermitteln, welchen Kategorien Ihr Problem zuzuordnen ist, führen Sie den für Ihr Szenario am besten geeigneten Befehl aus:

- Verwenden Sie den Vorschaubefehl, um die Telemetrie zu überprüfen

    ```cmd/bash
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Verwenden Sie zur Überprüfung von Eigenschaftsaktualisierungen den Vorschaubefehl

    ```cmd/bash
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Wenn Sie zum ersten Mal einen `validate`-Befehl ausführen, werden Sie möglicherweise aufgefordert, die `uamqp`-Bibliothek zu installieren.

Die folgende Ausgabe zeigt beispielhafte Fehler- und Warnmeldungen des validate-Befehls:

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Wenn Sie die Verwendung einer grafischen Benutzeroberfläche (GUI) vorziehen, verwenden Sie die Ansicht **Rohdaten** von IoT Central, um zu prüfen, ob etwas nicht modelliert wird. Die Ansicht **Rohdaten** erkennt nicht, ob das Gerät fehlerhaften JSON-Code sendet.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Screenshot der Ansicht „Rohdaten“":::

Wenn Sie das Problem erkannt haben, müssen Sie möglicherweise die Gerätefirmware aktualisieren oder eine neue Gerätevorlage erstellen, die bisher nicht modellierte Daten modelliert.

Wenn Sie sich dafür entschieden haben, eine neue Vorlage zu erstellen, die die Daten ordnungsgemäß modelliert, migrieren Sie Geräte von Ihrer alten Vorlage zu der neuen Vorlage. Weitere Informationen finden Sie unter [Verwalten von Geräten in Ihrer Azure IoT Central-Anwendung](howto-manage-devices.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Hilfe benötigen, können Sie sich über das [MSDN-Azure-Forum oder das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ können Sie ein [Azure-Supportticket](https://portal.azure.com/#create/Microsoft.Support) erstellen.

Weitere Informationen finden Sie unter [Support- und Hilfeoptionen für Azure IoT](../../iot-fundamentals/iot-support-help.md).
