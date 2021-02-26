---
title: Untersuchen der CIS-Vergleichstest-Empfehlung
titleSuffix: Azure Defender for IoT
description: Ausführen grundlegender und erweiterter Untersuchungen basierend auf Empfehlungen zur Betriebssystembaseline
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 2f68ebedb229f7295bc9c5dcc3b3349808970e8c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809603"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Untersuchen der Empfehlung zur Betriebssystembaseline (basierend auf CIS-Benchmark) 

Ausführen grundlegender und erweiterter Untersuchungen basierend auf Empfehlungen zur Betriebssystembaseline

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Untersuchen grundlegender Sicherheitsempfehlungen zur Betriebssystembaseline  

Sie können die Empfehlungen zur Betriebssystembaseline im Azure Defender für IoT-Portal unter **IoT Hub** untersuchen. Weitere Informationen finden Sie unter [Untersuchen von Sicherheitsempfehlungen](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Untersuchen erweiterter Sicherheitsempfehlungen zur Betriebssystembaseline  

In diesem Abschnitt wird beschrieben, wie Sie die Testergebnisse zur Betriebssystembaseline besser verstehen und Ereignisse in Azure Log Analytics abfragen.  

Die Untersuchung der erweiterten Sicherheitsempfehlungen zur Betriebssystembaseline wird nur mithilfe von Log Analytics unterstützt. Verbinden Sie Defender für IoT mit einem Log Analytics-Arbeitsbereich, bevor Sie fortfahren. Weitere Informationen zu den erweiterten Sicherheitsempfehlungen zur Betriebssystembaseline finden Sie unter [Konfigurieren einer Agent-basierten Lösung für Azure Defender für IoT](how-to-configure-agent-based-solution.md).

So fragen Sie IoT-Sicherheitsereignisse in Log Analytics für Warnungen ab

1. Navigieren Sie zur Seite **Warnungen**.

1. Wählen Sie **Investigate recommendations in Log Analytics workspace** (Empfehlungen im Log Analytics-Arbeitsbereich untersuchen) aus.

So fragen Sie IoT-Sicherheitsereignisse in Log Analytics für Empfehlungen ab

1. Navigieren Sie zur Seite **Empfehlungen**.

1. Wählen Sie **Investigate recommendations in Log Analytics workspace** (Empfehlungen im Log Analytics-Arbeitsbereich untersuchen) aus.

1. Wählen Sie auf der Schnellansichtsseite **Details zur Empfehlung** den Link **Show Operation system (OS) baseline rules details** (Details zu Regeln für die Betriebssystembaseline anzeigen) aus, um Details zu einem bestimmten Gerät anzuzeigen.

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Anzeigen der Details zu einem bestimmten Gerät"::: 

So fragen Sie IoT-Sicherheitsereignisse direkt im Log Analytics-Arbeitsbereich ab

1. Navigieren Sie zur Seite **Protokolle**.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Auswählen der Protokolle im linken Bereich":::

1. Wählen Sie **Warnungen untersuchen** oder die Option **Investigate the alerts in Log Analytics** (Warnungen in Log Analytics untersuchen) für eine Sicherheitsempfehlung oder Warnung aus.   

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Hilfreiche Abfragen zum Untersuchen der Ressourcen der Betriebssystembaseline 

> [!Note]
> Ersetzen Sie in den folgenden Abfragen `<device-id>` durch die Namen, die Sie Ihrem Gerät gegeben haben. 


### <a name="retrieve-the-latest-information"></a>Abrufen der aktuellsten Informationen

- **Fehler der Geräteflotte:** Führen Sie die folgende Abfrage aus, um die neuesten Informationen zu fehlerhaften Überprüfungen abzurufen, die in der Geräteflotte aufgetreten sind: 

    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Gerätespezifische Ausfälle:** Führen Sie die folgende Abfrage aus, um die neuesten Informationen zu fehlerhaften Überprüfungen bei einem bestimmten Gerät abzurufen:  

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    project DeviceId, event.CceId, event.Description 
    ```

- **Gerätespezifische Fehler:** Führen Sie die folgende Abfrage aus, um die neuesten Informationen zu Überprüfungen abzurufen, bei denen ein Fehler bei einem bestimmten Gerät erkannt wurde: 

    ```azurecli
    let LastEvents = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    where DeviceId == "<device-id>" | 
    
    top 1 by TimeStamp desc | 
    
    project IoTRawEventId; 
    
    LastEvents | join kind=leftouter SecurityIoTRawEvent on IoTRawEventId | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "ERROR" | 
    
    project DeviceId, event.CceId, event.Description 
    ```
 
- **Aktualisieren der Geräteliste für die Geräteflotte mit einer bestimmten fehlerhaften Überprüfung:** Führen Sie diese Abfrage aus, um eine aktualisierte Liste der Geräte (in der Geräteflotte) abzurufen, bei denen eine bestimmte Überprüfung fehlerhaft war:  
 
    ```azurecli
    let lastDates = SecurityIoTRawEvent | 
    
    where RawEventName == "OSBaseline" | 
    
    summarize TimeStamp=max(TimeStamp) by DeviceId; 
    
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId  | 
    
    extend event = parse_json(EventDetails) | 
    
    where event.Result == "FAIL" | 
    
    where event.CceId contains "6.2.8" | 
    
    project DeviceId; 
    ```
 
## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sicherheitsempfehlungen](quickstart-investigate-security-recommendations.md)
 