---
title: Azure Security Center für IoT-Leitfaden zur Untersuchung von Geräten (Vorschauversion) | Microsoft-Dokumentation
description: In dieser Anleitung wird beschrieben, wie Sie Azure Security Center für IoT verwenden, um mithilfe von Log Analytics ein verdächtiges IoT-Gerät zu untersuchen.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b18b48ae-b445-48f8-9ac0-365d6e065b64
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/18/2019
ms.author: mlottner
ms.openlocfilehash: 15e65c155a98ae12c156587735d34a16ed2c9109
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65192656"
---
# <a name="investigate-a-suspicious-iot-device"></a>Untersuchen eines verdächtigen IoT-Geräts

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Anhand von Warnungen und Beweisen des Azure Security Center (ASC) für IoT-Diensts wird eindeutig angegeben, wenn bei IoT-Geräten der Verdacht besteht, dass sie an verdächtigen Aktivitäten beteiligt sind, oder es Hinweise auf eine Kompromittierung eines Geräts gibt. 

In diesem Leitfaden verwenden Sie die Vorschläge für die Untersuchung. Sie dienen Ihnen als Hilfe beim Ermitteln der potenziellen Risiken für Ihre Organisation, Auswählen von Gegenmaßnahmen und Festlegen der besten Vorgehensweise zur Verhinderung ähnlicher Angriffe in der Zukunft.  

> [!div class="checklist"]
> * Suchen nach Ihren Gerätedaten
> * Untersuchen mit KQL-Abfragen


## <a name="how-can-i-access-my-data"></a>Wie kann ich auf meine Daten zugreifen?

Standardmäßig werden Ihre Sicherheitswarnungen und Empfehlungen von ASC für IoT in Ihrem Log Analytics-Arbeitsbereich gespeichert. Sie können auch angeben, dass Ihre Sicherheitsdaten im Rohformat gespeichert werden sollen.

Ermitteln Sie Ihren Log Analytics-Arbeitsbereich für die Datenspeicherung wie folgt:

1. Öffnen Sie Ihren IoT-Hub. 
1. Klicken Sie unter **Sicherheit** auf **Übersicht** und anschließend auf **Einstellungen**.
1. Ändern Sie die Konfigurationsdetails für Ihren Log Analytics-Arbeitsbereich. 
1. Klicken Sie auf **Speichern**. 

Gehen Sie nach der Konfiguration wie folgt vor, um auf Daten zuzugreifen, die in Ihrem Log Analytics-Arbeitsbereich gespeichert sind:

1. Wählen Sie in Ihrem IoT-Hub eine ASC für IoT-Warnung aus, und klicken Sie darauf. 
1. Klicken Sie auf **Further investigation** (Weitere Untersuchung). 
1. Wählen Sie **To see which devices have this alert click here and view the DeviceId column** (Klicken Sie hier, und zeigen Sie die Spalte „DeviceId“ an, um die Geräte mit dieser Warnung anzuzeigen).

## <a name="investigation-steps-for-suspicious-iot-devices"></a>Untersuchungsschritte für verdächtige IoT-Geräte

Wenn Sie auf Erkenntnisse und Rohdaten zu Ihren IoT-Geräten zugreifen möchten, navigieren Sie zu Ihrem Log Analytics-Arbeitsbereich, um dort [auf Ihre Daten zuzugreifen](#how-can-i-access-my-data).

Überprüfen und untersuchen Sie die Gerätedaten auf die folgenden Details und Aktivitäten, indem Sie die hier angegebenen KQL-Abfragen verwenden:

### <a name="related-alerts"></a>Zugehörige Warnungen

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, ob im gleichen Zeitraum andere Warnungen ausgelöst wurden:

  ~~~
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ~~~

### <a name="users-with-access"></a>Zugriffsberechtigte Benutzer

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Benutzer Zugriff auf dieses Gerät haben: 

  ~~~
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
  ~~~
Verwenden Sie für die Ermittlung diese Daten: 
  1. Welche Benutzer haben Zugriff auf das Gerät?
  2. Verfügen die zugriffsberechtigten Benutzer über die richtigen Berechtigungsstufen? 

### <a name="open-ports"></a>Öffnen von Ports

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Ports im Gerät derzeit verwendet werden oder verwendet wurden: 

  ~~~
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
  ~~~

    Use this data to discover:
  1. Welche lauschenden Sockets sind auf dem Gerät derzeit aktiv?
  2. Sollten die derzeit aktiven lauschenden Sockets zugelassen werden?
  3. Sind irgendwelche verdächtigen Remoteadressen mit dem Gerät verbunden?

### <a name="user-logins"></a>Benutzeranmeldungen

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, welche Benutzer sich am Gerät angemeldet haben: 
 
  ~~~
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
  ~~~

    Use the query results to discover:
  1. Welche Benutzer haben sich am Gerät angemeldet?
  2. Dürfen sich die angemeldeten Benutzer tatsächlich anmelden?
  3. Haben Benutzer, die sich angemeldet haben, die Verbindung über erwartete oder unerwartete IP-Adressen hergestellt?
  
### <a name="process-list"></a>Prozessliste

Verwenden Sie die folgende KQL-Abfrage, um zu ermitteln, ob die Prozessliste den Erwartungen entspricht: 

  ~~~
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
  ~~~

    Use the query results to discover:

  1. Wurden auf dem Gerät verdächtige Prozesse ausgeführt?
  2. Wurden Prozesse von entsprechenden Benutzern ausgeführt?
  3. Haben Ausführungen über die Befehlszeile die richtigen und erwarteten Argumente enthalten?

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie ein Gerät untersucht und ein besseres Verständnis der Risiken entwickelt haben, können Sie das [Konfigurieren von benutzerdefinierten Warnungen](quickstart-create-custom-alerts.md) erwägen, um den Sicherheitsstatus Ihrer IoT-Lösung zu verbessern. Falls Sie nicht bereits über einen Geräte-Agent verfügen, können Sie [einen Sicherheits-Agent bereitstellen](how-to-deploy-agent.md) oder [die Konfiguration eines vorhandenen Geräte-Agents ändern](how-to-agent-configuration.md), um Ihre Ergebnisse zu verbessern. 