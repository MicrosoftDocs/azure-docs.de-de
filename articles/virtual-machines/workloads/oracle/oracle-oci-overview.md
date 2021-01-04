---
title: Integrieren von Microsoft Azure mit Oracle Cloud Infrastructure | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu Lösungen, die in Microsoft Azure ausgeführte Oracle-Apps mit Datenbanken in Oracle Cloud Infrastructure (OCI) integrieren.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: e8e9bf23c236f805135d7d46b969c564975448ac
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965407"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Oracle-Anwendungslösungen mit Integration von Microsoft Azure und Oracle Cloud Infrastructure

Microsoft und Oracle haben sich zusammengetan, um cloudübergreifende Konnektivität mit geringer Wartezeit und hohem Durchsatz bereitzustellen, sodass Sie von den Vorteilen beider Clouds profitieren können. 

Dank dieser cloudübergreifenden Konnektivität können Sie eine mehrschichtige Anwendung partitionieren, um Ihre Datenbankschicht in Oracle Cloud Infrastructure (OCI) und die Anwendung sowie andere Schichten in Microsoft Azure auszuführen. Dies ist vergleichbar mit der Ausführung des gesamten Lösungsstapels in einer einzelnen Cloud. 

Wenn Sie Ihre Middleware (einschließlich WebLogic Server) in der Azure-Infrastruktur ausführen möchten, die Oracle-Datenbank jedoch in OCI ausgeführt wird, lesen Sie die Informationen unter [WebLogic Server: Azure-Anwendungen](oracle-weblogic.md).

Wenn Sie Oracle-Lösungen vollständig in der Azure-Infrastruktur bereitstellen möchten, lesen Sie [Oracle VM images and their deployment on Microsoft Azure](oracle-vm-solutions.md) (Oracle-VM-Images und deren Bereitstellung in Microsoft Azure).

## <a name="scenario-overview"></a>Übersicht über das Szenario

Mit cloudübergreifender Konnektivität können Sie die branchenführenden Anwendungen von Oracle sowie Ihre eigenen benutzerdefinierten Anwendungen auf virtuellen Azure-Computern ausführen und gleichzeitig von den Vorteilen gehosteter Datenbankdienste in OCI profitieren. 

Ab Mai 2020 sind die folgenden Anwendungen in einer cloudübergreifenden Konfiguration zertifiziert:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-Anwendungen
* Oracle Hyperion Financial Management

Das folgende Diagramm bietet einen allgemeinen Überblick über die verbundene Lösung. Der Einfachheit halber enthält das Diagramm nur eine Logik- und eine Datenschicht. Ihre Lösung kann je nach Anwendungsarchitektur auch weitere Schichten enthalten, etwa einen WebLogic Server-Cluster oder eine Webschicht in Azure. Weitere Informationen finden Sie in den folgenden Abschnitten.

![Übersicht über die Azure-OCI-Lösung](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Regionale Verfügbarkeit 

Die cloudübergreifende Konnektivität ist auf folgende Regionen beschränkt:
* Azure „USA, Osten“ (EastUS) und OCI „Ashburn, VA“ (USA, Osten)
* Azure „Vereinigtes Königreich, Süden“ (UKSouth) und OCI „London“ (Vereinigtes Königreich, Süden)
* Azure „Kanada, Mitte“ (CanadaCentral) und OCI „Toronto“ (Kanada, Südosten)
* Azure „Europa, Westen“ (WestEurope) und OCI „Amsterdam“ (Niederlande, Nordwesten)
* Azure „Japan, Osten“ (JapanEast) und OCI „Tokyo“ (Japan, Osten)
* Azure „USA, Wesen“ (WestUS) und OCI „San Jose“ (USA, Westen)

## <a name="networking"></a>Netzwerk

Unternehmenskunden entscheiden sich aus verschiedenen geschäftlichen und betrieblichen Gründen oftmals für eine Diversifizierung und stellen Workloads über mehrere Clouds bereit. Zur Diversifizierung verbinden Kunden Cloudnetzwerke über das Internet, per IPSec-VPN oder mithilfe der Direktverbindungslösung des Cloudanbieters über Ihr lokales Netzwerk. Die Verbindungsherstellung zwischen Cloudnetzwerken kann einen erheblichen Zeit- und Kostenaufwand bedeuten und hohe Investitionen für Entwurf, Beschaffung, Installation, Tests und Betrieb erfordern. 

Aus diesem Grund haben Oracle und Microsoft eine integrierte Multi-Cloud-Umgebung entwickelt. Zur Schaffung eines cloudübergreifenden Netzwerks wird eine Verbindung zwischen [ExpressRoute](../../../expressroute/expressroute-introduction.md) in Microsoft Azure und [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) in OCI hergestellt. Diese Konnektivität ist möglich, wenn sich ein Azure ExpressRoute-Peeringstandort in der Nähe oder am gleichen Peeringstandort befindet wie OCI FastConnect. Dies ermöglicht eine sichere und schnelle Verbindung zwischen den beiden Clouds ohne zusätzlichen Dienstanbieter.

Mit ExpressRoute und FastConnect können Kunden mittels Peering ein virtuelles Netzwerk in Azure mit einem virtuellen Cloudnetzwerk in OCI kombinieren – vorausgesetzt, der private IP-Adressbereich überschneidet sich nicht. Dank des Peerings der beiden Netzwerke kann eine Ressource im virtuellen Netzwerk mit einer Ressource im virtuellen Cloudnetzwerk von OCI kommunizieren, als befänden sich beide im gleichen Netzwerk.

## <a name="network-security"></a>Netzwerksicherheit

Netzwerksicherheit ist ein wichtiger Bestandteil jeder Unternehmensanwendung und ein zentraler Aspekt dieser Multi-Cloud-Lösung. Sämtlicher Datenverkehr über ExpressRoute und FastConnect wird über ein privates Netzwerk abgewickelt. Diese Konfiguration ermöglicht die sichere Kommunikation zwischen einem virtuellen Azure-Netzwerk und einem virtuellen Cloudnetzwerk von Oracle. Sie müssen keine öffentliche IP-Adresse für einen virtuellen Computer in Azure angeben. Und Sie benötigen auch kein Internetgateway in OCI. Die gesamte Kommunikation erfolgt über die private IP-Adresse der Computer.

Darüber hinaus können Sie [Sicherheitslisten](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) für Ihr virtuelles Cloudnetzwerk in OCI sowie Sicherheitsregeln einrichten, die mit [Netzwerksicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md) in Azure verknüpft sind. Diese Regeln dienen zum Steuern des Datenverkehrsfluss zwischen Computern in den virtuellen Netzwerken. Netzwerksicherheitsregeln können auf Computerebene, auf Subnetzebene sowie auf der Ebene des virtuellen Netzwerks hinzugefügt werden.

Die [Azure-Anwendungen von WebLogic Server](oracle-weblogic.md) erstellen jeweils eine Netzwerksicherheitsgruppe, die für die Verwendung mit den Portkonfigurationen von WebLogic Server vorkonfiguriert sind.
 
## <a name="identity"></a>Identity

Identität ist einer der Grundpfeiler der Partnerschaft zwischen Microsoft und Oracle. Für die Integration von [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) und [Azure Active Directory](../../../active-directory/index.yml) (Azure AD) wurde ein erheblicher Aufwand betrieben. Azure AD ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Ihre Benutzer können sich über Azure AD anmelden und auf verschiedene Ressourcen zugreifen. Darüber hinaus können Sie mithilfe von Azure AD Ihre Benutzer und deren Berechtigungen verwalten.

Diese Integration ermöglicht aktuell die Verwaltung an einem zentralen Ort (sprich: in Azure Active Directory). Azure AD synchronisiert sämtliche Änderungen im Verzeichnis mit dem entsprechenden Oracle-Verzeichnis und wird für einmaliges Anmelden bei cloudübergreifenden Oracle-Lösungen verwendet.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit einem [cloudübergreifenden Netzwerk](configure-azure-oci-networking.md) zwischen Azure und OCI. 

Weitere Informationen und Whitepaper zu OCI finden Sie in der [Dokumentation zu Oracle Cloud Infrastructure](https://docs.cloud.oracle.com/iaas/Content/home.htm).
