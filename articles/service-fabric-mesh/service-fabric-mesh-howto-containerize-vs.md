---
title: Packen einer vorhandenen .NET-App in einen Container für Service Fabric Mesh | Microsoft-Dokumentation
description: Hinzufügen von Mesh-Unterstützung zu einer vorhandenen .NET-App
services: service-fabric-mesh
keywords: Packen von Service Fabric Mesh in einen Container
author: dkkapur
ms.author: dekapur
ms.date: 11/08/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: cb4e327e1c8c0a653cb94233f568b4847494c439
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419437"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Packen einer vorhandenen .NET-App in einen Container für Service Fabric Mesh

In diesem Artikel erfahren Sie, wie Sie eine vorhandene .NET-App mit Service Fabric Mesh-Containerorchestrierungsunterstützung versehen.

In Visual Studio 2017 können Sie ASP.NET- und Konsolenprojekte, die das vollständige .NET Framework verwenden, mit Containerisierungsunterstützung versehen.

> [!NOTE]
> .NET **Core**-Projekte werden derzeit nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Vergewissern Sie sich, dass [Ihre Entwicklungsumgebung eingerichtet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ist. Diese Einrichtung umfasst die Installation von Service Fabric-Runtime, SDK, Docker und Visual Studio 2017 sowie die Erstellung eines lokalen Clusters.

## <a name="open-an-existing-net-app"></a>Öffnen einer vorhandenen .NET-App

Öffnen Sie die App, der Sie die Containerorchestrierungsunterstützung hinzufügen möchten.

Wenn Sie ein Beispiel ausprobieren möchten, können Sie das Codebeispiel [eShop](https://github.com/MikkelHegn/ContainersSFLab) verwenden. Im weiteren Verlauf dieses Artikels wird davon ausgegangen, dass dieses Projekt verwendet wird. Sie können die Schritte aber auch für ein eigenes Projekt ausführen.

Rufen Sie eine Kopie des Projekts **eShop** ab:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Öffnen Sie nach Abschluss des Downloadvorgangs die Projektmappe **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln** in Visual Studio 2017.

## <a name="add-container-support"></a>Hinzufügen von Containerunterstützung
 
Gehen Sie wie folgt vor, um ein vorhandenes ASP.NET- oder Konsolenprojekt mithilfe von Service Fabric Mesh-Tools mit Containerorchestrierungsunterstützung zu versehen:

Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Projektnamen (in diesem Beispiel: **eShopLegacyWebForms**), und wählen Sie anschließend **Hinzufügen** > **Unterstützung für Containerorchestrator** aus.
Das Dialogfeld **Unterstützung für Containerorchestrator hinzufügen** wird angezeigt.

![Visual Studio-Dialogfeld zum Hinzufügen des Containerorchestrators](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Wählen Sie in der Dropdownliste die Option **Service Fabric Mesh** aus, und klicken Sie anschließend auf **OK**.

Das Tool überprüft daraufhin, ob Docker installiert ist, fügt Ihrem Projekt ein Dockerfile hinzu und ruft ein Docker-Image für Ihr Projekt ab.  
Ihrer Projektmappe wird ein Service Fabric Mesh-Anwendungsprojekt hinzugefügt. Es enthält Ihre Mesh-Veröffentlichungsprofile und Konfigurationsdateien. Das Projekt hat den gleichen Namen wie Ihr Projekt, am Ende wird allerdings noch „Application“ angehängt (Beispiel: **eShopLegacyWebFormsApplication**). 

In dem neuen Mesh-Projekt sind zwei Ordner interessant:
- **App Resources**: Enthält YAML-Dateien, die zusätzliche Mesh-Ressourcen wie etwa das Netzwerk beschreiben.
- **Service Resources**: Enthält die Datei „service.yaml“, die beschreibt, wie Ihre App nach der Bereitstellung ausgeführt werden soll.

Nachdem Ihrer App die Containerorchestrierungsunterstützung hinzugefügt wurde, können Sie **F5** drücken, um Ihre .NET-App in Ihrem lokalen Service Fabric Mesh-Cluster zu debuggen. Die folgende Abbildung zeigt die in einem Service Fabric Mesh-Cluster ausgeführte ASP.NET-App „eShop“: 

![eShop-App](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Nun können Sie die App in Azure Service Fabric Mesh veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Veröffentlichen einer App in Service Fabric Mesh: [Tutorial: Bereitstellen einer Service Fabric Mesh-Anwendung](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)