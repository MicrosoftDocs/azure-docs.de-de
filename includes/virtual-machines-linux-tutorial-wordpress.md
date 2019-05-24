---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 5df1f7ff44a1603dd03d1d803ae9960dc124781e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161720"
---
## <a name="install-wordpress"></a>Installieren von WordPress

Installieren Sie zum Ausprobieren des Stapels eine Beispiel-App. Als Beispiel wird in den folgenden Schritten die Open Source-Plattform [WordPress](https://wordpress.org/) zum Erstellen von Websites und Blogs installiert. Sie können auch andere Workloads wie [Drupal](http://www.drupal.org) und [Moodle](https://moodle.org/) ausprobieren. 

Dieses WordPress-Setup dient nur als Proof of Concept. Informationen zum Installieren des aktuellen WordPress-Pakets in der Produktion mit den empfohlenen Sicherheitseinstellungen finden Sie in der [WordPress-Dokumentation](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Installieren des WordPress-Pakets

Führen Sie den folgenden Befehl aus:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>WordPress konfigurieren

Konfigurieren Sie WordPress für die Verwendung von MySQL und PHP.

Erstellen Sie in einem Arbeitsverzeichnis die Textdatei `wordpress.sql`, um die MySQL-Datenbank für WordPress zu konfigurieren: 

```bash
sudo sensible-editor wordpress.sql
```

Fügen Sie die folgenden Befehle hinzu, und ersetzen Sie dabei *yourPassword* durch ein Datenbankkennwort Ihrer Wahl. (Ändern Sie die anderen Werte nicht.) Wenn Sie zuvor zur Überprüfung der Kennwortsicherheit eine MySQL-Sicherheitsrichtlinie eingerichtet haben, stellen Sie sicher, dass das Kennwort die Sicherheitsanforderungen erfüllt. Speichern Sie die Datei .

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```

Führen Sie den folgenden Befehl aus, um die Datenbank zu erstellen:

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

Da die Datei `wordpress.sql` Datenbankanmeldeinformationen enthält, löschen Sie sie nach der Verwendung:

```bash
sudo rm wordpress.sql
```

Führen Sie zum Konfigurieren von PHP den folgenden Befehl aus, um einen Text-Editor Ihrer Wahl zu öffnen und die Datei `/etc/wordpress/config-localhost.php` zu erstellen:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Kopieren Sie die folgenden Zeilen in die Datei, und ersetzen Sie dabei *yourPassword* durch Ihr WordPress-Datenbankkennwort. (Ändern Sie die anderen Werte nicht.) Speichern Sie dann die Datei.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```


Verschieben Sie die WordPress-Installation in den Dokumentstamm des Webservers:

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Jetzt können Sie das WordPress-Setup abschließen und auf der Plattform veröffentlichen. Öffnen Sie einen Browser, und navigieren Sie zu `http://yourPublicIPAddress/wordpress`. Ersetzen Sie die öffentliche IP-Adresse Ihres virtuellen Computers. Die Seite sollte ähnlich wie diese Abbildung aussehen.

![WordPress-Installationsseite](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)