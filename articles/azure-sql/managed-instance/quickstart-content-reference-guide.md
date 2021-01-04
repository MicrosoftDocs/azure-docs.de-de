---
title: 'Erste Schritte: Inhaltsverweis'
titleSuffix: Azure SQL Managed Instance
description: 'Ein Verweis auf Inhalte, die Ihnen die ersten Schritte mit der verwalteten Azure SQL-Instanz erleichtern. '
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: quickstart
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 07/11/2019
ms.openlocfilehash: 936e4f8f54e92ba90372fff1c9d8dfc1982bbd62
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325114"
---
# <a name="getting-started-with-azure-sql-managed-instance"></a>Erste Schritte mit der verwalteten Azure SQL-Instanz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Die [verwaltete Azure SQL-Instanz](sql-managed-instance-paas-overview.md) erstellt eine Datenbank mit nahezu uneingeschränkter Kompatibilität mit der aktuellen SQL Server-Datenbank-Engine (Enterprise Edition). Darüber hinaus bietet sie eine native Implementierung eines [virtuellen Netzwerks (VNET)](../../virtual-network/virtual-networks-overview.md) zur Behebung allgemeiner Sicherheitsrisiken sowie ein vorteilhaftes [Geschäftsmodell](https://azure.microsoft.com/pricing/details/sql-database/) für Kunden mit vorhandener SQL Server-Instanz.

In diesem Artikel finden Sie Verweise auf Inhalte, die Ihnen zeigen, wie Sie schnell eine verwaltete SQL-Instanz konfigurieren und erstellen sowie Ihre Datenbanken migrieren können.

## <a name="quickstart-overview"></a>Schnellstartübersicht

In den folgenden Schnellstarts erfahren Sie, wie Sie schnell eine verwaltete SQL-Instanz erstellen, eine VM oder eine Point-to-Site-VPN-Verbindung für Clientanwendungen konfigurieren und eine Datenbank mithilfe einer `.bak`-Datei in Ihrer neuen verwalteten SQL-Instanz wiederherstellen.

### <a name="configure-environment"></a>Konfigurieren der Umgebung

Zunächst müssen Sie Ihre verwaltete SQL-Instanz mit der Netzwerkumgebung erstellen, in der sie angeordnet werden soll. Außerdem müssen Sie die Verbindung vom Computer bzw. virtuellen Computer aktivieren, auf dem Sie Abfragen für die verwaltete SQL-Instanz ausführen. Sie können die folgenden Leitfäden verwenden:

- [Erstellen einer verwalteten SQL-Instanz im Azure-Portal](instance-create-quickstart.md). Im Azure-Portal können Sie die erforderlichen Parameter (Benutzername/Kennwort, Anzahl von Kernen und maximale Speichermenge) konfigurieren und automatisch die Azure-Netzwerkumgebung erstellen, ohne sich mit Netzwerkdetails oder Infrastrukturanforderungen befassen zu müssen. Sie müssen lediglich sicherstellen, dass Sie über einen [Abonnementtyp](resource-limits.md#supported-subscription-types) verfügen, für den derzeit die Erstellung einer verwalteten SQL-Instanz zulässig ist. Wenn Sie ein eigenes Netzwerk verwenden oder das Netzwerk anpassen möchten, helfen Ihnen die Informationen unter [Konfigurieren eines vorhandenen virtuellen Netzwerks für eine verwaltete Azure SQL-Instanz](vnet-existing-add-subnet.md) oder [Erstellen eines virtuellen Netzwerks für eine verwaltete Azure SQL-Instanz](virtual-network-subnet-create-arm-template.md) weiter.
- Eine verwaltete SQL-Instanz wird in ihrem eigenen VNET ohne öffentlichen Endpunkt erstellt. Für den Clientanwendungszugriff können Sie wie in einem der folgenden Schnellstartanleitungen beschrieben **eine VM in demselben VNET (in einem anderen Subnetz)** oder **eine Point-to-Site-VPN-Verbindung mit dem VNET auf Ihrem Clientcomputer erstellen**:
  - Aktivieren Sie den [öffentlichen Endpunkt](public-endpoint-configure.md) auf Ihrer verwalteten SQL-Instanz, um direkt aus Ihrer Umgebung auf Ihre Daten zuzugreifen.
  - Für die Konnektivität von Clientanwendungen (einschließlich SQL Server Management Studio) erstellen Sie eine [Azure-VM im VNET der verwalteten SQL-Instanz](connect-vm-instance-configure.md).
  - Richten Sie auf dem Clientcomputer, auf dem Sie SQL Server Management Studio und andere Anwendungen mit Clientkonnektivität ausführen, eine [Point-to-Site-VPN-Verbindung mit Ihrer verwalteten SQL-Instanz](point-to-site-p2s-configure.md) ein. Dies ist eine der zwei Optionen für die Konnektivität mit Ihrer verwalteten SQL-Instanz und dem zugehörigen VNET.

  > [!NOTE]
  > - Sie können auch ExpressRoute oder eine Site-to-Site-Verbindung über Ihr lokales Netzwerk verwenden. Diese Methoden werden in den Schnellstarts jedoch nicht behandelt.
  > - Wenn Sie die Beibehaltungsdauer von „0“ (unbegrenzte Aufbewahrung) in einen anderen Wert ändern, beachten Sie, dass die Beibehaltung nur auf Protokolle angewendet wird, die nach dem Ändern des Beibehaltungswerts geschrieben wurden (Protokolle, die in dem Zeitraum geschrieben wurden, in dem die Beibehaltung auf unbegrenzt festgelegt war, bleiben auch nach Aktivieren der Beibehaltung erhalten).

Als Alternative zur manuellen Erstellung der verwalteten SQL-Instanz können Sie mithilfe von [PowerShell](scripts/create-configure-managed-instance-powershell.md), [PowerShell mit einer Resource Manager-Vorlage](scripts/create-powershell-azure-resource-manager-template.md) oder der [Azure-Befehlszeilenschnittstelle](/cli/azure/sql/mi#az-sql-mi-create) (Azure CLI) ein Skript erstellen und diesen Prozess automatisieren.

### <a name="migrate-your-databases"></a>Migrieren Ihrer Datenbanken

Nachdem Sie eine verwaltete SQL-Instanz erstellt und den Zugriff konfiguriert haben, können Sie mit der Migration Ihrer SQL Server-Datenbanken beginnen. Bei der Migration kann ein Fehler auftreten, wenn die zu migrierende Quelldatenbank nicht unterstützte Features enthält. Um Fehler zu vermeiden und die Kompatibilität zu überprüfen, können Sie den [Datenmigrations-Assistenten (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) verwenden, um Ihre Datenbanken in SQL Server zu analysieren und sämtliche Probleme zu ermitteln, die unter Umständen die Migration zur verwalteten SQL-Instanz verhindern (z. B. das Vorhandensein von [FileStream](/sql/relational-databases/blob/filestream-sql-server) oder mehreren Protokolldateien). Nachdem Sie diese Probleme behoben haben, sind Ihre Datenbanken bereit für die Migration zur verwalteten SQL-Instanz. Der [Assistent für Datenbankexperimente](/sql/dea/database-experimentation-assistant-overview) ist ein nützliches Tool, das Ihre Workload in SQL Server aufzeichnen und in der verwalteten SQL-Instanz wiedergeben kann, um zu ermitteln, ob nach der Migration zu einer verwalteten SQL-Instanz Leistungsprobleme zu erwarten sind.

Sobald Sie sicher sind, dass Ihre Datenbank zu einer verwalteten SQL-Instanz migriert werden kann, können Sie die nativen Wiederherstellungsfunktionen von SQL Server verwenden, um eine Datenbank aus einer `.bak`-Datei in einer verwalteten SQL-Instanz wiederherzustellen. Sie können diese Methode verwenden, um Datenbanken von der SQL Server-Datenbank-Engine, die lokal installiert ist, oder von Azure Virtual Machines zu migrieren. Einen Schnellstart finden Sie unter [Wiederherstellen einer Datenbank aus einer Sicherung in einer verwalteten SQL-Instanz](restore-sample-database-quickstart.md). In diesem Schnellstart stellen Sie eine Datenbank mit dem Transact-SQL-Befehl `RESTORE` aus einer in Azure Blob Storage gespeicherten `.bak`-Datei wieder her.

> [!TIP]
> Informationen zum Erstellen einer Sicherung Ihrer Datenbank in Azure Blob Storage mit dem Transact-SQL-Befehl `BACKUP` finden Sie unter [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url).

In diesen Schnellstarts erfahren Sie, wie Sie schnell eine Datenbanksicherung erstellen, konfigurieren und in einer verwalteten SQL-Instanz wiederherstellen. In einigen Szenarien müssen Sie die Bereitstellung der verwalteten SQL-Instanz und der erforderlichen Netzwerkumgebung anpassen oder automatisieren. Diese Szenarien werden im Anschluss beschrieben.

## <a name="customize-network-environment"></a>Anpassen der Netzwerkumgebung

Wenn die Instanz über das [Azure-Portal](instance-create-quickstart.md) erstellt wird, kann das VNET/Subnetz zwar automatisch konfiguriert werden, manchmal empfiehlt es sich jedoch, es vor der Erstellung von Instanzen in einer verwalteten SQL-Instanz zu erstellen, da Sie in diesem Fall die Parameter des VNETs/Subnetzes konfigurieren können. Die einfachste Möglichkeit zum Erstellen und Konfigurieren der Netzwerkumgebung ist die Verwendung der [Azure-Vorlage für die Ressourcenbereitstellung](virtual-network-subnet-create-arm-template.md). Diese erstellt und konfiguriert Ihr Netzwerk und das Subnetz, in dem die Instanz platziert wird. Sie müssen lediglich auf die Bereitstellungsschaltfläche von Azure Resource Manager klicken und das Formular mit Parametern ausfüllen.

Alternativ können Sie die Erstellung des Netzwerks auch mit diesem [PowerShell-Skript](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) automatisieren.

Wenn Sie bereits über ein VNET und ein Subnetz verfügen, in dem Sie Ihre verwaltete SQL-Instanz bereitstellen möchten, müssen diese die [Netzwerkanforderungen](connectivity-architecture-overview.md#network-requirements) erfüllen. Verwenden Sie dieses [PowerShell-Skript, um zu überprüfen, ob Ihr Subnetz korrekt konfiguriert ist](vnet-existing-add-subnet.md). Das Skript überprüft Ihr Netzwerk, meldet Probleme, informiert Sie über erforderliche Änderungen und bietet anschließend an, die nötigen Änderungen an Ihrem VNET/Subnetz vorzunehmen. Führen Sie dieses Skript aus, wenn Sie Ihr VNET/Subnetz nicht manuell konfigurieren möchten. Sie können es auch nach jeder größeren Neukonfigurationen Ihrer Netzwerkinfrastruktur ausführen. Wenn Sie Ihr eigenes Netzwerk erstellen und konfigurieren möchten, lesen Sie [Verbindungsarchitektur](connectivity-architecture-overview.md) und diesen [ultimativen Leitfaden für das Erstellen und Konfigurieren einer Umgebung für die verwaltete SQL-Instanz](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-sql-managed-instance"></a>Migrieren zu einer verwalteten SQL-Instanz

In den zuvor erwähnten Schnellstartanleitungen erfahren Sie, wie Sie schnell eine verwaltete SQL-Instanz einrichten und Ihre Datenbanken mithilfe der nativen `RESTORE`-Funktion migrieren. Dies ist ein guter Ausgangspunkt, wenn Sie schnelle Proof of Concepts durchführen und sicherstellen möchten, dass Ihre Lösung auf der verwalteten Instanz ausgeführt werden kann.

Um jedoch Ihre Produktionsdatenbank oder sogar Entwicklungs-/Testdatenbanken zu migrieren, die Sie für Leistungstests verwenden möchten, müssen Sie den Einsatz einiger zusätzlicher Verfahren in Erwägung ziehen, wie zum Beispiel:

- Leistungstests: Sie sollten die Baselineleistungsmetriken auf Ihrer SQL Server-Quellinstanz messen und mit den Leistungsmetriken auf der verwalteten SQL-Zielinstanz vergleichen, zu der Sie die Datenbank migriert haben. Erfahren Sie mehr über die [bewährten Methoden für den Leistungsvergleich](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Onlinemigration: Bei Verwendung der in diesem Artikel beschriebenen nativen `RESTORE`-Funktion müssen Sie jedoch warten, bis die Datenbanken wiederhergestellt wurden (und in Azure Blob Storage kopiert wurden, sofern sie dort noch nicht gespeichert sind). Dies führt insbesondere bei größeren Datenbanken zu einer Downtime Ihrer Anwendung. Verwenden Sie für Ihre Produktionsdatenbank [Data Migration Service (DMS)](../../dms/tutorial-sql-server-to-managed-instance.md?toc=%2fazure%2fsql-database%2ftoc.json), um die Datenbank mit minimaler Downtime zu migrieren. DMS pusht die in Ihrer Quelldatenbank vorgenommenen Änderungen inkrementell in die Datenbank der verwalteten SQL-Instanz, die wiederhergestellt wird, und minimiert so die Downtime. Dadurch können Sie Ihre Anwendung schnell und mit minimaler Downtime von der Quell- auf die Zieldatenbank umstellen.

Erfahren Sie mehr über den [empfohlenen Migrationsprozess](migrate-to-instance-from-sql-server.md).

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich mit der [allgemeinen Liste mit unterstützten Features in verwalteten SQL-Instanzen](../database/features-comparison.md) sowie mit [Details und bekannten Problemen](transact-sql-tsql-differences-sql-server.md) vertraut.
- Informieren Sie sich über [technische Eigenschaften der verwalteten SQL-Instanz](resource-limits.md#service-tier-characteristics).
- Weitere Informationen finden Sie unter [Verwenden einer verwalteten SQL-Instanz](how-to-content-reference-guide.md).
- [Identifizieren der richtigen SKU der verwalteten Azure SQL-Instanz für Ihre lokale Datenbank](/sql/dma/dma-sku-recommend-sql-db/)