---
title: Service Fabric-Anwendungsupgrade | Microsoft Docs
description: Dieser Artikel bietet eine Einführung in das Upgrade einer Service Fabric-Anwendung, einschließlich Wahl des Upgrademodus und der Durchführung der Integritätsüberprüfungen.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: e2b407733bcab7bc854e8e3703e53eb474f3425b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60615070"
---
# <a name="service-fabric-application-upgrade"></a>Service Fabric-Anwendungsupgrade
Eine Azure Service Fabric Anwendung ist eine Sammlung von Diensten. Während eines Upgrades vergleicht Service Fabric das neue [Anwendungsmanifest](service-fabric-application-and-service-manifests.md) mit der vorherigen Version und ermittelt, welche Dienste in der Anwendung aktualisiert werden müssen. Service Fabric vergleicht die Versionsnummern in den Dienstmanifesten mit den Versionsnummern in der vorherigen Version. Wenn sich ein Dienst nicht geändert hat, wird er nicht aktualisiert.

## <a name="rolling-upgrades-overview"></a>Übersicht über parallele Upgrades
Bei einem parallelen Anwendungsupgrade wird das Upgrade in verschiedenen Phasen durchgeführt. In jeder Phase wird das Upgrade auf eine Teilmenge von Knoten im Cluster angewendet, die als Updatedomäne bezeichnet wird. So bleibt die Anwendung während des gesamten Upgrades immer verfügbar. Während des Upgrades kann der Cluster eine Mischung aus der alten und neuen Version enthalten.

Aus diesem Grund müssen die beiden Versionen aufwärts- und abwärtskompatibel sein. Wenn sie nicht kompatibel sind, muss der Anwendungsadministrator ein mehrstufiges Upgrade bereitstellen, um die Verfügbarkeit aufrechtzuerhalten. Bei einem Upgrade in mehreren Phasen ist der erste Schritt das Aktualisieren einer Zwischenversion der Anwendung, die mit der vorherigen Version kompatibel ist. Der zweite Schritt ist das Upgrade auf die endgültige Version, die mit der Version vor dem Update nicht mehr, jedoch mit der Zwischenversion kompatibel ist.

Updatedomänen werden im Clustermanifest angegeben, wenn Sie den Cluster konfigurieren. Updatedomänen erhalten Updates nicht in einer bestimmten Reihenfolge. Bei einer Updatedomäne handelt es sich um eine logische Bereitstellungseinheit für eine Anwendung. Updatedomänen ermöglichen eine gleichbleibende Hochverfügbarkeit der Dienste während eines Upgrades.

Nicht parallele Upgrades sind möglich, wenn das Upgrade auf alle Knoten im Cluster angewendet wird. Dies ist der Fall, wenn die Anwendung nur über eine Updatedomäne verfügt. Von diesem Ansatz wird jedoch abgeraten, da der Dienst zum Zeitpunkt des Upgrades nicht aktiv und nicht verfügbar ist. Darüber hinaus übernimmt Azure keine Garantie, wenn ein Cluster mit nur einer Updatedomäne eingerichtet wird.

Nach Abschluss des Upgrades verbleiben alle Dienste und Replikate (Instanzen) in derselben Version. Dies bedeutet: Wenn das Upgrade erfolgreich durchgeführt wird, werden sie auf die neue Version aktualisiert. Wenn beim Upgrade ein Fehler auftritt und ein Rollback durchgeführt wird, werden sie auf die alte Version zurückgesetzt.

## <a name="health-checks-during-upgrades"></a>Integritätsprüfungen bei Upgrades
Für ein Upgrade müssen Integritätsrichtlinien festgelegt (oder Standardwerte verwendet) werden. Ein Upgrade wird als erfolgreich bezeichnet, wenn alle Updatedomänen innerhalb der angegebenen Timeouts aktualisiert werden und wenn alle Updatedomänen als fehlerfrei gelten.  Eine fehlerfreie Updatedomäne bedeutet, dass die Aktualisierungsdomäne alle in der Integritätsrichtlinie angegebenen Integritätsprüfungen bestanden hat. Eine Integritätsrichtlinie kann beispielsweise vorgeben, dass alle Dienste in einer Anwendungsinstanz *fehlerfrei*laufen müssen, so wie die Integrität von Service Fabric definiert ist.

Integritätsrichtlinien und -prüfungen während eines Upgrades von Service Fabric sind dienst- und anwendungsunabhängig. Das heißt, es werden keine dienstspezifischen Tests durchgeführt.  Beispiel: Für Ihren Dienst gilt ggf. eine Durchsatzanforderung, doch Service Fabric hat nicht die Informationen zum Überprüfen des Durchsatzes. Nähere Informationen zu den durchgeführten Tests finden Sie in den [Artikeln zur Integrität](service-fabric-health-introduction.md). Die Überprüfungen, die während eines Upgrades durchgeführt werden, beinhalten Tests, die prüfen, ob das Anwendungspaket richtig kopiert wurde, ob die Instanz gestartet wurde, und so weiter.

Die Anwendungsintegrität ist eine Aggregation der untergeordneten Entitäten der Anwendung. Kurz gesagt evaluiert Service Fabric die Integrität der Anwendung über die für die Anwendung gemeldete Integrität. Es wird auch die Integrität aller Dienste für die Anwendung auf diese Weise ausgewertet. Service Fabric wertet ebenso die Integrität der Anwendungsdienste durch Aggregation der Integritätswerte der zugehörigen untergeordneten Elemente wie z. B. der Dienstreplikate aus. Sobald die Anwendungsintegritätsrichtlinie erfüllt ist, kann das Upgrade fortgesetzt werden. Wenn die Integritätsrichtlinie verletzt wird, schlägt die Aktualisierung der Anwendung fehl.

## <a name="upgrade-modes"></a>Upgrademodi
Der Modus, den wir für Upgrades von Anwendungen empfehlen, ist der überwachte Modus, der üblicherweise verwendet wird. Im überwachten Modus wird das Update zunächst in einer Updatedomäne ausgeführt. Wenn alle Integritätsprüfungen erfolgreich durchgeführt wurden (entsprechend der angegebenen Richtlinie), wird automatisch die nächste Updatedomäne aktualisiert.  Wenn die Integritätsprüfungen fehlschlagen und/oder Timeouts überschritten werden, wird das Upgrade für die Updatedomäne entweder zurückgesetzt oder der Modus in den nicht überwachten manuellen Modus geändert. Sie können die Upgrades so konfigurieren, dass einer dieser beiden Modi bei misslungenen Upgrades verwendet wird. 

Der nicht überwachte manuelle Modus benötigt nach jedem Upgrade in einer Updatedomäne einen manuellen Eingriff, um das Upgrade für die nächste Updatedomäne zu starten. Es werden keine Service Fabric-Integritätsprüfungen ausgeführt. Der Administrator überprüft den Zustand oder Status vor dem Upgrade in der nächsten Updatedomäne.

## <a name="upgrade-default-services"></a>Durchführen eines Upgrades von Standarddiensten
Einige im [Anwendungsmanifest](service-fabric-application-and-service-manifests.md) definierte Standarddienstparameter können auch als Teil eines Anwendungsupgrades aktualisiert werden. Es können nur die Dienstparameter, die über [Update-ServiceFabricService](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) geändert werden können, als Teil eines Upgrades geändert werden. Das Ändern von Standarddiensten während des Anwendungsupgrades gestaltet sich wie folgt:

1. Standarddienste im neuen Anwendungsmanifest, die im Cluster nicht bereits vorhanden sind, werden erstellt.
2. Standarddienste, die im vorherigen und im neuen Anwendungsmanifest vorhanden sind, werden aktualisiert. Die Parameter des vorhandenen Diensts werden durch die Parameter des Standarddiensts im neuen Anwendungsmanifest überschrieben. Das Anwendungsupgrade wird bei einem Fehler beim Aktualisieren des Standarddiensts automatisch zurückgesetzt.
3. Die im neuen Anwendungsmanifest nicht vorhandenen Standarddienste werden gelöscht, wenn sie im Cluster vorhanden sind. **Beachten Sie, dass das Löschen eines Standarddiensts dazu führt, dass alle Status des Diensts gelöscht werden und dass der Löschvorgang nicht rückgängig gemacht werden kann.**

Wenn ein Anwendungsupgrade zurückgesetzt wird, werden die Standarddienstparameter auf ihre alten Werte vor dem Start des Upgrades zurückgesetzt. Gelöschte Dienste können jedoch nicht mit ihrem alten Status neu erstellt werden.

> [!TIP]
> Die Clusterkonfigurationseinstellung [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) muss auf *true* festgelegt werden, damit die Regeln 2) und 3) oben (Aktualisieren und Löschen von Standarddiensten) gelten. Dieses Feature wird ab Service Fabric Version 5.5 unterstützt.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Aktualisieren von mehreren Anwendungen mit HTTPS-Endpunkten
Sie müssen darauf achten, nicht **denselben Port** für verschiedene Instanzen der gleichen Anwendung zu verwenden, wenn Sie HTTP**S** verwenden. Der Grund ist, dass Service Fabric das Zertifikat für eine der Anwendungsinstanzen nicht aktualisieren kann. Wenn beispielsweise sowohl Anwendung 1 als auch Anwendung 2 ihr Zertifikat 1 auf Zertifikat 2 aktualisieren möchten. Wenn das Upgrade erfolgt, könnte Service Fabric möglicherweise die Zertifikat-1-Registrierung mit http.sys bereinigt haben, obwohl die andere Anwendung es immer noch verwendet. Um dies zu verhindern, erkennt Service Fabric, dass bereits eine andere Anwendungsinstanz auf dem Port mit dem Zertifikat registriert ist (aufgrund von http.sys) und der Vorgang schlägt fehl.

Da Service Fabric das Aktualisieren von zwei verschiedenen Diensten mit **dem gleichen Port** in verschiedenen Anwendungsinstanzen nicht unterstützt. Mit anderen Worten: Sie können das gleiche Zertifikat nicht für verschiedene Dienste auf demselben Port verwenden. Falls Sie ein gemeinsam genutztes Zertifikat auf demselben Port benötigen, müssen Sie sicherstellen, dass die Dienste auf unterschiedlichen Computern mit Platzierungseinschränkungen platziert werden. Oder verwenden Sie nach Möglichkeit, für jeden Dienst in jeder Anwendungsinstanz, dynamische Ports von Service Fabric. 

Eine Fehlermeldung wird Sie darauf hingewiesen, wenn ein Upgrade mit HTTPS ausfällt: „Der Windows-HTTP-Server-API unterstützt mehrere Zertifikate für Anwendungen mit einem gemeinsamen Port nicht.“

## <a name="application-upgrade-flowchart"></a>Flussdiagramm eines Anwendungsupgrades
Das Flussdiagramm unter diesem Absatz kann Ihnen dabei helfen, den Upgradevorgang einer Service Fabric-Anwendung zu verstehen. Im Speziellen ist hier schematisch dargestellt, wie die Timeouts, z.B. *HealthCheckStableDuration*, *HealthCheckRetryTimeout* und *UpgradeHealthCheckInterval*, beim Steuern helfen, wenn das Upgrade in einer Updatedomäne als erfolgreich oder fehlgeschlagen gilt.

![Upgradevorgang für eine Service Fabric-Anwendung][image]

## <a name="next-steps"></a>Nächste Schritte
[Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.

In [Upgrade Ihrer Anwendung mithilfe von PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) werden Sie schrittweise durch das Upgrade der Anwendung mithilfe von PowerShell geführt.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Machen Sie Ihre Anwendungsupgrades kompatibel, indem Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)vertraut machen.

Informieren Sie sich in [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)darüber, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
