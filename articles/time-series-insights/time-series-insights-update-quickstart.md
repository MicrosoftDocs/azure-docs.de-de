---
title: 'Schnellstart: Erkunden der Azure Time Series Insights-Demoumgebung (Vorschauversion) | Microsoft-Dokumentation'
description: Machen Sie sich mit der Azure Time Series Insights-Demoumgebung (Vorschauversion) vertraut.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: 0bbab2acc9bf9e22e1d3c36336aa9dad04b0b73a
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688490"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Schnellstart: Erkunden der Azure Time Series Insights-Demoumgebung (Vorschauversion)

Dieser Schnellstart behandelt die ersten Schritte mit der Azure Time Series Insights-Umgebung (Vorschauversion). In der kostenlosen Demo sehen Sie die wichtigsten Funktionen, die in der Vorschauversion von Time Series Insights hinzugefügt wurden.

Die Demoumgebung der Vorschauversion enthält das Szenariounternehmen Contoso, das zwei Windparks mit jeweils 10 Turbinen betreibt. Jede Turbine verfügt über 20 Sensoren, die jede Minute Daten an Azure IoT Hub melden. Die Sensoren erfassen Informationen zu Witterungsbedingungen, Schaufelteilung und Gierposition sowie Generatorleistung, Getriebeverhalten und Sicherheitsüberwachung und zeichnen diese Daten auf.

In diesem Schnellstart lernen Sie die Verwendung von Time Series Insights kennen, um handlungsrelevante Erkenntnisse aus Contoso-Daten zu gewinnen. Sie führen außerdem eine kurze Ursachenanalyse durch, um kritische Ausfälle besser vorherzusagen und Wartungsarbeiten durchzuführen.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Erkunden des Time Series Insights-Explorers in einer Demoumgebung

Der Time Series Insights-Explorer (Vorschauversion) zeigt Verlaufsdaten und Ursachenanalysen. Erste Schritte:

1. Erstellen Sie ein  [kostenloses Azure-Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , sofern nicht bereits geschehen.

1. Navigieren Sie zur Demoumgebung  [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples).  

1. Melden Sie sich bei Aufforderung mit Ihren Azure-Anmeldeinformationen beim Time Series Insights-Explorer an.

## <a name="work-with-historical-data"></a>Arbeiten mit Verlaufsdaten

1. Sehen Sie sich die Windturbine **W7** in **Contoso Plant 1** an.  

   1. Ändern Sie den Anzeigebereich in **1/1/17 20:00 bis 3/10/17 20:00 (UTC)** .
   1. Wählen Sie den Sensor **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed** aus. Überprüfen Sie anschließend die angezeigten Werte.

      [![W7 in Contoso Plant 1](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Vor Kurzem hat Contoso einen Brand in der Windturbine **W7** erkannt. Die Meinungen bezüglich der Brandursache gehen auseinander. In Time Series Insights ist erkennbar, dass während des Brands der Sensor für den Feueralarm aktiviert wurde.

   1. Ändern Sie den Anzeigebereich in **3/9/17 20:00 bis 3/10/17 20:00 (UTC)** .
   1. Wählen Sie den Sensor **Safety System** > **FireAlert** aus.

      [![Contoso hat einen Brand in der Windturbine W7 erkannt.](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Überprüfen Sie andere Ereignisse um den Zeitpunkt des Brandes, um zu verstehen, was passiert ist. Sowohl der Öldruck als auch die aktiven Warnungen stiegen kurz vor dem Brand an.

   1. Wählen Sie **Pitch System** > **HydraulicOilPressure** aus.
   1. Wählen Sie **Pitch System** > **ActiveWarning** aus.

      [![Überprüfen anderer Ereignisse zur gleichen Zeit](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Die Sensoren für Öldruck und aktive Warnungen stiegen direkt vor dem Brand stark an. Erweitern Sie die angezeigte Zeitreihe, um weitere Anzeichen im Vorfeld des Brandes anzuzeigen. Die Werte für beide Sensoren schwankten im Laufe der Zeit. Die Schwankungen deuten auf ein anhaltendes und besorgniserregendes Muster hin.

    * Ändern Sie den Anzeigebereich in **2/24/17 20:00 bis 3/10/17 20:00 (UTC)** .

      [![Sensoren für Öldruck und aktive Warnungen stiegen auch.](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. Eine Untersuchung der Verlaufsdaten aus zwei Jahren ergibt, dass es schon einmal einen Brand gab, bei dem die gleichen Sensorschwankungen aufgetreten sind.

    * Ändern Sie den Anzeigebereich in **1/1/16 bis 12/31/17** (alle Daten).

      [![Suchen nach Verlaufsmustern](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Mithilfe von Time Series Insights und unseren Sensortelemetriedaten haben wir einen langfristigen und problematischen Trend erkannt, der sich anhand der Verlaufsdaten zeigt. Mit diesen neuen Erkenntnissen haben wir folgende Möglichkeiten:

> [!div class="checklist"]
> * Erklären, was tatsächlich passiert ist
> * Beheben des Problems
> * Implementieren besserer Warnbenachrichtigungssysteme

## <a name="root-cause-analysis"></a>Analyse der Grundursache

1. Einige Szenarien erfordern eine anspruchsvolle Analyse, um subtile Hinweise in Daten aufzudecken. Wählen Sie Windrad **W6** am **25.6.** aus.

    1. Ändern Sie den Anzeigebereich in **6/1/17 20:00 bis 7/1/17 20:00 (UTC)** .
    1. Wählen Sie dann **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning** aus.

       [![Anzeigebereich ändern und W6 auswählen](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. Mit der Warnung wird auf ein Problem mit der Ausgangsspannung des Generators hingewiesen. Die Gesamtleistung des Generators liegt im aktuellen Intervall innerhalb normaler Parameter. Bei einer Erhöhung unseres Intervalls zeigt sich ein weiteres Muster: Es wird ein deutlicher Rückgang angezeigt.

    1. Entfernen Sie den Sensor **VoltageActuatorSwitchWarning**.
    1. Wählen Sie **Generator System** > **ActivePower** aus.
    1. Ändern Sie das Intervall in **3d**.

       [![Intervall in 3d ändern](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Durch die Erweiterung des Zeitbereichs können wir feststellen, ob das Problem behoben wurde oder ob es weiterhin besteht.

    * Erweitern Sie die Zeitspanne auf 60 Tage.

      [![Erweitern der Zeitspanne auf 60 Tage](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Es können weitere Sensordatenpunkte hinzugefügt werden, um den Kontext zu erweitern. Je mehr Sensoren wir anzeigen, desto vollständiger ist unser Verständnis des Problems. Wir setzen einen Marker, um die tatsächlichen Werte anzuzeigen. 

    1. Wählen Sie **Generator System** und anschließend diese drei Sensoren aus: **GridVoltagePhase1**, **GridVoltagePhase2** und **GridVoltagePhase3**.
    1. Setzen Sie einen Marker für den letzten Datenpunkt im sichtbaren Bereich.

       [![Setzen eines Markers](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Zwei der drei Spannungssensoren arbeiten vergleichbar und innerhalb normaler Parameter. Es sieht so aus, als wäre Sensor **GridVoltagePhase3** die Ursache.

1. Aufgrund der hinzugefügten stark kontextbezogenen Daten ist es noch wahrscheinlicher, dass der Rückgang in Phase 3 das Problem ist. Nun verfügen wir über einen guten Anhaltspunkt für die Ursache der Warnung. Wir können die Informationen zum Problem jetzt an unser Wartungsteam weitergeben.  

    * Ändern Sie die Anzeige, um für alle Sensoren unter **Generator System** denselben Diagrammmaßstab zu verwenden.

      [![Anzeige ändern, um alles einzuschließen](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

Sie sind jetzt dazu bereit, Ihre eigene Time Series Insights-Umgebung (Vorschauversion) zu erstellen. Für den Einstieg:

> [!div class="nextstepaction"]
> [Plan your Time Series Insights Preview environment](time-series-insights-update-plan.md) (Planen Ihrer Time Series Insights-Umgebung (Vorschauversion))

Informationen zur Navigation der Demoumgebung und ihren Funktionen:

> [!div class="nextstepaction"]
> [Visualisieren von Daten in der Explorer Preview](time-series-insights-update-explorer.md)
