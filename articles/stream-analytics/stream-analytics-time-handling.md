---
title: Grundlegendes zur Behandlung von Zeitangaben in Azure Stream Analytics
description: Erfahren Sie, wie Sie in Azure Stream Analytics die beste Startzeit auswählen, verspätete und zu früh eintreffende Ereignisse verarbeiten, und informieren Sie sich über Metriken zur Behandlung von Zeitangaben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: c8f40808834c64ad74673f1c5f0c19892607fdcc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127472"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Grundlegendes zur Behandlung von Zeitangaben in Azure Stream Analytics

In diesem Artikel erfahren Sie, wie Sie Entwurfsentscheidungen treffen können, um praktische Probleme bei der Behandlung von Zeitangaben in Azure Stream Analytics-Aufträgen zu lösen. Die Entwurfsentscheidungen für die Behandlung von Zeitangaben stehen in engem Zusammenhang mit den Faktoren der Ereignisreihenfolge.

## <a name="background-time-concepts"></a>Zeitkonzepte im Hintergrund

Um die Diskussion besser zu gestalten, lassen Sie uns einige Hintergrundkonzepte definieren:

- **Ereigniszeit**: Der Zeitpunkt, an dem das ursprüngliche Ereignis aufgetreten ist. Zum Beispiel, wenn sich ein fahrendes Auto auf der Autobahn einem Mauthäuschen nähert.

- **Verarbeitungszeit**: Der Zeitpunkt, an dem das Ereignis das Verarbeitungssystem erreicht und beobachtet wird. Zum Beispiel, wenn der Sensor im Mauthäuschen das Auto erkennt und das Computersystem einen Moment benötigt, um die Daten zu verarbeiten.

- **Wasserzeichen**: Ein Ereigniszeitmarker, der anzeigt, bis zu welchem Punkt Ereignisse in den Streaming-Prozessor eingegangen sind. Mit Wasserzeichen zeigt das System den Fortschritt beim Erfassen der Ereignisse an. Aufgrund der Art der Streams hören die eingehenden Ereignisdaten nie auf, sodass Wasserzeichen den Fortschritt bis zu einem bestimmten Punkt im Stream anzeigen.

   Das Wasserzeichenkonzept ist wichtig. Mit Wasserzeichen kann Stream Analytics bestimmen, wann das System vollständige, korrekte und wiederholbare Ergebnisse liefern kann, die nicht zurückgezogen werden müssen. Die Verarbeitung kann auf vorhersehbare und wiederholbare Weise erfolgen. Wenn zum Beispiel eine Nachzählung für eine Fehlerbehandlung durchgeführt werden muss, sind Wasserzeichen sichere Start- und Endpunkte.

Zusätzliche Ressourcen zu diesem Thema finden Sie in den Blogbeiträgen von Tyler Akidau [Streaming 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) und [Streaming 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choose-the-best-starting-time"></a>Auswählen der besten Startzeit

Stream Analytics bietet Benutzern zwei Optionen für die Auswahl der Ereigniszeit: Eingangszeit und Anwendungszeit.

### <a name="arrival-time"></a>Eingangszeit

Die Eingangszeit wird an der Eingabequelle zugewiesen, wenn das Ereignis die Quelle erreicht. Auf die Eingangszeit kann bei Event Hubs-Eingaben mithilfe der **EventEnqueuedUtcTime**-Eigenschaft, bei IoT Hub-Eingaben mit der Eigenschaft **IoTHub.EnqueuedTime** und bei Blobeingaben mithilfe der **BlobProperties.LastModified**-Eigenschaft zugegriffen werden.

Die Eingangszeit wird standardmäßig verwendet und eignet sich am besten für Datenarchivierungsszenarien, bei denen keine Zeitlogik erforderlich ist.

### <a name="application-time-also-named-event-time"></a>Anwendungszeit (auch als Ereigniszeit bezeichnet)

Die Anwendungszeit wird bei Erstellung des Ereignisses zugewiesen und ist Teil der Ereignisnutzlast. Verwenden Sie zur Verarbeitung von Ereignissen anhand der Anwendungszeit die **Timestamp by**-Klausel in der SELECT-Abfrage. Wenn **Timestamp by** nicht vorhanden ist, werden Ereignisse basierend auf der Eingangszeit verarbeitet.

Es ist wichtig, einen Zeitstempel in der Nutzlast zu verwenden, wenn Zeitlogik eine Rolle spielt, um Verzögerungen im Quellsystem oder im Netzwerk zu berücksichtigen. Die Zeit, die einem Ereignis zugeordnet ist steht in [SYSTEM.TIMESTAMP](/stream-analytics-query/system-timestamp-stream-analytics) zur Verfügung.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Zeitverlauf in Azure Stream Analytics

Wenn Sie Anwendungszeit verwenden, basiert der Zeitverlauf auf den eingehenden Ereignissen. Es ist für das Streamverarbeitungssystem schwierig einzuschätzen, ob es gerade keine Ereignisse gibt oder ob sich Ereignisse verzögern. Aus diesem Grund erzeugt Azure Stream Analytics für jede Eingangspartition heuristische Wasserzeichen auf folgende Weise:

* Wann immer ein Ereignis eintrifft, ist das Wasserzeichen die bisher größte Ereigniszeit, die Stream Analytics bis dahin erkannt hat, abzüglich der Größe des Toleranzfensters für Ereignisse mit falscher Reihenfolge.

* Wenn es kein eingehendes Ereignis gibt, ist das Wasserzeichen die aktuelle geschätzte Ankunftszeit abzüglich des Toleranzfensters für verspätete Ankunft. Die geschätzte Ankunftszeit ist die Zeit, die seit dem letzten Mal verstrichen ist, als ein Eingabeereignis gesehen wurde, zuzüglich der Ankunftszeit dieses Eingabeereignisses.

   Die Eingangszeit kann nur geschätzt werden, da die tatsächliche Eingangszeit auf dem Eingangsereignis-Broker, wie beispielsweise Event Hubs, generiert wird, nicht aber auf der Azure Stream Analytics-VM, die die Ereignisse verarbeitet.

Der Entwurf dient außer der Generierung von Wasserzeichen zwei weiteren Zwecken:

1. Das System erzeugt zeitnah Ergebnisse mit oder ohne eingehende Ereignisse.

   Sie können selbst steuern, wie zeitnah Sie die Ausgabeergebnisse sehen möchten. Im Azure-Portal können Sie auf der Seite **Ereignisreihenfolge** Ihres Stream Analytics-Auftrags die Einstellung **Ereignisse mit falscher Reihenfolge** konfigurieren. Bei der Konfiguration dieser Einstellung ist der Kompromiss zwischen Aktualität und Toleranz gegenüber Ereignissen mit falscher Reihenfolge im Ereignisstrom zu berücksichtigen.

   Das Toleranzfenster für Eingangsverzögerung ist erforderlich, um auch ohne eingehende Ereignisse immer wieder Wasserzeichen zu generieren. Manchmal kann es einen Zeitraum geben, in dem keine eingehenden Ereignisse eintreffen, etwa wenn ein Ereigniseingabesstrom eine geringe Dichte hat. Dieses Problem wird durch die Verwendung mehrerer Partitionen im Eingangsereignis-Broker noch verschärft.

   Bei Verarbeitungssystemen für Streamingdaten ohne ein Toleranzfenster für Eingangsverzögerung kann es zu Verzögerungen bei der Ausgabe kommen, wenn die Eingabe eine geringe Dichte hat, und mehrere Partitionen verwendet werden.

2. Das Verhalten des Systems muss wiederholbar sein. Wiederholbarkeit ist eine wichtige Eigenschaft von Verarbeitungssystemen für Streamingdaten.

   Das Wasserzeichen wird von der Eingangszeit und Anwendungszeit abgeleitet. Beide bleiben im Ereignisbroker vorhanden und sind somit wiederholbar. Wenn die Eingangszeit bei fehlenden Ereignissen geschätzt wird, erfasst Azure Stream Analytics die geschätzte Eingangszeit für die Wiederholbarkeit während der Wiedergabe zur Fehlerbehebung.

Wenn Sie sich entschließen, die **Eingangszeit** als Ereigniszeit zu verwenden, muss keine Toleranz für Ereignisse mit falscher Reihenfolge oder Eingangsverzögerung konfiguriert werden. Da die **Eingangszeit** im Eingangsereignis-Broker garantiert ansteigend ist, ignoriert Azure Stream Analytics einfach die Konfigurationen.

## <a name="late-arriving-events"></a>Eingangsverzögerung

Gemäß der Definition des Toleranzfenster für Eingangsverzögerung vergleicht Azure Stream Analytics für jedes eingehende Ereignis die **Ereigniszeit** mit der **Eingangszeit**. Wenn die Ereigniszeit außerhalb des Toleranzfensters liegt, können Sie das System dafür konfigurieren, das Ereignis zu verwerfen oder seine Zeit so anzupassen, dass sie innerhalb der Toleranz liegt.

Sobald Wasserzeichen generiert werden, kann der Dienst möglicherweise Ereignisse empfangen, deren Ereigniszeit niedriger ist als das Wasserzeichen. Sie können den Dienst so konfigurieren, dass diese Ereignisse entweder **gelöscht** werden, oder dass die Ereigniszeit an den Wasserzeichenwert **angepasst** wird.

Als Teil der Anpassung wird der **System.Timestamp** des Ereignisses auf den neuen Wert gesetzt, aber das Feld **Ereigniszeit** selbst wird nicht geändert. Diese Anpassung ist die einzige Situation, in der der Wert für **System.Timestamp** eines Ereignisses vom Wert im Feld „Ereigniszeit“ abweichen kann und unerwartete Ergebnisse hervorrufen kann.

## <a name="handle-time-variation-with-substreams"></a>Behandlung von Zeitabweichungen mit untergeordneten Datenströmen

Der beschriebene heuristische Mechanismus zur Generierung von Wasserzeichen funktioniert in den meisten Fällen gut, wenn die Zeit zwischen den verschiedenen Ereignissendern weitgehend synchronisiert ist. In der Praxis, insbesondere in vielen IoT-Szenarien, hat das System jedoch wenig Kontrolle über die Uhr auf den Ereignissendern. Die Ereignissender können alle möglichen Geräte im Feld sein, möglicherweise auf verschiedenen Versionen von Hard- und Software.

Anstatt ein Wasserzeichen zu verwenden, das für alle Ereignisse in einer Eingangspartition global ist, verfügt Stream Analytics über einen weiteren Mechanismus namens **untergeordnete Datenströme**. Sie können untergeordnete Datenströme in Ihrem Auftrag verwenden, indem Sie eine Auftragsabfrage mit der Klausel [**TIMESTAMP BY**](/stream-analytics-query/timestamp-by-azure-stream-analytics) und dem Schlüsselwort **OVER** schreiben. Um einen untergeordneten Datenstrom festzulegen, geben Sie den Namen der Schlüsselspalte nach dem Schlüsselwort **OVER** ein, wie z.B. `deviceid`, sodass das System Zeitrichtlinien gemäß dieser Spalte anwendet. Jeder untergeordnete Datenstrom erhält sein eigenes unabhängiges Wasserzeichen. Dieser Mechanismus ist nützlich, um eine zeitnahe Ausgabe zu ermöglichen, wenn große Uhrabweichungen oder Netzwerkverzögerungen zwischen Ereignissendern vorliegen.

Untergeordneter Datenströme sind eine einzigartige Lösung, die von Azure Stream Analytics bereitgestellt werden, und nicht von anderen Verarbeitungssystemen für Streamingdaten angeboten werden.

Wenn Sie untergeordnete Datenströme verwenden, wendet Stream Analytics das Toleranzfenster für Eingangsverzögerungen auf eingehende Ereignisse an. Die Toleranz für Eingangsverzögerung bestimmt den maximalen Betrag, um den verschiedene untergeordnete Datenströme voneinander abweichen können. Wenn beispielsweise Gerät 1 bei Zeitstempel 1 und Gerät 2 bei Zeitstempel 2 liegt, ist die größte Toleranz für Eingangsverzögerungen Zeitstempel 2 minus Zeitstempel 1. Die Standardeinstellung liegt bei 5 Sekunden und ist wahrscheinlich zu klein für Geräte mit abweichenden Zeitstempeln. Wir empfehlen, dass Sie mit 5 Minuten beginnen und die Einstellungen entsprechend dem Muster der Uhrabweichung des Geräts vornehmen.

## <a name="early-arriving-events"></a>Früh eingehende Ereignisse

Sie kennen vielleicht bereits ein anderes Konzept mit der Bezeichnung Fenster für den frühen Eingang. Dies ist das Gegenteil vom Toleranzfenster für Eingangsverzögerung. Dieses Fenster ist auf 5 Minuten festgelegt und dient einem anderen Zweck als das Toleranzfenster für Eingangsverzögerung.

Da Azure Stream Analytics stets vollständige Ergebnisse garantiert, können Sie als erste Ausgabezeit des Auftrags nur die **Auftragsstartzeit** angeben, nicht die Eingangszeit. Die Auftragsstartzeit s wird benötigt, damit das gesamte Fenster bearbeitet wird, nicht nur von der Mitte des Fensters aus.

Stream Analytics leitet die Startzeit von der Abfragespezifikation ab. Da der Eingangsereignis-Broker jedoch nur nach Eingangszeit indiziert wird, muss das System die Startzeit des Ereignisses in die Eingangszeit umrechnen. Das System kann die Verarbeitung von Ereignissen ab dieser Stelle im Eingangsereignis-Broker starten. Beim Grenzwert des Fensters für den verfrühten Eingang ist die Umrechnung ganz einfach: Startereigniszeit abzüglich des Fensters für den verfrühten Eingang von 5 Minuten. Diese Berechnung bedeutet auch, dass das System alle Ereignisse löscht, deren Ereigniszeit 5 Minuten vor der Eingangszeit liegt. Die [Metrik für frühe Eingabeereignisse](stream-analytics-monitoring.md) wird inkrementiert, wenn die Ereignisse gelöscht werden.

Dieses Konzept wird verwendet, um sicherzustellen, dass die Verarbeitung wiederholbar ist, unabhängig davon, von wo aus Sie mit der Ausgabe beginnen. Ohne einen solchen Mechanismus wäre es nicht möglich, die Wiederholbarkeit zu gewährleisten, wie es viele andere Streamingsysteme behaupten.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Nebeneffekte der Zeittoleranzen für die Ereignissortierung

Stream Analytics-Aufträge verfügen über mehrere Optionen für die **Ereignisreihenfolge**. Zwei können im Azure-Portal konfiguriert werden: die Einstellung **Ereignisse mit falscher Reihenfolge** (Toleranz für Ereignisse mit falscher Reihenfolge) und die Einstellung **Spät empfangene Ereignisse** (Toleranz für Eingangsverzögerung). Die Toleranz für den **frühen Eingang** ist unveränderlich und kann nicht angepasst werden. Diese Zeitrichtlinien werden von Stream Analytics verwendet, um starke Garantien zu bieten. Diese Einstellungen haben jedoch einige manchmal unerwartete Auswirkungen:

1. Versehentlich Senden von Ereignissen, die zu früh sind.

   Frühe Ereignisse sollten nicht regulär ausgegeben werden. Es ist möglich, dass frühe Ereignisse an die Ausgabe gesendet werden, wenn die Uhr des Absenders jedoch zu schnell läuft. Alle früh eingehenden Ereignisse werden gelöscht, sodass keines in der Ausgabe angezeigt wird.

2. Senden alter Ereignisse an Event Hubs zur Verarbeitung durch Azure Stream Analytics.

   Alte Ereignisse erscheinen zwar zunächst harmlos, da die Toleranz für Eingangsverzögerung angewendet wird, aber alte Ereignisse werden gelöscht. Wenn die Ereignisse zu alt sind, wird der Wert **System.Timestamp** während der Ereigniserfassung geändert. Aufgrund dieses Verhaltens ist Azure Stream Analytics derzeit eher für Szenarien der echtzeitnahen Ereignisverarbeitung geeignet als für Szenarien der historischen Ereignisverarbeitung. Sie können die Zeit für **Spät empfangene Ereignisse** auf den größtmöglichen Wert (20 Tage) einstellen, um dieses Verhalten in einigen Fällen zu umgehen.

3. Ausgaben scheinen verzögert zu sein.

   Das erste Wasserzeichen wird zu der berechneten Zeit generiert: die **maximale Ereigniszeit**, die das System bisher beobachtet hat, abzüglich der Größe des Toleranzfensters für Ereignisse mit falscher Reihenfolge. Standardmäßig ist die Toleranz für Ereignisse mit falscher Reihenfolge auf Null (00 Minuten und 00 Sekunden) konfiguriert. Wenn Sie den Wert auf einen höher Zeitwert ungleich Null einstellen, wird die erste Ausgabe des Streamingauftrags aufgrund der berechneten ersten Wasserzeichenzeit um diesen Zeitwert (oder höher) verzögert.

4. Eingaben haben eine geringe Dichte.

   Wenn es in einer bestimmten Partition keine Eingabe gibt, wird die Wasserzeichenzeit als **Eingangszeit** minus dem Toleranzfenster für Eingangsverzögerung berechnet. Infolgedessen kann die Ausgabe bei seltenen Eingangsereignissen und Eingangsereignissen mit geringer Dichte um diese Zeitspanne verzögert werden. Der Standardwert für **Spät empfangene Ereignisse** beträgt 5 Sekunden. Sie sollten mit einer gewissen Verzögerung rechnen, wenn Sie z.B. Eingangsereignisse einzeln senden. Die Verzögerungen können länger werden, wenn Sie für das Fenster **Spät empfangene Ereignisse** einen zu großen Wert festlegen.

5. Der Wert für **System.Timestamp** unterscheidet sich von der Zeit im Feld **Ereigniszeit**.

   Wie bereits beschrieben, passt das System die Ereigniszeit um das Toleranzfenster für Ereignisse mit falscher Reihenfolge oder für Eingangsverzögerung an. Der Wert für **System.Timestamp** des Ereignisses wird angepasst, aber nicht das Feld **Ereigniszeit**. Dies kann verwendet werden, um zu identifizieren, für welche Ereignisse die Zeitstempel angepasst wurden. Wenn das System den Zeitstempel aufgrund einer der Toleranzen geändert hat, sind sie normalerweise gleich.

## <a name="metrics-to-observe"></a>Zu überwachende Metriken

Anhand der [Stream Analytics-Auftragsmetriken](stream-analytics-monitoring.md) können Sie eine Vielzahl an Auswirkungen der Zeittoleranzen der Ereignisreihenfolge überwachen. Die folgenden Metriken sind relevant:

|Metrik  | BESCHREIBUNG  |
|---------|---------|
| **Ereignisse mit falscher Reihenfolge** | Gibt die Anzahl der Ereignisse an, die in falscher Reihenfolge empfangen und entweder gelöscht oder mit einem angepassten Zeitstempel versehen wurden. Diese Metrik wird direkt von der Konfiguration der Einstellung **Ereignisse mit falscher Reihenfolge** auf der Seite **Ereignisreihenfolge** des Auftrags im Azure-Portal beeinflusst. |
| **Ereignisse bei verspäteter Eingabe** | Gibt die Anzahl der Ereignisse an, die verspätet von der Quelle eintreffen. Diese Metrik umfasst Ereignisse, die gelöscht wurden oder deren Zeitstempel angepasst wurde. Diese Metrik wird direkt von der Konfiguration der Einstellung **Spät empfangene Ereignisse** auf der Seite **Ereignisreihenfolge** des Auftrags im Azure-Portal beeinflusst. |
| **Frühe Eingabeereignisse** | Zeigt die Anzahl der Ereignisse an, die früh von der Quelle eintreffen und entweder gelöscht wurden oder deren Zeitstempel angepasst wurde, wenn sie mehr als 5 Minuten früher eintreffen. |
| **Wasserzeichenverzögerung** | Gibt die Verzögerung bei der Verarbeitung der Streamingdaten an. Weitere Informationen finden Sie im folgenden Abschnitt.|

## <a name="watermark-delay-details"></a>Details zur Wasserzeichenverzögerung

Die Metrik **Wasserzeichenverzögerung** wird berechnet als die Gesamtbetrachtungszeit des Verarbeitungsknotens abzüglich des größten Wasserzeichens, das er bisher erkannt hat. Weitere Informationen finden Sie im [Blogbeitrag zur Wasserzeichenverzögerung](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Es kann mehrere Gründe geben, warum dieser metrische Wert im Normalbetrieb größer als 0 ist:

1. Eigene Verarbeitungsverzögerung der Streamingpipeline. Diese Verzögerung ist in der Regel nominal.

2. Das Toleranzfenster für Ereignisse mit falscher Reihenfolge führte zu einer Verzögerung, da das Wasserzeichen um die Größe des Toleranzfensters reduziert wird.

3. Das Toleranzfenster für Eingangsverzögerung führte zu einer Verzögerung, da das Wasserzeichen um die Größe des Toleranzfensters reduziert wird.

4. Uhrabweichung des Verarbeitungsknotens, der die Metrik generiert.

Es gibt eine Reihe von anderen Ressourceneinschränkungen, die die Streamingpipeline abbremsen können. Aus folgenden Gründen kann die Wasserzeichenverzögerung steigen:

1. Nicht genügend Verarbeitungsressourcen in Stream Analytics, um das Volumen der eingegebenen Ereignisse zu bewältigen. Informationen zum Hochskalieren von Ressourcen finden Sie unter [Verstehen und Anpassen von Streamingeinheiten](stream-analytics-streaming-unit-consumption.md).

2. Nicht genügend Durchsatz innerhalb der Eingabeereignis-Broker, sodass diese gedrosselt werden. Mögliche Lösungen finden Sie in [Automatisches Hochskalieren der Azure Event Hub-Durchsatzeinheiten](../event-hubs/event-hubs-auto-inflate.md).

3. Ausgabesenken werden nicht mit ausreichender Kapazität, bereitgestellt, sodass sie gedrosselt werden. Die möglichen Lösungen variieren stark je nach verwendetem Ausgabedienst.

## <a name="output-event-frequency"></a>Ausgabeereignisfrequenz

Azure Stream Analytics verwendet den Fortschritt des Wasserzeichens als einzigen Auslöser, um Ausgabeereignisse zu erzeugen. Da das Wasserzeichen aus den Eingangsdaten abgeleitet wird, ist es sowohl während der Wiederherstellung nach einem Fehler sowie bei der vom Benutzer initiierten erneute Verarbeitung wiederholbar. Bei der Verwendung von [Aggregate im Fenstermoduss](stream-analytics-window-functions.md) erzeugt der Dienst nur Ausgaben am Ende der Fenster. In einigen Fällen kann es vorkommen, dass Benutzer Teilaggregate sehen möchten, die aus den Fenstern generiert wurden. Teilaggregate werden in Azure Stream Analytics derzeit nicht unterstützt.

In anderen Streaminglösungen können Ausgabeereignisse je nach externen Gegebenheiten an verschiedenen Auslöserpunkten realisiert werden. Bei einigen Lösungen ist es möglich, dass die Ausgabeereignisse für ein bestimmtes Zeitfenster mehrfach generiert werden. Mit der Optimierung der Eingabewerte werden die Gesamtergebnisse genauer. Ereignisse könnten zunächst spekuliert und im Laufe der Zeit überarbeitet werden. Wenn beispielsweise ein bestimmtes Gerät nicht mit dem Netzwerk verbunden ist, kann das System einen Schätzwert verwenden. Zu einem späteren Zeitpunkt verbindet sich das Gerät wieder mit dem Netzwerk. Dann können die tatsächlichen Ereignisdaten in den Eingabestream enthalten sein. Die Ausgabe resultiert aus der Verarbeitung, die zu einer genaueren Ausgabe führt.

## <a name="illustrated-example-of-watermarks"></a>Illustriertes Beispiel für Wasserzeichen

Die folgenden Bilder zeigen, wie sich Wasserzeichen unter verschiedenen Umständen entwickeln.

Diese Tabelle zeigt die Beispieldaten, die im Folgenden dargestellt werden. Beachten Sie, dass die Ereigniszeit und die Eingangszeit variieren, manchmal übereinstimmen und manchmal nicht.

| Ereigniszeit | Eingangszeit | deviceId |
| --- | --- | --- |
| 12:07 | 12:07 | device1
| 12:08 | 12:08 | device2
| 12:17 | 12:11 | device1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | device1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | device2
| 12:20 | 12:19 | device2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | device2
| 12:22 | 12:24 | device2
| 12:21 | 12:27 | device3

In dieser Illustration werden die folgenden Toleranzen verwendet:

- Das Fenster für den frühen Eingang beträgt 5 Minuten.
- Das Fenster für die Eingangsverzögerung beträgt 5 Minuten.
- Das Fenster für die Neusortierung beträgt 2 Minuten.

1. Illustration des Wasserzeichenfortschritts durch diese Ereignisse:

   ![Illustration der Azure Stream Analytics-Wasserzeichen](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Beachtenswerte Prozesse, die in der vorhergehenden Grafik dargestellt sind:

   1. Beim ersten Ereignis (device1) und dem zweiten Ereignis (device2) sind die Zeiten ausgerichtete und die Ereignisse werden ohne Anpassungen verarbeitet. Das Wasserzeichen schreitet bei jedem Ereignis voran.

   2. Wenn das dritte Ereignis (device1) verarbeitet wird, liegt die Eingangszeit (12:11) vor der Ereigniszeit (12:17). Das Ereignis kam 6 Minuten früher an, sodass das Ereignis aufgrund der 5-minütigen Eingangstoleranz gelöscht wird.

      Geht das Ereignis früher ein, schreitet das Wasserzeichen nicht voran.

   3. Beim vierten Ereignis (device3) und dem fünften Ereignis (device1) sind die Zeiten ausgerichtete und die Ereignisse werden ohne Anpassungen verarbeitet. Das Wasserzeichen schreitet bei jedem Ereignis voran.

   4. Wenn das sechste-Ereignis (device3) verarbeitet wird, liegen die Eingangszeit (12:17) und Ereigniszeit (12:12) unterhalb der Wasserzeichenstufen. Die Ereigniszeit wird auf die Wasserzeichenstufen (12:17) angepasst.

   5. Wenn das zwölfte Ereignis (device3) verarbeitet wird, liegen die Eingangszeit (12:27) und die Ereigniszeit (12:21) sechs Minuten auseinander. Die Richtlinie für Eingangsverzögerung wird angewendet. Die Ereigniszeit wird angepasst (12:22). Dies liegt über dem Wasserzeichen (12:21), sodass keine weiteren Anpassungen angewendet werden.

2. Zweite Illustration des Wasserzeichenfortschritts ohne angewendete Richtlinie für den frühen Eingang.

   ![Illustration der Azure Stream Analytics-Wasserzeichen ohne Richtlinie für den frühen Eingang](media/stream-analytics-time-handling/watermark-graph-2.png)

   In diesem Beispiel wird keine Richtlinie für den frühen Eingang angewendet. Ausreißerereignisse, die früher eingehen, heben das Wasserzeichen erheblich an. Beachten Sie, dass das dritte Ereignis (deviceId1 zum Zeitpunkt 12:11) in diesem Szenario nicht gelöscht und das Wasserzeichen auf 12:15 erhöht wird. Die Zeit des vierten Ereignisses wird daher 7 Minuten vorverlegt (von 12:08 auf 12:15).

3. In der endgültigen Illustration werden untergeordnete Datenströme (ÜBER der DeviceID) verwendet. Es werden mehrere Wasserzeichen nachverfolgt, eines pro Datenstrom. Es gibt weniger Ereignisse, bei denen die Zeiten entsprechend angepasst werden.

   ![Illustration der Azure Stream Analytics-Wasserzeichen für untergeordnete Datenströme](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Nächste Schritte

- [Überlegungen zur Ereignisreihenfolge in Azure Stream Analytics]()
- [Stream Analytics-Auftragsmetriken](stream-analytics-monitoring.md)