---
title: Was sind Azure-Dienstintegritätsbenachrichtigungen?
description: Mit Dienstintegritätsbenachrichtigungen können Sie von Microsoft Azure veröffentlichte Dienstintegritätsmeldungen anzeigen.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 87efa7442b0c67f2ee5f83b6b3e8ac8530ce5285
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081803"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Anzeigen von Dienstintegritätsbenachrichtigungen im Azure-Portal

Dienstintegritätsbenachrichtigungen werden von Azure veröffentlicht und enthalten Informationen zu den Ressourcen in Ihrem Abonnement. Diese Benachrichtigungen sind eine Unterklasse von Aktivitätsprotokollereignissen und auch im Aktivitätsprotokoll zu finden. Dienstintegritätsbenachrichtigungen können je nach Klasse informativ oder handlungsrelevant sein.

Es gibt verschiedene Klassen von Dienstintegritätsbenachrichtigungen:  

- **Aktion erforderlich**: Möglicherweise wurde von Azure etwas Ungewöhnliches in Ihrem Konto festgestellt. Das Azure-Team arbeitet mit Ihnen zusammen, um das Problem zu beheben. Azure sendet Ihnen eine Benachrichtigung, in der beschrieben wird, welche Maßnahmen Sie ergreifen müssen oder wie Sie sich an das Azure-Engineeringteam oder den Azure-Support wenden.  
- **Unterstützte Wiederherstellung**: Ein Ereignis ist aufgetreten, und das Engineeringteam hat sich davon überzeugt, dass weiterhin Beeinträchtigungen vorliegen. Das Azure-Engineeringteam muss direkt mit Ihnen zusammenarbeiten, um Ihre Dienste vollständig wiederherzustellen.  
- **Incident**: Ein Ereignis, das den Dienst beeinträchtigt, wirkt sich derzeit auf mindestens eine Ressource in Ihrem Abonnement aus.  
- **Wartung**: Eine geplante Wartungsaktivität, die eine oder mehrere Ressourcen in Ihrem Abonnement beeinträchtigen kann.  
- **Information**: Potenzielle Optimierungen, mit denen Sie Ihre Ressourcennutzung verbessern können. 
- **Sicherheit**: Dringende sicherheitsbezogene Informationen zu Ihren auf Azure ausgeführten Lösungen.

Jede Dienstintegritätsbenachrichtigung schließt Details zum Umfang und zu den Auswirkungen auf Ihre Ressourcen ein. Zu den Details gehören:

Eigenschaftenname | BESCHREIBUNG
-------- | -----------
channels | Einer der folgenden Werte: **Administrator** oder **Vorgang**.
correlationId | Normalerweise eine GUID im Zeichenfolgenformat. Ereignisse, die zur gleichen Aktion gehören, weisen normalerweise den gleichen correlationId-Wert auf.
eventDataId | Der eindeutige Bezeichner eines Ereignisses.
eventName | Der Titel eines Ereignisses.
level | Die Stufe eines Ereignisses
resourceProviderName | Der Name des Ressourcenanbieters für die betroffene Ressource.
resourceType| Der Ressourcentyp der betroffenen Ressource.
subStatus | Üblicherweise der HTTP-Statuscode des entsprechenden REST-Aufrufs, kann aber auch weitere Zeichenfolgen zur Beschreibung eines untergeordneten Status enthalten. Beispiel:  OK (HTTP-Statuscode: 200), Erstellt (HTTP-Statuscode: 201), Akzeptiert (HTTP-Statuscode: 202), Kein Inhalt (HTTP-Statuscode: 204), Ungültige Anforderung (HTTP-Statuscode: 400), Nicht gefunden (HTTP-Statuscode: 404), Konflikt (HTTP-Statuscode: 409), Interner Serverfehler (HTTP-Statuscode: 500), Dienst nicht verfügbar (HTTP-Statuscode: 503) und Gatewaytimeout (HTTP-Statuscode: 504).
eventTimestamp | Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat.
submissionTimestamp | Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war.
subscriptionId | Das Azure-Abonnement, in dem dieses Ereignis protokolliert wurde.
status | Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: **Gestartet**, **In Bearbeitung**, **Erfolgreich**, **Fehler**, **Aktiv** und **Gelöst**.
operationName | Der Name des Vorgangs.
category | Diese Eigenschaft lautet immer **ServiceHealth**.
resourceId | Die Ressourcen-ID der betroffenen Ressource.
Properties.title | Der lokalisierte Titel für diese Kommunikation. Englisch ist der Standard.
Properties.communication | Die lokalisierten Details der Kommunikation mit HTML-Markup. Englisch ist der Standard.
Properties.incidentType | Einer der folgenden Werte: **ActionRequired**, **Zur Information**, **Incident**, **Wartung** oder **Sicherheit**.
Properties.trackingId | Der Incident, dem dieses Ereignis zugeordnet ist. Verwenden Sie diese Option, um die Ereignisse im Zusammenhang mit einem Vorfall zu korrelieren.
Properties.impactedServices | Ein JSON-Blob mit Escapezeichen, das die vom Incident betroffenen Dienste und Regionen beschreibt. Die Eigenschaft umfasst eine Liste der Dienste, von denen jeder über einen **ServiceName** verfügt, sowie eine Liste der betroffenen Regionen, von denen jede einen **RegionName** aufweist.
Properties.defaultLanguageTitle | Die Kommunikation auf Englisch.
Properties.defaultLanguageContent | Die Kommunikation auf Englisch, entweder als HTML-Markup oder Nur-Text.
Properties.stage | Die möglichen Werte für **Incident** und **Security** sind **Active,** **Resolved** oder **RCA**. Der einzige Wert für **ActionRequired** oder **Informational** ist **Active**. Für **Wartung** sind folgende Werte möglich: **Aktiv**, **Geplant**, **InProgress**, **Abgebrochen**, **Neu geplant**, **Aufgelöst** oder **Abgeschlossen**.
Properties.communicationId | Die Kommunikation, der dieses Ereignis ist zugeordnet ist.

### <a name="details-on-service-health-level-information"></a>Details zu Informationen zur Dienstintegritätsebene

**Aktion erforderlich** (properties.incidentType == ActionRequired)
- Zur Information: Aktion des Administrators erforderlich, um Auswirkungen auf bestehende Dienste zu vermeiden
    
**Wartung** (properties.incidentType == Maintenance)
- Warnung: Notfallwartung
- Zur Information: standardmäßig geplante Wartung

**Informationen** (properties.incidentType == Information)
- Zur Information: Administrator muss ggf. eingreifen, um Auswirkungen auf bestehende Dienste zu vermeiden

**Sicherheit** (properties.incidentType == Security)
- Fehler: Weit verbreitete Probleme beim Zugriff auf mehrere Dienste in verschiedenen Regionen wirken sich auf eine Vielzahl von Kunden aus.
- Warnung: Probleme beim Zugriff auf bestimmte Dienste und/oder bestimmte Regionen wirken sich auf eine Teilmenge von Kunden aus.
- Zur Information: Probleme, die sich auf den Verwaltungsbetrieb und/oder die Latenz auswirken, ohne die Verfügbarkeit von Diensten zu beeinträchtigen.

**Dienstprobleme** (properties.incidentType == Incident)
- Fehler: Weit verbreitete Probleme beim Zugriff auf mehrere Dienste in verschiedenen Regionen wirken sich auf eine Vielzahl von Kunden aus.
- Warnung: Probleme beim Zugriff auf bestimmte Dienste und/oder bestimmte Regionen wirken sich auf eine Teilmenge von Kunden aus.
- Zur Information: Probleme, die sich auf den Verwaltungsbetrieb und/oder die Latenz auswirken, ohne die Verfügbarkeit von Diensten zu beeinträchtigen.
