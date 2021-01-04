---
title: Konfigurieren der Datenreplikation – Azure Database for MariaDB
description: In diesem Artikel wird beschrieben, wie die Datenreplikation in Azure Database for MariaDB eingerichtet wird.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: fe7e02cc34dc9c97e540d7b8d96c48ee8d5cfe09
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535366"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurieren der Datenreplikation in Azure Database for MariaDB

In diesem Artikel erfahren Sie, wie Sie die [Datenreplikation](concepts-data-in-replication.md) in Azure Database for MariaDB einrichten, indem Sie Quell- und Replikatserver konfigurieren. In diesem Artikel wird davon ausgegangen, dass Sie über ein gewisses Maß an Erfahrung mit MariaDB-Servern und -Datenbanken verfügen.

Bei der [Datenreplikation](concepts-data-in-replication.md) werden Daten von einem lokalen MariaDB-Quellserver, virtuellen Computern (VMs) oder Cloud-Datenbankdiensten synchronisiert, um ein Replikat im Azure Database for MariaDB-Dienst zu erstellen. Die Datenreplikation basiert auf der nativen MariaDB-Replikation, die wiederum auf der Position der binären Protokolldatei (binlog) basiert. Weitere Informationen zur binlog-Replikation finden Sie [Übersicht über die binlog Replikation](https://mariadb.com/kb/en/library/replication-overview/).

Überprüfen Sie die [Einschränkungen und Anforderungen](concepts-data-in-replication.md#limitations-and-considerations) der Datenreplikation, bevor Sie die Schritte in diesem Artikel ausführen.

> [!NOTE]
> Wenn Ihr Quellserver die Version 10.2 oder höher hat, wird empfohlen, die Datenreplikation mithilfe der [globalen Transaktions-ID](https://mariadb.com/kb/en/library/gtid/) einzurichten.


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Erstellen eines als Replikat zu verwendenden MariaDB-Servers

1. Erstellen Sie einen neuen Azure Database for MariaDB-Server (z.B. replica.mariadb.database.azure.com). Der Server ist bei der Datenreplikation der Replikatserver.

    Informationen zur Servererstellung finden Sie unter [Erstellen eines Azure Database for MariaDB-Servers über das Azure-Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Sie müssen den Azure Database for MariaDB-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ erstellen.

2. Erstellen Sie identische Benutzerkonten und entsprechende Berechtigungen.
    
    Benutzerkonten werden nicht vom Quellserver auf den Replikatserver repliziert. Um Benutzern Zugriff auf den Replikatserver zu gewähren, müssen Sie alle Konten und die entsprechenden Berechtigungen für diesen neu erstellten Azure Database for MariaDB-Server manuell erstellen.

3. Fügen Sie den Firewallregeln des Replikats die IP-Adresse des Quellservers hinzu. 

   Aktualisieren Sie Firewallregeln über das [Azure-Portal](howto-manage-firewall-portal.md) oder über die [Azure-Befehlszeilenschnittstelle](howto-manage-firewall-cli.md).

> [!NOTE]
> Unvoreingenommene Kommunikation
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort _Slave_ (Sklave) verwendet. Laut [Microsoft-Styleguide für unvoreingenommene Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. In diesem Artikel wird es aus Konsistenzgründen verwendet, da das Wort derzeit noch in der Software vorkommt. Wenn die Software aktualisiert und um dieses Wort bereinigt wird, wird auch der Artikel entsprechend aktualisiert.
>

## <a name="configure-the-source-server"></a>Konfigurieren des Quellservers

Mit den folgenden Schritten wird der MariaDB-Server, der lokal, auf einer VM oder in einem Cloud-Datenbankdienst gehostet wird, für die Datenreplikation vorbereitet und konfiguriert. Der MariaDB-Server ist bei der Datenreplikation die Quelle.

1. Überprüfen Sie die [Anforderungen für den Masterserver](concepts-data-in-replication.md#requirements), bevor Sie fortfahren. 

2. Stellen Sie sicher, dass der Quellserver sowohl eingehenden als auch ausgehenden Datenverkehr an Port 3306 zulässt und über eine **öffentliche IP-Adresse** verfügt und dass der DNS öffentlich zugänglich ist oder über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügt. 
   
   Testen Sie die Konnektivität mit dem Quellserver, indem Sie versuchen, eine Verbindung über ein Tool, z. B. die MySQL-Befehlszeile auf einem anderen Computer, oder über die im Azure-Portal verfügbare [Azure Cloud Shell](../cloud-shell/overview.md) herzustellen.

   Wenn in Ihrer Organisation strenge Sicherheitsrichtlinien gelten, die nicht zulassen, dass alle IP-Adressen auf dem Quellserver eine Kommunikation von Azure mit dem Quellserver ermöglichen, können Sie potenziell mit dem folgenden Befehl die IP-Adresse Ihres Azure Database for MariaDB-Servers ermitteln.
    
   1. Melden Sie sich bei Ihrer Azure Database for MariaDB-Instanz mit einem Tool wie der MySQL-Befehlszeile an.
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

2. Aktivieren Sie die binäre Protokollierung.
    
    Um zu prüfen, ob die binäre Protokollierung auf dem Master aktiviert wurde, führen Sie den folgenden Befehl aus:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Wenn die Variable [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) den Wert `ON` zurückgibt, ist die binäre Protokollierung auf Ihrem Server aktiviert.

   Wenn `log_bin` den Wert `OFF` zurückgibt, bearbeiten Sie die Datei **my.cnf**, damit `log_bin=ON` die binäre Protokollierung aktiviert. Starten Sie den Server neu, damit die Änderung wirksam wird.

3. Konfigurieren Sie die Quellservereinstellungen.

    Der Parameter `lower_case_table_names` muss bei der Datenreplikation zwischen Quell- und Replikatserver konsistent sein. Der Parameter `lower_case_table_names` ist in Azure Database for MariaDB standardmäßig auf `1` festgelegt.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Erstellen Sie eine neue Replikationsrolle, und richten Sie Berechtigungen ein.

   Erstellen Sie auf dem Quellserver ein Benutzerkonto, das mit Replikationsberechtigungen konfiguriert ist. Sie können ein Konto mit SQL-Befehlen oder MySQL Workbench erstellen. Wenn Sie eine Replikation mit SSL planen, müssen Sie dies bei der Erstellung des Benutzerkontos angeben.
   
   Wie auf Ihrem Quellserver Benutzerkonten hinzugefügt werden, erfahren Sie in der [MariaDB-Dokumentation](https://mariadb.com/kb/en/library/create-user/).

   Bei Verwendung der folgenden Befehle kann die neu erstellte Replikationsrolle nicht nur vom Computer, auf dem die Quelle selbst gehostet wird, sondern von jedem Computer aus auf die Quelle zugreifen. Geben Sie für diesen Zugriff im Befehl **syncuser\@'%'** an, um einen Benutzer zu erstellen.
   
   Weitere Informationen finden Sie in der MariaDB-Dokumentation unter [Specifying Account Names](https://mariadb.com/kb/en/library/create-user/#account-names) (Angeben von Kontonamen).

   **SQL-Befehl**

   - Replikation mit SSL

       Um für alle Benutzerverbindungen SSL zu erzwingen, geben Sie den folgenden Befehl zum Erstellen eines Benutzers ein:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikation ohne SSL

       Wenn SSL nicht für alle Benutzerverbindungen erforderlich ist, geben Sie den folgenden Befehl zum Erstellen eines Benutzers ein:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Um die Replikationsrolle in MySQL Workbench zu erstellen, wählen Sie im Bereich **Management** (Verwaltung) **Users and Privileges** (Benutzer und Berechtigungen) aus. Klicken Sie dann auf **Add Account** (Konto hinzufügen).
 
   ![Benutzer und Berechtigungen](./media/howto-data-in-replication/users_privileges.png)

   Geben Sie in das Feld **Login Name** (Anmeldename) einen Benutzernamen ein.

   ![Benutzersynchronisierung](./media/howto-data-in-replication/syncuser.png)
 
   Wählen Sie den Bereich **Administratorrollen** (Administrative Roles) und dann in der Liste **Global Privileges** (Globale Berechtigungen) **Replication Slave** (Replikationsslave) aus. Klicken Sie dann auf **Apply** (Übernehmen), um die Replikationsrolle zu erstellen.

   ![Replikationsslave](./media/howto-data-in-replication/replicationslave.png)


5. Versetzen Sie den Quellserver in den schreibgeschützten Modus.

   Bevor Sie eine Datenbank sichern, muss der Server in den schreibgeschützten Modus versetzt werden. Im schreibgeschützten Modus kann die Quelle keine Schreibtransaktionen verarbeiten. Um Beeinträchtigung des Geschäftsbetriebs zu vermeiden, planen Sie das Versetzen in den schreibgeschützten Modus außerhalb der Spitzenzeiten ein.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Rufen Sie den Namen und Offset der aktuellen binären Protokolldatei ab.

   Führen Sie den Befehl [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) aus, um Name und Offset der aktuellen binären Protokolldatei zu ermitteln.
    
   ```sql
   show master status;
   ```
   Die Ergebnisse sollten in etwa wie in der folgenden Tabelle aussehen:
   
   ![Statusergebnisse des Masters](./media/howto-data-in-replication/masterstatus.png)

   Notieren Sie sich den Namen der Binärdatei, da dieser bei den nachfolgenden Schritten benötigt wird.
   
7. Rufen Sie (optional) die GTID-Position ab (die für die Replikation mit GTID benötigt wird).

   Führen Sie die Funktion [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) aus, um die GTID-Position für den entsprechenden Dateinamen und Offset der binären Protokolldatei zu erhalten.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-source-server"></a>Sichern und Wiederherstellen des Quellservers

1. Sichern Sie alle Datenbanken auf dem Quellserver.

   Verwenden Sie mysqldump, um alle Datenbanken auf dem Quellserver zu sichern. Die MySQL- und Testbibliothek müssen nicht gesichert werden.

    Weitere Informationen finden Sie unter [Dump and restore](howto-migrate-dump-restore.md) (Sichern und Wiederherstellen).

2. Versetzen Sie den Quellserver in den Lese-/Schreibmodus.

   Nachdem die Datenbank gesichert wurde, versetzen Sie den MariaDB-Quellserver wieder in den Lese-/Schreibmodus.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Stellen Sie die Sicherungsdatei auf dem neuen Server wieder her.

   Stellen Sie die Sicherungsdatei auf dem Server wieder her, der im Dienst Azure Database for MariaDB erstellt wurde. Informationen zum Wiederherstellen einer Sicherungsdatei auf einem MariaDB-Server finden Sie unter [Migrieren der MariaDB-Datenbank zu Azure Database for MariaDB durch Sicherungen und Wiederherstellungen](howto-migrate-dump-restore.md).

   Wenn es sich um eine große Sicherungsdatei handelt, laden Sie sie auf eine VM in der Region Ihres Replikatservers in Azure hoch. Stellen Sie sie von der VM aus auf dem Azure Database for MariaDB-Server wieder her.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation

1. Legen Sie den Quellserver fest.

   Alle Datenreplikationsfunktionen erfolgen über gespeicherte Prozeduren. Alle Prozeduren finden Sie unter [Gespeicherte Prozeduren für Datenreplikationen in Azure Database for MySQL](reference-stored-procedures.md). Gespeicherte Prozeduren können in der MySQL-Shell oder MySQL Workbench ausgeführt werden.

   Um zwei Server zu verknüpfen und die Replikation zu starten, melden Sie sich beim Zielreplikatserver im Dienst Azure Database for MariaDB an. Legen Sie anschließend die externe Instanz als Quellserver fest, indem Sie die gespeicherte Prozedur `mysql.az_replication_change_master` oder `mysql.az_replication_change_master_with_gtid` auf dem Azure Database for MariaDB-Server verwenden.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   oder
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: Hostname des Quellservers
   - master_user: Benutzername des Quellservers
   - master_password: Kennwort des Quellservers
   - master_log_file: Name der binären Protokolldatei durch Ausführung von `show master status`
   - master_log_pos: Position des binären Protokolls durch Ausführung von `show master status`
   - master_gtid_pos: GTID-Position nach Ausführen von `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Der Kontext des Zertifizierungsstellenzertifikats. Wenn SSL nicht verwendet wird, übergeben Sie eine leere Zeichenfolge.*
    
    
    *Es wird empfohlen, den Parameter „master_ssl_ca“ als Variable zu übergeben. Weitere Informationen finden Sie in den folgenden Beispielen.

   **Beispiele**

   - Replikation mit SSL

       Erstellen Sie die Variable `@cert` durch Ausführen der folgenden Befehle:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Eine Replikation mit SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MariaDB gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikation ohne SSL

       Eine Replikation ohne SSL wird zwischen einem Quellserver, der in der Domäne companya.com gehostet wird, und einem Replikatserver eingerichtet, der in Azure Database for MariaDB gehostet wird. Diese gespeicherte Prozedur wird auf dem Replikat ausgeführt.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Starten Sie die Replikation.

   Rufen Sie die gespeicherte Prozedur `mysql.az_replication_start` auf, um die Replikation zu starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Überprüfen Sie den Replikationsstatus.

   Rufen Sie den Befehl [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) auf dem Replikatserver auf, um den Replikationsstatus anzuzeigen.
    
   ```sql
   show slave status;
   ```

   Wenn `yes` der Status von `Slave_IO_Running` und `Slave_SQL_Running` ist und `0` der Wert von `Seconds_Behind_Master` ist, funktioniert die Replikation ordnungsgemäß. `Seconds_Behind_Master` gibt an, wie stark das Replikat verzögert ist. Wenn der Wert nicht `0` ist, bedeutet dies, dass das Replikat momentan Updates verarbeitet.

4. Aktualisieren Sie die entsprechenden Servervariablen, um die Datenreplikation sicherer zu machen (nur für die Replikation ohne GTID erforderlich).
    
    Aufgrund einer nativen Replikationseinschränkung in MariaDB müssen Sie bei der Replikation ohne GTID-Szenario die Variablen [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) und [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) festlegen.

    Überprüfen Sie die Variablen `sync_master_info` und `sync_relay_log_info` Ihres Slaveservers, um sicherzustellen, dass die Datenreplikation stabil ist, und legen Sie die Variablen auf `1` fest.
    
## <a name="other-stored-procedures"></a>Andere gespeicherte Prozeduren

### <a name="stop-replication"></a>Beenden der Replikation

Um die Replikation zwischen dem Quell- und Replikatserver zu beenden, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Entfernen der Replikationsbeziehung

Um die Replikationsbeziehung zwischen Quell- und Replikatserver zu entfernen, verwenden Sie die folgende gespeicherte Prozedur:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Überspringen des Replikationsfehlers

Um einen Replikationsfehler zu überspringen und die Replikation zuzulassen, verwenden Sie die folgende gespeicherte Prozedur:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über die [Datenreplikation](concepts-data-in-replication.md) für Azure Database for MariaDB.
