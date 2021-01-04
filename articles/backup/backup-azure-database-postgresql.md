---
title: Sichern von Azure Database for PostgreSQL
description: Weitere Informationen zur Azure Database for PostgreSQL-Sicherung mit Langzeitaufbewahrung (Vorschau)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: edbfdb6ea741cdb344a121acdbee3b8bd4bc743c
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927888"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Azure Database for PostgreSQL-Sicherung mit Langzeitaufbewahrung (Vorschau)

Azure Backup und Azure Database Services wurden zusammengeführt, um eine Sicherungslösung für Unternehmen mit Azure Database for PostgreSQL-Servern zu entwickeln, die Sicherungen für bis zu 10 Jahre aufbewahrt.

Neben der Langzeitaufbewahrung bietet die Lösung auch viele weitere Funktionen, wie die folgenden:

- rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) für die Datenbank mithilfe von Azure Active Directory und der MSI-Authentifizierung (verwaltete Dienstidentität)
- Kundenseitig gesteuerte, geplante und bedarfsorientierte Sicherungen der einzelnen Datenbanken.
- Wiederherstellungen auf Datenbankebene auf einem beliebigen Postgres-Server oder direkt im Blobspeicher.
- Langzeitaufbewahrung.
- Zentrale Überwachung aller Vorgänge und Aufträge.
- Sicherungen werden in separaten Sicherheits- und Fehlerdomänen gespeichert. Auch wenn der Quellserver kompromittiert oder sogar beendet werden sollte, sind die Sicherungen im [Sicherungstresor](backup-vault-overview.md) gut aufgehoben.
- Die Verwendung von **pg_dump** bietet mehr Flexibilität bei Wiederherstellungen, sodass Sie verschiedene Datenbankversionen oder sogar nur einen Teil der Sicherung wiederherstellen können.

Sie können diese Lösung unabhängig oder zusätzlich zur nativen Sicherungslösung verwenden, die von Azure PostgreSQL angeboten wird und die eine Aufbewahrungsdauer von bis zu 35 Tagen ermöglicht. Die native Lösung eignet sich für operative Wiederherstellungen, wenn Sie z. B. die neuesten Sicherungen wiederherstellen möchten. Die Azure Backup-Lösung hilft Ihnen bei der Erfüllung Ihrer Konformitätsanforderungen sowie bei genaueren und flexibleren Sicherungen und Wiederherstellungen.

## <a name="support-matrix"></a>Unterstützungsmatrix

|Support  |Details  |
|---------|---------|
|Unterstützte Bereitstellungen   |  Eigenständiger Azure Database for PostgreSQL-Einzelserver     |
|Unterstützte Azure-Regionen |  USA, Osten, USA, Osten 2, USA, Mitte, USA, Süden-Mitte, USA, Westen, USA, Westen 2; USA, Westen-Mitte, Brasilien, Süden; Kanada, Mitte, Europa, Norden, Europa, Westen, Vereinigtes Königreich, Süden, Vereinigtes Königreich, Westen, Deutschland, Westen-Mitte, Schweiz, Norden, Schweiz, Westen, Asien, Osten,Asien, Südosten, Japan, Osten, Japan, Westen, Südkorea, Mitte, Südkorea, Süden, Indien, Mitte, Australien, Osten, Australien, Mitte, Australien, Mitte 2, VAE, Norden  |
|Unterstützte Azure PostgreSQL-Versionen    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Überlegungen und Einschränkungen in Bezug auf Features

- Alle Vorgänge werden nur über das Azure-Portal unterstützt.
- Der empfohlene Grenzwert für die maximale Datenbankgröße beträgt 400 GB.
- Regionsübergreifende Sicherungen werden nicht unterstützt. Dies bedeutet, dass Sie einen Azure PostgreSQL-Server nicht in einem Tresor in einer anderen Region sichern können. Ebenso können Sie eine Sicherung nur auf einem Server wiederherstellen, der sich in derselben Region wie der Tresor befindet.
- Nur die Daten werden zum Zeitpunkt der Wiederherstellung wiederhergestellt. „Rollen“ werden nicht wieder hergestellt.
- In der Vorschauversion wird empfohlen, dass Sie die Lösung nur in der Testumgebung ausführen.

## <a name="backup-process"></a>Sicherungsprozess

1. Diese Lösung verwendet **pg_dump** , um Sicherungen Ihrer Azure PostgreSQL-Datenbanken zu erstellen.

2. Nachdem Sie die zu sichernden Azure PostgreSQL-Datenbanken angegeben haben, überprüft der Dienst, ob er über die richtigen Berechtigungen und Zugriffsrechte verfügt, um den Sicherungsvorgang auf dem Server und der Datenbank durchzuführen.

3. Azure Backup startet eine Workerrolle, in der eine Sicherungserweiterung installiert ist. Diese Erweiterung kommuniziert mit dem Postgres-Server.

4. Die Erweiterung besteht aus einem Koordinator und einem Azure Postgres-Plug-In. Während der Koordinator für das Auslösen von Workflows für verschiedene Vorgänge wie Konfigurieren der Sicherung, Sicheren und Wiederherstellen zuständig ist, ist das Plug-In für den tatsächlichen Datenfluss verantwortlich.
  
5. Sobald Sie die Konfiguration des Schutzes der ausgewählten Datenbanken auslösen, richtet der Sicherungsdienst den Koordinator mit den Sicherungszeitplänen und anderen Richtliniendetails ein.

6. Zum geplanten Zeitpunkt kommuniziert der Koordinator mit dem Plug-In, und das Streaming der Sicherungsdaten vom Postgres-Server mit **pg_dump** wird gestartet.

7. Weil das Plug-In die Daten direkt an den Backup-Tresor sendet, ist kein Stagingspeicherort erforderlich. Die Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt und vom Azure Backup-Dienst in Speicherkonten gespeichert.

8. Wenn die Datenübertragung abgeschlossen ist, bestätigt der Koordinator den Commit mit dem Sicherungsdienst.

    ![Sicherungsprozess](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Konfigurieren der Sicherung für Azure PostgreSQL-Datenbanken

Sie können die Sicherung für mehrere Datenbanken auf mehreren Azure PostgreSQL-Servern konfigurieren. Stellen Sie sicher, dass die [erforderlichen Berechtigungen](#prerequisite-permissions-for-configure-backup-and-restore), die der Dienst zum Sichern der Postgres-Server benötigt, bereits konfiguriert sind.

Die folgenden Anweisungen stellen eine Schrittanleitung zum Konfigurieren der Sicherung für die Azure PostgreSQL-Datenbanken mithilfe von Azure Backup dar:

1. Es gibt zwei Möglichkeiten, um den Prozess zu starten:

    1. Wechseln Sie zu [Backup Center](backup-center-overview.md) -> **Übersicht** -> **Sicherung**.

        ![Wechseln zu „Backup Center“](./media/backup-azure-database-postgresql/backup-center.png)

        Wählen Sie unter **Initiieren: Sicherung konfigurieren** als **Datenquellentyp** die Option **Azure Database for PostgreSQL** aus.

        ![Unter „Initiieren: Sicherung konfigurieren“ Auswahl eines Datenquellentyps](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Alternativ können Sie direkt zu [Sicherungstresore](backup-vault-overview.md) -> **Sicherung** wechseln.

        ![Wechseln zu „Sicherungstresore“](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Auswählen von „Sicherung“ unter „Sicherungstresor“](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Wählen Sie unter **Sicherung konfigurieren** den **Sicherungstresor** aus, in dem Sie Ihre Postgres-Datenbanken sichern möchten. Diese Informationen werden vorab ausgefüllt, wenn Sie sich bereits im Kontext des Tresors befinden.

    ![Auswählen des Sicherungstresors unter „Sicherung konfigurieren“](./media/backup-azure-database-postgresql/configure-backup.png)

1. Wählen Sie eine **Sicherungsrichtlinie** aus, oder erstellen Sie eine.

    ![Auswählen der Sicherungsrichtlinie](./media/backup-azure-database-postgresql/backup-policy.png)

1. Wählen Sie die zu sichernden Ressourcen oder Postgres-Datenbanken aus.

    ![Auswählen der zu sichernden Ressourcen](./media/backup-azure-database-postgresql/select-resources.png)

1. Wenn Sie sich in derselben Region wie der Tresor befinden, können Sie aus der Liste aller Azure PostgreSQL-Server abonnementübergreifend auswählen. Klappen Sie die Liste der Datenbanken innerhalb eines Servers auf, indem Sie auf den Pfeil klicken.

    ![Auswählen von Servern](./media/backup-azure-database-postgresql/choose-servers.png)

1. Der Dienst führt diese Überprüfungen für die ausgewählten Datenbanken aus, um sicherzustellen, dass der Tresor über Berechtigungen zum Sichern der ausgewählten Postgres-Server und -Datenbanken verfügt.
    1. Die Option **Sicherungsbereitschaft** muss für alle Datenbanken **Erfolgreich** anzeigen, um den Vorgang fortzusetzen.
    1. Wenn ein Fehler auftritt, **beheben** Sie entweder den Fehler und **überprüfen** dies erneut, oder Sie entfernen die Datenbank aus der Auswahl.

    ![Zu behebende Validierungsfehler](./media/backup-azure-database-postgresql/validation-errors.png)

1. Bestätigen Sie alle Details unter **Überprüfen und konfigurieren** , und wählen Sie **Sicherung konfigurieren** aus, um den Vorgang zu übermitteln.

    ![Bestätigen von Details unter „Überprüfen und Konfigurieren“](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Sobald der Vorgang **Sicherung konfigurieren** ausgelöst wurde, wird eine Sicherungsinstanz erstellt. Sie können den Status des Vorgangs in der Backup Center- oder der Tresoransicht im Bereich [Sicherungsinstanzen](backup-center-monitor-operate.md#backup-instances) verfolgen.

    ![Sicherungsinstanzen](./media/backup-azure-database-postgresql/backup-instances.png)

1. Die Sicherungen werden gemäß dem Sicherungszeitplan ausgelöst, der in der Richtlinie definiert ist. Die Aufträge lassen sich unter [Sicherungsaufträge](backup-center-monitor-operate.md#backup-jobs) nachverfolgen. Derzeit können Sie Aufträge für die letzten sieben Tage anzeigen.

    ![Sicherungsaufträge](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

1. Wechseln Sie zu **Backup Center** -> **Sicherungsrichtlinien** -> **Hinzufügen**. Alternativ können Sie direkt zu **Sicherungstresor** -> **Sicherungsrichtlinie** -> **Hinzufügen** wechseln.

    ![Hinzufügen einer Sicherungsrichtlinie](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Geben Sie unter **Name** einen Namen für die neue Richtlinie ein.

    ![Eingeben des Richtliniennamens](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Legen Sie den Sicherungszeitplan fest. Derzeit wird eine **wöchentliche** Sicherung unterstützt. Sie können die Sicherungen an einem oder mehreren Wochentagen planen.

    ![Festlegen des Sicherungszeitplans](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Legen Sie die Einstellungen für die **Aufbewahrung** fest. Sie können eine oder mehrere Aufbewahrungsregeln hinzufügen. Jede Aufbewahrungsregel nimmt Eingaben für bestimmte Sicherungen und den Datenspeicher sowie die Aufbewahrungsdauer für diese Sicherungen an.

1. Sie können Ihre Sicherungen in einem der beiden Datenspeicher (oder in einer der beiden Ebenen) speichern: **Sicherungsdatenspeicher** (heiße Ebene) oder **Archivdatenspeicher** (in der Vorschau). Sie können zwischen **zwei Tiering-Optionen** wählen, um festzulegen, wann die Sicherungen über die beiden Datenspeicher verteilt werden:

    - Kopieren Sie **sofort** , wenn Sie gleichzeitig eine Sicherungskopie für sowohl Sicherungs- als auch Archivdatenspeicher haben möchten.
    - Wählen Sie die Option zum Verschieben nach **Ablauf** , wenn Sie die Sicherung nach Ablauf im Sicherungsdatenspeicher in den Archivdatenspeicher verschieben möchten.

1. Die **Standardaufbewahrungsregel** wird angewandt, wenn keine andere Aufbewahrungsregel vorhanden ist. Sie hat einen Standardwert von drei Monaten.

    - Die Aufbewahrungsdauer reicht im **Sicherungsdatenspeicher** von sieben Tagen bis zu zehn Jahren.
    - Die Aufbewahrungsdauer reicht im **Archivdatenspeicher** von sechs Monaten bis zu zehn Jahren.

    ![Bearbeiten der Aufbewahrungsdauer](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>Die Aufbewahrungsregeln werden in einer vordefinierten Reihenfolge ausgewertet. Die oberste Priorität hat die Regel **Jährlich** , gefolgt von der Regel **Monatlich** und dann von der Regel **Wöchentlich**. Die Standardeinstellungen für die Aufbewahrung werden angewendet, wenn keine anderen Regeln zutreffen. Beispielsweise kann derselbe Wiederherstellungspunkt sowohl die erste erfolgreiche Sicherung, die jede Woche erstellt wird, als auch die erste erfolgreiche Sicherung, die jeden Monat erstellt wird, sein. Da die Priorität der monatlichen Regel jedoch höher als die der wöchentlichen Regel ist, gilt die Aufbewahrungsregel, die der ersten erfolgreichen, monatlich erstellten Sicherung entspricht.

## <a name="restore"></a>Restore

Sie können eine Datenbank auf jedem beliebigen Azure PostgreSQL-Server innerhalb desselben Abonnements wiederherstellen, wenn der Dienst über die entsprechenden Berechtigungen auf dem Zielserver verfügt. Stellen Sie sicher, dass die [erforderlichen Berechtigungen](#prerequisite-permissions-for-configure-backup-and-restore), die der Dienst zum Sichern der Postgres-Server benötigt, bereits konfiguriert sind.

Führen Sie diese Schrittanleitung aus, um eine Wiederherstellung auszulösen:

1. Es gibt zwei Möglichkeiten, den Wiederherstellungsvorgang zu starten:
    1. Wechseln Sie zu [Backup Center](backup-center-overview.md) -> **Übersicht** -> **Wiederherstellen**.

    ![Auswählen von „Wiederherstellen“ in Backup Center](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Wählen Sie unter **Initiieren: Wiederherstellen** als **Datenquellentyp** die Option **Azure Database for PostgreSQL** aus. Wählen Sie die **Sicherungsinstanz** aus.

    ![Auswählen des Datenquellentyps unter „Initiieren: Wiederherstellen“](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Alternativ können Sie direkt zu **Sicherungstresor** -> **Sicherungsinstanzen** wechseln. Wählen Sie **Sicherungsinstanz** entsprechend der Datenbank aus, die Sie wiederherstellen möchten.

    ![Sicherungsinstanzen für die Wiederherstellung](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Liste der Sicherungsinstanzen](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Auswählen von „Wiederherstellen“](./media/backup-azure-database-postgresql/select-restore.png)

1. Klicken Sie in der Liste der vollständigen Sicherungen, die für die ausgewählte Sicherungsinstanz verfügbar sind, auf **Wiederherstellungspunkt auswählen**. Standardmäßig ist der letzte Wiederherstellungspunkt ausgewählt.

    ![Auswählen eines Wiederherstellungspunkts](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Liste der Wiederherstellungspunkte](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Geben Sie die **Wiederherstellungsparameter** ein. Nun können Sie sich für eine von zwei Wegen für die Wiederherstellung entscheiden: **Wiederherstellen als Datenbank** und **Wiederherstellen als Dateien**.

1. **Wiederherstellen als Datenbank** :  Stellen Sie die Sicherungsdaten wieder her, um eine neue Datenbank auf dem PostgreSQL-Zielserver zu erstellen.

    - Der Zielserver darf mit dem Quellserver identisch sein. Das Überschreiben der ursprünglichen Datenbank wird jedoch nicht unterstützt.
    - Sie können aus dem Server aus allen Abonnements, aber in derselben Region wie der Tresor auswählen.
    - Wählen Sie **Überprüfen + Wiederherstellen** aus. Dadurch wird überprüft, ob der Dienst über die entsprechenden Berechtigungen zur Wiederherstellung auf dem Zielserver verfügt.

    ![Wiederherstellen als Datenbank](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Wiederherstellen als Dateien** : Sichern Sie die Sicherungsdateien im Zielspeicherkonto (Blobs).

    - Sie können aus den Speicherkonten aus allen Abonnements, aber in derselben Region wie der Tresor auswählen.
    - Wählen Sie den Zielcontainer in der Liste der Container aus, die für das ausgewählte Speicherkonto gefiltert wurde.
    - Wählen Sie **Überprüfen + Wiederherstellen** aus. Dadurch wird überprüft, ob der Dienst über die entsprechenden Berechtigungen zur Wiederherstellung auf dem Zielserver verfügt.

    ![Wiederherstellen als Dateien](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Überprüfen Sie die Informationen, und wählen Sie **Wiederherstellen** aus. Dadurch wird ein entsprechender Wiederherstellungsauftrag ausgelöst, der unter **Sicherungsaufträge** nachverfolgt werden kann.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Erforderliche Berechtigungen zum Konfigurieren der Sicherung und Wiederherstellung

Azure Backup hält strikte Sicherheitsrichtlinien ein. Obwohl es sich um einen nativen Azure-Dienst handelt, werden Berechtigungen für die Ressource nicht angenommen. Sie müssen explizit vom Benutzer erteilt werden.  Ebenso werden keine Anmeldeinformationen für die Verbindung mit der Datenbank gespeichert. Dies ist für den Schutz Ihrer Daten unerlässlich. Stattdessen wird die Azure Active Directory-Authentifizierung verwendet.

[Laden Sie dieses Dokument herunter](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx), um ein automatisiertes Skript und entsprechende Anweisungen zu erhalten. Es erteilt einem Azure PostgreSQL-Server ausreichende Berechtigungen für die Sicherung und Wiederherstellung.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Verwalten der gesicherten Azure PostgreSQL-Datenbanken

Dies sind die Verwaltungsvorgänge, die Sie für die **Sicherungsinstanzen** ausführen können:

### <a name="on-demand-backup"></a>Bedarfsgesteuerte Sicherung

Um eine Sicherung ohne den in der Richtlinie angegebenen Zeitplan zu starten, wechseln Sie zu **Sicherungsinstanzen** -> **Jetzt sichern**.
Wählen Sie Aufbewahrungsregeln in der Liste aus, die in der zugehörigen Sicherungsrichtlinie definiert wurden.

![Auslösen von „Jetzt sichern“](./media/backup-azure-database-postgresql/backup-now.png)

![Auswählen aus der Liste der Aufbewahrungsregeln](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Schutz beenden

Sie können den Schutz für ein Sicherungselement beenden. Dadurch werden auch die zugehörigen Wiederherstellungspunkte für das Sicherungselement gelöscht. Derzeit gibt es noch keine Möglichkeit, den Schutz unter Beibehaltung der bestehenden Wiederherstellungspunkte zu beenden.

![Schutz beenden](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Ändern einer Richtlinie

Sie können die zugehörige Richtlinie mit einer Sicherungsinstanz ändern.

1. Wählen Sie **Sicherungsinstanz** -> **Richtlinie ändern** aus.

    ![Ändern einer Richtlinie](./media/backup-azure-database-postgresql/change-policy.png)

1. Wählen Sie die neue Richtlinie aus, die Sie auf die Datenbank anwenden möchten.

    ![Neuzuweisen der Richtlinie](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Problembehandlung

Dieser Abschnitt enthält Informationen zur Problembehandlung beim Sichern von Azure PostgreSQL-Datenbanken mit Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Erteilen Sie der Sicherungstresor-MSI **Lesezugriff** auf dem PG-Server, den Sie sichern oder wiederherstellen möchten:

Zum Herstellen einer sicheren Verbindung mit der PostgreSQL-Datenbank verwendet Azure Backup das Authentifizierungsmodell [Verwaltete Dienstidentität (MSI)](../active-directory/managed-identities-azure-resources/overview.md). Dies bedeutet, dass der Sicherungstresor nur auf die Ressourcen zugreifen kann, denen der Benutzer explizit eine Berechtigung erteilt hat.

Eine System-MSI wird dem Tresor zum Zeitpunkt der Erstellung automatisch zugewiesen. Sie müssen dieser Tresor-MSI den Zugriff auf die PostgreSQL-Server einräumen, von denen Sie Datenbanken sichern möchten.

Schritte:

1. Wechseln Sie im Postgres-Server zum Bereich **Zugriffssteuerung (IAM)** .

    ![Bereich „Zugriffssteuerung“](./media/backup-azure-database-postgresql/access-control-pane.png)

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

    ![Rollenzuweisung hinzufügen](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. Geben Sie im rechten Kontextbereich, der geöffnet wird, Folgendes ein:<br>

    **Rolle:** Leser<br>
    **Zugriff zuweisen zu** : Wählen Sie **Sicherungstresor** aus.<br>
    Wenn Sie in der Dropdownliste die Option **Sicherungstresor** nicht finden können, wählen Sie die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus.<br>

    ![Rolle auswählen](./media/backup-azure-database-postgresql/select-role.png)

    **Auswählen** : Geben Sie den Namen des Sicherungstresors ein, in dem Sie diesen Server und dessen Datenbanken sichern möchten.<br>

    ![Eingeben des Namens des Sicherungstresors](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Erstellen Sie einen Benutzer für die Datenbanksicherung, der sich mit Azure Active Directory authentifizieren kann:

Dieser Fehler tritt möglicherweise auf, wenn kein Azure Active Directory-Administrator für den PostgreSQL-Server oder kein Sicherungsbenutzer vorhanden ist, der sich mit Azure Active Directory authentifizieren kann.

Schritte:

Fügen Sie einen Active Directory-Administrator für den OSS-Server hinzu:

Dieser Schritt ist erforderlich, um eine Verbindung mit der Datenbank über einen Benutzer herzustellen, der sich mit Azure Active Directory anstelle eines Kennworts authentifizieren kann. Der Azure AD-Administratorbenutzer in Azure Database for PostgreSQL übt die Rolle **azure_ad_admin** aus. Nur mit der Rolle **azure_ad_admin** können neue Datenbankbenutzer erstellt werden, die sich mit Azure AD authentifizieren können.

1. Wechseln Sie im linken Navigationsbereich der Serveransicht zur Registerkarte „Active Directory-Administrator“, und fügen Sie sich selbst (oder eine andere Person) als Active Directory-Administrator hinzu.

    ![Festlegen eines Active Directory-Administrators](./media/backup-azure-database-postgresql/set-admin.png)

1. Stellen Sie sicher, dass Sie **Speichern** auswählen, um die Benutzereinstellungen zum AD-Administrator zu übernehmen.

    ![Speichern der Benutzereinstellungen zum Active Directory-Administrator](./media/backup-azure-database-postgresql/save-admin-setting.png)

Die Liste der erforderlichen Schritte zum Erteilen der Berechtigungen finden Sie in [diesem Dokument](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx).

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Richten Sie die Netzwerkleitung ein, indem Sie in der Serveransicht das Flag **Zugriff auf Azure-Dienste zulassen** aktivieren. Legen Sie in der Serveransicht im Bereich **Verbindungssicherheit** das Flag **Zugriff auf Azure-Dienste zulassen** auf **Ja** fest.

![Zugriff auf Azure-Dienste erlauben](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Berechtigung zur Wiederherstellung in einem Speicherkontocontainer bei der Wiederherstellung als Dateien

1. Erteilen Sie der Sicherungstresor-MSI die Berechtigung für den Zugriff auf die Speicherkontocontainer über das Azure-Portal.
    1. Wechseln Sie zu **Speicherkonto** -> **Zugriffssteuerung** -> **Rollenzuweisung hinzufügen**.
    1. Weisen Sie Ihrer Sicherungstresor-MSI zunächst die Rolle **Mitwirkender an Storage-Blobdaten** zu.

    ![Zuweisen der Rolle „Mitwirkender an Storage-Blobdaten“](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Außerdem können Sie mit dem Azure CLI-Befehl [az role assignment create](/cli/azure/role/assignment) differenzierte Berechtigungen für den bestimmten Container erteilen, auf dem Sie die Wiederherstellung durchführen.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Ersetzen Sie den Parameter für die zugewiesene Person durch die **Anwendungs-ID** der MSI-Datei des Tresors und den Umfangsparameter, um auf Ihren spezifischen Container zu verweisen.
    1. Um die **Anwendungs-ID** der Tresor-MSI zu erhalten, wählen Sie unter **Anwendungstyp** die Option **Alle Anwendungen** aus:

        ![Auswählen von „Alle Anwendungen“](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Suchen Sie nach dem Tresornamen, und kopieren Sie die Anwendungs-ID:

        ![Suchen nach dem Tresornamen](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Sicherungstresore](backup-vault-overview.md)
