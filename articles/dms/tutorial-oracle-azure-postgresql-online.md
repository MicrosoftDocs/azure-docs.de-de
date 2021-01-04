---
title: 'Tutorial: Onlinemigration von Oracle zu Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer lokal oder auf virtuellen Computern gehosteten Oracle-Instanz zu Azure Database for PostgreSQL ausführen.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/24/2020
ms.openlocfilehash: df789161bb9db8d49f069992600b5fcb4f78dd03
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006541"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Tutorial: Onlinemigration von Oracle zu Azure Database for PostgreSQL mit DMS (Vorschauversion)

Mit Azure Database Migration Service können Sie die Datenbanken von lokal oder auf virtuellen Computern gehosteten Oracle-Datenbanken mit minimaler Downtime zu [Azure Database for PostgreSQL](../postgresql/index.yml) migrieren. Das heißt, dass Sie die Migration mit minimaler Downtime für die Anwendung ausführen können. In diesem Tutorial migrieren Sie die **HR**-Beispieldatenbank von einer lokal oder auf virtuellen Computern gehosteten Oracle 11g-Instanz zu Azure Database for PostgreSQL. Zu diesem Zweck verwenden Sie die Onlinemigrationsaktivität in Azure Database Migration Service.

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
* Laden Sie ora2pg herunter, und [installieren Sie das Tool unter Windows oder unter Linux](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf).
* [Erstellen einer Instanz in Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md).
* Stellen Sie eine Verbindung mit der Instanz her, und erstellen Sie eine Datenbank gemäß den Anweisungen in [diesem Dokument](../postgresql/tutorial-design-database-using-azure-portal.md).
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

  > [!NOTE]
  > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
  >
  > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
  > * Speicherendpunkt
  > * Service Bus-Endpunkt
  >
  > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Stellen Sie sicher, dass die Regeln für Netzwerksicherheitsgruppen (NSGs) des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den Oracle-Quellserver zugreifen kann (standardmäßig TCP-Port 1521).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
* Erstellen Sie für Azure Database for PostgreSQL eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
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

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Bewerten des Aufwands für eine Migration von Oracle zu Azure Database for PostgreSQL

Zum Bewerten des erforderlichen Aufwands für die Migration von Oracle zu Azure Database for PostgreSQL empfehlen wir die Verwendung von ora2pg. Verwenden Sie die Anweisung `ora2pg -t SHOW_REPORT`, um einen Bericht zu erstellen, in dem alle Oracle-Objekte, die geschätzten Migrationskosten (in Entwicklertagen) und bestimmte Datenbankobjekte aufgelistet werden, die im Rahmen der Konvertierung möglicherweise besondere Aufmerksamkeit erfordern.

Die meisten Kunden wenden sehr viel Zeit dafür auf, den Bewertungsbericht zu überprüfen und den Aufwand für die automatische und manuelle Konvertierung zu berücksichtigen.

Wenn Sie ora2pg zum Erstellen eines Bewertungsberichts konfigurieren und ausführen möchten, lesen Sie die Informationen im Abschnitt **Premigration: Assessment** (Prämigration: Bewertung) des Artikels [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) (Cookbook: Oracle zu Azure Database for PostgreSQL). [Hier](https://ora2pg.darold.net/report.html) finden Sie ein Beispiel für einen ora2pg-Bewertungsbericht zu Referenzzwecken.

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

Sie können Oracle-Tabellenschemas, gespeicherte Prozeduren, Pakete und andere Datenbankobjekte mithilfe von ora2pg konvertieren, um sie mit Postgres kompatibel zu machen, bevor Sie eine Migrationspipeline in Azure Database Migration Service starten. Anleitungen für ora2pg finden unter den folgenden Links:

* [Installieren von ora2pg unter Windows](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows%20and%20Linux.pdf)
* [Cookbook für die Migration von Oracle zu Azure PostgreSQL](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf)

Azure Database Migration Service kann auch das PostgreSQL-Tabellenschema erstellen. Der Dienst greift auf das Tabellenschema in der verbundenen Oracle-Quelle zu und erstellt in Azure Database for PostgreSQL ein kompatibles Tabellenschema. Es empfiehlt sich, das Schemaformat in Azure Database for PostgreSQL zu überprüfen, nachdem Azure Database Migration Service das Schema erstellt und die Daten verschoben hat.

> [!IMPORTANT]
> Azure Database Migration Service erstellt nur das Tabellenschema. Andere Datenbankobjekte wie gespeicherte Prozeduren, Pakete, Indizes und Ähnliches werden nicht erstellt.

Löschen Sie außerdem den Fremdschlüssel in der Zieldatenbank, damit der vollständige Ladevorgang ausgeführt wird. Im Abschnitt **Migrieren des Beispielschemas** des Artikels [Tutorial: Migrieren von PostgreSQL zu Azure Database for PostgreSQL (online) mit DMS](./tutorial-postgresql-azure-postgresql-online.md) finden Sie ein Skript, mit dem Sie den Fremdschlüssel löschen können. Verwenden Sie Azure Database Migration Service, um den vollständigen Ladevorgang und die Synchronisierung auszuführen.

### <a name="when-the-postgresql-table-schema-already-exists"></a>Vorgehensweise bei bereits vorhandenem PostgreSQL-Tabellenschema

Falls Sie mithilfe von Tools wie ora2pg ein PostgreSQL-Schema erstellen, bevor Sie mit der Datenverschiebung mit Azure Database Migration Service beginnen, ordnen Sie in Azure Database Migration Service die Quelltabellen den Zieltabellen zu.

1. Wenn Sie ein neues Projekt für die Migration von Oracle zu Azure Database for PostgreSQL erstellen, werden Sie im Schritt „Schemas auswählen“ zum Auswählen von Zieldatenbank und Zielschema aufgefordert. Geben Sie die Zieldatenbank und das Zielschema an.

   ![Screenshot, der „Den Zieldatenbanken zuordnen“ zeigt.](media/tutorial-oracle-azure-postgresql-online/dms-map-to-target-databases.png)

2. Im Bildschirm **Migrationseinstellungen** wird eine Liste mit Tabellen in der Oracle-Quelle angezeigt. Von Azure Database Migration Service wird versucht, Quell- und Zieltabellen anhand des Tabellennamens abzugleichen. Sind mehrere übereinstimmende Zieltabellen mit unterschiedlicher Groß-/Kleinschreibung vorhanden, können Sie die zuzuordnende Zieltabelle auswählen.

    ![Screenshot, der „Migrationseinstellungen“ zeigt.](media/tutorial-oracle-azure-postgresql-online/dms-migration-settings.png)

> [!NOTE]
> Falls Sie Quelltabellennamen Tabellen mit abweichenden Namen zuordnen müssen, wenden Sie sich per E-Mail an [dmsfeedback@microsoft.com](mailto:dmsfeedbac@microsoft.com), um ein Automatisierungsskript für diesen Prozess zu erhalten.

### <a name="when-the-postgresql-table-schema-doesnt-exist"></a>Vorgehensweise bei nicht vorhandenem PostgreSQL-Tabellenschema

Wenn die PostgreSQL-Zieldatenbank keinerlei Tabellenschemainformationen enthält, konvertiert Azure Database Migration Service das Quellschema und bildet es in der Zieldatenbank nach. Zur Erinnerung: Azure Database Migration Service erstellt nur das Tabellenschema, aber keine anderen Datenbankobjekte wie gespeicherte Prozeduren, Pakete und Indizes.
Stellen Sie sicher, dass Ihre Zielumgebung ein Schema ohne vorhandene Tabellen enthält, damit Azure Database Migration Service das Schema für Sie erstellen kann. Wenn Azure Database Migration Service eine Tabelle erkennt, geht der Dienst davon aus, dass das Schema von einem externen Tool wie ora2pg erstellt wurde.

> [!IMPORTANT]
> Azure Database Migration Service erfordert, dass alle Tabellen auf die gleiche Weise erstellt werden – entweder unter Verwendung von Azure Database Migration Service oder mithilfe eines Tools wie ora2pg, aber nicht mit beidem.

Erste Schritte:

1. Erstellen Sie auf der Grundlage Ihrer Anwendungsanforderungen ein Schema in der Zieldatenbank. Die Namen von PostgreSQL-Tabellenschema und -spalten sind standardmäßig klein geschrieben. Das Oracle-Tabellenschema und die dazugehörigen Spalten liegen dagegen standardmäßig vollständig in Großbuchstaben vor.
2. Geben Sie im Schritt „Schemas auswählen“ die Zieldatenbank und das Zielschema an.
3. Basierend auf dem in Azure Database for PostgreSQL erstellten Schema verwendet Azure Database Migration Service die folgenden Transformationsregeln:

    Wenn der Schemaname in der Oracle-Quelle dem Schemanamen in Azure Database for PostgreSQL entspricht, *erstellt Azure Database Migration Service das Tabellenschema mit der gleichen Groß-/Kleinschreibung wie im Ziel*.

    Beispiel:

    | Quelle: Oracle-Schema | Ziel: PostgreSQL-Datenbank.Schema | Von DMS erstellt: Schema.Tabelle.Spalte |
    | ------------- | ------------- | ------------- |
    | HR | targetHR.public | public.countries.country_id |
    | HR | targetHR.trgthr | trgthr.countries.country_id |
    | HR | targetHR.TARGETHR | "TARGETHR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.HR | "HR"."COUNTRIES"."COUNTRY_ID" |
    | HR | targetHR.Hr | *Gemischte Groß-/Kleinschreibung kann nicht zugeordnet werden. |

    *Wenden Sie sich an [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com), wenn Sie Schema- und Tabellennamen im PostgreSQL-Ziel mit gemischter Groß-/Kleinschreibung erstellen möchten. Sie erhalten von uns ein Skript für die Einrichtung eines Tabellenschemas mit gemischter Groß-/Kleinschreibung in der PostgreSQL-Zieldatenbank.

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

4. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk bietet Azure Database Migration Service Zugriff auf die Oracle-Quellinstanz und die Azure Database for PostgreSQL-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

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
* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
* Weitere Informationen zu Azure Database for PostgreSQL finden Sie im Artikel [Was ist Azure Database for PostgreSQL?](../postgresql/overview.md).