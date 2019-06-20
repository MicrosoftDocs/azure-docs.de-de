---
title: 'Verwaltete Azure SQL-Datenbank-Instanz: Überwachung | Microsoft-Dokumentation'
description: Lernen Sie die ersten Schritte bei der Überwachung von verwalteten Azure SQL-Datenbank-Instanzen mit T-SQL kennen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 6ada2a5e505bfe37f4f9a956570d8b6f38f55e55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702863"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Erste Schritte bei der Überwachung von verwalteten Azure SQL-Datenbank-Instanzen

Die [Überwachung verwalteter Instanzen](sql-database-managed-instance.md) verfolgt Datenbankereignisse nach und schreibt sie in ein Überwachungsprotokoll in Ihrem Azure-Speicherkonto. Die Überwachung ermöglicht außerdem Folgendes:

- Sie kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.
- Sie ermöglicht und unterstützt die Einhaltung von Standards, garantiert diese aber nicht. Weitere Informationen zu Azure-Programmen zur Unterstützung bei der Einhaltung von Standards finden Sie im [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliancezertifizierungen für SQL-Datenbank angezeigt wird.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Einrichten der Überwachung für Ihren Server in Azure Storage

Der folgende Abschnitt beschreibt die Konfiguration der Überwachung für Ihre verwaltete Instanz.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Erstellen Sie einen Azure Storage-**Container**, in dem die Überwachungsprotokolle gespeichert werden.

   1. Navigieren Sie zu der Azure Storage-Instanz, in der Sie Ihre Überwachungsprotokolle speichern möchten.

      > [!IMPORTANT]
      > Verwenden Sie ein Speicherkonto in derselben Region, in der sich auch die verwaltete Instanz befindet, um regionsübergreifende Lese-/Schreibvorgänge zu vermeiden.

   1. Wechseln Sie im Speicherkonto zu **Übersicht**, und klicken Sie auf **Blobs**.

      ![Azure-Blob-Widget](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. Klicken Sie im oberen Menü auf **+ Container**, um einen neuen Container zu erstellen.

      ![Erstellen eines Blobcontainersymbols](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Geben Sie einen **Namen** für den Container an, legen Sie die öffentliche Zugriffsebene auf **Privat** fest, und klicken Sie dann auf **OK**.

      ![Erstellen einer Blobcontainerkonfiguration](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Nach dem Erstellen des Containers für die Überwachungsprotokolle gibt es zwei Möglichkeiten, ihn als Ziel für die Überwachungsprotokolle zu konfigurieren: [mit T-SQL](#blobtsql) oder [mithilfe der Benutzeroberfläche von SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Konfigurieren von Blob Storage für Überwachungsprotokolle mit T-SQL:

     1. Klicken Sie in der Liste der Container auf den neu erstellten Container und dann auf **Containereigenschaften**.

        ![Schaltfläche „Containereigenschaften“](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kopieren Sie die Container-URL, indem Sie auf das Symbol „Kopieren“ klicken, und speichern Sie die URL für die zukünftige Verwendung (z.B. in Editor). Das Format der Container-URL sollte dem folgenden entsprechen: `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Kopieren der URL von Blobcontainern](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Generieren Sie ein Azure Storage-**SAS-Token**, das verwendet wird, um der Überwachung der verwalteten Instanz Zugriffsrechte für das Speicherkonto zu erteilen:

        - Navigieren Sie zu dem Azure Storage-Konto, in dem Sie im vorherigen Schritt den Container erstellt haben.

        - Klicken Sie in Storage im Menü „Einstellungen“ auf **Shared Access Signature**.

          ![Symbol „Shared Access Signature“ in Storage im Menü „Einstellungen“](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Konfigurieren Sie die SAS wie folgt:

          - **Zulässige Dienste:** Blob

          - **Startdatum:** Zur Vermeidung von Zeitzonenproblemen wird empfohlen, das gestrige Datum zu verwenden.

          - **Enddatum:** Wählen Sie das Datum aus, an dem dieses SAS-Token abläuft.

            > [!NOTE]
            > Erneuern Sie das Token nach Ablauf, um Überwachungsfehler zu vermeiden.

          - Klicken Sie auf **SAS generieren**.
            
            ![SAS-Konfiguration](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Nach dem Klicken auf „SAS generieren“ wird das SAS-Token unten angezeigt. Kopieren Sie das Token, indem Sie auf das Symbol „Kopieren“ klicken, und speichern Sie es für die zukünftige Verwendung (z.B. in Editor).

          ![Kopieren des SAS-Tokens](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Entfernen Sie das Fragezeichen („?“) am Anfang des Tokens.

     1. Stellen Sie mit SQL Server Management Studio (SSMS) oder einem anderen unterstützten Tool eine Verbindung mit der verwalteten Instanz her.

     1. Führen Sie die folgende T-SQL-Anweisung aus, um **neue Anmeldeinformationen zu erstellen**. Verwenden Sie dabei die Container-URL und das SAS-Token, die Sie in den vorherigen Schritten erstellt haben:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Führen Sie die folgende T-SQL-Anweisung aus, um eine neue Serverüberwachung zu erstellen (wählen Sie einen eigenen Namen für die Überwachung aus, und verwenden Sie die Container-URL, die Sie in den vorherigen Schritten erstellt haben). Wenn keine Angabe erfolgt, hat `RETENTION_DAYS` den Standardwert „0“ (unbegrenzte Aufbewahrungsdauer).

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Erstellen Sie als Nächstes eine [Spezifikation für die Serverüberwachung oder die Datenbanküberwachung](#createspec).

   - <a id="blobssms"></a>Konfigurieren von Blob Storage für Überwachungsprotokolle mit SQL Server Management Studio (SSMS) 18 (Vorschauversion):

     1. Verbinden Sie die verwaltete Instanz mithilfe der Benutzeroberfläche von SQL Server Management Studio (SSMS).

     1. Erweitern Sie den Stammknoten im Objekt-Explorer.

     1. Erweitern Sie den Knoten **Sicherheit**, klicken Sie mit der rechten Maustaste auf den Knoten **Überwachungen**, und klicken Sie auf „Neue Überwachung“:

        ![Erweitern des Knotens „Sicherheit und Überwachung“](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Vergewissern Sie sich, dass „URL“ in **Überwachungsziel** ausgewählt ist, und klicken Sie auf **Durchsuchen**:

        ![Durchsuchen von Azure Storage](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Optional:) Melden Sie sich bei Ihrem Azure-Konto an:

        ![Anmelden bei Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Wählen Sie in den Dropdownlisten ein Abonnement, ein Speicherkonto und einen Blobcontainer aus, oder erstellen Sie einen eigenen Container, indem Sie auf **Erstellen** klicken. Wenn Sie fertig sind, klicken Sie auf **OK**:

        ![Auswählen von Azure-Abonnement, Speicherkonto und Blobcontainer](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klicken Sie im Dialogfeld „Überwachung erstellen“ auf **OK**.

1. <a id="createspec"></a>Erstellen Sie nach dem Konfigurieren des Blobcontainers als Ziel für die Überwachungsprotokolle eine Spezifikation für die Serverüberwachung oder die Datenbanküberwachung. Die Vorgehensweise entspricht der für SQL Server:

   - [Erstellen einer Spezifikation für die Serverüberwachung – T-SQL-Anleitung](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Erstellen einer Spezifikation für die Datenbanküberwachung – T-SQL-Anleitung](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Aktivieren Sie die Serverüberwachung, die Sie in Schritt 6 erstellt haben:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Weitere Informationen:

- [Überwachungsunterschiede zwischen Einzeldatenbanken, Pools für elastische Datenbanken und verwaltete Instanzen in Azure SQL-Datenbank und Datenbanken in SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Einrichten der Überwachung für Ihren Server in Event Hubs oder Azure Monitor-Protokolle

Überwachungsprotokolle einer verwalteten Instanz können an Event Hubs oder Azure Monitor-Protokolle gesendet werden. In diesem Abschnitt wird die zugehörige Konfiguration beschrieben:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zur verwalteten Instanz.

2. Klicken Sie auf **Diagnoseeinstellungen**.

3. Klicken Sie auf **Diagnose aktivieren**. Wenn die Diagnose bereits aktiviert wurde, wird stattdessen *+ Diagnoseeinstellung hinzufügen* angezeigt.

4. Wählen Sie in der Liste der Protokolle **SQLSecurityAuditEvents** aus.

5. Wählen Sie ein Ziel für die Überwachungsereignisse aus: Event Hubs, Azure Monitor-Protokolle oder beide. Konfigurieren Sie für jedes Ziel die erforderlichen Parameter (z.B. den Log Analytics-Arbeitsbereich).

6. Klicken Sie auf **Speichern**.

    ![Konfigurieren von Diagnoseeinstellungen](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Stellen Sie mit **SQL Server Management Studio (SSMS)** oder einem anderen unterstützten Client eine Verbindung mit der verwalteten Instanz her.

8. Führen Sie die folgende T-SQL-Anweisung aus, um eine Serverüberwachung zu erstellen:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Erstellen Sie eine Spezifikation für die Serverüberwachung oder die Datenbanküberwachung wie für SQL Server:

   - [Erstellen einer Spezifikation für die Serverüberwachung – T-SQL-Anleitung](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Erstellen einer Spezifikation für die Datenbanküberwachung – T-SQL-Anleitung](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Aktivieren Sie die Serverüberwachung, die Sie in Schritt 8 erstellt haben:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Verwenden von Überwachungsprotokollen

### <a name="consume-logs-stored-in-azure-storage"></a>Verwenden von Protokollen, die in Azure Storage gespeichert sind

Es gibt verschiedene Methoden zum Anzeigen von Blobüberwachungsprotokollen.

- Verwenden Sie die Systemfunktion `sys.fn_get_audit_file` (T-SQL), um die Daten der Überwachungsprotokolle im Tabellenformat zurückzugeben. Weitere Informationen zur Verwendung dieser Funktion finden Sie in der [Dokumentation zu „sys.fn_get_audit_file“](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Sie können Überwachungsprotokolle mithilfe eines Tools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) untersuchen. In Azure Storage werden Überwachungsprotokolle als Sammlung von Blobdateien in einem Container gespeichert, der als Speicher für die Überwachungsprotokolle definiert wurde. Weitere Informationen zur Hierarchie des Speicherordners, zu Namenskonventionen und zum Protokollformat finden Sie in der [Formatreferenz für Blobüberwachungsprotokolle](https://go.microsoft.com/fwlink/?linkid=829599).

- Eine vollständige Liste der Verbrauchsmethoden für Überwachungsprotokolle finden Sie unter [Erste Schritte bei der Überwachung von SQL-Datenbank](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Verwenden von Protokollen, die in Event Hubs gespeichert sind

Um die Überwachungsprotokolldaten von Event Hub zu nutzen, müssen Sie einen Stream zum Nutzen von Ereignissen einrichten und diese in ein Ziel schreiben. Weitere Informationen finden Sie in der Dokumentation zu Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Verwenden und Analysieren von Protokollen, die in Azure Monitor-Protokolle gespeichert sind

Wenn die Überwachungsprotokolle in Azure Monitor-Protokolle geschrieben werden, stehen sie im Log Analytics-Arbeitsbereich bereit. Sie können dort erweiterte Suchvorgänge über die Überwachungsdaten ausführen. Navigieren Sie zunächst zum Log Analytics-Arbeitsbereich, klicken Sie im Abschnitt *Allgemein* auf *Protokolle*, und geben Sie eine einfache Abfrage ein, z. B. `search "SQLSecurityAuditEvents"`, um die Überwachungsprotokolle anzuzeigen.  

Mithilfe integrierter Suchfunktionen und benutzerdefinierter Dashboards bieten Azure Monitor-Protokolle Ihnen in Echtzeit Erkenntnisse zu Betriebsabläufen, sodass Sie Millionen von Datensätzen für alle Ihre Workloads und Server analysieren können. Weitere nützliche Informationen zur Suchsprache und den Befehlen in Azure Monitor-Protokolle finden Sie unter [Referenz zur Suche in Azure Monitor-Protokolle](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Überwachungsunterschiede zwischen Datenbanken in Azure SQL-Datenbank und Datenbanken in SQL Server

Die wichtigsten Unterschiede zwischen der Überwachung in Datenbanken in Azure SQL-Datenbank und SQL Server:

- Bei der Bereitstellungsoption „Verwaltete Instanz“ in Azure SQL-Datenbank wird die Überwachung auf Serverebene ausgeführt. Die Protokolldateien mit der Endung `.xel` werden in Azure Blob Storage gespeichert.
- Bei Bereitstellungen als Singleton und in Pools für elastische Datenbanken in Azure SQL-Datenbank erfolgt die Überwachung auf Datenbankebene.
- Auf lokalen oder virtuellen SQL Server-Computern wird die Überwachung auf Serverebene ausgeführt, Ereignisse werden jedoch in Dateisystemprotokollen bzw. Windows-Ereignisprotokollen gespeichert.

Die XEvent-Überwachung in einer verwalteten Instanz unterstützt Azure Blob Storage-Ziele. Dateiprotokolle und Windows-Protokolle werden **nicht unterstützt**.

Wichtigste Unterschiede in der Syntax von `CREATE AUDIT` zur Überwachung in Azure Blob Storage:

- Mit der neuen Syntax `TO URL` können Sie die URL des Azure Blob Storage-Containers angeben, in dem Dateien mit der Endung `.xel` gespeichert werden.
- Die neue Syntax `TO EXTERNAL MONITOR` wird bereitgestellt, um Ziele in Event Hubs und Azure Monitor-Protokolle zu ermöglichen.
- Die Syntax `TO FILE` wird **nicht unterstützt**, da SQL-Datenbank nicht auf Windows-Dateifreigaben zugreifen kann.
- Die Option zum Herunterfahren wird **nicht unterstützt**.
- Der Wert „0“ für `queue_delay` wird **nicht unterstützt**.

## <a name="next-steps"></a>Nächste Schritte

- Eine vollständige Liste der Verbrauchsmethoden für Überwachungsprotokolle finden Sie unter [Erste Schritte bei der Überwachung von SQL-Datenbank](sql-database-auditing.md).
- Weitere Informationen zu Azure-Programmen zur Unterstützung bei der Einhaltung von Standards finden Sie im [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), wo die aktuellste Liste von Compliancezertifizierungen für SQL-Datenbank angezeigt wird.

<!--Image references-->









