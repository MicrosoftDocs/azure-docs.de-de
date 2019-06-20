---
title: Upgraden eines eigenständigen Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Upgrade für die Version oder Konfiguration eines eigenständigen Azure Service Fabric-Clusters durchführen.  T
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 1d96a2e81917af5e80bb847ea25610ccb71ad70f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711055"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Upgrade und Update von eigenständigen Service Fabric-Clustern

Moderner Systeme müssen upgradefähig sein, um den langfristigen Erfolg Ihres Produkts zu gewährleisten. Ein eigenständiger Azure Service Fabric-Cluster ist eine Ressource, die sich in Ihrem Besitz befindet. In diesem Artikel wird beschrieben, für welche Elemente ein Upgrade oder Update ausgeführt werden kann.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Steuern der in Ihrem Cluster ausgeführten Fabric-Version
Achten Sie darauf, dass in Ihrem Cluster immer eine [unterstützte Service Fabric-Version](service-fabric-versions.md) ausgeführt wird. Wenn Microsoft eine neue Service Fabric-Version ankündigt, beträgt die verbleibende Supportdauer der vorherigen Version noch mindestens 60 Tage ab dem Datum der Ankündigung. Neue Releases werden im [Blog des Service Fabric-Teams](https://blogs.msdn.microsoft.com/azureservicefabric/) angekündigt. Ab dann kann das neue Release ausgewählt werden.

Sie können Ihren Cluster so konfigurieren, dass er Fabric-Upgrades automatisch erhält, wenn diese von Microsoft veröffentlicht werden. Alternativ können Sie manuell eine unterstützte Fabric-Version auswählen, die in Ihrem Cluster verwendet werden soll. Weitere Informationen finden Sie unter [Upgraden der in Ihrem Cluster ausgeführten Service Fabric-Version](service-fabric-cluster-upgrade-windows-server.md).

## <a name="customize-configuration-settings"></a>Anpassen der Konfigurationseinstellungen

Viele verschiedene [Konfigurationseinstellungen](service-fabric-cluster-manifest.md) wie die Zuverlässigkeitsstufe des Clusters und Knoteneigenschaften können in der Datei *ClusterConfig.json* angepasst werden.  Weitere Informationen finden Sie unter [Upgraden der Konfiguration eines eigenständigen Clusters](service-fabric-cluster-config-upgrade-windows-server.md).  Viele weitere, komplexere Einstellungen können ebenfalls angepasst werden.  Weitere Informationen finden Sie unter [Anpassen von Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md).

## <a name="define-node-properties"></a>Definieren von Knoteneigenschaften
Manchmal möchten Sie sicherstellen, dass bestimmte Workloads nur auf bestimmten Knotentypen im Cluster ausgeführt werden. Für einige Workloads sind beispielsweise GPUs oder SSDs erforderlich, für andere hingegen nicht. Für jeden der Knotentypen in einem Cluster können Sie den Clusterknoten benutzerdefinierte Knoteneigenschaften hinzufügen. Platzierungseinschränkungen sind die Anweisungen, die an einzelne Dienste angefügt sind und eine oder mehrere Knoteneigenschaften auswählen. Platzierungseinschränkungen definieren, an welcher Stelle Dienste ausgeführt werden sollen.

Ausführliche Informationen zur Verwendung von Platzierungseinschränkungen, Knoteneigenschaften und deren Definition finden Sie unter [Knoteneigenschaften und Platzierungseinschränkungen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).
 

## <a name="add-capacity-metrics"></a>Hinzufügen von Kapazitätsmetriken
Für die einzelnen Knotentypen können Sie benutzerdefinierte Kapazitätsmetriken hinzufügen, die Sie in Ihrer Anwendung zum Melden der Auslastung verwenden möchten. Ausführliche Informationen zur Verwendung von Kapazitätsmetriken zum Melden der Auslastung finden Sie in den Dokumenten zum Clusterressourcen-Manager von Service Fabric unter [Beschreiben Ihres Clusters](service-fabric-cluster-resource-manager-cluster-description.md) und [Metriken und Auslastung](service-fabric-cluster-resource-manager-metrics.md).

## <a name="patch-the-os-in-the-cluster-nodes"></a>Betriebssystempatches für Clusterknoten
Die Anwendung für die Patchorchestrierung (POA) ist eine Service Fabric-Anwendung, mit der das Aufspielen von Betriebssystempatches in einem Service Fabric-Cluster ohne Ausfallzeiten automatisiert werden kann. Die [Anwendung für die Patchorchestrierung für Windows](service-fabric-patch-orchestration-application.md) kann in Ihrem Cluster bereitgestellt werden, um Patches auf orchestrierte Weise zu installieren und gleichzeitig dafür zu sorgen, dass die Dienste jederzeit verfügbar bleiben. 


## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über die Vorgehensweise zum Anpassen einiger [Customize Service Fabric cluster settings and Fabric Upgrade policy](service-fabric-cluster-fabric-settings.md)
* Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](service-fabric-cluster-scale-up-down.md)
* Machen Sie sich mit [Anwendungsupgrades](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
