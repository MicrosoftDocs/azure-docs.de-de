---
title: Einrichten einer Windows-Entwicklungsumgebung für Azure-Microservices | Microsoft-Dokumentation
description: Installieren Sie Laufzeit, SDK und Tools, und erstellen Sie einen lokalen Entwicklungscluster. Nach Abschluss des Setups können Sie mit der Erstellung von Anwendungen unter Windows beginnen.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/24/2019
ms.author: aljo
ms.openlocfilehash: 68a91bfba07b31117f553e4d689adc51bf51824c
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297266"
---
# <a name="prepare-your-development-environment-on-windows"></a>Vorbereiten Ihrer Entwicklungsumgebung unter Windows
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Zum Erstellen und Ausführen von [Azure Service Fabric-Anwendungen][1] müssen auf dem Windows-Entwicklungscomputer die Service Fabric-Laufzeit, das SDK und Tools installiert werden. Außerdem müssen Sie die [Ausführung der im SDK enthaltenen Windows PowerShell-Skripts aktivieren](#enable-powershell-script-execution).

## <a name="prerequisites"></a>Voraussetzungen
### <a name="supported-operating-system-versions"></a>Unterstützte Betriebssystemversionen
Die folgenden Betriebssystemversionen werden bei der Entwicklung unterstützt:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7-Unterstützung:
> - Windows 7 enthält standardmäßig nur Windows PowerShell 2.0. Für Service Fabric PowerShell-Cmdlets ist PowerShell 3.0 oder höher erforderlich. Sie können [Windows PowerShell 5.0][powershell5-download] aus dem Microsoft Download Center herunterladen.
> - Der Service Fabric-Reverseproxy ist unter Windows 7 nicht verfügbar.
>

## <a name="install-the-sdk-and-tools"></a>Installieren des SDKs und der Tools
Für die Installation des SDK und der Tools wird der Webplattform-Installer (WebPI) empfohlen. Wenn bei der Verwendung des WebPI Laufzeitfehler auftreten, finden Sie in den Anmerkungen zu einer bestimmten Service Fabric-Version direkte Links zu den Installern. Die Anmerkungen zur Version finden Sie in den verschiedenen Versionsankündigungen im [Service Fabric-Teamblog](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Upgrades eines lokalen Service Fabric-Entwicklungsclusters werden nicht unterstützt.

### <a name="to-use-visual-studio-2017"></a>So verwenden Sie Visual Studio 2017
Die Service Fabric-Tools sind Bestandteil der Workload für die Azure-Entwicklung in Visual Studio 2017. Aktivieren Sie diese Workload im Rahmen der Visual Studio-Installation.
Zudem müssen Sie das Microsoft Azure Service Fabric SDK und die Laufzeit mithilfe des Webplattform-Installers installieren.

* [Installieren des Microsoft Azure Service Fabric-SDKs][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>So verwenden Sie Visual Studio 2015 (erfordert mindestens Visual Studio 2015 Update 2)
Für Visual Studio 2015 werden die Service Fabric-Tools zusammen mit dem SDK und der Laufzeit mithilfe des Webplattform-Installers installiert:

* [Installieren des Microsoft Azure Service Fabric-SDKs und der Tools][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Nur SDK-Installation
Wenn Sie nur das SDK benötigen, können Sie dieses Paket installieren:
* [Installieren des Microsoft Azure Service Fabric-SDKs][core-sdk]

Aktuelle Versionen:
* Service Fabric SDK und -Tools 3.3.664
* Service Fabric-Runtime 6.4.664
* Service Fabric-Tools für Visual Studio 2015 2.4.11116.1
* Visual Studio 2017 15.9 enthält Service Fabric-Tools für Visual Studio 2.4.11024.1 

Eine Liste mit unterstützten Versionen finden Sie unter [Unterstützte Service Fabric-Versionen](service-fabric-versions.md).

> [!NOTE]
> Cluster mit einem Computer (OneBox) werden für Anwendungs- oder Clusterupgrades nicht unterstützt. Löschen Sie den OneBox-Cluster, und erstellen Sie ihn neu, wenn Sie ein Clusterupgrade ausführen müssen oder wenn Probleme beim Ausführen eines Anwendungsupgrades auftreten. 

## <a name="enable-powershell-script-execution"></a>Aktivieren der PowerShell-Skriptausführung
Service Fabric verwendet Windows PowerShell-Skripts zum Erstellen eines lokalen Entwicklungsclusters und zum Bereitstellen von Anwendungen aus Visual Studio. Die Ausführung dieser Skripts wird von Windows standardmäßig blockiert. Um die Skripts zu aktivieren, müssen Sie die PowerShell-Ausführungsrichtlinie ändern. Öffnen Sie PowerShell als Administrator, und geben Sie folgenden Befehl ein:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Installieren von Docker (optional)
[Service Fabric ist der Containerorchestrator](service-fabric-containers-overview.md), der Microservices für einen Cluster von Computern bereitstellt. Zum Ausführen von Windows-Containeranwendungen in Ihrem lokalen Entwicklungscluster müssen Sie zunächst Docker für Windows installieren. Laden Sie [Docker CE für Windows (stabil)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) herunter. Klicken Sie nach dem Installieren und Starten von Docker mit der rechten Maustaste auf das Taskleistensymbol, und wählen Sie **Switch to Windows containers** (Zu Windows-Containern wechseln). Dieser Schritt ist für die Ausführung Windows-basierter Docker-Images erforderlich.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die Entwicklungsumgebung eingerichtet haben, können Sie nun mit dem Erstellen und Ausführen von Apps beginnen.

* [Informationen zum Erstellen, Bereitstellen und Verwalten von Anwendungen](service-fabric-tutorial-create-dotnet-app.md)
* [Weitere Informationen zu den Programmiermodellen: Reliable Services und Reliable Actors](service-fabric-choose-framework.md)
* [Service Fabric-Codebeispiele auf GitHub](https://aka.ms/servicefabricsamples)
* [Visualisieren des Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Informieren Sie sich über [Service Fabric-Supportoptionen](service-fabric-support.md).

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric-Kampagnenseite"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "WebPI-Link für VS 2015"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "WebPI-Link für Dev15"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "WebPI-Link für Core SDK"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
