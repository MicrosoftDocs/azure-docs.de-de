---
title: Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool
description: Aktivieren Sie das automatische Skalieren in einem Cloudpool, um die Anzahl von Computeknoten im Pool dynamisch anzupassen.
ms.topic: how-to
ms.date: 11/23/2020
ms.custom: H1Hack27Feb2017, fasttrack-edit, devx-track-csharp
ms.openlocfilehash: 033272f22b98b27c67e9a551bce952368d35a043
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95737291"
---
# <a name="create-an-automatic-formula-for-scaling-compute-nodes-in-a-batch-pool"></a>Erstellen einer Formel für die automatische Skalierung von Computeknoten in einem Batch-Pool

Azure Batch kann Pools basierend auf von Ihnen definierten Parametern automatisch skalieren und Ihnen somit Zeit und Geld sparen. Bei der automatischen Skalierung fügt Batch einem Pool Knoten dynamisch hinzu, wenn der Taskbedarf steigt, und entfernt Computeknoten bei einem Rückgang des Taskbedarfs.

Um die automatische Skalierung für einen Pool von Computeknoten zu aktivieren, ordnen Sie den Pool einer von Ihnen definierten *Formel für die automatische Skalierung* zu. Anhand der Formel für die automatische Skalierung ermittelt der Batch-Dienst die Anzahl von Knoten, die zum Ausführen Ihrer Workload erforderlich sind. Diese Knoten können dedizierte Knoten oder [Knoten mit niedriger Priorität](batch-low-pri-vms.md) sein. Batch überprüft dann regelmäßig Dienstmetrikdaten und verwendet diese, um die Anzahl der Knoten im Pool basierend auf Ihrer Formel in einem von Ihnen definierten Intervall anzupassen.

Sie können automatische Skalierung beim Erstellen eines Pools aktivieren oder später auf einen vorhandenen Pool anwenden. Batch gibt Ihnen die Möglichkeit, Formeln vor dem Zuweisen zu Pools auszuwerten und den Status automatischer Skalierungsausführungen zu überwachen. Nachdem Sie einen Pool mit automatischer Skalierung konfiguriert haben, können Sie später Änderungen an der Formel vornehmen.

> [!IMPORTANT]
> Wenn Sie ein Batch-Konto erstellen, können Sie den [Poolzuordnungsmodus](accounts.md) angeben, der bestimmt, ob Pools in einem Batch-Dienstabonnement (Standardeinstellung) oder in Ihrem Benutzerabonnement zugeordnet werden. Wenn Sie Ihr Batch-Konto mit der Batch-Standarddienstkonfiguration erstellt haben, ist Ihr Konto auf eine maximale Anzahl von Kernen beschränkt, die für die Verarbeitung verwendet werden können. Der Batch-Dienst skaliert Computeknoten nur bis zu diesem Kernspeichergrenzwert. Aus diesem Grund erreicht der Batch-Dienst möglicherweise nicht die Zielanzahl der von einer Formel für die automatische Skalierung angegebenen Computeknoten. Unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md) finden Sie Informationen zum Anzeigen und Erhöhen Ihrer Kontokontingente.
>
>Wenn Sie Ihr Konto im Benutzerabonnementmodus erstellt haben, teilt Ihr Konto das Kernkontingent für das Abonnement. Weitere Informationen finden Sie unter [Virtual Machines-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits) in [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="autoscale-formulas"></a>Formeln für automatische Skalierung

Eine Formel für automatische Skalierung ist ein von Ihnen definierter Zeichenfolgenwert, der mindestens eine Anweisung enthält. Die Formel für die Autoskalierung wird dem Element [autoScaleFormula](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (Batch REST) oder der Eigenschaft [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula) (Batch .NET) eines Pools zugewiesen. Der Batch-Dienst verwendet Ihre Formel, um für das nächste Verarbeitungsintervall die Zielanzahl der Computeknoten im Pool zu ermitteln. Die Formelzeichenfolge darf 8 KB nicht überschreiten und kann bis zu 100 durch Semikolons getrennte Anweisungen sowie Zeilenumbrüche und Kommentare enthalten.

Sie können sich Formeln für die automatische Skalierung als eine „Batch-Sprache“ für die automatische Skalierung vorstellen. Formelanweisungen sind frei definierte Ausdrücke, die sowohl dienstdefinierte Variablen (vom Batch-Dienst definiert) als auch benutzerdefinierte Variablen enthalten können. Mithilfe von integrierten Typen, Operatoren und Funktionen können Formeln für diese Werte eine Vielzahl von Operationen ausführen. Eine Anweisung kann beispielsweise wie folgt aussehen:

```
$myNewVariable = function($ServiceDefinedVariable, $myCustomVariable);
```

Formeln enthalten im Allgemeinen mehrere Anweisungen, die Operationen für Werte durchführen, die in vorherigen Anweisungen abgerufen wurden. So rufen wir beispielsweise zuerst einen Wert für `variable1` ab und übergeben ihn dann an eine Funktion, um `variable2` aufzufüllen:

```
$variable1 = function1($ServiceDefinedVariable);
$variable2 = function2($OtherServiceDefinedVariable, $variable1);
```

Fügen Sie diese Anweisungen zu Ihrer Formel für die automatische Skalierung hinzu, um eine Zielanzahl von Computeknoten zu erreichen. Dedizierte Knoten und Knoten mit niedriger Priorität verfügen jeweils über eigene Zieleinstellungen. Eine Formel für die automatische Skalierung kann einen Zielwert für dedizierte Knoten, einen Zielwert für Knoten mit niedriger Priorität oder beides enthalten.

Die Zielanzahl der Knoten ist möglicherweise höher oder niedriger als die aktuelle Anzahl der Knoten dieses Typs im Pool, oder sie ist mit der Anzahl identisch. Batch wertet die Formel für die automatische Skalierung des Pools in einem bestimmten [Intervall für automatische Skalierung](#automatic-scaling-interval) aus. Batch passt die Zielanzahl von den einzelnen Knotentypen im Pool an die Anzahl an, die durch die Formel für die automatische Skalierung zum Zeitpunkt der Auswertung angegeben wird.

### <a name="sample-autoscale-formulas"></a>Beispielformeln für die automatische Skalierung

Im Folgenden sehen Sie Beispiele für zwei Formeln für die automatische Skalierung, die für die meisten Szenarien angepasst werden können. Die Variablen `startingNumberOfVMs` und `maxNumberofVMs` in den Beispielformeln können Ihren Anforderungen angepasst werden.

#### <a name="pending-tasks"></a>Ausstehende Aufgaben

Mit dieser Formel für die automatische Skalierung wird der Pool zunächst mit einem einzigen virtuellen Computer erstellt. Die Metrik `$PendingTasks` definiert die Anzahl der Aufgaben, die ausgeführt werden oder sich in einer Warteschlange befinden. Die Formel sucht nach der durchschnittlichen Anzahl ausstehender Aufgaben in den letzten 180 Sekunden und legt die Variable `$TargetDedicatedNodes` entsprechend fest. Die Formel stellt sicher, dass die Zielanzahl der dedizierten Knoten niemals 25 virtuelle Computer überschreitet. Wenn neue Aufgaben gesendet werden, wächst der Pool automatisch an. Wenn Tasks abgeschlossen werden, werden VMs freigegeben, und der Pool wird durch die Formel für automatische Skalierung verkleinert.

Diese Formel skaliert dedizierte Knoten. Sie kann jedoch so geändert werden, dass sie auch Knoten mit niedriger Priorität skaliert.

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
$NodeDeallocationOption = taskcompletion;
```

#### <a name="preempted-nodes"></a>Vorzeitig entfernte Knoten

In diesem Beispiel wird ein Pool erstellt, der mit 25 Knoten mit niedriger Priorität beginnt. Jedes Mal, wenn ein Knoten mit niedriger Priorität vorzeitig entfernt wird, wird er durch einen dedizierten Knoten ersetzt. Wie im ersten Beispiel verhindert die `maxNumberofVMs`-Variable, dass der Pool 25 VMs überschreitet. Dieses Beispiel eignet sich für die Nutzung von VMs mit niedriger Priorität, während gleichzeitig sichergestellt wird, dass für die Lebensdauer des Pools nur eine festgelegte Anzahl vorzeitiger Entfernungen auftritt.

```
maxNumberofVMs = 25;
$TargetDedicatedNodes = min(maxNumberofVMs, $PreemptedNodeCount.GetSample(180 * TimeInterval_Second));
$TargetLowPriorityNodes = min(maxNumberofVMs , maxNumberofVMs - $TargetDedicatedNodes);
$NodeDeallocationOption = taskcompletion;
```

Weitere Informationen zum [Erstellen von Formeln für automatische Skalierung](#write-an-autoscale-formula) und weitere [Beispielformeln für automatische Skalierung](#example-autoscale-formulas) finden Sie weiter unten in diesem Thema.

## <a name="variables"></a>Variables

Sie können in Ihrer Formel für die automatische Skalierung sowohl **dienstdefinierte** als auch **benutzerdefinierte** Variablen verwenden.

Vom Dienst definierte Variablen sind in den Batch-Dienst integriert. Einige der vom Dienst definierten Variablen verfügen über Lese-/Schreibzugriff, und einige sind schreibgeschützt.

Benutzerdefinierte Variablen sind Variablen, die Sie definieren. In der oben gezeigten Beispielformel sind `$TargetDedicatedNodes` und `$PendingTasks` vom Dienst definierte Variablen, während `startingNumberOfVMs` und `maxNumberofVMs` benutzerdefinierte Variablen sind.

> [!NOTE]
> Vom Dienst definierten Variablen wird immer ein Dollarzeichen ($) vorangestellt. Für benutzerdefinierte Variablen ist das Dollarzeichen optional.

Die folgenden Tabellen enthalten sowohl die Variablen mit Lese-/Schreibzugriff als auch schreibgeschützte Variablen, die vom Batch-Dienst definiert werden.

### <a name="read-write-service-defined-variables"></a>Vom Dienst definierte Variablen mit Lese-/Schreibzugriff

Sie können diese vom Dienst definierten Variablen abrufen und festlegen, um die Anzahl der Computeknoten in einem Pool zu verwalten.

| Variable | BESCHREIBUNG |
| --- | --- |
| $TargetDedicatedNodes |Die Zielanzahl dedizierter Computeknoten für den Pool. Diese wird als Ziel angegeben, da ein Pool möglicherweise nicht immer die gewünschte Anzahl von Knoten erreicht. Wenn die Zielanzahl dedizierter Knoten beispielsweise durch eine Auswertung der automatischen Skalierung geändert wird, bevor der Pool das ursprüngliche Ziel erreicht hat, erreicht der Pool möglicherweise nicht die Zielanzahl. <br /><br /> Ein Pool in einem Konto, der im Batch-Dienstmodus erstellt wurde, erreicht möglicherweise nicht sein Ziel, wenn das Ziel ein Batch-Kontoknoten- oder -Kernkontingent überschreitet. Ein Pool in einem Konto, der im Benutzerabonnementmodus erstellt wurde, erreicht möglicherweise nicht sein Ziel, wenn das Ziel das freigegebene Kernkontingent für das Abonnement überschreitet.|
| $TargetLowPriorityNodes |Die Zielanzahl von Computeknoten mit niedriger Priorität für den Pool. Diese wird als Ziel angegeben, da ein Pool möglicherweise nicht immer die gewünschte Anzahl von Knoten erreicht. Wenn die Zielanzahl von Knoten mit niedriger Priorität beispielsweise durch eine Auswertung der automatischen Skalierung geändert wird, bevor der Pool das ursprüngliche Ziel erreicht hat, kann der Pool möglicherweise nicht die Zielanzahl erreichen. Ein Pool kann sein Ziel möglicherweise auch dann nicht erreichen, wenn das Ziel ein Batch-Kontoknoten- oder -Kernkontingent überschreitet. <br /><br /> Weitere Informationen zu Computeknoten mit niedriger Priorität finden Sie unter [Verwenden von VMs mit niedriger Priorität mit Batch](batch-low-pri-vms.md). |
| $NodeDeallocationOption |Dieser Vorgang wird ausgeführt, wenn Computeknoten aus einem Pool entfernt werden. Mögliche Werte:<ul><li>**requeue (erneut in Warteschlange einreihen)** : Der Standardwert. Tasks werden sofort beendet und erneut in die Auftragswarteschlange eingereiht, damit sie erneut geplant werden können. Dadurch wird sichergestellt, dass die Zielanzahl der Knoten so schnell wie möglich erreicht wird. Dies kann jedoch weniger effizient sein, da alle aktuell ausgeführten Tasks unterbrochen werden und dann vollständig neu gestartet werden müssen. <li>**terminate (beenden)** : Beendet Tasks sofort und entfernt sie aus der Auftragswarteschlange.<li>**taskcompletion (Taskabschluss)** : Auf aktuell ausgeführte Tasks wird gewartet und der Knoten dann aus dem Pool entfernt. Verwenden Sie diese Option, um zu verhindern, dass Aufgaben unterbrochen und nochmals der Warteschlange hinzugefügt werden, wodurch bereits durchgeführte Aufgabenschritte umsonst erfolgt wären.<li>**retaineddata (zurückbehaltene Daten)** : Wartet, bis alle lokal zurückbehaltenen Taskdaten des Pools gelöscht wurden, bevor der Knoten aus dem Pool entfernt wird.</ul> |

> [!NOTE]
> Die Variable `$TargetDedicatedNodes` kann auch mit dem Alias `$TargetDedicated` angegeben werden. Entsprechend kann die Variable `$TargetLowPriorityNodes` mit dem Alias `$TargetLowPriority` angegeben werden. Wenn sowohl die vollständig benannte Variable als auch ihr Alias von der Formel festgelegt werden, hat der Wert Vorrang, der der vollständig benannten Variablen zugewiesen ist.

### <a name="read-only-service-defined-variables"></a>Vom Dienst definierte schreibgeschützte Variablen

Sie können den Wert dieser vom Dienst definierten Variablen abrufen, um Anpassungen basierend auf den Metriken des Batch-Diensts vorzunehmen.

> [!IMPORTANT]
> Tasks zur Auftragsfreigabe sind derzeit nicht in den Variablen enthalten, die die Taskanzahl angeben, z. B. $ActiveTasks und $PendingTasks. Je nach verwendeter Formel für automatische Skalierung kann dies dazu führen, dass Knoten entfernt werden und keine Knoten mehr verfügbar sind, um die Tasks zur Auftragsfreigabe auszuführen.

| Variable | BESCHREIBUNG |
| --- | --- |
| $CPUPercent |Die durchschnittliche prozentuale CPU-Auslastung |
| $WallClockSeconds |Die Anzahl der verbrauchten Sekunden |
| $MemoryBytes |Die durchschnittliche Anzahl genutzter Megabyte |
| $DiskBytes |Die durchschnittliche Anzahl der auf den lokalen Datenträgern genutzten Gigabyte |
| $DiskReadBytes |Die Anzahl der gelesenen Bytes. |
| $DiskWriteBytes |Die Anzahl der geschriebenen Byte |
| $DiskReadOps |Die Anzahl der ausgeführten Datenträger-Lesevorgänge |
| $DiskWriteOps |Die Anzahl der ausgeführten Datenträger-Schreibvorgänge |
| $NetworkInBytes |Die Anzahl der eingehenden Byte |
| $NetworkOutBytes |Die Anzahl der ausgehenden Byte |
| $SampleNodeCount |Die Anzahl der Computeknoten |
| $ActiveTasks |Die Anzahl der Aufgaben, die zur Ausführung bereit sind, aber noch nicht ausgeführt werden. Dies umfasst alle Tasks, die sich im aktiven Zustand befinden und deren Abhängigkeiten erfüllt wurden. Alle Aufgaben, die sich nicht im aktiven Zustand befinden, deren Abhängigkeiten aber nicht erfüllt wurden, werden aus der Anzahl der $ActiveTasks ausgeschlossen. Bei einem Task mit mehreren Instanzen umfasst „$ActiveTasks“ die Anzahl der für den Task festgelegten Instanzen.|
| $RunningTasks |Die Anzahl der Aufgaben, die sich in einem Ausführungszustand befinden |
| $PendingTasks |Die Summe aus $ActiveTasks und $RunningTasks. |
| $SucceededTasks |Die Anzahl der Aufgaben, die erfolgreich abgeschlossen wurden |
| $FailedTasks |Die Anzahl der Aufgaben, bei denen Fehler aufgetreten sind |
| $CurrentDedicatedNodes |Die aktuelle Anzahl der zugewiesenen Computeknoten |
| $CurrentLowPriorityNodes |Die aktuelle Anzahl der Computeknoten mit niedriger Priorität einschließlich aller Knoten, die vorzeitig entfernt wurden. |
| $PreemptedNodeCount | Die Anzahl der Knoten im Pool, die sich im Zustand „Vorzeitig entfernt“ befinden. |

> [!TIP]
> Die vom Dienst definierten schreibgeschützten Variablen sind *Objekte*, die verschiedene Methoden für den Zugriff auf die zum jeweiligen Objekt gehörigen Daten bereitstellen. Weitere Informationen finden Sie weiter unten in diesem Artikel unter [Abrufen von Beispieldaten](#obtain-sample-data).

> [!NOTE]
> Verwenden Sie `$RunningTasks`, wenn die Skalierung auf der Anzahl der zu einem Zeitpunkt aktiven Tasks basiert, und `$ActiveTasks`, wenn die Skalierung auf der Anzahl der Tasks basiert, die in die Warteschlange gestellt werden.

## <a name="types"></a>Typen

Formeln für automatische Skalierung unterstützen die folgenden Typen:

- double
- doubleVec
- doubleVecList
- Zeichenfolge
- timestamp: eine Verbundstruktur, die die folgenden Member enthält:
  - year
  - Monat (1-12)
  - Tag (1-31)
  - Wochentag (im Zahlenformat; Beispiel: 1 für Montag)
  - Stunde (im 24-Stunden-Zahlenformat; Beispiel: 13 steht für 13 Uhr)
  - Minute (00-59)
  - Sekunde (00-59)
- timeInterval
  - TimeInterval_Zero
  - TimeInterval_100ns
  - TimeInterval_Microsecond
  - TimeInterval_Millisecond
  - TimeInterval_Second
  - TimeInterval_Minute
  - TimeInterval_Hour
  - TimeInterval_Day
  - TimeInterval_Week
  - TimeInterval_Year

## <a name="operations"></a>Operationen (Operations)

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
| *Operator* double |-, ! |double |
| *Operator* timeinterval |- |timeInterval |
| double *Operator* double |<, <=, ==, >=, >, != |double |
| string *Operator* string |<, <=, ==, >=, >, != |double |
| timestamp *Operator* timestamp |<, <=, ==, >=, >, != |double |
| timeinterval *Operator* timeinterval |<, <=, ==, >=, >, != |double |
| double *Operator* double |&&, &#124;&#124; |double |

Beim Testen von „double“ mit einem ternären Operator (`double ? statement1 : statement2`) sind Werte ungleich Null **true** und Nullwerte **false**.

## <a name="functions"></a>Functions

Sie können diese vordefinierten **Funktionen** verwenden, wenn Sie eine Formel für automatische Skalierung definieren.

| Funktion | Rückgabetyp | BESCHREIBUNG |
| --- | --- | --- |
| avg(doubleVecList) |double |Der Durchschnittswert aller Werte in der doubleVecList wird zurückgegeben. |
| len(doubleVecList) |double |Die Länge des Vektors, der aus der doubleVecList erstellt wurde, wird zurückgegeben. |
| lg(double) |double |Gibt für den double-Wert den Logarithmus zur Basis 2 zurück. |
| lg(doubleVecList) |doubleVec |Gibt für die doubleVecList den komponentenbezogenen Logarithmus zur Basis 2 zurück. Ein vec(double) muss explizit für den Parameter übergeben werden. Andernfalls wird von der double lg(double)-Version ausgegangen. |
| ln(double) |double |Gibt für den double-Wert den natürlichen Logarithmus zurück. |
| ln(doubleVecList) |doubleVec |Gibt für den double-Wert den natürlichen Logarithmus zurück. |
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
| time(string dateTime="") |timestamp |Es werden entweder der Zeitstempel der aktuellen Zeit zurückgegeben, wenn keine Parameter übergeben werden, oder andernfalls der Zeitstempel der dateTime-Zeichenfolge, wenn diese übergeben wird. Unterstützte DateTime-Formate sind W3C-DTF und RFC 1123. |
| val(doubleVec v, double i) |double |Der Wert des Elements an Position i im Vektor v mit einem Anfangsindex von 0 wird zurückgegeben. |

Einige der in der vorherigen Tabelle beschriebenen Funktionen akzeptieren eine Liste als Argument. Bei der durch Trennzeichen getrennten Liste handelt es sich um eine beliebige Kombination aus *double* und *doubleVec*. Beispiel:

`doubleVecList := ( (double | doubleVec)+(, (double | doubleVec) )* )?`

Der *doubleVecList*-Wert wird vor der Auswertung in einen einzelnen *doubleVec* konvertiert. Zum Beispiel hat bei `v = [1,2,3]` das Aufrufen von `avg(v)` den gleichen Effekt wie das Aufrufen von `avg(1,2,3)`. Das Aufrufen von `avg(v, 7)` entspricht dem Aufrufen von `avg(1,2,3,7)`.

## <a name="metrics"></a>Metriken

Für das Definieren einer Formel können Sie sowohl Ressourcenmetriken als auch Aufgabenmetriken verwenden. Sie passen die vorgegebene Anzahl dedizierter Knoten im Pool basierend auf den Metrikdaten an, die Sie abrufen und auswerten. Weitere Informationen zu den einzelnen Metriken finden Sie im Abschnitt [Variablen](#variables).

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

## <a name="obtain-sample-data"></a>Abrufen von Beispieldaten

Das Abrufen von Metrikdaten zu Tasks und Ressourcen (Stichproben) sowie das anschließende Anpassen der Poolgröße basierend auf diesen Daten ist die Kernfunktion einer Formel für automatische Skalierung. Daher ist es wichtig, sich damit vertraut zu machen, wie Formeln für automatische Skalierung mit Stichproben interagieren.

### <a name="methods"></a>Methoden

Die Formeln für automatische Skalierung greifen auf Stichproben von Metrikdaten zurück, die vom Batch-Dienst bereitgestellt werden. Eine Formel vergrößert oder verkleinert die Poolgröße basierend auf den Werten, die abgerufen werden. Vom Dienst definierte Variablen sind Objekte, die Methoden für den Zugriff auf die zum jeweiligen Objekt gehörigen Daten bereitstellen. Der folgende Ausdruck zeigt z. B. eine Anforderung zum Abrufen der letzten fünf Minuten der CPU-Auslastung:

```
$CPUPercent.GetSample(TimeInterval_Minute * 5)
```

Die folgenden Methoden können zum Abrufen von Stichprobendaten zu vom Dienst definierten Variablen verwendet werden.

| Methode | BESCHREIBUNG |
| --- | --- |
| GetSample() |Die `GetSample()`-Methode gibt einen Vektor aus Stichprobenwerten zurück.<br/><br/>Eine Stichprobe enthält Metrikdaten, die innerhalb von 30 Sekunden erfasst wurden. Dies bedeutet, dass alle 30 Sekunden eine Stichprobe genommen wird. Wie nachstehend erwähnt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichprobe und ihrer Verfügbarkeit für eine Formel. Daher stehen möglicherweise nicht alle Stichproben für einen bestimmten Zeitraum für die Bewertung durch eine Formel zur Verfügung.<ul><li>`doubleVec GetSample(double count)`: Gibt die Anzahl von Stichproben an, die aus den letzten erfassten Stichproben abgerufen werden soll. `GetSample(1)` gibt die neueste verfügbare Stichprobe zurück. Für Metriken wie `$CPUPercent` sollte `GetSample(1)` allerdings nicht verwendet werden, da unmöglich feststellbar ist, *wann* die Stichprobe erfasst wurde. Sie kann aktuell oder aufgrund von Systemproblemen auch wesentlich älter sein. In solchen Fällen ist es besser, wie unten gezeigt ein Zeitintervall zu verwenden.<li>`doubleVec GetSample((timestamp or timeinterval) startTime [, double samplePercent])`: Gibt einen Zeitraum für die Erfassung von Stichprobendaten an. Optional gibt diese Methode auch den Prozentsatz der Stichproben an, die im angeforderten Zeitraum verfügbar sein müssen. Beispielsweise gibt `$CPUPercent.GetSample(TimeInterval_Minute * 10)` 20 Stichproben zurück, wenn alle Stichproben der letzten 10 Minuten im `CPUPercent`-Verlauf vorhanden sind. Wenn die letzte Minute des Verlaufs nicht verfügbar ist, werden nur 18 Stichproben zurückgegeben. In diesem Fall tritt für `$CPUPercent.GetSample(TimeInterval_Minute * 10, 95)` ein Fehler auf, da nur 90 Prozent der Stichproben verfügbar sind, `$CPUPercent.GetSample(TimeInterval_Minute * 10, 80)` wäre jedoch erfolgreich.<li>`doubleVec GetSample((timestamp or timeinterval) startTime, (timestamp or timeinterval) endTime [, double samplePercent])`: Gibt einen Zeitrahmen für die Datenerfassung mit einer Start- und einer Endzeit an. Wie bereits erwähnt, gibt es eine Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichprobe und ihrer Verfügbarkeit für eine Formel. Berücksichtigen Sie diese Verzögerung, wenn Sie die Methode `GetSample` verwenden. Weitere Informationen finden Sie bei `GetSamplePercent` weiter unten. |
| GetSamplePeriod() |Gibt den Zeitraum zurück, in dem die Stichproben aus einem alten Stichproben-Dataset gesammelt wurden. |
| Count() |Liefert die Gesamtzahl der Stichprobenwerte im Metrikverlauf zurück. |
| HistoryBeginTime() |Gibt den Zeitstempel des ältesten verfügbaren Stichprobenwerts für die Metrik zurück. |
| GetSamplePercent() |Gibt den Prozentsatz an Stichprobenwerten zurück, die für ein bestimmtes Intervall verfügbar sind. Beispiel: `doubleVec GetSamplePercent( (timestamp or timeinterval) startTime [, (timestamp or timeinterval) endTime] )`. Da die `GetSample`-Methode einen Fehler erzeugt, wenn der Prozentsatz der zurückgegebenen Stichproben kleiner als der angegebene `samplePercent`-Wert ist, können Sie vorab mithilfe der `GetSamplePercent`-Methode eine Prüfung vornehmen. Wenn nicht genügend Beispiele vorhanden sind, können Sie anschließend eine andere Aktion ausführen, , ohne die Auswertung der automatischen Skalierung zu unterbrechen. |

### <a name="samples"></a>Beispiele

Der Batch-Dienst nimmt regelmäßig Stichproben von Task- und Ressourcenmetriken und stellt sie Ihren Formeln für die automatische Skalierung zur Verfügung. Diese Stichproben werden alle 30 Sekunden vom Batch-Dienst aufgezeichnet. Allerdings kommt es meist zu einer Verzögerung zwischen dem Zeitpunkt der Erfassung der Stichproben und dem Zeitpunkt, zu dem sie Ihren Formeln für die automatische Skalierung zur Verfügung gestellt (und von diesen gelesen) werden können. Darüber hinaus können Stichproben aufgrund von Faktoren wie Netzwerk- oder anderen Infrastrukturproblemen möglicherweise nicht für ein bestimmtes Intervall aufgezeichnet werden.

### <a name="sample-percentage"></a>Prozentsatz für die Stichprobe

Beim Übergeben eines `samplePercent`-Werts an die `GetSample()`-Methode oder Aufrufen der `GetSamplePercent()`-Methode bezieht sich _Percent_ auf einen Vergleich zwischen der möglichen Gesamtzahl der vom Batch-Dienst erfassten Stichproben und der Anzahl von Stichproben, die für Ihre Formel für die automatische Skalierung verfügbar sind.

Lassen Sie uns als Beispiel eine Zeitspanne von 10 Minuten betrachten. Da Stichproben in einer Zeitspanne von 10 Minuten alle 30 Sekunden erfasst werden, werden maximal 20 Stichproben von Batch erfasst (zwei pro Minute). Doch aufgrund der erwähnten Latenz des Berichterstellungsmechanismus und anderer Probleme in Azure stehen Ihrer Formel für die automatische Skalierung möglicherweise nur 15 Stichproben zur Verfügung. Das bedeutet, dass in diesem zehnminütigen Zeitraum nur 75 % der Gesamtanzahl erfasster Stichproben für Ihre Formel verfügbar sind.

### <a name="getsample-and-sample-ranges"></a>GetSample() und Stichprobenbereiche

Die Formeln für automatische Skalierung vergrößern oder verkleinern ihre Pools durch Hinzufügen oder Entfernen von Knoten. Da Knoten Geld kosten, müssen Sie sicherstellen, dass Ihre Formeln eine sinnvolle Analysemethode verwenden, die auf einer ausreichenden Menge von Daten basiert. Es wird empfohlen, eine Analyse vom Typ „Trend“ in Ihren Formeln zu verwenden. Dieser Typ vergrößert oder verkleinert Ihre Pools basierend auf einem Bereich gesammelter Stichproben.

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

Weil bei der Verfügbarkeit von Stichproben eine Verzögerung auftreten kann, sollten Sie stets einen Zeitbereich mit einer Startzeit angeben, die mehr als eine Minute zurückliegt. Es dauert ca. eine Minute, bis Stichproben das System durchlaufen haben, weshalb Stichproben im Bereich `(0 * TimeInterval_Second, 60 * TimeInterval_Second)` möglicherweise nicht verfügbar sind. In diesem Fall können Sie den Prozentsatzparameter `GetSample()` angeben, um einen bestimmten Prozentsatz von Stichproben zu erzwingen.

> [!IMPORTANT]
> Es wird dringend empfohlen, **sich in den Formeln für die automatische Skalierung *nicht ausschließlich* auf `GetSample(1)` zu verlassen**. Der Grund ist, dass `GetSample(1)` im Wesentlichen den Batch-Dienst anweist, die letzte vorhandene Stichprobe unabhängig vom Zeitpunkt ihrer Erfassung bereitzustellen. Da es sich nur um ein Stichprobe handelt, die ggf. schon älter ist, ist diese möglicherweise nicht für den aktuellen Aufgaben- oder Ressourcenstatus repräsentativ. Stellen Sie bei Verwendung von `GetSample(1)` sicher, dass dieser Wert zu einer längeren Anweisung gehört und nicht der einzige Datenpunkt ist, auf dem Ihre Formel basiert.

## <a name="write-an-autoscale-formula"></a>Schreiben einer Formel für die automatische Skalierung

Sie können eine Formel für die automatische Skalierung erstellen, indem Sie mithilfe der oben aufgeführten Komponenten Anweisungen formulieren und diese dann zu einer vollständigen Formel kombinieren. In diesem Abschnitt erstellen wir eine Beispielformel für die automatische Skalierung, mit der praxistaugliche Skalierungsentscheidungen getroffen und Anpassungen vorgenommen werden können.

Zuerst definieren wir die Anforderungen für die neue Formel für die automatische Skalierung. Mit der Formel soll Folgendes erreicht werden:

- Die dedizierte Anzahl von Computeknoten in einem Pool soll erhöht werden, wenn die CPU-Auslastung hoch ist.
- Die dedizierte Anzahl von Computeknoten in einem Pool soll reduziert werden, wenn die CPU-Auslastung gering ist.
- Die maximale Anzahl dedizierter Knoten muss immer auf 400 beschränkt sein.
- Wenn Sie die Anzahl der Knoten reduzieren, entfernen Sie keine Knoten, die Tasks ausführen. Warten Sie ggf., bis die Tasks abgeschlossen sind, bevor Sie Knoten entfernen.

Mit der ersten Anweisung in unserer Formel wird die Anzahl der Knoten während der hohen CPU-Auslastung erhöht. Wir definieren eine Anweisung, die eine benutzerdefinierte Variable (`$totalDedicatedNodes`) mit einem Wert auffüllt, der 110 Prozent der aktuellen Zielanzahl dedizierte Knoten darstellt. Dies gilt aber nur, wenn die minimale durchschnittliche CPU-Auslastung in den vergangenen 10 Minuten über 70 Prozent lag. Andernfalls wird der Wert für die aktuelle Anzahl dedizierter Knoten verwendet.

```
$totalDedicatedNodes =
    (min($CPUPercent.GetSample(TimeInterval_Minute * 10)) > 0.7) ?
    ($CurrentDedicatedNodes * 1.1) : $CurrentDedicatedNodes;
```

Zum Reduzieren der Anzahl dedizierter Knoten während einer Phase mit geringer CPU-Auslastung legen wir mit der nächsten Anweisung in unserer Formel dieselbe Variable `$totalDedicatedNodes` auf 90 Prozent der aktuellen Zielanzahl dedizierter Knoten fest, wenn die durchschnittliche CPU-Auslastung in den letzten 60 Minuten unter 20 Prozent lag. Andernfalls wird der aktuelle Wert von `$totalDedicatedNodes` verwendet, den wir oben in der Anweisung angegeben haben.

```
$totalDedicatedNodes =
    (avg($CPUPercent.GetSample(TimeInterval_Minute * 60)) < 0.2) ?
    ($CurrentDedicatedNodes * 0.9) : $totalDedicatedNodes;
```

Nun beschränken Sie die Zielanzahl dedizierter Computeknoten auf maximal 400.

```
$TargetDedicatedNodes = min(400, $totalDedicatedNodes)
```

Schließlich stellen wir sicher, dass Knoten erst entfernt werden, wenn ihre Tasks abgeschlossen sind.

```
$NodeDeallocationOption = taskcompletion;
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
$NodeDeallocationOption = taskcompletion;
```

> [!NOTE]
> Wenn Sie sich für entscheiden, können Sie in Formelzeichenfolgen sowohl Kommentare als auch Zeilenumbrüche einschließen. Beachten Sie auch, dass fehlende Semikolons zu Auswertungsfehlern führen können.

## <a name="automatic-scaling-interval"></a>Intervall für die automatische Skalierung

Standardmäßig passt der Batch-Dienst alle 15 Minuten die Poolgröße gemäß seiner Formel für die automatische Skalierung an. Dieses Intervall kann mithilfe der folgenden Pooleigenschaften konfiguriert werden:

- [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval) (Batch .NET)
- [autoScaleEvaluationInterval](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST-API)

Das kürzeste Intervall ist fünf Minuten, das längste 168 Stunden. Wenn ein Intervall außerhalb dieses Bereichs angegeben wird, gibt der Batch-Dienst einen Fehler des Typs „Unzulässige Anforderung (400)“ zurück.

> [!NOTE]
> Die automatische Skalierung ist derzeit nicht als Reaktion im Zeitraum unter einer Minute auf Änderungen vorgesehen, sondern dient eher zum allmählichen Anpassen der Größe Ihres Pools während der Ausführung Ihres Workloads.

## <a name="create-an-autoscale-enabled-pool-with-batch-sdks"></a>Erstellen eines Pools mit aktivierter Autoskalierung mit Batch SDKs

Die automatische Skalierung für Pools kann mit einem der [Batch SDKs](batch-apis-tools.md#azure-accounts-for-batch-development), den [Batch-PowerShell-Cmdlets](batch-powershell-cmdlets-get-started.md) der [Batch REST-API](/rest/api/batchservice/) und der [Batch-CLI](batch-cli-get-started.md) konfiguriert werden. In diesem Abschnitt sehen Sie Beispiele sowohl für .NET als auch für Python.

### <a name="net"></a>.NET

Gehen Sie wie folgt vor, um einen Pool mit automatischer Skalierung in .NET zu erstellen:

1. Erstellen Sie den Pool mit [BatchClient.PoolOperations.CreatePool](/dotnet/api/microsoft.azure.batch.pooloperations.createpool).
1. Legen Sie die [CloudPool.AutoScaleEnabled](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleenabled)-Eigenschaft auf `true` fest.
1. Legen Sie die [CloudPool.AutoScaleFormula](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleformula)-Eigenschaft mit Ihrer Formel für die automatische Skalierung fest.
1. (Optional) Legen Sie die [CloudPool.AutoScaleEvaluationInterval](/dotnet/api/microsoft.azure.batch.cloudpool.autoscaleevaluationinterval)-Eigenschaft fest (Standardeinstellung ist 15 Minuten).
1. Führen Sie für den Pool mit [CloudPool.Commit](/dotnet/api/microsoft.azure.batch.cloudpool.commit) oder [CommitAsync](/dotnet/api/microsoft.azure.batch.cloudpool.commitasync) einen Commit durch.

Mit dem folgenden Beispiel wird ein Pool mit aktivierter automatischer Skalierung in .NET erstellt. Die Formel für automatische Skalierung des Pools legt die vorgegebene Anzahl dedizierter Knoten auf 5 am Montag und auf 1 an allen anderen Wochentagen fest. Das [Intervall für die automatische Skalierung](#automatic-scaling-interval) wird auf 30 Minuten festgelegt. In diesem und anderen C#-Codeausschnitten in diesem Artikel ist `myBatchClient` eine ordnungsgemäß initialisierte Instanz der Klasse [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

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
> Wenn Sie einen Pool mit aktivierter automatischer Skalierung erstellen, geben Sie beim Aufruf von **CreatePool** nicht den Parameter _targetDedicatedNodes_ oder den Parameter _targetLowPriorityNodes_ an. Geben Sie stattdessen die Eigenschaften **AutoScaleEnabled** und **AutoScaleFormula** im Pool an. Die Werte für diese Eigenschaften bestimmen die Zielanzahl der einzelnen Knotentypen.
>
> Sie müssen zur manuellen Anpassung der Größe eines Pools mit aktivierter automatischer Skalierung (etwa mit [BatchClient.PoolOperations.ResizePool](/dotnet/api/microsoft.azure.batch.pooloperations.resizepoolasync)) zunächst die automatische Skalierung im Pool deaktivieren, um anschließend die Größe des Pools ändern zu können.

### <a name="python"></a>Python

Um einen Pool mit aktivierter Autoskalierung mit dem Python SDK erstellen, gehen Sie folgendermaßen vor:

1. Erstellen Sie einen Pool, und geben Sie die Konfiguration an.
1. Fügen Sie den Pool dem Dienstclient hinzu.
1. Aktivieren Sie die Autoskalierung im Pool mit einer von Ihnen erstellten Formel.

Diese Schritte werden im folgenden Beispiel veranschaulicht.

```python
# Create a pool; specify configuration
new_pool = batch.models.PoolAddParameter(
    id="autoscale-enabled-pool",
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
          publisher="Canonical",
          offer="UbuntuServer",
          sku="18.04-LTS",
          version="latest"
            ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size="STANDARD_D1_v2",
    target_dedicated_nodes=0,
    target_low_priority_nodes=0
)
batch_service_client.pool.add(new_pool) # Add the pool to the service client

formula = """$curTime = time();
             $workHours = $curTime.hour >= 8 && $curTime.hour < 18;
             $isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
             $isWorkingWeekdayHour = $workHours && $isWeekday;
             $TargetDedicated = $isWorkingWeekdayHour ? 20:10;""";

# Enable autoscale; specify the formula
response = batch_service_client.pool.enable_auto_scale(pool_id, auto_scale_formula=formula,
                                            auto_scale_evaluation_interval=datetime.timedelta(minutes=10),
                                            pool_enable_auto_scale_options=None,
                                            custom_headers=None, raw=False)
```

> [!TIP]
> Weitere Beispiele für die Verwendung des Python SDK finden Sie auf GitHub im [Batch Python-Schnellstartrepository](https://github.com/Azure-Samples/batch-python-quickstart).

## <a name="enable-autoscaling-on-an-existing-pool"></a>Aktivieren der automatischen Skalierung für einen vorhandenen Pool

Jedes Batch-SDK bietet eine Möglichkeit für die Aktivierung der automatischen Skalierung. Beispiel:

- [BatchClient.PoolOperations.EnableAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.enableautoscaleasync) (Batch .NET)
- [Aktivieren des automatischen Skalierens für einen Pool](/rest/api/batchservice/enable-automatic-scaling-on-a-pool) (REST-API)

Beachten Sie Folgendes, wenn Sie automatische Skalierung für einen vorhandenen Pool aktivieren:

- Falls automatische Skalierung für den Pool derzeit deaktiviert ist, müssen Sie eine gültige Formel für automatische Skalierung angeben, wenn Sie die Anforderung ausgeben. Sie können optional ein Intervall für automatische Skalierung angeben. Wenn Sie kein Intervall angeben, wird der Standardwert von 15 Minuten verwendet.
- Wenn automatische Skalierung für den Pool derzeit aktiviert ist, können Sie eine neue Formel, ein neues Intervall oder beides angeben. Sie müssen mindestens eine dieser Eigenschaften angeben.
  - Wenn Sie ein neues Intervall für automatische Skalierung angeben, wird der vorhandene Zeitplan beendet und ein neuer Zeitplan gestartet. Die Startzeit des neuen Zeitplans ist der Zeitpunkt, zu dem die Anforderung zur Aktivierung der automatischen Skalierung ausgegeben wurde.
  - Wenn Sie die Formel oder das Intervall für automatische Skalierung auslassen, nutzt der Batch-Dienst weiterhin den aktuellen Wert dieser Einstellung.

> [!NOTE]
> Wenn Sie bei der Erstellung des Pools in .NET oder für vergleichbare Parameter in einer anderen Sprache Werte für den Parameter *targetDedicatedNodes* oder *targetLowPriorityNodes* der **CreatePool**-Methode angegeben haben, werden diese Werte bei der Auswertung der Formel für automatische Skalierung ignoriert.

In diesem C#-Beispiel wird die [Batch .NET](/dotnet/api/microsoft.azure.batch)-Bibliothek verwendet, um automatische Skalierung in einem vorhandenen Pool zu aktivieren.

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

Sie können eine Formel auswerten, bevor Sie sie auf einen Pool anwenden. Auf diese Weise können Sie die Ergebnisse der Formel testen, bevor Sie sie in der Produktion einsetzen.

Bevor Sie eine Formel für automatische Skalierung auswerten können, müssen Sie zunächst automatische Skalierung für den Pool mit einer gültigen Formel aktivieren, z. B. mit der einzeiligen Formel `$TargetDedicatedNodes = 0`. Verwenden Sie anschließend eines der folgenden Verfahren, um die zu testende Formel auszuwerten:

- [BatchClient.PoolOperations.EvaluateAutoScale](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscale) oder [EvaluateAutoScaleAsync](/dotnet/api/microsoft.azure.batch.pooloperations.evaluateautoscaleasync)

    Für diese Batch .NET-Methoden sind die ID eines vorhandenen Pools und eine Zeichenfolge mit der auszuwertenden autoscale-Formel erforderlich.

- [Auswerten einer Formel für die automatische Skalierung](/rest/api/batchservice/evaluate-an-automatic-scaling-formula)

    Geben Sie in dieser REST-API-Anforderung die Pool-ID im URI und die autoscale-Formel im *autoScaleFormula*-Element des Anforderungstexts an. Die bei der Anfrage generierte Antwort enthält Fehlerinformationen, die in Zusammenhang mit der Formel stehen können.

In diesem [Batch .NET](/dotnet/api/microsoft.azure.batch)-Beispiel wird eine Formel für automatische Skalierung ausgewertet. Wenn der Pool noch keine automatische Skalierung verwendet, aktivieren wir diese zuerst.

```csharp
// First obtain a reference to an existing pool
CloudPool pool = await batchClient.PoolOperations.GetPoolAsync("myExistingPool");

// If autoscaling isn't already enabled on the pool, enable it.
// You can't evaluate an autoscale formula on a non-autoscale-enabled pool.
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

Um sicherzustellen, dass die Formel wie erwartet funktioniert, sind regelmäßige Überprüfungen der Ergebnisse von Ausführungen der automatischen Skalierung ratsam, die von Batch für den Pool vorgenommen werden. Rufen Sie hierzu einen Verweis auf den Pool auf (oder aktualisieren Sie ihn), und untersuchen Sie dann die Eigenschaften der letzten Ausführung der automatischen Skalierung.

In Batch .NET verfügt die Eigenschaft [CloudPool.AutoScaleRun](/dotnet/api/microsoft.azure.batch.cloudpool.autoscalerun) über mehrere Eigenschaften, die Informationen zur letzten Ausführung der automatischen Skalierung für den Pool liefern:

- [AutoScaleRun.Timestamp](/dotnet/api/microsoft.azure.batch.autoscalerun.timestamp)
- [AutoScaleRun.Results](/dotnet/api/microsoft.azure.batch.autoscalerun.results)
- [AutoScaleRun.Error](/dotnet/api/microsoft.azure.batch.autoscalerun.error)

In der REST-API gibt die Anforderung zum [Abrufen von Informationen zu einem Pool](/rest/api/batchservice/get-information-about-a-pool) Informationen zum Pool zurück, z.B. zur letzten Ausführung der automatischen Skalierung in der Eigenschaft [autoScaleRun](/rest/api/batchservice/get-information-about-a-pool).

Im folgenden C#-Beispiel wird die Batch .NET-Bibliothek verwendet, um Informationen zur letzten Ausführung der automatischen Skalierung für den Pool _myPool_ auszugeben.

```csharp
await Cloud pool = myBatchClient.PoolOperations.GetPoolAsync("myPool");
Console.WriteLine("Last execution: " + pool.AutoScaleRun.Timestamp);
Console.WriteLine("Result:" + pool.AutoScaleRun.Results.Replace("$", "\n  $"));
Console.WriteLine("Error: " + pool.AutoScaleRun.Error);
```

Die Beispielausgabe aus dem vorherigen Beispiel lautet wie folgt:

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

## <a name="get-autoscale-run-history-using-pool-autoscale-events"></a>Abrufen des Ausführungsverlaufs für die Autoskalierung mithilfe von Ereignissen für die Poolautoskalierung
Sie können den Verlauf für die automatische Skalierung auch abrufen, indem Sie [PoolAutoScaleEvent](batch-pool-autoscale-event.md) abfragen. Dieses Ereignis wird vom Batch-Dienst ausgegeben, um jedes Vorkommen der Auswertung und Ausführung der Formel für die automatische Skalierung aufzuzeichnen. Dies kann bei der Behebung potenzieller Probleme hilfreich sein.

Beispielereignis für PoolAutoScaleEvent:
```json
{
    "id": "poolId",
    "timestamp": "2020-09-21T23:41:36.750Z",
    "formula": "...",
    "results": "$TargetDedicatedNodes=10;$NodeDeallocationOption=requeue;$curTime=2016-10-14T18:36:43.282Z;$isWeekday=1;$isWorkingWeekdayHour=0;$workHours=0",
    "error": {
        "code": "",
        "message": "",
        "values": []
    }
}
```

## <a name="example-autoscale-formulas"></a>Beispiele für autoscale-Formeln

Hier sind einige Formeln angegeben, die verschiedene Möglichkeiten zum Anpassen der Anzahl von Computeressourcen in einem Pool darstellen.

### <a name="example-1-time-based-adjustment"></a>Beispiel 1: Zeitbasierte Anpassung

Angenommen Sie möchten die Poolgröße basierend auf dem Wochentag und der Tageszeit anpassen. Dieses Beispiel zeigt, wie Sie die Knotenanzahl im Pool entsprechend Anzahl zum erhöhen oder verringern.

Die Formel ruft zunächst die aktuelle Uhrzeit ab. Wenn es sich um einen Werktag (1 bis 5) handelt und der Wert innerhalb der Geschäftszeiten (8:00 Uhr bis 18:00 Uhr) liegt, wird die Zielgröße des Pools auf 20 Knoten festgelegt. Andernfalls wird der Wert auf 10 Knoten festgelegt.

```
$curTime = time();
$workHours = $curTime.hour >= 8 && $curTime.hour < 18;
$isWeekday = $curTime.weekday >= 1 && $curTime.weekday <= 5;
$isWorkingWeekdayHour = $workHours && $isWeekday;
$TargetDedicatedNodes = $isWorkingWeekdayHour ? 20:10;
$NodeDeallocationOption = taskcompletion;
```

`$curTime` kann angepasst werden, um Ihre lokale Zeitzone widerzuspiegeln, indem `time()` zum Produkt aus `TimeZoneInterval_Hour` und Ihrer UTC-Abweichung hinzugefügt wird. Verwenden Sie z. B. `$curTime = time() + (-6 * TimeInterval_Hour);` für Mountain Daylight Time (MDT). Denken Sie daran, dass die Abweichung zu Beginn und Ende der Sommerzeit (falls zutreffend) angepasst werden müsste.

### <a name="example-2-task-based-adjustment"></a>Beispiel 2: Aufgabenbasierte Anpassung

In diesem C#-Beispiel wird die Größe des Pools basierend auf der Anzahl der Tasks in der Warteschlange angepasst. Wir haben sowohl Kommentare als auch Zeilenumbrüche in die Formelzeichenfolgen eingefügt.

```csharp
// Get pending tasks for the past 15 minutes.
$samples = $PendingTasks.GetSamplePercent(TimeInterval_Minute * 15);
// If we have fewer than 70 percent data points, we use the last sample point,
// otherwise we use the maximum of last sample point and the history average.
$tasks = $samples < 70 ? max(0,$PendingTasks.GetSample(1)) : max( $PendingTasks.GetSample(1), avg($PendingTasks.GetSample(TimeInterval_Minute * 15)));
// If number of pending tasks is not 0, set targetVM to pending tasks, otherwise
// half of current dedicated.
$targetVMs = $tasks > 0? $tasks:max(0, $TargetDedicatedNodes/2);
// The pool size is capped at 20, if target VM value is more than that, set it
// to 20. This value should be adjusted according to your use case.
$TargetDedicatedNodes = max(0, min($targetVMs, 20));
// Set node deallocation mode - let running tasks finish before removing a node
$NodeDeallocationOption = taskcompletion;
```

### <a name="example-3-accounting-for-parallel-tasks"></a>Beispiel 3: Berücksichtigung paralleler Aufgaben

In diesem C#-Beispiel wird die Poolgröße basierend auf der Anzahl von Tasks angepasst. Diese Formel berücksichtigt auch den für den Pool festgelegten Wert [TaskSlotsPerNode](/dotnet/api/microsoft.azure.batch.cloudpool.taskslotspernode). Dieser Ansatz ist besonders hilfreich, wenn in Ihrem Pool die [parallele Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert wurde.

```csharp
// Determine whether 70 percent of the samples have been recorded in the past
// 15 minutes; if not, use last sample
$samples = $ActiveTasks.GetSamplePercent(TimeInterval_Minute * 15);
$tasks = $samples < 70 ? max(0,$ActiveTasks.GetSample(1)) : max( $ActiveTasks.GetSample(1),avg($ActiveTasks.GetSample(TimeInterval_Minute * 15)));
// Set the number of nodes to add to one-fourth the number of active tasks
// (the TaskSlotsPerNode property on this pool is set to 4, adjust
// this number for your use case)
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

Dieses Beispiel zeigt einen C#-Beispiel mit einer Formel für automatische Skalierung, welche die Größe des Pools für einen anfänglichen Zeitraum auf eine angegebene Anzahl von Knoten festlegt. Danach wird die Poolgröße auf der Grundlage der Anzahl der aktuell ausgeführten und aktiven Tasks angepasst.

Diese Formel führt die folgenden Aufgaben aus:

- Die anfängliche Poolgröße wird auf 4 Knoten festgelegt.
- Die Größe des Pools wird innerhalb der ersten 10 Minuten des Lebenszyklus des Pools nicht angepasst.
- Nach 10 Minuten wird die maximale Anzahl ausgeführter und aktiver Aufgaben in den letzten 60 Minuten abgerufen.
  - Falls beide Werte 0 sind (d. h., dass in den letzten 60 Minuten keine Aufgaben ausgeführt wurden oder aktiv waren), wird die Poolgröße auf 0 festgelegt.
  - Wenn einer der Werte größer als null ist, erfolgt keine Änderung.

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

- Erfahren Sie, wie Sie [mehrere Tasks gleichzeitig auf den Computeknoten in Ihrem Pool](batch-parallel-node-tasks.md) ausführen. Zusammen mit automatischer Skalierung können Sie mit diesem Feature die Auftragsdauer für einige Workloads verringern und so Geld sparen.
- Erfahren Sie, wie Sie [den Azure Batch-Dienst effizient abfragen](batch-efficient-list-queries.md), um die Effizienz zu erhöhen.
