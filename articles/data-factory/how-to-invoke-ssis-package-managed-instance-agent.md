---
title: Ausführen von SSIS-Paketen mit dem Azure SQL Managed Instance-Agent
description: Erfahren Sie, wie Sie SSIS-Pakete mit dem Azure SQL Managed Instance-Agent ausführen.
ms.service: data-factory
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 916d799ba08f46cb86ee2e22c4af7fc1b92b385f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386151"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>Ausführen von SSIS-Paketen mit dem Azure SQL Managed Instance-Agent

In diesem Artikel wird beschrieben, wie Sie ein SSIS-Paket (SQL Server Integration Services) mit dem Agent einer verwalteten Azure SQL-Datenbankinstanz ausführen. Dieses Feature bietet ein ähnliches Verhalten wie bei der Planung von SSIS-Paketen mit dem SQL Server-Agent in Ihrer lokalen Umgebung.

Mit diesem Feature können Sie SSIS-Pakete ausführen, die in SSISDB in einer Instanz von SQL Managed Instance, einem Dateisystem wie Azure Files oder in einem Azure-SSIS Integration Runtime-Paketspeicher gespeichert sind.

## <a name="prerequisites"></a>Voraussetzungen

Um diese Funktion zu verwenden, müssen Sie die neueste Version von SQL Server Management Studio (SSMS) [herunterladen](/sql/ssms/download-sql-server-management-studio-ssms) und installieren. Nachfolgend die Details zu unterstützten Versionen:

- Zum Ausführen von Paketen in SSISDB oder einem Dateisystem installieren Sie SSMS, Version 18.5 oder höher.
- Zum Ausführen von Paketen im Paketspeicher installieren Sie SSMS, Version 18.6 oder höher.

Sie müssen auch eine [Azure-SSIS Integration Runtime](./tutorial-deploy-ssis-packages-azure.md) in Azure Data Factory bereitstellen. Dabei wird eine Instanz von SQL Managed Instance als Endpunktserver verwendet.

## <a name="run-an-ssis-package-in-ssisdb"></a>Ausführen eines SSIS-Pakets in SSISDB

Bei diesem Verfahren verwenden Sie den SQL Managed Instance-Agent zum Aufrufen eines SSIS-Pakets, das in SSISDB gespeichert ist.

1. Stellen Sie in der aktuellen Version von SSMS eine Verbindung mit einer Instanz von SQL Managed Instance her.
1. Erstellen Sie einen neuen Agent-Auftrag und einen neuen Auftragsschritt. Klicken Sie unter **SQL Server-Agent** mit der rechten Maustaste auf den Ordner **Aufträge**, und wählen Sie dann **Neuer Auftrag** aus.

   ![Auswahl zum Erstellen eines neuen Agent-Auftrags](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Wählen Sie auf der Seite **Neuer Auftragsschritt** den Typ **SQL Server Integration Services-Paket** aus.

   ![Auswahl zum Erstellen eines neuen SSIS-Auftragsschritts](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Wählen Sie auf der Registerkarte **Paket** als Paketspeicherort **SSIS-Katalog** aus.
1. Da sich SSISDB in einer Instanz von SQL Managed Instance befindet, müssen Sie keine Authentifizierung angeben.
1. Geben Sie ein SSIS-Paket aus der SSISDB an.

   ![Registerkarte „Paket“ mit Auswahl für den Paketquellentyp](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. Auf der Registerkarte **Konfiguration** haben Sie folgende Möglichkeiten:
  
   - Geben Sie Parameterwerte unter **Parameter** an.
   - Setzen Sie die Werte unter **Verbindungs-Manager** außer Kraft.
   - Setzen Sie die Eigenschaft außer Kraft, und wählen Sie die Protokollierungsebene unter **Erweitert** aus.

   ![Registerkarte „Konfiguration“ mit Auswahl für den Paketquellentyp](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. Wählen Sie **OK** aus, um die Agent-Auftragskonfiguration zu speichern.
1. Starten Sie den Agent-Auftrag, um das SSIS-Paket auszuführen.

## <a name="run-an-ssis-package-in-the-file-system"></a>Ausführen eines SSIS-Pakets im Dateisystem

Bei diesem Verfahren verwenden Sie den SQL Managed Instance-Agent zum Ausführen eines SSIS-Pakets, das im Dateisystem gespeichert ist.

1. Stellen Sie in der aktuellen Version von SSMS eine Verbindung mit einer Instanz von SQL Managed Instance her.
1. Erstellen Sie einen neuen Agent-Auftrag und einen neuen Auftragsschritt. Klicken Sie unter **SQL Server-Agent** mit der rechten Maustaste auf den Ordner **Aufträge**, und wählen Sie dann **Neuer Auftrag** aus.

   ![Auswahl zum Erstellen eines neuen Agent-Auftrags](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Wählen Sie auf der Seite **Neuer Auftragsschritt** den Typ **SQL Server Integration Services-Paket** aus.

   ![Auswahl zum Erstellen eines neuen SSIS-Auftragsschritts](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Auf der Registerkarte **Paket**:

   1. Wählen Sie als **Paketspeicherort** die Option **Dateisystem** aus.

   1. Für **Dateiquellentyp**:

      - Wenn Ihr Paket zu Azure Files hochgeladen wird, wählen Sie **Azure-Dateifreigabe** aus.

        ![Optionen für den Dateiquellentyp](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        Der Paketpfad ist **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** .

        Geben Sie unter **Anmeldeinformationen für den Paketdateizugriff** den Kontonamen und Kontoschlüssel der Azure-Datei ein, um auf die Azure-Datei zuzugreifen. Die Domäne wird auf **Azure** festgelegt.

      - Wenn Ihr Paket auf eine Netzwerkfreigabe hochgeladen wird, wählen Sie **Netzwerkfreigabe** aus.

        Der Paketpfad ist der UNC-Pfad Ihrer Paketdatei mit der Erweiterung „DTSX“.

        Geben Sie die entsprechende Domäne, den Benutzernamen und das Kennwort ein, um auf die Paketdatei für die Netzwerkfreigabe zuzugreifen.
   1. Falls Ihre Paketdatei mit einem Kennwort verschlüsselt ist, müssen Sie die Option **Verschlüsselungskennwort** auswählen und das Kennwort eingeben.
1. Geben Sie auf der Registerkarte **Konfigurationen** den Pfad zur Konfigurationsdatei ein, falls Sie eine Konfigurationsdatei zum Ausführen des SSIS-Pakets benötigen.
   Wenn Sie Ihre Konfiguration in Azure Files speichern, lautet der Konfigurationspfad **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Auf der Registerkarte **Ausführungsoptionen** können Sie auswählen, ob zum Ausführen des SSIS-Pakets die **Windows-Authentifizierung** oder **32-Bit-Runtime** verwendet werden soll.
1. Auf der Registerkarte **Protokollierung** können Sie den Protokollierungspfad und die entsprechenden Anmeldeinformationen für den Protokollierungszugriff zur Speicherung der Protokolldateien auswählen. 
   Standardmäßig entspricht der Protokollierungspfad dem Paketordnerpfad, und die Anmeldeinformationen für den Protokollierungszugriff entsprechen den Anmeldeinformationen für den Paketzugriff.
   Wenn Sie Ihre Protokolle in Azure Files speichern, lautet Ihr Protokollierungspfad **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Auf der Registerkarte **Werte festlegen** können Sie den Eigenschaftspfad und den Wert zum Überschreiben der Paketeigenschaften eingeben.

   Geben Sie also beispielsweise zum Überschreiben des Werts Ihrer Benutzervariablen den zugehörigen Pfad im folgenden Format ein: **`\Package.Variables[User::<variable name>].Value`** .
1. Wählen Sie **OK** aus, um die Agent-Auftragskonfiguration zu speichern.
1. Starten Sie den Agent-Auftrag, um das SSIS-Paket auszuführen.

## <a name="run-an-ssis-package-in-the-package-store"></a>Ausführen eines SSIS-Pakets im Paketspeicher

Bei diesem Verfahren verwenden Sie den SQL Managed Instance-Agent zum Ausführen eines SSIS-Pakets, das im Azure-SSIS IR-Paketspeicher gespeichert ist.

1. Stellen Sie in der aktuellen Version von SSMS eine Verbindung mit einer Instanz von SQL Managed Instance her.
1. Erstellen Sie einen neuen Agent-Auftrag und einen neuen Auftragsschritt. Klicken Sie unter **SQL Server-Agent** mit der rechten Maustaste auf den Ordner **Aufträge**, und wählen Sie dann **Neuer Auftrag** aus.

   ![Auswahl zum Erstellen eines neuen Agent-Auftrags](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. Wählen Sie auf der Seite **Neuer Auftragsschritt** den Typ **SQL Server Integration Services-Paket** aus.

   ![Auswahl zum Erstellen eines neuen SSIS-Auftragsschritts](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. Auf der Registerkarte **Paket**:

   1. Wählen Sie als **Paketspeicherort** die Option **Paketspeicher** aus.

   1. Für **Paketpfad**:

      Der Paketpfad ist **`<package store name>\<folder name>\<package name>`** .

      ![Optionen für den Paketspeichertyp](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. Falls Ihre Paketdatei mit einem Kennwort verschlüsselt ist, müssen Sie die Option **Verschlüsselungskennwort** auswählen und das Kennwort eingeben.
1. Geben Sie auf der Registerkarte **Konfigurationen** den Pfad zur Konfigurationsdatei ein, falls Sie eine Konfigurationsdatei zum Ausführen des SSIS-Pakets benötigen.
   Wenn Sie Ihre Konfiguration in Azure Files speichern, lautet der Konfigurationspfad **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** .
1. Auf der Registerkarte **Ausführungsoptionen** können Sie auswählen, ob zum Ausführen des SSIS-Pakets die **Windows-Authentifizierung** oder **32-Bit-Runtime** verwendet werden soll.
1. Auf der Registerkarte **Protokollierung** können Sie den Protokollierungspfad und die entsprechenden Anmeldeinformationen für den Protokollierungszugriff zur Speicherung der Protokolldateien auswählen.
   Standardmäßig entspricht der Protokollierungspfad dem Paketordnerpfad, und die Anmeldeinformationen für den Protokollierungszugriff entsprechen den Anmeldeinformationen für den Paketzugriff.
   Wenn Sie Ihre Protokolle in Azure Files speichern, lautet Ihr Protokollierungspfad **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** .
1. Auf der Registerkarte **Werte festlegen** können Sie den Eigenschaftspfad und den Wert zum Überschreiben der Paketeigenschaften eingeben.

   Geben Sie also beispielsweise zum Überschreiben des Werts Ihrer Benutzervariablen den zugehörigen Pfad im folgenden Format ein: **`\Package.Variables[User::<variable name>].Value`** .
1. Wählen Sie **OK** aus, um die Agent-Auftragskonfiguration zu speichern.
1. Starten Sie den Agent-Auftrag, um das SSIS-Paket auszuführen.

## <a name="cancel-ssis-package-execution"></a>Abbrechen der SSIS-Paketausführung

Um die Paketausführung über einen Auftrag des SQL Managed Instance-Agents abzubrechen, führen Sie die folgenden Schritte aus, anstatt den Agent-Auftrag direkt zu beenden:

1. Suchen Sie in **msdb.dbo.sysjobs** nach der **jobId** Ihres SQL-Agents.
1. Verwenden Sie die folgende Abfrage, um anhand der Auftrags-ID nach der entsprechenden **executionId** von SSIS zu suchen:
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   Wenn sich Ihre SSIS-Pakete in SSISDB befinden, verwenden Sie **ssisdb.internal.execution_parameter_values** als Tabelle für die Auftragsausführung. Wenn sich Ihre SSIS-Pakete im Dateisystem befinden, verwenden Sie **ssisdb.internal.execution_parameter_values_noncatalog**.
1. Klicken Sie mit der rechten Maustaste auf den SSISDB-Katalog, und wählen Sie dann **Aktive Vorgänge** aus.

   ![„Aktive Vorgänge“ im Kontextmenü für den SSISDB-Katalog](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. Beenden Sie den entsprechenden Vorgang basierend auf der **executionId**.

## <a name="next-steps"></a>Nächste Schritte
Sie können SSIS-Pakete auch mit Azure Data Factory planen. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Azure Data Factory-Ereignisauslöser](how-to-create-event-trigger.md).