---
title: 'Tutorial: Entwerfen eines Servers – Azure PowerShell – Azure Database for MariaDB'
description: In diesem Tutorial wird das Erstellen und Verwalten von Azure Database for MariaDB-Servern und -Datenbanken mithilfe von PowerShell erläutert.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: tutorial
ms.date: 05/26/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 9b4500df459e4d4ef67f97dc4fa923988f30401b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023090"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-using-powershell"></a>Tutorial: Entwerfen einer Azure Database for MariaDB-Instanz mithilfe von PowerShell

Azure Database for MariaDB ist ein relationaler Datenbankdienst in der Microsoft-Cloud, der auf der Datenbank-Engine „MariaDB Community Edition“ basiert. In diesem Tutorial verwenden Sie PowerShell und andere Hilfsprogramme, um die Vorgehensweise bei folgenden Aufgaben kennenzulernen:

> [!div class="checklist"]
> - Erstellen einer Azure Database for MariaDB-Instanz
> - Konfigurieren der Serverfirewall
> - Verwenden des [mysql-Befehlszeilentools](https://dev.mysql.com/doc/refman/5.6/en/mysql.html) zum Erstellen einer Datenbank
> - Laden von Beispieldaten
> - Daten abfragen
> - Aktualisieren von Daten
> - Wiederherstellen von Daten

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls Az.MariaDb verfügbar ist, müssen Sie es separat über das Az-PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Sobald das PowerShell-Modul Az.MariaDb allgemein verfügbar ist, wird es in zukünftige Releases des Az-PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Falls Sie den Dienst Azure Database for MariaDB erstmals verwenden, müssen Sie den Ressourcenanbieter **Microsoft.DBforMariaDB** registrieren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforMariaDB
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) eine bestimmte Abonnement-ID aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myresourcegroup** in der Region **USA, Westen** erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Erstellen eines Azure Database for MariaDB-Servers

Erstellen Sie mit dem Cmdlet `New-AzMariaDbServer` einen Azure Database for MariaDB-Server. Ein Server kann mehrere Datenbanken verwalten. In der Regel wird für jedes Projekt oder jeden Benutzer eine separate Datenbank verwendet.

Im folgenden Beispiel wird ein MariaDB-Server in der Region **USA, Westen** mit dem Namen **mydemoserver** in der Ressourcengruppe **myresourcegroup** und mit dem Benutzernamen **myadmin** für den Serveradministrator erstellt. Es handelt sich um einen Gen 5-Server im Tarif „Universell“ mit zwei virtuellen Kernen und Aktivierung von georedundanten Sicherungen. Notieren Sie sich das Kennwort, das in der ersten Zeile des Beispiels verwendet wird, da dies das Kennwort für das Administratorkonto des MariaDB-Servers ist.

> [!TIP]
> Ein Servername wird einem DNS-Namen zugeordnet und muss in Azure global eindeutig sein.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Für den Parameterwert **Sku** wird das Format **Tarif\_Computegeneration\_V-Kerne** verwendet. Dies ist in den folgenden Beispielen dargestellt.

- `-Sku B_Gen5_1` ist „Basic“, „Gen 5“ und „1 virtueller Kern“ zugeordnet. Bei dieser Option handelt es sich um die kleinste verfügbare SKU.
- `-Sku GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
- `-Sku MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Informationen zu den gültigen Werten für **Sku** nach Region sowie Tarife finden Sie unter [Azure Database for MariaDB – Tarife](./concepts-pricing-tiers.md).

Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn eine geringe Compute- und E/A-Leistung für Ihre Workload ausreichend ist.

> [!IMPORTANT]
> Server, die unter dem Tarif „Basic“ erstellt wurden, können später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert und nicht georepliziert werden.

## <a name="configure-a-firewall-rule"></a>Konfigurieren einer Firewallregel

Erstellen Sie eine Firewallregel auf der Ebene des Azure Database for MariaDB-Servers mit dem Cmdlet `New-AzMariaDbFirewallRule`. Eine Firewallregel auf Serverebene ermöglicht es einer externen Anwendung, z. B. dem Befehlszeilentool `mysql` oder MariaDB Workbench, über die Firewall des Azure Database for MariaDB-Diensts eine Verbindung mit Ihrem Server herzustellen.

Im folgenden Beispiel wird eine Firewallregel mit dem Namen **AllowMyIP** erstellt, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Geben Sie gemäß dem Standort, von dem aus Sie eine Verbindung herstellen, eine IP-Adresse oder einen Bereich mit IP-Adressen an.

```azurepowershell-interactive
New-AzMariaDbFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure Database for MariaDB erfolgt über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 unter Umständen nicht zugelassen. In diesem Szenario können Sie nur dann eine Verbindung mit dem Server herstellen, wenn Ihre IT-Abteilung Port 3306 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben. Verwenden Sie das folgende Beispiel, um die Verbindungsinformationen zu ermitteln. Notieren Sie sich die Werte für **FullyQualifiedDomainName** und **AdministratorLogin**.

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.mariadb.database.azure.com       myadmin
```

## <a name="connect-to-the-server-using-the-mysql-command-line-tool"></a>Herstellen einer Verbindung mit dem Server mit dem Befehlszeilentool „mysql“

Stellen Sie mit dem Befehlszeilentool `mysql` eine Verbindung mit Ihrem Server her. Informationen zum Herunterladen und Installieren des Befehlszeilentools finden Sie unter [MariaDB Community Downloads](https://dev.mysql.com/downloads/shell/). Sie können auch auf eine vorinstallierte Version des Befehlszeilentools `mysql` in Azure Cloud Shell zugreifen, indem Sie in einem Codebeispiel dieses Artikels die Schaltfläche **Jetzt ausprobieren** auswählen. Weitere Möglichkeiten zum Zugreifen auf Azure Cloud Shell sind das Auswählen der Schaltfläche **>_** auf der Symbolleiste oben rechts im Azure-Portal oder das Navigieren zu [shell.azure.com](https://shell.azure.com/).

```azurepowershell-interactive
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-database"></a>Erstellen einer Datenbank

Sobald Sie mit dem Server verbunden sind, erstellen Sie eine leere Datenbank.

```sql
mysql> CREATE DATABASE mysampledb;
```

Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zu dieser neu erstellten Datenbank aus:

```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank

Da Sie nun wissen, wie Sie eine Verbindung mit der Azure Database for MariaDB-Datenbank herstellen, können Sie einige grundlegende Aufgaben ausführen.

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Wir erstellen eine Tabelle, die Bestandsinformationen speichert.

```sql
CREATE TABLE inventory (
  id serial PRIMARY KEY,
  name VARCHAR(50),
  quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Laden von Daten in die Tabellen

Jetzt können Sie Daten in die Tabelle einfügen. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage zum Einfügen einiger Datenzeilen aus.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Die Tabelle, die Sie zuvor erstellt haben, enthält nun zwei Zeilen mit Beispieldaten.

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen

Führen Sie die folgende Abfrage aus, um Informationen aus der Datenbanktabelle abzurufen.

```sql
SELECT * FROM inventory;
```

Sie können auch die Daten in den Tabellen aktualisieren.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die Zeile wird entsprechend aktualisiert, wenn Sie Daten abrufen.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Wiederherstellen eines früheren Zustands einer Datenbank

Sie können den Zustand des Servers zu einem früheren Zeitpunkt wiederherstellen. Die wiederhergestellten Daten werden auf einen neuen Server kopiert, und der vorhandene Server bleibt unverändert. Wenn zum Beispiel eine Tabelle versehentlich gelöscht wird, können Sie den Zustand kurz vor dem Löschen wiederherstellen. Dann können Sie die fehlende Tabelle und die fehlenden Daten aus der wiederhergestellten Kopie des Servers abrufen.

Verwenden Sie zum Wiederherstellen des Servers das PowerShell-Cmdlet `Restore-AzMariaDbServer`.

### <a name="run-the-restore-command"></a>Ausführen des Befehls „restore“

Führen Sie das folgende Beispiel aus PowerShell aus, um den Server wiederherzustellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Wenn Sie den Zustand eines Servers zu einem früheren Zeitpunkt wiederherstellen, wird ein neuer Server erstellt. Der ursprüngliche Server und seine Datenbanken zum angegebenen Zeitpunkt werden auf den neuen Server kopiert.

Die Werte zum Standort und Tarif des wiederhergestellten Servers bleiben mit denen des ursprünglichen Servers identisch.

Suchen Sie nach Abschluss der Wiederherstellung den neuen Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden. Der neue Server verfügt über den gleichen Anmeldenamen für den Serveradministrator (und das dazugehörige Kennwort), der für den vorhandenen Server beim Start der Wiederherstellung gültig war. Sie können das Kennwort auf der Seite **Übersicht** des neuen Servers ändern.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die VNET-Dienstendpunkte auf, die auf dem ursprünglichen Server vorhanden waren. Diese Regeln müssen separat für den neuen Server eingerichtet werden. Firewallregeln vom ursprünglichen Server werden wiederhergestellt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Sichern und Wiederherstellen eines Azure Database for MariaDB-Servers mithilfe von PowerShell](howto-restore-server-powershell.md)