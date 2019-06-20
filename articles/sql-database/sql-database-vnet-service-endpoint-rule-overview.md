---
title: VNET-Endpunkte und -Regeln für Einzel- und Pooldatenbanken  in Azure SQL | Microsoft-Dokumentation
description: Markieren Sie ein Subnetz als VNET-Dienstendpunkt. Nutzen Sie dann den Endpunkt als VNET-Regel für die Zugriffssteuerungsliste Ihrer Azure SQL-Datenbank-Instanz. Ihre Azure SQL-Datenbank-Instanz akzeptiert anschließend Nachrichten von allen virtuellen Computern und anderen Knoten im Subnetz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: vanto, genemi
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 8c33cd7fe702f46f9c88643895b96445a9aa6a78
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331424"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Verwenden von VNET-Dienstendpunkten und -Regeln für Datenbankserver

*VNET-Regeln* sind eine Firewallsicherheitsfunktion, die steuert, ob der Datenbankserver für Ihre Einzeldatenbanken und Ihren Pool für elastische Datenbanken in [Azure SQL-Datenbank](sql-database-technical-overview.md) oder für Ihre Datenbanken in [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Nachrichten akzeptiert, die von bestimmten Subnetzen in virtuellen Netzwerken gesendet werden. In diesem Artikel wird erklärt, warum VNET-Regeln mitunter die beste Möglichkeit darstellen, Nachrichten an Ihre Azure SQL-Datenbank- und SQL Data Warehouse-Instanz sicher zuzulassen.

> [!IMPORTANT]
> Dieser Artikel gilt für den Azure SQL-Datenbankserver sowie für Datenbanken von SQL-Datenbank und SQL Data Warehouse, die auf dem Azure SQL-Datenbankserver erstellt werden. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind. Dieser Artikel gilt *nicht* für die Bereitstellungsoption **Verwaltete Instanz** in Azure SQL-Datenbank, da dieser kein Dienstendpunkt zugeordnet ist.

Damit eine VNET-Regel erstellt werden kann, muss zuerst ein [VNET-Dienstendpunkt][vm-virtual-network-service-endpoints-overview-649d] vorhanden sein, auf den die Regel verweisen kann.

## <a name="how-to-create-a-virtual-network-rule"></a>Erstellen einer VNET-Regel

Wenn Sie nur eine VNET-Regel erstellen, können Sie mit den Schritten und der Erklärung [weiter unten in diesem Artikel](#anchor-how-to-by-using-firewall-portal-59j) fortfahren.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie und Beschreibung

**Virtuelles Netzwerk:** Sie können Ihrem Azure-Abonnement virtuelle Netzwerke zuordnen.

**Subnetz:** Ein virtuelles Netzwerk enthält **Subnetze**. Ihre virtuellen Azure-Computer (VMs) sind Subnetzen zugewiesen. Ein Subnetz kann mehrere VMs oder andere Computeknoten enthalten. Computeknoten, die sich außerhalb Ihres virtuellen Netzwerks befinden, können nicht auf Ihr virtuelles Netzwerk zugreifen, es sei denn, Sie konfigurieren für sie den sicheren Zugriff.

**Virtual Network-Dienstendpunkt:** Ein [Virtual Network-Dienstendpunkt][vm-virtual-network-service-endpoints-overview-649d] ist ein Subnetz, dessen Eigenschaftswerte mindestens einen formalen Azure-Diensttypnamen enthalten. In diesem Artikel beschäftigen wir uns mit dem Typnamen **Microsoft.Sql**, der auf einen Azure-Dienst mit dem Namen „SQL-Datenbank“ verweist.

**VNET-Regel:** Eine VNET-Regel für Ihren SQL-Datenbank-Server ist ein Subnetz, das in der Zugriffssteuerungsliste des SQL-Datenbank-Servers aufgeführt ist. Um in die Zugriffssteuerungsliste für Ihre SQL-Datenbank-Instanz zu gelangen, muss das Subnetz den Typnamen **Microsoft.Sql** enthalten.

Eine VNET-Regel weist Ihren Azure SQL-Datenbank-Server an, Nachrichten von jedem Knoten anzunehmen, der zum Subnetz gehört.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Vorteile einer VNET-Regel

Bis Sie Maßnahmen ergreifen, können die virtuellen Computer in Ihren Subnetzen nicht mit Ihrer Azure SQL-Datenbank-Instanz kommunizieren. Eine Aktion zum Herstellen der Kommunikation stellt die Erstellung einer VNET-Regel dar. Die Begründung der Entscheidung für eine VNET-Regel erfordert eine Erörterung der Vor- und Nachteile, die die von der Firewall gebotenen konkurrierenden Sicherheitsoptionen berücksichtigt.

### <a name="a-allow-access-to-azure-services"></a>A. Zugriff auf Azure-Dienste erlauben

Der Firewallbereich enthält eine Schaltfläche des Typs **EIN/AUS** mit der Bezeichnung **Zugriff auf Azure-Dienste erlauben**. Die Einstellung **EIN** lässt Nachrichten von allen Azure IP-Adressen und aus allen Azure-Subnetzen zu. Diese Azure-IP-Adressen oder -Subnetze gehören möglicherweise nicht Ihnen. Die Einstellung **EIN** lässt wahrscheinlich einen umfassenderen Zugriff auf Ihre SQL-Datenbank-Instanz zu, als von Ihnen gewünscht. Eine VNET-Regel ermöglicht eine präzisere Steuerung.

### <a name="b-ip-rules"></a>B: IP-Regeln

Die Firewall von SQL-Datenbank ermöglicht Ihnen das Angeben von IP-Adressbereichen, aus denen Nachrichten an die SQL-Datenbank-Instanz gesendet werden dürfen. Dieser Ansatz eignet sich gut für statische IP-Adressen, die sich außerhalb des privaten Azure-Netzwerks befinden. Doch viele Knoten innerhalb des privaten Azure-Netzwerks sind mit *dynamischen* IP-Adressen konfiguriert. Dynamische IP-Adressen können sich ändern, z.B. wenn Ihre VM neu gestartet wird. Es wäre töricht, eine dynamische IP-Adresse in einer Firewallregel in einer Produktionsumgebung anzugeben.

Sie können die IP-Option weiter nutzen, indem Sie eine *statische* IP-Adresse für Ihre VM abrufen. Einzelheiten finden Sie unter [Konfigurieren von privaten IP-Adressen für einen virtuellen Computer über das Azure-Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Der Ansatz mit statischen IP-Adressen kann jedoch schwierig zu handhaben und aufwendig sein, wenn er in großem Maßstab befolgt wird. VNET-Regeln sind einfacher einzurichten und zu verwalten.

> [!NOTE]
> Azure SQL-Datenbank wird noch nicht in einem Subnetz unterstützt. Wenn sich Ihr Azure SQL-Datenbank-Server auf einem Knoten in einem Subnetz in Ihrem virtuellen Netzwerk befände, könnten alle Knoten innerhalb des virtuellen Netzwerks mit Ihrer SQL-Datenbank-Instanz kommunizieren. In diesem Fall könnten Ihre virtuellen Computer mit der SQL-Datenbank-Instanz kommunizieren, ohne dass VNET-Regeln oder IP-Regeln erforderlich sind.

Doch im September 2017 gehört der Azure SQL-Datenbank-Dienst noch nicht zu den Diensten, die einem Subnetz zugewiesen werden können.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Details zu VNET-Regeln

In diesem Abschnitt werden verschiedene Details zu VNET-Regeln beschrieben.

### <a name="only-one-geographic-region"></a>Nur eine geografische Region

Jeder Virtual Network-Dienstendpunkt gehört nur zu einer Azure-Region. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz.

Eine VNET-Regel ist auf die Region beschränkt, zu der der zugrunde liegende Endpunkt gehört.

### <a name="server-level-not-database-level"></a>Auf Serverebene, nicht auf Datenbankebene

Jede VNET-Regel gilt für den gesamten Azure SQL-Datenbank-Server und nicht nur für eine bestimmte Datenbank auf dem Server. Das heißt, dass VNET-Regeln auf Server- und nicht auf Datenbankebene gelten.

- Im Gegensatz dazu können IP-Regeln auf beiden Ebenen gelten.

### <a name="security-administration-roles"></a>Sicherheitsverwaltungsrollen

Bei der Verwaltung der VNET-Dienstendpunkte erfolgt eine Trennung von Sicherheitsrollen. Die folgenden Rollen müssen Aktionen ausführen:

- **Netzwerkadministrator:** &nbsp;Aktivieren des Endpunkts.
- **Datenbankadministrator:** &nbsp;Aktualisieren der Zugriffssteuerungsliste durch Hinzufügen des angegebenen Subnetzes zum Azure SQL-Datenbank-Server.

*Alternative zur rollenbasierten Zugriffssteuerung:*

Die Rollen „Netzwerkadministrator“ und „Datenbankadministrator“ haben mehr Zugriffsrechte, als für die Verwaltung von VNET-Regeln erforderlich ist. Es wird nur eine Teilmenge der Zugriffsrechte benötigt.

Sie können mit der [rollenbasierten Zugriffssteuerung (RBAC)][rbac-what-is-813s] in Azure arbeiten, um eine einzelne benutzerdefinierte Sicherheitsrolle zu erstellen, die nur über die benötigte Teilmenge von Zugriffsrechten verfügt. Die benutzerdefinierte Rolle kann definiert werden, anstatt den Netzwerk- oder Datenbankadministrator einzubeziehen. Die auf die Sicherheit bezogene Angriffsfläche ist kleiner, wenn Sie einen Benutzer einer benutzerdefinierte Rolle hinzufügen und ihn nicht den beiden anderen wichtigen Administratorrollen hinzufügen.

> [!NOTE]
> In einigen Fällen befinden sich die Azure SQL-Datenbank und das VNET-Subnetz in unterschiedlichen Abonnements. In diesen Fällen müssen Sie folgende Konfigurationen sicherstellen:
> - Beide Abonnements müssen demselben Azure Active Directory-Mandanten zugeordnet sein.
> - Der Benutzer muss über die erforderlichen Berechtigungen zum Initiieren der Vorgänge verfügen. Dazu gehören z.B. das Aktivieren von Dienstendpunkten und das Hinzufügen eines VNET-Subnetzes auf dem angegebenen Server.
> - In beiden Abonnements muss der Anbieter „Microsoft.Sql“ registriert sein.

## <a name="limitations"></a>Einschränkungen

Bei Azure SQL-Datenbank gelten für VNET-Regeln folgende Einschränkungen:

- Eine Web-App kann einer privaten IP in einem VNET/Subnetz zugeordnet werden. Auch wenn Dienstendpunkte im entsprechenden VNET/Subnetz aktiviert sind, haben Verbindungen zwischen der Web-App und dem Server keine VNET-/Subnetzquelle, sondern eine öffentliche Azure-IP-Quelle. Um die Verbindung zwischen einer Web-App und einem Server mit VNET-Firewallregeln zu ermöglichen, müssen Sie auf dem Server **Azure-Diensten Zugriff auf den Server erlauben**.

- In der Firewall für Ihre SQL-Datenbank verweist jede VNET-Regel auf ein Subnetz. Alle Subnetze, auf die verwiesen wird, müssen in derselben geografischen Region gehostet werden, in der die SQL-Datenbank gehostet wird.

- Für jeden Azure SQL-Datenbank-Server können für ein angegebenes virtuelles Netzwerk maximal 128 Einträge in der Zugriffssteuerungsliste vorhanden sein.

- VNET-Regeln gelten nur für virtuelle Netzwerke gemäß dem Azure Resource Manager-Modell und nicht gemäß dem [klassischen Bereitstellungsmodell][arm-deployment-model-568f].

- Durch das Aktivieren von VNET-Dienstendpunkten für Azure SQL-Datenbank werden auch die Endpunkte für die Azure-Dienste MySQL und PostgreSQL aktiviert. Jedoch treten bei Verbindungen von den Endpunkten mit den MySQL- oder PostgreSQL-Instanzen möglicherweise Fehler auf, wenn Endpunkte aktiviert sind.
  - Der Grund dafür ist, dass für MySQL und PostgreSQL wahrscheinlich keine VNET-Regel konfiguriert wurde. Sie müssen für Azure Database for MySQL und PostgreSQL eine VNET-Regel konfigurieren, damit die Verbindung erfolgreich hergestellt wird.

- In der Firewall gelten zwar IP-Adressbereiche für die folgenden Netzwerkelemente, VNET-Regeln jedoch nicht:
  - [Virtuelles privates Netzwerk zwischen Standorten][vpn-gateway-indexmd-608y]
  - Lokal über [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Überlegungen zur Verwendung von Dienstendpunkten

Stellen Sie bei der Verwendung von Dienstendpunkten für die Azure SQL-Datenbank folgende Überlegungen an:

- **Ausgehend zu öffentlichen IP-Adressen der Azure SQL-Datenbank ist erforderlich**: Netzwerksicherheitsgruppen (NSGs) müssen für IP-Adressen der Azure SQL-Datenbank geöffnet werden, um Verbindungen zuzulassen. Sie erreichen dies, indem Sie [Diensttags](../virtual-network/security-overview.md#service-tags) der Netzwerksicherheitsgruppe für die Azure SQL-Datenbank verwenden.

### <a name="expressroute"></a>ExpressRoute

Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lokal für öffentliches Peering oder für Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die verwendeten NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt. Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Erfahren Sie mehr über [NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).
  
Um die Kommunikation von Ihrer Verbindung mit Azure SQL-Datenbank zu ermöglichen, müssen Sie IP-Netzwerkregeln für die öffentlichen IP-Adressen Ihrer Netzwerkadressenübersetzung erstellen.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Auswirkungen des Entfernens des Zugriffs „Azure-Diensten Zugriff auf den Server erlauben“

Viele Benutzer möchten den Zugriff **Azure-Diensten Zugriff auf den Server erlauben** von ihren Azure SQL-Servern entfernen und durch eine VNET-Firewallregel ersetzen.
Das Entfernen wirkt sich aber auf die folgenden Features aus:

### <a name="import-export-service"></a>Import/Export-Dienst

Der Azure SQL-Datenbank-Import-/Exportdienst wird auf virtuellen Computern in Azure ausgeführt. Diese virtuellen Computer befinden sich nicht in Ihrem VNET und erhalten daher beim Verbinden mit Ihrer Datenbank eine Azure-IP-Adresse. Beim Entfernen des Zugriffs **Azure-Diensten den Zugriff auf den Server erlauben** können diese virtuellen Computer nicht mehr auf Ihre Datenbanken zugreifen.
Sie können das Problem umgehen. Führen Sie den BACPAC-Import oder -Export mithilfe der mithilfe der DACFx-API direkt im Code aus. Stellen Sie sicher, dass die Bereitstellung auf einem virtuellen Computer in dem VNET-Subnetz erfolgt, für das Sie die Firewallregel festgelegt haben.

### <a name="sql-database-query-editor"></a>Abfrage-Editor für SQL-Datenbank

Der Abfrage-Editor für Azure SQL-Datenbank wird auf virtuellen Computern in Azure bereitgestellt. Diese virtuellen Computer befinden sich nicht in Ihrem VNET. Aus diesem Grund erhalten die virtuellen Computer beim Verbinden mit Ihrer Datenbank eine Azure-IP-Adresse. Beim Entfernen von **Azure-Diensten den Zugriff auf den Server erlauben** können diese virtuellen Computer nicht mehr auf Ihre Datenbanken zugreifen.

### <a name="table-auditing"></a>Tabellenüberwachung

Derzeit stehen Ihnen zwei Möglichkeiten zum Aktivieren der Überwachung für Ihre SQL-Datenbank-Instanz zur Verfügung. Die Tabellenüberwachung führt zu einem Fehler, nachdem Sie Dienstendpunkte in Ihrer Azure SQL Server-Instanz aktiviert haben. Um dieses Problem zu umgehen, wechseln Sie zur Blobüberwachung.

### <a name="impact-on-data-sync"></a>Auswirkungen auf die Datensynchronisierung

Azure SQL-Datenbank verfügt über ein Datensynchronisierungsfeature, das unter Verwendung von Azure-IP-Adressen eine Verbindung mit Ihren Datenbanken herstellt. Bei der Verwendung von Dienstendpunkten werden Sie wahrscheinlich den Zugriff **Allen Azure-Diensten Zugriff auf den Server erlauben** auf Ihrem SQL-Datenbank-Server deaktivieren. Dadurch wird die Datensynchronisierungsfunktion unterbrochen.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Auswirkungen der Verwendung von VNET-Dienstendpunkten mit Azure Storage

In Azure Storage ist dasselbe Feature implementiert, mit dem Sie die Konnektivität mit Ihrem Azure Storage-Konto beschränken können. Wenn Sie dieses Feature mit einem Azure Storage-Konto verwenden, das von Azure SQL Server verwendet wird, können Probleme auftreten. Im Folgenden finden Sie eine Liste mit Erläuterungen der Features von Azure SQL-Datenbank und Azure SQL Data Warehouse, die hiervon betroffen sind.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse – PolyBase

PolyBase wird häufig verwendet, um Daten aus Azure Storage-Konten in Azure SQL Data Warehouse zu laden. Wenn das Azure Storage-Konto, aus dem Sie Daten laden, den Zugriff auf einen Satz von VNET-Subnetzen beschränkt, wird die Konnektivität zwischen PolyBase und dem Konto unterbrochen. Führen Sie die unten angegebenen Schritte aus, um PolyBase-Import- und -Exportszenarien zu ermöglichen, in denen Azure SQL Data Warehouse eine Verbindung mit Azure Storage (im VNET gesichert) herstellt:

#### <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

1.  Installieren Sie Azure PowerShell anhand [dieses Leitfadens](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Falls Sie über ein universelles Speicherkonto (v1) oder ein Blobspeicherkonto verfügen, müssen Sie zuerst das Upgrade auf Version 2 des universellen Speicherkontos durchführen, indem Sie [diesen Leitfaden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) verwenden.
3.  Im Einstellungsmenü **Firewalls und virtuelle Netzwerke** des Azure Storage-Kontos muss die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert sein. Weitere Informationen finden Sie in [diesem Leitfaden](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
 
#### <a name="steps"></a>Schritte
1. **Registrieren Sie Ihren SQL-Datenbank-Server** in PowerShell mit Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Erstellen Sie ein **Speicherkonto vom Typ „Universell v2“** , indem Sie [diesen Leitfaden](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) verwenden.

   > [!NOTE]
   > - Falls Sie über ein universelles Speicherkonto (v1) oder ein Blobspeicherkonto verfügen, müssen Sie zuerst das **Upgrade auf Version 2** durchführen, indem Sie [diesen Leitfaden](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) verwenden.
   > - Informationen zu bekannten Problemen mit Azure Data Lake Storage Gen2 finden Sie in [diesem Leitfaden](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. Navigieren Sie unter Ihrem Speicherkonto zu **Zugriffssteuerung (IAM)** , und klicken Sie auf **Rollenzuweisung hinzufügen**. Weisen Sie Ihrem SQL-Datenbank-Server die RBAC-Rolle **Mitwirkender an Storage-Blobdaten** zu.

   > [!NOTE] 
   > Nur Mitglieder mit der Berechtigung „Besitzer“ können diesen Schritt ausführen. Verschiedene integrierte Rollen für Azure-Ressourcen finden Sie in [diesem Leitfaden](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **PolyBase-Konnektivität mit dem Azure Storage-Konto:**

   1. Erstellen Sie einen **[Masterschlüssel](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** für die Datenbank, falls Sie dies noch nicht durchgeführt haben:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Erstellen Sie mit **IDENTITY = 'Managed Service Identity'** datenbankweit gültige Anmeldeinformationen:

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - Es ist nicht erforderlich, für den Azure Storage-Zugriffsschlüssel den Zusatz SECRET anzugeben, da bei diesem Vorgang die [verwaltete Identität](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) im Hintergrund verwendet wird.
       > - Der IDENTITY-Name sollte **'Managed Service Identity'** lauten, damit die PolyBase-Konnektivität mit dem im VNET gesicherten Azure Storage-Konto funktioniert.    
    
   1. Erstellen Sie mit dem Schema „abfss://“ eine externe Datenquelle für die Verbindungsherstellung mit Ihrem Speicherkonto vom Typ „Universell v2“, indem Sie PolyBase verwenden:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Falls dem Speicherkonto vom Typ „Universell v1“ oder dem Blobspeicherkonto bereits externe Tabellen zugeordnet sind, sollten Sie zuerst diese externen Tabellen und dann die entsprechende externe Datenquelle verwerfen. Erstellen Sie anschließend mit dem Schema „abfss://“ eine externe Datenquelle, um wie oben die Verbindung mit dem Speicherkonto vom Typ „Universell v2“ herzustellen, und erstellen Sie dann alle externen Tabellen neu, indem Sie diese neue externe Datenquelle verwenden. Sie können den [Assistenten zum Generieren und Veröffentlichen von Skripts](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) verwenden, um auf einfache Weise Erstellungsskripts für alle externen Tabellen zu generieren.
       > - Weitere Informationen zum Schema „abfss://“ finden Sie in [diesem Leitfaden](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Weitere Informationen zu CREATE EXTERNAL DATA SOURCE finden Sie in [diesem Leitfaden](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Führen Sie Abfragen wie gewohnt durch, indem Sie [externe Tabellen](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) verwenden.

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL-Datenbank – Blobüberwachung

Bei der Blobüberwachung werden die Überwachungsprotokolle in Ihr eigenes Speicherkonto gepusht. Wenn dieses Speicherkonto das Feature für VNET-Dienstendpunkte verwendet, wird die Konnektivität zwischen Azure SQL-Datenbank und dem Speicherkonto unterbrochen.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Hinzufügen einer VNET-Firewallregel zu Ihrem Server ohne Aktivierung von VNET-Dienstendpunkten

Vor der Verbesserung dieses Features mussten Sie die VNET-Dienstendpunkte aktivieren, bevor Sie eine Live-VNET-Regel in der Firewall implementieren konnten. Die Endpunkte verknüpften ein bestimmtes VNET-Subnetz mit einer Azure SQL-Datenbank. Aber seit Januar 2018 können Sie diese Anforderung umgehen, indem Sie das Flag **IgnoreMissingVNetServiceEndpoint** festlegen.

Allein das Festlegen einer Firewallregel trägt nicht zur Sicherung des Servers bei. Sie müssen auch VNET-Dienstendpunkte aktivieren, damit der Server gesichert wird. Wenn Sie Dienstendpunkte aktivieren, fällt das VNET-Subnetz solange aus, bis der Übergang von „deaktiviert“ zu „aktiviert“ abgeschlossen ist. Dies gilt vor allem für sehr umfangreiche VNETs. Mithilfe des Flags **IgnoreMissingVNetServiceEndpoint** können Sie die Ausfallzeit während des Übergangs reduzieren bzw. vermeiden.

Verwenden Sie PowerShell, um das Flag **IgnoreMissingVNetServiceEndpoint** festzulegen. Weitere Informationen finden Sie unter [Verwenden von PowerShell zum Erstellen eines Virtual Network-Dienstendpunkts und einer Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Fehler 40914 und 40615

Der Verbindungsfehler 40914 bezieht sich auf *VNET-Regeln*, die im Azure-Portal im Bereich „Firewall“ angegeben werden. Beim Fehler 40615 verhält es sich ähnlich, nur dass sich dieser Fehler auf *IP-Adressregeln* in der Firewall bezieht.

### <a name="error-40914"></a>Fehler 40914

*Nachrichtentext:* Der bei der Anmeldung angeforderte Server „ *[Servername]* “ kann nicht geöffnet werden. Dem Client ist der Zugriff auf den Server nicht gestattet.

*Fehlerbeschreibung:* Der Client befindet sich in einem Subnetz, das Endpunkte des virtuellen Netzwerkservers enthält. Der Azure SQL-Datenbank-Server enthält jedoch keine VNET-Regeln, die dem Subnetz die Berechtigung zur Kommunikation mit SQL-Datenbank gewähren.

*Fehlerbehebung:* Verwenden Sie im Azure-Portal im Bereich „Firewall“ die Steuerung von VNET-Regeln, um [VNET-Regel](#anchor-how-to-by-using-firewall-portal-59j) für das Subnetz hinzuzufügen.

### <a name="error-40615"></a>Fehler 40615

*Nachrichtentext:* Der für die Anmeldung angeforderte Server „{0}“ kann nicht geöffnet werden. Der Client mit der IP-Adresse „{1}“ hat keine Zugriffsberechtigung für den Server.

*Fehlerbeschreibung:* Der Client versucht, eine Verbindung über eine IP-Adresse herzustellen, die nicht zum Herstellen einer Verbindung mit dem Azure SQL-Datenbank-Server autorisiert ist. Die Serverfirewall enthält keine IP-Adressregel, die einem Client die Kommunikation mit der SQL-Datenbank über eine bestimmte IP-Adresse erlaubt.

*Fehlerbehebung:* Geben Sie als IP-Regel die IP-Adresse des Clients ein. Tun Sie dies im Bereich „Firewall“ des Azure-Portals.

Eine Liste verschiedener Fehlermeldungen der SQL-Datenbank ist [hier][sql-database-develop-error-messages-419g] dokumentiert.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Erstellen einer VNET-Regel über das Portal

In diesem Abschnitt wird veranschaulicht, wie Sie im [Azure-Portal][http-azure-portal-link-ref-477t] eine *VNET-Regel* in Ihrer Azure SQL-Datenbank-Instanz erstellen. Die Regel weist Ihre SQL-Datenbank-Instanz an, Nachrichten von einem bestimmten Subnetz zu akzeptieren, das als *VNET-Dienstendpunkt* gekennzeichnet ist.

> [!NOTE]
> Wenn Sie beabsichtigen, einen Dienstendpunkt zu den VNET-Firewallregeln Ihres Azure SQL-Datenbank-Servers hinzuzufügen, stellen Sie zunächst sicher, dass die Dienstendpunkte für das Subnetz eingeschaltet sind.
>
> Wenn Dienstendpunkte für das Subnetz nicht eingeschaltet werden, fordert Sie das Portal auf, diese zu aktivieren. Klicken Sie auf demselben Blatt, auf dem Sie auch die Regel hinzufügen, auf die Schaltfläche **Aktivieren**.

## <a name="powershell-alternative"></a>PowerShell-Alternative

VNET-Regeln können auch mit einem PowerShell-Skript erstellt werden. Dazu dient das Cmdlet **New-AzSqlServerVirtualNetworkRule**. Lesen Sie bei Interesse [Verwenden von PowerShell zum Erstellen eines Virtual Network-Dienstendpunkts und einer Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST-API-Alternative

Intern rufen die PowerShell-Cmdlets für SQL-VNet-Aktionen REST-APIs auf. Sie können die REST-APIs direkt aufrufen.

- [VNET-Regeln: Vorgänge][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie bereits ein Subnetz haben, das mit dem bestimmten Virtual Network-Dienstendpunkt gekennzeichnet ist, *geben Sie den Namen ein*, der zur Azure SQL-Datenbank-Instanz gehört.

- Der Typname dieses Endpunkts ist **Microsoft.Sql**.
- Wenn Ihr Subnetz nicht mit dem Typnamen gekennzeichnet werden kann, siehe [Überprüfen, dass Ihr Subnetz ein Endpunkt ist][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Schritte im Azure-Portal

1. Melden Sie sich beim [Azure-Portal][http-azure-portal-link-ref-477t] an.

2. Navigieren Sie dann im Portal zu **Computer mit SQL Server**&gt;**Firewall/Virtuelle Netzwerke**.

3. Legen Sie das Steuerelement **Zugriff auf Azure-Dienste erlauben** auf AUS fest.

    > [!IMPORTANT]
    > Wenn Sie das Steuerelement auf ON festgelegt lassen, akzeptiert der Azure SQL-Datenbank-Server Kommunikation von beliebigen Subnetzen. Das Steuerelement auf ON festgelegt zu lassen, führt also möglicherweise aus Sicht der Sicherheit zu einem übermäßigen Zugriff. Mithilfe der Funktionen „Microsoft Azure Virtual Network-Dienstendpunkte“ und „VNET-Regel“ von SQL-Datenbank können Sie die sicherheitsbezogene Angriffsfläche verkleinern.

4. Klicken Sie im Abschnitt **Virtuelle Netzwerke** auf **+ Vorhandene hinzufügen**.

    ![Klicken Sie auf „Vorhandene hinzufügen“ (Subnetzendpunkt, als SQL-Regel).][image-portal-firewall-vnet-add-existing-10-png]

5. Füllen Sie im Bereich **Erstellen/Aktualisieren** die Steuerelemente mit den Namen Ihrer Azure-Ressourcen aus.

    > [!TIP]
    > Sie müssen das richtige **Adresspräfix** für Ihr Subnetz hinzufügen. Den Wert finden Sie im Portal.
    > Navigieren Sie zu **Alle Ressourcen**&gt;**Alle Typen**&gt;**Virtuelle Netzwerke**. Der Filter zeigt Ihre virtuellen Netzwerke an. Klicken Sie auf das virtuelle Netzwerk und dann auf **Subnetze**. Die Spalte **ADRESSBEREICH** weist das Adresspräfix auf, das Sie benötigen.

    ![Füllen Sie Felder für die neue Regel aus.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Klicken Sie am unteren Rand des Bereichs auf die Schaltfläche **OK**.

7. Die resultierende VNET-Regel wird im Firewallbereich angezeigt.

    ![Sie finden die neue Regel im Firewallbereich.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Für die Regeln gelten die folgenden Status oder Zustände:
> - **Bereit:** Gibt an, dass der von Ihnen initiierte Vorgang erfolgreich war.
> - **Fehlerhaft:** Gibt an, dass der von Ihnen initiierte Vorgang zu einem Fehler geführt hat.
> - **Gelöscht:** Gilt nur für den Löschvorgang und gibt an, dass die Regel gelöscht wurde und nicht mehr angewendet wird.
> - **In Bearbeitung:** Gibt an, dass der Vorgang ausgeführt wird. In diesem Zustand wird die alte Regel weiter angewendet.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Verwandte Artikel

- [Azure-VNET-Dienstendpunkte][vm-virtual-network-service-endpoints-overview-649d]
- [Firewallregeln auf Server- und Datenbankebene für Azure SQL-Datenbank][sql-db-firewall-rules-config-715d]

Das Feature für VNET-Regeln für Azure SQL-Datenbank wurde Ende September 2017 eingeführt.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von PowerShell zum Erstellen eines VNET-Dienstendpunkts und einer VNET-Regel für Azure SQL-Datenbank][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [VNET-Regeln: Vorgänge][rest-api-virtual-network-rules-operations-862r] mit REST-APIs

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
