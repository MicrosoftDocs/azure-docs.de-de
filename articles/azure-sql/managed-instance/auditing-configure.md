---
title: SQL Managed Instance-Überwachung
description: Lernen Sie die ersten Schritte bei der Azure SQL Managed Instance-Überwachung mit T-SQL kennen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 31a1169ca6c2194b8d5564e5d0df50116dd25084
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94505664"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Erste Schritte bei der Azure SQL Managed Instance-Überwachung
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Bei der [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)-Überwachung werden Datenbankereignisse nachverfolgt und in ein Überwachungsprotokoll in Ihrem Azure-Speicherkonto geschrieben. Die Überwachung ermöglicht außerdem Folgendes:

- Sie kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.
- Sie ermöglicht und unterstützt die Einhaltung von Standards, garantiert diese aber nicht. Weitere Informationen zu Azure-Programmen zur Unterstützung der Einhaltung von Standards finden Sie im [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). Dort ist die aktuelle Liste mit Compliancezertifizierungen enthalten.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Einrichten der Überwachung für Ihren Server in Azure Storage

Der folgende Abschnitt beschreibt die Konfiguration der Überwachung für Ihre verwaltete Instanz.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Erstellen Sie einen Azure Storage-**Container**, in dem die Überwachungsprotokolle gespeichert werden.

   1. Navigieren Sie zu dem Azure Storage-Konto, in dem Sie Ihre Überwachungsprotokolle speichern möchten.

      > [!IMPORTANT]
      > - Verwenden Sie ein Speicherkonto in derselben Region, in der sich auch die verwaltete Instanz befindet, um regionsübergreifende Lese-/Schreibvorgänge zu vermeiden. 
      > - Wenn sich das Speicherkonto hinter einem virtuellen Netzwerk oder einer Firewall befindet, finden Sie weitere Informationen unter [Gewähren von Zugriff aus einem virtuellen Netzwerk](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network).
      > - Wenn Sie die Beibehaltungsdauer von „0“ (unbegrenzte Aufbewahrung) in einen anderen Wert ändern, beachten Sie, dass die Beibehaltung nur auf Protokolle angewendet wird, die nach dem Ändern des Beibehaltungswerts geschrieben wurden (Protokolle, die in dem Zeitraum geschrieben wurden, in dem die Beibehaltung auf unbegrenzt festgelegt war, bleiben auch nach Aktivieren der Beibehaltung erhalten).

   1. Wechseln Sie im Speicherkonto zu **Übersicht**, und klicken Sie auf **Blobs**.

      ![Azure-Blob-Widget](./media/auditing-configure/1_blobs_widget.png)

   1. Klicken Sie im oberen Menü auf **+ Container**, um einen neuen Container zu erstellen.

      ![Symbol zum Erstellen eines Blobcontainers](./media/auditing-configure/2_create_container_button.png)

   1. Geben Sie einen **Namen** für den Container an, legen Sie den Wert für **Öffentliche Zugriffsebene** auf **Privat** fest, und klicken Sie dann auf **OK**.

      ![Symbol zum Erstellen einer Blobcontainerkonfiguration](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Kunden, die eine unveränderliche Protokollspeicherung für Überwachungsereignisse auf Server- oder Datenbankebene konfigurieren möchten, sollten den [Anweisungen in Azure Storage](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes) folgen. (Stellen Sie sicher, dass Sie beim Konfigurieren des unveränderlichen Blobspeichers **Weitere Anfügungen zulassen** ausgewählt haben.)
  
3. Nach dem Erstellen des Containers für die Überwachungsprotokolle gibt es zwei Möglichkeiten, ihn als Ziel für die Überwachungsprotokolle zu konfigurieren: [mit T-SQL](#blobtsql) oder [mithilfe der Benutzeroberfläche von SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>**Konfigurieren von Blobspeicher für Überwachungsprotokolle mit T-SQL:**

     1. Klicken Sie in der Liste der Container auf den neu erstellten Container und dann auf **Containereigenschaften**.

        ![Schaltfläche „Containereigenschaften“](./media/auditing-configure/4_container_properties_button.png)

     1. Kopieren Sie die Container-URL, indem Sie auf das Symbol „Kopieren“ klicken, und speichern Sie die URL für die zukünftige Verwendung (z.B. in Editor). Das Format der Container-URL sollte dem folgenden entsprechen: `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Kopieren der URL von Blobcontainern](./media/auditing-configure/5_container_copy_name.png)

     1. Generieren Sie ein Azure Storage-**SAS-Token**, das verwendet wird, um der Überwachung der verwalteten Instanz Zugriffsrechte für das Speicherkonto zu erteilen:

        - Navigieren Sie zu dem Azure Storage-Konto, in dem Sie im vorherigen Schritt den Container erstellt haben.

        - Klicken Sie im Menü **Speichereinstellungen** auf **Shared Access Signature**.

          ![Symbol „Shared Access Signature“ im Menü „Speichereinstellungen“](./media/auditing-configure/6_storage_settings_menu.png)

        - Konfigurieren Sie die SAS wie folgt:

          - **Zulässige Dienste:** Blob

          - **Startdatum:** Verwenden Sie zur Vermeidung von Zeitzonenproblemen das gestrige Datum.

          - **Enddatum:** Wählen Sie das Datum aus, an dem dieses SAS-Token abläuft.

            > [!NOTE]
            > Erneuern Sie das Token nach Ablauf, um Überwachungsfehler zu vermeiden.

          - Klicken Sie auf **SAS generieren**.

            ![SAS-Konfiguration](./media/auditing-configure/7_sas_configure.png)

        - Das SAS-Token wird unten angezeigt. Kopieren Sie das Token, indem Sie auf das Symbol „Kopieren“ klicken, und speichern Sie es für die zukünftige Verwendung (z.B. in Editor).

          ![Kopieren des SAS-Tokens](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Entfernen Sie das Fragezeichen („?“) am Anfang des Tokens.

     1. Stellen Sie über SQL Server Management Studio (SSMS) oder ein anderes unterstütztes Tool eine Verbindung mit Ihrer verwalteten Instanz her.

     1. Führen Sie die folgende T-SQL-Anweisung aus, um **neue Anmeldeinformationen zu erstellen**. Verwenden Sie dabei die Container-URL und das SAS-Token, das bzw. die Sie in den vorherigen Schritten erstellt haben:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Führen Sie die folgende T-SQL-Anweisung aus, um eine neue Serverüberwachung zu erstellen (wählen Sie einen eigenen Namen für die Überwachung aus, und verwenden Sie die Container-URL, die Sie in den vorherigen Schritten erstellt haben). Sofern nicht angegeben, wird für `RETENTION_DAYS` der Standardwert „0“ verwendet (unbegrenzte Aufbewahrungsdauer):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. Erstellen Sie als Nächstes eine [Spezifikation für die Serverüberwachung oder die Datenbanküberwachung](#createspec).

   - <a id="blobssms"></a>**Konfigurieren von Blobspeicher für Überwachungsprotokolle mit SQL Server Management Studio 18:**

     1. Stellen Sie mithilfe der Benutzeroberfläche von SQL Server Management Studio (SSMS) eine Verbindung mit der verwalteten Instanz her.

     1. Erweitern Sie den Stammknoten im Objekt-Explorer.

     1. Erweitern Sie den Knoten **Sicherheit**, klicken Sie mit der rechten Maustaste auf den Knoten **Überwachungen**, und klicken Sie auf **Neue Überwachung**:

        ![Erweitern des Knotens „Sicherheit und Überwachung“](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Vergewissern Sie sich, dass **URL** in **Überwachungsziel** ausgewählt ist, und klicken Sie auf **Durchsuchen**:

        ![Durchsuchen von Azure Storage](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (Optional:) Melden Sie sich bei Ihrem Azure-Konto an:

        ![Anmelden bei Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Wählen Sie in den Dropdownlisten ein Abonnement, ein Speicherkonto und einen Blobcontainer aus, oder erstellen Sie einen eigenen Container, indem Sie auf **Erstellen** klicken. Wenn Sie fertig sind, klicken Sie auf **OK**:

        ![Auswählen von Azure-Abonnement, Speicherkonto und Blobcontainer](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Klicken Sie im Dialogfeld **Überwachung erstellen** auf **OK**.
     
     1. <a id="createspec"></a>Erstellen und aktivieren Sie nach dem Konfigurieren des Blobcontainers als Ziel für die Überwachungsprotokolle eine Spezifikation für die Serverüberwachung oder die Datenbanküberwachung. Die Vorgehensweise entspricht der für SQL Server:

   - [Erstellen einer Spezifikation für die Serverüberwachung – T-SQL-Anleitung](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Erstellen einer Spezifikation für die Datenbanküberwachung – T-SQL-Anleitung](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Aktivieren Sie die Serverüberwachung, die Sie in Schritt 3 erstellt haben:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Weitere Informationen:

- [Überwachungsunterschiede zwischen Datenbanken in Azure SQL Managed Instance und Datenbanken in SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Einrichten der Überwachung für Ihren Server in Event Hubs oder Azure Monitor-Protokollen

Überwachungsprotokolle einer verwalteten Instanz können an Event Hubs oder Azure Monitor-Protokolle gesendet werden. In diesem Abschnitt wird die zugehörige Konfiguration beschrieben:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zur verwalteten Instanz.

2. Klicken Sie auf **Diagnoseeinstellungen**.

3. Klicken Sie auf **Diagnose aktivieren**. Wenn die Diagnose bereits aktiviert wurde, wird stattdessen **+ Diagnoseeinstellung hinzufügen** angezeigt.

4. Wählen Sie in der Liste der Protokolle **SQLSecurityAuditEvents** aus.

5. Wählen Sie ein Ziel für die Überwachungsereignisse aus: Event Hubs, Azure Monitor-Protokolle oder beides. Konfigurieren Sie für jedes Ziel die erforderlichen Parameter (z.B. den Log Analytics-Arbeitsbereich).

6. Klicken Sie auf **Speichern**.

    ![Konfigurieren von Diagnoseeinstellungen](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Stellen Sie mit **SQL Server Management Studio (SSMS)** oder einem anderen unterstützten Client eine Verbindung mit der verwalteten Instanz her.

8. Führen Sie die folgende T-SQL-Anweisung aus, um eine Serverüberwachung zu erstellen:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Erstellen und aktivieren Sie eine Spezifikation für die Serverüberwachung oder die Datenbanküberwachung wie für SQL Server:

   - [Erstellen einer Spezifikation für die Serverüberwachung – T-SQL-Anleitung](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Erstellen einer Spezifikation für die Datenbanküberwachung – T-SQL-Anleitung](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Aktivieren Sie die Serverüberwachung, die Sie in Schritt 8 erstellt haben:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Verwenden von Überwachungsprotokollen

### <a name="consume-logs-stored-in-azure-storage"></a>Verwenden von Protokollen, die in Azure Storage gespeichert sind

Es gibt verschiedene Methoden zum Anzeigen von Blobüberwachungsprotokollen.

- Verwenden Sie die Systemfunktion `sys.fn_get_audit_file` (T-SQL), um die Daten der Überwachungsprotokolle im Tabellenformat zurückzugeben. Weitere Informationen zur Verwendung dieser Funktion finden Sie in der [Dokumentation zu „sys.fn_get_audit_file“](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Sie können Überwachungsprotokolle mithilfe eines Tools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) untersuchen. In Azure Storage werden Überwachungsprotokolle als Sammlung von Blobdateien in einem Container gespeichert, der als Speicher für die Überwachungsprotokolle definiert wurde. Weitere Informationen zur Hierarchie des Speicherordners, zu Namenskonventionen und zum Protokollformat finden Sie in der [Formatreferenz für Blobüberwachungsprotokolle](../database/audit-log-format.md).

- Eine vollständige Liste der Nutzungsmethoden für Überwachungsprotokolle finden Sie unter [Erste Schritte bei der Azure SQL-Datenbank-Überwachung](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Nutzen von Protokollen, die in Event Hubs gespeichert sind

Um die Überwachungsprotokolldaten aus Event Hubs zu nutzen, müssen Sie einen Stream zum Nutzen von Ereignissen einrichten und diese in ein Ziel schreiben. Weitere Informationen finden Sie in der Dokumentation zu Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Nutzen und Analysieren von Protokollen, die in Azure Monitor-Protokolle gespeichert sind

Wenn die Überwachungsprotokolle in Azure Monitor-Protokolle geschrieben werden, stehen sie im Log Analytics-Arbeitsbereich bereit. Sie können dort erweiterte Suchvorgänge über die Überwachungsdaten ausführen. Navigieren Sie zunächst zum Log Analytics-Arbeitsbereich. Klicken Sie im Abschnitt **Allgemein** auf **Protokolle**, und geben Sie eine einfache Abfrage wie z. B. `search "SQLSecurityAuditEvents"` ein, um die Überwachungsprotokolle anzuzeigen.  

Mithilfe integrierter Suchfunktionen und benutzerdefinierter Dashboards bieten Azure Monitor-Protokolle Ihnen in Echtzeit Erkenntnisse zu Betriebsabläufen, sodass Sie Millionen von Datensätzen für alle Ihre Workloads und Server analysieren können. Weitere nützliche Informationen zur Suchsprache und den Befehlen in Azure Monitor-Protokolle finden Sie unter [Referenz zur Suche in Azure Monitor-Protokolle](../../azure-monitor/log-query/log-query-overview.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Überwachungsunterschiede zwischen Datenbanken in Azure SQL Managed Instance und Datenbanken in SQL Server

Die wichtigsten Unterschiede zwischen der Überwachung in Datenbanken in Azure SQL Managed Instance und Datenbanken in SQL Server sind folgende:

- Bei Azure SQL Managed Instance wird die Überwachung auf Serverebene ausgeführt, und Protokolldateien mit der Endung `.xel` werden in Azure Blob Storage gespeichert.
- In SQL Server erfolgt die Überwachung auf Serverebene, aber Ereignisse werden in Dateisystemprotokollen bzw. Windows-Ereignisprotokollen gespeichert.

Die XEvent-Überwachung in verwalteten Instanzen unterstützt Azure Blob Storage-Ziele. Dateiprotokolle und Windows-Protokolle werden **nicht unterstützt**.

Wichtigste Unterschiede in der Syntax von `CREATE AUDIT` zur Überwachung in Azure Blob Storage:

- Mit der neuen Syntax `TO URL` können Sie die URL des Azure Blob Storage-Containers angeben, in dem Dateien mit der Endung `.xel` platziert werden.
- Die neue Syntax `TO EXTERNAL MONITOR` wird bereitgestellt, um Ziele in Event Hubs und Azure Monitor-Protokolle verwenden zu können.
- Die Syntax `TO FILE` wird **nicht unterstützt**, da Azure SQL Managed Instance nicht auf Windows-Dateifreigaben zugreifen kann.
- Die Option zum Herunterfahren wird **nicht unterstützt**.
- Der Wert „0“ für `queue_delay` wird **nicht unterstützt**.

## <a name="next-steps"></a>Nächste Schritte

- Eine vollständige Liste der Nutzungsmethoden für Überwachungsprotokolle finden Sie unter [Erste Schritte bei der Azure SQL-Datenbank-Überwachung](../../azure-sql/database/auditing-overview.md).
- Weitere Informationen zu Azure-Programmen zur Unterstützung der Einhaltung von Standards finden Sie im [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942). Dort ist die aktuelle Liste mit Compliancezertifizierungen enthalten.

<!--Image references-->
