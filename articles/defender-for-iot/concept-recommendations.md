---
title: Sicherheitsempfehlungen
description: Lernen Sie das Konzept der Sicherheitsempfehlungen kennen, und erfahren Sie, wie sie in Defender für IoT verwendet werden.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: 10246277715d23f42eb0bd17d19794b8d4503f3f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521865"
---
# <a name="security-recommendations"></a>Sicherheitsempfehlungen

Defender für IoT scannt Ihre Azure-Ressourcen und IoT-Geräte und gibt Sicherheitsempfehlungen zur Reduzierung der Angriffsfläche.
Sicherheitsempfehlungen sind umsetzbar und zielen darauf ab, Kunden bei der Einhaltung von Best Practices im Bereich der Sicherheit zu unterstützen.

In diesem Artikel finden Sie eine Liste von Empfehlungen, die in Ihrem IoT-Hub und/oder auf Ihren IoT-Geräten ausgelöst werden können.

## <a name="agent-based-recommendations"></a>Agent-basierte Empfehlungen

Geräteempfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Gerätesicherheit und des Geräteverhaltens.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| Medium | Offene Ports am Gerät | Klassisches Sicherheitsmodul | Auf dem Gerät wurde ein lauschender Endpunkt gefunden. |
| Medium | In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden. | Klassisches Sicherheitsmodul | Zulässige Firewallrichtlinie gefunden(INPUT/OUTPUT). Die Firewallrichtlinie sollte standardmäßig den gesamten Datenverkehr verweigern und Regeln definieren, die die notwendige Kommunikation mit dem/vom Gerät ermöglichen. |
| Medium | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | Klassisches Sicherheitsmodul | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. |
| Medium | In der OUTPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden | Klassisches Sicherheitsmodul | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält. |
| Medium | Fehler bei der Überprüfung der Betriebssystembaseline | Klassisches Sicherheitsmodul | Gerät entspricht nicht den [CIS-Linux-Benchmarks](https://www.cisecurity.org/cis-benchmarks/) |

### <a name="agent-based-operational-recommendations"></a>Agent-basierte Betriebsempfehlungen

Operative Empfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Konfiguration des Sicherheits-Agent.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| Niedrig | Der Agent sendet ungenutzte Meldungen | Klassisches Sicherheitsmodul | 10 % oder mehr der Sicherheitsmeldungen in den letzten 24 Stunden waren kleiner als 4 KB. |
| Niedrig | Sicherheitszwillingskonfiguration nicht optimal | Klassisches Sicherheitsmodul | Die Konfiguration des Sicherheitszwillings ist nicht optimal. |
| Niedrig | Konflikt bei Sicherheitszwillingskonfiguration | Klassisches Sicherheitsmodul | In der Konfiguration des Sicherheitszwillings wurden Konflikte identifiziert. |  |


## <a name="built-in-recommendations-in-iot-hub"></a>Integrierte Empfehlungen in IoT Hub

Empfehlungsbenachrichtigungen bieten Einblicke und Vorschläge für Aktionen zum Verbessern des Sicherheitszustands Ihrer Umgebung.

| severity | Name | Data source | BESCHREIBUNG |
|--|--|--|--|
| High | Identische Authentifizierungsinformationen wurden von mehreren Geräten verwendet | IoT Hub | Von mehreren Geräten wurden die gleichen IoT Hub-Authentifizierungsinformationen verwendet. Dieser Prozess kann auf ein unzulässiges Gerät hinweisen, das die Identität eines legitimen Geräts vorgibt. Die Verwendung doppelter Berechtigungen erhöht das Risiko eines Geräteidentitätswechsels durch einen bösartigen Akteur. |
| Medium | Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein | IoT Hub | Die IP-Filterkonfiguration sollte Regeln für zulässigen Datenverkehr definieren und standardmäßig den gesamten übrigen Datenverkehr ablehnen. |
| Medium | IP-Filterregel umfasst großen IP-Adressbereich | IoT Hub | Der Quell-IP-Adressbereich einer Filterregel für zulässige IP-Adressen ist zu groß. Durch zu wenig einschränkende Regeln ist Ihr IoT Hub möglicherweise böswilligen Akteuren ausgesetzt. |
| Niedrig | Diagnoseprotokolle in IoT Hub aktivieren | IoT Hub | Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Durch Aufbewahrung der Protokolle können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist. |

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht](overview.md) über den Defender für IoT-Dienst
- Erfahren Sie, wie Sie auf Ihre [Sicherheitsdaten zugreifen](how-to-security-data-access.md).
- Erfahren Sie mehr über das [Untersuchen eines Geräts](how-to-investigate-device.md).
