---
title: Azure Service Fabric Reliable Services-Lebenszyklus | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Lebenszyklusereignisse in Service Fabric Reliable Services.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 36c1ff2ace944d84120bf456060c7504170a814c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772940"
---
# <a name="reliable-services-lifecycle"></a>Reliable Services-Lebenszyklus
> [!div class="op_single_selector"]
> * [C# unter Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java unter Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services zählen zu den in Azure Service Fabric verfügbaren Programmiermodellen. Wenn Sie sich über den Lebenszyklus von Reliable Services informieren möchten, müssen Sie vor allem die grundlegenden Lebenszyklusereignisse verstehen. Die genaue Reihenfolge der Ereignisse hängt von Konfigurationsdetails ab. 

Im Allgemeinen schließt der Reliable Services-Lebenszyklus die folgenden Ereignisse ein:

* Während des Starts:
  * Dienste werden erstellt.
  * Dienste können keinen oder mehrere Listener erstellen und zurückgeben.
  * Alle zurückgegebenen Listener werden für die Kommunikation mit dem Dienst geöffnet.
  * Die `runAsync`-Methode des Diensts wird aufgerufen, sodass der Dienst langwierige Aufgaben oder Hintergrundarbeit ausführen kann.
* Während des Herunterfahrens:
  * Das an `runAsync` übergebene Abbruchtoken wird abgebrochen, und die Listener werden geschlossen.
  * Das Dienstobjekt selbst wird zerstört.

Die Reihenfolge der Ereignisse in Reliable Services kann geringfügig abweichen, je nachdem, ob der zuverlässige Dienst zustandslos oder zustandsbehaftet ist. 

Für zustandsbehaftete Dienste müssen Sie außerdem das Szenario mit Austausch des primären Replikats berücksichtigen. Während dieser Sequenz wird die Rolle des primären Replikats auf ein anderes Replikat (oder wieder zurück) übertragen, ohne dass der Dienst heruntergefahren wird. 

Schließlich müssen Sie sich mit Fehlerbedingungen beschäftigen.

## <a name="stateless-service-startup"></a>Start zustandsloser Dienste
Der Lebenszyklus eines zustandslosen Diensts ist recht einfach. So sieht die Reihenfolge der Ereignisse aus:

1. Der Dienst wird erstellt.
2. Diese Ereignisse treten parallel auf:
    - `StatelessService.createServiceInstanceListeners()` wird aufgerufen, und alle zurückgegebenen Listener werden geöffnet. `CommunicationListener.openAsync()` wird für jeden Listener aufgerufen.
    - Die `runAsync`-Methode des Diensts (`StatelessService.runAsync()`) wird aufgerufen.
3. Die eigene `onOpenAsync`-Methode des Diensts wird aufgerufen (sofern vorhanden). Insbesondere wird `StatelessService.onOpenAsync()` aufgerufen. Diese Außerkraftsetzung ist ungewöhnlich, aber verfügbar.

Beachten Sie unbedingt, dass es keine Reihenfolge zwischen den Aufrufen zum Erstellen und Öffnen der Listener und dem Aufruf von `runAsync` gibt. Die Listener können vor dem Starten von `runAsync` geöffnet werden. Ebenso wird `runAsync` möglicherweise aufgerufen, bevor die Kommunikationslistener geöffnet sind oder überhaupt erstellt wurden. Wenn eine Synchronisierung erforderlich ist, muss diese vom Implementierer durchgeführt werden. Hier sind einige gängige Lösungen:

* In einigen Fällen funktionieren Listener erst dann, wenn andere Informationen erstellt oder andere Aufgaben durchgeführt wurden. Für zustandslose Dienste können diese Aufgaben in der Regel im Konstruktor des Diensts ausgeführt werden. Dies kann während des Aufrufs von `createServiceInstanceListeners()` oder als Teil der Erstellung des Listeners erfolgen.
* In einigen Fällen wird der Code in `runAsync` erst gestartet, wenn die Listener geöffnet sind. In diesem Fall ist zusätzliche Koordination erforderlich. Eine gebräuchliche Lösung besteht darin, in den Listenern ein Flag hinzufügen. Das Flag gibt an, wann die Listener beendet wurden. Die `runAsync`-Methode überprüft dies, bevor mit der eigentlichen Arbeit fortgefahren wird.

## <a name="stateless-service-shutdown"></a>Herunterfahren zustandsloser Dienste
Beim Herunterfahren eines zustandslosen Diensts wird dasselbe Muster in umgekehrter Reihenfolge befolgt:

1. Diese Ereignisse treten parallel auf:
    - Alle geöffneten Listener werden geschlossen. `CommunicationListener.closeAsync()` wird für jeden Listener aufgerufen.
    - Das an `runAsync()` übergebene Abbruchtoken wird verworfen. Es wird überprüft, ob die `isCancelled`-Eigenschaft des Abbruchtokens `true` zurückgibt und ob die `throwIfCancellationRequested`-Methode eine Ausnahme vom Typ `CancellationException` auslöst.
2. Nach Abschluss von `closeAsync()` für die einzelnen Listener und nach Abschluss von `runAsync()` wird die `StatelessService.onCloseAsync()`-Methode des Diensts aufgerufen (sofern vorhanden). Auch hier handelt es sich nicht um eine gängige Überschreibung. Die Methode kann jedoch verwendet werden, um Ressourcen sicher zu schließen, die Hintergrundverarbeitung anzuhalten, das Speichern des externen Zustands zu beenden oder bestehende Verbindungen zu trennen.
3. Nach Abschluss von `StatelessService.onCloseAsync()` wird das Dienstobjekt zerstört.

## <a name="stateful-service-startup"></a>Start zustandsbehafteter Dienste
Zustandsbehaftete Dienste befolgen mit wenigen Änderungen ein ähnliches Muster wie zustandslose Dienste.  Beim Starten eines zustandsbehafteten Diensts lautet die Reihenfolge der Ereignisse wie folgt:

1. Der Dienst wird erstellt.
2. `StatefulServiceBase.onOpenAsync()` wird aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.
3. Diese Ereignisse treten parallel auf:
    - `StatefulServiceBase.createServiceReplicaListeners()` wird aufgerufen. 
      - Wenn es sich bei dem Dienst um einen primären Dienst handelt, werden alle zurückgegebenen Listener geöffnet. `CommunicationListener.openAsync()` wird für jeden Listener aufgerufen.
      - Bei einem sekundären Dienst werden nur als `listenOnSecondary = true` gekennzeichnete Listener geöffnet. Für sekundäre Dienste geöffnete Listener sind eher selten.
    - Wenn es sich bei dem Dienst derzeit um einen primären Dienst handelt, wird die `StatefulServiceBase.runAsync()`-Methode des Diensts aufgerufen.
4. Nachdem alle Replikatlisteneraufrufe von `openAsync()` abgeschlossen wurden und `runAsync()` aufgerufen wurde, wird `StatefulServiceBase.onChangeRoleAsync()` aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

Ähnlich wie bei zustandslosen Diensten wird bei einem zustandsbehafteten Dienst die Reihenfolge, in der die Listener erstellt und geöffnet werden und `runAsync` aufgerufen wird, nicht koordiniert. Wenn Sie eine Koordinierung benötigen, sind die Lösungen nahezu identisch. Es gibt jedoch einen zusätzlichen Fall für einen zustandsbehafteten Dienst. Angenommen, die bei den Kommunikationslistenern eingehenden Aufrufe benötigen Informationen aus [zuverlässigen Sammlungen](service-fabric-reliable-services-reliable-collections.md). Da die Kommunikationslistener geöffnet werden könnten, bevor die Reliable Collections gelesen oder geschrieben oder `runAsync` gestartet werden kann, ist einige zusätzliche Koordination erforderlich. Die einfachste und gängigste Lösung besteht darin, dass die Kommunikationslistener einen Fehlercode zurückgeben. Anhand des Fehlercodes stellt der Client fest, dass die Anforderung wiederholt werden muss.

## <a name="stateful-service-shutdown"></a>Herunterfahren zustandsbehafteter Dienste
Wie bei zustandslosen Diensten stimmen die Lebenszyklusereignisse während des Herunterfahrens mit denen während des Starts überein, aber in umgekehrter Reihenfolge. Wenn ein zustandsbehafteter Dienst heruntergefahren wird, treten die folgenden Ereignisse auf:

1. Diese Ereignisse treten parallel auf:
    - Alle geöffneten Listener werden geschlossen. `CommunicationListener.closeAsync()` wird für jeden Listener aufgerufen.
    - Das an `runAsync()` übergebene Abbruchtoken wird verworfen. Ein Aufruf der `isCancelled()`-Methode des Abbruchtokens gibt `true` zurück, und die `throwIfCancellationRequested()`-Methode des Tokens löst bei Aufruf eine Ausnahme vom Typ `OperationCanceledException` aus.
2. Nach Abschluss von `closeAsync()` für die einzelnen Listener und nach Abschluss von `runAsync()` wird die Methode `StatefulServiceBase.onChangeRoleAsync()` des Diensts aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

   > [!NOTE]  
   > Ein Warten auf den Abschluss von `runAsync` ist nur erforderlich, wenn das Replikat ein primäres Replikat ist.

3. Nach Abschluss der Methode `StatefulServiceBase.onChangeRoleAsync()` wird die Methode `StatefulServiceBase.onCloseAsync()` aufgerufen. Dieser Aufruf ist eine ungewöhnliche Außerkraftsetzung, aber verfügbar.
3. Nach Abschluss von `StatefulServiceBase.onCloseAsync()` wird das Dienstobjekt zerstört.

## <a name="stateful-service-primary-swaps"></a>Tausch des primären Replikats bei zustandsbehafteten Diensten
Während ein zustandsbehafteter Dienst ausgeführt wird, werden Kommunikationslistener geöffnet, und die `runAsync`-Methode wird nur für die primären Replikate dieses zustandsbehafteten Diensts aufgerufen. Die sekundären Replikate werden erstellt, erhalten jedoch keine weiteren Aufrufe. Während ein zustandsbehafteter Dienst ausgeführt wird, kann sich das Replikat ändern, das derzeit das primäre ist. Die für das zustandsbehaftete Replikat sichtbare Lebenszyklusereignisse sind davon abhängig, ob das Replikat während des Austauschs höher oder tiefer gestuft werden kann.

### <a name="for-the-demoted-primary"></a>Tiefer gestuftes primäres Replikat
Das tiefer gestufte primäre Replikat muss für Service Fabric die Verarbeitung von Nachrichten und jegliche Hintergrundverarbeitung beenden. Dieser Schritt ist ähnlich dem Herunterfahren des Diensts. Ein Unterschied besteht darin, dass der Dienst nicht zerstört oder geschlossen wird, da er als sekundäres Replikat erhalten bleibt. Die folgenden Ereignisse treten auf:

1. Diese Ereignisse treten parallel auf:
    - Alle geöffneten Listener werden geschlossen. `CommunicationListener.closeAsync()` wird für jeden Listener aufgerufen.
    - Das an `runAsync()` übergebene Abbruchtoken wird verworfen. Eine Überprüfung der `isCancelled()`-Methode des Abbruchtokens gibt `true` zurück. Bei Aufruf löst die `throwIfCancellationRequested()`-Methode des Tokens eine Ausnahme vom Typ `OperationCanceledException` aus.
2. Nach Abschluss von `closeAsync()` für die einzelnen Listener und nach Abschluss von `runAsync()` wird die Methode `StatefulServiceBase.onChangeRoleAsync()` des Diensts aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

### <a name="for-the-promoted-secondary"></a>Höher gestuftes sekundäres Replikat
Analog dazu muss das höher gestufte sekundäre Replikat für Service Fabric mit dem Lauschen auf Nachrichten beginnen und alle Hintergrundaufgaben starten, die abgeschlossen werden müssen. Dieser Vorgang ähnelt dem Erstellen des Diensts. Der Unterschied besteht darin, dass das Replikat bereits vorhanden ist. Die folgenden Ereignisse treten auf:

1. Diese Ereignisse treten parallel auf:
    - `StatefulServiceBase.createServiceReplicaListeners()` wird aufgerufen, und alle zurückgegebenen Listener werden geöffnet. `CommunicationListener.openAsync()` wird für jeden Listener aufgerufen.
    - Die Methode `StatefulServiceBase.runAsync()` des Diensts wird aufgerufen.
2. Nachdem alle Replikatlisteneraufrufe von `openAsync()` abgeschlossen wurden und `runAsync()` aufgerufen wurde, wird `StatefulServiceBase.onChangeRoleAsync()` aufgerufen. Dieser Aufruf wird normalerweise nicht im Dienst außer Kraft gesetzt.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Allgemeine Probleme beim Herunterfahren zustandsbehafteter Dienste und beim Tieferstufen des primären Replikats
Service Fabric ändert das primäre Replikat eines zustandsbehafteten Diensts aus verschiedenen Gründen. Zu den gängigsten Gründen zählen [Lastenausgleich im Cluster](service-fabric-cluster-resource-manager-balancing.md) und [Anwendungsupgrade](service-fabric-application-upgrade.md). Während dieser Vorgänge ist es wichtig, dass der Dienst das `cancellationToken` berücksichtigt. Dies gilt auch während des normalen Herunterfahren des Diensts, z.B. wenn der Dienst gelöscht wurde.

Bei Diensten ohne ordnungsgemäße Abbruchbehandlung können mehrere Probleme auftreten. Diese Vorgänge werden verlangsamt, da Service Fabric auf eine ordnungsgemäße Beendigung der Dienste wartet. Dies kann letztendlich zu nicht erfolgreichen Upgrades mit Timeout und Rollback führen. Wenn das Abbruchtoken nicht berücksichtigt wird, können auch unausgeglichene Cluster entstehen. Cluster werden unausgeglichen, wenn Knoten stark ausgelastet sind. Die Dienste können jedoch nicht ausgeglichen werden, da ihre Verschiebung zu lange dauert. 

Da es sich um zustandsbehaftete Dienste handelt, ist es auch wahrscheinlich, dass sie [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) verwenden. Beim Tieferstufen eines primären Replikats wird in Service Fabric als eine der ersten Maßnahmen der Schreibzugriff auf den zugrunde liegenden Zustand entzogen. Dies führt zu weiteren Problemen, die Einfluss auf den Lebenszyklus des Diensts haben können. Die Sammlungen geben Ausnahmen auf der Grundlage des Timings und abhängig davon zurück, ob das Replikat verschoben oder heruntergefahren wird. Diese Ausnahmen müssen ordnungsgemäß behandelt werden. 

Von Service Fabric ausgelöste Ausnahmen sind permanent [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) oder vorübergehend [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Bei permanenten Ausnahmen sollte eine Ausnahme ausgelöst werden, und sie sollten protokolliert werden. Vorübergehende Ausnahmen können basierend auf Wiederholungslogik wiederholt werden.

Ein wichtiger Bestandteil der Tests und Prüfungen von Reliable Services ist die Behandlung von Ausnahmen, die auf die Verwendung von `ReliableCollections` in Verbindung mit Dienstlebenszyklusereignissen zurückzuführen sind. Es wird empfohlen, den Dienst immer unter Last auszuführen. Sie sollten vor der Bereitstellung in der Produktion auch Upgrades und [Chaostests](service-fabric-controlled-chaos.md) ausführen. Mit diesen einfachen Schritten können Sie sicherstellen, dass Ihr Dienst ordnungsgemäß implementiert ist und Lebenszyklusereignisse korrekt behandelt.

## <a name="notes-on-service-lifecycle"></a>Hinweise zum Dienstlebenszyklus
* Sowohl die `runAsync()`-Methode als auch die `createServiceInstanceListeners/createServiceReplicaListeners`-Aufrufe sind optional. Ein Dienst kann über eine dieser Varianten, beide oder keine von beiden verfügen. Wenn der Dienst beispielsweise alle Vorgänge infolge von Benutzeraufrufen ausführt, muss `runAsync()` nicht implementiert werden. Nur die Kommunikationslistener und der zugehörige Code sind erforderlich.  Ebenso ist das Erstellen und Zurückgeben von Kommunikationsüberwachungen optional. Der Dienst muss möglicherweise nur Hintergrundverarbeitung ausführen, daher muss er nur `runAsync()` implementieren.
* Ein Dienst kann `runAsync()` erfolgreich abschließen und dann zurückkehren. Dies wird nicht als Fehlerbedingung angesehen. Es stellt die Hintergrundverarbeitung des Diensts während des Abschlusses dar. Für zustandsbehaftete Reliable Services wird `runAsync()` erneut aufgerufen, wenn der Dienst vom primären Replikat tiefer gestuft und dann wieder auf das primäre Replikat höher gestuft wird.
* Wenn ein Dienst die Ausführung von `runAsync()` mit einer unerwarteten Ausnahme beendet, ist dies ein Fehler. Das Dienstobjekt wird heruntergefahren, und ein Integritätsfehler wird gemeldet.
* Zwar gibt es keine zeitliche Begrenzung für die Rückkehr von diesen Methoden, Sie verlieren jedoch sofort die Möglichkeit zum Schreiben. Daher können Sie Ihre eigentlichen Arbeiten nicht abschließen. Es wird empfohlen, sie so schnell wie möglich nach dem Empfang der Abbruchanforderung zurückzugeben. Wenn der Dienst nicht in einem angemessenen Zeitraum auf diese API-Aufrufe reagiert, kann Service Fabric das Beenden des Diensts erzwingen. Dies geschieht normalerweise nur während Anwendungsupgrades oder beim Löschen eines Diensts. Das Timeout beträgt standardmäßig 15 Minuten.
* Fehler im `onCloseAsync()`-Pfad führen zu einem Aufruf von `onAbort()`. Dadurch erhält der Dienst eine letzte Gelegenheit zum Bereinigen und Freigeben aller beanspruchten Ressourcen. Diese Methode wird im Allgemeinen verwendet, wenn auf dem Knoten ein dauerhafter Fehler erkannt wird oder Service Fabric den Lebenszyklus der Dienstinstanz aufgrund von internen Fehlern nicht zuverlässig verwalten kann.
* `OnChangeRoleAsync()` wird immer dann aufgerufen, wenn das Replikat des zustandsbehafteten Diensts die Rolle wechselt und beispielsweise ein primäres oder sekundäres Replikat wird. Primäre Replikate erhalten Schreibstatus (mit Erlaubnis zum Erstellen und Schreiben in Reliable Collections). Sekundäre Replikate erhalten Lesestatus (können nur aus vorhandenen Reliable Collections lesen). Die meisten Aufgaben in einem zustandsbehafteten Dienst werden im primären Replikat ausgeführt. Sekundäre Replikate können schreibgeschützte Überprüfungen durchführen, Berichte generieren und Data Mining oder andere schreibgeschützte Aufträge ausführen.

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Reliable Services](service-fabric-reliable-services-introduction.md)
* [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start-java.md)

