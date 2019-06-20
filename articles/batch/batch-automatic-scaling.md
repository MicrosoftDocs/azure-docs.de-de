---
title: Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool | Microsoft Azure
description: Aktivieren Sie das automatische Skalieren in einem Cloudpool, um die Anzahl von Computeknoten im Pool dynamisch anzupassen.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: c624cdfc-c5f2-4d13-a7d7-ae080833b779
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: multiple
ms.date: 06/20/2017
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdc2cd8f2218d50aa49d6b4eab2800eb6c92d9c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118110"
---
# <a name="create-an-automatic-scaling-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Erstellen einer Formel für die automatische Skalierung von Computeknoten in einem Batch-Pool

Azure Batch kann Pools basierend auf von Ihnen definierten Parametern automatisch skalieren. Bei der automatischen Skalierung fügt Batch Knoten dynamisch zu einem Pool hinzu, wenn der Aufgabenbedarf steigt, und entfernt Computeknoten bei einem Rückgang des Bedarfs. Sie können sowohl Zeit als auch Geld sparen, indem Sie automatisch die von Ihrer Batch-Anwendung verwendete Anzahl von Computeknoten anpassen. 

Sie können die automatische Skalierung für einen Pool von Computeknoten aktivieren, indem Sie ihn einer von Ihnen definierten *Formel für die automatische Skalierung* zuordnen. Anhand der Formel für die automatische Skalierung ermittelt der Batch-Dienst dann die Anzahl von Computeknoten, die zum Ausführen Ihrer Workload erforderlich sind. Computeknoten können dedizierte Knoten oder [Knoten mit niedriger Priorität](batch-low-pri-vms.md) sein. Batch reagiert auf Dienstmetrikdaten, die in regelmäßigen Abständen gesammelt werden. Anhand dieser Metrikdaten wird die Anzahl von Computeknoten im Pool basierend auf Ihrer Formel und einem konfigurierbaren Intervall von Batch angepasst.

Sie können die automatische Skalierung beim Erstellen eines Pools oder für einen bereits vorhandenen Pool aktivieren. Sie können auch eine vorhandene Formel für einen Pool ändern, für den die automatische Skalierung aktiviert ist. Batch gibt Ihnen die Möglichkeit, Formeln vor dem Zuweisen zu Pools auszuwerten und den Status automatischer Skalierungsausführungen zu überwachen.

In diesem Artikel werden die verschiedenen Entitäten erläutert, aus denen sich Ihre Formeln für die automatische Skalierung zusammensetzen. Dazu zählen Variablen, Operatoren, Operationen und Funktionen. Es wird erläutert, wie verschiedene Metriken zu Compute-Ressourcen und Aufgaben in Batch abgerufen werden können. Sie können diese Metriken verwenden, um die Knotenanzahl des Pools basierend auf Ressourcenverbrauch und Aufgabenstatus automatisch anzupassen. Anschließend wird beschrieben, wie Sie mit der REST-API und der .NET-API von Batch eine Formel erstellen und die automatische Skalierung für einen Pool aktivieren. Zum Abschluss werden nun verschiedene Beispielformeln gezeigt.

> [!IMPORTANT]
> Wenn Sie ein Batch-Konto erstellen, können Sie die [Kontokonfiguration](batch-api-basics.md#account) angeben, die bestimmt, ob Pools in einem Batch-Dienstabonnement (Standard) oder in Ihrem Benutzerabonnement zugeordnet werden. Wenn Sie Ihr Batch-Konto mit der Batch-Standarddienstkonfiguration erstellt haben, ist Ihr Konto auf eine maximale Anzahl von Kernen beschränkt, die für die Verarbeitung verwendet werden können. Der Batch-Dienst skaliert Computeknoten nur bis zu diesem Kernspeichergrenzwert. Aus diesem Grund erreicht der Batch-Dienst möglicherweise nicht die Zielanzahl der von einer Formel für die automatische Skalierung angegebenen Computeknoten. Unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md) finden Sie Informationen zum Anzeigen und Erhöhen Ihrer Kontokontingente.
>
>Wenn Sie Ihr Konto mit der Konfiguration „Benutzerabonnement“ erstellt haben, teilt Ihr Konto das Kernkontingent für das Abonnement. Weitere Informationen finden Sie unter [Virtual Machines-Grenzwerte](../azure-subscription-service-limits.md#virtual-machines-limits) in [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-subscription-service-limits.md).
>
>

## <a name="automatic-scaling-formulas"></a>Formeln für die automatische Skalierung
Eine Formel für die automatische Skalierung ist ein von Ihnen definierter Zeichenfolgenwert, der mindestens eine Anweisung enthält. Die Formel für die automatische Skalierung wird dem Element [autoScaleFormula][rest_autoscaleformula] (Batch REST) oder der Eigenschaft [CloudPool.AutoScaleFormula][net_cloudpool_autoscaleformula] (Batch .NET) eines Pools zugewiesen. Der Batch-Dienst verwendet Ihre Formel, um für das nächste Verarbeitungsintervall die Zielanzahl der Computeknoten im Pool zu ermitteln. Die Formelzeichenfolge darf 8 KB nicht überschreiten und kann bis zu 100 durch Semikolons getrennte Anweisungen sowie Zeilenumbrüche und Kommentare enthalten.

Sie können sich Formeln für die automatische Skalierung als eine „Batch-Sprache“ für die automatische Skalierung vorstellen. Formelanweisungen sind frei definierte Ausdrücke, die sowohl dienstdefinierte Variablen (vom Batch-Dienst definiert) als auch benutzerdefinierte Variablen (von Ihnen selbst definiert) enthalten können. Mithilfe von mitgelieferten Typen, Operatoren und Funktionen können sie für diese Werte eine Vielzahl von Operationen ausführen. Eine Anweisung kann beispielsweise wie folgt aussehen:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formeln enthalten im Allgemeinen mehrere Anweisungen, die Operationen für Werte durchführen, die in vorherigen Anweisungen abgerufen wurden. So rufen wir beispielsweise zuerst einen Wert für `variable1` ab und übergeben ihn dann an eine Funktion, um `variable2` aufzufüllen:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Fügen Sie diese Anweisungen zu Ihrer Formel für die automatische Skalierung hinzu, um eine Zielanzahl von Computeknoten zu erreichen. Dedizierte Knoten und Knoten mit niedriger Priorität verfügen über eigene Zieleinstellungen, damit Sie für jeden Knotentyp ein Ziel definieren können. Eine Formel für die automatische Skalierung kann einen Zielwert für dedizierte Knoten, einen Zielwert für Knoten mit niedriger Priorität oder beides enthalten.

Die Zielanzahl der Knoten ist möglicherweise höher oder niedriger als die aktuelle Anzahl der Knoten dieses Typs im Pool, oder sie ist mit der Anzahl identisch. Batch wertet die Formel für die automatische Skalierung des Pools in einem bestimmten Intervall aus (siehe [Intervalle für die automatische Skalierung](#automatic-scaling-interval)). Batch passt die Zielanzahl von den einzelnen Knotentypen im Pool an die Anzahl an, die durch die Formel für die automatische Skalierung zum Zeitpunkt der Auswertung angegeben wird.

### <a name="sample-autoscale-formula"></a>Beispielformel für die automatische Skalierung

Hier sehen Sie ein Beispiel für eine Formel für die automatische Skalierung, die für die meisten Szenarien angepasst werden kann. Die Variablen `startingNumberOfVMs` und `maxNumberofVMs` in der Beispielformel können an Ihre Anforderungen angepasst werden. Diese Formel skaliert dedizierte Knoten. Sie kann jedoch so geändert werden, dass sie auch Knoten mit niedriger Priorität skaliert. 

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

Mit dieser Formel für die automatische Skalierung wird der Pool zunächst mit einem einzigen virtuellen Computer erstellt. Die Metrik `$PendingTasks` definiert die Anzahl der Aufgaben, die ausgeführt werden oder sich in einer Warteschlange befinden. Die Formel sucht nach der durchschnittlichen Anzahl ausstehender Aufgaben in den letzten 180 Sekunden und legt die Variable `$TargetDedicatedNodes` entsprechend fest. Die Formel stellt sicher, dass die Zielanzahl der dedizierten Knoten niemals 25 virtuelle Computer überschreitet. Wenn neue Aufgaben gesendet werden, wächst der Pool automatisch an. Werden Aufgaben abgeschlossen, werden VMs nacheinander frei, und der Pool wird durch die Formel für die automatische Skalierung verkleinert.

## <a name="variables"></a>Variables
Sie können in Ihrer Formel für die automatische Skalierung sowohl **dienstdefinierte** als auch **benutzerdefinierte** Variablen verwenden. Vom Dienst definierte Variablen sind in den Batch-Dienst integriert. Einige der vom Dienst definierten Variablen verfügen über Lese-/Schreibzugriff, und einige sind schreibgeschützt. Benutzerdefinierte Variablen sind Variablen, die Sie definieren. In der im vorherigen Abschnitt gezeigten Beispielformel sind `$TargetDedicatedNodes` und `$PendingTasks` vom Dienst definierte Variablen. Die Variablen `startingNumberOfVMs` und `maxNumberofVMs` sind benutzerdefinierte Variablen.

> [!NOTE]
> Vom Dienst definierten Variablen wird immer ein Dollarzeichen ($) vorangestellt. Für benutzerdefinierte Variablen ist das Dollarzeichen optional.
>
>

Die folgenden Tabellen enthalten sowohl Variablen mit Lese-/Schreibzugriff als auch schreibgeschützte Variablen, die vom Batch-Dienst definiert werden.

Sie können diese vom Dienst definierten Variablen abrufen und festlegen, um die Anzahl der Computeknoten in einem Pool zu verwalten:

| Vom Dienst definierte Variablen mit Lese-/Schreibzugriff | BESCHREIBUNG |
| --- | --- |
| $TargetDedicatedNodes |Die Zielanzahl dedizierter Computeknoten für den Pool. Die Anzahl der dedizierten Knoten wird als Ziel angegeben, da ein Pool möglicherweise nicht immer die gewünschte Anzahl von Knoten erreicht. Wenn die Zielanzahl dedizierter Knoten beispielsweise durch eine Auswertung der automatischen Skalierung geändert wird, bevor der Pool das ursprüngliche Ziel erreicht hat, erreicht der Pool möglicherweise nicht die Zielanzahl. <br /><br /> Ein Pool in einem Konto, der mit der Konfiguration „Batch-Dienst“ erstellt wurde, erreicht möglicherweise nicht sein Ziel, wenn das Ziel ein Batch-Kontoknoten- oder -Kernkontingent überschreitet. Ein Pool in einem Konto, der mit der Konfiguration „Benutzerabonnement“ erstellt wurde, erreicht möglicherweise nicht sein Ziel, wenn das Ziel das freigegebene Kernkontingent für das Abonnement überschreitet.|
| $TargetLowPriorityNodes |Die Zielanzahl von Computeknoten mit niedriger Priorität für den Pool. Die Anzahl der Knoten mit niedriger Priorität wird als Ziel angegeben, da ein Pool möglicherweise nicht immer die gewünschte Anzahl von Knoten erreichen kann. Wenn die Zielanzahl von Knoten mit niedriger Priorität beispielsweise durch eine Auswertung der automatischen Skalierung geändert wird, bevor der Pool das ursprüngliche Ziel erreicht hat, kann der Pool möglicherweise nicht die Zielanzahl erreichen. Ein Pool kann sein Ziel möglicherweise auch dann nicht erreichen, wenn das Ziel ein Batch-Kontoknoten- oder -Kernkontingent überschreitet. <br /><br /> Weitere Informationen zu Computeknoten mit niedriger Priorität finden Sie unter [Verwenden von VMs mit niedriger Priorität mit Batch (Vorschau)](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Dieser Vorgang wird ausgeführt, wenn Computeknoten aus einem Pool entfernt werden. Mögliche Werte:<ul><li>**requeue**: Aufgaben sofort beenden und wieder in die Auftragswarteschlange einfügen, damit sie neu geplant werden können.<li>**terminate**: Aufgaben sofort beenden und aus der Auftragswarteschlange entfernen.<li>**taskcompletion**: Auf derzeit ausgeführte Aufgaben warten und den Knoten dann aus dem Pool entfernen.<li>**retaineddata**: Warten, bis alle lokal vorgehaltenen Aufgabendaten des Pools gelöscht wurden, bevor der Knoten aus dem Pool entfernt wird.</ul> |

Sie können den Wert dieser vom Dienst definierten Variablen abrufen, um Anpassungen basierend auf den Metriken des Batch-Diensts vorzunehmen:

| Vom Dienst definierte schreibgeschützte Variablen | BESCHREIBUNG |
| --- | --- |
| $CPUPercent |Die durchschnittliche prozentuale CPU-Auslastung |
| $WallClockSeconds |Die Anzahl der verbrauchten Sekunden |
| $MemoryBytes |Die durchschnittliche Anzahl genutzter Megabyte |
| $DiskBytes |Die durchschnittliche Anzahl der auf den lokalen Datenträgern genutzten Gigabyte |
| $DiskReadBytes |Die Anzahl der gelesenen Byte |
| $DiskWriteBytes |Die Anzahl der geschriebenen Byte |
| $DiskReadOps |Die Anzahl der ausgeführten Datenträger-Lesevorgänge |
| $DiskWriteOps |Die Anzahl der ausgeführten Datenträger-Schreibvorgänge |
| $NetworkInBytes |Die Anzahl der eingehenden Byte |
| $NetworkOutBytes |Die Anzahl der ausgehenden Byte |
| $SampleNodeCount |Die Anzahl der Computeknoten |
| $ActiveTasks |Die Anzahl der Aufgaben, die zur Ausführung bereit sind, aber noch nicht ausgeführt werden. Die Anzahl der $ActiveTasks umfasst alle Aufgaben, die sich im aktiven Zustand befinden und deren Abhängigkeiten erfüllt wurden. Alle Aufgaben, die sich nicht im aktiven Zustand befinden, deren Abhängigkeiten aber nicht erfüllt wurden, werden aus der Anzahl der $ActiveTasks ausgeschlossen.|
| $RunningTasks |Die Anzahl der Aufgaben, die sich in einem Ausführungszustand befinden |
| $PendingTasks |Die Summe aus $ActiveTasks und $RunningTasks. |
| $SucceededTasks |Die Anzahl der Aufgaben, die erfolgreich abgeschlossen wurden |
| $FailedTasks |Die Anzahl der Aufgaben, bei denen Fehler aufgetreten sind |
| $CurrentDedicatedNodes |Die aktuelle Anzahl der zugewiesenen Computeknoten |
| $CurrentLowPriorityNodes |Die aktuelle Anzahl der Computeknoten mit niedriger Priorität einschließlich aller Knoten, die vorzeitig entfernt wurden. |
| $PreemptedNodeCount | Die Anzahl der Knoten im Pool, die sich im Zustand „Vorzeitig entfernt“ befinden. |

> [!TIP]
> Die oben in der Tabelle angezeigten, vom Dienst definierten schreibgeschützten Variablen sind *Objekte*, die verschiedene Methoden für den Zugriff auf die zum jeweiligen Objekt gehörigen Daten bereitstellen. Weitere Informationen finden Sie weiter unten in diesem Artikel unter [Abrufen von Beispieldaten](#getsampledata).
>
>

## <a name="types"></a>Typen
Folgende Typen werden in einer Formel unterstützt:

* double
* doubleVec
* doubleVecList
* Zeichenfolge
* timestamp – „timestamp“ ist eine Verbundstruktur, die folgende Member enthält:

  * year
  * Monat (1-12)
  * Tag (1-31)
  * Wochentag (im Zahlenformat; Beispiel: 1 für Montag)
  * Stunde (im 24-Stunden-Zahlenformat; Beispiel: 13 steht für 13 Uhr)
  * Minute (00-59)
  * Sekunde (00-59)
* timeInterval

  * TimeInterval_Zero
  * TimeInterval_100ns
  * TimeInterval_Microsecond
  * TimeInterval_Millisecond
  * TimeInterval_Second
  * TimeInterval_Minute
  * TimeInterval_Hour
  * TimeInterval_Day
  * TimeInterval_Week
  * TimeInterval_Year

## <a name="operations"></a>Vorgänge
Für die im vorherigen Abschnitt aufgeführten Typen sind folgende Vorgänge zulässig.

| Vorgang | Unterstützte Operatoren | Ergebnistyp |
| --- | --- | --- |
| double *Operator* double |+, -, *, / |double |
| double *Operator* timeinterval |* |timeInterval |
| doubleVec *Operator* double |+, -, *, / |doubleVec |
| doubleVec *Operator* doubleVec |+, -, *, / |doubleVec |
| timeinterval *Operator* double |*, / |timeInterval |
| timeinterval *Operator* timeinterval |+, - |timeInterval |
| timeinterval *Operator* timestamp |+ |timestamp |
| timestamp *Operator* timeinterval |+ |timestamp |
| timestamp *Operator* timestamp |- |timeInterval |
| *Operator*double |-, ! |double |
| *Operator*timeInterval |- |timeInterval |
| double *Operator* double |<, <=, ==, >=, >, != |double |
| string *Operator* string |<, <=, ==, >=, >, != |double |
| timestamp *Operator* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *Operator* timeinterval |<, <=, ==, >=, >, != |double |
| double *Operator* double |&&, &#124;&#124; |double |

Beim Testen von „double“ mit einem ternären Operator (`double ? statement1 : statement2`) sind Werte ungleich Null **true** und Nullwerte **false**.

## <a name="functions"></a>Functions
Zum Definieren einer Formel für die automatische Skalierung stehen folgende vordefinierte **Funktionen** zur Verfügung.

| Funktion | Rückgabetyp | BESCHREIBUNG |
| --- | --- | --- |
| avg(doubleVecList) |double |Der Durchschnittswert aller Werte in der doubleVecList wird zurückgegeben. |
| len(doubleVecList) |double |Die Länge des Vektors, der aus der doubleVecList erstellt wurde, wird zurückgegeben. |
| lg(double) |double |Gibt für den double-Wert den Logarithmus zur Basis 2 zurück. |
| lg(doubleVecList) |doubleVec |Gibt für die doubleVecList den komponentenbezogenen Logarithmus zur Basis 2 zurück. Ein vec(double) muss explizit für den Parameter übergeben werden. Andernfalls wird von der double lg(double)-Version ausgegangen. |
| ln(double) |double |Gibt für den double-Wert den natürlichen Logarithmus zurück. |
| ln(doubleVecList) |doubleVec |Gibt für die doubleVecList den komponentenbezogenen Logarithmus zur Basis 2 zurück. Ein vec(double) muss explizit für den Parameter übergeben werden. Andernfalls wird von der double lg(double)-Version ausgegangen. |
| log(double) |double |Gibt für den double-Wert den Logarithmus zur Basis 10 zurück. |
| log(doubleVecList) |doubleVec |Gibt für die doubleVecList den komponentenbezogenen Logarithmus zur Basis 10 zurück. Ein vec(double) muss explizit für den einzelnen double-Parameter übergeben werden. Andernfalls wird von der double log (double)-Version ausgegangen. |
| max(doubleVecList) |double |Der maximale Wert in der doubleVecList wird zurückgegeben. |
| min(doubleVecList) |double |Der minimale Wert in der doubleVecList wird zurückgegeben. |
| norm(doubleVecList) |double |Die Zweiernorm des Vektors, der aus der doubleVecList erstellt wurde, wird zurückgegeben. |
| percentile(doubleVec v, double p) |double |Das Perzentil-Element des Vektors v wird zurückgegeben. |
| rand() |double |Ein Zufallswert zwischen 0,0 und 1,0 wird zurückgegeben. |
| range(doubleVecList) |double |Der Unterschied zwischen dem Minimal- und Maximalwert in doubleVecList wird zurückgegeben. |
| std(doubleVecList) |double |Die Stichproben-Standardabweichung der Werte in der doubleVecList wird zurückgegeben. |
| stop() | |Beendet die Auswertung des Ausdrucks für die automatische Skalierung. |
| sum(doubleVecList) |double |Die Summe aller Komponenten von doubleVecList wird zurückgegeben. |
| time(string dateTime="") |timestamp |Es werden entweder der Zeitstempel der aktuellen Zeit zurückgegeben, wenn keine Parameter übergeben werden, oder andernfalls der Zeitstempel der DateTime-Zeichenfolge, wenn diese übergeben wird. Unterstützte DateTime-Formate sind W3C-DTF und RFC 1123. |
| val(doubleVec v, double i) |double |Der Wert des Elements an Position i im Vektor v mit einem Anfangsindex von 0 wird zurückgegeben. |

Einige der in der vorherigen Tabelle beschriebenen Funktionen akzeptieren eine Liste als Argument. Bei der durch Trennzeichen getrennten Liste handelt es sich um eine beliebige Kombination aus *double* und *doubleVec*. Beispiel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Der *doubleVecList*-Wert wird vor der Auswertung in einen einzelnen *doubleVec* konvertiert. Zum Beispiel hat bei `v = [1,2,3]` das Aufrufen von `avg(v)` den gleichen Effekt wie das Aufrufen von `avg(1,2,3)`. Das Aufrufen von `avg(v, 7)` entspricht dem Aufrufen von `avg(1,2,3,7)`.

## <a name="getsampledata"></a>Erfassen von Stichprobendaten
Die Formeln für die automatische Skalierung greifen auf Metrikdaten (Stichproben) zurück, die vom Batch-Dienst bereitgestellt werden. Eine Formel vergrößert oder verkleinert den Pool basierend auf den Werten, die sie vom Dienst erhält. Die obigen, vom Dienst definierten Variablen sind Objekte, die verschiedene Methoden für den Zugriff auf die zum jeweiligen Objekt gehörigen Daten bereitstellen. Der folgende Ausdruck zeigt z. B. eine Anforderung zum Abrufen der letzten fünf Minuten der CPU-Auslastung:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

| Methode | BESCHREIBUNG |
| --- | --- |
| GetSample() |Die `GetSample()`-Methode gibt einen Vektor aus Stichprobenwerten zurück.<br/><br/>Eine Stichprobe enthält Metrikdaten, die innerhalb von 30 Sekunden erfasst wurden. Dies bedeutet, dass alle 30 Sekunden eine Stichprobe genommen wird. Wie nachstehend erwähnt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichprobe und ihrer Verfügbarkeit für eine Formel. Daher stehen möglicherweise nicht alle Stichproben für einen bestimmten Zeitraum für die Bewertung durch eine Formel zur Verfügung.<ul><li>`doubleVec GetSample(double count)`<br/>Gibt die Anzahl von Stichproben an, die aus den letzten erfassten Stichproben abgerufen werden soll.<br/><br/>`GetSample(1)` gibt die neueste verfügbare Stichprobe zurück. Für Metriken wie `$CPUPercent`sollte diese Methode allerdings nicht verwendet werden, da unmöglich feststellbar ist, *wann* die Stichprobe erfasst wurde. Sie kann aktuell oder aber, aufgrund von Systemproblemen, auch wesentlich älter sein. In solchen Fällen ist es besser, wie unten gezeigt ein Zeitintervall zu verwenden.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`<br/>Gibt einen Zeitraum für die Erfassung von Stichprobendaten an. Optional gibt diese Methode auch den Prozentsatz der Stichproben an, die im angeforderten Zeitraum verfügbar sein müssen.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10)` gibt 20 Stichproben zurück, wenn alle Stichproben der letzten 10 Minuten im CPUPercent-Verlauf vorhanden sind. Wenn jedoch die letzte Minute des Verlaufs nicht verfügbar ist, werden nur 18 Stichproben zurückgegeben. In diesem Fall:<br/><br/>Für `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` tritt ein Fehler auf, da nur 90 Prozent der Stichproben verfügbar sind.<br/><br/>`$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` wird erfolgreich durchgeführt.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`<br/>Gibt einen Zeitrahmen für die Datenerfassung mit einer Start- und einer Endzeit an.<br/><br/>Wie bereits erwähnt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichprobe und ihrer Verfügbarkeit für eine Formel. Berücksichtigen Sie diese Verzögerung, wenn Sie die Methode `GetSample` verwenden. Weitere Informationen finden Sie bei `GetSamplePercent` weiter unten. |
| GetSamplePeriod() |Gibt den Zeitraum zurück, in dem die Stichproben aus einem alten Stichproben-Dataset gesammelt wurden. |
| Count() |Liefert die Gesamtzahl der Stichprobenwerte im Metrikverlauf zurück. |
| HistoryBeginTime() |Gibt den Zeitstempel des ältesten verfügbaren Stichprobenwerts für die Metrik zurück. |
| GetSamplePercent() |Gibt den Prozentsatz an Stichprobenwerten zurück, die für ein bestimmtes Intervall verfügbar sind. Beispiel:<br/><br/>`doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`<br/><br/>Da die `GetSample`-Methode einen Fehler erzeugt, wenn der Prozentsatz der zurückgegebenen Stichproben kleiner als der angegebene `samplePercent`-Wert ist, können Sie vorab mithilfe der `GetSamplePercent`-Methode eine Prüfung vornehmen. Wenn nicht genügend Beispiele vorhanden sind, können Sie anschließend eine andere Aktion ausführen, , ohne die Auswertung der automatischen Skalierung zu unterbrechen. |

### <a name="samples-sample-percentage-and-the-getsample-method"></a>Stichproben, Prozentsatz für die Stichprobe und die *GetSample()* -Methode
Das Abrufen von Metrikdaten zu Aufgaben und Ressourcen sowie das anschließende Anpassen der Poolgröße basierend auf diesen Daten ist die Kernfunktion einer Formel für die automatische Skalierung. Daher ist es wichtig, sich damit vertraut zu machen, wie Formeln für die automatische Skalierung mit Metrikdaten (Stichproben) interagieren.

**Beispiele**

Der Batch-Dienst nimmt regelmäßig Stichproben von Task- und Ressourcenmetriken und stellt sie Ihren Formeln für die automatische Skalierung zur Verfügung. Diese Stichproben werden alle 30 Sekunden vom Batch-Dienst aufgezeichnet. Allerdings kommt es meist zu einer Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichproben und dem Zeitpunkt, zu dem sie Ihren Formeln für die automatische Skalierung zur Verfügung gestellt (und von diesen gelesen) werden können. Darüber hinaus werden Stichproben gelegentlich aufgrund verschiedener Faktoren wie Netzwerk- oder anderer Infrastrukturprobleme für ein bestimmtes Intervall nicht erfasst.

**Prozentsatz für die Stichprobe**

Beim Übergeben eines `samplePercent`-Werts an die `GetSample()`-Methode oder Aufrufen der `GetSamplePercent()`-Methode bezieht sich _Percent_ auf einen Vergleich zwischen der möglichen Gesamtzahl der vom Batch-Dienst erfassten Stichproben und der Anzahl von Stichproben, die für Ihre Formel für die automatische Skalierung verfügbar sind.

Lassen Sie uns als Beispiel eine Zeitspanne von 10 Minuten betrachten. Da Stichproben in einer Zeitspanne von 10 Minuten alle 30 Sekunden erfasst werden, werden maximal 20 Stichproben von Batch erfasst (zwei pro Minute). Doch aufgrund der erwähnten Latenz des Berichterstellungsmechanismus und anderer Probleme in Azure stehen Ihrer Formel für die automatische Skalierung möglicherweise nur 15 Stichproben zur Verfügung. Das bedeutet, dass in diesem zehnminütigen Zeitraum nur 75 % der Gesamtanzahl erfasster Stichproben für Ihre Formel verfügbar sind.

**GetSample() und Stichprobenbereiche**

Die Formeln für die automatische Skalierung werden Ihre Pools &mdash; vergrößern und verkleinern und Knoten hinzufügen oder entfernen. Da Knoten Geld Kosten, möchten Sie sicherstellen, dass Ihre Formeln eine sinnvolle Analysemethode verwenden, die auf ausreichend Daten basiert. Aus diesem Grund wird empfohlen, dass Sie eine Analyse des Typs Trend in Ihren Formeln verwenden. Dieser Typ vergrößert oder verkleinert Ihre Pools basierend auf einem Bereich gesammelter Stichproben.

Verwenden Sie hierzu `GetSample(interval look-back start, interval look-back end)`, um einen Vektor von Stichproben zurückzugeben:

```
$runningTasksSample = $RunningTasks.GetSample(1 * TimeInterval_Minute, 6 * TimeInterval_Minute);
```

Wenn die obige Zeile von Batch ausgewertet wird, gibt sie einen Bereich von Stichproben als Vektor von Werten zurück. Beispiel:

```
$runningTasksSample=[1,1,1,1,1,1,1,1,1,1];
```

Nachdem Sie den Vektor von Stichproben erfasst haben, können Sie Funktionen wie `min()`, `max()` und `avg()` verwenden, um aussagekräftige Werte aus dem erfassten Bereich abzuleiten.

Zur Erhöhung der Sicherheit können Sie einen Fehler bei der Formelauswertung erzwingen, wenn für einen bestimmten Zeitraum weniger als ein bestimmter Prozentsatz von Stichproben verfügbar ist. Mit dem Erzwingen eines Fehlers bei einer Formelauswertung weisen Sie Batch an, die weitere Auswertung der Formel zu beenden, sofern der angegebene Prozentsatz von Stichproben nicht zur Verfügung steht. An der Größe des Pools erfolgt dann keinerlei Änderung. Wenn Sie einen erforderlichen Prozentsatz von Stichproben angeben möchten, damit die Auswertung Erfolg hat, geben Sie diesen in `GetSample()` als dritten Parameter an. Hier wird ein Mindestprozentsatz von 75 % angegeben:

```
$runningTasksSample = $RunningTasks.GetSample(60 * TimeInterval_Second, 120 * TimeInterval_Second, 75);
```

Weil bei der Verfügbarkeit von Stichproben eine Verzögerung auftreten kann, müssen Sie stets einen Zeitbereich mit einer Startzeit wählen, die mehr als eine Minute zurückliegt. Es dauert ca. eine Minute, bis Stichproben das System durchlaufen haben, weshalb Stichproben im Bereich `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` möglicherweise nicht verfügbar sind. In diesem Fall können Sie den Prozentsatzparameter `GetSample()` angeben, um einen bestimmten Prozentsatz von Stichproben zu erzwingen.

> [!IMPORTANT]
> Es wird **dringend empfohlen**, sich **in den Formeln für die automatische Skalierung nicht *ausschließlich* auf `GetSample(1)` zu verlassen**. Der Grund ist, dass `GetSample(1)` im Wesentlichen den Batch-Dienst anweist, die letzte vorhandene Stichprobe unabhängig vom Zeitpunkt ihrer Erfassung bereitzustellen. Da es sich nur um ein Stichprobe handelt, die ggf. schon älter ist, ist diese möglicherweise nicht für den aktuellen Aufgaben- oder Ressourcenstatus repräsentativ. Stellen Sie bei Verwendung von `GetSample(1)` sicher, dass dieser Wert zu einer längeren Anweisung gehört und nicht der einzige Datenpunkt ist, auf dem Ihre Formel basiert.
>
>

## <a name="metrics"></a>metrics
Für das Definieren einer Formel können Sie sowohl Ressourcenmetriken als auch Aufgabenmetriken verwenden. Sie passen die vorgegebene Anzahl dedizierter Knoten im Pool basierend auf den Metrikdaten an, die Sie abrufen und auswerten. Im Abschnitt [Variablen](#variables) finden Sie weitere Informationen zu den einzelnen Metriken.

<table>
  <tr>
    <th>Metrik</th>
    <th>BESCHREIBUNG</th>
  </tr>
  <tr>
    <td><b>Ressource</b></td>
    <td><p>Ressourcenmetriken basieren auf der CPU-Auslastung, der Bandbreite und Speicherauslastung der Computeknoten sowie auf der Anzahl der Knoten.</p>
        <p> Folgende vom Dienst definierte Variablen eignen sich für Anpassungen auf der Grundlage der Knotenanzahl:</p>
    <p><ul>
            <li>$TargetDedicatedNodes</li>
            <li>$TargetLowPriorityNodes</li>
            <li>$CurrentDedicatedNodes</li>
            <li>$CurrentLowPriorityNodes</li>
            <li>$PreemptedNodeCount</li>
            <li>$SampleNodeCount</li>
    </ul></p>
    <p>Folgende vom Dienst definierte Variablen eignen sich für Anpassungen auf der Grundlage der Ressourcenverwendung von Knoten:</p>
    <p><ul>
      <li>$CPUPercent</li>
      <li>$WallClockSeconds</li>
      <li>$MemoryBytes</li>
      <li>$DiskBytes</li>
      <li>$DiskReadBytes</li>
      <li>$DiskWriteBytes</li>
      <li>$DiskReadOps</li>
      <li>$DiskWriteOps</li>
      <li>$NetworkInBytes</li>
      <li>$NetworkOutBytes</li></ul></p>
  </tr>
  <tr>
    <td><b>Aufgabe</b></td>
    <td><p>Aufgabenmetriken basieren auf dem Aufgabenstatus (z.B. „Aktiv“, „Ausstehend“ oder „Abgeschlossen“). Folgende vom Dienst definierte Variablen eignen sich für Poolgrößenanpassungen auf der Grundlage von Aufgabenmetriken:</p>
    <p><ul>
      <li>$ActiveTasks</li>
      <li>$RunningTasks</li>
      <li>$PendingTasks</li>
      <li>$SucceededTasks</li>
            <li>$FailedTasks</li></ul></p>
        </td>
  </tr>
</table>

## <a name="write-an-autoscale-formula"></a>Schreiben einer Formel für die automatische Skalierung
Sie können eine Formel für die automatische Skalierung erstellen, indem Sie mithilfe der oben aufgeführten Komponenten Anweisungen formulieren und diese dann zu einer vollständigen Formel kombinieren. In diesem Abschnitt erstellen wir eine Beispielformel für die automatische Skalierung, mit der einige praxistaugliche Skalierungsentscheidungen getroffen werden können.

Zuerst definieren wir die Anforderungen für die neue Formel für die automatische Skalierung. Mit der Formel soll Folgendes erreicht werden:

1. Die dedizierte Anzahl von Computeknoten in einem Pool soll erhöht werden, wenn die CPU-Auslastung hoch ist.
2. Die dedizierte Anzahl von Computeknoten in einem Pool soll reduziert werden, wenn die CPU-Auslastung gering ist.
3. Die maximale Anzahl dedizierter Knoten muss immer auf 400 beschränkt sein.

Zur Erhöhung der Knotenanzahl bei hoher CPU-Auslastung definieren wir eine Anweisung, die eine benutzerdefinierte Variable (`$totalDedicatedNodes`) mit einem Wert auffüllt, der 110 Prozent der aktuellen Zielanzahl dedizierte Knoten darstellt. Dies gilt aber nur, wenn die minimale durchschnittliche CPU-Auslastung in den vergangenen 10 Minuten über 70 Prozent lag. Verwenden Sie andernfalls den Wert für die aktuelle Anzahl der dedizierten Knoten.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Zum *Reduzieren* der Anzahl dedizierter Knoten während einer Phase mit geringer CPU-Auslastung legen wir mit der nächsten Anweisung in unserer Formel dieselbe Variable `$totalDedicatedNodes` auf 90 Prozent der aktuellen Zielanzahl dedizierter Knoten fest, wenn die durchschnittliche CPU-Auslastung in den letzten 60 Minuten unter 20 Prozent lag. Verwenden Sie andernfalls den aktuellen Wert von `$totalDedicatedNodes`, den wir oben in der Anweisung angegeben haben.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Beschränken Sie nun die vorgegebene Anzahl dedizierter Computeknoten auf maximal 400:

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Die vollständige Formel lautet:

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

## <a name="create-an-autoscale-enabled-pool-with-net"></a>Erstellen eines Pools mit aktivierter automatischer Skalierung in .NET

Gehen Sie wie folgt vor, um einen Pool mit automatischer Skalierung in .NET zu erstellen:

1. Erstellen Sie den Pool mit [BatchClient.PoolOperations.CreatePool](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
2. Legen Sie die [CloudPool.AutoScaleEnabled](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled)-Eigenschaft auf `true` fest.
3. Legen Sie die [CloudPool.AutoScaleFormula](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula)-Eigenschaft mit Ihrer Formel für die automatische Skalierung fest.
4. (Optional) Legen Sie die [CloudPool.AutoScaleEvaluationInterval](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval)-Eigenschaft fest (Standardeinstellung ist 15 Minuten).
5. Führen Sie für den Pool mit [CloudPool.Commit](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commit) oder [CommitAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) einen Commit durch.

Mit dem folgenden Codeausschnitt wird ein Pool mit aktivierter automatischer Skalierung in .NET erstellt. Die Formel für die automatische Skalierung des Pools legt die vorgegebene Anzahl dedizierter Knoten auf 5 am Montag und auf 1 an allen anderen Wochentagen fest. Das [Intervall für die automatische Skalierung](#automatic-scaling-interval) wird auf 30 Minuten festgelegt. In diesem und anderen C#-Codeausschnitten in diesem Artikel ist `myBatchClient` eine ordnungsgemäß initialisierte Instanz der Klasse [BatchClient][net_batchclient].

```csharp
CloudPool pool = myBatchClient.PoolOperations.CreatePool(
                    poolId: "mypool",
                    virtualMachineSize: "standard_d1_v2",
                    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));    
pool.AutoScaleEnabled = true;
pool.AutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";
pool.AutoScaleEvaluationInterval = TimeSpan.FromMinutes(30);
await pool.CommitAsync();
```

> [!IMPORTANT]
> Wenn Sie einen Pool mit aktivierter automatischer Skalierung erstellen, geben Sie beim Aufruf von **CreatePool** nicht den Parameter _targetDedicatedNodes_ oder den Parameter _targetLowPriorityNodes_ an. Geben Sie stattdessen die Eigenschaften **AutoScaleEnabled** und **AutoScaleFormula** im Pool an. Die Werte für diese Eigenschaften bestimmen die Zielanzahl der einzelnen Knotentypen. Zudem müssen Sie zur manuellen Anpassung der Größe eines Pools mit aktivierter automatischer Skalierung (etwa mit [BatchClient.PoolOperations.ResizePool][net_poolops_resizepoolasync]) zunächst die automatische Skalierung im Pool **deaktivieren**, um anschließend die Größe des Pools ändern zu können.
>
>

Neben Batch .NET können Sie auch die anderen [Batch-SDKs](batch-apis-tools.md#azure-accounts-for-batch-development), [Batch-REST](https://docs.microsoft.com/rest/api/batchservice/), [Batch PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md) und die [Batch-CLI](batch-cli-get-started.md) für die Konfiguration der automatischen Skalierung verwenden.


### <a name="automatic-scaling-interval"></a>Intervall für die automatische Skalierung
Standardmäßig passt der Batch-Dienst alle 15 Minuten die Poolgröße gemäß seiner Formel für die automatische Skalierung an. Dieses Intervall kann mithilfe der folgenden Pooleigenschaften konfiguriert werden:

* [CloudPool.AutoScaleEvaluationInterval][net_cloudpool_autoscaleevalinterval] (Batch .NET)
* [autoScaleEvaluationInterval][rest_autoscaleinterval] (REST-API)

Das kürzeste Intervall ist fünf Minuten, das längste 168 Stunden. Wenn ein Intervall außerhalb dieses Bereichs angegeben wird, gibt der Batch-Dienst einen Fehler des Typs „Unzulässige Anforderung (400)“ zurück.

> [!NOTE]
> Die automatische Skalierung ist derzeit nicht als Reaktion im Zeitraum unter einer Minute auf Änderungen vorgesehen, sondern dient eher zum allmählichen Anpassen der Größe Ihres Pools während der Ausführung Ihres Workloads.
>
>

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivieren der automatischen Skalierung für einen vorhandenen Pool

Jedes Batch SDK bietet eine Möglichkeit für die Aktivierung der automatischen Skalierung. Beispiel:

* [BatchClient.PoolOperations.EnableAutoScaleAsync][net_enableautoscaleasync] (Batch .NET)
* [Enable automatic scaling on a pool][rest_enableautoscale] (Aktivieren des automatischen Skalierens für einen Pool) (REST-API)

Beachten Sie folgende Punkte, wenn Sie die automatische Skalierung für einen vorhandenen Pool aktivieren:

* Falls die automatische Skalierung für den Pool derzeit deaktiviert ist, wenn Sie die Anforderung zur Aktivierung der automatischen Skalierung ausführen, müssen Sie beim Ausgeben der Anforderung eine gültige Formel für die automatische Skalierung angeben. Sie können optional ein Auswertungsintervall für die automatische Skalierung angeben. Wenn Sie kein Intervall angeben, wird der Standardwert „15 Minuten“ verwendet.
* Wenn die automatische Skalierung für den Pool derzeit aktiviert ist, können Sie eine Formel für die automatische Skalierung, ein Auswertungsintervall oder beides angeben. Sie müssen mindestens eine dieser Eigenschaften angeben.

  * Wenn Sie ein neues Auswertungsintervall für die automatische Skalierung angeben, wird der vorhandene Auswertungszeitplan beendet und ein neuer Zeitplan gestartet. Die Startzeit des neuen Zeitplans ist der Zeitpunkt, zu dem die Anforderung zur Aktivierung der automatischen Skalierung ausgegeben wurde.
  * Wenn Sie die autoscale-Formel oder das Auswertungsintervall weglassen, nutzt der Batch-Dienst weiterhin den aktuellen Wert dieser Einstellung.

> [!NOTE]
> Wenn Sie bei der Erstellung des Pools in .NET oder für vergleichbare Parameter in einer anderen Sprache Werte für den Parameter *targetDedicatedNodes* oder *targetLowPriorityNodes* der **CreatePool**-Methode angegeben haben, werden diese Werte bei der Auswertung der Formel für die automatische Skalierung ignoriert.
>
>

In diesem C#-Codeausschnitt wird die [Batch .NET][net_api]-Bibliothek verwendet, um die automatische Skalierung in einem vorhandenen Pool zu aktivieren:

```csharp
// Define the autoscaling formula. This formula sets the target number of nodes
// to 5 on Mondays, and 1 on every other day of the week
string myAutoScaleFormula = "$TargetDedicatedNodes = (time().weekday == 1 ? 5:1);";

// Set the autoscale formula on the existing pool
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myAutoScaleFormula);
```

### <a name="update-an-autoscale-formula"></a>Aktualisieren einer Formel für die automatische Skalierung

Zur Aktualisierung der Formel für einen vorhandenen Pool mit aktivierter automatischer Skalierung müssen Sie den Vorgang zur Aktivierung der automatischen Skalierung mit der neuen Formel erneut aufrufen. Wenn die automatische Skalierung für `myexistingpool` beispielsweise bereits aktiviert ist, wird die Formel für die automatische Skalierung beim Ausführen des folgenden .NET-Codes durch den Inhalt von `myNewFormula` ersetzt.

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleFormula: myNewFormula);
```

### <a name="update-the-autoscale-interval"></a>Aktualisieren des Intervalls für die automatische Skalierung

Zur Aktualisierung des Auswertungsintervalls der automatischen Skalierung für einen vorhandenen Pool mit aktivierter automatischer Skalierung müssen Sie den Vorgang zur Aktivierung der automatischen Skalierung mit dem neuen Intervall erneut aufrufen. Gehen Sie beispielsweise wie folgt vor, um das Auswertungsintervall für die automatische Skalierung für einen Pool, der in .NET bereits entsprechend aktiviert wurde, auf 60 Minuten festzulegen:

```csharp
await myBatchClient.PoolOperations.EnableAutoScaleAsync(
    "myexistingpool",
    autoscaleEvaluationInterval: TimeSpan.FromMinutes(60));
```

## <a name="evaluate-an-autoscale-formula"></a>Erstellen einer autoscale-Formel

Sie können eine Formel auswerten, bevor Sie sie auf einen Pool anwenden. Auf diese Weise können Sie die Formel testen, um die Auswertung für die Anweisungen zu prüfen, bevor Sie die Formel in der Produktion einsetzen.

Damit Sie eine Formel für die automatische Skalierung auswerten können, müssen Sie zunächst die automatische Skalierung für den Pool mit einer gültigen Formel aktivieren. Wenn Sie eine Formel für einen Pool testen möchten, für den die automatische Skalierung noch nicht aktiviert wurde, verwenden Sie beim ersten Aktivieren der automatischen Skalierung die einzeilige Formel `$TargetDedicatedNodes = 0`. Verwenden Sie anschließend eines der folgenden Verfahren, um die zu testende Formel auszuwerten:

* [BatchClient.PoolOperations.EvaluateAutoScale](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) oder [EvaluateAutoScaleAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Für diese Batch .NET-Methoden sind die ID eines vorhandenen Pools und eine Zeichenfolge mit der auszuwertenden autoscale-Formel erforderlich.

* [Auswerten einer Formel für die automatische Skalierung](https://docs.microsoft.com/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Geben Sie in dieser REST-API-Anforderung die Pool-ID im URI und die autoscale-Formel im *autoScaleFormula*-Element des Anforderungstexts an. Die bei der Anfrage generierte Antwort enthält Fehlerinformationen, die in Zusammenhang mit der Formel stehen können.

In diesem [Batch .NET][net_api]-Codeausschnitt wird eine Formel für die automatische Skalierung ausgewertet. Wenn für den Pool noch keine automatische Skalierung aktiviert ist, führen wir dies zuerst aus.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on non-autoscale-enabled pool.
if (pool.AutoScaleEnabled == false)
{
    // We need a valid autoscale formula to enable autoscaling on the
    // pool. This formula is valid, but won't resize the pool:
    await pool.EnableAutoScaleAsync(
        autoscaleFormula: "$TargetDedicatedNodes = $CurrentDedicatedNodes;",
        autoscaleEvaluationInterval: TimeSpan.FromMinutes(5));

    // Batch limits EnableAutoScaleAsync calls to once every 30 seconds.
    // Because we want to apply our new autoscale formula below if it
    // evaluates successfully, and we *just* enabled autoscaling on
    // this pool, we pause here to ensure we pass that threshold.
    Thread.Sleep(TimeSpan.FromSeconds(31));

    // Refresh the properties of the pool so that we've got the
    // latest value for AutoScaleEnabled
    await pool.RefreshAsync();
}

// We must ensure that autoscaling is enabled on the pool prior to
// evaluating a formula
if (pool.AutoScaleEnabled == true)
{
    // The formula to evaluate - adjusts target number of nodes based on
    // day of week and time of day
    string myFormula = @"
        $curTime = time();
        $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
        $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
        $isWorkingWeekdayHour = $workHours && $isWeekday;
        $TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
    ";

    // Perform the autoscale formula evaluation. Note that this code does not
    // actually apply the formula to the pool.
    AutoScaleRun eval =
        await batchClient.PoolOperations.EvaluateAutoScaleAsync(pool.Id, myFormula);

    if (eval.Error == null)
    {
        // Evaluation success - print the results of the AutoScaleRun.
        // This will display the values of each variable as evaluated by the
        // autoscale formula.
        Console.WriteLine("AutoScaleRun.Results: " +
            eval.Results.Replace("$", "\n    $"));

        // Apply the formula to the pool since it evaluated successfully
        await batchClient.PoolOperations.EnableAutoScaleAsync(pool.Id, myFormula);
    }
    else
    {
        // Evaluation failed, output the message associated with the error
        Console.WriteLine("AutoScaleRun.Error.Message: " +
            eval.Error.Message);
    }
}
```

Eine erfolgreiche Auswertung der Formel, die in diesem Codeausschnitt angezeigt wird, führt zu Ergebnissen, die etwa wie folgt aussehen:

```
AutoScaleRun.Results:
    $TargetDedicatedNodes=10;
    $NodeDeallocationOption=requeue;
    $curTime=2016-10-13T19:18:47.805Z;
    $isWeekday=1;
    $isWorkingWeekdayHour=0;
    $workHours=0
```

## <a name="get-information-about-autoscale-runs"></a>Abrufen von Informationen zu Ausführungen der automatischen Skalierung

Um sicherzustellen, dass die Formel wie erwartet funktioniert, sind regelmäßige Überprüfungen der Ergebnisse von Ausführungen der automatischen Skalierung ratsam, die von Batch für den Pool vorgenommen werden. Rufen Sie hierzu einen Verweis auf den Pool auf (oder aktualisieren Sie ihn), und untersuchen Sie die Eigenschaften der letzten Ausführung der automatischen Skalierung.

In Batch .NET verfügt die Eigenschaft [CloudPool.AutoScaleRun](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) über mehrere Eigenschaften, die Informationen zur letzten Ausführung der automatischen Skalierung für den Pool liefern:

* [AutoScaleRun.Timestamp](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
* [AutoScaleRun.Results](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.results)
* [AutoScaleRun.Error](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.autoscalerun.error)

In der REST-API gibt die Anforderung zum [Abrufen von Informationen zu einem Pool](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool) Informationen zum Pool zurück, z.B. zur letzten Ausführung der automatischen Skalierung in der Eigenschaft [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-pool).

Im folgenden C#-Codeausschnitt wird die Batch .NET-Bibliothek verwendet, um Informationen zur letzten Ausführung der automatischen Skalierung für den Pool _myPool_ auszugeben:

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Beispielausgabe des obigen Codeausschnitts:

```
Last execution: 10/14/2016 18:36:43
Result:
  $TargetDedicatedNodes=10;
  $NodeDeallocationOption=requeue;
  $curTime=2016-10-14T18:36:43.282Z;
  $isWeekday=1;
  $isWorkingWeekdayHour=0;
  $workHours=0
Error:
```

## <a name="example-autoscale-formulas"></a>Beispiele für autoscale-Formeln
Hier sind einige Formeln angegeben, die verschiedene Möglichkeiten zum Anpassen der Anzahl von Computeressourcen in einem Pool darstellen.

### <a name="example-1-time-based-adjustment"></a>Beispiel 1: Zeitbasierte Anpassung
Angenommen Sie möchten die Poolgröße basierend auf dem Wochentag und der Tageszeit anpassen. Dieses Beispiel zeigt, wie Sie die Knotenanzahl im Pool entsprechend Anzahl zum erhöhen oder verringern.

Die Formel ruft zunächst die aktuelle Uhrzeit ab. Wenn es sich um einen Werktag (1 bis 5) handelt und der Wert innerhalb der Geschäftszeiten (8:00 Uhr bis 18:00 Uhr) liegt, wird die Zielgröße des Pools auf 20 Knoten festgelegt. Andernfalls wird der Wert auf 10 Knoten festgelegt.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
```

### <a name="example-2-task-based-adjustment"></a>Beispiel 2: Aufgabenbasierte Anpassung
In diesem Beispiel wird die Größe des Pools basierend auf der Anzahl der Aufgaben in der Warteschlange angepasst. In Formelzeichenfolgen können sowohl Kommentare als auch Zeilenumbrüche verwendet werden.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1), avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - keep nodes active only until tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Beispiel 3: Berücksichtigung paralleler Aufgaben
In diesem Beispiel wird die Poolgröße basierend auf der Anzahl von Aufgaben angepasst. Diese Formel berücksichtigt auch den für den Pool festgelegten Wert [MaxTasksPerComputeNode][net_maxtasks]. Dieser Ansatz ist besonders hilfreich, wenn in Ihrem Pool die [parallele Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert wurde.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks (the
// MaxTasksPerComputeNode property on this pool is set to 4, adjust this number
// for your use case)
$cores = $TargetDedicatedNodes * 4;
$extraVMs = (($tasks - $cores) + 3) / 4;
$targetVMs = ($TargetDedicatedNodes + $extraVMs);
// Attempt to grow the number of compute nodes to match the number of active
// tasks, with a maximum of 3
$TargetDedicatedNodes = max(0,min($targetVMs,3));
// Keep the nodes active until the tasks finish
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-4-setting-an-initial-pool-size"></a>Beispiel 4: Festlegen einer anfänglichen Poolgröße
Dieses Beispiel zeigt einen C#-Codeausschnitt mit einer Formel für die automatische Skalierung, welche die Größe des Pools für einen anfänglichen Zeitraum auf eine angegebene Anzahl von Knoten festlegt. Dann passt sie die Größe des Pools nach dem ersten Zeitraum basierend auf der Anzahl der ausgeführten und aktive Aufgaben an.

Mit der Formel im folgenden Codeausschnitt wird Folgendes durchgeführt:

* Die anfängliche Poolgröße wird auf 4 Knoten festgelegt.
* Die Größe des Pools wird innerhalb der ersten 10 Minuten des Lebenszyklus des Pools nicht angepasst.
* Nach 10 Minuten wird die maximale Anzahl ausgeführter und aktiver Aufgaben in den letzten 60 Minuten abgerufen.
  * Falls beide Werte 0 sind (d. h., dass in den letzten 60 Minuten keine Aufgaben ausgeführt wurden oder aktiv waren), wird die Poolgröße auf 0 festgelegt.
  * Wenn einer der Werte größer als null ist, erfolgt keine Änderung.

```csharp
string now = DateTime.UtcNow.ToString("r");
string formula = string.Format(@"
    $TargetDedicatedNodes = {1};
    lifespan         = time() - time(""{0}"");
    span             = TimeInterval_Minute * 60;
    startup          = TimeInterval_Minute * 10;
    ratio            = 50;

    $TargetDedicatedNodes = (lifespan > startup ? (max($RunningTasks.GetSample(span, ratio), $ActiveTasks.GetSample(span, ratio)) == 0 ? 0 : $TargetDedicatedNodes) : {1});
    ", now, 4);
```

## <a name="next-steps"></a>Nächste Schritte
* [Maximieren der Azure Batch-Computeressourcenauslastung mit parallelen Knotenaufgaben](batch-parallel-node-tasks.md) enthält Informationen dazu, wie Sie auf den Computeknoten im Pool mehrere Tasks gleichzeitig ausführen können. Zusätzlich zur automatischen Skalierung können Sie mit diesem Feature die Auftragsdauer für einige Workloads verringern und so Geld sparen.
* Um die Effizienz weiter zu steigern, stellen Sie sicher, dass die Batch-Anwendung den Batch-Dienst in optimaler Weise abfragt. Unter [Effizientes Abfragen des Azure Batch-Diensts](batch-efficient-list-queries.md) erfahren Sie, wie Sie die Datenmenge beschränken, die übertragen wird, wenn Sie den Status von möglicherweise Tausenden von Computeknoten oder -aufgaben abfragen.

[net_api]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[net_batchclient]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient
[net_cloudpool_autoscaleformula]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula
[net_cloudpool_autoscaleevalinterval]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval
[net_enableautoscaleasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync
[net_maxtasks]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudpool.maxtaskspercomputenode
[net_poolops_resizepoolasync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync

[rest_api]: https://docs.microsoft.com/rest/api/batchservice/
[rest_autoscaleformula]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_autoscaleinterval]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
[rest_enableautoscale]: https://docs.microsoft.com/rest/api/batchservice/enable-automatic-scaling-on-a-pool
