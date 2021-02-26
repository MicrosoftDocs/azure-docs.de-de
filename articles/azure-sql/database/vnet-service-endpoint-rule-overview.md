---
title: VNET-Endpunkte und -Regeln für Datenbanken in Azure SQL-Datenbank
description: Markieren Sie ein Subnetz als VNET-Dienstendpunkt. Fügen Sie dann den Endpunkt als VNET-Regel der Zugriffssteuerungsliste Ihrer Datenbank hinzu. Ihre Datenbank akzeptiert anschließend Nachrichten von allen virtuellen Computern und anderen Knoten im Subnetz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095357"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Verwenden von Virtual Network-Dienstendpunkten und -Regeln für Server in Azure SQL-Datenbank

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*VNET-Regeln* sind ein Firewallsicherheitsfeature, mit dem gesteuert wird, ob der Server für Ihre Datenbanken und Pools für elastische Datenbanken in [Azure SQL-Datenbank](sql-database-paas-overview.md) oder für Ihre Datenbanken mit dediziertem SQL-Pool (ehemals SQL DW) in [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Nachrichten akzeptiert, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden. In diesem Artikel wird erläutert, warum VNET-Regeln manchmal die beste Möglichkeit darstellen, um eine sichere Kommunikation mit Ihrer Datenbank in SQL-Datenbank und Azure Synapse Analytics zu ermöglichen.

> [!NOTE]
> Dieser Artikel gilt sowohl für SQL-Datenbank als auch für Azure Synapse Analytics. Der Einfachheit halber wird der Begriff *Datenbank* verwendet, wenn auf Datenbanken sowohl in SQL-Datenbank als auch in Azure Synapse Analytics verwiesen wird. Ebenso bezieht sich der Begriff *Server* auf den [logischen SQL-Server](logical-servers.md), der SQL-Datenbank und Azure Synapse Analytics hostet.

Damit eine VNET-Regel erstellt werden kann, muss zuerst ein [VNET-Dienstendpunkt][vm-virtual-network-service-endpoints-overview-649d] vorhanden sein, auf den die Regel verweisen kann.

## <a name="create-a-virtual-network-rule"></a>Erstellen von VNET-Regeln

Wenn Sie nur eine VNET-Regel erstellen möchten, können Sie mit den Schritten und der Erklärung [weiter unten in diesem Artikel](#anchor-how-to-by-using-firewall-portal-59j) fortfahren.

## <a name="details-about-virtual-network-rules"></a>Details zu VNET-Regeln

In diesem Abschnitt werden verschiedene Details zu VNET-Regeln beschrieben.

### <a name="only-one-geographic-region"></a>Nur eine geografische Region

Jeder VNET-Dienstendpunkt gehört nur zu einer Azure-Region. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz.

Eine VNET-Regel ist auf die Region beschränkt, zu der der zugrunde liegende Endpunkt gehört.

### <a name="server-level-not-database-level"></a>Auf Serverebene, nicht auf Datenbankebene

Jede VNET-Regel gilt für den gesamten Server und nicht nur für eine bestimmte Datenbank auf dem Server. Das heißt, dass VNET-Regeln auf Server- und nicht auf Datenbankebene gelten.

Im Gegensatz dazu können IP-Regeln auf beiden Ebenen gelten.

### <a name="security-administration-roles"></a>Sicherheitsverwaltungsrollen

Bei der Verwaltung der VNET-Dienstendpunkte erfolgt eine Trennung von Sicherheitsrollen. Die folgenden Rollen müssen Aktionen ausführen:

- **Netzwerkadministrator (Rolle [Netzwerkmitwirkender](../../role-based-access-control/built-in-roles.md#network-contributor)):** &nbsp;Aktivieren des Endpunkts.
- **Datenbankadministrator (Rolle [Mitwirkender von SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor)):** &nbsp;Aktualisieren der Zugriffssteuerungsliste durch Hinzufügen des angegebenen Subnetzes zum Server.

#### <a name="azure-rbac-alternative"></a>Alternative zur Azure RBAC

Die Rollen „Netzwerkadministrator“ und „Datenbankadministrator“ haben mehr Zugriffsrechte, als für die Verwaltung von VNET-Regeln erforderlich ist. Es wird nur eine Teilmenge der Zugriffsrechte benötigt.

Sie können mit der [rollenbasierten Zugriffssteuerung (RBAC)][rbac-what-is-813s] in Azure arbeiten, um eine einzelne benutzerdefinierte Rolle zu erstellen, die nur über die benötigte Teilmenge von Zugriffsrechten verfügt. Die benutzerdefinierte Rolle kann definiert werden, anstatt den Netzwerk- oder Datenbankadministrator einzubeziehen. Die auf die Sicherheit bezogene Angriffsfläche ist kleiner, wenn Sie einen Benutzer einer benutzerdefinierte Rolle hinzufügen und ihn nicht den beiden anderen wichtigen Administratorrollen hinzufügen.

> [!NOTE]
> In einigen Fällen befinden sich die Datenbank in SQL-Datenbank und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
>
> - Beide Abonnements müssen demselben Azure Active Directory-Mandanten (Azure AD) zugeordnet sein.
> - Der Benutzer muss über die erforderlichen Berechtigungen zum Initiieren der Vorgänge verfügen. Dazu gehören z. B. das Aktivieren von Dienstendpunkten und das Hinzufügen eines VNET-Subnetzes auf dem angegebenen Server.
> - In beiden Abonnements muss der Anbieter „Microsoft.Sql“ registriert sein.

## <a name="limitations"></a>Einschränkungen

Bei SQL-Datenbank gelten für VNET-Regeln folgende Einschränkungen:

- In der Firewall für Ihre Datenbank in SQL-Datenbank verweist jede VNET-Regel auf ein Subnetz. Alle Subnetze, auf die verwiesen wird, müssen in derselben geografischen Region gehostet werden, in der die Datenbank gehostet wird.
- Für jeden Server können für ein angegebenes virtuelles Netzwerk maximal 128 Einträge in der Zugriffssteuerungsliste enthalten sein.
- VNET-Regeln gelten nur für virtuelle Netzwerke im Azure Resource Manager-Bereitstellungsmodell und nicht im [klassischen Bereitstellungsmodell][arm-deployment-model-568f].
- Durch das Aktivieren von VNET-Dienstendpunkten für SQL-Datenbank werden auch die Endpunkte für Azure Database for MySQL und Azure Database for PostgreSQL aktiviert. Wenn Endpunkte auf **EIN** festgelegt sind, führen Verbindungsversuche von den Endpunkten mit Ihren Azure Database for MySQL- oder Azure Database for PostgreSQL-Instanzen zu Fehlern.
  - Der Grund dafür ist, dass für Azure Database for MySQL und Azure Database for PostgreSQL wahrscheinlich keine VNET-Regel konfiguriert wurde. Sie müssen für Azure Database for MySQL und Azure Database for PostgreSQL eine VNET-Regel konfigurieren, damit die Verbindung erfolgreich hergestellt wird.
  - Legen Sie zum Definieren von Firewallregeln für virtuelle Netzwerke auf einem logischen SQL-Server, der bereits mit privaten Endpunkten konfiguriert wurde, die Einstellung **Zugriff auf öffentliches Netzwerk verweigern** auf **Nein** fest.
- In der Firewall gelten zwar IP-Adressbereiche für die folgenden Netzwerkelemente, VNET-Regeln jedoch nicht:
  - [Virtuelles privates S2S-Netzwerk (Site-to-Site-VPN)][vpn-gateway-indexmd-608y]
  - Lokal über [Azure ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-you-use-service-endpoints"></a>Überlegungen zur Verwendung von Dienstendpunkten

Berücksichtigen Sie bei der Verwendung von Dienstendpunkten für SQL-Datenbank folgende Überlegungen:

- **Ausgehend zu öffentlichen IP-Adressen von Azure SQL-Datenbank ist erforderlich:** Netzwerksicherheitsgruppen (NSGs) müssen für IP-Adressen von SQL-Datenbank geöffnet werden, um Verbindungen zuzulassen. Sie erreichen dies, indem Sie für SQL-Datenbank NSG-[Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) verwenden.

### <a name="expressroute"></a>ExpressRoute

Wenn Sie [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Weitere Informationen zur Netzwerkadressenübersetzung für öffentliches ExpressRoute-Peering und Microsoft-Peering finden Sie unter [NAT-Anforderungen für öffentliches Azure-Peering](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Um die Kommunikation von Ihrer Verbindung mit SQL-Datenbank zu ermöglichen, müssen Sie IP-Netzwerkregeln für die öffentlichen IP-Adressen Ihrer Netzwerkadressenübersetzung erstellen.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Auswirkungen der Verwendung von VNET-Dienstendpunkten mit Azure Storage

In Azure Storage ist dasselbe Feature implementiert, mit dem Sie die Konnektivität mit Ihrem Azure Storage-Konto beschränken können. Wenn Sie dieses Feature mit einem Azure Storage-Konto verwenden, das von SQL-Datenbank genutzt wird, können Probleme auftreten. Im Folgenden finden Sie eine Liste mit Erläuterungen der Features von SQL-Datenbank und Azure Synapse Analytics, die hiervon betroffen sind.

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase und COPY-Anweisung

PolyBase und die COPY-Anweisung werden häufig verwendet, um Daten aus Azure Storage-Konten in Azure Synapse Analytics zu laden und damit einen hohem Durchsatz bei der Datenerfassung zu erzielen. Wenn das Azure Storage-Konto, aus dem Sie Daten laden, den Zugriff nur auf eine Gruppe von VNET-Subnetzen beschränkt, wird die Konnektivität mit dem Speicherkonto unterbrochen, wenn PolyBase und die COPY-Anweisung verwendet werden. Führen Sie die in diesem Abschnitt angegebenen Schritte aus, um Import- und Exportszenarien mit der COPY-Anweisung und PolyBase zu ermöglichen, in denen Azure Synapse Analytics eine Verbindung mit Azure Storage (im VNET gesichert) herstellt.

#### <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie Azure PowerShell anhand [dieses Leitfadens](/powershell/azure/install-az-ps).
- Wenn Sie über ein Konto vom Typ „Universell V1“ oder ein Azure Blob Storage-Konto verfügen, müssen Sie zunächst ein Upgrade auf „Universell V2“ durchführen. Befolgen Sie dazu die Schritte in [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Universell V2“](../../storage/common/storage-account-upgrade.md).
- Im Einstellungsmenü **Firewalls und virtuelle Netzwerke** des Azure Storage-Kontos muss die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert sein. Die Aktivierung dieser Konfiguration ermöglicht es PolyBase und der COPY-Anweisung, eine Verbindung mit dem Speicherkonto unter Verwendung starker Authentifizierung herzustellen, wobei der Netzwerkdatenverkehr auf dem Azure-Backbone verbleibt. Weitere Informationen finden Sie in [diesem Leitfaden](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Das Azure Resource Manager-Modul von PowerShell wird von SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen. Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Schritte

1. Wenn Sie über einen eigenständigen dedizierten SQL-Pool verfügen, registrieren Sie Ihren SQL-Server mithilfe von PowerShell bei Azure AD:

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Dieser Schritt ist für dedizierte SQL-Pools in einem Azure Synapse Analytics-Arbeitsbereich nicht erforderlich.

1. Wenn Sie über einen Azure Synapse Analytics-Arbeitsbereich verfügen, registrieren Sie die vom System verwaltete Identität Ihres Arbeitsbereichs:

   1. Wechseln Sie im Azure-Portal zu Ihrem Azure Synapse Analytics-Arbeitsbereich.
   2. Wechseln Sie zum Bereich **Verwaltete Identitäten**.
   3. Vergewissern Sie sich, dass die Option **Pipelines zulassen** aktiviert ist.
   
1. Erstellen Sie anhand der Schritte unter [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) ein **Speicherkonto vom Typ „Universell V2“** .

   > [!NOTE]
   >
   > - Wenn Sie über ein Konto vom Typ „Universell V1“ oder ein Blob Storage-Konto verfügen, müssen Sie *zunächst ein Upgrade auf „Universell V2“ durchführen*. Befolgen Sie dazu die Schritte in [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Universell V2“](../../storage/common/storage-account-upgrade.md).
   > - Weitere Informationen zu Problemen mit Azure Data Lake Storage Gen2 finden Sie unter [Bekannte Probleme mit Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-known-issues.md).

1. Navigieren Sie unter Ihrem Speicherkonto zu **Zugriffssteuerung (IAM)** , und wählen Sie **Rollenzuweisung hinzufügen** aus. Weisen Sie dem Server oder Arbeitsbereich, auf bzw. in dem Ihr bei Azure AD registrierter dedizierter SQL-Pool gehostet wird, die Azure-Rolle **Mitwirkender an Storage-Blobdaten** zu.

   > [!NOTE]
   > Nur Mitglieder mit der Berechtigung „Besitzer“ für das Speicherkonto können diesen Schritt ausführen. Informationen zu den integrierten Azure-Rollen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).
  
1. So aktivieren Sie PolyBase-Konnektivität mit dem Azure Storage-Konto

   1. Erstellen Sie einen [Masterschlüssel](/sql/t-sql/statements/create-master-key-transact-sql) für die Datenbank, falls Sie dies noch nicht durchgeführt haben.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Erstellen Sie mit **IDENTITY = 'Managed Service Identity'** datenbankweit gültige Anmeldeinformationen.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Es ist nicht erforderlich, für den Azure Storage-Zugriffsschlüssel den Zusatz SECRET anzugeben, da bei diesem Vorgang die [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) im Hintergrund verwendet wird.
       > - Der IDENTITY-Name sollte **'Managed Service Identity'** lauten, damit die PolyBase-Konnektivität mit dem im virtuellen Netzwerk geschützten Azure Storage-Konto funktioniert.

   1. Erstellen Sie mit dem Schema `abfss://` eine externe Datenquelle für die Verbindungsherstellung mit Ihrem Speicherkonto vom Typ „Universell V2“ unter Verwendung von PolyBase.

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Falls dem Speicherkonto vom Typ „Universell V1“ oder dem Blob Storage-Konto bereits externe Tabellen zugeordnet sind, sollten Sie zuerst diese externen Tabellen verwerfen. Trennen Sie danach die entsprechende externe Datenquelle. Erstellen Sie als Nächstes wie oben gezeigt mit dem Schema `abfss://` eine externe Datenquelle, die mit Ihrem Speicherkonto vom Typ „Universell V2“ verbunden ist. Erstellen Sie dann alle externen Tabellen mit dieser neuen externen Datenquelle neu. Sie können den [Assistenten zum Generieren und Veröffentlichen von Skripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) verwenden, um auf einfache Weise Erstellungsskripts für alle externen Tabellen zu generieren.
       > - Weitere Informationen zum Schema `abfss://` finden Sie unter [Verwenden des Azure Data Lake Storage Gen2-URI](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Weitere Informationen zu CREATE EXTERNAL DATA SOURCE finden Sie in [diesem Leitfaden](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Führen Sie Abfragen wie gewohnt durch, indem Sie [externe Tabellen](/sql/t-sql/statements/create-external-table-transact-sql) verwenden.

### <a name="sql-database-blob-auditing"></a>SQL-Datenbank – Blobüberwachung

Bei der Blobüberwachung werden die Überwachungsprotokolle in Ihr eigenes Speicherkonto gepusht. Wenn dieses Speicherkonto das Feature für VNET-Dienstendpunkte verwendet, wird die Konnektivität zwischen SQL-Datenbank und dem Speicherkonto unterbrochen.

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>Hinzufügen einer Firewallregel für virtuelle Netzwerke auf dem Server

Vor der Verbesserung dieses Features mussten Sie die VNET-Dienstendpunkte aktivieren, bevor Sie eine aktive VNET-Regel in der Firewall implementieren konnten. Die Endpunkte verknüpften ein bestimmtes VNET-Subnetz mit einer Datenbank in SQL-Datenbank. Seit Januar 2018 können Sie diese Anforderung umgehen, indem Sie das Flag **IgnoreMissingVNetServiceEndpoint** festlegen. Nun können Sie auf dem Server eine Firewallregel für virtuelle Netzwerke hinzufügen, ohne VNET-Dienstendpunkte zu aktivieren.

Allein das Festlegen einer Firewallregel trägt nicht zum Schutz des Servers bei. Sie müssen auch VNET-Dienstendpunkte aktivieren, damit der Server geschützt wird. Wenn Sie Dienstendpunkte aktivieren, fällt das VNET-Subnetz solange aus, bis der Übergang von „deaktiviert“ zu „aktiviert“ abgeschlossen ist. Diese Ausfallzeit ist insbesondere bei großen virtuellen Netzwerken von Bedeutung. Mithilfe des Flags **IgnoreMissingVNetServiceEndpoint** können Sie die Ausfallzeit während des Übergangs reduzieren bzw. vermeiden.

Verwenden Sie PowerShell, um das Flag **IgnoreMissingVNetServiceEndpoint** festzulegen. Weitere Informationen finden Sie unter [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer Regel für SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fehler 40914 und 40615

Der Verbindungsfehler 40914 bezieht sich auf *VNET-Regeln*, die im Azure-Portal im Bereich **Firewall** angegeben werden. Beim Fehler 40615 verhält es sich ähnlich, nur dass sich dieser Fehler auf *IP-Adressregeln* in der Firewall bezieht.

### <a name="error-40914"></a>Fehler 40914

**Nachrichtentext:** „Der bei der Anmeldung angeforderte Server ' *[Servername]* ' kann nicht geöffnet werden. Dem Client ist der Zugriff auf den Server nicht gestattet.“

**Fehlerbeschreibung:** Der Client befindet sich in einem Subnetz, das Endpunkte des virtuellen Netzwerkservers enthält. Der Server enthält jedoch keine VNET-Regeln, die dem Subnetz die Berechtigung zur Kommunikation mit der Datenbank gewähren.

**Fehlerbehebung:** Verwenden Sie im Azure-Portal im Bereich **Firewall** die Steuerung von VNET-Regeln, um eine [VNET-Regel für das Subnetz hinzuzufügen](#anchor-how-to-by-using-firewall-portal-59j).

### <a name="error-40615"></a>Fehler 40615

**Nachrichtentext:** „In Anmeldung angeforderter Server '{0}'kann nicht geöffnet werden. Der Client mit der IP-Adresse '{1}' hat keine Zugriffsberechtigung für den Server.“

**Fehlerbeschreibung:** Der Client versucht, eine Verbindung über eine IP-Adresse herzustellen, die nicht zum Herstellen einer Verbindung mit dem Server autorisiert ist. Die Serverfirewall enthält keine IP-Adressregel, die einem Client die Kommunikation mit der Datenbank über eine bestimmte IP-Adresse erlaubt.

**Fehlerbehebung:** Geben Sie als IP-Regel die IP-Adresse des Clients ein. Führen Sie diesen Schritt im Bereich **Firewall** des Azure-Portals aus.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>Erstellen einer VNET-Regel im Portal

In diesem Abschnitt wird veranschaulicht, wie Sie im [Azure-Portal][http-azure-portal-link-ref-477t] eine *VNET-Regel* in Ihrer Datenbank in SQL-Datenbank erstellen. Die Regel weist Ihre Datenbank an, Nachrichten von einem bestimmten Subnetz zu akzeptieren, das als *VNET-Dienstendpunkt* gekennzeichnet ist.

> [!NOTE]
> Wenn Sie beabsichtigen, den VNET-Firewallregeln Ihres Servers einen Dienstendpunkt hinzuzufügen, stellen Sie zunächst sicher, dass die Dienstendpunkte für das Subnetz aktiviert sind.
>
> Wenn Dienstendpunkte für das Subnetz nicht aktiviert sind, werden Sie im Portal dazu aufgefordert. Wählen Sie im selben Bereich, auf dem Sie auch die Regel hinzufügen, die Schaltfläche **Aktivieren** aus.

## <a name="powershell-alternative"></a>PowerShell-Alternative

Mit einem Skript können Sie auch VNET-Regeln erstellen. Verwenden Sie dazu das PowerShell-Cmdlet **New-AzSqlServerVirtualNetworkRule** oder den Befehl [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Lesen Sie bei Interesse [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer Regel für SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST-API-Alternative

Intern rufen die PowerShell-Cmdlets für SQL-VNET-Aktionen REST-APIs auf. Sie können die REST-APIs direkt aufrufen.

- [VNET-Regeln: Vorgänge][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie bereits ein Subnetz haben, das mit dem bestimmten VNET-Dienstendpunkt gekennzeichnet ist, *geben Sie den Namen ein*, der zur SQL-Datenbank-Instanz gehört.

- Der Typname dieses Endpunkts ist **Microsoft.Sql**.
- Wenn Ihr Subnetz nicht mit dem Typnamen gekennzeichnet werden kann, lesen Sie unter [Überprüfen, ob Ihr Subnetz ein Endpunkt ist][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100] nach.

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Schritte im Azure-Portal

1. Melden Sie sich beim [Azure-Portal][http-azure-portal-link-ref-477t] an.

1. Suchen Sie nach **SQL-Server**, und wählen Sie dann Ihren Server aus. Wählen Sie unter **Sicherheit** die Option **Firewalls und virtuelle Netzwerke** aus.

1. Legen Sie **Zugriff auf Azure-Dienste zulassen** auf **AUS** fest.

    > [!IMPORTANT]
    > Wenn Sie das Steuerelement auf **EIN** festgelegt lassen, akzeptiert der Server Kommunikation von beliebigen Subnetzen in Azure. Dies umfasst die gesamte Kommunikation, die von einer der IP-Adressen stammt, die innerhalb der für Azure-Rechenzentren definierten Bereiche liegen. Das Steuerelement auf **EIN** festgelegt zu lassen, führt also möglicherweise aus Sicht der Sicherheit zu einem übermäßigen Zugriff. Mithilfe der Features VNET-Dienstendpunkte von Microsoft Azure und VNET-Regeln von SQL-Datenbank können Sie die sicherheitsrelevante Angriffsfläche verkleinern.

1. Wählen Sie im Abschnitt **Virtuelle Netzwerke** die Option **+ Vorhandene hinzufügen** aus.

    ![Screenshot der Auswahl von „+ Vorhandene hinzufügen“ (Subnetzendpunkt, als SQL-Regel)][image-portal-firewall-vnet-add-existing-10-png]

1. Füllen Sie im Bereich **Erstellen/Aktualisieren** die Felder mit den Namen Ihrer Azure-Ressourcen aus.

    > [!TIP]
    > Sie müssen das richtige Adresspräfix für Ihr Subnetz hinzufügen. Den Wert für das **Adresspräfix** finden Sie im Portal. Navigieren Sie zu **Alle Ressourcen** &gt; **Alle Typen** &gt; **Virtuelle Netzwerke**. Der Filter zeigt Ihre virtuellen Netzwerke an. Wählen Sie Ihr virtuelles Netzwerk und dann **Subnetze** aus. Die Spalte **ADRESSBEREICH** enthält das Adresspräfix, das Sie benötigen.

    ![Screenshot des Ausfüllens der Felder für die neue Regel][image-portal-firewall-create-update-vnet-rule-20-png]

1. Wählen Sie am unteren Rand des Bereichs die Schaltfläche **OK** aus.

1. Die resultierende VNET-Regel wird im Bereich **Firewall** angezeigt.

    ![Screenshot der neuen Regel im Bereich „Firewall“][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Für die Regeln gelten die folgenden Status oder Zustände:
>
> - **Bereit:** Gibt an, dass der von Ihnen initiierte Vorgang erfolgreich war.
> - **Fehler:** Gibt an, dass der von Ihnen initiierte Vorgang zu einem Fehler geführt hat.
> - **Gelöscht:** Gilt nur für den Löschvorgang und gibt an, dass die Regel gelöscht wurde und nicht mehr angewandt wird.
> - **InProgress** (In Bearbeitung): Gibt an, dass der Vorgang ausgeführt wird. In diesem Zustand wird die alte Regel weiter angewendet.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Verwandte Artikel

- [Azure-VNET-Dienstendpunkte][vm-virtual-network-service-endpoints-overview-649d]
- [Firewallregeln auf Server- und Datenbankebene][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer VNET-Regel für SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [VNET-Regeln: Vorgänge][rest-api-virtual-network-rules-operations-862r] mit REST-APIs

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
