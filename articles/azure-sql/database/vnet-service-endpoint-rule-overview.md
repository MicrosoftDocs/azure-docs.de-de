---
title: VNET-Endpunkte und -Regeln für Datenbanken in Azure SQL-Datenbank
description: Markieren Sie ein Subnetz als VNET-Dienstendpunkt. Nutzen Sie dann den Endpunkt als VNET-Regel für die Zugriffssteuerungsliste Ihrer Datenbank. Ihre Datenbank akzeptiert anschließend Nachrichten von allen virtuellen Computern und anderen Knoten im Subnetz.
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
ms.openlocfilehash: c5839589c35ea5a9c52303801a8767fc598434fc
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905875"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Verwenden von Virtual Network-Dienstendpunkten und -Regeln für Server in Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

*VNET-Regeln* sind ein Firewallsicherheitsfeature, das steuert, ob der Server für Ihre Datenbanken und Pools für elastische Datenbanken in [Azure SQL-Datenbank](sql-database-paas-overview.md) oder für Ihre Datenbanken in [Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Nachrichten akzeptiert, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden. In diesem Artikel wird erläutert, warum die VNET-Regeln manchmal die beste Möglichkeit darstellen, um eine sichere Kommunikation mit Ihrer Datenbank in Azure SQL-Datenbank und Azure Synapse Analytics zu ermöglichen.

> [!NOTE]
> Dieser Artikel gilt sowohl für Azure SQL-Datenbank als auch für Azure Synapse Analytics. Der Einfachheit halber wird der Begriff „Datenbank“ verwendet, wenn auf Datenbanken sowohl in Azure SQL-Datenbank als auch in Azure Synapse Analytics verwiesen wird. Ebenso bezieht sich der Begriff „Server“ auf den [logischen SQL-Server](logical-servers.md), der Azure SQL-Datenbank und Azure Synapse Analytics hostet.

Damit eine VNET-Regel erstellt werden kann, muss zuerst ein [VNET-Dienstendpunkt][vm-virtual-network-service-endpoints-overview-649d] vorhanden sein, auf den die Regel verweisen kann.

## <a name="how-to-create-a-virtual-network-rule"></a>Erstellen einer VNET-Regel

Wenn Sie nur eine VNET-Regel erstellen, können Sie mit den Schritten und der Erklärung [weiter unten in diesem Artikel](#anchor-how-to-by-using-firewall-portal-59j) fortfahren.

## <a name="details-about-virtual-network-rules"></a>Details zu VNET-Regeln

In diesem Abschnitt werden verschiedene Details zu VNET-Regeln beschrieben.

### <a name="only-one-geographic-region"></a>Nur eine geografische Region

Jeder Virtual Network-Dienstendpunkt gehört nur zu einer Azure-Region. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz.

Eine VNET-Regel ist auf die Region beschränkt, zu der der zugrunde liegende Endpunkt gehört.

### <a name="server-level-not-database-level"></a>Auf Serverebene, nicht auf Datenbankebene

Jede VNET-Regel gilt für den gesamten Server und nicht nur für eine bestimmte Datenbank auf dem Server. Das heißt, dass VNET-Regeln auf Server- und nicht auf Datenbankebene gelten.

- Im Gegensatz dazu können IP-Regeln auf beiden Ebenen gelten.

### <a name="security-administration-roles"></a>Sicherheitsverwaltungsrollen

Bei der Verwaltung der VNET-Dienstendpunkte erfolgt eine Trennung von Sicherheitsrollen. Die folgenden Rollen müssen Aktionen ausführen:

- **Netzwerkadministrator:** &nbsp;Aktivieren des Endpunkts.
- **Datenbankadministrator:** &nbsp;Aktualisieren der Zugriffssteuerungsliste durch Hinzufügen des angegebenen Subnetzes zum Server.

*Alternative zur rollenbasierten Zugriffssteuerung von Azure (Azure RBAC):*

Die Rollen „Netzwerkadministrator“ und „Datenbankadministrator“ haben mehr Zugriffsrechte, als für die Verwaltung von VNET-Regeln erforderlich ist. Es wird nur eine Teilmenge der Zugriffsrechte benötigt.

Sie können die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC)][rbac-what-is-813s] in Azure verwenden, um eine einzelne benutzerdefinierte Rolle zu erstellen, die nur über die erforderliche Teilmenge von Berechtigungen verfügt. Die benutzerdefinierte Rolle kann definiert werden, anstatt den Netzwerk- oder Datenbankadministrator einzubeziehen. Die auf die Sicherheit bezogene Angriffsfläche ist kleiner, wenn Sie einen Benutzer einer benutzerdefinierte Rolle hinzufügen und ihn nicht den beiden anderen wichtigen Administratorrollen hinzufügen.

> [!NOTE]
> In einigen Fällen befinden sich die Datenbank in Azure SQL-Datenbank und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
>
> - Beide Abonnements müssen demselben Azure Active Directory-Mandanten zugeordnet sein.
> - Der Benutzer muss über die erforderlichen Berechtigungen zum Initiieren der Vorgänge verfügen. Dazu gehören z.B. das Aktivieren von Dienstendpunkten und das Hinzufügen eines VNET-Subnetzes auf dem angegebenen Server.
> - In beiden Abonnements muss der Anbieter „Microsoft.Sql“ registriert sein.

## <a name="limitations"></a>Einschränkungen

Bei Azure SQL-Datenbank gelten für VNET-Regeln folgende Einschränkungen:

- In der Firewall für Ihre Datenbank in Azure SQL-Datenbank verweist jede VNET-Regel auf ein Subnetz. Alle Subnetze, auf die verwiesen wird, müssen in derselben geografischen Region gehostet werden, in der die Datenbank gehostet wird.

- Für jeden Server können für ein angegebenes virtuelles Netzwerk maximal 128 Einträge in der Zugriffssteuerungsliste vorhanden sein.

- VNET-Regeln gelten nur für virtuelle Netzwerke gemäß dem Azure Resource Manager-Modell und nicht gemäß dem [klassischen Bereitstellungsmodell][arm-deployment-model-568f].

- Durch das Aktivieren von VNET-Dienstendpunkten für Azure SQL-Datenbank werden auch die Endpunkte für die Azure-Dienste MySQL und PostgreSQL aktiviert. Jedoch treten bei Verbindungen von den Endpunkten mit den MySQL- oder PostgreSQL-Instanzen möglicherweise Fehler auf, wenn Endpunkte aktiviert sind.
  - Der Grund dafür ist, dass für MySQL und PostgreSQL wahrscheinlich keine VNET-Regel konfiguriert wurde. Sie müssen für Azure Database for MySQL und PostgreSQL eine VNET-Regel konfigurieren, damit die Verbindung erfolgreich hergestellt wird.
  - Legen Sie zum Definieren von VNET-Firewallregeln auf einem logischen SQL-Server, der bereits mit privaten Endpunkten konfiguriert wurde, die Einstellung **Zugriff auf öffentliches Netzwerk verweigern** auf **Nein** fest.

- In der Firewall gelten zwar IP-Adressbereiche für die folgenden Netzwerkelemente, VNET-Regeln jedoch nicht:
  - [Virtuelles privates S2S-Netzwerk (Site-to-Site-VPN)][vpn-gateway-indexmd-608y]
  - Lokal über [ExpressRoute](../../expressroute/index.yml)

### <a name="considerations-when-using-service-endpoints"></a>Überlegungen zur Verwendung von Dienstendpunkten

Stellen Sie bei der Verwendung von Dienstendpunkten für die Azure SQL-Datenbank folgende Überlegungen an:

- **Ausgehend zu öffentlichen IP-Adressen der Azure SQL-Datenbank ist erforderlich**: Netzwerksicherheitsgruppen (NSGs) müssen für IP-Adressen der Azure SQL-Datenbank geöffnet werden, um Verbindungen zuzulassen. Sie erreichen dies, indem Sie [Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) der Netzwerksicherheitsgruppe für die Azure SQL-Datenbank verwenden.

### <a name="expressroute"></a>ExpressRoute

Wenn Sie [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

Um die Kommunikation von Ihrer Verbindung mit Azure SQL-Datenbank zu ermöglichen, müssen Sie IP-Netzwerkregeln für die öffentlichen IP-Adressen Ihrer Netzwerkadressenübersetzung erstellen.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Auswirkungen der Verwendung von VNET-Dienstendpunkten mit Azure Storage

In Azure Storage ist dasselbe Feature implementiert, mit dem Sie die Konnektivität mit Ihrem Azure Storage-Konto beschränken können. Wenn Sie dieses Feature mit einem Azure Storage-Konto verwenden, das von Azure SQL-Datenbank verwendet wird, können Probleme auftreten. Im Folgenden finden Sie eine Liste mit Erläuterungen der Features von Azure SQL-Datenbank und Azure Synapse Analytics, die hiervon betroffen sind.

### <a name="azure-synapse-polybase-and-copy-statement"></a>Azure Synapse PolyBase und COPY-Anweisung

Polybase und die COPY-Anweisung werden häufig verwendet, um Daten aus Azure Storage-Konten in Azure Synapse Analytics zu laden, um Datenerfassung mit hohem Durchsatz zu erzielen. Wenn das Azure Storage-Konto, aus dem Sie Daten laden, Zugriff nur auf eine Gruppe von VNET-Subnetzen beschränkt, wird die Konnektivität mit dem Speicherkonto unterbrochen, wenn PolyBase und die COPY-Anweisung verwendet werden. Führen Sie die unten angegebenen Schritte aus, um Import- und Exportszenarien mit COPY und PolyBase zu ermöglichen, in denen Azure Synapse Analytics eine Verbindung mit Azure Storage (im VNET gesichert) herstellt:

#### <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie Azure PowerShell anhand [dieses Leitfadens](/powershell/azure/install-az-ps).
- Falls Sie über ein universelles Speicherkonto (v1) oder ein Blobspeicherkonto verfügen, müssen Sie zuerst das Upgrade auf Version 2 des universellen Speicherkontos durchführen, indem Sie [diesen Leitfaden](../../storage/common/storage-account-upgrade.md) verwenden.
- Im Einstellungsmenü **Firewalls und virtuelle Netzwerke** des Azure Storage-Kontos muss die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert sein. Die Aktivierung dieser Konfiguration ermöglicht es PolyBase und der COPY-Anweisung, eine Verbindung mit dem Speicherkonto unter Verwendung starker Authentifizierung herzustellen, wobei der Netzwerkdatenverkehr auf dem Azure-Backbone verbleibt. Weitere Informationen finden Sie in [diesem Leitfaden](../../storage/common/storage-network-security.md#exceptions).

> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Das AzureRM-Modul erhält mindestens bis Dezember 2020 weiterhin Fehlerbehebungen.  Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch. Weitere Informationen zur Kompatibilität finden Sie in der [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

#### <a name="steps"></a>Schritte

1. Wenn Sie über einen eigenständigen dedizierten SQL-Pool verfügen, registrieren Sie Ihren SQL-Server mithilfe von PowerShell bei Azure Active Directory (AAD): 

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Dieser Schritt ist für dedizierte SQL-Pools in einem Synapse-Arbeitsbereich nicht erforderlich.

1. Wenn Sie über einen Synapse-Arbeitsbereich verfügen, registrieren Sie die vom System verwaltete Identität Ihres Arbeitsbereichs:

   1. Wechseln Sie im Azure-Portal zu Ihrem Synapse-Arbeitsbereich.
   2. Wechseln Sie zum Blatt „Verwaltete Identitäten“. 
   3. Vergewissern Sie sich, dass die Option „Pipelines zulassen“ aktiviert ist.
   
1. Erstellen Sie ein **Speicherkonto vom Typ „Universell v2“** , indem Sie [diesen Leitfaden](../../storage/common/storage-account-create.md) verwenden.

   > [!NOTE]
   >
   > - Falls Sie über ein universelles Speicherkonto (v1) oder ein Blobspeicherkonto verfügen, müssen Sie zuerst das **Upgrade auf Version 2** durchführen, indem Sie [diesen Leitfaden](../../storage/common/storage-account-upgrade.md) verwenden.
   > - Informationen zu bekannten Problemen mit Azure Data Lake Storage Gen2 finden Sie in [diesem Leitfaden](../../storage/blobs/data-lake-storage-known-issues.md).

1. Navigieren Sie unter Ihrem Speicherkonto zu **Zugriffssteuerung (IAM)** , und wählen Sie **Rollenzuweisung hinzufügen** aus. Weisen Sie dem Server oder Arbeitsbereich, auf bzw. in dem Ihr bei Azure Active Directory (AAD) registrierter dedizierter SQL-Pool gehostet wird, die Azure-Rolle **Mitwirkender an Storage-Blobdaten** zu.

   > [!NOTE]
   > Nur Mitglieder mit der Berechtigung „Besitzer“ für das Speicherkonto können diesen Schritt ausführen. Verschiedene integrierte Azure-Rollen finden Sie in diesem [Leitfaden](../../role-based-access-control/built-in-roles.md).
  
1. **PolyBase-Konnektivität mit dem Azure Storage-Konto:**

   1. Erstellen Sie einen **[Masterschlüssel](/sql/t-sql/statements/create-master-key-transact-sql)** für die Datenbank, falls Sie dies noch nicht durchgeführt haben:

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. Erstellen Sie mit **IDENTITY = 'Managed Service Identity'** datenbankweit gültige Anmeldeinformationen:

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - Es ist nicht erforderlich, für den Azure Storage-Zugriffsschlüssel den Zusatz SECRET anzugeben, da bei diesem Vorgang die [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) im Hintergrund verwendet wird.
       > - Der IDENTITY-Name sollte **'Managed Service Identity'** lauten, damit die PolyBase-Konnektivität mit dem im VNET gesicherten Azure Storage-Konto funktioniert.

   1. Erstellen Sie mit dem Schema `abfss://` eine externe Datenquelle für die Verbindungsherstellung mit Ihrem Speicherkonto vom Typ „Universell v2“ unter Verwendung von PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - Falls dem Speicherkonto vom Typ „Universell v1“ oder dem Blobspeicherkonto bereits externe Tabellen zugeordnet sind, sollten Sie zuerst diese externen Tabellen und dann die entsprechende externe Datenquelle verwerfen. Erstellen Sie anschließend mit dem Schema `abfss://` eine externe Datenquelle, um wie oben die Verbindung mit dem Speicherkonto vom Typ „Universell v2“ herzustellen, und erstellen Sie dann alle externen Tabellen mit dieser neuen externen Datenquelle neu. Sie können den [Assistenten zum Generieren und Veröffentlichen von Skripts](/sql/ssms/scripting/generate-and-publish-scripts-wizard) verwenden, um auf einfache Weise Erstellungsskripts für alle externen Tabellen zu generieren.
       > - Weitere Informationen zum Schema `abfss://` finden Sie in dieser [Anleitung](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md).
       > - Weitere Informationen zu CREATE EXTERNAL DATA SOURCE finden Sie in [diesem Leitfaden](/sql/t-sql/statements/create-external-data-source-transact-sql).

   1. Führen Sie Abfragen wie gewohnt durch, indem Sie [externe Tabellen](/sql/t-sql/statements/create-external-table-transact-sql) verwenden.

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL-Datenbank – Blobüberwachung

Bei der Blobüberwachung werden die Überwachungsprotokolle in Ihr eigenes Speicherkonto gepusht. Wenn dieses Speicherkonto das Feature für VNET-Dienstendpunkte verwendet, wird die Konnektivität zwischen Azure SQL-Datenbank und dem Speicherkonto unterbrochen.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Hinzufügen einer VNET-Firewallregel zu Ihrem Server ohne Aktivierung von VNET-Dienstendpunkten

Vor der Verbesserung dieses Features mussten Sie die VNET-Dienstendpunkte aktivieren, bevor Sie eine Live-VNET-Regel in der Firewall implementieren konnten. Die Endpunkte verknüpften ein bestimmtes VNET-Subnetz mit einer Datenbank in Azure SQL-Datenbank. Aber seit Januar 2018 können Sie diese Anforderung umgehen, indem Sie das Flag **IgnoreMissingVNetServiceEndpoint** festlegen.

Allein das Festlegen einer Firewallregel trägt nicht zur Sicherung des Servers bei. Sie müssen auch VNET-Dienstendpunkte aktivieren, damit der Server gesichert wird. Wenn Sie Dienstendpunkte aktivieren, fällt das VNET-Subnetz solange aus, bis der Übergang von „deaktiviert“ zu „aktiviert“ abgeschlossen ist. Dies gilt vor allem für sehr umfangreiche VNETs. Mithilfe des Flags **IgnoreMissingVNetServiceEndpoint** können Sie die Ausfallzeit während des Übergangs reduzieren bzw. vermeiden.

Verwenden Sie PowerShell, um das Flag **IgnoreMissingVNetServiceEndpoint** festzulegen. Weitere Informationen finden Sie unter [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fehler 40914 und 40615

Der Verbindungsfehler 40914 bezieht sich auf *VNET-Regeln*, die im Azure-Portal im Bereich „Firewall“ angegeben werden. Beim Fehler 40615 verhält es sich ähnlich, nur dass sich dieser Fehler auf *IP-Adressregeln* in der Firewall bezieht.

### <a name="error-40914"></a>Fehler 40914

*Nachrichtentext:* Der bei der Anmeldung angeforderte Server „ *[Servername]* “ kann nicht geöffnet werden. Dem Client ist der Zugriff auf den Server nicht gestattet.

*Fehlerbeschreibung:* Der Client befindet sich in einem Subnetz, das Endpunkte des virtuellen Netzwerkservers enthält. Der Server enthält jedoch keine VNET-Regeln, die dem Subnetz die Berechtigung zur Kommunikation mit der Datenbank gewähren.

*Fehlerbehebung:* Verwenden Sie im Azure-Portal im Bereich „Firewall“ die Steuerung von VNET-Regeln, um [VNET-Regel](#anchor-how-to-by-using-firewall-portal-59j) für das Subnetz hinzuzufügen.

### <a name="error-40615"></a>Fehler 40615

*Nachrichtentext:* Der für die Anmeldung angeforderte Server „{0}“ kann nicht geöffnet werden. Der Client mit der IP-Adresse „{1}“ hat keine Zugriffsberechtigung für den Server.

*Fehlerbeschreibung:* Der Client versucht, eine Verbindung über eine IP-Adresse herzustellen, die nicht zum Herstellen einer Verbindung mit dem Server autorisiert ist. Die Serverfirewall enthält keine IP-Adressregel, die einem Client die Kommunikation mit der Datenbank über eine bestimmte IP-Adresse erlaubt.

*Fehlerbehebung:* Geben Sie als IP-Regel die IP-Adresse des Clients ein. Tun Sie dies im Bereich „Firewall“ des Azure-Portals.

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="portal-can-create-a-virtual-network-rule"></a>Erstellen einer VNET-Regel über das Portal

In diesem Abschnitt wird veranschaulicht, wie Sie im [Azure-Portal][http-azure-portal-link-ref-477t] eine *VNET-Regel* in Ihrer Datenbank in Azure SQL-Datenbank erstellen. Die Regel weist Ihre Datenbank an, Nachrichten von einem bestimmten Subnetz zu akzeptieren, das als *VNET-Dienstendpunkt* gekennzeichnet ist.

> [!NOTE]
> Wenn Sie beabsichtigen, einen Dienstendpunkt zu den VNET-Firewallregeln Ihres Servers hinzuzufügen, stellen Sie zunächst sicher, dass die Dienstendpunkte für das Subnetz eingeschaltet sind.
>
> Wenn Dienstendpunkte für das Subnetz nicht eingeschaltet werden, fordert Sie das Portal auf, diese zu aktivieren. Klicken Sie auf demselben Blatt, auf dem Sie auch die Regel hinzufügen, auf die Schaltfläche **Aktivieren**.

## <a name="powershell-alternative"></a>PowerShell-Alternative

Mit einem Skript können Sie auch Regeln für virtuelle Netzwerke mithilfe des PowerShell-Cmdlets **New-AzSqlServerVirtualNetworkRule** oder [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) erstellen. Lesen Sie bei Interesse [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST-API-Alternative

Intern rufen die PowerShell-Cmdlets für SQL-VNet-Aktionen REST-APIs auf. Sie können die REST-APIs direkt aufrufen.

- [VNET-Regeln: Vorgänge][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie bereits ein Subnetz haben, das mit dem bestimmten Virtual Network-Dienstendpunkt gekennzeichnet ist, *geben Sie den Namen ein*, der zur Azure SQL-Datenbank-Instanz gehört.

- Der Typname dieses Endpunkts ist **Microsoft.Sql**.
- Wenn Ihr Subnetz nicht mit dem Typnamen gekennzeichnet werden kann, lesen Sie unter [Überprüfen, ob Ihr Subnetz ein Endpunkt ist][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100] nach.

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Schritte im Azure-Portal

1. Melden Sie sich beim [Azure-Portal][http-azure-portal-link-ref-477t] an.

2. Suchen Sie nach **SQL-Server**, und wählen Sie dann Ihren Server aus. Wählen Sie unter **Sicherheit** die Option **Firewalls und virtuelle Netzwerke** aus.

3. Legen Sie das Steuerelement **Zugriff auf Azure-Dienste erlauben** auf AUS fest.

    > [!IMPORTANT]
    > Wenn Sie für das Steuerelement die Einstellung „EIN“ beibehalten, akzeptiert der Server jegliche Kommunikation aus einem Subnetz innerhalb der Azure-Grenze (also jegliche Kommunikation, die von einer der IP-Adressen stammt, die innerhalb der für Azure-Rechenzentren definierten Bereiche liegen). Das Steuerelement auf ON festgelegt zu lassen, führt also möglicherweise aus Sicht der Sicherheit zu einem übermäßigen Zugriff. Mithilfe der Funktionen „Microsoft Azure Virtual Network-Dienstendpunkte“ und „VNET-Regel“ von SQL-Datenbank können Sie die sicherheitsbezogene Angriffsfläche verkleinern.

4. Klicken Sie im Abschnitt **Virtuelle Netzwerke** auf **+ Vorhandene hinzufügen**.

    ![Klicken Sie auf „Vorhandene hinzufügen“ (Subnetzendpunkt, als SQL-Regel).][image-portal-firewall-vnet-add-existing-10-png]

5. Füllen Sie im Bereich **Erstellen/Aktualisieren** die Steuerelemente mit den Namen Ihrer Azure-Ressourcen aus.

    > [!TIP]
    > Sie müssen das richtige **Adresspräfix** für Ihr Subnetz hinzufügen. Den Wert finden Sie im Portal.
    > Navigieren Sie zu **Alle Ressourcen** &gt; **Alle Typen** &gt; **Virtuelle Netzwerke**. Der Filter zeigt Ihre virtuellen Netzwerke an. Klicken Sie auf das virtuelle Netzwerk und dann auf **Subnetze**. Die Spalte **ADRESSBEREICH** weist das Adresspräfix auf, das Sie benötigen.

    ![Füllen Sie Felder für die neue Regel aus.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicken Sie am unteren Rand des Bereichs auf die Schaltfläche **OK**.

7. Die resultierende VNET-Regel wird im Firewallbereich angezeigt.

    ![Sie finden die neue Regel im Firewallbereich.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Für die Regeln gelten die folgenden Status oder Zustände:
>
> - **Bereit:** Gibt an, dass der von Ihnen initiierte Vorgang erfolgreich war.
> - **Fehlerhaft:** Gibt an, dass der von Ihnen initiierte Vorgang zu einem Fehler geführt hat.
> - **Gelöscht:** Gilt nur für den Löschvorgang und gibt an, dass die Regel gelöscht wurde und nicht mehr angewendet wird.
> - **In Bearbeitung:** Gibt an, dass der Vorgang ausgeführt wird. In diesem Zustand wird die alte Regel weiter angewendet.

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>Verwandte Artikel

- [Azure-VNET-Dienstendpunkte][vm-virtual-network-service-endpoints-overview-649d]
- [Firewallregeln auf Server- und Datenbankebene][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer VNET-Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
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
