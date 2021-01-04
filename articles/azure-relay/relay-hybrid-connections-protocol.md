---
title: Leitfaden zum Azure Relay-Hybridverbindungsprotokoll | Microsoft-Dokumentation
description: In diesem Artikel werden die clientseitigen Interaktionen mit dem Hybridverbindungsrelay für die Verbindung von Clients in Listener- und Absenderrollen beschrieben.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8a812aa401077b81934d89ada99cf1dc312d8dbc
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862325"
---
# <a name="azure-relay-hybrid-connections-protocol"></a>Azure Relay-Hybridverbindungsprotokoll

Azure Relay ist eine der Säulen der Schlüsselfunktionen der Azure Service Bus-Plattform Die neue Funktion _Hybridverbindungen_ von Relay ist eine sichere Entwicklung mit offenem Protokoll, die auf HTTP und WebSockets basiert. Sie ersetzt die ehemalige, gleichnamige Funktion _BizTalk Services_, die auf einer proprietären Protokollbasis erstellt wurde. Die Integration von Hybridverbindungen in Azure App Services funktioniert weiterhin ohne weiteren Aufwand.

Hybridverbindungen ermöglichen eine bidirektionale, binäre Streamkommunikation und einen einfachen Datagrammfluss zwischen zwei vernetzten Anwendungen. Eine oder beide Parteien können sich hierbei hinter NATs oder Firewalls befinden.

In diesem Artikel werden die clientseitigen Interaktionen mit dem Hybridverbindungsrelay für die Verbindung von Clients in Listener- und Absenderrollen beschrieben. Außerdem wird beschrieben, wie Listener neue Verbindungen und Anforderungen akzeptieren.

## <a name="interaction-model"></a>Interaktionsmodell

Das Hybridverbindungs-Relay verbindet zwei Parteien durch einen Rendezvouspunkt in der Azure-Cloud, den die Parteien erkennen können und mit dem sie eine Verbindung aus ihrer eigenen Netzwerkperspektive herstellen können. In dieser Dokumentation und anderen Dokumentationen, in den APIs und auch im Azure-Portal hat der Rendezvouspunkt die Bezeichnung „Hybridverbindung“. Der Endpunkt dieses Hybridverbindungsdiensts wird im weiteren Verlauf dieses Artikels als „Dienst“ bezeichnet.

Der Dienst ermöglicht die Bereitstellung von WebSocket-Verbindungen und HTTP(S)-Anforderungen und -Antworten.

Für das Interaktionsmodell werden Bezeichnungen verwendet, die auch für viele andere Netzwerk-APIs gängig sind. Es gibt einen Listener, der zuerst die Bereitschaft anzeigt, eingehende Verbindungen zu verarbeiten und akzeptiert diese anschließend, wenn sie ankommen. Auf der anderen Seite stellt ein Client eine Verbindung mit dem Listener her und erwartet, dass diese Verbindung für die Erstellung eines bidirektionalen Kommunikationspfads akzeptiert wird. „Verbinden“ (Connect), „Lauschen“ (Listen) und „Akzeptieren“ (Accept) sind die Begriffe, die Sie in den meisten Socket-APIs finden.

In jedem Relaykommunikationsmodell stellen beide Parteien ausgehende Verbindungen mit einem Dienstendpunkt her. Hierdurch wird der „Listener“ umgangssprachlich auch zu einem „Client“ und kann ebenfalls weitere Überschneidungen in der Terminologie verursachen. Daher wird bei Hybridverbindungen die folgende präzise Terminologie verwendet:

Die Programme auf beiden Seiten der Verbindung werden als „Clients“ bezeichnet, da sie für den Dienst Clients darstellen. Der Client, der auf Verbindungen wartet und diese akzeptiert, ist der „Listener“ oder befindet sich in der „Listenerrolle“. Der Client, der über den Dienst eine neue Verbindung mit einem Listener herstellt, heißt „Absender“ bzw. befindet sich in der „Absenderrolle“.

### <a name="listener-interactions"></a>Listener-Interaktionen

Der Listener verfügt über fünf Interaktionen mit dem Dienst. Alle Transportdetails werden später im Abschnitt „Referenz“ dieses Artikels erläutert.

Die Listen-, Accept- und Request-Nachrichten gehen vom Dienst ein. Die Renew- und Ping-Vorgänge werden vom Listener gesendet.

#### <a name="listen-message"></a>Listen-Nachricht

Um die Bereitschaft an einen Dienst zu übermitteln, dass ein Listener bereit zum Akzeptieren von Verbindungen ist, erstellt er eine ausgehende WebSocket-Verbindung. Der Verbindungshandshake enthält den Namen einer Hybridverbindung, die im Relaynamespace konfiguriert wurde, sowie ein Sicherheitstoken, das die Berechtigung „Lauschen“ direkt innerhalb dieses Namens überträgt.

Wenn der WebSocket vom Dienst akzeptiert wird, ist die Registrierung abgeschlossen, und der erstellte WebSocket wird als „Steuerungskanal“ für die Aktivierung aller nachfolgenden Interaktionen aufrechterhalten. Der Dienst lässt bis zu 25 gleichzeitige Listener für eine Hybridverbindung zu. Das Kontingent für AppHooks muss noch ermittelt werden.

Wenn für Hybridverbindungen zwei oder mehr aktive Listener vorhanden sind, werden eingehende Verbindungen in zufälliger Reihenfolge darauf aufgeteilt. Es wird versucht, eine möglichst gleichmäßige Aufteilung zu erzielen.

#### <a name="accept-message"></a>Accept-Nachricht

Wenn ein Absender eine neue Verbindung im Dienst öffnet, wählt und benachrichtigt der Dienst einen der aktiven Listener in der Hybridverbindung. Diese Benachrichtigung wird über den offenen Steuerkanal als JSON-Nachricht an den Listener gesendet. Die Nachricht enthält die URL des WebSocket-Endpunkts, mit dem der Listener eine Verbindung herstellen muss, um die Verbindung zu akzeptieren.

Die URL kann bzw. muss vom Listener ohne zusätzlichen Aufwand direkt verwendet werden.
Die codierten Informationen sind nur für kurze Zeit gültig. Dies ist im Grunde genommen der Zeitraum, der vom Absender toleriert wird, bis die End-to-End-Verbindung hergestellt wurde. Der anzunehmende Höchstwert beträgt 30 Sekunden. Die URL kann nur für einen erfolgreichen Verbindungsversuch verwendet werden. Sobald die WebSocket-Verbindung mit der Rendezvous-URL hergestellt wurde, wird jede weitere Aktivität in diesem WebSocket an den bzw. vom Absender übermittelt. Dies erfolgt ohne Eingriff oder Interpretation des Diensts.

### <a name="request-message"></a>Request-Nachricht

Zusätzlich zu WebSocket-Verbindungen kann der Listener auch HTTP-Anforderungsframes von einem Absender empfangen, wenn diese Funktion für die Hybridverbindung explizit aktiviert wird.

Listener, die an Hybridverbindungen mit HTTP-Unterstützung angefügt sind, MÜSSEN die `request`-Geste verarbeiten. Ein Listener, der `request` nicht verarbeitet und so wiederholt Zeitüberschreitungsfehler verursacht, während die Verbindung besteht, KANN vom Dienst im weiteren Verlauf blockiert werden.

Die Metadaten von HTTP-Frame-Headern werden in das JSON-Format übersetzt, damit sie vom Listener-Framework einfacher verarbeitet werden können. Ein Grund hierfür ist, dass HTTP-Header-Analysebibliotheken seltener als JSON-Parser sind. HTTP-Metadaten, die nur für die Beziehung zwischen dem Absender und dem Relay-HTTP-Gateway relevant sind, z.B. Autorisierungsinformationen, werden nicht weitergeleitet. HTTP-Anforderungstexte werden auf transparente Weise als binäre WebSocket-Frames übertragen.

Der Listener kann auf HTTP-Anforderungen antworten, indem er eine äquivalente Antwortgeste verwendet.

Für den Anforderung/Antwort-Datenfluss wird standardmäßig der Steuerkanal verwendet, aber es kann bei Bedarf jederzeit ein „Upgrade“ auf ein eindeutiges WebSocket-Rendezvouselement durchgeführt werden. Mit eindeutigen WebSocket-Verbindungen wird der Durchsatz für die einzelnen Clientkonversationen verbessert. Sie stellen für den Listener aber eine erhöhte Belastung dar, weil eine größere Zahl von Verbindungen verarbeitet werden muss. Dies ist für einfache Clients unter Umständen nicht wünschenswert.

Für den Steuerkanal sind Anforderungs- und Antworttexte auf eine maximale Größe von 64 KB beschränkt. Die Metadaten von HTTP-Headern sind auf insgesamt 32 KB beschränkt. Wenn dieser Schwellenwert entweder von der Anforderung oder der Antwort überschritten wird, MUSS der Listener ein Upgrade auf ein WebSocket-Rendezvouselement durchführen, indem er eine Geste verwendet, die zur Verarbeitung der [Accept](#accept-message)-Nachricht äquivalent ist.

Für Anforderungen entscheidet der Dienst, ob Anforderungen über den Steuerkanal geleitet werden. Beispiele hierfür sind u.a. Fälle, in denen eine Anforderung den Wert von 64 KB (Header und Text zusammen) klar überschreitet, die Anforderung mit [der Übertragungscodierung „Chunked“](https://tools.ietf.org/html/rfc7230#section-4.1) gesendet wird und der Dienst den begründeten Verdacht hegt, dass die Anforderung 64 KB überschreitet, oder das Lesen der Anforderung nicht sofort erfolgt. Wenn der Dienst für die Zustellung der Anforderung das Rendezvouselement wählt, wird nur die Rendezvousadresse an den Listener übergeben.
Der Listener MUSS anschließend das WebSocket-Rendezvouselement einrichten, und der Dienst stellt die vollständige Anforderung, einschließlich der Texte, sofort über das WebSocket-Rendezvouselement bereit. Auch für die Antwort MUSS das WebSocket-Rendezvouselement verwendet werden.

Für Anforderungen, die über den Steuerkanal eingehen, entscheidet der Listener, ob über den Steuerkanal oder das Rendezvouselement geantwortet werden soll. Der Dienst MUSS eine Rendezvousadresse enthalten, bei der jede Anforderung über den Steuerkanal geleitet wird. Diese Adresse ist nur für Upgrades basierend auf der aktuellen Anforderung gültig.

Wenn sich der Listener für die Durchführung des Upgrades entscheidet, stellt er eine Verbindung her und stellt die Antwort sofort über das eingerichtete WebSocket-Rendezvouselement zu.

Nachdem das WebSocket-Rendezvouselement eingerichtet wurde, SOLLTE der Listener es für die weitere Verarbeitung der Anforderungen und Antworten desselben Clients beibehalten. Der Dienst behält das WebSocket-Element so lange bei, wie die HTTPS-Socketverbindung mit dem Absender besteht, und alle nachfolgenden Anforderungen von diesem Absender werden über das beibehaltene WebSocket-Element geleitet. Wenn der Listener sich auf seiner Seite für das Verwerfen des WebSocket-Rendezvouselement entscheidet, verwirft der Dienst auch die Verbindung mit dem Absender. Dies gilt unabhängig davon, ob sich eine nachfolgende Anforderung ggf. bereits in Bearbeitung befindet.

#### <a name="renew-operation"></a>Renew-Vorgang

Das Sicherheitstoken, das verwendet werden muss, um den Listener zu registrieren und den Steuerungskanal zu verwalten, läuft möglicherweise ab, währen der Listener aktiv ist. Der Ablauf des Tokens beeinträchtigt keine laufenden Verbindungen, aber der Steuerungskanal wird dadurch während oder kurz nach dem Moment des Ablaufs vom Dienst entfernt. Der „renew“-Vorgang zum Erneuern ist eine JSON-Nachricht, die der Listener senden kann, um das Token zu ersetzen, das dem Steuerungskanal zugeordnet ist. Dadurch kann der Steuerungskanal über einen längeren Zeitraum beibehalten werden.

#### <a name="ping-operation"></a>Ping-Vorgang

Wenn der Steuerungskanal für lange Zeit im Leerlauf bleibt, wird die TCP-Verbindung möglicherweise von geplanten Vermittlern, z.B. vom Lastenausgleich oder von NATs, gelöscht. Der „ping“-Vorgang vermeidet dies, indem eine kleine Datenmenge durch den Kanal gesendet wird, die alle beteiligten Elemente auf der Netzwerkroute daran erinnert, dass die Verbindung aufrechterhalten werden soll. Der Vorgang dient auch als Aktivitätstest für den Listener. Wenn der Ping-Befehl fehlschlägt, sollte der Steuerungskanal als nicht verwendbar angesehen werden und der Listener sollte eine neue Verbindung herstellen.

### <a name="sender-interaction"></a>Absenderinteraktion

Der Absender verfügt über zwei Interaktionen mit dem Dienst: Er stellt eine Verbindung für ein WebSocket-Element her oder sendet Anforderungen per HTTPS. Anforderungen können per Absenderrolle nicht über ein WebSocket-Element gesendet werden.

#### <a name="connect-operation"></a>Connect-Vorgang

Der „connect“-Vorgang zum Verbinden öffnet einen WebSocket im Dienst, der den Namen der Hybridverbindung sowie ein (optionales, aber standardmäßig erforderliches) Sicherheitstoken bereitstellt, das Berechtigungen zum Senden in der Abfragezeichenfolge überträgt. Der Dienst interagiert dann mit dem Listener, wie oben beschrieben. Der Listener erstellt eine Rendezvousverbindung, die mit diesem WebSocket verknüpft wird. Nachdem der WebSocket akzeptiert wurde, werden alle weiteren Interaktionen für diesen WebSocket mit dem verbundenen Listener durchgeführt.

#### <a name="request-operation"></a>Request-Vorgang

Für Hybridverbindungen, für die das Feature aktiviert wurde, kann der Absender größtenteils unbeschränkte HTTP-Anforderungen an Listener senden.

Bis auf ein Relay-Zugriffstoken, das entweder in die Abfragezeichenfolge oder einen HTTP-Header der Anforderung eingebettet ist, ist der Relay-Vorgang für alle HTTP-Vorgänge unter der Relay-Adresse und alle Suffixe des Relay-Adresspfads vollständig transparent. Somit verfügt der Listener über die volle Kontrolle der End-to-End-Autorisierung und sogar über HTTP-Erweiterungsfeatures wie [CORS](https://www.w3.org/TR/cors/).

Die Absenderautorisierung für den Relay-Endpunkt ist standardmäßig aktiviert, aber sie ist OPTIONAL. Der Besitzer der Hybridverbindung kann auswählen, dass anonyme Absender zulässig sind. Der Dienst führt für Autorisierungsinformationen wie folgt das Abfangen, Untersuchen und Entfernen durch:

1. Wenn die Abfragezeichenfolge einen `sb-hc-token`-Ausdruck enthält, wird der Ausdruck IMMER aus der Abfragezeichenfolge entfernt. Die Auswertung erfolgt, wenn die Relay-Autorisierung aktiviert ist.
2. Wenn die Anforderungsheader einen `ServiceBusAuthorization`-Header enthalten, wird der Ausdruck IMMER aus der Headersammlung entfernt.
   Die Auswertung erfolgt, wenn die Relay-Autorisierung aktiviert ist.
3. Für den Header wird die Auswertung und Entfernung nur dann durchgeführt, wenn die Relay-Autorisierung aktiviert ist, die Anforderungsheader einen `Authorization`-Header enthalten und keiner der obigen Ausdrücke vorhanden ist. Andernfalls wird `Authorization` immer unverändert übergeben.

Falls kein aktiver Listener vorhanden ist, gibt der Dienst den Fehlercode „502 Ungültiges Gateway“ zurück. Wenn die Anforderung vom Dienst scheinbar nicht verarbeitet wird, gibt der Dienst nach 60 Sekunden „504 Gatewaytimeout“ zurück.

### <a name="interaction-summary"></a>Zusammenfassung der Interaktion

Das Ergebnis dieses Interaktionsmodells ist, dass der Absenderclient den Handshake mit einem „bereinigten“ WebSocket verlässt, der mit einem Listener verbunden ist und keine weitere Präambel oder Vorbereitung benötigt. Dieses Modell ermöglicht, dass praktisch alle vorhandenen Implementierungen des WebSocket-Clients den Hybridverbindungsdienst nutzen, indem eine richtig strukturierte URL für die WebSocket-Clientebene angegeben wird.

Der WebSocket der Rendezvousverbindung, den der Listener durch die akzeptierten Interaktionen abruft, ist ebenfalls bereinigt und kann mit minimaler zusätzlicher Abstraktion an jede vorhandene WebSocket-Serverimplementierung übergeben werden. Die Abstraktion unterscheidet zwischen „accept“-Vorgängen in den lokalen Netzwerklistenern des Frameworks und den „accept“-Remotevorgängen der Hybridverbindungen.

Beim Modell der HTTP-Anforderungen und -Antworten erhält der Absender einen größtenteils unbeschränkten HTTP-Protokolloberflächenbereich mit einer OPTIONALEN Autorisierungsebene. Der Listener erhält einen vorab analysierten HTTP-Anforderungsheaderabschnitt, der zurück in eine nachgeschaltete HTTP-Anforderung verwandelt oder unverändert verarbeitet werden kann, wobei binäre Frames mit HTTP-Texten verwendet werden. Für Antworten wird das gleiche Format verwendet. Interaktionen mit weniger als 64 KB an Anforderungs- und Antworttext können über ein einzelnes WebSocket-Element verarbeitet werden, das für alle Absender gemeinsam verwendet wird. Umfangreichere Anforderungen und Antworten können mit dem Rendezvousmodell verarbeitet werden.

## <a name="protocol-reference"></a>Protokollreferenz

In diesem Abschnitt werden die Details der oben erwähnten Protokollinteraktionen beschrieben.

Alle WebSocket-Verbindungen werden an Port 443 als Upgrade von HTTPS 1.1 durchgeführt, das häufig durch ein WebSocket-Framework oder eine API abstrahiert wird. Die Beschreibung hier hält die Implementierung neutral und ohne ein bestimmtes Framework vorzuschlagen.

### <a name="listener-protocol"></a>Listener-Protokoll

Das Listener-Protokoll besteht aus zwei Verbindungsgesten und drei Nachrichtenvorgängen.

#### <a name="listener-control-channel-connection"></a>Verbindung des Listener-Steuerungskanals

Der Steuerungskanal wird mit der Erstellung der folgenden WebSocket-Verbindung geöffnet:

`wss://{namespace-address}/$hc/{path}?sb-hc-action=...[&sb-hc-id=...]&sb-hc-token=...`

Die `namespace-address` ist der vollqualifizierte Domänenname des Azure Relaynamespace, der die Hybridverbindung hostet, typischerweise in der Form `{myname}.servicebus.windows.net`.

Der Abfragezeichenfolgenparameter verfügt über folgende Optionen.

| Parameter        | Erforderlich | BESCHREIBUNG
| ---------------- | -------- | -------------------------------------------
| `sb-hc-action`   | Ja      | Für die Listener-Rolle muss der Parameter **sb-hc-action=listen** lauten.
| `{path}`         | Ja      | Der URL-codierte Namespacepfad der vorkonfigurierten Hybridverbindung, für die dieser Listener registriert wird. Dieser Ausdruck wird an den festen Pfadteil `$hc/` angehängt.
| `sb-hc-token`    | Ja\*    | Der Listener muss ein gültiges URL-codiertes Service Bus Shared Access-Token für den Namespace bereitstellen, oder eine Hybridverbindung, die **Listen** (Lauschen) direkt verleiht.
| `sb-hc-id`       | Nein       | Diese vom Client bereitgestellte optionale ID ermöglicht die End-to-End-Diagnoseablaufverfolgung.

Wenn die WebSocket-Verbindung nicht hergestellt werden kann, weil der Pfad der Hybridverbindung nicht registriert ist, ein Token ungültig ist oder fehlt oder ein anderer Fehler vorliegt, wird das Fehlerfeedback mithilfe des regulären HTTP 1.1-Statusinformationsmodells bereitgestellt. Die Statusbeschreibung enthält eine Fehlernachverfolgungs-ID, die an die Azure-Supportmitarbeiter übermittelt werden kann:

| Code | Fehler          | BESCHREIBUNG
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nicht gefunden      | Der Pfad der Hybridverbindung ist ungültig, oder die Basis-URL ist falsch formatiert.
| 401  | Nicht autorisiert   | Das Sicherheitstoken ist nicht vorhanden, falsch formatiert oder ungültig.
| 403  | Verboten      | Das Sicherheitstoken ist für diesen Pfad für diese Aktion ungültig.
| 500  | Interner Fehler | Es ist ein Fehler im Dienst aufgetreten.

Wenn die WebSocket-Verbindung absichtlich durch den Dienst heruntergefahren wird, nachdem sie anfangs eingerichtet wurde, erfolgt die Angabe des Grunds dafür mithilfe eines entsprechenden WebSocket-Protokollfehlercodes sowie mit einer beschreibenden Fehlermeldung, die auch eine Nachverfolgungs-ID enthält. Ohne eine erkennbare Fehlerbedingung fährt der Dienst den Steuerungskanal nicht herunter. Jedes saubere Herunterfahren wird durch den Client gesteuert.

| WS-Status | BESCHREIBUNG
| --------- | -------------------------------------------------------------------------------
| 1001      | Der Hybridverbindungspfad wurde gelöscht oder deaktiviert.
| 1008      | Das Sicherheitstoken ist abgelaufen, sodass die Autorisierungsrichtlinie verletzt wurde.
| 1011      | Es ist ein Fehler im Dienst aufgetreten.

#### <a name="accept-handshake"></a>Akzeptieren von Handshake

Die Benachrichtigung zum Akzeptieren wird vom Dienst als JSON-Nachricht in einem WebSocket-Textrahmen über den zuvor erstellten Steuerungskanal an den Listener übermittelt. Es gibt keine Antwort auf diese Nachricht.

Die Nachricht enthält ein JSON-Objekt mit dem Namen „accept“, das zu diesem Zeitpunkt die folgenden Eigenschaften definiert:

* **address**: Die URL-Zeichenfolge, die zum Erstellen des WebSockets für den Dienst verwendet wird, um eingehende Verbindungen zu akzeptieren.
* **id**: Der eindeutige Bezeichner für diese Verbindung. Wenn die ID vom Absenderclient angegeben wurde, ist sie der Wert, der vom Absender angegeben wurde. Andernfalls ist sie ein systemgenerierter Wert.
* **connectHeaders**: Alle HTTP-Header, die durch den Absender auf dem Relay-Endpunkt angegeben wurden, der ebenso das Sec-WebSocket-Protocol und die Sec-WebSocket-Extensions-Header enthält.

```json
{
    "accept" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?..."
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736",
        "connectHeaders" : {
            "Host" : "...",
            "Sec-WebSocket-Protocol" : "...",
            "Sec-WebSocket-Extensions" : "..."
        }
     }
}
```

Die in der JSON-Nachricht bereitgestellte Adress-URL wird vom Listener verwendet, um den WebSocket zu erstellen, der den Absendersocket akzeptieren oder ablehnen soll.

##### <a name="accepting-the-socket"></a>Akzeptieren des Sockets

Der Listener erstellt eine WebSocket-Verbindung zu der bereitgestellten Adresse, um zu akzeptieren.

Wenn die Benachrichtigung zum Akzeptieren einen `Sec-WebSocket-Protocol`-Header enthält, wird erwartet, dass der Listener den WebSocket nur dann akzeptiert, sofern er dieses Protokoll unterstützt. Außerdem wird der Header bei der WebSocket-Einrichtung festgelegt.

Dasselbe gilt für den `Sec-WebSocket-Extensions`-Header. Wenn das Framework eine Erweiterung unterstützt, sollte es den Header auf die serverseitige Antwort des erforderlichen `Sec-WebSocket-Extensions`-Handshakes für die Erweiterung festlegen.

Die URL muss unverändert für die Erstellung des accept-Sockets verwendet werden, doch enthält folgenden Parameter:

| Parameter      | Erforderlich | BESCHREIBUNG
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Zum Akzeptieren eines Sockets muss der Parameter `sb-hc-action=accept` lauten.
| `{path}`       | Ja      | (Siehe folgenden Abschnitt.)
| `sb-hc-id`     | Nein       | Siehe obige Beschreibung von **id**.

`{path}` ist der URL-codierte Namespacepfad der vorkonfigurierten Hybridverbindung, für die dieser Listener registriert werden soll. Dieser Ausdruck wird an den festen Pfadteil `$hc/` angehängt.

Der `path`-Ausdruck kann mit einem Suffix und einem Abfragezeichenfolgenausdruck erweitert werden, der nach einem trennenden Schrägstrich auf den registrierten Namen folgt.
Hiermit kann der Absenderclient dispatch-Argumente an den akzeptierenden Listener übergeben, wenn das Einschließen von HTTP-Headern nicht möglich ist. Erwartet wird, dass das Listenerframework den festen Pfadteil und den registrierten Namen aus dem Pfad analysiert und den Rest – unter Umständen ohne Abfragezeichenfolgenargumente mit vorangestelltem `sb-` – für die Anwendung verfügbar macht, damit diese entscheiden kann, ob die Verbindung akzeptiert wird.

Weitere Informationen finden Sie im Abschnitt „Absenderprotokoll“ weiter unten.

Wenn ein Fehler auftritt, kann der Dienst wie folgt reagieren:

| Code | Fehler          | BESCHREIBUNG
| ---- | -------------- | -----------------------------------
| 403  | Verboten      | Die URL ist nicht gültig.
| 500  | Interner Fehler | Es ist ein Fehler im Dienst aufgetreten

 Nachdem die Verbindung hergestellt wurde, beendet der Server den WebSocket, wenn der Absender-WebSocket beendet wird oder folgenden Status aufweist:

| WS-Status | BESCHREIBUNG                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Der Absenderclient beendet die Verbindung.                                    |
| 1001      | Der Hybridverbindungspfad wurde gelöscht oder deaktiviert.                        |
| 1008      | Das Sicherheitstoken ist abgelaufen, sodass die Autorisierungsrichtlinie verletzt wurde. |
| 1011      | Es ist ein Fehler im Dienst aufgetreten.                                            |

##### <a name="rejecting-the-socket"></a>Ablehnen des Sockets

 Das Ablehnen des Sockets nach dem Untersuchen der `accept`-Nachricht erfordert einen ähnlichen Handshake, sodass der Statuscode und die Statusbeschreibung, über die der Grund für die Ablehnung mitgeteilt wird, an den Absender zurückgegeben werden können.

 Gemäß Protokollentwurf wird hier ein WebSocket-Handshake verwendet (der per Entwurf in einem definierten Fehlerstatus endet), sodass Implementierungen von Listenerclients weiterhin auf einem WebSocket-Client basieren können und keinen zusätzlichen reinen HTTP-Client verwenden müssen.

 Zum Ablehnen des Sockets verwendet der Client den Adress-URI aus der `accept`-Nachricht und fügt wie folgt zwei Abfragezeichenfolgenparameter an:

| Parameter                   | Erforderlich | BESCHREIBUNG                              |
| ----------------------- | -------- | ---------------------------------------- |
| sb-hc-statusCode        | Ja      | Numerischer HTTP-Statuscode.                |
| sb-hc-statusDescription | Ja      | Ein visuell lesbarer Grund für die Ablehnung. |

Der resultierende URI wird dann verwendet, um eine WebSocket-Verbindung herzustellen.

Wenn der Vorgang erfolgreich abgeschlossen ist, schlägt dieser Handshake absichtlich mit dem HTTP-Fehlercode 410 fehl, da kein WebSocket erstellt wurde. Im Fehlerfall werden Fehler mit den folgenden Codes beschrieben:

| Code | Fehler          | BESCHREIBUNG                          |
| ---- | -------------- | ------------------------------------ |
| 403  | Verboten      | Die URL ist nicht gültig.                |
| 500  | Interner Fehler | Es ist ein Fehler im Dienst aufgetreten. |

#### <a name="request-message"></a>Request-Nachricht

Die `request`-Nachricht wird vom Dienst über den Steuerkanal an den Listener gesendet. Die gleiche Nachricht wird auch über das WebSocket-Rendezvouselement gesendet, nachdem es eingerichtet wurde.

`request` besteht aus zwei Teilen: einem Header und einem oder mehreren binären Textframes.
Wenn kein Text vorhanden ist, werden die Textframes weggelassen. Der Indikator für das Vorhandensein eines Texts ist die boolesche `body`-Eigenschaft in der „request“-Nachricht.

Für eine Anforderung mit einem Anforderungstext kann die Struktur beispielsweise wie folgt aussehen:

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : true,
        ...
    }
}
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame ----
FEFEFEFEFEFEFEFEFEFEF...
----- Web Socket binary frame -FIN
FEFEFEFEFEFEFEFEFEFEF...
----------------------------------
```

Der Listener MUSS den Empfang des Anforderungsheaders, der auf mehrere binäre Frames aufgeteilt ist (siehe [WebSocket-Fragmente](https://tools.ietf.org/html/rfc6455#section-5.4)), verarbeiten können.
Die Anforderung endet, wenn ein binärer Frame mit FIN-Flag empfangen wurde.

Bei einer Anforderung ohne Text ist nur ein Textframe vorhanden.

``` text
----- Web Socket text frame ----
{
    "request" :
    {
        "body" : false,
        ...
    }
}
----------------------------------
```

Der JSON-Inhalt für `request` lautet wie folgt:

* **address**: URI-Zeichenfolge. Dies ist die Rendezvousadresse, die für diese Anforderung verwendet wird. Wenn die eingehende Anforderung größer als 64 KB ist, ist der Rest dieser Nachricht leer, und der Client MUSS einen Rendezvous-Handshake initiieren, der äquivalent zum unten beschriebenen `accept`-Vorgang ist. Anschließend legt der Dienst das vollständige `request`-Element auf dem eingerichteten WebSocket-Element ab. Falls die Antwort voraussichtlich größer als 64 KB ist, MUSS der Listener auch einen Rendezvous-Handshake initiieren und die Antwort dann über das eingerichtete WebSocket-Element übertragen.
* **id**: Zeichenfolge. Der eindeutige Bezeichner für diese Anforderung.
* **requestHeaders**: Dieses Objekt enthält alle HTTP-Header, die vom Absender für den Endpunkt bereitgestellt wurden, mit Ausnahme der Autorisierungsinformationen (wie [oben](#request-operation) beschrieben) und von Headern, die sich ausschließlich auf die Verbindung mit dem Gateway beziehen. Es werden für ALLE Header, die in [RFC7230](https://tools.ietf.org/html/rfc7230) definiert oder reserviert sind (mit Ausnahme von `Via`), Entfernungen durchgeführt, und sie werden nicht weitergeleitet:

  * `Connection` (RFC7230, Abschnitt 6.1)
  * `Content-Length` (RFC7230, Abschnitt 3.3.2)
  * `Host` (RFC7230, Abschnitt 5.4)
  * `TE` (RFC7230, Abschnitt 4.3)
  * `Trailer` (RFC7230, Abschnitt 4.4)
  * `Transfer-Encoding` (RFC7230, Abschnitt 3.3.1)
  * `Upgrade` (RFC7230, Abschnitt 6.7)
  * `Close` (RFC7230, Abschnitt 8.1)

* **requestTarget**: Zeichenfolge. Diese Eigenschaft enthält das [„Anforderungsziel“ (RFC7230, Abschnitt 5.3)](https://tools.ietf.org/html/rfc7230#section-5.3) der Anforderung. Hierin ist der Abfragezeichenfolgen-Teil enthalten, aus dem ALLE Parameter mit dem Präfix `sb-hc-` entfernt werden.
* **method**: Zeichenfolge. Dies ist die Methode der Anforderung gemäß [RFC7231, Abschnitt 4](https://tools.ietf.org/html/rfc7231#section-4). Die `CONNECT`-Methode darf NICHT verwendet werden.
* **body**: Boolescher Wert. Gibt an, ob ein einzelner Frame oder mehrere Frames mit binärem Text folgen.

``` JSON
{
    "request" : {
        "address" : "wss://dc-node.servicebus.windows.net:443/$hc/{path}?...",
        "id" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "requestTarget" : "/abc/def?myarg=value&otherarg=...",
        "method" : "GET",
        "requestHeaders" : {
            "Host" : "...",
            "Content-Type" : "...",
            "User-Agent" : "..."
        },
        "body" : true
     }
}
```

##### <a name="responding-to-requests"></a>Antworten auf Anforderungen

Der Empfänger MUSS antworten. Wenn eine Antwort auf Anforderungen wiederholt ausbleibt, während die Verbindung besteht, kann dies dazu führen, dass der Listener blockiert wird.

Antworten können in einer beliebigen Reihenfolge gesendet werden, aber auf jede Anforderung muss innerhalb von 60 Sekunden eine Antwort erfolgen, da die Zustellung ansonsten als nicht erfolgreich gemeldet wird. Der Zeitraum von 60 Sekunden läuft, bis der `response`-Frame vom Dienst empfangen wurde. Eine aktive Antwort mit mehreren binären Frames darf sich nicht länger als 60 Sekunden im Leerlauf befinden, da sie sonst beendet wird.

Wenn die Anforderung über den Steuerkanal empfangen wird, MUSS die Antwort entweder über den Steuerkanal gesendet werden, über den die Anforderung eingegangen ist, oder sie MUSS über einen Rendezvous-Kanal gesendet werden.

Die Antwort ist ein JSON-Objekt mit dem Namen „response“. Die Regeln für die Verarbeitung des Textinhalts entsprechen genau der Verwendung der `request`-Nachricht und basieren auf der `body`-Eigenschaft.

* **requestId**: Zeichenfolge. REQUIRED. (ERFORDERLICH.) Der `id`-Eigenschaftswert der `request`-Nachricht, auf die geantwortet wird.
* **statusCode**: Zahl. REQUIRED. (ERFORDERLICH.) Ein numerischer HTTP-Statuscode, der das Ergebnis der Benachrichtigung angibt. Alle Statuscodes aus [RFC7231, Abschnitt 6](https://tools.ietf.org/html/rfc7231#section-6) sind zulässig, mit Ausnahme von [502 „Ungültiges Gateway“](https://tools.ietf.org/html/rfc7231#section-6.6.3) und [504 „Gatewaytimeout“](https://tools.ietf.org/html/rfc7231#section-6.6.5).
* **statusDescription**: Zeichenfolge. OPTIONAL. HTTP-Statuscode-Ursachentext gemäß [RFC7230, Abschnitt 3.1.2](https://tools.ietf.org/html/rfc7230#section-3.1.2)
* **responseHeaders**: HTTP-Header, die in einer externen HTTP-Antwort festgelegt werden.
  Wie bei `request` auch, dürfen per RFC7230 definierte Header NICHT verwendet werden.
* **body**: Boolescher Wert. Gibt an, ob ein oder mehrere Textframes folgen.

``` text
----- Web Socket text frame ----
{
    "response" : {
        "requestId" : "42c34cb5-7a04-4d40-a19f-bdc66441e736",
        "statusCode" : "200",
        "responseHeaders" : {
            "Content-Type" : "application/json",
            "Content-Encoding" : "gzip"
        }
         "body" : true
     }
}
----- Web Socket binary frame -FIN
{ "hey" : "mydata" }
----------------------------------
```

##### <a name="responding-via-rendezvous"></a>Antworten per Rendezvous

Für Antworten, die größer als 64 KB sind, MUSS die Antwort per Rendezvous-Socket zugestellt werden. Falls die Anforderung größer als 64 KB ist und `request` nur das Adressfeld enthält, muss ein Rendezvous-Socket eingerichtet werden, um das `request`-Element zu erhalten. Nachdem ein Rendezvous-Socket eingerichtet wurde, MÜSSEN Antworten an den jeweiligen Client und nachfolgende Anforderungen vom Client über den Rendezvous-Socket zugestellt werden, solange dieser aktiv ist.

Die URL von `address` im `request`-Element muss unverändert für die Erstellung des Rendezvous-Sockets verwendet werden, aber sie enthält die folgenden Parameter:

| Parameter      | Erforderlich | BESCHREIBUNG
| -------------- | -------- | -------------------------------------------------------------------
| `sb-hc-action` | Ja      | Zum Akzeptieren eines Sockets muss der Parameter `sb-hc-action=request` lauten.

Wenn ein Fehler auftritt, kann der Dienst wie folgt reagieren:

| Code | Fehler           | BESCHREIBUNG
| ---- | --------------- | -----------------------------------
| 400  | Ungültige Anforderung | Nicht erkannte Aktion oder ungültige URL.
| 403  | Verboten       | Die URL ist abgelaufen.
| 500  | Interner Fehler  | Es ist ein Fehler im Dienst aufgetreten

 Nachdem die Verbindung hergestellt wurde, beendet der Server den WebSocket, wenn der HTTP-Socket des Clients beendet wird oder folgenden Status aufweist:

| WS-Status | BESCHREIBUNG                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1001      | Der Absenderclient beendet die Verbindung.                                    |
| 1001      | Der Hybridverbindungspfad wurde gelöscht oder deaktiviert.                        |
| 1008      | Das Sicherheitstoken ist abgelaufen, sodass die Autorisierungsrichtlinie verletzt wurde. |
| 1011      | Es ist ein Fehler im Dienst aufgetreten.                                            |


#### <a name="listener-token-renewal"></a>Erneuerung des Listenertokens

Wenn das Listenertoken abläuft, kann es ersetzt werden, indem über den eingerichteten Steuerungskanal eine Textrahmennachricht an den Dienst gesendet wird. Die Nachricht enthält ein JSON-Objekt mit dem Namen `renewToken`, das derzeit die folgende Eigenschaft definiert:

* **token**: Ein gültiges URL-codiertes Service Bus Shared Access-Token für den Namespace oder eine Hybridverbindung zum direkten Gewähren des Rechts **Listen** (Lauschen).

```json
{
  "renewToken": {
    "token":
      "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fhyco%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
  }
}
```

Wenn die Tokenüberprüfung nicht erfolgreich ist, wird der Zugriff verweigert, und der Clouddienst schließt den WebSocket des Steuerkanals mit einem Fehler. Andernfalls erfolgt keine Antwort.

| WS-Status | BESCHREIBUNG                                                                     |
| --------- | ------------------------------------------------------------------------------- |
| 1008      | Das Sicherheitstoken ist abgelaufen, sodass die Autorisierungsrichtlinie verletzt wurde. |

### <a name="web-socket-connect-protocol"></a>WebSocket-Verbindungsprotokoll

Das Absenderprotokoll ist praktisch identisch mit der Erstellung eines Listeners.
Das Ziel ist die maximale Transparenz für den End-to-End-WebSocket. Die Adresse für die Verbindung ist dieselbe wie für den Listener, aber die „action“ ist anders, und das Token benötigt eine andere Berechtigung:

```
wss://{namespace-address}/$hc/{path}?sb-hc-action=...&sb-hc-id=...&sb-hc-token=...
```

Die _namespace-address_ ist der vollqualifizierte Domänenname des Azure Relaynamespace, der die Hybridverbindung hostet, typischerweise in der Form `{myname}.servicebus.windows.net`.

Die Anforderung kann beliebige zusätzliche HTTP-Header enthalten, z.B. auch von der Anwendung definierte Header. Alle angegebenen Header werden an den Listener weitergegeben und befinden sich im `connectHeader`-Objekt der **accept**-Steuerungsnachricht.

Die Optionen des Abfragezeichenfolgenparameters lauten wie folgt:

| Parameter          | Erforderlich? | BESCHREIBUNG
| -------------- | --------- | -------------------------- |
| `sb-hc-action` | Ja       | Für die Absenderrolle muss der Parameter `sb-hc-action=connect` lauten.
| `{path}`       | Ja       | (Siehe folgenden Abschnitt.)
| `sb-hc-token`  | Ja\*     | Der Listener muss ein gültiges URL-codiertes Service Bus Shared Access-Token für den Namespace oder die Hybridverbindung bereitstellen, das die Berechtigung **Senden** direkt überträgt.
| `sb-hc-id`     | Nein        | Eine optionale ID, mit der die End-to-End-Diagnoseablaufverfolgung möglich ist und die während des accept-Handshakes für den Listener verfügbar gemacht wird.

 Der `{path}` ist der URL-codierte Namespacepfad der vorkonfigurierten Hybridverbindung, für die dieser Listener registriert werden soll. Der `path`-Ausdruck kann mit einem Suffix und einem Abfragezeichenfolgenausdruck für die weitere Kommunikation erweitert werden. Wenn die Hybridverbindung unter dem Pfad `hyco` registriert ist, kann der `path`-Ausdruck `hyco/suffix?param=value&...` lauten, gefolgt von den hier definierten Abfragezeichenfolgenparametern. Ein vollständiger Ausdruck kann dann wie folgt lauten:

```
wss://{namespace-address}/$hc/hyco/suffix?param=value&sb-hc-action=...[&sb-hc-id=...&]sb-hc-token=...
```

Der `path`-Ausdruck wird an den Listener in dem Adress-URI übergeben, der in der accept-Steuerungsnachricht enthalten ist.

Wenn die WebSocket-Verbindung nicht hergestellt werden kann, weil der Pfad der Hybridverbindung nicht registriert ist, ein Token ungültig ist oder fehlt oder ein anderer Fehler vorliegt, wird das Fehlerfeedback mithilfe des regulären HTTP 1.1-Statusinformationsmodells bereitgestellt. Die Statusbeschreibung enthält eine Fehlernachverfolgungs-ID, die an die Azure-Supportmitarbeiter übermittelt werden kann:

| Code | Fehler          | BESCHREIBUNG
| ---- | -------------- | -------------------------------------------------------------------
| 404  | Nicht gefunden      | Der Pfad der Hybridverbindung ist ungültig, oder die Basis-URL ist falsch formatiert.
| 401  | Nicht autorisiert   | Das Sicherheitstoken ist nicht vorhanden, falsch formatiert oder ungültig.
| 403  | Verboten      | Das Sicherheitstoken ist für diesen Pfad und für diese Aktion ungültig.
| 500  | Interner Fehler | Es ist ein Fehler im Dienst aufgetreten.

Wenn die WebSocket-Verbindung absichtlich durch den Dienst heruntergefahren wird, nachdem sie anfangs eingerichtet wurde, erfolgt die Angabe des Grunds dafür mithilfe eines entsprechenden WebSocket-Protokollfehlercodes sowie mit einer beschreibenden Fehlermeldung, die auch eine Nachverfolgungs-ID enthält.

| WS-Status | BESCHREIBUNG
| --------- | ------------------------------------------------------------------------------- 
| 1000      | Der Listener beendet den Socket.
| 1001      | Der Hybridverbindungspfad wurde gelöscht oder deaktiviert.
| 1008      | Das Sicherheitstoken ist abgelaufen, sodass die Autorisierungsrichtlinie verletzt wurde.
| 1011      | Es ist ein Fehler im Dienst aufgetreten.

### <a name="http-request-protocol"></a>HTTP-Anforderungsprotokoll

Das HTTP-Anforderungsprotokoll ermöglicht beliebige HTTP-Anforderungen, mit Ausnahme von Protokollupgrades.
Für HTTP-Anforderungen wird auf die reguläre Laufzeitadresse der Entität verwiesen, aber ohne das Infix $hc, das für WebSocket-Clients von Hybridverbindungen verwendet wird.

```
https://{namespace-address}/{path}?sb-hc-token=...
```

Die _namespace-address_ ist der vollqualifizierte Domänenname des Azure Relaynamespace, der die Hybridverbindung hostet, typischerweise in der Form `{myname}.servicebus.windows.net`.

Die Anforderung kann beliebige zusätzliche HTTP-Header enthalten, z.B. auch von der Anwendung definierte Header. Alle bereitgestellten Header – mit Ausnahme der direkt in RFC7230 definierten (siehe [Request-Nachricht](#request-message)) – fließen an den Listener und befinden sich im `requestHeader`-Objekt der **request**-Nachricht.

Die Optionen des Abfragezeichenfolgenparameters lauten wie folgt:

| Parameter          | Erforderlich? | BESCHREIBUNG
| -------------- | --------- | ---------------- |
| `sb-hc-token`  | Ja\*     | Der Listener muss ein gültiges URL-codiertes Service Bus Shared Access-Token für den Namespace oder die Hybridverbindung bereitstellen, das die Berechtigung **Senden** direkt überträgt.

Das Token kann auch im HTTP-Header `ServiceBusAuthorization` oder `Authorization` enthalten sein. Das Token kann weggelassen werden, wenn die Hybridverbindung so konfiguriert ist, dass anonyme Anforderungen zulässig sind.

Da der Dienst quasi als Proxy fungiert (wenn auch nicht wie ein echter HTTP-Proxy), fügt er entweder einen `Via`-Header hinzu oder fügt entsprechend gemäß [RFC7230, Abschnitt 5.7.1](https://tools.ietf.org/html/rfc7230#section-5.7.1) Anmerkungen an den vorhandenen `Via`-Header an.
Der Dienst fügt den Hostnamen des Relay-Namespace an `Via` an.

| Code | `Message`  | BESCHREIBUNG                    |
| ---- | -------- | ------------------------------ |
| 200  | OK       | Die Anforderung wurde von mindestens einem Listener verarbeitet.  |
| 202  | Zulässig | Die Anforderung wurde von mindestens einem Listener akzeptiert. |

Wenn ein Fehler auftritt, kann der Dienst wie folgt reagieren. Anhand des Vorhandenseins des `Via`-Headers kann identifiziert werden, ob die Antwort vom Dienst oder vom Listener stammt. Wenn der Header vorhanden ist, stammt die Antwort vom Listener.

| Code | Fehler           | BESCHREIBUNG
| ---- | --------------- |--------- |
| 404  | Nicht gefunden       | Der Pfad der Hybridverbindung ist ungültig, oder die Basis-URL ist falsch formatiert.
| 401  | Nicht autorisiert    | Das Sicherheitstoken ist nicht vorhanden, falsch formatiert oder ungültig.
| 403  | Verboten       | Das Sicherheitstoken ist für diesen Pfad und für diese Aktion ungültig.
| 500  | Interner Fehler  | Es ist ein Fehler im Dienst aufgetreten.
| 503  | Ungültiges Gateway     | Die Anforderung konnte nicht an einen Listener weitergeleitet werden.
| 504  | Gatewaytimeout | Die Anforderung wurde an einen Listener weitergeleitet, aber der Listener hat den Empfang nicht innerhalb des erforderlichen Zeitraums bestätigt.

## <a name="next-steps"></a>Nächste Schritte

* [Relay – häufig gestellte Fragen](relay-faq.md)
* [Erstellen eines Namespaces](relay-create-namespace-portal.md)
* [Erste Schritte mit .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Erste Schritte mit Node](relay-hybrid-connections-node-get-started.md)
