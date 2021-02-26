---
title: Skalierbarkeit von Azure Service Fabric Mesh-Apps
description: Einer der Vorteile bei der Bereitstellung von Anwendungen in Service Fabric Mesh ist, dass Sie Ihre Dienste einfach skalieren können, manuell oder mit Richtlinien für die automatische Skalierung.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3f0e115e596925878bf9fdd43b7074cefdba47b2
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626857"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Skalieren von Service Fabric Mesh-Anwendungen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

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
