---
title: Grundlegendes zu IoT-Sicherheitsempfehlungen für Azure Security Center für (Vorschauversion) | Microsoft-Dokumentation
description: Lernen Sie das Konzept der Sicherheitsempfehlungen kennen, und erfahren Sie, wie sie in Azure Security Center für IoT verwendet werden.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 122a1585abf43d1aedc7f329cd7e41a6228f8c3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200670"
---
# <a name="security-recommendations"></a>Sicherheitsempfehlungen

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Das Azure Security Center (ASC) für IoT scannt Ihre Azure-Ressourcen und IoT-Geräte und gibt Sicherheitsempfehlungen zur Reduzierung Ihrer Angriffsfläche. Sicherheitsempfehlungen sind umsetzbar und zielen darauf ab, Kunden bei der Einhaltung von bewährten Sicherheitsmethoden zu unterstützen.

In diesem Artikel finden Sie eine Liste von Empfehlungen, die auf Ihrem IoT Hub und/oder IoT-Geräten ausgelöst werden können.

## <a name="recommendations-for-iot-devices"></a>Empfehlungen für IoT-Geräte

Geräteempfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Gerätesicherheit und des Geräteverhaltens. 

| Severity | NAME                                                      | Data source | BESCHREIBUNG                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Mittel   | Offene Ports am Gerät                                      | Agent       | Auf dem Gerät wurde ein lauschender Endpunkt gefunden                                                                                                                                                          |
| Mittel   | In einer der Ketten wurde eine zu wenig einschränkende Firewallrichtlinie gefunden. | Agent       | Zulässige Firewallrichtlinie gefunden(INPUT/OUTPUT). Die Firewallrichtlinie sollte standardmäßig den gesamten Datenverkehr verweigern und Regeln definieren, die die notwendige Kommunikation mit dem/vom Gerät ermöglichen.                               |
| Mittel   | In der INPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden     | Agent       | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält.                                                                                    |
| Mittel   | In der OUTPUT-Kette wurde eine zu wenig einschränkende Firewallregel gefunden    | Agent       | In der Firewall wurde eine Regel gefunden, die ein zu wenig einschränkendes Muster für eine Vielzahl von IP-Adressen oder Ports enthält.                                                                                   |
| Mittel   | Fehler bei der Überprüfung der Betriebssystembaseline           | Agent       | Gerät entspricht nicht den [CIS Linux-Benchmarks](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Operative Empfehlungen für IoT-Geräte

Operative Empfehlungen liefern Einblicke und Vorschläge zur Verbesserung der Konfiguration des Sicherheits-Agent.

| Severity | NAME                                    | Data source | BESCHREIBUNG                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Niedrig      | Der Agent sendet ungenutzte Meldungen          | Agent       | 10 % oder mehr der Sicherheitsmeldungen in den letzten 24 Stunden waren kleiner als 4 KB.  |
| Niedrig      | Sicherheitszwillingskonfiguration nicht optimal | Agent       | Die Konfiguration des Sicherheitszwillings ist nicht optimal.                                        |
| Niedrig      | Konflikt bei Sicherheitszwillingskonfiguration    | Agent       | In der Konfiguration des Sicherheitszwillings wurden Konflikte identifiziert.                           |


## <a name="recommendations-for-iot-hub"></a>Empfehlungen für IoT Hub

Empfehlungsbenachrichtigungen bieten Einblicke und Vorschläge für Aktionen zum Verbessern des Sicherheitszustands Ihrer Umgebung.  

| Severity | NAME                                                     | Data source | BESCHREIBUNG                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hoch     | Identische Authentifizierungsinformationen wurden von mehreren Geräten verwendet | IoT Hub     | Von mehreren Geräten wurden die gleichen IoT Hub-Authentifizierungsinformationen verwendet. Dies kann auf ein unzulässiges Gerät hinweisen, das die Identität eines legitimen Geräts vorgibt. Die Verwendung doppelter Berechtigungen erhöht das Risiko eines Geräteidentitätswechsels durch einen bösartigen Akteur. |
| Mittel   | Die Standard-IP-Filterrichtlinie muss auf „Verweigern“ festgelegt sein                  | IoT Hub     | Die IP-Filterkonfiguration sollte Regeln für zulässigen Datenverkehr definieren und standardmäßig den gesamten übrigen Datenverkehr ablehnen.                                                                                                     |
| Mittel   | IP-Filterregel umfasst großen IP-Adressbereich                   | IoT Hub     | Der Quell-IP-Adressbereich einer Filterregel für zulässige IP-Adressen ist zu groß. Durch zu wenig einschränkende Regeln ist Ihr IoT Hub möglicherweise böswilligen Akteuren ausgesetzt.                                                                                       |
| Niedrig      | Diagnoseprotokolle in IoT Hub aktivieren                       | IoT Hub     | Aktivieren Sie Protokolle, und bewahren Sie sie bis zu ein Jahr lang auf. Durch Aufbewahrung der Protokolle können Sie vergangene Aktivitäten nachvollziehen, wenn Sie Sicherheitsincidents untersuchen oder Ihr Netzwerk gefährdet ist.                                       |
|