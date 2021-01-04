---
title: Übersicht über SQL Server auf Azure Virtual Machines für Linux | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie vollwertige SQL Server-Editionen auf Azure Virtual Machines für Linux ausführen. Außerdem finden Sie hier direkte Links zu allen Linux-basierten SQL Server-VM-Images sowie zu verwandten Inhalten.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c394fe2aa7639d32e5d79bcb22a01151f7666f5b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324617"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Mit SQL Server auf Azure Virtual Machines können Sie Vollversionen von SQL Server in der Cloud nutzen, ohne lokale Hardware verwalten zu müssen. Virtuelle SQL Server-Computer vereinfachen außerdem die Lizenzierungskosten, wenn Sie eine nutzungsbasierte Bezahlung verwenden.

Virtuelle Azure-Computer werden in verschiedensten [geografischen Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt ausgeführt. Sie bieten auch eine Vielzahl von [Computergrößen](../../../virtual-machines/sizes.md). Über den Katalog mit VM-Images können Sie virtuelle SQL Server-Computer mit passender Version, passender Edition und passendem Betriebssystem erstellen. Dadurch stellen virtuelle Computer eine gute Wahl für viele verschiedene SQL Server-Workloads dar. 

Wenn Sie noch nicht mit Azure SQL vertraut sind, sollten Sie sich das Video mit der *Übersicht über SQL Server auf einer Azure-VM* ansehen, das Teil unserer detaillierten [Azure SQL-Videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) ist:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Erste Schritte mit SQL Server-VMs

Wählen Sie als Erstes ein SQL Server-VM-Image mit der benötigten Version, der benötigten Edition und dem benötigten Betriebssystem aus. Die folgenden Abschnitte enthalten direkte Links zum Azure-Portal für die Katalogimages für virtuelle SQL Server-Computer.

> [!TIP]
> Weitere Informationen zur Preisgestaltung für SQL Server-Images finden Sie auf der [Seite mit den Preisinformationen für Linux-VMs mit ausgeführtem SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Informationen zu den verfügbaren Images für virtuelle Computer mit SQL Server für Windows finden Sie in der [Übersicht über SQL Server auf Azure Virtual Machines (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Installierte Pakete

Wenn Sie SQL Server unter Linux konfigurieren, installieren Sie das Datenbank-Engine-Paket und je nach Bedarf mehrere optionale Pakete. Die meisten Pakete werden von den Linux-VM-Images für SQL Server automatisch installiert. Die folgende Tabelle zeigt, welche Pakete für die einzelnen Distributionen installiert werden:

| Distribution | [Datenbank-Engine](/sql/linux/sql-server-linux-setup) | [Tools](/sql/linux/sql-server-linux-setup-tools) | [SQL Server-Agent](/sql/linux/sql-server-linux-setup-sql-agent) | [Volltextsuche](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [HA-Add-On](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL und Datenbank-Engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL und Tools](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL und SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL und Volltextsuche](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL und SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL und Add-On für Hochverfügbarkeit](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES und Datenbank-Engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES und Tools](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES und SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES und Volltextsuche](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES und SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES und Add-On für Hochverfügbarkeit](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu und Datenbank-Engine](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu und Tools](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu und SQL Server-Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu und Volltextsuche](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu und SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu und Add-On für Hochverfügbarkeit](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Verwandte Produkte und Dienste

### <a name="linux-virtual-machines"></a>Virtuelle Linux-Computer

* [Übersicht über Azure Virtual Machines](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Storage

* [Einführung in Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netzwerk

* [Virtuelle Netzwerke im Überblick](../../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../../virtual-network/public-ip-addresses.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server unter Linux – Dokumentation](/sql/linux)
* [Vergleich mit Azure SQL-Datenbank](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Nächste Schritte

Führen Sie erste Schritte mit SQL Server auf virtuellen Linux-Computern durch:

* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](sql-vm-create-portal-quickstart.md)

Antworten auf häufig gestellte Fragen zu SQL Server-VMs unter Linux:

* [SQL Server auf virtuellen Azure-Computern – FAQ](frequently-asked-questions-faq.md)