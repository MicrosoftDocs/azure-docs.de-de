---
title: Grundlegendes zu den Kosten für Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Informationen zu den für Azure Security Center für IoT anfallenden Kosten und dazu, wie sie gesteuert werden können.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 40963e0d81ccff21cc625b799833b996547a13b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65198319"
---
# <a name="pricing-and-associated-costs"></a>Preise und anfallende Kosten

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird das Preismodell für Azure Security Center (ASC) für IoT erläutert. Außerdem werden alle anfallenden Kosten zusammenfasst, und es wird erläutert, wie diese verwaltet werden.

## <a name="pricing"></a>Preise

Das Preismodell von ASC für IoT besteht aus zwei Teilen. Die Abrechnung erfolgt, sobald ein IoT Hub in ASC für IoT [aktiviert](quickstart-onboard-iot-hub.md) wird:

- Kosten nach Gerät: integrierte Sicherheitsfunktionen basierend auf der Analyse von IoT Hub-Protokollen

- Kosten nach Meldung: erweiterte Sicherheitsfunktionen basierend auf Sicherheitsmeldungen von IoT Edge oder Blattgeräten

  >[!Note]
  > Für Sicherheitsmeldungen fällt außerdem die Kontingentnutzung in IoT Hub an.

Weitere Informationen finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Anfallende Kosten

Für ASC für IoT fallen zwei Arten von Kosten an, die nicht Teil der direkten Preise sind:

- Nutzung des IoT Hub-Kontingents

- Kosten für Log Analytics-Speicher

Sie können die anfallenden Kosten reduzieren, indem Sie bestimmte Funktionen deaktivieren und die entsprechenden Änderungen in den Einstellungen vornehmen.

So ändern Sie Ihre Einstellungen

1. Öffnen Sie IoT Hub.

2. Klicken Sie unter **Sicherheit** auf **Übersicht**.

3. Klicken Sie auf **Einstellungen**.

Die folgende Tabelle enthält eine Zusammenfassung der anfallenden Kosten und Auswirkungen der einzelnen Optionen.

|     | Verwendung | Comment |
| --- | --- | --- |
| **Nutzung des IoT Hub-Kontingents** |  |
| Auftrag zum [Exportieren von Geräten](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) (Export von Gerätezwillingen) | einmal täglich | Deaktivieren von _Twin metadata collection_ (Sammlung mit Gerätezwilling-Metadaten) |
| **Log Analytics-Speicher** |  |
| Geräteempfehlungen und -warnungen| Vom Dienst generierte Sicherheitsempfehlungen und -warnungen | Nicht optional |
| Sicherheitsrohdaten| Sicherheitsrohdaten von IoT-Geräten, erfasst von Sicherheits-Agents | Deaktivieren von _Store raw device security events_ (Sicherheitsrohereignisse für Geräte speichern) |

>[!Important]
> Die Deaktivierung hat schwerwiegende Auswirkungen auf die verfügbaren Sicherheitsfunktionen.
  
| Deaktivierung | Auswirkungen |
| --- | --- |
| _Twin metadata collection (Sammlung von Gerätezwillingsmetadaten)_ | [Benutzerdefinierte Benachrichtigungen](quickstart-create-custom-alerts.md) sind deaktiviert |
| | Empfehlungen zum IoT Edge-Manifest sind deaktiviert |
| | Empfehlungen und Warnungen basierend auf Geräteidentität sind deaktiviert |
| _Store raw device security events (Sicherheitsrohereignisse für Geräte speichern)_ | Details zu grundlegenden Empfehlungen zum Gerätebetriebssystem sind nicht verfügbar |
| | Details zu Untersuchungen von [Warnungen](concept-security-alerts.md) und [Empfehlungen](concept-recommendations.md) sind nicht verfügbar |


## <a name="see-also"></a>Weitere Informationen

- [Zugreifen auf Sicherheitsdaten](how-to-security-data-access.md)
- [Untersuchen eines Geräts](how-to-investigate-device.md)
- Verstehen und Erkunden von [Sicherheitsempfehlungen](concept-recommendations.md)
- Verstehen und Erkunden von [Sicherheitswarnungen](concept-security-alerts.md)
