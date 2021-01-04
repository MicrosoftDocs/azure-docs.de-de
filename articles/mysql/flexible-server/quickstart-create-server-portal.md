---
title: 'Schnellstart: Erstellen einer Azure Database for MySQL Flexible Server-Instanz – Azure-Portal'
description: In diesem Artikel wird erläutert, wie Sie über das Azure-Portal in wenigen Minuten eine Azure Database for MySQL Flexible Server-Instanz erstellen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566757"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>Schnellstart: Verwenden des Azure-Portals zum Erstellen einer Azure Database for MySQL Flexible Server-Instanz

Azure Database for MySQL Flexible Server ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Serverinstanzen in der Cloud ausführen, verwalten und skalieren können. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit dem Azure-Portal eine Flexible Server-Instanz erstellen.

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Erstellen einer Azure Database for MySQL Flexible Server-Instanz

Eine Flexible Server-Instanz wird mit einer definierten Gruppe von [Compute- und Speicherressourcen](./concepts-compute-storage.md) erstellt. Der Server wird in einer [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) erstellt.

Führen Sie die folgenden Schritte aus, um eine Flexible Server-Instanz zu erstellen:

1. Suchen Sie im Portal nach **Azure Database for MySQL-Server**, und wählen Sie die Option aus:
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="Screenshot: Suchen nach „Azure Database for MySQL-Server“":::

2. Wählen Sie **Hinzufügen**. 

3. Wählen Sie auf der Seite **Option „Azure Database for MySQL-Bereitstellung“ auswählen** die Bereitstellungsoption **Flexibler Server** aus.
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="Screenshot: Option „Flexibler Server“":::    

4. Geben Sie auf der Registerkarte **Grundlegende Einstellungen** folgende Informationen ein: 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="Screenshot: Registerkarte „Grundeinstellungen“ auf der Seite „Flexibler Server“"::: 
                                    
    |**Einstellung**|**Empfohlener Wert**|**Beschreibung**|
    |---|---|---|
    Subscription|Ihr Abonnementname|Das Azure-Abonnement, das Sie für Ihren Server verwenden möchten. Falls Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, über das die Ressource abgerechnet werden soll.|
    Resource group|**myresourcegroup**| Ein neuer Ressourcengruppenname oder ein bereits vorhandener Name aus Ihrem Abonnement|
    Servername |**mydemoserver**|Ein eindeutiger Name, der Ihre Flexible Server-Instanz identifiziert. Der Domänenname `mysql.database.azure.com` wird an den angegebenen Servernamen angefügt. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen lang sein.|
    Administratorbenutzername |**mydemouser**| Ihr Anmeldekonto für die Verbindungsherstellung mit dem Server. Der Administratorbenutzername darf nicht **azure_superuser**, **admin**, **administrator**, **root**, **guest** oder **public** lauten.|
    Kennwort |Ihr Kennwort| Ein neues Kennwort für das Serveradministratorkonto. Es muss zwischen acht und 128 Zeichen lang sein. Darüber hinaus muss es Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen (0 bis 9) und nicht alphanumerische Zeichen (!, $, #, % usw.).|
    Region|Die Region, die Ihren Benutzern am nächsten liegt| Der Standort, der Ihren Benutzern am nächsten ist|
    Version|**5.7**| Eine MySQL-Hauptversion|
    Compute und Speicher | **Burstfähig**, **Standard_B1ms**, **10 GiB**, **7 Tage** | Die Compute-, Speicher- und Sicherungskonfigurationen für Ihren neuen Server. Wählen Sie **Server konfigurieren** aus. **Burstfähig**, **Standard_B1ms**, **10 GiB** und **7 Tage** sind die Standardwerte für **Computetarif**, **Computegröße**, **Speichergröße** und **Aufbewahrungszeitraum für Sicherung**. Sie können diese Werte unverändert lassen oder anpassen. Um die Compute- und Speicherauswahl zu speichern und die Konfiguration fortzusetzen, wählen Sie **Speichern** aus. Der folgende Screenshot zeigt die Compute- und Speicheroptionen:|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="Screenshot: Compute- und Speicheroptionen":::

5. Konfigurieren Sie Netzwerkoptionen.

    Auf der Registerkarte **Netzwerk** können Sie auswählen, wie der Server erreichbar sein soll. Azure Database for MySQL Flexible Server bietet zwei Optionen zum Herstellen einer Verbindung mit Ihrem Server: 
   - Öffentlicher Zugriff (zugelassene IP-Adressen)
   - Privater Zugriff (VNET-Integration) 
   
   Bei öffentlichem Zugriff ist der Zugriff auf Ihren Server auf zugelassene IP-Adressen beschränkt, die einer Firewallregel hinzugefügt werden. Diese Methode verhindert, dass externe Anwendungen und Tools eine Verbindung mit der Serverinstanz oder mit Datenbanken auf dem Server herstellen – es sei denn, Sie erstellen eine Regel, um die Firewall für bestimmte IP-Adressen oder einen bestimmten IP-Adressbereich zu öffnen. Wenn Sie privaten Zugriff (VNET-Integration) verwenden, ist der Zugriff auf Ihren Server auf Ihr virtuelles Netzwerk beschränkt. In dieser Schnellstartanleitung erfahren Sie, wie Sie den öffentlichen Zugriff aktivieren, um eine Verbindung mit dem Server herzustellen. Im Artikel zu [Konzepten](./concepts-networking.md) erfahren Sie mehr über Konnektivitätsmethoden.

    > [!NOTE]
    > Nach der Erstellung des Servers kann die Verbindungsmethode nicht mehr geändert werden. Wenn Sie während der Erstellung des Servers beispielsweise **Öffentlicher Zugriff (zugelassene IP-Adressen)** ausgewählt haben, können Sie nach der Erstellung nicht zu **Privater Zugriff (VNET-Integration)** wechseln. Es wird dringend empfohlen, den Server mit privatem Zugriff zu erstellen, um den Zugriff auf Ihren Server über die VNET-Integration zu schützen. Weitere Informationen zum privaten Zugriff finden Sie im Artikel zu [Konzepten](./concepts-networking.md).

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="Screenshot: Registerkarte „Netzwerk“":::  

6. Wählen Sie **Überprüfen + erstellen** aus, um Ihre Flexible Server-Konfiguration zu überprüfen.

7. Wählen Sie **Erstellen** aus, um den Server bereitzustellen. Die Bereitstellung kann einige Minuten dauern.

8. Wählen Sie auf der Symbolleiste die Option **Benachrichtigungen** (Glockenschaltfläche) aus, um den Bereitstellungsprozess zu überwachen. Nach Abschluss der Bereitstellung können Sie **An Dashboard anheften** auswählen, um auf Ihrem Azure-Portal-Dashboard eine Kachel für die Flexible Server-Instanz zu erstellen. Über diese Kachel gelangen Sie direkt zur Seite **Übersicht** des Servers. Wenn Sie **Zu Ressource wechseln** auswählen, wird die **Übersicht** des Servers geöffnet.

Unter Ihrem Server werden standardmäßig folgende Datenbanken erstellt: „information_schema“, „mysql“, „performance_schema“ und „sys“.

> [!NOTE]
> Überprüfen Sie, ob Ihr Netzwerk ausgehenden Datenverkehr über Port 3306 zulässt, um Verbindungsprobleme zu vermeiden. Dieser Port wird von Azure Database for MySQL Flexible Server verwendet.  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Herstellen einer Serverbindung über „mysql.exe“

Wenn Sie Ihre Flexible Server-Instanz mit privatem Zugriff (VNET-Integration) erstellt haben, müssen Sie eine Verbindung mit der Serverinstanz über eine Ressource innerhalb desselben virtuellen Netzwerks herstellen. Sie können einen virtuellen Computer erstellen und zum virtuellen Netzwerk hinzufügen, das mit Ihrer Flexible Server-Instanz erstellt wurde.

Wenn Sie Ihre Flexible Server-Instanz mit öffentlichem Zugriff (zulässige IP-Adressen) erstellt haben, können Sie die lokale IP-Adresse der Liste der Firewallregeln für die Serverinstanz hinzufügen.

Sie können [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) oder [MySQL Workbench](./connect-workbench.md) auswählen, um aus Ihrer lokalen Umgebung eine Verbindung mit dem Server herzustellen. 

Stellen Sie bei Verwendung von „mysql.exe“ mithilfe des folgenden Befehls eine Verbindung her. Verwenden Sie im Befehl Ihren Servernamen, Benutzernamen und Ihr Kennwort. 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Sie haben jetzt eine Azure Database for MySQL Flexible Server-Instanz in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, können Sie sie löschen, indem Sie die Ressourcengruppe oder einfach den MySQL-Server löschen. Um die Ressourcengruppe zu löschen, führen Sie die folgenden Schritte aus:

1. Suchen Sie im Azure-Portal nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie den Namen Ihrer Ressourcengruppe in der Liste der Ressourcengruppen aus.
1. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

Zum Löschen des Servers können Sie auf der Seite **Übersicht** Ihres Servers **Löschen** auswählen, wie hier gezeigt:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="Screenshot: Löschen eines Servers":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)