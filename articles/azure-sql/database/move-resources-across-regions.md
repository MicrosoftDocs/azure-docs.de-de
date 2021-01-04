---
title: Verschieben von Ressourcen in eine neue Region
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Erfahren Sie, wie Sie eine Datenbank oder eine verwaltete Instanz in eine andere Region verschieben.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594163"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Verschieben von Ressourcen in eine neue Region – Azure SQL-Datenbank und Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In diesem Artikel finden Sie einen allgemeinen Workflow zum Verschieben einer Datenbank oder einer verwalteten Instanz in eine neue Region.

## <a name="overview"></a>Übersicht

Es gibt verschiedene Szenarien, in denen Sie Ihre vorhandene Datenbank oder verwaltete Instanz von einer Region in eine andere verschieben möchten. Beispiel: Sie erweitern Ihr Unternehmen in eine neue Region und möchten es für die neue Kundenbasis optimieren. Oder Sie müssen die Vorgänge aus Compliancegründen in eine andere Region verlegen. Oder Azure hat eine neue Region freigegeben, die eine bessere Nähe bietet und die Kundenzufriedenheit verbessert.  

Dieser Artikel bietet einen allgemeinen Workflow zum Verschieben von Ressourcen in eine andere Region. Der Workflow umfasst folgende Schritte:

1. Überprüfen der Voraussetzungen für das Verschieben
1. Vorbereiten auf das Verschieben der Ressourcen im Gültigkeitsbereich
1. Überwachen der Vorbereitung
1. Testen des Verschiebungsvorgangs
1. Initiieren der eigentlichen Verschiebung
1. Entfernen der Ressourcen aus der Quellregion

> [!NOTE]
> Dieser Artikel betrifft Migrationsvorgänge innerhalb der öffentlichen Azure-Cloud oder innerhalb derselben Sovereign Cloud.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Verschieben einer Datenbank

### <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen

1. Erstellen Sie einen Zielserver für jeden Quellserver.
1. Konfigurieren Sie die Firewall mit den richtigen Ausnahmen mithilfe von [PowerShell](scripts/create-and-configure-database-powershell.md).  
1. Konfigurieren Sie die Server mit den richtigen Anmeldedaten. Wenn Sie nicht der Abonnement- oder SQL Server-Administrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).
1. Wenn Ihre Datenbanken mit transparenter Datenverschlüsselung verschlüsselt sind und Sie Ihren eigenen Schlüssel in Azure Key Vault verwenden, stellen Sie sicher, dass das richtige Verschlüsselungsmaterial in den Zielregionen bereitgestellt wird. Weitere Informationen finden Sie unter [Azure SQL Transparent Data Encryption mithilfe von Schlüsseln, die vom Kunden in Azure Key Vault verwaltet werden](transparent-data-encryption-byok-overview.md).
1. Wenn die Überwachung auf Datenbankebene aktiviert ist, deaktivieren Sie sie, und aktivieren Sie stattdessen die Überwachung auf Serverebene. Nach dem Failover ist für die Überwachung auf Datenbankebene überregionaler Datenverkehr erforderlich. Dies ist nach der Verschiebung jedoch nicht gewünscht oder möglich.
1. Stellen Sie für Überwachung auf Serverebene Folgendes sicher:
   - Der Speichercontainer, Log Analytics oder der Event Hub mit den vorhandenen Überwachungsprotokollen wird in die Zielregion verschoben.
   - Die Überwachung ist auf dem Zielserver konfiguriert. Weitere Informationen finden Sie unter [Erste Schritte mit der SQL-Datenbanküberwachung](../../azure-sql/database/auditing-overview.md).
1. Wenn für Ihre Instanz eine Richtlinie für die Langzeitaufbewahrung (LTR) vorhanden ist, bleiben die vorhandenen LTR-Sicherungen dem aktuellen Server zugeordnet. Da ein anderer Zielserver verwendet wird, können Sie über den Quellserver auf die älteren LTR-Sicherungen in der Quellregion zugreifen, selbst wenn der Server gelöscht wird.

      > [!NOTE]
      > Dies reicht für das Verschieben zwischen der Sovereign Cloud und einer öffentlichen Region nicht aus. Für eine solche Migration müssen die LTR-Sicherungen auf den Zielserver verschoben werden. Dies wird derzeit nicht unterstützt.

### <a name="prepare-resources"></a>Vorbereiten der Ressourcen

1. Erstellen Sie eine [Failovergruppe](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) zwischen dem Server der Quelle und dem Server des Ziels.  
1. Fügen Sie die Datenbanken hinzu, die Sie in die Failovergruppe verschieben möchten.
  
    Die Replikation aller hinzugefügten Datenbanken wird automatisch initiiert. Weitere Informationen finden Sie unter [Bewährte Methoden für die Verwendung von Failovergruppen für Einzeldatenbanken](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Überwachen des Vorbereitungsprozesses

Sie können in regelmäßigen Abständen [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) aufrufen, um die Replikation Ihrer Datenbanken von der Quelle zum Ziel zu überwachen. Das Ausgabeprojekt von `Get-AzSqlDatabaseFailoverGroup` umfasst eine Eigenschaft für den **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) gibt an, dass die Datenbank synchronisiert ist und das Failover sicher ausgeführt werden kann.
- **ReplicationState = 0** (SEEDING) gibt an, dass das Seeding für die Datenbank noch nicht abgeschlossen ist und bei einem Failover ein Fehler auftritt.

### <a name="test-synchronization"></a>Testen der Synchronisierung

Nachdem **ReplicationState** den Wert `2` aufweist, stellen Sie eine Verbindung mit den einzelnen Datenbanken oder einer Teilmenge der Datenbanken mit dem sekundären Endpunkt `<fog-name>.secondary.database.windows.net` her, und führen Sie eine Abfrage der Datenbanken durch, um Konnektivität, die richtige Sicherheitskonfiguration und Datenreplikation sicherzustellen.

### <a name="initiate-the-move"></a>Initiieren der Verschiebung

1. Stellen Sie über den sekundären Endpunkt `<fog-name>.secondary.database.windows.net` eine Verbindung zum Zielserver her.
1. Verwenden Sie [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup), um die sekundäre verwaltete Instanz auf die primäre mit vollständiger Synchronisation umzuschalten. Dieser Vorgang wird erfolgreich ausgeführt, oder es wird ein Rollback ausgeführt.
1. Vergewissern Sie sich, dass der Befehl erfolgreich abgeschlossen wurde, indem Sie `nslook up <fog-name>.secondary.database.windows.net` verwenden, um sicherzustellen, dass der DNS CNAME-Eintrag auf die IP-Adresse der Zielregion verweist. Wenn beim Switch-Befehl ein Fehler auftritt, wird CNAME nicht aktualisiert.

### <a name="remove-the-source-databases"></a>Entfernen von Quelldatenbanken

Nachdem der Verschiebe Vorgang abgeschlossen ist, entfernen Sie die Ressourcen in der Quellregion, um unnötige Gebühren zu vermeiden.

1. Löschen Sie die Failovergruppe mit [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Löschen Sie jede Quelldatenbank mit [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) für jede der Datenbanken auf dem Quellserver. Dadurch werden die Links für die Georeplikation automatisch beendet.
1. Löschen Sie den Quellserver mit [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Entfernen Sie den Schlüsseltresor, die Überwachungsspeichercontainer, den Event Hub, die Azure Active Directory-Instanz (Azure AD) und andere abhängige Ressourcen, damit Ihnen diese nicht mehr in Rechnung gestellt werden.

## <a name="move-elastic-pools"></a>Verschieben der Pools für elastische Datenbanken

### <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen

1. Erstellen Sie einen Zielserver für jeden Quellserver.
1. Konfigurieren Sie die Firewall mit den richtigen Ausnahmen mithilfe von [PowerShell](scripts/create-and-configure-database-powershell.md).
1. Konfigurieren Sie die Server mit den richtigen Anmeldedaten. Wenn Sie nicht der Abonnement- oder Serveradministrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).
1. Wenn Ihre Datenbanken mit transparenter Datenverschlüsselung verschlüsselt sind und Sie Ihren eigenen Schlüssel in Azure Key Vault verwenden, stellen Sie sicher, dass das richtige Verschlüsselungsmaterial in den Zielregionen bereitgestellt wird.
1. Erstellen Sie für jeden Quellpool für elastische Datenbanken einen Zielpool, und stellen Sie sicher, dass der Pool in der gleichen Serviceebene, mit dem gleichen Namen und der gleichen Größe angelegt wird.
1. Wenn die Überwachung auf Datenbankebene aktiviert ist, deaktivieren Sie sie, und aktivieren Sie stattdessen die Überwachung auf Serverebene. Nach dem Failover ist für die Überwachung auf Datenbankebene überregionaler Datenverkehr erforderlich. Dies ist nach der Verschiebung jedoch nicht gewünscht oder nicht mehr möglich.
1. Stellen Sie für Überwachung auf Serverebene Folgendes sicher:
    - Der Speichercontainer, Log Analytics oder der Event Hub mit den vorhandenen Überwachungsprotokollen wird in die Zielregion verschoben.
    - Die Überwachungskonfiguration ist auf dem Zielserver konfiguriert. Weitere Informationen finden Sie unter [SQL-Datenbanküberwachung](../../azure-sql/database/auditing-overview.md).
1. Wenn für Ihre Instanz eine Richtlinie für die Langzeitaufbewahrung (LTR) vorhanden ist, bleiben die vorhandenen LTR-Sicherungen dem aktuellen Server zugeordnet. Da ein anderer Zielserver verwendet wird, können Sie über den Quellserver auf die älteren LTR-Sicherungen in der Quellregion zugreifen, selbst wenn der Server gelöscht wird.

      > [!NOTE]
      > Dies reicht für das Verschieben zwischen der Sovereign Cloud und einer öffentlichen Region nicht aus. Für eine solche Migration müssen die LTR-Sicherungen auf den Zielserver verschoben werden. Dies wird derzeit nicht unterstützt.

### <a name="prepare-to-move"></a>Vorbereiten der Verschiebung

1. Erstellen Sie eine separate [Failovergruppe](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) zwischen dem Pool für elastische Datenbanken auf dem Quellserver und dem jeweiligen Gegenstück auf dem Zielserver.
1. Fügen Sie alle Datenbanken im Pool zur Failovergruppe hinzu.

    Die Replikation der hinzugefügten Datenbanken wird automatisch initiiert. Weitere Informationen finden Sie unter [Bewährte Methoden für Failovergruppen mit Pools für elastische Datenbanken](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Es ist zwar möglich, eine Failovergruppe zu erstellen, die mehrere Pools für elastische Datenbanken umfasst, aber es wird dringend empfohlen, für jeden Pool eine eigene Failovergruppe zu erstellen. Wenn Sie eine große Anzahl von Datenbanken über mehrere Pools für elastische Datenbanken verteilt haben, die Sie verschieben müssen, können Sie die Vorbereitungsschritte parallel ausführen und dann den Verschiebungsschritt parallel starten. Dieser Prozess kann besser skaliert werden und nimmt weniger Zeit in Anspruch als mehrere Pools für elastische Datenbanken in derselben Failovergruppe.

### <a name="monitor-the-preparation-process"></a>Überwachen des Vorbereitungsprozesses

Sie können in regelmäßigen Abständen [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) aufrufen, um die Replikation Ihrer Datenbanken von der Quelle zum Ziel zu überwachen. Das Ausgabeprojekt von `Get-AzSqlDatabaseFailoverGroup` umfasst eine Eigenschaft für den **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) gibt an, dass die Datenbank synchronisiert ist und das Failover sicher ausgeführt werden kann.
- **ReplicationState = 0** (SEEDING) gibt an, dass das Seeding für die Datenbank noch nicht abgeschlossen ist und bei einem Failover ein Fehler auftritt.

### <a name="test-synchronization"></a>Testen der Synchronisierung

Nachdem **ReplicationState** den Wert `2` aufweist, stellen Sie eine Verbindung mit den einzelnen Datenbanken oder einer Teilmenge der Datenbanken mit dem sekundären Endpunkt `<fog-name>.secondary.database.windows.net` her, und führen Sie eine Abfrage der Datenbanken durch, um Konnektivität, die richtige Sicherheitskonfiguration und Datenreplikation sicherzustellen.

### <a name="initiate-the-move"></a>Initiieren der Verschiebung

1. Stellen Sie über den sekundären Endpunkt `<fog-name>.secondary.database.windows.net` eine Verbindung zum Zielserver her.
1. Verwenden Sie [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup), um die sekundäre verwaltete Instanz auf die primäre mit vollständiger Synchronisation umzuschalten. Dieser Vorgang wird entweder erfolgreich ausgeführt, oder es wird ein Rollback ausgeführt.
1. Vergewissern Sie sich, dass der Befehl erfolgreich abgeschlossen wurde, indem Sie `nslook up <fog-name>.secondary.database.windows.net` verwenden, um sicherzustellen, dass der DNS CNAME-Eintrag auf die IP-Adresse der Zielregion verweist. Wenn beim Switch-Befehl ein Fehler auftritt, wird CNAME nicht aktualisiert.

### <a name="remove-the-source-elastic-pools"></a>Entfernen der Quellpools für elastische Datenbanken

Nachdem der Verschiebe Vorgang abgeschlossen ist, entfernen Sie die Ressourcen in der Quellregion, um unnötige Gebühren zu vermeiden.

1. Löschen Sie die Failovergruppe mit [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Löschen Sie jeden Quellpool für elastische Datenbanken auf dem Quellserver mit [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool).
1. Löschen Sie den Quellserver mit [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver).
1. Entfernen Sie den Schlüsseltresor, die Überwachungsspeichercontainer, den Event Hub, die Azure AD-Instanz und andere abhängige Ressourcen, damit Ihnen diese nicht mehr in Rechnung gestellt werden.

## <a name="move-a-managed-instance"></a>Verschieben einer verwalteten Instanz

### <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen

1. Erstellen Sie für jede verwaltete Quellinstanz eine Zielinstanz von SQL Managed Instance derselben Größe in der Zielregion.  
1. Konfigurieren Sie das Netzwerk für eine verwaltete Instanz. Weitere Informationen finden Sie unter [Netzwerkkonfiguration](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Konfigurieren Sie die Ziel-Masterdatenbank mit den richtigen Anmeldedaten. Wenn Sie nicht der Abonnementadministrator oder SQL Managed Instance-Administrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist.
1. Wenn Ihre Datenbanken mit transparenter Datenverschlüsselung verschlüsselt sind und Sie Ihren eigenen Schlüssel in Azure Key Vault verwenden, stellen Sie sicher, dass die Azure Key Vault-Instanz mit identischen Verschlüsselungsschlüsseln in der Quell- und der Zielregion vorhanden ist. Weitere Informationen finden Sie unter [Transparente Datenverschlüsselung mit von Kunden verwalteten Schlüsseln in Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Wenn für die verwaltete Instanz Überwachung aktiviert ist, stellen Sie Folgendes sicher:
    - Der Speichercontainer oder der Event Hub mit den vorhandenen Protokollen wird in die Zielregion verschoben.
    - Die Überwachung ist auf der Zielinstanz konfiguriert. Weitere Informationen finden Sie unter [Überwachen mit SQL Managed Instance](../managed-instance/auditing-configure.md).
1. Wenn für Ihre Instanz eine Richtlinie für die Langzeitaufbewahrung (Long-Term Retention, LTR) gilt, sind die vorhandenen LTR-Sicherungen weiterhin mit der aktuellen Instanz verknüpft. Da eine andere Zielinstanz verwendet wird, können Sie über die Quellinstanz auf die älteren LTR-Sicherungen in der Quellregion zugreifen, selbst wenn die Instanz gelöscht wird.

  > [!NOTE]
  > Dies reicht für das Verschieben zwischen der Sovereign Cloud und einer öffentlichen Region nicht aus. Für eine solche Migration müssen die LTR-Sicherungen auf die Zielinstanz verschoben werden. Dies wird derzeit nicht unterstützt.

### <a name="prepare-resources"></a>Vorbereiten der Ressourcen

Erstellen Sie eine Failovergruppe zwischen jeder verwalteten Quellinstanz und der entsprechenden Zielinstanz von SQL Managed Instance.

Die Replikation aller Datenbanken auf den Instanzen wird automatisch initiiert. Weitere Informationen finden Sie unter [Autofailover-Gruppen](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Überwachen des Vorbereitungsprozesses

Sie können in regelmäßigen Abständen [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) aufrufen, um die Replikation Ihrer Datenbanken von der Quelle zum Ziel zu überwachen. Das Ausgabeprojekt von `Get-AzSqlDatabaseFailoverGroup` umfasst eine Eigenschaft für den **ReplicationState**:

- **ReplicationState = 2** (CATCH_UP) gibt an, dass die Datenbank synchronisiert ist und das Failover sicher ausgeführt werden kann.
- **ReplicationState = 0** (SEEDING) gibt an, dass das Seeding für die Datenbank noch nicht abgeschlossen ist und bei einem Failover ein Fehler auftritt.

### <a name="test-synchronization"></a>Testen der Synchronisierung

Sobald **ReplicationState**`2` ist, verbinden Sie sich mit der jeweiligen Datenbank oder einer Teilmenge von Datenbanken unter Verwendung des sekundären Endpunkts `<fog-name>.secondary.database.windows.net`, und führen Sie jede Abfrage an den Datenbanken durch, um die Konnektivität, die richtige Sicherheitskonfiguration und die Datenreplikation sicherzustellen.

### <a name="initiate-the-move"></a>Initiieren der Verschiebung

1. Stellen Sie über den sekundären Endpunkt `<fog-name>.secondary.database.windows.net` eine Verbindung mit der verwalteten Zielinstanz her.
1. Verwenden Sie [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup), um die sekundäre verwaltete Instanz auf die primäre mit vollständiger Synchronisation umzuschalten. Dieser Vorgang wird erfolgreich ausgeführt, oder es wird ein Rollback ausgeführt.
1. Vergewissern Sie sich, dass der Befehl erfolgreich abgeschlossen wurde, indem Sie `nslook up <fog-name>.secondary.database.windows.net` verwenden, um sicherzustellen, dass der DNS CNAME-Eintrag auf die IP-Adresse der Zielregion verweist. Wenn beim Switch-Befehl ein Fehler auftritt, wird CNAME nicht aktualisiert.

### <a name="remove-the-source-managed-instances"></a>Entfernen der verwalteten Quellinstanzen

Nachdem die Verschiebung abgeschlossen ist, entfernen Sie die Ressourcen in der Quellregion, um unnötige Gebühren zu vermeiden.

1. Löschen Sie die Failovergruppe mit [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Dadurch wird die Konfiguration der Failovergruppe gelöscht und die Links für die Georeplikation zwischen den beiden Instanzen beendet.
1. Löschen Sie die verwaltete Quellinstanz mithilfe von [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance).
1. Entfernen Sie alle zusätzlichen Ressourcen in der Ressourcengruppe, wie beispielsweise den virtuellen Cluster, das virtuelle Netzwerk und die Sicherheitsgruppe.

## <a name="next-steps"></a>Nächste Schritte

[Verwalten](manage-data-after-migrating-to-database.md) Sie Ihre Datenbank nach der Migration.
