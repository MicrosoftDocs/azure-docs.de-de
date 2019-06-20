---
title: 'Tutorial: Installieren einer Anwendung in Ihrem eigenständigen Service Fabric-Cluster –Azure Service Fabric | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie in Ihrem eigenständigen Service Fabric-Cluster eine Anwendung installieren.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 5bc326bbc16ef93d484425f26b6f8226150c77c6
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66302420"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: Bereitstellen einer Anwendung in Ihrem eigenständigen Service Fabric-Cluster

Mit eigenständigen Service Fabric-Clustern können Sie Ihre eigene Umgebung wählen und einen Cluster im Rahmen des Service Fabric-Konzepts „Jedes Betriebssystem, jede Cloud“ erstellen. In dieser Tutorialreihe erstellen Sie einen eigenständigen, in AWS gehosteten Cluster und stellen darin eine Anwendung bereit.

Dieses Tutorial ist der dritte Teil einer Reihe.  Mit eigenständigen Service Fabric-Clustern können Sie Ihre eigene Umgebung wählen und einen Cluster im Rahmen unseres Konzepts „Jedes Betriebssystem, jede Cloud“ mit Service Fabric erstellen. In diesem Tutorial erfahren Sie, wie Sie die erforderliche AWS-Infrastruktur zum Hosten dieses eigenständigen Clusters erstellen.

Im dritten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Herunterladen der Beispiel-App
> * Bereitstellen im Cluster

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* [Installieren Sie Visual Studio 2019](https://www.visualstudio.com/) und die Workloads **Azure-Entwicklung** und **ASP.NET und Webentwicklung**.
* [Installieren Sie das Service Fabric SDK](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>Herunterladen der Beispielanwendung „Voting“

Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Bereitstellen der App im Service Fabric-Cluster

Die heruntergeladene Anwendung kann direkt über Visual Studio in einem Cluster bereitgestellt werden.

1. Öffnen Sie Visual Studio.

2. Klicken Sie auf **Datei** > **Öffnen**.

3. Navigieren Sie zu dem Ordner mit dem geklonten Git-Repository, und klicken Sie auf „Voting.sln“.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Anwendungsprojekt `Voting`, und klicken Sie anschließend auf **Veröffentlichen**.

5. Klicken Sie auf das Dropdownmenü für den **Verbindungsendpunkt**, und geben Sie den öffentlichen DNS-Namen eines der Knoten in Ihrem Cluster ein.  Zum Beispiel, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

6. Öffnen Sie Ihren bevorzugten Browser, und geben Sie die Clusteradresse ein (also den Verbindungsendpunkt, den diese App am Port 8080 bereitstellt; Beispiel: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![API-Antwort aus dem Cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Nächste Schritte

Im dritten Teil der Reihe haben Sie gelernt, wie Sie in Ihrem Cluster eine Anwendung bereitstellen:

> [!div class="checklist"]
> * Herunterladen der Beispiel-App
> * Bereitstellen im Cluster

Im vierten Teil der Reihe erfahren Sie, wie Sie Ihren Cluster bereinigen.

> [!div class="nextstepaction"]
> [Bereinigen Ihres eigenständigen Clusters](service-fabric-tutorial-standalone-clean-up.md)