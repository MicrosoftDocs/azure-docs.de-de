---
title: Konfigurieren eines Azure Security Center für IoT-Agents (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Agents für die Verwendung mit Azure Security Center für IoT konfigurieren.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 311a867c863cbd72292d8e34b4f7abdb949b12c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65198441"
---
# <a name="tutorial-configure-security-agents"></a>Tutorial: Konfigurieren von Sicherheits-Agents

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird Azure Security Center (ASC) für den IoT-Sicherheit-Agent erläutert, wie Sie ihn ändern und wie ASC für den IoT-Sicherheits-Agent konfiguriert wird.

> [!div class="checklist"]
> * Konfigurieren von Sicherheits-Agents
> * Ändern des Agent-Verhaltens durch Bearbeiten von Zwillingseigenschaften
> * Erkennen der Standardkonfiguration

## <a name="agents"></a>Agents

ASC für IoT-Sicherheits-Agents sammeln Daten aus IoT-Geräten und führen Sicherheitsaktionen aus, um die erkannten Sicherheitsrisiken zu verringern. Die Konfiguration von Sicherheits-Agents erfolgt durch die Anpassung verschiedener Modulzwillingeigenschaften. Im Allgemeinen kommen sekundäre Updates an diesen Eigenschaften nur selten vor.  

Bei dem Objekt für die Zwillingskonfiguration des ASC für IoT-Sicherheits-Agents handelt es sich um ein Objekt im JSON-Format. Das Konfigurationsobjekt besteht aus mehreren steuerbaren Eigenschaften, über deren Definition Sie das Verhalten des Agents steuern können. 

Mithilfe dieser Konfigurationen können Sie den Agent für jedes erforderliche Szenario anpassen. Sie können zum Beispiel einige Ereignisse automatisch ausschließen oder den Stromverbrauch auf ein Minimum reduzieren, indem Sie diese Eigenschaften konfigurieren.  

Verwenden Sie das [Konfigurationsschema](https://aka.ms/iot-security-github-module-schema) des ASC für IoT-Sicherheits-Agents, um Änderungen vorzunehmen.  

## <a name="configuration-objects"></a>Konfigurationsobjekte 

Jede mit einem ASC für IoT-Sicherheits-Agent verknüpfte Eigenschaft befindet sich in einem Agent-Konfigurationsobjekt (innerhalb des Abschnitts der gewünschten Eigenschaften) des **azureiotsecurity**-Moduls. 

Wenn Sie die Konfiguration ändern möchten, erstellen und ändern Sie dieses Objekt in der Identität des **azureiotsecurity**-Modulzwillings. 

Ist das Agent-Konfigurationsobjekt im **azureiotsecurity**-Modulzwilling nicht vorhanden, werden alle Eigenschaftswerte des Sicherheits-Agents auf die Standardeinstellungen gesetzt. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Stellen Sie sicher, dass Sie Ihre Agent-Konfigurationsänderungen anhand dieses [Schemas](https://aka.ms/iot-security-github-module-schema) überprüfen.
Wenn das Konfigurationsobjekt nicht dem Schema entspricht, kann der Agent nicht gestartet werden.

## <a name="configuration-schema-and-validation"></a>Konfigurationsschema und Validierung 

Stellen Sie sicher, dass Sie Ihre Agent-Konfiguration anhand dieses [Schemas](https://aka.ms/iot-security-github-module-schema) validieren. Wenn das Konfigurationsobjekt nicht dem Schema entspricht, kann der Agent nicht gestartet werden.

 
Wenn das Konfigurationsobjekt während der Ausführung des Agents in eine ungültige Konfiguration geändert wird (die Konfiguration stimmt nicht mit dem Schema überein), ignoriert der Agent die ungültige Konfiguration und verwendet weiterhin die aktuelle Konfiguration. 

## <a name="editing-a-property"></a>Bearbeiten einer Eigenschaft 

Alle benutzerdefinierten Eigenschaften müssen innerhalb des Agent-Konfigurationsobjekts im **azureiotsecurity**-Modulzwilling festgelegt werden.
Um einen Standardeigenschaftswert zu verwenden, entfernen Sie die Eigenschaft aus dem Konfigurationsobjekt.

### <a name="setting-a-property"></a>Festlegen einer Eigenschaft

1. Suchen und wählen Sie in Ihrem IoT Hub das Gerät aus, das Sie ändern möchten.

1. Klicken Sie auf Ihr Gerät und dann auf das Modul **azureiotsecurity**.

1. Klicken Sie auf **Modulidentitätszwilling**.

1. Bearbeiten Sie die gewünschten Eigenschaften des Sicherheitsmoduls.
   
   Verwenden Sie z. B. die folgende Konfiguration, um Verbindungsereignisse mit hoher Priorität zu konfigurieren und Ereignisse mit hoher Priorität alle 7 Minuten zu sammeln.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Klicken Sie auf **Speichern**.

### <a name="using-a-default-value"></a>Verwendung eines Standardwerts

Um einen Standardeigenschaftswert zu verwenden, entfernen Sie die Eigenschaft aus dem Konfigurationsobjekt.

## <a name="default-properties"></a>Standardeigenschaften 

Die folgende Tabelle enthält die steuerbaren Eigenschaften von ASC für IoT-Sicherheits-Agents.

Standardwerte sind im entsprechenden Schema auf [GitHub](https://aka.ms/iot-security-module-default) verfügbar.

| NAME| Status | Gültige Werte| Standardwerte| BESCHREIBUNG |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Erforderlich: „false“ |Gültige Werte:  Dauer im ISO 8601-Format |Standardwert: PT7M |Maximale Zeit, bevor Nachrichten mit hoher Priorität gesendet werden.|
|lowPriorityMessageFrequency |Erforderlich: „false“|Gültige Werte:  Dauer im ISO 8601-Format |Standardwert: PT5H |Maximale Zeit, bevor Nachrichten mit niedriger Priorität gesendet werden.| 
|snapshotFrequency |Erforderlich: „false“|Gültige Werte: Dauer im ISO 8601-Format |Standardwert PT13H |Zeitintervall für die Erstellung von Momentaufnahmen des Gerätstatus.| 
|maxLocalCacheSizeInBytes |Erforderlich: „false“ |Gültige Werte: |Standardwert: 2560000, größer als 8192 | Maximaler, für den Nachrichtencache eines Agents zulässiger Speicher (in Byte). Maximale Größe des zum Speichern von Nachrichten auf dem Gerät verwendeten Speicherplatzes, bevor Nachrichten gesendet werden.| 
|maxMessageSizeInBytes |Erforderlich: „false“ |Gültige Werte:  Eine positive Zahl, größer als 8192 und kleiner als 262144 |Standardwert: 204800 |Maximal zulässige Größe einer Agent-an-Cloud-Nachricht. Diese Einstellung steuert die maximal gesendete Datenmenge in den einzelnen Nachrichten. |
|eventPriority${EventName} |Erforderlich: „false“ |Gültige Werte:  Hoch, Niedrig, Aus |Standardwerte: |Priorität jedes von einem Agent generierten Ereignisses | 

### <a name="supported-security-events"></a>Unterstützte Sicherheitsereignisse

|Ereignisname| PropertyName | Standardwert| Momentaufnahmenereignis| Detailstatus  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnoseereignis|eventPriorityDiagnostic| Aus| False| Diagnoseereignisse im Zusammenhang mit dem Agent. Verwenden Sie dieses Ereignis für die ausführliche Protokollierung.| 
|Konfigurationsfehler |eventPriorityConfigurationError |Niedrig |False |Der Agent konnte die Konfiguration nicht analysieren. Überprüfen Sie die Übereinstimmung der Konfiguration mit dem Schema.| 
|Statistik der gelöschten Ereignisse |eventPriorityDroppedEventsStatistics |Niedrig |True|Statistiken zu Ereignissen im Zusammenhang mit dem Agent. |
|Nachrichtenstatistiken|eventPriorityMessageStatistics |Niedrig |True |Statistiken zu Ereignissen im Zusammenhang mit der Nachricht. |
|Angeschlossene Hardware|eventPriorityConnectedHardware |Niedrig |True |Momentaufnahme der gesamten mit dem Gerät verbundenen Hardware.|
|Überwachungsports|eventPriorityListeningPorts |Hoch |True |Momentaufnahme aller offenen Überwachungsports auf dem Gerät.|
|Prozesserstellung |eventPriorityProcessCreate |Niedrig |False |Überwachung der Prozesserstellung auf dem Gerät.|
|Prozessbeendigung|eventPriorityProcessTerminate |Niedrig |False |Überwachung der Prozessbeendigung auf dem Gerät.| 
|Systeminformationen |eventPrioritySystemInformation |Niedrig |True |Momentaufnahme von Systeminformationen (Beispiel: Betriebssystem oder CPU).| 
|Lokale Benutzer| eventPriorityLocalUsers |Hoch |True|Momentaufnahme der registrierten lokalen Benutzer innerhalb des Systems. |
|Anmeldung|  eventPriorityLogin |Hoch|False|Überwachung der Anmeldeereignisse beim Gerät (lokale und Remoteanmeldungen).|
|Verbindungserstellung |eventPriorityConnectionCreate|Niedrig|False|Überwachung der zum und vom Gerät hergestellten TCP-Verbindungen. |
|Firewall-Konfiguration| eventPriorityFirewallConfiguration|Niedrig|True|Momentaufnahme der Gerätefirewallkonfiguration (Firewallregeln). |
|Betriebssystembaseline| eventPriorityOSBaseline| Niedrig|True|Momentaufnahme der Überprüfung der Betriebssystembaseline des Geräts.|
 

## <a name="next-steps"></a>Nächste Schritte

- [ASC für IoT-Empfehlungen](concept-recommendations.md)
- [Informationen zu ASC für IoT-Benachrichtigungen](concept-security-alerts.md)
- [Access raw security data (Zugreifen auf Sicherheitsrohdaten)](how-to-security-data-access.md)
