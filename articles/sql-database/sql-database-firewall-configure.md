---
title: IP-Firewallregeln für Azure SQL-Datenbank und Data Warehouse | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Firewall für SQL-Datenbank oder SQL Data Warehouse mit IP-Firewallregeln auf Serverebene zum Verwalten des Zugriffs sowie IP-Firewallregeln auf Datenbankebene für eine Einzeldatenbank oder eine Pooldatenbank konfigurieren.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 513836257a292069da709ad7a71e480f2b4d069d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158279"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>IP-Firewallregeln für Azure SQL-Datenbank and SQL Data Warehouse

Microsoft Azure [SQL-Datenbank](sql-database-technical-overview.md) und [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) stellen einen relationalen Datenbankdienst für Ihre internetbasierten Azure-Anwendungen (und weitere Anwendungen) bereit. Zum Schutz Ihrer Daten verhindern Firewalls jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

> [!NOTE]
> Dieser Artikel gilt für den Azure SQL-Datenbankserver sowie für Datenbanken von SQL-Datenbank und SQL Data Warehouse, die auf dem Azure SQL-Datenbankserver erstellt werden. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind.
> [!IMPORTANT]
> Dieser Artikel gilt *nicht* für **verwaltete Azure SQL-Datenbank-Instanzen**. Weitere Informationen über die erforderliche Netzwerkkonfiguration finden Sie im Artikel zum [Herstellen einer Verbindung mit einer verwalteten Instanz](sql-database-managed-instance-connect-app.md).

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>VNET-Regeln als Alternative zu IP-Regeln

Zusätzlich zu den IP-Regeln verwaltet die Firewall ebenfalls *VNET-Regeln*. VNET-Regeln basieren auf den Virtual Network-Dienstendpunkten. VNET-Regeln sollten den IP-Regeln in manchen Fällen vorgezogen werden. Weitere Informationen finden Sie unter [Virtual Network service endpoints and rules for Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md) (VNET-Dienstendpunkte und -Regeln für Azure SQL-Datenbank).

## <a name="overview"></a>Übersicht

Anfänglich wird jeglicher Zugriff auf Ihren Azure SQL Server durch die SQL-Datenbankfirewall blockiert. Damit Sie auf den Datenbankserver zugreifen können, müssen Sie eine oder mehrere IP-Firewallregeln auf Serverebene angeben, die Zugriff auf Ihre Azure SQL Server-Instanz ermöglichen. Geben Sie mit den IP-Firewallregeln an, welche IP-Adressbereiche aus dem Internet zulässig sind, und ob Azure-Anwendungen versuchen können, eine Verbindung mit Ihrer Azure SQL Server-Instanz herzustellen.

Um selektiv Zugriff auf nur eine der Datenbanken in Ihrer Azure SQL Server-Instanz zu gewähren, müssen Sie eine Regel auf Datenbankebene für die erforderliche Datenbank erstellen. Geben Sie in der IP-Firewallregel auf Datenbankebene einen IP-Adressbereich an, der außerhalb des Bereichs liegt, der in der IP-Firewallregel auf Serverebene angegeben ist, und stellen Sie sicher, dass die IP-Adresse des Clients innerhalb des auf Datenbankebene angegebenen Bereichs liegt.

> [!IMPORTANT]
> SQL Data Warehouse unterstützt nur IP-Firewallregeln auf Serverebene und keine IP-Firewallregeln auf Datenbankebene.

Verbindungsversuche über das Internet und Azure müssen zunächst die Firewall passieren, bevor sie Ihren Azure SQL-Server oder Ihre SQL-Datenbank erreichen (wie im folgenden Diagramm dargestellt):

   ![Diagramm zur Beschreibung der Firewallkonfiguration][1]

- **IP-Firewallregeln auf Serverebene:**

  Diese Regeln ermöglichen es Clients, auf den gesamten Azure SQL-Server zuzugreifen (also auf alle Datenbanken innerhalb desselben SQL-Datenbank-Servers). Diese Regeln sind in der **master** -Datenbank gespeichert. IP-Firewallregeln auf Serverebene können über das Portal oder mithilfe von Transact-SQL-Anweisungen konfiguriert werden. Zum Erstellen von IP-Firewallregeln auf Serverebene mit dem Azure-Portal oder PowerShell müssen Sie der Besitzer oder ein Mitwirkender des Abonnements sein. Zum Erstellen einer IP-Firewallregel auf Serverebene mit Transact-SQL müssen Sie die Verbindung mit der SQL-Datenbankinstanz als Prinzipalanmeldung auf Serverebene oder als Azure Active Directory-Administrator herstellen (d.h. ein Benutzer mit Azure-Berechtigungen muss zuerst eine IP-Firewallregel auf Serverebene erstellen).

- **IP-Firewallregeln auf Datenbankebene:**

  Diese Regeln ermöglichen Clients, auf bestimmte (sichere) Datenbanken innerhalb desselben SQL-Datenbank-Servers zuzugreifen. Sie können diese Regeln für jede Datenbank (einschließlich der Datenbank **master**) erstellen, die in den einzelnen Datenbanken gespeichert werden. IP-Firewallregeln auf Datenbankebene für Master- und Benutzerdatenbanken können nur mithilfe von Transact-SQL-Anweisungen erstellt und verwaltet werden und erst dann, nachdem Sie die erste Firewall auf Serverebene konfiguriert haben. Wenn Sie in der IP-Firewallregel auf Datenbankebene einen IP-Adressbereich angeben, der außerhalb des Bereichs liegt, der in der IP-Firewallregel auf Serverebene angegeben ist, können nur die Clients auf die Datenbank zugreifen, die IP-Adressen in dem auf Datenbankebene angegebenen Bereich aufweisen. Es können maximal 128 IP-Firewallregeln auf Datenbankebene für eine Datenbank verwendet werden. Weitere Informationen zum Konfigurieren von IP-Firewallregeln auf Datenbankebene finden Sie im Beispiel weiter unten in diesem Artikel und unter [sp_set_database_firewall_rule (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Empfehlung

Microsoft empfiehlt, nach Möglichkeit IP-Firewallregeln auf Datenbankebene zu verwenden, um die Sicherheit und die Portabilität der Datenbank zu verbessern. Verwenden Sie IP-Firewallregeln auf Serverebene für Administratoren, und wenn Sie über viele Datenbanken mit identischen Zugriffsanforderungen verfügen und die Datenbanken nicht einzeln konfigurieren möchten.

> [!IMPORTANT]
> Microsoft Azure SQL-Datenbank unterstützt maximal 128 IP-Firewallregeln.
> [!NOTE]
> Informationen zu portablen Datenbanken im Kontext der Geschäftskontinuität finden Sie unter [Authentifizierungsanforderungen für die Notfallwiederherstellung](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Herstellen einer Verbindung über das Internet

Wenn ein Computer versucht, über das Internet eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall zuerst die Ursprungs-IP-Adresse der Anforderung anhand von IP-Firewallregeln auf Serverebene für die Datenbank, die von der Verbindung angefordert wird:

- Liegt die IP-Adresse der Anforderung innerhalb eines der in den IP-Firewallregeln auf Datenbankebene angegebenen Bereiche, wird die Verbindung der SQL-Datenbank gewährt, die die Regel enthält.
- Liegt die IP-Adresse der Anforderung nicht innerhalb eines der in den IP-Firewallregeln auf Datenbankebene angegebenen Bereiche, werden die IP-Firewallregeln auf Serverebene geprüft. Liegt die IP-Adresse der Anforderung innerhalb eines der in den IP-Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung gewährt. IP-Firewallregeln auf Serverebene wirken sich auf alle SQL-Datenbanken auf dem Azure SQL-Server aus.  
- Liegt die IP-Adresse der Anforderung nicht innerhalb der in den IP-Firewallregeln auf Datenbankebene oder Serverebene angegebenen Bereiche, schlägt die Verbindungsanforderung fehl.

> [!NOTE]
> Um von Ihrem lokalen Computer aus auf die Azure SQL-Datenbank zuzugreifen, stellen Sie sicher, dass die Firewall im Netzwerk und auf dem lokalen Computer eine ausgehende Kommunikation an TCP-Port 1433 zulässt.

### <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure

Um Anwendungen von Azure die Verbindung mit dem Azure SQL-Server zu ermöglichen, müssen Azure-Verbindungen aktiviert sein. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Azure-Verbindungen zulässig sind. Ist der Verbindungsversuch nicht zulässig, gelangt die Anforderung nicht zum Azure SQL-Datenbank-Server.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.

## <a name="creating-and-managing-ip-firewall-rules"></a>Erstellen und Verwalten von IP-Firewallregeln

Die erste Firewalleinstellung auf Serverebene kann im [Azure-Portal](https://portal.azure.com/) oder programmgesteuert mithilfe von [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), der [Azure-CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) oder der [REST-API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) erstellt werden. Nachfolgende IP-Firewallregeln auf Serverebene können anhand dieser Methoden sowie über Transact-SQL erstellt und verwaltet werden.

> [!IMPORTANT]
> IP-Firewallregeln auf Datenbankebene können nur mit Transact-SQL erstellt und verwaltet werden.

Um die Leistung zu verbessern, werden IP-Firewallregeln auf Serverebene vorübergehend auf Datenbankebene zwischengespeichert. Informationen zum Aktualisieren des Caches finden Sie unter [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Mithilfe der [SQL Server-Datenbanküberwachung](sql-database-auditing.md) können Sie Firewall-Änderungen auf Server- und Datenbankebene überwachen.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Verwalten von IP-Firewallregeln auf Serverebene mit dem Azure-Portal

Zum Festlegen einer IP-Firewallregel auf Serverebene im Azure-Portal können Sie entweder zur Seite „Übersicht“ für die Azure SQL-Datenbank oder zur Seite „Übersicht“ für den SQL-Datenbank-Server wechseln.

> [!TIP]
> Ein Tutorial finden Sie unter [Erstellen einer Datenbank im Azure-Portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Auf der Seite „Übersicht“ der Datenbank

1. Um eine IP-Firewallregel auf Serverebene auf der Seite „Übersicht“ der Datenbank festzulegen, klicken Sie auf der Symbolleiste auf **Serverfirewall festlegen**, wie in der folgenden Abbildung dargestellt: Die Seite **Firewalleinstellungen** für den SQL-Datenbank-Server wird geöffnet.

      ![IP-Serverfirewallregel](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen**, um die IP-Adresse des Computers hinzuzufügen, den Sie derzeit verwenden. Klicken Sie dann auf **Speichern**. Eine IP-Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

      ![Festlegen von IP-Firewallregeln auf Serverebene](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Auf der Seite „Übersicht“ für den Server

Die Übersichtsseite für Ihren Server wird geöffnet, die den vollqualifizierten Servernamen (z.B. **mynewserver20170403.database.windows.net**) und Optionen für die weitere Konfiguration enthält.

1. Um eine Regel auf Serverebene auf der Seite „Übersicht“ für den Server festzulegen, klicken Sie im linken Menü unter „Einstellungen“ auf **Firewall**:

2. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen**, um die IP-Adresse des Computers hinzuzufügen, den Sie derzeit verwenden. Klicken Sie dann auf **Speichern**. Eine IP-Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Verwalten von IP-Firewallregeln mit Transact-SQL

| Katalogsicht oder gespeicherte Prozedur | Level | BESCHREIBUNG |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Server |Zeigt die aktuellen IP-Firewallregeln auf Serverebene an |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Server |Erstellt oder aktualisiert IP-Firewallregeln auf Serverebene |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Server |Entfernt IP-Firewallregeln auf Serverebene |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Datenbank |Zeigt die aktuellen IP-Firewallregeln auf Datenbankebene an |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Datenbank |Erstellt oder aktualisiert IP-Firewallregeln auf Datenbankebene |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Datenbanken |Entfernt IP-Firewallregeln auf Datenbankebene |

In den folgenden Beispielen werden die vorhandenen Regeln überprüft, ein IP-Adressbereich auf dem Server „Contoso“ aktiviert und eine IP-Firewallregel gelöscht:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Fügen Sie anschließend eine IP-Firewallregel auf Serverebene hinzu.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Um eine IP-Firewallregel auf Serverebene zu löschen, führen Sie die gespeicherte Prozedur "sp_delete_firewall_rule" aus. Im folgenden Beispiel wird die Regel mit dem Namen „ContosoFirewallRule“ gelöscht:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe von Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und in den AzureRm-Modulen sind im Wesentlichen identisch.

| Cmdlet | Level | BESCHREIBUNG |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Gibt die aktuellen Firewallregeln auf Serverebene zurück. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Erstellt eine neue Firewallregel auf Serverebene. |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aktualisiert die Eigenschaften einer vorhandenen Firewallregel auf Serverebene. |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Entfernt Firewallregeln auf Serverebene. |

Im folgenden Beispiel wird mithilfe von PowerShell eine IP-Firewallregel auf Serverebene festgelegt:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> PowerShell-Beispiele im Kontext eines Schnellstarts finden Sie unter [Erstellen einer Datenbank – PowerShell](sql-database-powershell-samples.md) und [Erstellen einer einzelnen Datenbank und Konfigurieren einer IP-Firewallregel auf SQL-Datenbank-Serverebene mithilfe von PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe der Azure CLI

| Cmdlet | Level | BESCHREIBUNG |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Erstellt eine IP-Serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Listet die IP-Firewallregeln auf einem Server auf|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zeigt die Details einer IP-Firewallregel an|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualisiert eine IP-Firewallregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Löscht eine IP-Firewallregel|

Im folgenden Beispiel wird mithilfe der Azure CLI eine IP-Firewallregel auf Serverebene festgelegt:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Ein Azure CLI-Beispiel im Kontext einer Schnellstartanleitung finden Sie unter [Erstellen einer Datenbank – Azure CLI](sql-database-cli-samples.md) und [Erstellen einer einzelnen Datenbank und Konfigurieren einer SQL-Datenbank-IP-Firewallregel mithilfe der Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe der REST-API

| API | Level | BESCHREIBUNG |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Server |Zeigt die aktuellen IP-Firewallregeln auf Serverebene an |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Server |Erstellt oder aktualisiert IP-Firewallregeln auf Serverebene |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Server |Entfernt IP-Firewallregeln auf Serverebene |
| [Abrufen von Firewallregeln](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Server | Ruft IP-Firewallregeln auf Serverebene ab |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-Firewallregeln auf Serverebene im Vergleich zu Regeln auf Datenbankebene

F: Sollten Benutzer einer Datenbank vollständig von anderen Datenbanken isoliert werden?
Wenn ja, sollten Sie den Zugriff mit IP-Firewallregeln auf Datenbankebene gewähren. Dadurch verhindern Sie die Verwendung von IP-Firewallregeln auf Serverebene, die durch die Firewall den Zugriff auf alle Datenbanken ermöglichen und weniger Sicherheit bieten.

F: Benötigen Benutzer unter der IP-Adresse Zugriff auf alle Datenbanken?
Verwenden Sie IP-Firewallregeln auf Serverebene, wenn Sie weniger IP-Firewallregeln konfigurieren möchten.

F: Wird den Benutzern oder Teams, die die IP-Firewallregeln konfigurieren, der Zugriff nur über das Azure-Portal, PowerShell oder die REST-API gewährt?
Dann müssen Sie IP-Firewallregeln auf Serverebene verwenden. IP-Firewallregeln auf Datenbankebene können nur mit Transact-SQL konfiguriert werden.  

F: Werden den Benutzern oder Teams, die die IP-Firewallregeln konfigurieren, übergeordnete Berechtigungen auf Datenbankebene verweigert?
Verwenden von IP-Firewallregeln auf Serverebene Zum Konfigurieren von IP-Firewallregeln auf Datenbankebene mit Transact-SQL benötigen Sie mindestens eine `CONTROL DATABASE`-Berechtigung auf Datenbankebene.  

F: Verwalten die Benutzer oder Teams, die die IP-Firewallregeln konfigurieren oder überwachen, die IP-Firewallregeln für viele (vielleicht sogar Hunderte) Datenbanken zentral?
Diese Entscheidung hängt von Ihren Anforderungen und Ihrer Umgebung ab. IP-Firewallregeln auf Serverebene sind möglicherweise einfacher zu konfigurieren, doch durch Skripterstellung können Sie Regeln auf Datenbankebene konfigurieren. Selbst wenn Sie IP-Firewallregeln auf Serverebene verwenden, müssen Sie möglicherweise IP-Firewallregeln auf Datenbankebene überwachen, um zu prüfen, ob Benutzer mit der `CONTROL`-Berechtigung für die Datenbank IP-Firewallregeln auf Datenbankebene erstellt haben.

F: Kann ich ein Kombination aus IP-Firewallregeln auf Serverebene und IP-Firewallregeln auf Datenbankebene verwenden?
Ja. Einige Benutzer, z. B. Administratoren, benötigen möglicherweise IP-Firewallregeln auf Serverebene. Andere Benutzer, z. B. Benutzer einer Datenbankanwendung, benötigen möglicherweise IP-Firewallregeln auf Datenbankebene.

## <a name="troubleshooting-the-database-firewall"></a>Problembehandlung der Datenbankfirewall

Wenn der Zugriff auf den Microsoft Azure SQL-Datenbank-Dienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

- **Lokale Firewallkonfiguration**:

  Bevor Ihr Computer auf Azure SQL-Datenbank zugreifen kann, müssen Sie möglicherweise eine Firewallausnahme auf Ihrem Computer für TCP-Port 1433 erstellen. Wenn Sie innerhalb der Grenzen der Azure-Cloud Verbindungen herstellen, müssen Sie möglicherweise zusätzliche Ports öffnen. Weitere Informationen finden Sie im Abschnitt **SQL-Datenbank: ,Außerhalb‘ im Vergleich zu ,Innerhalb‘** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Netzwerkadressenübersetzung (NAT)** :

  Aufgrund der NAT kann sich die IP-Adresse, die Ihr Computer zur Verbindung mit Azure SQL-Datenbank verwendet, möglicherweise von der IP-Adresse unterscheiden, die in den IP-Konfigurationseinstellungen des Computers angezeigt wird. Um die IP-Adresse anzuzeigen, mit der der Computer die Verbindung mit Azure herstellt, melden Sie sich beim Portal an, und navigieren Sie zur Registerkarte **Konfigurieren** des Servers, der Ihre Datenbank hostet. Im Abschnitt **Zulässige IP-Adressen** wird die **aktuelle Client-IP-Adresse** angezeigt. Klicken Sie bei **Zulässige IP-Adressen** auf **Hinzufügen**, um diesem Computer den Zugriff auf den Server zu ermöglichen.

- **Änderungen an der Zulassungsliste sind noch nicht wirksam**:

  Änderungen der Firewallkonfiguration für Azure SQL-Datenbank werden möglicherweise erst nach fünf Minuten wirksam.

- **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet**:

  Wenn eine Anmeldung nicht über Berechtigungen für den Azure SQL-Datenbank-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure SQL-Datenbank-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, eine Verbindung mit dem Server herzustellen. Jeder Client muss die erforderlichen Sicherheitsanmeldeinformationen bereitstellen. Weitere Informationen zum Vorbereiten von Anmeldungen finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](sql-database-manage-logins.md).

- **Dynamische IP-Adresse**:

  Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:
  
  - Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, die auf den Azure SQL-Datenbank-Server zugreifen, und fügen Sie dann den IP-Adressbereich als IP-Firewallregel hinzu.
  - Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die IP-Adressen als IP-Firewallregeln hinzu.

## <a name="next-steps"></a>Nächste Schritte

- Bestätigen Sie, dass die Netzwerkumgebung Ihres Unternehmens die eingehende Kommunikation aus den Compute-IP-Adressbereichen (einschließlich SQL-Bereichen) zulässt, die von den Microsoft Azure-Rechenzentren verwendet werden. Möglicherweise müssen diese IP-Adressen auf die Whitelist gesetzt werden; siehe [IP-Bereiche für Microsoft Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653).  
- Einen Schnellstart zum Erstellen einer IP-Firewallregel auf Serverebene finden Sie unter [Erstellen einer Azure SQL-Datenbank](sql-database-single-database-get-started.md).
- Hilfe beim Herstellen einer Verbindung mit einer Azure SQL-Datenbank über Open Source-Anwendungen oder Anwendungen von Drittanbietern finden Sie unter [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Informationen zu zusätzlichen Ports, die Sie ggf. öffnen müssen, finden Sie im Abschnitt **SQL-Datenbank: ,Außerhalb‘ im Vergleich zu ,Innerhalb‘** im Artikel [Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank](sql-database-develop-direct-route-ports-adonet-v12.md).
- Eine Übersicht über die Sicherheit von Azure SQL-Datenbank finden Sie unter [Sichern der SQL-Datenbank](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
