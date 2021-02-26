---
title: Untersuchen eines verdächtigen Geräts
description: In dieser Anleitung wird beschrieben, wie Sie Defender für IoT verwenden, um mithilfe von Log Analytics ein verdächtiges IoT-Gerät zu untersuchen.
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
ms.openlocfilehash: a7b51138abe6d8e97f55ceae11d4cf13b9ebc136
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820601"
---
# <a name="investigate-a-suspicious-iot-device"></a>Untersuchen eines verdächtigen IoT-Geräts

Anhand von Warnungen des Defender für IoT-Diensts wird eindeutig angegeben, wenn bei IoT-Geräten der Verdacht besteht, dass sie an verdächtigen Aktivitäten beteiligt sind, oder wenn es Hinweise auf eine Kompromittierung eines Geräts gibt.

In diesem Leitfaden verwenden Sie die Vorschläge für die Untersuchung. Sie dienen Ihnen als Hilfe beim Ermitteln der potenziellen Risiken für Ihre Organisation, Auswählen von Gegenmaßnahmen und Festlegen der besten Vorgehensweise zur Verhinderung ähnlicher Angriffe in der Zukunft.

> [!div class="checklist"]
> * Suchen nach Ihren Gerätedaten
> * Untersuchen mit KQL-Abfragen

## <a name="how-can-i-access-my-data"></a>Wie kann ich auf meine Daten zugreifen?

Standardmäßig werden Ihre Sicherheitswarnungen und Empfehlungen von Defender für IoT in Ihrem Log Analytics-Arbeitsbereich gespeichert. Sie können auch angeben, dass Ihre Sicherheitsdaten im Rohformat gespeichert werden sollen.

So suchen Sie Ihren Log Analytics-Arbeitsbereich für die Datenspeicherung

1. Öffnen Sie Ihren IoT-Hub.
1. Wählen Sie unter **Sicherheit** die Option **Einstellungen** und dann **Datensammlung** aus.
1. Ändern Sie die Konfigurationsdetails für Ihren Log Analytics-Arbeitsbereich.
1. Klicken Sie auf **Speichern**.

Gehen Sie nach der Konfiguration wie folgt vor, um auf Daten zuzugreifen, die in Ihrem Log Analytics-Arbeitsbereich gespeichert sind:

1. Wählen Sie in Ihrem IoT-Hub eine Defender für IoT-Warnung aus, und klicken Sie darauf.
1. Klicken Sie auf **Further investigation** (Weitere Untersuchung).
1. Wählen Sie **To see which devices have this alert click here and view the DeviceId column** (Klicken Sie hier, und zeigen Sie die Spalte „DeviceId“ an, um die Geräte mit dieser Warnung anzuzeigen).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Untersuchungsschritte für verdächtige IoT-Geräte

Wenn Sie Erkenntnisse und Rohdaten zu Ihren IoT-Geräten anzeigen möchten, navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich, um [auf Ihre Daten zuzugreifen](#how-can-i-access-my-data).

Informationen zu den ersten Schritten zum Untersuchen von Warnungen und Aktivitäten auf Ihrem Gerät finden Sie weiter unten in den KQL-Beispielabfragen.

### <a name="related-alerts"></a>Zugehörige Warnungen

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, ob im gleichen Zeitraum andere Warnungen ausgelöst wurden:

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>Zugriffsberechtigte Benutzer

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Benutzer Zugriff auf dieses Gerät haben:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
Verwenden Sie für die Ermittlung diese Daten:

- Welche Benutzer haben Zugriff auf das Gerät?
- Verfügen die zugriffsberechtigten Benutzer über die erwarteten Berechtigungsstufen?

### <a name="open-ports"></a>Öffnen von Ports

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Ports im Gerät derzeit verwendet werden oder verwendet wurden:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

Verwenden Sie für die Ermittlung diese Daten:

- Welche lauschenden Sockets sind auf dem Gerät derzeit aktiv?
- Sollten die derzeit aktiven lauschenden Sockets zugelassen werden?
- Sind irgendwelche verdächtigen Remoteadressen mit dem Gerät verbunden?

### <a name="user-logins"></a>Benutzeranmeldungen

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Benutzer sich am Gerät angemeldet haben:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

Nutzen Sie die Abfrageergebnisse, um Folgendes zu ermitteln:

- Welche Benutzer haben sich am Gerät angemeldet?
- Dürfen sich die angemeldeten Benutzer tatsächlich anmelden?
- Haben Benutzer, die sich angemeldet haben, die Verbindung über erwartete oder unerwartete IP-Adressen hergestellt?

### <a name="process-list"></a>Prozessliste

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, ob die Prozessliste den Erwartungen entspricht:

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

Nutzen Sie die Abfrageergebnisse, um Folgendes zu ermitteln:

- Wurden auf dem Gerät verdächtige Prozesse ausgeführt?
- Wurden Prozesse von entsprechenden Benutzern ausgeführt?
- Haben Ausführungen über die Befehlszeile die richtigen und erwarteten Argumente enthalten?

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie ein Gerät untersucht und ein besseres Verständnis der Risiken entwickelt haben, können Sie das [Konfigurieren von benutzerdefinierten Warnungen](quickstart-create-custom-alerts.md) erwägen, um den Sicherheitsstatus Ihrer IoT-Lösung zu verbessern. Falls Sie nicht bereits über einen Geräte-Agent verfügen, können Sie [einen Sicherheits-Agent bereitstellen](how-to-deploy-agent.md) oder [die Konfiguration eines vorhandenen Geräte-Agents ändern](how-to-agent-configuration.md), um Ihre Ergebnisse zu verbessern.
