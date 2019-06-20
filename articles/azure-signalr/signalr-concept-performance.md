---
title: Leitfaden zur Leistung für Azure SignalR Service
description: Enthält eine Übersicht über die Leistung von Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269440"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Leitfaden zur Leistung für Azure SignalR Service

Einer der Hauptvorteile der Verwendung von Azure SignalR Service ist die einfache Skalierung von SignalR-Anwendungen. In einem Szenario mit großem Umfang ist die Leistung ein wichtiger Faktor. 

In diesem Leitfaden werden die Faktoren vorgestellt, die sich auf die Leistung der SignalR-Anwendung auswirken. Es werden typische Leistungen in verschiedenen Anwendungsszenarien beschrieben. Am Ende stellen wir Ihnen die Umgebung und die Tools vor, mit denen Sie einen Leistungsbericht generieren können.

## <a name="term-definitions"></a>Begriffsdefinitionen

*Eingehend*: Die eingehende Nachricht an den Azure SignalR Service.

*Ausgehend*: Die ausgehende Nachricht vom Azure SignalR Service.

*Bandbreite*: Die Gesamtgröße aller Nachrichten in einer Sekunde.

*Standardmodus*: Der Standardarbeitsmodus, wenn eine Azure SignalR Service-Instanz erstellt wurde. Der Azure SignalR Service erwartet, dass der App-Server eine Verbindung mit ihm aufbaut, bevor Clientverbindungen akzeptiert werden.

*Serverloser Modus*: Ein Modus, in dem der Azure SignalR Service nur Clientverbindungen akzeptiert. Es ist keine Serververbindung gestattet.

## <a name="overview"></a>Übersicht

Azure SignalR Service definiert sieben Standard-Tarife für unterschiedliche Leistungskapazitäten. Dieser Leitfaden beantwortet die folgenden Fragen:

-   Was ist die typische Azure SignalR Service-Leistung für die einzelnen Tarife?

-   Erfüllt der Azure SignalR Service meine Anforderungen an den Nachrichtendurchsatz (z. B. Senden von 100.000 Nachrichten pro Sekunde)?

-   Welcher Tarif ist für mein bestimmtes Szenario geeignet? Oder wie kann ich den richtigen Tarif auswählen?

-   Welche Art von App-Server (VM-Größe) ist für mich geeignet? Wie viele sollte ich bereitstellen?

In diesem Leitfaden werden zunächst die Faktoren, die sich auf die Leistung auswirken, auf hoher Ebene erläutert. Anschließend werden für typische Anwendungsfälle die maximalen ein- und ausgehenden Nachrichten für die einzelnen Tarife dargestellt: **Echo**, **Broadcast**, **An Gruppe senden** und **An Verbindung senden** (Peer-zu-Peer-Chat).

Dieser Leitfaden kann nicht alle Szenarien abdecken (und verschiedene Anwendungsfälle, Nachrichtengrößen, Nachrichtensendemuster usw.). Aber er bietet einige hilfreiche Methoden:

- Bewerten Sie Ihre ungefähre Anforderung hinsichtlich der ein- oder ausgehenden Nachrichten.
- Suchen Sie die richtigen Tarife, indem Sie die Leistungstabelle überprüfen.

## <a name="performance-insight"></a>Leistungsübersicht

Dieser Abschnitt beschreibt die Methoden zur Leistungsauswertung und listet anschließend alle Faktoren auf, die sich auf die Leistung auswirken. Am Ende werden Methoden bereitgestellt, die Ihnen helfen, die Leistungsanforderungen auszuwerten.

### <a name="methodology"></a>Methodik

*Durchsatz* und *Latenz* sind zwei typische Aspekte der Leistungsüberprüfung. Für Azure SignalR Service verfügt jeder SKU-Tarif über eine eigene Richtlinie zur Durchsatzdrosselung. Die Richtlinie definiert *den maximal zulässigen Durchsatz (ein- und ausgehende Bandbreite)* als den maximal erreichten Durchsatz, wenn 99 Prozent der Nachrichten eine Latenzzeit von weniger als 1 Sekunde aufweisen.

Die Latenzzeit ist die Zeitspanne der Verbindung vom Senden der Nachricht bis zum Empfangen der Antwortnachricht von Azure SignalR Service. Nehmen wir **Echo** als Beispiel. Jede Clientverbindung fügt der Nachricht einen Zeitstempel hinzu. Der Hub des App-Servers sendet die ursprüngliche Nachricht an den Client zurück. So kann die Weitergabeverzögerung von den einzelnen Clientverbindungen leicht berechnet werden. Der Zeitstempel wird für jede Nachricht in **Broadcast**, **An Gruppe senden** und **An Verbindung senden** angehängt.

Um Tausende von gleichzeitigen Clientverbindungen zu simulieren, werden in Azure mehrere virtuelle Computer in einem virtuellen privaten Netzwerk erstellt. Alle diese virtuellen Computer verbinden sich mit derselben Azure SignalR Service-Instanz.

Im Standardmodus von Azure SignalR Service werden App-Server-VMs in demselben virtuellen privaten Netzwerk wie Client-VMs bereitgestellt. Alle Client-VMs und App-Server-VMs werden im gleichen Netzwerk derselben Region bereitgestellt, um eine überregionale Latenz zu vermeiden.

### <a name="performance-factors"></a>Leistungsfaktoren

Theoretisch ist die Kapazität des Azure SignalR Service durch Berechnungsressourcen begrenzt: CPU, Arbeitsspeicher und Netzwerk. Eine größere Anzahl von Verbindungen mit Azure SignalR Service führt z. B. dazu, dass der Dienst mehr Arbeitsspeicher benötigt. Für umfangreicheren Nachrichtenverkehr (z. B. ist jede Nachricht größer als 2.048 Byte) muss Azure SignalR Service mehr CPU-Zyklen für die Verarbeitung des Datenverkehrs aufwenden. In der Zwischenzeit setzt die Netzwerkbandbreite von Azure auch ein Limit für den maximalen Datenverkehr fest.

Der Transporttyp ist ein weiterer Faktor, der sich auf die Leistung auswirkt. Die drei Typen sind [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events) und [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket ist ein bidirektionales und Vollduplex-Kommunikationsprotokoll über eine einzelne TCP-Verbindung. Server-Sent-Event ist ein unidirektionales Protokoll zur Übertragung von Nachrichten vom Server zum Client. Beim Long-Polling müssen die Clients regelmäßig Informationen vom Server über eine HTTP-Anfrage abfragen. Für dieselbe API unter denselben Bedingungen weist WebSocket die beste Leistung auf, während Server-Sent-Event langsamer und Long-Polling die langsamste Option ist. WebSocket wird von Azure SignalR Service standardmäßig empfohlen.

Die Kosten für das Nachrichtenrouting begrenzen auch die Leistung. Azure SignalR Service spielt eine Rolle als Nachrichtenrouter, der die Nachricht von einer Reihe von Clients oder Servern an andere Clients oder Server weiterleitet. Ein anderes Szenario oder eine andere API erfordert eine andere Routingrichtlinie. 

Bei **Echo** sendet der Client eine Nachricht an sich selbst, und das Routingziel ist ebenfalls er selbst. Dieses Muster weist die niedrigsten Routingkosten auf. Aber für **Broadcast**, **An Gruppe senden** und **An Verbindung senden** muss der Azure SignalR Service die Zielverbindungen über die intern verteilte Datenstruktur suchen. Diese zusätzliche Verarbeitung beansprucht mehr CPU, Arbeitsspeicher und Netzwerkbandbreite. Infolgedessen ist die Leistung geringer.

Im Standardmodus kann der App-Server auch für bestimmte Szenarien zu einem Engpass werden. Das Azure SignalR SDK muss den Hub aufrufen, während es über Heartbeatsignale eine Liveverbindung mit den einzelnen Clients aufrechterhält.

Im serverlosen Modus sendet der Client eine Nachricht per HTTP-Post, was nicht so effizient ist wie WebSocket.

Ein weiterer Faktor ist das Protokoll: JSON und [MessagePack](https://msgpack.org/index.html). MessagePack ist kleiner und wird schneller als JSON bereitgestellt. MessagePack verbessert jedoch möglicherweise nicht die Leistung. Die Leistung von Azure SignalR Service ist nicht protokollabhängig, da die Nachricht während der Nachrichtenweiterleitung von Clients an Server oder umgekehrt nicht decodiert wird.

Zusammenfassend lässt sich sagen, dass sich die folgenden Faktoren auf die ein- und ausgehenden Kapazitäten auswirken:

-   SKU-Tarif (CPU/Arbeitsspeicher)

-   Anzahl der Verbindungen

-   Nachrichtengröße

-   Nachrichtensendungsrate

-   Transporttyp (WebSocket, Server-Sent-Event oder Long-Polling)

-   Anwendungsfallszenario (Routingkosten)

-   App-Server- und Dienstverbindungen (im Servermodus)


### <a name="finding-a-proper-sku"></a>Suchen einer geeigneten SKU

Wie können Sie die ein- und ausgehende Kapazität bewerten oder herausfinden, welcher Tarif für einen bestimmten Anwendungsfall geeignet ist?

Angenommen, der App-Server ist ausreichend leistungsstark und nicht der Leistungsengpass. Überprüfen Sie dann die maximale ein- und ausgehende Bandbreite für die einzelnen Tarife.

#### <a name="quick-evaluation"></a>Schnellauswertung

Zunächst vereinfachen wir die Auswertung, indem wir einige Standardeinstellungen übernehmen: 

- Der Transporttyp ist WebSocket.
- Die Nachrichtengröße ist 2.048 Bytes.
- Jede Sekunde wird eine Nachricht gesendet.
- Azure SignalR Service ist im Standardmodus.

Jeder Tarif hat seine eigene maximale ein- und ausgehende Bandbreite. Eine einwandfreie Benutzererfahrung ist nicht gewährleistet, nachdem die ein- oder ausgehende Verbindung den Grenzwert überschritten hat.

**Echo** gibt die maximale eingehende Bandbreite an, da sie die niedrigsten Routingkosten aufweist. **Broadcast** definiert die maximale Bandbreite der ausgehenden Nachrichten.

Überschreiten Sie *nicht* die in den folgenden beiden Tabellen hervorgehobenen Werte.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen                       | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| **Eingehende Bandbreite** | **2 MBit/s**    | **4 MBit/s**    | **10 MBit/s**   | **20 MBit/s**    | **40 MBit/s**    | **100 MBit/s**   | **200 MBit/s**    |
| Ausgehende Bandbreite | 2 MBit/s   | 4 MBit/s   | 10 MBit/s  | 20 MBit/s   | 40 MBit/s   | 100 MBit/s  | 200 MBit/s   |


|     Broadcast             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000  | 100\.000 |
| Eingehende Bandbreite  | 4 KBit/s   | 4 KBit/s   | 4 KBit/s    | 4 KBit/s    | 4 KBit/s    | 4 KBit/s     | 4 KBit/s    |
| **Ausgehende Bandbreite** | **4 MBit/s**    | **8 MBit/s**    | **20 MBit/s**    | **40 MBit/s**    | **80 MBit/s**    | **200 MBit/s**    | **400 MBit/s**   |

*Eingehende Bandbreite* und *Ausgehende Bandbreite* geben die gesamte Nachrichtengröße pro Sekunde an.  Hier sind die entsprechenden Formeln:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Die Anzahl der Verbindungen, die die Nachricht senden.

- *outboundConnections*: Die Anzahl der Verbindungen, die die Nachricht empfangen.

- *messageSize*: Die Größe einer einzelnen Nachricht (Durchschnittswert). Eine kleine Nachricht mit weniger als 1.024 Bytes hat eine Leistungsauswirkung, die einer 1.024-Byte-Nachricht ähnlich ist.

- *sendInterval*: Die Zeit zum Senden einer Nachricht. Normalerweise entspricht dies eine Sekunde pro Nachricht, also wird jede Sekunde eine Nachricht gesendet. Ein kleineres Intervall bedeutet, dass mehr Nachrichten in einem Zeitraum gesendet werden. So bedeutet z. B. ein Wert von 0,5 Sekunden pro Nachricht, dass zwei Nachrichten pro Sekunde gesendet werden.

- *Verbindungen*: Der committete maximale Schwellenwert für den Azure SignalR Service für die einzelnen Tarife. Bei einer weiteren Erhöhung der Verbindungsanzahl kommt es zu einer Verbindungsdrosselung.

#### <a name="evaluation-for-complex-use-cases"></a>Auswertung für komplexe Anwendungsfälle

##### <a name="bigger-message-size-or-different-sending-rate"></a>Größere Nachrichten oder andere Sendungsrate

Der tatsächliche Anwendungsfall ist komplizierter. Es wird möglicherweise eine Nachricht mit einer Größe von mehr als 2.048 Byte gesendet, oder die Rate beim Senden der Nachrichten entspricht nicht einer Nachricht pro Sekunde. Nehmen wir den Wert für „Broadcast“ von Unit100 als Beispiel zur Ermittlung, wie die Leistung ausgewertet wird.

Die folgende Tabelle zeigt einen realen Anwendungsfall von **Broadcast**. Aber die Nachrichtengröße, die Verbindungsanzahl und die Rate beim Senden der Nachrichten unterscheiden sich von dem, was wir im vorherigen Abschnitt angenommen haben. Die Frage ist, wie wir jedes dieser Elemente (Nachrichtengröße, Verbindungsanzahl oder Nachrichtensendungsrate) ableiten können, wenn nur zwei von ihnen bekannt sind.

| Broadcast  | Nachrichtengröße | Eingehende Nachrichten pro Sekunde | Verbindungen | Sendeintervalle |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100\.000     | 5 Sekunden                      |
| 2 | 256 KB               | 1                        | 8\.000       | 5 Sekunden                      |

Die folgende Formel lässt sich anhand der vorherigen Formel einfach ableiten:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Für Unit100 beträgt die maximale ausgehende Bandbreite gemäß der vorherigen Tabelle 400 MB. Bei einer Nachrichtengröße von 20 KB sollten die maximalen ausgehenden Verbindungen 400 MB \* 5 / 20 KB = 100.000 betragen, was dem tatsächlichen Wert entspricht.

##### <a name="mixed-use-cases"></a>Gemischte Anwendungsfälle

Der reale Anwendungsfall kombiniert typischerweise die vier grundlegenden Anwendungsfälle miteinander: **Echo**, **Broadcast**, **An Gruppe senden** und **An Verbindung senden**. Die von Ihnen verwendete Methodik zur Auswertung der Kapazität sieht wie folgt aus:

1. Unterteilen Sie die gemischten Anwendungsfälle in vier grundlegende Anwendungsfälle.
1. Berechnen Sie die maximale Bandbreite der eingehenden und ausgehenden Nachrichten, indem Sie die vorhergehenden Formeln separat verwenden.
1. Summieren Sie die Bandbreitenberechnungen, um die gesamte maximale eingehende/ausgehende Bandbreite zu erhalten. 

Wählen Sie dann den richtigen Tarif aus den Tabellen für die maximale eingehende/ausgehende Bandbreite aus.

> [!NOTE]
> Wenn Sie eine Nachricht an Hunderte oder Tausende kleine Gruppen senden oder wenn Tausende Clients sich gegenseitig eine Nachricht senden, werden die Routingkosten immer höher. Berücksichtigen Sie diese Auswirkungen.

Stellen Sie für den Anwendungsfall „Senden einer Nachricht an Clients“ sicher, dass der App-Server *nicht* der Engpass ist. Der folgende Abschnitt „Fallstudie“ enthält Richtlinien zur Anzahl der erforderlichen App-Server und zur Anzahl der Serververbindungen, die Sie konfigurieren sollten.

## <a name="case-study"></a>Fallstudie

Die folgenden Abschnitte führen durch vier typische Anwendungsfälle für den WebSocket-Transport: **Echo**, **Broadcast**, **An Gruppe senden** und **An Verbindung senden**. Für jedes Szenario werden im Abschnitt die aktuelle ein- und ausgehende Kapazität für den Azure SignalR Service aufgelistet. Darüber hinaus werden die wichtigsten Faktoren erläutert, die sich auf die Leistung auswirken.

Im Standardmodus erstellt der App-Server fünf Serververbindungen mit Azure SignalR Service. Der App-Server verwendet standardmäßig das Azure SignalR Service-SDK. In den folgenden Leistungstestergebnissen werden die Serververbindungen auf 15 erhöht (oder mehr, falls eine Nachricht an eine große Gruppe gesendet wird).

Unterschiedliche Anwendungsfälle haben unterschiedliche Anforderungen an den App-Server. **Broadcast** benötigt eine geringe Anzahl von App-Servern. **Echo** oder **An Verbindung senden** benötigt viele App-Server.

In allen Anwendungsfällen beträgt die standardmäßige Nachrichtengröße 2.048 Byte und das Sendeintervall eine Sekunde.

### <a name="default-mode"></a>Standardmodus

Clients, Web-App-Server und Azure SignalR Service sind im Standardmodus einbezogen. Jeder Client steht für eine einzelne Verbindung.

#### <a name="echo"></a>Echo

Zunächst stellt eine Web-App eine Verbindung mit Azure SignalR Service her. Anschließend stellen viele Clients eine Verbindung mit der Web-App her, die die Clients mit dem Zugriffstoken und dem Endpunkt an Azure SignalR Service weiterleitet. Dann stellen die Clients WebSocket-Verbindungen mit Azure SignalR Service her.

Nachdem alle Clients Verbindungen hergestellt haben, beginnen sie jede Sekunde eine Nachricht mit einem darin enthaltenen Zeitstempel an den jeweiligen Hub zu senden. Der Hub gibt die Nachricht an seinen ursprünglichen Client zurück. Jeder Client berechnet die Wartezeit, nachdem die Rückmeldung empfangen wurde.

Im folgenden Diagramm befinden sich 5 bis 8 (rot markierter Datenverkehr) in einer Schleife. Die Schleife hat eine Standardlaufzeit (5 Minuten) und erhält die Statistik aller Wartezeiten für die Nachrichten.

![Datenverkehr für den Anwendungsfall „Echo“](./media/signalr-concept-performance/echo.png)

Das Verhalten von **Echo** bestimmt, dass die maximale eingehende Bandbreite gleich der maximalen ausgehenden Bandbreite ist. Details finden Sie in der folgenden Tabelle.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen                       | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Ein-/Ausgehende Nachrichten pro Sekunde | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Ein-/Ausgehende Bandbreite | 2 MBit/s   | 4 MBit/s   | 10 MBit/s  | 20 MBit/s   | 40 MBit/s   | 100 MBit/s  | 200 MBit/s   |

In diesem Anwendungsfall ruft jeder Client den im App-Server definierten Hub auf. Der Hub ruft nur die Methode auf, die auf der ursprünglichen Clientseite definiert ist. Dieser Hub ist der leichteste Hub für **Echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Selbst für diesen einfachen Hub ist der Datenverkehrsdruck auf dem App-Server mit zunehmender Arbeitslast durch eingehende Nachrichten groß, die von **Echo** verursacht werden. Dieser Datenverkehrsdruck erfordert viele App-Server für große SKU-Tarife. In der folgenden Tabelle ist die Anzahl der App-Server für die einzelnen Tarife aufgeführt.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Die Anzahl der Clientverbindungen, die Nachrichtengröße, die Nachrichtensendungsrate, der SKU-Tarif und CPU/Arbeitsspeicher des App-Servers wirken sich auf die Gesamtleistung von **Echo** aus.

#### <a name="broadcast"></a>Broadcast

Bei **Broadcast** sendet die Web-App, wenn sie die Nachricht empfängt, an alle Clients. Je mehr Clients zu übertragen sind, desto mehr Nachrichtendatenverkehr entsteht zu allen Clients. Sehen Sie sich das folgende Diagramm an.

![Datenverkehr für den Anwendungsfall „Broadcast“](./media/signalr-concept-performance/broadcast.png)

Eine geringe Anzahl von Clients sendet Nachrichten. Die Bandbreite für eingehende Nachrichten ist klein, aber die Bandbreite für ausgehende Nachrichten ist beträchtlich. Die Bandbreite für ausgehende Nachrichten steigt mit zunehmender Clientverbindungsanzahl oder Übertragungsrate.

Die folgende Tabelle fasst die maximalen Clientverbindungen, die Anzahl der eingehenden/ausgehenden Nachrichten und die Bandbreite zusammen.

|     Broadcast             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000  | 100\.000 |
| Eingehende Nachrichten pro Sekunde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Ausgehende Nachrichten pro Sekunde | 2\.000 | 4\.000 | 10\.000 | 20\.000 | 40\.000 | 100\.000 | 200\.000 |
| Eingehende Bandbreite  | 4 KBit/s   | 4 KBit/s   | 4 KBit/s    | 4 KBit/s    | 4 KBit/s    | 4 KBit/s     | 4 KBit/s     |
| Ausgehende Bandbreite | 4 MBit/s   | 8 MBit/s   | 20 MBit/s   | 40 MBit/s   | 80 MBit/s   | 200 MBit/s   | 400 MBit/s   |

Es gibt nicht mehr als vier übertragende Clients, die Nachrichten posten. Sie benötigen im Vergleich zu **Echo** weniger App-Server, da die Anzahl der eingehenden Nachrichten gering ist. Zwei App-Server genügen sowohl für SLA- als auch für Leistungsüberlegungen. Sie sollten jedoch die Standardserververbindungen erhöhen, um ein Ungleichgewicht zu vermeiden, insbesondere bei Unit50 und Unit100.

|   Broadcast      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Erhöhen Sie die Standardserververbindungen von 5 auf 40 auf jedem App-Server, um mögliche nicht ausgeglichene Serververbindungen zu Azure SignalR Service zu vermeiden.
>
> Die Anzahl der Clientverbindungen, die Nachrichtengröße, die Nachrichtensendungsrate und der SKU-Tarif wirken sich auf die Gesamtleistung für **Broadcast** aus.

#### <a name="send-to-group"></a>An Gruppe senden

Der Anwendungsfall **An Gruppe senden** weist ein ähnliches Datenverkehrsmuster auf wie **Broadcast**. Der Unterschied besteht darin, dass Clients, nachdem sie WebSocket-Verbindungen mit Azure SignalR Service hergestellt haben, Gruppen beitreten müssen, bevor sie eine Nachricht an eine bestimmte Gruppe senden können. Das folgende Diagramm veranschaulicht den Datenverkehrsfluss.

![Datenverkehr für den Anwendungsfall „An Gruppe senden“](./media/signalr-concept-performance/sendtogroup.png)

Anzahl der Gruppenmitglieder und Gruppen sind zwei Faktoren, die sich auf die Leistung auswirken. Es werden zwei Arten von Gruppen definiert, um die Analyse zu vereinfachen:

- **Kleine Gruppe**: Jede Gruppe verfügt über 10 Verbindungen. Die Gruppenanzahl entspricht: (maximale Verbindungsanzahl) / 10. Wenn es z. B. für Unit1 1.000 Verbindungen gibt, dann sind das 1000 / 10 = 100 Gruppen.

- **Große Gruppe**: Die Gruppenanzahl ist immer 10. Die Anzahl der Gruppenmitglieder entspricht: (maximale Verbindungszahl) / 10. Wenn es z. B. für Unit1 1.000 Verbindungen gibt, dann besteht jede Gruppe aus 1000 / 10 = 100 Mitgliedern.

**An Gruppe senden** verursacht Routingkosten für Azure SignalR Service, da die Zielverbindungen über eine verteilte Datenstruktur gefunden werden müssen. Mit zunehmender Anzahl der sendenden Verbindungen steigen die Kosten.

##### <a name="small-group"></a>Kleine Gruppe

Die Routingkosten sind für das Senden von Nachrichten an viele kleine Gruppen beträchtlich. Derzeit erreicht die Implementierung von Azure SignalR Service die Routingkostengrenze bei Unit50. Das Erhöhen der Anzahl von CPUs und Arbeitsspeicher hilft in diesem Fall nicht, daher kann Unit100 vorsätzlich nicht verbessert werden. Wenn Sie eine höhere eingehende Bandbreite benötigen, wenden Sie sich an den Kundensupport.

|   An kleine Gruppe senden     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000 | 100\.000
| Anzahl der Gruppenmitglieder        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Gruppenanzahl               | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000  | 10\.000 
| Eingehende Nachrichten pro Sekunde  | 200   | 400   | 1\.000  | 2\.500  | 4\.000  | 7\.000  | 7\.000   |
| Eingehende Bandbreite  | 400 KBit/s  | 800 KBit/s  | 2 MBit/s     | 5 MBit/s     | 8 MBit/s     | 14 MBit/s    | 14 MBit/s     |
| Ausgehende Nachrichten pro Sekunde | 2\.000 | 4\.000 | 10\.000 | 25\.000 | 40\.000 | 70\.000 | 70\.000  |
| Ausgehende Bandbreite | 4 MBit/s    | 8 MBit/s    | 20 MBit/s    | 50 MBit/s     | 80 MBit/s    | 140 MBit/s   | 140 MBit/s    |

Viele Clientverbindungen rufen den Hub auf, sodass die Anzahl der App-Server auch für die Leistung entscheidend ist. In der folgenden Tabelle ist die jeweilige Anzahl der vorgeschlagenen App-Server aufgeführt.

|  An kleine Gruppe senden   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Die Anzahl der Clientverbindungen, die Nachrichtengröße, die Nachrichtensendungsrate, die Routingkosten, der SKU-Tarif und CPU/Arbeitsspeicher des App-Servers wirken sich auf die Gesamtleistung von **An kleine Gruppe senden** aus.

##### <a name="big-group"></a>Große Gruppe

Bei **An große Gruppe senden** wird die ausgehende Bandbreite zum Engpass, bevor die Routingkostengrenze erreicht wird. In der folgenden Tabelle ist die maximale ausgehende Bandbreite aufgeführt, die fast mit derjenigen für **Broadcast** identisch ist.

|    An große Gruppe senden      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000  | 100\.000
| Anzahl der Gruppenmitglieder        | 100   | 200   | 500    | 1\.000  | 2\.000  | 5\.000   | 10\.000 
| Gruppenanzahl               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Eingehende Nachrichten pro Sekunde  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Eingehende Bandbreite  | 80 KBit/s   | 40 KBit/s   | 40 KBit/s    | 20 KBit/s    | 40 KBit/s    | 40 KBit/s     | 40 KBit/s     |
| Ausgehende Nachrichten pro Sekunde | 2\.000 | 4\.000 | 10\.000 | 20\.000 | 40\.000 | 100\.000 | 200\.000 |
| Ausgehende Bandbreite | 8 MBit/s    | 8 MBit/s    | 20 MBit/s    | 40 MBit/s    | 80 MBit/s    | 200 MBit/s    | 400 MBit/s    |

Die Anzahl der sendenden Verbindungen ist nicht höher als 40. Die Belastung des App-Servers ist gering, sodass die empfohlene Anzahl von Web-Apps niedrig ist.

|  An große Gruppe senden  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Erhöhen Sie die Standardserververbindungen von 5 auf 40 auf jedem App-Server, um mögliche nicht ausgeglichene Serververbindungen zu Azure SignalR Service zu vermeiden.
> 
> Die Anzahl der Clientverbindungen, die Nachrichtengröße, die Nachrichtensendungsrate, die Routingkosten und der SKU-Tarif wirken sich auf die Gesamtleistung für **An große Gruppe senden** aus.

#### <a name="send-to-connection"></a>An Verbindung senden

Im Anwendungsfall **An Verbindung senden** ruft jeder Client beim Herstellen der Verbindungen mit Azure SignalR Service einen speziellen Hub auf, um eine eigene Verbindungs-ID zu erhalten. Der Leistungsbenchmark erfasst alle Verbindungs-IDs, mischt sie und ordnet sie allen Clients als Sendeziel neu zu. Die Clients senden die Nachricht weiterhin an die Zielverbindung, bis der Leistungstest abgeschlossen ist.

![Datenverkehr für den Anwendungsfall „An Client senden“](./media/signalr-concept-performance/sendtoclient.png)

Die Routingkosten für **An Verbindung senden** sind vergleichbar mit den Kosten für **An kleine Gruppe senden**.

Mit zunehmender Anzahl von Verbindungen begrenzen die Routingkosten die Gesamtleistung. Unit50 hat den Grenzwert erreicht. Infolgedessen kann Unit100 nicht weiter verbessert werden.

Die folgende Tabelle ist eine statistische Zusammenfassung nach zahlreichen Durchläufen des Benchmarks **An Verbindung senden**.

|   An Verbindung senden   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Verbindungen                        | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000          | 100\.000         |
| Ein-/Ausgehende Nachrichten pro Sekunde | 1\.000 | 2\.000 | 5\.000 | 8\.000  | 9\.000  | 20\.000 | 20\.000 |
| Ein-/Ausgehende Bandbreite | 2 MBit/s    | 4 MBit/s    | 10 MBit/s   | 16 MBit/s    | 18 MBit/s    | 40 MBit/s       | 40 MBit/s       |

Dieser Anwendungsfall erfordert eine hohe Arbeitslast auf der Seite des App-Servers. Weitere Informationen zur Anzahl der vorgeschlagenen App-Server finden Sie in der folgenden Tabelle.

|  An Verbindung senden  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Die Anzahl der Clientverbindungen, die Nachrichtengröße, die Nachrichtensendungsrate, die Routingkosten, der SKU-Tarif und CPU/Arbeitsspeicher des App-Servers wirken sich auf die Gesamtleistung von **An Verbindung senden** aus.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>„Echo“, „Broadcast“ und „An kleine Gruppe senden“ – ASP.NET SignalR

Azure SignalR Service bietet dieselbe Leistungskapazität für ASP.NET SignalR. 

Der Leistungstest verwendet Azure-Web-Apps aus dem [Serviceplan Standard S3](https://azure.microsoft.com/pricing/details/app-service/windows/) für ASP.NET SignalR.

Die folgende Tabelle enthält die vorgeschlagene Anzahl der Web-Apps für ASP.NET SignalR **Echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

Die folgende Tabelle enthält die vorgeschlagene Anzahl der Web-Apps für ASP.NET SignalR **Broadcast**.

|  Broadcast       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

Die folgende Tabelle enthält die vorgeschlagene Anzahl der Web-Apps für ASP.NET SignalR **An kleine Gruppe senden**.

|  An kleine Gruppe senden     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindungen      | 1\.000 | 2\.000 | 5\.000 | 10\.000 | 20\.000 | 50\.000 | 100\.000 |
| Anzahl der App-Server | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Serverloser Modus

Der serverlose Modus umfasst Clients und Azure SignalR Service. Jeder Client steht für eine einzelne Verbindung. Der Client sendet Nachrichten über die REST-API an einen anderen Client oder Broadcastmeldungen an alle Clients.

Das Senden von Nachrichten mit hoher Dichte über die REST-API ist nicht so effizient wie die Verwendung von WebSocket. Sie müssen dazu jedes Mal eine neue HTTP-Verbindung herstellen, und das ist im serverlosen Modus ein zusätzlicher Kostenfaktor.

#### <a name="broadcast-through-rest-api"></a>Übertragen über die REST-API
Alle Clients stellen WebSocket-Verbindungen mit Azure SignalR Service her. Dann beginnen einige Clients mit der Übertragung über die REST-API. Der Nachrichtenversand (eingehend) erfolgt ausschließlich über HTTP-Post, was im Vergleich zu WebSocket nicht effizient ist.

|   Übertragen über die REST-API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000  | 100\.000 |
| Eingehende Nachrichten pro Sekunde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Ausgehende Nachrichten pro Sekunde | 2\.000 | 4\.000 | 10\.000 | 20\.000 | 40\.000 | 100\.000 | 200\.000 |
| Eingehende Bandbreite  | 4 KBit/s    | 4 KBit/s    | 4 KBit/s     | 4 KBit/s     | 4 KBit/s     | 4 KBit/s      | 4 KBit/s      |
| Ausgehende Bandbreite | 4 MBit/s    | 8 MBit/s    | 20 MBit/s    | 40 MBit/s    | 80 MBit/s    | 200 MBit/s    | 400 MBit/s    |

#### <a name="send-to-user-through-rest-api"></a>Über die REST-API an Benutzer senden
Der Benchmark weist allen Clients Benutzernamen zu, bevor sie beginnen, eine Verbindung mit Azure SignalR Service herzustellen. Nachdem die Clients WebSocket-Verbindungen mit dem Azure SignalR Service hergestellt haben, beginnen sie damit, Nachrichten über HTTP-Post an andere Clients zu senden.

|   Über die REST-API an Benutzer senden | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindungen               | 1\.000 | 2\.000 | 5\.000  | 10\.000 | 20\.000 | 50\.000  | 100\.000 |
| Eingehende Nachrichten pro Sekunde  | 300   | 600   | 900    | 1\.300  | 2\.000  | 10\.000  | 18\.000  |
| Ausgehende Nachrichten pro Sekunde | 300   | 600   | 900    | 1\.300  | 2\.000  | 10\.000  | 18\.000 |
| Eingehende Bandbreite  | 600 KBit/s  | 1,2 MBit/s  | 1,8 MBit/s   | 2,6 MBit/s   | 4 MBit/s     | 10 MBit/s     | 36 MBit/s    |
| Ausgehende Bandbreite | 600 KBit/s  | 1,2 MBit/s  | 1,8 MBit/s   | 2,6 MBit/s   | 4 MBit/s     | 10 MBit/s     | 36 MBit/s    |

## <a name="performance-test-environments"></a>Leistungstestumgebungen

Für alle zuvor aufgeführten Anwendungsfälle haben wir die Leistungstests in einer Azure-Umgebung durchgeführt. Wir haben höchstens 50 Client-VMs und 20 App-Server-VMs verwendet. Hier sind einige Details:

- Größe der Client-VM: StandardDS2V2 (2 vCPUs, 7G Arbeitsspeicher)

- Größe der App-Server-VM: StandardF4sV2 (4 vCPUs, 8G Arbeitsspeicher)

- Azure SignalR-SDK-Serververbindungen: 15

## <a name="performance-tools"></a>Leistungstools

Leistungstools für Azure SignalR Service finden Sie auf [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen Überblick über die Leistung von Azure SignalR Service in typischen Anwendungsfällen erhalten.

Weitere Informationen zu den internen Funktionen des Diensts und seiner Skalierung finden Sie in den folgenden Leitfäden:

* [Ausführliche Informationen zu Azure SignalR Service](signalr-concept-internals.md)
* [Skalierung von Azure SignalR Service](signalr-howto-scale-multi-instances.md)
