---
title: Einrichten einer Windows-Entwicklungsumgebung für Service Fabric Mesh
description: Einrichten der Windows-Entwicklungsumgebung, damit Sie eine Service Fabric Mesh-Anwendung erstellen und in Azure Service Fabric Mesh bereitstellen können.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625734"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Einrichten der Windows-Entwicklungsumgebung zum Erstellen von Service Fabric Mesh-Anwendungen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Zum Entwickeln und Ausführen von Azure Service Fabric Mesh-Anwendungen auf dem Windows-Entwicklungscomputer benötigen Sie:

* Docker
* Visual Studio 2017 oder höher
* Service Fabric Mesh-Runtime
* Service Fabric Mesh-SDK und -Tools.

Außerdem eine der folgenden Versionen von Windows:

* Windows 10 (Enterprise, Professional, oder Education), Versionen 1709 (Fall Creators-Update) oder 1803 (Windows 10 April 2018-Update)
* Windows Server, Version 1709
* Windows Server, Version 1803

Die folgenden Anweisungen helfen Ihnen bei der Installation basierend auf der von Ihnen verwendeten Version von Windows.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 oder höher ist erforderlich, um Service Fabric Mesh-Anwendungen bereitzustellen. [Installieren Sie Version 15.6.0][download-visual-studio] oder höher, und aktivieren Sie die folgenden Workloads:

* ASP.NET und Webentwicklung
* Azure-Entwicklung

## <a name="install-docker"></a>Installieren von Docker

Wenn Sie Docker bereits installiert haben, stellen Sie sicher, dass Sie über die neueste Version verfügen. Docker informiert Sie unter Umständen, wenn eine neue Version herausgekommen ist. Sie sollten dies aber manuell überprüfen, um sicherzustellen, dass Sie über die neueste Version verfügen.

#### <a name="install-docker-on-windows-10"></a>Installieren von Docker unter Windows 10

Laden Sie die aktuelle Version der [Docker Community Edition für Windows][download-docker] herunter, und installieren Sie sie, um die containerbasierten Service Fabric-Apps zu unterstützen, die von Service Fabric Mesh verwendet werden.

Wählen Sie während der Installation **Windows-Container anstelle von Linux-Containern verwenden** aus, falls Sie dazu aufgefordert werden.

Wenn Hyper-V auf Ihrem Computer nicht aktiviert ist, wird dieser Schritt vom Docker-Installationsprogramm angeboten. Klicken Sie auf **OK**, wenn die entsprechende Aufforderung angezeigt wird.

#### <a name="install-docker-on-windows-server-2016"></a>Installieren von Docker unter Windows Server 2016

Ist die Hyper-V-Rolle nicht aktiviert, öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus, um Hyper-V zu aktivieren. Starten Sie anschließend Ihren Computer neu. Weitere Informationen finden Sie in der [Docker Enterprise Edition für Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Starten Sie den Computer neu.

Öffnen Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus, um Docker zu installieren:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDKs und Tools

Installieren Sie die Service Fabric Mesh-Laufzeit, das SDK und die Tools in der folgenden Reihenfolge.

1. Installieren Sie das [Service Fabric Mesh SDK][download-sdkmesh] mithilfe des Webplattform-Installers. Dadurch werden auch das Microsoft Azure Service Fabric SDK und die Laufzeit installiert.
2. Installieren Sie [Service Fabric Mesh Tools für Visual Studio (Vorschauversion)][download-tools] aus Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Erstellen eines Clusters

> [!IMPORTANT]
> Docker **muss** ausgeführt werden, bevor Sie einen Cluster erstellen können.
> Testen Sie, ob Docker ausgeführt wird, indem Sie ein Terminalfenster öffnen und `docker ps` ausführen, um zu sehen, ob ein Fehler auftritt. Wenn in der Antwort kein Fehler angegeben wird, wird Docker ausgeführt, und Sie sind bereit, einen Cluster zu erstellen.

> [!Note]
> Wenn Sie zur Entwicklung einen Computer verwenden, auf dem das Windows Fall Creators-Update (Version 1709) installiert ist, können Sie nur Docker-Images für Windows-Version 1709 verwenden.
> Wenn Sie zur Entwicklung einen Computer verwenden, auf dem das Windows 10-Update vom April 2018 (Version 1803) installiert ist, können Sie entweder Docker-Images für Windows-Version 1709 oder 1803 verwenden.

Bei Verwendung von Visual Studio können Sie diesen Abschnitt überspringen, da Visual Studio einen lokalen Cluster für Sie erstellt, falls dieser nicht vorhanden ist.

Zur Erzielung einer optimalen Debugleistung beim Erstellen und Ausführen von jeweils einer einzelnen Service Fabric-App sollten Sie einen lokalen Entwicklungscluster mit nur einem Knoten erstellen. Erstellen Sie einen lokalen Entwicklungscluster mit fünf Knoten, wenn Sie mehrere Anwendungen gleichzeitig ausführen. Dieser Cluster muss immer dann ausgeführt werden, wenn Sie ein Service Fabric Mesh-Projekt bereitstellen oder debuggen.

Nachdem Sie die Laufzeit, SDKs, Visual Studio-Tools und Docker installiert haben und Docker ausgeführt wird, erstellen Sie einen Entwicklungscluster.

1. Schließen Sie das PowerShell-Fenster.
2. Öffnen Sie als Administrator ein neues PowerShell-Fenster mit erhöhten Rechten. Dieser Schritt ist erforderlich, um die zuletzt installierten Service Fabric-Module zu laden.
3. Führen Sie den folgenden PowerShell-Befehl aus, um einen Entwicklungscluster zu erstellen:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Um das lokale Cluster-Manager-Tool zu starten, führen Sie den folgenden PowerShell-Befehl aus:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Sobald das Dienstcluster-Manager-Tool ausgeführt wird (es wird auf Ihrer Taskleiste angezeigt), klicken Sie mit der rechten Maustaste darauf, und klicken Sie auf **Lokalen Cluster starten**.

![Abbildung 1: Starten des lokalen Clusters](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

Jetzt können Sie Service Fabric Mesh-Anwendungen erstellen!

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie das Tutorial [Erstellen einer Azure Service Fabric-App](service-fabric-mesh-tutorial-create-dotnetcore.md).

[Hier](service-fabric-mesh-faq.md) finden Sie Antworten auf häufig gestellte Fragen sowie Informationen zu bekannten Problemen.

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
