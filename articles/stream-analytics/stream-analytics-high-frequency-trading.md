---
title: Hochfrequenzhandel-Simulation mit Azure Stream Analytics
description: Hier erfahren Sie, wie Sie das lineare Regressionsmodell in einem Azure Stream Analytics-Auftrag trainieren und bewerten.
services: stream-analytics
author: zhongc
ms.author: zhongc
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 33a7b27d065fc0383e4693053f7bfb6d56e2d33b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61480067"
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Hochfrequenzhandel-Simulation mit Stream Analytics
Die Kombination aus SQL-Sprache und benutzerdefinierten JavaScript-Funktionen (User-Defined Functions, UDFs) bzw. benutzerdefinierten Aggregaten (User-Defined Aggregates, UDAs) in Azure Stream Analytics ermöglicht Benutzern die Nutzung von Advanced Analytics. Beispiele für Analysen mit Advanced Analytics sind das Online-Trainieren und -Bewerten für Machine Learning und die Simulation zustandsbehafteter Prozesse. In diesem Artikel wird beschrieben, wie Sie die lineare Regression in einem Azure Stream Analytics-Auftrag durchführen, mit dem ständig Trainings- und Bewertungsschritte für ein Hochfrequenzhandel-Szenario ausgeführt werden.

## <a name="high-frequency-trading"></a>Hochfrequenzhandel
Beim logischen Ablauf des Hochfrequenzhandels geht es um Folgendes:
1. Beschaffen von Echtzeit-Kursnotierungen von einer Wertpapierbörse
2. Erstellen eines Vorhersagemodells für die Kursnotierungen, um die Kursbewegung zu antizipieren
3. Platzieren von entsprechenden Kauf- oder Verkaufsaufträgen, um aufgrund der erfolgreichen Vorhersage der Preisbewegungen Geld zu verdienen 

Daher benötigen wir Folgendes:
* Einen Feed mit Echtzeit-Kursnotierungen
* Ein Vorhersagemodell für die Echtzeit-Kursnotierungen
* Eine Handelssimulation, die für den Handelsalgorithmus den Gewinn und Verlust angibt

### <a name="real-time-quote-feed"></a>Feed mit Echtzeit-Kursnotierungen
Über IEX sind kostenlose [Geld- und Brief-Kursnotierungen](https://iextrading.com/developer/docs/#websockets) per „socket.io“ verfügbar. Es kann ein einfaches Konsolenprogramm geschrieben werden, um Echtzeit-Kursnotierungen zu erhalten und per Pushvorgang an Azure Event Hubs als Datenquelle zu übertragen. Der folgende Code bildet das Grundgerüst des Programms. Die Fehlerbehandlung wurde im Code weggelassen, um die Komplexität gering zu halten. Es ist erforderlich, dass Sie auch die NuGet-Pakete SocketIoClientDotNet und WindowsAzure.ServiceBus in Ihr Projekt einbinden.

```csharp
using Quobject.SocketIoClientDotNet.Client;
using Microsoft.ServiceBus.Messaging;
var symbols = "msft,fb,amzn,goog";
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");
socket.On(Socket.EVENT_MESSAGE, (message) =>
{
    eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
});
socket.On(Socket.EVENT_CONNECT, () =>
{
    socket.Emit("subscribe", symbols);
});
```

Hier sind einige generierte Beispielereignisse angegeben:

```json
{"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
{"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}
```

>[!NOTE]
>Der Zeitstempel des Ereignisses lautet **lastUpdated** (in Epochenzeit).

### <a name="predictive-model-for-high-frequency-trading"></a>Vorhersagemodell für Hochfrequenzhandel
Zu Demonstrationszwecken verwenden wir ein lineares Modell, das Darryl Shen in [seinem Dokument](http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf) beschreibt.

„Volume Order Imbalance“ (VOI) ist eine Funktion des Geld-/Briefkurses (aktuell und letzter Tick-Vorgang). Im Dokument wird die Korrelation zwischen VOI und der zukünftigen Preisbewegung erläutert. Es wird ein lineares Modell zwischen den letzten fünf VOI-Werten und der Preisänderung der nächsten zehn Tick-Vorgänge erstellt. Das Modell wird mit den Daten des vorherigen Tags per linearer Regression trainiert. 

Das trainierte Modell wird dann verwendet, um Vorhersagen zu Preisänderungen für Kursnotierungen des aktuellen Handelstags in Echtzeit zu erstellen. Wenn eine ausreichend hohe Preisänderung vorhergesagt wird, wird ein Handelsvorgang ausgeführt. Je nach Schwellenwerteinstellung sind für eine einzelne Aktie während eines Handelstags Tausende von Handelsvorgängen zu erwarten.

![Volume Order Imbalance-Definition](./media/stream-analytics-high-frequency-trading/volume-order-imbalance-formula.png)

Wir drücken die Trainings- und Vorhersagevorgänge jetzt in einem Azure Stream Analytics-Auftrag aus.

Zunächst werden die Eingaben bereinigt. Die Epochenzeit wird per **DATEADD** in „datetime“ konvertiert. **TRY_CAST** wird verwendet, um Datentypen umzuwandeln, ohne dass für die Abfrage ein Fehler auftritt. Es ist immer ratsam, Eingabefelder in die erwarteten Datentypen umzuwandeln, damit es nicht zu unerwartetem Verhalten kommt, wenn die Felder bearbeitet oder verglichen werden sollen.

```SQL
WITH
typeconvertedquotes AS (
    /* convert all input fields to proper types */
    SELECT
        System.Timestamp AS lastUpdated,
        symbol,
        DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
        TRY_CAST(bidSize as bigint) AS bidSize,
        TRY_CAST(bidPrice as float) AS bidPrice,
        TRY_CAST(askSize as bigint) AS askSize,
        TRY_CAST(askPrice as float) AS askPrice,
        TRY_CAST(volume as bigint) AS volume,
        TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
        TRY_CAST(lastSalePrice as float) AS lastSalePrice
    FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
),
timefilteredquotes AS (
    /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
    /* clean up invalid data points */
    SELECT * FROM typeconvertedquotes
    WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
),
```

Als Nächstes verwenden wir die Funktion **LAG**, um Werte des letzten Tick-Vorgangs zu erhalten. Für den Wert **LIMIT DURATION** wird ein willkürlicher Wert von einer Stunde gewählt. Anhand der Häufigkeit von Kursnotierungen kann sicher angenommen werden, dass der vorherige Tick-Vorgang innerhalb der vergangenen Stunde zu finden ist.  

```SQL
shiftedquotes AS (
    /* get previous bid/ask price and size in order to calculate VOI */
    SELECT
        symbol,
        (bidPrice + askPrice)/2 AS midPrice,
        bidPrice,
        bidSize,
        askPrice,
        askSize,
        LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
        LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
        LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
        LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
    FROM timefilteredquotes
),
```

Anschließend können wir den VOI-Wert berechnen. Wir filtern die NULL-Werte für den Fall heraus, dass der vorherige Tick-Vorgang nicht vorhanden ist.

```SQL
currentPriceAndVOI AS (
    /* calculate VOI */
    SELECT
        symbol,
        midPrice,
        (CASE WHEN (bidPrice < bidPricePrev) THEN 0
            ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
         END) -
        (CASE WHEN (askPrice < askPricePrev) THEN askSize
            ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
         END) AS VOI
    FROM shiftedquotes
    WHERE
        bidPrice IS NOT NULL AND
        bidSize IS NOT NULL AND
        askPrice IS NOT NULL AND
        askSize IS NOT NULL AND
        bidPricePrev IS NOT NULL AND
        bidSizePrev IS NOT NULL AND
        askPricePrev IS NOT NULL AND
        askSizePrev IS NOT NULL
),
```

Anschließend verwenden wir erneut **LAG**, um eine Sequenz mit zwei aufeinanderfolgenden VOI-Werten zu erstellen, gefolgt von zehn aufeinanderfolgenden Werten mit mittleren Preisen.

```SQL
shiftedPriceAndShiftedVOI AS (
    /* get 10 future prices and 2 previous VOIs */
    SELECT
        symbol,
        midPrice AS midPrice10,
        LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
        LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
        LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
        LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
        LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
        LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
        LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
        LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
        LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
        LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
        LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
        LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
```

Anschließend formen wir die Daten in Eingaben für ein lineares Modell mit zwei Variablen um. Wir filtern wieder die Ereignisse heraus, für die wir nicht alle Daten besitzen.

```SQL
modelInput AS (
    /* create feature vector, x being VOI, y being delta price */
    SELECT
        symbol,
        (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
        VOI1 AS x1,
        VOI2 AS x2
    FROM shiftedPriceAndShiftedVOI
    WHERE
        midPrice1 IS NOT NULL AND
        midPrice2 IS NOT NULL AND
        midPrice3 IS NOT NULL AND
        midPrice4 IS NOT NULL AND
        midPrice5 IS NOT NULL AND
        midPrice6 IS NOT NULL AND
        midPrice7 IS NOT NULL AND
        midPrice8 IS NOT NULL AND
        midPrice9 IS NOT NULL AND
        midPrice10 IS NOT NULL AND
        midPrice IS NOT NULL AND
        VOI1 IS NOT NULL AND
        VOI2 IS NOT NULL
),
```

Da Azure Stream Analytics nicht über eine integrierte Funktion für die lineare Regression verfügt, verwenden wir die Aggregatfunktionen **SUM** und **AVG**, um die Koeffizienten für das lineare Modell zu berechnen.

![Mathematische Formel für lineare Regression](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

```SQL
modelagg AS (
    /* get aggregates for linear regression calculation,
     http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
    SELECT
        symbol,
        SUM(x1 * x1) AS x1x1,
        SUM(x2 * x2) AS x2x2,
        SUM(x1 * y) AS x1y,
        SUM(x2 * y) AS x2y,
        SUM(x1 * x2) AS x1x2,
        AVG(y) AS avgy,
        AVG(x1) AS avgx1,
        AVG(x2) AS avgx2
    FROM modelInput
    GROUP BY symbol, TumblingWindow(hour, 24, -4)
),
modelparambs AS (
    /* calculate b1 and b2 for the linear model */
    SELECT
        symbol,
        (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
        (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
        avgy,
        avgx1,
        avgx2
    FROM modelagg
),
model AS (
    /* calculate a for the linear model */
    SELECT
        symbol,
        avgy - b1 * avgx1 - b2 * avgx2 AS a,
        b1,
        b2
    FROM modelparambs
),
```

Wir möchten die Kursnotierungen in das Modell einbinden, um das Modell des vorherigen Tags für die Bewertung des aktuellen Ereignisses zu verwenden. Anstelle von **JOIN** verwenden wir **UNION** für die Modell- und Kursnotierungsereignisse. Anschließend verwenden wir **LAG**, um die Ereignisse mit dem Modell des vorherigen Tags zu koppeln, damit wir genau eine Übereinstimmung erhalten. Aufgrund des Wochenendes müssen wir drei Tage zurückgehen. Wenn wir einfach **JOIN** verwenden, erhalten wir drei Modelle für jedes Kursnotierungsereignis.

```SQL
shiftedVOI AS (
    /* get two consecutive VOIs */
    SELECT
        symbol,
        midPrice,
        VOI AS VOI1,        
        LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
    FROM currentPriceAndVOI
),
VOIAndModel AS (
    /* combine VOIs and models */
    SELECT
        'voi' AS type,
        symbol,
        midPrice,
        VOI1,
        VOI2,
        0.0 AS a,
        0.0 AS b1,
        0.0 AS b2
    FROM shiftedVOI
    UNION
    SELECT
        'model' AS type,
        symbol,
        0.0 AS midPrice,
        0 AS VOI1,
        0 AS VOI2,
        a,
        b1,
        b2
    FROM model
),
VOIANDModelJoined AS (
    /* match VOIs with the latest model within 3 days (72 hours, to take the weekend into account) */
    SELECT
        symbol,
        midPrice,
        VOI1 as x1,
        VOI2 as x2,
        LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
        LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
        LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
    FROM VOIAndModel
    WHERE type = 'voi'
),
```

Jetzt können wir anhand des Modells Vorhersagen treffen und Kauf- bzw. Verkaufssignale generieren. Der Schwellenwert beträgt hierbei 0,02. Der Handelsvorgangswert 10 steht für einen Kauf. Der Wert -10 steht für einen Verkauf.

``` SQL
prediction AS (
    /* make prediction if there is a model */
    SELECT
        symbol,
        midPrice,
        a + b1 * x1 + b2 * x2 AS efpc
    FROM VOIANDModelJoined
    WHERE
        a IS NOT NULL AND
        b1 IS NOT NULL AND
        b2 IS NOT NULL AND
        x1 IS NOT NULL AND
        x2 IS NOT NULL
),
tradeSignal AS (
    /* generate buy/sell signals */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,     
        midPrice,
        efpc,
        CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
        DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
    FROM prediction
),
```

### <a name="trading-simulation"></a>Handelssimulation
Da wir jetzt über die Handelssignale verfügen, möchten wir als Nächstes testen, wie effektiv die Handelsstrategie ist, ohne tatsächlich Käufe und Verkäufe abzuwickeln. 

Diesen Test können wir mit einem benutzerdefinierten Aggregat mit springenden Fenstern durchführen, die jede Minute wechseln. Dank der zusätzlichen Gruppierung nach Datum und der having-Klausel kann das Fenster auf Ereignisse desselben Tags beschränkt werden. Wenn ein springendes Fenster über zwei Tage verlaufen soll, kann die Gruppierung mit dem **GROUP BY**-Datum in den vorherigen Tag und den aktuellen Tag unterteilt werden. Mit der **HAVING**-Klausel werden die Fenster herausgefiltert, die an demselben Tag enden, aber für den vorherigen Tag gruppiert sind.

```SQL
simulation AS
(
    /* perform trade simulation for the past 7 hours to cover an entire trading day, and generate output every minute */
    SELECT
        DateAdd(hour, -7, System.Timestamp) AS time,
        symbol,
        date,
        uda.TradeSimulation(tradeSignal) AS s
    FROM tradeSignal
    GROUP BY HoppingWindow(minute, 420, 1), symbol, date
    Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
)
```

Mit dem JavaScript-UDA werden Akkumulatoren in der `init`-Funktion initialisiert, der Statusübergang wird für jedes im Fenster hinzugefügte Ereignis berechnet, und unten im Fenster werden die Simulationsergebnisse zurückgegeben. Der allgemeine Handelsprozess läuft wie folgt ab:

- Aktien kaufen, wenn ein Kaufsignal empfangen wird und keine Aktien gehalten werden.
- Aktien verkaufen, wenn ein Verkaufssignal empfangen wird und Aktien gehalten werden.
- Leerverkauf durchführen, falls keine Aktien gehalten werden. 

Wenn eine Short-Position vorhanden ist und ein Kaufsignal empfangen wird, Aktien kaufen, um den Bestand zu decken. In dieser Simulation halten oder „leerverkaufen“ wir niemals zehn Anteilsscheine einer bestimmten Aktie. Die Transaktionskosten liegen genau bei 8 US-Dollar.

```javascript
function main() {
    var TRADE_COST = 8.0;
    var SHARES = 10;
    this.init = function () {
        this.own = false;
        this.pos = 0;
        this.pnl = 0.0;
        this.tradeCosts = 0.0;
        this.buyPrice = 0.0;
        this.sellPrice = 0.0;
        this.buySize = 0;
        this.sellSize = 0;
        this.buyTotal = 0.0;
        this.sellTotal = 0.0;
    }
    this.accumulate = function (tradeSignal, timestamp) {
        if(!this.own && tradeSignal.trade == 10) {
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
        } else if(!this.own && tradeSignal.trade == -10) {
          // Sell to open
          this.own = true;
          this.pos = -1
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
          // Sell to close
          this.own = false;
          this.pos = 0;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.sellSize += SHARES;
          this.sellTotal += SHARES * tradeSignal.midprice;
          // Sell to open
          this.own = true;
          this.pos = -1;
          this.sellPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.sellSize += SHARES;        
          this.sellTotal += SHARES * tradeSignal.midprice;
        } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
          // Buy to close
          this.own = false;
          this.pos = 0;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
          this.buySize += SHARES;
          this.buyTotal += SHARES * tradeSignal.midprice;
          // Buy to open
          this.own = true;
          this.pos = 1;
          this.buyPrice = tradeSignal.midprice;
          this.tradeCosts += TRADE_COST;
          this.buySize += SHARES;         
          this.buyTotal += SHARES * tradeSignal.midprice;
        }
    }
    this.computeResult = function () {
        var result = {
            "pnl": this.pnl,
            "buySize": this.buySize,
            "sellSize": this.sellSize,
            "buyTotal": this.buyTotal,
            "sellTotal": this.sellTotal,
            "tradeCost": this.tradeCost
            };
        return result;
    }
}
```

Abschließend geben wir die Daten zur Visualisierung im Power BI-Dashboard aus.

```SQL
SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
SELECT
    symbol,
    time,
    date,
    TRY_CAST(s.pnl as float) AS pnl,
    TRY_CAST(s.buySize as bigint) AS buySize,
    TRY_CAST(s.sellSize as bigint) AS sellSize,
    TRY_CAST(s.buyTotal as float) AS buyTotal,
    TRY_CAST(s.sellTotal as float) AS sellTotal
    INTO pnlDashboard
FROM simulation /* output trade simulation to PBI */
```

![Power BI-Diagrammvisual „Trades“](./media/stream-analytics-high-frequency-trading/trades-power-bi-chart.png)

![Power BI-Diagrammvisual „PNL“](./media/stream-analytics-high-frequency-trading/pnl-power-bi-chart.png)


## <a name="summary"></a>Zusammenfassung
Wir können mit einer nicht übermäßig aufwändigen Abfrage in Azure Stream Analytics ein realistisches Modell für den Hochfrequenzhandel implementieren. Hierfür müssen wir das Modell von fünf auf zwei Eingabevariablen vereinfachen, da keine integrierte Funktion für die lineare Regression vorhanden ist. Es ist für interessierte Benutzer aber durchaus möglich, auch anspruchsvollere Algorithmen mit höheren Dimensionen als JavaScript-UDA zu implementieren. 

Beachten Sie hierbei Folgendes: Im Gegensatz zum JavaScript-UDA kann der Großteil der Abfrage in Visual Studio mit dem [Azure Stream Analytics-Tool für Visual Studio](stream-analytics-tools-for-visual-studio-install.md) getestet und debuggt werden. Nachdem die erste Abfrage geschrieben wurde, hat der Verfasser für das Testen und Debuggen der Abfrage in Visual Studio weniger als 30 Minuten benötigt. 

Derzeit ist das Debuggen des UDA in Visual Studio nicht möglich. Wir arbeiten daran, dies und den Schritt-für-Schritt-Durchlauf des JavaScript-Codes zu ermöglichen. Beachten Sie auch, dass die Namen der Felder für die Erreichung des UDA nur Kleinbuchstaben enthalten. Dies war beim Testen der Abfrage kein offensichtliches Verhalten. Für Azure Stream Analytics-Kompatibilitätsebene 1.1 wird die Groß-/Kleinschreibung von Feldnamen aber beibehalten, damit das Verhalten natürlicher ist.

Ich hoffe, dieser Artikel ist eine Inspiration für alle Azure Stream Analytics-Benutzer unseres Diensts, wenn es um die dauerhafte Durchführung von erweiterten Analysevorgängen nahezu in Echtzeit geht. Teilen Sie uns Ihr Feedback mit, um das Implementieren von Abfragen für Szenarien mit erweiterten Analysevorgängen zu erleichtern.
