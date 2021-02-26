---
title: Ereignisaggregation (Vorschau)
titleSuffix: Azure Defender for IoT
description: Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 9ce24a44e48f090a0dc7d355952b3cf50dd4318c
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809642"
---
# <a name="event-aggregation-preview"></a>Ereignisaggregation (Vorschau)

Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden die Daten zur Verarbeitung und Analyse an die Azure-Cloud. Der Defender für IoT-Micro-Agent sammelt viele Arten von Geräteereignissen, einschließlich neuer Prozess- und Verbindungsereignisse. Die neuen Prozess- und Verbindungsereignisse können auf einem Gerät innerhalb einer Sekunde vielfach auftreten. Diese Fähigkeit ist für eine umfassende Sicherheit wichtig. Die Anzahl der von den Sicherheits-Agents gesendeten Nachrichten kann jedoch schnell Ihre IoT Hub-Kontingente und Kostenlimits erreichen oder überschreiten. Diese Ereignisse enthalten allerdings äußerst wertvolle Sicherheitsinformationen, die für den Schutz Ihres Geräts von entscheidender Bedeutung sind. 

Um das zusätzliche Kontingent und die Kosten zu reduzieren und Ihre Geräte gleichzeitig geschützt zu halten, aggregieren Defender für IoT-Agents diese Ereignistypen: 

- ProcessCreate (nur Linux) 

- ConnectionCreate (nur Azure RTOS) 

## <a name="how-does-event-aggregation-work"></a>Wie funktioniert die Ereignisaggregation? 

Defender für IoT-Agents aggregieren Ereignisse für den Intervallzeitraum oder das Zeitfenster. Nachdem der Intervallzeitraum abgelaufen ist, sendet der Agent die aggregierten Ereignisse zur weiteren Analyse an die Azure-Cloud. Die aggregierten Ereignisse werden im Arbeitsspeicher gespeichert, bis sie an die Azure-Cloud gesendet werden. 

Um den Speicherbedarf des Agents zu verringern, erhöht der Agent immer dann die Trefferanzahl dieses spezifischen Ereignisses, wenn er ein identisches Ereignis zu einem Ereignis erfasst, das im Arbeitsspeicher bereits enthalten ist. Wenn das Aggregationszeitfenster überschritten wird, sendet der Agent die Trefferanzahl für jeden Ereignistyp, der aufgetreten ist. Die Ereignisaggregation ist einfach die Aggregation der Trefferanzahl für die einzelnen erfassten Ereignistypen. 

## <a name="process-events"></a>Verarbeiten von Ereignissen 

Prozessereignisse werden derzeit nicht unter Linux-Betriebssystemen unterstützt. 

Prozessereignisse werden als identisch angesehen, wenn die *Befehlszeile* und  *userid* identisch sind. 

Der Standardpuffer für Prozessereignisse umfasst 32 Prozesse. Danach wird der Puffer durchlaufen, und die ältesten Prozessereignisse werden verworfen, um Platz für neue Prozessereignisse zu schaffen.  

## <a name="network-connection-events"></a>Netzwerkverbindungsereignisse 

Netzwerkverbindungsereignisse werden derzeit nur in Azure RTOS unterstützt. 

Netzwerkverbindungsereignisse werden als identisch angesehen, wenn der *lokale Port*, der  *Remoteport*, das  *Transportprotokoll*, die  *lokale Adresse* und die  *Remoteadresse* identisch sind. 

Der Standardpuffer für Netzwerkverbindungsereignisse beträgt 64. Danach werden bis zum nächsten Sammlungszyklus keine neuen Netzwerkereignisse mehr zwischengespeichert. Es wird eine Warnung zur Erhöhung der Cachegröße protokolliert.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Defender für IoT-Sicherheitswarnungen](concept-security-alerts.md).
