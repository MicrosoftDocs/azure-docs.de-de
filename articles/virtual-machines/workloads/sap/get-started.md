---
title: Erste Schritte mit SAP in Azure-VMs | Microsoft-Dokumentation
description: Erfahren Sie etwas über SAP-Lösungen auf virtuellen Windows-Computern in Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f14110cec0650927f74dfe3a38f06187c6df39de
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792659"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Verwenden von Azure zum Hosten und Ausführen von SAP-Workloadszenarien

Mit Microsoft Azure können Sie Ihre geschäftskritischen SAP-Workloads und -Szenarien zuverlässig auf einer skalierbaren, konformen und in Unternehmen bewährten Plattform ausführen. Sie profitieren von der Skalierbarkeit, der Flexibilität und den Kosteneinsparungen von Azure. Dank der erweiterten Partnerschaft zwischen Microsoft und SAP können Sie SAP-Anwendungen in Entwicklungs-, Test- und Produktionsszenarien in Azure ausführen und erhalten dabei vollständigen Support. Wir können Sie in allen Belangen unterstützen, von SAP NetWeaver bis SAP S/4HANA, SAP BI unter Linux bis Windows und SAP HANA bis SQL.

Neben dem Hosten von SAP NetWeaver-Szenarien mit verschiedenen DBMS-Lösungen in Azure können Sie auch andere SAP-Workloadszenarien hosten, z. B. SAP BI unter Azure. 

Die Einzigartigkeit von Azure für SAP HANA ist ein Angebot, mit dem sich Azure absetzt. Um das Hosting von SAP-Szenarien mit höherem Arbeitsspeicher- und CPU-Ressourcenbedarf mit SAP HANA zu ermöglichen, bietet Azure für den Kunden dedizierte Bare-Metal-Hardware an. Verwenden Sie diese Lösung zum Ausführen von SAP HANA-Bereitstellungen, die bis zu 24 TB (120 TB bei horizontaler Hochskalierung) Arbeitsspeicher für S/4HANA- oder andere SAP HANA-Workloads erfordern. 

Durch das Hosten von SAP-Workloadszenarien in Azure können auch Anforderungen an Identitätsintegration und einmaliges Anmelden entstehen. Diese Situation kann auftreten, wenn Sie Azure Active Directory (Azure AD) verwenden, um verschiedene SAP-Komponenten und SaaS- (Software-as-a-Service) oder PaaS-Angebote (Platform as a Service) von SAP zu verbinden. Eine Liste solcher Integrations- und SSO-Szenarien mit Azure AD und SAP-Entitäten mit den entsprechenden Beschreibungen finden Sie im Abschnitt „AAD-SAP-Identitätsintegration und einmaliges Anmelden“.

## <a name="latest-changes"></a>Aktuelle Änderungen

- Veröffentlichung von [Steuerung von HANA in Azure (große Instanzen) über das Azure-Portal](hana-li-portal.md)

- Veröffentlichung von [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen](high-availability-guide-suse-netapp-files.md)

- Klarstellung zu den Einstellungen für den **Linux OS-Parameter net.ipv4.tcp_timestamps** in Kombination mit einer Azure Load Balancer-Instanz

- Veröffentlichung von [SAP-Workloadkonfigurationen mit Azure-Verfügbarkeitszonen](sap-ha-availability-zones.md)

- Freigabe der [Prüfliste für SAP-Workloadplanung und -bereitstellung](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA in Azure (große Instanzen)

Eine Dokumentationsreihe führt Sie durch SAP HANA in Azure (große Instanzen), Kurzform: HANA (große Instanzen). Informationen zu den folgenden Bereichen von HANA (große Instanzen) finden Sie in den jeweiligen Artikeln:

- [Übersicht über SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektur von SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktur und Verbindungen mit SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Installieren von SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Problembehandlung und Überwachung von SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Nächste Schritte:

- Lesen Sie [Übersicht und Architektur von SAP HANA in Azure (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA auf virtuellen Azure-Computern
In diesem Abschnitt der Dokumentation werden verschiedene Aspekte von SAP HANA behandelt. Dabei wird vorausgesetzt, dass Sie mit den wichtigsten Diensten von Azure vertraut sind, die elementare Dienste von Azure IaaS bereitstellen. Sie benötigen somit hauptsächlich Kenntnisse über Azure-Compute, -Speicher und -Netzwerke. Viele dieser Themen werden im SAP NetWeaver-bezogenen [Azure-Planungsleitfaden](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) behandelt. 

Informationen zu HANA in Azure finden Sie in den folgenden Artikeln und den jeweiligen Unterartikeln:

- [Schnellstart: Manuelle Installation von SAP HANA (Einzelinstanz) auf Azure-VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Bereitstellen von SAP S/4HANA oder BW/4HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Hochverfügbarkeit von SAP HANA für virtuelle Azure-Computer](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Verfügbarkeit von SAP HANA innerhalb einer Azure-Region](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA-Verfügbarkeit in den Azure-Regionen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Sicherungsanleitung für SAP HANA auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA-Sicherung auf der Grundlage von Speichermomentaufnahmen](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>Bereitstellung von SAP NetWeaver auf virtuellen Azure-Computern
In diesem Abschnitt finden Sie die Dokumentation zur Planung und Bereitstellung von SAP NetWeaver und Business One in Azure. Die Dokumentation behandelt in erster Linie die Grundlagen und die Verwendung von Nicht-HANA-Datenbanken mit einer SAP-Workload in Azure. Die Dokumente und Artikel über Hochverfügbarkeit bilden auch die Grundlage für die Hochverfügbarkeit von HANA in Azure, z. B.:

- [SAP Business One auf virtuellen Azure-Computern](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Bereitstellen von SAP IDES EHP7 SP3 für SAP ERP 6.0 in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Ausführen von SAP NetWeaver auf Microsoft Azure-SUSE Linux-VMs](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Schützen einer SAP NetWeaver-Anwendungsbereitstellung mit mehreren Ebenen mit Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [SAP LaMa-Connector für Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Informationen zu Nicht-HANA-Datenbanken unter einer SAP-Workload in Azure finden Sie unter:

- [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Azure Virtual Machines – SQL Server-DBMS-Bereitstellung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Azure Virtual Machines – Oracle-DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Azure Virtual Machines – IBM DB2-DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Bereitstellung von SAP MaxDB, SAP liveCache und SAP Content Server in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Informationen zu SAP HANA-Datenbanken in Azure finden Sie im Abschnitt „SAP HANA auf virtuellen Azure-Computern“.

Informationen zur Hochverfügbarkeit von SAP-Workloads in Azure finden Sie unter:

- [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Dieses Dokument verweist auf verschiedene andere Dokumente zur Architektur und zu ähnlichen Szenarien. In nachfolgenden Szenariodokumenten werden Links zu ausführlichen technischen Dokumenten bereitgestellt, in denen die Bereitstellung und Konfiguration der verschiedenen Hochverfügbarkeitsmethoden erläutert werden. In den anderen Dokumenten zum Einrichten und Konfigurieren von Hochverfügbarkeit für SAP NetWeaver-Workloads werden Linux- und Windows-Betriebssysteme behandelt.


Informationen zur Integration von Azure Active Directory (Azure AD) und SAP-Diensten mit einmaligem Anmelden finden Sie unter:

- [Tutorial: Azure Active Directory-Integration mit SAP Cloud for Customer](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform Identity Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Azure Active Directory-Integration mit SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Azure Active Directory-Integration mit SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Tutorial: Azure Active Directory-Integration mit SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Ihre S/4HANA-Umgebung: SAML-SSO für Fiori Launchpad mit Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informationen zur Integration von Azure-Diensten in SAP-Komponenten finden Sie unter:

- [Verwenden von SAP HANA in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery und SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Verwenden des SAP BW-Connectors in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [SAP HANA- und Business Warehouse-Datenintegration mit Azure Data Factory](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




