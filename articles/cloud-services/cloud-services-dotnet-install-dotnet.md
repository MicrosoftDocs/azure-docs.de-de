---
title: Installieren von .NET in Rollen in Azure Cloud Services (klassisch) | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die manuelle Installation von .NET Framework in Clouddienstweb- und -workerrollen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: aa05fc9f02c26192762ed34db54b60b4760bf3bf
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99061850"
---
# <a name="install-net-on-azure-cloud-services-classic-roles"></a>Installieren von .NET in Rollen in Azure Cloud Services (klassisch)

> [!IMPORTANT]
> [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) ist ein neues auf Azure Resource Manager basierendes Bereitstellungsmodell für Azure Cloud Services. Im Zuge dieser Änderung wurden Azure Cloud Services-Instanzen, die unter dem Azure Service Manager-basierten Bereitstellungsmodell ausgeführt werden, in „Cloud Services (klassisch)“ umbenannt. Für alle neuen Bereitstellungen wird [Azure Cloud Services (erweiterter Support)](../cloud-services-extended-support/overview.md) verwendet.

Dieser Artikel beschreibt die Installation von .NET Framework-Versionen, die nicht im Funktionsumfang des Azure-Gastbetriebssystems vorhanden sind. Sie können .NET im Gastbetriebssystem zum Konfigurieren Ihrer Clouddienstweb- und -workerrollen nutzen.

Sie können .NET Framework 4.6.2 für die Gastbetriebssystemfamilie 4 installieren, die keine Version von .NET Framework 4.6 enthält. (Die Gastbetriebssystemfamilie 5 ist in .NET Framework 4.6 enthalten.) Aktuelle Informationen zu Versionen von Azure-Gastbetriebssystemversionen finden Sie unter [Neuigkeiten zur Version des Azure-Gastbetriebssystems](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>Das Azure SDK 2.9 weist eine Einschränkung für die Bereitstellung von .NET Framework 4.6 in Gastbetriebssystemfamilien bis Version 4 auf. Eine Lösung zum Beseitigen dieser Einschränkung finden Sie auf der Website mit der [Microsoft-Dokumentation](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9).

Zum Installieren von .NET für Ihre Web- und Workerrollen fügen Sie Ihrem Clouddienstprojekt den .NET-Webinstaller hinzu. Starten Sie den Installer im Rahmen der Startaufgaben der Rolle. 

## <a name="add-the-net-installer-to-your-project"></a>Das Installationsprogramm für .NET zu Ihrem Projekt hinzufügen
Laden Sie den Webinstaller der .NET Framework-Version herunter, die Sie installieren möchten:

* [Webinstaller für .NET Framework 4.8](https://go.microsoft.com/fwlink/?LinkId=2150985)
* [Webinstaller für .NET Framework 4.7.2](https://go.microsoft.com/fwlink/?LinkId=863262)
* [Webinstaller für .NET Framework 4.6.2](https://www.microsoft.com/download/details.aspx?id=53345)

So fügen Sie den Installer für eine *Webrolle* hinzu
  1. Klicken Sie im **Projektmappen-Explorer** im Clouddienstprojekt unter **Rollen** mit der rechten Maustaste auf Ihre *Webrolle*, und wählen Sie **Hinzufügen** > **Neuer Ordner** aus. Erstellen Sie einen Ordner mit dem Namen **bin**.
  2. Klicken Sie mit der rechten Maustaste auf den Ordner „bin“, und wählen Sie **Hinzufügen** > **Vorhandenes Element** aus. Wählen Sie das Installationsprogramm für .NET aus und fügen Sie es dem Ordner "Bin" hinzu.
  
So fügen Sie den Installer für eine *Workerrolle* hinzu
* Klicken Sie mit der rechten Maustaste auf Ihre *Workerrolle*, und wählen Sie **Hinzufügen** > **Vorhandenes Element** aus. Wählen Sie das Installationsprogramm für .NET aus und fügen Sie zur Rolle hinzu. 

Wenn Dateien auf diese Weise dem Inhaltsordner für die Rolle hinzugefügt werden, werden sie automatisch Ihrem Clouddienstpaket hinzugefügt. Die Dateien werden dann in einem einheitlichen Speicherort auf dem virtuellen Computer bereitgestellt. Wiederholen Sie diesen Vorgang für alle Web- und Workerrollen in Ihrem Clouddienst, sodass alle Rollen über eine Kopie des Installers verfügen.

> [!NOTE]
> Sie sollten .NET Framework 4.6.2 für Ihre Clouddienstrolle installieren, auch wenn Ihre Anwendung auf .NET Framework 4.6 ausgerichtet ist. Das Gastbetriebssystem enthält [Update 3098779](https://support.microsoft.com/kb/3098779) und [Update 3097997](https://support.microsoft.com/kb/3097997) aus der Knowledge Base. Probleme beim Ausführen Ihrer .NET-Anwendungen können auftreten, wenn .NET Framework 4.6 über die Knowledge Base-Updates installiert wird. Installieren Sie .NET Framework 4.6.2 statt Version 4.6, um diese Probleme zu vermeiden. Weitere Informationen finden Sie in den Knowledge Base-Artikeln [3118750](https://support.microsoft.com/kb/3118750) und [4340191](https://support.microsoft.com/kb/4340191).
> 
> 

![Inhalte der Rolle mit Installerdateien][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definieren von Startaufgaben für Ihre Rollen
Mit Startaufgaben können Sie Vorgänge ausführen, bevor eine Rolle gestartet wird. Die Installation von .NET Framework als Teil der Startaufgabe stellt sicher, dass das Framework installiert ist, bevor Anwendungscode ausgeführt wird. Weitere Informationen zu Startaufgaben finden Sie unter [Ausführen von Startaufgaben in Azure](cloud-services-startup-tasks.md). 

1. Fügen Sie der Datei „ServiceDefinition.csdef“ unter dem Knoten **WebRole** oder **WorkerRole** für alle Rollen folgenden Inhalt hinzu:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    Die oben genannte Konfiguration führt den Konsolenbefehl `install.cmd` mit Administratorrechten aus, um .NET Framework zu installieren. Über die Konfiguration wird auch ein **LocalStorage**-Element mit dem Namen **NETFXInstall** erstellt. Das Startskript aktiviert den temporären Ordner zur Verwendung der lokalen Speicherressource. 
    
    > [!IMPORTANT]
    > Um die ordnungsgemäße Installation des Frameworks sicherzustellen, legen Sie die Größe dieser Ressource auf mindestens 1.024 MB fest.
    
    Weitere Informationen zu Startaufgaben finden Sie unter [Allgemeine Startaufgaben für Azure Cloud Services](cloud-services-startup-tasks-common.md).

2. Erstellen Sie eine Datei mit dem Namen **install.cmd**, und fügen Sie ihr das folgende Installationsskript hinzu.

   Das Skript überprüft, ob die angegebene .NET-Framework-Version bereits auf dem Computer installiert ist, indem die Registrierung abgefragt wird. Wenn die .NET Framework-Version nicht installiert ist, wird der .NET Framework-Webinstaller geöffnet. Zur Unterstützung der Problembehandlung protokolliert das Skript alle Aktivitäten in einer Datei mit dem Namen „startuptasklog-(aktuelles Datum mit Uhrzeit).txt“, die unter **InstallLogs** lokal gespeichert ist.
   
   > [!IMPORTANT]
   > Erstellen Sie die Datei „install.cmd“ in einem einfachen Texteditor wie Windows-Editor. Wenn Sie eine Textdatei mit Visual Studio erstellen und die Erweiterung in „.cmd“ ändern, kann die Datei immer noch eine UTF-8-Bytereihenfolge-Marke enthalten. Diese Marke kann einen Fehler verursachen, wenn die erste Zeile des Skripts ausgeführt wird. Um diesen Fehler zu vermeiden, versehen Sie die erste Zeile des Skripts mit einer REM-Anweisung, die von der Bytereihenfolgenverarbeitung übersprungen werden kann. 
   > 
   >
   
   ```cmd
   REM Set the value of netfx to install appropriate .NET Framework. 
   REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
   REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
   REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" ***** https://go.microsoft.com/fwlink/?LinkId=671729
   REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" ***** https://www.microsoft.com/download/details.aspx?id=53345
   REM ***** To install .NET 4.7 set the variable netfx to "NDP47" ***** 
   REM ***** To install .NET 4.7.1 set the variable netfx to "NDP471" ***** https://go.microsoft.com/fwlink/?LinkId=852095
   REM ***** To install .NET 4.7.2 set the variable netfx to "NDP472" ***** https://go.microsoft.com/fwlink/?LinkId=863262
   set netfx="NDP472"
   REM ***** To install .NET 4.8 set the variable netfx to "NDP48" ***** https://dotnet.microsoft.com/download/thank-you/net48
      
   REM ***** Set script start timestamp *****
   set timehour=%time:~0,2%
   set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
   set "log=install.cmd started %timestamp%."
   
   REM ***** Exit script if running in Emulator *****
   if "%ComputeEmulatorRunning%"=="true" goto exit
   
   REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
   set TMP=%PathToNETFXInstall%
   set TEMP=%PathToNETFXInstall%
   
   REM ***** Setup .NET filenames and registry keys *****
   if %netfx%=="NDP472" goto NDP472
   if %netfx%=="NDP471" goto NDP471
   if %netfx%=="NDP47" goto NDP47
   if %netfx%=="NDP462" goto NDP462
   if %netfx%=="NDP461" goto NDP461
   if %netfx%=="NDP46" goto NDP46
   
   set "netfxinstallfile=NDP452-KB2901954-Web.exe"
   set netfxregkey="0x5cbf5"
   goto logtimestamp
   
   :NDP46
   set "netfxinstallfile=NDP46-KB3045560-Web.exe"
   set netfxregkey="0x6004f"
   goto logtimestamp
   
   :NDP461
   set "netfxinstallfile=NDP461-KB3102438-Web.exe"
   set netfxregkey="0x6040e"
   goto logtimestamp
   
   :NDP462
   set "netfxinstallfile=NDP462-KB3151802-Web.exe"
   set netfxregkey="0x60632"
   goto logtimestamp
   
   :NDP47
   set "netfxinstallfile=NDP47-KB3186500-Web.exe"
   set netfxregkey="0x707FE"
   goto logtimestamp
   
   :NDP471
   set "netfxinstallfile=NDP471-KB4033344-Web.exe"
   set netfxregkey="0x709fc"
   goto logtimestamp
   
   :NDP472
   set "netfxinstallfile=NDP472-KB4054531-Web.exe"
   set netfxregkey="0x70BF6"
   goto logtimestamp
   
   :logtimestamp
   REM ***** Setup LogFile with timestamp *****
   md "%PathToNETFXInstall%\log"
   set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
   set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
   echo %log% >> %startuptasklog%
   echo Logfile generated at: %startuptasklog% >> %startuptasklog%
   echo TMP set to: %TMP% >> %startuptasklog%
   echo TEMP set to: %TEMP% >> %startuptasklog%
   
   REM ***** Check if .NET is installed *****
   echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
   set /A netfxregkeydecimal=%netfxregkey%
   set foundkey=0
   FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
   echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
   if %foundkey% GEQ %netfxregkeydecimal% goto installed
   
   REM ***** Installing .NET *****
   echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
   start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
   if %ERRORLEVEL%== 0 goto installed
       echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
       if %ERRORLEVEL%== 3010 goto restart
       if %ERRORLEVEL%== 1641 goto restart
       echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
       goto exit
       
   :restart
   echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
   shutdown.exe /r /t 5 /c "Installed .NET framework" /f /d p:2:4
   
   :installed
   echo .NET (%netfx%) is installed >> %startuptasklog%
   
   :end
   echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
   
   :exit
   EXIT /B 0
   ```

3. Fügen Sie jeder Rolle die Datei „install.cmd“ durch Aufrufen von **Hinzufügen** > **Vorhandenes Element** im **Projektmappen-Explorer** hinzu (siehe die Beschreibung weiter oben). 

    Nach diesem Schritt sollten alle Rollen über die .NET-Installerdateien und die Datei „install.cmd“ verfügen.

   ![Inhalte der Rolle mit allen Dateien][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Konfigurieren der Diagnose zur Übertragung der Startaufgabenprotokolle in Blobspeicher
Zur Vereinfachung der Problembehandlung können Sie die Azure-Diagnose so konfigurieren, dass Protokolldateien, die vom Startskript oder .NET-Installer erstellt wurden, in Azure Blob Storage übertragen werden. Bei Wahl dieser Vorgehensweise können Sie die Protokolle anzeigen, indem Sie die Protokolldateien aus dem Blobspeicher herunterladen, anstatt sich per Remotedesktop bei der Rolle anzumelden.


Öffnen Sie zur Konfiguration der Diagnose die Datei „diagnostics.wadcfgx“, und fügen Sie unter dem Knoten **Directories** folgenden Inhalt hinzu: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Dieser XML-Code dient zum Konfigurieren der Diagnose zur Übertragung der Dateien im Protokollverzeichnis in der Ressource **NETFXInstall** in das Diagnosespeicherkonto des Blobcontainers **netfx-install**.

## <a name="deploy-your-cloud-service"></a>Bereitstellen des Clouddiensts
Wenn Sie Ihren Clouddienst bereitstellen, installieren die Startaufgaben .NET Framework, sofern es nicht bereits installiert ist. Ihre Clouddienstrollen haben, während das Framework installiert wird, den Status *Ausgelastet*. Wenn die Installation des Frameworks einen Neustart erfordert, werden möglicherweise die Dienstrollen auch neu gestartet. 

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Installation des .NET Frameworks][Installing the .NET Framework]
* [Ermitteln der installierten .NET Framework-Versionen][How to: Determine Which .NET Framework Versions Are Installed]
* [Beheben von Problemen bei .NET Framework-Installationen][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png



