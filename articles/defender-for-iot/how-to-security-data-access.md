---
title: Zugreifen auf Sicherheits- und Empfehlungsdaten
description: Erfahren Sie, wie Sie bei Verwendung von Defender für IoT auf Ihre Sicherheitswarnungen und Empfehlungsdaten zugreifen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: c4f65da4f9a4f7ade94d76e99d57439e62b3c808
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092056"
---
# <a name="access-your-security-data"></a>Zugreifen auf Sicherheitsdaten

Defender für IoT speichert Sicherheitswarnungen, Empfehlungen und Sicherheitsrohdaten (falls Sie diese speichern möchten) in Ihrem Log Analytics-Arbeitsbereich.

## <a name="log-analytics"></a>Log Analytics

So konfigurieren Sie, wie der Log Analytics-Arbeitsbereich verwendet wird:

1. Öffnen Sie Ihren IoT Hub.
1. Klicken Sie im Abschnitt **Sicherheit** auf das Blatt **Einstellungen**.
1. Klicken Sie auf **Datensammlung**, und ändern Sie die Konfiguration für Ihren Log Analytics-Arbeitsbereich.

So greifen Sie nach der Konfiguration auf Ihre Warnungen und Empfehlungen in Ihrem Log Analytics-Arbeitsbereich zu:

1. Wählen Sie eine Warnung oder Empfehlung in Defender für IoT aus.
1. Klicken Sie auf **Weitere Untersuchungen**, und dann auf **Klicken Sie hier, und zeigen Sie die Spalte „DeviceId“ an, um die Geräte mit dieser Warnung anzuzeigen**.

Ausführliche Informationen zum Abfragen von Daten aus Log Analytics finden Sie unter [Erste Schritte mit Abfragen in Log Analytics](/azure/azure-monitor/log-query/get-started-queries).

## <a name="security-alerts"></a>Sicherheitswarnungen

Sicherheitswarnungen werden in der Tabelle _AzureSecurityOfThings.SecurityAlert_ im Log Analytics-Arbeitsbereich gespeichert, der für die Defender für IoT-Lösung konfiguriert wurde.

Wir haben eine Reihe nützlicher Fragen bereitgestellt, die Ihnen den Einstieg in die Untersuchung von Sicherheitswarnungen erleichtern.

### <a name="sample-records"></a>Musterdatensätze

Auswählen einiger zufälliger Datensätze

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | BESCHREIBUNG                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18:10:29.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | Erfolgreicher Brute-Force-Angriff           | Ein Brute-Force-Angriff auf dem Gerät war erfolgreich        |    { "Vollständige Quelladresse": "[\"10.165.12.18:\"]", "Benutzernamen": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | Erfolgreiche lokale Anmeldung auf dem Gerät      | Eine erfolgreiche lokale Anmeldung auf dem Gerät wurde erkannt     | { "Remoteadresse": "?", "Remoteport": "", "Lokaler Port": "", "Anmeldeshell": "/bin/su", "Anmeldeprozess-ID": "28207", "Benutzername": "Angreifer", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | Ein fehlgeschlagener Anmeldeversuch auf dem Gerät wurde erkannt |    { "Remoteadresse": "?", "Remoteport": "", "Lokaler Port": "", "Anmeldeshell": "/bin/su", "Anmeldeprozess-ID": "22644", "Benutzername": "Angreifer", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Gerätezusammenfassung

Rufen Sie die Reihe von unterschiedlichen Sicherheitswarnungen ab, die in der letzten Woche erkannt wurden – gruppiert nach IoT Hub, Gerät, Schweregrad der Warnung und Warnungstyp.

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Anzahl |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | Erfolgreicher Brute-Force-Angriff           | 9   |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium        | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | 242 |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | Erfolgreiche lokale Anmeldung auf dem Gerät      | 31  |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium        | Crypto Coin Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT Hub-Zusammenfassung

Wählen Sie Reihe von unterschiedlichen Geräten, für die in der letzten Woche Warnungen aufgetreten sind, nach IoT Hub, Schweregrad der Warnung, Warnungstyp aus.

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | High          | Erfolgreicher Brute-Force-Angriff           | 1          |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Medium        | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | 1          |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | High          | Erfolgreiche lokale Anmeldung auf dem Gerät      | 1          |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Medium        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Sicherheitsempfehlungen

Sicherheitsempfehlungen werden in der Tabelle _AzureSecurityOfThings.SecurityRecommendation_ im Log Analytics-Arbeitsbereich gespeichert, der für die Defender für IoT-Lösung konfiguriert wurde.

Wir haben eine Reihe nützlicher Fragen bereitgestellt, die Ihnen den Einstieg in die Untersuchung von Sicherheitsempfehlungen erleichtern.

### <a name="sample-records"></a>Musterdatensätze

Auswählen einiger zufälliger Datensätze

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | deviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | BESCHREIBUNG | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium | Aktiv | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium | Aktiv | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Gerätezusammenfassung

Rufen Sie die Reihe von unterschiedlichen aktiven Sicherheitsempfehlungen ab – gruppiert nach IoT Hub, Gerät, Empfehlungsschweregrad und Typ.

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Anzahl |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | 2   |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium        | 1 |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | High          | 1  |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Medium        | 4   |

## <a name="next-steps"></a>Nächste Schritte

- Lesen der [Übersicht](overview.md) über Defender für IoT
- Informationen zur Defender für IoT-[Architektur](architecture.md)
- Grundlegende und weiterführende Informationen zu [für IoT-Warnungen](concept-security-alerts.md)
- Grundlegende und weiterführende Informationen zu [für IoT-Empfehlungen](concept-recommendations.md)