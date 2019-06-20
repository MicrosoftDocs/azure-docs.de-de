---
title: Übersicht über SQL Server auf virtuellen Azure Windows-Computern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie vollwertige Editionen von SQL Server auf virtuellen Azure-Computern ausführen.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99c4f0f99af61196cf1a12f2f68a7d10d8b2e6c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477160"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Was ist SQL Server auf virtuellen Azure-Computern? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

Mit [SQL Server auf virtuellen Azure-Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) können Sie Vollversionen von SQL Server in der Cloud nutzen, ohne lokale Hardware verwalten zu müssen. Virtuelle SQL Server-Computer vereinfachen außerdem die Lizenzierungskosten, wenn Sie eine nutzungsbasierte Bezahlung verwenden.

Virtuelle Azure-Computer werden in verschiedensten [geografischen Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt ausgeführt. Sie bieten auch eine Vielzahl von [Computergrößen](../sizes.md). Über den Katalog mit VM-Images können Sie virtuelle SQL Server-Computer mit passender Version, passender Edition und passendem Betriebssystem erstellen. Dadurch stellen virtuelle Computer eine gute Wahl für viele verschiedene SQL Server-Workloads dar.

## <a name="automated-updates"></a>Automatische Updates

Für virtuelle Azure-Computer mit SQL Server kann das [automatisierte Patchen](virtual-machines-windows-sql-automated-patching.md) verwendet werden, um ein Wartungsfenster zum automatischen Installieren wichtiger Windows- und SQL Server-Updates zu planen.

## <a name="automated-backups"></a>Automatisierte Sicherungen

Für virtuelle Azure-Computer mit SQL Server kann die [automatisierte Sicherung](virtual-machines-windows-sql-automated-backup-v2.md)verwendet werden, bei der regelmäßig Sicherungen Ihrer Datenbank im Blobspeicher erstellt werden. Sie können dieses Verfahren auch manuell verwenden. Weitere Informationen finden Sie unter [Verwenden von Azure Storage für SQL Server-Sicherung und -Wiederherstellung](virtual-machines-windows-use-storage-sql-server-backup-restore.md).

## <a name="high-availability"></a>Hochverfügbarkeit

Wenn Sie Hochverfügbarkeit benötigen, sollten Sie SQL Server-Verfügbarkeitsgruppen konfigurieren. Dies beinhaltet mehrere virtuelle Azure-Computer mit SQL Server in einem virtuellen Netzwerk. Sie können Ihre Hochverfügbarkeitslösung entweder manuell konfigurieren oder die vorlagenbasierte automatische Konfiguration im Azure-Portal verwenden. Eine Übersicht über alle Hochverfügbarkeitsoptionen finden Sie unter [Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="performance"></a>Leistung

Virtuelle Azure-Computer sind in unterschiedlichen Größen verfügbar, um verschiedene Workload-Anforderungen zu erfüllen. Virtuelle SQL-Computer stellen auch eine automatisierte Speicherkonfiguration bereit, die für Ihre Leistungsanforderungen optimiert ist. Weitere Informationen zum Konfigurieren von Speicher für virtuelle SQL-Computer finden Sie unter [Speicherkonfiguration für SQL Server-VMs](virtual-machines-windows-sql-server-storage-configuration.md). Informationen zum Optimieren der Leistung finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-sql-performance.md).

## <a name="get-started-with-sql-vms"></a>Erste Schritte mit virtuellen SQL-Computern

Wählen Sie als Erstes ein SQL Server-VM-Image mit der benötigten Version, der benötigten Edition und dem benötigten Betriebssystem aus. Die folgenden Abschnitte enthalten direkte Links zum Azure-Portal für die Katalogimages für virtuelle SQL Server-Computer.

> [!TIP]
> Weitere Informationen zu den Preisen für SQL-Images finden Sie unter [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Preisinformationen für SQL Server-Azure-VMs). 

### <a id="payasyougo"></a> Nutzungsbasierte Bezahlung
Die folgende Tabelle enthält eine Matrix für SQL Server-Images mit nutzungsbasierter Bezahlung:

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Informationen zu den verfügbaren Images für virtuelle Linux-Computer mit SQL Server finden Sie unter [Overview of SQL Server on Azure Virtual Machines (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md) (Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)).

> [!NOTE]
> Es ist nun möglich, das Lizenzierungsmodell eines virtuellen SQL Server-Computers mit nutzungsbasierter Bezahlung zu ändern und eine eigene Lizenz zu verwenden. Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer](virtual-machines-windows-sql-ahb.md). 

### <a id="BYOL"></a> BYOL (Bring Your Own License)
Sie können auch Ihre eigene Lizenz nutzen (BYOL). In diesem Fall zahlen Sie nur für die VM, ohne dass zusätzliche Gebühren für die SQL Server-Lizenzierung anfallen.  Durch die Nutzung einer eigenen Lizenz können Sie im Laufe der Zeit Geld für kontinuierliche Produktionsworkloads sparen. Weitere Anforderungen für diese Option finden Sie unter [Preisinformationen für virtuelle Azure-Computer mit SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md#byol).

Um Ihre eigene Lizenz zu verwenden, können Sie entweder einen vorhandenen virtuellen SQL-Computer mit nutzungsbasierter Bezahlung ändern oder ein Image mit dem Präfix **{BYOL}** bereitstellen. Weitere Informationen zum Umstellen Ihres Lizenzierungsmodells zwischen nutzungsbasierter Bezahlung und BYOL finden Sie unter [How to change the licensing model for a SQL virtual machine in Azure](virtual-machines-windows-sql-ahb.md) (Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer in Azure). 

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Ist es möglich, mit PowerShell ein älteres Image von SQL Server bereitzustellen, das im Azure-Portal nicht verfügbar ist? Um alle verfügbaren Images mit PowerShell anzuzeigen, verwenden Sie den folgenden Befehl:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Weitere Informationen zum Bereitstellen von SQL Server-VMs über PowerShell finden Sie unter [Bereitstellen von SQL Server-VMs mit Azure PowerShell](virtual-machines-windows-ps-sql-create.md).


### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM
Stellen Sie nach dem Erstellen des virtuellen SQL Server-Computers über Anwendungen oder Tools wie etwa SQL Server Management Studio (SSMS) eine Verbindung damit her. Eine entsprechende Anleitung finden Sie unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Ressourcen-Manager)](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrieren Ihrer Daten
Wenn Sie über eine vorhandene Datenbank verfügen, empfiehlt sich die Verschiebung auf die neu bereitgestellte SQL-VM. Eine Liste mit Migrationsoptionen und Anleitungen finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](virtual-machines-windows-migrate-sql.md).

## <a id="lifecycle"></a> Aktualisierungsrichtlinie für SQL-VM-Images
Azure verwaltet für jedes unterstützte Betriebssystem, jede Version und jede Kombination von Editionen nur ein VM-Image. Dies bedeutet, dass Images im Laufe der Zeit aktualisiert und ältere Images entfernt werden. Weitere Informationen finden Sie im Abschnitt **Images** der [häufig gestellten Fragen zu SQL Server-VMs](virtual-machines-windows-sql-server-iaas-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP)
Das Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP) ist standardmäßig aktiviert. Es sendet in regelmäßigen Abständen Berichte an Microsoft, damit die Nutzung von SQL Server verbessert werden kann. Für CEIP ist nur dann eine Verwaltungsaufgabe erforderlich, wenn Sie das Programm nach der Bereitstellung deaktivieren möchten. Sie können CEIP anpassen oder deaktivieren, indem Sie eine Verbindung mit der VM per Remotedesktop herstellen. Führen Sie anschließend das SQL Server-Hilfsprogramm **Fehler- und Verwendungsberichterstellung** aus. Befolgen Sie die Anleitung, um die Berichterstellung zu deaktivieren. Weitere Informationen zur Datensammlung finden Sie unter [Datenschutzbestimmungen für SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Verwandte Produkte und Dienste
### <a name="windows-virtual-machines"></a>Virtuelle Windows-Computer
* [Übersicht über virtuelle Computer](../overview.md)

### <a name="storage"></a>Storage
* [Einführung in Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netzwerk
* [Virtuelle Netzwerke im Überblick](../../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server-Dokumentation](https://docs.microsoft.com/sql/index)
* [Vergleich mit Azure SQL-Datenbank](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Nächste Schritte

Führen Sie erste Schritte mit SQL Server auf virtuellen Azure-Computern durch:

* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](quickstart-sql-vm-create-portal.md)

Erhalten Sie Antworten auf häufig gestellte Fragen zu virtuellen SQL-Computern:

* [SQL Server auf virtuellen Azure-Computern – FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
