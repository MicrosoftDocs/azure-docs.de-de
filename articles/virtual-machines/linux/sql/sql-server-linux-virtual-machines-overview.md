---
title: Übersicht über SQL Server auf virtuellen Azure-Computern unter Linux | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie vollwertige SQL Server-Editionen auf virtuellen Azure-Computern unter Linux ausführen. Außerdem finden Sie hier direkte Links zu allen Linux-basierten SQL Server-VM-Images sowie zu verwandten Inhalten.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473480"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Mit SQL Server auf virtuellen Azure-Computern können Sie Vollversionen von SQL Server in der Cloud nutzen, ohne lokale Hardware verwalten zu müssen. Virtuelle SQL Server-Computer vereinfachen außerdem die Lizenzierungskosten, wenn Sie eine nutzungsbasierte Bezahlung verwenden.

Virtuelle Azure-Computer werden in verschiedensten [geografischen Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt ausgeführt. Sie bieten auch eine Vielzahl von [Computergrößen](../sizes.md). Über den Katalog mit VM-Images können Sie virtuelle SQL Server-Computer mit passender Version, passender Edition und passendem Betriebssystem erstellen. Dadurch stellen virtuelle Computer eine gute Wahl für viele verschiedene SQL Server-Workloads dar.

## <a id="create"></a>Erste Schritte mit virtuellen SQL-Computern

Wählen Sie als Erstes ein SQL Server-VM-Image mit der benötigten Version, der benötigten Edition und dem benötigten Betriebssystem aus. Die folgenden Abschnitte enthalten direkte Links zum Azure-Portal für die Katalogimages für virtuelle SQL Server-Computer.

> [!TIP]
> Weitere Informationen zur Preisgestaltung für SQL-Images finden Sie auf der [Seite mit den Preisinformationen für SQL Server-VMs unter Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Informationen zu den verfügbaren Images für virtuelle Windows-Computer mit SQL Server finden Sie in der [Übersicht über SQL Server auf virtuellen Azure-Computern](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Installierte Pakete

Wenn Sie SQL Server unter Linux konfigurieren, installieren Sie das Datenbank-Engine-Paket und je nach Bedarf mehrere optionale Pakete. Die meisten Pakete werden von den Linux-VM-Images für SQL Server automatisch installiert. Die folgende Tabelle zeigt, welche Pakete für die einzelnen Distributionen installiert werden:

| Distribution | [Datenbank-Engine](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Tools](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server-Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Volltextsuche](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA-Add-On](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Ja](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Verwandte Produkte und Dienste

### <a name="linux-virtual-machines"></a>Virtuelle Linux-Computer

* [Übersicht über virtuelle Computer](../overview.md)

### <a name="storage"></a>Storage

* [Einführung in Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netzwerk

* [Virtuelle Netzwerke im Überblick](../../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server unter Linux – Dokumentation](https://docs.microsoft.com/sql/linux)
* [Vergleich mit Azure SQL-Datenbank](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Nächste Schritte

Führen Sie erste Schritte mit SQL Server auf virtuellen Azure-Computern unter Linux durch:

* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](provision-sql-server-linux-virtual-machine.md)

Erhalten Sie Antworten auf häufig gestellte Fragen zu virtuellen SQL-Computern unter Linux:

* [Häufig gestellte Fragen zu SQL Server auf virtuellen Linux-Computern in Azure](sql-server-linux-faq.md)
