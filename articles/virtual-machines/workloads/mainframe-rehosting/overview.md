---
title: Mainframerehosting auf virtuellen Azure-Computern
description: Erfahren Sie, wie Sie für Mainframeworkloads wie IBM Z-basierte Systeme auf virtuellen Microsoft Azure-Computern ein Rehosting durchführen.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61487453"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Mainframerehosting auf virtuellen Azure-Computern

Durch das Migrieren von Workloads aus Mainframeumgebungen in die Cloud können Sie ihre Infrastruktur modernisieren und oft Kosten einsparen. Viele Workloads können mit nur geringfügigen Änderungen am Code, z.B. Aktualisieren der Namen der Datenbanken, in Azure übertragen werden.

Im Allgemeinen bezeichnet der Begriff *Mainframe* ein großes Computersystem. Insbesondere sind die meisten derzeit verwendeten Systeme IBM System Z-Server oder IBM-plug-kompatible Systeme, auf denen MVS, DOS, VSE, OS/390 oder z/OS ausgeführt wird.

Mit einem virtuellen Azure-Computer (VM) werden Ressourcen für eine bestimmte Anwendung auf einer einzigen Instanz isoliert und verwaltet. Mainframes wie IBM z/OS nutzen dafür logische Partitionen (LPARS). Ein Mainframe kann eine LPAR für eine CICS-Region mit zugeordneten COBOL-Programmen und eine separate LPAR für eine IBM Db2-Datenbank verwenden. Eine typische [n-schichtige Anwendung in Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) stellt Azure-VMs in einem virtuellen Netzwerk bereit, das für jede Schicht in Subnetze aufgeteilt werden kann.

Auf Azure-VMs können Mainframeemulationsumgebungen und Compiler ausgeführt werden, die Migrationen per Lift & Shift unterstützen. Die Entwicklungs- und Testumgebung werden häufig als erste Workloads von einem Mainframe zu Azure migriert. Zu den häufig eingesetzten Serverkomponenten, die Sie emulieren können, gehören Onlinetransaktionsverarbeitungs-, Batch- und Datenerfassungssysteme (siehe Abbildung).

![Mithilfe von Azure-Emulationsumgebungen können Sie z/OS-basierte Systeme ausführen.](media/01-overview.png)

Einige Mainframeworkloads lassen sich relativ leicht zu Azure migrieren, während für andere ein Rehosting in Azure mit einer Partnerlösung durchgeführt werden kann. Bei der Wahl einer Partnerlösung unterstützt Sie das [Azure-Migrationscenter für Mainframes](https://azure.microsoft.com/migration/mainframe/) mit einem ausführlichen Leitfaden.

## <a name="mainframe-migration"></a>Mainframemigration

Erneut hosten, neu erstellen, ersetzen oder außer Betrieb nehmen? IaaS oder PaaS? Wie Sie die richtige Migrationsstrategie für Ihre Mainframeanwendung ermitteln, erfahren Sie in dem Handbuch [Übersicht zur Mainframemigration](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) im Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Micro Focus-Plattform für erneutes Hosten

Micro Focus Enterprise Server ist eine der größten verfügbaren Mainframerehostingplattformen. Mit dieser können Sie z/OS-Workloads auf einer günstigeren x86-Plattform in Azure ausführen.

Erste Schritte:

- [Installieren von Enterprise Server und Enterprise Developer in Azure](./microfocus/set-up-micro-focus-azure.md)
- [Einrichten von CICS BankDemo für Enterprise Developer in Azure](./microfocus/demo.md)
- [Ausführen von Enterprise Server in einem Docker-Container in Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame in Azure

TmaxSoft OpenFrame ist eine beliebte Lösung zum erneuten Hosten von Mainframes, die in Lift & Shift-Szenarios verwendet wird. Eine OpenFrame-Umgebung in Azure eignet sich für Entwicklungs-, Demo-, Test- und Produktionsworkloads.

Erste Schritte:

- [Erste Schritte mit TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [E-Book herunterladen](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

IBM Z Development and Test Environment (IBM ZD&T) richtet eine Umgebung in Azure ein, die nicht für den Produktionsbetrieb, sondern für das Entwickeln, Testen und Demonstrieren von z/OS-basierten Anwendungen verwendet werden kann.

In der Azure-Emulationsumgebung können verschiedene Arten von Z-Instanzen mithilfe von Application Developers Controlled Distributions (ADCDs) gehostet werden. Sie können in Azure und Azure Stack ZD&T Personal Edition, ZD&T Parallel Sysplex und ZD&T Enterprise Edition ausführen.

Erste Schritte:

- [Set up IBM zD&T 12.0 on Azure (Einrichten von IBM ZD&T 12.0 in Azure)](./ibm/install-ibm-z-environment.md)
- [Set up ADCD on zD&T (Einrichten von ADCD auf ZD&T)](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale in Azure

Die IBM DB2 pureScale-Umgebung stellt einen Datenbankcluster für Azure bereit. Er ist nicht identisch mit der ursprünglichen Umgebung, bietet aber ähnliche Verfügbarkeit und Skalierung wie IBM DB2 für z/OS bei Ausführung in einem Parallel Sysplex-Setup.

Hinweise zu den ersten Schritten finden Sie unter [IBM DB2 pureScale on Azure (IBM DB2 pureScale in Azure)](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Überlegungen

Wenn Sie Mainframeworkloads zu Azure-IaaS (Infrastructure-as-a-Service) migrieren, können Sie zwischen mehreren Typen von bedarfsgesteuerten, skalierbaren Computeressourcen einschließlich Azure-VMs auswählen. Azure stellt eine Reihe von [Linux-](/azure/virtual-machines/linux/overview) und [Windows-VMs](/azure/virtual-machines/windows/overview) bereit.

### <a name="compute"></a>Compute

Azure-Computeleistung ist ideal mit der Kapazität eines Mainframes vergleichbar. Wenn Sie erwägen, eine Mainframeworkload zu Azure zu verschieben, vergleichen Sie die Mainframemetrik von einer Million Anweisungen pro Sekunde (Million Instructions Per Second, MIPS) mit virtuellen CPUs. 

Informieren Sie sich über das [Verschieben von Mainframe-Computing zu Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Hochverfügbarkeit und Failover

Azure bietet verpflichtungsbasierte Vereinbarungen zum Service Level (Service Level Agreements, SLAs). Hohe Verfügbarkeit ist der Standard, und SLAs können mit lokaler oder geobasierter Replikation von Diensten optimiert werden. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

Mit Azure-IaaS wie einer VM bieten bestimmte Systemfunktionen Failoverunterstützung – z.B. Failoverclusteringinstanzen und [Verfügbarkeitsgruppen](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Wenn Sie Platform-as-a-Service-Ressourcen (PaaS) von Azure verwenden, handhabt die Plattform Failover automatisch. Beispiele hierfür sind [Azure SQL-Datenbank](/azure/sql-database/sql-database-technical-overview) und [Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Skalierbarkeit

Mainframes werden in der Regel zentral hochskaliert, Cloudumgebungen horizontal. Azure bietet für [Linux](/azure/virtual-machines/linux/sizes) und [Windows](/azure/virtual-machines/windows/sizes) mehrere VM-Größen für unterschiedliche Anforderungen an. Auch die Cloud kann entsprechend der genauen Benutzeransprüche zentral hoch- oder herunterskaliert werden. Computeleistung, Speicher und Dienste werden in einem nutzungsbasierten Abrechnungsmodell nach Bedarf [skaliert](/azure/architecture/best-practices/auto-scaling).

### <a name="storage"></a>Storage

In der Cloud stehen mehrere Speicheroptionen zur Verfügung, die flexibel und skalierbar sind. Die Kosten richten sich nach der tatsächlichen Nutzung. [Azure Storage](/azure/storage/common/storage-introduction) bietet einen massiv skalierbaren Objektspeicher für Datenobjekte, einen Dateisystemdienst für die Cloud, einen zuverlässigen Messagingspeicher und einen NoSQL-Speicher. Für VMs bieten verwaltete und nicht verwaltete Datenträger einen dauerhaften und sicheren Speicher.

Informieren Sie sich über das [Verschieben von Mainframespeicher zu Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

Die Verwaltung eines eigenen Notfallwiederherstellungsstandorts kann teuer sein. Azure bietet daher kostengünstige und leicht implementierbare Optionen für die [Sicherung](/azure/backup/backup-introduction-to-azure-backup), [Wiederherstellung](/azure/site-recovery/site-recovery-overview) und [Redundanz](/azure/storage/common/storage-redundancy) auf lokaler oder regionaler Ebene oder mittels Georedundanz an.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government für Mainframemigrationen

Viele öffentliche Stellen würden ihre Mainframeanwendungen gerne zu einer moderneren und flexibleren Plattform migrieren. Microsoft Azure Government ist eine physisch getrennte Instanz der globalen Microsoft Azure-Plattform – an Behördensysteme auf staatlicher, bundesstaatlicher und kommunaler Ebene angepasst. Dieser Dienst stellt hervorragende Sicherheits-, Schutz- und Compliancedienste speziell für US-Regierungsbehörden und ihre Partner bereit.

Azure Government verfügt über eine Provisional Authority to Operate (P-ATO) auf FedRAMP High Impact-Stufe, die für bestimmte Systeme erforderlich ist.

Laden Sie [Microsoft Azure Government cloud for mainframe applications (Microsoft Azure Government-Cloud für Mainframeanwendungen)](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/) herunter, um sich einen Überblick über die ersten Schritte zu verschaffen.

## <a name="next-steps"></a>Nächste Schritte

Bitten Sie unsere [Partner](partner-workloads.md), Ihnen bei der Migration zu helfen, oder Ihre Mainframeanwendungen neu zu hosten. Ausführliche Anleitungen zur Auswahl einer Partnerlösung finden Sie auf der [Platform Modernization Alliance](https://www.platformmodernization.org/pages/mainframe.aspx)-Website.

Weitere Informationen:

- [Azure-Whitepapers zu Mainframethemen](mainframe-white-papers.md)
- [Mainframemigration](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Problembehandlung](/azure/virtual-machines/troubleshooting/)
- [Demystifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/) (Beseitigen von Irrtümern in Bezug auf die Migration von Mainframesystemen zu Azure)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
