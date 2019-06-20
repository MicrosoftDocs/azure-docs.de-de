---
title: Planen von U-SQL-Aufträgen für Azure Data Lake Analytics mithilfe von SSIS
description: Erfahren Sie, wie mit SQL Server Integration Services U-SQL-Aufträge planen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/17/2018
ms.openlocfilehash: 6894486118f69e682353142be04821e1d28440e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814671"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Planen von U-SQL-Aufträgen mit SQL Server Integration Services (SSIS)

In diesem Dokument erfahren Sie, wie Sie U-SQL-Aufträge mithilfe von SQL Server Integration Services (SSIS) orchestrieren und erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Das [Azure Feature Pack für Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud) bietet den [Task „Azure Data Lake Analytics“](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017) und [Azure Data Lake Analytics-Verbindungs-Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017), mit deren Hilfe Sie eine Verbindung mit dem Azure Data Lake Analytics-Dienst herstellen können. Um diesen Task verwenden zu können, müssen Sie die folgenden Aufgaben ausführen:

- [Herunterladen und Installieren von SQL Server Data Tools (SSDT) für Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017)
- [Installieren von Azure Feature Pack für SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017)

## <a name="azure-data-lake-analytics-task"></a>Der Task „Azure Data Lake Analytics“

Der Task „Azure Data Lake Analytics“ ermöglicht Benutzern das Übermitteln von U-SQL-Aufträgen an das Azure Data Lake Analytics-Konto. 

[Erfahren Sie, wie Sie den Task „Azure Data Lake Analytics“ konfigurieren](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

![Der Task „Azure Data Lake Analytics“ in SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Sie können das U-SQL-Skript aus verschiedenen Quellen mithilfe der in SSIS integrierten Funktionen und Aufgaben abrufen. Die folgenden Szenarien zeigen, wie Sie die U-SQL-Skripts für verschiedene Anwendungsfälle konfigurieren können.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Szenario 1: Verwenden des Inlineskripts zum Aufrufen von Tabellenwertfunktionen und gespeicherten Prozeduren

Konfigurieren Sie im Editor für den Azure Data Lake Analytics-Task **SourceType** als **DirectInput**, und fügen Sie in **USQLStatement** die U-SQL-Anweisungen ein.

Zur Vereinfachung der Wartung und Codeverwaltung sollten Sie nur kurze U-SQL-Skripts als Inline-Skripts verwenden. Sie können z.B. vorhandene Tabellenwertfunktionen und gespeicherte Prozeduren in Ihren U-SQL-Datenbanken aufrufen. 

![Bearbeiten eines Inline-U-SQL-Skripts im SSIS-Task](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Verwandter Artikel: [Übergeben von Parametern an gespeicherte Prozeduren](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Szenario 2: Verwenden von U-SQL-Dateien in Azure Data Lake Store

Sie können auch U-SQL-Dateien in Azure Data Lake Store verwenden, indem Sie den **Task „Azure Data Lake Store File System“** im Azure Feature Pack verwenden. Dieser Ansatz ermöglicht Ihnen die Verwendung der Skripts, die in der Cloud gespeichert sind.

Führen Sie die folgenden Schritte aus, um die Verbindung zwischen dem Task „Azure Data Lake Store File System“ und dem Task „Azure Data Lake Analytics“ herzustellen.

### <a name="set-task-control-flow"></a>Festlegen der Ablaufsteuerung für Tasks

Fügen Sie in der SSIS-Paketentwurfsansicht den **Task „Azure Data Lake Store File System“** , einen **Foreach-Schleifencontainer** und den **Task „Azure Data Lake Analytics“** im Foreach-Schleifencontainer hinzu. Mithilfe des Tasks „Azure Data Lake Store File System“ können Sie U-SQL-Dateien in Ihrem ADLS-Konto in einen temporären Ordner herunterladen. Der Foreach-Schleifencontainer und der Task „Azure Data Lake Analytics“ dienen zum Übermitteln aller U-SQL-Dateien im temporären Ordner in das Azure Data Lake Analytics-Konto als U-SQL-Auftrag.

![Verwenden von U-SQL-Dateien in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurieren des Tasks „Azure Data Lake Store File System“

1. Legen Sie **Vorgang** auf **CopyFromADLS** fest.
2. Richten Sie **AzureDataLakeConnection** ein. Erfahren Sie mehr zum [Azure Data Lake Store-Verbindungs-Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017).
3. Legen Sie **AzureDataLakeDirectory** fest. Zeigen Sie auf den Ordner, in dem Ihre U-SQL-Skripts gespeichert sind. Verwenden Sie einen relativen Pfad zum Stammordner des Azure Data Lake Store-Kontos.
4. Legen Sie **Ziel** auf einen Ordner fest, in dem die heruntergeladenen U-SQL-Skripts zwischengespeichert werden. Dieser Pfad wird im Foreach-Schleifencontainer zum Übermitteln von U-SQL-Aufträgen verwendet. 

![Konfigurieren des Tasks „Azure Data Lake Store File System“](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Erfahren Sie mehr zum Task „Azure Data Lake Store File System“](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-store-file-system-task?view=sql-server-2017).

### <a name="configure-foreach-loop-container"></a>Konfigurieren des Foreach-Schleifencontainers

1. Legen Sie auf der Seite **Sammlung** den Eintrag **Enumerator** auf **Foreach-Datei-Enumerator**.

2. Legen Sie **Ordner** unter der Gruppe **Enumeratorkonfiguration** auf den temporären Ordner fest, der die heruntergeladenen U-SQL-Skripts enthält.

3. Legen Sie **Dateien** unter **Enumeratorkonfiguration** auf `*.usql` fest, damit der Schleifencontainer nur die Dateien mit der Endung `.usql` abfängt.

    ![Konfigurieren des Foreach-Schleifencontainers](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Fügen Sie auf der Seite **Variablenzuordnungen** eine benutzerdefinierte Variable hinzu, um den Dateinamen für jede U-SQL-Datei abzurufen. Legen Sie **Index** auf 0 fest, um den Dateinamen abzurufen. Bei diesem Beispiel definieren Sie die Variable `User::FileName`. Diese Variable wird verwendet, um eine Verbindung mit einer U-SQL-Skriptdatei dynamisch herzustellen und den Namen eines U-SQL-Auftrags im Task „Azure Data Lake Analytics“ festzulegen.

    ![Konfigurieren des Foreach-Schleifencontainers zum Abrufen des Dateinamens](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurieren des Tasks „Azure Data Lake Analytics“ 

1. Legen Sie **SourceType** auf **FileConnection** fest.

2. Legen Sie **FileConnection** auf die Dateiverbindung fest, die auf die vom Foreach-Schleifencontainer zurückgegebenen Dateiobjekte zeigt.
    
    So erstellen Sie diese Dateiverbindung

   1. Wählen Sie in der Einstellung „FileConnection“ die Option **\<Neue Verbindung... >** aus.
   2. Legen Sie **Verwendungstyp** auf **Vorhandene Datei** und **Datei** auf den Pfad einer vorhandenen Datei fest.

       ![Konfigurieren des Foreach-Schleifencontainers](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Klicken Sie in der Ansicht **Verbindungs-Manager** mit der rechten Maustaste auf die zuvor erstellte Dateiverbindung, und wählen Sie **Eigenschaften** aus.

   4. Erweitern Sie im Fenster **Eigenschaften** den Eintrag **Ausdrücke**, und legen Sie **ConnectionString** auf die im Foreach-Schleifencontainer definierte Variable fest, z.B. `@[User::FileName]`.

       ![Konfigurieren des Foreach-Schleifencontainers](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Legen Sie **AzureDataLakeAnalyticsConnection** auf das Azure Data Lake Analytics-Konto fest, an das Sie Aufträge übermitteln möchten. Erfahren Sie mehr über den [Azure Data Lake Analytics-Verbindungs-Manager](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager?view=sql-server-2017).

4. Legen Sie andere Auftragskonfigurationen fest. [Weitere Informationen](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017).

5. Verwenden Sie **Ausdrücke**, um den Namen des U-SQL-Auftrags dynamisch festzulegen:

    1. Fügen Sie auf der Seite **Ausdrücke** für **JobName** einen neuen aus einem Schlüssel-Wert-Paar bestehenden Ausdruck hinzu.
    2. Legen Sie den Wert für „JobName“ auf die im Foreach-Schleifencontainer definierte Variable fest, z.B. `@[User::FileName]`.
    
        ![Konfigurieren des SSIS-Ausdrucks für den Namen des U-SQL-Auftrags](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Szenario 3: Verwenden von U-SQL-Dateien in Azure Blob Storage

Sie können U-SQL-Dateien in Azure Blob Storage verwenden, indem Sie den **Task „Azure Blob Download“** im Azure Feature Pack nutzen. Dieser Ansatz ermöglicht Ihnen die Verwendung der Skripts in der Cloud.

Die Schritte ähneln [Szenario 2: Verwenden von U-SQL-Dateien in Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Ändern Sie den Task „Azure Data Lake Store File System“ in den Task „Azure Blob Download“. [Erfahren Sie mehr über den Task „Azure Blob Download“](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task?view=sql-server-2017).

Die Ablaufsteuerung ist ähnlich wie unten.

![Verwenden von U-SQL-Dateien in Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Szenario 4: Verwenden von U-SQL-Dateien auf dem lokalen Computer

Sie können nicht nur U-SQL-Dateien verwenden, die in der Cloud gespeichert sind, sondern auch Dateien auf Ihrem lokalen Computer oder Dateien, die mit Ihren SSIS-Paketen bereitgestellt werden.

1. Klicken Sie im SSIS-Projekt mit der rechten Maustaste auf **Verbindungs-Manager**, und wählen Sie **Neuer Verbindungs-Manager** aus.

2. Wählen Sie den Typ **Datei** aus, und klicken Sie auf **Hinzufügen**.

3. Legen Sie **Verwendungstyp** auf **Vorhandene Datei** und **Datei** auf die Datei auf dem lokalen Computer fest.

    ![Hinzufügen der Dateiverbindung zur lokalen Datei](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Fügen Sie den Task **Azure Data Lake Analytics** hinzu und:
    1. Legen Sie **SourceType** auf **FileConnection** fest.
    2. Legen Sie **FileConnection** auf die zuvor erstellte Dateiverbindung fest.

5. Schließen Sie andere Konfigurationsaufgaben für den Task „Azure Data Lake Analytics“ ab.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Szenario 5: Verwenden einer U-SQL-Anweisung in einer SSIS-Variablen

In einigen Fällen müssen Sie die U-SQL-Anweisungen ggf. dynamisch generieren. Sie können **SSIS-Variable** mit **SSIS-Ausdruck** und anderen SSIS-Tasks wie „Skripttask“ verwenden, um die U-SQL-Anweisung dynamisch zu generieren.

1. Öffnen Sie im Menü der obersten Ebene über **SSIS > Variablen** das Fenster des Tools „Variablen“.

2. Fügen Sie eine SSIS-Variable hinzu. Legen Sie den Wert direkt fest, oder verwenden Sie **Ausdruck**, um den Wert zu generieren.

3. Fügen Sie den Task **Azure Data Lake Analytics** hinzu und:
    1. Legen Sie **SourceType** auf **Variable** fest.
    2. Legen Sie **SourceVariable** auf die zuvor erstellte SSIS-Variable fest.

4. Schließen Sie andere Konfigurationsaufgaben für den Task „Azure Data Lake Analytics“ ab.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Szenario 6: Übergeben von Parametern an ein U-SQL-Skript

In einigen Fällen kann es sinnvoll sein, den Wert der U-SQL-Variablen im U-SQL-Skript dynamisch festzulegen. Das Feature **Parameterzuordnung** im Task „Azure Data Lake Analytics“ hilft bei diesem Szenario. Es gibt in der Regel zwei typische Anwendungsfälle:

- Legen Sie die Eingabe- und Ausgabevariablen für den Dateipfad basierend auf dem aktuellen Datum und der aktuellen Uhrzeit dynamisch fest.
- Legen Sie den Parameter für gespeicherte Prozeduren fest.

[Erfahren Sie mehr zum Festlegen von Parametern für das U-SQL-Skript](https://docs.microsoft.com/sql/integration-services/control-flow/azure-data-lake-analytics-task?view=sql-server-2017#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen von SSIS-Paketen in Azure](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
- [Azure Feature Pack für Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-2017#scenario-managing-data-in-the-cloud)
- [Planen von U-SQL-Aufträgen mit Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

