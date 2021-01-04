---
title: Bereitstellen eines Split-Merge-Diensts
description: Verwenden Sie das Split-Merge-Tool, um Daten zwischen Sharddatenbanken zu verschieben.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 71aad7699c5af6ce2a1b9d82a340138200cfb5e1
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792071"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Bereitstellen eines Split-Merge-Diensts, um Daten zwischen Sharddatenbanken zu verschieben
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mit dem Split-Merge-Tool können Sie Daten zwischen Sharddatenbanken verschieben. Siehe [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken](elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Herunterladen der Split-Merge-Pakete

1. Laden Sie die neueste NuGet-Version von [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget)herunter.

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in das Sie „nuget.exe“ heruntergeladen haben. Der Download enthält PowerShell-Befehle.

1. Laden Sie das neueste Split-Merge-Paket mit folgendem Befehl in das aktuelle Verzeichnis herunter:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Die Dateien werden in einem Verzeichnis mit dem Namen **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** abgelegt, wobei *x.x.xxx.x* der Versionsnummer entspricht. Die Split-Merge-Dienstdateien befinden sich im Unterverzeichnis **content\splitmerge\service** und die Split-Merge-PowerShell-Skripts (und erforderlichen Client-DLLs) im Unterverzeichnis **content\splitmerge\powershell** .

## <a name="prerequisites"></a>Voraussetzungen

1. Erstellen Sie eine Datenbank in Azure SQL-Datenbank, die als Split-Merge-Statusdatenbank verwendet wird. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Erstellen Sie eine neue **SQL-Datenbank** . Geben Sie einen Namen für die Datenbank ein, und erstellen Sie einen neuen Administrator und ein neues Kennwort. Achten Sie darauf, dass Sie den Namen und das Kennwort zur späteren Verwendung notieren.

1. Achten Sie darauf, dass Ihr Server Verbindungen mit Azure-Diensten zulässt. Stellen Sie im Portal in den **Firewalleinstellungen** sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** auf **Ein** festgelegt ist. Klicken Sie auf das Symbol "Speichern".

1. Erstellen Sie ein Azure-Speicherkonto für die Diagnoseausgabe.

1. Erstellen Sie einen Azure-Clouddienst für den Split-Merge-Dienst.

## <a name="configure-your-split-merge-service"></a>Konfigurieren des Split-Merge-Diensts

### <a name="split-merge-service-configuration"></a>Konfiguration des Split-Merge-Diensts

1. Erstellen Sie in dem Ordner, in den Sie die Split-Merge-Assemblys heruntergeladen haben, eine Kopie der Datei *ServiceConfiguration.Template.cscfg* , die zusammen mit *SplitMergeService.cspkg* bereitgestellt wurde. Benennen Sie die Datei in *ServiceConfiguration.cscfg* um.

1. Öffnen Sie *ServiceConfiguration.cscfg* in einem Texteditor, z.B. Visual Studio, mit dem Eingaben wie das Format von Zertifikatfingerabdrücken überprüft werden.

1. Erstellen Sie eine neue Datenbank, oder wählen Sie eine vorhandene Datenbank als Statusdatenbank für Teilungs-/Zusammenführungsvorgänge (Split/Merge) aus, und rufen Sie die Verbindungszeichenfolge dieser Datenbank ab.

   > [!IMPORTANT]
   > Zu diesem Zeitpunkt muss die Statusdatenbank die Sortierung „Latin“ verwenden (SQL\_Latin1\_General\_CP1\_CI\_AS). Weitere Informationen finden Sie unter [Name der Windows-Sortierung (Transact-SQL)](/sql/t-sql/statements/windows-collation-name-transact-sql).

   Bei Azure SQL-Datenbank weist die Verbindungszeichenfolge in der Regel folgendes Format auf:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Geben Sie die Verbindungszeichenfolge in der *CSCFG* -Datei sowohl im Abschnitt für die **SplitMergeWeb** -Rolle als auch für die **SplitMergeWorker** -Rolle der Einstellung „ElasticScaleMetadata“ ein.

1. Geben Sie für die **SplitMergeWorker** -Rolle eine gültige Verbindungszeichenfolge für den Azure-Speicher für die Einstellung **WorkerRoleSynchronizationStorageAccountConnectionString** ein.

### <a name="configure-security"></a>Konfigurieren der Sicherheit

Ausführliche Anweisungen zum Konfigurieren der Dienstsicherheit finden Sie unter [Split-Merge-Sicherheitskonfiguration](elastic-scale-split-merge-security-configuration.md).

Für eine einfache Testbereitstellung, die für dieses Tutorial geeignet ist, führen Sie einige wenige Konfigurationsschritte aus, um den Dienst zu aktivieren und auszuführen. Durch diese Schritte werden nur ein Computer und Konto für die Kommunikation mit dem Dienst aktiviert.

### <a name="create-a-self-signed-certificate"></a>Erstellen eines selbstsignierten Zertifikats

Erstellen Sie ein neues Verzeichnis, und führen Sie aus diesem Verzeichnis über ein Fenster [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) folgenden Befehl aus:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Sie werden aufgefordert, ein Kennwort zum Schutz des privaten Schlüssels anzugeben. Geben Sie ein sicheres Kennwort ein, und bestätigen Sie es. Danach werden Sie aufgefordert, das zu verwendende Kennwort noch einmal einzugeben. Klicken Sie zum Schluss auf **Ja** , um das Kennwort in den Speicher vertrauenswürdiger Stammzertifizierungsstellen zu importieren.

### <a name="create-a-pfx-file"></a>Erstellen einer PFX-Datei

Führen Sie den folgenden Befehl im gleichen Fenster aus, in dem „makecert“ ausgeführt wurde. Verwenden Sie das gleiche Kennwort, das Sie zum Erstellen des Zertifikats verwendet haben:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importieren des Clientzertifikats in den persönlichen Speicher

1. Doppelklicken Sie im Windows-Explorer auf *MyCert.pfx* .
2. Wählen Sie im **Zertifikatimport-Assistenten** die Option **Aktueller Benutzer** aus, und klicken Sie auf **Weiter** .
3. Bestätigen Sie den Dateipfad, und klicken Sie auf **Weiter** .
4. Geben Sie das Kennwort ein, lassen Sie **Alle erweiterten Eigenschaften mit einbeziehen** aktiviert, und klicken Sie auf **Weiter** .
5. Lassen Sie **Zertifikatspeicher automatisch auswählen[…]** aktiviert, und klicken Sie auf **Weiter** .
6. Klicken Sie auf **Fertig stellen** und auf **OK** .

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Hochladen der PFX-Datei in den Cloud-Dienst

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie **Cloud-Dienste** .
3. Wählen Sie den oben für den Split-Merge-Dienst erstellten Cloud-Dienst aus.
4. Klicken Sie im oberen Menü auf **Zertifikate** .
5. Klicken Sie in der unteren Leiste auf **Hochladen** .
6. Wählen Sie die PFX-Datei aus, und geben Sie dasselbe Kennwort wie oben ein.
7. Nach Abschluss kopieren Sie den Zertifikatfingerabdruck aus dem neuen Eintrag in der Liste.

### <a name="update-the-service-configuration-file"></a>Aktualisieren der Dienstkonfigurationsdatei

Fügen Sie den oben kopierten Zertifikatfingerabdruck in das thumbprint-Attribut bzw. value-Attribut der folgenden Einstellungen ein.
Für die Workerrolle:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Für die Webrolle:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Beachten Sie, dass in Produktionsbereitstellungen für die Zertifizierungsstelle, die Verschlüsselung, das Serverzertifikat und die Clientzertifikate getrennte Zertifikate verwendet werden müssen. Ausführliche Anweisungen finden Sie unter [Sicherheitskonfiguration](elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Bereitstellen des Diensts

1. [Navigieren Sie zum Azure-Portal](https://portal.azure.com).
2. Wählen Sie den Clouddienst aus, den Sie zuvor erstellt haben.
3. Klicken Sie auf **Overview** .
4. Wählen Sie die Stagingumgebung aus, und klicken Sie dann auf **Hochladen** .
5. Geben Sie im Dialogfeld eine Bezeichnung für die Bereitstellung ein. Klicken Sie sowohl unter „Paket“ als auch „Konfiguration“ auf „Aus lokaler Ressource“, und wählen Sie die Datei *SplitMergeService.cspkg* und die zuvor konfigurierte CSCFG-Datei aus.
6. Stellen Sie sicher, dass das Kontrollkästchen **Auch dann bereitstellen, wenn für eine oder mehrere Rollen nur eine Instanz vorhanden ist** aktiviert ist.
7. Aktivieren Sie die Schaltfläche mit dem Häkchen unten rechts, um die Bereitstellung zu starten. Die Ausführung dauert einige Minuten.

## <a name="troubleshoot-the-deployment"></a>Problembehandlung bei der Bereitstellung

Wenn Ihre Webrolle nicht online geschaltet wird, liegt möglicherweise ein Problem mit der Sicherheitskonfiguration vor. Überprüfen Sie, ob TLS/SSL wie oben beschrieben konfiguriert ist.

Wenn die Workerrolle nicht online geschaltet wird, während der Vorgang bei der Webrolle erfolgreich ist, liegt sehr wahrscheinlich ein Problem beim Herstellen der Verbindung mit der Statusdatenbank vor, die Sie zuvor erstellt haben.

- Stellen Sie sicher, dass die Verbindungszeichenfolge in der CSCFG-Datei richtig ist.
- Vergewissern Sie sich, dass der Server und die Datenbank vorhanden sind und dass die Benutzer-ID und das Kennwort korrekt sind.
- Bei Azure SQL-Datenbank sollte die Verbindungszeichenfolge folgendes Format aufweisen:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Stellen Sie sicher, dass der Servername nicht mit **https://** beginnt.
- Achten Sie darauf, dass Ihr Server Verbindungen mit Azure-Diensten zulässt. Öffnen Sie dazu Ihre Datenbak im Portal, und stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** auf **Ein** ** festgelegt ist.

## <a name="test-the-service-deployment"></a>Testen der Dienstbereitstellung

### <a name="connect-with-a-web-browser"></a>Herstellen einer Verbindung mit einem Webbrowser

Ermitteln Sie den Webendpunkt Ihres Split-Merge-Diensts. Sie finden diesen im Portal, indem Sie zu **Übersicht** für Ihren Clouddienst wechseln und auf der rechten Seite unter **Website-URL** nachschauen. Ersetzen Sie **http://** durch **https://** , da die Standardsicherheitseinstellungen den HTTP-Endpunkt deaktivieren. Laden Sie die Seite für diese URL in Ihrem Browser.

### <a name="test-with-powershell-scripts"></a>Testen mit PowerShell-Skripts

Die Bereitstellung und Ihre Umgebung können getestet werden, indem Sie die im Paket enthaltenen PowerShell-Beispielskripts ausführen.

> [!IMPORTANT]
> Die Beispielskripts werden unter PowerShell 5.1 ausgeführt. Sie können derzeit nicht unter PowerShell 6 oder höher ausgeführt werden.

Die enthaltenen Skriptdateien lauten:

1. *SetupSampleSplitMergeEnvironment.ps1* : Richtet eine Testdatenebene für Split/Merge ein (eine ausführliche Beschreibung finden Sie in der Tabelle unten).
2. *ExecuteSampleSplitMerge.ps1* : Führt Testvorgänge auf der Testdatenebene aus (eine ausführliche Beschreibung finden Sie in der Tabelle unten).
3. *GetMappings.ps1* : Beispielskript auf oberster Ebene, das den aktuellen Status der Shardzuordnungen ausgibt
4. *ShardManagement.psm1* : Hilfsskript, das die ShardManagement-API umschließt
5. *SqlDatabaseHelpers.psm1* : Hilfsskript zum Erstellen und Verwalten von Datenbanken in Azure SQL-Datenbank

   <table style="width:100%">
     <tr>
       <th>PowerShell-Datei</th>
       <th>Schritte</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1. Erstellt eine ShardMapManager-Datenbank.</td>
     </tr>
     <tr>
       <td>2. Erstellt zwei Sharddatenbanken.
     </tr>
     <tr>
       <td>3. Erstellt eine Shard Map für diese Datenbanken (löscht alle vorhandenen Shard Maps für diese Datenbanken). </td>
     </tr>
     <tr>
       <td>4. Erstellt eine kleine Beispieltabelle in beiden Shards und füllt die Tabelle in einem der Shards auf.</td>
     </tr>
     <tr>
       <td>5. Deklariert SchemaInfo für die Shardtabelle.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>PowerShell-Datei</th>
       <th>Schritte</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1. Sendet eine Teilungsanforderung an das Web-Front-End des Split-Merge-Diensts, wodurch eine Hälfte der Daten vom ersten auf den zweiten Shard verteilt wird.</td>
     </tr>
     <tr>
       <td>2. Ruft den Status der Teilungsanforderung vom Web-Front-End ab und wartet, bis die Anforderung abgeschlossen ist.</td>
     </tr>
     <tr>
       <td>3. Sendet eine Zusammenführungsanforderung an das Web-Front-End des Split-Merge-Diensts, wodurch die Daten vom zweiten wieder auf den ersten Shard verschoben werden.</td>
     </tr>
     <tr>
       <td>4. Ruft den Status der Zusammenführungsanforderung vom Web-Front-End ab und wartet, bis die Anforderung abgeschlossen ist.</td>
     </tr>
   </table>

## <a name="use-powershell-to-verify-your-deployment"></a>Überprüfen der Bereitstellung mithilfe von PowerShell

1. Öffnen Sie ein neues PowerShell-Fenster, und navigieren Sie zu dem Verzeichnis, in das Sie das Split-Merge-Paket heruntergeladen haben, und wechseln Sie dann in das Verzeichnis „powershell“.

2. Erstellen Sie einen Server (oder wählen Sie einen vorhandenen Server aus), auf dem ShardMapManager und Shards erstellt werden.

   > [!NOTE]
   > Das Skript *SetupSampleSplitMergeEnvironment.ps1* erstellt alle diese Datenbanken standardmäßig auf dem gleichen Server, um das Skript einfach zu halten. Dies ist keine Einschränkung des Split-Merge-Diensts selbst.

   Damit der Split-Merge-Dienst Daten verschieben und die Shard Map aktualisieren kann, ist eine Anmeldung mit SQL-Authentifizierung und Lese-/Schreibzugriff auf die Datenbanken erforderlich. Da der Split-Merge-Dienst in der Cloud ausgeführt wird, bietet er derzeit keine Unterstützung für die integrierte Authentifizierung.

   Stellen Sie sicher, dass der Server so konfiguriert ist, dass er den Zugriff über die IP-Adresse des Computers zulässt, auf dem diese Skripts ausgeführt werden. Diese Einstellung finden Sie unter „SQL Server“ > „Firewalls und virtuelle Netzwerke“ > „Client-IP-Adressen“.

3. Führen Sie das Skript *SetupSampleSplitMergeEnvironment.ps1* aus, um die Beispielumgebung zu erstellen.

   Durch die Ausführung dieses Skripts werden alle vorhandenen Datenstrukturen der Shard Map-Verwaltung in der ShardMapManager-Datenbank und auf den Shards gelöscht. Möglicherweise ist es hilfreich, das Skript erneut auszuführen, wenn Sie die Shard Map oder Shards erneut initialisieren möchten.

   Beispiel für eine Befehlszeile:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Führen Sie das Skript „Getmappings.ps1“ aus, um die derzeit in der Beispielumgebung vorhandenen Zuordnungen anzuzeigen.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Führen Sie das Skript *ExecuteSampleSplitMerge.ps1* aus, um einen Teilungsvorgang auszuführen (die Hälfte der Daten vom ersten Shard auf den zweiten Shard zu verschieben) und dann einen Zusammenführungsvorgang auszuführen (die Daten wieder zurück auf den ersten Shard zu verschieben). Wenn Sie TLS konfiguriert und den http-Endpunkt deaktiviert gelassen haben, stellen Sie sicher, dass Sie stattdessen den https://-Endpunkt verwenden.

    Beispiel für eine Befehlszeile:

    ```cmd
    .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd'
    -ShardMapManagerServerName 'abcdefghij.database.windows.net'
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net'
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
    ```

    Wenn Sie den folgenden Fehler erhalten, liegt sehr wahrscheinlich ein Problem mit dem Zertifikat Ihres Webendpunkts vor. Stellen Sie unter Verwendung Ihres bevorzugten Webbrowsers eine Verbindung mit dem Webendpunkt her, und überprüfen Sie, ob ein Zertifikatfehler auftritt.

    `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

    War die Verbindung erfolgreich, sollte die Ausgabe wie folgt aussehen:

    ```output
    > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
    > Sending split request
    > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
    > Polling split-merge request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    > Sending merge request
    > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
    > Polling request status. Press Ctrl-C to end
    > Progress: 0% | Status: Queued | Details: [Informational] Queued request
    > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
    > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
    > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
    > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
    > ...
    > ...
    > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
    > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
    > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
    >
    ```

6. Probieren Sie andere Datentypen aus. Alle diese Skripts akzeptieren einen optionalen -ShardKeyType-Parameter, mit dem Sie den Schlüsseltyp angeben können. Der Standardwert ist „Int32“, Sie können aber auch „Int64“, „Guid“ oder „Binary“ angeben.

## <a name="create-requests"></a>Erstellen von Anforderungen

Der Dienst kann entweder über die Web-Benutzeroberfläche verwendet werden, oder indem Sie das PowerShell-Modul „SplitMerge.psm1“ importieren und verwenden, das Ihre Anforderungen über die Webrolle übermittelt.

Der Dienst kann Daten sowohl in Shardtabellen als auch in Verweistabellen verschieben. Eine Shardtabelle verfügt über eine Shardingschlüsselspalte und weist auf jedem Shard unterschiedliche Zeilendaten auf. Eine Verweistabelle ist keine Shardtabelle, sodass sie auf jedem Shard dieselben Zeilendaten enthält. Verweistabellen sind hilfreich für Daten, die sich nicht häufig ändern, und werden für JOIN-Vorgänge mit Shardtabellen in Abfragen verwendet.

Um einen Teilungs-/Zusammenführungsvorgang auszuführen, müssen Sie die zu verschiebenden Shardtabellen und Verweistabellen deklarieren. Sie verwenden dazu die **SchemaInfo** -API. Diese API befindet sich im **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** -Namespace.

1. Erstellen Sie für jede Shardtabelle ein **ShardedTableInfo** -Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig „dbo“), den Tabellennamen und den Spaltennamen in der Tabelle angibt, die den Shardingschlüssel enthält.
2. Erstellen Sie für jede Verweistabelle ein **ReferenceTableInfo** -Objekt, das den Namen des übergeordneten Schemas der Tabelle (optional, standardmäßig „dbo“) und den Tabellennamen angibt.
3. Fügen Sie die oben beschriebenen TableInfo-Objekte einem neuen **SchemaInfo** -Objekt hinzu.
4. Rufen Sie einen Verweis auf ein **ShardMapManager** -Objekt ab, und rufen Sie **GetSchemaInfoCollection** auf.
5. Fügen Sie **SchemaInfo** unter Angabe des Namens der Shard Map dem **SchemaInfoCollection** -Element hinzu.

Ein Beispiel dazu finden Sie im Skript „SetupSampleSplitMergeEnvironment.ps1“.

Durch den Split-Merge-Dienst wird keine Zieldatenbank (bzw. kein Schema für Tabellen in der Datenbank) für Sie erstellt. Diese müssen erstellt werden, bevor eine Anforderung an den Dienst gesendet wird.

## <a name="troubleshooting"></a>Problembehandlung

Beim Ausführen der PowerShell-Beispielskripts kann folgende Meldung angezeigt werden:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Dieser Fehler weist darauf hin, dass das TLS/SSL-Zertifikat nicht ordnungsgemäß konfiguriert ist. Folgen Sie den Anweisungen im Abschnitt „Herstellen einer Verbindung mit einem Webbrowser“.

Wenn Sie keine Anforderungen übermitteln können, wird möglicherweise Folgendes angezeigt:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

Überprüfen Sie in diesem Fall Ihre Konfigurationsdatei, insbesondere die Einstellung für **WorkerRoleSynchronizationStorageAccountConnectionString** . Dieser Fehler weist normalerweise darauf hin, dass die Workerrolle die Metadaten-Datenbank bei der ersten Verwendung nicht erfolgreich initialisieren konnte.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png