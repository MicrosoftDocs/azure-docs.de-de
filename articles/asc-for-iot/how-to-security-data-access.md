---
title: Zugreifen auf Daten mit der Vorschauversion von Azure Security Center für IoT | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie bei Verwendung von Azure Security Center für IoT auf Ihre Sicherheitswarnungen und Empfehlungsdaten zugreifen.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 1ec6a174d05f8707bbffcc9fb013a98c2eb9196c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200546"
---
# <a name="access-your-security-data"></a>Zugreifen auf Sicherheitsdaten 

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Security Center (ASC) für IoT speichert Sicherheitswarnungen, Empfehlungen und Sicherheitsrohdaten (falls Sie diese speichern möchten) in Ihrem Log Analytics-Arbeitsbereich.

## <a name="log-analytics"></a>Log Analytics

So konfigurieren Sie, wie der Log Analytics-Arbeitsbereich verwendet wird:

1. Öffnen Sie Ihren IoT Hub.
1. Klicken Sie auf **Sicherheit**.
2. Klicken Sie auf **Einstellungen**, und ändern Sie die Konfiguration für Ihren Log Analytics-Arbeitsbereich.

So greifen Sie nach der Konfiguration auf Ihren Log Analytics-Arbeitsbereich zu:

1. Wählen Sie eine Warnung oder Empfehlung in ASC für IoT aus. 
2. Klicken Sie auf **Weitere Untersuchungen**, und dann auf **Klicken Sie hier, und zeigen Sie die Spalte „DeviceId“ an, um die Geräte mit dieser Warnung anzuzeigen**.

Ausführliche Informationen zum Abfragen von Daten aus Log Analytics finden Sie unter [Erste Schritte mit Abfragen in Log Analytics](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries).

## <a name="security-alerts"></a>Sicherheitswarnungen

Sicherheitswarnungen werden in der Tabelle _AzureSecurityOfThings.SecurityAlert_ im Log Analytics-Arbeitsbereich gespeichert, der für die ASC für IoT-Lösung konfiguriert wurde.

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
| 2018-11-18T18:10:29.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | Erfolgreicher Brute-Force-Angriff           | Ein Brute-Force-Angriff auf dem Gerät war erfolgreich        |    { "Vollständige Quelladresse": "[\"10.165.12.18:\"]", "Benutzernamen": "[\"\"]", "DeviceId": "IoT-Device-Linux" }                                                                       |
| 2018-11-19T12:40:31.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | Erfolgreiche lokale Anmeldung auf dem Gerät      | Eine erfolgreiche lokale Anmeldung auf dem Gerät wurde erkannt     | { "Remoteadresse": "?", "Remoteport": "", "Lokaler Port": "", "Anmeldeshell": "/bin/su", "Anmeldeprozess-ID": "28207", "Benutzername": "Angreifer", "DeviceId": "IoT-Device-Linux" } |
| 2018-11-19T12:40:31.000 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | Ein fehlgeschlagener Anmeldeversuch auf dem Gerät wurde erkannt |  { "Remoteadresse": "?", "Remoteport": "", "Lokaler Port": "", "Anmeldeshell": "/bin/su", "Anmeldeprozess-ID": "22644", "Benutzername": "Angreifer", "DeviceId": "IoT-Device-Linux" } |

### <a name="device-summary"></a>Gerätezusammenfassung

Wählen Sie eine Reihe von unterschiedlichen in der letzten Woche erkannten Sicherheitswarnungen nach IoT Hub, Gerät, Schweregrad der Warnung, Warnungstyp aus.

```
// Select number of distinct security alerts detected last week by 
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

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | Erfolgreicher Brute-Force-Angriff           | 9   |   
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel        | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | 242 |    
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | Erfolgreiche lokale Anmeldung auf dem Gerät      | 31  |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel        | Crypto Coin Miner                     | 4   |

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
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Hoch          | Erfolgreicher Brute-Force-Angriff           | 1          |    
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Mittel        | Fehler bei lokalem Anmeldeversuch auf dem Gerät  | 1          | 
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Hoch          | Erfolgreiche lokale Anmeldung auf dem Gerät      | 1          |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | Mittel        | Crypto Coin Miner                     | 1          |

## <a name="security-recommendations"></a>Sicherheitsempfehlungen

Sicherheitsempfehlungen werden in der Tabelle _AzureSecurityOfThings.SecurityRecommendation_ im Log Analytics-Arbeitsbereich gespeichert, der für die ASC für IoT-Lösung konfiguriert wurde.

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
| 2019-03-22T10:21:06.060 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel | Aktiv | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |
| 2019-03-22T10:50:27.237 | /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel | Aktiv | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. | {"Rules":"[{\"SourceAddress\":\"\",\"SourcePort\":\"\",\"DestinationAddress\":\"\",\"DestinationPort\":\"1337\"}]"} |

### <a name="device-summary"></a>Gerätezusammenfassung

Wählen Sie die Anzahl der verschiedenen aktiven Sicherheitsempfehlungen nach IoT Hub, Gerät, Empfehlungsschweregrad und Typ aus.

```
// Select number of distinct active security recommendations by 
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | deviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | 2   |    
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel        | 1 |  
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Hoch          | 1  |
| /subscriptions/<Abonnement-ID>/resourceGroups/<Ressourcengruppe>/providers/Microsoft.Devices/IotHubs/<IoT_Hub> | <Gerätename> | Mittel        | 4   |


## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Übersicht](overview.md) über ASC für IoT
- Informieren Sie sich über die [Architektur](architecture.md) von ASC für IoT
- Grundlegende und weiterführende Informationen zu [ASC für IoT-Warnungen](concept-security-alerts.md)
- Grundlegende und weiterführende Informationen zu [ASC für IoT-Empfehlungen](concept-recommendations.md)