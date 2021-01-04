---
title: IP-Firewallregeln
description: Hier erfahren Sie, wie Sie IP-Firewallregeln auf Serverebene für eine Datenbank in SQL-Datenbank oder Azure Synapse Analytics-Firewall konfigurieren. Außerdem erfahren Sie, wie Sie den Zugriff verwalten und IP-Firewallregeln für SQL-Datenbank konfigurieren.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: ce528e268e0ed1e34f53e32196bceef5ad8a2fcb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452489"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL-Datenbank- und Azure Synapse-IP-Firewallregeln
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Wenn Sie z. B. einen neuen Server in Azure SQL-Datenbank oder Azure Synapse Analytics namens *mysqlserver* erstellen, blockiert eine Firewall auf Serverebene alle Zugriffe auf den öffentlichen Endpunkt für den Server (auf den unter *mysqlserver.database.windows.net* zugegriffen werden kann). Der Einfachheit halber wird *SQL-Datenbank* verwendet, um sowohl auf SQL-Datenbank als auch auf Azure Synapse Analytics zu verweisen.

> [!IMPORTANT]
> Dieser Artikel gilt *nicht* für *Azure SQL Managed Instance*. Informationen zur Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Instanz](../managed-instance/connect-application-instance.md).
>
> Azure Synapse unterstützt nur IP-Firewallregeln auf Serverebene. IP-Firewallregeln auf Datenbankebene werden nicht unterstützt.

## <a name="how-the-firewall-works"></a>Funktionsweise der Firewall

Verbindungsversuche über das Internet und Azure müssen die Firewall durchlaufen, bevor sie Ihren Server oder Ihre Datenbank erreichen, wie im folgenden Diagramm dargestellt.

   ![Diagramm: Firewallkonfiguration][1]

### <a name="server-level-ip-firewall-rules"></a>IP-Firewallregeln auf Serverebene

Diese Regeln erlauben es Clients, auf Ihren gesamten Server zuzugreifen, d. h. auf alle vom Server verwalteten Datenbanken. Die Regeln sind in der *Masterdatenbank* gespeichert. Es können maximal 128 IP-Firewallregeln auf Serverebene für einen Server konfiguriert werden. Wenn die Einstellung **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aktiviert ist, zählt dies als einzelne Firewallregel für den Server.
  
IP-Firewallregeln auf Serverebene können über das Azure-Portal, mithilfe von PowerShell oder mithilfe von Transact-SQL-Anwendungen konfiguriert werden.

- Wenn Sie das Portal oder PowerShell verwenden möchten, müssen Sie der Besitzer oder ein Mitwirkender des Abonnements sein.
- Wenn Sie Transact-SQL verwenden möchten, müssen Sie mithilfe einer Prinzipalanmeldung auf Serverebene oder als Azure Active Directory-Administrator eine Verbindung mit der *Masterdatenbank* herstellen. (Eine IP-Firewallregel auf Serverebene muss zuerst von einem Benutzer erstellt werden, der über Berechtigungen auf Azure-Ebene verfügt.)

### <a name="database-level-ip-firewall-rules"></a>Firewallregeln auf Datenbankebene

IP-Firewallregeln auf Datenbankebene gestatten Clients den Zugriff auf bestimmte (sichere) Datenbanken. Die Regeln können für jede Datenbank (einschließlich der *Masterdatenbank*) erstellt werden und werden in der jeweiligen Datenbank gespeichert.
  
- IP-Firewallregeln auf Datenbankebene für Master- und Benutzerdatenbanken können nur mithilfe von Transact-SQL-Anweisungen und erst nach der Konfiguration der ersten Firewall auf Serverebene erstellt und verwaltet werden.
- Wenn Sie in der IP-Firewallregel auf Datenbankebene einen IP-Adressbereich angeben, der außerhalb des Bereichs der IP-Firewallregel auf Serverebene liegt, können nur die Clients auf die Datenbank zugreifen, die über IP-Adressen in dem auf Datenbankebene angegebenen Bereich verfügen.
- Es können maximal 128 IP-Firewallregeln auf Datenbankebene für eine Datenbank verwendet werden. Weitere Informationen zum Konfigurieren von IP-Firewallregeln auf Datenbankebene finden Sie im Beispiel weiter unten in diesem Artikel und unter [sp_set_database_firewall_rule (Azure SQL-Datenbank)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Empfehlungen zum Festlegen von Firewallregeln

Verwenden Sie nach Möglichkeit IP-Firewallregeln auf Datenbankebene. Diese Vorgehensweise erhöht die Sicherheit und macht Ihre Datenbank portabler. Verwenden Sie IP-Firewallregeln auf Serverebene für Administratoren. Darüber hinaus können Sie diese Regeln verwenden, wenn Sie über viele Datenbanken mit identischen Zugriffsanforderungen verfügen und die Datenbanken nicht einzeln konfigurieren möchten.

> [!NOTE]
> Informationen zu portablen Datenbanken im Kontext der Geschäftskontinuität finden Sie unter [Authentifizierungsanforderungen für die Notfallwiederherstellung](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-Firewallregeln auf Serverebene im Vergleich zu Regeln auf Datenbankebene

*Sollen Benutzer einer Datenbank vollständig von anderen Datenbanken isoliert werden?*

Falls *ja*, verwenden Sie IP-Firewallregeln auf Datenbankebene, um Zugriff zu gewähren. Dadurch vermeiden Sie die Verwendung von IP-Firewallregeln auf Serverebene, die durch die Firewall den Zugriff auf alle Datenbanken ermöglichen. Dies würde die Sicherheit beeinträchtigen.

*Benötigen Benutzer an den IP-Adressen Zugriff auf alle Datenbanken?*

Falls *ja*, verwenden Sie IP-Firewallregeln auf Serverebene, um weniger IP-Firewallregeln konfigurieren zu müssen.

*Haben die Person oder das Team, die bzw. das die IP-Firewallregeln konfiguriert, nur Zugriff über das Azure-Portal, über PowerShell oder über die REST-API?*

Falls ja, müssen IP-Firewallregeln auf Serverebene verwendet werden. IP-Firewallregeln auf Datenbankebene können nur über Transact-SQL konfiguriert werden.  

*Werden der Person oder dem Team, die bzw. das die IP-Firewallregeln konfiguriert, übergeordnete Berechtigungen auf Datenbankebene verweigert?*

Falls ja, verwenden Sie IP-Firewallregeln auf Serverebene. Sie benötigen auf der Datenbankebene mindestens die Berechtigung *CONTROL DATABASE*, um IP-Firewallregeln auf Datenbankebene über Transact-SQL konfigurieren zu können.  

*Verwaltet die Person oder das Team, die bzw. das die IP-Firewallregeln konfiguriert oder überwacht, die IP-Firewallregeln für viele (vielleicht sogar hunderte) Datenbanken zentral?*

In diesem Fall hängen die Best Practices von Ihren Anforderungen und Ihrer Umgebung ab. IP-Firewallregeln auf Serverebene sind möglicherweise einfacher zu konfigurieren, doch durch Skripterstellung können Sie Regeln auf Datenbankebene konfigurieren. Selbst wenn Sie IP-Firewallregeln auf Serverebene verwenden, müssen Sie möglicherweise IP-Firewallregeln auf Datenbankebene überprüfen, um zu ermitteln, ob Benutzer mit der *CONTROL*-Berechtigung für die Datenbank IP-Firewallregeln auf Datenbankebene erstellen.

*Kann ich eine Kombination aus IP-Firewallregeln auf Serverebene und IP-Firewallregeln auf Datenbankebene verwenden?*

Ja. Bestimmte Benutzer (z. B. Administratoren) benötigen möglicherweise IP-Firewallregeln auf Serverebene. Andere Benutzer, z. B. Benutzer einer Datenbankanwendung, benötigen möglicherweise IP-Firewallregeln auf Datenbankebene.

### <a name="connections-from-the-internet"></a>Verbindungen über das Internet

Wenn ein Computer versucht, über das Internet eine Verbindung mit dem Server herzustellen, prüft die Firewall zunächst die Ursprungs-IP-Adresse der Anforderung anhand von IP-Firewallregeln auf Serverebene für die Datenbank, die von der Verbindung angefordert wird.

- Liegt die Adresse innerhalb eines Bereichs, der in den IP-Firewallregeln auf Datenbankebene angegeben ist, wird die Verbindung mit der Datenbank gewährt, die die Regel enthält.
- Liegt die Adresse nicht innerhalb eines Bereichs in den IP-Firewallregeln auf Datenbankebene, überprüft die Firewall die IP-Firewallregeln auf Serverebene. Wenn die Adresse innerhalb eines Bereichs in den IP-Firewallregeln auf Serverebene liegt, wird die Verbindung gewährt. IP-Firewallregeln auf Serverebene gelten für alle vom Server verwalteten Datenbanken.  
- Liegt die Adresse nicht innerhalb eines Bereichs in einer der IP-Firewallregeln auf Datenbank- oder Serverebene, ist die Verbindungsanforderung nicht erfolgreich.

> [!NOTE]
> Wenn Sie von Ihrem lokalen Computer aus auf Azure SQL-Datenbank zugreifen möchten, stellen Sie sicher, dass die Firewall in Ihrem Netzwerk und auf Ihrem lokalen Computer ausgehende Kommunikation am TCP-Port 1433 zulässt.

### <a name="connections-from-inside-azure"></a>Verbindungen aus Azure

Wenn Sie in Azure gehosteten Anwendungen die Verbindungsherstellung mit Ihrer SQL Server-Instanz ermöglichen möchten, müssen Azure-Verbindungen aktiviert sein. Wenn eine Anwendung in Azure versucht, eine Verbindung mit Ihrem Server herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Dies kann direkt über das Azure-Portalblatt aktiviert werden, indem Sie Firewallregeln und die Option **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** auf **EIN** in den Einstellungen **Firewalls und virtuelle Netzwerke** festlegen. Ist die Verbindung nicht zulässig, erreicht die Anforderung den Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall so, dass alle von Azure ausgehenden Verbindungen zugelassen werden (einschließlich Verbindungen von den Abonnements anderer Kunden). Stellen Sie bei Verwendung dieser Option sicher, dass Ihre Anmelde- und Benutzerberechtigungen den Zugriff auf autorisierte Benutzer beschränken.

## <a name="permissions"></a>Berechtigungen

Um IP-Firewallregeln für die Azure SQL Server-Instanz erstellen und verwalten zu können, müssen Sie über eine der folgenden Rollen verfügen:

- Rolle [Mitwirkender von SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- Rolle [SQL-Sicherheits-Manager](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- Besitzer der Ressource, die die Azure SQL Server-Instanz enthält

## <a name="create-and-manage-ip-firewall-rules"></a>Erstellen und Verwalten von IP-Firewallregeln

Die erste Firewalleinstellung auf Serverebene können Sie im [Azure-Portal](https://portal.azure.com/) oder programmgesteuert mithilfe von [Azure PowerShell](/powershell/module/az.sql), über die [Azure CLI](/cli/azure/sql/server/firewall-rule) oder mithilfe einer [REST-API](/rest/api/sql/firewallrules/createorupdate) von Azure erstellen. Weitere IP-Firewallregeln auf Serverebene können mithilfe dieser Methoden oder mithilfe von Transact-SQL erstellt und verwaltet werden.

> [!IMPORTANT]
> IP-Firewallregeln auf Datenbankebene können nur mithilfe von Transact-SQL erstellt und verwaltet werden.

Um die Leistung zu verbessern, werden IP-Firewallregeln auf Serverebene vorübergehend auf Datenbankebene zwischengespeichert. Informationen zum Aktualisieren des Caches finden Sie unter [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Mithilfe der [Datenbanküberwachung](../../azure-sql/database/auditing-overview.md) können Sie Firewalländerungen auf Server- und Datenbankebene überwachen.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Verwalten von IP-Firewallregeln auf Serverebene über das Azure-Portal

Navigieren Sie zum Festlegen einer IP-Firewallregel auf Serverebene im Azure-Portal zur Seite „Übersicht“ für Ihre Datenbank oder Ihren Server.

> [!TIP]
> Ein Tutorial finden Sie unter [Erstellen einer Datenbank im Azure-Portal](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Vorgehensweise auf der Datenbankübersichtsseite

1. Wählen Sie zum Festlegen einer IP-Firewallregel auf Serverebene auf der Symbolleiste der Datenbankübersichtsseite die Option **Serverfirewall festlegen** aus, wie in der folgenden Abbildung zu sehen.

    ![IP-Firewallregel auf Serverebene](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Die Seite **Firewalleinstellungen** für den Server wird geöffnet.

2. Wählen Sie auf der Symbolleiste die Option **Client-IP-Adresse hinzufügen** aus, um die IP-Adresse des verwendeten Computers hinzuzufügen, und wählen Sie anschließend **Speichern** aus. Eine IP-Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

    ![Festlegen einer IP-Firewallregel auf Serverebene](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Vorgehensweise auf der Serverübersichtsseite

Die Übersichtsseite für Ihren Server wird geöffnet. Sie enthält den vollqualifizierten Servernamen (z. B. *mynewserver20170403.database.windows.net*) sowie Optionen für die weitere Konfiguration.

1. Auf dieser Seite können Sie wie folgt eine Regel auf Serverebene festlegen: Wählen Sie auf der linken Seite im Menü **Einstellungen** die Option **Firewall** aus.

2. Wählen Sie auf der Symbolleiste die Option **Client-IP-Adresse hinzufügen** aus, um die IP-Adresse des verwendeten Computers hinzuzufügen, und wählen Sie anschließend **Speichern** aus. Eine IP-Firewallregel auf Serverebene wird für Ihre aktuelle IP-Adresse erstellt.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Verwalten von IP-Firewallregeln mithilfe von Transact-SQL

| Katalogsicht oder gespeicherte Prozedur | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Zeigt die aktuellen IP-Firewallregeln auf Serverebene an |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Erstellt oder aktualisiert IP-Firewallregeln auf Serverebene |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Entfernt IP-Firewallregeln auf Serverebene |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Datenbank |Zeigt die aktuellen IP-Firewallregeln auf Datenbankebene an |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Datenbank |Erstellt oder aktualisiert IP-Firewallregeln auf Datenbankebene |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Datenbanken |Entfernt IP-Firewallregeln auf Datenbankebene |

Im folgenden Beispiel werden die vorhandenen Regeln überprüft. Darüber hinaus wird ein IP-Adressbereich auf dem Server *contoso* aktiviert und eine IP-Firewallregel gelöscht:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Fügen Sie anschließend eine IP-Firewallregel auf Serverebene hinzu.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Führen Sie zum Löschen einer IP-Firewallregel auf Serverebene die gespeicherte Prozedur *sp_delete_firewall_rule* aus. Im folgenden Beispiel wird die Regel *ContosoFirewallRule* gelöscht:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell-Azure Resource Manager-Modul wird von Azure SQL-Datenbank zwar weiterhin unterstützt, die gesamte Entwicklung konzentriert sich inzwischen jedoch auf das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und AzureRM-Modul sind im Wesentlichen identisch.

| Cmdlet | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Gibt die aktuellen Firewallregeln auf Serverebene zurück. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Erstellt eine neue Firewallregel auf Serverebene. |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Aktualisiert die Eigenschaften einer vorhandenen Firewallregel auf Serverebene. |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Entfernt Firewallregeln auf Serverebene. |

Im folgenden Beispiel wird eine IP-Firewallregel auf Serverebene mithilfe von PowerShell festgelegt:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Geben Sie für „$servername“ den Servernamen und nicht den voll qualifizierten DNS-Namen an. Geben Sie beispielsweise **mysqldbserver** anstelle von **mysqldbserver.database.windows.net** an.
>
> PowerShell-Beispiele im Kontext einer Schnellstartanleitung finden Sie unter [Erstellen einer Datenbank – PowerShell](powershell-script-content-guide.md) sowie unter [Erstellen einer einzelnen Datenbank und Konfigurieren einer IP-Firewallregel auf Serverebene mithilfe von PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe der CLI

| Cmdlet | Ebene | BESCHREIBUNG |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Server|Erstellt eine IP-Serverfirewallregel|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Server|Listet die IP-Firewallregeln auf einem Server auf|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Server|Zeigt die Details einer IP-Firewallregel an|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Server|Aktualisiert eine IP-Firewallregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Server|Löscht eine IP-Firewallregel|

Im folgenden Beispiel wird eine IP-Firewallregel auf Serverebene mithilfe der CLI festgelegt:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Geben Sie für „$servername“ den Servernamen und nicht den voll qualifizierten DNS-Namen an. Geben Sie beispielsweise **mysqldbserver** anstelle von **mysqldbserver.database.windows.net** an.
>
> Ein CLI-Beispiel im Kontext einer Schnellstartanleitung finden Sie unter [Erstellen einer Datenbank – Azure CLI](az-cli-script-samples-content-guide.md) sowie unter [Erstellen einer einzelnen Datenbank und Konfigurieren einer IP-Firewallregel auf Serverebene mithilfe der Azure CLI](scripts/create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Verwalten von IP-Firewallregeln auf Serverebene mithilfe einer REST-API

| API | Ebene | BESCHREIBUNG |
| --- | --- | --- |
| [List firewall rules](/rest/api/sql/firewallrules/listbyserver) |Server |Zeigt die aktuellen IP-Firewallregeln auf Serverebene an |
| [Create or update firewall rules](/rest/api/sql/firewallrules/createorupdate) |Server |Erstellt oder aktualisiert IP-Firewallregeln auf Serverebene |
| [Delete firewall rules](/rest/api/sql/firewallrules/delete) |Server |Entfernt IP-Firewallregeln auf Serverebene |
| [Get firewall rules](/rest/api/sql/firewallrules/get) | Server | Ruft IP-Firewallregeln auf Serverebene ab |

## <a name="troubleshoot-the-database-firewall"></a>Behandeln von Problemen mit der Datenbankfirewall

In diesem Abschnitt finden Sie einige Punkte für den Fall, dass sich der Zugriff auf Azure SQL-Datenbank nicht wie erwartet verhält.

- **Lokale Firewallkonfiguration**:

  Bevor Ihr Computer auf Azure SQL-Datenbank zugreifen kann, müssen Sie möglicherweise eine Firewallausnahme auf Ihrem Computer für TCP-Port 1433 erstellen. Wenn Sie innerhalb der Grenzen der Azure-Cloud Verbindungen herstellen, müssen Sie möglicherweise zusätzliche Ports öffnen. Weitere Informationen finden Sie im Abschnitt „Außerhalb im Vergleich zu innerhalb“ des Artikels [Andere Ports als 1433 für ADO.NET 4.5 und Azure SQL-Datenbank](adonet-v12-develop-direct-route-ports.md).

- **Netzwerkadressenübersetzung:**

  Aufgrund der Netzwerkadressübersetzung (Network Address Translation, NAT) kann sich die IP-Adresse, die Ihr Computer für die Verbindungsherstellung mit Azure SQL-Datenbank verwendet, von der IP-Adresse in den IP-Konfigurationseinstellungen des Computers unterscheiden. Gehen Sie wie folgt vor, um die IP-Adresse anzuzeigen, die Ihr Computer für die Verbindungsherstellung mit Azure verwendet:
    1. Melden Sie sich beim Portal an.
    1. Navigieren Sie auf dem Server, der Ihre Datenbank hostet, zur Registerkarte **Konfigurieren**.
    1. Im Abschnitt **Zulässige IP-Adressen** wird unter **Aktuelle Client-IP-Adresse** die aktuelle Client-IP-Adresse angezeigt. Wählen Sie für **Zulässige IP-Adressen** die Option **Hinzufügen** aus, um diesem Computer Zugriff auf den Server zu gewähren.

- **Änderungen an der Zulassungsliste noch nicht wirksam:**

  Es kann bis zu fünf Minuten dauern, bis Änderungen an der Azure SQL-Datenbank-Firewallkonfiguration wirksam werden.

- **Anmeldung nicht autorisiert oder falsches Kennwort verwendet:**

  Wenn eine Anmeldung nicht über Berechtigungen für den Server verfügt oder das Kennwort falsch ist, wird die Verbindung mit dem Server verweigert. Durch Erstellen einer Firewalleinstellung wird Clients lediglich die *Möglichkeit* gegeben, eine Verbindung mit dem Server herzustellen. Der Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen. Weitere Informationen zum Vorbereiten von Anmeldungen finden Sie unter [Steuern und Gewähren des Datenbankzugriffs](logins-create-manage.md).

- **Dynamische IP-Adresse**:

  Wenn Sie über eine Internetverbindung mit dynamischer IP-Adresszuweisung verfügen und Probleme beim Passieren der Firewall haben, versuchen Sie Folgendes:
  
  - Erkundigen Sie sich bei Ihrem Internetdienstanbieter nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, die auf den Server zugreifen. Fügen Sie diesen IP-Adressbereich als IP-Firewallregel hinzu.
  - Verwenden Sie für Ihre Clientcomputer stattdessen eine statische IP-Adressierung. Fügen Sie die IP-Adressen als IP-Firewallregeln hinzu.

## <a name="next-steps"></a>Nächste Schritte

- Vergewissern Sie sich, dass die Netzwerkumgebung Ihres Unternehmens eingehende Kommunikation aus den Compute-IP-Adressbereichen (einschließlich SQL-Bereichen) zulässt, die von den Azure-Rechenzentren verwendet werden. Diese IP-Adressen müssen ggf. der Zulassungsliste hinzugefügt werden. Weitere Informationen finden Sie unter [IP-Bereiche für Microsoft Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653).  
- Eine Schnellstartanleitung zum Erstellen einer IP-Firewallregel auf Serverebene finden Sie unter [Erstellen einer einzelnen Datenbank in Azure SQL-Datenbank](single-database-create-quickstart.md).
- Hilfe beim Herstellen einer Verbindung mit einer Datenbank in Azure SQL-Datenbank über Open-Source-Anwendungen oder Drittanbieteranwendungen finden Sie unter [Clientschnellstart: Codebeispiele für Azure SQL-Datenbank](connect-query-content-reference-guide.md#libraries).
- Informationen zu zusätzlichen Ports, die Sie ggf. öffnen müssen, finden Sie im Abschnitt „Außerhalb im Vergleich zu innerhalb“ des Artikels [Andere Ports als 1433 für ADO.NET 4.5](adonet-v12-develop-direct-route-ports.md).
- Eine Übersicht über die Sicherheitsfunktionen von Azure SQL-Datenbank finden Sie [hier](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
