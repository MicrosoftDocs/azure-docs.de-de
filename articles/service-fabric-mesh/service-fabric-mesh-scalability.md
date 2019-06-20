---
title: Skalierbarkeit von Azure Service Fabric Mesh-Anwendungen | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Skalieren von Diensten in Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 1688cac35ea9de43bac529a4994bd4ea55eb0ab7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811092"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalieren von Service Fabric Mesh-Anwendungen

Einer der Hauptvorteile bei der Bereitstellung von Anwendungen in Service Fabric Mesh ist die Möglichkeit, Ihre Dienste ganz einfach horizontal hoch- und herunterzuskalieren. Auf diese Weise können Sie unterschiedlich starke Dienstauslastungen auffangen oder die Verfügbarkeit verbessern. Sie können Ihre Dienste manuell herunter- oder hochskalieren oder Richtlinien für die automatische Skalierung einrichten.

## <a name="manual-scaling-instances"></a>Manuelles Skalieren von Instanzen

In der Bereitstellungsvorlage für die Anwendungsressource weist jeder Dienst eine Eigenschaft *replicaCount* auf, mit der Sie festlegen können, wie oft der Dienst bereitgestellt werden soll. Eine Anwendung kann aus mehreren zusammen bereitgestellten und verwalteten Diensten bestehen, wobei jeder Dienst eine eindeutige *replicaCount*-Zahl aufweist. Um die Anzahl von Dienstreplikaten zu skalieren, ändern Sie für jeden Dienst, der skaliert werden soll, den *replicaCount*-Wert in der Bereitstellungsvorlage oder in der Parameterdatei. Führen Sie anschließend ein Upgrade der Anwendung durch.

Beispiele zum manuellen Skalieren von Dienstinstanzen finden Sie unter [Manuelles horizontales Hoch- und Herunterskalieren Ihrer Dienste](service-fabric-mesh-tutorial-template-scale-services.md).

## <a name="autoscaling-service-instances"></a>Automatische Skalierung von Dienstinstanzen
Die automatische Skalierung ist eine weitere Möglichkeit von Service Fabric, um die Anzahl Ihrer Dienstinstanzen dynamisch zu skalieren (horizontale Skalierung). Die automatische Skalierung bietet eine hohe Flexibilität und ermöglicht die Bereitstellung oder Entfernung von Dienstinstanzen basierend auf der CPU- oder Speicherauslastung.  Mithilfe der automatischen Skalierung können Sie die richtige Anzahl von Dienstinstanzen für Ihre Workload ausführen und Kosten optimieren.

Pro Dienst wird eine Richtlinie für die automatische Skalierung in der Ressourcendatei für den Dienst definiert. Jede Skalierungsrichtlinie besteht aus zwei Teilen:

- Der Skalierungstrigger beschreibt, wann die Skalierung des Diensts ausgeführt wird. Es gibt drei Faktoren, die bestimmen, wann der Dienst skaliert wird: Der *untere Lastschwellenwert* ist ein Wert, der bestimmt, wann der Dienst horizontal herunterskaliert wird. Wenn die durchschnittliche Last aller Instanzen der Partitionen niedriger als dieser Wert ist, wird der Dienst horizontal herunterskaliert. Der *obere Lastschwellenwert* ist ein Wert, der bestimmt, wann der Dienst horizontal hochskaliert wird. Wenn die durchschnittliche Last aller Instanzen der Partition höher als dieser Wert ist, wird der Dienst horizontal hochskaliert. Das *Skalierungsintervall* legt (in Sekunden) fest, wie oft der Trigger überprüft wird. Sobald der Trigger überprüft wurde, wird der Mechanismus angewendet, wenn eine Skalierung erforderlich ist. Wenn keine Skalierung erforderlich ist, wird keine Aktion ausgeführt. In beiden Fällen wird der Trigger erst wieder überprüft, wenn das Skalierungsintervall abläuft.

- Der Skalierungsmechanismus beschreibt, wie die Skalierung ausgeführt wird, wenn sie ausgelöst wurde. Das *Skalierungsinkrement* bestimmt, wie viele Instanzen hinzugefügt oder entfernt werden, wenn der Mechanismus ausgelöst wird. Die *maximale Anzahl der Instanzen* definiert die Obergrenze für die Skalierung. Wenn die Anzahl von Instanzen diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal hochskaliert. Die *minimale Anzahl der Instanzen* definiert die Untergrenze für die Skalierung. Wenn die Anzahl von Instanzen der Partition diesen Grenzwert erreicht, wird der Dienst unabhängig von der Last nicht horizontal herunterskaliert.

Lesen Sie [Automatisches Skalieren von Diensten](service-fabric-mesh-howto-auto-scale-services.md), um zu erfahren, wie Sie eine Richtlinie für die automatische Skalierung für Ihren Dienst festlegen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anwendungsmodell finden Sie unter [Service Fabric-Ressourcen](service-fabric-mesh-service-fabric-resources.md).
