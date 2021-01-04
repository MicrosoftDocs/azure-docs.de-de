---
title: Konvertieren von Azure Cloud Services-Apps in Service Fabric
description: In dieser Anleitung werden Cloud Services-Web- und -Workerrollen mit zustandslosen Service Fabric-Diensten verglichen, um die Migration von Cloud Services zu Service Fabric zu unterstützen.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: cf593f793aabf2a0650684ed8d02fe02d756ec2b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575736"
---
# <a name="guide-to-converting-web-and-worker-roles-to-service-fabric-stateless-services"></a>Anleitung zur Konvertierung von Web- und Workerrollen in zustandslose Service Fabric-Dienste
In diesem Artikel wird beschrieben, wie Sie Ihre Cloud Services-Web- und -Workerrollen zu zustandslosen Service Fabric-Diensten migrieren. Dies ist für Anwendungen, deren allgemeine Architektur weitgehend gleich bleibt, der einfachste Migrationspfad von Cloud Services zu Service Fabric.

## <a name="cloud-service-project-to-service-fabric-application-project"></a>Vom Cloud Services-Projekt zum Service Fabric-Anwendungsprojekt
 Ein Cloud Services-Projekt und ein Service Fabric-Anwendungsprojekt haben eine ähnliche Struktur, und beide stellen die Bereitstellungseinheit für Ihre Anwendung dar. Sie dienen also jeweils zum Definieren des kompletten Pakets, das zur Ausführung Ihrer Anwendung bereitgestellt wird. Ein Cloud Services-Projekt enthält mindestens eine Web- oder Workerrolle. Analog dazu enthält ein Service Fabric-Anwendungsprojekt mindestens einen Dienst. 

Der Unterschied besteht darin, dass die Anwendungsbereitstellung beim Cloud Services-Projekt mit einer VM-Bereitstellung gekoppelt ist und daher VM-Konfigurationseinstellungen enthält. Beim Service Fabric-Anwendungsprojekt wird dagegen nur eine Anwendung definiert, die für eine Gruppe vorhandener VMs in einem Service Fabric-Cluster bereitgestellt wird. Der eigentliche Service Fabric-Cluster wird nur einmal bereitgestellt, und zwar entweder über eine Resource Manager-Vorlage oder über das Azure-Portal. Hierfür können mehrere Service Fabric-Anwendungen bereitgestellt werden.

![Vergleich von Service Fabric- und Cloud Services-Projekten][3]

## <a name="worker-role-to-stateless-service"></a>Von der Workerrolle zum zustandslosen Dienst
In Bezug auf das Konzept stellt eine Workerrolle eine zustandslose Workload dar. Dies bedeutet, dass jede Instanz der Workload identisch ist und Anforderungen jederzeit an alle Instanzen geleitet werden können. Für die einzelnen Instanzen wird nicht erwartet, dass sie sich der vorherigen Anforderung jeweils bewusst sind. Der Zustand, unter dem die Workload verarbeitet wird, wird von einem externen Statusspeicher verwaltet, z.B. Azure Table Storage oder Azure Cosmos DB. Unter Service Fabric wird diese Art von Workload durch einen zustandslosen Dienst dargestellt. Der einfachste Ansatz zum Migrieren einer Workerrolle zu Service Fabric ist das Konvertieren von Workerrollencode in einen zustandslosen Dienst.

![Von der Workerrolle zum zustandslosen Dienst][4]

## <a name="web-role-to-stateless-service"></a>Von der Webrolle zum zustandslosen Dienst
Ähnlich wie bei der Workerrolle stellt auch eine Webrolle eine zustandslose Workload dar, sodass sie vom Konzept her auch einem zustandslosen Service Fabric-Dienst zugeordnet werden kann. Im Gegensatz zu Webrollen weist Service Fabric aber keine IIS-Unterstützung auf. Zum Migrieren einer Webanwendung aus einer Webrolle in einen zustandslosen Dienst ist es zuerst erforderlich, zu einem Web-Framework zu wechseln, das sich selbst hosten kann und nicht von IIS oder System.Web abhängig ist, z. B. ASP.NET Core 1.

| **Anwendung** | **Unterstützt** | **Migrationspfad** |
| --- | --- | --- |
| ASP.NET Web Forms |Nein |In ASP.NET Core 1 MVC konvertieren |
| ASP.NET MVC |Per Migration |Upgrade auf ASP.NET Core 1 MVC |
| ASP.NET-Web-API |Per Migration |Selbst gehosteten Server oder ASP.NET Core 1 verwenden |
| ASP.NET Core 1 |Ja |– |

## <a name="entry-point-api-and-lifecycle"></a>Einstiegspunkt-API und Lebenszyklus
APIs von Workerrollen und Service Fabric-Diensten verfügen über ähnliche Einstiegspunkte: 

| **Einstiegspunkt** | **Workerrolle** | **Service Fabric-Dienst** |
| --- | --- | --- |
| Verarbeitung |`Run()` |`RunAsync()` |
| VM starten |`OnStart()` |– |
| VM beenden |`OnStop()` |– |
| Listener für Clientanforderungen öffnen |– |<ul><li> Zustandslos: `CreateServiceInstanceListener()`</li><li>Zustandsbehaftet: `CreateServiceReplicaListener()`</li></ul> |

### <a name="worker-role"></a>Workerrolle
```csharp

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### <a name="service-fabric-stateless-service"></a>Zustandsloser Service Fabric-Dienst
```csharp

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Beide verfügen über eine primäre „Run“-Außerkraftsetzung (Override) zum Starten der Verarbeitung. Bei Service Fabric-Diensten werden `Run`, `Start` und `Stop` zu einem einzelnen Einstiegspunkt kombiniert: `RunAsync`. Ihr Dienst sollte gestartet werden, wenn `RunAsync` gestartet wird, und beendet werden, wenn das Signal für das CancellationToken der `RunAsync`-Methode gegeben wird. 

Es gibt einige wichtige Unterschiede zwischen dem Lebenszyklus und der Lebensdauer von Workerrollen und Service Fabric-Diensten:

* **Lebenszyklus:** Der größte Unterschied ist, dass eine Workerrolle ein virtueller Computer ist und ihr Lebenszyklus daher an den virtuellen Computer gebunden ist. Dies umfasst auch Ereignisse, die mit dem Starten und Beenden des virtuellen Computers verbunden sind. Ein Service Fabric-Dienst hat einen Lebenszyklus, der vom VM-Lebenszyklus getrennt ist. Er enthält also keine Ereignisse, die sich auf das Starten und Beenden der Host-VM oder des Computers beziehen, da diese nicht verwandt sind.
* **Lebensdauer:** Eine Workerrolleninstanz wird wiederverwendet, wenn die `Run`-Methode beendet wird. Die `RunAsync` -Methode in einem Service Fabric-Dienst kann aber bis zum Schluss ausgeführt werden, und die Dienstinstanz bleibt verfügbar. 

Service Fabric stellt einen optionalen Kommunikationssetup-Einstiegspunkt für Dienste bereit, die auf Clientanforderungen lauschen. Sowohl der RunAsync- als auch der Kommunikations-Einstiegspunkt sind optionale Außerkraftsetzungen (Overrides) in Service Fabric-Diensten. - Ihr Dienst lauscht unter Umständen nur auf Clientanforderungen oder führt nur eine Verarbeitungsschleife aus (oder beides). - Dies ist der Grund, warum die RunAsync-Methode den Vorgang beenden kann, ohne dass die Dienstinstanz neu gestartet wird. Ggf. lauscht sie weiter auf Clientanforderungen.

## <a name="application-api-and-environment"></a>Anwendungs-API und Umgebung
Die API für die Cloud Services-Umgebung liefert Informationen und Funktionen für die aktuelle VM-Instanz und Informationen zu anderen VM-Rolleninstanzen. Service Fabric liefert Informationen zu seiner Laufzeit und einige Informationen zu dem Knoten, auf dem ein Dienst derzeit ausgeführt wird. 

| **Umgebungsaufgabe** | **Cloud Services** | **Service Fabric** |
| --- | --- | --- |
| Konfigurationseinstellungen und Änderungsbenachrichtigung |`RoleEnvironment` |`CodePackageActivationContext` |
| Lokaler Speicher |`RoleEnvironment` |`CodePackageActivationContext` |
| Endpunktinformationen |`RoleInstance` <ul><li>Aktuelle Instanz: `RoleEnvironment.CurrentRoleInstance`</li><li>Andere Rollen und Instanz: `RoleEnvironment.Roles`</li> |<ul><li>Aktuelle Knotenadresse: `NodeContext`</li><li>Ermittlung von Dienstendpunkten: `FabricClient` und `ServicePartitionResolver`</li> |
| Umgebungsemulation |`RoleEnvironment.IsEmulated` |– |
| Gleichzeitiges Änderungsereignis |`RoleEnvironment` |– |

## <a name="configuration-settings"></a>Konfigurationseinstellungen
Konfigurationseinstellungen in Cloud Services werden für eine VM-Rolle festgelegt und gelten für alle Instanzen dieser VM-Rolle. Diese Einstellungen sind in Dateien vom Typ „ServiceConfiguration.*.cscfg“ festgelegte Schlüssel-Wert-Paare, auf die direkt über RoleEnvironment zugegriffen werden kann. In Service Fabric gelten Einstellungen individuell für jeden Dienst und jede Anwendung, anstatt für eine VM, da eine VM mehrere Dienste und Anwendungen hosten kann. Ein Dienst besteht aus drei Paketen:

* **Code:** Enthält die ausführbaren Dienstdateien, Binärdateien, DLLs und alle anderen Dateien, die ein Dienst für die Ausführung benötigt.
* **Config:** Alle Konfigurationsdateien und Einstellungen für einen Dienst.
* **Data:** Statische Datendateien, die einem Dienst zugeordnet sind.

Diese Pakete können unabhängig voneinander mit einer Versionsangabe versehen und aktualisiert werden. Ähnlich wie bei Cloud Services kann auf ein Config-Paket programmgesteuert über eine API zugegriffen werden, und es sind Ereignisse verfügbar, um den Dienst über eine Änderung im Config-Paket zu benachrichtigen. Eine „Settings.xml“-Datei kann für die Schlüssel-Wert-Konfiguration und den programmgesteuerten Zugriff verwendet werden, ähnlich wie der Bereich „App-Einstellungen“ in einer „App.config“-Datei. Im Gegensatz zu Cloud Services kann ein Service Fabric-Config-Paket aber Konfigurationsdateien mit einem beliebigen Format enthalten, z. B. XML, JSON, YAML oder ein benutzerdefiniertes Binärformat. 

### <a name="accessing-configuration"></a>Zugreifen auf die Konfiguration
#### <a name="cloud-services"></a>Cloud Services
Sie können auf die Konfigurationseinstellungen aus „ServiceConfiguration.*.cscfg“ über `RoleEnvironment`zugreifen. Diese Einstellungen sind für alle Rolleninstanzen einer Cloud Services-Bereitstellung global verfügbar.

```csharp

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### <a name="service-fabric"></a>Service Fabric
Jeder Dienst verfügt über sein eigenes Konfigurationspaket. Es gibt keinen integrierten Mechanismus für globale Konfigurationseinstellungen, auf die mit allen Anwendungen in einem Cluster zugegriffen werden kann. Wenn Sie die spezielle Konfigurationsdatei „Settings.xml“ von Service Fabric in einem Konfigurationspaket verwenden, können Werte in „Settings.xml“ auf Anwendungsebene überschrieben werden, um Konfigurationseinstellungen auf Anwendungsebene zu ermöglichen.

Auf Konfigurationseinstellungen wird in jeder Dienstinstanz über das `CodePackageActivationContext`-Element des Diensts zugegriffen.

```csharp

ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### <a name="configuration-update-events"></a>Ereignisse für die Aktualisierung der Konfiguration
#### <a name="cloud-services"></a>Cloud Services
Das `RoleEnvironment.Changed`-Ereignis wird für die Benachrichtigung aller Rolleninstanzen verwendet, wenn in der Umgebung eine Änderung vorgenommen wird, etwa eine Konfigurationsänderung. Es wird zum Nutzen von Konfigurationsupdates eingesetzt, ohne dass Rolleninstanzen wiederverwendet werden oder ein Workerprozess neu gestartet wird.

```csharp

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### <a name="service-fabric"></a>Service Fabric
Jeder der drei Pakettypen in einem Dienst – Code, Config und Data – verfügt über Ereignisse, mit denen eine Dienstinstanz benachrichtigt wird, wenn ein Paket aktualisiert, hinzugefügt oder entfernt wird. Ein Dienst kann mehrere Pakete der unterschiedlichen Typen enthalten. Beispielsweise kann ein Dienst mehrere Config-Pakete aufweisen, von denen jedes einzeln mit Versionsangabe versehen und aktualisiert werden kann. 

Diese Ereignisse sind verfügbar, um Änderungen von Dienstpaketen zu verarbeiten, ohne die Dienstinstanz neu zu starten.

```csharp

this.Context.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## <a name="startup-tasks"></a>Startaufgaben
Startaufgaben sind Aktionen, die vor dem Starten einer Anwendung durchgeführt werden. Eine Startaufgabe wird normalerweise zum Ausführen von Setupskripts mit erhöhten Rechten verwendet. Startaufgaben werden sowohl für Cloud Services als auch für Service Fabric unterstützt. Der Hauptunterschied besteht darin, dass eine Startaufgabe in Cloud Services an eine VM gebunden ist, da sie Teil einer Rolleninstanz ist. In Service Fabric ist eine Startaufgabe dagegen an einen Dienst gebunden, der nicht an eine bestimmte VM gebunden ist.

| Service Fabric | Cloud Services |
| --- | --- |
| Speicherort der Konfiguration |ServiceDefinition.csdef |
| Rechte |„Eingeschränkt“ oder „Erhöht“ |
| Sequenzierung |„Einfach“, „Hintergrund“, „Vordergrund“ |

### <a name="cloud-services"></a>Cloud Services
Bei Cloud Services wird in „ServiceDefinition.csdef“ pro Rolle ein Einstiegspunkt für den Start konfiguriert. 

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### <a name="service-fabric"></a>Service Fabric
Bei Service Fabric wird ein Einstiegspunkt für den Start pro Dienst in „ServiceManifest.xml“ konfiguriert:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## <a name="a-note-about-development-environment"></a>Hinweis zur Entwicklungsumgebung
Sowohl Cloud Services als auch Service Fabric sind in Visual Studio mit Projektvorlagen integriert und verfügen über Unterstützung für das Debuggen, Konfigurieren und Bereitstellen lokal und unter Azure. Sowohl unter Cloud Services als auch unter Service Fabric wird zudem eine lokale Laufzeitumgebung für die Entwicklung bereitgestellt. Der Unterschied besteht hierbei darin, dass von der Cloud Services-Entwicklungslaufzeit die Azure-Umgebung emuliert wird, in der sie ausgeführt wird. Service Fabric verwendet dagegen keinen Emulator, sondern die vollständige Service Fabric-Laufzeit. Die Service Fabric-Umgebung, die Sie auf Ihrem lokalen Entwicklungscomputer ausführen, ist die gleiche Umgebung, die in der Produktion ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die weiteren Informationen zu Service Fabric Reliable Services und den grundlegenden Unterschieden zwischen Cloud Services der Service Fabric-Anwendungsarchitektur, um zu verstehen, wie Sie die Vorteile der umfassenden Service Fabric-Features nutzen können.

* [Erste Schritte mit Service Fabric Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Konzeptioneller Leitfaden zu den Unterschieden zwischen Cloud Services und Service Fabric](service-fabric-cloud-services-migration-differences.md)

<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png
