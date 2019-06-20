---
title: Anpassen des Setups für Azure-SSIS Integration Runtime | Microsoft-Dokumentation
description: In diesem Artikel wird die Verwendung der Schnittstelle für das benutzerdefinierte Setup von Azure-SSIS Integration Runtime zum Installieren zusätzlicher Komponenten oder Ändern von Einstellungen beschrieben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: cfa9d6a1a287281bec91facf04c73506db81f84a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711560"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Anpassen des Setups für Azure-SSIS Integration Runtime

Mit der Schnittstelle für das benutzerdefinierte Setup von Azure-SSIS Integration Runtime (IR) können Sie während der Bereitstellung oder Neukonfiguration von Azure-SSIS IR eigene Setupschritte hinzufügen. Durch benutzerdefiniertes Setup können Sie die Standardkonfiguration oder -umgebung für den Betrieb ändern (z.B. zusätzliche Windows-Dienste starten oder Anmeldeinformationen für den Zugriff auf Dateifreigaben beibehalten). Sie können aber auch für jeden Knoten von Azure-SSIS IR zusätzliche Komponenten (z.B. Assemblys, Treiber oder Erweiterungen) installieren.

Zur Konfiguration des benutzerdefinierten Setups müssen Sie ein Skript und die zugehörigen Dateien vorbereiten und diese in einen Blobcontainer in Ihrem Azure Storage-Konto hochladen. Sie müssen einen SAS-URI (Shared Access Signature-Uniform Resource Identifier) für den Container bereitstellen, wenn Sie Azure-SSIS IR bereitstellen oder neu konfigurieren. Anschließend lädt jeder Knoten von Azure-SSIS IR das Skript und die zugehörigen Dateien aus dem Container herunter, und führt das benutzerdefinierte Setup mit erhöhten Rechten aus. Wenn das benutzerdefinierte Setup abgeschlossen ist, lädt jeder Knoten die standardmäßige Ausgabe der Ausführung und andere Protokolle in den Container hoch.

Sie können sowohl kostenlose bzw. unlizenzierte als auch kostenpflichtige oder lizenzierte Komponenten installieren. ISVs sollten den Artikel [Entwicklung von kostenpflichtigen oder lizenzierten Komponenten für Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md) lesen.

> [!IMPORTANT]
> Knoten der v2-Serie der Azure-SSIS IR eignen sich nicht für ein benutzerdefiniertes Setup, daher sollten Sie stattdessen Knoten der v3-Serie verwenden.  Wenn Sie bereits Knoten der v2-Serie verwenden, wechseln Sie so bald wie möglich zu Knoten der v3-Serie.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

-   Wenn Sie `gacutil.exe` für die Installation von Assemblys im globalen Assemblycache (GAC) verwenden möchten, müssen Sie `gacutil.exe` bei Ihrem benutzerdefinierten Setup angeben oder die im Public Preview-Container bereitgestellte Kopie verwenden.

-   Wenn Sie auf einen Unterordner in Ihrem Skript verweisen möchten, unterstützt `msiexec.exe` nicht die `.\`-Notation, um auf den Stammordner zu verweisen. Verwenden Sie einen Befehl wie `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` anstelle von `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Wenn Azure-SSIS IR im Rahmen des benutzerdefinierten Setups einem virtuellen Netzwerk beitreten soll, wird nur das virtuelle Azure Resource Manager-Netzwerk unterstützt. Das klassische virtuelle Netzwerk wird nicht unterstützt.

-   Administrative Freigabe wird für die Azure-SSIS-Integrationslaufzeit derzeit nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zum Anpassen von Azure-SSIS IR benötigen Sie Folgendes:

-   [Azure-Abonnement](https://azure.microsoft.com/)

-   [Server mit Azure SQL-Datenbank oder verwalteter Azure SQL-Datenbank-Instanz](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Bereitstellung von Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Azure Storage-Konto](https://azure.microsoft.com/services/storage/) Für das benutzerdefinierte Setup müssen Sie das benutzerdefinierte Setupskript und die zugehörigen Dateien in einen Blobcontainer hochladen und dort speichern. Auch die Ausführungsprotokolle werden vom benutzerdefinierten Setupvorgang in diesen Blobcontainer hochgeladen.

## <a name="instructions"></a>Anleitung

1. Laden Sie [Azure PowerShell](/powershell/azure/install-az-ps)herunter, und installieren Sie sie.

1. Bereiten Sie Ihr benutzerdefiniertes Setupskript und die zugehörigen Dateien (z. B. BAT-, CMD-, EXE-, DLL-, MSI- oder PS1-Dateien) vor.

   1.  Sie benötigen eine Skriptdatei namens `main.cmd`. Sie ist der Einstiegspunkt für Ihr benutzerdefiniertes Setup.

   1.  Wenn zusätzliche Protokolle von anderen Tools (z. B. `msiexec.exe`) generiert und in den Container hochgeladen werden sollen, geben Sie die vordefinierte Umgebungsvariable `CUSTOM_SETUP_SCRIPT_LOG_DIR` als Protokollordner in Ihren Skripts an (z. B. `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Laden Sie [Azure Storage-Explorer](https://storageexplorer.com/) herunter, installieren und starten Sie ihn.

   1. Klicken Sie unter **(Lokal und angefügt)** mit der rechten Maustaste auf **Speicherkonten**, und wählen Sie **Verbindung mit Azure-Speicher herstellen** aus.

      ![Herstellen einer Verbindung mit Azure-Speicher](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Aktivieren Sie **Einen Speicherkontonamen und -schlüssel verwenden**, und wählen Sie dann **Weiter** aus.

      ![Verwenden eines Speicherkontonamens und -schlüssels](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Geben Sie Ihren Azure Storage-Kontonamen und -schlüssel ein, und wählen Sie **Weiter** und dann **Verbinden** aus.

      ![Bereitstellen von Speicherkontoname und -schlüssel](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. Klicken Sie unter Ihrem verbundenen Azure Storage-Konto mit der Maustaste auf **Blobcontainer**, wählen Sie **Blobcontainer erstellen** aus, und benennen Sie den neuen Container.

      ![Erstellen eines Blobcontainers](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Wählen Sie den neuen Container aus, und laden Sie Ihr benutzerdefiniertes Setupskript und die zugehörigen Dateien hoch. Stellen Sie sicher, dass Sie `main.cmd` auf die oberste Ebene des Containers und nicht in einen Ordner hochladen. Vergewissern Sie sich außerdem, dass Ihr Container nur die erforderlichen Dateien für das benutzerdefinierte Setup enthält, damit das spätere Herunterladen in Ihre Azure-SSIS IR nicht so lange dauert. Der maximale Zeitraum für das benutzerdefinierte Setup ist derzeit auf 45 Minuten festgelegt, bevor ein Timeout auftritt, und dies umfasst auch die Zeit zum Herunterladen aller Dateien aus Ihrem Container und deren Installation auf Azure-SSIS IR. Wenn eine längere Frist erforderlich ist, senden Sie ein Supportticket.

      ![Hochladen von Dateien in den Blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Klicken Sie mit der rechten Maustaste auf den Container, und wählen Sie **Shared Access Signature abrufen** aus.

      ![Abrufen der Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Erstellen Sie den SAS-URI für den Container mit einer ausreichend langen Ablaufzeit und mit Lese-, Schreib- und Auflistungsberechtigungen. Den SAS-URI benötigen Sie jedes Mal, wenn für einen Knoten von Azure-SSIS IR ein Reimaging ausgeführt / er neu gestartet wird, zum Herunterladen und Ausführen Ihres benutzerdefinierten Setupskripts und der zugehörigen Dateien. Schreibberechtigungen sind zum Hochladen der Setupausführungsprotokolle erforderlich.

      > [!IMPORTANT]
      > Stellen Sie sicher, dass der SAS-URI nicht abläuft und benutzerdefinierte Installationsressourcen während des gesamten Lebenszyklus Ihres Azure-SSIS IR vom Erstellen bis zum Löschen stets verfügbar sind, vor allem, wenn Sie Ihren Azure-SSIS IR während dieses Zeitraums regelmäßig beenden und starten.

      ![Generieren der Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Kopieren und speichern Sie den SAS-URI des Containers.

      ![Kopieren und Speichern der Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Wenn Sie Ihre Azure-SSIS IR mit der Data Factory-Benutzeroberfläche bereitstellen oder neu konfigurieren, geben Sie vor dem Start Ihrer Azure-SSIS IR die SAS-URI Ihres Containers in das entsprechende Feld im Bereich **Erweiterte Einstellungen** ein:

      ![Eingeben der Shared Access Signature](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Wenn Sie Azure-SSIS IR mit PowerShell bereitstellen oder neu konfigurieren, führen Sie vor dem Start von Azure-SSIS IR das Cmdlet `Set-AzDataFactoryV2IntegrationRuntime` mit dem SAS-URI des Containers als Wert für den neuen Parameter `SetupScriptContainerSasUri` aus. Beispiel:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Nachdem das benutzerdefinierte Setup abgeschlossen und Azure-SSIS IR gestartet wurde, finden Sie die Standardausgabe von `main.cmd` und andere Ausführungsprotokolle im Ordner `main.cmd.log` Ihres Speichercontainers.

1. Um weitere Beispiele für ein benutzerdefiniertes Setup anzuzeigen, stellen Sie mit Azure Storage-Explorer eine Verbindung zum Container „Öffentliche Vorschau“ her.

   a.  Klicken Sie unter **(Lokal und angefügt)** mit der rechten Maustaste auf **Speicherkonten**, und wählen Sie nacheinander **Mit Azure-Speicher verbinden**, **Verbindungszeichenfolge oder SAS-URI verwenden** und **Weiter** aus.

      ![Herstellen einer Verbindung zum Azure-Speicher mit Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Wählen Sie **SAS-URI verwenden** aus, und geben Sie den folgenden SAS-URI für den Container „Öffentliche Vorschau“ ein. Wählen Sie **Weiter** und dann **Verbinden** aus.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Bereitstellen von Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wählen Sie den verbundenen Container „Öffentliche Vorschau“ aus, und doppelklicken Sie auf den Ordner `CustomSetupScript`. In diesem Ordner befinden sich die folgenden Elemente:

      1. Ein Ordner `Sample` mit einem benutzerdefinierten Setup zum Installieren eines einfachen Tasks auf jedem Knoten des Azure-SSIS IR. Der Task hat keine Funktion, bleibt aber ein paar Sekunden im Standbymodus. Der Ordner enthält außerdem einen `gacutil`-Ordner, dessen gesamter Inhalt (`gacutil.exe`, `gacutil.exe.config` und `1033\gacutlrc.dll`) unverändert in Ihren Container kopiert werden kann. Darüber hinaus enthält `main.cmd` Kommentare zum Beibehalten von Anmeldeinformationen für den Zugriff auf Dateifreigaben.

      1. Der Ordner `UserScenarios` mit mehreren benutzerdefinierten Setups für echte Benutzerszenarios.

   ![Inhalt des Containers „Öffentliche Vorschau“](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Doppelklicken Sie auf den Ordner `UserScenarios`. In diesem Ordner befinden sich die folgenden Elemente:

      1. Ein Ordner `.NET FRAMEWORK 3.5` mit einem benutzerdefinierten Setup zum Installieren einer früheren Version von .NET Framework, das möglicherweise für benutzerdefinierte Komponenten auf jedem Knoten von Azure-SSIS IR erforderlich ist.

      1. Ein Ordner `BCP` mit einem benutzerdefinierten Setup zum Installieren von SQL Server-Befehlszeilenprogrammen (`MsSqlCmdLnUtils.msi`), einschließlich des Massenkopierprogramms (`bcp`), auf jedem Knoten von Azure-SSIS IR.

      1. Ein Ordner `EXCEL` mit einem benutzerdefinierten Setup zum Installieren von Open Source-Assemblys (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll` und `ExcelDataReader.dll`) auf jedem Knoten von Azure-SSIS IR.

      1. Ein Ordner namens `ORACLE ENTERPRISE` mit einem benutzerdefinierten Setupskript (`main.cmd`) und einer Konfigurationsdatei für die unbeaufsichtigte Installation (`client.rsp`) zum Installieren der Oracle-Connectors und des OCI-Treibers auf jedem Knoten von Azure-SSIS IR, Enterprise Edition. Bei diesem Setup können Sie Oracle Connection Manager, Quelle und Ziel verwenden. Laden Sie zunächst Microsoft Connectors v5.0 für Oracle (`AttunitySSISOraAdaptersSetup.msi` und `AttunitySSISOraAdaptersSetup64.msi`) aus dem [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) und den neuesten Oracle-Client (z.B. `winx64_12102_client.zip`) von [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) herunter, und laden Sie dann alles zusammen mit `main.cmd` und `client.rsp` in Ihren Container hoch. Wenn Sie bei der Herstellung einer Verbindung mit Oracle TNS verwenden, müssen Sie auch `tnsnames.ora` herunterladen, bearbeiten und in Ihren Container hochladen, damit die Datei während des Setups in den Oracle-Installationsordner kopiert werden kann.

      1. Ein Ordner `ORACLE STANDARD ADO.NET` mit einem benutzerdefinierten Setupskript (`main.cmd`) zum Installieren des Oracle ODP.NET-Treibers auf jedem Knoten von Azure-SSIS IR. Bei diesem Setup können Sie ADO.NET Connection Manager, Quelle und Ziel verwenden. Laden Sie zunächst den aktuellen Oracle ODP.NETe-Treiber – z.B. `ODP.NET_Managed_ODAC122cR1.zip` – von [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) herunter, und laden Sie ihn anschließend zusammen mit `main.cmd` in Ihren Container hoch.
       
      1. Ein Ordner `ORACLE STANDARD ODBC` mit einem benutzerdefinierten Setupskript (`main.cmd`) zum Installieren des Oracle ODBC-Treibers und Konfigurieren von DSN auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie den ODBC-Verbindungs-Manager (Quelle/Ziel) oder den Power Query-Verbindungs-Manager (Quelle) mit ODBC-Datenquellen verwenden, um eine Verbindung mit dem Oracle-Server herzustellen. Laden Sie zunächst den neuesten Oracle Instant Client (Basic-Paket oder Basic Lite-Paket) und das ODBC-Paket herunter – z.B. die 64-Bit-Pakete [hier](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-Paket: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, Basic Lite-Paket: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, ODBC-Paket: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) oder die 32-Bit-Pakete [hier](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-Paket: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, Basic Lite-Paket: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, ODBC-Paket: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`). Anschließend laden Sie diese zusammen mit `main.cmd` in Ihren Container hoch.

      1. Ein Ordner namens `SAP BW` mit einem benutzerdefinierten Setupskript (`main.cmd`) zum Installieren des .NET-Connector-Assemblys von SAP (`librfc32.dll`) auf jedem Knoten von Azure-SSIS IR, Enterprise Edition. Bei diesem Setup können Sie SAP BW-Verbindungs-Manager, Quelle und Ziel verwenden. Laden Sie zuerst die 64-Bit- oder 32-Bit-Version von `librfc32.dll` aus dem SAP-Installationsordner zusammen mit `main.cmd`in Ihren Container hoch. Das Skript kopiert dann während des Setups die SAP-Assembly in den Ordner `%windir%\SysWow64` oder `%windir%\System32`.

      1. Ein Ordner `STORAGE` mit einem benutzerdefinierten Setup zum Installieren von Azure PowerShell auf jedem Knoten von Azure-SSIS IR. Bei diesem Setup können Sie SSIS-Pakete bereitstellen und ausführen, die [PowerShell-Skripts zum Bearbeiten Ihres Azure Storage-Kontos](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) ausführen. Kopieren Sie `main.cmd`, ein Muster von `AzurePowerShell.msi` (oder installieren Sie die neueste Version) und `storage.ps1` auf den Container. Verwenden Sie PowerShell.dtsx als Vorlage für Ihre Pakete. In der Paketvorlage sind der [Azure Blob-Download-Task](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), der `storage.ps1` als modifizierbares PowerShell-Skript herunterlädt, und der [Task „Prozess ausführen“](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/), der das Skript auf jedem Knoten ausführt, zusammengefasst.

      1. Ein Ordner `TERADATA`, der ein benutzerdefiniertes Setupskript (`main.cmd` mit der zugehörigen Datei `install.cmd`) und die Installationspakete (`.msi`) enthält. Diese Dateien installieren Teradata-Connectors, die TPT-API und den ODBC-Treiber auf jedem Knoten von Azure-SSIS IR, Enterprise Edition. Bei diesem Setup können Sie Teradata-Verbindungs-Manager, Quelle und Ziel verwenden. Laden Sie zuerst die 15.x-ZIP-Datei (z. B. `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) mit den Teradata Tools und Utilities (TTU) von [Teradata](http://partnerintelligence.teradata.com) herunter, und laden Sie diese dann zusammen mit den oben genannten Dateien `.cmd` und `.msi` in Ihren Container hoch.

   ![Ordner im Ordner „Benutzerszenarios“](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Um diese Beispiele für ein benutzerdefiniertes Setup zu testen, kopieren Sie den Inhalt des ausgewählten Ordners, und fügen Sie ihn in Ihren Container ein. Wenn Sie Azure-SSIS IR mit PowerShell bereitstellen oder neu konfigurieren, führen Sie das Cmdlet `Set-AzDataFactoryV2IntegrationRuntime` mit dem SAS-URI des Containers als Wert für den neuen Parameter `SetupScriptContainerSasUri` aus.

## <a name="next-steps"></a>Nächste Schritte

-   [Enterprise Edition von Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Entwicklung von kostenpflichtigen oder lizenzierten benutzerdefinierten Komponenten für Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
