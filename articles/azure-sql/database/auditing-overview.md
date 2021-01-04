---
title: Azure SQL-Überwachung für Azure SQL-Datenbank und Azure Synapse Analytics
description: Verwenden von Azure SQL-Datenbank-Überwachung, um Datenbankereignisse in einem Überwachungsprotokoll nachzuverfolgen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 11/08/2020
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: b09eb03994098f8cb68033f3c42309a77e15f91c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620990"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Überwachen von Azure SQL-Datenbank und Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Die Überwachung von [Azure SQL-Datenbank](sql-database-paas-overview.md) und [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) verfolgt Datenbankereignisse und schreibt sie in ein Überwachungsprotokoll in Ihrem Azure Storage-Konto, Ihrem Log Analytics-Arbeitsbereich oder in Event Hubs.

Die Überwachung ermöglicht außerdem Folgendes:

- Sie kann Ihnen dabei helfen, die gesetzlichen Bestimmungen einzuhalten, die Datenbankaktivität zu verstehen und Einblicke in Abweichungen und Anomalien zu erhalten, die auf geschäftsspezifische Bedenken oder mutmaßliche Sicherheitsverstöße hinweisen können.

- Sie ermöglicht und unterstützt die Einhaltung von Standards, garantiert diese aber nicht. Weitere Informationen zu Azure-Programmen zur Unterstützung bei der Einhaltung von Standards finden Sie im [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), in dem Sie auch die aktuelle Liste von Compliancezertifizierungen für Azure SQL-Datenbank anzeigen können.

> [!NOTE]
> Informationen zur Überwachung in Azure SQL Managed Instance finden Sie im Artikel [Erste Schritte mit der Überwachung in SQL Managed Instance](../managed-instance/auditing-configure.md).

## <a name="overview"></a><a id="overview"></a>Übersicht

Sie können die SQL-Datenbanküberwachung für folgende Zwecke verwenden:

- **Beibehalten** eines Überwachungspfads von ausgewählten Ereignissen. Sie können Kategorien für zu protokollierende Datenbankaktionen und -ereignisse konfigurieren.
- **Melden** von Datenbankaktivitäten. Sie können vorkonfigurierte Berichte und ein Dashboard verwenden, um schnell mit der Berichterstattung über Aktivitäten und Ereignisse zu beginnen.
- **Analysieren** von Berichten. Sie können nach verdächtigen Ereignissen, ungewöhnliche Aktivitäten und Trends suchen.

> [!IMPORTANT]
> Die Überwachung für Azure SQL-Datenbank und Azure Synapse ist für Verfügbarkeit und Leistung optimiert. Bei sehr hoher Aktivität oder hoher Netzwerkauslastung erlauben Azure SQL-Datenbank und Azure Synapse das Fortsetzen von Vorgängen und zeichnen einige überwachte Ereignisse möglicherweise nicht auf.

### <a name="auditing-limitations"></a>Einschränkungen der Überwachung

- **Storage Premium** wird derzeit **nicht unterstützt**.
- Der **hierarchische Namespace** für ein **Azure Data Lake Storage Gen2-Speicherkonto** wird derzeit **nicht unterstützt**.
- Das Aktivieren der Überwachung für eine angehaltene **Azure Synapse**-Instanz wird nicht unterstützt. Setzen Sie Azure Synapse fort, um die Überwachung zu aktivieren.

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>Definieren von Überwachungsrichtlinien auf Serverebene und auf Datenbankebene

Eine Überwachungsrichtlinie kann für eine spezifische Datenbank oder als [Standardserverrichtlinie](logical-servers.md) in Azure (dem Host von SQL-Datenbank oder Azure Synapse) definiert werden:

- Eine Serverrichtlinie wird auf alle vorhandenen und neu erstellten Datenbanken auf dem Server angewendet.

- Wenn die *Serverüberwachung aktiviert ist*, gilt sie *immer für die Datenbank*. Die Datenbank wird unabhängig von den Datenbanküberwachungseinstellungen überwacht.

- Die Aktivierung der Überwachung in der Datenbank sowie auf dem Server hebt die Einstellungen der Serverüberwachung *nicht* auf und ändert sie nicht. Beide Überwachungen existieren nebeneinander. Das heißt, die Datenbank wird zweimal parallel überwacht: einmal anhand der Serverrichtlinie und einmal anhand der Datenbankrichtlinie.

   > [!NOTE]
   > Aktivieren Sie die Serverüberwachung und die Datenbankblobüberwachung nicht zusammen, es sei denn:
    >
    > - Sie möchten ein anderes *Speicherkonto*, eine andere *Beibehaltungsdauer* oder einen anderen *Log Analytics-Arbeitsbereich* für eine bestimmte Datenbank verwenden.
    > - Sie möchten andere Ereignistypen oder Kategorien für eine bestimmte Datenbank überwachen als die, die für die restlichen Datenbanken auf dem Server überwacht werden. Beispielsweise sollen Tabelleneinfügungen nur für eine bestimmte Datenbank überwacht werden.
   >
   > Andernfalls wird empfohlen, die Überwachung nur auf Serverebene zu aktivieren und die Überwachung auf Datenbankebene für alle Datenbanken deaktiviert zu lassen.

#### <a name="remarks"></a>Bemerkungen

- Überwachungsprotokolle werden in Ihrem Azure-Abonnement als **Anfügeblobs** in Azure Blob Storage geschrieben.
- Überwachungsprotokolle liegen im XEL-Format vor und können mithilfe von [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) geöffnet werden.
- Wenn Sie eine unveränderliche Protokollspeicherung für Überwachungsereignisse auf Server- oder Datenbankebene konfigurieren möchten, sollten Sie der [von Azure Storage bereitgestellten Anleitung](../../storage/blobs/storage-blob-immutability-policies-manage.md#enabling-allow-protected-append-blobs-writes) folgen. Stellen Sie sicher, dass Sie beim Konfigurieren des unveränderlichen Blobspeichers **Weitere Anfügungen zulassen** ausgewählt haben.
- Sie können Überwachungsprotokolle in ein Azure Storage-Konto hinter einem VNET oder einer Firewall schreiben. Spezielle Anweisungen finden Sie unter [Schreiben von Überwachungsprotokollen in ein Speicherkonto hinter einem VNET oder einer Firewall](audit-write-storage-account-behind-vnet-firewall.md).
- Informationen zum Protokollformat, zur Hierarchie des Speicherordners und zu Namenskonventionen finden Sie in der [Formatreferenz für Blobüberwachungsprotokolle](./audit-log-format.md).
- Die Überwachung von [schreibgeschützten Replikaten](read-scale-out.md) wird automatisch aktiviert. Weitere Informationen zur Hierarchie der Speicherordner, zu Namenskonventionen und zum Protokollformat finden Sie unter [Format für SQL-Datenbank-Überwachungsprotokolle](audit-log-format.md).
- Wenn Sie die Azure AD-Authentifizierung verwenden, werden Datensätze zu Anmeldungsfehlern im SQL-Überwachungsprotokoll *nicht* angezeigt. Um Überwachungsdatensätze zu Fehlern bei der Anmeldung anzuzeigen, müssen Sie das [Azure Active Directory-Portal](../../active-directory/reports-monitoring/reference-sign-ins-error-codes.md) verwenden, in dem Details zu diesen Ereignissen protokolliert werden.
- Anmeldungen werden vom Gateway an die bestimmte Instanz weitergeleitet, in der sich die Datenbank befindet.  Im Fall von AAD-Anmeldungen erfolgt die Überprüfung der Anmeldeinformationen vor dem Versuch, den Benutzer für die Anmeldung bei der angeforderten Datenbank zu verwenden.  Bei einem Fehler wird nie auf die angeforderte Datenbank zugegriffen, sodass keine Überwachung erfolgt.  Bei SQL-Anmeldungen werden die Anmeldeinformationen für die angeforderten Daten überprüft, sodass sie in diesem Fall überwacht werden können.  Erfolgreiche Anmeldungen, die die Datenbank offensichtlich erreichen, werden in beiden Fällen überwacht.
- Nachdem Sie Ihre Überwachungseinstellungen konfiguriert haben, können Sie das neue Feature der Bedrohungserkennung aktivieren und die E-Mail-Konten konfigurieren, an die Sicherheitswarnungen gesendet werden sollen. Mit der Bedrohungserkennung können Sie proaktive Warnungen bei anomalen Datenbankaktivitäten erhalten, die auf mögliche Sicherheitsbedrohungen hinweisen können. Weitere Informationen finden Sie unter [Erste Schritte mit der Bedrohungserkennung](threat-detection-overview.md).

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>Einrichten der Überwachung für Ihren Server

Die Standardüberwachungsrichtlinie enthält alle Aktionen sowie den folgenden Satz von Aktionsgruppen, mit denen alle Abfragen und gespeicherten Prozeduren, die für die Datenbank ausgeführt werden, sowie erfolgreiche und fehlerhafte Anmeldungen überwacht werden:
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
Sie können die Überwachung für verschiedene Arten von Aktionen und Aktionsgruppen mithilfe von PowerShell konfigurieren, wie im Abschnitt [Verwalten der Überwachung von SQL-Datenbank mithilfe von Azure PowerShell](#manage-auditing) beschrieben.

Die Überwachung von Azure SQL-Datenbank und Azure Synapse speichert 4.000 Datenzeichen für Zeichenfelder in einem Überwachungsdatensatz. Wenn der von einer überwachbaren Aktion zurückgegebene Wert **statement** oder **data_sensitivity_information** mehr als 4000 Zeichen enthält, werden alle Daten über die ersten 4000 Zeichen hinaus **abgeschnitten und nicht überwacht**.
Der folgende Abschnitt beschreibt die Konfiguration der Überwachung über das Azure-Portal.

  > [!NOTE]
  > Die Überwachung kann bei einem angehaltenen dedizierten SQL-Pool nicht aktiviert werden. Zum Aktivieren der Überwachung setzen Sie den Pool fort. Informieren Sie sich ausführlicher über den [dedizierten SQL-Pool](../..//synapse-analytics/sql/best-practices-sql-pool.md).

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie im Bereich für die **SQL-Datenbank** oder den **SQL Server** unter der Überschrift „Sicherheit“ zu **Überwachung**.
3. Wenn Sie eine Serverüberwachungsrichtlinie einrichten möchten, wählen Sie auf dem Blatt für die Datenbanküberwachung den Link **Servereinstellungen anzeigen** aus. Anschließend können Sie die Serverüberwachungseinstellungen anzeigen oder ändern. Eine Richtlinien für die Serverüberwachung gelten für alle vorhandenen und neu erstellten Datenbanken auf diesem Server.

    ![Der Screenshot zeigt den Link „Servereinstellungen anzeigen“, der auf dem Blatt für die Datenbanküberwachung hervorgehoben ist.](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. Wenn Sie die Überwachung auf Datenbankebene aktivieren möchten, ändern Sie **Überwachung** in **EIN**. Wenn die Serverüberwachung aktiviert ist, existiert die konfigurierte Datenbanküberwachung parallel zur Serverüberwachung.

5. Sie haben mehrere Optionen zur Auswahl, um zu konfigurieren, wohin Überwachungsprotokolle geschrieben werden sollen. Sie können die Protokolle in ein Azure Storage-Konto, in einen Log Analytics-Arbeitsbereich für die Nutzung durch Azure Monitor-Protokolle (Vorschauversion) oder in einen Event Hub für die Nutzung durch den Event Hub (Vorschauversion) schreiben. Sie können eine beliebige Kombination dieser Optionen konfigurieren, und die Überwachungsprotokolle werden in die jeweils angegebenen Speicherorte geschrieben.
  
   ![Speicheroptionen](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations-preview"></a><a id="auditing-of-microsoft-support-operations"></a>Überwachung von Microsoft-Supportvorgängen (Vorschau)

Mit der Überwachung von Microsoft-Supportvorgängen (Vorschau) für Azure SQL Server können Sie die Vorgänge von Microsoft-Supporttechnikern überwachen, wenn diese während einer Supportanfrage auf Ihren Server zugreifen müssen. In Kombination mit der Überwachung sorgt diese Funktion für eine höhere Personaltransparenz und ermöglicht Anomalieerkennung, Trendvisualisierung und die Verhinderung von Datenverlusten.

Um die Überwachung von Microsoft-Supportvorgängen (Vorschau) zu aktivieren, navigieren Sie im Bereich **Azure SQL Server** unter der Überschrift „Sicherheit“ zu **Überwachung**, und legen Sie **Auditing of Microsoft support operations (Preview)** (Überwachung von Microsoft-Supportvorgängen (Vorschau)) auf **EIN** fest.

  > [!IMPORTANT]
  > Die Überwachung von Microsoft-Supportvorgängen (Vorschau) unterstützt keine Speicherkontoziele. Zum Aktivieren der Funktion muss ein Log Analytics-Arbeitsbereich oder ein Event Hub-Ziel konfiguriert werden.

![Screenshot: Microsoft-Supportvorgänge](./media/auditing-overview/support-operations.png)

Mit der folgenden Abfrage können Sie die Überwachungsprotokolle von Microsoft-Support-Vorgängen in Ihrem Log Analytics Arbeitsbereich überprüfen:

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>Überwachung in Speicherziel

Um das Schreiben von Überwachungsprotokollen in ein Speicherkonto zu konfigurieren, wählen Sie **Storage** aus, und öffnen Sie **Speicherdetails**. Wählen Sie das Azure Storage-Konto, in dem die Protokolle gespeichert werden sollen, und dann die Beibehaltungsdauer aus. Klicken Sie dann auf **OK**. Protokolle, die älter als die Aufbewahrungsdauer sind, werden gelöscht.

- Der Standardwert für die Beibehaltungsdauer ist „0“ (unbegrenzte Aufbewahrung). Sie können diesen Wert ändern, indem Sie den Schieberegler **Beibehaltung (Tage)** in **Speichereinstellungen** verschieben, wenn Sie das Speicherkonto für die Überwachung konfigurieren.
  - Wenn Sie die Beibehaltungsdauer von „0“ (unbegrenzte Aufbewahrung) in einen anderen Wert ändern, beachten Sie, dass die Beibehaltung nur auf Protokolle angewendet wird, die nach dem Ändern des Beibehaltungswerts geschrieben wurden (Protokolle, die in dem Zeitraum geschrieben wurden, in dem die Beibehaltung auf unbegrenzt festgelegt war, bleiben auch nach Aktivieren der Beibehaltung erhalten).

  ![Speicherkonto](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Überwachen in Log Analytics-Ziel
  
Um das Schreiben von Überwachungsprotokollen in einen Log Analytics-Arbeitsbereich zu konfigurieren, wählen Sie **Log Analytics (Vorschau)** aus und öffnen **Log Analytics-Details**. Wählen Sie den Log Analytics-Arbeitsbereich aus (oder erstellen Sie ihn), in den Protokolle geschrieben werden sollen, und klicken Sie dann auf **OK**.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Weitere Informationen zum Azure Monitor Log Analytics-Arbeitsbereich finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../../azure-monitor/platform/design-logs-deployment.md).
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>Überwachen in Event Hub-Ziel

Um das Schreiben von Überwachungsprotokollen in einen Event Hub zu konfigurieren, wählen Sie **Event Hub (Vorschau)** aus, und öffnen Sie **Event Hub-Details**. Wählen Sie den Event Hub aus, in den die Protokolle geschrieben werden sollen, und klicken Sie dann auf **OK**. Achten Sie darauf, dass sich der Event Hub in derselben Region wie Ihre Datenbank und der Server befindet.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>Analysieren von Überwachungsprotokollen und -berichten

Führen Sie die folgenden Schritte aus, wenn Sie Überwachungsprotokolle in Azure Monitor-Protokolle schreiben möchten:

- Verwenden Sie das [Azure-Portal](https://portal.azure.com). Öffnen Sie die entsprechende Datenbank. Wählen Sie in der Datenbank oben auf der Seite **Überwachung** die Option **Überwachungsprotokolle anzeigen** aus.

    ![Überwachungsprotokolle anzeigen](./media/auditing-overview/auditing-view-audit-logs.png)

- Anschließend haben Sie zwei Möglichkeiten, die Protokolle anzuzeigen:

    Klicken Sie am oberen Rand der Seite **Überwachungsdatensätze** auf **Log Analytics**. Daraufhin wird die Protokollansicht im Log Analytics-Arbeitsbereich geöffnet, in der Sie den Zeitbereich und die Suchabfrage anpassen können.

    ![In Log Analytics-Arbeitsbereich öffnen](./media/auditing-overview/auditing-log-analytics.png)

    Wenn Sie oben auf der Seite **Überwachungsdatensätze** auf **Dashboard anzeigen** klicken, wird ein Dashboard geöffnet, das Überwachungsprotokollinformationen anzeigt, für die Sie in Security Insights ein Drilldown ausführen oder auf vertrauliche Daten zugreifen können und vieles mehr. Dieses Dashboard soll Ihnen helfen, Sicherheitseinblicke in Ihre Daten zu erhalten.
    Sie können den Zeitbereich und die Suchabfrage auch anpassen.
    ![Anzeigen des Log Analytics-Dashboards](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics-Dashboard](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics Security Insights](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- Alternativ können Sie auch vom Blatt „Log Analytics“ auf die Überwachungsprotokolle zugreifen. Öffnen Sie Ihren Log Analytics-Arbeitsbereich und klicken Sie im Abschnitt **Allgemein** auf **Protokolle**. Beginnen Sie mit einer einfachen Abfrage, wie z. B. *"SQLSecurityAuditEvents" suchen*, um die Überwachungsprotokolle anzuzeigen.
    Hier können Sie auch [Azure Monitor-Protokolle](../../azure-monitor/log-query/log-query-overview.md) nutzen, um erweiterte Suchen für Ihre Überwachungsprotokolldaten durchzuführen. Mithilfe integrierter Suchfunktionen und benutzerdefinierter Dashboards bieten Azure Monitor-Protokolle Ihnen in Echtzeit Erkenntnisse zu Betriebsabläufen, sodass Sie Millionen von Datensätzen für alle Ihre Workloads und Server analysieren können. Weitere nützliche Informationen zur Suchsprache und den Befehlen in Azure Monitor-Protokolle finden Sie unter [Referenz zur Suche in Azure Monitor-Protokolle](../../azure-monitor/log-query/log-query-overview.md).

Wenn Sie Überwachungsprotokolle in Event Hub schreiben möchten:

- Um die Überwachungsprotokolldaten von Event Hub zu nutzen, müssen Sie einen Stream zum Nutzen von Ereignissen einrichten und diese in ein Ziel schreiben. Weitere Informationen finden Sie in der [Dokumentation zu Azure Event Hubs](../index.yml).
- Überwachungsprotokolle werden in Event Hub derzeit im Text von [Apache Avro](https://avro.apache.org/)-Ereignissen erfasst und im JSON-Format mit UTF-8-Codierung gespeichert. Zum Lesen der Überwachungsprotokolle können Sie [Avro Tools](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) oder ähnliche Tools verwenden, die dieses Format verarbeiten können.

Wenn Sie die Überwachungsprotokolle in ein Azure-Speicherkonto schreiben möchten, gibt es mehrere Methoden zum Anzeigen der Protokolle:

- Überwachungsprotokolle werden in dem Konto aggregiert, das Sie während der Einrichtung ausgewählt haben. Sie können Überwachungsprotokolle mithilfe eines Tools wie [Azure Storage-Explorer](https://storageexplorer.com/) untersuchen. In Azure Storage werden Überwachungsprotokolle als Sammlung von Blobdateien in einem Container namens **sqldbauditlogs** gespeichert. Weitere Informationen zur Hierarchie der Speicherordner, zu Namenskonventionen und zum Protokollformat finden Sie unter [Format für SQL-Datenbank-Überwachungsprotokolle](./audit-log-format.md).

- Verwenden Sie das [Azure-Portal](https://portal.azure.com).  Öffnen Sie die entsprechende Datenbank. Klicken Sie in der Datenbank oben auf der Seite **Überwachung** auf **Überwachungsprotokolle anzeigen**.

    ![Der Screenshot zeigt die Schaltfläche „Überwachungsprotokolle anzeigen“, die auf dem Blatt für die Datenbanküberwachung hervorgehoben ist.](./media/auditing-overview/7_auditing_get_started_blob_view_audit_logs.png)

    Der Bereich **Überwachungsdatensätze** wird geöffnet, in dem Sie die Protokolle anzeigen können.

  - Sie können Protokolle für einen bestimmten Zeitraum anzeigen, indem Sie im oberen Bereich der Seite **Überwachungsdatensätze** auf **Filter** klicken.
  - Sie können zwischen Überwachungsdatensätzen wechseln, die anhand der *Serverüberwachungsrichtlinie* oder der *Datenbanküberwachungsrichtlinie* erstellt wurden, indem Sie die Option unter **Überwachungsquelle** ändern.
  - Sie können nur Überwachungsdatensätze zur Einschleusung von SQL-Befehlen anzeigen, indem Sie das Kontrollkästchen **Nur Überwachungsdatensätze zur Einschleusung von SQL-Befehlen anzeigen** aktivieren.

       ![Der Screenshot zeigt die Optionen zum Anzeigen der Überwachungsdatensätze.]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- Verwenden Sie die Systemfunktion **sys.fn_get_audit_file** (T-SQL), um die Daten der Überwachungsprotokolle im Tabellenformat zurückzugeben. Weitere Informationen zur Verwendung dieser Funktion finden Sie unter [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Verwenden von **Überwachungsdateien zusammenführen** in SQL Server Management Studio (ab SSMS 17):
    1. Wählen Sie im SSMS-Menü **Datei** > **Öffnen** > **Überwachungsdateien zusammenführen**.

        ![Der Screenshot zeigt die Menüoption „Überwachungsdateien zusammenführen“.](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. Das Dialogfeld **Überwachungsdateien hinzufügen** wird geöffnet. Wählen Sie eine der Optionen zum **Hinzufügen** aus, um festzulegen, ob die Überwachungsdateien von einem lokalen Datenträger zusammengeführt oder aus Azure Storage importiert werden sollen. Sie müssen Ihre Azure Storage-Details und Ihren Kontoschlüssel angeben.

    3. Nachdem Sie alle zusammenzuführenden Dateien hinzugefügt haben, klicken Sie auf **OK**, um die Zusammenführung abzuschließen.

    4. Die zusammengeführte Datei wird in SSMS geöffnet. Hier können Sie die Datei anzeigen und analysieren und in eine XEL- oder CSV-Datei oder in eine Tabelle exportieren.

- Verwenden Sie Power BI. Sie können Überwachungsprotokolldateien in Power BI anzeigen und analysieren. Weitere Informationen finden Sie unter [Analysieren von Überwachungsprotokolldaten in Power BI](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895). Dort können Sie auch auf eine herunterladbare Vorlage zugreifen.
- Laden Sie Protokolldateien aus Ihrem Azure Storage-Blobcontainer über das Portal oder mithilfe eines Tools wie [Azure Storage-Explorer](https://storageexplorer.com/) herunter.
  - Nachdem Sie die Protokolldatei lokal heruntergeladen haben, können Sie auf die Datei doppelklicken, um die Protokolle in SSMS zu öffnen, anzuzeigen und zu analysieren.
  - Sie können mit dem Azure Storage-Explorer auch mehrere Dateien gleichzeitig herunterladen. Klicken Sie dazu mit der rechten Maustaste auf einen bestimmten Unterordner, und wählen Sie **Speichern unter** aus, um die Dateien in einem lokalen Ordner zu speichern.

- Weitere Methoden:

  - Nach dem Herunterladen mehrerer Dateien oder eines Unterordners, der Protokolldateien enthält, können Sie die Dateien lokal zusammenführen, wie in den Anweisungen weiter oben für das Zusammenführen von Überwachungsdateien in SSMS beschrieben.
  - Programmgesteuertes Anzeigen von Blobüberwachungsprotokollen: Führen Sie [Abfragen von Dateien mit erweiterten Ereignissen](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/) mithilfe von PowerShell durch.

## <a name="production-practices"></a><a id="production-practices"></a>Produktionsverfahren


### <a name="auditing-geo-replicated-databases"></a>Überwachung von georeplizierten Datenbanken

Wenn Sie bei georeplizierten Datenbanken die Überwachung für die primäre Datenbank aktivieren, verfügt die sekundäre Datenbank über eine identische Überwachungsrichtlinie. Es ist auch möglich, die Überwachung für die sekundäre Datenbank einzurichten, indem Sie die Überwachung auf dem **sekundären Server** unabhängig von der primären Datenbank aktivieren.

- Auf Serverebene (**empfohlen**): Aktivieren Sie die Überwachung sowohl auf dem **primären Server** als auch auf dem **sekundären Server**. Dadurch werden die primäre und die sekundäre Datenbank jeweils unabhängig voneinander anhand der jeweiligen Richtlinie auf Serverebene überwacht.
- Auf Datenbankebene: Die Überwachung auf Datenbankebene kann für sekundäre Datenbanken nur über die Überwachungseinstellungen der primären Datenbank konfiguriert werden.
  - Die Überwachung muss in der *primären Datenbank selbst* aktiviert werden, nicht auf dem Server.
  - Nachdem die Überwachung in der primären Datenbank aktiviert wurde, wird sie auch in der sekundären Datenbank aktiv.

    > [!IMPORTANT]
    > Bei der Überwachung auf Datenbankebene sind die Speichereinstellungen für die sekundäre Datenbank identisch mit den Einstellungen der primären Datenbank, wodurch regionsübergreifender Datenverkehr generiert wird. Es wird empfohlen, die Überwachung nur auf Serverebene zu aktivieren und die Überwachung auf Datenbankebene für alle Datenbanken deaktiviert zu lassen.

### <a name="storage-key-regeneration"></a>Erneute Speicherschlüsselgenerierung

In einer Produktionsumgebung werden Sie Ihre Speicherschlüssel wahrscheinlich regelmäßig aktualisieren. Wenn Sie Überwachungsprotokolle in Azure Storage schreiben, müssen Sie Ihre Überwachungsrichtlinie neu speichern, wenn Sie die Schlüssel aktualisieren. Der Prozess sieht folgendermaßen aus:

1. Öffnen Sie **Speicherdetails**. Wählen Sie im Feld **Speicherzugriffsschlüssel** die Option **Sekundär** aus, und klicken Sie dann auf **OK**. Klicken Sie anschließend oben auf der Seite für die Überwachungskonfiguration auf **Speichern**.

    ![Der Screenshot zeigt den Prozess zum Auswählen eines sekundären Speicherzugriffsschlüssels.](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. Wechseln Sie zur Seite für die Speicherkonfiguration, und generieren Sie erneut den primären Zugriffsschlüssel.

    ![Navigationsbereich](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. Wechseln Sie zurück zur Seite für die Überwachungskonfiguration, ändern Sie den Speicherzugriffsschlüssel von „Sekundär“ in „Primär“, und klicken Sie dann auf **OK**. Klicken Sie anschließend oben auf der Seite für die Überwachungskonfiguration auf **Speichern**.
4. Wechseln Sie wieder zurück zur Seite für die Speicherkonfiguration, und generieren Sie den sekundären Zugriffsschlüssel erneut (in Vorbereitung auf den nächsten Schlüsselaktualisierungszyklus).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Verwalten der Überwachung von Azure SQL-Datenbank

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

**PowerShell-Cmdlets (einschließlich Unterstützung der WHERE-Klausel für zusätzliche Filterung)** :

- [Erstellen oder Aktualisieren der Datenbanküberwachungsrichtlinie (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Erstellen oder Aktualisieren der Serverüberwachungsrichtlinie (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [Abrufen der Datenbanküberwachungsrichtlinie (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Abrufen der Serverüberwachungsrichtlinie (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [Entfernen der Datenbanküberwachungsrichtlinie (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Entfernen der Serverüberwachungsrichtlinie (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

Ein Skriptbeispiel finden Sie unter [Konfigurieren von Überwachung von SQL-Datenbank und Bedrohungserkennung mit PowerShell](scripts/auditing-threat-detection-powershell-configure.md).

### <a name="using-rest-api"></a>Verwenden der REST-API

**REST-API**:

- [Erstellen oder Aktualisieren einer Richtlinie für die Überwachung von Datenbanken](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Erstellen oder Aktualisieren einer Richtlinie für die Überwachung von Servern](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Abrufen einer Richtlinie für die Überwachung von Datenbanken](/rest/api/sql/database%20auditing%20settings/get)
- [Abrufen einer Richtlinie für die Überwachung von Servern](/rest/api/sql/server%20auditing%20settings/get)

Erweiterte Richtlinie mit Unterstützung der WHERE-Klausel für zusätzliche Filterung:

- [Erstellen oder Aktualisieren einer *erweiterten* Datenbanküberwachungsrichtlinie](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Erstellen oder Aktualisieren einer *erweiterten* Serverüberwachungsrichtlinie](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Abrufen einer *erweiterten* Datenbanküberwachungsrichtlinie](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Abrufen einer *erweiterten* Serverüberwachungsrichtlinie](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Sie können die Überwachung von Azure SQL-Datenbank mithilfe von [Azure Resource Manager-Vorlagen](../../azure-resource-manager/management/overview.md) verwalten, wie die folgenden Beispiele zeigen:

- [Bereitstellen von Azure SQL-Datenbank mit aktivierter Überwachung zum Schreiben von Überwachungsprotokollen in ein Azure Blob Storage-Konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Bereitstellen von Azure SQL-Datenbank mit aktivierter Überwachung zum Schreiben von Überwachungsprotokollen in Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Bereitstellen von Azure SQL-Datenbank mit aktivierter Überwachung zum Schreiben von Überwachungsprotokollen in Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Die verknüpften Beispiele befinden sich in einem externen öffentlichen Repository und werden wie besehen ohne Gewähr zur Verfügung gestellt und werden von keinem Microsoft-Supportprogramm/-dienst unterstützt.
