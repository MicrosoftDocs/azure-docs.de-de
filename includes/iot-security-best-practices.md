---
title: include file
description: include file
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 2138eed9975abe804442c476d19b5b7229685362
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178206"
---
# <a name="security-best-practices-for-internet-of-things-iot"></a>Bewährte Methoden für die Sicherheit für das Internet der Dinge (IoT)

Zum Schützen einer IoT-Infrastruktur (Internet of Things, Internet der Dinge) ist eine umfassende Sicherheitsstrategie erforderlich. Diese Strategie muss folgende Bereiche abdecken: das Sichern von Daten in der Cloud, das Schützen der Integrität der Daten bei der Übertragung über das öffentliche Internet sowie das sichere Bereitstellen von Geräten. Jede Ebene leistet ihren eigenen Beitrag zur Sicherheit der Gesamtinfrastruktur, und die Beiträge bauen aufeinander auf und ergänzen sich.

## <a name="secure-an-iot-infrastructure"></a>Schützen einer IoT-Infrastruktur

Diese umfassende Sicherheitsstrategie kann mit aktiver Beteiligung verschiedener mit der Herstellung, Entwicklung und Bereitstellung von IoT-Geräten und -Infrastruktur beteiligten Personen entwickelt und durchgesetzt werden. Diese Beteiligten werden nachfolgend ausführlich beschrieben.

* **IoT-Hardwarehersteller/-integrator**: Dies sind in der Regel die Hersteller der bereitgestellten IoT-Hardware, die Integratoren, die Hardware von verschiedenen Herstellern zusammenstellen, oder die Lieferanten, die Hardware für eine von anderen Lieferanten hergestellte oder integrierte IoT-Bereitstellung liefern.

* **IoT-Lösungsentwickler**: IoT-Lösungen werden normalerweise von einem Lösungsentwickler entwickelt. Dieser Entwickler kann zu einem internen Team oder zu einem auf diese Aktivität spezialisierten Systemintegrator gehören. Der IoT-Lösungsentwickler kann verschiedene Komponenten der IoT-Lösung von Grund auf neu entwickeln, verschiedene Standard- oder Open Source-Komponenten integrieren oder Solution Accelerators verwenden und geringfügige Anpassungen vornehmen.

* **IoT-Lösungsbereitsteller**: Nachdem eine IoT-Lösung entwickelt wurde, muss sie vor Ort bereitgestellt werden. Dieser Prozess umfasst die Bereitstellung von Hardware, die Kopplung oder Verbindung von Geräten sowie die Bereitstellung von Lösungen in Hardwaregeräten oder in der Cloud.

* **IoT-Lösungsoperator**: Nachdem die IoT-Lösung bereitgestellt wurde, muss sie längerfristig betrieben, überwacht, aktualisiert und gewartet werden. Diese Aufgaben können durch ein internes Team aus IT-Experten, Hardwarebetriebs- und Wartungsteams sowie Fachspezialisten erfolgen, die das richtige Verhalten der gesamten IoT-Infrastruktur überwachen.

In den folgenden Abschnitten finden Sie für alle Beteiligten bewährte Methoden zum Entwickeln, Bereitstellen und Betreiben einer sicheren IoT-Infrastruktur.

## <a name="iot-hardware-manufacturerintegrator"></a>IoT-Hardwarehersteller/-integrator

Nachfolgend finden Sie die bewährten Methoden für IoT-Hardwarehersteller und Hardwareintegratoren.

* **Einhalten der Mindestanforderungen für Hardware**: Die Hardware sollte lediglich die für den Betrieb der Hardware erforderlichen Features enthalten. Beispielsweise sollten USB-Anschlüsse nur enthalten sein, wenn sie für den Betrieb des Geräts erforderlich sind. Zusätzliche Features machen das Gerät anfällig für unerwünschte Angriffe, die tunlichst vermieden werden sollten.

* **Manipulationssicheres Gestalten von Hardware**: Integrieren Sie Mechanismen zum Erkennen von physischen Manipulationen, z. B. dem Öffnen der Geräteabdeckung oder Entfernen eines Teils des Geräts. Diese Manipulationssignale können Teil des in die Cloud hochgeladenen Datenstroms sein, der die Operatoren über solche Ereignisse benachrichtigt.

* **Entwickeln sicherer Hardware**: Integrieren Sie – falls die Kosten es zulassen –, Sicherheitsfeatures wie sichere und verschlüsselte Speicher und eine auf TPM (Trusted Platform Module) basierende Startfunktion. Diese Features verbessern die Sicherheit der Geräte und tragen zum Schutz der gesamten IoT-Infrastruktur bei.

* **Schützen von Upgrades**: Während der Lebensdauer von Geräten sind Firmwareupgrades unvermeidlich. Durch die Entwicklung von Geräten mit sicheren Pfaden für Upgrades und eine kryptografische Sicherung der Firmwareversionen wird das Gerät während und nach Upgrades geschützt.

## <a name="iot-solution-developer"></a>IoT-Lösungsentwickler

Nachfolgend finden Sie die bewährten Methoden für IoT-Lösungsentwickler:

* **Befolgen einer sicheren Softwareentwicklungsmethodik**: Sichere Software kann nur entwickelt werden, wenn Fragen der Sicherheit von Anfang an und in allen Phasen bedacht werden: zu Beginn des Projekts, während der Implementierung und der Tests und auch bei der Bereitstellung. Eine solche Methodik wirkt sich auf die Auswahl der Plattformen, Sprachen und Tools aus. Der Microsoft Security Development Lifecycle bietet einen schrittweisen Ansatz für die Entwicklung sicherer Software.

* **Auswählen von Open-Source-Software mit Bedacht**: Open-Source-Software ermöglicht eine schnelle Lösungsentwicklung. Beim Auswählen von Open Source-Software sollten Sie jedoch berücksichtigen, wie aktiv die Community für die jeweilige Open Source-Komponente ist. Eine aktive Community stellt sicher, dass die Software ausreichend unterstützt wird und dass Probleme erkannt und behoben werden. Ein wenig bekanntes Open Source-Softwareprojekt, zu dem es keine aktive Community gibt, wird unter Umständen nicht unterstützt, und Probleme werden wahrscheinlich nicht entdeckt.

* **Sorgfältiges Integrieren**: Viele Sicherheitsschwachstellen von Software befinden sich an der Grenze zwischen Bibliotheken und APIs. Funktionen, die für die aktuelle Bereitstellung nicht erforderlich sind, sind möglicherweise immer noch über eine API-Schicht verfügbar. Um die Gesamtsicherheit zu gewährleisten, sollten Sie alle Schnittstellen der zu integrierenden Komponenten auf Sicherheitslücken überprüfen.

## <a name="iot-solution-deployer"></a>IoT-Lösungsbereitsteller

Nachfolgend finden Sie die bewährten Methoden für IoT-Lösungsbereitsteller:

* **Hardware sicher bereitstellen**: Für IoT-Bereitstellungen muss Hardware möglicherweise an unsicheren Orten wie öffentlichen Bereichen oder unbeaufsichtigten Gebieten bereitgestellt werden. Stellen Sie in solchen Situationen sicher, dass die Hardwarebereitstellung bestmöglich vor Manipulation geschützt ist. Wenn die Hardware über USB-Anschlüsse oder andere Anschlüsse verfügt, müssen diese sicher verdeckt werden. Solche Anschlüsse werden häufig als Einstiegspunkt für Angriffe genutzt.

* **Authentifizierungsschlüssel schützen**: Jedes Gerät benötigt während der Bereitstellung Geräte-IDs und die zugehörigen Authentifizierungsschlüssel, die vom Clouddienst generiert werden. Bewahren Sie diese Schlüssel auch nach der Bereitstellung sicher auf. Jeder kompromittierte Schlüssel kann von einem böswilligen Gerät verwendet werden, um sich als ein vorhandenes Gerät auszugeben.

## <a name="iot-solution-operator"></a>IoT-Lösungsoperator

Nachfolgend finden Sie die bewährten Methoden für IoT-Lösungoperatoren:

* **Stetige Aktualisierung des Systems**: Stellen Sie sicher, dass Gerätebetriebssysteme und alle Gerätetreiber stets auf die neueste Version aktualisiert werden. Wenn Sie in Windows 10 (IoT oder andere SKUs) die automatischen Updates aktivieren, wird Windows von Microsoft auf dem aktuellen Stand gehalten und ist somit ein sicheres Betriebssystem für IoT-Geräte. Auch andere Betriebssysteme wie Linux sollten Sie unbedingt auf dem aktuellen Stand halten, um sie vor böswilligen Angriffen zu schützen.

* **Vor schädlichen Aktivitäten schützen**: Wenn es das Betriebssystem zulässt, sollten Sie die neueste Antivirus- und Antischadsoftware auf jedem Gerät unter dem Betriebssystem installieren. Dadurch werden die meisten externen Bedrohungen abgewehrt. Die meisten modernen Betriebssysteme lassen sich durch geeignete Maßnahmen vor Bedrohungen schützen.

* **Häufig überwachen**: Die Überwachung der IoT-Infrastruktur auf sicherheitsbezogene Probleme ist entscheidend für die Reaktion auf Sicherheitsvorfälle. Die meisten Betriebssysteme bieten eine integrierte Ereignisprotokollierung, die häufig auf Sicherheitsverletzungen geprüft werden sollte. Die Überwachungsinformationen können als separater Telemetriedatenstrom an den Clouddienst gesendet und dort analysiert werden.

* **IoT-Infrastruktur physisch schützen**: Die verheerendsten Angriffe gegen die IoT-Infrastruktur beginnen mit dem physischen Zugriff auf Geräte. Der Schutz vor böswilligem Zugriff auf USB-Anschlüsse und anderweitigem physischen Zugriff ist eine wichtige Sicherheits- und Schutzmaßnahme. Ein entscheidender Schlüssel zum Aufdecken von Sicherheitsverstößen liegt in der Protokollierung des physischen Zugriffs, z.B. der Nutzung des USB-Anschlusses. Auch hier ermöglicht Windows 10 (IoT und andere SKUs) eine ausführliche Protokollierung dieser Ereignisse.

* **Cloud-Anmeldeinformationen schützen**: Cloudanmeldeinformationen zur Authentifizierung für die Konfiguration und den Betrieb einer IoT-Bereitstellung sind wahrscheinlich die einfachste Möglichkeit, um sich Zugang zu einem IoT-System zu verschaffen und es zu kompromittieren. Schützen Sie die Anmeldeinformationen, indem Sie das Kennwort häufig ändern und diese Anmeldeinformationen nicht auf öffentlichen Computern verwenden.

Die Funktionen der einzelnen IoT-Geräte variieren. Bei manchen Geräten handelt es sich möglicherweise um Computer mit gängigen Betriebssystemen, während auf anderen Geräten unter Umständen sehr einfache Betriebssysteme ausgeführt werden. Die zuvor beschriebenen bewährten Methoden für die Sicherheit können in unterschiedlichem Ausmaß auf diese Geräte angewendet werden. Falls vorhanden, sollten zusätzliche bewährte Methoden vom Hersteller dieser Geräte für die Sicherheit und Bereitstellung befolgt werden.

Manche älteren Geräte unterliegen möglicherweise Einschränkungen und sind nicht speziell für eine IoT-Bereitstellung ausgelegt. Diese Geräte können möglicherweise keine Daten verschlüsseln oder keine Verbindung mit dem Internet herstellen, oder sie ermöglichen keine erweiterte Überwachung. In diesen Fällen können Sie ein modernes und sicheres Bereichsgateway verwenden, um Daten von älteren Geräten zu aggregieren und für die erforderliche Sicherheit zum Anschließen dieser Geräte an das Internet zu sorgen. Mit Bereichsgateways können Sie eine sichere Authentifizierung, die Aushandlung von verschlüsselten Sitzungen sowie den Empfang von Befehlen aus der Cloud ermöglichen. Außerdem bieten diese Gateways noch viele andere Sicherheitsfunktionen.