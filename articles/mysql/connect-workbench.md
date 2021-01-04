---
title: 'Schnellstart: Herstellen einer Verbindung – MySQL Workbench – Azure Database for MySQL'
description: Diese Schnellstartanleitung veranschaulicht die Verwendung von MySQL Workbench – einem Tool, mit dem Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen und Daten daraus abfragen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 5e27cfec0a3f0a58c1e94a822e0c831f4efa1b32
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535536"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von MySQL Workbench zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL

Dieser Schnellstart zeigt, wie Sie mit der Anwendung MySQL Workbench eine Verbindung mit einer Azure-Datenbank für MySQL herstellen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Schnellstart werden die Ressourcen, die in den folgenden Anleitungen erstellt wurden, als Startpunkt verwendet:
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Sicherstellen, dass der IP-Adresse, über die Sie eine Verbindung herstellen, die Firewallregeln des Servers über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder die [Azure CLI](./howto-manage-firewall-using-cli.md) hinzugefügt wurden

## <a name="install-mysql-workbench"></a>Installieren von MySQL Workbench
Laden Sie MySQL-Workbench von der [MySQL-Website](https://dev.mysql.com/downloads/workbench/) auf Ihren Computer herunter, und installieren Sie die Anwendung.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Rufen Sie die Verbindungsinformationen ab, die zum Herstellen einer Verbindung mit der Azure SQL-Datenbank für MySQL erforderlich sind. Sie benötigen den vollqualifizierten Servernamen und die Anmeldeinformationen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

2. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**, und suchen Sie dann nach dem soeben erstellten Server, z.B. **mydemoserver**.

3. Klicken Sie auf den Servernamen.

4. Notieren Sie sich im Bereich **Übersicht** des Servers den **Servernamen** und den **Anmeldenamen des Serveradministrators**. Wenn Sie Ihr Kennwort vergessen haben, können Sie es in diesem Bereich auch zurücksetzen.
 :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Servername für Azure-Datenbank für MySQL":::

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Herstellen einer Verbindung mit dem Server unter Verwendung von MySQL Workbench 
So stellen Sie eine Verbindung mit dem Server für Azure MySQL mithilfe des GUI-Tools „MySQL Workbench“ her:

1.    Starten Sie die Anwendung MySQL Workbench auf dem Computer. 

2.    Geben Sie im Dialogfeld **Setup New Connection** (Neue Verbindung einrichten) auf der Registerkarte **Parameter** folgende Informationen ein:

:::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="Einrichten einer neuen Verbindung":::

| **Einstellung** | **Empfohlener Wert** | **Feldbeschreibung** |
|---|---|---|
|    Verbindungsname | Beispielverbindung | Geben Sie eine Bezeichnung für diese Verbindung an. |
| Verbindungsmethode | Standard (TCP/IP) | Standard (TCP/IP) ist ausreichend. |
| Hostname | *Servername* | Geben Sie den Servernamen an, den Sie zuvor beim Erstellen von Azure-Datenbank für MySQL verwendet haben. Unser Beispielserver heißt „mydemoserver.mysql.database.azure.com“. Verwenden Sie den vollqualifizierten Domänennamen (\*.mysql.database.azure.com), wie im Beispiel gezeigt. Sollten Sie sich nicht an Ihren Servernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt.  |
| Port | 3306 | Verwenden Sie immer den Port 3306, wenn Sie eine Verbindung mit Azure-Datenbank für MySQL herstellen. |
| Username |  *Anmeldename des Serveradministrators* | Geben Sie den Anmeldename des Serveradministrators ein, den Sie zuvor beim Erstellen von Azure-Datenbank für MySQL verwendet haben. Unser Beispielbenutzername lautet myadmin@mydemoserver. Sollten Sie sich nicht an Benutzernamen erinnern, ermitteln Sie die Verbindungsinformationen gemäß den Schritten aus dem vorherigen Abschnitt. Das Format lautet: *Benutzername\@Servername*.
| Kennwort | Ihr Kennwort | Klicken Sie auf die Schaltfläche **In Vault speichern**, um das Kennwort zu speichern. |

3.   Klicken Sie auf **Verbindung testen**, um zu testen, ob alle Parameter richtig konfiguriert wurden. 

4.   Klicken Sie zum Speichern der Verbindung auf **OK**. 

5.   Klicken Sie in der Auflistung der **MySQL-Verbindungen** auf die Kachel Ihres Servers, und warten Sie, bis die Verbindung hergestellt wurde.

        Es wird eine neue SQL-Registerkarte mit einem leeren-Editor geöffnet, auf der Sie Ihre Abfragen eingeben können.
    
        > [!NOTE]
        > Standardmäßig ist SSL-Sicherheit in Verbindung mit Ihrer Azure-Datenbank für MySQL-Server erforderlich und wird erzwungen. In der Regel ist mit SSL-Zertifikaten keine zusätzliche Konfiguration erforderlich, damit MySQL Workbench eine Verbindung mit Ihrem Server herstellen kann. Es empfiehlt sich jedoch, die Zertifizierung der SSL-Zertifizierungsstelle mit MySQL Workbench zu binden. Weitere Informationen zum Herunterladen und Binden der Zertifizierung finden Sie unter [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit der Azure-Datenbank für MySQL](./howto-configure-ssl.md).  Wenn Sie SSL deaktivieren müssen, besuchen Sie das Azure-Portal, und klicken Sie auf die Seite „Verbindungssicherheit“, um die Umschaltfläche „SSL-Verbindung erzwingen“ zu deaktivieren.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Erstellen einer Tabelle, Einfügen von Daten, Lesen von Daten, Aktualisieren von Daten, Löschen von Daten
1. Kopieren Sie den SQL-Beispielcode, und fügen Sie ihn auf einer leeren SQL-Registerkarte ein, um Beispieldaten zu veranschaulichen.

    Dieser Code erstellt die leere Datenbank „quickstartdb“ und anschließend die Beispieltabelle „inventory“. Er fügt einige Zeilen ein und liest sie dann aus. Er ändert die Daten mit einer update-Anweisung und liest die Zeilen erneut aus. Zum Schluss löscht er eine Zeile und liest die Zeilen noch einmal aus.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Der Screenshot zeigt ein Beispiel für SQL-Code in SQL Workbench und die Ausgabe nach der Ausführung.
    
    :::image type="content" source="media/connect-workbench/3-workbench-sql-tab.png" alt-text="SQL-Registerkarte in MySQL Workbench für die Ausführung von SQL-Beispielcode":::

2. Um den SQL-Beispielcode auszuführen, klicken Sie auf der Registerkarte **SQL-Datei** auf der Symbolleiste auf das Blitzsymbol.
3. Beachten Sie die drei im Registerkartenformat angezeigten Ergebnisse im Abschnitt **Ergebnisraster** in der Mitte der Seite. 
4. Beachten Sie auch die Liste **Ausgabe** am unteren Rand der Seite. Der Status der einzelnen Befehle wird angezeigt. 

Sie haben mithilfe von MySQL Workbench eine Verbindung mit Azure Database for MySQL hergestellt und Daten mit der Sprache SQL abgefragt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Migrieren der Datenbank mit Export und Import](./concepts-migrate-import-export.md)
