---
title: 'Schnellstart: Erstellen eines Servers – Azure PowerShell – Azure Database for PostgreSQL-Einzelserver'
description: Schnellstartanleitung zum Erstellen eines Azure Database for PostgreSQL-Einzelservers mithilfe von Azure PowerShell
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: quickstart
ms.date: 06/08/2020
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 91351c0b2982c6ee0e96cc1433c0fadf67e3bcc0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010655"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-powershell"></a>Schnellstart: Erstellen eines Azure Database for PostgreSQL-Einzelservers mithilfe von PowerShell

In dieser Schnellstartanleitung wird die Verwendung von PowerShell zum Erstellen eines Azure Database for PostgreSQL-Servers in einer Azure-Ressourcengruppe beschrieben. Sie können PowerShell verwenden, um Azure-Ressourcen interaktiv oder in Skripts zu erstellen und zu verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.PostgreSql“ verfügbar ist, müssen Sie es separat über das Az PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Sobald das PowerShell-Modul „Az.PostgreSql“ allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Falls Sie den Dienst „Azure Database for PostgreSQL“ zum ersten Mal verwenden, müssen Sie den Ressourcenanbieter **Microsoft.DBforPostgreSQL** registrieren.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.DBforPostgreSQL
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

## <a name="create-an-azure-database-for-postgresql-server"></a>Erstellen einer Azure-Datenbank für PostgreSQL-Server

Erstellen Sie mit dem Cmdlet `New-AzPostgreSqlServer` einen Azure Database for PostgreSQL-Server. Ein Server kann mehrere Datenbanken verwalten. In der Regel wird für jedes Projekt oder jeden Benutzer eine separate Datenbank verwendet.

Die folgende Tabelle enthält eine Liste mit den häufig verwendeten Parametern und Beispielwerten für das Cmdlet `New-AzPostgreSqlServer`.

|        **Einstellung**         | **Beispielwert** |                                                                                                                                                             **Beschreibung**                                                                                                                                                              |
| -------------------------- | ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                       | mydemoserver     | Wählen Sie einen in Azure global eindeutigen Namen aus, mit dem Ihr Azure Database for PostgreSQL-Server identifiziert wird. Der Servername darf nur Buchstaben, Zahlen und den Bindestrich (-) enthalten. Beim Erstellungsvorgang werden alle angegebenen Großbuchstaben automatisch in Kleinbuchstaben konvertiert. Es muss zwischen drei und 63 Zeichen lang sein. |
| ResourceGroupName          | myresourcegroup  | Geben Sie den Namen der Azure-Ressourcengruppe an.                                                                                                                                                                                                                                                                                            |
| Sku                        | GP_Gen5_2        | Der Name der SKU. Für die Benennung wird das Format **Tarif\_Computegeneration\_V-Kerne** in Kurzform verwendet. Weitere Informationen zum SKU-Parameter finden Sie in den Informationen, die auf diese Tabelle folgen.                                                                                                                                           |
| BackupRetentionDay         | 7                | Gibt die Aufbewahrungsdauer für eine Sicherung an. Die Einheit ist Tage. Der Bereich umfasst 7 bis 35.                                                                                                                                                                                                                                                                       |
| GeoRedundantBackup         | Aktiviert          | Gibt an, ob georedundante Sicherungen für diesen Server aktiviert werden sollen. Dieser Wert kann für Server mit dem Tarif „Basic“ nicht aktiviert und nach der Erstellung des Servers nicht mehr geändert werden. Zulässige Werte: „Aktiviert“, „Deaktiviert“                                                                                                      |
| Standort                   | westus           | Die Azure-Region für den Server.                                                                                                                                                                                                                                                                                                         |
| SslEnforcement             | Aktiviert          | Gibt an, ob SSL für diesen Server aktiviert werden soll. Zulässige Werte: „Aktiviert“, „Deaktiviert“                                                                                                                                                                                                                                                 |
| StorageInMb                | 51.200            | Die Speicherkapazität des Servers (Einheit: MB). Für „Valid StorageInMb“ gilt ein Mindestwert von 5.120 MB, der in Schritten von jeweils 1.024 MB erhöht werden kann. Weitere Informationen zu Grenzwerten für die Speichergröße finden Sie unter [Azure Database for PostgreSQL – Tarife](./concepts-pricing-tiers.md).                                                                               |
| Version                    | 9,6              | Die PostgreSQL-Hauptversion.                                                                                                                                                                                                                                                                                                                 |
| AdministratorUserName      | myadmin          | Der Benutzername für die Administratoranmeldung. Dieser darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.                                                                                                                                                                                            |
| AdministratorLoginPassword | `<securestring>` | Das Kennwort des Administratorbenutzers in Form einer sicheren Zeichenfolge. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.                                       |

Für den Parameterwert **Sku** wird das Format **Tarif\_Computegeneration\_V-Kerne** verwendet. Dies ist in den folgenden Beispielen dargestellt.

- `-Sku B_Gen5_1` ist „Basic“, „Gen 5“ und „1 virtueller Kern“ zugeordnet. Bei dieser Option handelt es sich um die kleinste verfügbare SKU.
- `-Sku GP_Gen5_32` ist „Universell“, „Gen 5“ und „32 V-Kerne“ zugeordnet.
- `-Sku MO_Gen5_2` ist „Arbeitsspeicheroptimiert“, „Gen 5“ und „2 V-Kerne“ zugeordnet.

Informationen zu den gültigen Werten für **Sku** nach Region und für Tarife finden Sie unter [Azure Database for PostgreSQL – Tarife](./concepts-pricing-tiers.md).

Im folgenden Beispiel wird ein PostgreSQL-Server in der Region **USA, Westen** mit dem Namen **mydemoserver** in der Ressourcengruppe **myresourcegroup** erstellt. Der Benutzername für den Serveradministrator lautet **myadmin**. Es handelt sich um einen Gen 5-Server im Tarif „Universell“ mit zwei virtuellen Kernen und Aktivierung von georedundanten Sicherungen. Notieren Sie sich das Kennwort, das in der ersten Zeile des Beispiels verwendet wird, da dies das Kennwort für das Administratorkonto des PostgreSQL-Servers ist.

> [!TIP]
> Ein Servername wird einem DNS-Namen zugeordnet und muss in Azure global eindeutig sein.

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -GeoRedundantBackup Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn eine geringe Compute- und E/A-Leistung für Ihre Workload ausreichend ist.

> [!IMPORTANT]
> Server, die unter dem Tarif „Basic“ erstellt wurden, können später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert und nicht georepliziert werden.

## <a name="configure-a-firewall-rule"></a>Konfigurieren einer Firewallregel

Erstellen Sie mithilfe des Cmdlets `New-AzPostgreSqlFirewallRule` eine Firewallregel auf der Ebene des Azure Database for PostgreSQL-Servers. Eine Firewallregel auf Serverebene ermöglicht es einer externen Anwendung, z. B. dem Befehlszeilentool `psql` oder PostgreSQL Workbench, über die Firewall des Diensts „Azure Database for PostgreSQL“ eine Verbindung mit Ihrem Server herzustellen.

Im folgenden Beispiel wird eine Firewallregel mit dem Namen **AllowMyIP** erstellt, die Verbindungen über eine bestimmte IP-Adresse (192.168.0.1) zulässt. Geben Sie gemäß dem Standort, von dem aus Sie eine Verbindung herstellen, eine IP-Adresse oder einen Bereich mit IP-Adressen an.

```azurepowershell-interactive
New-AzPostgreSqlFirewallRule -Name AllowMyIP -ResourceGroupName myresourcegroup -ServerName mydemoserver -StartIPAddress 192.168.0.1 -EndIPAddress 192.168.0.1
```

> [!NOTE]
> Die Kommunikation für Verbindungen mit Azure Database for PostgreSQL erfolgt über Port 5432. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 5432 unter Umständen nicht zugelassen. In diesem Szenario können Sie nur dann eine Verbindung mit dem Server herstellen, wenn Ihre IT-Abteilung Port 5432 öffnet.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Zum Herstellen einer Verbindung zum Server müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben. Verwenden Sie das folgende Beispiel, um die Verbindungsinformationen zu ermitteln. Notieren Sie sich die Werte für **FullyQualifiedDomainName** und **AdministratorLogin**.

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Select-Object -Property FullyQualifiedDomainName, AdministratorLogin
```

```Output
FullyQualifiedDomainName                    AdministratorLogin
------------------------                    ------------------
mydemoserver.postgres.database.azure.com       myadmin
```

## <a name="connect-to-postgresql-database-using-psql"></a>Herstellen einer Verbindung mit einer PostgreSQL-Datenbank mithilfe von psql

Wenn auf Ihrem Clientcomputer PostgreSQL installiert ist, können Sie mit einer lokalen Instanz von [psql](https://www.postgresql.org/docs/current/static/app-psql.html) eine Verbindung mit einer Azure-PostgreSQL-Serverinstanz herstellen. Sie können auch auf eine vorinstallierte Version des Befehlszeilentools `psql` in Azure Cloud Shell zugreifen, indem Sie in einem Codebeispiel dieses Artikels die Schaltfläche **Jetzt ausprobieren** auswählen. Weitere Möglichkeiten zum Zugreifen auf Azure Cloud Shell sind das Auswählen der Schaltfläche **>_** auf der Symbolleiste oben rechts im Azure-Portal oder das Navigieren zu [shell.azure.com](https://shell.azure.com/).

1. Stellen Sie mithilfe des Befehlszeilen-Hilfsprogramms `psql` eine Verbindung mit Ihrem Azure PostgreSQL-Server her.

   ```azurepowershell-interactive
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Der folgende Befehl stellt beispielsweise mithilfe der Zugriffsanmeldeinformationen eine Verbindung mit der Standarddatenbank **postgres** auf Ihrem PostgreSQL-Server `mydemoserver.postgres.database.azure.com` her. Geben Sie das `<server_admin_password>` ein, das Sie bei der Aufforderung zur Kennworteingabe ausgewählt haben.

   ```azurepowershell-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Wenn Sie lieber einen URL-Pfad zum Herstellen einer Verbindung mit Postgres verwenden, codiert die URL das @-Zeichen im Benutzernamen mit `%40`. Die Verbindungszeichenfolge für psql sieht beispielsweise so aus: `psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres`.

1. Sobald Sie mit dem Server verbunden sind, erstellen Sie eine leere Datenbank an der Eingabeaufforderung.

   ```sql
   CREATE DATABASE mypgsqldb;
   ```

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zur neu erstellten Datenbank **mypgsqldb** aus:

   ```sql
   \c mypgsqldb
   ```

## <a name="connect-to-the-postgresql-server-using-pgadmin"></a>Herstellen einer Verbindung mit dem PostgreSQL-Server mithilfe von pgAdmin

pgAdmin ist ein Open-Source-Tool, das zusammen mit PostgreSQL verwendet wird. Sie können pgAdmin von der [pgAdmin-Website](https://www.pgadmin.org/) installieren. Ihre pgAdmin-Version unterscheidet sich unter Umständen von der Version aus dieser Schnellstartanleitung. Weitere Informationen finden Sie bei Bedarf in der pgAdmin-Dokumentation.

1. Öffnen Sie die Anwendung pgAdmin auf dem Clientcomputer.

1. Navigieren Sie über die Symbolleiste zu **Objekt**, zeigen Sie auf **Erstellen**, und klicken Sie auf **Server**.

1. Geben Sie im Dialogfeld **Erstellen – Server** auf der Registerkarte **Allgemein** einen eindeutigen Anzeigenamen für den Server ein (z.B. **mydemoserver**).

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/9-pgadmin-create-server.png" alt-text="Die Registerkarte „Allgemein“":::

1. Füllen Sie im Dialogfeld **Erstellen – Server** auf der Registerkarte **Verbindung** die folgende Einstellungstabelle aus.

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/10-pgadmin-create-server.png" alt-text="Die Registerkarte „Verbindung“":::

    pgAdmin-Parameter |Wert|BESCHREIBUNG
    ---|---|---
    Hostname/Adresse | Servername | Der Servername, den Sie weiter oben beim Erstellen des Azure-Datenbank für PostgreSQL-Servers verwendet haben. Unser Beispielserver heißt **mydemoserver.postgres.database.azure.com**. Verwenden Sie den vollqualifizierten Domänennamen ( **\*.postgres.database.azure.com**), wie im Beispiel gezeigt. Sollten Sie sich nicht an Ihren Servernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt.
    Port | 5432 | Der Port zum Herstellen einer Verbindung mit dem Azure-Datenbank für PostgreSQL-Server
    Wartungsdatenbank | *postgres* | Der vom System generierte Standardname der Datenbank
    Username | Anmeldename des Serveradministrators | Der Anmeldename des Serveradministrators, den Sie zuvor beim Erstellen des Azure-Datenbank für PostgreSQL-Servers verwendet haben. Sollten Sie sich nicht an Benutzernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt. Das Format lautet: *Benutzername\@Servername*.
    Kennwort | Ihr Administratorkennwort | Das Kennwort, das Sie beim Erstellen des Servers weiter oben in dieser Schnellstartanleitung ausgewählt haben
    Role | Nicht ausfüllen | Zu diesem Zeitpunkt müssen Sie keinen Rollennamen angeben. Lassen Sie das Feld leer.
    SSL-Modus | *Anfordern* | Der TLS-/SSL-Modus kann auf der Registerkarte „SSL“ von pgAdmin festgelegt werden. In der Standardeinstellung werden alle Azure Database for PostgreSQL-Server mit aktivierter TLS-Erzwingung erstellt. Informationen zum Deaktivieren der TLS-Erzwingung finden Sie unter [Konfigurieren der Erzwingung von TLS](./concepts-ssl-connection-security.md#configure-enforcement-of-tls).

1. Wählen Sie **Speichern** aus.

1. Erweitern Sie im linken Bereich **Browser** den Knoten **Server**. Wählen Sie Ihren Server aus (z.B. **mydemoserver**). Durch Klicken stellen Sie eine Verbindung her.

1. Erweitern Sie den Serverknoten und anschließend darunter den Eintrag **Datenbanken**. Die Liste sollte Ihre Datenbank vom Typ *postgres* sowie andere Datenbanken enthalten, die Sie ggf. erstellt haben. Mit Azure Database for PostgreSQL können mehrere Datenbanken pro Server erstellt werden.

1. Klicken Sie mit der rechten Maustaste auf **Datenbanken**, wählen Sie das Menü **Erstellen** und dann **Datenbank** aus.

1. Geben Sie im Feld **Datenbank** einen Datenbanknamen Ihrer Wahl ein (z.B. **mypgsqldb2**).

1. Wählen Sie im Listenfeld den **Besitzer** der Datenbank aus. Wählen Sie den Anmeldenamen des Serveradministrators aus (in diesem Beispiel **my admin**).

   :::image type="content" source="./media/quickstart-create-postgresql-server-database-using-azure-powershell/11-pgadmin-database.png" alt-text="Erstellen einer Datenbank in pgAdmin":::

1. Wählen Sie **Speichern** aus, um eine neue leere Datenbank zu erstellen.

1. Die erstellte Datenbank wird im Bereich **Browser** in der Liste mit den Datenbanken unter Ihrem Servernamen angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in dieser Schnellstartanleitung erstellten Ressourcen nicht für eine andere Schnellstartanleitung oder ein anderes Tutorial benötigen, können Sie sie löschen, indem Sie das folgende Beispiel ausführen.

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieser Schnellstartanleitung gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Verwenden Sie das Cmdlet `Remove-AzPostgreSqlServer`, um nur den in dieser Schnellstartanleitung erstellten Server ohne die Ressourcengruppe zu löschen.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwerfen einer Azure Database for PostgreSQL-Instanz mithilfe von PowerShell](tutorial-design-database-using-powershell.md)