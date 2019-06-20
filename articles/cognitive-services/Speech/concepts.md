---
title: Bing-Spracheingabe-Konzepte | Microsoft-Dokumentation
titlesuffix: Azure Cognitive Services
description: Grundlegende Konzepte des Microsoft Speech Service.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cbf1514ac5eba4e288ecb78944878217fc5ba3e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954519"
---
# <a name="basic-concepts"></a>Grundlegende Konzepte

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Auf dieser Seite werden einige grundlegende Konzepte im Spracherkennungsdienst von Microsoft beschrieben. Wir empfehlen Ihnen, diese Seite zu lesen, bevor Sie die Spracherkennungs-API von Microsoft in der Anwendung verwenden.

## <a name="understanding-speech-recognition"></a>Grundlegendes zur Spracherkennung

Wenn Sie hier zum ersten Mal eine sprachaktivierte Anwendung erstellen oder einer vorhandenen Anwendung Sprachfunktionen hinzufügen, erleichtert dieser Abschnitt Ihnen den Einstieg. Wenn Sie bereits über Erfahrung mit sprachaktivierten Anwendungen verfügen, können Sie diesen Abschnitt auch überfliegen bzw. vollständig überspringen, wenn Sie über entsprechende Routine mit sprachfähigen Anwendungen verfügen und nur an den Protokolldetails interessiert sind.

### <a name="audio-streams"></a>Audiodatenströme

Der *Audiodatenstrom* steht bei den grundlegenden Konzepten der Sprache an erster Stelle. Im Gegensatz zu einem Tastendruck, der zu einem einzelnen Zeitpunkt auftritt und eine einzige Information enthält, ist eine gesprochene Anforderung über Hunderte von Millisekunden verteilt und enthält viele Kilobytes an Informationen. Die Dauer der gesprochenen Äußerungen stellt Entwickler, die in ihren Anwendungen eine optimierte und elegante Spracherfahrung bieten möchten, vor einige Probleme. Die heutigen Computer und Algorithmen führen eine Sprachtranskription in ungefähr der Hälfte der Dauer der Äußerung durch, d.h. eine Äußerung von 2 Sekunden Dauer kann in ungefähr 1 Sekunde transkribiert werden, aber jede Anwendung, bei der eine Verzögerung von 1 Sekunde bei der Verarbeitung auftritt, ist weder optimiert noch elegant.

Glücklicherweise gibt es Möglichkeiten, die Transkriptionszeit zu „verstecken“, indem die Transkription an einem Teil der Äußerung durchgeführt wird, während der Benutzer einen anderen Teil spricht. Wenn z. B. eine 1-Sekunden-Äußerung in 10 Blöcke von 100 Millisekunden aufgeteilt und ein Block nach dem anderen transkribiert wird, können über 450 der insgesamt für die Transkription erforderlichen 500 Millisekunden „versteckt“ werden, sodass der Benutzer nicht bemerkt, dass die Transkription durchgeführt wird, während er spricht. Beachten Sie bei diesem Beispiel, dass der Dienst die vorherigen 100 Millisekunden der Audioaufzeichnung transkribiert, während der Benutzer die nächsten 100 spricht; wenn der Benutzer also aufhört zu sprechen, muss der Dienst nur ungefähr 100 Millisekunden der Audioaufzeichnung transkribieren, um ein Ergebnis zu erzeugen.

Um diese Benutzererfahrung zu realisieren, werden gesprochene Audioinformationen in Blöcken erfasst und transkribiert, während der Benutzer spricht. Diese Audioblöcke stammen aus dem *Audiodatenstrom*, und das Senden dieser Audioblöcke an den Dienst wird als *Audiostreaming* bezeichnet. Audiostreaming ist ein wichtiger Teil jeder sprachaktivierten Anwendung; das Abstimmen der Blockgröße und Optimieren der Streamingimplementierung gehören zu den wirkungsvollsten Maßnahmen zur Verbesserung der Benutzerfreundlichkeit Ihrer Anwendung.

### <a name="microphones"></a>Mikrofone

Personen nehmen Audioinformationen mit ihren Ohren wahr, unbelebte Hardware benötigt hierzu Mikrofone. Um Sprache in einer Anwendung zu aktivieren, müssen Sie das Mikrofon, das den Audiodatenstrom liefert, in Ihre Anwendung integrieren.

Die APIs für Ihr Mikrofon müssen Ihnen ermöglichen, die Aufnahme von Audiobytes mit dem Mikrofon zu beginnen und zu beenden. Sie müssen entscheiden, welche Benutzeraktionen die Aufnahmebereitschaft des Mikrofons auslösen. Sie entscheiden, ob die Aufnahmebereitschaft durch einen Tastendruck ausgelöst wird, oder ob ein Spotter das Mikrofon permanent überwacht, der auf ein *Schlüsselwort* oder *Aktivierungswort* reagiert, und die Ausgabe dieses Moduls verwendet wird, um das Senden von Audiodaten an den Microsoft Speech Service auszulösen.

### <a name="end-of-speech"></a>Ende der Spracheingabe

Zu erkennen, *wann* ein Sprecher *aufhört* zu sprechen, ist für Menschen kein Problem, aber außerhalb von Laborbedingungen ziemlich schwierig. Es reicht nicht aus, einfach auf völlige Stille nach einer Äußerung zu reagieren, da Umgebungsgeräusche dies häufig erschweren. Der Microsoft Speech Service funktioniert hervorragend, wenn es darum geht, schnell zu erkennen, wenn ein Benutzer aufhört zu sprechen, und der Dienst kann Ihre Anwendung hierüber informieren, doch in dieser Konstellation erfährt ihre Anwendung zuletzt, dass der Benutzer aufhört zu sprechen. Dies unterscheidet sich grundlegend von anderen Formen der Eingabe, bei denen Ihre Anwendung als *erste* weiß, wann die Benutzereingabe beginnt *und* endet.

### <a name="asynchronous-service-responses"></a>Asynchrone Dienstantworten

Aus der Tatsache, dass Ihre Anwendung über den Abschluss der Benutzereingabe informiert sein muss, resultieren keine Leistungseinbußen oder Komplikationen bei der Programmierung der Anwendung, aber dies erfordert, dass Sie Überlegungen zu Sprachanforderungen anstellen, die sich von den Anforderungs-/Antwortmustern unterscheiden, mit denen Sie vertraut sind. Da Ihre Anwendung nicht weiß, wann der Benutzer aufhört zu sprechen, muss die Anwendung das Audiostreaming an den Dienst fortsetzen und gleichzeitig und asynchron auf eine Antwort des Diensts warten. Dieses Muster unterscheidet sich von Anforderungs-/Antwortwebprotokollen wie HTTP. In diesen Protokollen müssen Sie eine Anforderung abschließen, bevor Sie eine Antwort empfangen; beim Protokoll des Microsoft Speech Service erhalten Sie Antworten, *während Sie weiterhin Audiodaten für die Anforderung übertragen*.

> [!NOTE]
> Diese Funktion wird bei Verwendung der Spracherkennungs-HTTP-REST-API nicht unterstützt.

### <a name="turns"></a>Turns

Sprache ist ein Informationsträger. Wenn Sie sprechen, möchten Sie Informationen, über die Sie verfügen, einer Person vermitteln, die diese Informationen hört. Wenn Sie Informationen vermitteln, sprechen und hören Sie in der Regel abwechselnd. Ebenso interagiert Ihre sprachaktivierte Anwendung mit Benutzern durch abwechselndes Hören und Antworten, auch wenn bei Ihrer Anwendung in der Regel das Hören überwiegt. Die gesprochene Benutzereingabe und die Antwort des Diensts auf diese Eingabe wird als *Turn* bezeichnet. Ein *Turn* beginnt, wenn der Benutzer spricht, und endet, wenn Ihre Anwendung die Behandlung der Antwort des Spracherkennungsdiensts abgeschlossen hat.

### <a name="telemetry"></a>Telemetrie

Das Erstellen eines sprachaktivierten Geräts oder einer Anwendung kann auch für erfahrene Entwickler eine Herausforderung sein. Protokolle auf Datenstrombasis wirken auf den ersten Blick entmutigend, und wichtige Details wie die Erkennung der Stille sind möglicherweise völlig neu. Da sehr viele Meldungen erfolgreich gesendet und empfangen werden müssen, um ein einzelnes Anforderung/Antwort-Paar abzuschließen, ist es *sehr* wichtig, vollständige und genaue Daten zu diesen Meldungen zu sammeln. Das Protokoll des Microsoft Speech Service sorgt für die Erfassung dieser Daten. Stellen Sie unbedingt sicher, dass Sie die erforderlichen Daten so genau wie möglich bereitstellen; mit vollständigen und genauen Daten helfen Sie sich selbst – sollten Sie jemals Hilfe vom Team des Microsoft Speech Service bei der Problembehandlung Ihrer Clientimplementierung benötigen, ist die Qualität der von Ihnen gesammelten Telemetriedaten für die Problemanalyse entscheidend.

> [!NOTE]
> Diese Funktion wird bei Verwendung der Spracherkennungs-REST-API nicht unterstützt.

### <a name="speech-application-states"></a>Anwendungszustände der Spracherkennung

Die Schritte, mit denen Sie die Spracheingabe in Ihrer Anwendung aktivieren, unterscheiden sich ein wenig von den Schritten für andere Formen der Eingabe, z.B. Mausklicks oder Tippen mit dem Finger. Sie müssen verfolgen, wann Ihre Anwendung auf das Mikrofon hört und Daten an den Spracherkennungsdienst sendet, wann sie auf eine Antwort des Diensts wartet, und wann sie sich im Leerlauf befindet. Im folgenden Diagramm wird die Beziehung zwischen diesen Zuständen gezeigt.

![Diagramm der Zustände der Spracherkennungsanwendung](Images/speech-application-state-diagram.png)

Da der Microsoft Speech Service an einigen der Zustände beteiligt ist, definiert das Dienstprotokoll Meldungen, die Ihrer Anwendung den Übergang zwischen Zuständen erleichtern. Ihre Anwendung muss diese Protokollmeldungen interpretieren und verarbeiten, um die Zustände der Spracherkennungsanwendung zu verfolgen und zu verwalten.

## <a name="using-the-speech-recognition-service-from-your-apps"></a>Nutzung des Spracherkennungsdiensts durch Ihre Apps

Der Spracherkennungsdienst von Microsoft bietet Entwicklern zwei Möglichkeiten, ihre Apps mit Sprachfunktionen zu erweitern.

- [REST-APIs](GetStarted/GetStartedREST.md): Entwickler können in ihren Apps HTTP-Aufrufe an den Spracherkennungsdienst richten.
- [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md): Entwickler, die erweiterte Features nutzen möchten, können Microsoft Speech-Clientbibliotheken herunterladen und in ihre Apps einbinden.  Die Clientbibliotheken sind für verschiedene Plattformen (Windows, Android, iOS) mit verschiedenen Sprachen (C#, Java, JavaScript, Objective-C) verfügbar.

| Anwendungsfälle | [REST-APIs](GetStarted/GetStartedREST.md) | [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertieren von kurzem gesprochenem Audio mit einer Länge von weniger als 15 Sekunden (beispielsweise Befehle) ohne Zwischenergebnisse | Ja | Ja |
| Konvertieren von langem Audio (länger als 15 Sekunden) | Nein | Ja |
| Streamen von Audio mit Zwischenergebnissen | Nein | Ja |
| Verstehen des aus Audio konvertierten Texts mithilfe von LUIS | Nein | Ja |

 Wenn Ihre Sprache oder Plattform nicht über ein SDK verfügt, können Sie eine eigene Implementierung auf Basis der [Protokolldokumentation](API-Reference-REST/websocketprotocol.md) erstellen.

## <a name="recognition-modes"></a>Erkennungsmodi

Es gibt drei Erkennungsmodi: `interactive`, `conversation` und `dictation`. Der Erkennungsmodus passt Spracherkennung nach der Art und Weise an, wie Benutzer wahrscheinlich sprechen. Wählen Sie den entsprechenden Erkennungsmodus für Ihre Anwendung aus.

> [!NOTE]
> Erkennungsmodi zeigen möglicherweise im REST-Protokoll andere Verhaltensweisen als im WebSocket-Protokoll. Die REST-API unterstützt z.B. auch im Konversations- oder Diktiermodus keine kontinuierliche Erkennung.
> [!NOTE]
> Diese Modi gelten, wenn Sie direkt das REST- oder WebSocket-Protokoll verwenden. Die [Clientbibliotheken](GetStarted/GetStartedClientLibraries.md) verwenden verschiedene Parameter, um den Erkennungsmodus anzugeben. Weitere Informationen finden Sie in der Clientbibliothek Ihrer Wahl.

Der Microsoft Speech Service gibt nur ein Erkennungsausdruckergebnis für alle Erkennungsmodi zurück. Für jede einzelne Äußerung gilt ein Limit von 15 Sekunden.

### <a name="interactive-mode"></a>Interaktiver Modus

Im `interactive`-Modus stellt ein Benutzer kurze Anforderungen und erwartet, dass die Anwendung mit einer Aktion antwortet.

Die folgenden Eigenschaften sind typisch für Anwendungen im interaktiven Modus:

- Benutzer wissen, dass sie mit einem Computer und nicht mit einer anderen Person sprechen.
- Anwendungsbenutzer wissen im Voraus, was sie sagen möchten, da sie von der Anwendung bestimmte Aktionen erwarten.
- Äußerungen dauern i.d.R. 2 bis 3 Sekunden.

### <a name="conversation-mode"></a>Konversationsmodus

Im `conversation`-Modus sind Benutzer an einer Konversation zwischen Personen beteiligt.

Die folgenden Eigenschaften sind typisch für Anwendungen im Konversationsmodus:

- Benutzer wissen, dass sie mit einer anderen Person sprechen.
- Spracherkennung verbessert menschliche Konversationen, weil ein oder beide Teilnehmer die Anzeige des gesprochenen Texts sehen können.
- Benutzer planen nicht immer, was sie sagen möchten.
- Benutzer verwenden häufig Umgangssprache und andere informelle Sprachformen.

### <a name="dictation-mode"></a>Diktiermodus

Im `dictation`-Modus tragen Benutzer der Anwendung längere Äußerungen zur weiteren Verarbeitung vor.

Die folgenden Eigenschaften sind typisch für Anwendungen im Diktiermodus:

- Benutzer wissen, dass sie mit einem Computer sprechen.
- Benutzern werden die Textergebnisse der Spracherkennung angezeigt.
- Benutzer planen häufig, was sie sagen möchten, und verwenden eine formalere Sprache.
- Benutzer sprechen in vollständigen Sätzen, die 5 bis 8 Sekunden dauern.

> [!NOTE]
> Im Diktier- und Konversationsmodus gibt der Microsoft Speech Service keine Teilergebnisse zurück. Stattdessen gibt der Dienst nach Stillephasen im Audiodatenstrom stabile Ausdrücke als Ergebnisse zurück. Microsoft könnte das Spracherkennungsprotokoll überarbeiten, um die Benutzerfreundlichkeit in diesen Modi der fortlaufenden Erkennung zu verbessern.

## <a name="recognition-languages"></a>Erkennungssprachen

Mit der *Erkennungssprache* wird die Sprache angegeben, die der Benutzer der Anwendung spricht. Geben Sie die *Erkennungssprache* mit dem URL-Abfrageparameter *language* für die Verbindung an. Der Wert des Abfrageparameters *language* verwendet das IETF-Sprachtag [BCP-47](https://en.wikipedia.org/wiki/IETF_language_tag) und **muss** eine der Sprachen sein, die von der Spracherkennungs-API unterstützt werden. Eine vollständige Liste mit den vom Microsoft Speech Service unterstützten Sprachen finden Sie unter [Unterstützte Sprachen](API-Reference-REST/supportedlanguages.md).

Der Microsoft Speech Service weist ungültige Verbindungsanforderungen durch Anzeigen einer `HTTP 400 Bad Request`-Antwort zurück. Merkmale einer ungültigen Anforderung:

- Sie enthält keinen Wert für den *language*-Abfrageparameter.
- Sie enthält einen falsch formatierten *language*-Abfrageparameter.
- Sie enthält einen *language*-Abfrageparameter, der keiner unterstützten Anzeigesprache entspricht.

Sie können wahlweise eine Anwendung erstellen, die eine oder alle Sprachen unterstützt, die vom Dienst unterstützt werden.

### <a name="example"></a>Beispiel

Im folgenden Beispiel verwendet eine Anwendung den *Konversationsmodus* der Spracherkennung für einen US-Englisch sprechenden Benutzer.

```HTTP
https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US
```

## <a name="transcription-responses"></a>Transkriptionsantworten

Die Transkriptionsantworten geben den aus Audiodaten konvertierten Text an Clients zurück. Eine Transkriptionsantwort enthält die folgenden Felder:

- `RecognitionStatus` gibt den Status der Erkennung an. In der folgenden Tabelle sind die möglichen Werte angegeben.

| Status | BESCHREIBUNG |
| ------------- | ---------------- |
| Erfolgreich | Die Erkennung war erfolgreich und das DisplayText-Feld ist vorhanden. |
| NoMatch | Im Audiodatenstrom wurde Sprache erkannt, aber es wurde keine Übereinstimmung mit Wörtern aus der Zielsprache festgestellt. Weitere Details finden Sie unter [NoMatch-Erkennungsstatus(#nomatch-recognition-status)].  |
| InitialSilenceTimeout | Der Anfang des Audiodatenstroms enthielt nur Stille, und beim Warten auf Sprache wurde der Timeout des Diensts aktiviert. |
| BabbleTimeout | Der Anfang des Audiodatenstroms enthielt nur Rauschen, und beim Warten auf Sprache wurde der Timeout des Diensts aktiviert. |
| Error | Der Erkennungsdienst hat einen internen Fehler erkannt und konnte nicht fortgesetzt werden. |

- `DisplayText` stellt den erkannten Ausdruck nach Anwendung von Großschreibung, Interpunktion und Inverse Text Normalization sowie Maskierung anstößiger Ausdrücke mit Sternchen dar. Das DisplayText-Feld ist *nur* vorhanden, wenn das `RecognitionStatus`-Feld den Wert `Success` hat.

- `Offset` gibt den Versatz (in Einheiten von 100 Nanosekunden) an, der zwischen der Erkennung des Ausdrucks und dem Beginn des Audiodatenstroms besteht.

- `Duration` gibt die Dauer (in Einheiten von 100 Nanosekunden) dieses gesprochenen Ausdrucks an.

Eine Transkriptionsantwort gibt mehr Informationen zurück, falls gewünscht. Unter [Ausgabeformat](#output-format) erfahren Sie, wie ausführlichere Ausgaben zurückgegeben werden.

Der Microsoft Speech Service unterstützt einen zusätzlichen Transkriptionsprozess, der Hinzufügen von Großschreibung und Interpunktion, Maskierung anstößiger Ausdrücke und Normalisieren von Text in häufig verwendete Formen beinhaltet. Wenn ein Benutzer z.B. einen Ausdruck verwendet, der mit den Wörtern „erinnere mich daran, sechs iPhones zu kaufen“ dargestellt wird, gibt der Microsoft Speech Service den transkribierten Text „Erinnere mich daran, sechs iPhones zu kaufen“ zurück. Der Prozess, der das Wort „sechs“ in die Zahl „6“ konvertiert, wird als *Inverse Text Normalization*  (*ITN*) bezeichnet.

### <a name="nomatch-recognition-status"></a>NoMatch-Erkennungsstatus

Die Transkriptionsantwort gibt `NoMatch` in `RecognitionStatus` zurück, wenn der Microsoft Speech Service Sprache im Audiodatenstrom erkennt, jedoch keine Übereinstimmung dieser Sprache mit der für die Anforderung verwendeten Grammatik feststellen kann. Eine *NoMatch*-Bedingung kann z.B. auftreten, wenn ein Benutzer etwas auf Deutsch sagt, während die Erkennung US-Englisch als gesprochenen Sprache erwartet. Das Wellenformmuster der Äußerung würde das Vorhandensein menschlicher Sprache angeben, aber keines der gesprochenen Wörter würde mit dem von der Erkennung für US-Englisch verwendeten Lexikon übereinstimmen.

Eine andere *NoMatch*-Bedingung liegt vor, wenn der Erkennungsalgorithmus keine genaue Übereinstimmung mit den im Audiodatenstrom enthaltenen Lauten finden kann. Wenn dies der Fall ist, kann der Microsoft Speech Service *speech.hypothesis*-Meldungen erzeugen, die *hypothetischen Text* enthalten, er erzeugt jedoch eine *speech.phrase*-Meldung mit dem *RecognitionStatus* *NoMatch*. Diese Bedingung ist normal. Sie müssen keine Annahmen über die Genauigkeit des Texts in der *speech.hypothesis*-Meldung machen. Darüber hinaus dürfen Sie nicht davon ausgehen, dass der Dienst eine *speech.phrase*-Meldung mit dem  *RecognitionStatus* *Success* produzieren kann, weil der Microsoft Speech Service *speech.hypothesis*-Meldungen produziert.

## <a name="output-format"></a>Ausgabeformat

Microsoft Speech Service kann eine Vielzahl von Nutzlastformaten in Transkriptionsantworten zurückgeben. Alle Nutzlasten sind JSON-Strukturen.

Sie können das Ausdrucksergebnisformat durch Angeben des `format`-URL-Abfrageparameters festlegen. Standardmäßig gibt der Dienst `simple`-Ergebnisse zurück.

| Format | BESCHREIBUNG |
|-----|-----|
| `simple` | Ein vereinfachtes Ausdrucksergebnis mit Erkennungsstatus und dem erkanntem Text in Darstellungsform. |
| `detailed` | Ein Erkennungsstatus und eine Liste der N besten Ausdrucksergebnisse, wo jedes Ausdrucksergebnis alle vier Erkennungsformen und eine Zuverlässigkeitsbewertung enthält. |

Die Antwort des `detailed`-Formats enthält [N beste Werte](#n-best-values) ergänzend zu `RecognitionStatus`, `Offset` und `duration`.

### <a name="n-best-values"></a>N beste Werte

Listener, ob Personen oder Computer, können nie sicher sein, dass sie *genau* das gehört haben, was gesprochen wurde. Ein Listener kann nur einer bestimmten Interpretation einer Äußerung eine *Wahrscheinlichkeit* zuweisen.

Unter normalen Umständen ist die Wahrscheinlichkeit hoch, dass Personen im Gespräch mit anderen, mit denen sie häufig interagieren, die gesprochenen Wörter erkennen. Sprachlistener auf Computerbasis streben ein ähnliches Genauigkeitsniveau an, und unter den richtigen Bedingungen [erzielen sie einen Gleichstand mit Menschen](https://blogs.microsoft.com/next/2016/10/18/historic-achievement-microsoft-researchers-reach-human-parity-conversational-speech-recognition/#sm.001ykosqs14zte8qyxj2k9o28oz5v).

Die zur Spracherkennung verwendeten Algorithmen untersuchen im Rahmen der normalen Verarbeitung alternative Interpretationen einer Äußerung. Diese Alternativen werden in der Regel verworfen, sobald die Beweise zugunsten einer einzelnen Interpretation überzeugend sind. Unter nicht optimalen Umständen endet die Spracherkennung jedoch mit einer Liste alternativer möglicher Interpretationen. Die oberen *N* Alternativen in dieser Liste werden als *Liste der N besten* bezeichnet. Jeder Alternative wird eine [Zuverlässigkeitsbewertung](#confidence) zugewiesen. Zuverlässigkeitsbewertungen liegen zwischen 0 und 1. Eine Bewertung von 1 stellt das höchste Maß an Zuverlässigkeit dar. Eine Bewertung von 0 stellt das niedrigste Maß an Zuverlässigkeit dar.

> [!NOTE]
> Die Anzahl der Einträge in der Liste der N besten variiert über mehrere Äußerungen. Die Anzahl der Einträge kann über mehrere Erkennungen der *gleichen* Äußerung variieren. Diese Variation ist ein natürliches und erwartetes Ergebnis des probabilistischen Wesens des Spracherkennungsalgorithmus.

Jeder in der Liste der N besten zurückgegebene Eintrag enthält

- `Confidence`, was die [Zuverlässigkeitsbewertungen](#confidence) dieses Eintrags darstellt.
- `Lexical`, was die [lexikalische Form](#lexical-form) des erkannten Texts darstellt.
- `ITN`, was die [ITN-Form](#itn-form) des erkannten Texts darstellt.
- `MaskedITN`, was die [maskierte ITN-Form](#masked-itn-form) des erkannten Texts darstellt.
- `Display`, was die [Anzeigeform](#display-form) des erkannten Texts darstellt.

### Zuverlässigkeitsbewertungen <a id="confidence"></a>

Zuverlässigkeitsbewertungen sind integraler Bestandteil von Spracherkennungssystemen. Der Microsoft Speech Service erhält Zuverlässigkeitsbewertungen von einer *Zuverlässigkeitsklassifizierung*. Microsoft trainiert die Klassifizierung anhand eines Satzes von Features, die zur genauesten Unterscheidung zwischen richtiger und falscher Erkennung konzipiert sind. Zuverlässigkeitsbewertungen werden für einzelne Wörter und gesamte Äußerungen durchgeführt.

Wenn Sie entscheiden, die vom Dienst zurückgegebenen Zuverlässigkeitsbewertungen zu verwenden, achten Sie auf folgendes Verhalten:

- Zuverlässigkeitsbewertungen können nur im Rahmen desselben Erkennungsmodus und derselben Sprache verglichen werden. Vergleichen Sie Bewertungen nicht Sprachen oder Erkennungsmodi übergreifend. Beispielsweise besteht *keine* Korrelation zwischen einer Zuverlässigkeitsbewertung im interaktivem Erkennungsmodus und einer Zuverlässigkeitsbewertung im Diktiermodus.
- Zuverlässigkeitsbewertungen werden am besten auf einen eingeschränkten Satz von Äußerungen angewendet. Bei einem großen Satz von Äußerungen liegt natürlich eine hohe Variabilität der Bewertungen vor.

Wenn Sie entscheiden, einen Zuverlässigkeitsbewertungswert als *Schwellenwert* für Ihre Anwendung zu verwenden, legen Sie die Schwellenwerte mithilfe der Spracherkennung fest.

- Führen Sie die Spracherkennung für eine repräsentative Stichprobe von Äußerungen für Ihre Anwendung aus.
- Erfassen Sie die Zuverlässigkeitsbewertungen für jede Erkennung im Stichprobensatz.
- Legen Sie ein Zuverlässigkeitsquantil für diese Stichprobe als Grundlage des Schwellenwerts fest.

Kein einzelner Schwellenwert eignet sich für alle Anwendungen. Eine akzeptable Zuverlässigkeitsbewertung für eine Anwendung ist möglicherweise für eine andere Anwendung inakzeptabel.

### <a name="lexical-form"></a>Lexikalische Form

Die lexikalische Form ist der erkannte Text in genau der Form, in der er in der Äußerung vorkommt, ohne Interpunktion oder Großschreibung. Beispielsweise wäre die lexikalische Form der Adresse „1020 Enterprise Way“ *ten twenty enterprise way*, vorausgesetzt, sie wird auf diese Weise ausgesprochen. Die lexikalische Form des Satzes „Remind me to buy 5 pencils“ ist *remind me to buy five pencils*.

Die lexikalische Form ist am besten geeignet für Anwendungen, die eine nicht standardmäßige Textnormalisierung ausführen müssen. Die lexikalische Form eignet sich auch für Anwendungen, die nicht verarbeitete Erkennungswörter benötigen.

Anstößige Ausdrücke werden in der lexikalischen Form nie maskiert.

### <a name="itn-form"></a>ITN-Form

Textnormalisierung ist das Konvertieren von Text von einer Form in eine andere „kanonische“ Form. Z.B. könnte die Telefonnummer „555-1212“ in die kanonische Form *five five five one two one two* konvertiert werden. *Inverse* Text Normalization (ITN) kehrt diesen Prozess um und konvertiert die Wörter „five five five one two one two“ in die invertierte kanonische Form *555-1212*. Die ITN-Form eines Erkennungsergebnisses umfasst nicht Großschreibung oder Interpunktion.

Die ITN-Form eignet sich am besten für Anwendungen, die den erkannten Text bearbeiten. Beispielsweise würde eine Anwendung, die einem Benutzer ermöglicht, Suchbegriffe zu sprechen, und diese Begriffe dann in einer Webabfrage verwendet, die ITN-Form verwenden. Anstößige Ausdrücke werden in der ITN-Form nie maskiert. Um anstößige Ausdrücke zu maskieren, verwenden Sie die *maskierte ITN-Form*.

### <a name="masked-itn-form"></a>Maskierte ITN-Form

Da anstößige Ausdrücke natürlich Teil der gesprochenen Sprache sind, erkennt der Microsoft Speech Service solche Wörter und Ausdrücke, wenn sie ausgesprochen werden. Anstößige Ausdrücke sind sicher nicht in allen Anwendungen angemessen, insbesondere nicht in Anwendungen, deren Zielgruppe Kinder und Jugendliche sind.

Bei der maskierten ITN-Form wird die Maskierung anstößiger Ausdrücke auf die ITN-Form angewendet. Legen Sie zur Maskierung anstößiger Ausdrücke den Wert des Parameters für anstößige Ausdrücke auf `masked` fest. Bei der Maskierung anstößiger Ausdrücke werden Wörter, die als Teil des Lexikons für anstößige Ausdrücke der Sprache erkannt werden, durch Sternchen ersetzt. Beispiel: *remind me to buy 5 **** pencils*. Die maskierte ITN-Form eines Erkennungsergebnisses umfasst nicht Großschreibung oder Interpunktion.

> [!NOTE]
> Wenn der Abfrageparameterwert für anstößige Ausdrücke auf `raw` festgelegt ist, entspricht die maskierte ITN-Form der ITN-Form. Anstößige Ausdrücke werden *nicht* maskiert.

### <a name="display-form"></a>Darstellungsform

Interpunktion und Großschreibung signalisieren eine Hervorhebung, wo eine Pause einzulegen ist etc., um das Textverständnis zu erleichtern. In der Darstellungsform werden Interpunktion und Großschreibung den Erkennungsergebnissen hinzugefügt, sodass sie die am besten geeignete Form für Anwendungen ist, die den gesprochenen Text darstellen.

Da die Darstellungsform eine Erweiterung der maskierten ITN-Form ist, können Sie den Parameterwert für anstößige Ausdrücke auf `masked` oder `raw` festlegen. Wenn der Wert auf `raw` festgelegt ist, enthalten die Erkennungsergebnisse alle vom Benutzer ausgesprochenen anstößigen Ausdrücke. Wenn der Wert auf `masked` festgelegt ist, werden Wörter, die als Teil des Lexikons für anstößige Ausdrücke der Sprache erkannt werden, durch Sternchen ersetzt.

### <a name="sample-responses"></a>Beispielantworten

Alle Nutzlasten sind JSON-Strukturen.

Das Nutzlastformat des `simple`-Ausdrucksergebnisses:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Das Nutzlastformat des `detailed`-Ausdrucksergebnisses:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="profanity-handling-in-speech-recognition"></a>Behandlung anstößiger Ausdrücke in der Spracherkennung

Der Microsoft Speech Service erkennt alle Formen der menschlichen Sprache, einschließlich solcher Wörter und Ausdrücke, die häufig als „anstößig“ klassifiziert werden. Mit dem *profanity*-Abfrageparameter können Sie steuern, wie der Dienst anstößige Ausdrücke behandelt. Standardmäßig maskiert der Dienst anstößige Ausdrücke in *speech.phrase*-Ergebnissen und gibt keine *speech.hypothesis*-Meldungen zurück, die anstößige Ausdrücke enthalten.

| *Profanity*-Wert | BESCHREIBUNG |
| - | - |
| `masked` | Maskiert anstößige Ausdrücke mit Sternchen. Dies ist das Standardverhalten. |
| `removed` | Entfernt anstößige Ausdrücke aus allen Ergebnissen. |
| `raw` | Erkennt anstößige Ausdrücke in allen Ergebnissen und gibt sie zurück. |

### <a name="profanity-value-masked"></a>Profanity-Wert `Masked`

Legen Sie zur Maskierung anstößiger Ausdrücke *masked* als Wert des *profanity*-Abfrageparameters fest. Wenn der *profanity*-Abfrageparameter diesen Wert aufweist oder nicht für eine Anforderung angegeben ist, *maskiert* der Dienst anstößige Ausdrücke. Der Dienst führt die Maskierung durch, indem anstößige Ausdrücke in den Erkennungsergebnissen durch Sternchen ersetzt werden. Wenn Sie die Maskierung anstößiger Ausdrücke angeben, gibt der Dienst keine *speech.hypothesis*-Meldungen zurück, die anstößige Ausdrücke enthalten.

### <a name="profanity-value-removed"></a>Profanity-Wert `Removed`

Wenn der *profanity*-Abfrageparameter den Wert *removed* hat, entfernt der Dienst anstößige Ausdrücke sowohl aus *speech.phrase*- als auch *speech.hypothesis*-Meldungen. Die Ergebnisse sind dann so, *als ob die anstößigen Ausdrücke nicht gesprochen worden seien*.

#### <a name="profanity-only-utterances"></a>Äußerungen, die nur aus anstößigen Ausdrücken bestehen

Angenommen, ein Benutzer spricht *nur* anstößige Ausdrücke, wenn eine Anwendung den Dienst so konfiguriert hat, dass anstößige Ausdrücke entfernt werden. Wenn der Erkennungsmodus in diesem Szenario *Diktat* oder *Konversation* ist, gibt der Dienst kein *speech.result* zurück. Wenn der Erkennungsmodus *interaktiv* ist, gibt der Dienst ein *speech.result* mit dem Statuscode *NoMatch* zurück.

### <a name="profanity-value-raw"></a>Profanity-Wert `Raw`

Wenn der *profanity*-Abfrageparameter den Wert *raw* hat, entfernt der Dienst anstößige Ausdrücke weder aus *speech.phrase*- noch *speech.hypothesis*-Meldungen und maskiert sie auch nicht.
