---
title: Verwalten von Paketen mit dem Paketspeicher für Azure-SSIS Integration Runtime
description: Informationen zum Verwalten von Paketen mit dem Paketspeicher für Azure-SSIS Integration Runtime
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 29d072c513d9a75055d4bb486f44b17b00b7f0a9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638345"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Verwalten von Paketen mit dem Paketspeicher für Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Sie Ihre lokalen SQL Server Integration Services-Workloads (SSIS) in die Cloud verschieben möchten, können Sie Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen einer Azure-SSIS IR-Instanz](./tutorial-deploy-ssis-packages-azure.md). Die Azure-SSIS IR unterstützt Folgendes:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder in SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird (Paketbereitstellungsmodell)

Wenn Sie das Paketbereitstellungsmodell verwenden, können Sie auswählen, ob Sie Ihre Azure-SSIS IR mit Paketspeichern bereitstellen möchten. Sie stellen eine Paketverwaltungsebene oberhalb des Dateisystems, von Azure Files oder der von Azure SQL Managed Instance gehosteten msdb bereit. Ein Azure-SSIS IR-Paketspeicher ermöglicht Ihnen das Importieren, Exportieren, Löschen und Ausführen von Paketen sowie das Überwachen und Beenden der Ausführung von Paketen über SQL Server Management Studio (SSMS), ähnlich wie im [Legacy-SSIS-Paketspeicher](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Herstellen einer Verbindung mit Azure-SSIS IR

Nachdem Sie Ihre Azure-SSIS IR-Instanz bereitgestellt haben, können Sie eine Verbindung mit dieser herstellen, um die zugehörigen Paketspeicher in SSMS zu durchsuchen.

![Herstellen einer Verbindung mit Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Klicken Sie im Fenster **Objekt-Explorer** von SSMS im Dropdownmenü **Verbinden** auf **Azure-SSIS Integration Runtime** . Melden Sie sich als Nächstes bei Azure an, und wählen Sie das passende Abonnement, ADF und die Azure-SSIS IR-Instanz aus, die Sie mit Paketspeichern bereitgestellt haben. Dann wird Ihre Azure-SSIS IR-Instanz mit den Knoten **Ausgeführte Pakete** und **Gespeicherte Pakete** angezeigt. Erweitern Sie den Knoten **Gespeicherte Pakete** , um die darunter liegenden Paketspeicher anzuzeigen. Erweitern Sie die Paketspeicher, um die darunter liegenden Ordner und Pakete anzuzeigen. Möglicherweise werden Sie aufgefordert, die Anmeldeinformationen für den Zugriff auf Ihre Paketspeicher einzugeben, wenn SSMS nicht automatisch eine Verbindung mit diesen herstellen kann. Wenn Sie z. B. einen Paketspeicher über MSDB erweitern, werden Sie möglicherweise zuerst aufgefordert, eine Verbindung mit Azure SQL Managed Instance herzustellen.

![Herstellen einer Verbindung mit Azure SQL Managed Instance](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Verwalten von Ordnern und Paketen

Nachdem Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS hergestellt haben, können Sie mit der rechten Maustaste auf jeden beliebigen Paketspeicher, Ordner und jedes beliebige Paket klicken, um ein Popupmenü mit den folgenden Optionen anzuzeigen: **Neuer Ordner** , **Paket importieren** , **Paket exportieren** , **Löschen** und **Aktualisieren** .

   ![Verwalten von Ordnern und Paketen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Klicken Sie auf **Neuer Ordner** , um einen neuen Ordner für importierte Pakete zu erstellen.

   *  Klicken Sie auf **Paket importieren** , um Pakete aus dem **Dateisystem** , aus **SQL Server** (MSDB) oder dem **Legacy-SSIS-Paketspeicher** in Ihren Paketspeicher zu importieren.

      ![Importieren des Pakets](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Wählen Sie je nach **Paketspeicherort** , aus dem Sie importieren, den betreffenden **Server**/**Authentifizierungstyp** aus, geben Sie wenn nötig die Anmeldeinformationen für den Zugriff ein, wählen Sie den **Paketpfad** aus, und geben Sie den neuen **Paketnamen** ein. Beim Paketimport kann die Schutzebene der Pakete nicht geändert werden. Wenn Sie sie ändern möchten, verwenden Sie SQL Server Data Tools (SSDT) oder das Befehlszeilenprogramm `dtutil`.

      > [!NOTE]
      > Das Importieren von SSIS-Paketen in Azure-SSIS IR-Paketspeicher kann nur einzeln durchgeführt werden. Sie werden dabei lediglich in die zugrunde liegende msdb, das Dateisystem oder Azure Files kopiert, wobei die SQL Server- oder SSIS-Version beibehalten wird. 
      >
      > Da Azure-SSIS IR aktuell auf **SQL Server 2017** basiert, werden Pakete mit einer niedrigeren Version zur Laufzeit auf SSIS 2017-Pakete aktualisiert, wenn sie mit Azure-SSIS IR ausgeführt werden. Das Ausführen von Paketen höherer Version wird nicht unterstützt.
      >
      > Da darüber hinaus die Legacy-SSIS-Paketspeicher an eine bestimmte SQL Server-Version gebunden sind und nur im SSMS für diese Version verfügbar sind, müssen Pakete mit einer niedrigeren Version in den Legacy-SSIS-Paketspeichern zunächst mithilfe der angegebenen SSMS-Version in das Dateisystem exportiert werden, bevor sie mit SSMS 2019 oder höheren Versionen in Azure-SSIS IR-Paketspeicher importiert werden können.
      >
      > Wenn Sie mehrere SSIS-Pakete in Azure-SSIS IR-Paketspeicher importieren und dabei ihre Schutzebene wechseln möchten, können Sie alternativ das Befehlszeilen-Hilfsprogramm [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) verwenden. Weitere Informationen finden Sie unter [Bereitstellen mehrerer Pakete mit dtutil](#deploying-multiple-packages-with-dtutil).

   *  Klicken Sie auf **Paket exportieren** , um Pakete aus Ihrem Paketspeicher in das **Dateisystem** , in **SQL Server** (MSDB) oder in den **Legacy-SSIS-Paketspeicher** zu exportieren.

      ![Paket exportieren](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Wählen Sie je nach **Paketspeicherort** , in den Sie exportieren, den betreffenden **Server**/**Authentifizierungstyp** aus, geben Sie wenn nötig die Anmeldeinformationen für den Zugriff ein, und wählen Sie den **Paketpfad** aus. Wenn Sie verschlüsselte Pakete exportieren, geben Sie die Kennwörter ein, um sie zunächst zu entschlüsseln. Anschließend können Sie ihre Schutzebene ändern, z. B. damit vertrauliche Daten nicht gespeichert werden oder damit diese oder alle Daten mit einem Benutzerschlüssel oder Kennwort verschlüsselt werden.

      > [!NOTE]
      > Das Exportieren von SSIS-Paketen aus Azure-SSIS IR-Paketspeichern kann nur einzeln durchgeführt werden. Wenn Sie dabei nicht die Schutzebene wechseln, werden sie einfach unter Beibehaltung der SQL Server-/SSIS-Version kopiert. Andernfalls werden die Pakete auf SSIS 2019 oder eine höhere Versionen aktualisiert.
      >
      > Da Azure-SSIS IR aktuell auf **SQL Server 2017** basiert, werden Pakete mit einer niedrigeren Version zur Laufzeit auf SSIS 2017-Pakete aktualisiert, wenn sie mit Azure-SSIS IR ausgeführt werden. Das Ausführen von Paketen höherer Version wird nicht unterstützt.
      >
      > Wenn Sie mehrere SSIS-Pakete aus Azure-SSIS IR-Paketspeichern exportieren und dabei die Schutzebene wechseln möchten, können Sie alternativ das Befehlszeilen-Hilfsprogramm [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) verwenden. Weitere Informationen finden Sie unter [Bereitstellen mehrerer Pakete mit dtutil](#deploying-multiple-packages-with-dtutil).

   *  Klicken Sie auf **Löschen** , um vorhandene Ordner/Pakete aus dem Paketspeicher zu löschen.

   *  Klicken Sie auf **Aktualisieren** , um neu hinzugefügte Ordner/Pakete im Paketspeicher anzuzeigen.

## <a name="execute-packages"></a>Ausführen von Paketen

Nachdem Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS hergestellt haben, können Sie mit der rechten Maustaste auf ein beliebiges gespeichertes Paket klicken. Dadurch wird ein Popupmenü angezeigt, in dem Sie die Option **Paket ausführen** auswählen können.  Dann wird das Dialogfeld **Paketausführungsprogramm** geöffnet, in dem Sie die Paketausführungen in Azure-SSIS IR als Aktivitäten vom Typ „SSIS-Paket ausführen“ in ADF-Pipelines konfigurieren können.

![Paketausführungsprogramm, Seite 1 und 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Paketausführungsprogramm, Seite 3 und 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Die Seiten **Allgemein** , **Konfigurationen** , **Ausführungsoptionen** und **Protokollierung** im Dialogfeld **Paketausführungsprogramm** entsprechen der Registerkarte **Einstellungen** für die Aktivität „SSIS-Paket ausführen“. Auf diesen Seiten können Sie das Verschlüsselungskennwort für Ihr Paket und Zugriffsinformationen für die Paketkonfigurationsdatei eingeben. Sie können auch Ihre Anmeldeinformationen und Eigenschaften für die Paketausführung sowie die Zugriffsinformationen für den Protokollordner eingeben.  Die Seite **Werte festlegen** des Dialogfelds **Paketausführungsprogramm** entspricht der Registerkarte **Eigenschaftsüberschreibungen** der Aktivität „SSIS-Paket ausführen“, auf der Sie die bestehenden Paketeigenschaften eingeben können, die überschrieben werden sollen. Weitere Informationen finden Sie unter [Ausführen von SSIS-Paketen als Aktivitäten vom Typ „SSIS-Paket ausführen“ in ADF-Pipelines](./how-to-invoke-ssis-package-ssis-activity.md).

Wenn Sie die Schaltfläche **Ausführen** auswählen, wird automatisch eine neue ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“ erstellt und ausgelöst. Wenn bereits eine ADF-Pipeline mit denselben Einstellungen vorhanden ist, wird sie erneut ausgeführt, und es wird keine neue Pipeline generiert. Die ADF-Pipeline und die Aktivität „SSIS-Paket ausführen“ werden `Pipeline_SSMS_YourPackageName_HashString` bzw. `Activity_SSMS_YourPackageName`genannt.

![Schaltfläche „Paketausführungsprogramm“](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Ausführen einer SSIS-Paketaktivität](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Überwachen und Beenden von ausgeführten Paketen

Nachdem Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS hergestellt haben, können Sie den Knoten **Pakete ausführen** erweitern, um die derzeit ausgeführten Pakete anzuzeigen.  Klicken Sie erst mit der rechten Maustaste auf ein beliebiges Popupmenü, und anschließend mit der linken auf **Beenden** oder **Aktualisieren** .

   ![Überwachen und Beenden von ausgeführten Paketen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Klicken Sie auf **Beenden** , um die laufende ADF-Pipeline zu unterbrechen, die das Paket als Aktivität vom Typ „SSIS-Paket ausführen“ ausführt.

   *  Klicken Sie auf **Aktualisieren** , um neu ausgeführte Pakete aus Ihren Paketspeichern anzuzeigen.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Überwachen von Azure-SSIS IR und Bearbeiten von Paketspeichern

Nachdem Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS hergestellt haben, können Sie mit der rechten Maustaste auf diese klicken, um ein Popupmenü anzuzeigen, in dem Sie die Optionen **Go to Azure Data Factory portal** (Azure Data Factory-Portal aufrufen) oder **Aktualisieren** auswählen können.

   ![ADF-Portal aufrufen](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Klicken Sie auf **Go to Azure Data Factory portal** (Azure Data Factory-Portal aufrufen), um die Seite **Integration Runtimes** des ADF-Überwachungshubs aufzurufen, auf der Sie Ihre Azure-SSIS IR-Instanz überwachen können. Der Kachel **PAKETSPEICHER** können Sie die Anzahl der Paketspeicher entnehmen, die an Ihre Azure-SSIS IR-Instanz angefügt sind.  Wenn Sie diese Zahl auswählen, wird ein Popupfenster angezeigt, in dem Sie verknüpfte ADF-Dienste bearbeiten können, in denen die Zugriffsinformationen für Ihre Paketspeicher gespeichert werden.

      ![Bearbeiten von Paketspeichern](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Klicken Sie auf **Aktualisieren** , um neu hinzugefügte Ordner/Pakete in Ihren Paketspeichern und die über diese ausgeführten Pakete anzuzeigen.

## <a name="deploying-multiple-packages-with-dtutil"></a>Bereitstellen mehrerer Pakete mit dtutil

Wenn Sie Ihre lokalen SSIS-Workloads per Lift & Shift nach SSIS in ADF verschieben und dabei das bestehende Paketbereitstellungsmodell beibehalten möchten, müssen Sie die Pakete aus dem Dateisystem, der von SQL Server gehosteten msdb oder Legacy-SSIS-Paketspeichern in Azure Files, in der von Azure SQL Managed Instance gehosteten msdb oder in Azure-SSIS IR-Paketspeichern bereitstellen. Gleichzeitig sollten Sie auch Ihre Schutzebene von der Verschlüsselung mit Benutzerschlüsseln in eine unverschlüsselte oder mit Kennwort verschlüsselte Speicherung wechseln, wenn Sie dies noch nicht getan haben.

Sie können das Befehlszeilen-Hilfsprogramm [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) verwenden, das in der SQL Server-/SSIS-Installation enthalten ist, um mehrere Pakete in Batches bereitzustellen. Es ist an eine bestimmte SSIS-Version gebunden. Wenn Sie es also verwenden, um Pakete mit einer niedrigeren Version bereitzustellen, ohne ihre Schutzebene zu wechseln, werden sie einfach kopiert, und die SSIS-Version wird beibehalten. Wenn Sie es zur Bereitstellung verwenden und die Schutzebene gleichzeitig wechseln, werden sie auf die neue SSIS-Version aktualisiert.

 Da Azure-SSIS IR aktuell auf **SQL Server 2017** basiert, werden Pakete mit einer niedrigeren Version zur Laufzeit auf SSIS 2017-Pakete aktualisiert, wenn sie mit Azure-SSIS IR ausgeführt werden. Das Ausführen von Paketen höherer Version wird nicht unterstützt.

Damit Upgrades zur Laufzeit vermieden werden, sollte für die Bereitstellung von Paketen, die mit der Azure-SSIS IR im Paketbereitstellungsmodell ausgeführt werden sollen, dtutil 2017 verwendet werden, das in der Installation von SQL Server/SSIS 2017 enthalten ist. Sie können zu diesem Zweck die kostenlose [Developer Edition von SQL Server/SSIS 2017](https://go.microsoft.com/fwlink/?linkid=853016) herunterladen und installieren. Nach der Installation finden Sie dtutil 2017 in diesem Ordner: `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn`.

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Bereitstellen von mehreren Paketen aus dem lokalen Dateisystem in Azure Files mit dtutil

 Wenn Sie mehrere Pakete aus dem Dateisystem in Azure Files bereitstellen und deren Schutzebene gleichzeitig wechseln möchten, können Sie die folgenden Befehle an einer Eingabeaufforderung ausführen. Ersetzen Sie alle Zeichenfolgen durch die für Ihren Fall geltenden Werte.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

Um die obigen Befehle in einer Batchdatei auszuführen, ersetzen Sie `%f` durch `%%f`.

Wenn Sie mehrere Pakete aus Legacy-SSIS-Paketspeichern oberhalb des Dateisystems in Azure Files bereitstellen und deren Schutzebene gleichzeitig wechseln möchten, können Sie dieselben Befehle verwenden, aber `YourLocalDrive:\...\YourPackageFolder` durch einen lokalen Ordner ersetzen, der von den Legacy-SSIS-Paketspeichern verwendet wird: `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder`. Wenn Ihr Legacy-SSIS-Paketspeicher z. B. an SQL Server 2016 gebunden ist, wechseln Sie zu `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder`.  Sie finden den Wert für `YourSQLServerDefaultCompatibilityLevel` in einer [Liste mit SQL Server-Standardkompatibilitätsgraden](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments).

Wenn Sie Azure-SSIS IR-Paketspeicher für Azure Files konfiguriert haben, werden die bereitgestellten Pakete darin angezeigt, wenn Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS 2019 oder höheren Versionen herstellen.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Bereitstellen von mehreren Paketen aus der lokalen msdb in die msdb in Azure mit dtutil

 Wenn Sie mehrere Pakete aus der von SQL Server gehosteten msdb oder aus Legacy-SSIS-Paketspeichern dieser msdb in der von Azure SQL Managed Instance gehostete msdb bereitstellen und deren Schutzebene gleichzeitig wechseln möchten, können Sie in SSMS eine Verbindung mit Ihrer SQL Server-Instanz herstellen. Klicken Sie im **Objekt-Explorer** von SSMS mit der rechten Maustaste auf den Knoten `Databases->System Databases->msdb`, um das Fenster **Neue Abfrage** zu öffnen, und führen Sie das folgende T-SQL-Skript aus. Ersetzen Sie alle Zeichenfolgen durch die für Ihren Fall geltenden Werte:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Um den privaten/öffentlichen Endpunkt Ihrer Instanz von Azure SQL Managed Instance zu verwenden, ersetzen Sie `YourSQLManagedInstanceEndpoint` durch `YourSQLMIName.YourDNSPrefix.database.windows.net`/`YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342`.

Das Skript generiert dtutil-Befehlszeilen für alle Pakete in der msdb, die in einer Eingabeaufforderung zur Mehrfachauswahl, das Kopieren und Einfügen und zum Ausführen bereitstehen.

![Generieren von dtutil-Befehlszeilen](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

Wenn Sie Azure-SSIS IR-Paketspeicher für die msdb konfiguriert haben, werden die bereitgestellten Pakete darin angezeigt, wenn Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS 2019 oder höheren Versionen herstellen.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Bereitstellen von mehreren Paketen aus der lokalen msdb in Azure Files mit dtutil

 Wenn Sie mehrere Pakete aus der von SQL Server gehosteten msdb oder aus Legacy-SSIS-Paketspeichern dieser msdb in Azure Files bereitstellen und deren Schutzebene gleichzeitig wechseln möchten, können Sie in SSMS eine Verbindung mit Ihrer SQL Server-Instanz herstellen. Klicken Sie im **Objekt-Explorer** von SSMS mit der rechten Maustaste auf den Knoten `Databases->System Databases->msdb`, um das Fenster **Neue Abfrage** zu öffnen, und führen Sie das folgende T-SQL-Skript aus. Ersetzen Sie alle Zeichenfolgen durch die für Ihren Fall geltenden Werte:  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Das Skript generiert dtutil-Befehlszeilen für alle Pakete in der msdb, die in einer Eingabeaufforderung zur Mehrfachauswahl, das Kopieren und Einfügen und zum Ausführen bereitstehen.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Wenn Sie Azure-SSIS IR-Paketspeicher für Azure Files konfiguriert haben, werden die bereitgestellten Pakete darin angezeigt, wenn Sie eine Verbindung mit Ihrer Azure-SSIS IR-Instanz in SSMS 2019 oder höheren Versionen herstellen.

## <a name="next-steps"></a>Nächste Schritte

Sie können die automatisch generierten ADF-Pipelines mit Aktivitäten vom Typ „SSIS-Paket ausführen“ erneut ausführen bzw. bearbeiten oder neue Pipelines im ADF-Portal erstellen. Weitere Informationen finden Sie unter [Ausführen von SSIS-Paketen als Aktivitäten vom Typ „SSIS-Paket ausführen“ in ADF-Pipelines](./how-to-invoke-ssis-package-ssis-activity.md).