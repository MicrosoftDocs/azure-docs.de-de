---
title: Ausführen von Startaufgaben in Azure Cloud Services | Microsoft Docs
description: Startaufgaben helfen dabei, die Clouddienstumgebung für die App vorbereiten. Erfahren Sie, wie Startaufgaben funktionieren und erstellt werden.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: f2417389de98f9998c189e7cbbbcdae77fbb8840
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020703"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst
Mit Startaufgaben können Sie Vorgänge ausführen, bevor eine Rolle gestartet wird. Zu den Vorgängen, die Sie vielleicht ausführen möchten, gehören das Installieren von Komponenten, das Registrieren von COM-Komponenten, das Festlegen von Registrierungsschlüsseln und das Starten eines lang andauernden Prozesses.

> [!NOTE]
> Startaufgaben gelten nicht für virtuelle Computer, sondern nur für Web- und Workerrollen von Clouddiensten.
> 
> 

## <a name="how-startup-tasks-work"></a>Funktionsweise von Startaufgaben
Startaufgaben sind Aktionen, die ausgeführt werden, bevor die Rollen beginnen. Sie werden in der Datei [ServiceDefinition.csdef] mithilfe des [Aufgabe]-Elements im[Startup]-Element definiert. Häufig sind Startaufgaben Batchdateien, aber es kann sich auch um Konsolenanwendungen handeln, oder um Batchdateien, die PowerShell-Skripts starten.

Startaufgaben erhalten ihre Informationen aus Umgebungsvariablen, und mit lokalem Speicher können Informationen aus einer Startaufgabe heraus übergeben werden. Eine Umgebungsvariable kann beispielsweise den Pfad zu einem Programm festlegen, das Sie installieren möchten, und Dateien können in den lokalen Speicher geschrieben werden, aus dem sie später von den Rollen gelesen werden können.

Die Startaufgabe kann Informationen und Fehler im angegebenen Verzeichnis protokollieren, das in der **TEMP** -Umgebungsvariable festgelegt ist. Während der Startaufgabe entspricht die **TEMP**-Umgebungsvariable dem Verzeichnis *C:\\Resources\\temp\\[guid].[Rollenname]\\RoleTemp*, wenn die Ausführung in der Cloud erfolgt.

Startaufgaben können auch mehrmals zwischen Neustarts ausgeführt werden. Beispielsweise wird die Startaufgabe bei jeder zyklischen Ausführung der Rolle ausgeführt, und die zyklische Rollenausführung ist nicht immer mit einem Neustart verbunden. Startaufgaben sollten so geschrieben werden, die sie ohne Probleme mehrmals ausgeführt werden können.

Startaufgaben müssen mit dem **errorlevel** (oder Exitcode) Null (0) enden, damit der Startprozess abgeschlossen wird. Wenn eine Startaufgabe mit einem von Null abweichenden **errorlevel** beendet wird, wird die Rolle nicht gestartet.

## <a name="role-startup-order"></a>Reihenfolge beim Starten einer Rolle
Im Folgenden werden die Schritte beim Starten einer Rolle in Azure aufgeführt:

1. Die Instanz wird mit **Starting** markiert und empfängt keinen Datenverkehr.
2. Alle Startaufgaben werden gemäß ihres **taskType** -Attributs ausgeführt.
   
   * Die mit **simple** gekennzeichneten Aufgaben werden synchron, nacheinander ausgeführt.
   * Die **background**- und **foreground**-Aufgaben werden asynchron, parallel zur Startaufgabe gestartet.  
     
     > [!WARNING]
     > IIS ist während der Startaufgabephase des Startprozesses möglicherweise nicht vollständig konfiguriert, sodass rollenspezifische Daten möglicherweise nicht verfügbar sind. Startaufgaben, die rollenspezifische Daten erfordern, sollten [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100))verwenden.
     > 
     > 
3. Der Rollenhostprozess wird gestartet, und die Website in IIS erstellt.
4. Die [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) -Methode wird aufgerufen.
5. Die Instanz wird als **Ready** markiert, und Datenverkehr wird an die Instanz weitergeleitet.
6. Die [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](/previous-versions/azure/reference/ee772746(v=azure.100)) -Methode wird aufgerufen.

## <a name="example-of-a-startup-task"></a>Beispiel für eine Startaufgabe
Startaufgaben werden in der Datei [ServiceDefinition.csdef] im **Task** -Element definiert. Das **commandLine**-Attribut gibt den Namen und die Parameter der Batchdatei oder des Konsolenbefehls für den Startvorgang an, das **executionContext**-Attribut gibt die Berechtigungsstufe der Startaufgabe an, und das **taskType**-Attribut gibt an, wie die Aufgabe ausgeführt wird.

In diesem Beispiel wird die Umgebungsvariable **MyVersionNumber** für die Startaufgabe erstellt und auf den Wert „**1.0.0.0**“ festgelegt.

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Im folgenden Beispiel schreibt die Batchdatei **Startup.cmd** die Zeile "The current version is 1.0.0.0" in die Datei "StartupLog.txt" im von der TEMP-Umgebungsvariablen angegebenen Verzeichnis. Die Zeile `EXIT /B 0` sorgt dafür, dass die Startaufgabe mit dem **errorlevel** Null (0) beendet wird.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> In Visual Studio sollte die Eigenschaft **In Ausgabeverzeichnis kopieren** für die Startbatchdatei auf **Immer kopieren** festgelegt werden, damit die Startbatchdatei ordnungsgemäß in Azure für das Projekt bereitgestellt wird (**approot\\bin** für Webrollen und **approot** für Workerrollen).
> 
> 

## <a name="description-of-task-attributes"></a>Beschreibung der Aufgabenattribute
Im Folgenden werden die Attribute des **Task** -Elements in der Datei [ServiceDefinition.csdef] beschrieben:

**commandLine** – Legt die Befehlszeile für die Startaufgabe fest:

* Der Befehl, mit optionalen Befehlszeilenparametern, der die Startaufgabe beginnt.
* Dies ist häufig der Dateiname einer CMD- oder BAT-Batchdatei.
* Die Aufgabe ist relativ zum Ordner „AppRoot\\Bin“ für die Bereitstellung. Umgebungsvariablen werden bei der Bestimmung von Pfad und Dateinamen der Aufgabe nicht erweitert. Falls die Erweiterung der Umgebung erforderlich ist, können Sie ein kleines CMD-Skript erstellen, das die Startaufgabe aufruft.
* Dies kann eine Konsolenanwendung oder eine Batchdatei sein, die ein [PowerShell-Skript](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)startet.

**executionContext** – Legt die Berechtigungsstufe für die Startaufgabe fest. Die Berechtigungsstufe kann eingeschränkt oder mit erhöhten Rechten sein:

* **limited**  
  – Die Startaufgabe wird mit den gleichen Berechtigungen wie die Rolle ausgeführt. Wenn das **executionContext**-Attribut für das [Laufzeit]-Element ebenfalls **eingeschränkt** ist, werden Benutzerberechtigungen verwendet.
* **elevated**  
  Die Startaufgabe wird mit Administratorrechten ausgeführt. Dadurch können Startaufgaben Programme installieren, IIS-Konfigurationsänderungen oder Registrierungsänderungen durchführen, ohne die Berechtigungsstufe der Rolle selbst zu erhöhen.  

> [!NOTE]
> Die Berechtigungsstufe einer Startaufgabe muss nicht mit derjenigen der Rolle selbst identisch sein.
> 
> 

**taskType** – Legt fest, wie eine Startaufgabe ausgeführt wird.

* **simple**  
  – Die Aufgaben werden synchron und nacheinander in der Reihenfolge ausgeführt, in der sie in der Datei [ServiceDefinition.csdef] aufgeführt werden. Wenn eine **simple**-Startaufgabe mit **errorlevel** Null (0) beendet wird, wird die nächste **simple**-Startaufgabe ausgeführt. Wenn keine weiteren **simple** -Startaufgaben ausgeführt werden sollen, wird die Rolle selbst gestartet.   
  
  > [!NOTE]
  > Wenn die **simple**-Aufgabe mit einem von Null abweichenden **errorlevel** beendet wird, wird die Instanz blockiert. Nachfolgende **simple** -Startaufgaben sowie die Rolle selbst werden nicht gestartet.
  > 
  > 
  
    Um sicherzustellen, dass die Batchdatei mit **errorlevel** Null (0) beendet wird, führen Sie den Befehl `EXIT /B 0` am Ende des Batchdateiprozesses aus.
* **background**  
  -Aufgaben werden asynchron, parallel zum Start der Rolle ausgeführt.
* **foreground**  
  -Aufgaben werden asynchron, parallel zum Start der Rolle ausgeführt. Der Hauptunterschied zwischen einer **foreground**- und einer **background**-Aufgabe ist, dass eine **foreground**-Aufgabe verhindert, dass die Rolle zyklisch erneut gestartet oder heruntergefahren wird, bis die Aufgabe beendet wurde. Die **background** -Aufgaben haben diese Einschränkung nicht.

## <a name="environment-variables"></a>Umgebungsvariablen
Umgebungsvariablen sind eine Möglichkeit zur Datenübergabe an eine Startaufgabe. Beispielsweise können Sie dort den Pfad auf ein Blob mit einem zu installierenden Programm, die von der Rolle zu verwendenden Portnummern oder Einstellungen zur Steuerung der Startaufgabe festlegen.

Es gibt zwei Arten von Umgebungsvariablen für Startaufgaben: statische Umgebungsvariablen und Umgebungsvariablen basierend auf Mitgliedern der [RoleEnvironment] -Klasse. Beide befinden sich im [Umgebung]-Abschnitt der Datei [ServiceDefinition.csdef], und beide verwenden das [Variable]-Element und das **name**-Attribut.

Statische Umgebungsvariablen verwenden das **value** -Attribut des [Variable] -Elements. Im obigen Beispiel wird die Umgebungsvariable **MyVersionNumber** erstellt, die den statischen Wert „**1.0.0.0**“ erhält. Ein weiteres Beispiel wäre das Erstellen einer **StagingOrProduction**-Umgebungsvariable, die Sie manuell auf die Werte „**staging**“ oder „**production**“ setzen können, um anhand des Werts der **StagingOrProduction**-Umgebungsvariablen verschiedene Startvorgänge auszuführen.

Umgebungsvariablen, die auf Mitgliedern der RoleEnvironment-Klasse basieren, verwenden nicht das **value** -Attribut des [Variable] -Elements. Stattdessen wird das untergeordnete [RoleInstanceValue]-Element mit dem entsprechenden **xPath**-Attributwert verwendet, um eine Umgebungsvariable basierend auf einem bestimmten Mitglied der [RoleEnvironment]-Klasse zu erstellen. Werte für das **XPath**-Attribut zum Zugriff auf verschiedene [RoleEnvironment]-Werte finden Sie [hier](cloud-services-role-config-xpath.md).

Um beispielsweise eine Umgebungsvariable zu erstellen, die den Wert „**true**“ hat, wenn die Instanz im Serveremulator ausgeführt wird, und „**false**“, wenn sie in der Cloud ausgeführt wird, verwenden Sie die folgenden [Variable]- und [RoleInstanceValue]-Elemente:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie einige [allgemeine Startaufgaben](cloud-services-startup-tasks-common.md) mit dem Clouddienst ausführen können.

[Paket](cloud-services-model-and-package.md) des Clouddiensts erstellen.  

[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Aufgabe]: /previous-versions/azure/reference/gg557552(v=azure.100)#Task
[Startup]: /previous-versions/azure/reference/gg557552(v=azure.100)#Startup
[Laufzeit]: /previous-versions/azure/reference/gg557552(v=azure.100)#Runtime
[Umgebung]: /previous-versions/azure/reference/gg557552(v=azure.100)#Environment
[Variable]: /previous-versions/azure/reference/gg557552(v=azure.100)#Variable
[RoleInstanceValue]: /previous-versions/azure/reference/gg557552(v=azure.100)#RoleInstanceValue
[RoleEnvironment]: /previous-versions/azure/reference/ee773173(v=azure.100)