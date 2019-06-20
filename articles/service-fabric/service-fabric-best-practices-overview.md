---
title: Bewährte Methoden für Azure Service Fabric-Anwendungen und -Cluster | Microsoft-Dokumentation
description: Bewährte Methoden für die Verwaltung von Service Fabric-Clustern und -Anwendungen.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2019
ms.author: pepogors
ms.openlocfilehash: 051d6b1129724ce4e8a67bde4e56ebe61cd832f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231369"
---
# <a name="azure-service-fabric-application-and-cluster-best-practices"></a>Bewährte Methoden für Azure Service Fabric-Anwendungen und -Cluster

Für die erfolgreiche Verwaltung von Azure Service Fabric-Anwendungen und -Clustern empfehlen wir bestimmte Vorgänge, die Sie zum Optimieren der Zuverlässigkeit Ihrer Produktionsumgebung unbedingt durchführen sollten. Führen Sie die in diesem Dokument aufgeführten Vorgänge aus, und wählen Sie eine unserer [Beispielvorlagen für Azure Service Fabric-Cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates) aus, um mit dem Entwurf Ihrer Produktionslösung zu beginnen, oder ändern Sie Ihre vorhandene Vorlage, um diese Methoden einzubinden.

## <a name="security"></a>Sicherheit 

* [Bewährte Methoden für die Sicherheit](service-fabric-best-practices-security.md)

## <a name="networking"></a>Netzwerk

* [Bewährte Methoden für Netzwerke](service-fabric-best-practices-networking.md)

## <a name="compute-planning-and-scaling"></a>Computeplanung und -skalierung

* [Bewährte Methoden für die Skalierung von Computeressourcen](service-fabric-best-practices-capacity-scaling.md)
* [Planen der Computekapazität](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity)

## <a name="infrastructure-as-code"></a>Infrastructure-as-Code

* [Bewährte Methoden für die Implementierung von Infrastructure-as-Code](service-fabric-best-practices-infrastructure-as-code.md)

## <a name="monitoring-and-diagnostics"></a>Überwachung und Diagnose

* [Bewährte Methoden für die Überwachung von Clustern und die Diagnose](service-fabric-best-practices-monitoring.md)

## <a name="application-design"></a>Anwendungsentwurf

* [Bewährte Methoden für en Anwendungsentwurf](service-fabric-best-practices-applications.md)

## <a name="checklist"></a>Checkliste

Nachdem Sie die Schritte in jedem der obigen Abschnitte ausgeführt haben, stellen Sie sicher, dass Sie alle bewährten Methoden in der Prüfliste für die Produktionsbereitschaft integriert haben:
* [Azure Service Fabric: Prüfliste für die Produktionsbereitschaft](https://docs.microsoft.com/azure/service-fabric/service-fabric-production-readiness-checklist)

## <a name="next-steps"></a>Nächste Schritte

* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Windows Server: [Erstellen eines Service Fabric-Clusters für Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Erstellen eines Clusters auf virtuellen Computern oder Computern mit Linux: [Erstellen eines Linux-Clusters](service-fabric-cluster-creation-via-portal.md)
* Problembehandlung: [Service Fabric Troubleshooting Guides](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) (Leitfäden zur Problembehandlung für Service Fabric)