---
title: Optimieren der Leistung von Visual Studio für Azure Service Fabric Mesh-Projekte | Microsoft-Dokumentation
description: Optimieren der Leistung von Visual Studio für Azure Service Fabric Mesh-Apps
services: service-fabric-mesh
keywords: Optimieren der Debugleistung
author: dkkapur
ms.author: dekapur
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: chakdan
ms.openlocfilehash: f7a0cb47ad8010bd54a817e9990221b320cde541
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60419050"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Optimieren der Leistung von Visual Studio für Service Fabric Mesh-Projekte

In diesem Artikel wird veranschaulicht, wie Sie die Leistung von Visual Studio für Service Fabric Mesh-Projekte optimieren, damit Ihr erster Debuglauf (F5) deutlich schneller durchgeführt wird.  

## <a name="change-visual-studio-settings"></a>Ändern der Visual Studio-Einstellungen
 
In Visual Studio können Sie unter **Extras** > **Optionen**  > **Service Fabric Mesh-Tools** > **Allgemein** die folgenden Einstellungen anpassen:

- Mit der Option **Erforderliche Docker-Images beim Laden des Projekts mithilfe von Pull übertragen** wird Ihr erster Debuglauf (F5) schneller durchgeführt, indem das Herunterladen des Images während des Ladevorgangs für das Projekt gestartet wird.  
- Mit der Option **Deploy application on project open** (Anwendung beim Öffnen des Projekts bereitstellen) kann Ihr erster Debuglauf (F5) beschleunigt werden, indem der Bereitstellungsprozess gestartet wird, nachdem das Projekt geöffnet wurde.  
- Mit der Option **Remove application on project close** (Anwendung beim Schließen des Projekts entfernen) können die der App zugeordneten Ressourcen (CPU, RAM) wieder beansprucht werden, indem die Mesh-App beim Schließen des Projekts entfernt wird.  

Wenn im Ausgabefenster der Service Fabric-Tools Meldungen mit dem Hinweis angezeigt werden, dass Visual Studio „Images überträgt“, „sich in der Aufwärmphase befindet“ oder „die Anwendung entfernt“, bezieht sich dies auf die obigen Einstellungen. Sie können diese Einstellungen deaktivieren.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Tutorial: Debuggen einer Service Fabric Mesh-Anwendung, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).