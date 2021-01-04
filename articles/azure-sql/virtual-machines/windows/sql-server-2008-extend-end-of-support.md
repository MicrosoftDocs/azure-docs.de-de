---
title: Erweiterte Unterstützung für SQL Server 2008 und 2008 R2
description: Verlängern Sie den Support für SQL Server 2008 und SQL Server 2008 R2, indem Sie Ihre SQL Server-Instanz zu Azure migrieren oder verlängerten Support erwerben, um lokale Instanzen zu behalten.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 4c25adc16d14b4a5fb72ae0103ca05b193b40499
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359165"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Verlängerung des Supports für SQL Server 2008 und SQL Server 2008 R2 mit Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Sowohl SQL Server 2008 als auch SQL Server 2008 R2 haben das [Ende ihres Supportlebenszyklus (EOS)](https://www.microsoft.com/sql-server/sql-server-2008) erreicht. Da viele Kunden noch beide Versionen verwenden, stellen wir verschiedene Optionen zur Verfügung, um weiterhin Support zu erhalten. Sie können Ihre lokalen SQL Server-Instanzen zu virtuellen Azure-Computern (VMs) migrieren, zu Azure SQL-Datenbank migrieren oder lokal bleiben und erweiterte Sicherheitsupdates erwerben.

Im Gegensatz zu einer verwalteten Instanz ist für die Migration zu einer Azure-VM keine erneute Zertifizierung Ihrer Anwendungen erforderlich. Außerdem erhalten Sie im Gegensatz zur fortgesetzten lokalen Bereitstellung mit der Migration zu einer Azure-VM kostenlose erweiterte Sicherheitspatches.

Im weiteren Verlauf dieses Artikels werden Überlegungen zur Migration Ihrer SQL Server-Instanz zu einer Azure-VM aufgeführt.

Weitere Informationen zu den Optionen für das Ende des Supports finden Sie unter [Ende des Supports](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Bereitstellung

Im Azure Marketplace steht ein Image für **SQL Server 2008 R2 unter Windows Server 2008 R2** mit nutzungsbasierter Bezahlung bereit.

Kunden mit SQL Server 2008 müssen entweder selbst SQL Server 2008 R2 installieren oder ein entsprechendes Upgrade durchführen. Analog dazu müssen Kunden mit Windows Server 2008 ihren virtuellen Computer entweder über eine benutzerdefinierte VHD bereitstellen oder ein Upgrade auf Windows Server 2008 R2 durchführen.

Bei über den Azure Marketplace bereitgestellten Images ist die SQL-IaaS-Erweiterung bereits installiert. Die SQL-IaaS-Erweiterung ist eine Voraussetzung für die flexible Lizenzierung und automatisierte Patches. Kunden, die selbst installierte virtuelle Computer bereitstellen, müssen die SQL-IaaS-Erweiterung manuell installieren. Die SQL-IaaS-Erweiterung wird unter Windows Server 2008 nicht unterstützt.

> [!NOTE]
> Die SQL Server-Blätter **Erstellen** und **Verwalten** funktionieren zwar mit dem SQL Server 2008 R2-Image im Azure-Portal, die folgenden Features werden jedoch _nicht unterstützt_: Automatische Sicherungen, Azure Key Vault-Integration, R-Dienste und Speicherkonfiguration.

## <a name="licensing"></a>Lizenzierung
Bereitstellungen von SQL Server 2008 R2 mit nutzungsbasierter Bezahlung können in den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) konvertiert werden.

Kunden, die eine Software Assurance-basierte Lizenz in nutzungsbasierte Bezahlung umwandeln möchten, müssen den virtuellen Computer mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) registrieren. Nach dieser Registrierung kann beim SQL-Lizenztyp zwischen Azure-Hybridvorteil und nutzungsbasierter Bezahlung gewechselt werden.

Selbst installierte SQL Server 2008- oder SQL Server 2008 R2-Instanzen auf einem virtuellen Azure-Computer können mit der SQL-IaaS-Agent-Erweiterung registriert werden, und ihr Lizenztyp kann dann auf die nutzungsbasierte Bezahlung umgestellt werden.

## <a name="migration"></a>Migration
SQL Server-Instanzen am Ende des Supportlebenszyklus können mit manuellen Sicherungs-/Wiederherstellungsmethoden zu einem virtuellen Azure-Computer migriert werden. Dies ist die gängigste Methode für eine Migration von einer lokalen Instanz zu einem virtuellen Azure-Computer.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Für Massenmigrationsvorgänge empfehlen wir den Dienst [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md). Mit Azure Site Recovery können Kunden den gesamten virtuellen Computer replizieren – einschließlich SQL Server von einer lokalen Instanz zu einem virtuellen Azure-Computer.

SQL Server erfordert App-konsistente Azure Site Recovery-Momentaufnahmen, um die Wiederherstellung zu gewährleisten. Azure Site Recovery unterstützt App-konsistente Momentaufnahmen mit einem Mindestintervall von einer Stunde. Die kleinstmögliche Recovery Point Objective (RPO) für SQL Server mit Azure Site Recovery-Migrationen beträgt eine Stunde. Die Recovery Time Objective (RTO) beträgt zwei Stunden plus SQL Server-Wiederherstellungszeit.

### <a name="database-migration-service"></a>Database Migration Service

[Azure Database Migration Service](../../../dms/dms-overview.md) ist eine Option für Kunden, die von einer lokalen Instanz zu einem virtuellen Azure-Computer migrieren, indem sie SQL Server auf Version 2012 (oder eine höhere Version) upgraden.

## <a name="disaster-recovery"></a>Notfallwiederherstellung

Für die Notfallwiederherstellung von SQL Server am Ende des Supportlebenszyklus auf einem virtuellen Azure-Computer stehen folgende Lösungen zur Verfügung:

- **SQL Server-Sicherungen:** Verwenden Sie Azure Backup, um Ihre SQL Server 2008- und 2008 R2-Instanzen am Ende des Supportlebenszyklus mit einem RPO von 15 Minuten und Point-in-Time-Wiederherstellung vor Ransomware, unbeabsichtigtem Löschen und Beschädigungen zu schützen. Ausführlichere Informationen finden Sie in [diesem Artikel](../../../backup/sql-support-matrix.md#scenario-support).
- **Protokollversand:** Sie können ein Protokollversandreplikat in einer anderen Zone oder Azure-Region mit fortlaufenden Wiederherstellungen erstellen, um das RTO zu verringern. Der Protokollversand muss manuell konfiguriert werden.
- **Azure Site Recovery**: Sie können Ihren virtuellen Computer mithilfe der Azure Site Recovery-Replikation zonen- und regionsübergreifend replizieren. SQL Server erfordert App-konsistente Momentaufnahmen, um die Wiederherstellung im Notfall zu gewährleisten. Azure Site Recovery bietet für die Notfallwiederherstellung von SQL Server am Ende des Supportlebenszyklus eine RPO von mindestens einer Stunde und eine RTO von mindestens zwei Stunden (plus SQL Server-Wiederherstellungszeit).

## <a name="security-patching"></a>Sicherheitspatches

Erweiterte Sicherheitsupdates für virtuelle SQL Server-Computer werden über die Microsoft Update-Kanäle bereitgestellt, nachdem der virtuelle SQL Server-Computer mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) registriert wurde. Patches können manuell oder automatisch heruntergeladen werden.

*Automatisiertes Patchen* ist standardmäßig aktiviert. Beim automatisierten Patchen kann Azure automatisch Patches für SQL Server und das Betriebssystem anwenden. Sie können einen Wochentag, eine Uhrzeit und eine Dauer für ein Wartungsfenster angeben, wenn die SQL Server-IaaS-Erweiterung installiert wurde. Azure führt das Patchen in diesem Wartungsfenster durch. Für die Zeitplanung des Wartungsfensters wird die Uhrzeit des VM-Gebietsschemas verwendet. Weitere Informationen finden Sie unter [Automatisches Patchen für SQL Server auf virtuellen Azure-Computern](automated-patching.md).


## <a name="next-steps"></a>Nächste Schritte

Migrieren Ihres virtuellen SQL Server-Computers zu Azure:

* [Migrieren einer SQL Server-Datenbank zu SQL Server auf einem virtuellen Azure-Computer](migrate-to-vm-from-sql-server.md)

Erste Schritte mit SQL Server auf virtuellen Azure-Computern:

* [Bereitstellen eines virtuellen Windows-Computers mit SQL Server im Azure-Portal](sql-vm-create-portal-quickstart.md)

Antworten auf häufig gestellte Fragen zu virtuellen SQL Server-Computern:

* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure](frequently-asked-questions-faq.md)

Erfahren Sie mehr über die Optionen für das Ende des Supports und erweiterte Sicherheitsupdates:

* [Ende des Supports](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Erweiterte Sicherheitsupdates](/sql/sql-server/end-of-support/sql-server-extended-security-updates)