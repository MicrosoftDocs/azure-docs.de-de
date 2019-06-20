---
title: Übersicht über den Lebenszyklus von Azure Service Fabric Reliable Services | Microsoft-Dokumentation
description: Informationen zu den verschiedenen Lebenszyklusereignissen in Service Fabric Reliable Services
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723557"
---
# <a name="reliable-services-lifecycle-overview"></a>Übersicht über den Lebenszyklus von Reliable Services
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java unter Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Bei der Betrachtung der Lebenszyklen von Azure Service Fabric Reliable Services sind vor allem die Grundlagen zum Lebenszyklus relevant. Im Allgemeinen enthält der Lebenszyklus Folgendes:

- Während des Starts:
  - Dienste werden erstellt.
  - Die Dienste können keinen oder mehrere Listener erstellen und zurückgeben.
  - Alle zurückgegebenen Listener werden geöffnet und ermöglichen so eine Kommunikation mit dem Dienst.
  - Die Methode **RunAsync** des Diensts wird aufgerufen und ermöglicht dem Dienst die Ausführung von langwierigen Aufgaben oder von Hintergrundaufgaben.
- Während des Herunterfahrens:
  - Das an **RunAsync** übergebene Abbruchtoken wird abgebrochen, und die Listener werden geschlossen.
  - Sobald die Listener geschlossen sind, wird das Dienstobjekt selbst zerstört.

Es gibt Informationen zur genauen Reihenfolge dieser Ereignisse. Die Reihenfolge der Ereignisse kann sich geringfügig ändern, je nachdem, ob der zuverlässige Dienst zustandslos oder zustandsbehaftet ist. Für zustandsbehaftete Dienste müssen wir uns außerdem mit dem Tauschszenario für primäre Replikate befassen. Während dieser Sequenz wird die Rolle des primären Replikats auf ein anderes Replikat (oder wieder zurück) übertragen, ohne dass der Dienst heruntergefahren wird. Schließlich müssen wir uns mit Fehlerbedingungen beschäftigen.

## <a name="stateless-service-startup"></a>Start zustandsloser Dienste
Der Lebenszyklus eines zustandslosen Diensts ist einfach. So sieht die Reihenfolge der Ereignisse aus:

1. Der Dienst wird erstellt.
2. Gleichzeitig geschehen zwei Dinge:
    - `StatelessService.CreateServiceInstanceListeners()` wird aufgerufen, und alle zurückgegebenen Listener werden geöffnet. `ICommunicationListener.OpenAsync()` wird für jeden Listener aufgerufen.
    - Die Methode `StatelessService.RunAsync()` des Diensts wird aufgerufen.
3. Die Methode `StatelessService.OnOpenAsync()` des Diensts wird aufgerufen (sofern vorhanden). Dieser Aufruf ist eine ungewöhnliche Außerkraftsetzung, aber verfügbar. Erweiterte Serviceinitialisierungsaufgaben können zu diesem Zeitpunkt gestartet werden.

Beachten Sie, dass es keine Reihenfolge zwischen den Aufrufen zum Erstellen und Öffnen der Listener und **RunAsync** gibt. Die Listener können vor dem Start von **RunAsync** geöffnet werden. Sie können **RunAsync** allerdings auch aufrufen, bevor die Kommunikationslistener geöffnet oder überhaupt erstellt wurden. Wenn eine Synchronisierung erforderlich ist, wird diese dem Implementierer als Übung überlassen. Hier sind einige gängige Lösungen:

  - In einigen Fällen funktionieren Listener erst dann, wenn andere Informationen erstellt oder Aufgaben durchgeführt wurden. Bei zustandslosen Diensten können diese Aufgaben üblicherweise an anderen Orten ausgeführt werden, beispielsweise an den folgenden: 
    - Im Konstruktor des Diensts
    - Im Rahmen des `CreateServiceInstanceListeners()`-Aufrufs
    - Bei der Erstellung des eigentlichen Listeners
  - In einigen Fällen startet der Code in **RunAsync** erst, wenn die Listener geöffnet sind. In diesem Fall ist zusätzliche Koordination erforderlich. Eine gängige Lösung ist ein Flag im Listener, das angibt, wann die Ausführung beendet ist. Dieses Flag wird dann in **RunAsync** überprüft, bevor mit der eigentlichen Arbeit fortgefahren wird.

## <a name="stateless-service-shutdown"></a>Herunterfahren zustandsloser Dienste
Beim Herunterfahren eines zustandslosen Diensts wird dasselbe Muster in umgekehrter Reihenfolge befolgt:

1. Gleichzeitig:
    - Alle geöffneten Listener werden geschlossen. `ICommunicationListener.CloseAsync()` wird für jeden Listener aufgerufen.
    - Das an `RunAsync()` übergebene Abbruchtoken wird abgebrochen. Eine Überprüfung der Eigenschaft `IsCancellationRequested` des Abbruchtokens gibt „true“ zurück, und die Methode `ThrowIfCancellationRequested` des Tokens löst eine Ausnahme vom Typ `OperationCanceledException` aus, sofern sie aufgerufen wird.
2. Nach Abschluss von `CloseAsync()` für die einzelnen Listener und nach Abschluss von `RunAsync()` wird die Methode `StatelessService.OnCloseAsync()` des Diensts aufgerufen (sofern vorhanden).  „OnCloseAsync“ wird aufgerufen, wenn die Instanz des zustandslosen Diensts ordnungsgemäß beendet wird. Dies kann der Fall sein, wenn Code für den Dienst aktualisiert, die Dienstinstanz aufgrund des Lastenausgleichs verschoben oder ein vorübergehender Fehler erkannt wird. Es ist nicht üblich, `StatelessService.OnCloseAsync()` außer Kraft zu setzen. Die Methode kann jedoch verwendet werden, um Ressourcen sicher zu schließen, die Hintergrundverarbeitung anzuhalten, das Speichern des externen Status zu beenden oder bestehende Verbindungen zu deaktivieren.
3. Nach Abschluss von `StatelessService.OnCloseAsync()` wird das Dienstobjekt zerstört.

## <a name="stateful-service-startup"></a>Start zustandsbehafteter Dienste
Zustandsbehaftete Dienste befolgen mit wenigen Änderungen ein ähnliches Muster wie zustandslose Dienste. Beim Starten eines zustandsbehafteten Diensts lautet die Reihenfolge der Ereignisse wie folgt:

1. Der Dienst wird erstellt.
2. `StatefulServiceBase.OnOpenAsync()` wird aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.
3. Folgende Vorgänge finden parallel statt:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` wird aufgerufen. 
      - Falls es sich bei dem Dienst um einen primären Dienst handelt, werden alle zurückgegebenen Listener geöffnet. `ICommunicationListener.OpenAsync()` wird für jeden Listener aufgerufen.
      - Bei einem sekundären Dienst werden nur als `ListenOnSecondary = true` gekennzeichnete Listener geöffnet. Für sekundäre Dienste geöffnete Listener sind eher selten.
    - Falls es sich bei dem Dienst gerade um einen primären Dienst handelt, wird die Methode `StatefulServiceBase.RunAsync()` des Diensts aufgerufen.
4. Nachdem alle Replikatlisteneraufrufe von `OpenAsync()` abgeschlossen wurden und `RunAsync()` aufgerufen wurde, wird `StatefulServiceBase.OnChangeRoleAsync()` aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

Ähnlich wie bei zustandslosen Diensten wird die Reihenfolge, in der die Listener erstellt und geöffnet werden und **RunAsync** aufgerufen wird, nicht koordiniert. Wenn Sie eine Koordinierung benötigen, sind die Lösungen nahezu identisch. Es gibt einen zusätzlichen Fall für einen zustandsbehafteten Dienst. Angenommen, die bei den Kommunikationslistenern eingehenden Aufrufe benötigen Informationen aus [zuverlässigen Sammlungen](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Da die Kommunikationslistener geöffnet werden könnten, bevor die zuverlässigen Sammlungen gelesen oder geschrieben werden können oder **RunAsync** gestartet werden konnte, ist einige zusätzliche Koordination erforderlich. Die einfachste und gängigste Lösung besteht darin, dass die Kommunikationslistener einen Fehlercode zurückgeben, anhand dessen der Client feststellt, dass die Anforderung wiederholt werden muss.

## <a name="stateful-service-shutdown"></a>Herunterfahren zustandsbehafteter Dienste
Wie bei zustandslosen Diensten stimmen die Lebenszyklusereignisse während des Herunterfahrens mit denen während des Starts überein, aber in umgekehrter Reihenfolge. Wenn ein zustandsbehafteter Dienst heruntergefahren wird, treten die folgenden Ereignisse auf:

1. Gleichzeitig:
    - Alle geöffneten Listener werden geschlossen. `ICommunicationListener.CloseAsync()` wird für jeden Listener aufgerufen.
    - Das an `RunAsync()` übergebene Abbruchtoken wird abgebrochen. Eine Überprüfung der Eigenschaft `IsCancellationRequested` des Abbruchtokens gibt „true“ zurück, und die Methode `ThrowIfCancellationRequested` des Tokens löst eine Ausnahme vom Typ `OperationCanceledException` aus, sofern sie aufgerufen wird.
2. Nach Abschluss von `CloseAsync()` für die einzelnen Listener und nach Abschluss von `RunAsync()` wird die Methode `StatefulServiceBase.OnChangeRoleAsync()` des Diensts aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

   > [!NOTE]  
   > Der Abschluss von **RunAsync** muss nur abgewartet werden, wenn dieses Replikat ein primäres Replikat ist.

3. Nach Abschluss der Methode `StatefulServiceBase.OnChangeRoleAsync()` wird die Methode `StatefulServiceBase.OnCloseAsync()` aufgerufen. Dieser Aufruf ist eine ungewöhnliche Außerkraftsetzung, aber verfügbar.
3. Nach Abschluss von `StatefulServiceBase.OnCloseAsync()` wird das Dienstobjekt zerstört.

## <a name="stateful-service-primary-swaps"></a>Tausch des primären Replikats bei zustandsbehafteten Diensten
Während ein zustandsbehafteter Dienst ausgeführt wird, werden nur für die primären Replikate dieser zustandsbehafteten Dienste die Kommunikationslistener geöffnet und die Methode **RunAsync** aufgerufen. Die sekundären Replikate werden erstellt, erhalten jedoch keine weiteren Aufrufe. Während ein zustandsbehafteter Dienst ausgeführt wird, kann sich das Replikat, das zurzeit das primäre Replikat ist, als Ergebnis eines Fehlers oder einer Clusterausgleichsoptimierung ändern. Was bedeutet dies im Hinblick auf die für ein Replikat sichtbaren Lebenszyklusereignisse? Das für das zustandsbehaftete Replikat sichtbare Verhalten hängt davon ab, ob das Replikat während des Tauschs höher oder tiefer gestuft wird.

### <a name="for-the-primary-thats-demoted"></a>Für das tiefer gestufte primäre Replikat
Für das tiefer gestufte primäre Replikat muss Service Fabric die Verarbeitung von Nachrichten und jegliche Hintergrundverarbeitung beenden. Folglich ähnelt dieser Schritt dem Herunterfahren des Diensts. Ein Unterschied besteht darin, dass der Dienst nicht zerstört oder geschlossen wird, weil er als sekundäres Replikat erhalten bleibt. Die folgenden APIs werden aufgerufen:

1. Gleichzeitig:
    - Alle geöffneten Listener werden geschlossen. `ICommunicationListener.CloseAsync()` wird für jeden Listener aufgerufen.
    - Das an `RunAsync()` übergebene Abbruchtoken wird abgebrochen. Eine Überprüfung der Eigenschaft `IsCancellationRequested` des Abbruchtokens gibt „true“ zurück, und die Methode `ThrowIfCancellationRequested` des Tokens löst eine Ausnahme vom Typ `OperationCanceledException` aus, sofern sie aufgerufen wird.
2. Nach Abschluss von `CloseAsync()` für die einzelnen Listener und nach Abschluss von `RunAsync()` wird die Methode `StatefulServiceBase.OnChangeRoleAsync()` des Diensts aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

### <a name="for-the-secondary-thats-promoted"></a>Für das höher gestufte sekundäre Replikat
Analog dazu muss das höher gestufte sekundäre Replikat für Service Fabric mit dem Lauschen auf Nachrichten beginnen und alle Hintergrundaufgaben starten, die abgeschlossen werden müssen. Demzufolge ähnelt dieser Prozess dem Erstellen des Diensts, mit der Ausnahme, dass das Replikat selbst bereits vorhanden ist. Die folgenden APIs werden aufgerufen:

1. Gleichzeitig:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` wird aufgerufen, und alle zurückgegebenen Listener werden geöffnet. `ICommunicationListener.OpenAsync()` wird für jeden Listener aufgerufen.
    - Die Methode `StatefulServiceBase.RunAsync()` des Diensts wird aufgerufen.
2. Nachdem alle Replikatlisteneraufrufe von `OpenAsync()` abgeschlossen wurden und `RunAsync()` aufgerufen wurde, wird `StatefulServiceBase.OnChangeRoleAsync()` aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Allgemeine Probleme beim Herunterfahren zustandsbehafteter Dienste und beim Tieferstufen des primären Replikats
Service Fabric ändert das primäre Replikat eines zustandsbehafteten Diensts aus verschiedenen Gründen. Zu den gängigsten Gründen zählen [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md) und [Anwendungsupgrade](service-fabric-application-upgrade.md). Während dieser Vorgänge (sowie beim normalen Herunterfahren des Diensts – etwa, wenn der Dienst gelöscht wird) muss der Dienst das Abbruchtoken (`CancellationToken`) berücksichtigen. 

Bei Diensten ohne ordnungsgemäße Abbruchbehandlung können mehrere Probleme auftreten. Diese Vorgänge werden verlangsamt, da Service Fabric auf eine ordnungsgemäße Beendigung der Dienste wartet. Dies kann letztendlich zu nicht erfolgreichen Upgrades mit Timeout und Rollback führen. Wenn das Abbruchtoken nicht berücksichtigt wird, können auch unausgeglichene Cluster entstehen. Cluster sind unausgeglichen, da Knoten ausgelastet werden, die Dienste aber nicht ausgeglichen werden können, weil ihre Verschiebung zu lange dauert. 

Da es sich um zustandsbehaftete Dienste handelt, ist es auch wahrscheinlich, dass sie die [zuverlässigen Sammlungen](service-fabric-reliable-services-reliable-collections.md) verwenden. Beim Tieferstufen eines primären Replikats wird in Service Fabric als eine der ersten Maßnahmen der Schreibzugriff auf den zugrunde liegenden Zustand entzogen. Dies führt zu weiteren Problemen, die Einfluss auf den Lebenszyklus des Diensts haben können. Die Sammlungen geben Ausnahmen auf der Grundlage des Timings und abhängig davon zurück, ob das Replikat verschoben oder heruntergefahren wird. Diese Ausnahmen müssen korrekt behandelt werden. Von Service Fabric ausgelöste Ausnahmen können dauerhaft [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) oder vorübergehend [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) sein. Dauerhafte Ausnahmen müssen protokolliert und ausgelöst werden. Vorgänge mit vorübergehenden Ausnahmen können hingegen mit einer Wiederholungslogik wiederholt werden.

Die Behandlung von Ausnahmen, die auf die Verwendung von `ReliableCollections` in Verbindung mit Dienstlebenszyklusereignissen zurückzuführen sind, ist ein wichtiger Bestandteil der Tests und Prüfungen für einen zuverlässigen Dienst. Es empfiehlt sich, den Dienst während der Durchführung von Upgrades und [Chaostests](service-fabric-controlled-chaos.md) immer unter Last auszuführen, bevor Sie ihn in einer Produktionsumgebung bereitstellen. Mit diesen einfachen Schritten können Sie sicherstellen, dass Ihr Dienst ordnungsgemäß implementiert ist und Lebenszyklusereignisse korrekt behandelt.


## <a name="notes-on-the-service-lifecycle"></a>Hinweise zum Dienstlebenszyklus
  - Sowohl die `RunAsync()`-Methode als auch die `CreateServiceReplicaListeners/CreateServiceInstanceListeners`-Aufrufe sind optional. Ein Dienst kann eine dieser Optionen, beide oder keine von beiden aufweisen. Wenn der Dienst beispielsweise alle Vorgänge infolge von Benutzeraufrufen ausführt, muss `RunAsync()` nicht implementiert werden. Nur die Kommunikationslistener und der zugehörige Code sind erforderlich. Ebenso ist das Erstellen und Zurückgeben von Kommunikationslistenern optional, da der Dienst möglicherweise nur Hintergrundaufgaben ausführt und daher nur `RunAsync()` implementieren muss.
  - Ein Dienst kann `RunAsync()` erfolgreich abschließen und dann zurückkehren. Der Abschluss ist keine Fehlerbedingung. Der Abschluss von `RunAsync()` gibt an, dass die Hintergrundaufgaben des Diensts abgeschlossen sind. Bei zustandsbehafteten zuverlässigen Diensten wird `RunAsync()` erneut aufgerufen, wenn das Replikat vom primären Replikat zum sekundären Replikat tiefer gestuft und dann wieder zum primären Replikat höher gestuft wird.
  - Wenn ein Dienst die Ausführung von `RunAsync()` mit einer unerwarteten Ausnahme beendet, liegt ein Fehler vor. Das Dienstobjekt wird heruntergefahren, und ein Integritätsfehler wird gemeldet.
  - Zwar gibt es keine zeitliche Begrenzung für die Rückgabe dieser Methoden, aber Sie verlieren sofort die Möglichkeit zum Schreiben in zuverlässige Sammlungen und können daher Ihre eigentlichen Arbeiten nicht abschließen. Es wird empfohlen, dass Sie sie so schnell wie möglich nach dem Empfang der Abbruchanforderung zurückgeben. Wenn der Dienst nicht in einem angemessenen Zeitraum auf diese API-Aufrufe reagiert, kann Service Fabric das Beenden des Diensts erzwingen. Dies geschieht normalerweise nur während Anwendungsupgrades oder beim Löschen eines Diensts. Das Timeout beträgt standardmäßig 15 Minuten.
  - Fehler im `OnCloseAsync()`-Pfad führen zu einem Aufruf von `OnAbort()`. So erhält der Dienst eine letzte Gelegenheit zum Bereinigen und Freigeben aller beanspruchten Ressourcen. Diese Methode wird im Allgemeinen verwendet, wenn auf dem Knoten ein dauerhafter Fehler erkannt wird oder Service Fabric den Lebenszyklus der Dienstinstanz aufgrund von internen Fehlern nicht zuverlässig verwalten kann.
  - `OnChangeRoleAsync()` wird immer dann aufgerufen, wenn das Replikat des zustandsbehafteten Diensts die Rolle wechselt und beispielsweise ein primäres oder sekundäres Replikat wird. Primäre Replikate erhalten Schreibstatus (mit Erlaubnis zum Erstellen und Schreiben in Reliable Collections). Sekundäre Replikate erhalten Lesestatus (können nur aus vorhandenen Reliable Collections lesen). Die meisten Aufgaben in einem zustandsbehafteten Dienst werden im primären Replikat ausgeführt. Sekundäre Replikate können schreibgeschützte Überprüfungen durchführen, Berichte generieren und Data Mining oder andere schreibgeschützte Aufträge ausführen.

## <a name="next-steps"></a>Nächste Schritte
- [Einführung in Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
- [Replikate und Instanzen](service-fabric-concepts-replica-lifecycle.md)
