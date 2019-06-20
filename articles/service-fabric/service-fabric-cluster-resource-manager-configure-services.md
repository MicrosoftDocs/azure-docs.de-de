---
title: Angeben von Metriken und Platzierungseinstellungen in Azure Service Fabric | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Service Fabric-Dienst durch Angeben von Metriken, Platzierungseinschränkungen und anderen Platzierungsrichtlinien beschreiben.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 21fcac62c9335652d0c682a6ac889be82e649464
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844141"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurieren von Einstellungen des Clusterressourcen-Managers für Service Fabric-Dienste
Der Clusterressourcen-Manager von Service Fabric ermöglicht eine detaillierte Steuerung der Regeln, die für jeden einzelnen benannten Dienst gelten. Jeder benannte Dienst kann Regeln für seine Zuordnung im Cluster angeben. Darüber hinaus kann jeder benannte Dienst die zu meldenden Metriken und deren jeweilige Bedeutung für den Dienst definieren. Zum Konfigurieren von Diensten müssen drei Aufgaben ausgeführt werden:

1. Konfigurieren von Platzierungseinschränkungen
2. Konfigurieren von Metriken
3. Konfigurieren von erweiterten Platzierungsrichtlinien und anderen Regeln (eher selten)

## <a name="placement-constraints"></a>Platzierungseinschränkungen
Platzierungseinschränkungen steuern, an welchen Knoten im Cluster ein Dienst tatsächlich ausgeführt werden kann. Hierbei handelt es sich in der Regel um eine bestimmte benannte Dienstinstanz oder um alle Dienste eines bestimmten Typs, die auf die Ausführung auf einem bestimmten Knotentyp beschränkt sind. Platzierungseinschränkungen können erweitert werden. Sie können eine beliebige Gruppe von Eigenschaften pro Knotentyp definieren und anschließend beim Erstellen von Diensten mit Einschränkungen eine Auswahl für diese treffen. Sie können die Platzierungseinschränkungen eines Diensts auch ändern, während dieser ausgeführt wird. Dadurch können Sie auf Änderungen im Cluster oder auf die Anforderungen des Diensts reagieren. Die Eigenschaften eines Knotens können auch dynamisch im Cluster aktualisiert werden. Weitere Informationen zu Platzierungseinschränkungen und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>metrics
Bei Metriken handelt es sich um einen Satz von Ressourcen, den ein bestimmter benannter Dienst benötigt. Die Metrikkonfiguration eines Diensts gibt unter anderem den Anteil der Ressource an, den jedes zustandsbehaftete Replikat und jede zustandslose Instanz dieses Diensts standardmäßig verbraucht. Metriken enthalten auch eine Gewichtung, die angibt, wie wichtig der Ausgleich dieser Metrik für den Dienst ist, falls Kompromisse erforderlich sind.

## <a name="advanced-placement-rules"></a>Fortgeschrittene Platzierungsregeln
Es gibt weitere Typen von Platzierungsregeln, die in weniger häufigen Szenarien hilfreich sein können. Hier einige Beispiele:
- Einschränkungen, die bei geografisch verteilten Clustern hilfreich sind
- Gewisse Anwendungsarchitekturen

Weitere Platzierungsregeln werden entweder über Korrelationen oder über Richtlinien konfiguriert.

## <a name="next-steps"></a>Nächste Schritte
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Affinität ist ein Modus, den Sie für Ihre Dienste konfigurieren können. Er ist nicht üblich, aber bei Bedarf erhalten Sie [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Es gibt viele verschiedene Platzierungsregeln, die für Ihren Dienst konfiguriert werden können, um zusätzliche Szenarien zu verarbeiten. Informationen zu diesen verschiedenen Platzierungsrichtlinien finden Sie [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md)
- Der Clusterressourcen-Manager bietet zahlreiche Optionen zum Beschreiben des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).
