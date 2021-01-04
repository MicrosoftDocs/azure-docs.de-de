---
title: Ausführen eines Skripts, wenn ein Azure Service Fabric-Dienst startet
description: Informationen zum Konfigurieren einer Richtlinie für einen Setupeinstiegspunkt für einen Service Fabric-Dienst und Ausführen eines Skripts beim Dienststart.
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012526"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>Ausführen eines Dienststartskripts als lokales Benutzerkonto oder Systemkonto
Bevor eine ausführbare Service Fabric-Dienstdatei startet, können Konfigurations- oder Setupeinstellungen erforderlich sein.  Beispiel: Konfiguration von Umgebungsvariablen. Sie können angeben, dass ein Skript ausgeführt wird, bevor die ausführbare Dienstdatei im Dienstmanifest für den Dienst startet. Durch Konfigurieren einer RunAs-Richtlinie für den Setupeinstiegspunkt des Diensts können Sie das Konto ändern, unter dem die Setupdatei ausgeführt wird.  Mit einem separaten Setupeinstiegspunkt können Sie für einen kurzen Zeitraum eine Konfiguration mit hohen Berechtigungen ausführen, damit die ausführbare Diensthostdatei nicht für längere Zeiträume mit hohen Berechtigungen ausgeführt werden muss.

Der Setupeinstiegspunkt (**SetupEntryPoint** im [Dienstmanifest](service-fabric-application-and-service-manifests.md)) ist ein privilegierter Einstiegspunkt, der standardmäßig mit den gleichen Anmeldeinformationen wie Service Fabric (meist mit dem Konto *NetworkService*) vor jedem anderen Einstiegspunkt ausgeführt wird. Die durch **EntryPoint** angegebene ausführbare Datei ist üblicherweise der Diensthost mit langer Ausführungsdauer. Die von **EntryPoint** angegebene ausführbare Datei wird ausgeführt, nachdem **SetupEntryPoint** erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (er beginnt wieder mit **SetupEntryPoint**), sofern er beendet wird oder abstürzt. 

## <a name="configure-the-service-setup-entry-point"></a>Konfigurieren des Setupeinstiegspunkts für Dienste
Im Folgenden finden Sie ein einfaches Dienstmanifestbeispiel für einen zustandslosen Dienst, in dem ein Setupskript *MySetup.bat* im Dienst-**SetupEntryPoint** angegeben wird.  Mit **Arguments** werden dem Skript während der Ausführung Argumente übergeben.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>Konfigurieren der Richtlinie für einen Setupeinstiegspunkt für Dienste
Standardmäßig wird die ausführbare Setupeinstiegspunkt-Datei des Diensts unter den gleichen Anmeldeinformationen wie Service Fabric ausgeführt (in der Regel das *NetworkService*-Konto).  Im Anwendungsmanifest können Sie die Sicherheitsberechtigungen zur Ausführung des Startskripts unter einem lokalen Systemkonto oder einem Administratorkonto ändern.

### <a name="configure-the-policy-by-using-a-local-system-account"></a>Konfigurieren der Richtlinie mithilfe eines lokalen Systemkontos
Das folgende Anwendungsmanifestbeispiel zeigt das Konfigurieren des Dienst-Setupeinstiegspunkts für die Ausführung unter dem Administratorkonto des Benutzers (SetupAdminUser).

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

Erstellen Sie zuerst den Abschnitt **Principals** mit einem Benutzernamen, z.B. „SetupAdminUser“. Das SetupAdminUser-Benutzerkonto ist ein Mitglied der Systemgruppe „Administratoren“.

Konfigurieren Sie als Nächstes im Abschnitt **ServiceManifestImport** eine Richtlinie, gemäß der dieser Prinzipal auf **SetupEntryPoint** angewendet werden kann. Diese Richtlinie teilt Service Fabric mit, dass die Datei **MySetup.bat** als SetupAdminUser (mit Administratorrechten) ausgeführt werden soll. Da Sie *keine* Richtlinie auf den primären Einstiegspunkt angewendet haben, wird der Code in **MyServiceHost.exe** unter dem Systemkonto **NetworkService** ausgeführt. Dies ist das Standardkonto, unter dem alle Diensteinstiegspunkte ausgeführt werden.

### <a name="configure-the-policy-by-using-local-system-accounts"></a>Konfigurieren der Richtlinie mithilfe eines lokalen Systemkontos
Häufig ist es von Vorteil, das Startskript mithilfe eines lokalen Systemkontos statt eines Administratorkontos auszuführen. Die Ausführung der RunAs-Richtlinie als Mitglied der Administratorgruppe funktioniert in der Regel nicht gut, da die Benutzerkontensteuerung (User Access Control, UAC) auf Computern standardmäßig aktiviert ist. In solchen Fällen wird empfohlen, den SetupEntryPoint als LocalSystem auszuführen, anstatt als lokaler Benutzer, der der Administratorgruppe hinzugefügt wurde. Das folgende Beispiel zeigt, wie SetupEntryPoint zur Ausführung als LocalSystem festgelegt wird:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Für Linux-Cluster können Sie den **AccountType** als **LocalSystem** bestimmen, um einen Dienst oder einen Setupeinstiegspunkt als **Stamm** auszuführen.

## <a name="run-a-script-from-the-setup-entry-point"></a>Ausführen eines Skripts vom Setupeinstiegspunkt
Fügen Sie dem Projekt jetzt ein Startskript hinzu, das mit Administratorberechtigungen ausgeführt wird. 

Klicken Sie in Visual Studio mit der rechten Maustaste auf das Dienstprojekt, und fügen Sie eine neue Datei mit dem Namen *MySetup.bat* hinzu.

Stellen Sie anschließend sicher, dass die Datei *MySetup.bat* im Dienstpaket enthalten ist. Standardmäßig ist dies nicht der Fall. Wählen Sie die Datei aus, klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Eigenschaften**. Stellen Sie im Dialogfeld „Eigenschaften“ sicher, dass **In Ausgabeverzeichnis kopieren** auf **Kopieren, wenn neuer** festgelegt ist. Der folgende Screenshot zeigt dies.

![Visual Studio – Batchdatei für „CopyToOutput“ für „SetupEntryPoint“][image1]

Bearbeiten Sie jetzt die Datei *MySetup.bat*, fügen Sie der folgenden Befehlsgruppe eine Systemumgebungsvariable hinzu, und geben Sie eine Textdatei aus:

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

Als Nächstes müssen Sie die Projektmappe erstellen und in einem lokalen Entwicklungscluster bereitstellen. Nachdem der Dienst wie im [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) angezeigt gestartet wurde, können Sie erkennen, dass die Datei „MySetup.bat“ auf zwei Arten erfolgreich war. Öffnen Sie eine PowerShell-Eingabeaufforderung, und geben Sie Folgendes ein:

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

Notieren Sie anschließend den Namen des Knotens, auf dem der Dienst bereitgestellt und im [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) gestartet wurde. Beispiel: Knoten 2. Navigieren Sie als Nächstes zum Arbeitsordner der Anwendungsinstanz, um die Datei „out.txt“ zu ermitteln, in der der Wert von **TestVariable** angezeigt wird. Wenn dieser Dienst z.B. auf Knoten 2 bereitgestellt wurde, können Sie für **MyApplicationType** auf diesen Pfad zugreifen:

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>Ausführen von PowerShell-Befehlen über einen Setupeinstiegspunkt
Zum Ausführen von PowerShell über den Punkt **SetupEntryPoint** können Sie **PowerShell.exe** in einer Batchdatei ausführen, die auf eine PowerShell-Datei verweist. Fügen Sie zuerst dem Dienstprojekt eine PowerShell-Datei hinzu, z.B. **MySetup.ps1**. Denken Sie daran, die Eigenschaft *Kopieren, wenn neuer* so festzulegen, dass die Datei in das Dienstpaket einbezogen wird. Das folgende Beispiel zeigt eine Beispielbatchdatei zum Starten einer PowerShell-Datei namens „MySetup.ps1“, mit der die Systemumgebungsvariable **TestVariable** festgelegt wird.

„MySetup.bat“ zum Starten der PowerShell-Datei:

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

Fügen Sie in der PowerShell-Datei Folgendes ein, um eine Systemumgebungsvariable festzulegen:

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> Standardmäßig sucht die Batchdatei bei der Ausführung in dem Anwendungsordner **work** nach Dateien. In diesem Fall soll „MySetup.bat“ bei der Ausführung die Datei „MySetup.ps1“ im gleichen Ordner suchen, also im Anwendungsordner **CodePackage**. Um diesen Ordner zu ändern, legen Sie den Arbeitsordner fest:
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>Lokales Debuggen eines Startskripts mit Konsolenumleitung
Gelegentlich ist es hilfreich, zum Debuggen die Konsolenausgabe der Ausführung eines Setupskripts auszuwerten. Sie können eine Richtlinie für die Konsolenumleitung am Setupeinstiegspunkt im Dienstmanifest festlegen, die die Ausgabe in eine Datei schreibt. Die Dateiausgabe wird in den Anwendungsordner **log** auf dem Clusterknoten geschrieben, auf dem die Anwendung bereitgestellt und ausgeführt wird. 

> [!WARNING]
> Verwenden Sie die Richtlinie zur Konsolenumleitung nie in einer Anwendung, die in der Produktionsumgebung bereitgestellt wurde, da sich dies auf das Anwendungsfailover auswirken kann. Verwenden Sie diese *nur* für die lokale Entwicklung und das Debuggen.  
> 
> 

Das folgende Dienstmanifestbeispiel zeigt das Festlegen der Konsolenumleitung mit einem FileRetentionCount-Wert:

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

Wenn Sie nun die Datei „MySetup.ps1“ so ändern, dass ein **Echo**-Befehl geschrieben wird, wird zu Debugzwecken in die Ausgabedatei geschrieben:

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> Nachdem Sie Ihr Skript gedebuggt haben, entfernen Sie diese Richtlinie zur Konsolenumleitung sofort.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte
* [Erfahren Sie mehr über Anwendungs- und Dienstsicherheit](service-fabric-application-and-service-security.md)
* [Informationen zum Anwendungsmodell](service-fabric-application-model.md)
* [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
