---
title: Anpassen des Setups für eine Azure-SSIS Integration Runtime
description: In diesem Artikel wird beschrieben, wie über die Schnittstelle für das benutzerdefinierte Setup für eine Azure-SSIS Integration Runtime zusätzliche Komponenten installiert oder Einstellungen geändert werden können.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 11/06/2020
ms.openlocfilehash: bc5d2cad7b4fbf1871d2c02dc91db30daf55e855
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600726"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Anpassen des Setups für eine Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Sie können Ihre Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) über benutzerdefinierte Setups anpassen. Diese bieten Ihnen die Möglichkeit, während der Bereitstellung oder Neukonfiguration Ihrer Azure-SSIS IR eigene Schritte hinzufügen. 

Mithilfe benutzerdefinierter Setups können Sie die Standardkonfiguration des Betriebssystems oder der Umgebung Ihrer Azure-SSIS IR ändern. Auf diese Weise können Sie beispielsweise zusätzliche Windows-Dienste starten, Zugriffsanmeldeinformationen für Dateifreigaben beibehalten oder eine starke Kryptografie/ein sichereres Netzwerkprotokoll (TLS 1.2) verwenden. Oder Sie können zusätzliche Komponenten, z. B. Assemblys, Treiber oder Erweiterungen, auf jedem Knoten ihrer Azure-SSIS IR installieren. Dies können benutzerdefinierte, Open Source- oder Drittanbieterkomponenten sein. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md).

Es gibt zwei Möglichkeiten zur Durchführung benutzerdefinierter Setups auf Ihrer Azure-SSIS IR: 
* **Benutzerdefiniertes Standard-Setup mit einem Skript**: Bereiten Sie ein Skript und die zugehörigen Dateien vor, und laden Sie alle zusammen in einen Blobcontainer in Ihrem Azure Storage-Konto hoch. Sie müssen dann einen SAS-URI (Shared Access Signature-Uniform Resource Identifier) für Ihren Container bereitstellen, wenn Sie Azure-SSIS IR einrichten oder neu konfigurieren. Anschließend lädt jeder Knoten Ihrer Azure-SSIS IR das Skript und die zugehörigen Dateien aus Ihrem Container herunter und führt Ihr benutzerdefiniertes Setup mit erhöhten Berechtigungen aus. Wenn Ihr benutzerdefiniertes Setup abgeschlossen ist, lädt jeder Knoten die standardmäßige Ausgabe der Ausführung und andere Protokolle in den Container hoch.
* **Benutzerdefiniertes Express-Setup ohne ein Skript**: Führen Sie einige gängige Systemkonfigurationen und Windows-Befehle aus, oder installieren Sie einige beliebte oder empfohlene zusätzliche Komponenten, ohne Skripts zu verwenden.

Sie können sowohl kostenlose (nicht lizenzierte) als auch kostenpflichtige (lizenzierte) Komponenten mit benutzerdefinierten Standard- und Express-Setups installieren. Informationen für unabhängige Softwareanbieter (Independent Software Vendor, ISV) finden Sie unter [Installieren kostenpflichtiger oder lizenzierter benutzerdefinierter Komponenten für Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Um von zukünftigen Verbesserungen zu profitieren, empfehlen wir die Verwendung von mindestens V3 für Ihre Azure-SSIS IR mit benutzerdefiniertem Setup.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Die folgenden Einschränkungen gelten nur für benutzerdefinierte Standard-Setups:

- Wenn Sie *gacutil.exe* in Ihrem Skript verwenden möchten, um Assemblys im globalen Assemblycache (GAC) zu installieren, müssen Sie *gacutil.exe* als Teil Ihres benutzerdefinierten Setups bereitstellen. Sie können auch die im Ordner *Sample* unseres *Public Preview*-Containers bereitgestellte Kopie verwenden. Weitere Informationen finden Sie unten im Abschnitt **Beispiele für benutzerdefinierte Standardsetups**.

- Wenn Sie in Ihrem Skript auf einen Unterordner verweisen möchten, unterstützt *msiexec.exe* nicht die `.\`-Notation zum Verweisen auf den Stammordner. Verwenden Sie einen Befehl, z. B. `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`, anstelle von `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Administrative Freigaben oder verborgene Netzwerkfreigaben, die von Windows automatisch erstellt werden, werden in der Azure-SSIS IR derzeit nicht unterstützt.

- Der ODBC-Treiber „IBM iSeries Access“ wird in der Azure-SSIS IR nicht unterstützt. Während Ihres benutzerdefinierten Setups werden möglicherweise Installationsfehler angezeigt. Sollte dies der Fall sein, wenden Sie sich an den IBM-Kundendienst.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zum Anpassen Ihrer Azure-SSIS IR benötigen Sie die folgenden Elemente:

- [Ein Azure-Abonnement](https://azure.microsoft.com/)

- [Bereitstellung von Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)

- [Ein Azure-Speicherkonto](https://azure.microsoft.com/services/storage/). Nicht erforderlich für benutzerdefinierte Express-Setups. Für benutzerdefinierte Standard-Setups müssen Sie das benutzerdefinierte Setupskript und die zugehörigen Dateien in einen Blobcontainer hochladen und dort speichern. Auch die Ausführungsprotokolle werden vom benutzerdefinierten Setupvorgang in diesen Blobcontainer hochgeladen.

## <a name="instructions"></a>Instructions

Sie können Ihre Azure-SSIS IR mit benutzerdefinierten Setups auf der ADF-Benutzeroberfläche bereitstellen oder neu konfigurieren. Wenn Sie diese Vorgänge über PowerShell ausführen möchten, laden Sie [Azure PowerShell](/powershell/azure/install-az-ps) herunter, und installieren Sie das Tool.

### <a name="standard-custom-setup"></a>Benutzerdefiniertes Standardsetup

Um Ihre Azure-SSIS IR mit benutzerdefinierten Standardsetups auf der ADF-Benutzeroberfläche bereitzustellen oder neu zu konfigurieren, führen Sie die folgenden Schritte aus.

1. Bereiten Sie Ihr benutzerdefiniertes Setupskript und die zugehörigen Dateien (z. B. BAT-, CMD-, EXE-, DLL-, MSI- oder PS1-Dateien) vor.

   * Sie benötigen dazu die Skriptdatei *main.cmd*. Sie ist der Einstiegspunkt für Ihr benutzerdefiniertes Setup.  
   * Um sicherzustellen, dass das Skript im Hintergrund ausgeführt werden kann, sollten Sie es zuerst auf Ihrem lokalen Computer testen.  
   * Wenn Sie möchten, dass zusätzliche Protokolle, die von anderen Tools (z. B. *msiexec.exe*) generiert wurden, in Ihren Container hochgeladen werden, geben Sie die vordefinierte Umgebungsvariable, `CUSTOM_SETUP_SCRIPT_LOG_DIR`, als Protokollordner in Ihren Skripts an (z. B. *msiexec/i xxx.msi/quiet/lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. Laden Sie [Azure Storage-Explorer](https://storageexplorer.com/) herunter, installieren und öffnen Sie ihn.

   a. Klicken Sie unter **(Lokal und angefügt)** mit der rechten Maustaste auf **Speicherkonten**, und wählen Sie dann **Verbindung mit Azure Storage herstellen** aus.

      ![Verbinden mit Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. Aktivieren Sie **Einen Speicherkontonamen und -schlüssel verwenden**, und klicken Sie dann auf **Weiter**.

      ![Verwenden eines Speicherkontonamens und -schlüssels](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. Geben Sie Ihren Azure Storage-Kontonamen und -schlüssel ein, und wählen Sie **Weiter** und dann **Verbinden** aus.

      ![Bereitstellen des Speicherkontonamens und -schlüssels](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. Klicken Sie unter Ihrem verbundenen Azure Storage-Konto mit der rechten Maustaste auf **Blobcontainer**, wählen Sie **Blobcontainer erstellen** aus, und benennen Sie den neuen Container.

      ![Erstellen eines Blobcontainers](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. Wählen Sie den neuen Container aus, und laden Sie Ihr benutzerdefiniertes Setupskript und die zugehörigen Dateien hoch. Stellen Sie sicher, dass Sie *main.cmd* auf die oberste Ebene Ihres Containers und nicht in einen Ordner hochladen. Ihr Container sollte nur die erforderlichen Dateien für das benutzerdefinierte Setup enthalten, damit das spätere Herunterladen in Ihre Azure-SSIS IR nicht lange dauert. Die maximale Dauer für ein benutzerdefiniertes Setup ist zurzeit auf 45 Minuten festgelegt, bevor ein Timeout eintritt. Diese Dauer umfasst die Zeit zum Herunterladen aller Dateien aus Ihrem Container und deren Installation in der Azure-SSIS IR. Wenn das Setup mehr Zeit erfordert, erstellen Sie ein Supportticket.

      ![Hochladen von Dateien in den Blobcontainer](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. Klicken Sie mit der rechten Maustaste auf den Container, und wählen Sie dann **Shared Access Signature abrufen** aus.

      ![Abrufen der Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. Erstellen Sie den SAS-URI für Ihren Container mit einer ausreichend langen Ablaufzeit sowie mit Lese-, Schreib- und Auflistungsberechtigungen. Den SAS-URI benötigen Sie zum Herunterladen und Ausführen Ihres benutzerdefinierten Setupskripts und der zugehörigen Dateien. Dies ist jedes Mal dann der Fall, wenn für einen Knoten Ihrer Azure-SSIS IR ein Reimaging durchgeführt oder er neu gestartet wird. Sie benötigen außerdem Schreibberechtigungen zum Hochladen der Setupausführungsprotokolle.

      > [!IMPORTANT]
      > Stellen Sie sicher, dass der SAS-URI nicht abläuft und dass die benutzerdefinierten Setupressourcen während des gesamten Lebenszyklus Ihrer Azure-SSIS IR (vom Erstellen bis zum Löschen) immer verfügbar sind – insbesondere dann, wenn Sie Ihre Azure-SSIS IR während dieses Zeitraums regelmäßig beenden und starten.

      ![Generieren der Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. Kopieren und speichern Sie den SAS-URI des Containers.

      ![Kopieren und Speichern der Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Aktivieren Sie im Bereich **Setup für Integration Runtime** auf der Seite **Erweiterte Einstellungen** das Kontrollkästchen **Azure-SSIS Integration Runtime mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen**. Als Nächstes geben Sie den SAS-URI Ihres Containers in das Textfeld **SAS-URI des Containers für benutzerdefinierte Setups** ein.

   ![Erweiterte Einstellungen bei benutzerdefinierten Setups](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

Nachdem Ihr benutzerdefiniertes Standardsetup abgeschlossen und Ihre Azure-SSIS IR gestartet wurde, können Sie alle benutzerdefinierten Setupprotokolle im Ordner *main.cmd.log* Ihres Containers finden. Diese umfassen die Standardausgabe von *main.cmd* und andere Ausführungsprotokolle.

### <a name="express-custom-setup"></a>Benutzerdefiniertes Express-Setup

Um Ihre Azure-SSIS IR mit benutzerdefinierten Express-Setups auf der ADF-Benutzeroberfläche bereitzustellen oder neu zu konfigurieren, führen Sie die folgenden Schritte aus.

1. Aktivieren Sie im Bereich **Setup für Integration Runtime** auf der Seite **Erweiterte Einstellungen** das Kontrollkästchen **Azure-SSIS Integration Runtime mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen**. 

1. Wählen Sie **Neu** aus, um den Bereich **Benutzerdefiniertes Express-Setup hinzufügen** zu öffnen, und wählen Sie dann in der Dropdownliste **Typ des benutzerdefinierten Express-Setups** den gewünschten Typ aus. Es stehen derzeit benutzerdefinierte Express-Setups für das Ausführen des Befehls „cmdkey“, das Hinzufügen von Umgebungsvariablen, das Installieren von Azure PowerShell und das Installieren lizenzierter Komponenten bereit.

#### <a name="running-cmdkey-command"></a>Ausführen des Befehls „cmdkey“

Wenn Sie den Typ **Befehl „cmdkey“ ausführen** für Ihr benutzerdefiniertes Express-Setup auswählen, können Sie den Windows-Befehl „cmdkey“ in Ihrer Azure-SSIS IR ausführen. Geben Sie hierzu in den Textfeldern **/Add**, **/User** und **/Pass** die Namen Ihres Zielcomputers oder Ihrer Domäne, des Benutzers oder Kontos sowie das Kennwort oder den Kontoschlüssel ein. Auf diese Weise können Sie Zugriffsanmeldeinformationen für SQL Server-Instanzen, Dateifreigaben oder Azure Files in Ihrer Azure-SSIS IR beibehalten. Für den Zugriff auf Azure Files können Sie beispielsweise `YourAzureStorageAccountName.file.core.windows.net`, `azure\YourAzureStorageAccountName` und `YourAzureStorageAccountKey` für **/Add**, **/User** und **/Pass** eingeben. Dies ist vergleichbar mit der Ausführung des Windows-Befehls [cmdkey](/windows-server/administration/windows-commands/cmdkey) auf Ihrem lokalen Computer. Derzeit wird nur ein benutzerdefiniertes Express-Setup für die Ausführung des Befehls „cmdkey“ unterstützt. Verwenden Sie zum Ausführen mehrerer Befehle vom Typ „cmdkey“ stattdessen ein benutzerdefiniertes Standardsetup.

#### <a name="adding-environment-variables"></a>Hinzufügen von Umgebungsvariablen

Wenn Sie den Typ **Umgebungsvariable hinzufügen** für Ihr benutzerdefiniertes Express-Setup auswählen, können Sie eine Windows-Umgebungsvariable in Ihrer Azure-SSIS IR hinzufügen. Geben Sie hierzu den Namen und den Wert der Umgebungsvariablen in die Textfelder **Variablenname** und **Variablenwert** ein. Auf diese Weise können Sie die Umgebungsvariable in den Paketen verwenden, die in der Azure-SSIS IR ausgeführt werden, z. B. in Skriptkomponenten/-tasks. Dies ist vergleichbar mit der Ausführung des Windows-Befehls [set](/windows-server/administration/windows-commands/set_1) auf Ihrem lokalen Computer.

#### <a name="installing-azure-powershell"></a>Installieren von Azure PowerShell

Wenn Sie den Typ **Azure PowerShell installieren** für das benutzerdefinierte Express-Setup auswählen, können Sie das Az-Modul von PowerShell in Ihrer Azure-SSIS IR installieren. Geben Sie dazu die gewünschte Versionsnummer des Az-Moduls (x.y.z) aus der [Liste unterstützter Versionen](https://www.powershellgallery.com/packages/az) ein. Auf diese Weise können Sie Azure PowerShell-Cmdlets/Skripts in Ihren Paketen zum Verwalten von Azure-Ressourcen ausführen, z. B. [Azure Analysis Services (AAS)](../analysis-services/analysis-services-powershell.md).

#### <a name="installing-licensed-components"></a>Installieren lizenzierter Komponenten

Wenn Sie den Typ **Lizenzierte Komponente installieren** für das benutzerdefinierte Express-Setup auswählen, können Sie anschließend in der Dropdownliste **Komponentenname** eine integrierte Komponente unserer ISV-Partner auswählen:

   * Wenn Sie die Komponente **Task Factory von SentryOne** auswählen, können Sie die [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)-Komponentensammlung von SentryOne in Ihrer Azure-SSIS IR installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **2020.1.3**.

   * Wenn Sie die Komponente **HEDDA.IO von oh22** auswählen, können Sie die [HEDDA.IO](https://github.com/oh22is/HEDDA.IO/tree/master/SSIS-IR)-Datenqualitäts-/Bereinigungskomponente von oh22 in Ihrer Azure-SSIS IR installieren. Dazu müssen Sie den entsprechenden Dienst zuvor erworben haben. Die aktuelle integrierte Version ist **1.0.14**.

   * Wenn Sie die Komponente **SQLPhonetics.NET von oh22** auswählen, können Sie die [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis)-Datenqualitäts-/Zuordnungskomponente von oh22 in Ihrer Azure-SSIS IR installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **1.0.45**.

   * Wenn Sie die Komponente **SSIS Integration Toolkit von KingswaySoft** auswählen, können Sie die [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365)-Connector-Sammlung für Apps für CRM/ERP/Marketing/Zusammenarbeit, z. B. Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud usw., von KingswaySoft in Ihrer Azure-SSIS IR installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **2020.1**.

   * Wenn Sie die Komponente **SSIS Productivity Pack von KingswaySoft** auswählen, können Sie die [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack)-Komponentensammlung von KingswaySoft in Ihrer Azure-SSIS IR installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **20.1**.

   * Wenn Sie die Komponente **Xtract IS von Theobald Software** auswählen, können Sie die [Xtract IS](https://theobald-software.com/en/xtract-is/)-Connector-Sammlung für SAP-Systeme (ERP, S/4HANA, BW) von Theobald Software in Ihrer Azure-SSIS IR installieren. Dazu müssen Sie die zuvor erworbene Produktlizenzdatei per Drag & Drop in das Eingabefeld **Lizenzdatei** ziehen oder hochladen. Die aktuelle integrierte Version ist **6.1.1.3**.

   * Wenn Sie die Komponente **Integration Service von AecorSoft** auswählen, können Sie die [Integration Service](https://www.aecorsoft.com/en/products/integrationservice)-Connector-Sammlung für SAP- und Salesforce-Systeme von AecorSoft in Ihrer Azure-SSIS IR installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **3.0.00**.

   * Wenn Sie die Komponente **CData's SSIS Standard Package** auswählen, können Sie die Suite [SSIS Standard Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#standard) mit den gängigsten Komponenten von CData (wie etwa Microsoft SharePoint-Connectors) in Ihrer Azure-SSIS IR-Instanz installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **19.7354**.

   * Wenn Sie die Komponente **CData's SSIS Extended Package** auswählen, können Sie die Suite [SSIS Extended Package](https://www.cdata.com/kb/entries/ssis-adf-packages.rst#extended) mit allen Komponenten von CData (etwa Microsoft Dynamics 365 Business Central-Connectors und andere Komponenten aus **SSIS Standard Package**) in Ihrer Azure-SSIS IR-Instanz installieren. Geben Sie hierzu im Textfeld **Lizenzschlüssel** den zuvor erworbenen Produktlizenzschlüssel ein. Die aktuelle integrierte Version ist **19.7354**. Stellen Sie aufgrund des großen Umfangs sicher, dass Ihre Azure-SSIS IR-Instanz über mindestens vier CPU-Kerne pro Knoten verfügt, um ein Timeout bei der Installation zu vermeiden.

Ihre hinzugefügten benutzerdefinierten Express-Setups werden auf der Seite **Erweiterte Einstellungen** angezeigt. Wenn Sie sie entfernen möchten, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

### <a name="azure-powershell"></a>Azure PowerShell

Um Ihre Azure-SSIS IR über Azure PowerShell mit benutzerdefinierten Setups bereitzustellen oder neu zu konfigurieren, führen Sie die folgenden Schritte aus.

1. Wenn Ihre Azure-SSIS IR bereits ausgeführt wird, halten Sie sie zuerst an.

1. Dann können Sie benutzerdefinierte Setups hinzufügen oder entfernen, indem Sie das Cmdlet `Set-AzDataFactoryV2IntegrationRuntime` ausführen, bevor Sie Ihre Azure-SSIS IR starten.

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "InstallAzurePowerShell")
       {
           $moduleVersion = "YourAzModuleVersion"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Standard")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "CData.Extended")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```

### <a name="standard-custom-setup-samples"></a>Beispiele für benutzerdefinierte Standardsetups

Um Beispiele für benutzerdefinierte Standardsetups anzuzeigen und wiederzuverwenden, führen Sie die folgenden Schritte aus.

1. Stellen Sie über Azure Storage-Explorer eine Verbindung mit unserem Public Preview-Container her.

   a. Klicken Sie unter **(Lokal und angefügt)** mit der rechten Maustaste auf **Speicherkonten**, und wählen Sie nacheinander **Mit Azure-Speicher verbinden**, **Verbindungszeichenfolge oder SAS-URI verwenden** und **Weiter** aus.

      ![Herstellen einer Verbindung zum Azure-Speicher mit Shared Access Signature](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. Wählen Sie **SAS-URI verwenden** aus, und geben Sie dann im Textfeld **URI** den folgenden SAS-URI ein:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![Bereitstellen von Shared Access Signature für den Container](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wählen Sie **Weiter** und anschließend **Verbinden** aus.

   d. Wählen Sie im linken Bereich den verbundenen Container **publicpreview** aus, und doppelklicken Sie dann auf den Ordner *CustomSetupScript*. In diesem Ordner befinden sich die folgenden Elemente:

      * Der Ordner *Beispiel* mit einem benutzerdefinierten Setup zum Installieren eines einfachen Tasks auf jedem Knoten Ihrer Azure-SSIS IR. Der Task hat keine Funktion, bleibt aber ein paar Sekunden im Standbymodus. Außerdem enthält der Ordner den Ordner *gacutil*, dessen gesamter Inhalt (*gacutil.exe*, *gacutil.exe.config* und *1033\ gacutlrc.dll*) unverändert in Ihren Container kopiert werden kann.

      * Der Ordner *UserScenarios* mit mehreren Beispielen für benutzerdefinierte Setups von echten Benutzerszenarien. Wenn Sie mehrere Beispiele auf Ihrer Azure-SSIS IR installieren möchten, können Sie deren benutzerdefiniertes Setupskriptdateien (*main.cmd*) zu einer kombinieren und mit allen zugehörigen Dateien in den Container hochladen.

        ![Inhalt des Containers „Öffentliche Vorschau“](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. Doppelklicken Sie auf den Ordner *UserScenarios*, um die folgenden Elemente zu suchen:

      * Den Ordner *.NET FRAMEWORK 3.5* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren einer früheren Version von .NET Framework auf jedem Knoten Ihrer Azure-SSIS IR. Diese Version ist möglicherweise für einige benutzerdefinierte Komponenten erforderlich.

      * Den Ordner *BCP* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren von SQL Server-Befehlszeilenprogrammen (*MsSqlCmdLnUtils.msi*) auf jedem Knoten Ihrer Azure-SSIS IR. Eines dieser Hilfsprogramme ist das Massenkopierprogramm (*bcp*).

      * Den Ordner *DNS SUFFIX*. Dieser enthält ein benutzerdefiniertes Setupskript (*main.cmd*), um ein eigenes DNS-Suffix (etwa *test.com*) an alle nicht qualifizierten einteiligen Domänennamen anzufügen und sie in vollqualifizierte Domänennamen umzuwandeln (Fully Qualified Domain Name, FQDN), bevor diese über die Azure-SSIS IR in DNS-Abfragen verwendet werden.

      * Den Ordner *EXCEL* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren einiger C#-Assemblys und -Bibliotheken auf jedem Knoten Ihrer Azure-SSIS IR. Diese können Sie in Skripttasks zum dynamischen Lesen und Schreiben von Excel-Dateien verwenden. 
      
        Laden Sie zuerst [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) und [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) herunter, und laden Sie dann alle zusammen mit *main.cmd* in Ihren Container hoch. Wenn Sie lediglich die Excel-Standardconnectors (Verbindungs-Manager, Quelle und Ziel) verwenden möchten, ist die Access Redistributable-Komponente mit diesen Connectors bereits in der Azure-SSIS IR vorinstalliert, sodass kein benutzerdefiniertes Setup notwendig ist.
      
      * Den Ordner *MYSQL ODBC* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren der MySQL ODBC-Treibers auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie die ODBC-Connectors (Verbindungs-Manager, Quelle und Ziel) für die Verbindung mit dem MySQL-Server verwenden. 
     
        [Laden Sie zuerst die neuesten 64-Bit- und 32-Bit-Versionen der MySQL ODBC-Treiberinstallationsprogramme](https://dev.mysql.com/downloads/connector/odbc/) herunter (z. B. *mysql-connector-odbc-8.0.13-winx64.msi* und *mysql-connector-odbc-8.0.13-win32.msi*), und laden Sie dann alle zusammen mit *main.cmd* in Ihren Container hoch.

      * Den Ordner *ORACLE ENTERPRISE* mit einem benutzerdefinierten Setupskript (*main.cmd*) und einer Konfigurationsdatei für die unbeaufsichtigte Installation (*client.rsp*) zum Installieren der Oracle-Connectors und des OCI-Treibers auf jedem Knoten Ihrer Azure-SSIS IR Enterprise Edition. Bei diesem Setup können Sie den Oracle-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem Oracle-Server verwenden. 
      
        Laden Sie zuerst Microsoft Connectors v5.0 für Oracle (*AttunitySSISOraAdaptersSetup.msi* und *AttunitySSISOraAdaptersSetup64.msi*) aus dem [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) sowie den neuesten Oracle-Client (z. B *winx64_12102_client.zip*) von [Oracle](https://www.oracle.com/database/technologies/oracle19c-windows-downloads.html) herunter. Als Nächstes laden Sie dann alle zusammen mit *main.cmd* und *client.rsp* auf Ihren Container hoch. Wenn Sie TNS zum Herstellen einer Verbindung mit Oracle verwenden, müssen Sie auch die Datei *tnsnames.ora* herunterladen, bearbeiten und auf Ihren Container hochladen. Auf diese Weise kann sie während des Setups in den Oracle-Installationsordner kopiert werden.

      * Den Ordner *ORACLE STANDARD ADO.NET* mit einem benutzerdefinierten Setupskript *(main.cmd*) zum Installieren des Oracle ODP.NET-Treibers auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie den ADO.NET-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem Oracle-Server verwenden. 
      
        [Laden Sie zuerst den neuesten Oracle ODP.NET-Treiber herunter](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (z. B. *ODP.NET_Managed_ODAC122cR1.zip*), und laden Sie ihn dann zusammen mit *main.cmd* in Ihren Container hoch.
       
      * Den Ordner *ORACLE STANDARD ODBC* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren des Oracle ODBC-Treibers auf jedem Knoten Ihrer Azure-SSIS IR. Das Skript konfiguriert auch den Datenquellennamen (DSN). Bei diesem Setup können Sie den ODBC-Verbindungs-Manager, die Quelle und das Ziel oder den Power Query-Verbindungs-Manager und die Quelle mit dem ODBC-Datenquellentyp verwenden, um eine Verbindung mit dem Oracle-Server herzustellen. 
      
        Laden Sie zuerst das neueste Oracle Instant Client-Paket (Basic-Paket oder Basic Lite-Paket) und das ODBC-Paket herunter, und laden Sie dann alle zusammen mit *main.cmd* in Ihren Container hoch:
        * [Herunterladen von 64-Bit-Paketen](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic-Paket: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*; Basic Lite-Paket: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*; ODBC-Paket: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [Herunterladen von 32-Bit-Paketen](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic-Paket: *instantclient-basic-nt-18.3.0.0.0dbru.zip*; Basic Lite-Paket: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*; ODBC-Paket: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * Den Ordner *ORACLE STANDARD OLEDB* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren des Oracle OLE DB-Treibers auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie den OLE DB-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem Oracle-Server verwenden. 
     
        [Laden Sie zuerst den neuesten Oracle OLE DB-Treiber herunter](https://www.oracle.com/partners/campaign/index-090165.html) (z. B. *ODAC122010Xcopy_x64.zip*), und laden Sie ihn dann zusammen mit *main.cmd* in Ihren Container hoch.

      * Den Ordner *POSTGRESQL ODBC* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren des PostgreSQL ODBC-Treibers auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie den ODBC-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem PostgreSQL-Server verwenden. 
     
        [Laden Sie zuerst die neuesten 64-Bit- und 32-Bit-Versionen der PostgreSQL-ODBC-Treiberinstallationsprogramme herunter](https://www.postgresql.org/ftp/odbc/versions/msi/) (z. B. *psqlodbc_x64. msi* und *psqlodbc_x86. msi*), und laden Sie dann alle zusammen mit *main.cmd* in Ihren Container hoch.

      * Den Ordner *SAP BW* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren der .NET-Connector-Assembly von SAP (*librfc32.dll*) auf jedem Knoten Ihrer Azure-SSIS IR Enterprise Edition. Bei diesem Setup können Sie den SAP BW-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem SAP BW-Server verwenden. 
      
        Laden Sie zuerst die 64-Bit- oder 32-Bit-Version von *librfc32.dll* aus dem SAP-Installationsordner zusammen mit *main.cmd* in Ihren Container hoch. Das Skript kopiert dann die SAP-Assembly während des Setups in den Ordner *%windir%\SysWow64* oder *%windir%\System32*.

      * Den Ordner *STORAGE* mit einem benutzerdefinierten Setupskript (*main.cmd*) zum Installieren von Azure PowerShell auf jedem Knoten Ihrer Azure-SSIS IR. Bei diesem Setup können Sie SSIS-Pakete bereitstellen und ausführen, die [Azure PowerShell-Cmdlets/Skripts zum Verwalten von Azure Storage](../storage/blobs/storage-quickstart-blobs-powershell.md) ausführen. 
      
        Kopieren Sie *main.cmd*, ein Beispiel für *AzurePowerShell.msi* (oder verwenden Sie die neueste Version) und *storage.ps1* in Ihren Container. Verwenden Sie *PowerShell.dtsx* als Vorlage für Ihre Pakete. In der Paketvorlage sind der [Azure Blob-Download-Task](/sql/integration-services/control-flow/azure-blob-download-task), der ein modifizierbares PowerShell-Skript (*storage.ps1*) herunterlädt, und der [Task „Prozess ausführen“](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) zusammengefasst, der das Skript auf jedem Knoten ausführt.

      * Der Ordner *TERADATA*, der ein benutzerdefiniertes Setupskript (*main.cmd*), die zugehörige Datei (*install.cmd*) und die Installer-Pakete ( *.msi*) enthält. Diese Dateien installieren die Teradata-Connectors, die Teradata Parallel Transporter (TPT)-API und den ODBC-Treiber auf jedem Knoten Ihrer Azure-SSIS IR Enterprise Edition. Bei diesem Setup können Sie den Teradata-Verbindungs-Manager, die Quelle und das Ziel für die Verbindung mit dem Teradata-Server verwenden. 
      
        [Laden Sie zuerst die ZIP-Datei „Teradata Tools und Utilities 15.x“ herunter](http://partnerintelligence.teradata.com) (z. B. *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*), und laden Sie sie dann zusammen mit den zuvor erwähnten Dateien *.cmd* und *.msi* Dateien in Ihren Container hoch.

      * Den Ordner *TLS 1.2* mit einem benutzerdefinierten Setupskript (*main.cmd*) zur Verwendung einer starken Kryptografie und eines sichereren Netzwerkprotokolls (TLS 1.2) auf jedem Knoten Ihrer Azure-SSIS IR. Das Skript deaktiviert auch ältere SSL/TLS-Versionen.

      * Den Ordner *ZULU OPENJDK* Ordner mit einem benutzerdefinierten Setupskript (*main.cmd*) und einer PowerShell-Datei (*install_openjdk.ps1*) zum Installieren des Zulu OpenJDK auf jedem Knoten Ihrer Azure-SSIS IR. Dieses Setup ermöglicht Ihnen die Verwendung von Azure Data Lake Store- und Flexible File-Connectors zur Verarbeitung von ORC- und Parquet-Dateien. Weitere Informationen finden Sie unter [Azure Feature Pack für Integration Services](/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java). 
      
        [Laden Sie zuerst das neueste Zulu OpenJDK herunter](https://www.azul.com/downloads/zulu/zulu-windows/) (z. B. *zulu8.33.0.1-jdk8.0.192-win_x64.zip*), und laden Sie es dann zusammen mit *main.cmd* und *install_openjdk.ps1* in Ihren Container hoch.

        ![Ordner im Ordner „Benutzerszenarios“](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. Um diese Beispiele für benutzerdefinierte Standardsetups wiederzuverwenden, kopieren Sie den Inhalt des ausgewählten Ordners in Ihren Container.

1. Wenn Sie Ihre Azure-SSIS IR über die ADF-Benutzeroberfläche bereitstellen oder neu konfigurieren, aktivieren Sie das Kontrollkästchen **Azure-SSIS Integration Runtime mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen** auf der Seite **Erweiterte Einstellungen** des Bereichs **Setup für Integration Runtime**. Als Nächstes geben Sie den SAS-URI Ihres Containers in das Textfeld **SAS-URI des Containers für benutzerdefinierte Setups** ein.
   
1. Wenn Sie Ihre Azure-SSIS IR über Azure PowerShell bereitstellen oder neu konfigurieren, halten Sie die Runtime an, falls sie bereits ausgeführt wird. Führen Sie dann das Cmdlet `Set-AzDataFactoryV2IntegrationRuntime` mit dem SAS-URI Ihres Containers als Wert für den Parameter `SetupScriptContainerSasUri` aus, und starten Sie die Azure-SSIS IR neu.

1. Nachdem Ihr benutzerdefiniertes Standardsetup abgeschlossen und Ihre Azure-SSIS IR gestartet wurde, können Sie alle benutzerdefinierten Setupprotokolle im Ordner *main.cmd.log* Ihres Containers finden. Diese umfassen die Standardausgabe von *main.cmd* und andere Ausführungsprotokolle.

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Enterprise Edition der Azure-SSIS IR](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Entwickeln von zahlungspflichtigen oder lizenzierten Komponenten für die Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md)
