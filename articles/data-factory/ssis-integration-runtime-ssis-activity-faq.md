---
title: Behandeln von Problemen bei der Paketausführung in der SSIS Integration Runtime
description: Dieser Artikel enthält Anleitungen zur Problembehandlung bei der Ausführung von SSIS-Paketen in der SSIS Integration Runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: b4902e1fb7a2a181d3d5b2ce2ac6d1d458500fce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844181"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>Behandeln von Problemen bei der Paketausführung in der SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden die häufigsten Fehler behandelt, die beim Ausführen von SQL Server Integration Services-Paketen (SSIS) in der SSIS Integration Runtime auftreten können. Beschrieben werden die möglichen Ursachen und Maßnahmen zur Behebung der Fehler.

## <a name="where-to-find-logs-for-troubleshooting"></a>Speicherorte von Protokollen zur Problembehandlung

Verwenden Sie das Azure Data Factory-Portal, um die Ausgabe der SSIS-Paketausführungsaktivität zu überprüfen. Die Ausgabe enthält das Ausführungsergebnis, Fehlermeldungen und die Vorgangs-ID. Weitere Informationen finden Sie unter [Überwachen der Pipeline](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline).

Verwenden Sie den SSIS-Katalog (SSISDB), um die Detailprotokolle zur Ausführung zu überprüfen. Weitere Informationen finden Sie unter [Überwachen von ausgeführten Paketen und anderen Vorgängen](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017).

## <a name="common-errors-causes-and-solutions"></a>Häufige Fehler, Ursachen und Lösungen

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>Fehlermeldung: „Verbindungstimeout überschritten“ oder „Der Dienst bei der Verarbeitung Ihrer Anforderung ist auf einen Fehler gestoßen. Versuchen Sie es erneut.“

Hier sind mögliche Ursachen und empfohlenen Maßnahmen:
* Die Datenquelle oder das Ziel ist überlastet. Überprüfen Sie die Auslastung Ihrer Datenquelle oder Ihres Ziels, und prüfen Sie, ob diese über genügend Kapazität verfügen. Wenn Sie beispielsweise Azure SQL-Datenbank verwendet haben, erwägen Sie eine zentrale Hochskalierung, wenn bei der Datenbank wahrscheinlich ein Timeout auftreten wird.
* Das Netzwerk zwischen der SSIS Integration Runtime und der Datenquelle oder dem Ziel ist instabil, insbesondere wenn die Verbindung regionsübergreifend oder zwischen lokalen Standorten und Azure besteht. Wenden Sie das Wiederholungsmuster im SSIS-Paket an, indem Sie diese Schritte ausführen:
  * Stellen Sie sicher, dass Ihre SSIS-Pakete bei einem Ausfall ohne Nebenwirkungen (z. B. Datenverlust oder -duplizierung) erneut ausgeführt werden können.
  * Konfigurieren Sie auf der Registerkarte **Allgemein** die Optionen **Wiederholen** und **Wiederholungsintervall** der Aktivität **SSIS-Paket ausführen**. ![Festlegen von Eigenschaften auf der Registerkarte „Allgemein“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * Legen Sie im Verbindungs-Manager im SSIS-Paket oder in der SSIS-Aktivität für eine ADO.NET- und OLE DB-Quell- oder Zielkomponente **ConnectRetryCount** und **ConnectRetryInterval** fest.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>Fehlermeldung: „Fehler beim Abrufen der ‚...‘-Verbindung durch die ADO .NET-Quelle.“ mit „Netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit der SQL Server-Instanz. Der Server wurde nicht gefunden oder ist nicht zugänglich.“

Dieses Problem bedeutet in der Regel, dass Datenquelle oder -ziel von der SSIS Integration Runtime aus nicht erreichbar ist. Die Gründe können variieren. Probieren Sie diese Maßnahmen:
* Stellen Sie sicher, dass Sie die Datenquelle oder den Zielnamen/die IP-Adresse ordnungsgemäß übergeben.
* Stellen Sie sicher, dass die Firewall ordnungsgemäß eingerichtet ist.
* Stellen Sie sicher, dass das virtuelle Netzwerk ordnungsgemäß konfiguriert ist, wenn die Datenquelle oder das Ziel lokal ist:
  * Sie können überprüfen, ob das Problem durch die Konfiguration eines virtuellen Netzwerks verursacht wurde, indem Sie eine Azure-VM im selben virtuellen Netzwerk bereitstellen. Überprüfen Sie dann, ob auf die Datenquelle oder das Ziel von der Azure VM aus zugegriffen werden kann.
  * Weitere Details zur Verwendung eines virtuellen Netzwerks mit einer SSIS Integration Runtime finden Sie unter [Verbinden einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>Fehlermeldung: „Fehler beim Abrufen der ‚...‘-Verbindung durch die ADO .NET-Quelle.“ mit „Ein verwalteter Verbindungs-Manager konnte nicht erstellt werden.“

Die mögliche Ursache ist, dass der im Paket verwendete ADO.NET-Anbieter nicht in der SSIS Integration Runtime installiert ist. Sie können den Anbieter über ein benutzerdefiniertes Setup installieren. Ausführlichere Informationen zum benutzerdefinierten Setup finden Sie unter [Anpassen des Setups für Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-the-connection--is-not-found"></a>Fehlermeldung: „Die Verbindung ‚...‘ wurde nicht gefunden“

Ein bekanntes Problem in älteren Versionen von SQL Server Management Studio (SSMS) kann diesen Fehler verursachen. Wenn das Paket eine benutzerdefinierte Komponente enthält (z. B. SSIS Azure Feature Pack oder Partnerkomponenten), die nicht auf dem Computer installiert ist, auf dem SSMS zur Durchführung der Bereitstellung verwendet wird, entfernt SSMS die Komponente und verursacht den Fehler. Aktualisieren Sie [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) auf die neueste Version, für die das Problem behoben wurde.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>Fehlermeldung: „SSIS Executor exit code: -1073741819.“ (SSIS-Executor-Exitcode: -1073741819.)

* Mögliche Ursache und empfohlene Maßnahme:
  * Dieser Fehler wird möglicherweise durch die Beschränkung der Excel-Quellen und -Ziele verursacht, wenn mehrere Excel-Quellen oder -Ziele gleichzeitig in mehreren Threads ausgeführt werden. Sie können diese Beschränkung umgehen, indem Sie festlegen, dass die Excel-Komponenten nacheinander ausgeführt werden, oder sie in unterschiedliche Pakete aufteilen und „Task "Paket ausführen"“ anwenden, wobei die Eigenschaft „ExecuteOutOfProcess“ auf „True“ festgelegt ist.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>Fehlermeldung: „Es ist nicht genügend Speicherplatz auf dem Datenträger vorhanden“

Dieser Fehler bedeutet, dass der lokale Datenträger auf dem Knoten mit SSIS Integration Runtime vollständig belegt ist. Prüfen Sie, ob Ihr Paket oder Ihr benutzerdefiniertes Setup viel Datenträgerspeicher belegt:
* Wenn der Datenträger von Ihrem Paket belegt wird, wird Speicherplatz nach Abschluss der Paketausführung freigegeben.
* Wenn der Datenträger von Ihrem benutzerdefinierten Setup belegt wird, müssen Sie die SSIS Integration Runtime beenden, Ihr Skript ändern und die Integration Runtime erneut starten. Der gesamte Azure-Blobcontainer, den Sie für das benutzerdefinierte Setup angegeben haben, wird auf den Knoten mit der SSIS Integration Runtime kopiert. Überprüfen Sie daher, ob sich unter diesem Container unnötige Inhalte befinden.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>Fehlermeldung: „Fehler beim Abrufen der Ressource aus dem Master. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: Code: 300004. Beschreibung: Das Laden der Datei "***" ist fehlgeschlagen.“

* Mögliche Ursache und empfohlene Maßnahme:
  * Wenn die SSIS-Aktivität ein Paket aus dem Dateisystem (Paketdatei oder Projektdatei) ausführt und dieser Fehler auftritt, kann mit den Anmeldeinformationen für den Paketzugriff, die Sie in der SSIS-Aktivität angegeben haben, nicht auf die Projekt-, Paket- oder Konfigurationsdatei zugegriffen werden.
    * Wenn Sie eine Azure-Datei verwenden:
      * Der Dateipfad sollte mit \\\\\<storage account name\>.file.core.windows.net\\\<file share path\> beginnen.
      * Die Domäne muss „Azure“ lauten.
      * Der Benutzername muss \<storage account name\> lauten.
      * Das Kennwort muss \<storage access key\> lauten.
    * Wenn Sie eine lokale Datei verwenden, überprüfen Sie, ob VNet sowie die Anmeldeinformationen und Berechtigungen für den Paketzugriff ordnungsgemäß konfiguriert wurden, damit die Azure-SSIS Integration Runtime auf Ihre lokale Dateifreigabe zugreifen kann.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>Fehlermeldung: „Der in der Verbindung angegebene Dateiname "..." war ungültig.“

* Mögliche Ursache und empfohlene Maßnahme:
  * Es wurde ein ungültiger Dateiname angegeben.
  * Stellen Sie sicher, dass Sie im Verbindungs-Manager anstelle der Kurzform einen vollqualifizierten Domänennamen (FQDN) verwenden.

### <a name="error-message-cannot-open-file-"></a>Fehlermeldung: „Datei ‚...‘ kann nicht geöffnet werden“

Dieser Fehler tritt auf, wenn die Paketausführung in der SSIS Integration Runtime keine Datei auf dem lokalen Datenträger finden kann. Probieren Sie diese Maßnahmen:
* Verwenden Sie nicht den absoluten Pfad im Paket, das in der SSIS Integration Runtime ausgeführt wird. Verwenden Sie stattdessen das aktuelle Arbeitsverzeichnis (.) oder den temporären Ordner (%TEMP%).
* Wenn Sie einige Dateien auf Knoten mit der SSIS Integration Runtime dauerhaft speichern müssen, bereiten Sie die Dateien wie unter [Anpassen des Setups](how-to-configure-azure-ssis-ir-custom-setup.md) beschrieben vor. Alle Dateien im Arbeitsverzeichnis werden nach Abschluss der Ausführung bereinigt.
* Verwenden Sie Azure Files, anstatt die Datei auf dem Knoten mit SSIS Integration Runtime zu speichern. Weitere Informationen finden Sie unter [Verwenden von Azure-Dateifreigaben](/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares).

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>Fehlermeldung: „Das Kontingent der SSISDB-Datenbank wurde erreicht“

Eine mögliche Ursache ist, dass die in Azure SQL-Datenbank oder in der verwalteten SQL-Instanz erstellte SSISDB-Datenbank ihr Kontingent erreicht hat. Probieren Sie diese Maßnahmen:
* Erwägen Sie das Vergrößern der DTU Ihrer Datenbank. Ausführliche Informationen finden Sie unter [SQL Database limits for an logical server](../azure-sql/database/resource-limits-logical-server.md) (SQL-Datenbanklimits für einen logischen Server).
* Überprüfen Sie, ob Ihre Paket ggf. zu viele Protokolle generiert. Falls ja, können Sie zum Bereinigen dieser Protokolle einen elastischen Auftrag konfigurieren. Weitere Informationen finden Sie unter [Bereinigen von SSISDB-Protokollen mit Aufträgen für die elastische Azure-Datenbank](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md).

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>Fehlermeldung: „Die Anforderungsgrenze für die Datenbank beträgt … und wurde erreicht.“

Wenn in der SSIS Integration Runtime viele Pakete parallel ausgeführt werden, kann dieser Fehler auftreten, weil SSISDB die Anforderungsgrenze erreicht hat. Erwägen Sie, den DTC-Wert der SSISDB zu erhöhen, um dieses Problem zu beheben. Ausführliche Informationen finden Sie unter [SQL Database limits for an logical server](../azure-sql/database/resource-limits-logical-server.md) (SQL-Datenbanklimits für einen logischen Server).

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>Fehlermeldung: „SSIS-Vorgang mit unerwartetem Vorgangsstatus fehlgeschlagen: ...“

Der Fehler wird hauptsächlich durch ein vorübergehendes Problem verursacht. Wiederholen Sie die Paketausführung. Wenden Sie das Wiederholungsmuster im SSIS-Paket an, indem Sie diese Schritte ausführen:

* Stellen Sie sicher, dass Ihre SSIS-Pakete bei einem Ausfall ohne Nebenwirkungen (z. B. Datenverlust oder -duplizierung) erneut ausgeführt werden können.
* Konfigurieren Sie auf der Registerkarte **Allgemein** die Optionen **Wiederholen** und **Wiederholungsintervall** der Aktivität **SSIS-Paket ausführen**. ![Festlegen von Eigenschaften auf der Registerkarte „Allgemein“](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* Legen Sie im Verbindungs-Manager im SSIS-Paket oder in der SSIS-Aktivität für eine ADO.NET- und OLE DB-Quell- oder Zielkomponente **ConnectRetryCount** und **ConnectRetryInterval** fest.

### <a name="error-message-there-is-no-active-worker"></a>Fehlermeldung: „Es ist kein aktiver Worker vorhanden.“

Dieser Fehler bedeutet in der Regel, dass die SSIS Integration Runtime einen fehlerhaften Status hat. Überprüfen Sie das Azure-Portal auf den Status und detaillierte Fehler. Weitere Informationen finden Sie unter [Azure-SSIS Integration Runtime ](./monitor-integration-runtime.md#azure-ssis-integration-runtime).

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>Fehlermeldung: „Ihre Integration Runtime kann nicht aktualisiert werden und wird irgendwann nicht mehr funktionieren, da wir nicht auf den Azure Blobcontainer zugreifen können, den Sie für das benutzerdefinierte Setup bereitgestellt haben.“

Dieser Fehler tritt auf, wenn die SSIS Integration Runtime nicht auf den für das benutzerdefinierte Setup konfigurierten Speicher zugreifen kann. Überprüfen Sie, ob der von Ihnen angegebene URI für die Shared Access Signature (SAS) gültig und noch nicht abgelaufen ist.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>Fehlermeldung: „Microsoft OLE DB-Anbieter für Analysis Services. 'Hresult: 0 x 80004005 Beschreibung:' COM-Fehler: COM-Fehler: mscorlib; Ein Aufrufziel hat einen Ausnahmefehler verursacht“

Eine mögliche Ursache ist, dass der Benutzername oder das Kennwort mit aktivierter Azure AD Multi-Factor Authentication für die Authentifizierung bei Azure Analysis Services konfiguriert ist. Diese Authentifizierung wird in der SSIS Integration Runtime nicht unterstützt. Versuchen Sie, ein Dienstprinzipal für die Authentifizierung bei Azure Analysis Services zu verwenden:

1. Bereiten Sie ein Dienstprinzipal vor, wie in [Automatisierung mit Dienstprinzipalen](../analysis-services/analysis-services-service-principal.md) beschrieben.
2. Konfigurieren Sie im Connection Manager die Option **SQL-Server-Authentifizierung verwenden**. Legen Sie **AppID** als Benutzernamen und **clientSecret** als Kennwort fest.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>Fehlermeldung: „Fehler beim Abrufen der {GUID}-Verbindung durch die ADO .NET-Quelle. Fehlermeldung: Die Anmeldung für den Benutzer 'NT AUTHORITY\ANONYMOUS LOGON'“ ist fehlgeschlagen, wenn eine verwaltete Identität verwendet wird

Stellen Sie sicher, dass Sie die Authentifizierungsmethode von Connection Manager nicht als **Active Directory-Kennwortauthentifizierung** konfigurieren, wenn der Parameter *ConnectUsingManagedIdentity* auf **True** festgelegt ist. Sie können stattdessen die Konfiguration **SQL-Authentifizierung** verwenden, die ignoriert wird, wenn *ConnectUsingManagedIdentity* festgelegt ist.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>Fehlermeldung: "0xC020801F an ..., OData-Quelle [...]: Eine verwaltete Verbindung kann aus dem Laufzeitverbindungs-Manager nicht abgerufen werden“

Eine mögliche Ursache ist, dass TLS (Transport Layer Security) in SSIS Integration Runtime nicht aktiviert und für Ihre OData-Quelle erforderlich ist. Sie können TLS in SSIS Integration Runtime durch Anpassen des Setups aktivieren. Weitere Informationen finden Sie unter [Sie können keine Verbindung mit Project Online OData von SSIS herstellen](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) und [Anpassen des Setups für Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>Fehlermeldung: „Fehler beim Anfordern der Stagingaufgabe mit Vorgangs-GUID ... aufgrund des Fehlers: Fehler beim Verteilen des Stagingvorgangs mit der folgenden Fehlermeldung: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgentException: Fehler beim Laden des Datenproxys.“

Stellen Sie sicher, dass Ihre Azure-SSIS Integration Runtime mit selbstgehosteter Integration Runtime konfiguriert ist. Weitere Details finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>Fehlermeldung: „Status der Stagingaufgabe: Fehler. Fehler bei Stagingaufgabe: Fehlercode: 2010, ErrorMessage: Die selbstgehostete Integration Runtime ... ist offline“

Stellen Sie sicher, dass Ihre selbstgehostete Integration Runtime installiert ist und gestartet wurde. Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](create-self-hosted-integration-runtime.md).

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>Fehlermeldung: „Fehler bei Stagingaufgabe: Fehlercode: 2906, ErrorMessage: Fehler bei der Paketausführung. Ausgabe: {"OperationErrorMessages": „Fehler: Der angeforderte OLE DB Anbieter... ist nicht registriert. Wenn der 64-Bit-Treiber nicht installiert ist, führen Sie das Paket im 32-Bit-Modus aus... „

Stellen Sie sicher, dass der entsprechende Anbieter, der von Ihren OLE DB-Connectors in Ihrem Paket verwendet wird, auf dem Computer mit der selbstgehosteten Integration Runtime ordnungsgemäß installiert ist. Weitere Details finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir).

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>Fehlermeldung: „Fehler bei Stagingaufgabe: Fehlercode: 2906, ErrorMessage: Fehler bei der Paketausführung. Ausgabe: {"OperationErrorMessages": „Fehler: System.IO.FileLoadException: Dabei oder Assembly 'Microsoft.WindowsAzure.Storage, Version=..., Culture=neutral, PublicKeyToken=31bf3856ad364e35' oder eine ihrer Abhängigkeiten kann nicht geladen werden. Die Manifestdefinition der gefundenen Assembly stimmt nicht mit dem Assemblyverweis ‚...‘ überein.

Eine mögliche Ursache ist, dass Ihre selbstgehosteter Integration Runtime nicht ordnungsgemäß installiert oder aktualisiert wurde. Laden Sie die neueste selbstgehostete Integration Runtime herunter, und installieren Sie sie erneut. Weitere Informationen finden Sie unter [Erstellen und Konfigurieren einer selbstgehosteten Integration Runtime](create-self-hosted-integration-runtime.md#installation-best-practices).

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>Fehlermeldung: „Eine Verbindung ist erforderlich, wenn Metadaten angefordert werden. Wenn Sie offline arbeiten, deaktivieren Sie im SSIS-Menü ‚Offline arbeiten‘, um die Verbindung zu aktivieren“

* Mögliche Ursache und empfohlene Maßnahme:
  * Wenn im Ausführungsprotokoll auch die Warnmeldung „Die Komponente unterstützt die Verwendung des Verbindungs-Managers nicht, wenn ConnectByProxy auf TRUE festgelegt ist“ enthalten ist, bedeutet dies, dass ein Verbindungs-Manager für eine Komponente verwendet wird, die „ConnectByProxy“ noch nicht unterstützt. Die unterstützten Komponenten finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy).
  * Das Ausführungsprotokoll befindet sich im [SSMS-Bericht](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) oder in dem Protokollordner, den Sie in der SSIS-Paketausführungsaktivität angegeben haben.
  * Alternativ kann ein VNet für den Zugriff auf lokale Daten verwendet werden. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>Fehlermeldung: „Status der Stagingaufgabe: Fehler. Fehler bei Stagingaufgabe: Fehlercode: 2906, ErrorMessage: Fehler bei der Paketausführung. Ausgabe: {"OperationErrorMessages": "SSIS Executor exit code: -1.\n", "LogLocation": "...\\SSISTelemetry\\ExecutionLog\\...", "effectiveIntegrationRuntime": "...", "executionDuration": ..., "durationInQueue": { "integrationRuntimeQueue": ... }}"

Stellen Sie sicher, dass die Visual C++-Runtime auf dem selbstgehosteten Integration Runtime-Computer installiert ist. Weitere Details finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR in ADF](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir).

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>Unerwartetes Auslösen der Ausführung mehrerer Pakete

* Mögliche Ursache und empfohlene Maßnahme:
  * Eine Aktivität in einer gespeicherten ADF-Prozedur oder eine Nachschlageaktivität wird verwendet, um die Ausführung von SSIS-Paketen auszulösen. Der t-sql-Befehl kann auf ein vorübergehendes Problem stoßen und die Wiederholung auslösen, was zu einer Ausführung mehrerer Pakete führen kann.
  * Verwenden Sie stattdessen die Aktivität ExecuteSSISPackage, mit der sichergestellt wird, dass die Paketausführung nicht erneut erfolgt, es sei denn, der Benutzer hat in der Aktivität die Anzahl der Wiederholungen festgelegt. Details finden Sie unter [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * Optimieren Sie Ihren t-sql-Befehl, um ihn erneut ausführen zu können, indem Sie prüfen, ob eine Ausführung bereits ausgelöst wurde.

### <a name="package-execution-takes-too-long"></a>Paketausführung dauert zu lange

Hier sind mögliche Ursachen und empfohlenen Maßnahmen:

* Es wurden zu viele Paketausführungen für die SSIS Integration Runtime geplant. Alle diese Ausführungen warten in einer Warteschlange auf ihre Ausführung.
  * Bestimmen Sie das Maximum mit dieser Formel:

    Maximale Anzahl paralleler Ausführungen pro IR = Knotenanzahl x Maximale parallele Ausführung pro Knoten
  * Informationen zum Festlegen der Knotenanzahl und der maximalen parallelen Ausführung pro Knoten finden Sie unter [Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).
* Die SSIS Integration Runtime wird beendet oder hat einen fehlerhaften Status. Um zu erfahren, wie Sie den Status und die Fehler der SSIS Integration Runtime überprüfen können, finden Sie unter [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).

Wir empfehlen auch, dass Sie auf der Registerkarte **Allgemein** ein Zeitlimit festlegen: ![Legen Sie Eigenschaften auf der Registerkarte „Allgemein“ fest.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

### <a name="poor-performance-in-package-execution"></a>Schlechte Leistung bei der Paketausführung

Probieren Sie diese Maßnahmen:

* Stellen Sie sicher, dass sich die SSIS Integration Runtime in der gleichen Region wie Datenquelle und -ziel befindet.

* Legen Sie den Protokolliergrad der Paketausführung auf **Leistung** fest, um Informationen zur Dauer der Ausführung jeder Komponente zu sammeln. Weitere Informationen finden Sie unter [Integration Services-Protokollierung (SSIS)](/sql/integration-services/performance/integration-services-ssis-logging).

* Überprüfen Sie die Leistung des IR-Knotens im Azure-Portal:
  * Informationen zur Überwachung der SSIS Integration Runtime finden Sie unter [Azure-SSIS Integration Runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime).
  * Sie finden den CPU-/Speicherverlauf für die SSIS Integration Runtime, indem Sie sich im Azure Portal die Metriken der Data Factory ansehen.
    ![Überwachung der Metriken der SSIS Integration Runtime](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)