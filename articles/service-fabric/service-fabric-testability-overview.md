---
title: Übersicht über den Fault Analysis Service | Microsoft Docs
description: In diesem Artikel wird der Fault Analysis Service in Service Fabric zum Auslösen von Fehlern und Ausführen von Testszenarien für Ihre Dienste beschrieben.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544812"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Einführung in den Fault Analysis Service
Der Fault Analysis Service ist für das Testen von Diensten ausgelegt, die in Microsoft Azure Service Fabric erstellt werden. Mit dem Fault Analysis Service können Sie aussagekräftige Fehler auslösen und vollständige Testszenarien für Ihre Anwendungen ausführen. Mit diesen Fehlern und Szenarien werden die verschiedenen Zustände und Übergänge durchgespielt und überprüft, die während der Lebensdauer eines Diensts auftreten – und zwar auf kontrollierte, sichere und einheitliche Weise.

Aktionen sind die individuellen Fehler, die auf einen Dienst angewendet werden, um ihn zu testen. Ein Dienstentwickler kann sie als Bausteine zum Schreiben komplizierter Szenarien verwenden. Beispiel:

* Starten Sie einen Knoten neu, um eine beliebige Anzahl von Situationen zu simulieren, in denen ein Computer oder ein virtueller Computer neu gestartet wird.
* Verschieben Sie ein Replikat Ihres zustandsbehafteten Diensts, um Lastenausgleich, Failover oder ein Anwendungsupgrade zu simulieren.
* Lösen Sie einen Quorumverlust für einen zustandsbehafteten Dienst aus, um zu bewirken, dass Schreibvorgänge nicht fortgesetzt werden können, weil nicht genügend Backupreplikate oder sekundäre Replikate zum Aufnehmen neuer Daten vorhanden sind.
* Lösen Sie einen Datenverlust für einen zustandsbehafteten Dienst aus, um eine Situation zu schaffen, in der alle In-Memory-Zustände vollständig entfernt werden.

Szenarien sind komplexe Vorgänge, die aus einem oder mehreren Vorgängen bestehen. Der Fault Analysis Services stellt zwei integrierte, vollständige Szenarien bereit:

* Chaosszenario
* Failoverszenario

## <a name="testing-as-a-service"></a>Testing as a Service
Der Fault Analysis Service ist ein Service Fabric-Systemdienst, der automatisch mit einem Service Fabric-Cluster gestartet wird. Dieser Dienst agiert als Host für die Fault Injection, die Ausführung von Testszenarien und die Integritätsanalyse. 

![Fault Analysis Service][0]

Wenn eine Fehleraktion oder ein Testszenario initiiert werden, wird ein Befehl an den Fault Analysis Service gesendet, diese Aktion bzw. dieses Szenario auszuführen. Der Fault Analysis Service ist zustandsbehaftet und kann daher zuverlässig Fehler und Szenarien ausführen und Ergebnisse überprüfen. Testszenarien mit langer Laufzeit können beispielsweise zuverlässig vom Fault Analysis Service ausgeführt werden. Und da Tests innerhalb des Clusters ausgeführt werden, kann der Dienst den Status des Clusters und Ihrer Dienste untersuchen, um genauere Informationen zu Fehlern bereitzustellen.

## <a name="testing-distributed-systems"></a>Testen von verteilten Systemen
Service Fabric vereinfacht das Schreiben und Verwalten von verteilten skalierbaren Anwendungen erheblich. Ebenso gestaltet der Fault Analysis Service das Testen einer verteilten Anwendung einfacher. Es gibt drei vorrangige Probleme, die beim Testen gelöst werden müssen:

1. Simulieren/Generieren von Fehlern, die in realen Szenarien auftreten können: Einer der wichtigsten Aspekte von Service Fabric ist, dass für verteilte Anwendungen damit die Wiederherstellung nach verschiedenen Fehlern möglich ist. Um zu testen, ob die Anwendung nach diesen Fehlern wiederhergestellt werden kann, benötigen wir ein aber Verfahren zum Simulieren bzw. Generieren dieser Fehler aus der Praxis in einer kontrollierten Testumgebung.
1. Die Möglichkeit zum Generieren korrelierter Fehler: Grundlegende Fehler im System, z.B. ein Netzwerk- oder Computerausfall, können leicht individuell ausgelöst werden. Das Generieren einer signifikanten Anzahl von Szenarien, die in der Praxis aufgrund von Interaktionen zwischen diesen Einzelfehlern auftreten können, ist dagegen nicht so einfach.
1. Einheitliche Benutzeroberfläche auf verschiedenen Entwicklungs- und Bereitstellungsebenen: Es gibt viele Systeme zur Fehlerinjektion, die verschiedene Arten von Fehlern verursachen können. Dies ist aber nicht sehr benutzerfreundlich, wenn zwischen One-Box-Entwicklerszenarien, dem Ausführen der gleichen Tests in großen Testumgebungen und dem Testen in der Produktion gewechselt wird.

Es gibt zwar viele Verfahren zum Lösen dieser Probleme, aber es fehlt ein System, das diese Aufgaben mit der erforderlichen Garantie ausführen kann – von der One-Box-Entwicklerumgebung bis zum Testen in Produktionsclustern. Dank des Fault Analysis Service können sich Anwendungsentwickler mehr auf das Testen der Geschäftslogik konzentrieren. Der Fault Analysis Service bietet alle Funktionen, die zum Testen der Interaktion des Diensts mit dem zugrunde liegenden verteilten System erforderlich sind.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulieren/Generieren von Fehlerszenarien aus der Praxis
Um die Robustheit eines verteilten Systems in Bezug auf Fehler zu testen, benötigen wir ein Verfahren zum Generieren von Fehlern. Das Generieren eines Fehlers, z. B. der Ausfall eines Knotens, ist theoretisch zwar einfach, aber dabei kommt es zu den gleichen Konsistenzproblemen, die von Service Fabric gelöst werden müssen. Wenn ein Knoten heruntergefahren werden soll, ist der folgende Workflow erforderlich:

1. Geben Sie auf dem Client eine Anforderung zum Herunterfahren des Knotens aus.
1. Senden Sie die Anforderung an den richtigen Knoten.
   
    a. Wenn der Knoten nicht gefunden wird, tritt ein Fehler auf.
   
    b. Wenn der Knoten gefunden wird, sollte die Rückgabe nur durchgeführt werden, sofern der Knoten heruntergefahren wurde.

Zum Überprüfen des Fehlers aus Testsicht muss die Information vorliegen, ob der Fehler nach der Auslösung wirklich auftritt. Die Garantie von Service Fabric besteht darin, dass der Knoten entweder ausfällt oder bereits ausgefallen ist, wenn der Befehl den Knoten erreicht. In beiden Fällen sollte der Test in der Lage sein, den Zustand und den Erfolg bzw. Misserfolg richtig zu bestimmen und dies bei der Überprüfung richtig wiederzugeben. Für ein System, das außerhalb von Service Fabric für die gleiche Gruppe von Fehlern implementiert wird, können viele Netzwerk-, Hardware- und Softwarefehler auftreten, sodass die oben beschriebene Garantie nicht erreichbar ist. Bei Auftreten der oben beschriebenen Probleme wird der Clusterzustand von Service Fabric neu konfiguriert, um diese zu umgehen. Daher können mit dem Fault Analysis Service weiterhin die richtigen Garantien gegeben werden.

### <a name="generating-required-events-and-scenarios"></a>Generieren von erforderlichen Ereignissen und Szenarien
Das konsistente Simulieren von Fehlern aus der Praxis ist schon schwierig, aber das Generieren von korrelierten Fehlern ist noch deutlich aufwendiger. Beispielsweise tritt für einen zustandsbehafteten dauerhaften Dienst ein Datenverlust auf, wenn Folgendes passiert:

1. Bei der Replikation wird nur ein Schreibquorum der Replikate berücksichtigt. Alle sekundären Replikate hinken dem primären Replikat hinterher.
1. Das Schreibquorum fällt aus, weil die Replikate ausfallen (aufgrund eines Ausfalls des Codepakets oder Knotens).
1. Das Schreibquorum kann nicht wiederhergestellt werden, weil die Daten für die Replikate verloren gegangen sind (aufgrund einer Beschädigung der Festplatte oder eines Reimagings des Computers).

Diese korrelierten Fehler kommen auch in der Praxis vor, aber nicht so häufig wie Einzelfehler. Es ist wichtig, dass diese Szenarien getestet werden können, bevor sie in der Produktion auftreten. Noch wichtiger ist die Möglichkeit, diese Szenarien mit Produktionsworkloads in kontrolliertem Rahmen zu simulieren (am Tag, wenn alle Techniker anwesend sind). Dies ist viel besser, als wenn der Fehler zum ersten Mal um 2 Uhr morgens in der Produktion auftritt.

### <a name="unified-experience-across-different-environments"></a>Einheitliche Benutzeroberfläche in unterschiedlichen Umgebungen
Bisher wurde so vorgegangen, dass drei unterschiedliche Benutzeroberflächen erstellt wurden: eine für die Entwicklungsumgebung, eine für Tests und eine für die Produktion. Das Modell lautete wie folgt:

1. In der Entwicklungsumgebung werden Statusübergänge produziert, mit denen Komponententests einzelner Methoden durchgeführt werden können.
1. In der Testumgebung werden Fehler produziert, um umfassende Tests mit unterschiedlichen Fehlerszenarien zu ermöglichen.
1. Die Produktionsumgebung wird nicht beeinträchtigt, alle nicht natürlichen Fehler werden verhindert, und es wird sichergestellt, dass eine extrem schnelle menschliche Reaktion auf Fehler erfolgt.

In Service Fabric haben wir mit dem Fault Analysis Service einen neuen Ansatz gewählt und nutzen von der Entwicklerumgebung bis zur Produktion dieselbe Methodik. Es gibt zwei Möglichkeiten, dies zu erreichen:

1. Um kontrollierte Fehler auszulösen, werden die Fault Analysis Service-APIs von einer One-Box-Umgebung bis zu den Produktionsclustern durchgängig verwendet.
1. Verwenden Sie den Fault Analysis Service, um automatische Fehler zu generieren und so für den Cluster das automatische Auslösen von Fehlern zu bewirken. Das Steuern der Fehlerrate per Konfiguration ermöglicht es, dass derselbe Dienst in verschiedenen Umgebungen auf unterschiedliche Weise getestet wird.

Bei Service Fabric ist das eigentliche Verfahren identisch, auch wenn das Ausmaß der Fehler in den unterschiedlichen Umgebungen variieren würde. Dies ermöglicht eine deutlich schnellere Pipeline vom Code bis zur Bereitstellung und das Testen von Diensten mit praxisnahen Auslastungen.

## <a name="using-the-fault-analysis-service"></a>Verwenden des Fault Analysis Service
**C#**

Fault Analysis Service-Features befinden sich im System.Fabric-Namespace im NuGet-Paket „Microsoft.ServiceFabric“. Fügen Sie das NuGet-Paket als Referenz in Ihr Projekt ein, um die Fault Analysis Service-Features zu nutzen.

**PowerShell**

Um PowerShell nutzen zu können, müssen Sie das Service Fabric SDK installieren. Nach der Installation des SDK wird das ServiceFabric-PowerShell-Modul automatisch geladen.

## <a name="next-steps"></a>Nächste Schritte
Zum Erstellen echter Dienste für die Cloud müssen Sie vor und nach der Bereitstellung unbedingt sicherstellen, dass die Dienste für die in der Praxis auftretenden Fehler gerüstet sind. Für Dienste ist es heutzutage wichtig, dass schnelle Innovationen möglich sind und Code schnell in die Produktion verlagert werden kann. Genau bei dieser Aufgabe werden Dienstentwickler von Fault Analysis Service unterstützt.

Beginnen Sie damit, Ihre Anwendungen und Dienste mithilfe der integrierten [Testszenarien](service-fabric-testability-scenarios.md) zu testen, oder erstellen Sie eigene Testszenarien mithilfe der vom Fault Analysis Service bereitgestellten [Fehleraktionen](service-fabric-testability-actions.md).

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
