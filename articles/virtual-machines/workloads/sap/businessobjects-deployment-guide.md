---
title: Bereitstellung der SAP BusinessObjects BI-Plattform in Azure | Microsoft-Dokumentation
description: Planen, Bereitstellen und Konfigurieren der SAP BusinessObjects BI-Plattform in Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 0c2deb1ae1d41f8daaed8856f97c4b458930c616
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484378"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Planungs- und Implementierungsleitfaden für die SAP BusinessObjects BI-Plattform in Azure

## <a name="overview"></a>Übersicht

Zweck dieses Leitfadens ist es, Richtlinien für die Planung, Bereitstellung und Konfiguration der SAP BusinessObjects BI-Plattform, auch bekannt als SAP BOBI-Plattform, in Azure bereitzustellen. Dieser Leitfaden soll allgemeine Azure-Dienste und -Features behandeln, die für die SAP BOBI-Plattform relevant sind. Dieser Leitfaden ist keine vollständige Liste aller möglichen Konfigurationsoptionen. Er behandelt Lösungen, die für typische Bereitstellungsszenarien üblich sind.

Dieser Leitfaden ist nicht dazu gedacht, die standardmäßigen Installations- und Administrationsleitfäden für die SAP BOBI-Plattform, die Betriebssystem- oder die Datenbankdokumentation zu ersetzen.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planen und Implementieren der SAP BusinessObjects BI-Plattform in Azure

Microsoft Azure bietet eine breite Palette von Diensten wie Compute, Speicher, Netzwerk und viele andere, mit denen Unternehmen ihre Anwendungen ohne langwierige Beschaffungszyklen erstellen können. Virtuelle Azure-Computer (VM) helfen Unternehmen auf der Grundlage ihrer Geschäftsanforderungen bei der Bereitstellung von skalierbaren On-Demand-Computeressourcen für verschiedene SAP-Anwendungen wie SAP NetWeaver-basierte Anwendungen, SAP Hybris, SAP BusinessObjects BI-Plattform. Azure unterstützt auch die standortübergreifende Konnektivität, wodurch Unternehmen virtuelle Azure-Computer in ihre lokale Domänen, privaten Clouds und ihre SAP-Systemumgebung integrieren können.

Dieses Dokument enthält Hinweise zur Planung und Überlegungen zur Implementierung der SAP BusinessObjects BI-Plattform in Azure. Es ergänzt die SAP-Installationsdokumentation und die SAP-Hinweise, die die primäre Ressource für Installationen und Bereitstellungen von SAP BOBI auf verschiedenen Plattformen darstellen.

### <a name="architecture-overview"></a>Übersicht über die Architektur

Die SAP BusinessObjects BI-Plattform ist ein eigenständiges System, das auf einem einzelnen virtuellen Azure-Computer existieren oder in einem Cluster aus vielen virtuellen Azure-Computern skaliert werden kann, auf denen verschiedene Komponenten ausgeführt werden. Die SAP BOBI-Plattform besteht aus sechs konzeptionellen Ebenen: Clientebene, Webebene, Verwaltungsebene, Speicherebene, Verarbeitungsebene und Datenebene. (Weitere Informationen zu den einzelnen Ebenen finden Sie im Administratorhandbuch im Hilfeportal [SAP BusinessObjects Business Intelligence-Plattform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US).) Nachfolgend sind die allgemeinen Angaben zu den einzelnen Ebenen aufgeführt:

- **Clientebene:** Sie enthält alle Desktopclientanwendungen, die mit der BI-Plattform interagieren, um verschiedene Arten von Berichts-, Analyse- und Verwaltungsfunktionen bereitzustellen.
- **Webebene:** Sie enthält Webanwendungen, die auf JAVA-Webanwendungsservern bereitgestellt werden. Webanwendungen stellen Endbenutzern die Funktionalität der BI-Plattform über einen Webbrowser zur Verfügung.
- **Verwaltungsebene:** Sie koordiniert und steuert alle Komponenten, aus denen die BI-Plattform besteht. Sie umfasst den zentralen Verwaltungsserver (CMS) und den Ereignisserver sowie die zugehörigen Dienste.
- **Speicherebene:** Sie ist für die Bearbeitung von Dateien, z. B. Dokumente und Berichte, zuständig. Außerdem übernimmt sie die Zwischenspeicherung von Berichten, um Systemressourcen zu sparen, wenn Benutzer auf Berichte zugreifen.
- **Verarbeitungsebene:** Sie analysiert Daten und erzeugt Berichte und andere Ausgabetypen. Dies ist die einzige Ebene, die auf die Datenbanken zugreift, die Berichtsdaten enthalten.
- **Datenebene:** Sie besteht aus den Datenbankservern, die die CMS-Systemdatenbanken und den Überwachungsdatenspeicher hosten.

Die SAP BI-Plattform besteht aus einer Sammlung von Servern, die auf einem oder mehreren Hosts ausgeführt werden. Es ist wichtig, dass Sie die richtige Bereitstellungsstrategie auf Basis von Dimensionierung, Geschäftsanforderung und Umgebungstyp auswählen. Für kleine Installationen, z. B. für Entwicklung oder Test, können Sie eine einzelne Azure Virtual Machine-Instanz für Webanwendungsserver, Datenbankserver und alle BI-Plattformserver verwenden. Falls Sie das DBaaS-Angebot (Database-as-a-Service) von Azure verwenden, wird der Datenbankserver getrennt von anderen Komponenten ausgeführt. Für mittlere und große Installationen können Server auf mehreren virtuellen Azure-Computern ausgeführt werden.

In der folgenden Abbildung ist die Architektur der umfangreichen Bereitstellung der SAP BOBI-Plattform auf den virtuellen Azure-Computern dargestellt, wobei jede Komponente verteilt und in Verfügbarkeitsgruppen positioniert wird, die im Falle einer Dienstunterbrechung ein Failover unterstützen.

![Architektur der SAP BusinessObjects BI-Plattform in Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Architekturdetails

- Load Balancer

  Bei der Bereitstellung von SAP BOBI mit mehreren Instanzen werden Webanwendungsserver (oder Webebene) auf zwei oder mehr Hosts ausgeführt. Um die Benutzerauslastung gleichmäßig auf die Webserver zu verteilen, können Sie einen Lastenausgleich zwischen Endbenutzern und Webservern verwenden. In Azure können Sie entweder [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) oder [Azure Application Gateway](../../../application-gateway/overview.md) verwenden, um den Datenverkehr zu Ihren Webservern zu verwalten.

- Webanwendungsserver

  Der Webserver hostet die Webanwendungen der SAP BOBI-Plattform, z. B. CMC und BI Launch Pad. Um Hochverfügbarkeit für Webserver zu erreichen, müssen Sie mindestens zwei Webanwendungsserver bereitstellen, um Redundanz und Lastenausgleich zu verwalten. In Azure können diese Webanwendungsserver entweder in Verfügbarkeitsgruppen oder Verfügbarkeitszonen platziert werden, um eine bessere Verfügbarkeit zu erreichen.

  Tomcat ist die Standardwebanwendung für die SAP BI-Plattform. Aktivieren Sie die Sitzungsreplikation mithilfe des [Interceptors für die statische Mitgliedschaft](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) in Azure, um Hochverfügbarkeit für Tomcat zu erreichen. Sie stellt sicher, dass Benutzer auf die SAP BI-Webanwendung auch dann zugreifen können, wenn der Tomcat-Dienst unterbrochen ist.

  > [!Important]
  > Tomcat verwendet standardmäßig Multicast-IP und Multicast-Port für das Clustering, was in Azure nicht unterstützt wird (SAP-Hinweis [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI-Plattformserver

  BI-Plattformserver umfassen alle Dienste, die Teil der SAP BOBI-Anwendung sind (Verwaltungsebene, Verarbeitungsebene und Speicherebene). Wenn ein Webserver eine Anforderung empfängt, erkennt er jeden Server der BI-Plattform (insbesondere alle CMS-Server in einem Cluster) und führt automatisch einen Lastenausgleich für ihre Anforderungen aus. Falls einer der Hosts der BI-Plattform ausfällt, sendet der Webserver automatisch Anforderungen an einen anderen Host.

  Um Hochverfügbarkeit oder Redundanz für die BI-Plattform zu erreichen, müssen Sie die Anwendung auf mindestens zwei virtuellen Azure-Computern bereitstellen. Je nach Dimensionierung können Sie Ihre BI-Plattform so skalieren, dass sie auf mehr virtuellen Azure-Computern ausgeführt werden kann.

- File Repository Server (FRS)

  File Repository Server enthält alle Berichte und anderen BI-Dokumente, die erstellt wurden. Bei einer Bereitstellung mit mehreren Instanzen werden die BI-Plattformserver auf mehreren virtuellen Computern ausgeführt, und jeder virtuelle Computer sollte Zugriff auf diese Berichte und andere BI-Dokumente haben. Daher muss ein Dateisystem von allen Servern der BI-Plattform gemeinsam genutzt werden.

  In Azure können Sie entweder [Azure Files Premium](../../../storage/files/storage-files-introduction.md) oder [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) für File Repository Server verwenden. Beide dieser Azure-Dienste verfügen über eine integrierte Redundanz.

  > [!Important]
  > Das SMB-Protokoll für Azure Files ist allgemein verfügbar, aber die Unterstützung des NFS-Protokolls für Azure Files befindet sich derzeit in der Vorschauversion. Weitere Informationen finden Sie unter [Unterstützung von NFS 4.1 für Azure Files befindet sich jetzt in der Vorschau](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

- CMS und Überwachungsdatenbank
  
  Die SAP BOBI-Plattform benötigt eine Datenbank zur Speicherung ihrer Systemdaten, die als CMS-Datenbank bezeichnet wird. Sie wird verwendet, um Informationen zur BI-Plattform wie Benutzer, Server, Ordner, Dokument, Konfiguration und Authentifizierungsdetails zu speichern.

  Azure bietet das [MySQL Database](https://azure.microsoft.com/en-us/services/mysql/)- und DBaaS-Angebot ([Azure SQL-Datenbank](https://azure.microsoft.com/en-us/services/sql-database/)), das für die CMS- und Überwachungsdatenbank verwendet werden kann. Da es sich um ein PaaS-Angebot handelt, müssen sich Kunden nicht um Betrieb, Verfügbarkeit und Wartung der Datenbanken kümmern. Kunden können auch ihre eigene Datenbank für CMS und Überwachungsrepository auf der Grundlage ihrer Geschäftsanforderungen auswählen.

## <a name="support-matrix"></a>Unterstützungsmatrix

In diesem Abschnitt wird die Unterstützbarkeit verschiedener SAP BOBI-Komponenten wie die SAP BusinessObjects BI-Plattformversion, das Betriebssystem und die Datenbanken in Azure beschrieben.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI-Plattform

Azure-IaaS (Infrastructure-as-a-Service) ermöglicht Ihnen die Bereitstellung und Konfiguration der SAP BusinessObjects BI-Plattform für Azure Compute. Es unterstützt die folgende Version der SAP BOBI-Plattform:

- SAP BusinessObjects BI-Plattform 4.3
- SAP BusinessObjects BI-Plattform 4.2 SP04+
- SAP BusinessObjects BI-Plattform 4.2 4.1 SP05+

Die SAP BI-Plattform wird unter verschiedenen Betriebssystemen und Datenbanken ausgeführt. Die Unterstützbarkeit der SAP BOBI-Plattform zwischen Betriebssystem- und Datenbankversion kann in der [Produktverfügbarkeitsmatrix](https://apps.support.sap.com/sap/support/pam) für SAP BOBI gefunden werden.

### <a name="operating-system"></a>Betriebssystem

Azure unterstützt folgende Betriebssysteme für die Bereitstellung der SAP BusinessObjects BI-Plattform.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

Die Betriebssystemversionen, die in der [Produktverfügbarkeitsmatrix (PAM) für die SAP BusinessObjects BI-Plattform](https://support.sap.com/pam) aufgeführt sind, werden unterstützt, sofern sie mit der Ausführung in der Azure-Infrastruktur kompatibel sind.

### <a name="databases"></a>Datenbanken

Die BI-Plattform benötigt eine Datenbank für CMS und Überwachungsdatenspeicher, die für alle unterstützten Datenbanken installiert werden können, die in der [Produktverfügbarkeitsmatrix von SAP](https://support.sap.com/pam) aufgeführt sind, die Folgendes umfasst:

- Microsoft SQL Server

- [Azure SQL-Datenbank](https://azure.microsoft.com/en-us/services/sql-database/) (Unterstützte Datenbank nur für SAP BOBI-Plattform unter Windows)

  Dies ist eine vollständig verwaltete SQL Server-Datenbank-Engine, basierend auf der letzten stabilen Enterprise Edition von SQL Server. Azure SQL-Datenbank übernimmt die meisten Datenbankverwaltungsfunktionen wie Upgrades, Patches und Überwachung ohne Benutzereingriff. Mithilfe von Azure SQL-Datenbank können Sie eine hochverfügbare und hochleistungsfähige Datenspeicherebene für Anwendungen und Lösungen in Azure erstellen. Weitere Informationen finden Sie in der Dokumentation [Azure SQL-Datenbank](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md).

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (Befolgen Sie dieselben Kompatibilitätsrichtlinien, wie sie für MySQL AB in der SAP PAM erwähnt wurden).

  Es handelt sich um einen relationalen Datenbankdienst, der von der MySQL Community-Edition unterstützt wird. Das vollständig verwaltete DBaaS-Angebot (Database-as-a-Service) kann geschäftskritische Workloads mit vorhersagbarer Leistung und dynamischer Skalierung verarbeiten. Es verfügt über integrierte Hochverfügbarkeit, automatische Sicherungen, Softwarepatches, automatische Fehlererkennung und Zeitpunktwiederherstellung für bis zu 35 Tage, wodurch die Betriebsaufgaben erheblich reduziert werden. Weitere Informationen finden Sie in der Dokumentation zu [Azure Database for MySQL](../../../mysql/overview.md).

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (Informationen zu Version und Einschränkung finden Sie im SAP-Hinweis [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Dieses Dokument veranschaulicht die Richtlinien zur Bereitstellung der **SAP BOBI-Plattform unter Windows mit Azure SQL-Datenbank** und der **SAP BOBI-Plattform unter Linux mit Azure Database for MySQL**. Es ist auch unser empfohlener Ansatz für die Ausführung der SAP BusinessObjects BI-Plattform in Azure.

## <a name="sizing"></a>Festlegen der Größe

Die Dimensionierung ist ein Prozess, bei dem die Hardwareanforderungen zur effizienten Ausführung der Anwendung bestimmt werden. Für die SAP BOBI-Plattform muss die Dimensionierung mithilfe des SAP-Dimensionierungstools namens [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer) durchgeführt werden. Das Tool stellt die SAPS auf der Grundlage der Eingabe bereit, die dann zu zertifizierten virtuellen Azure-Computertypen für SAP zugeordnet werden müssen. Im SAP-Hinweis [1928533](https://launchpad.support.sap.com/#/notes/1928533) finden Sie die Liste der unterstützten SAP-Produkte und Azure-VM-Typen zusammen mit SAPS. Weitere Informationen zur Dimensionierung finden Sie im [Leitfaden zur SAP BI-Dimensionierung](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Für den Speicherbedarf der SAP BOBI-Plattform bietet Azure verschiedene Typen von [Managed Disks](../../managed-disks-overview.md). Für das SAP BOBI-Installationsverzeichnis wird empfohlen, einen verwalteten Premium-Datenträger zu verwenden, und für die auf virtuellen Computern ausgeführte Datenbank befolgen Sie die Anweisungen in [DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md).

Azure unterstützt zwei DBaaS-Angebote für die Datenebene der SAP BOBI-Plattform: [Azure SQL-Datenbank](https://azure.microsoft.com/en-us/services/sql-database) (BI-Anwendung unter Windows) und [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (BI-Anwendung unter Linux und Windows). Somit können Sie, basierend auf dem Ergebnis der Dimensionierung, das Kaufmodell wählen, das Ihren Anforderungen am besten entspricht.

> [!Tip]
> Für eine schnelle Größenbestimmung berücksichtigen Sie, dass 800 SAPS = 1 vCPU, während Sie das SAPS-Ergebnis der Datenbankebene der SAP BOBI-Plattform zu Azure-DBaaS (Database-as-a-Service) zuordnen (Azure SQL-Datenbank oder Azure Database for MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Dimensionierungsmodelle für Azure SQL-Datenbank

Für Azure SQL-Datenbank gibt es die folgenden drei Kaufmodelle:

- vCore-basiertes Modell

  Damit können Sie die Anzahl der virtuellen Kerne, die Arbeitsspeichergröße sowie die Größe und Geschwindigkeit des Speichers auswählen. Mit dem vCore-basierten Kaufmodell können Sie auch den [Azure-Hybridvorteil für SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) nutzen, um Kosten einzusparen. Dieses Modell eignet sich für Kunden, für die Flexibilität, Kontrolle und Transparenz im Vordergrund stehen.

  Im vCore-basierten Modell werden drei [Dienstebenenoptionen](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) angeboten – „Universell“, „Unternehmenskritisch“ und „Hyperscale“. Die Dienstebene definiert die Speicherarchitektur, die Speicherplatz- und E/A-Grenzwerte sowie Optionen für die Geschäftskontinuität im Zusammenhang mit der Verfügbarkeit und Notfallwiederherstellung. Nachfolgend finden Sie allgemeine Details zu den einzelnen Optionen der Dienstebene:

  1. **Universell** – Diese Dienstebene ist am besten für geschäftliche Workloads geeignet. Sie bietet budgetorientierte, ausgewogene und skalierbare Compute- und Speicheroptionen. Weitere Informationen finden Sie unter [Ressourcenoptionen und -grenzwerte](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. **Unternehmenskritisch** – Diese Dienstebene bietet Geschäftsanwendungen die höchste Resilienz gegenüber Fehlern durch die Verwendung mehrerer isolierter Replikate sowie die höchste E/A-Leistung pro Datenbankreplikat. Weitere Informationen finden Sie unter [Ressourcenoptionen und -grenzwerte](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. **Hyperscale** – Diese Dienstebene ist am besten für geschäftliche Workloads mit hohen Anforderungen an skalierbaren Speicher und Leseskalierung geeignet. Sie bietet eine höhere Ausfallsicherheit, da mehrere isolierte Datenbankreplikate konfiguriert werden können. Weitere Informationen finden Sie unter [Ressourcenoptionen und -grenzwerte](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- DTU-basiertes Modell

  Das DTU-basierte Kaufmodell bietet zur Unterstützung einfacher und komplexer Datenbankworkloads eine Mischung aus Compute-, Arbeitsspeicher- und E/A-Ressourcen auf drei Dienstebenen. Die Computegrößen der einzelnen Ebenen bieten unterschiedliche Ressourcenzusammenstellungen, denen Sie zusätzliche Speicherressourcen hinzufügen können. Es eignet sich am besten für Kunden, die einfache, vorkonfigurierte Ressourcenoptionen benötigen.

  [Dienstebenen](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) beim DTU-basierten Kaufmodell unterscheiden sich durch eine Reihe von Computegrößen mit einer festen Menge an integriertem Speicher, einem festen Aufbewahrungszeitraum für Sicherungen und einem festen Preis.

- Serverlos

  Das serverlose Modell, welches die Computekapazität basierend auf dem Workloadbedarf skaliert und verwendete Computeressourcen nach Menge pro Sekunde abrechnet. Im Tarif „Serverloses Computing“ werden Datenbanken während inaktiver Zeiten, in denen nur Speicher in Rechnung gestellt wird, automatisch angehalten und bei Fortsetzen der Aktivität automatisch wieder in Betrieb genommen. Weitere Informationen finden Sie unter [Ressourcenoptionen und -grenzwerte](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  Sie ist eher für die wechselnde, unvorhersehbare Nutzung mit niedriger durchschnittlicher Computenutzung im Zeitverlauf geeignet. Daher kann dieses Modell für die SAP BOBI-Bereitstellung in einer Nicht-Produktionsumgebung verwendet werden.

> [!Note]
> Für SAP BOBI ist es praktisch, das auf vCore-basierte Modell zu verwenden und je nach Geschäftsanforderung entweder die Dienstebene „Universell“ oder „Unternehmenskritisch“ zu wählen.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Dimensionierungsmodelle für Azure Database for MySQL

Azure Database for MySQL verfügt über drei unterschiedliche Tarife. Sie unterscheiden sich anhand der Menge an Computeleistung in virtuellen Kernen, der Menge des Arbeitsspeichers pro virtuellem Kern und der zum Speichern der Daten verwendeten Speichertechnologie. Nachfolgend finden Sie die allgemeinen Details zu den Optionen und weitere Einzelheiten zu den verschiedenen Attributen unter [Tarif](../../../mysql/concepts-pricing-tiers.md) für Azure Database for MySQL.

- Basic

  Für Zielworkloads mit geringen Anforderungen an Compute- und E/A-Leistung.

- Universell

  Für die meisten Unternehmensworkloads mit ausgeglichenen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz geeignet.

- Arbeitsspeicheroptimiert

  Für Hochleistungs-Datenbankworkloads geeignet, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen.

> [!Note]
> Für SAP BOBI ist es zweckmäßig, den Tarif „Universell“ oder „Arbeitsspeicheroptimiert“ auf der Grundlage der Unternehmensworkload zu verwenden.

## <a name="azure-resources"></a>Azure-Ressourcen

### <a name="choosing-regions"></a>Auswählen von Regionen

Azure-Region bezeichnet ein Rechenzentrum oder eine Sammlung von Rechenzentren, die die Infrastruktur zum Ausführen und Hosten verschiedener Azure-Dienste enthält. Diese Infrastruktur umfasst eine Vielzahl von Knoten, die als Computeknoten oder Speicherknoten fungieren bzw. Netzwerkfunktionen ausführen. Nicht alle Regionen bieten dieselben Dienste.

Die SAP BI-Plattform enthält verschiedene Komponenten, die für ihre Datenebene möglicherweise bestimmte VM-Typen oder Speicher wie Azure Files oder Azure NetApp Files oder DBaaS (Database-as-a-Service) erfordern, die in bestimmten Regionen möglicherweise nicht verfügbar sind. Die genauen Informationen zu VM-Typen, Azure Storage-Typen oder anderen Azure-Diensten finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/en-us/global-infrastructure/services/). Wenn Sie Ihre SAP-Systeme bereits in Azure ausführen, haben Sie wahrscheinlich Ihre Azure-Region identifiziert. In diesem Fall müssen Sie zunächst untersuchen, ob die erforderlichen Dienste in diesen Regionen verfügbar sind, um über die Architektur der SAP BI-Plattform zu entscheiden.

### <a name="availability-zones"></a>Verfügbarkeitszonen

Verfügbarkeitszonen sind physisch getrennte Standorte in einer Azure-Region. Jede Verfügbarkeitszone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren.

Um auf jeder Ebene Hochverfügbarkeit für die SAP BI-Plattform zu erreichen, können Sie virtuelle Computer über die Verfügbarkeitszone verteilen, indem Sie ein Hochverfügbarkeitsframework implementieren, das die beste Vereinbarung zum Servicelevel in Azure bieten kann. Überprüfen Sie für diese SLA für virtuelle Computer in Azure die neueste Version der [SLAs für virtuelle Computer](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

Für die Datenebene bietet der Azure-DBaaS-Dienst (Database-as-a-Service) standardmäßig ein Hochverfügbarkeitsframework. Sie müssen nur die Region und den Dienst auswählen, der über Funktionen für Hochverfügbarkeit, Redundanz und Resilienz verfügt, um die Datenbankdowntime aufgrund von geplanten und ungeplanten Ausfällen zu minimieren, ohne dass Sie zusätzliche Komponenten konfigurieren müssen. Weitere Informationen zur Vereinbarung zum Servicelevel des unterstützten DBaaS-Angebots in Azure finden Sie unter [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md) und [Hochverfügbarkeit für Azure SQL-Datenbank](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Verfügbarkeitsgruppen

Eine Verfügbarkeitsgruppe ist eine Funktion zur logischen Gruppierung, um virtuelle Computerressourcen (VM) bei ihrer Bereitstellung voneinander zu isolieren. Azure stellt sicher, dass die virtuellen Computer innerhalb einer Verfügbarkeitsgruppe auf mehrere physische Server, Computeracks, Speichereinheiten und Netzwerkswitches verteilt werden. Bei einem Hardware- oder Softwarefehler ist nur ein Teil der virtuellen Computer betroffen, sodass Ihre Lösung weiterhin verwendet werden kann. Somit verteilt Azure Fabric Controller, wenn virtuelle Computer in Verfügbarkeitsgruppen platziert werden, die VMs auf verschiedene [Fehler-](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) und [Upgrade](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains)-Domänen, um zu verhindern, dass alle VMs aufgrund der Wartung oder des Ausfalls der Infrastruktur innerhalb einer Fehlerdomäne nicht zugänglich sind.

Die SAP BI-Plattform enthält viele verschiedene Komponenten, und beim Entwurf der Architektur müssen Sie sicherstellen, dass jede dieser Komponenten hinsichtlich jeglicher Störungen resilient ist. Dies kann erreicht werden, indem virtuelle Azure-Computer jeder Komponente innerhalb von Verfügbarkeitsgruppen platziert werden. Bedenken Sie, dass Sie beim Kombinieren von VMs verschiedener VM-Familien innerhalb einer Verfügbarkeitsgruppe auf Probleme stoßen können, die Sie daran hindern, einen bestimmten VM-Typ in eine solche Verfügbarkeitsgruppe aufzunehmen. Verwenden Sie also separate Verfügbarkeitsgruppen für die Webanwendung und die BI-Anwendung für die SAP BI-Plattform, wie in der Übersicht zur Architektur hervorgehoben.

Die Anzahl der von einer Azure-Verfügbarkeitsgruppe innerhalb einer Azure-Skalierungseinheit verwendbaren Update- und Fehlerdomänen ist ebenfalls begrenzt. Wenn Sie einer einzelnen Verfügbarkeitsgruppe immer weiter virtuelle Computer hinzufügen, werden schließlich mindestens zwei virtuelle Computer in dieselbe Fehler- oder Updatedomäne aufgenommen. Weitere Informationen finden Sie im Abschnitt [Azure-Verfügbarkeitsgruppen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) des Dokuments zur Planung und Implementierung von virtuellen Azure-Computern für SAP.

Lesen Sie zum besseren Verständnis des Konzepts von Azure-Verfügbarkeitsgruppen und der Beziehung von Verfügbarkeitsgruppen zu Fehler- und Upgradedomänen den Artikel zum [Verwalten der Verfügbarkeit](../../manage-availability.md).

> [!Important]
> Die Konzepte von Azure-Verfügbarkeitszonen und Azure-Verfügbarkeitsgruppen schließen sich gegenseitig aus. Dies bedeutet, dass Sie entweder zwei oder mehrere VMs in einer bestimmten Verfügbarkeitszone oder einer Azure-Verfügbarkeitsgruppe bereitstellen können, jedoch nicht in beiden.

### <a name="virtual-machines"></a>Virtuelle Computer

Azure Virtual Machine ist ein Dienst, der es Ihnen ermöglicht, benutzerdefinierte Images als IaaS-Instanzen (Infrastructure-as-a-Service) für Azure bereitzustellen. Sie vereinfacht die Wartung und den Betrieb von Anwendungen durch die Bereitstellung von bedarfsgesteuerten Server- und Speicherressourcen zum Hosten, Skalieren und Verwalten von Webanwendungen und verbundenen Anwendungen.

Azure bietet verschiedene virtuelle Computer für alle Ihre Anwendungsanforderungen. Aber für SAP-Workloads hat Azure die Auswahlmöglichkeiten auf verschiedene VM-Familien beschränkt, die für SAP-Workloads und insbesondere für SAP HANA-Workloads geeignet sind. Weitere Informationen finden Sie unter [Welche SAP-Software wird für Azure-Bereitstellungen unterstützt?](sap-supported-product-on-azure.md).

Basierend auf der Dimensionierung der SAP BI-Plattform müssen Sie Ihre Anforderung zu Azure Virtual Machine zuordnen, die in Azure für das SAP-Produkt unterstützt wird. SAP-Hinweis [1928533](https://launchpad.support.sap.com/#/notes/1928533) ist ein guter Startpunkt, der die unterstützten Azure VM-Typen für SAP-Produkte unter Windows und Linux auflistet. Außerdem ist zu beachten, dass über die Auswahl der ausschließlich unterstützten VM-Typen hinaus auch zu prüfen ist, ob diese VM-Typen in einer bestimmten Region verfügbar sind. Sie können die Verfügbarkeit des VM-Typs auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/) prüfen. Informationen zum Auswählen des Preismodells finden Sie unter [Virtuelle Azure-Computer für SAP-Workloads](planning-guide.md#azure-virtual-machines-for-sap-workload).

### <a name="storage"></a>Storage

Azure Storage ist ein von Azure verwalteter Clouddienst, über den hoch verfügbarer, sicherer, stabiler, skalierbarer und redundanter Speicher bereitgestellt wird. Einige der Speichertypen sind für SAP-Szenarios nur eingeschränkt verwendbar. Dagegen sind verschiedene Azure Storage-Typen für bestimmte SAP-Workloadszenarien gut geeignet und optimiert. Weitere Informationen finden Sie im Leitfaden [Azure-Speichertypen für SAP-Workloads](planning-guide-storage.md), in dem verschiedene Speicheroptionen hervorgehoben werden, die für SAP geeignet sind.

Azure Storage stellt Kunden verschiedene Speichertypen zur Verfügung und Informationen dazu finden Sie im Artikel [Welche Datenträgertypen stehen in Azure zur Verfügung?](../../disks-types.md). Die SAP BOBI-Plattform verwendet den folgenden Azure-Speicher, um die Anwendung zu erstellen:

- Verwaltete Azure-Datenträger

  Dabei handelt es sich um ein Speichervolume auf Blockebene, das von Azure verwaltet wird. Sie können die Datenträger für Anwendungsserver und Datenbanken der SAP BOBI-Plattform verwenden, wenn sie auf virtuellen Azure-Computern installiert sind. Es sind verschiedene Arten von [verwalteten Azure-Datenträgern](../../managed-disks-overview.md) verfügbar, aber es wird empfohlen, [SSD Premium](../../disks-types.md#premium-ssd) für Anwendungen und Datenbanken der SAP BOBI-Plattform zu verwenden.

  Im folgenden Beispiel wird SSD Premium für das Installationsverzeichnis der BOBI-Plattform verwendet. Für Datenbanken, die auf einem virtuellen Computer installiert sind, können Sie gemäß den Richtlinien verwaltete Datenträger für Daten- und Protokollvolumes verwenden. CMS- und Überwachungsdatenbanken sind in der Regel klein und haben nicht dieselben Anforderungen an die Speicherleistung wie andere SAP OLTP/OLAP-Datenbanken.

- Azure Files Premium oder Azure NetApp Files

  Auf der SAP BOBI-Plattform bezieht sich File Repository Server (FRS) auf die Datenträgerverzeichnisse, in denen Inhalte wie Berichte, Universen und Verbindungen gespeichert sind, die von allen Anwendungsservern dieses Systems verwendet werden. [Azure Files Premium](../../../storage/files/storage-files-introduction.md) oder [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) können als gemeinsames Dateisystem für den FRS von SAP BOBI Anwendungen verwendet werden. Da dieses Speicherangebot nicht in allen Regionen verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/en-us/global-infrastructure/services/) aktuelle Informationen.

  Wenn der Dienst in Ihrer Region nicht verfügbar ist, können Sie einen NFS-Server erstellen, über den Sie das Dateisystem für die SAP BOBI-Anwendung freigeben können. Sie müssen aber auch seine Hochverfügbarkeit berücksichtigen.

![Speicherlayout der SAP BusinessObjects BI-Plattform in Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Netzwerk

SAP BOBI ist eine BI-Plattform für die Berichterstellung und Analyse, die keine Geschäftsdaten speichert. Daher ist das System mit anderen Datenbankservern verbunden, von denen es alle Daten abruft und den Benutzern Erkenntnisse gewährt. Azure bietet eine Netzwerkinfrastruktur, die die Zuordnung aller Szenarien ermöglicht, die mit der SAP BI-Plattform realisiert werden können, z. B. die Verbindung mit lokalen Systemen, Systemen in verschiedenen virtuellen Netzwerken und andere. Weitere Informationen finden Sie unter [Microsoft Azure-Netzwerke für die SAP-Workload](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Beim DBaaS-Angebot (Database-as-a-Service) verfügt jede neu erstellte Datenbank (Azure SQL-Datenbank oder Azure Database for MySQL) über eine Firewall, die alle externen Verbindungen blockiert. Um den Zugriff auf den DBaaS-Dienst von virtuellen Computern der BI-Plattform aus zu ermöglichen, müssen Sie eine oder mehrere Firewallregeln auf Serverebene angeben, um den Zugriff auf Ihren DBaaS-Server zu ermöglichen. Weitere Informationen finden Sie im Abschnitt [Firewallregeln](../../../mysql/concepts-firewall-rules.md) für Azure Database for MySQL und im Abschnitt [Kontrolle des Netzwerkzugriffs](../../../azure-sql/database/network-access-controls-overview.md) für Azure SQL-Datenbank.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellung der SAP BusinessObjects BI-Plattform unter Linux](businessobjects-deployment-guide-linux.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP](./dbms_guide_general.md)