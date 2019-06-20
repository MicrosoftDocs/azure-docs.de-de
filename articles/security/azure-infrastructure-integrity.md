---
title: Integrität der Azure-Infrastruktur
description: Dieser Artikel behandelt die Integrität der Azure-Infrastruktur.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 24d54fa7a8985a6af58cddfc969b8023485c73c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587120"
---
# <a name="azure-infrastructure-integrity"></a>Integrität der Azure-Infrastruktur

## <a name="software-installation"></a>Softwareinstallation
Alle Komponenten im Softwarestack, die in der Azure-Umgebung installiert sind, werden gemäß dem Microsoft SDL-Prozess (Microsoft Security Development Lifecycle) von Microsoft speziell erstellt. Alle Softwarekomponenten, einschließlich Betriebssystem-Images und SQL-Datenbank, werden im Rahmen des Prozesses für Change Management und Release Management bereitgestellt. Bei dem Betriebssystem, das auf allen Knoten ausgeführt wird, handelt es sich um eine angepasste Version von Windows Server 2008 oder Windows Server 2012. Die genaue Version wird vom Fabric Controller (FC) entsprechend der Rolle ausgewählt, die das Betriebssystem übernehmen soll. Darüber hinaus gestattet das Hostbetriebssystem keine Installation von nicht autorisierten Softwarekomponenten.

Einige Azure-Komponenten werden als Azure-Kunden auf einer Gast-VM bereitgestellt, die auf einem Gastbetriebssystem ausgeführt wird.

## <a name="virus-scans-on-builds"></a>Virenscans auf Builds
Builds von Azure-Softwarekomponenten (einschließlich Betriebssystem) müssen einen Virenscan mit dem Endpoint Protection (MEP)-Virenschutztool durchlaufen. Bei jedem Virenscan wird ein Protokoll im zugehörigen Buildverzeichnis erstellt, in dem die gescannten Komponenten und die Ergebnisse des Scans detailliert aufgeführt sind. Der Virenscan ist Teil des Buildquellcodes für jede Komponente in Azure. Code wird ohne einen sauberen und erfolgreichen Virensan nicht in die Produktionsumgebung übernommen. Wenn Probleme erkannt werden, wird der Build eingefroren und dann an die Sicherheitsteams bei Microsoft Security weitergeleitet, um festzustellen, wo der „gefährliche“ Code in den Build gelangt ist.

## <a name="closed-and-locked-environment"></a>Geschlossene und gesperrte Umgebung
Standardmäßig werden auf Knoten und Gast-VMs in der Azure-Infrastruktur keine Benutzerkonten erstellt. Zudem sind auch standardmäßige Windows-Administratorkonten deaktiviert. Administratoren des Azure Live Support können sich – bei ordnungsgemäßer Authentifizierung – an diesen Computern anmelden und das Azure-Produktionsnetzwerk für Notfallreparaturen verwalten.

## <a name="azure-sql-database-authentication"></a>Azure SQL-Datenbank-Authentifizierung
Wie bei jeder Implementierung von SQL Server muss die Verwaltung von Benutzerkonten streng kontrolliert werden. Azure SQL-Datenbank unterstützt nur SQL Server-Authentifizierung. Als Ergänzung zum Datensicherheitsmodell des Kunden sollten zudem Benutzerkonten verwendet werden, die sichere Kennwörter haben und mit spezifischen Rechten konfiguriert sind.

## <a name="acls-and-firewalls-between-the-microsoft-corporate-network-and-an-azure-cluster"></a>ACLs und Firewalls zwischen dem Microsoft-Unternehmensnetzwerk und einem Azure-Cluster
Zugriffssteuerungsliste (Access Control Lists, ACLs) bzw. Firewalls zwischen der Dienstplattform und dem Microsoft-Unternehmensnetzwerk schützen die SQL-Datenbankinstanzen vor nicht autorisiertem Insiderzugriff. Darüber hinaus können nur Benutzer von IP-Adressbereichen des Microsoft-Unternehmensnetzwerks aus auf den Verwaltungsendpunkt der Windows Fabric-Plattform zugreifen.

## <a name="acls-and-firewalls-between-nodes-in-a-sql-database-cluster"></a>ACLs und Firewalls zwischen Knoten in einem SQL-Datenbankcluster
Als zusätzlicher Schutz im Rahmen der Strategie für tiefgreifende Verteidigung wurden ACLs bzw. eine Firewall zwischen Knoten in einem SQL-Datenbankcluster implementiert. Die gesamte Kommunikation innerhalb des Windows Fabric-Plattformclusters sowie der gesamte ausgeführte Code sind vertrauenswürdig.

## <a name="custom-monitoring-agents"></a>Benutzerdefinierte Überwachungs-Agents
SQL-Datenbank setzt benutzerdefinierte Überwachungs-Agents (Monitoring Agents, MAs), auch Watchdogs genannt, ein, um die Integrität des SQL-Datenbankclusters zu überwachen.

## <a name="web-protocols"></a>Webprotokolle

### <a name="role-instance-monitoring-and-restart"></a>Überwachung und Neustart von Rolleninstanzen
Azure stellt sicher, dass für alle bereitgestellten und ausgeführten Rollen (Webrolle mit Internetzugriff oder Workerrolle für Back-End-Verarbeitung) eine dauerhafte Integritätsüberwachung erfolgt, um zu gewährleisten, dass diese auf effektive und effiziente Weise die Dienste liefern, in denen sie bereitgestellt wurden. Wenn Integrität einer Rolle entweder durch einen kritischen Fehler in der gehosteten Anwendung oder aufgrund eines Konfigurationsproblems in der Rolleninstanz selbst nicht mehr gewährleistet ist, erkennt FC das Problem in der Rolleninstanz und initiiert einen Korrekturstatus.

### <a name="compute-connectivity"></a>Compute-Konnektivität
Azure stellt sicher, dass die bereitgestellte Anwendung bzw. der bereitgestellte Dienst über webbasierte Standardprotokolle erreichbar ist. Virtuelle Instanzen der Webrollen mit Internetzugriff verfügen über externe Internetkonnektivität und sind für Webbenutzer direkt erreichbar. Virtuelle Instanzen der Workerrolle für die Back-End-Verarbeitung verfügen über externe Internetkonnektivität, doch kann ein externer Webbenutzer nicht direkt darauf zugreifen, um die Vertraulichkeit und Integrität der Vorgänge zu schützen, die von Workerrollen für die öffentlich zugänglichen virtuellen Instanzen der Webrolle ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über die Sicherung der Azure-Infrastruktur seitens Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)
