---
title: 'Schnellstart: Herstellen einer Verbindung unter Verwendung von PHP: Azure Database for MySQL'
description: Dieser Schnellstart enthält mehrere PHP-Codebeispiele, die Sie nutzen können, um zu den Daten von Azure-Datenbank für MySQL eine Verbindung herzustellen und Abfragen dafür durchzuführen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: cb3b711c532ccf44bebf08d42b5284db458cf5b7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492657"
---
# <a name="quickstart-use-php-to-connect-and-query-data-in-azure-database-for-mysql"></a>Schnellstart: Verwenden von PHP zum Herstellen einer Verbindung und zum Abfragen von Daten in Azure Database for MySQL
Dieser Schnellstart zeigt, wie Sie mit einer [PHP](https://secure.php.net/manual/intro-whatis.php)-Anwendung eine Verbindung mit einer Azure-Datenbank für MySQL herstellen. Es wird veranschaulicht, wie Sie SQL-Anweisungen zum Abfragen, Einfügen, Aktualisieren und Löschen von Daten in der Datenbank verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Schnellstartanleitung benötigen Sie Folgendes:

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free).
- Erstellen eines Azure Database for MySQL-Einzelservers mithilfe des [Azure-Portals](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> oder der [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md), wenn Sie noch keinen besitzen.
- Je nachdem, ob Sie öffentlichen oder privaten Zugriff verwenden, führen Sie **EINE** der folgenden Aktionen aus, um Konnektivität herzustellen:

    |Action| Konnektivitätsmethode|Schrittanleitung|
    |:--------- |:--------- |:--------- |
    | **Konfigurieren von Firewallregeln** | Öffentlich | [Portal](./howto-manage-firewall-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-firewall-using-cli.md)|
    | **Konfigurieren des Dienstendpunkts** | Öffentlich | [Portal](./howto-manage-vnet-using-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-manage-vnet-using-cli.md)|
    | **Konfigurieren von Private Link** | Privat | [Portal](./howto-configure-privatelink-portal.md) <br/> [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./howto-configure-privatelink-cli.md) |

- [Erstellen einer Datenbank und eines Nicht-Administratorbenutzers](./howto-create-users.md?tabs=single-server)
- Installieren der aktuellen PHP-Version für das Betriebssystem
    - [PHP unter macOS](https://secure.php.net/manual/install.macosx.php)
    - [PHP unter Linux](https://secure.php.net/manual/install.unix.php)
    - [PHP unter Windows](https://secure.php.net/manual/install.windows.php)

> [!NOTE]
> In diesem Schnellstart verwenden wir die [MySQLi](https://www.php.net/manual/en/book.mysqli.php)-Bibliothek zum Verwalten von Verbindungsinformationen und für Serverabfragen.

## <a name="get-connection-information"></a>Abrufen von Verbindungsinformationen
Mithilfe der folgenden Schritte können Sie die Verbindungsinformationen für den Datenbankserver aus dem Azure-Portal abrufen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Navigieren Sie zur Seite „Azure Database for MySQL“. Suchen Sie nach **Azure Database for MySQL**, und wählen Sie die Option aus.
:::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Suchen von Azure Database for MySQL":::

2. Wählen Sie Ihren MySQL-Server (z. B. **mydemoserver**) aus.
3. Kopieren Sie auf der Seite **Übersicht** den vollqualifizierten Servernamen neben **Servername** und den Administratorbenutzernamen neben **Anmeldename des Serveradministrators**. Um den Namen des Servers oder Hosts zu kopieren, zeigen Sie darauf, und wählen Sie das Symbol **Kopieren** aus.

> [!IMPORTANT]
> - Sollten Sie Ihr Kennwort vergessen haben, können Sie das [Kennwort zurücksetzen](./howto-create-manage-server-portal.md#update-admin-password).
> - Ersetzen Sie die Parameter **host, username, password** und **db_name** durch Ihre eigenen Werte.**

## <a name="step-1-connect-to-the-server"></a>Schritt 1: Herstellen einer Verbindung mit dem Server
SSL ist standardmäßig aktiviert. Möglicherweise müssen Sie das [SSL-Zertifikat „DigiCertGlobalRootG2“](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) herunterladen, um eine Verbindung von der lokalen Umgebung herzustellen. Mit diesem Code wird Folgendes aufgerufen:
- [mysqli_init](https://secure.php.net/manual/mysqli.init.php) zur Initialisierung von MySQLi
- [mysqli_ssl_set](https://www.php.net/manual/en/mysqli.ssl-set.php) zum Verweisen auf den SSL-Zertifikatpfad. Dies ist für Ihre lokale Umgebung erforderlich, aber nicht für die App Service-Web-App oder Azure Virtual Machines.
- [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) zur Verbindung mit MySQL
- [mysqli_close](https://secure.php.net/manual/mysqli.close.php) zum Schließen der Verbindung


```php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Initializes MySQLi
$conn = mysqli_init();

// If using  Azure Virtual machines or Azure Web App, 'mysqli-ssl_set()' is not required as the certificate is already installed on the machines.
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootG2.crt.pem", NULL, NULL);

// Establish the connection
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);

//If connection failed, show the error
if (mysqli_connect_errno($conn))
{
    die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
[Treten Probleme auf? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-create-a-table"></a>Schritt 2: Erstellen einer Tabelle
Stellen Sie die Verbindung mit dem folgenden Code her. Mit diesem Code wird Folgendes aufgerufen:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) zum Ausführen der Abfrage
```php
// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}
```

## <a name="step-3-insert-data"></a>Schritt 3: Einfügen von Daten
Verwenden Sie den folgenden Code, um mit einer SQL-Anweisung des Typs **INSERT** Daten einzufügen. Für diesen Code werden folgende Methoden verwendet:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) zum Erstellen einer vorbereiteten INSERT-Anweisung
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) zum Binden der Parameter für jeden eingefügten Spaltenwert
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php)
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) zum Schließen der Anweisung mithilfe der Methode


```php
//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)"))
{
    mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
    mysqli_stmt_execute($stmt);
    printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
    mysqli_stmt_close($stmt);
}

```

## <a name="step-4-read-data"></a>Schritt 4: Lesen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung vom Typ **SELECT** zu lesen.  Für diesen Code werden folgende Methoden verwendet:
- [mysqli_query](https://secure.php.net/manual/mysqli.query.php) zum Ausführen der **SELECT**-Abfrage
- [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) zum Abrufen der resultierenden Zeilen

```php
//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res))
 {
    var_dump($row);
 }

```


## <a name="step-5-delete-data"></a>Schritt 5: Löschen von Daten
Verwenden Sie den folgenden Code, um die Daten mit einer SQL-Anweisung vom Typ **DELETE** zu löschen. Für diesen Code werden folgende Methoden verwendet:
- [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) zum Erstellen einer vorbereiteten DELETE-Anweisung
- [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php) zum Binden der Parameter
- [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) zum Ausführen der vorbereiteten DELETE-Anweisung
- [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php) zum Schließen der Anweisung

```php
//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit dem folgenden Befehl, um alle in dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe des Portals](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Verwalten eines Azure Database for MySQL-Servers mithilfe der CLI](./how-to-manage-single-server-cli.md)

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)