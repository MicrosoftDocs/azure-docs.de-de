---
title: Azure-Infrastrukturüberwachung
description: In diesem Artikel wird die Überwachung des Azure-Produktionsnetzwerks erläutert.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591639"
---
# <a name="azure-infrastructure-monitoring"></a>Azure-Infrastrukturüberwachung   

## <a name="configuration-and-change-management"></a>Konfigurationsverwaltung und Change Management
Azure überprüft und aktualisiert jährlich die Konfigurationseinstellungen und Baselinekonfigurationen von Hardware, Software und Netzwerkgeräten. Änderungen werden vor der Einführung in die Produktionsumgebung in einer Entwicklungs- und/oder Testumgebung entwickelt, getestet und genehmigt.

Die für Azure-basierte Dienste erforderlichen Baselinekonfigurationen werden vom Azure Security- und Compliance-Team und von Serviceteams überprüft. Die Überprüfung durch ein Serviceteam ist Teil der Tests, die vor der Bereitstellung des Produktionsdiensts durchgeführt werden.

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken
Die Verwaltung von Sicherheitsupdates hilft beim Schützen von Systemen vor bekannten Sicherheitsrisiken. Azure verwendet integrierte Bereitstellungssysteme zur Verwaltung der Verteilung und Installation von Sicherheitsaktualisierungen für Microsoft-Software. Azure kann auch auf die Ressourcen des Microsoft Security Response Centers (MSRC) zurückgreifen. Das MSRC identifiziert und überwacht Sicherheitsincidents und Sicherheitsrisiken in Clouds rund um die Uhr an jedem Tag des Jahres, reagiert darauf und löst diese Probleme.

## <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken
Die Überprüfung auf Sicherheitsrisiken wird für Serverbetriebssysteme, Datenbanken und Netzwerkgeräte durchgeführt. Die Sicherheitsrisikoüberprüfungen werden mindestens vierteljährlich ausgeführt. Azure beauftragt unabhängige Prüfer mit der Durchführung von Penetrationstests für die Bereichsgrenzen von Azure. Außerdem werden routinemäßig Red-Team-Übungen durchgeführt und die Ergebnisse zur Verbesserung der Sicherheit genutzt.

## <a name="protective-monitoring"></a>Vorbeugende Überwachung
Azure Security weist definierte Anforderungen für die aktive Überwachung auf. Dienstteams konfigurieren die Tools für die aktive Überwachung in Übereinstimmung mit diesen Anforderungen. Zu den aktiven Überwachungstools zählen der Microsoft Monitoring Agent (MMA) und System Center Operations Manager. Diese Tools werden für die Bereitstellung von Echtzeitwarnungen für Azure-Sicherheitspersonal in Situationen konfiguriert, die ein sofortiges Handeln erfordern.

## <a name="incident-management"></a>Incident Management
Microsoft implementiert einen Prozess zum Verwalten von Sicherheitsvorfällen, um eine koordinierte Reaktion auf Vorfälle zu erleichtern, sollte es zu einem Vorfall kommen.

Wenn Microsoft Kenntnis von unberechtigten Zugriffen auf Kundendaten erhält, die auf Microsoft-Komponenten oder in Microsoft-Einrichtungen gespeichert sind, oder wenn Microsoft Kenntnis von unberechtigten Zugriffen auf solche Komponenten und Einrichtungen erhält, die zu Verlust, Offenlegung oder Veränderung von Kundendaten führen, ergreift Microsoft folgende Maßnahmen:

- Der Kunden wird sofort bezüglich des Sicherheitsincidents benachrichtigt.
- Der Sicherheitsincident wird sofort untersucht, und der Kunde erhält detaillierte Informationen über den Incident.
- Es werden sofort angemessene Schritte durchgeführt, um die Auswirkungen zu verringern und die aus dem Sicherheitsincident resultierenden Schäden zu minimieren.

Es wurde ein Incident-Management-Framework eingerichtet, das Rollen definiert und Zuständigkeiten zuordnet. Das Azure Security Incident Management-Team ist für die Behandlung von Sicherheitsincidents zuständig, einschließlich Eskalation und der Hinzuziehung von Spezialistenteams, sollte dies erforderlich sein. Azure-Betriebsmanager sind für die Überwachung der Untersuchung und die Behebung von Sicherheits- und Datenschutzincidents zuständig.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über die Sicherung der Azure-Infrastruktur seitens Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)
