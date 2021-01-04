---
title: Architekturen für die Bereitstellung von Oracle-Apps auf virtuellen Azure-Computern | Microsoft-Dokumentation
description: Anwendungsarchitekturen zum Bereitstellen von Oracle-Apps, z. B. E-Business Suite, JD Edwards EnterpriseOne und PeopleSoft, auf virtuellen Microsoft Azure-Computern mit Datenbanken in Azure oder in der Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 07/18/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: aa481090e3483e58f6a88304e3e9d8c1a16df3c7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965917"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>Architekturen für die Bereitstellung von Oracle-Anwendungen in Azure

Microsoft und Oracle sind eine Kooperation eingegangen, um Kunden die Bereitstellung von Oracle-Anwendungen, z. B. Oracle E-Business Suite, JD Edwards EnterpriseOne und PeopleSoft, in der Cloud zu ermöglichen. Mit der Einführung der Vorschauversion der [privaten Netzwerkverbindung](configure-azure-oci-networking.md) zwischen Microsoft Azure und der Oracle Cloud Infrastructure (OCI) können Oracle-Anwendungen jetzt in Azure bereitgestellt werden, während die Back-End-Datenbanken in Azure oder in der OCI angeordnet sind. Oracle-Anwendungen können auch in Azure Active Directory integriert werden. Sie können einmaliges Anmelden einrichten, damit sich Benutzer mit ihren Azure AD-Anmeldeinformationen (Azure Active Directory) an der Oracle-Anwendung anmelden können.

Die OCI verfügt über mehrere Oracle-Datenbankoptionen für Oracle-Anwendungen, z. B. DBaaS, Exadata Cloud Service, Oracle RAC und Infrastructure-as-a-Service (IaaS). Derzeit ist Autonomous Database kein unterstütztes Back-End für Oracle-Anwendungen.

Es gibt [mehrere Optionen](oracle-overview.md) für die Bereitstellung von Oracle-Anwendungen in Azure, z. B. auch mit hoher Verfügbarkeit und Sicherheit. Darüber hinaus verfügt Azure über [Oracle-Datenbank-VM-Images](oracle-vm-solutions.md), die Sie bereitstellen können, wenn Sie sich für die vollständige Ausführung Ihrer Oracle-Anwendungen in Azure entscheiden.

Die folgenden Abschnitte enthalten Architekturempfehlungen von Microsoft und Oracle zur Bereitstellung von Oracle E-Business Suite, JD Edwards EnterpriseOne und PeopleSoft in einer cloudübergreifenden Konfiguration oder vollständig in Azure. Microsoft und Oracle haben diese Anwendungen getestet und sichergestellt, dass die Leistung dem Standard entspricht, der von Oracle für diese Anwendungen festgelegt wurde.

## <a name="architecture-considerations"></a>Überlegungen zur Architektur

Oracle-Anwendungen bestehen aus mehreren Diensten, die auf einem oder mehreren virtuellen Computern in Azure oder optional in der OCI gehostet werden können. 

Anwendungsinstanzen können mit privaten oder öffentlichen Endpunkten eingerichtet werden. Microsoft und Oracle empfehlen, eine *Bastionhost-VM* mit einer öffentlichen IP-Adresse in einem separaten Subnetz für die Verwaltung der Anwendung einzurichten. Weisen Sie den anderen Computern dann nur private IP-Adressen zu (einschließlich Datenbankschicht). 

Beim Einrichten einer Anwendung in einer cloudübergreifenden Architektur ist eine Planung erforderlich, um sicherzustellen, dass sich der IP-Adressraum im virtuellen Azure-Netzwerk nicht mit dem privaten IP-Adressraum im virtuellen Cloudnetzwerk der OCI überschneidet.

Richten Sie zur Steigerung der Sicherheit Netzwerksicherheitsgruppen auf Subnetzebene ein, um sicherzustellen, dass nur Datenverkehr über bestimmte Ports und IP-Adressen zulässig ist. Computer auf der mittleren Ebene sollten nur Datenverkehr aus dem virtuellen Netzwerk empfangen. Kein externer Datenverkehr sollte direkt auf die Computer der mittleren Ebene gelangen.

Zur Sicherstellung von Hochverfügbarkeit können Sie redundante Instanzen der unterschiedlichen Server in derselben Verfügbarkeitsgruppe oder in unterschiedlichen Verfügbarkeitszonen einrichten. Mit Verfügbarkeitszonen können Sie eine SLA mit einer Verfügbarkeit von 99,99 % erzielen, und mit Verfügbarkeitsgruppen ist innerhalb einer Region eine SLA mit einer Verfügbarkeit von 99,95 % möglich. Die in diesem Artikel beschriebenen Beispielarchitekturen werden übergreifend in zwei Verfügbarkeitszonen bereitgestellt.

Beim Bereitstellen einer Anwendung per cloudübergreifender Verbindung können Sie weiterhin eine vorhandene ExpressRoute-Leitung nutzen, um Ihre Azure-Umgebung mit Ihrem lokalen Netzwerk zu verbinden. Sie benötigen für die Verbindung mit der OCI zusätzlich zu der Leitung, über die die Verbindung mit Ihrem lokalen Netzwerk hergestellt wird, aber eine weitere ExpressRoute-Leitung.

## <a name="e-business-suite"></a>E-Business Suite

Bei Oracle E-Business Suite (EBS) handelt es sich um eine Suite mit Anwendungen, z. B. für Lieferkettenverwaltung (Supply Chain Management, SCM) und Customer Relationship Management (CRM). Zur Nutzung des OCI-Portfolios für verwaltete Datenbanken kann EBS bereitgestellt werden, indem die cloudübergreifende Verbindung zwischen Microsoft Azure und der OCI verwendet wird. Bei dieser Konfiguration werden die Darstellungs- und die Logikschicht in Azure und die Datenbankschicht in der OCI ausgeführt. Dies ist im folgenden Architekturdiagramm dargestellt (Abbildung 1).

![E-Business Suite: Cloudübergreifende Architektur](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*Abbildung 1: E-Business Suite: Cloudübergreifende Architektur* 

In dieser Architektur ist das virtuelle Netzwerk in Azure über die cloudübergreifende Verbindung mit einem virtuellen Cloudnetzwerk in der OCI verbunden. Die Anwendungsebene wird in Azure eingerichtet, und die Datenbank in der OCI. Wir empfehlen Ihnen, jede Komponente in einem eigenen Subnetz mit Netzwerksicherheitsgruppen bereitzustellen, damit es möglich ist, Datenverkehr nur aus bestimmten Subnetzen über bestimmte Ports zuzulassen.

Die Architektur kann auch so angepasst werden, dass die gesamte Bereitstellung in Azure erfolgt und hoch verfügbare Oracle-Datenbanken mit Verwendung von Oracle Data Guard in zwei Verfügbarkeitszonen einer Region genutzt werden. Das folgende Diagramm (Abbildung 2) zeigt ein Beispiel für dieses Architekturmuster:

![E-Business Suite: Reine Azure-Architektur](media/oracle-oci-applications/ebs-arch-azure.png)

*Abbildung 2: E-Business Suite: Reine Azure-Architektur*

Die folgenden Abschnitte enthalten allgemeine Beschreibungen der verschiedenen Komponenten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>Logikschicht (mittlere Ebene)

Die Logikschicht ist in einem eigenen Subnetz isoliert. Für die Fehlertoleranz und die einfache Patchverwaltung werden mehrere virtuelle Computer eingerichtet. Diese VMs können durch gemeinsam genutzten Speicher unterstützt werden, der von Azure NetApp Files und SSD Ultra-Einheiten bereitgestellt wird. Diese Konfiguration ermöglicht eine einfachere Bereitstellung von Patches ohne Ausfallzeiten. Die Computer auf der Logikschicht sollten über ein vorgeschaltetes Lastenausgleichsmodul verfügen, damit an die EBS-Logikschicht gesendete Anforderungen auch dann verarbeitet werden, wenn ein Computer der Schicht aufgrund eines Fehlers offline ist.

### <a name="load-balancer"></a>Load Balancer

Mit einem Azure Load Balancer können Sie Datenverkehr auf mehrere Instanzen Ihrer Workload verteilen, um die Hochverfügbarkeit sicherzustellen. In diesem Fall wird ein öffentliches Lastenausgleichsmodul eingerichtet, weil es für Benutzer zulässig ist, über das Web auf die EBS-Anwendung zuzugreifen. Das Lastenausgleichsmodul verteilt die Last auf beide Computer der mittleren Ebene. Um die Sicherheit weiter zu erhöhen, sollten Sie nur Datenverkehr von Benutzern zulassen, die aus Ihrem Unternehmensnetzwerk auf das System zugreifen, indem sie eine Site-to-Site-VPN-Verbindung oder eine ExpressRoute-Leitung und Netzwerksicherheitsgruppen verwenden.

### <a name="database-tier"></a>Datenbankschicht

Auf dieser Schicht wird die Oracle-Datenbank gehostet, und zwar separat in einem eigenen Subnetz. Wir empfehlen Ihnen das Hinzufügen von Netzwerksicherheitsgruppen, die nur Datenverkehr von der Logikschicht zur Datenbankschicht über den Oracle-spezifischen Datenbankport 1521 zulassen.

Microsoft und Oracle empfehlen die Einrichtung für Hochverfügbarkeit. Sie können in Azure Hochverfügbarkeit erzielen, indem Sie zwei Oracle-Datenbanken in zwei Verfügbarkeitszonen mit Oracle Data Guard einrichten oder Oracle Database Exadata Cloud Service in der OCI verwenden. Bei Verwendung des Oracle Database Exadata Cloud Service wird Ihre Datenbank in zwei Subnetzen bereitgestellt. Sie können Oracle Database auf VMs auch in zwei Verfügbarkeitsdomänen der OCI mit Oracle Data Guard einrichten.


### <a name="identity-tier"></a>Identitätsschicht

Die Identitätsschicht enthält die EBS Asserter-VM. Mit EBS Asserter können Sie Identitäten für Oracle Identity Cloud Service (IDCS) und Azure AD synchronisieren. EBS Asserter wird benötigt, weil für EBS keine Protokolle für einmaliges Anmelden unterstützt werden, z. B. SAML 2.0 oder OpenID Connect. EBS Asserter nutzt das OpenID Connect-Token (von IDCS generiert), überprüft es und erstellt dann eine Sitzung für den Benutzer in EBS. 

In dieser Architektur wird die IDCS-Integration angezeigt, aber der einheitliche Zugriff über Azure AD und einmaliges Anmelden kann auch mit Oracle Access Manager per Oracle Internet Directory oder Oracle Unified Directory ermöglicht werden. Weitere Informationen finden Sie in den Whitepapers [Deploying Oracle EBS with IDCS Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf) (Bereitstellen von Oracle EBS mit IDCS-Integration) und [Deploying Oracle EBS with OAM Integration](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf) (Bereitstellen von Oracle EBS mit OAM-Integration).

Wir empfehlen Ihnen, zum Erzielen von Hochverfügbarkeit redundante Server von EBS Asserter in mehreren Verfügbarkeitszonen mit einem vorgeschalteten Lastenausgleichsmodul bereitzustellen.

Nachdem Sie die Infrastruktur eingerichtet haben, können Sie die E-Business-Suite installieren, indem Sie die Anleitung im von Oracle bereitgestellten Installationshandbuch befolgen.

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

JD Edwards EnterpriseOne von Oracle ist eine integrierte Suite mit Anwendungen für umfassendes Enterprise Resource Planning. Es handelt sich um eine Anwendung mit mehreren Ebenen, die entweder mit einem Oracle- oder SQL Server-Datenbank-Back-End eingerichtet werden kann. In diesem Abschnitt werden die Details der Bereitstellung von JD Edwards EnterpriseOne mit einem Oracle-Datenbank-Back-End in der OCI bzw. in Azure beschrieben.

In der folgenden empfohlenen Architektur (Abbildung 3) werden die Verwaltungsschicht, Darstellungsschicht und mittlere Ebene im virtuellen Netzwerk in Azure bereitgestellt. Die Datenbank wird in einem virtuellen Cloudnetzwerk in der OCI bereitgestellt.

Wie auch bei der E-Business Suite, können Sie eine optionale Bastionschicht für die sichere Verwaltung einrichten. Verwenden Sie die Bastionhost-VM als Sprungserver für den Zugriff auf die Anwendungs- und Datenbankinstanzen.

![JD Edwards EnterpriseOne: Cloudübergreifende Architektur](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*Abbildung 3: JD Edwards EnterpriseOne: Cloudübergreifende Architektur*

In dieser Architektur ist das virtuelle Netzwerk in Azure über die cloudübergreifende Verbindung mit dem virtuellen Cloudnetzwerk in der OCI verbunden. Die Anwendungsebene wird in Azure eingerichtet, und die Datenbank in der OCI. Wir empfehlen Ihnen, jede Komponente in einem eigenen Subnetz mit Netzwerksicherheitsgruppen bereitzustellen, damit es möglich ist, Datenverkehr nur aus bestimmten Subnetzen über bestimmte Ports zuzulassen.

Die Architektur kann auch so angepasst werden, dass die gesamte Bereitstellung in Azure erfolgt und hoch verfügbare Oracle-Datenbanken mit Verwendung von Oracle Data Guard in zwei Verfügbarkeitszonen einer Region genutzt werden. Das folgende Diagramm (Abbildung 4) zeigt ein Beispiel für dieses Architekturmuster:

![JD Edwards EnterpriseOne: Reine Azure-Architektur](media/oracle-oci-applications/jdedwards-arch-azure.png)

*Abbildung 4: JD Edwards EnterpriseOne: Reine Azure-Architektur*

Die folgenden Abschnitte enthalten allgemeine Beschreibungen der verschiedenen Komponenten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>Verwaltungsschicht

Wie der Name bereits vermuten lässt, wird diese Schicht für Verwaltungsaufgaben genutzt. Sie können ein separates Subnetz für die Verwaltungsschicht verwenden. Die Dienste und Server auf dieser Schicht werden hauptsächlich für die Installation und Verwaltung der Anwendung verwendet. Daher sind einzelne Instanzen dieser Server ausreichend. Es sind keine redundanten Instanzen erforderlich, um für Ihre Anwendung Hochverfügbarkeit zu erzielen.

Die Komponenten dieser Schicht lauten wie folgt:
    
 - **Bereitstellungsserver**: Dieser Server wird für die End-to-End-Bereitstellung der unterschiedlichen Komponenten einer Anwendung verwendet. Er kommuniziert mit den Instanzen der anderen Schichten, einschließlich der Instanzen auf der Datenbankschicht, über Port 22. Er hostet die Server Manager Console für JD Edwards EnterpriseOne.
 - **Bereitstellungsserver**: Dieser Server ist hauptsächlich für die Installation von JD Edwards EnterpriseOne erforderlich. Während des Installationsprozesses fungiert dieser Server als zentrales Repository für die erforderlichen Dateien und Installationspakete. Die Software wird von diesem Server aus auf andere Server und Clients verteilt bzw. darauf bereitgestellt.
 - **Entwicklungsclient**: Dieser Server enthält Komponenten, die in einem Webbrowser und in nativen Anwendungen ausgeführt werden.

### <a name="presentation-tier"></a>Darstellungsschicht

Diese Schicht enthält verschiedene Komponenten, z. B. Application Interface Services (AIS), Application Development Framework (ADF) und Java Application Servers (JAS). Die Server dieser Schicht kommunizieren mit den Servern der mittleren Ebene. Ihnen ist ein Lastenausgleichsmodul vorgeschaltet, mit dem Datenverkehr basierend auf der Portnummer und der URL, über den bzw. die der Datenverkehr empfangen wird, an den erforderlichen Server geleitet wird. Wir empfehlen Ihnen, mehrere Instanzen jedes Servertyps bereitzustellen, um Hochverfügbarkeit zu erzielen.

Hier sind die Komponenten dieser Schicht aufgeführt:
    
- **Application Interface Services (AIS)** : Der AIS-Server stellt die Kommunikationsschnittstelle zwischen mobilen JD Edwards EnterpriseOne-Unternehmensanwendungen und JD Edwards EnterpriseOne bereit.
- **Java Application Server (JAS)** : Mit JAS werden Anforderungen vom Lastenausgleichsmodul empfangen und an die mittlere Ebene übergeben, um komplizierte Aufgaben durchzuführen. Mit JAS kann einfache Geschäftslogik ausgeführt werden.
- **BI Publisher Server (BIP)** : Mit diesem Server werden Berichte basierend auf den Daten bereitgestellt, die von der JD Edwards EnterpriseOne-Anwendung erfasst werden. Sie können anhand von unterschiedlichen Vorlagen festlegen und steuern, wie die Daten im Bericht dargestellt werden.
- **Business Services Server (BSS)** : BSS ermöglicht den Informationsaustausch und die Interoperabilität mit anderen Oracle-Anwendungen.
- **Real-Time Events Server (RTE)** : Mit RTE Server können Sie Benachrichtigungen für externe Systeme zu Transaktionen einrichten, die im JDE EnterpriseOne-System durchgeführt werden. Es wird ein Abonnentenmodell verwendet, und Drittanbietersysteme können Ereignisse abonnieren. Stellen Sie sicher, dass sich die Server in einem Cluster befinden, um für Anforderungen an beide RTE Server-Einheiten einen Lastenausgleich durchzuführen.
- **Application Development Framework (ADF) Server**: ADF Server wird zum Ausführen von JD Edwards EnterpriseOne-Anwendungen verwendet, die mit Oracle ADF entwickelt wurden. Die Bereitstellung erfolgt auf einem Oracle WebLogic-Server mit ADF-Runtime.

### <a name="middle-tier"></a>Mittlere Ebene

Die mittlere Ebene enthält den Logikserver und den Batchserver. In diesem Fall sind beide Server auf demselben virtuellen Computer installiert. Für Produktionsszenarien empfehlen wir Ihnen aber, den Logik- und Batchserver auf separaten Servern bereitzustellen. Mehrere Server werden auf der mittleren Ebene in zwei Verfügbarkeitszonen bereitgestellt, um eine bessere Hochverfügbarkeit zu erzielen. Es sollte ein Azure Load Balancer erstellt werden. Außerdem sollten diese Server im zugehörigen Back-End-Pool angeordnet werden, um sicherzustellen, dass beide Server aktiv sind und Anforderungen verarbeiten.

Die Server der mittleren Ebene empfangen nur Anforderungen von den Servern der Darstellungsschicht und vom öffentlichen Lastenausgleich. Es müssen Netzwerksicherheitsgruppen-Regeln eingerichtet werden, um Datenverkehr von allen Adressen abzulehnen, die nicht aus dem Subnetz der Darstellungsschicht und vom Lastenausgleich stammen. Eine NSG-Regel kann auch so eingerichtet werden, dass Datenverkehr über Port 22 vom Bastionhost zu Verwaltungszwecken zugelassen wird. Unter Umständen können Sie den öffentlichen Lastenausgleich auch nutzen, um die Last zwischen den VMs der mittleren Ebene auszugleichen.

Die beiden folgenden Komponenten befinden sich auf der mittleren Ebene:

- **Logikserver**: Enthält die Geschäftslogik oder Geschäftsfunktionen.
- **Batchserver**: Wird für die Batchverarbeitung verwendet.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Die PeopleSoft-Anwendungssuite von Oracle enthält Software für das Personalwesen und das Finanzmanagement. Die Anwendungssuite hat mehrere Schichten und verfügt über Anwendungen für die Bereiche Personalwesen (Human Resource Management Systems, HRMS), Customer Relationship Management (CRM), Finanzen und Lieferkettenverwaltung (Financials and Supply Chain Management, FSCM) und Enterprise Performance Management (EPM).

Wir empfehlen Ihnen, jede Schicht der Softwaresuite in einem eigenen Subnetz bereitzustellen. Eine Oracle-Datenbank oder eine Microsoft SQL Server-Instanz wird als Back-End-Datenbank für die Anwendung benötigt. In diesem Abschnitt werden Details zur Bereitstellung von PeopleSoft mit einem Oracle-Datenbank-Back-End beschrieben.

Im Folgenden ist eine kanonische Architektur für die Bereitstellung der PeopleSoft-Anwendungssuite in einer cloudübergreifenden Architektur dargestellt (Abbildung 5).

![PeopleSoft: Cloudübergreifende Architektur](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*Abbildung 5: PeopleSoft: Cloudübergreifende Architektur*

In dieser Beispielarchitektur ist das virtuelle Netzwerk in Azure über die cloudübergreifende Verbindung mit dem virtuellen Cloudnetzwerk in der OCI verbunden. Die Anwendungsebene wird in Azure eingerichtet, und die Datenbank in der OCI. Wir empfehlen Ihnen, jede Komponente in einem eigenen Subnetz mit Netzwerksicherheitsgruppen bereitzustellen, damit es möglich ist, Datenverkehr nur aus bestimmten Subnetzen über bestimmte Ports zuzulassen.

Die Architektur kann auch so angepasst werden, dass die gesamte Bereitstellung in Azure erfolgt und hoch verfügbare Oracle-Datenbanken mit Verwendung von Oracle Data Guard in zwei Verfügbarkeitszonen einer Region genutzt werden. Das folgende Diagramm (Abbildung 6) zeigt ein Beispiel für dieses Architekturmuster:

![PeopleSoft: Reine Azure-Architektur](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*Abbildung 6: PeopleSoft: Reine Azure-Architektur*

Die folgenden Abschnitte enthalten allgemeine Beschreibungen der verschiedenen Komponenten.

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>Logikschicht

Die Logikschicht enthält Instanzen der PeopleSoft-Anwendungsserver, PeopleSoft-Webserver, von Elasticsearch und des PeopleSoft Process Scheduler. Ein Azure Load Balancer wird so eingerichtet, dass Anforderungen von Benutzern akzeptiert werden, die an den entsprechenden Server auf der Logikschicht geleitet werden.

Erwägen Sie zur Erzielung von Hochverfügbarkeit die Einrichtung von redundanten Instanzen jedes Servers auf der Logikschicht über unterschiedliche Verfügbarkeitszonen hinweg. Der Azure Load Balancer kann mit mehreren Back-End-Pools eingerichtet werden, um jede Anforderung an den richtigen Server leiten zu können.

### <a name="peopletools-client"></a>PeopleTools-Client

Der People Tools-Client wird verwendet, um Verwaltungsaktivitäten durchzuführen, z. B. Entwicklung, Migration und Upgrade. Da der PeopleTools-Client nicht erforderlich ist, um für Ihre Anwendung Hochverfügbarkeit zu erzielen, werden für den PeopleTools-Client keine redundanten Server benötigt.

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [Terraform-Skripts](https://github.com/microsoft/azure-oracle), um Oracle-Apps in Azure einzurichten und die cloudübergreifende Konnektivität mit der OCI herzustellen.

Weitere Informationen und Whitepaper zu OCI finden Sie in der [Dokumentation zu Oracle Cloud Infrastructure](https://docs.cloud.oracle.com/iaas/Content/home.htm).
