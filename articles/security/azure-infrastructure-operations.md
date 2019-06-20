---
title: Verwaltung des Microsoft Azure-Produktionsnetzwerks – Microsoft Azure
description: In diesem Artikel ist beschrieben, wie Microsoft das Azure-Produktionsnetzwerk verwaltet und betreibt, um die Azure-Rechenzentren zu schützen.
services: security
documentationcenter: n
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2019
ms.author: terrylan
ms.openlocfilehash: 515896851c5bf7b9c847416ebadedf1242e0eeb9
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428266"
---
# <a name="management-and-operation-of-the-azure-production-network"></a>Verwaltung und Betrieb des Azure-Produktionsnetzwerks    
In diesem Artikel ist beschrieben, wie Microsoft das Azure-Produktionsnetzwerk verwaltet und betreibt, um die Azure-Rechenzentren zu schützen.

## <a name="monitor-log-and-report"></a>Überwachen, protokollieren und berichten

Für die Verwaltung und den Betrieb des Azure-Produktionsnetzwerks sind die Betriebsteams von Azure und Azure SQL-Datenbank gemeinsam zuständig. Die Teams verwenden mehrere Tools zur Überwachung der System- und Anwendungsleistung in der Umgebung. Darüber hinaus verwenden Sie entsprechende Tools, um Netzwerkgeräte, Server, Dienste und Anwendungsprozesse zu überwachen.

Um die sichere Ausführung der Dienste in der Azure-Umgebung zu gewährleisten, implementieren die Betriebsteams mehrere Ebenen der Überwachung, Protokollierung und Berichterstellung. Dazu gehören folgende Aktionen:

- Der Microsoft Monitoring Agent (MMA) erfasst in erster Linie Überwachungs- und Diagnoseprotokollinformationen aus vielen Quellen, einschließlich des Fabric Controller (FC) und des Stammbetriebssystems (BS), und schreibt sie in Protokolldateien. Der Agent überträgt schließlich eine Teilmenge der Informationen in Digestform in ein vorkonfiguriertes Azure Storage-Konto. Der eigenständige Überwachungs- und Diagnosedienst liest verschiedene Überwachungs- und Diagnoseprotokolldaten und fasst die Informationen zusammen. Der Überwachungs- und Diagnosedienst schreibt die Informationen in ein integriertes Protokoll. Azure verwendet das benutzerdefinierte Azure Security Monitoring, eine Erweiterung des Azure-Überwachungssystems. Es verfügt über Komponenten, die sicherheitsrelevante Ereignisse an verschiedenen Stellen in der Plattform beobachten, analysieren und melden.

- Die Windows Fabric-Plattform in Azure SQL-Datenbank bietet die Verwaltung, Bereitstellung, Entwicklung und Betriebsüberwachungsdienste für Azure SQL-Datenbank. Die Plattform bietet verteilte, mehrstufige Bereitstellungsdienste, Überwachung der Integrität, automatische Reparaturen und Dienstversionskonformität. Sie bietet folgende Dienste:

   - Dienstmodellierungsfunktionen mit realitätsnaher Entwicklungsumgebung (Rechenzentrumscluster sind teuer und selten).
   - Bereitstellung per Mausklick und Upgradeworkflows für Dienstbootstrap und Wartung.
   - Integritätsberichterstellung mit automatisiertem Reparaturworkflow zum Aktivieren der Selbstreparatur.
   - Echtzeitüberwachung, Warnungen und Debuggingoptionen für die Knoten eines verteilten Systems
   - Zentrale Sammlung von operativen Daten und Metriken für die verteilte Analyse der Grundursache und Dienstinformationen.
   - Operative Tools für die Bereitstellung, für das Change Management und für die Überwachung.
   - Windows Fabric-Plattform und Watchdogskripts von Azure SQL-Datenbank werden fortlaufend ausgeführt und überwachen in Echtzeit.

Wenn Anomalien auftreten, wird der Prozess zur Reaktion auf Incidents gefolgt vom Azure-Incidentselektierungsteam aktiviert. Die entsprechenden Azure-Supportmitarbeiter werden benachrichtigt, damit sie auf den Incident reagieren können. Nachverfolgung und Lösung von Problemen werden in einem zentralen Ticketausstellungssystem dokumentiert und verwaltet. Systemverfügbarkeitsmetriken sind im Rahmen des Geheimhaltungsvertrags (NDA) und auf Anfrage erhältlich.

## <a name="corporate-network-and-multi-factor-access-to-production"></a>Unternehmensnetzwerk- und Multi-Factor-Zugriff auf die Produktion
Die Benutzerbasis des Unternehmensnetzwerks umfasst Azure-Supportmitarbeiter. Das Unternehmensnetzwerk unterstützt interne Unternehmensfunktionen und umfasst den Zugriff auf interne Anwendungen, die für den Azure-Kundensupport verwendet werden. Das Unternehmensnetzwerk ist logisch und physisch vom Azure-Produktionsnetzwerk getrennt. Azure-Mitarbeiter greifen mit Azure-Arbeitsstationen und -Laptops auf das Unternehmensnetzwerk zu. Alle Benutzer benötigen ein Azure Active Directory-Konto (Azure AD), einschließlich Benutzername und Kennwort, um auf die Unternehmensnetzwerkressourcen zuzugreifen. Der Unternehmensnetzwerkzugriff verwendet Azure AD-Konten, die für alle Mitarbeiter, Auftragnehmer, Lieferanten von Microsoft ausgestellt sind und von Microsoft Information Technology verwaltet werden. Eindeutige Benutzer-IDs unterscheiden Mitarbeiter basierend auf deren Beschäftigungsstatus bei Microsoft.

Der Zugriff auf interne Azure-Anwendungen wird über die Authentifizierung mit den Active Directory-Verbunddiensten (AD FS) gesteuert. AD FS ist ein von Microsoft Information Technology gehosteter Dienst, der die Authentifizierung von Benutzern des Unternehmensnetzwerks über die Anwendung eines sicheren Tokens und von Benutzeransprüchen bereitstellt. AD FS ermöglicht internen Azure-Anwendungen, Benutzer anhand der Active Directory-Unternehmensdomäne von Microsoft zu authentifizieren. Um über die Unternehmensnetzwerkumgebung auf das Produktionsnetzwerk zuzugreifen, muss der Benutzer mithilfe der mehrstufigen Authentifizierung authentifizieren.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über die Sicherung der Azure-Infrastruktur seitens Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)
