---
title: 'Schnellstart: Erstellen eines Servers – Azure-Portal – Azure Database for PostgreSQL-Einzelserver'
description: In dieser Schnellstartanleitung erstellen und verwalten Sie einen Azure Database for PostgreSQL-Server über das Azure-Portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492385"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Schnellstart: Erstellen eines Azure Database for PostgreSQL-Servers mithilfe des Azure-Portals

Azure-Datenbank für PostgreSQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. In diesem Schnellstart wird veranschaulicht, wie Sie einen einzelnen Azure Database for PostgreSQL-Server erstellen und eine Verbindung damit herstellen.

## <a name="prerequisites"></a>Voraussetzungen
Ein Azure-Abonnement ist erforderlich. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-database-for-postgresql-server"></a>Erstellen einer Azure-Datenbank für PostgreSQL-Server
Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), um eine Datenbank vom Typ „Azure Database for PostgreSQL-Einzelserver“ zu erstellen. Suchen Sie nach *Azure Database for PostgreSQL-Server*, und wählen Sie die Option aus.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Suchen nach „Azure Database for PostgreSQL“":::

1. Wählen Sie **Hinzufügen**.

2. Wählen Sie auf der Seite zum Erstellen einer Azure Database for PostgreSQL-Instanz die Option **Einzelserver** aus.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Auswählen von „Einzelserver“":::

3. Geben Sie nun im Formular **Grundlagen** die folgenden Informationen ein:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Screenshot: Registerkarte „Grundeinstellungen“ für das Erstellen eines Einzelservers":::

   |Einstellung|Vorgeschlagener Wert|BESCHREIBUNG|
   |:---|:---|:---|
   |Subscription|Ihr Abonnementname|Wählen Sie das gewünschte Azure-Abonnement aus.|
   |Resource group|*myresourcegroup*| Eine neue oder eine vorhandene Ressourcengruppe aus Ihrem Abonnement|
   |Servername |*mydemoserver*|Ein eindeutiger Name, der Ihren Azure-Datenbank für PostgreSQL-Server identifiziert. Der Domänenname *postgres.database.azure.com* wird an den angegebenen Servernamen angefügt. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen enthalten.|
   |Datenquelle | Keine | Wählen Sie **Keine** aus, um einen neuen Server ohne Vorlage zu erstellen. Wählen Sie **Sicherung** nur aus, wenn Sie die Wiederherstellung aus einer Geosicherung eines vorhandenen Servers durchführen.|
   |Administratorbenutzername |*myadmin*| Geben Sie den Benutzernamen des Serveradministrators ein. Er darf nicht mit **pg_** beginnen, und die folgenden Werte sind nicht zulässig: **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** oder **public**.|
   |Kennwort |Ihr Kennwort| Ein neues Kennwort für den Serveradministratorbenutzer. Es muss 8 bis 128 Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (z. B. !, $, #, %).|
   |Standort|Der gewünschte Standort| Wählen Sie in der Dropdownliste einen Standort aus.|
   |Version|Die aktuelle Hauptversion| Die aktuelle PostgreSQL-Hauptversion (es sei denn, Sie haben besondere Anforderungen).|
   |Compute und Speicher | *Verwenden Sie die Standardwerte.*| Der Standardtarif ist **Universell** mit **4 virtuellen Kernen** und **100 GB** Speicher. Die Aufbewahrungsdauer für Sicherungen ist auf **7 Tage** mit der Sicherungsoption **Georedundant** festgelegt.<br/>Informieren Sie sich über die [Preise](https://azure.microsoft.com/pricing/details/postgresql/server/), und aktualisieren Sie bei Bedarf die Standardwerte.|


   > [!NOTE]
   > Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn geringe Compute- und E/A-Leistung für Ihre Workload angemessen ist. Beachten Sie, dass im Tarif „Basic“ erstellte Server später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert werden können.

5. Wählen Sie **Überprüfen + erstellen** aus, um ihre Auswahl zu überprüfen. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Dieser Vorgang kann einige Minuten dauern.
    > [!NOTE]
    > Eine leere Datenbank (**postgres**) wird erstellt. Darüber hinaus wird die Datenbank **azure_maintenance** angezeigt. Mit dieser Datenbank werden die Prozesse des verwalteten Diensts von den Benutzeraktionen getrennt. Auf die Datenbank **azure_maintenance** kann nicht zugegriffen werden.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="Erfolgreiche Bereitstellung":::

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Konfigurieren einer Firewallregel
Der von Ihnen erstellte Server ist standardmäßig nicht öffentlich zugänglich. Sie müssen Ihrer IP-Adresse Berechtigungen erteilen. Navigieren Sie im Azure-Portal zu Ihrer Serverressource, und wählen Sie im Menü auf der linken Seite die Option **Verbindungssicherheit** für Ihre Serverressource aus. Wenn Sie nicht sicher sind, wie Sie Ihre Ressource finden, lesen Sie die Informationen unter [Öffnen von Ressourcen](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Screenshot: Firewallregeln für die Verbindungssicherheit":::

Wählen Sie **Aktuelle Client-IP-Adresse hinzufügen** und dann **Speichern** aus. Sie können weitere IP-Adressen hinzufügen oder einen IP-Adressbereich angeben, um über diese IP-Adressen eine Verbindung mit dem Server herzustellen. Weitere Informationen finden Sie unter [Firewallregeln in Azure Database for PostgreSQL – Einzelserver](./concepts-firewall-rules.md).

> [!NOTE]
> Überprüfen Sie, ob Ihr Netzwerk ausgehenden Datenverkehr über Port 5432 zulässt, um Verbindungsprobleme zu vermeiden. Azure Database for PostgreSQL nutzt diesen Port.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Herstellen einer Verbindung mit dem Server mithilfe von psql

Sie können die beliebten PostgreSQL-Clients [psql](http://postgresguide.com/utilities/psql.html) oder [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) verwenden. In dieser Schnellstartanleitung wird die Verbindung mithilfe von psql in [Azure Cloud Shell](../cloud-shell/overview.md) über das Azure-Portal hergestellt.

1. Notieren Sie sich den Servernamen, den Anmeldenamen des Serveradministrators, das Kennwort und die Abonnement-ID für den neu erstellten Server im Abschnitt **Übersicht** Ihres Servers.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="Abrufen von Verbindungsinformationen":::


2. Öffnen Sie Azure Cloud Shell im Portal, indem Sie oben links das Symbol auswählen.

   > [!NOTE]
   > Wenn Sie Cloud Shell zum ersten Mal öffnen, wird eine Eingabeaufforderung zum Erstellen einer Ressourcengruppe und eines Speicherkontos angezeigt. Dieser Schritt ist nur einmal erforderlich. Diese Komponenten werden für alle Sitzungen automatisch angefügt.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Screenshot: Serverinformationen und das Symbol zum Öffnen von Azure Cloud Shell":::

3. Führen Sie den nachstehenden Befehl im Azure Cloud Shell-Terminal aus. Ersetzen Sie die Werte durch den tatsächlichen Servernamen und den Anmeldenamen des Administratorbenutzers. Verwenden Sie die leere Datenbank **postgres** mit dem Administratorbenutzer im folgenden Format: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   So sieht die Anzeige im Cloud Shell-Terminal aus:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Erstellen Sie im gleichen Azure Cloud Shell-Terminal eine Datenbank namens **guest**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Sie können Verbindungen auf die neu erstellte Datenbank **guest** umstellen.

   ```bash
   \c guest
   ```
6. Geben Sie `\q` ein, und drücken Sie anschließend die EINGABETASTE, um psql zu schließen.

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie haben erfolgreich einen Azure Database for PostgreSQL-Server in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, können Sie sie löschen, indem Sie entweder die Ressourcengruppe oder den PostgreSQL-Server löschen.

So löschen Sie die Ressourcengruppe:

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
2. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.
3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

Wählen Sie zum Löschen des Servers auf der Seite **Übersicht** Ihres Servers die Schaltfläche **Löschen** aus:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Screenshot: Schaltfläche zum Löschen eines Servers":::

[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Erstellen einer Datenbank](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/postgres-doc-feedback)