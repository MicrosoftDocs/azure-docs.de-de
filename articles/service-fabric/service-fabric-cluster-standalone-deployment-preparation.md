---
title: Vorbereiten der Bereitstellung eines eigenständigen Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: Diese Dokumentation erläutert die Vorbereitung der Umgebung und Erstellung der Clusterkonfiguration. Diese Aktionen müssen vor der Bereitstellung eines Clusters durchgeführt werden, der eine Produktionsworkload verarbeiten soll.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: e5fa46930a3be3c85cd76e655fac3164cc45d957
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544737"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planen und Vorbereiten der Bereitstellung eines eigenständigen Service Fabric-Clusters

<a id="preparemachines"></a>Führen Sie die folgenden Schritte aus, bevor Sie den Cluster erstellen.

## <a name="plan-your-cluster-infrastructure"></a>Planen der Clusterinfrastruktur
Da Sie im Begriff sind, auf Ihren eigenen Computern einen Service Fabric-Cluster zu erstellen, können Sie entscheiden, welche Arten von Fehlern der Cluster überstehen soll. Benötigen Sie beispielsweise separate Stromversorgungsleitungen oder Internetverbindungen zur Versorgung dieser Computer? Berücksichtigen Sie darüber hinaus auch die physische Sicherheit der Computer. Wo befinden sich die Computer, und wer benötigt Zugang zu ihnen? Nachdem Sie diese Entscheidungen getroffen haben, können Sie die Computer logisch den verschiedenen Fehlerdomänen zuordnen (siehe nächster Schritt). Die Infrastrukturplanung für Produktionscluster ist komplizierter als für Testcluster.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Bestimmen der Anzahl von Fehlerdomänen und Upgradedomänen
Eine [*Fehlerdomäne* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) ist eine physische Fehlereinheit, die in direktem Zusammenhang mit der physischen Infrastruktur von Rechenzentren steht. Eine Fehlerdomäne besteht aus Hardwarekomponenten (Computer, Switches, Netzwerken usw.), die sich eine einzelne Fehlerquelle teilen. Obwohl es keine 1:1-Zuordnung zwischen Fehlerdomänen und Racks gibt, kann praktisch jedes Rack als Fehlerdomäne betrachtet werden.

Beim Angeben von FDs in „ClusterConfig.json“ können Sie jeweils den Namen der FD wählen. Service Fabric unterstützt hierarchische FDs, damit sie Ihre Infrastrukturtopologie reflektieren können.  Beispielsweise sind die folgenden Fehlerdomänen zulässig:

* „faultDomain“: „fd:/Room1/Rack1/Machine1“
* „faultDomain“: „fd:/FD1“
* „faultDomain“: „fd:/Room1/Rack1/PDU1/M1“

Eine *Upgradedomäne* (UD) ist eine logische Einheit von Knoten. Während eines orchestrierten Service Fabric-Upgrades (Anwendungsupgrade oder Clusterupgrade) werden alle Knoten in einer Upgradedomäne außer Betrieb genommen, um das Upgrade auszuführen, während Knoten in anderen UDs zur Erfüllung von Anforderungen zur Verfügung stehen. Die Firmwareupgrades, die Sie auf Ihren Computern ausführen, berücksichtigen UDs nicht, darum können Sie sie nur jeweils auf einem Computer ausführen und nicht auf allen gleichzeitig.

Die einfachste Möglichkeit, sich diese Konzepte vorzustellen, besteht darin, FDs als Einheit des ungeplanten Ausfalls und UDs als Einheit der geplanten Wartung zu betrachten.

Beim Angeben von UDs in „ClusterConfig.json“ können Sie jeweils den Namen der UD wählen. Beispielsweise sind die folgenden Namen zulässig:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

Ausführlichere Informationen zu FDs und UDs finden Sie im Artikel [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).

Wenn Sie die vollständige Kontrolle über die Wartung und Verwaltung der Knoten haben (also Sie für das Aktualisieren und Ersetzen von Computern zuständig sind), sollte sich ein Cluster in der Produktion über mindestens drei FDs erstrecken, damit er in einer Produktionsumgebung unterstützt wird. Für Cluster in Umgebungen (d.h. VM-Instanzen mit Amazon Web Services), in denen Sie nicht die vollständige Kontrolle über die Computer haben, sollten mindestens fünf FDs in Ihrem Cluster vorhanden sein. Jede FD kann mehrere Knoten enthalten. Hierdurch sollen Probleme durch Computerupgrades und -updates verhindert werden, die abhängig vom Zeitpunkt die Ausführung von Anwendungen und Diensten in Clustern beeinträchtigen können.

## <a name="determine-the-initial-cluster-size"></a>Bestimmen der anfänglichen Clustergröße

Im Allgemeinen wird die Anzahl von Knoten im Cluster durch Ihre Geschäftsanforderungen bestimmt, d.h. durch die Anzahl der auszuführenden Dienste und Container im Cluster und die Anzahl der für Ihre Workloads benötigten Ressourcen. Für Produktionscluster werden mindestens fünf Knoten im Cluster mit fünf FDs empfohlen. Wie oben beschrieben gilt jedoch Folgendes: Wenn Sie die vollständige Kontrolle über Ihre Knoten haben und drei FDs vorhanden sind, sollten auch drei Knoten ausreichen.

Testcluster, die zustandsbehaftete Workloads ausführen, müssen drei Knoten enthalten. Testcluster, die nur zustandslose Workloads ausführen, benötigen hingegen nur einen Knoten. Beachten Sie, dass Sie für Entwicklungszwecke über mehrere Knoten auf einem bestimmten Computer verfügen können. In einer Produktionsumgebung unterstützt Service Fabric jedoch nur einen Knoten pro physischem oder virtuellem Computer.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Vorbereiten der Computer, die als Knoten dienen

Hier sind einige empfohlene Spezifikationen für jeden Computer aufgeführt, den Sie dem Cluster hinzufügen möchten:

* Mindestens 16 GB RAM
* Mindestens 40 GB verfügbarer Speicherplatz
* CPU mit mindestens vier Kernen
* Verbindung mit einem sicheren Netzwerk oder sicheren Netzwerken für alle Computer
* Windows Server-Betriebssystem installiert (gültige Versionen: 2012 R2, 2016, 1709, 1803)
* [.NET Framework 4.5.1 oder höher](https://www.microsoft.com/download/details.aspx?id=40773), vollständig installiert
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* Der [RemoteRegistry-Dienst](https://technet.microsoft.com/library/cc754820) muss auf allen Computern ausgeführt werden.

Der Clusteradministrator, der den Cluster bereitstellt und konfiguriert, muss auf jedem Computer [Administratorrechte](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) besitzen. Service Fabric kann nicht auf einem Domänencontroller installiert werden.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Herunterladen des eigenständigen Service Fabric-Pakets für Windows Server
[Downloadlink: Service Fabric Standalone Package – Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690). Laden Sie das Paket herunter, und entpacken Sie es entweder auf einem Bereitstellungscomputer, der nicht Teil des Clusters ist, oder auf einem der Computer, die Teil des Clusters sein werden.

## <a name="modify-cluster-configuration"></a>Ändern der Clusterkonfiguration
Um einen eigenständigen Cluster zu erstellen, müssen Sie eine ClusterConfig.json-Datei für die Konfiguration des eigenständigen Clusters erstellen, die die Spezifikation des Clusters beschreibt. Als Grundlage für die Konfigurationsdatei können Sie die Vorlagen verwenden, die Sie unter dem nachfolgenden Link finden. <br>
[Standalone Cluster Configurations](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Ausführliche Informationen zu den Abschnitten in dieser Datei finden Sie im Artikel [Konfigurationseinstellungen für eigenständige Windows-Cluster](service-fabric-cluster-manifest.md).

Öffnen Sie eine der „ClusterConfig.json“-Dateien aus dem Paket, das Sie heruntergeladen haben, und ändern Sie die folgenden Einstellungen:

| **Konfigurationseinstellung** | **Beschreibung** |
| --- | --- |
| **NodeTypes** |Mit Knotentypen können Sie die Clusterknoten in verschiedene Gruppen unterteilen. Ein Cluster muss über mindestens einen Knotentyp verfügen. Alle Knoten in einer Gruppe haben die folgenden gemeinsamen Merkmale: <br> **Name**: Dies ist der Knotentypname. <br>**Endpunktports**: Verschiedene benannte Endpunkte (Ports), die diesem Knotentyp zugeordnet sind. Sie können eine beliebige Portnummer verwenden, solange sie nicht mit anderen Elementen in diesem Manifest in Konflikt steht und nicht bereits von einem anderen Programm auf dem (virtuellen) Computer verwendet wird. <br> **Platzierungseigenschaften**: Eigenschaften für diesen Knotentyp, die Sie später als Platzierungseinschränkungen für Systemdienste oder eigene Dienste verwenden. Diese Eigenschaften sind benutzerdefinierte Schlüssel-Wert-Paare, mit denen für einen bestimmten Knoten zusätzliche Metadaten bereitgestellt werden. Beispiele für Knoteneigenschaften wären etwa die Angabe, ob der Knoten über eine Festplatte oder Grafikkarte verfügt, die Anzahl von Spindeln des Festplattenlaufwerks, die Anzahl von Kernen und andere physische Eigenschaften. <br> **Kapazitäten**: Knotenkapazitäten definieren Name und Menge einer bestimmten Ressource, die ein bestimmter Knoten nutzen kann. Ein Knoten definiert z. B., dass er über Kapazität für eine Metrik mit dem Namen „MemoryInMb“ verfügt und standardmäßig 2.048 MB an verfügbarem Arbeitsspeicher aufweist. Diese Kapazitäten werden zur Laufzeit verwendet, um sicherzustellen, dass Dienste, die bestimmte Ressourcenmengen beanspruchen, auf Knoten mit den verfügbaren Ressourcen in der benötigten Menge platziert werden.<br>**IsPrimary**: Wenn Sie mehr als ein NodeType-Element definiert haben, sollten Sie sicherstellen, dass nur ein Element mit dem Wert *TRUE* als primäres Element festgelegt ist. Auf diesem Element werden die Systemdienste ausgeführt. Alle anderen Knotentypen sollten auf den Wert *FALSE* festgelegt werden. |
| **Nodes** |Dies sind die Details für jeden Knoten, der Teil des Clusters ist (Knotentyp, Knotenname, IP-Adresse, Fehlerdomäne und Upgradedomäne des Knotens). Die Computer, aus denen Sie den Cluster erstellen möchten, müssen mit ihren IP-Adressen hier aufgeführt werden. <br> Wenn Sie die gleiche IP-Adresse für alle Knoten verwenden, wird ein Cluster mit einem Computer erstellt, den Sie für Tests verwenden können. Cluster mit einem Computer dürfen nicht zur Bereitstellung von Produktionsworkloads verwendet werden. |

Wenn in der Clusterkonfiguration alle Einstellungen für die Umgebung konfiguriert wurden, kann die Konfiguration in der Clusterumgebung getestet werden (Schritt 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Einrichten der Umgebung

Wenn ein Clusteradministrator einen eigenständigen Service Fabric-Cluster konfiguriert, muss die Umgebung mit den folgenden Kriterien eingerichtet werden: <br>
1. Der Benutzer, der den Cluster erstellt, muss über Administratorberechtigungen auf allen Computern verfügen, die in der Clusterkonfigurationsdatei als Knoten aufgeführt sind.
2. Für den Computer, auf dem der Cluster erstellt wird, sowie für jeden anderen Computerknoten gilt Folgendes:
   * Das Service Fabric-SDK muss deinstalliert sein.
   * Die Service Fabric-Laufzeit muss deinstalliert sein. 
   * Der Windows-Firewalldienst (mpssvc) muss aktiviert sein.
   * Der Remoteregistrierungsdienst (Remoteregistrierung) muss aktiviert sein.
   * Die Dateifreigabe (SMB) muss aktiviert sein.
   * Die erforderlichen Ports müssen geöffnet sein, basierend auf den Ports der Clusterkonfiguration.
   * Die erforderlichen Ports für den Windows SMB-Dienst und den Remoteregistrierungsdienst müssen geöffnet sein: 135, 137, 138, 139 und 445.
   * Die Computer müssen über das Netzwerk miteinander verbunden sein.
3. Keiner der Clusterknotencomputer darf ein Domänencontroller sein.
4. Wenn es sich bei dem bereitzustellenden Cluster um einen sicheren Cluster handelt, überprüfen Sie, ob die Sicherheitsvoraussetzungen erfüllt und ordnungsgemäß konfiguriert sind.
5. Wenn die Clustercomputer nicht über das Internet erreichbar sind, legen Sie in der Clusterkonfiguration Folgendes fest:
   * Deaktivieren der Telemetrie: Legen Sie unter *properties* die Option *"enableTelemetry": false* fest.
   * Automatisches Herunterladen von Fabric-Versionen und Benachrichtigungen zum nahenden Supportende der aktuellen Clusterversion deaktivieren: Legen Sie unter *properties* die Option *"fabricClusterAutoupgradeEnabled": true* fest.
   * Selbst wenn der Netzwerk-/Internetzugriff auf die in der Whitelist aufgeführten Domänen beschränkt ist, sind die unten aufgeführten Domänen für ein automatisches Upgrade erforderlich: go.microsoft.com, download.microsoft.com.

6. Legen Sie geeignete Service Fabric-Virenschutzausschlüsse fest:

| **Vom Virenschutz ausgeschlossene Verzeichnisse** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (aus der Clusterkonfiguration) |
| FabricLogRoot (aus der Clusterkonfiguration) |

| **Vom Virenschutz ausgeschlossene Prozesse** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Überprüfen der Umgebung anhand des TestConfiguration-Skripts
Das Skript „TestConfiguration.ps1“ befindet sich im eigenständigen Paket. Es wird als Best Practices Analyzer verwendet, um einige der oben genannten Kriterien zu überprüfen. Es dient zur Überprüfung der Integrität, um zu ermitteln, ob ein Cluster in einer bestimmten Umgebung bereitgestellt werden kann. Wenn ein Fehler vorliegt, finden Sie Maßnahmen zur Problembehandlung in der Liste unter [Einrichten der Umgebung](service-fabric-cluster-standalone-deployment-preparation.md). 

Dieses Skript kann auf jedem Computer ausgeführt werden, der Administratorzugriffsrechte auf alle Computer hat, die als Knoten in der Clusterkonfigurationsdatei aufgeführt sind. Der Computer, auf dem dieses Skript ausgeführt wird, muss nicht Teil des Clusters sein.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

Zurzeit überprüft dieses Modul für Konfigurationstests nicht die Sicherheitskonfiguration – dies muss also unabhängig vom Modul erfolgen.  

> [!NOTE]
> Wir nehmen kontinuierlich Verbesserungen vor, um das Modul stabiler zu machen. Wenn Sie also einen Fall bemerken, der fehlerhaft oder in TestConfiguration zurzeit nicht erfasst ist, informieren Sie uns bitte über unsere [Supportkanäle](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen eines eigenständigen Clusters unter Windows Server](service-fabric-cluster-creation-for-windows-server.md)
