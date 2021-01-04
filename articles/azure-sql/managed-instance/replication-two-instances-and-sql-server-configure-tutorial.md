---
title: Konfigurieren der Transaktionsreplikation zwischen verwalteten Azure SQL-Instanzen und SQL Server
description: In diesem Tutorial wird die Replikation zwischen einer verwalteten Verlegerinstanz, einer verwalteten Verteilerinstanz und einem SQL Server-Abonnenten auf einer Azure-VM konfiguriert. Darüber hinaus werden erforderliche Netzwerkkomponenten konfiguriert, z. B. die private DNS-Zone und das VNET-Peering.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/21/2019
ms.openlocfilehash: d2b45f5b51f4656294632aa46f679a7a09c06ed3
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593925"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>Tutorial: Konfigurieren der Transaktionsreplikation zwischen verwalteten Azure SQL-Instanzen und SQL Server
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Mithilfe der Transaktionsreplikation können Sie Daten von einer Datenbank auf eine andere replizieren, die in SQL Server oder [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) gehostet wird. SQL Managed Instance kann in der Replikationstopologie ein Verleger, Verteiler oder Abonnent sein. Unter [Konfigurationen für die Transaktionsreplikation](replication-transactional-overview.md#common-configurations) finden Sie die verfügbaren Konfigurationen. 

Die Transaktionsreplikation ist zurzeit als öffentliche Vorschau für SQL Managed Instance verfügbar. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Konfigurieren einer verwalteten Instanz als Replikationsverleger.
> - Konfigurieren einer verwalteten Instanz als Replikationsverteiler.
> - Konfigurieren von SQL Server als Abonnent.

![Replikation zwischen einem Verleger/Verteiler einer verwalteten Instanz und einem SQL Server-Abonnenten](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

Dieses Tutorial ist für erfahrene Benutzer bestimmt. Es wird vorausgesetzt, dass sich Benutzer sowohl für verwaltete Instanzen als auch für SQL Server-VMs in Azure mit der Bereitstellung und Verbindungsherstellung auskennen. 


> [!NOTE]
> In diesem Artikel wird die Verwendung der [Transaktionsreplikation](/sql/relational-databases/replication/transactional/transactional-replication) in einer verwalteten SQL-Datenbank-Instanz beschrieben. Sie steht nicht im Zusammenhang mit [Failovergruppen](../database/auto-failover-group-overview.md). Dieses verwaltete Azure SQL Managed Instance-Feature ermöglicht das Erstellen vollständiger Lesereplikate einzelner Instanzen. Beim Konfigurieren der [Transaktionsreplikation mit Failovergruppen](replication-transactional-overview.md#with-failover-groups) sind weitere Aspekte zu berücksichtigen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial wird Folgendes vorausgesetzt:

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Erfahrung mit der Bereitstellung von zwei verwalteten Instanzen in demselben virtuellen Netzwerk.
- Ein SQL Server-Abonnent, entweder lokal oder auf einer Azure-VM. In diesem Tutorial wird eine Azure-VM verwendet.  
- [SQL Server Management Studio (SSMS) 18.0 oder höher](/sql/ssms/download-sql-server-management-studio-ssms).
- Die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps)
- Für Ports 445 und 1433 ist SQL-Datenverkehr für die Azure Firewall und die Windows-Firewall zulässig.

## <a name="create-the-resource-group"></a>Erstellen der Ressourcengruppe

Verwenden Sie den folgenden PowerShell-Codeausschnitt, um eine neue Ressourcengruppe zu erstellen:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="create-two-managed-instances"></a>Erstellen von zwei verwalteten Instanzen

Erstellen Sie in dieser neuen Ressourcengruppe mit dem [Azure-Portal](https://portal.azure.com) zwei neue verwaltete Instanzen.

- Der Name der verwalteten Verlegerinstanz sollte `sql-mi-publisher` lauten (zuzüglich einiger weiterer Zeichen zur zufälligen Benennung), und als Name des virtuellen Netzwerks sollte `vnet-sql-mi-publisher` verwendet werden.
- Der Name der verwalteten Verteilerinstanz sollte `sql-mi-distributor` lauten (zuzüglich einiger weiterer Zeichen zur zufälligen Benennung) und sich _in demselben virtuellen Netzwerk wie die verwaltete Verlegerinstanz_ befinden.

   ![Verwenden des Verleger-VNet für den Verteiler](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

Weitere Informationen zur Erstellung einer verwalteten Instanz finden Sie unter [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz](instance-create-quickstart.md).

  > [!NOTE]
  > Der Einfachheit halber und aus dem Grund, dass es die am häufigsten verwendete Konfiguration ist, wird in diesem Tutorial empfohlen, die verwaltete Verteilerinstanz in demselben virtuellen Netzwerk anzuordnen, in dem sich auch der Verleger befindet. Es ist aber auch möglich, den Verteiler in einem separaten virtuellen Netzwerk zu erstellen. Hierfür müssen Sie das VNET-Peering zwischen den virtuellen Netzwerken des Verlegers und Verteilers und anschließend zwischen den virtuellen Netzwerken des Verteilers und des Abonnenten konfigurieren.

## <a name="create-a-sql-server-vm"></a>Erstellen einer SQL Server-VM

Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen virtuellen SQL Server-Computer (SQL Server-VM). Die SQL Server-VM sollte über die folgenden Merkmale verfügen:

- Name: `sql-vm-sub`
- Image: SQL Server 2016 oder höher
- Ressourcengruppe: wie bei der verwalteten Instanz
- Virtuelles Netzwerk: `sql-vm-sub-vnet`

Weitere Informationen zum Bereitstellen einer SQL Server-VM in Azure finden Sie unter [Schnellstart: Erstellen einer SQL Server-VM](../virtual-machines/windows/sql-vm-create-portal-quickstart.md).

## <a name="configure-vnet-peering"></a>Konfigurieren von VNET-Peering

Konfigurieren Sie das VNET-Peering, um die Kommunikation zwischen dem virtuellen Netzwerk der zwei verwalteten Instanzen und dem virtuellen Netzwerk von SQL Server zu ermöglichen. Verwenden Sie hierzu den folgenden PowerShell-Codeausschnitt:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VNet peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VNet peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Nachdem das VNET-Peering eingerichtet wurde, sollten Sie die Konnektivität testen, indem Sie SQL Server Management Studio (SSMS) in SQL Server starten und mit beiden verwalteten Instanzen eine Verbindung herstellen. Weitere Informationen zum Herstellen einer Verbindung mit einer verwalteten Instanz per SSMS finden Sie unter [Herstellen einer Verbindung mit der verwalteten SQL-Instanz per SSMS](point-to-site-p2s-configure.md#connect-with-ssms).

![Testen der Konnektivität mit den verwalteten Instanzen](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="create-a-private-dns-zone"></a>Erstellen einer privaten DNS-Zone

Eine private DNS-Zone ermöglicht das DNS-Routing zwischen den verwalteten Instanzen und SQL Server.

### <a name="create-a-private-dns-zone"></a>Erstellen einer privaten DNS-Zone

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie die Option **Ressource erstellen** aus, um eine neue Azure-Ressource zu erstellen.
1. Suchen Sie auf dem Azure Marketplace nach `private dns zone`.
1. Wählen Sie die Ressource **Private DNS-Zone** aus, die von Microsoft veröffentlicht wird, und anschließend **Erstellen**, um die DNS-Zone zu erstellen.
1. Wählen Sie in der Dropdownliste das Abonnement und die Ressourcengruppe aus.
1. Geben Sie einen beliebigen Namen für Ihre DNS-Zone an, z. B. `repldns.com`.

   ![Erstellen einer privaten DNS-Zone](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Parameter für Ihre private DNS-Zone, und wählen Sie anschließend **Erstellen** aus, um Ihre Ressource zu erstellen.

### <a name="create-an-a-record"></a>Erstellen eines A-Eintrags

1. Navigieren Sie zu Ihrer neuen **Privaten DNS-Zone**, und wählen Sie **Übersicht** aus.
1. Wählen Sie **+ Eintrag** aus, um einen neuen A-Eintrag zu erstellen.
1. Geben Sie den Namen Ihrer SQL Server-VM und die private interne IP-Adresse an.

   ![Konfigurieren eines A-Eintrags](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. Wählen Sie **OK** aus, um den A-Eintrag zu erstellen.

### <a name="link-the-virtual-network"></a>Verknüpfen des virtuellen Networks

1. Navigieren Sie zu Ihrer neuen **Privaten DNS-Zone**, und wählen Sie **Verknüpfungen virtueller Netzwerke** aus.
1. Wählen Sie **+ Hinzufügen**.
1. Geben Sie einen Namen für die Verknüpfung an, z. B. `Pub-link`.
1. Wählen Sie in der Dropdownliste Ihr Abonnement und anschließend das virtuelle Netzwerk für Ihre verwaltete Verlegerinstanz aus.
1. Aktivieren Sie das Kontrollkästchen neben **Automatische Registrierung aktivieren**.

   ![Erstellen einer VNet-Verknüpfung](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. Wählen Sie **OK** aus, um eine Verknüpfung mit Ihrem virtuellen Netzwerk einzurichten.
1. Wiederholen Sie diese Schritte, um einen Link für das virtuelle Abonnentennetzwerk mit einem Namen wie `Sub-link` hinzuzufügen.

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

[Erstellen Sie ein Azure-Speicherkonto](../../storage/common/storage-account-create.md#create-a-storage-account) für das Arbeitsverzeichnis und anschließend im Speicherkonto eine [Dateifreigabe](../../storage/files/storage-how-to-create-file-share.md).

Kopieren Sie den Dateifreigabepfad im Format: `\\storage-account-name.file.core.windows.net\file-share-name`

Beispiel: `\\replstorage.file.core.windows.net\replshare`

Kopieren Sie die Verbindungszeichenfolge für den Speicherzugriffsschlüssel im folgenden Format: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Beispiel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).

## <a name="create-a-database"></a>Erstellen einer Datenbank

Erstellen Sie eine neue Datenbank auf der verwalteten Verlegerinstanz. Gehen Sie dazu folgendermaßen vor:

1. Starten Sie SQL Server Management Studio unter on SQL Server.
1. Stellen Sie eine Verbindung mit der verwalteten Instanz `sql-mi-publisher` her.
1. Öffnen Sie ein Fenster vom Typ **Neue Abfrage**, und führen Sie die folgende T-SQL-Abfrage aus, um die Datenbank zu erstellen.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="configure-distribution"></a>Konfigurieren der Verteilung

Nachdem die Verbindung hergestellt wurde und Sie über eine Beispieldatenbank verfügen, können Sie die Verteilung auf Ihrer verwalteten Instanz `sql-mi-distributor` konfigurieren. Gehen Sie dazu folgendermaßen vor:

1. Starten Sie SQL Server Management Studio unter on SQL Server.
1. Stellen Sie eine Verbindung mit der verwalteten Instanz `sql-mi-distributor` her.
1. Öffnen Sie ein Fenster vom Typ **Neue Abfrage**, und führen Sie den folgenden Transact-SQL-Code aus, um die Verteilung auf der verwalteten Verteilerinstanz zu konfigurieren:

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Achten Sie darauf, dass Sie für den Parameter @working_directory nur umgekehrte Schrägstriche (`\`) verwenden. Die Verwendung eines regulären Schrägstrichs (`/`) kann zu einem Fehler führen, wenn die Verbindung mit der Dateifreigabe hergestellt wird.

1. Stellen Sie eine Verbindung mit der verwalteten Instanz `sql-mi-publisher` her.
1. Öffnen Sie ein Fenster vom Typ **Neue Abfrage**, und führen Sie den folgenden Transact-SQL-Code aus, um den Verteiler für den Verleger zu registrieren:

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="create-the-publication"></a>Erstellen der Veröffentlichung

Nachdem die Verteilung konfiguriert wurde, können Sie nun die Veröffentlichung erstellen. Gehen Sie dazu folgendermaßen vor:

1. Starten Sie SQL Server Management Studio unter on SQL Server.
1. Stellen Sie eine Verbindung mit der verwalteten Instanz `sql-mi-publisher` her.
1. Erweitern Sie im **Objekt-Explorer** den Knoten **Replikation**, und klicken Sie mit der rechten Maustaste auf den Ordner **Lokale Veröffentlichung**. Wählen Sie **Neue Veröffentlichung...** aus.
1. Wählen Sie **Weiter** aus, um die Willkommensseite zu verlassen und den Vorgang fortzusetzen.
1. Wählen Sie auf der Seite **Veröffentlichungsdatenbank** die Datenbank `ReplTutorial` aus, die Sie zuvor erstellt haben. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Publikationstyp** die Option **Transaktionsveröffentlichung** aus. Wählen Sie **Weiter** aus.
1. Aktivieren Sie auf der Seite **Artikel** das Kontrollkästchen neben **Tabellen**. Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Tabellenzeilen filtern** die Option **Weiter** aus, ohne Filter hinzuzufügen.
1. Aktivieren Sie auf der Seite **Momentaufnahme-Agent** das Kontrollkästchen neben der Option **Create snapshot immediately and keep the snapshot available to initialize subscriptions** (Momentaufnahme sofort erstellen und zum Initialisieren von Abonnements vorhalten). Wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Agent-Sicherheit** die Option **Sicherheitseinstellungen...** aus. Geben Sie die Anmeldeinformationen für die SQL Server-Anmeldung an, die für den Momentaufnahmen-Agent und die Verbindungsherstellung mit dem Verleger verwendet werden sollen. Wählen Sie **OK** aus, um die Seite **Sicherheit für den Momentaufnahme-Agent** zu schließen. Wählen Sie **Weiter** aus.

   ![Konfigurieren der Sicherheit für den Momentaufnahme-Agent](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. Wählen Sie auf der Seite **Aktionen des Assistenten** die Option **Veröffentlichung erstellen** und (optional) **Skriptdatei mit Schritten zur Veröffentlichungserstellung generieren** aus, wenn Sie dieses Skript zur späteren Verwendung speichern möchten.
1. Geben Sie Ihrer Veröffentlichung auf der Seite **Assistenten abschließen** den Namen `ReplTest`, und wählen Sie **Weiter** aus, um Ihre Veröffentlichung zu erstellen.
1. Aktualisieren Sie nach der Erstellung der Veröffentlichung im **Objekt-Explorer** den Knoten **Replikation**, und erweitern Sie **Lokale Veröffentlichungen**, um Ihre neue Veröffentlichung anzuzeigen.

## <a name="create-the-subscription"></a>Erstellen des Abonnements

Nachdem die Veröffentlichung erstellt wurde, können Sie das Abonnement erstellen. Gehen Sie dazu folgendermaßen vor:

1. Starten Sie SQL Server Management Studio unter on SQL Server.
1. Stellen Sie eine Verbindung mit der verwalteten Instanz `sql-mi-publisher` her.
1. Öffnen Sie ein Fenster vom Typ **Neue Abfrage**, und führen Sie den folgenden Transact-SQL-Code aus, um das Abonnement und den Verteilungs-Agent hinzuzufügen. Verwenden Sie den DNS als Teil des Abonnentennamens.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="test-replication"></a>Testen der Replikation

Nachdem die Replikation konfiguriert wurde, können Sie sie testen, indem Sie neue Elemente auf dem Verleger einfügen und beobachten, wie die Änderungen an den Abonnenten weitergegeben werden.

Führen Sie den folgenden T-SQL-Codeausschnitt aus, um die Zeilen auf dem Abonnenten anzuzeigen:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Führen Sie den folgenden T-SQL-Codeausschnitt aus, um zusätzliche Zeilen auf dem Verleger einzufügen. Überprüfen Sie dann die Zeilen erneut auf dem Abonnenten.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Ressourcengruppe.
1. Wählen Sie die verwaltete(n) Instanz(en) und dann **Löschen** aus. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus. Dieser Vorgang kann im Hintergrund einige Zeit in Anspruch nehmen. Bis der Vorgang abgeschlossen ist, können Sie den *virtuellen Cluster* oder andere abhängige Ressourcen nicht löschen. Überwachen Sie den Löschvorgang auf der Registerkarte **Aktivität**, um zu bestätigen, dass die verwaltete Instanz gelöscht wurde.
1. Nachdem die verwaltete Instanz gelöscht wurde, löschen Sie den *virtuellen Cluster*, indem Sie ihn in der Ressourcengruppe auswählen und dann **Löschen** auswählen. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus.
1. Löschen Sie alle verbleibenden Ressourcen. Geben Sie `yes` in das Textfeld ein, um zu bestätigen, dass Sie die Ressource löschen möchten, und wählen Sie dann **Löschen** aus.
1. Löschen Sie die Ressourcengruppe, indem Sie die Option **Ressourcengruppe löschen** auswählen, geben Sie den Namen der Ressourcengruppe (`myResourceGroup`) ein, und wählen Sie dann **Löschen** aus.

## <a name="known-errors"></a>Bekannte Fehler

### <a name="windows-logins-are-not-supported"></a>Windows-Anmeldungen werden nicht unterstützt

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Der Agent wurde mit einer Windows-Anmeldung konfiguriert, aber stattdessen muss eine SQL Server-Anmeldung verwendet werden. Nutzen Sie die Seite **Agentsicherheit** unter **Veröffentlichungseigenschaften**, um die Anmeldeinformationen auf eine SQL Server-Anmeldung umzustellen.

### <a name="failed-to-connect-to-azure-storage"></a>Fehler beim Herstellen der Verbindung mit Azure Storage

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Obtained Azure Storage Connection String for replstorage 2019-11-19 02:21:05.07 Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Failed to connect to Azure Storage '' with OS error: 53.

Der Grund hierfür ist wahrscheinlich, dass Port 445 entweder in der Azure Firewall, der Windows-Firewall oder in beiden Firewalls nicht geöffnet ist.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Dieser Fehler wird unter Umständen verursacht, wenn im Dateipfad für die Dateifreigabe anstelle eines umgekehrten Schrägstrichs ein regulärer Schrägstrich verwendet wird.
  
  - Dies ist die richtige Vorgehensweise: `\\replstorage.file.core.windows.net\replshare`
  - Dies kann zum Betriebssystemfehler 55 führen: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Verbindung mit Abonnent kann nicht hergestellt werden

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Lösungsvorschläge:

- Stellen Sie sicher, dass Port 1433 geöffnet ist.
- Stellen Sie sicher, dass TCP/IP für den Abonnenten aktiviert ist.
- Vergewissern Sie sich, dass der DNS-Name beim Erstellen des Abonnenten verwendet wurde.
- Vergewissern Sie sich, dass Ihre virtuellen Netzwerke in der privaten DNS-Zone richtig verknüpft sind.
- Vergewissern Sie sich, dass Ihr A-Eintrag richtig konfiguriert ist.
- Vergewissern Sie sich, dass Ihr VNET-Peering richtig konfiguriert ist.

### <a name="no-publications-to-which-you-can-subscribe"></a>Keine Veröffentlichungen vorhanden, die Sie abonnieren können

Beim Hinzufügen eines neuen Abonnements mit dem Assistenten **Neues Abonnement** stellen Sie auf der Seite **Veröffentlichung** kann es vorkommen, dass keine Datenbanken und Veröffentlichungen als verfügbare Optionen aufgeführt sind. Unter Umständen wird die folgende Fehlermeldung angezeigt:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

Möglicherweise stimmt diese Fehlermeldung, und für den Verleger, mit dem Sie die Verbindung hergestellt haben, sind wirklich keine Veröffentlichungen verfügbar. Es kann auch sein, dass sie nicht über ausreichende Berechtigungen verfügen. Dieser Fehler kann ggf. aber auch verursacht werden, weil eine ältere Version von SQL Server Management Studio verwendet wird. Versuchen Sie, ein Upgrade auf SQL Server Management Studio 18.0 oder höher durchzuführen, um diese Grundursache auszuschließen.

## <a name="next-steps"></a>Nächste Schritte

### <a name="enable-security-features"></a>Aktivieren der Sicherheitsfeatures

Unter [Was ist die verwaltete SQL-Instanz?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) finden Sie eine umfassende Liste mit Schutzmöglichkeiten für Ihre Datenbank. Folgende Sicherheitsfeatures werden behandelt:

- [Überwachung verwalteter SQL-Instanzen](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Bedrohungserkennung](threat-detection-configure.md)
- [Dynamische Datenmaskierung](/sql/relational-databases/security/dynamic-data-masking)
- [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security)
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Funktionen verwalteter SQL-Instanzen

Eine vollständige Übersicht über die Funktionen einer verwalteten Instanz finden Sie hier:

> [!div class="nextstepaction"]
> [Funktionen verwalteter SQL-Instanzen](sql-managed-instance-paas-overview.md)