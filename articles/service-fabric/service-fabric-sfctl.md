---
title: Azure Service Fabric CLI – sfctl | Microsoft-Dokumentation
description: Beschreibt die sfctl-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: c195d0c4250022102e735cf584370278e354bc41
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60545043"
---
# <a name="sfctl"></a>sfctl
Befehle zum Verwalten von Service Fabric-Clustern und -Entitäten. Diese Version ist mit der Service Fabric Runtime 6.4 kompatibel.

Befehle folgen dem Muster „Nomen-Verb“. Weitere Informationen finden Sie in den Untergruppen.

## <a name="subgroups"></a>Untergruppen
|Untergruppe|BESCHREIBUNG|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Ermöglicht es, Anwendungen und Anwendungstypen zu erstellen, zu löschen und zu verwalten. |
| [chaos](service-fabric-sfctl-chaos.md) | Ermöglicht es, den Chaos-Testdienst zu starten und zu beenden sowie seine Berichte anzuzeigen. |
| [cluster](service-fabric-sfctl-cluster.md) | Ermöglicht es, Service Fabric-Cluster auszuwählen, zu verwalten und zu betreiben. |
| [compose](service-fabric-sfctl-compose.md) | Ermöglicht es, Docker Compose Anwendungen zu erstellen, zu löschen und zu verwalten. |
| [container](service-fabric-sfctl-container.md) | Führt containerbezogene Befehle auf einem Clusterknoten aus. |
| [is](service-fabric-sfctl-is.md) | Ermöglicht es, Befehle an den Infrastrukturdienst abzufragen und zu senden. |
| [mesh](service-fabric-sfctl-mesh.md) | Löschen und Verwalten von Service Fabric Mesh-Anwendungen |
| [Node](service-fabric-sfctl-node.md) | Ermöglicht es, Knoten zu verwalten, die einen Cluster bilden. |
| [partition](service-fabric-sfctl-partition.md) | Ermöglicht es, Partitionen für jeden Dienst abzufragen und zu verwalten. |
| [property](service-fabric-sfctl-property.md) | Speichern und Abfragen von Eigenschaften unter Service Fabric-Namen. |
| [replica](service-fabric-sfctl-replica.md) | Ermöglicht es, Replikate zu verwalten, die zu Servicepartitionen gehören. |
| [rpm](service-fabric-sfctl-rpm.md) | Ermöglicht es, Befehle an den Reparatur-Manager-Dienst abzufragen und zu senden. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Verwaltet eigenständige Service Fabric-Cluster. |
| [service](service-fabric-sfctl-service.md) | Ermöglicht es, Dienste, Diensttypen und Dienstpakete zu erstellen, zu löschen und zu verwalten. |
| [settings](service-fabric-sfctl-settings.md) | Konfigurieren Sie Einstellungen, die für diese Instanz von sfctl lokal sind. |
| [store](service-fabric-sfctl-store.md) | Ermöglicht es, grundlegende Vorgänge auf Dateiebene für den Clusterimagespeicher auszuführen. |

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)