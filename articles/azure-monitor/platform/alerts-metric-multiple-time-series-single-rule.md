---
title: Überwachen mehrerer Zeitreihen in einer einzelnen Metrikwarnungsregel
description: Bedarfsorientierte Warnung mithilfe einer einzelnen Warnungsregel für mehrere Zeitreihen
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 11/12/2020
ms.subservice: alerts
ms.openlocfilehash: 66987a28acc8a2c9ae71d89ff5760fa508e32963
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566502"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Überwachen mehrerer Zeitreihen in einer einzelnen Metrikwarnungsregel

Eine einzelne Metrikwarnungsregel kann zum Überwachen einer oder vieler metrischer Zeitreihen verwendet werden und dadurch die bedarfsorientierte Überwachung von Ressourcen vereinfachen.

## <a name="metric-time-series"></a>Metrische Zeitreihen

Bei einer metrischen Zeitreihe handelt es sich um eine Reihe von Messwerten (oder „Metrikwerte“), die über einen Zeitraum erfasst werden. 

Beispiel:

- Die CPU-Auslastung eines virtuellen Computers
- Die eingehenden Bytes (Eingang) eines Speicherkontos
- Die Anzahl fehlerhafter Anforderungen einer Webanwendung



## <a name="alert-rule-on-a-single-time-series"></a>Warnungsregel für eine einzelne Zeitreihe
Eine Warnungsregel überwacht eine einzelne Zeitreihe, wenn sie alle folgenden Bedingungen erfüllt:
-   Die Regel überwacht eine einzelne Zielressource. 
-   Sie enthält eine einzelne Bedingung.
-   Sie wertet eine Metrik ohne Auswahl von Dimensionen aus (vorausgesetzt, dass die Metrik Dimensionen unterstützt).

Hier ein Beispiel einer solchen Warnungsregel (nur die relevanten Eigenschaften sind angegeben):
-   Zielressource: *myVM1*
-   Metrik: *CPU-Prozentsatz*
-   Operator: *Größer als*
-   Schwellenwert: *70*


Für diese Warnungsregel wird eine einzelne metrische Zeitreihe überwacht:
-   CPU-Prozentsatz, wobei *Ressource*=„myVM1“ > 70 %

![Warnungsregel für eine einzelne Zeitreihe](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Warnungsregel für mehrere Zeitreihen
Eine Warnungsregel überwacht mehrere Zeitreihen, wenn mindestens eines der folgenden Features verwendet wird: 
-   Mehrere Ressourcen
-   Mehrere Bedingungen 
-   Mehrere Dimensionen


## <a name="multiple-resources-multi-resource"></a>Mehrere Ressourcen

Eine einzelne Metrikwarnungsregel kann mehrere Ressourcen unter der Voraussetzung überwachen, dass die Ressourcen vom gleichen Typ sind und sich in der gleichen Azure-Region befinden. Durch Verwendung dieses Regeltyps werden die Komplexität und die Gesamtanzahl der zu verwaltenden Warnungsregeln reduziert. 

Hier ein Beispiel für eine solche Warnungsregel:
-   Zielressource: *myVM1, myVM2*
-   Metrik: *CPU-Prozentsatz*
-   Operator: *Größer als*
-   Schwellenwert: *70*

Für diese Warnungsregel werden zwei metrische Zeitreihen separat überwacht:
-   CPU-Prozentsatz, wobei *Ressource*=„myVM1“ > 70 %
-   CPU-Prozentsatz, wobei *Ressource*=„myVM2“ > 70 %

![Warnungsregel für mehrere Ressourcen](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
In einer Warnungsregel für mehrere Ressourcen wird die Bedingung für jede der Ressourcen **separat** ausgewertet (oder genauer gesagt, für jede metrische Zeitreihe, die einer einzelnen Ressource entspricht). Das bedeutet, dass Warnungen auch für jede Ressource separat ausgelöst werden.

Angenommen, die oben gezeigte Warnungsregel wird auf die Überwachung der CPU auf Werte von über 70 % im ausgewerteten Zeitraum (d. h. in den letzten fünf Minuten) festgelegt:
-   Der Wert für *CPU-Prozentsatz* von *myVM1* ist größer als 70 %. 
-   Der Wert für *CPU-Prozentsatz* von *myVM2* beträgt 50 %.

Die Warnungsregel wird für *myVM1* ausgelöst, aber nicht für *myVM2*. Diese ausgelösten Warnungen sind unabhängig voneinander. Sie können auch je nach individuellem Verhalten der einzelnen virtuellen Computer zu unterschiedlichen Zeiten aufgehoben werden.

Weitere Informationen zu Warnungsregeln für mehrere Ressourcen und den für diese Funktion unterstützten Ressourcentypen finden Sie unter [Bedarfsorientierte Überwachung mithilfe von Metrikwarnungen in Azure Monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> In einer Metrikwarnungsregel, mit der mehrere Ressourcen überwacht werden, ist nur eine einzige Bedingung zulässig.

## <a name="multiple-conditions-multi-condition"></a>Mehrere Bedingungen

Eine einzelne Metrikwarnungsregel kann auch bis zu fünf Bedingungen pro Warnungsregel überwachen. 

Beispiel:

- Zielressource: *myVM1*
- Bedingung1
  - Metrik: *CPU-Prozentsatz*
  - Operator: *Größer als*
  - Schwellenwert: *70*
- Bedingung2
  - Metrik: *Eingehender Netzwerkdatenverkehr gesamt*
  - Operator: *Größer als*
  - Schwellenwert: *20 MB*

Für diese Warnungsregel werden zwei metrische Zeitreihen überwacht:

- CPU-Prozentsatz, wobei *Ressource*=„myVM1“ > 70 %
- Eingehender Netzwerkdatenverkehr gesamt, wobei *Ressource*=„myVM1“ > 20 MB

![Warnungsregel für mehrere Bedingungen](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Zwischen den Bedingungen wird ein UND-Operator verwendet. Die Warnungsregel löst eine Warnung aus, wenn **alle** Bedingungen erfüllt sind. Die ausgelöste Warnung wird aufgehoben, wenn mindestens eine der Bedingungen nicht mehr erfüllt ist. 

> [!NOTE]
> Es gibt Einschränkungen bei der Verwendung von Dimensionen in einer Warnungsregel mit mehreren Bedingungen. Weitere Informationen dazu finden Sie unter [Einschränkungen bei der Verwendung von Dimensionen in einer Metrikwarnungsregel mit mehreren Bedingungen](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Mehrere Dimensionen

Eine einzelne Metrikwarnungsregel kann auch mehrere Dimensionswerte einer Metrik überwachen. Die Dimensionen einer Metrik sind Name-Wert-Paare, die zusätzliche Daten zum Beschreiben des Metrikwerts enthalten. Beispielsweise weist die Metrik **Transaktionen** eines Speicherkontos eine Dimension namens **API-Name** auf, die den Namen der von jeder Transaktion aufgerufenen API angibt (z. B. GetBlob, DeleteBlob, PutPage). Die Verwendung von Dimensionen ist optional, ermöglicht aber das Filtern der Metrik sowie das ausschließliche Überwachen bestimmter Zeitreihen, anstatt die Metrik als Aggregat aller Dimensionswerte insgesamt zu überwachen. 

Sie können beispielsweise festlegen, dass eine Warnung dann ausgelöst werden soll, wenn die Anzahl der Transaktionen für alle API-Namen insgesamt hoch ist (d. h. die aggregierten Daten), oder diese aufzuschlüsseln und nur dann eine Warnung auszulösen, wenn die Anzahl der Transaktionen für bestimmte API-Namen hoch ist. 

Hier ein Beispiel einer Warnungsregel zur Überwachung mehrerer Dimensionen:

- Zielressource: *myStorage1*
- Metrik: *Transaktionen*
- Dimensionen
  * API-Name = *GetBlob, DeleteBlob, PutPage*
- Operator: *Größer als*
- Schwellenwert: *70*

Für diese Warnungsregel werden drei metrische Zeitreihen überwacht:

- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„GetBlob“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„DeleteBlob“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„PutPage“ > 70

![Warnungsregel für mehrere Dimensionen mit Werten aus einer Dimension](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Eine Metrikwarnungsregel für mehrere Dimensionen kann auch mehrere Dimensionswerte aus **verschiedenen** Dimensionen einer Metrik überwachen. In diesem Fall werden von der Warnungsregel alle Kombinationen der ausgewählten Dimensionswerte **separat** überwacht.

Hier ein Beispiel für diesen Warnungsregeltyp:

- Zielressource: *myStorage1*
- Metrik: *Transaktionen*
- Dimensionen
  * API-Name = *GetBlob, DeleteBlob, PutPage*
  * Authentifizierung = *SAS, AccountKey*
- Operator: *Größer als*
- Schwellenwert: *70*

Für diese Warnungsregel werden sechs metrische Zeitreihen separat überwacht:

- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„GetBlob“ und *Authentifizierung*=„SAS“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„GetBlob“ und *Authentifizierung*=„AccountKey“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„DeleteBlob“ und *Authentifizierung*=„SAS“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„DeleteBlob“ und *Authentifizierung*=„AccountKey“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„PutPage“ und *Authentifizierung*=„SAS“ > 70
- Transaktionen, wobei *Ressource*=„myStorage1“ und *API-Name*=„PutPage“ und *Authentifizierung*=„AccountKey“ > 70

![Warnungsregel für mehrere Dimensionen mit Werten aus mehreren Dimensionen](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Erweiterte Funktionen für mehrere Dimensionen

1.  **Auswählen aller aktuellen und zukünftigen Dimensionen**: Sie können die Überwachung aller möglichen Werte einer Dimension auswählen, einschließlich zukünftiger Werte. Eine solche Warnungsregel wird automatisch skaliert, um alle Werte der Dimension zu überwachen, ohne dass Sie die Warnungsregel jedes Mal ändern müssen, wenn ein Dimensionswert hinzugefügt oder entfernt wird.
2.  **Ausschließen von Dimensionen**: Wenn Sie den Operator zum Ausschließen (≠) für einen Dimensionswert auswählen, entspricht dies der Auswahl aller anderen Werte dieser Dimension, einschließlich zukünftiger Werte.
3.  **Neue und benutzerdefinierte Dimensionen**: Die im Azure-Portal angezeigten Dimensionswerte basieren auf Metrikdaten, die in den letzten drei Tagen gesammelt wurden. Wenn der gesuchte Dimensionswert noch nicht ausgegeben wird, können Sie einen benutzerdefinierten Dimensionswert hinzuzufügen.
4. **Vergleichen von Dimensionen mit einem Präfix**: Sie können alle Dimensionswerte überwachen, die mit einem bestimmten Muster beginnen, indem Sie den Operator „Starts with“ (Beginnt mit) auswählen und ein benutzerdefiniertes Präfix eingeben.

![Erweiterte Funktionen für mehrere Dimensionen](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Preise für Metrikwarnungen

Die Preise für Metrikwarnungsregeln finden Sie auf der [Preisübersichtsseite für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Beim Erstellen einer Metrikwarnungsregel basiert die angegebene Preisschätzung auf den ausgewählten Funktionen und der Anzahl der überwachten Zeitreihen, die anhand der Regelkonfiguration und der aktuellen Metrikwerte ermittelt werden. Die monatlichen Kosten basieren jedoch auf den tatsächlichen Auswertungen der Zeitreihen und können daher von der ursprünglichen Schätzung abweichen, wenn bei einige Zeitreihen keine Daten auszuwerten sind oder wenn die Warnungsregel Funktionen verwendet, durch die eine dynamische Skalierung erfolgt.

Für eine Warnungsregel kann z. B. eine hohe Preisschätzung angegeben sein, wenn sie die Funktion für mehrere Dimensionen nutzt und eine große Anzahl von Kombinationen für Dimensionswerte ausgewählt ist, was zur Überwachung vieler Zeitreihen führt. Die tatsächlichen Kosten für diese Warnungsregel können jedoch niedriger sein, wenn nicht bei allen Zeitreihen, die sich aus den Kombinationen von Dimensionswerten ergeben, tatsächlich Daten auszuwerten sind.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Anzahl der von einer einzelnen Warnungsregel überwachten Zeitreihen

Jede Warnungsregel kann standardmäßig bis zu 5.000 Zeitreihen überwachen, damit überschüssige Kosten vermieden werden. Wenn Sie dieses Limit für Ihr Abonnement aufheben möchten, können Sie ein Supportticket eröffnen.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die bedarfsorientierte Überwachung mithilfe von Metrikwarnungen und [dynamischen Schwellenwerten](alerts-dynamic-thresholds.md).
