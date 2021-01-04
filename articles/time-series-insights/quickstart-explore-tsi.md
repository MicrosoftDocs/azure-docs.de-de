---
title: 'Schnellstart: Erkunden der Gen2-Demoumgebung – Azure Time Series Insights Gen2 | Microsoft-Dokumentation'
description: Erkunden Sie wichtige Features der Azure Time Series Insights Gen2-Demoumgebung.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 10/02/2020
ms.openlocfilehash: 3956f8d91793367f7200d2f349a8713aff3d2f59
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016223"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Schnellstart: Erkunden der Azure Time Series Insights Gen2-Demoumgebung

Dieser Schnellstart behandelt die ersten Schritte mit einer Azure Time Series Insights Gen2-Umgebung. In der kostenlosen Demo sehen Sie die wichtigsten Funktionen, die zu Azure Time Series Insights Gen2 hinzugefügt wurden.

Die Azure Time Series Insights Gen2-Demoumgebung enthält das Szenariounternehmen Contoso, das zwei Windparks mit jeweils 10 Turbinen betreibt. Jede Turbine verfügt über 20 Sensoren, die jede Minute Daten an Azure IoT Hub melden. Die Sensoren erfassen Informationen zu Witterungsbedingungen, Schaufelteilung und Gierposition sowie Generatorleistung, Getriebeverhalten und Sicherheitsüberwachung und zeichnen diese Daten auf.

In diesem Schnellstart lernen Sie die Verwendung von Azure Time Series Insights Gen2 kennen, um handlungsrelevante Erkenntnisse aus Contoso-Daten zu gewinnen. Sie führen außerdem eine kurze Ursachenanalyse durch, um kritische Ausfälle besser vorherzusagen und Wartungsarbeiten durchzuführen.

> [!IMPORTANT]
> Erstellen Sie ein  [kostenloses Azure-Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , sofern nicht bereits geschehen.

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Erkunden des Azure Time Series Insights Gen2-Explorers in einer Demoumgebung

Der Azure Time Series Insights Gen2-Explorer zeigt Verlaufsdaten und Ursachenanalysen. Erste Schritte:

1. Navigieren Sie zur Demoumgebung  [Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples).  

1. Melden Sie sich bei Aufforderung mit Ihren Azure-Anmeldeinformationen beim Azure Time Series Insights Gen2-Explorer an.

## <a name="work-with-historical-data"></a>Arbeiten mit Verlaufsdaten

1. Sehen Sie sich die Windturbine **W7** in **Contoso Plant 1** an.  

   1. Ändern Sie den Anzeigebereich in **1/1/17 20:00:00.00 bis 3/10/17 20:00:00.00 (UTC)** .
   1. Wählen Sie den Sensor **Contoso Plant 1** > **W7** > **Generator System** > **GeneratorSpeed** aus. Überprüfen Sie anschließend die angezeigten Werte.

      [![W7 in Contoso Plant 1](media/v2-update-quickstart/quick-start-generator-speed.png)](media/v2-update-quickstart/quick-start-generator-speed.png#lightbox)

1. Vor Kurzem hat Contoso einen Brand in der Windturbine **W7** erkannt. Die Meinungen bezüglich der Brandursache gehen auseinander. In Azure Time Series Insights Gen2 wird der Sensor für den Feueralarm angezeigt, der während des Brands aktiviert wurde.

   1. Ändern Sie den Anzeigebereich in **3/9/17 20:00:00.00 bis 3/10/17 20:00:00.00 (UTC)** .
   1. Wählen Sie den Sensor **Safety System** > **FireAlert** aus.

      [![Contoso hat einen Brand in der Windturbine W7 erkannt.](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Überprüfen Sie andere Ereignisse um den Zeitpunkt des Brandes, um zu verstehen, was passiert ist. Sowohl der Öldruck als auch die aktiven Warnungen stiegen kurz vor dem Brand an.

   1. Wählen Sie **Pitch System** > **HydraulicOilPressure** aus.
   1. Wählen Sie **Pitch System** > **ActiveWarning** aus.

      [![Überprüfen anderer Ereignisse zur gleichen Zeit](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Die Sensoren für Öldruck und aktive Warnungen stiegen direkt vor dem Brand stark an. Erweitern Sie die angezeigte Zeitreihe, um weitere Anzeichen im Vorfeld des Brandes anzuzeigen. Die Werte für beide Sensoren schwankten im Laufe der Zeit. Die Schwankungen deuten auf ein anhaltendes und besorgniserregendes Muster hin.

    * Ändern Sie den Anzeigebereich in **2/24/17 20:00:00.00 bis 3/10/17 20:00:00.00 (UTC)** .

      [![Sensoren für Öldruck und aktive Warnungen stiegen auch.](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. Eine Untersuchung der Verlaufsdaten aus zwei Jahren ergibt, dass es schon einmal einen Brand gab, bei dem die gleichen Sensorschwankungen aufgetreten sind.

    * Ändern Sie den Anzeigebereich in **1/1/16 bis 12/31/17** (alle Daten).

      [![Suchen nach Verlaufsmustern](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Mithilfe von Azure Time Series Insights Gen2 und Sensortelemetriedaten haben wir einen langfristigen Trend erkannt, der sich anhand der Verlaufsdaten zeigt. Mit diesen neuen Erkenntnissen haben wir folgende Möglichkeiten:

* Erklären, was tatsächlich passiert ist
* Beheben des Problems
* Implementieren besserer Warnbenachrichtigungssysteme.

## <a name="root-cause-analysis"></a>Analyse der Grundursache

1. Einige Szenarien erfordern eine anspruchsvolle Analyse, um Hinweise in Daten aufzudecken. Wählen Sie Windrad **W6** am **25.6.** aus.

    1. Ändern Sie den Anzeigebereich in **6/1/17 20:00:00.00 bis 7/1/17 20:00:00.00 (UTC)** .
    1. Wählen Sie dann **Contoso Plant 1** > **W6** > **Safety System** > **VoltageActuatorSwitchWarning** aus.

       [![Anzeigebereich ändern und W6 auswählen](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Mit der Warnung wird auf ein Problem mit der Spannung des Generators hingewiesen. Die Gesamtleistung des Generators liegt im aktuellen Intervall innerhalb normaler Parameter. Bei einer Erhöhung unseres Intervalls zeigt sich ein weiteres Muster: Es wird ein Rückgang angezeigt.

    1. Entfernen Sie den Sensor **VoltageActuatorSwitchWarning**.
    1. Wählen Sie **Generator System** > **ActivePower** aus.
    1. Ändern Sie das Intervall in **3d**.

       [![Intervall in 3d ändern](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Durch die Erweiterung des Zeitbereichs können wir feststellen, ob das Problem behoben wurde oder ob es weiterhin besteht.

    * Erweitern Sie die Zeitspanne auf 60 Tage.

      [![Erweitern der Zeitspanne auf 60 Tage](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Es können weitere Sensordatenpunkte hinzugefügt werden, um den Kontext zu erweitern. Je mehr Sensoren wir anzeigen, desto vollständiger ist unser Verständnis des Problems. Wir setzen einen Marker, um die tatsächlichen Werte anzuzeigen.

    1. Wählen Sie **Generator System** und anschließend diese drei Sensoren aus: **GridVoltagePhase1**, **GridVoltagePhase2** und **GridVoltagePhase3**.
    1. Setzen Sie einen Marker für den letzten Datenpunkt im sichtbaren Bereich.

       [![Setzen eines Markers](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Zwei der drei Spannungssensoren arbeiten vergleichbar und innerhalb normaler Parameter. Es sieht so aus, als wäre Sensor **GridVoltagePhase3** die Ursache.

1. Aufgrund der hinzugefügten stark kontextbezogenen Daten ist es noch wahrscheinlicher, dass der Rückgang in Phase 3 das Problem ist. Nun verfügen wir über einen guten Anhaltspunkt für die Ursache der Warnung. Wir können die Informationen zum Problem jetzt an unser Wartungsteam weitergeben.  

    * Ändern Sie die Anzeige, um für alle Sensoren unter **Generator System** denselben Diagrammmaßstab zu verwenden.

      [![Anzeige ändern, um alles einzuschließen](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie nach dem Schnellstart die von Ihnen erstellten Ressourcen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite **Alle Ressourcen** aus, und suchen Sie Ihre Azure Time Series Insights Gen2-Ressourcengruppe.
1. Löschen Sie entweder die gesamte Ressourcengruppe (und alle darin enthaltenen Ressourcen), indem Sie auf **Löschen** klicken, oder entfernen Sie die Ressourcen einzeln.

## <a name="next-steps"></a>Nächste Schritte

Sie sind jetzt dazu bereit, Ihre eigene Azure Time Series Insights Gen2-Umgebung zu erstellen. Für den Einstieg:

> [!div class="nextstepaction"]
> [Planen Ihrer Azure Time Series Insights Gen2-Umgebung](./how-to-plan-your-environment.md)

Informationen zur Verwendung der Demoumgebung und ihrer Funktionen:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2-Explorer](./concepts-ux-panels.md)