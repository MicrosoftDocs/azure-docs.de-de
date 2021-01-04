---
title: Verwalten von StorSimple-Geräten mit Azure Automation-Runbooks
description: Hier erfahren Sie, wie Sie Azure Automation-Runbooks zum Verwalten Ihrer StorSimple-Geräte der Serie 8000 im Azure-Portal verwenden.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: b5f2a86fb7c776436ea707ef293a66c033014c33
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021077"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Verwalten von StorSimple-Geräten mit Azure Automation-Runbooks

In diesem Artikel wird beschrieben, wie Geräte der StorSimple 8000-Serie mit Azure Automation Runbooks im Azure-Portal verwaltet werden können. Ein Beispiel für ein Runbook wird bereitgestellt, um Ihnen die Schritte zum Konfigurieren Ihrer Umgebung für die Ausführung dieses Runbooks zu zeigen.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurieren, Hinzufügen und Ausführen von Azure-Runbooks

In diesem Abschnitt wird ein Windows PowerShell-Beispielskript für StorSimple verwendet, um die verschiedenen Schritte zu erläutern, die zum Importieren des Skripts in ein Runbook und dem anschließenden Veröffentlichen und Ausführen des Runbooks erforderlich sind.

### <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen stellen Sie sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Abonnement mit zugeordnetem StorSimple-Geräte-Manager-Dienst, der mit einem Gerät der StorSimple 8000-Serie registriert wurde.

* Windows PowerShell 5.0, das auf Ihrem Computer installiert ist (oder Ihr Windows Server-Host für Ihr StorSimple, sofern Sie einen verwenden).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Erstellen eines Automation-Runbookmoduls in Windows PowerShell

Führen Sie die folgenden Schritte aus, um ein Automation-Modul für die Geräteverwaltung der StorSimple 8000-Serie zu erstellen:

1. Starten Sie Windows PowerShell. Erstellen Sie einen neuen Ordner, und ändern Sie das Verzeichnis in den neuen Ordner.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Laden Sie die NuGet-CLI in den Ordner herunter](https://www.nuget.org/downloads), den Sie im vorherigen Schritt erstellt haben. Es gibt verschiedene Versionen von _nuget.exe_. Wählen Sie die für Ihr SDK entsprechende Version aus. Jeder Downloadlink verweist direkt auf eine _EXE_-Datei. Führen Sie die Datei nicht über den Browser aus, sondern klicken Sie mit der rechten Maustaste auf die Datei, und speichern Sie sie auf Ihrem Computer.

    Sie können auch den folgenden Befehl ausführen, um das Skript in den zuvor erstellten Ordner herunterzuladen und zu speichern.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Laden Sie das abhängige SDK herunter.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Laden Sie das Skript aus dem GitHub-Beispielprojekt herunter.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Erstellen Sie ein Azure Automation-Runbookmodul für die Geräteverwaltung der StorSimple 8000er Serie. Führen Sie die folgenden Befehle im Windows PowerShell-Fenster aus:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Stellen Sie sicher, dass eine ZIP-Datei für das Automation-Modul in `C:\scripts\StorSimpleSDKTools` erstellt wird.

    ![Screenshot: Ordner „StorSimpleSDKTools“ mit der ZIP-Datei für das Automation-Modul](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Die folgende Ausgabe wird angezeigt, wenn ein Automation-Modul über Windows PowerShell erstellt wird.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importieren, Veröffentlichen und Ausführen von Automation-Runbooks

1. Erstellen Sie ein ausführendes Azure Automation-Konto im Azure-Portal. Wechseln Sie zu diesem Zweck zu **Azure Marketplace > Alles**, und suchen Sie dann nach **Automation**. Wählen Sie die Option **Automation-Konten** aus.

    ![search-automation](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. Auf dem Blatt **Automation-Konto hinzufügen**:

   1. Geben Sie den **Namen** für Ihr Automation-Konto an.
   2. Wählen Sie das **Abonnement** aus, das mit Ihrem StorSimple-Geräte-Manager-Dienst verknüpft ist.
   3. Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   4. Wählen Sie einen **Speicherort** aus (nach Möglichkeit denselben, in dem auch Ihr Dienst ausgeführt wird).
   5. Lassen Sie die Standardoption **Ausführendes Konto erstellen** aktiviert.
   6. Aktivieren Sie optional die Option **An Dashboard anheften**. Klicken Sie auf **Erstellen**.

       ![Screenshot: Bereich „Automation-Konto hinzufügen“ mit Werten für „Abonnement“, „Ausführendes Azure-Konto erstellen“ und „Erstellen“ (2)](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Sie werden benachrichtigt, nachdem das Automation-Konto erfolgreich erstellt wurde. Weitere Informationen zum Erstellen eines Automation-Kontos finden Sie unter [Erstellen eines ausführenden Kontos](../automation/manage-runas-account.md).

3. Um sicherzustellen, dass das erstellte Automation-Konto auf den StorSimple-Geräte-Manager-Dienst zugreifen kann, müssen Sie dem Automation-Konto entsprechende Berechtigungen zuzuweisen. Wechseln Sie in Ihrem StorSimple-Geräte-Manager-Dienst zu **Access Control**. Klicken Sie auf **+ Hinzufügen**, und geben Sie den Namen Ihres Azure Automation-Kontos an. **Speichern** Sie die Einstellungen.

    ![add-permissions-automation-account](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. Wechseln Sie im neu erstellten Konto zu **Freigegebene Ressourcen > Module**, und klicken Sie auf **+ Modul hinzufügen**.

5. Navigieren Sie auf dem Blatt **Modul hinzufügen** zum Speicherort des gezippten Moduls. Wählen Sie das Modul aus, um es dann zu öffnen. Klicken Sie auf **OK**.

    ![Screenshot: Bereich „Modul hinzufügen“ mit einem Wert für „Datei hochladen“ und Schaltfläche „OK“](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Wechseln Sie zu **Prozessautomatisierung > Runbooks, und klicken Sie auf + Runbook hinzufügen**. Klicken Sie auf dem Blatt **Runbook hinzufügen** auf die Option **Vorhandenes Runbook importieren**. Zeigen Sie auf die Windows PowerShell-Skriptdatei für die **Runbookdatei**. Das Runbooktyp wird automatisch ausgewählt. Geben Sie einen Namen und eine optionale Beschreibung für das Runbook an. Klicken Sie auf **Erstellen**.

    ![add-module](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Das Runbook wird zur Liste der Runbooks hinzugefügt. Wählen Sie dieses Runbook aus, und klicken Sie dann darauf.

    ![click-new-runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Bearbeiten Sie das Runbook, und klicken Sie auf **Testbereich**. Stellen Sie die Parameter wie den Namen Ihres StorSimple-Geräte-Manager-Diensts, den Namen des StorSimple-Geräts und das Abonnement bereit. **Starten** Sie den Test. Der Bericht wird nach Abschluss der Ausführung generiert. Weitere Informationen finden Sie unter [Testen eines Runbooks](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Screenshot: Bereich „Testen“ zum Eingeben von Parameterwerten und Starten eines Tests](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Untersuchen Sie die Ausgabe des Runbooks im Testbereich. Wenn Sie mit dem Ergebnis zufrieden sind, schließen Sie den Bereich. Klicken Sie auf **Veröffentlichen**, und wenn Sie zur Bestätigung aufgefordert werden, bestätigen und veröffentlichen Sie das Runbook.

    ![Screenshot: Bereich „PowerShell-Runbook bearbeiten“ mit der Aufforderung zum Veröffentlichen des Runbooks](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Nächste Schritte

[Verwalten Sie Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).