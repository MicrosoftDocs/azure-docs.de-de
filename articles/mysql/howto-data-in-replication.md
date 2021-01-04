---
title: Konfigurieren der Datenreplikation – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie die Datenreplikation für Azure Database for MySQL eingerichtet wird.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: b7f1f16b5182658f42ad6594aace22fb5a1a80fc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541401"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Gewusst wie: Konfigurieren der Datenreplikation in Azure Database for MySQL

In diesem Artikel erfahren Sie, wie Sie die [Datenreplikation](concepts-data-in-replication.md) in Azure Database for MySQL einrichten, indem Sie Quell- und Replikatserver konfigurieren. In diesem Artikel wird davon ausgegangen, dass Sie über ein gewisses Maß an Erfahrung mit MySQL-Servern und -Datenbanken verfügen.

> [!NOTE]
> Unvoreingenommene Kommunikation
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort _Slave_ (Sklave) verwendet. Laut [Microsoft-Styleguide für unvoreingenommene Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. In diesem Artikel wird es aus Konsistenzgründen verwendet, da das Wort derzeit noch in der Software vorkommt. Sobald die Software so aktualisiert wurde, dass das Wort nicht mehr auftaucht, wird dieser Artikel entsprechend aktualisiert.
>

Um ein Replikat im Azure Database for MySQL-Dienst zu erstellen, synchronisiert die [Datenreplikation](concepts-data-in-replication.md) Daten von einem lokalen MySQL-Quellserver, virtuellen Computern (VMs) oder Clouddatenbankdiensten. Die Replikation eingehender Daten basiert auf der nativen MySQL-Replikation, die wiederum auf der Position der binären Protokolldatei (binlog) basiert. Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht).

Überprüfen Sie die [Einschränkungen und Anforderungen](concepts-data-in-replication.md#limitations-and-considerations) der Datenreplikation, bevor Sie die Schritte in diesem Artikel ausführen.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Erstellen eines als Replikat zu verwendenden MySQL-Servers

1. Erstellen Sie einen neuen Azure Database for MySQL-Server.

   Erstellen Sie einen neuen MySQL-Server (z.B. „replica.mysql.database.azure.com“). Informationen zur Servererstellung finden Sie unter [Erstellen eines Azure Database for MySQL-Servers über das Azure-Portal](quickstart-create-mysql-server-database-using-azure-portal.md). Dieser Server ist der Replikatserver bei der Datenreplikation.

   > [!IMPORTANT]
   > Der Azure Database for MySQL-Server muss in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ erstellt werden.
   > 

1. Erstellen Sie dieselben Benutzerkonten und entsprechenden Berechtigungen.

   Benutzerkonten werden nicht vom Quellserver auf den Replikatserver repliziert. Wenn Sie Benutzern Zugriff auf den Replikatserver gewähren möchten, müssen Sie alle Konten und entsprechenden Berechtigungen für diesen neu erstellten Azure Database for MySQL-Server manuell erstellen.

1. Fügen Sie den Firewallregeln des Replikats die IP-Adresse des Quellservers hinzu. 

   Aktualisieren Sie Firewallregeln über das [Azure-Portal](howto-manage-firewall-using-portal.md) oder über die [Azure-Befehlszeilenschnittstelle](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>Konfigurieren des Quellservers
Mit den folgenden Schritten wird der MySQL-Server, der lokal, auf einem virtuellen Computer oder von einem von anderen Cloudanbietern gehosteten Datenbankdienst gehostet wird, für die Replikation eingehender Daten vorbereitet und konfiguriert. Dieser Server ist bei der Datenreplikation der „Master“.


1. Überprüfen Sie die [Anforderungen für den Masterserver](concepts-data-in-replication.md#requirements), bevor Sie fortfahren. 

2. Stellen Sie sicher, dass der Quellserver sowohl eingehenden als auch ausgehenden Datenverkehr an Port 3306 zulässt und über eine **öffentliche IP-Adresse** verfügt und dass der DNS öffentlich zugänglich ist oder über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügt. 
   
   Testen Sie die Konnektivität mit dem Quellserver, indem Sie versuchen, eine Verbindung über ein Tool, z. B. die MySQL-Befehlszeile auf einem anderen Computer, oder über die im Azure-Portal verfügbare [Azure Cloud Shell](../cloud-shell/overview.md) herzustellen.

   Wenn in Ihrer Organisation strenge Sicherheitsrichtlinien gelten, die nicht zulassen, dass alle IP-Adressen auf dem Quellserver eine Kommunikation von Azure mit dem Quellserver ermöglichen, können Sie möglicherweise mit dem folgenden Befehl die IP-Adresse Ihres MySQL-Servers ermitteln.

   1. Melden Sie sich bei Ihrer Azure Database for MySQL-Instanz mit einem Tool wie der MySQL-Befehlszeile an.
   2. Führen Sie die folgenden Abfrage aus.
      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```
      Nachstehend ist eine Beispielausgabe aufgeführt:
      ```bash 
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```
   3. Beenden Sie die Ausführung mit dem MySQL-Befehlszeilenprogramm.
   4. Führen Sie den folgenden Befehl im Hilfsprogramm ping aus, um die IP-Adresse anzurufen.
      ```bash
      ping <output of step 2b>
      ``` 
      Beispiel: 
      ```bash      
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Konfigurieren Sie die Firewallregeln des Quellservers so, dass die im letzten Schritt ausgegebene IP-Adresse an Port 3306 eingeschlossen ist.

   > [!NOTE]
   > Diese IP-Adresse kann sich bei Wartungs- und Bereitstellungsvorgängen ändern. Diese Verbindungsmethode gilt nur für Kunden, die nicht alle IP-Adressen an Port 3306 zulassen können.
   
1. Aktivieren Sie die binäre Protokollierung.

   Überprüfen Sie, ob die binäre Protokollierung auf der Quelle aktiviert wurde, indem Sie den folgenden Befehl ausführen: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Wenn die Variable [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) mit dem Wert „ON“ zurückgegeben wird, ist die binäre Protokollierung auf Ihrem Server aktiviert. 

   Wenn `log_bin` mit dem Wert „OFF“ zurückgegeben wird, aktivieren Sie die binäre Protokollierung, indem Sie die Datei „my.cnf“ ändern, sodass `log_bin=ON` lautet. Starten Sie dann den Server neu, damit die Änderung wirksam wird.

1. Quellservereinstellungen

   Der Parameter `lower_case_table_names` muss bei der Datenreplikation zwischen Quell- und Replikatserver konsistent sein. Dieser Parameter ist bei Azure Database for MySQL standardmäßig „1“. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

1. Erstellen Sie eine neue Replikationsrolle, und richten Sie Berechtigungen ein.

   Erstellen Sie auf dem Quellserver ein Benutzerkonto, das mit Replikationsberechtigungen konfiguriert ist. Dies kann über SQL-Befehle oder ein Tool wie MySQL Workbench erfolgen. Treffen Sie die Entscheidung, ob Sie eine Replikation mit SSL durchführen möchten, da dies bei der Erstellung des Benutzers angegeben werden muss. Wie [Ihrem Quellserver Benutzerkonten hinzugefügt werden](https://dev.mysql.com/doc/refman/5.7/en/user-names.html), erfahren Sie in der MySQL-Dokumentation. 

   In den unten aufgeführten Befehlen kann die neu erstellte Replikationsrolle nicht nur vom Computer, auf dem die Quelle selbst gehostet wird, auf die Quelle zugreifen, sondern von jedem Computer aus. Hierfür muss „syncuser@'%'“ im Befehl zum Erstellen von Benutzern angegeben werden. Weitere Informationen finden Sie in der MySQL-Dokumentation unter [Specifying Account Names](https://dev.mysql.com/doc/refman/5.7/en/account-names.html) (Angeben von Kontonamen).

   **SQL-Befehl**

   *Replikation mit SSL*

   Um für alle Benutzerverbindungen SSL zu erzwingen, verwenden Sie den folgenden Befehl zum Erstellen eines Benutzers: 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replikation ohne SSL*

   Wenn SSL nicht für alle Benutzerverbindungen erforderlich ist, verwenden Sie den folgenden Befehl zum Erstellen eines Benutzers:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Um die Replikationsrolle in MySQL Workbench zu erstellen, navigieren Sie zu **Benutzer und Berechtigungen** im Bereich **Verwaltung**. Klicken Sie anschließend auf **Konto hinzufügen**. 
 
   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Benutzer und Berechtigungen":::

   Geben Sie den Benutzernamen in das Feld **Anmeldename** ein. 

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Benutzersynchronisierung":::
 
   Klicken Sie auf den Bereich **Administratorrollen**, und wählen Sie aus der Liste **Globale Berechtigungen** die Option **Replikationsslave** aus. Klicken Sie dann auf **Anwenden**, um die Replikationsrolle zu erstellen.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replikationsslave":::

1. Versetzen des Quellservers in den schreibgeschützten Modus

   Bevor Sie mit dem Sichern der Datenbank beginnen, muss der Server in den schreibgeschützten Modus versetzt werden. Im schreibgeschützten Modus kann die Quelle keine Schreibtransaktionen verarbeiten. Werten Sie die Auswirkungen auf Ihr Unternehmen aus, und planen Sie das Fenster für den schreibgeschützten Modus bei Bedarf in der Nebenzeit.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

1. Rufen Sie den Namen und das Offset der binären Protokolldatei ab.

   Führen Sie den Befehl [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) aus, um den aktuellen Namen und das Offset der binären Protokolldatei zu ermitteln.
    
   ```sql
    show master status;
   ```
   Die Ergebnisse sollten wie folgt aussehen. Notieren Sie sich den Namen der Binärdatei, da dieser bei den nachfolgenden Schritten benötigt wird.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Statusergebnisse des Masters":::
 
## <a name="dump-and-restore-source-server"></a>Sichern und Wiederherstellen des Quellservers

1. Legen Sie fest, welche Datenbanken und Tabellen in Azure Database for MySQL repliziert werden sollen, und führen Sie die Sicherung vom Quellserver aus.
 
    Mit „mysqldump“ können Sie Datenbanken von Ihrem Masterserver sichern. Weitere Informationen finden Sie unter [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](concepts-migrate-dump-restore.md). Die MySQL- und die Testbibliothek müssen nicht gesichert werden.

1. Versetzen des Quellservers in den Lese-/Schreibmodus

   Nachdem die Datenbank gesichert wurde, versetzen Sie den MySQL-Quellserver wieder in den Lese-/Schreibmodus.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

1. Stellen Sie die Sicherungsdatei auf dem neuen Server wieder her.

   Stellen Sie die Sicherungsdatei auf dem Server wieder her, der im Dienst Azure Database for MySQL erstellt wurde. Informationen zum Wiederherstellen einer Sicherungsdatei auf einem MySQL-Server finden Sie unter [Migrieren der MySQL-Datenbank auf Azure-Datenbank für MySQL durch Sicherungen und Wiederherstellungen](concepts-migrate-dump-restore.md). Handelt es sich um eine große Sicherungsdatei, laden Sie sie auf einen virtuellen Computer in Azure in der gleichen Region wie Ihr Replikatserver hoch. Stellen Sie sie auf dem Azure Database for MySQL-Server vom virtuellen Computer wieder her.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation

1. Festlegen des Quellservers

   Alle Datenreplikationsfunktionen erfolgen über gespeicherte Prozeduren. Alle Prozeduren finden Sie unter [Gespeicherte Prozeduren für Datenreplikationen in Azure Database for MySQL](./reference-stored-procedures.md). Die gespeicherten Prozeduren können in der MySQL-Shell oder in MySQL Workbench ausgeführt werden. 

   Um zwei Server zu verknüpfen und die Replikation zu starten, melden Sie sich beim Zielreplikatserver im Dienst Azure Database for MySQL an, und legen Sie die externe Instanz als Quellserver fest. Hierfür verwenden Sie die gespeicherte Prozedur `mysql.az_replication_change_master` auf dem Azure Database for MySQL-Server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: Hostname des Quellservers
   - master_user: Benutzername des Quellservers
   - master_password: Kennwort des Quellservers
   - master_log_file: Name der binären Protokolldatei durch Ausführung von `show master status`
   - master_log_pos: Position des binären Protokolls durch Ausführung von `show master status`
   - master_ssl_ca: Der Kontext des Zertifizierungsstellenzertifikats. Wenn SSL nicht verwendet wird, übergeben Sie eine leere Zeichenfolge.
       - Es wird empfohlen, diesen Parameter als Variable zu übergeben. Weitere Informationen finden Sie in den folgenden Beispielen.

   > [!NOTE]
   > Wenn der Quellserver auf einer Azure-VM gehostet wird, legen Sie „Zugriff auf Azure-Dienste erlauben“ auf „EIN“ fest, damit Quell- und Replikatserver miteinander kommunizieren können. Diese Einstellung kann über die Optionen für die **Verbindungssicherheit** geändert werden. Weitere Informationen finden Sie unter [Verwaltung von Firewallregeln mit dem Portal](howto-manage-firewall-using-portal.md).
      
   **Beispiele**
   
   *Replikation mit SSL*
   
   Die Variable `@cert` wird durch Ausführung der folgenden MySQL-Befehle erstellt: 
   
      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```
   
   Eine Replikation mit SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MySQL gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt. 
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```
   *Replikation ohne SSL*
   
   Eine Replikation ohne SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MySQL gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt.
   
      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

1. Filterung 
 
   Wenn Sie die Replikation einiger Tabellen von Ihrem Master überspringen möchten, aktualisieren Sie den Serverparameter `replicate_wild_ignore_table` auf Ihrem Replikatserver. Mithilfe einer durch Trennzeichen getrennten Liste können Sie mehr als ein Tabellenmuster angeben.

   Weitere Informationen zu diesem Parameter finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table). 
    
   Sie können den Parameter über das [Azure-Portal](howto-server-parameters.md) oder die [Azure-Befehlszeilenschnittstelle](howto-configure-server-parameters-using-cli.md) aktualisieren.

1. Starten Sie die Replikation.

   Rufen Sie die gespeicherte Prozedur `mysql.az_replication_start` auf, um die Replikation zu initiieren.

   ```sql
   CALL mysql.az_replication_start;
   ```

1. Überprüfen Sie den Replikationsstatus.

   Rufen Sie den Befehl [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) auf dem Replikatserver auf, um den Replikationsstatus anzuzeigen.
    
   ```sql
   show slave status;
   ```

   Wenn der Status von `Slave_IO_Running` und `Slave_SQL_Running` „yes“ lautet und der Wert von `Seconds_Behind_Master` „0“ ist, funktioniert die Replikation ordnungsgemäß. `Seconds_Behind_Master` gibt an, wie stark das Replikat verzögert ist. Wenn der Wert nicht „0“ ist, bedeutet dies, dass das Replikat momentan Updates verarbeitet. 

## <a name="other-stored-procedures"></a>Andere gespeicherte Prozeduren

### <a name="stop-replication"></a>Beenden der Replikation

Um die Replikation zwischen dem Quell- und Replikatserver zu beenden, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Entfernen der Replikationsbeziehung

Um die Replikationsbeziehung zwischen Quell- und Replikatserver zu entfernen, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Überspringen des Replikationsfehlers

Um einen Replikationsfehler zu überspringen und die Replikation fortzusetzen, verwenden Sie die folgende gespeicherte Prozedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über die [Replikation eingehender Daten](concepts-data-in-replication.md) für Azure Database for MySQL.