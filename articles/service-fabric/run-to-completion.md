---
title: RunToCompletion-Semantik in Service Fabric
description: Beschreibt die RunToCompletion-Semantik in Service Fabric.
author: shsha-msft
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: shsha
ms.openlocfilehash: 6f2f6aa4380fcf6909957118bf682275350ce68c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000265"
---
# <a name="runtocompletion"></a>RunToCompletion

Ab Version 7.1 unterstützt Service Fabric **RunToCompletion**-Semantik für [Container-][containers-introduction-link] und [ausführbare Gastanwendungen][guest-executables-introduction-link]. Diese Semantik ermöglicht Anwendungen und Dienste, die einen Task abschließen und beendet werden, im Gegensatz zu Anwendungen und Diensten, die immer ausgeführt werden.

Bevor Sie mit diesem Artikel fortfahren, sollten Sie sich mit dem [Service Fabric-Anwendungsmodell][application-model-link] und dem [Service Fabric-Hostingmodell][hosting-model-link] vertraut machen.

> [!NOTE]
> RunToCompletion-Semantik wird derzeit nicht für Dienste unterstützt, die mit dem [Reliable Services][reliable-services-link]-Programmiermodell geschrieben wurden.
 
## <a name="runtocompletion-semantics-and-specification"></a>RunToCompletion-Semantik und -Spezifikation
RunToCompletion-Semantik kann als **ExecutionPolicy** angegeben werden, wenn das [ServiceManifest importiert wird][application-and-service-manifests-link]. Die angegebene Richtlinie wird von allen CodePackages geerbt, die das ServiceManifest enthalten. Der folgende Ausschnitt aus der Datei „ApplicationManifest.xml“ enthält ein Beispiel.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="RunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
  <Policies>
    <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
  </Policies>
</ServiceManifestImport>
```
**ExecutionPolicy** lässt die folgenden zwei Attribute zu:
* **Typ:** **RunToCompletion** ist derzeit der einzige zulässige Wert für dieses Attribut.
* **Neustart:** Dieses Attribut gibt die Neustartrichtlinie an, die bei einem Fehler auf CodePackages angewendet wird, die das ServicePackage enthalten. Ein CodePackage, das mit einem **Exitcode ungleich NULL beendet wird**, wird als fehlgeschlagen betrachtet. Zulässige Werte für dieses Attribut sind **OnFailure** und **Never**. **OnFailure** ist dabei die Standardeinstellung.

Wenn die Neustartrichtlinie auf **OnFailure** festgelegt ist und ein CodePackage fehlschlägt **(Exitcode ungleich NULL)** , wird es mit Sicherungen zwischen wiederholten Fehlern neu gestartet. Wenn die Richtlinie für Neustart auf **Never** festgelegt ist, wird der Bereitstellungsstatus von DeployedServicePackage als **fehlgeschlagen** gekennzeichnet, wenn ein CodePackage fehlschlägt, aber andere CodePackages können die Ausführung fortsetzen. Wenn alle CodePackages, die das ServicePackage enthalten, bis zum erfolgreichen Abschluss **(Exitcode 0)** ausgeführt werden, wird der Bereitstellungsstatus von DeployedServicePackage als **RanToCompletion** gekennzeichnet. 

## <a name="complete-example-using-runtocompletion-semantics"></a>Vollständiges Beispiel mit RunToCompletion-Semantik

Sehen wir uns ein vollständiges Beispiel mit RunToCompletion-Semantik an.

> [!IMPORTANT]
> Im folgenden Beispiel wird davon ausgegangen, dass Sie mit der Erstellung von [Windows-Containeranwendungen mit Service Fabric und Docker][containers-getting-started-link] vertraut sind.
>
> In diesem Beispiel wird auf mcr.microsoft.com/windows/nanoserver:1809 verwiesen. Windows Server-Container sind nicht mit allen Versionen eines Hostbetriebssystems kompatibel. Weitere Informationen finden Sie unter [Versionskompatibilität von Windows-Containern](/virtualization/windowscontainers/deploy-containers/version-compatibility).

In der folgenden Datei „ServiceManifest.xml“ wird ein ServicePackage beschrieben, das aus zwei CodePackages besteht, die Container darstellen. *RunToCompletionCodePackage1* protokolliert lediglich eine Meldung in **stdout** und wird dann beendet. *RunToCompletionCodePackage2* pingt die Loopbackadresse eine Zeit lang und wird dann mit einem Exitcode von **0**, **1** oder **2** beendet.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ServiceManifest Name="WindowsRunToCompletionServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>Windows RunToCompletion Service</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="WindowsRunToCompletionServiceType"  UseImplicitHost="true"/>
  </ServiceTypes>
  <CodePackage Name="RunToCompletionCodePackage1" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/c,echo Hi from RunToCompletionCodePackage1 &amp;&amp; exit 0</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>

  <CodePackage Name="RunToCompletionCodePackage2" Version="1.0">
    <EntryPoint>
      <ContainerHost>
        <ImageName>mcr.microsoft.com/windows/nanoserver:1809</ImageName>
        <Commands>/v,/c,ping 127.0.0.1 &amp;&amp; set /a exitCode=%random% % 3 &amp;&amp; exit !exitCode!</Commands>
        <EntryPoint>cmd</EntryPoint>
      </ContainerHost>
    </EntryPoint>
  </CodePackage>
</ServiceManifest>
```

In der folgenden Datei „ApplicationManifest.xml“ wird eine Anwendung auf der Grundlage der oben erläuterten Datei „ServiceManifest.xml“ beschrieben. Sie gibt **RunToCompletion** **ExecutionPolicy** für *WindowsRunToCompletionServicePackage* mit einer Neustartrichtlinie **OnFailure** an. Bei Aktivierung von *WindowsRunToCompletionServicePackage* werden die zugehörigen CodePackages gestartet. *RunToCompletionCodePackage1* sollten bei der ersten Aktivierung erfolgreich beendet werden. *RunToCompletionCodePackage2* kann jedoch fehlschlagen **(Exitcode ungleich NULL)** . In diesem Fall wird es neu gestartet, da die Neustartrichtlinie **OnFailure** ist.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WindowsRunToCompletionApplicationType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <Description>Windows RunToCompletion Application</Description>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="WindowsRunToCompletionServicePackage" ServiceManifestVersion="1.0"/>
    <Policies>
      <ExecutionPolicy Type="RunToCompletion" Restart="OnFailure"/>
    </Policies>
  </ServiceManifestImport>

  <DefaultServices>
    <Service Name="WindowsRunToCompletionService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="WindowsRunToCompletionServiceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="querying-deployment-status-of-a-deployedservicepackage"></a>Abfragen des Bereitstellungsstatus eines DeployedServicePackage
Der Bereitstellungsstatus eines DeployedServicePackage kann mithilfe von PowerShell mit [Get-ServiceFabricDeployedServicePackage][deployed-service-package-link] oder aus C# mit der [FabricClient][fabric-client-link]-API [GetDeployedServicePackageListAsync(String, Uri, String)][deployed-service-package-fabricclient-link] abgerufen werden.

## <a name="considerations-when-using-runtocompletion-semantics"></a>Überlegungen bei der Verwendung von RunToCompletion-Semantik

Die folgenden Punkte sollten für die aktuelle Unterstützung von RunToCompletion beachtet werden.
* Diese Semantik wird nur für [Container-][containers-introduction-link] und [ausführbare Gastanwendungen][guest-executables-introduction-link] unterstützt.
* Upgradeszenarien für Anwendungen mit RunToCompletion-Semantik sind unzulässig. Benutzer sollten solche Anwendungen ggf. löschen und neu erstellen.
* Failoverereignisse können bewirken, dass CodePackages nach erfolgreichem Abschluss auf demselben Knoten oder auf anderen Knoten des Clusters erneut ausgeführt werden. Beispiele für Failoverereignisse sind Knotenneustarts und Service Fabric-Runtimeupgrades auf einem Knoten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln.

* [Service Fabric und Container][containers-introduction-link]
* [Service Fabric und ausführbare Gastdateien][guest-executables-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[containers-getting-started-link]: service-fabric-get-started-containers.md
[guest-executables-introduction-link]: service-fabric-guest-executables-introduction.md
[reliable-services-link]: service-fabric-reliable-services-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md
[application-and-service-manifests-link]: service-fabric-application-and-service-manifests.md
[setup-entry-point-link]: service-fabric-run-script-at-service-startup.md
[deployed-service-package-working-with-link]: service-fabric-hosting-model.md#work-with-a-deployed-service-package
[deployed-code-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedcodepackage
[deployed-service-package-link]: /powershell/module/servicefabric/get-servicefabricdeployedservicepackage
[fabric-client-link]: /dotnet/api/system.fabric.fabricclient
[deployed-service-package-fabricclient-link]: /dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync
