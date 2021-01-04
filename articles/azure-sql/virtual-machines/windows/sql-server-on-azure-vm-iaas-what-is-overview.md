---
title: Übersicht über SQL Server auf virtuellen Azure Windows-Computern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Vollversionen von SQL Server auf virtuellen Azure-Computern in der Cloud ausführen können, ohne lokale Hardware verwalten zu müssen.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356802"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Was ist SQL Server auf Azure Virtual Machines (Windows)?
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

Mit [SQL Server auf Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) können Sie Vollversionen von SQL Server in der Cloud nutzen, ohne lokale Hardware verwalten zu müssen. Virtuelle SQL Server-Computer (VMs) vereinfachen außerdem die Lizenzierungskosten, wenn Sie nutzungsbasierte Bezahlung verwenden.

Virtuelle Azure-Computer werden in verschiedensten [geografischen Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt ausgeführt. Sie bieten auch eine Vielzahl von [Computergrößen](../../../virtual-machines/sizes.md). Über den Katalog mit VM-Images können Sie virtuelle SQL Server-Computer mit passender Version, passender Edition und passendem Betriebssystem erstellen. Dadurch stellen virtuelle Computer eine gute Wahl für viele verschiedene SQL Server-Workloads dar.

Wenn Sie noch nicht mit SQL Server auf Azure-VMs vertraut sind, sehen Sie sich das Video *SQL Server on Azure VM Overview* aus der detaillierten [Azure SQL-Videoserie](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner) an:
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Automatische Updates

Für SQL Server auf Azure Virtual Machines kann [automatisiertes Patchen](automated-patching.md) verwendet werden, um ein Wartungsfenster zum automatischen Installieren wichtiger Windows- und SQL Server-Updates zu planen.

## <a name="automated-backups"></a>Automatisierte Sicherungen

Für SQL Server auf Azure Virtual Machines kann die [automatisierte Sicherung](automated-backup.md) verwendet werden, bei der regelmäßig Sicherungen Ihrer Datenbank in Blobspeicher erstellt werden. Sie können dieses Verfahren auch manuell verwenden. Weitere Informationen finden Sie unter [Verwenden von Azure Storage für SQL Server-Sicherung und -Wiederherstellung](azure-storage-sql-server-backup-restore-use.md).

Azure bietet auch eine Sicherungslösung der Unternehmensklasse für SQL Server-Instanzen auf Azure-VMs. Es handelt sich um eine vollständig verwaltete Sicherungslösung, die Always On-Verfügbarkeitsgruppen, Langzeitaufbewahrung Zeitpunktwiederherstellung sowie zentrale Verwaltung und Überwachung unterstützt. Weitere Informationen finden Sie unter [Azure Backup für SQL Server in Azure-VMs](../../../backup/backup-azure-sql-database.md).
  

## <a name="high-availability"></a>Hochverfügbarkeit

Wenn Sie Hochverfügbarkeit benötigen, sollten Sie SQL Server-Verfügbarkeitsgruppen konfigurieren. Dies umfasst mehrere Instanzen von SQL Server auf Azure Virtual Machines in einem virtuellen Netzwerk. Sie können Ihre Hochverfügbarkeitslösung entweder manuell konfigurieren oder die vorlagenbasierte automatische Konfiguration im Azure-Portal verwenden. Eine Übersicht über alle Hochverfügbarkeitsoptionen finden Sie unter [Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Leistung

Virtuelle Azure-Computer sind in unterschiedlichen Größen verfügbar, um verschiedene Workload-Anforderungen zu erfüllen. SQL Server-VMs stellen auch eine automatisierte Speicherkonfiguration bereit, die für Ihre Leistungsanforderungen optimiert ist. Weitere Informationen zum Konfigurieren von Speicher für SQL Server-VMs finden Sie unter [Speicherkonfiguration für SQL Server-VMs](storage-configuration.md). Informationen zum Optimieren der Leistung finden Sie unter [Optimale Verfahren für die Leistung von SQL Server auf Azure Virtual Machines](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Erste Schritte mit SQL Server-VMs

Wählen Sie als Erstes ein SQL Server-VM-Image mit der benötigten Version, der benötigten Edition und dem benötigten Betriebssystem aus. Die folgenden Abschnitte enthalten direkte Links zum Azure-Portal für die Katalogimages für virtuelle SQL Server-Computer.

> [!TIP]
> Weitere Informationen zu den Preisen für SQL Server-Images finden Sie unter [Preisinformationen für Azure-VMs mit SQL Server](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Nutzungsbasierte Bezahlung
Die folgende Tabelle enthält eine Matrix für SQL Server-Images mit nutzungsbasierter Bezahlung:

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Informationen zu den verfügbaren Images für virtuelle Linux-Computer mit SQL Server finden Sie unter [Übersicht über SQL Server auf Azure Virtual Machines (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Es ist nun möglich, das Lizenzierungsmodell eines virtuellen SQL Server-Computers mit nutzungsbasierter Bezahlung zu ändern und eine eigene Lizenz zu verwenden. Weitere Informationen finden Sie unter [Ändern des Lizenzierungsmodells für eine SQL Server-VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> BYOL (Bring Your Own License)
Sie können auch Ihre eigene Lizenz nutzen (BYOL). In diesem Fall zahlen Sie nur für die VM, ohne dass zusätzliche Gebühren für die SQL Server-Lizenzierung anfallen.  Durch die Nutzung einer eigenen Lizenz können Sie im Laufe der Zeit Geld für kontinuierliche Produktionsworkloads sparen. Weitere Anforderungen für diese Option finden Sie unter [Preisinformationen für virtuelle Azure-Computer mit SQL Server](pricing-guidance.md#byol).

Um Ihre eigene Lizenz zu verwenden, können Sie entweder einen vorhandenen SQL Server-VM mit nutzungsbasierter Bezahlung ändern oder ein Image mit dem Präfix **{BYOL}** bereitstellen. Weitere Informationen zum Umstellen Ihres Lizenzierungsmodells zwischen nutzungsbasierter Bezahlung und BYOL finden Sie unter [Ändern des Lizenzierungsmodells für einen virtuellen SQL-Computer in Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Version | Betriebssystem | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

Ist es möglich, mit PowerShell ein älteres Image von SQL Server bereitzustellen, das im Azure-Portal nicht verfügbar ist? Um alle verfügbaren Images mit PowerShell anzuzeigen, verwenden Sie den folgenden Befehl:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Weitere Informationen zum Bereitstellen von SQL Server-VMs über PowerShell finden Sie unter [Bereitstellen von SQL Server-VMs mit Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM
Stellen Sie nach dem Erstellen des virtuellen SQL Server-Computers über Anwendungen oder Tools wie etwa SQL Server Management Studio (SSMS) eine Verbindung damit her. Eine entsprechende Anleitung finden Sie unter [Herstellen einer Verbindung mit einem virtuellen SQL Server-Computer in Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrieren Ihrer Daten
Wenn Sie über eine vorhandene Datenbank verfügen, empfiehlt sich die Verschiebung auf die neu bereitgestellte SQL Server-VM. Eine Liste mit Migrationsoptionen und Anleitungen finden Sie unter [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Erstellen und Verwalten von Azure SQL-Ressourcen im Azure-Portal

Das Azure-Portal bietet die Möglichkeit, [all Ihre Azure SQL-Ressourcen](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql), einschließlich SQL-VMs, auf einer einzigen Seite zu verwalten.

Wählen Sie zum Zugreifen auf die Seite **Azure SQL-Ressourcen** im Menü des Azure-Portals **Azure SQL** aus, oder suchen Sie auf einer beliebigen Seite nach **Azure SQL** und wählen Sie "Azure SQL" aus.

![Suche nach Azure SQL](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL bietet eine einfache und schnelle Möglichkeit, auf all Ihre Azure SQL-Datenbank-Instanzen, Pools für elastische Datenbanken, logische Server, verwaltete Instanzen und VMs zuzugreifen. Azure SQL ist weder ein Dienst noch eine Ressource. 

Um vorhandene Ressourcen zu verwalten, wählen Sie das gewünschte Element aus der Liste aus. Um neue Azure SQL-Ressourcen zu erstellen, klicken Sie auf **+ Hinzufügen**. 

![Erstellen einer Azure SQL-Ressource](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Zeigen Sie nach dem Klicken auf **+ Hinzufügen** zusätzliche Informationen zu den verschiedenen Optionen an, indem Sie auf einer beliebigen Kachel auf **Details anzeigen** klicken.

![Details auf Datenbankkachel](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Einzelheiten dazu finden Sie unter:

- [Erstellen einer Einzeldatenbank](../../database/single-database-create-quickstart.md)
- [Erstellen eines Pools für elastische Datenbanken](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Erstellen einer verwalteten Instanz](../../managed-instance/instance-create-quickstart.md)
- [Erstellen einer SQL Server-VM](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Aktualisierungsrichtlinie für SQL Server-VM-Images
Azure verwaltet für jedes unterstützte Betriebssystem, jede Version und jede Kombination von Editionen nur ein VM-Image. Dies bedeutet, dass Images im Laufe der Zeit aktualisiert und ältere Images entfernt werden. Weitere Informationen finden Sie im Abschnitt **Images** der [häufig gestellten Fragen zu SQL Server-VMs](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP)
Das Programm zur Verbesserung der Benutzerfreundlichkeit (Customer Experience Improvement Program, CEIP) ist standardmäßig aktiviert. Es sendet in regelmäßigen Abständen Berichte an Microsoft, damit die Nutzung von SQL Server verbessert werden kann. Für CEIP ist nur dann eine Verwaltungsaufgabe erforderlich, wenn Sie das Programm nach der Bereitstellung deaktivieren möchten. Sie können CEIP anpassen oder deaktivieren, indem Sie eine Verbindung mit der VM per Remotedesktop herstellen. Führen Sie anschließend das SQL Server-Hilfsprogramm **Fehler- und Verwendungsberichterstellung** aus. Befolgen Sie die Anleitung, um die Berichterstellung zu deaktivieren. Weitere Informationen zur Datensammlung finden Sie unter [Datenschutzbestimmungen für SQL Server](/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Verwandte Produkte und Dienste
### <a name="windows-virtual-machines"></a>Virtuelle Windows-Computer
* [Übersicht über Azure Virtual Machines](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Storage
* [Einführung in Microsoft Azure Storage](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Netzwerk
* [Virtuelle Netzwerke im Überblick](../../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../../virtual-network/public-ip-addresses.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server-Dokumentation](/sql/index)
* [Vergleich mit Azure SQL-Datenbank](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit SQL Server auf virtuellen Azure-Computern:

* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](sql-vm-create-portal-quickstart.md)

Antworten auf häufig gestellte Fragen zu virtuellen SQL Server-Computern:

* [SQL Server auf virtuellen Azure-Computern – FAQ](frequently-asked-questions-faq.md)

Anzeigen von Referenzarchitekturen zum Ausführen von n-schichtigen Anwendungen auf SQL Server in IaaS

* [n-schichtige Windows-Anwendung in Azure mit SQL Server](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Ausführen einer n-schichtigen Anwendung in mehreren Azure-Regionen für Hochverfügbarkeit](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)