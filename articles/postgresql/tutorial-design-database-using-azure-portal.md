---
title: 'Tutorial: Entwerfen eines Azure Database for PostgreSQL-Einzelservers – Azure-Portal'
description: In diesem Tutorial lernen Sie, wie Sie Ihre erste Azure-Datenbank für PostgreSQL – Einzelserver mithilfe des Azure-Portals entwerfen.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: tutorial, mvc
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 179d4124e28abfffa83b16cfdb418d8e860192d8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337131"
---
# <a name="tutorial-design-an-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Tutorial: Entwerfen einer Azure Database for PostgreSQL – Einzelserver unter Verwendung des Azure-Portals

Azure-Datenbank für PostgreSQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare PostgreSQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Mit dem Azure-Portal können Sie mühelos Ihren Server verwalten und eine Datenbank entwerfen.

In diesem Tutorial verwenden Sie das Azure-Portal, um Folgendes zu lernen:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html)
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-database-for-postgresql"></a>Erstellen einer Azure-Datenbank für PostgreSQL

Eine Azure-Datenbank für PostgreSQL-Server wird mit einer definierten Gruppe von [Compute- und Speicherressourcen](./concepts-pricing-tiers.md) erstellt. Der Server wird in einer [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) erstellt.

Führen Sie die folgenden Schritte aus, um eine Azure-Datenbank für PostgreSQL-Server zu erstellen:
1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.
2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann auf der Seite **Datenbanken** die Option **Azure-Datenbank für PostgreSQL** aus.
   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/1-create-database.png" alt-text="Azure-Datenbank für PostgreSQL – Erstellen der Datenbank":::

3. Wählen Sie die Bereitstellungsoption **Einzelserver** aus.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/select-deployment-option.png" alt-text="Auswählen der Bereitstellungsoption „Einzelserver“ für Azure Database for PostgreSQL":::

4. Füllen Sie dann das Formular **Grundlagen** mit den folgenden Informationen aus:

    :::image type="content" source="./media/tutorial-design-database-using-azure-portal/create-basics.png" alt-text="Erstellen eines Servers":::

    Einstellung|Empfohlener Wert|BESCHREIBUNG
    ---|---|---
    Subscription|Ihr Abonnementname|Das Azure-Abonnement, das Sie für Ihren Server verwenden möchten. Falls Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, über das die Ressource abgerechnet wird.
    Resource group|*myresourcegroup*| Ein neuer Ressourcengruppenname oder ein bereits vorhandener Name aus Ihrem Abonnement
    Servername |*mydemoserver*|Ein eindeutiger Name, der Ihren Azure-Datenbank für PostgreSQL-Server identifiziert. Der Domänenname *postgres.database.azure.com* wird an den angegebenen Servernamen angefügt. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss 3 bis 63 Zeichen umfassen.
    Datenquelle | *None* | Wählen Sie *Keine* aus, um einen neuen Server ohne Vorlage zu erstellen. (Die Option *Sicherung* können Sie auswählen, wenn Sie einen Server auf der Grundlage einer Geosicherung eines vorhandenen Azure Database for PostgreSQL-Servers erstellen.)
    Administratorbenutzername |*myadmin*| Ihr eigenes Anmeldekonto für die Verbindungsherstellung mit dem Server. Der Administratoranmeldename darf nicht **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest**, oder **public** lauten. Er kann nicht mit **pg_** beginnen.
    Kennwort |Ihr Kennwort| Ein neues Kennwort für das Serveradministratorkonto. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
    Position|Die Region, die Ihren Benutzern am nächsten liegt| Der Standort, der Ihren Benutzern am nächsten ist.
    Version|Die aktuelle Hauptversion| Die aktuelle PostgreSQL-Hauptversion (es sei denn, Sie haben besondere Anforderungen).
    Compute und Speicher | **Universell**, **Gen 5**, **2 virtuelle Kerne**, **5 GB**, **7 Tage**, **Georedundant** | Die Compute-, Speicher- und Sicherungskonfigurationen für Ihren neuen Server. Wählen Sie **Server konfigurieren** aus. Klicken Sie als Nächstes auf die Registerkarte **Universell**. *Gen 5*, *4 virtuelle Kerne*, *100 GB* und *7 Tage* sind die Standardwerte für **Computegeneration**, **Virtuelle Kerne**, **Speicher** und **Aufbewahrungszeit für Sicherung**. Sie können diese Schieberegler unverändert lassen oder sie anpassen. Wählen Sie zum Aktivieren der Serversicherungen in georedundantem Speicher unter **Optionen für Sicherungsredundanz** die Option **Georedundant** aus. Klicken Sie auf **OK**, um die Tarifauswahl zu speichern. Der nächste Screenshot zeigt die gewählten Optionen.

   > [!NOTE]
   > Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn geringe Compute- und E/A-Leistung für Ihre Workload angemessen ist. Beachten Sie, dass im Tarif „Basic“ erstellte Server später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert werden können. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/postgresql/).
   > 

    :::image type="content" source="./media/quickstart-create-database-portal/2-pricing-tier.png" alt-text="Der Bereich „Tarif“":::

    > [!TIP]
    > Ist **Automatische Vergrößerung** aktiviert, erhöht Ihr Server den Speicher, wenn Sie sich dem zugeordneten Grenzwert nähern. Ihre Workload wird dadurch nicht beeinträchtigt.

5. Wählen Sie **Überprüfen + erstellen** aus, um ihre Auswahl zu überprüfen. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Dieser Vorgang kann einige Minuten dauern.

6. Klicken Sie auf der Symbolleiste auf das Symbol **Benachrichtigungen** (eine Glocke), um den Bereitstellungsprozess zu überwachen. Nach Abschluss der Bereitstellung können Sie auf **An Dashboard anheften** klicken, um auf Ihrem Azure-Portal-Dashboard eine Kachel für diesen Server zu erstellen, über die Sie direkt zur **Übersicht** des Servers gelangen. Wenn Sie auf **Zu Ressource wechseln** klicken, wird die **Übersicht** des Servers geöffnet.

    :::image type="content" source="./media/quickstart-create-database-portal/3-notifications.png" alt-text="Der Bereich „Benachrichtigungen“":::
   
   Standardmäßig werden **postgres**-Datenbanken unter dem Server erstellt. Die [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html)-Datenbank ist eine Standarddatenbank für die Verwendung durch Benutzer, Hilfsprogramme und Drittanbieteranwendungen. (Die andere Standarddatenbank ist **azure_maintenance**. Ihre Funktion besteht darin, die Prozesse verwalteter Dienste von Benutzeraktionen zu trennen. Sie können auf diese Datenbank nicht zugreifen.


## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Der Azure Database for PostgreSQL-Dienst verwendet eine Firewall auf der Serverebene. Diese Firewall hindert standardmäßig alle externen Anwendungen und Tools daran, eine Verbindung mit dem Server und den Datenbanken auf dem Server herzustellen (sofern keine Firewallregel erstellt wird, um die Firewall für einen bestimmten Bereich von IP-Adressen zu öffnen). 

1. Klicken Sie nach Abschluss der Bereitstellung im linken Menü auf **Alle Ressourcen**, und geben Sie den Namen **mydemoserver** ein, um nach dem neu erstellten Server zu suchen. Klicken Sie auf den im Suchergebnis aufgelisteten Servernamen. Die Seite **Übersicht** für Ihren Server wird geöffnet und enthält Optionen für die weitere Konfiguration.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure-Datenbank für PostgreSQL – Suchen nach dem Server":::

2. Wählen Sie auf der Serverseite die Option **Verbindungssicherheit** aus. 

3. Klicken Sie in das Textfeld unter **Regelname**, und fügen Sie eine neue Firewallregel hinzu, um den IP-Adressbereich für die Konnektivität anzugeben. Geben Sie den IP-Adressbereich ein. Klicken Sie auf **Speichern**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/5-firewall-2.png" alt-text="Azure-Datenbank für PostgreSQL – Erstellen einer Firewallregel":::

4. Klicken Sie auf **Speichern** und dann auf **X**, um die Seite **Verbindungssicherheit** zu schließen.

   > [!NOTE]
   > Der Azure-PostgreSQL-Server kommuniziert über Port 5432. Wenn Sie versuchen, eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 5432 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit Ihrem Azure SQL-Datenbank-Server herstellen, wenn Ihre IT-Abteilung Port 5432 öffnet.
   >

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Als Sie den Azure Database for PostgreSQL-Server erstellt haben, wurde auch die Standarddatenbank **postgres** erstellt. Zum Herstellen einer Verbindung mit dem Datenbankserver müssen Sie Hostinformationen und Anmeldeinformationen für den Zugriff angeben.

1. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie nach dem Server, den Sie gerade erstellt haben.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/4-locate.png" alt-text="Azure-Datenbank für PostgreSQL – Suchen nach dem Server":::

2. Klicken Sie auf den Servernamen **mydemoserver**.

3. Wählen Sie die Seite **Übersicht** des Servers aus. Notieren Sie sich den **Servernamen** und den **Anmeldenamen des Serveradministrators**.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/6-server-name.png" alt-text="Azure-Datenbank für PostgreSQL-Server – Anmeldename des Serveradministrators":::


## <a name="connect-to-postgresql-database-using-psql"></a>Herstellen einer Verbindung mit einer PostgreSQL-Datenbank mithilfe von psql
Wenn auf Ihrem Clientcomputer PostgreSQL installiert ist, können Sie mit einer lokalen Instanz von [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) oder der Azure-Cloudkonsole eine Verbindung mit einem Azure-PostgreSQL-Server herstellen. Wir stellen jetzt mit dem Befehlszeilen-Hilfsprogramm psql eine Verbindung mit der Azure-Datenbank für PostgreSQL-Server her.

1. Führen Sie den folgenden psql-Befehl aus, um eine Verbindung mit einer Azure Database for PostgreSQL-Datenbank herzustellen:
   ```
   psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
   ```

   Mit dem folgenden Befehl wird beispielsweise mit den Zugriffsanmeldeinformationen eine Verbindung mit der Standarddatenbank **postgres** auf Ihrem PostgreSQL-Server **mydemoserver.postgres.database.azure.com** hergestellt. Geben Sie das `<server_admin_password>` ein, das Sie bei der Aufforderung zur Kennworteingabe ausgewählt haben.
  
   ```
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Wenn Sie lieber einen URL-Pfad zum Herstellen einer Verbindung mit Postgres verwenden, codiert die URL das @-Zeichen im Benutzernamen mit `%40`. Die Verbindungszeichenfolge für psql sieht beispielsweise so aus:
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```

2. Sobald Sie mit dem Server verbunden sind, erstellen Sie an der Eingabeaufforderung eine leere Datenbank:
   ```sql
   CREATE DATABASE mypgsqldb;
   ```

3. Führen Sie an der Eingabeaufforderung den folgenden Befehl zum Wechseln der Verbindung zur neu erstellten Datenbank **mypgsqldb** aus:
   ```sql
   \c mypgsqldb
   ```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank
Da Sie nun wissen, wie Sie eine Verbindung mit Azure Database for PostgreSQL herstellen, können Sie einige grundlegende Aufgaben ausführen:

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Erstellen Sie nun mit dem folgenden SQL-Code eine Tabelle, die Bestandsinformationen nachverfolgt:
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

Um die neu erstellte Tabelle in der Liste der Tabellen anzuzeigen, geben Sie Folgendes ein:
```sql
\dt
```

## <a name="load-data-into-the-tables"></a>Laden von Daten in die Tabellen
Jetzt können Sie Daten in die Tabelle einfügen. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage zum Einfügen einiger Datenzeilen aus.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

Die Bestandstabelle, die Sie zuvor erstellt haben, enthält nun zwei Zeilen mit Beispieldaten.

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen
Führen Sie die folgende Abfrage aus, um Informationen aus der Bestandsdatenbanktabelle abzurufen. 
```sql
SELECT * FROM inventory;
```

Sie können die Daten in der Tabelle auch aktualisieren.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die aktualisierten Werte werden angezeigt, wenn Sie die Daten abrufen.
```sql
SELECT * FROM inventory;
```

## <a name="restore-data-to-a-previous-point-in-time"></a>Wiederherstellung des Zustands der Daten zu einem früheren Zeitpunkt
Stellen Sie sich vor, Sie haben versehentlich diese Tabelle gelöscht. Dies ist eine Situation, in der Sie die Datenbank nicht einfach wiederherstellen können. Mit Azure Database for PostgreSQL können Sie eine beliebige Zeitpunktsicherung Ihres Servers (abhängig vom konfigurierten Aufbewahrungszeitraum für Sicherungen) auf einem neuen Server wiederherstellen. Sie können diesen neuen Server zur Wiederherstellung gelöschter Daten verwenden. Mithilfe der folgenden Schritte wird der Server **mydemoserver** zu einem Zeitpunkt wiederhergestellt, der vor dem Hinzufügen der Bestandstabelle liegt.

1. Klicken Sie auf der Seite **Übersicht** von Azure Database for PostgreSQL für Ihren Server auf der Symbolleiste auf **Wiederherstellen**. Die Seite **Wiederherstellen** wird geöffnet.

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/9-azure-portal-restore.png" alt-text="Der Screenshot zeigt die Azure Database for PostgreSQL-Seite **Übersicht** für Ihren Server und hebt die Schaltfläche „Wiederherstellen“ hervor.":::

2. Geben Sie im Formular **Wiederherstellen** die erforderlichen Informationen ein:

   :::image type="content" source="./media/tutorial-design-database-using-azure-portal/10-azure-portal-restore.png" alt-text="Azure-Portal – Optionen des Wiederherstellungsformulars":::

   - **Wiederherstellungspunkt**: Wählen Sie einen Zeitpunkt vor der Änderung des Servers aus.
   - **Zielserver**: Geben Sie einen neuen Servernamen für die Wiederherstellung ein.
   - **Standort**: Sie können nicht die Region wählen. Standardmäßig ist dieser Wert mit dem Quellserver identisch.
   - **Tarif:** Sie können diesen Wert beim Wiederherstellen eines Servers nicht ändern. Er ist mit dem Wert für den Quellserver identisch. 
3. Klicken Sie auf **OK**, um den [Server zu einem Zeitpunkt wiederherzustellen](./howto-restore-server-portal.md), der vor dem Löschen der Tabelle liegt. Beim Wiederherstellen eines Servers im Zustand eines anderen Zeitpunkts wird ein Duplikat des ursprünglichen Servers im Zustand des von Ihnen angegebenen Zeitpunkts als neuer Server erstellt – vorausgesetzt, dieser Zeitpunkt liegt innerhalb des Aufbewahrungszeitraums für Ihren [Tarif](./concepts-pricing-tiers.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Servergruppe erstellt. Wenn Sie diese Ressourcen nicht mehr benötigen, löschen Sie die Servergruppe. Klicken Sie auf der Seite *Übersicht* für Ihre Servergruppe auf die Schaltfläche *Löschen*. Wenn Sie auf einer Popupseite dazu aufgefordert werden, bestätigen Sie den Namen der Servergruppe, und klicken Sie abschließend auf die Schaltfläche *Löschen*.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie gelernt, wie Sie das Azure-Portal und andere Hilfsprogramme für Folgendes verwenden:
> [!div class="checklist"]
> * Erstellen einer Azure-Datenbank für PostgreSQL-Server
> * Konfigurieren der Serverfirewall
> * Erstellen einer Datenbank mit dem Hilfsprogramm **psql**
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

> [!div class="nextstepaction"]
>[Entwerfen Ihrer ersten Azure-Datenbank für PostgreSQL mithilfe von Azure CLI](tutorial-design-database-using-azure-cli.md)