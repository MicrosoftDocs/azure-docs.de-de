---
title: 'Schnellstart: Erstellen eines Servers – Azure-Portal – Azure Database for MySQL'
description: Dieser Artikel führt Sie durch die Verwendung des Azure-Portals, um in etwa fünf Minuten eine Azure-Beispieldatenbank für MySQL-Server zu erstellen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: f71bcc1fd3b92a32a3e6d9fa056bae7131a663bd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492606"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Schnellstart: Erstellen eines Servers für Azure-Datenbank für MySQL über das Azure-Portal

Azure Database for MySQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. In diesem Schnellstart wird veranschaulicht, wie Sie einen Azure Database for MySQL-Einzelserver über das Azure-Portal erstellen. Außerdem erfahren Sie, wie Sie eine Verbindung mit dem Server herstellen.

## <a name="prerequisites"></a>Voraussetzungen
Ein Azure-Abonnement ist erforderlich. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Erstellen eines Azure Database for MySQL-Einzelservers
1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/), um eine Datenbank vom Typ „MySQL Single Server“ zu erstellen. Suchen Sie nach **Azure Database for MySQL**, und wählen Sie die Option aus:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Suchen von Azure Database for MySQL":::

1. Wählen Sie **Hinzufügen**.

2. Wählen Sie auf der Seite **Option „Azure Database for MySQL-Bereitstellung“ auswählen** die Option **Einzelserver** aus:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Screenshot mit der Option „Einzelserver“":::

3. Geben Sie die Standardeinstellungen für einen neuen Einzelserver ein:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Screenshot mit der Seite „MySQL-Server erstellen“":::

   **Einstellung** | **Empfohlener Wert** | **Beschreibung**
   ---|---|---
   Subscription | Ihr Abonnement | Wählen Sie das gewünschte Azure-Abonnement aus.
   Resource group | **myresourcegroup** | Geben Sie eine neue Ressourcengruppe oder eine bereits vorhandene Gruppe aus Ihrem Abonnement ein.
   Servername | **mydemoserver** | Geben Sie einen eindeutigen Namen ein. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen enthalten.
   Datenquelle |**None** | Wählen Sie **Keine** aus, um einen neuen Server ohne Vorlage zu erstellen. Wählen Sie **Sicherung** nur aus, wenn Sie die Wiederherstellung aus einer Geosicherung eines vorhandenen Servers durchführen.
   Position |Der gewünschte Speicherort | Wählen Sie einen Ort aus der Liste aus.
   Version | Die aktuelle Hauptversion| Verwenden Sie die aktuelle Hauptversion. Zeigen Sie [alle unterstützten Versionen](../postgresql/concepts-supported-versions.md) an.
   Compute und Speicher | Verwenden von Standardwerten| Der Standardtarif ist **Universell** mit **4 virtuellen Kernen** und **100 GB** Speicher. Die Aufbewahrungsdauer für Sicherungen ist auf **7 Tage** mit der Sicherungsoption **Georedundant** festgelegt.<br/>Überprüfen Sie die Seite [Preise](https://azure.microsoft.com/pricing/details/mysql/), und aktualisieren Sie ggf. die Standardwerte.
   Administratorbenutzername | **mydemoadmin** | Geben Sie den Benutzernamen des Serveradministrators ein. Der Benutzername des Administrators darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.
   Kennwort | Ein Kennwort | Ein neues Kennwort für den Serveradministratorbenutzer. Das Kennwort muss zwischen 8 und 128 Zeichen lang sein und eine Kombination aus Groß- oder Kleinbuchstaben, Ziffern und nicht alphanumerischen Zeichen (!, $, #, % usw.) aufweisen.
  

   > [!NOTE]
   > Ziehen Sie die Verwendung des Tarifs „Basic“ in Betracht, wenn geringe Compute- und E/A-Leistung für Ihre Workload angemessen ist. Beachten Sie, dass im Tarif „Basic“ erstellte Server später nicht auf „Universell“ oder „Arbeitsspeicheroptimiert“ skaliert werden können.

4. Wählen Sie **Bewerten + erstellen** aus, um den Server bereitzustellen.

5. Warten Sie, bis auf der Portalseite **Ihre Bereitstellung wurde abgeschlossen.** angezeigt wird. Wählen Sie **Zu Ressource wechseln** aus, um die neu erstellte Serverseite aufzurufen:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Screenshot mit der Meldung „Ihre Bereitstellung wurde abgeschlossen.“":::

[Sind Probleme aufgetreten? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurieren einer Firewallregel auf Serverebene

Standardmäßig ist der neue Server durch eine Firewall geschützt. Zum Herstellen einer Verbindung müssen Sie den Zugriff auf die IP-Adresse wie folgt bereitstellen:

1. Wechseln Sie im linken Bereich der Serverressource zu **Verbindungssicherheit**. Wenn Sie nicht wissen, wie Sie Ihre Ressource finden können, lesen Sie die Informationen unter [Öffnen einer Ressource](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Screenshot mit der Seite „Verbindungssicherheit > Firewallregeln“":::

2. Wählen Sie **Aktuelle Client-IP-Adresse hinzufügen** und dann **Speichern** aus.

   > [!NOTE]
   > Überprüfen Sie, ob Ihr Netzwerk ausgehenden Datenverkehr über Port 3306 zulässt, um Verbindungsprobleme zu vermeiden. Dieser Port wird von Azure Database for MySQL verwendet. 

Sie können weitere IP-Adressen hinzufügen oder einen IP-Adressbereich angeben, um über diese IP-Adressen eine Verbindung mit dem Server herzustellen. Weitere Informationen finden Sie unter [Verwalten von Firewallregeln für den Azure Database for MySQL-Server](./concepts-firewall-rules.md).


[Sind Probleme aufgetreten? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Herstellen einer Serverbindung über „mysql.exe“
Sie können [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) oder [MySQL Workbench](./connect-workbench.md) auswählen, um aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herzustellen. In diesem Schnellstart führen wir „mysql.exe“ in [Azure Cloud Shell](../cloud-shell/overview.md) aus, um eine Verbindung mit dem Server herzustellen.


1. Öffnen Sie Azure Cloud Shell im Portal, indem Sie die erste Schaltfläche auf der Symbolleiste wie im folgenden Screenshot gezeigt auswählen. Notieren Sie sich den Servernamen, den Namen des Serveradministrators und das Abonnement für den neuen Server im Abschnitt **Übersicht**, wie im Screenshot gezeigt.

    > [!NOTE]
    > Wenn Sie Cloud Shell zum ersten Mal öffnen, werden Sie aufgefordert, eine Ressourcengruppe und ein Speicherkonto zu erstellen. Dies ist ein einmaliger Schritt. Diese Informationen werden für alle Sitzungen automatisch angefügt.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Screenshot mit der Cloud Shell im Azure-Portal":::
2. Führen Sie den nachstehenden Befehl im Azure Cloud Shell-Terminal aus. Ersetzen Sie die hier angezeigten Werte durch den tatsächlichen Servernamen und den Namen des Administratorbenutzers. Für Azure Database for MySQL müssen Sie `@\<servername>` den Namen des Administratorbenutzers hinzufügen, wie hier gezeigt: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Das sieht im Cloud Shell-Terminal wie folgt aus:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. Erstellen Sie im gleichen Azure Cloud Shell-Terminal eine Datenbank namens `guest`:
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Wechseln Sie zur Datenbank `guest`:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Geben Sie `quit` ein, und drücken Sie die **EINGABETASTE**, um mysql zu beenden.

[Sind Probleme aufgetreten? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie haben jetzt einen Azure Database for MySQL-Server in einer Ressourcengruppe erstellt.  Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, können Sie sie löschen, indem Sie die Ressourcengruppe oder einfach den MySQL-Server löschen. Um die Ressourcengruppe zu löschen, führen Sie die folgenden Schritte aus:
1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
2. Wählen Sie den Namen Ihrer Ressourcengruppe in der Liste der Ressourcengruppen aus.
3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

Zum Löschen des Servers können Sie auf der Seite **Übersicht** Ihres Servers auf **Löschen** klicken, wie hier gezeigt:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Screenshot mit der Schaltfläche „Löschen“ auf der Seite mit der Serverübersicht":::

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
>[Erstellen einer PHP-App unter Windows mit MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[Erstellen einer PHP-App unter Linux mit MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Können Sie die gesuchten Informationen nicht finden? Informieren Sie uns darüber.](https://aka.ms/mysql-doc-feedback)