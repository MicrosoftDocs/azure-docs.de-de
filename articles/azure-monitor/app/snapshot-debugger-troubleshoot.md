---
title: Problembehandlung für den Azure Application Insights-Momentaufnahmedebugger
description: Dieser Artikel enthält Schritte zur Problembehandlung sowie Informationen, um Entwickler bei der Aktivierung oder Verwendung des Application Insights-Momentaufnahmedebuggers zu unterstützen.
ms.topic: conceptual
author: cweining
ms.date: 03/07/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 49a4ab0315dad539a594a20e53eae9fd2890e551
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504967"
---
# <a name="troubleshoot-problems-enabling-application-insights-snapshot-debugger-or-viewing-snapshots"></a><a id="troubleshooting"></a> Behandeln von Problemen beim Aktivieren des Application Insights-Momentaufnahmedebuggers oder Anzeigen von Momentaufnahmen
Wenn Sie den Application Insights-Momentaufnahmedebugger für Ihre Anwendung aktiviert haben, aber keine Momentaufnahmen für Ausnahmen angezeigt werden, können Sie diese Anweisungen zur Problembehandlung verwenden. Es kann viele verschiedene Gründe geben, warum keine Momentaufnahmen generiert werden. Sie können die Integritätsprüfung für Momentaufnahmen ausführen, um einige der möglichen Ursachen zu ermitteln.

## <a name="use-the-snapshot-health-check"></a>Verwenden der Integritätsprüfung für Momentaufnahmen
Einige verbreitete Probleme führen dazu, dass „Debugmomentaufnahme öffnen“ nicht angezeigt wird. Beispiele wären etwa die Verwendung eines veralteten Snapshot Collectors, das Erreichen des täglichen Uploadlimits oder ein zeitaufwendiger Uploadvorgang für die Momentaufnahme. Verwenden Sie die Integritätsprüfung für Momentaufnahmen, um verbreitete Probleme zu behandeln.

Der Ausnahmenbereich der End-to-End-Überwachungsansicht enthält einen Link, über den Sie zur Integritätsprüfung für Momentaufnahmen gelangen.

![Aufrufen der Integritätsprüfung für Momentaufnahmen](./media/snapshot-debugger/enter-snapshot-health-check.png)

Die interaktive, chatähnliche Oberfläche sucht nach verbreiteten Problemen und unterstützt Sie bei der Behebung.

![Integritätsprüfung](./media/snapshot-debugger/healthcheck.png)

Sollte sich das Problem dadurch nicht beheben lassen, lesen Sie die folgenden Schritte zur manuellen Problembehandlung:

## <a name="verify-the-instrumentation-key"></a>Überprüfen des Instrumentierungsschlüssels

Vergewissern Sie sich, dass Sie in Ihrer veröffentlichten Anwendung den richtigen Instrumentierungsschlüssel verwenden. Der Instrumentierungsschlüssel wird in der Regel aus der Datei „ApplicationInsights.config“ gelesen. Vergewissern Sie sich, dass der Wert dem Instrumentierungsschlüssel für die im Portal angezeigte Application Insights-Ressource entspricht.

## <a name="check-ssl-client-settings-aspnet"></a><a id="SSL"></a>Überprüfen der SSL-Clienteinstellungen (ASP.NET)

Wenn Sie auf einem virtuellen Computer eine ASP.NET-Anwendung haben, die in Azure App Service oder IIS gehostet wird, kann Ihre Anwendung aufgrund eines fehlenden SSL-Sicherheitsprotokolls möglicherweise keine Verbindung mit dem Dienst „Momentaufnahmedebugger“ herstellen.
[Der Endpunkt von Momentaufnahmedebugger erfordert die TLS-Version 1.2](snapshot-debugger-upgrade.md?toc=/azure/azure-monitor/toc.json). Die Gruppe der SSL-Sicherheitsprotokolle ist eine der Tücken, die sich durch den Wert „httpRuntime targetFramework“ im Abschnitt „system.web“ der Datei „web.config“ ergibt. Wenn „httpRuntime targetFramework“ 4.5.2 oder niedriger ist, ist TLS 1.2 nicht standardmäßig inbegriffen.

> [!NOTE]
> Der Wert von „httpRuntime targetFramework“ ist unabhängig von dem beim Erstellen Ihrer Anwendung verwendeten Zielframework.

Um die Einstellung zu überprüfen, öffnen Sie die Datei „web.config“, und wechseln Sie zum Abschnitt „system.web“. Vergewissern Sie sich, dass `targetFramework` für `httpRuntime` auf 4.6 oder höher festgelegt ist.

   ```xml
   <system.web>
      ...
      <httpRuntime targetFramework="4.7.2" />
      ...
   </system.web>
   ```

> [!NOTE]
> Das Ändern des Werts „httpRuntime targetFramework“ wirkt sich auf das Verhalten Ihrer Anwendung zur Laufzeit aus und kann zu anderen, geringfügigen Verhaltensänderungen führen. Testen Sie Ihre Anwendung nach dieser Änderung unbedingt gründlich. Eine vollständige Liste der Kompatibilitätsänderungen finden Sie unter https://docs.microsoft.com/dotnet/framework/migration-guide/application-compatibility#retargeting-changes

> [!NOTE]
> Wenn „targetFramework“ 4.7 oder höher ist, bestimmt Windows die verfügbaren Protokolle. In Azure App Service ist TLS 1.2 verfügbar. Wenn Sie jedoch Ihren eigenen virtuellen Computer verwenden, müssen Sie möglicherweise TLS 1.2 im Betriebssystem aktivieren.

## <a name="preview-versions-of-net-core"></a>Vorschauversionen von .NET Core
Wenn die Anwendung eine Vorschauversion von .NET Core verwendet und der Momentaufnahmedebugger über den [Application Insights-Bereich](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) im Portal aktiviert wurde, wird der Momentaufnahmedebugger möglicherweise nicht gestartet. Befolgen Sie zuerst die Anleitung unter [Aktivieren des Momentaufnahmedebuggers für andere Umgebungen](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json), um **zusätzlich** zur Aktivierung im [Application Insights-Bereich](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) das NuGet-Paket [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) in die Anwendung einzubinden.


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Upgraden auf die neueste Version des NuGet-Pakets

Wenn der Momentaufnahmedebugger über den [Application Insights-Bereich im Portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) aktiviert wurde, sollte in Ihrer Anwendung bereits das aktuellste NuGet-Paket ausgeführt werden. Wurde der Momentaufnahmedebugger durch Einbinden des NuGet-Pakets [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) aktiviert, verwenden Sie den NuGet-Paket-Manager in Visual Studio, um sicherzustellen, dass Sie die neueste Version von „Microsoft.ApplicationInsights.SnapshotCollector“ verwenden.

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./snapshot-collector-release-notes.md).

## <a name="check-the-uploader-logs"></a>Überprüfen der Uploaderprotokolle

Nachdem eine Momentaufnahme erstellt wurde, wird eine Minidumpdatei (DMP) auf dem Datenträger erstellt. Diese Minidumpdatei wird durch einen separaten Uploaderprozess erstellt und zusammen mit allen dazugehörigen PDB-Dateien in den Speicher des Application Insights-Momentaufnahmedebuggers hochgeladen. Nachdem die Minidumpdatei erfolgreich hochgeladen wurde, wird sie vom Datenträger gelöscht. Die Protokolldateien für den Uploaderprozess bleiben auf dem Datenträger erhalten. In einer App Service-Umgebung finden Sie diese Protokolle unter `D:\Home\LogFiles`. Verwenden Sie die Verwaltungswebsite von Kudu für App Service, um nach diesen Protokolldateien zu suchen.

1. Öffnen Sie im Azure-Portal Ihre App Service-Anwendung.
2. Klicken Sie auf _*Erweiterte Tools**, oder suchen Sie nach **Kudu**.
3. Klicken Sie auf **Start**.
4. Wählen Sie im Dropdown-Listenfeld **Debugging-Konsole** die Option **CMD** aus.
5. Klicken Sie auf **LogFiles**.

Daraufhin sollte mindestens eine Datei mit einem Namen angezeigt werden, der mit `Uploader_` oder `SnapshotUploader_` beginnt und die Erweiterung `.log` aufweist. Klicken Sie auf das entsprechende Symbol, um Protokolldateien herunterzuladen oder in einem Browser zu öffnen.
Der Dateiname enthält ein eindeutiges Suffix, mit dem die App Service-Instanz identifiziert wird. Wenn Ihre App Service-Instanz auf mehreren Computern gehostet wird, werden separate Protokolldateien für jeden Computer erstellt. Wenn der Uploader eine neue Minidumpdatei erkennt, wird diese in der Protokolldatei erfasst. Hier ist ein Beispiel für eine erfolgreiche Momentaufnahme und einen Upload angegeben:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> Das obige Beispiel stammt aus Version 1.2.0 des NuGet-Pakets „Microsoft.ApplicationInsights.SnapshotCollector“. In früheren Versionen hatte der Uploader-Prozess die Bezeichnung `MinidumpUploader.exe`, und das Protokoll enthielt weniger Details.

Im vorherigen Beispiel lautet der Instrumentierungsschlüssel `c12a605e73c44346a984e00000000000`. Dieser Wert sollte dem Instrumentierungsschlüssel für Ihre Anwendung entsprechen.
Die Minidumpdatei ist einer Momentaufnahme mit der ID `139e411a23934dc0b9ea08a626db16c5` zugeordnet. Sie können diese ID später verwenden, um nach der zugehörigen Ausnahmetelemetrie in Application Insights Analytics zu suchen.

Der Uploader sucht alle 15 Minuten nach neuen PDB-Dateien. Hier sehen Sie ein Beispiel:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Bei Anwendungen, die _nicht_ in App Service gehostet werden, befinden sich die Uploaderprotokolle im selben Ordner wie die Minidumpdateien: `%TEMP%\Dumps\<ikey>` (`<ikey>` steht hierbei für Ihren Instrumentierungsschlüssel).

## <a name="troubleshooting-cloud-services"></a>Problembehandlung bei Cloud Services
Für Rollen in Cloud Services ist der standardmäßige temporäre Ordner möglicherweise zu klein, um die MiniDump-Dateien zu speichern, was dazu führt, dass Momentaufnahmen verloren gehen.
Der erforderliche Speicherplatz hängt vom gesamten Arbeitssatz Ihrer Anwendung und der Anzahl gleichzeitiger Momentaufnahmen ab.
Der Arbeitssatz einer 32-Bit-ASP.NET-Webrolle liegt in der Regel zwischen 200 MB und 500 MB.
Lassen Sie mindestens zwei gleichzeitige Momentaufnahmen zu.
Wenn Ihre Anwendung beispielsweise 1 GB des gesamten Arbeitssatzes verwendet, sollten Sie sicherstellen, dass ein Speicherplatz von mindestens 2 GB zum Speichern von Momentaufnahmen zur Verfügung steht.
Führen Sie die folgenden Schritte aus, um Ihre Clouddienstrolle mit einer dedizierten lokalen Ressource für Momentaufnahmen zu konfigurieren.

1. Fügen Sie Ihrem Clouddienst eine neue lokale Ressource hinzu, indem Sie die Clouddienst-Definitionsdatei (.csdef) bearbeiten. Im folgenden Beispiel wird eine Ressource namens `SnapshotStore` mit einer Größe von 5 GB definiert.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Ändern Sie den Startcode Ihrer Rolle, um eine Umgebungsvariable hinzuzufügen, die auf die lokale Ressource `SnapshotStore` zeigt. Für Workerrollen sollte der Code der `OnStart`-Methode Ihrer Rolle hinzugefügt werden:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Für Webrollen (ASP.NET) sollte der Code der `Application_Start`-Methode Ihrer Webanwendung hinzugefügt werden:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Aktualisieren Sie die Datei „ApplicationInsights.config“ Ihrer Rolle, um den von `SnapshotCollector` verwendeten Speicherort des temporären Ordners zu überschreiben.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Überschreiben des Momentaufnahmeordners

Wenn der Snapshot Collector gestartet wird, versucht er, einen Ordner auf dem Datenträger zu finden, der für die Ausführung des Snapshot Uploader-Prozesses geeignet ist. Der ausgewählte Ordner wird als Momentaufnahmeordner bezeichnet.

Der Snapshot Collector überprüft einige bekannte Speicherorte und stellt dabei sicher, dass er über die Berechtigungen zum Kopieren der Binärdateien für den Snapshot Uploader verfügt. Folgende Umgebungsvariablen werden verwendet:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Wenn kein geeigneter Ordner gefunden werden kann, meldet der Snapshot Collector einen Fehler, dass _kein geeigneter Momentaufnahmeordner gefunden wurde_.

Bei einem fehlerhaften Kopiervorgang meldet Snapshot Collector den Fehler `ShadowCopyFailed`.

Wenn der Uploader nicht gestartet werden kann, meldet Snapshot Collector den Fehler `UploaderCannotStartFromShadowCopy`. Der Text der Meldung enthält häufig `System.UnauthorizedAccessException`. Dieser Fehler tritt gewöhnlich auf, wenn die Anwendung unter einem Konto mit eingeschränkten Berechtigungen ausgeführt wird. Das Konto verfügt über die Berechtigung zum Schreiben in den Momentaufnahmeordner, aber es hat keine Berechtigung zum Ausführen von Code.

Da diese Fehler in der Regel während des Starts auftreten, folgt darauf meist der Fehler `ExceptionDuringConnect`, der aussagt, dass _der Uploader nicht gestartet werden konnte_.

Um diese Fehler zu umgehen, können Sie den Momentaufnahmeordner manuell über die Konfigurationsoption `ShadowCopyFolder` angeben. Sie können beispielsweise „ApplicationInsights.config“ verwenden:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Oder Sie können „appsettings.json“ mit einer .NET Core-Anwendung verwenden:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Suchen nach Ausnahmen mit Momentaufnahmen über die Application Insights-Suche

Wenn eine Momentaufnahme erstellt wird, wird die auslösende Ausnahme mit einer Momentaufnahme-ID gekennzeichnet. Diese Momentaufnahme-ID ist als benutzerdefinierte Eigenschaft enthalten, wenn die Ausnahmetelemetrie an Application Insights gemeldet wird. In Application Insights können Sie über die **Suche** nach sämtlicher Telemetrie mit der benutzerdefinierten Eigenschaft `ai.snapshot.id` suchen.

1. Navigieren Sie im Azure-Portal zu Ihrer Application Insights-Ressource.
2. Klicken Sie auf **Suchen**.
3. Geben Sie in das Textfeld „Suchen“ `ai.snapshot.id` ein, und drücken Sie die Eingabetaste.

![Suchen nach Telemetrie im Portal mit einer Momentaufnahme-ID](./media/snapshot-debugger/search-snapshot-portal.png)

Wenn bei dieser Suche keine Ergebnisse zurückgegeben werden, wurden Application Insights im ausgewählten Zeitraum keine Momentaufnahmen für Ihre Anwendung gemeldet.

Um über die Uploaderprotokolle nach einer bestimmten Momentaufnahme-ID zu suchen, geben Sie die jeweilige ID in das Suchfeld ein. Wenn Sie für eine Momentaufnahme, die Ihres Wissens nach hochgeladen wurde, keine Telemetrie finden können, gehen Sie folgendermaßen vor:

1. Vergewissern Sie sich, dass Sie die Suche in der richtigen Application Insights-Ressource durchführen, indem Sie den Instrumentierungsschlüssel überprüfen.

2. Passen Sie mithilfe des Zeitstempels aus dem Uploaderprotokoll den Filter „Zeitbereich“ an, um den jeweiligen Zeitbereich bei der Suche zu berücksichtigen.

Wenn trotzdem keine Ausnahme mit dieser Momentaufnahme-ID angezeigt wird, wurde Application Insights die Ausnahmetelemetrie nicht gemeldet. Diese Situation kann vorkommen, wenn Ihre Anwendung abgestürzt ist, nachdem die Momentaufnahme erfasst wurde, jedoch bevor die Ausnahmetelemetrie gemeldet wurde. Überprüfen Sie in diesem Fall die App Service-Protokolle unter `Diagnose and solve problems`, um festzustellen, ob unerwartete Neustarts oder Ausnahmefehler vorliegen.

## <a name="edit-network-proxy-or-firewall-rules"></a>Bearbeiten von Netzwerkproxy- oder Firewallregeln

Wenn Ihre Anwendung über einen Proxy oder über eine Firewall mit dem Internet verbunden ist, müssen Sie ggf. die Regeln bearbeiten, damit Ihre Anwendung mit dem Momentaufnahmedebugger-Dienst kommunizieren kann. Die vom Momentaufnahmedebugger verwendeten IP-Adressen sind im Azure Monitor-Diensttag enthalten.
