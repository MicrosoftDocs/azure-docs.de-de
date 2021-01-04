---
title: 'Tutorial: Entwerfen einer Azure Database for MariaDB-Instanz – Azure-Portal'
description: In diesem Tutorial wird erläutert, wie Sie einen Azure Database for MariaDB-Server und eine Azure Database for MariaDB-Datenbank mithilfe des Azure-Portals erstellen und verwalten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542438"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Tutorial: Entwerfen einer Azure Database for MariaDB-Datenbank mithilfe des Azure-Portals

Azure Database for MariaDB ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Über das Azure-Portal können Sie mühelos Ihren Server verwalten und eine Datenbank entwerfen.

In diesem Tutorial verwenden Sie das Azure-Portal, um Folgendes zu lernen:

> [!div class="checklist"]
> * Erstellen einer Azure Database for MariaDB-Instanz
> * Konfigurieren der Serverfirewall
> * Verwenden des MySQL-Befehlszeilentools zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="create-an-azure-database-for-mariadb-server"></a>Erstellen eines Azure Database for MariaDB-Servers

Sie erstellen einen Azure Database for MariaDB-Server mit einer definierten Gruppe von [Compute- und Speicherressourcen](concepts-pricing-tiers.md). Der Server wird in einer [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) erstellt.

1. Klicken Sie in der linken oberen Ecke des Portals auf die Schaltfläche **Ressource erstellen** (+).

2. Wählen Sie **Datenbanken** > **Azure Database for MariaDB** aus. Sie können auch **MariaDB** in das Suchfeld eingeben, um nach dem Dienst zu suchen.

   ![Wechseln zu MySQL](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Wählen Sie die Kachel **Azure Database for MariaDB** aus. Geben Sie die erforderlichen Informationen ein, oder wählen Sie sie aus.

   ![Erstellen des Formulars](./media/tutorial-design-database-using-portal/2-create-form.png)

    Einstellung | Vorgeschlagener Wert | Feldbeschreibung
    ---|---|---
    Servername | *Ein eindeutiger Servername* | Wählen Sie einen eindeutigen Namen aus, der Ihren Azure Database for MariaDB-Server identifiziert. Beispiel: **mydemoserver**. Der Domänenname *.mariadb.database.azure.com* wird an den angegebenen Servernamen angefügt. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen lang sein.
    Subscription | *Ihr Abonnement* | Wählen Sie das Azure-Abonnement aus, das Sie für Ihren Server verwenden möchten. Falls Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, über das die Ressource abgerechnet wird.
    Resource group | **myresourcegroup** | Geben Sie einen neuen Ressourcengruppennamen ein, oder wählen Sie eine vorhandene Ressourcengruppe aus.
    Quelle auswählen | **Leer** | Wählen Sie **Leer** aus, um einen neuen Server zu erstellen. (Wählen Sie **Sicherung** aus, wenn Sie einen Server auf der Grundlage einer Geosicherung eines vorhandenen Azure Database for MariaDB-Servers erstellen.)
    Serveradministratoranmeldung | **myadmin** | Ein Anmeldekonto für die Verbindungsherstellung mit dem Server. Der Administratoranmeldename darf nicht **azure_superuser** , **admin** , **administrator** , **root** , **guest** oder **public** lauten.
    Kennwort | *Beliebig* | Geben Sie ein neues Kennwort für das Serveradministratorkonto ein. Es muss zwischen acht und 128 Zeichen lang sein. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0-9) und nicht alphanumerische Zeichen (!, $, #, % usw.).
    Kennwort bestätigen | *Beliebig*| Bestätigen Sie das Kennwort des Administratorkontos.
    Standort | *Die Region, die Ihren Benutzern am nächsten ist*| Wählen Sie den Standort aus, der Ihren Benutzern oder Ihren anderen Azure-Anwendungen am nächsten ist.
    Version | *Die aktuelle Version*| Die aktuelle Version (es sei denn, Sie haben besondere Anforderungen, die eine andere Version erfordern).
    Tarif | Siehe Beschreibung. | Die Compute-, Speicher- und Sicherungskonfigurationen für Ihren neuen Server. Klicken Sie auf **Tarif** > **Universell**. Übernehmen Sie die Standardwerte für die folgenden Einstellungen:<br><ul><li>**Computegeneration** (Gen 5)</li><li>**Virtuelle Kerne** (4 virtuelle Kerne)</li><li>**Speicher** (100 GB)</li><li>**Aufbewahrungszeit für Sicherung** (sieben Tage)</li></ul><br>Wählen Sie zum Aktivieren der Serversicherungen in georedundantem Speicher unter **Optionen für Sicherungsredundanz** die Option **Georedundant** aus. <br><br>Klicken Sie auf **OK** , um die Tarifauswahl zu speichern. Der nächste Screenshot zeigt die gewählten Optionen.
    
   ![Tarif](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > Ist **Automatische Vergrößerung** aktiviert, erhöht Ihr Server den Speicher, wenn Sie sich dem zugeordneten Grenzwert nähern. Ihre Workload wird dadurch nicht beeinträchtigt.

4. Klicken Sie auf **Überprüfen + erstellen**. Sie können in der Symbolleiste auf die Schaltfläche **Benachrichtigungen** klicken, um den Bereitstellungsprozess zu überwachen. Die Bereitstellung kann bis zu 20 Minuten dauern.

## <a name="configure-the-firewall"></a>Konfigurieren der Firewall

Eine Azure Database for MariaDB-Instanz wird durch eine Firewall geschützt. Standardmäßig werden alle Verbindungen mit dem Server und den Datenbanken auf dem Server abgelehnt. Vor dem erstmaligen Herstellen einer Verbindung mit Azure Database for MariaDB müssen Sie die Firewall konfigurieren, um die öffentliche Netzwerk-IP-Adresse (oder den IP-Adressbereich) des Clientcomputers hinzuzufügen.

1. Wählen Sie den neu erstellten Server aus, und klicken Sie dann auf **Verbindungssicherheit**.

   ![Verbindungssicherheit](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. Hier können Sie **Meine IP-Adresse hinzufügen** auswählen oder Firewallregeln konfigurieren. Denken Sie daran, nach dem Erstellen der Regeln auf **Speichern** zu klicken.

Sie können jetzt mit dem MySQL-Befehlszeilentool oder MySQL Workbench eine Verbindung mit dem Server herstellen.

> [!TIP]
> Der Azure Database for MariaDB-Server kommuniziert über Port 3306. Wenn Sie versuchen, eine Verbindung über ein Unternehmensnetzwerk herzustellen, wird ausgehender Datenverkehr über Port 3306 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall muss Ihre IT-Abteilung Port 3306 öffnen, damit Sie eine Verbindung mit dem Azure Database for MariaDB-Server herstellen können.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen

Rufen Sie die Werte für **Servername** (vollqualifiziert) und **Anmeldename des Serveradministrators** für Ihren Azure Database for MariaDB-Server aus dem Azure-Portal ab. Sie verwenden den vollqualifizierten Servernamen, um mit dem MySQL-Befehlszeilentool eine Verbindung mit Ihrem Server herzustellen.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) im Menü auf der linken Seite auf **Alle Ressourcen**. Geben Sie den Servernamen ein, und suchen Sie nach Ihrem Azure Database for MariaDB-Server. Wählen Sie den Servernamen aus, um die Details zum Server anzuzeigen.

2. Notieren Sie auf der Seite **Übersicht** die Werte für **Servername** und **Anmeldename des Serveradministrators**. Sie können auch neben den beiden Feldern auf die Schaltfläche **Kopieren** klicken, um die Werte in die Zwischenablage zu kopieren.

   ![Servereigenschaften](./media/tutorial-design-database-using-portal/2-server-properties.png)

In unserem Beispiel lautet der Servername **mydemoserver.mariadb.database.azure.com** und der Benutzername für die Serveradministratoranmeldung **myadmin\@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Herstellen einer Verbindung mit dem Server mit MySQL

Verwenden Sie das [MySQL-Befehlszeilentool](https://dev.mysql.com/doc/refman/5.7/en/mysql.html), um eine Verbindung mit Ihrem Azure Database for MariaDB-Server herzustellen. Sie können das MySQL-Befehlszeilentool über Azure Cloud Shell im Browser oder auf Ihrem Computer über die lokal installierten MySQL-Tools ausführen. Um Azure Cloud Shell zu öffnen, klicken Sie in einem Codeblock in diesem Artikel auf die Schaltfläche **Ausprobieren** , oder besuchen Sie das Azure-Portal, und klicken Sie auf der Symbolleiste oben rechts auf das Symbol **>_** .

Geben Sie den Befehl zum Herstellen einer Verbindung ein:

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Leere Datenbank erstellen

Nachdem Sie eine Verbindung mit dem Server hergestellt haben, erstellen Sie als Nächstes eine leere Datenbank:

```sql
CREATE DATABASE mysampledb;
```

Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um die Verbindung auf die neu erstellte Datenbank umzustellen:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Erstellen von Tabellen in der Datenbank

Da Sie nun wissen, wie Sie eine Verbindung mit einer Azure Database for MariaDB-Datenbank herstellen, können Sie einige grundlegende Aufgaben ausführen.

Zunächst erstellen Sie eine Tabelle und füllen sie mit einigen Daten auf. Wir erstellen eine Tabelle, in der Bestandsinformationen gespeichert werden:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Laden von Daten in die Tabellen

Jetzt können Sie Daten in die Tabelle einfügen. Führen Sie im geöffneten Eingabeaufforderungsfenster die folgende Abfrage aus, um einige Datenzeilen einzufügen:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>Abfragen und Aktualisieren der Daten in den Tabellen

Führen Sie die folgende Abfrage aus, um Informationen aus der Datenbanktabelle abzurufen:

```sql
SELECT * FROM inventory;
```

Sie können die Daten in den Tabellen auch aktualisieren:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

Die Zeile wird aktualisiert, wenn Sie Daten abrufen:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Wiederherstellen eines früheren Zustands einer Datenbank

Angenommen, Sie haben versehentlich eine wichtige Datenbanktabelle gelöscht und können die Daten nicht problemlos wiederherstellen. In Azure Database for MariaDB können Sie den Zustand des Servers zu einem bestimmten Zeitpunkt wiederherstellen, indem Sie eine Kopie der Datenbanken auf dem neuen Server erstellen. Sie können diesen neuen Server zur Wiederherstellung gelöschter Daten verwenden. Mit den folgenden Schritte wird der Zustand des Beispielservers zu einem Zeitpunkt vor dem Hinzufügen der Tabelle wiederhergestellt:

1. Suchen Sie im Azure-Portal Ihre Azure Database for MariaDB-Instanz. Klicken Sie auf der Seite **Übersicht** auf **Wiederherstellen**.

   ![Wiederherstellen einer Datenbank](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Geben Sie auf der Seite **Wiederherstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

   ![Wiederherstellungsformular](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Wiederherstellungspunkt** : Wählen Sie im angezeigten Zeitraum einen Zeitpunkt aus, den Sie wiederherstellen möchten. Stellen Sie sicher, dass Sie die lokale Zeitzone in UTC konvertieren.
   - **Auf neuem Server wiederherstellen** : Geben Sie einen neuen Servernamen für die Wiederherstellung ein.
   - **Standort** : Die Region ist identisch mit dem Quellserver und kann nicht geändert werden.
   - **Tarif:** Der Tarif ist identisch mit dem Quellserver und kann nicht geändert werden.
   
3. Klicken Sie auf **OK** , um den Zustand des Servers [zu einem Zeitpunkt wiederherzustellen](./howto-restore-server-portal.md), der vor dem Löschen der Tabelle liegt. Durch das Wiederherstellen eines Servers wird eine neue Kopie des Servers zum ausgewählten Zeitpunkt erstellt. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial verwenden Sie das Azure-Portal, um Folgendes zu lernen:

> [!div class="checklist"]
> * Erstellen einer Azure Database for MariaDB-Instanz
> * Konfigurieren der Serverfirewall
> * Verwenden des mysql-Befehlszeilentools zum Erstellen einer Datenbank
> * Laden von Beispieldaten
> * Daten abfragen
> * Aktualisieren von Daten
> * Wiederherstellen von Daten

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung zwischen Anwendungen und Azure Database for MariaDB](howto-connection-string.md)
