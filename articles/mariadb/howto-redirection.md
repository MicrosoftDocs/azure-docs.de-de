---
title: Herstellen einer Verbindung mit Umleitung – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie Sie Ihre Anwendung konfigurieren können, damit sie eine Verbindung mit Azure Database for MariaDB mit Umleitung herstellt.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 6/8/2020
ms.openlocfilehash: 3f26de72839fcaa39bff4d827aba757721736934
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94537423"
---
# <a name="connect-to-azure-database-for-mariadb-with-redirection"></a>Herstellen einer Verbindung mit Azure Database for MariaDB mit Umleitung

In diesem Thema wird erläutert, wie Sie eine Anwendung mit Ihrem Azure Database for MariaDB-Server im Umleitungsmodus verbinden. Mithilfe der Umleitung soll die Netzwerklatenz zwischen Clientanwendungen und MariaDB-Servern dadurch verringert werden, dass Anwendungen eine direkte Verbindung mit Back-End-Serverknoten herstellen dürfen.

## <a name="before-you-begin"></a>Voraussetzungen
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Erstellen Sie einen Azure Database for MariaDB-Server mit Engine-Version 10.2 oder 10.3. 

Weitere Informationen finden Sie unter den Ausführungen zum Erstellen eines Azure Database for MariaDB-Servers mit dem [Azure-Portal](quickstart-create-mariadb-server-database-using-azure-portal.md) oder der [Azure CLI](quickstart-create-mariadb-server-database-using-azure-cli.md).

## <a name="enable-redirection"></a>Aktivieren der Umleitung

Konfigurieren Sie auf Ihrem Azure Database for MariaDB-Server den `redirect_enabled`-Parameter auf `ON`, um Verbindungen mit Umleitungsmodus zuzulassen. Verwenden Sie zum Aktualisieren dieses Serverparameters das [Azure-Portal](howto-server-parameters.md) oder die [Azure CLI](howto-configure-server-parameters-cli.md).

## <a name="php"></a>PHP

Unterstützung für die Umleitung in PHP-Anwendungen ist über die von Microsoft entwickelte [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)-Erweiterung verfügbar. 

Die Erweiterung „mysqlnd_azure“ kann über PECL zu PHP-Anwendungen hinzugefügt werden. Es wird dringend empfohlen, die Erweiterung über das offizielle veröffentlichte [PECL-Paket](https://pecl.php.net/package/mysqlnd_azure) zu installieren und zu konfigurieren.

> [!IMPORTANT]
> Unterstützung für die Umleitung in der PHP-Erweiterung [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) befindet sich derzeit in der Vorschauphase.

### <a name="redirection-logic"></a>Umleitungslogik

>[!IMPORTANT]
> Die Umleitungslogik bzw. das Umleitungsverhalten ab Version 1.1.0 wurde aktualisiert. **Es wird empfohlen, Version 1.1.0+ zu verwenden**.

Das Umleitungsverhalten wird durch den Wert von `mysqlnd_azure.enableRedirect` bestimmt. In der folgenden Tabelle wird das Verhalten der Umleitung basierend auf dem Wert dieses Parameters ab **Version 1.1.0+** beschrieben.

Wenn Sie eine ältere Version der Erweiterung „mysqlnd_azure“ (Version 1.0.0-1.0.3) verwenden, wird das Umleitungsverhalten durch den Wert von `mysqlnd_azure.enabled` bestimmt. Die gültigen Werte sind `off` (verhält sich ähnlich wie das in der folgenden Tabelle beschriebene Verhalten) und `on` (verhält sich wie `preferred` in der folgenden Tabelle).  

|**mysqlnd_azure.enableRedirect-Wert**| **Verhalten**|
|----------------------------------------|-------------|
|`off` oder `0`|Die Umleitung wird nicht verwendet. |
|`on` oder `1`|– Wenn die Verbindung treiberseitig nicht SSL verwendet, wird keine Verbindung hergestellt. Der folgende Fehler wird zurückgegeben: *mysqlnd_azure.enableRedirect ist aktiviert, die SSL-Option ist in der Verbindungszeichenfolge jedoch nicht festgelegt. Die Umleitung ist nur mit SSL möglich.*<br>– Wenn SSL auf der Treiberseite verwendet wird, die Umleitung auf dem Server aber nicht unterstützt wird, wird die erste Verbindung abgebrochen, und der folgende Fehler wird zurückgegeben: *„Die Verbindung wurde abgebrochen, da die Umleitung auf dem MariaDB-Server nicht aktiviert ist oder das Netzwerkpaket nicht dem Umleitungsprotokoll entspricht.“*<br>– Wenn der MariaDB-Server die Umleitung unterstützt, bei der umgeleiteten Verbindung jedoch aus irgendeinem Grund ein Fehler aufgetreten ist, brechen Sie auch die erste Proxyverbindung ab. Geben Sie den Fehler der umgeleiteten Verbindung zurück.|
|`preferred` oder `2`<br> (Standardwert)|– wenn möglich, verwendet mysqlnd_azure die Umleitung.<br>– Wenn die Verbindung kein SSL auf Treiberseite verwendet, der Server keine Umleitung unterstützt oder die umgeleitete Verbindung aus einem nicht schwerwiegenden Grund nicht hergestellt werden kann, während die Proxyverbindung weiterhin gültig ist, wird auf die erste Proxyverbindung zurückgegriffen.|

In den nachfolgenden Abschnitten dieses Dokuments wird erläutert, wie Sie die `mysqlnd_azure`-Erweiterung mithilfe von PECL installieren und den Wert dieses Parameters festlegen.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Voraussetzungen 
- PHP-Versionen 7.2.15+ und 7.3.2+
- PHP PEAR 
- php-mysql
- Azure Database for MariaDB-Server

1. Installieren Sie [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) mit [PECL](https://pecl.php.net/package/mysqlnd_azure). Empfohlen wird die Verwendung von Version 1.1.0+.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Suchen Sie das Erweiterungsverzeichnis (`extension_dir`) mit den folgenden Schritten:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Ändern Sie die Verzeichnisse auf den zurückgegebenen Ordner, und sorgen Sie dafür, dass sich `mysqlnd_azure.so` in diesem Ordner befindet. 

4. Suchen Sie den Ordner für INI-Dateien mit folgenden Schritten: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Ändern Sie die Verzeichnisse auf diesen zurückgegebenen Ordner. 

6. Erstellen Sie eine neue INI-Datei für `mysqlnd_azure`. Vergewissern Sie sich, dass die alphabetische Reihenfolge des Namens nach der von „mysqnld“ beginnt, da die Module entsprechend der Namensreihenfolge der INI-Dateien geladen werden. Wenn die Datei `mysqlnd`.ini beispielsweise den Namen `10-mysqlnd.ini` hat, benennen Sie die Datei „mysqlnd.ini“ um in `20-mysqlnd-azure.ini`.

7. Fügen Sie in der neuen INI-Datei die folgenden Zeilen hinzu, um die Umleitung zu aktivieren.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Voraussetzungen 
- PHP-Versionen 7.2.15+ und 7.3.2+
- php-mysql
- Azure Database for MariaDB-Server

1. Ermitteln Sie, ob Sie eine x64- oder x86-Version von PHP ausführen, indem Sie den folgenden Befehl ausführen:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Laden Sie die entsprechende x64- oder x86-Version der [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure)-DLL aus der [PECL](https://pecl.php.net/package/mysqlnd_azure) herunter, die Ihrer PHP-Version entspricht. Empfohlen wird die Verwendung von Version 1.1.0+.

3. Extrahieren Sie die ZIP-Datei, und suchen Sie die DLL `php_mysqlnd_azure.dll`.

4. Suchen Sie das Erweiterungsverzeichnis (`extension_dir`), indem Sie den folgenden Befehl ausführen:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopieren Sie die Datei `php_mysqlnd_azure.dll` in das Verzeichnis, das in Schritt 4 zurückgegeben wurde. 

6. Suchen Sie mit dem folgenden Befehl den PHP-Ordner, der die Datei `php.ini` enthält:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Ändern Sie die Datei `php.ini`, und fügen Sie die folgenden zusätzlichen Zeilen hinzu, um die Umleitung zu aktivieren. 

    Im Abschnitt „Dynamische Erweiterungen“: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Im Abschnitt „Moduleinstellungen“:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Bestätigen der Umleitung

Sie können auch bestätigen, dass die Umleitung mit dem nachstehenden PHP-Beispielcode konfiguriert wurde. Erstellen Sie die PHP-Datei `mysqlConnect.php`, und fügen Sie den nachstehenden Code ein. Aktualisieren Sie den Servernamen, den Benutzernamen und das Kennwort mit Ihren eigenen Namen bzw. Ihrem eigenen Kennwort. 
 
 ```php
<?php
$host = '<yourservername>.mariadb.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Verbindungszeichenfolgen](howto-connection-string.md).
