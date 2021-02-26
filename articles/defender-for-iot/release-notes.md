---
title: Neuerungen in Azure Defender für IoT
description: In diesem Artikel wird beschrieben, welche Neuerungen das aktuelle Release von Defender für IoT enthält.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2021
ms.author: shhazam
ms.openlocfilehash: f15c4ed6bb7da5cd33eca96c171bfada68092b35
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820669"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Neuerungen in Azure Defender für IoT

In diesem Artikel sind neue und verbesserte Features von Defender für IoT aufgeführt.

Die genannten Features befinden sich in der Vorschauphase. Die [ergänzenden Bestimmungen für Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
## <a name="february-2021"></a>Februar 2021

### <a name="sensor---enhanced-custom-alert-rules"></a>Sensor: erweiterte benutzerdefinierte Warnungsregeln

Sie können jetzt benutzerdefinierte Regeln für Warnungen erstellen, die auf dem Tag, einer Gruppe von Tagen und dem Zeitraum basieren, in dem Netzwerkaktivität erkannt wurde.  Das Arbeiten mit Regelbedingungen für Tag und Uhrzeit ist nützlich, z. B. in Fällen, in denen der Schweregrad einer Warnung von der Uhrzeit des Ereignisses abgeleitet wird. Erstellen Sie z. B. eine benutzerdefinierte Regel, die eine Warnung mit hohem Schweregrad auslöst, wenn am Wochenende oder am Abend Netzwerkaktivität festgestellt wird.

Dieses Feature ist mit der Veröffentlichung der Version 10.1 im Sensor verfügbar.

### <a name="on-premises-management-console---export-alerts"></a>Lokale Verwaltungskonsole: Exportieren von Warnungen

Informationen zu Warnungen können jetzt aus der lokalen Verwaltungskonsole in eine CSV-Datei exportiert werden. Sie können Informationen zu allen erkannten Warnungen oder Informationen basierend auf der gefilterten Ansicht exportieren.

Dieses Feature ist in der lokalen Verwaltungskonsole mit Veröffentlichung von Version 10.1 verfügbar.
### <a name="device-builder---new-micro-agent-public-preview"></a>Gerätehersteller: neuer Mikro-Agent (öffentliche Vorschau)

Ein neues Geräteherstellermodul ist verfügbar. Das Modul, das als Micro-Agent bezeichnet wird, ermöglicht Folgendes:

- **Integration in Azure IoT Hub und Azure Defender für IoT** : Integrieren Sie mehr Endpunktsicherheit direkt in Ihre IoT-Geräte, indem Sie sie mit der Überwachungsoption von Azure IoT Hub und Azure Defender für IoT verbinden.
- **Flexible Bereitstellungsoptionen mit Unterstützung für IoT-Standardbetriebssysteme** können entweder als binäres Paket oder als modifizierbarer Quellcode bereitgestellt werden, mit Unterstützung für IoT-Standardbetriebssysteme wie Linux und Azure RTOS.
- **Minimale Ressourcenanforderungen ohne Kernelabhängigkeiten vom Betriebssystem**: geringer Platzbedarf, geringe CPU-Auslastung und keine Kernelabhängigkeiten vom Betriebssystem.
- **Verwaltung des Sicherheitsstatus**: Sie können den Sicherheitsstatus Ihrer IoT-Geräte aktiv verwalten.
- **Kontinuierliche Erkennung von IoT/OT-Bedrohungen in Echtzeit**: Erkennung von Bedrohungen wie Botnets, Brute-Force-Angriffen, Krypto-Minern und verdächtigen Netzwerkaktivitäten

Die veraltete Dokumentation zum Sicherheitsmodul wird in den Ordner *Agent-basierte Lösung für Gerätehersteller > Classic* verschoben.

Diese Featuregruppe ist in der aktuellen öffentlichen Vorschauversion verfügbar.

## <a name="january-2021"></a>Januar 2021

- [Security](#security)
- [Onboarding](#onboarding)
- [Benutzerfreundlichkeit](#usability)
- [Andere Updates](#other-updates)
### <a name="security"></a>Sicherheit

Für dieses Release wurden Verbesserungen bei der Wiederherstellung von Zertifikaten und Kennwörtern vorgenommen.

#### <a name="certificates"></a>Zertifikate
  
Diese Version ermöglicht Folgendes:

- Direktes Hochladen von SSL-Zertifikaten auf die Sensoren und lokalen Verwaltungskonsolen
- Durchführen der Überprüfung zwischen der lokalen Verwaltungskonsole und den verbundenen Sensoren sowie zwischen einer Verwaltungskonsole und einer Verwaltungskonsole für Hochverfügbarkeit. Die Überprüfung basiert auf Ablaufdaten, der Echtzeit der Stammzertifizierungsstelle und auf Zertifikatssperrlisten.  Wenn bei der Überprüfung ein Fehler auftritt, wird die Sitzung nicht fortgesetzt.

Für Upgrades:

- Änderungen in Bezug auf die Funktionen für SSL-Zertifikate oder die Überprüfung während des Upgrades wurden nicht vorgenommen.
- Nach dem Upgrade können Administratorbenutzer der Sensoren und der lokalen Verwaltungskonsole SSL-Zertifikate ersetzen oder die Überprüfung von SSL-Zertifikaten über das Fenster „Systemeinstellungen“ > „SSL-Zertifikat“ aktivieren.  

Für Neuinstallationen:

- Bei der erstmaligen Anmeldung müssen Benutzer entweder ein SSL-Zertifikat (empfohlen) oder ein lokal generiertes selbstsigniertes Zertifikat (nicht empfohlen) verwenden.
- Für Neuinstallationen ist die Zertifikatüberprüfung standardmäßig aktiviert.

#### <a name="password-recovery"></a>Kennwortwiederherstellung
  
Administratorbenutzer der Sensoren und der lokalen Verwaltungskonsole können jetzt im Azure Defender für IoT-Portal Kennwörter wiederherstellen. Bisher musste für die Kennwortwiederherstellung das Supportteam hinzugezogen werden.

### <a name="onboarding"></a>Onboarding

#### <a name="on-premises-management-console---committed-devices"></a>Lokale Verwaltungskonsole: Committete Geräte

Nach der erstmaligen Anmeldung bei der lokalen Verwaltungskonsole müssen Benutzer jetzt eine Aktivierungsdatei hochladen. Die Datei enthält die aggregierte Anzahl von Geräten, die im Netzwerk der Organisation überwacht werden sollen. Diese Zahl wird als Anzahl von committeten Geräten bezeichnet.
Committete Geräte werden beim Onboardingprozess im Azure Defender für IoT-Portal definiert, bei dem die Aktivierungsdatei generiert wird.
Erstmalige Benutzer und Benutzer, die ein Upgrade durchführen, müssen die Aktivierungsdatei hochladen.
Nach der ersten Aktivierung kann es vorkommen, dass die Anzahl von im Netzwerk erkannten Geräten die Anzahl von committeten Geräten übersteigt. Dies kann beispielsweise der Fall sein, wenn Sie weitere Sensoren mit der Verwaltungskonsole verbinden. Bei einer Abweichung zwischen der Anzahl erkannter Geräte und der Anzahl committeter Geräte wird in der Verwaltungskonsole eine Warnung angezeigt. In diesem Fall sollten Sie eine neue Aktivierungsdatei hochladen.

#### <a name="pricing-page-options"></a>Optionen auf der Preisseite

Auf der Preisseite können Sie für neue Abonnements das Onboarding für Azure Defender für IoT durchführen und committete Geräte in Ihrem Netzwerk definieren.  
Darüber hinaus können Sie auf der Preisseite jetzt vorhandene Abonnements verwalten, die einem Sensor zugeordnet sind, und die committeten Geräte aktualisieren.

#### <a name="view-and-manage-onboarded-sensors"></a>Anzeigen und Verwalten von integrierten Sensoren

Auf einer neuen Portalseite für Standorte und Sensoren können Sie folgende Aktionen durchführen:

- Hinzufügen von beschreibenden Informationen zum Sensor. Dies können beispielsweise Zonen sein, die dem Sensor zugeordnet sind, oder Freitext-Tags.
- Anzeigen und Filtern von Sensorinformationen. Sie können beispielsweise Details zu Sensoren, die mit der Cloud verbunden sind oder lokal verwaltet werden, oder Informationen zu Sensoren in einer bestimmten Zone anzeigen.  

### <a name="usability"></a>Benutzerfreundlichkeit

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel-Seite „Neuer Connector“

Die Azure Defender für IoT-Seite für den Datenconnector in Azure Sentinel wurde überarbeitet. Der Datenconnector basiert jetzt nicht mehr auf IoT-Hubs, sondern auf Abonnements, um Kunden eine bessere Verwaltung ihrer Konfigurationsverbindung in Azure Sentinel zu ermöglichen.

#### <a name="azure-portal-permission-updates"></a>Updates für Berechtigungen im Azure-Portal  

Die Unterstützung für „Sicherheitsleseberechtigte“ und „Sicherheitsadministrator“ wurde hinzugefügt.

### <a name="other-updates"></a>Weitere Updates

#### <a name="access-group---zone-permissions"></a>Zugriffsgruppe: Zonenberechtigungen
  
Die Zugriffsgruppenregeln für die lokale Verwaltungskonsole enthalten nicht die Option zum Gewähren des Zugriffs auf eine bestimmte Zone. Es wurde keine Änderung für das Definieren von Regeln vorgenommen, in denen Standorte, Regionen und Geschäftseinheiten verwendet werden.   Nach dem Upgrade werden Zugriffsgruppen, für die über Regeln der Zugriff auf bestimmte Zonen zulässig war, so geändert, dass der Zugriff auf deren übergeordneten Standort, einschließlich aller Zonen, möglich ist.

#### <a name="terminology-changes"></a>Terminologieänderungen

Anstelle des Ausdrucks „Ressource“ wird für den Sensor und die lokale Verwaltungskonsole, in den Berichten und in anderen Lösungsbenutzeroberflächen jetzt „Gerät“ verwendet.
Anstelle von „Ereignis verwalten“ wird in Warnungen für den Sensor und in der lokalen Verwaltungskonsole jetzt „Problembehandlungsschritte“ verwendet.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit Azure Defender für IoT](getting-started.md)
