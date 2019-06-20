---
title: Häufige Starttasks für Clouddienste | Microsoft-Dokumentation
description: Enthält einige Beispiele für häufige Starttasks, die Sie vielleicht in der Web- oder Workerrolle des Clouddiensts ausführen möchten.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 1d78ab917589af0eae72eb70e3cdc2cc751072eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076439"
---
# <a name="common-cloud-service-startup-tasks"></a>Allgemeine Starttasks für Clouddienste
Dieser Artikel enthält einige Beispiele für häufiger ausgeführte Starttasks, die Sie vielleicht im Clouddienst ausführen möchten. Mit Starttasks können Sie Vorgänge ausführen, bevor eine Rolle gestartet wird. Zu den Vorgängen, die Sie vielleicht ausführen möchten, gehören das Installieren von Komponenten, das Registrieren von COM-Komponenten, das Festlegen von Registrierungsschlüsseln und das Starten eines lang andauernden Prozesses. 

In [diesem Artikel](cloud-services-startup-tasks.md) erfahren Sie etwas über die Funktionsweise von Starttasks und insbesondere darüber, wie die Einträge zum Definieren einer Starttask erstellt werden.

> [!NOTE]
> Starttasks gelten nicht für virtuelle Computer, sondern nur für Web- und Workerrollen von Clouddiensten.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definieren von Umgebungsvariablen vor dem Starten einer Rolle
Wenn Sie Umgebungsvariablen für einen bestimmten Task benötigen, können Sie das [Umgebung]-Element innerhalb des [Aufgabe]-Elements verwenden.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Variablen können auch einen [gültigen Azure XPath-Wert](cloud-services-role-config-xpath.md) verwenden, um auf etwas für die Bereitstellung zu verweisen. Anstatt das `value` -Attribut zu verwenden, definieren Sie ein untergeordnetes [RoleInstanceValue] -Element.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Konfigurieren des IIS-Startvorgangs mit "AppCmd.exe"
Das Befehlszeilentool [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) kann zum Verwalten von IIS-Einstellungen beim Start von Azure verwendet werden. *AppCmd.exe* bietet einen praktischen Zugriff über die Befehlszeile auf Konfigurationseinstellungen in den Azure-Starttasks. Mit *AppCmd.exe*können Einstellungen von Websites für Anwendungen und Websites hinzugefügt, geändert oder entfernt werden.

Es gibt jedoch einige Dinge, die bei der Verwendung von *AppCmd.exe* als Starttask zu beachten sind:

* Starttasks können zwischen Neustarts mehrmals ausgeführt werden. Beispielsweise beim Neustart einer Rolle.
* Wenn eine *AppCmd.exe*-Aktion mehr als einmal ausgeführt wird, generiert sie möglicherweise einen Fehler. Beispielsweise könnte beim Versuch, *Web.config* zweimal einen Abschnitt hinzuzufügen, ein Fehler generiert werden.
* Starttasks schlagen fehl, wenn sie einen von Null abweichenden Exitcode oder **errorlevel** zurückgeben. Dies kann z.B. passieren, wenn *AppCmd.exe* einen Fehler erzeugt.

Es empfiehlt sich, nach dem Aufruf von *AppCmd.exe* den **errorlevel** zu prüfen, was einfach ist, wenn Sie den Aufruf von *AppCmd.exe* in eine *CMD*-Datei einbetten. Wenn Sie eine bekannte **errorlevel** -Antwort erkennen, können Sie diese ignorieren oder zurückgeben.

Die von *AppCmd.exe* zurückgegebenen errorlevel-Werte sind in der Datei „Winerror.h“ aufgeführt und können auch auf [MSDN](/windows/desktop/Debug/system-error-codes--0-499-)nachgelesen werden.

### <a name="example-of-managing-the-error-level"></a>Beispiel für die Verwaltung von Fehlerstufen
In diesem Beispiel wird ein Komprimierungsabschnitt und ein Komprimierungseintrag für JSON mit Fehlerbehandlung und-Protokollierung in der Datei *Web.config* hinzugefügt.

Die relevanten Abschnitte der Datei [ServiceDefinition.csdef] werden hier gezeigt, insbesondere das Festlegen des [executionContext](/previous-versions/azure/reference/gg557552(v=azure.100)#task)-Attributs auf `elevated`, um *AppCmd.exe* ausreichende Berechtigungen zum Ändern der Einstellungen in der Datei *Web.config* zu gewähren:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Die Batchdatei *Startup.cmd* verwendet *AppCmd.exe*, um in der Datei *Web.config* einen Komprimierungsabschnitt und einen Komprimierungseintrag für JSON hinzuzufügen. Der erwartete **errorlevel**-Wert 183 wird mithilfe des Befehlszeilenprogramms VERIFY.EXE auf Null (0) festgelegt. Unerwartete Errorlevel werden in "StartupErrorLog.txt" protokolliert.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in an Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Hinzufügen von Firewallregeln
In Azure sind tatsächlich zwei Firewalls vorhanden. Die erste Firewall kontrolliert Verbindungen zwischen dem virtuellen Computer und der Außenwelt. Diese Firewall wird durch das [EndPoints]-Element in der Datei [ServiceDefinition.csdef] gesteuert.

Die zweite Firewall kontrolliert Verbindungen zwischen einem virtuellen Computer und den Prozessen innerhalb dieses virtuellen Computers. Diese Firewall kann durch das Befehlszeilentool `netsh advfirewall firewall` gesteuert werden.

Azure erstellt Firewallregeln für die in den Rollen gestarteten Prozesse. Wenn Sie beispielsweise einen Dienst oder ein Programm starten, erstellt Azure automatisch die erforderlichen Firewallregeln, die diesem Dienst das Kommunizieren mit dem Internet ermöglichen. Wenn Sie einen Dienst erstellen, der durch einen Prozess außerhalb Ihrer Rolle gestartet wird (z.B. einen COM+-Dienst oder einen geplanten Windows-Task), müssen Sie manuell eine Firewallregel für den Zugriff auf diesen Dienst erstellen. Diese Firewallregeln können unter Verwendung einer Starttask erstellt werden.

Ein Starttask, der eine Firewallregel erstellt, muss einen [executionContext][aufgabe] mit der Einstellung **elevated** zurückgeben. Fügen Sie folgenden Starttask der Datei [ServiceDefinition.csdef] hinzu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Um die Firewallregel hinzuzufügen, müssen Sie die entsprechenden `netsh advfirewall firewall` -Befehle in der Startbatchdatei verwenden. In diesem Beispiel erfordert der Starttask Sicherheit und Verschlüsselung für TCP-Port 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Blockieren bestimmter IP-Adressen
Sie können den Zugriff einer Azure-Webrolle auf einen Satz angegebener IP-Adressen beschränken, indem Sie Ihre **web.config**-IIS-Datei ändern. Sie müssen auch eine Befehlsdatei verwenden, die den Abschnitt **ipSecurity** der Datei **ApplicationHost.config** entsperrt.

Um den Abschnitt **ipSecurity** der Datei **ApplicationHost.config** zu entsperren, erstellen Sie eine Befehlsdatei, die beim Starten der Rolle ausgeführt wird. Erstellen Sie einen Ordner mit dem Namen **startup** auf der Stammebene der Webrolle, und erstellen Sie in diesem Ordner die Batchdatei **startup.cmd**. Fügen Sie diese Rolle zu Ihrem Visual Studio-Projekt hinzu, und legen Sie die Eigenschaften auf **Immer kopieren** fest, um sicherzustellen, dass sie in Ihrem Paket enthalten ist.

Fügen Sie folgenden Starttask der Datei [ServiceDefinition.csdef] hinzu.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Fügen Sie diesen Befehl in der Datei **startup.cmd** hinzu:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Dadurch wird die Batchdatei **startup.cmd** bei jeder Initialisierung der Webrolle ausgeführt, sodass der erforderliche **ipSecurity**-Abschnitt entsperrt wird.

Ändern Sie abschließend den Abschnitt [system.webServer](https://www.iis.net/configreference/system.webserver/security/ipsecurity#005) in der Datei **web.config** Ihrer Webrolle, um eine Liste der IP-Adressen hinzuzufügen, denen Zugriff gewährt wird, wie im folgenden Beispiel gezeigt:

Diese Beispielkonfiguration **erlaubt** allen IP-Adressen Zugriff auf den Server, mit Ausnahme der beiden definierten.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Diese Beispielkonfiguration **verweigert** allen IP-Adressen den Zugriff auf den Server, mit Ausnahme der beiden definierten.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Erstellen eines PowerShell-Starttasks
Windows PowerShell-Skripts können nicht direkt aus der Datei [ServiceDefinition.csdef] aufgerufen werden, aber sie können aus einer Startbatchdatei aus aufgerufen werden.

PowerShell führt standardmäßig keine nicht signierten Skripts aus. Wenn Sie die Skripts nicht signieren, müssen Sie PowerShell zum Ausführen von nicht signierten Skripts konfigurieren. Zum Ausführen von nicht signierten Skripts muss die **ExecutionPolicy** auf **Uneingeschränkt** festgelegt werden. Die verwendete **ExecutionPolicy** -Einstellung basiert auf der Version von Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Wenn Sie ein Gastbetriebssystem mit PowerShell 2.0 oder 1.0 verwenden, können Sie die Ausführung von Version 2 erzwingen und bei Nichtverfügbarkeit Version 1 verwenden.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Erstellen von Dateien im lokalen Speicher über einen Starttask
Sie können eine lokale Speicherressource zum Speichern von Dateien verwenden, die durch den Starttask erstellt werden und auf die später von der Anwendung zugegriffen wird.

Zum Erstellen der lokalen Speicherressource fügen Sie in der Datei [ServiceDefinition.csdef] einen [LocalResources]-Abschnitt und dann das untergeordnete [LocalStorage]-Element hinzu. Geben Sie der lokalen Speicherressource einen eindeutigen Namen und eine für den Starttask angemessene Größe.

Um eine lokale Speicherressource im Starttask zu verwenden, müssen Sie eine Umgebungsvariable erstellen, um auf den Speicherort der lokalen Ressource zu verweisen. Dann können der Starttask und die Anwendung Schreib- und Lesevorgänge in Dateien in der lokalen Speicherressource ausführen.

Die relevanten Abschnitte in der Datei **ServiceDefinition.csdef** werden hier gezeigt:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Beispielsweise verwendet diese **Startup.cmd**-Batchdatei die Umgebungsvariable **PathToStartupStorage** zum Erstellen der Datei **MyTest.txt** am lokalen Speicherort.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Sie können mithilfe der [GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100))-Methode aus dem Azure SDK auf den lokalen Speicherordner zugreifen.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Ausführen im Emulator oder in der Cloud
Sie können mit dem Starttask andere Schritte ausführen, wenn er in der Cloud ausgeführt wird, als wenn er im Serveremulator ausgeführt wird. Beispiel: Sie möchten eine neue Kopie der SQL-Daten nur bei Ausführung im Emulator verwenden. Oder Sie benötigen eine bestimmte Leistungsoptimierung für die Cloud, die bei Ausführung im Emulator nicht benötigt wird.

Diese Möglichkeit, unterschiedliche Aktionen im Serveremulator und in der Cloud auszuführen, kann durch Erstellen einer Umgebungsvariablen in der Datei [ServiceDefinition.csdef] im Starttask erzielt werden. Dann testen Sie diese Umgebungsvariable auf einen Wert in Ihrem Starttask.

Zum Erstellen der Umgebungsvariablen fügen Sie das [Variable]/[RoleInstanceValue]-Element hinzu und legen als XPath-Wert `/RoleEnvironment/Deployment/@emulated` fest. Der Wert der Umgebungsvariable **%ComputeEmulatorRunning%** ist bei Ausführung im Serveremulator `true` und bei Ausführung in der Cloud `false`.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

Jetzt kann der Task die Umgebungsvariable **%ComputeEmulatorRunning%** zur Ausführung unterschiedlicher Aktionen nutzen, je nachdem, ob die Rolle in der Cloud oder im Emulator ausgeführt wird. Dies ist ein CMD-Shell-Skript, das diese Umgebungsvariable überprüft.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Ermitteln, ob der Task bereits ausgeführt wurde
Die Rolle kann zyklisch ausgeführt werden, ohne einen Neustart zu bewirken, wodurch die Starttasks erneut ausgeführt werden. Es gibt kein Flag, das anzeigt, ob ein Task bereits auf dem virtuellen Computer ausgeführt wurde, der als Host fungiert. Bei einigen Tasks spielt es sicher keine Rolle, ob sie mehrere Male ausgeführt werden. Jedoch können Situationen eintreten, in denen die mehrmalige Ausführung eines Tasks verhindert werden muss.

Die einfachste Methode, um zu erkennen, dass ein Task bereits ausgeführt wurde, ist das Erstellen einer Datei im **%TEMP%** -Ordner, wenn der Task erfolgreich ist, um dann zu Beginn des Tasks zu prüfen, ob die Datei bereits angelegt wurde. Dies ist ein Beispiel CMD-Shell-Skript, das dieses Verfahren umsetzt.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%PathToApp1Install%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%PathToApp1Install%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Bewährte Methoden für Tasks
Hier sind einige bewährten Methoden, die Sie beim Konfigurieren eines Tasks für Ihre Web- oder Workerrolle befolgen sollten.

### <a name="always-log-startup-activities"></a>Protokollieren Sie stets alle Startaktivitäten
Visual Studio bietet keinen Debugger zum schrittweisen Durchlaufen von Batchdateien, daher ist es ratsam, möglichst viele Daten über die Ausführung der Batchdateien zu erhalten. Beim Protokollieren der Ausgabe von Batchdateien können sowohl **stdout** als auch **stderr** wichtige Informationen beim Debuggen und Korrigieren von Batchdateien liefern. Um sowohl **stdout** als auch **stderr** in der Datei „StartupLog.txt“ in dem Verzeichnis zu protokollieren, auf das die **%TEMP%** -Umgebungsvariable verweist, fügen Sie den Text `>>  "%TEMP%\\StartupLog.txt" 2>&1` am Ende der Zeilen ein, die Sie protokollieren möchten. So führen Sie z.B. „setup.exe“ im **%PathToApp1Install%** -Verzeichnis aus:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Zum Vereinfachen des XML-Codes können Sie eine *CMD*-Wrapperdatei erstellen, die alle Starttasks sowie die Protokollierung aufruft und sicherstellt, dass jeder untergeordnete Task die gleichen Umgebungsvariablen verwendet.

Möglicherweise finden Sie es dennoch lästig, am Ende jedes Starttasks `>> "%TEMP%\StartupLog.txt" 2>&1` zu verwenden. Sie können die Taskprotokollierung erzwingen, indem Sie einen Wrapper erstellen, der die Protokollierung für Sie verarbeitet. Dieser Wrapper ruft die echte Batchdatei auf, die Sie ausführen möchten. Jede Ausgabe der Zielbatchdatei wird an die Datei *Startuplog.txt* umgeleitet.

Das folgende Beispiel zeigt, wie die gesamte Ausgabe einer Startbatchdatei umgeleitet wird. In diesem Beispiel erstellt die Datei ServerDefinition.csdef einen Starttask, der *logwrap.cmd* aufruft. *logwrap.cmd* ruft *Startup2.cmd* auf, wodurch die gesamte Ausgabe an **%TEMP%\\StartupLog.txt** umgeleitet wird.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Beispielausgabe in der Datei **StartupLog.txt**:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> Die Datei **StartupLog.txt** befindet sich im Ordner *C:\Resources\temp\\{Rollenbezeichner}\RoleTemp*.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Legen Sie einen geeigneten ExecutionContext für die Starttasks fest
Legen Sie geeignete Berechtigungen für den Starttask fest. Manchmal müssen Starttasks mit erhöhten Rechten ausgeführt werden, selbst wenn die Rolle mit normalen Berechtigungen ausgeführt wird.

Das Befehlszeilentool [executionContext][aufgabe] -Attribut legt die Berechtigungsstufe des Starttasks fest. Der Wert `executionContext="limited"` bedeutet, dass der Starttask die gleiche Berechtigungsstufe wie die Rolle aufweist. Der Wert `executionContext="elevated"` bedeutet, dass der Starttask über Administratorrechte verfügt, wodurch der Starttask Administratoraufgaben ausführen kann, ohne dass Sie der Rolle Administratorrechte zuweisen müssen.

Ein Beispiel für einen Starttask, der erhöhte Rechte erfordert, ist ein Starttask, der **AppCmd.exe** zum Konfigurieren von IIS verwendet. **AppCmd.exe** erfordert `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Verwenden Sie einen geeigneten taskType
Das [taskType][aufgabe]-Attribut bestimmt, wie der Starttask ausgeführt wird. Es gibt drei Werte: **simple**, **background** und **foreground**. Die background- und foreground-Tasks werden asynchron gestartet. Die simple-Tasks werden dann synchron jeweils nacheinander ausgeführt.

Bei **simple**-Starttasks lässt sich die Reihenfolge, in der die Tasks abgearbeitet werden, durch die Reihenfolge der Tasks in der Datei ServiceDefinition.csdef festlegen. Wenn ein **simple**-Task mit einem von Null abweichenden Exitcode beendet wird, dann wird die Startprozedur beendet, und die Rolle wird nicht gestartet.

Der Unterschied zwischen **background**-Starttasks und **foreground**-Starttasks ist, dass bei **foreground**-Tasks die Rolle ausgeführt wird, bis der **foreground**-Task beendet wurde. Dies bedeutet auch, dass bei einem Aufhängen oder Abstürzen des **foreground**-Tasks die Rolle solange nicht erneut ausgeführt werden kann, bis der **foreground**-Task zwangsweise beendet wird. Aus diesem Grund werden für asynchrone Starttasks **background**-Tasks empfohlen, sofern nicht speziell diese Funktion des **foreground**-Tasks benötigt wird.

### <a name="end-batch-files-with-exit-b-0"></a>Beenden Sie Batchdateien mit "EXIT /B 0"
Die Rolle wird nur gestartet, wenn die **errorlevel** -Werte aller simple-Starttasks Null (0) sind. Nicht alle Programme legen den **errorlevel**-Wert (Exitcode) richtig fest, daher sollte die Batchdatei mit einem `EXIT /B 0` enden, wenn alles ordnungsgemäß ausgeführt wurde.

Ein Fehlen von `EXIT /B 0` am Ende einer Startbatchdatei ist eine häufige Ursache, wenn Rollen nicht gestartet werden.

> [!NOTE]
> Ich habe festgestellt, dass geschachtelte Batchdateien bei Verwendung des `/B`-Parameters manchmal hängenbleiben. Sie müssen sicherstellen, dass dieses Problem nicht auftritt, wenn eine andere Batchdatei die aktuelle Batchdatei aufruft, beispielsweise bei Verwendung des [Protokollwrappers](#always-log-startup-activities). In diesem Fall können Sie den `/B`-Parameter weglassen.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Gehen Sie von einer mehrfachen Ausführung der Starttasks aus
Nicht jede zyklische Ausführung einer Rolle bewirkt einen Neustart, aber bei jeder zyklischen Ausführungen einer Rolle werden alle Starttasks ausgeführt. Dies bedeutet, dass Starttasks mehrmals ohne Probleme zwischen Neustarts ausgeführt werden müssen. Dies wurde im [vorigen Abschnitt](#detect-that-your-task-has-already-run) erläutert.

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Verwenden Sie lokalen Speicher für Dateien, auf die von der Rolle aus zugegriffen werden soll
Wenn während des Starttasks Dateien kopiert oder erstellt werden sollen, auf die dann von Ihrer Rolle aus zugegriffen werden muss, müssen diese Dateien im lokalen Speicher abgelegt werden. Siehe den [vorigen Abschnitt](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie [Clouddienstmodell und -paket](cloud-services-model-and-package.md)

Erfahren Sie mehr über die Funktionsweise von [Tasks](cloud-services-startup-tasks.md) .

[Erstellen und Bereitstellen](cloud-services-how-to-create-deploy-portal.md) Ihres Clouddienstpakets durch.

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Aufgabe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Umgebung]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[EndPoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
