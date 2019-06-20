---
title: Clusterressourcen-Manager von Service Fabric – Anwendungsgruppen | Microsoft Docs
description: Übersicht über die Anwendungsgruppenfunktionalität im Clusterressourcen-Manager von Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7e90dc00a8e042e48d8016e25dda04c15ce9f619
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114072"
---
# <a name="introduction-to-application-groups"></a>Einführung in Anwendungsgruppen
Der Clusterressourcen-Manager von Service Fabric verwaltet die Clusterressourcen in der Regel durch gleichmäßiges Verteilen der Last (durch [Metriken](service-fabric-cluster-resource-manager-metrics.md) dargestellt) über den gesamten Cluster. Service Fabric verwaltet die Kapazität der Knoten im Cluster und den Cluster als Ganzes über [Kapazität](service-fabric-cluster-resource-manager-cluster-description.md). Metriken und Kapazität funktionieren hervorragend für eine Vielzahl von Workloads, aber die Muster, die intensiven Gebrauch von anderen Service Fabric-Anwendungsinstanzen machen, bringen manchmal zusätzliche Anforderungen ein. Dazu zählen beispielsweise:

- Reservieren von Kapazität auf den Knoten im Cluster für die Dienste innerhalb einer benannten Anwendungsinstanz
- Beschränken der Gesamtzahl der Knoten, auf denen die Dienste innerhalb einer benannten Anwendungsinstanz (anstatt sie im gesamten Cluster zu verteilen)
- Definieren von Kapazitäten in der benannten Anwendungsinstanz selbst, um die Anzahl der Dienste oder den Gesamtressourcenverbrauch der Dienste darin zu beschränken

Um diese Anforderungen zu erfüllen, unterstützt der Service Fabric-Clusterressourcen-Manager ein Feature namens „Anwendungsgruppen“.

## <a name="limiting-the-maximum-number-of-nodes"></a>Begrenzung der maximalen Anzahl von Knoten
Der einfachste Anwendungsfall für Anwendungskapazität ist, wenn eine Anwendungsinstanz auf eine bestimmte maximale Anzahl von Knoten beschränkt werden muss. Dadurch werden alle Dienste in dieser Anwendungsinstanz auf einer festgelegten Anzahl von Computern konsolidiert. Eine Konsolidierung ist nützlich, wenn Sie versuchen, die Nutzung physischer Ressourcen durch die Dienste in dieser benannten Anwendungsinstanz vorherzusagen oder zu begrenzen. 

Die folgende Abbildung zeigt eine Anwendungsinstanz mit und ohne eine definierte maximale Anzahl von Knoten:

<center>

![Anwendungsinstanzen – Definieren der maximalen Anzahl von Knoten][Image1]
</center>

Im linken Beispiel ist für die Anwendung keine maximale Knotenanzahl festgelegt, und sie verfügt über drei Dienste. Der Clusterressourcen-Manager hat alle Replikate über sechs verfügbare Knoten verteilt, um das optimale Gleichgewicht im Cluster (das Standardverhalten) zu erzielen. Im rechten Beispiel sehen wir die gleiche Anwendung, die auf drei Knoten beschränkt wurde.

Der Parameter MaximumNodes steuert dieses Verhalten. Dieser Parameter kann während der Anwendungserstellung festgelegt oder für eine bereits ausgeführte Anwendungsinstanz aktualisiert werden.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Innerhalb der Gruppe von Knoten garantiert der Clusterressourcen-Manager nicht, welche Dienstobjekte zusammen platziert oder welche Knoten verwendet werden.

## <a name="application-metrics-load-and-capacity"></a>Metriken, Last und Kapazität der Anwendung
Mit Anwendungsgruppen können Sie auch Metriken für eine bestimmte benannte Anwendungsinstanz sowie die Kapazität der Anwendung für diese Metriken definieren. Mit Anwendungsmetriken können Sie den Ressourcenverbrauch der Dienste innerhalb dieser Anwendungsinstanz verfolgen, reservieren und begrenzen.

Für jede Anwendungsmetrik gibt es zwei Werte, die festgelegt werden können:

- **Gesamte Anwendungskapazität**: Diese Einstellung stellt die Gesamtkapazität der Anwendung für eine bestimmte Metrik dar. Der Clusterressourcen-Manager lässt die Erstellung neuer Dienste innerhalb dieser Anwendungsinstanz nicht zu, wenn die Gesamtlast dadurch diesen Wert überschreiten würde. Nehmen wir z.B. an, die Anwendungsinstanz weist eine Kapazität von zehn und bereits eine Last von fünf auf. Die Erstellung eines Diensts mit einer Standardgesamtlast von 10 wäre nicht zulässig.
- **Maximale Knotenkapazität**: Diese Einstellung gibt die maximale Gesamtlast für die Anwendung auf einem einzelnen Knoten an. Wenn die Last diese Kapazität überschreitet, verschiebt der Clusterressourcen-Manager Replikate auf andere Knoten, um die Last zu reduzieren.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Reservieren von Kapazität
Ein weiterer üblicher Verwendungszweck für Anwendungsgruppen besteht darin sicherzustellen, dass Ressourcen innerhalb des Clusters für eine bestimmte Anwendungsinstanz reserviert werden. Der Speicherplatz wird beim Erstellen der Anwendungsinstanz immer reserviert.

Das Reservieren von Speicherplatz im Cluster für die Anwendung erfolgt sofort, selbst wenn:
- die Anwendungsinstanz erstellt wurde, aber noch keine Dienste enthält
- die Anzahl der Dienste innerhalb der Anwendungsinstanz sich jedes Mal ändert 
- die Dienste vorhanden sind, jedoch die Ressourcen nicht nutzen 

Das Reservieren von Ressourcen für eine Anwendungsinstanz erfordert die Angabe zweier zusätzlicher Parameter: *MinimumNodes* und *NodeReservationCapacity*

- **MinimumNodes**: Definiert die Mindestanzahl von Knoten, auf denen die Anwendungsinstanz ausgeführt werden soll.  
- **NodeReservationCapacity**: Diese Einstellung wird für jede Metrik innerhalb der Anwendung definiert. Der Wert ist die Menge dieser Metrik, die für die Anwendung auf jedem Knoten reserviert ist, auf dem die Dienste in der Anwendung ausgeführt werden.

Das Kombinieren von **MinimumNodes** und **NodeReservationCapacity** garantiert eine minimale Lastreservierung für die Anwendung innerhalb des Clusters. Wenn im Cluster weniger Kapazität verbleibt, als für die Gesamtreservierung erforderlich ist, schlägt das Erstellen der Anwendung fehl. 

Betrachten wir nun ein Beispiel für die Kapazitätsreservierung:

<center>

![Anwendungsinstanzen – Definieren der reservierten Kapazität][Image2]
</center>

Im linken Beispiel ist für Anwendungen keine Anwendungskapazität definiert. Der Clusterressourcen-Manager gleicht alle Komponenten den normalen Regeln entsprechend aus.

Im Beispiel auf der rechten Seite gehen wir davon aus, dass Anwendung1 mit den folgenden Einstellungen erstellt wurde:

- Festlegung von MinimumNodes auf zwei
- Definition einer Anwendungsmetrik mit
  - NodeReservationCapacity von 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserviert Kapazität auf zwei Knoten für Anwendung1 und lässt die Nutzung dieser Kapazität durch Dienste von Anwendung2 nicht zu, selbst wenn zur entsprechenden Zeit keine Last von den Diensten in Anwendung1 in Anspruch genommen wird. Diese reservierte Anwendungskapazität wird als verbraucht betrachtet und von der verbleibenden Kapazität auf diesem Knoten und innerhalb des Clusters abgezogen.  Die Reservierung wird sofort von der verbleibenden Clusterkapazität abgezogen, die Nutzung der reservierten Kapazität wird jedoch nur von der Kapazität eines bestimmten Knotens abgezogen, wenn mindestens ein Dienstobjekt darauf platziert wird. Diese spätere Reservierung ermöglicht Flexibilität und eine bessere Ressourcennutzung, da Ressourcen nur bei Bedarf auf Knoten reserviert werden.

## <a name="obtaining-the-application-load-information"></a>Abrufen von Anwendungslastinformationen
Für jede Anwendung, für die eine Anwendungskapazität für mindestens eine Metrik definiert wurde, können Sie Informationen über die aggregierte Last abrufen, die von Replikaten ihrer Dienste gemeldet werden.

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

Die ApplicationLoad-Abfrage gibt grundlegende Informationen zu der Anwendungskapazität zurück, die für die Anwendung angegeben wurde. Zu diesen Informationen gehören die Mindest- und Höchstanzahl von Knoten und die Anzahl, die derzeit von der Anwendung belegt wird. Zudem sind darin Informationen zu den einzelnen Anwendungslastmetriken enthalten:

* Metrikname: Der Name der Metrik.
* Reservierungskapazität: Die für diese Anwendung im Cluster reservierte Clusterkapazität.
* Anwendungslast: Die Gesamtlast der untergeordneten Replikate dieser Anwendung.
* Anwendungskapazität: Der maximal zulässige Wert der Anwendungslast.

## <a name="removing-application-capacity"></a>Entfernen der Anwendungskapazität
Sobald die Anwendungskapazitätsparameter für eine Anwendung festgelegt sind, können sie mit den APIs zur Anwendungsaktualisierung oder PowerShell-Cmdlets entfernt werden. Beispiel:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Dieser Befehl entfernt alle Verwaltungsparameter zur Anwendungskapazität aus der Anwendungsinstanz. Dazu zählen „MinimumNodes“, „MaximumNodes“ und ggf. die Metriken der Anwendung. Der Befehl zeigt eine sofortige Wirkung. Nach Abschluss dieses Befehls weist der Clusterressourcen-Manager das Standardverhalten für die Verwaltung von Anwendungen auf. Parameter zur Anwendungskapazität können über `Update-ServiceFabricApplication`/`System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` erneut angegeben werden.

### <a name="restrictions-on-application-capacity"></a>Einschränkungen der Anwendungskapazität
Es gibt mehrere Einschränkungen für Anwendungskapazitätsparameter, die berücksichtigt werden müssen. Wenn Überprüfungsfehler vorhanden sind, erfolgen keine Änderungen.

- Alle Integerparameter müssen positive Zahlen sein.
- MinimumNodes darf nie größer sein als MaximumNodes.
- Wenn Kapazitäten für eine Lastmetrik definiert sind, müssen sie die folgenden Regeln einhalten:
  - Die Knotenreservierungskapazität darf nie größer sein als die maximale Knotenkapazität. Sie können z.B. nicht die Kapazität für die Metrik „CPU“ auf dem Knoten auf zwei Einheiten beschränken und versuchen, drei Einheiten auf jedem Knoten zu reservieren.
  - Wenn MaximumNodes angegeben wird, darf das Produkt aus MaximumNodes und maximaler Knotenkapazität nicht größer sein als die Gesamtkapazität der Anwendung. Nehmen wir beispielsweise an, dass die maximale Knotenkapazität für die Lastmetrik „CPU“ auf acht festgelegt ist. Außerdem nehmen wir an, dass Sie die maximale Anzahl von Knoten auf 10 festlegen. In diesem Fall muss die Gesamtanwendungskapazität für diese Lastmetrik größer als 80 sein.

Die Einschränkungen werden sowohl während der Anwendungserstellung als auch während Updates erzwungen.

## <a name="how-not-to-use-application-capacity"></a>Wie Sie die Anwendungskapazität nicht verwenden sollten
- Versuchen Sie nicht, die Anwendungsgruppenfunktionen zum Einschränken der Anwendung auf eine _bestimmte_ Teilmenge von Knoten zu verwenden. Das heißt, Sie können angeben, dass die Anwendung auf höchstens fünf Knoten ausgeführt wird, aber nicht, auf welchen fünf Knoten im Cluster. Das Einschränken einer Anwendung auf bestimmte Knoten kann mithilfe von Platzierungseinschränkungen für Dienste erreicht werden.
- Verwenden Sie nicht die Anwendungskapazität, um sicherzustellen, dass zwei Dienste aus derselben Anwendung auf demselben Knoten platziert werden. Verwenden Sie stattdessen [Affinität](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) oder [Platzierungseinschränkungen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Konfigurieren von Diensten finden Sie unter [Konfigurieren von Einstellungen des Clusterressourcen-Managers für Service Fabric-Dienste](service-fabric-cluster-resource-manager-configure-services.md).
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md)
- Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md)
- Weitere Informationen zur Funktionsweise von Metriken im Allgemeinen finden Sie unter [Verwalten von Ressourcenverbrauch und Auslastung in Service Fabric mit Metriken](service-fabric-cluster-resource-manager-metrics.md)
- Der Clusterressourcen-Manager bietet zahlreiche Optionen zum Beschreiben des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
