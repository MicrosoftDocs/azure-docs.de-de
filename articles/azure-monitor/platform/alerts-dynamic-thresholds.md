---
title: Erstellen von Warnungen mit dynamischen Schwellenwerten in Azure Monitor
description: Erstellen von Warnungen mit Machine Learning-basierten dynamischen Schwellenwerten
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 110d4a3219b4898fa6f138e29f1112d7134f674c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012338"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Metrikwarnungen mit dynamischen Schwellenwerten in Azure Monitor

Die Metrikwarnung mit Erkennung dynamischer Schwellenwerte nutzt erweitertes Machine Learning (ML), um das bisherige Verhalten von Metriken zu erfassen sowie Muster und Anomalien zu erkennen, die auf mögliche Probleme hinweisen. Benutzer profitieren von einer einfachen Benutzeroberfläche sowie von skalierbaren Vorgängen und können über die Azure Resource Manager-API vollständig automatisierte Warnungsregeln konfigurieren.

Wenn eine Warnungsregel erstellt wurde, wird diese nur ausgelöst, wenn die überwachte Metrik nicht wie erwartet reagiert (basierend auf individuellen Schwellenwerten).

Wir freuen uns auf Ihr Feedback: <azurealertsfeedback@microsoft.com>

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Warum und wann ist der dynamische Bedingungstyp empfehlenswert?

1. **Skalierbare Warnungen:** Warnungsregeln für den dynamischen Schwellenwert können individuelle Schwellenwerte für Hunderte von Metriken erstellen. Gleichzeitig kann eine Warnungsregel für eine einzelne Metrik ganz einfach definiert werden. Dadurch müssen Sie weniger Warnungen erstellen und verwalten. Zur Erstellung können Sie entweder das Azure-Portal oder die Azure Resource Manager-API verwenden. Der skalierbare Ansatz ist besonders hilfreich beim Umgang mit Metrikdimensionen oder bei der Anwendung auf mehrere Ressourcen, z. B. auf alle Abonnementressourcen.  [Erfahren Sie mehr über das Konfigurieren von Metrikwarnungen mit dynamischen Schwellenwerten mithilfe von Vorlagen](alerts-metric-create-templates.md).

1. **Intelligente Erkennung von Metrikmustern:** Mit unserer ML-Technologie können wir Metrikmuster automatisch erkennen und uns im Laufe der Zeit an Metrikänderungen anpassen, die häufig Saisonalität (stündlich/täglich/wöchentlich) enthalten. Durch die Anpassung an das Metrikverhalten und Warnungen basierend auf Abweichungen von Mustern müssen Sie nicht mehr den „richtigen“ Schwellenwert für die einzelnen Metriken ermitteln. Der ML-Algorithmus in dynamischen Schwellenwerten soll überflüssige (geringe Genauigkeit) oder zu weit gefasste (geringe Trefferquote) Schwellenwerte ohne erwartetes Muster verhindern.

1. **Intuitive Konfiguration:** Dynamische Schwellenwerte ermöglichen die Einrichtung von Metrikwarnungen mit allgemeinen Konzepten, ohne dass umfassendes Domänenwissen zur Metrik erforderlich ist.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Wie werden Warnungen mit dynamischen Schwellenwerten konfiguriert?

Warnungen mit dynamischen Schwellenwerten können über Metrikwarnungen in Azure Monitor konfiguriert werden. [Erfahren Sie mehr über die Konfiguration von Metrikwarnungen](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Wie werden die Schwellenwerte berechnet?

Dynamische Schwellenwerte erfassen fortlaufend Daten der Metrikreihe und versuchen, sie mit einem Satz von Algorithmen und Methoden zu modellieren. Sie erkennen Muster in den Daten, z.B. die Saisonalität (stündlich/täglich/wöchentlich), und können überflüssige Metriken (z.B. Geräte-CPU oder Arbeitsspeicher) sowie Metriken mit geringer Genauigkeit (z.B. die Verfügbarkeit und Fehler) verarbeiten.

Die Schwellenwerte werden so ausgewählt, dass eine Abweichung von diesen Schwellenwerten eine Anomalie im Metrikverhalten ergibt.

> [!NOTE]
> Saisonale Mustererkennung ist auf ein Stunden-, Tage- oder Wochenintervall festgelegt. Dies bedeutet, dass andere Muster wie zweistündige oder halbwöchentliche möglicherweise nicht erkannt werden.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Was bedeutet die „Sensitivity“-Einstellung in dynamischen Schwellenwerten?

Mit der Empfindlichkeit des Schwellenwerts legen Sie fest, bei welcher Abweichung vom Metrikverhalten eine Warnung ausgelöst werden soll.
Diese Option erfordert kein Domänenwissen zur Metrik, z.B. statische Schwellenwerte. Die verfügbaren Optionen sind:

- Hoch – Die Schwellenwerte sind streng und nah am Muster der Metrikreihe. Eine Warnungsregel wird schon bei der kleinsten Abweichung ausgelöst, sodass mehr Warnungen auftreten.
- Mittel – Weniger strenge und ausgewogenere Schwellenwerte, sodass weniger Warnungen auftreten als bei der hohen Empfindlichkeit (Standard).
- Niedrig – Die Schwellenwerte werden mit zunehmender Distanz zum Muster der Metrikreihe lockerer. Eine Warnungsregel wird nur bei großen Abweichungen ausgelöst, was zu weniger Warnungen führt.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Was bedeuten die „Operator“-Einstellungen in dynamischen Schwellenwerten?

Mit der Warnungsregel für dynamische Schwellwerte können Sie individuelle Schwellenwerte festlegen, die auf dem Metrikverhalten für die obere und untere Grenze mit der gleichen Warnungsregel basieren.
Sie können die Warnung bei einer der drei folgenden Bedingungen auslösen:

- Überschreitung des oberen Schwellenwerts oder Unterschreitung des unteren Schwellenwerts (Standardeinstellung)
- Überschreitung des oberen Schwellenwerts
- Unterschreitung des unteren Schwellenwerts

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Was bedeuten die erweiterten Einstellungen im dynamischen Schwellenwerten?

**Zeiträume, die Fehler ausweisen:** Mit dem dynamischen Schwellenwert können Sie die „Anzahl von Verstößen zum Auslösen der Warnung“ festlegen. Damit bestimmen Sie, ab wie vielen Abweichungen innerhalb eines bestimmten Zeitfensters das System eine Warnung auslöst (Das Zeitfenster ist standardmäßig auf vier Abweichungen in 20 Minuten festgelegt). Der Benutzer kann Zeiträume, die Fehler aufweisen, konfigurieren und die Warnungen auswählen, indem er die Zeiträume, die Fehler aufweisen, und das Zeitfenster auswählt. So reduzieren Sie überflüssige Warnungen als Reaktion auf vorübergehende Spitzen. Beispiel:

Mit den folgenden Einstellungen wird eine Warnung ausgelöst, wenn das Problem 20 Minuten lang (viermal hintereinander in einem Zeitraum von jeweils fünf Minuten) aufgetreten ist:

![Einstellungen für Zeiträume, die Fehler aufweisen, bei kontinuierlichen Problemen über einen Zeitraum von 20 Minuten, viermal hintereinander in einem Zeitraum von jeweils fünf Minuten](media/alerts-dynamic-thresholds/0008.png)

Mit den folgenden Einstellungen wird eine Warnung ausgelöst, wenn ein dynamischer Schwellenwert innerhalb der letzten 30 Minuten für 20 Minuten (Zeitraum von fünf Minuten) verletzt wurde:

![Einstellungen für Zeiträume, die Fehler aufweisen, über einen Zeitraum von 30 Minuten, mehrmals hintereinander hintereinander in einem Zeitraum von jeweils fünf Minuten](media/alerts-dynamic-thresholds/0009.png)

**Vor dem folgenden Datum liegende Daten ignorieren** – Benutzer können optional auch ein Startdatum definieren, ab dem das System mit der Berechnung der Schwellenwerte beginnen soll. Ein typischer Anwendungsfall kann auftreten, wenn eine Ressource in einem Testmodus ausgeführt wurde und nun höher gestuft wird, um eine Produktionsworkload zu verarbeiten. Dann sollte das Verhalten jeder beliebigen Metrik während der Testphase ignoriert werden.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Wie finden Sie heraus, warum eine „Dynamische Schwellenwerte“-Warnung ausgelöst wurde?

Um ausgelöste Warnungsinstanzen in der Warnungsansicht zu untersuchen, klicken Sie auf den Link in E-Mail, SMS oder Browser, um die Warnungsansicht im Azure-Portal anzuzeigen. Erfahren Sie mehr über die [Warnungsansicht](alerts-overview.md#alerts-experience).

Die Warnungsansicht zeigt Folgendes an:

- Alle Metrikendetails für den Zeitpunkt, zu dem die „Dynamische Schwellenwerte“-Warnung ausgelöst wurde.
- Ein Diagramm des Zeitraums, in dem die Warnung ausgelöst wurde, inklusive der zu diesem Zeitpunkt verwendeten dynamischen Schwellenwerte.
- Die Möglichkeit, Feedback zur „Dynamische Schwellenwerte“-Warnung und der Warnungsansicht-Benutzeroberfläche zu geben, um ggf. zukünftige Erkennungen zu verbessern.

## <a name="will-slow-behavior-changes-in-the-metric-trigger-an-alert"></a>Wird bei langsamen Verhaltensveränderungen der Metrik eine Warnung ausgelöst?

Wahrscheinlich nicht. Dynamische Schwellenwerte eignen sich besser für die Erkennung erheblicher Abweichungen und weniger für Probleme, die sich langsam entwickeln.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Wie viele Daten werden für die Vorschau und die anschließende Berechnung der Schwellenwerte verwendet?

Wenn eine Warnungsregel zum ersten Mal erstellt wird, werden die im Diagramm angezeigten Schwellenwerte basierend auf genügend Verlaufsdaten berechnet, um stündliche oder tägliche Saisonmuster (10 Tage) zu berechnen. Sobald eine Warnungsregel erstellt wurde, verwenden die dynamischen Schwellenwerte alle benötigten Verlaufsdaten, die verfügbar sind, und lernen und passen sich kontinuierlich basierend auf neuen Daten an, um die Schwellenwerte genauer zu gestalten. Dies bedeutet, dass nach diesem Berechnungsdiagramm auch wöchentliche Muster angezeigt werden.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Wie viele Daten sind zum Auslösen einer Warnung erforderlich?

Wenn Sie über eine neue Ressource verfügen oder Metrikdaten fehlen, lösen dynamische Schwellenwerte keine Warnungen aus, bevor die Daten für drei Tage und mindestens 30 Stichproben von metrischen Daten verfügbar sind, um genaue Schwellenwerte sicherzustellen.
Für vorhandene Ressourcen mit ausreichenden Metrikdaten können dynamische Schwellenwerte sofort Warnungen auslösen.

## <a name="dynamic-thresholds-best-practices"></a>Bewährte Methoden für dynamische Schwellenwerte

Dynamische Schwellenwerte können auf die meisten Plattform- und benutzerdefinierten Metriken in Azure Monitor angewendet werden. Darüber hinaus wurde die Funktion auch für die gängigen Anwendungs- und Infrastrukturmetriken optimiert.
In den folgenden bewährten Methoden wird gezeigt, wie Sie für einige dieser Metriken Warnungen mit dynamischen Schwellenwerten konfigurieren.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamische Schwellenwerte für prozentuale VM-CPU-Metriken

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Überwachen**. In der Ansicht „Überwachen“ sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.

2. Klicken Sie auf **Warnungen** und dann auf **+ Neue Warnungsregel**.

    > [!TIP]
    > Die meisten Ressourcenblätter verfügen auch über **Warnungen** in ihrem Ressourcenmenü (unter **Überwachung**). Sie können auch von dort aus Benachrichtigungen erstellen.

3. Klicken Sie auf **Ziel auswählen** und wählen Sie im geladenen Kontextbereich eine Zielressource aus, für die Sie eine Warnungsregel erstellen möchten. Verwenden Sie die Dropdownlisten **Abonnement** und **Ressourcentyp „Virtual Machines“** , um die zu überwachende Ressource zu finden. Sie können auch die Suchleiste verwenden, um Ihre Ressource zu finden.

4. Nachdem Sie eine Zielressource ausgewählt haben, klicken Sie auf **Bedingung hinzufügen**.

5. Wählen Sie **CPU-Prozentsatz** aus.

6. Optimieren Sie die Metrik optional durch Anpassen von **Zeitraum** und **Aggregation**. Es empfiehlt sich, für diesen Metriktyp nicht den Aggregationstyp „Maximum“ zu verwenden, da er für das Verhalten weniger repräsentativ ist. Für den Aggregationstyp „Maximum“ ist ggf. ein statischer Schwellenwert besser geeignet.

7. Es wird ein Diagramm für die Metrik für die letzten sechs Stunden angezeigt. Definieren Sie die Warnungsparameter:
    1. **Bedingungstyp:** Wählen Sie die Option „Dynamisch“ aus.
    1. **Empfindlichkeit:** Wählen Sie eine mittlere/niedrige Empfindlichkeit aus, um überflüssige Warnungen zu verringern.
    1. **Operator:** Wählen Sie „Größer als“ aus, es sei denn, das Verhalten stellt die Anwendungsnutzung dar.
    1. **Häufigkeit:** Verwenden Sie abhängig von den geschäftlichen Auswirkungen der Warnung ggf. einen niedrigeren Wert.
    1. **Failing Periods** (Zeiträume mit Fehler) (erweiterte Option): Das zurückliegende Zeitfenster muss mindestens 15 Minuten betragen. Ist der Zeitraum also beispielsweise auf fünf Minuten festgelegt, müssen die Zeiträume mit Fehler mindestens auf „3“ festgelegt werden.

8. Das Metrikdiagramm zeigt die berechneten Schwellenwerte basierend auf aktuellen Daten an.

9. Klicken Sie auf **Fertig**.

10. Geben Sie **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.

11. Fügen Sie der Warnung eine Aktionsgruppe hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.

12. Klicken Sie auf **Fertig**, um die Metrikwarnungsregel zu speichern.

> [!NOTE]
> Über das Portal erstellte Metrikwarnungsregeln werden in derselben Ressourcengruppe erstellt wie die Zielressource.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Dynamische Schwellenwerte für die Ausführungszeit der HTTP-Anforderung von Application Insights

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Überwachen**. In der Ansicht „Überwachen“ sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.

2. Klicken Sie auf **Warnungen** und dann auf **+ Neue Warnungsregel**.

    > [!TIP]
    > Die meisten Ressourcenblätter verfügen auch über **Warnungen** in ihrem Ressourcenmenü (unter **Überwachung**). Sie können auch von dort aus Benachrichtigungen erstellen.

3. Klicken Sie auf **Ziel auswählen** und wählen Sie im geladenen Kontextbereich eine Zielressource aus, für die Sie eine Warnungsregel erstellen möchten. Verwenden Sie die Dropdownlisten **Abonnement** und **Ressourcentyp „Application Insights“** , um die zu überwachende Ressource zu finden. Sie können auch die Suchleiste verwenden, um Ihre Ressource zu finden.

4. Nachdem Sie eine Zielressource ausgewählt haben, klicken Sie auf **Bedingung hinzufügen**.

5. Wählen Sie die **Ausführungszeit der HTTP-Anforderung** aus.

6. Optimieren Sie die Metrik optional durch Anpassen von **Zeitraum** und **Aggregation**. Es empfiehlt sich, für diesen Metriktyp nicht den Aggregationstyp „Maximum“ zu verwenden, da er für das Verhalten weniger repräsentativ ist. Für den Aggregationstyp „Maximum“ ist ggf. ein statischer Schwellenwert besser geeignet.

7. Es wird ein Diagramm für die Metrik für die letzten sechs Stunden angezeigt. Definieren Sie die Warnungsparameter:
    1. **Bedingungstyp:** Wählen Sie die Option „Dynamisch“ aus.
    1. **Operator:** Wählen Sie „Größer als“ aus, um die Menge an ausgelösten Warnungen zur Optimierung der Dauer zu verringern.
    1. **Häufigkeit:** Verwenden Sie abhängig von den geschäftlichen Auswirkungen der Warnung ggf. einen niedrigeren Wert.

8. Das Metrikdiagramm zeigt die berechneten Schwellenwerte basierend auf aktuellen Daten an.

9. Klicken Sie auf **Fertig**.

10. Geben Sie **Warnungsdetails** wie **Warnungsregelname**, **Beschreibung** und **Schweregrad** ein.

11. Fügen Sie der Warnung eine Aktionsgruppe hinzu, indem Sie entweder eine bestehende Aktionsgruppe auswählen oder eine neue Aktionsgruppe erstellen.

12. Klicken Sie auf **Fertig**, um die Metrikwarnungsregel zu speichern.

> [!NOTE]
> Über das Portal erstellte Metrikwarnungsregeln werden in derselben Ressourcengruppe erstellt wie die Zielressource.

## <a name="interpreting-dynamic-threshold-charts"></a>Interpretieren dynamischer Schwellenwertdiagramme

Im Folgenden finden Sie ein Diagramm, in dem eine Metrik, ihre dynamischen Schwellenwertgrenzwerte und einige ausgelöste Warnungen dargestellt werden, wenn der Wert außerhalb der zulässigen Schwellenwerte liegt.

![Weiteres über die Konfiguration von Metrikwarnungen](media/alerts-dynamic-thresholds/threshold-picture-8bit.png)

Verwenden Sie die folgenden Informationen zur Interpretation des oben stehenden Diagramms.

- **Blaue Linie:** Diese zeigt die über die Zeit aktuell gemessene Metrik an.
- **Blau schattierte Bereiche:** Diese zeigen den zulässigen Bereich für die Metrik an. Solange diese Metrikwerte innerhalb dieses Bereichs liegen, wird keine Warnung ausgegeben.
- **Blaue Punkte:** Wenn Sie im linken Teil des Diagramms klicken und dann mit der Maus über die blaue Linie fahren, erscheint ein blauer Punkt unter Ihrem Cursor, der einen individuellen aggregierten Metrikwert anzeigt.
- **Popupfenster mit blauem Punkt:** In diesem Fenster werden der gemessene Metrikwert (der blaue Punkt) sowie die oberen und unteren Werte des zulässigen Bereichs angezeigt.  
- **Roter Punkt mit einer schwarzen Umrandung:** Dieser Punkt zeigt den ersten Metrikwert des zulässigen Bereichs an. Dieser Wert löst eine Metrikwarnung aus und versetzt diese in einen aktiven Zustand.
- **Rote Punkte:** Diese Punkte geben die zusätzlich gemessenen Werte außerhalb des zulässigen Bereichs an. Die Werte lösen keine zusätzlichen Metrikwarnungen aus, die Warnung bleibt jedoch aktiv.
- **Roter Bereich:** Dieser Bereich zeigt die Zeit an, in der der Metrikwert außerhalb des zulässigen Bereichs lag. Die Warnung bleibt aktiv, solange nachfolgende Messwerte außerhalb des zulässigen Bereichs liegen, aber keine neuen Warnungen ausgelöst werden.
- **Ende des roten Bereichs:** Wenn die blaue Linie wieder im zulässigen Bereich liegt, endet der rote Bereich und die gemessene Wertelinie wird blau. Der Status der Metrikwarnung, die zum Zeitpunkt des roten Punkts mit einer schwarzen Umrandung ausgelöst wurde, wird gelöst festgelegt. 
