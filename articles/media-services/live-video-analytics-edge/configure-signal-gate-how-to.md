---
title: Konfigurieren eines Signalgates für ereignisbasierte Videoaufzeichnungen – Azure
description: In diesem Artikel wird erläutert, wie Sie ein Signalgate in einem Mediendiagramm konfigurieren.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410792"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurieren eines Signalgates für ereignisbasierte Videoaufzeichnungen

In einem Mediendiagramm ermöglicht ein [Signalgateprozessor-Knoten](media-graph-concept.md#signal-gate-processor) die Weiterleitung von Medien von einem Knoten zu einem anderen, wenn das Gate durch ein Ereignis ausgelöst wird. Wenn das Gate ausgelöst wird, öffnet es sich und erlaubt die Übertragung von Mediendaten für einen festgelegten Zeitraum. Wenn keine Ereignisse vorhanden sind, die das Gate auslösen, wird es geschlossen, und der Mediendatenfluss wird beendet. Sie können den Signalgateprozessor für ereignisbasierte Videoaufzeichnungen verwenden.

In diesem Artikel erfahren Sie, wie Sie einen Signalgateprozessor konfigurieren.

## <a name="suggested-prereading"></a>Empfohlene Vorablektüre
-   [Mediendiagramm](media-graph-concept.md)
-   [Ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)


## <a name="problem"></a>Problem
Ein Benutzer möchte eine Aufzeichnung zu einem bestimmten Zeitpunkt vor oder nach der Auslösung des Gates durch ein Ereignis starten. Der Benutzer kennt die akzeptable Wartezeit im System. Daher möchte er die Wartezeit des Signalgateprozessors angeben. Er möchte auch die minimale und die maximale Dauer der Aufzeichnung angeben, unabhängig davon, wie viele neue Ereignisse empfangen werden.
 
### <a name="use-case-scenario"></a>Anwendungsfall
Angenommen, Sie möchten jedes Mal ein Video aufzeichnen, wenn die Vordertür Ihres Gebäudes geöffnet wird. Die Aufzeichnung soll folgende Anforderungen erfüllen: 

- Sie soll die *X* Sekunden enthalten, bevor die Tür geöffnet wird. 
- Sie soll mindestens *Y* Sekunden dauern, wenn die Tür nicht erneut geöffnet wird. 
- Sie soll höchstens *Z* Sekunden dauern, wenn die Tür wiederholt geöffnet wird. 
 
Sie wissen, dass Ihr Türsensor eine Wartezeit von *K* Sekunden aufweist. Um die Wahrscheinlichkeit zu verringern, dass Ereignisse als zu spät eingetroffen ignoriert werden, möchten Sie mindestens *K* Sekunden Karenzzeit für das Eintreffen der Ereignisse einräumen.


## <a name="solution"></a>Lösung

Um dieses Problem zu lösen, ändern Sie die Parameter Ihres Signalgateprozessors.

Zum Konfigurieren eines Signalgateprozessors verwenden Sie die folgenden vier Parameter:
- Zeitfenster zur Auswertung der Aktivierung
- Offset für das Aktivierungssignal
- Mindestens gültiges Zeitfenster für die Aktivierung
- Höchstens gültiges Zeitfenster für die Aktivierung

Wenn der Signalgateprozessor ausgelöst wird, bleibt das Gate für den Mindestzeitraum der Aktivierung offen. Das Aktivierungsereignis beginnt beim Zeitstempel des frühesten Ereignisses plus Offset für das Aktivierungssignal. 

Wenn der Signalgateprozessor erneut ausgelöst wird, während das Gate noch offen ist, wird der Timer zurückgesetzt, und das Gate bleibt mindestens für den Mindestzeitraum der Aktivierung offen. Das Signalgate bleibt nie länger offen, als der maximale Zeitraum der Aktivierung dauert. 

Ein Ereignis (Ereignis 1), das gemäß Medienzeitstempel vor einem anderen Ereignis (Ereignis 2) eintritt, wird möglicherweise ignoriert, wenn das System Verzögerungen aufweist und Ereignis 1 nach Ereignis 2 im Signalgateprozessor eintrifft. Wenn Ereignis 1 nicht zwischen dem Eintreffen von Ereignis 2 und dem Zeitfenster zur Auswertung der Aktivierung eintrifft, wird es ignoriert. Es wird nicht durch den Signalgateprozessor geleitet. 

Für jedes Ereignis werden Korrelations-IDs festgelegt. Diese IDs werden ab dem ursprünglichen Ereignis festgelegt. Sie sind sequenziell für jedes folgende Ereignis.

> [!IMPORTANT]
> Die Medienzeit basiert auf dem Medienzeitstempel des Zeitpunkts, zu dem ein Ereignis im Medium auftritt. Die Reihenfolge, in der Ereignisse am Signalgate eintreffen, entspricht möglicherweise nicht der Reihenfolge, in der die Ereignisse zur Medienzeit eintreffen.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parameter basierend auf dem physischen Zeitpunkt des Eintreffens von Ereignissen am Signalgate

* **minimumActivationTime (kürzeste mögliche Dauer einer Aufzeichnung)** : Die Mindestanzahl von Sekunden, für die ein Signalgate nach dem Auslösen für den Empfang neuer Ereignisse offen bleibt, sofern es nicht durch die MaximumActivationTime unterbrochen wird.
* **maximumActivationTime (längste mögliche Dauer einer Aufzeichnung)** : Die maximale Anzahl von Sekunden ab dem ursprünglichen Ereignis, für die das Signalgate nach dem Auslösen für den Empfang neuer Ereignisse offen bleibt, unabhängig davon, welche Ereignisse empfangen werden.
* **activationSignalOffset**: Die Anzahl von Sekunden zwischen der Aktivierung des Signalgateprozessors und dem Start der Videoaufzeichnung. Dieser Wert ist in der Regel negativ, weil er die Aufzeichnung vor dem auslösenden Ereignis startet.
* **activationEvaluationWindow**: Die Anzahl von Sekunden (in Medienzeit) ab dem ursprünglichen auslösenden Ereignis, in denen ein Ereignis, das vor dem ursprünglichen Ereignis eingetreten ist, beim Signalgateprozessor eintreffen muss, bevor es als zu spät eingetroffen ignoriert wird.

> [!NOTE]
> Als *zu spät eingetroffen* gilt jedes Ereignis, das nach Ablauf des Zeitfensters zur Auswertung der Aktivierung, aber vor dem ursprünglichen Ereignis in Medienzeit eintrifft.

### <a name="limits-of-parameters"></a>Grenzwerte der Parameter

* **activationEvaluationWindow**: 0 bis 10 Sekunden
* **activationSignalOffset**: -1 bis 1 Minute
* **minimumActivationTime**: 1 Sekunde bis 1 Stunde
* **maximumActivationTime**: 1 Sekunde bis 1 Stunde


Im vorliegenden Szenario würden Sie die Parameter folgendermaßen festlegen:

* **activationEvaluationWindow**: *K* Sekunden
* **activationSignalOffset**: *-X* Sekunden
* **minimumActivationWindow**: *Y* Sekunden
* **maximumActivationWindow**: *Z* Sekunden


Im Folgenden finden Sie ein Beispiel dafür, wie der Abschnitt für den **Signalgateprozessor**-Knoten in einer Mediendiagrammtopologie für die folgenden Parameterwerte aussehen würde:
* **activationEvaluationWindow**: 1 Sekunde
* **activationSignalOffset**: -5 Sekunden
* **minimumActivationTime**: 20 Sekunden
* **maximumActivationTime**: 40 Sekunden

> [!IMPORTANT]
> Für alle Parameterwerte wird das [ISO 8601-Format für Zeitspannen](https://en.wikipedia.org/wiki/ISO_8601#Durations
) erwartet. Beispiel: PT1S = 1 Sekunde.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Sehen wir uns jetzt an, wie diese Konfiguration des Signalgateprozessors sich in verschiedenen Aufzeichnungsszenarien verhält.

### <a name="recording-scenarios"></a>Aufzeichnungsszenarien

**Ein Ereignis aus einer Quelle (*normale Aktivierung*)**

Ein Signalgateprozessor, der ein einziges Ereignis empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignal = 5 Sekunden) vor dem Eintreffen des Ereignisses am Gate startet. Der Rest der Aufzeichnung dauert 20 Sekunden (Mindestzeitraum der Aktivierung = 20 Sekunden), da vor dem Ende des Mindestzeitraums der Aktivierung keine weiteren Ereignisse eintreffen, die das Gate erneut auslösen.

Beispieldiagramm:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagramm der normalen Aktivierung eines einzigen Ereignisses aus einer einzigen Quelle":::

* Dauer der Aufzeichnung = -Offset + minimumActivationTime = [E1 + Offset, E1 + minimumActivationTime]


**Zwei Ereignisse aus einer einzigen Quelle (*erneut ausgelöste Aktivierung*)**

Ein Signalgateprozessor, der zwei Ereignisse empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignaloffset = 5 Sekunden) vor dem Eintreffen des Ereignisses am Gate startet. Ereignis 2 trifft 5 Sekunden nach Ereignis 1 ein. Da Ereignis 2 vor dem Ende des Mindestzeitraums der Aktivierung von Ereignis 1 (20 Sekunden) eintrifft, wird das Gate erneut ausgelöst. Der Rest der Aufzeichnung dauert 20 Sekunden (Mindestzeitraum der Aktivierung = 20 Sekunden), da vor dem Ende des Mindestzeitraums der Aktivierung von Ereignis 2 keine weiteren Ereignisse eintreffen, die das Gate erneut auslösen.

Beispieldiagramm:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagramm der erneut ausgelösten Aktivierung von zwei Ereignissen aus einer einzigen Quelle":::

* Dauer der Aufzeichnung = -Offset + (Eintreffen von Ereignis 2 - Eintreffen von Ereignis 1) + minimumActivationTime


***N* Ereignisse aus einer einzigen Quelle (*maximale Aktivierung*)**

Ein Signalgateprozessor, der *N* Ereignisse empfängt, führt zu einer Aufzeichnung, die 5 Sekunden (Aktivierungssignaloffset = 5 Sekunden) vor dem Eintreffen des ersten Ereignisses am Gate startet. Da jedes Ereignis vor dem Ende des Mindestzeitraums der Aktivierung (20 Sekunden nach dem vorherigen Ereignis) eintrifft, wird das Gate fortlaufend erneut ausgelöst. Es bleibt offen, bis der maximale Zeitraum der Aktivierung (40 Sekunden nach dem ersten Ereignis) abgelaufen ist. Dann wird das Gate geschlossen und akzeptiert keine neuen Ereignisse mehr.

Beispieldiagramm:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagramm des maximalen Zeitraums der Aktivierung von N Ereignissen aus einer einzigen Quelle":::
 
* Dauer der Aufzeichnung = -Offset + maximumActivationTime

> [!IMPORTANT]
> In den obigen Diagrammen wird davon ausgegangen, dass jedes Ereignis sowohl in physischer als auch in Medienzeit im selben Moment eintrifft. Anders gesagt: Es wird angenommen, dass kein Ereignis zu spät eintrifft.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie das [Tutorial zur ereignisbasierten Videoaufzeichnung](event-based-video-recording-tutorial.md) durch. Bearbeiten Sie zunächst die Datei [topology.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Ändern Sie die Parameter für den signalgateProcessor-Knoten, und gehen Sie dann den Rest des Tutorials durch. Sehen Sie sich die Videoaufzeichnungen an, um die Auswirkungen der verschiedenen Parameter zu analysieren.



