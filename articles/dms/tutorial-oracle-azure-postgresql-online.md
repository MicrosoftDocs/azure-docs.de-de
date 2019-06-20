---
title: 'Tutorial: Ausführen einer Onlinemigration von Oracle zu Azure Database for PostgreSQL mit Azure Database Migration Service | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer lokal oder auf virtuellen Computern gehosteten Oracle-Instanz zu Azure Database for PostgreSQL ausführen.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 0b3af3d29e6e938f0301d751a79170c7c1964b45
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243799"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Onlinemigration von Oracle zu Azure Database for PostgreSQL mit DMS (Vorschauversion)

Mit Azure Database Migration Service können Sie die Datenbanken von lokal oder auf virtuellen Computern gehosteten Oracle-Datenbanken mit minimaler Downtime zu [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) migrieren. Das heißt, dass Sie die Migration mit minimaler Downtime für die Anwendung ausführen können. In diesem Tutorial migrieren Sie die **HR**-Beispieldatenbank von einer lokal oder auf virtuellen Computern gehosteten Oracle 11g-Instanz zu Azure Database for PostgreSQL. Zu diesem Zweck verwenden Sie die Onlinemigrationsaktivität in Azure Database Migration Service.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Bewerten des Migrationsaufwands mit dem Tool ora2pg
> * Migrieren des Beispielschemas mit dem Tool ora2pg
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird beschrieben, wie Sie eine Onlinemigration von Oracle zu Azure Database for PostgreSQL ausführen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Sie müssen [Oracle Database 11g Release 2 (Standard Edition, Standard Edition One oder Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html) herunterladen und installieren.
* Laden Sie die **HR**-Beispieldatenbank [hier](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002) herunter.
* Laden Sie ora2pg herunter, und installieren Sie das Tool unter [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) oder unter [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Erstellen einer Instanz in Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Stellen Sie eine Verbindung mit der Instanz her, und erstellen Sie eine Datenbank gemäß den Anweisungen in [diesem Dokument](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Erstellen Sie ein virtuelles Azure-Netzwerk (VNET) für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das über [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Site-to-Site-Konnektivität für Ihre lokalen Quellserver bereitstellt. Weitere Informationen zum Erstellen eines VNET finden Sie in der [Dokumentation zu Virtual Network](https://docs.microsoft.com/azure/virtual-network/) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

  > [!NOTE]
  > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des VNET-Setups die folgenden [Dienstendpunkte](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
  > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
  > * Speicherendpunkt
  > * Service Bus-Endpunkt
  >
  > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Stellen Sie sicher, dass die für Ihr VNet geltenden Regeln für Netzwerksicherheitsgruppen (NSG) die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service nicht blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem Azure-VNET finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den Oracle-Quellserver zugreifen kann (standardmäßig TCP-Port 1521).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
* Erstellen Sie für Azure Database for PostgreSQL eine [Firewallregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten VNet an.
* Aktivieren Sie den Zugriff auf die Oracle-Quelldatenbanken.

  > [!NOTE]
  > Damit ein Benutzer eine Verbindung mit der Oracle-Quelle herstellen kann, muss er über die DBA-Rolle verfügen.

  * Für die inkrementelle Synchronisierung in Azure Database Migration Service sind Archive Redo Logs erforderlich, um Datenänderungen zu erfassen. Führen Sie zum Konfigurieren der Oracle-Quelle die folgenden Schritte aus:
    * Melden Sie sich mit SYSDBA Berechtigungen an, indem Sie den folgenden Befehl ausführen:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Fahren Sie die Datenbankinstanz herunter, indem Sie den folgenden Befehl ausführen.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Warten Sie auf die Bestätigung `'ORACLE instance shut down'`.

    * Starten Sie die neue Instanz, und stellen Sie die Datenbank bereit (ohne sie jedoch zu öffnen), um die Archivierung zu aktivieren oder zu deaktivieren, indem Sie den folgenden Befehl ausführen:

      ```
      STARTUP MOUNT;
      ```

      Die Datenbank muss bereitgestellt werden. Warten Sie auf Bestätigung, dass die Oracle-Instanz gestartet wurde („Oracle instance started“).

    * Ändern Sie den Datenbankarchivierungsmodus, indem Sie den folgenden Befehl ausführen:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Öffnen Sie die Datenbank für normale Vorgänge, indem Sie den folgenden Befehl ausführen:

      ```
      ALTER DATABASE OPEN;
      ```

      Sie müssen möglicherweise einen Neustart ausführen, damit die ARC-Datei angezeigt wird.

    * Führen Sie zur Überprüfung den folgenden Befehl aus:

      ```
      SELECT log_mode FROM v$database;
      ```

      Sie sollten die Antwort `'ARCHIVELOG'` erhalten. Wenn die Antwort `'NOARCHIVELOG'` lautet, wurde die Anforderung nicht erfüllt.

  * Aktivieren Sie eine zusätzliche Protokollierung für die Replikation mit einer der folgenden Optionen.

    * **Option 1**:
      Ändern Sie die zusätzliche Protokollierung auf Datenbankebene, um alle Tabellen mit Primärschlüssel und eindeutigem Index abzudecken. Die Erkennungsabfrage gibt `'IMPLICIT'` zurück.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Ändern Sie die zusätzliche Protokollierung auf Tabellenebene. Führen Sie den Befehl nur für Tabellen aus, die eine Datenbearbeitung aufweisen und nicht über Primärschlüssel oder eindeutige Indizes verfügen.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Option 2**:
      Ändern Sie die zusätzliche Protokollierung auf Datenbankebene, um alle Tabellen abzudecken. Die Erkennungsabfrage gibt dann `'YES'` zurück.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Ändern Sie die zusätzliche Protokollierung auf Tabellenebene. Folgen Sie der nachstehenden Logik, um für jede Tabelle nur eine Anweisung auszuführen.

      Wenn die Tabelle über einen Primärschlüssel verfügt, führen Sie den folgenden Befehl aus:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Wenn die Tabelle über einen eindeutigen Index verfügt, führen Sie den folgenden Befehl aus:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      Führen Sie andernfalls den folgenden Befehl aus:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Führen Sie zur Überprüfung den folgenden Befehl aus:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Sie sollten die Antwort `'YES'` erhalten.

> [!IMPORTANT]
> Für die öffentliche Vorschauversion dieses Szenarios unterstützt Azure Database Migration Service die Oracle-Version 10g oder 11g. Kunden, die Oracle Database 12c oder eine höhere Version ausführen, müssen beachten, dass das minimale Authentifizierungsprotokoll, das für die Verbindung des ODBC-Treibers mit Oracle zulässig ist, Version 8 sein muss. Bei einer Oracle-Quelle der Version 12c (oder höher) müssen Sie das Authentifizierungsprotokoll wie folgt konfigurieren:
>
> * Aktualisieren Sie die Datei „sqlnet.ora“:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Starten Sie den Computer neu, damit die neuen Einstellungen wirksam werden.
> * Ändern Sie das Kennwort für vorhandene Benutzer:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Weitere Informationen finden Sie [hier](http://www.dba-oracle.com/t_allowed_login_version_server.htm).
>
> Denken Sie daran, dass sich die Änderung des Authentifizierungsprotokolls auf die Clientauthentifizierung auswirken kann.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Bewerten des Aufwands für eine Migration von Oracle zu Azure Database for PostgreSQL

Zum Bewerten des erforderlichen Aufwands für die Migration von Oracle zu Azure Database for PostgreSQL empfehlen wir die Verwendung von ora2pg. Verwenden Sie die Anweisung `ora2pg -t SHOW_REPORT`, um einen Bericht zu erstellen, in dem alle Oracle-Objekte, die geschätzten Migrationskosten (in Entwicklertagen) und bestimmte Datenbankobjekte aufgelistet werden, die im Rahmen der Konvertierung möglicherweise besondere Aufmerksamkeit erfordern.

Die meisten Kunden wenden sehr viel Zeit dafür auf, den Bewertungsbericht zu überprüfen und den Aufwand für die automatische und manuelle Konvertierung zu berücksichtigen.

Wenn Sie ora2pg zum Erstellen eines Bewertungsberichts konfigurieren und ausführen möchten, lesen Sie die Informationen im Abschnitt **Premigration: Assessment** (Prämigration: Bewertung) des Artikels [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) (Cookbook: Oracle zu Azure Database for PostgreSQL). [Hier](http://ora2pg.darold.net/report.html) finden Sie ein Beispiel für einen ora2pg-Bewertungsbericht zu Referenzzwecken.

## <a name="export-the-oracle-schema"></a>Exportieren des Oracle-Schemas

Zum Konvertieren des Oracle-Schemas und anderer Oracle-Objekte (wie Typen, Prozeduren, Funktionen usw.) in ein mit Azure Database for PostgreSQL kompatibles Schema empfehlen wir die Verwendung von ora2pg. ora2pg enthält viele Anweisungen, mit denen Sie bestimmte Datentypen vorab definieren können. Beispielsweise können Sie die Anweisung `DATA_TYPE` verwenden, um NUMBER(*,0) standardmäßig durch bigint statt NUMERIC(38) zu ersetzen.

Sie können ora2pg ausführen, um die einzelnen Datenbankobjekte in SQL-Dateien zu exportieren. Sie können dann die SQL-Dateien vor dem Importieren in Azure Database for PostgreSQL mithilfe von psql überprüfen, oder Sie können das SQL-Skript in PgAdmin ausführen.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Beispiel:

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Informationen zum Konfigurieren und Ausführen von ora2pg für die Schemakonvertierung finden Sie im Abschnitt **Migration: Schema & Data** des Whitepapers [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Einrichten des Schemas in Azure Database for PostgreSQL

Standardmäßig schreibt Oracle SCHEMA.TABLE.COLUMN (SCHEMA.TABELLE.SPALTE) in Großbuchstaben, während PostgreSQL schema.table.column (schema.tabelle.spalte) in Kleinbuchstaben schreibt. Damit Azure Database Migration Service die Datenverschiebung aus Oracle in Azure Database for PostgreSQL starten kann, muss schema.table.column (schema.tabelle.spalte) dieselbe Schreibweise wie in der Oracle-Quelle aufweisen.

Wenn die Oracle-Quelle beispielsweise das Schema „HR.EMPLOYEES.EMPLOYEE_ID“ verwendet, muss das PostgreSQL-Schema das gleiche Format aufweisen.

Um sicherzustellen, dass in Oracle und Azure Database for PostgreSQL dieselbe Schreibweise von schema.table.column (schema.tabelle.spalte) verwendet wird, empfehlen wir die Ausführung der folgenden Schritte.

> [!NOTE]
> Sie können einen anderen Ansatz verwenden, um das Großbuchstabenschema abzuleiten. Wir arbeiten an der Verbesserung und Automatisierung dieses Schritts.

1. Exportieren Sie Schemas mit ora2pg in Kleinbuchstaben. Erstellen Sie im SQL-Tabellenerstellungsskript manuell ein Schema mit der Großschreibung „SCHEMA“.
2. Importieren Sie die restlichen Oracle-Objekte (z. B. Trigger, Sequenzen, Prozeduren, Typen und Funktionen) in Azure Database for PostgreSQL.
3. Führen Sie das folgende Skript aus, damit TABLE (TABELLE) und COLUMN (SPALTE) in Großbuchstaben geschrieben werden:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Löschen Sie den Fremdschlüssel in der Zieldatenbank, damit der vollständige Ladevorgang ausgeführt wird. Im Abschnitt **Migrieren des Beispielschemas** des Artikels [Tutorial: Migrieren von PostgreSQL zu Azure Database for PostgreSQL (online) mit DMS](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) finden Sie ein Skript, mit dem Sie den Fremdschlüssel löschen können.
* Verwenden Sie Azure Database Migration Service, um den vollständigen Ladevorgang und die Synchronisierung auszuführen.
* Führen Sie in Azure Database Migration Service eine Datenbankübernahme aus, nachdem die Daten in der Azure Database for PostgreSQL-Zielinstanz mit der Quelle synchronisiert wurden.
* Führen Sie das folgende Skript aus, damit SCHEMA, TABLE (TABELLE) und COLUMN (SPALTE) in Kleinbuchstaben umgewandelt werden (sofern dieses Schema in Azure Database for PostgreSQL für die Anwendungsabfrage verwendet werden soll):

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Erstellen einer DMS-Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie ein vorhandenes VNet aus, oder erstellen Sie ein neues VNet.

    Das VNet bietet Azure Database Migration Service Zugriff auf die Oracle-Quellinstanz und die Azure Database for PostgreSQL-Zielinstanz.

    Weitere Informationen zum Erstellen eines VNet im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](https://aka.ms/DMSVnet).

5. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

    ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Suchen Sie im Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

    ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.
4. Geben Sie im Bereich **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **Oracle** aus, und wählen Sie dann im Textfeld **Typ des Zielservers** den Eintrag **Azure Database for PostgreSQL** aus.
5. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

   ![Erstellen eines Database Migration Service-Projekts](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

6. Wählen Sie **Speichern** aus, beachten Sie die Anforderungen für die erfolgreiche Verwendung von Azure Database Migration Service zum Durchführen einer Onlinemigration, und wählen Sie dann **Aktivität erstellen und ausführen** aus.

## <a name="specify-source-details"></a>Angeben von Quelldetails

* Geben Sie im Bereich **Quelldetails hinzufügen** die Verbindungsdetails für die Oracle-Quellinstanz an.

  ![Bildschirm „Quelldetails hinzufügen“](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Hochladen des Oracle OCI-Treibers

1. Wählen Sie **Speichern** aus, melden Sie sich auf dem Bildschirm **OCI-Treiber installieren** bei Ihrem Oracle-Konto an, und laden Sie dann den Treiber **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37,128,586 bytes) (cksum - 865082268) [hier](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst) herunter.
2. Laden Sie den Treiber in einen freigegebenen Ordner herunter.

   Stellen Sie sicher, dass der Ordner mit dem von Ihnen angegebenen Benutzernamen mit minimalem schreibgeschützten Zugriff freigegeben ist. Azure Database Migration Service greift auf die Freigabe zu und liest daraus, um den OCI-Treiber in Azure hochzuladen. Dabei erfolgt ein Identitätswechsel des von Ihnen angegebenen Benutzernamens.

   Bei dem angegebenen Benutzernamen muss es sich um ein Windows-Benutzerkonto handeln.

   ![Installation des OCI-Treibers](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern** aus, und geben Sie dann auf dem Bildschirm **Zieldetails** die Verbindungsdetails für den Azure Database for PostgreSQL-Zielserver an. Dabei handelt es sich um die vorab bereitgestellte Azure Database for PostgreSQL-Instanz, für die das **HR**-Schema bereitgestellt wurde.

    ![Bildschirm „Zieldetails“](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

  Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Ausgeführt** lautet.

     ![Aktivitätsstatus: Wird ausgeführt](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. Wählen Sie unter **Datenbankname** eine bestimmte Datenbank aus, um den Migrationsstatus für die Vorgänge **Vollständiger Datenladevorgang** und **Inkrementelle Datensynchronisierung** abzurufen.

    Unter „Full data load“ (Vollständiger Datenladevorgang) wird der Migrationsstatus des ersten Ladevorgangs und unter „Inkrementelle Datensynchronisierung“ der CDC-Status (Change Data Capture) angezeigt.

     ![Aktivitätsstatus: Vollständiger Ladevorgang abgeschlossen](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Aktivitätsstatus: Inkrementelle Datensynchronisierung](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

2. Achten Sie darauf, alle eingehenden Transaktionen für die Quelldatenbank zu beenden. Warten Sie, bis der Zähler **Ausstehende Änderungen** das Ergebnis **0** zeigt.

   ![Starten der Übernahme](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Aktivieren Sie das Kontrollkästchen **Bestätigen**, und klicken Sie anschließend auf **Anwenden**.
4. Wenn der Status der Datenbankmigration als **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure Database for PostgreSQL-Zielinstanz her.

 > [!NOTE]
 > Da PostgreSQL schema.table.column (schema.tabelle.spalte) standardmäßig in Kleinbuchstaben schreibt, können Sie das weiter oben in diesem Artikel im Abschnitt **Einrichten des Schemas in Azure Database for PostgreSQL** aufgeführte Skript verwenden, um die Großbuchstaben in Kleinbuchstaben umzuwandeln.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu Azure Database for PostgreSQL finden Sie im Artikel [Bekannte Probleme/Migrationseinschränkungen bei der Onlinemigration zu Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview).
* Weitere Informationen zu Azure Database for PostgreSQL finden Sie im Artikel [Was ist Azure Database for PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).
