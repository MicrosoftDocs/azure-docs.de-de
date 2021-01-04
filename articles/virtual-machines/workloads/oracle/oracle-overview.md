---
title: Oracle-Lösungen in Microsoft Azure | Microsoft-Dokumentation
description: Lernen Sie Optionen zum Bereitstellen von Oracle-Anwendungen und -Lösungen in Microsoft Azure kennen, einschließlich der vollständigen Ausführung in einer Azure-Infrastruktur oder der Verwendung von cloudübergreifender Konnektivität mit Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: d9b032bbd89f8b6d300650ac54567b07ae3307b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948390"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Übersicht über Oracle-Anwendungen und -Lösungen in Azure

Dieser Artikel führt Funktionen zum Ausführen von Oracle-Lösungen mithilfe der Azure-Infrastruktur ein. Außerdem finden Sie ausführliche Einführungen zu den im Azure Marketplace verfügbaren [Azure-Anwendungen für WebLogic Server](oracle-weblogic.md), [Oracle-VM-Images](oracle-vm-solutions.md) sowie zur Funktion zum [Verbinden von Azure mit der Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-Datenbanken in einer Azure-Infrastruktur

Oracle-Datenbanken führen Sie in einer Azure-Infrastruktur mithilfe von Oracle Database on Oracle Linux-Images aus, die im Azure Marketplace verfügbar sind:

* Oracle Database 12.1, 12.2 und 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 und 18.3 Standard Edition

* Oracle Database 19.3

Sie haben auch die Möglichkeit, Oracle Database in einem Nicht-Oracle Linux Image einzurichten, das in Azure verfügbar ist, eine Lösung auf einem benutzerdefinierten Image aufzusetzen, das sie in Azure von Grund auf neu erstellen, oder ein benutzerdefiniertes Image aus ihrer lokalen Umgebung hochzuladen.

Sie können sie optional mit mehreren angefügten Datenträgern konfigurieren und die Datenbankleistung verbessern, indem Sie Oracle Automated Storage Management (ASM) installieren.

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server mit Azure-Dienstintegrationen

Wählen Sie aus einer Vielzahl von Azure-Anwendungen für WebLogic Server, um Ihren Weg in die Cloud zu beschleunigen.  Es steht eine Reihe von vorkonfigurierten Azure-Dienstintegrationen zur Verfügung, darunter Datenbank, Azure App Gateway und Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Anwendungen unter Oracle Linux und WebLogic Server

Führen Sie Unternehmensanwendungen in Azure mit unterstützten Oracle Linux-Images aus. Die folgenden Images von virtuellen Computern sind im Azure Marketplace erhältlich:

* Oracle WebLogic Server 12.1.2

* Oracle Linux mit Unbreakable Enterprise Kernel (UEK) 6.8, 6.9, 6.10, 7.3 bis 7.7, 8.0 und 8.1 

## <a name="high-availability-and-disaster-recovery-options"></a>Hochverfügbarkeits- und Notfallwiederherstellungsoptionen

* Für Hochverfügbarkeit in einer Region konfigurieren Sie [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard mit FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) oder [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) in Azure-Infrastruktur zusammen mit [Verfügbarkeitszonen](../../../availability-zones/az-overview.md). Sie können diese Konfigurationen auch über mehrere Azure-Regionen verteilt einrichten, um eine höhere Verfügbarkeit und bessere Notfallwiederherstellung zu erreichen.

* Verwenden Sie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md), um die Notfallwiederherstellung für Ihre Oracle Linux-VMs in Azure sowie für Ihre physischen Server zu organisieren und zu verwalten. 

* Aktivieren Sie Oracle Real Application Cluster (RAC) in Azure mithilfe von [Azure VMware Solution](../../../vmware-cloudsimple/oracle-real-application-clusters.md) oder [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Sichern von Oracle-Workloads

* Sichern Sie Ihre Oracle-VMs mit [Azure Backup](../../../backup/backup-overview.md).

* Sichern Sie Ihre Oracle Database-Datenbank mit Oracle RMAN, und verwenden Sie optional [Azure Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md), um ein [hochgradig redundantes Azure Blob Storage-Konto](../../../storage/common/storage-redundancy.md) einzubinden und ihre RMAN-Sicherungen in dieses Konto zu schreiben, um weitere Resilienz zu erreichen.

## <a name="integration-of-azure-with-oci"></a>Integration von Azure in OCI

Führen Sie Oracle-Anwendungen in der Azure-Infrastruktur aus mit bestehenden Verbindungen zu Back-End-Datenbanken in Oracle Cloud Infrastructure (OCI). Diese Lösung verwendet die folgenden Funktionen: 

* **Cloudübergreifende Netzwerke**: Verwenden Sie die direkte Verbindungen zwischen Azure ExpressRoute und Oracle FastConnect, um hohe Bandbreite und private Verbindungen mit niedrigen Wartezeiten zwischen der Anwendung und der Datenbankschicht herzustellen.
* **Integrierte Identität**: Richten Sie eine Verbundidentität zwischen Azure AD und Oracle IDCS ein, um eine einzige Identitätsquelle für die Lösungen zu erstellen. Aktivieren Sie einmaliges Anmelden, um Ressourcen in OCI und Azure zu verwalten.

### <a name="deploy-oracle-applications-on-azure"></a>Bereitstellen von Oracle-Anwendungen in Azure

Verwenden Sie Terraform-Vorlagen, um die Azure-Infrastruktur einzurichten und Oracle-Anwendungen zu installieren. 

Die folgenden Anwendungen wurden von Oracle für die Ausführung in Azure zertifiziert, wenn über die Azure/Oracle Cloud Interconnect-Lösung eine Verbindung mit einer Oracle-Datenbank hergestellt wird:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-Anwendungen
* Oracle Hyperion Financial Management

Stellen Sie außerdem benutzerdefinierte Anwendungen in Azure bereit, die eine Verbindung mit OCI und anderen Azure-Diensten herstellen.

### <a name="set-up-oracle-databases-in-oci"></a>Einrichten von Oracle-Datenbanken in OCI

Verwenden Sie Oracle Database-Clouddienste (Autonomous Database, RAC, Exadata, DBaaS, Single Node) in Verbindung mit Oracle-Software, die in Azure ausgeführt wird. Weitere Informationen zu [OCI-Datenbankoptionen](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Lizenzierung

Die Bereitstellung von Oracle-Anwendungen in Azure basiert auf einem BYOL-Modell (Bring Your Own License). Es wird vorausgesetzt, dass Sie über eine ordnungsgemäße Lizenz für die Verwendung von Oracle-Software und über einen aktuellen Supportvertrag mit Oracle verfügen. Oracle bietet eine garantierte Lizenzmobilität von einer lokalen Verwendung zu Azure. Siehe in den [Häufig gestellten Fragen](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) zu Oracle und Azure.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Azure-Anwendungen für WebLogic Server](oracle-weblogic.md) und die von ihnen unterstützten Azure-Dienstintegrationen.

* Weitere Informationen zum Bereitstellen von [Oracle VM-Images](oracle-vm-solutions.md) in der Azure-Infrastruktur.

* Weitere Informationen zum [Verbinden von Azure mit OCI](oracle-oci-overview.md).

* Sehen Sie sich die [Sitzung der Oracle in Azure-Übersicht](https://myignite.techcommunity.microsoft.com/sessions/82915) von Ignite 2019 an. 
