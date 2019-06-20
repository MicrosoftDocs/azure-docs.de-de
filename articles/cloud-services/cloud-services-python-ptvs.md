---
title: Erste Schritte mit Python und Azure Cloud Services | Microsoft-Dokumentation
description: Übersicht über die Verwendung von Python-Tools für Visual Studio für die Erstellung von Azure-Cloud-Diensten, einschließlich Webrollen und Workerrollen.
services: cloud-services
documentationcenter: python
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 5489405d-6fa9-4b11-a161-609103cbdc18
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 2cfb8f922819802834d9833ae614f5bc5b4ff886
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125439"
---
# <a name="python-web-and-worker-roles-with-python-tools-for-visual-studio"></a>Python-Web- und -Workerrollen mit Python-Tools für Visual Studio

Dieser Artikel enthält eine Übersicht über die Verwendung von Python-Web- und -Workerrollen mit [Python-Tools für Visual Studio][Python Tools for Visual Studio]. Sie erfahren, wie Sie mit Visual Studio einen einfachen Clouddienst, für den Python verwendet wird, erstellen und bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen
* [Visual Studio 2013, 2015 oder 2017](https://www.visualstudio.com/)
* [Python-Tools für Visual Studio][Python Tools for Visual Studio] (PTVS)
* [Azure SDK-Tools für VS 2013][Azure SDK Tools for VS 2013] oder  
[Azure SDK-Tools für VS 2015][Azure SDK Tools for VS 2015] oder  
[Azure SDK-Tools für VS 2017][Azure SDK Tools for VS 2017]
* [Python 2.7 (32 Bit)][Python 2.7 32-bit] oder [Python 3.5 (32 Bit)][Python 3.5 32-bit]

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-are-python-web-and-worker-roles"></a>Was sind Python-Web- und Workerrollen?
Azure stellt drei Computemodelle bereit, die Sie zum Ausführen von Anwendungen verwenden können: [Web-Apps-Feature in Azure App Service][execution model-web sites], [Azure Virtual Machines][execution model-vms] und [Azure Cloud Services][execution model-cloud services]. Alle drei Modelle unterstützen Python. Cloud Services, die auch Web- und Workerrollen umfassen, ermöglichen *Platform as a Service (PaaS)* . In einem Clouddienst bietet eine Webrolle einen speziellen IIS-Webserver (Internet Information Services), um Front-End-Webanwendungen zu hosten, während eine Workerrolle asynchrone, langfristige oder fortwährende Aufgaben ausführen kann, die unabhängig von einer Benutzerinteraktion oder -eingabe sind.

Weitere Informationen finden Sie unter [Was ist ein Clouddienst?].

> [!NOTE]
> *Möchten Sie eine einfache Website erstellen?*
> Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die Verwendung des einfachen Web-Apps-Features von Azure App Service in Betracht ziehen. Sie können einen Cloud-Dienst leicht upgraden, wenn die Website größer wird und sich Ihre Anforderungen ändern. Im [Python Developer Center](https://azure.microsoft.com/develop/python/) finden Sie Artikel zur Entwicklung des Web-Apps-Features von Azure App Service.
> <br />
> 
> 

## <a name="project-creation"></a>Projekterstellung
In Visual Studio können Sie im Dialogfeld **Neues Projekt** unter **Python** die Option **Azure Cloud Service** auswählen.

![Dialogfeld "Neues Projekt"](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

Im Assistenten für Azure-Clouddienste können Sie neue Web- und Workerrollen erstellen.

![Dialogfeld "Azure Cloud Service"](./media/cloud-services-python-ptvs/new-service-wizard.png)

Die Workerrollenvorlage enthält Codebausteine für eine Verbindung mit einem Azure-Speicherkonto oder Azure Service Bus.

![Cloud-Dienstlösung](./media/cloud-services-python-ptvs/worker.png)

Sie können Web- oder Workerrollen jederzeit zu einem vorhandenen Cloud-Dienst hinzufügen.  Sie können vorhandene Projekte zu Ihrer Lösung hinzufügen oder neue erstellen.

![Befehl "Rolle hinzufügen"](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Ihr Cloud-Dienst kann Rollen enthalten, die in verschiedenen Sprachen implementiert wurden.  Sie können beispielsweise eine Python-Webrolle mithilfe von Django in Python- oder C#-Workerrollen implementieren.  Sie können mit Service Bus-Warteschlangen oder Speicherwarteschlangen einfach zwischen Ihren Rollen kommunizieren.

## <a name="install-python-on-the-cloud-service"></a>Installieren von Python im Clouddienst
> [!WARNING]
> Die mit Visual Studio installierten Setupskripts funktionieren nicht (zum Zeitpunkt der letzten Aktualisierung dieses Artikels). In diesem Abschnitt wird eine Problemumgehung beschrieben.
> 
> 

Das Hauptproblem der Setupskripts ist, dass Python nicht installiert wird. Definieren Sie zuerst zwei [Startaufgaben](cloud-services-startup-tasks.md) in der Datei [ServiceDefinition.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef). Mit der ersten Aufgabe (**PrepPython.ps1**) wird die Python-Laufzeit heruntergeladen und installiert. Mit der zweiten Aufgabe (**PipInstaller.ps1**) wird PIP ausgeführt, um alle ggf. vorhandenen Abhängigkeiten zu installieren.

Die folgenden Skripts wurden für Python 3.5 geschrieben. Falls Sie Version 2.x von Python verwenden möchten, können Sie die Variable **PYTHON2** für die beiden Startaufgaben und die Laufzeitaufgabe auf **on** festlegen: `<Variable name="PYTHON2" value="<mark>on</mark>" />`.

```xml
<Startup>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>
  </Task>

  <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
    <Environment>
      <Variable name="EMULATED">
        <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
      </Variable>
      <Variable name="PYTHON2" value="off" />
    </Environment>

  </Task>

</Startup>
```

Die Variablen **PYTHON2** und **PYPATH** müssen der Workerstartaufgabe hinzugefügt werden. Die Variable **PYPATH** wird nur verwendet, wenn die Variable **PYTHON2** auf **on** festgelegt ist.

```xml
<Runtime>
  <Environment>
    <Variable name="EMULATED">
      <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
    </Variable>
    <Variable name="PYTHON2" value="off" />
    <Variable name="PYPATH" value="%SystemDrive%\Python27" />
  </Environment>
  <EntryPoint>
    <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
  </EntryPoint>
</Runtime>
```

#### <a name="sample-servicedefinitioncsdef"></a>Beispieldatei „ServiceDefinition.csdef“
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicePython" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
      <Setting name="Python2" />
    </ConfigurationSettings>
    <Startup>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PrepPython.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
      <Task executionContext="elevated" taskType="simple" commandLine="bin\ps.cmd PipInstaller.ps1">
        <Environment>
          <Variable name="EMULATED">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
          <Variable name="PYTHON2" value="off" />
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="EMULATED">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
        </Variable>
        <Variable name="PYTHON2" value="off" />
        <Variable name="PYPATH" value="%SystemDrive%\Python27" />
      </Environment>
      <EntryPoint>
        <ProgramEntryPoint commandLine="bin\ps.cmd LaunchWorker.ps1" setReadyOnProcessStart="true" />
      </EntryPoint>
    </Runtime>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
  </WorkerRole>
</ServiceDefinition>
```



Erstellen Sie als Nächstes die Dateien **PrepPython.ps1** und **PipInstaller.ps1** im Ordner **./bin** Ihrer Rolle.

#### <a name="preppythonps1"></a>PrepPython.ps1
Mit diesem Skript wird Python installiert. Wenn die Umgebungsvariable **PYTHON2** auf **on** festgelegt ist, wird Python 2.7 installiert. Andernfalls wird Python 3.5 installiert.

```powershell
[Net.ServicePointManager]::SecurityProtocol = "tls12, tls11, tls"
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if python is installed...$nl"
    if ($is_python2) {
        & "${env:SystemDrive}\Python27\python.exe"  -V | Out-Null
    }
    else {
        py -V | Out-Null
    }

    if (-not $?) {

        $url = "https://www.python.org/ftp/python/3.5.2/python-3.5.2-amd64.exe"
        $outFile = "${env:TEMP}\python-3.5.2-amd64.exe"

        if ($is_python2) {
            $url = "https://www.python.org/ftp/python/2.7.12/python-2.7.12.amd64.msi"
            $outFile = "${env:TEMP}\python-2.7.12.amd64.msi"
        }

        Write-Output "Not found, downloading $url to $outFile$nl"
        Invoke-WebRequest $url -OutFile $outFile
        Write-Output "Installing$nl"

        if ($is_python2) {
            Start-Process msiexec.exe -ArgumentList "/q", "/i", "$outFile", "ALLUSERS=1" -Wait
        }
        else {
            Start-Process "$outFile" -ArgumentList "/quiet", "InstallAllUsers=1" -Wait
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Already installed"
    }
}
```

#### <a name="pipinstallerps1"></a>PipInstaller.ps1
Mit diesem Skript wird PIP aufgerufen, und alle Abhängigkeiten in der Datei **requirements.txt** werden installiert. Wenn die Umgebungsvariable **PYTHON2** auf **on** festgelegt ist, wird Python 2.7 verwendet. Andernfalls wird Python 3.5 verwendet.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated){
    Write-Output "Checking if requirements.txt exists$nl"
    if (Test-Path ..\requirements.txt) {
        Write-Output "Found. Processing pip$nl"

        if ($is_python2) {
            & "${env:SystemDrive}\Python27\python.exe" -m pip install -r ..\requirements.txt
        }
        else {
            py -m pip install -r ..\requirements.txt
        }

        Write-Output "Done$nl"
    }
    else {
        Write-Output "Not found$nl"
    }
}
```

#### <a name="modify-launchworkerps1"></a>Ändern von „LaunchWorker.ps1“
> [!NOTE]
> Im Fall eines **Workerrollenprojekts** ist zum Ausführen der Startdatei die Datei **LauncherWorker.ps1** erforderlich. In einem **Webrollenprojekt** wird stattdessen die Startdatei in den Projekteigenschaften festgelegt.
> 
> 

**bin\LaunchWorker.ps1** wurde ursprünglich erstellt, um eine Reihe von Vorbereitungsschritten auszuführen, aber dies funktioniert nicht wie gewünscht. Ersetzen Sie den Inhalt dieser Datei durch das folgende Skript.

Mit dem Skript wird die Datei **worker.py** aus Ihrem Python-Projekt aufgerufen. Wenn die Umgebungsvariable **PYTHON2** auf **on** festgelegt ist, wird Python 2.7 verwendet. Andernfalls wird Python 3.5 verwendet.

```powershell
$is_emulated = $env:EMULATED -eq "true"
$is_python2 = $env:PYTHON2 -eq "on"
$nl = [Environment]::NewLine

if (-not $is_emulated)
{
    Write-Output "Running worker.py$nl"

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
else
{
    Write-Output "Running (EMULATED) worker.py$nl"

    # Customize to your local dev environment

    if ($is_python2) {
        cd..
        iex "$env:PYPATH\python.exe worker.py"
    }
    else {
        cd..
        iex "py worker.py"
    }
}
```

#### <a name="pscmd"></a>ps.cmd
Von den Visual Studio-Vorlagen sollte im Ordner **./bin** die Datei **ps.cmd** erstellt worden sein. Mit diesem Shellskript werden die obigen PowerShell-Wrapperskripts verwendet, und die Anmeldung ist basierend auf dem Namen des aufgerufenen PowerShell-Wrappers möglich. Hier ist der Inhalt der Datei angegeben, falls sie nicht erstellt wurde. 

```bat
@echo off

cd /D %~dp0

if not exist "%DiagnosticStore%\LogFiles" mkdir "%DiagnosticStore%\LogFiles"
%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Unrestricted -File %* >> "%DiagnosticStore%\LogFiles\%~n1.txt" 2>> "%DiagnosticStore%\LogFiles\%~n1.err.txt"
```



## <a name="run-locally"></a>Lokales Ausführen
Wenn Sie Ihr Clouddienstprojekt als Startprojekt festlegen und F5 drücken, wird der Clouddienst im lokalen Azure-Emulator ausgeführt.

Auch wenn PTVS den Start im Emulator unterstützt, funktioniert das Debuggen (etwa Haltepunkte) nicht.

Um Web- und Workerrollen zu debuggen, können Sie das Rollenprojekt als Startprojekt festlegen und dieses stattdessen debuggen.  Sie können auch mehrere Startprojekte festlegen.  Klicken Sie mit der rechten Maustaste auf die Lösung, und wählen Sie **Startprojekte festlegen** aus.

![Startprojekteigenschaften der Lösung](./media/cloud-services-python-ptvs/startup.png)

## <a name="publish-to-azure"></a>Veröffentlichen in Azure
Klicken Sie für die Veröffentlichung mit der rechten Maustaste auf das Clouddienstprojekt in der Lösung, und wählen Sie **Veröffentlichen** aus.

![Anmeldung für Microsoft Azure-Veröffentlichung](./media/cloud-services-python-ptvs/publish-sign-in.png)

Führen Sie die Schritte des Assistenten aus. Aktivieren Sie den Remotedesktop, wenn dies erforderlich ist. Der Remotedesktop ist hilfreich für das Debuggen.

Klicken Sie auf **Veröffentlichen**, wenn Sie mit den Konfigurationseinstellungen fertig sind.

Der Fortschritt wird im Ausgabefenster angezeigt, anschließend wird das Fenster mit dem Microsoft Azure-Aktivitätsprotokoll angezeigt.

![Fenster mit Microsoft Azure-Aktivitätsprotokoll](./media/cloud-services-python-ptvs/publish-activity-log.png)

Der Abschluss der Bereitstellung dauert einige Minuten, anschließend werden Ihre Web- und/oder Workerrollen in Azure ausgeführt.

### <a name="investigate-logs"></a>Untersuchen von Protokollen
Nachdem der virtuelle Computer des Clouddiensts gestartet und Python installiert wurde, können Sie in den Protokollen nach Fehlermeldungen suchen. Diese Protokolle befinden sich im Ordner **C:\Resources\Directory\\{role}\LogFiles**. **PrepPython.err.txt** enthält mindestens einen Fehler, da vom Skript überprüft wird, ob Python installiert ist, und **PipInstaller.err.txt** meldet unter Umständen eine veraltete Version von PIP.

## <a name="next-steps"></a>Nächste Schritte
Genauere Informationen zur Arbeit mit Web- und Workerrollen in Python-Tools für Visual Studio finden Sie in der PTVS-Dokumentation:

* [Cloud Service-Projekte][Cloud Service Projects]

Weitere Details zur Verwendung von Azure-Diensten aus Ihren Web- und Workerrollen, etwa zur Verwendung von Azure Storage oder Azure Service Bus, finden Sie in folgenden Artikeln:

* [Blob-Dienst][Blob Service]
* [Tabellenspeicherdienst][Table Service]
* [Warteschlangendienst][Queue Service]
* [Service Bus-Warteschlangen][Service Bus Queues]
* [Service Bus-Themen][Service Bus Topics]

<!--Link references-->

[Was ist ein Clouddienst?]: cloud-services-choose-me.md
[execution model-web sites]: ../app-service/overview.md
[execution model-vms]:../virtual-machines/windows/overview.md
[execution model-cloud services]: cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob Service]:../storage/blobs/storage-python-how-to-use-blob-storage.md
[Queue Service]: ../storage/queues/storage-python-how-to-use-queue-storage.md
[Table Service]:../cosmos-db/table-storage-how-to-use-python.md
[Service Bus Queues]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md
[Service Bus Topics]: ../service-bus-messaging/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python Tools for Visual Studio]: https://aka.ms/ptvs
[Python Tools for Visual Studio Documentation]: https://aka.ms/ptvsdocs
[Cloud Service Projects]: https://docs.microsoft.com/visualstudio/python/python-azure-cloud-service-project-template
[Azure SDK Tools for VS 2013]: https://go.microsoft.com/fwlink/?LinkId=746482
[Azure SDK Tools for VS 2015]: https://go.microsoft.com/fwlink/?LinkId=746481
[Azure SDK Tools for VS 2017]: https://go.microsoft.com/fwlink/?LinkId=746483
[Python 2.7 32-bit]: https://www.python.org/downloads/
[Python 3.5 32-bit]: https://www.python.org/downloads/
