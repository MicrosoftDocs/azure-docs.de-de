---
title: 'Tutorial: Verwenden von Gerätegruppen in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation'
description: 'Tutorial: Hier erfahren Sie, wie Sie als Operator mithilfe von Gerätegruppen Telemetriedaten von Geräten in Ihrer Azure IoT Central-Anwendung analysieren.'
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 8c26afc9cf9630f6d26ddc76759393a6ea1a8696
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990272"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Tutorial: Verwenden von Gerätegruppen zum Analysieren von Gerätetelemetriedaten

In diesem Artikel wird beschrieben, wie Sie als Operator Gerätegruppen zum Analysieren von Gerätetelemetriedaten in Ihrer Azure IoT Central-Anwendung verwenden.

Eine Gerätegruppe ist eine Liste der Geräte, die gruppiert werden, weil sie einigen der angegebenen Kriterien entsprechen. Gerätegruppen unterstützen Sie bei der Verwaltung, Visualisierung und Analyse aller im Umfang enthaltenen Geräte, indem Geräte in kleineren, logischen Gruppen gruppiert werden. Sie können beispielsweise eine Gerätegruppe zum Auflisten aller Klimageräte in Seattle erstellen, damit ein Techniker alle Geräte finden kann, für die er zuständig ist.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Gerätegruppe
> * Verwenden einer Gerätegruppe zum Analysieren von Gerätetelemetriedaten

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie zunächst mithilfe der Schnellstartanleitungen [Erstellen einer Azure IoT Central-Anwendung](./quick-deploy-iot-central.md) und [Hinzufügen eines simulierten Geräts zu Ihrer IoT Central-Anwendung](./quick-create-simulated-device.md) die Gerätevorlage **Sensor Controller** (Sensorcontroller), um sie hier verwenden zu können.

## <a name="create-simulated-devices"></a>Erstellen von simulierten Geräten

Fügen Sie vor dem Erstellen einer Gerätegruppe mindestens fünf simulierte Geräte auf der Grundlage der Gerätevorlage **Sensor Controller** hinzu, um sie in diesem Tutorial zu verwenden:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Screenshot: Fünf simulierte Geräte vom Typ „Sensor Controller“":::

Verwenden Sie bei vier der simulierten Sensorgeräte die Ansicht **Gerät verwalten**, um den Kundennamen auf *Contoso* festzulegen:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Screenshot: Festlegen der Cloudeigenschaft „Kundenname“":::

## <a name="create-a-device-group"></a>Erstellen einer Gerätegruppe

Gehen Sie wie folgt vor, um eine Gerätegruppe zu erstellen:

1. Wählen Sie im linken Bereich **Gerätegruppen** aus.

1. Wählen Sie **+ Neu** aus.

1. Nennen Sie Ihre Gerätegruppe *Contoso devices* (Contoso-Geräte). Sie können auch eine Beschreibung hinzufügen. Eine Gerätegruppe kann nur Geräte aus einer einzigen Gerätevorlage enthalten. Wählen Sie für diese Gruppe die Gerätevorlage **Sensor Controller** aus.

1. Wenn Sie die Gruppe so anpassen möchten, dass sie nur die zu **Contoso** gehörenden Geräte enthält, wählen Sie **+ Filter** aus. Wählen Sie die Eigenschaft **Kundenname**, den Vergleichsoperator **Ist gleich** und **Contoso** als Wert aus. Sie können mehrere Filter hinzufügen. Dann werden Geräte, die **alle** Filterkriterien erfüllen, in die Gerätegruppe aufgenommen. Die von Ihnen erstellte Gerätegruppe ist für jeden zugänglich, der Zugriff auf die Anwendung hat, sodass jeder die Gerätegruppe anzeigen, ändern oder löschen kann.

    > [!TIP]
    > Die Gerätegruppe ist eine dynamische Abfrage. Jedes Mal, wenn Sie die Liste der Geräte anzeigen, werden möglicherweise andere Geräte in der Liste angezeigt. Die Liste hängt davon ab, welche Geräte derzeit die Kriterien der Abfrage erfüllen.

1. Wählen Sie **Speichern** aus.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Screenshot: Konfiguration der Gerätegruppenabfrage":::

> [!NOTE]
> Wählen Sie für „Azure IoT Edge-Geräte“ die Option „Azure IoT Edge-Vorlagen“ aus, um eine Gerätegruppe zu erstellen.

## <a name="analytics"></a>Analytics

Sie können **Analytics** bei einer Gerätegruppe verwenden, um die Telemetriedaten aus den Geräten in der Gruppe zu analysieren. Beispielsweise können Sie die durchschnittliche Temperatur darstellen, die von allen Contoso-Umgebungssensoren gemeldet wird.

So analysieren Sie die Telemetriedaten für eine Gerätegruppe:

1. Wählen Sie im linken Bereich **Analytics** aus.

1. Wählen Sie in der von Ihnen erstellten Gerätegruppe **Contoso-Geräte** aus. Fügen Sie dann die Telemetrietypen **Temperatur** und **Luftfeuchtigkeit** hinzu:

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Screenshot: Für die Analyse ausgewählte Telemetrietypen":::

    Verwenden Sie die Zahnradsymbole neben den Telemetrietypen, um einen Aggregationstyp auszuwählen. Der Standardwert ist **Durchschnitt**. Verwenden Sie **Gruppieren nach**, um die Darstellung der Aggregatdaten zu ändern. Sollten Sie z. B. nach „Geräte-ID“ aufteilen, wird für jedes Gerät ein Plot angezeigt, wenn Sie **Analysieren** auswählen.

1. Wählen Sie **Analysieren** aus, um die durchschnittlichen Telemetriewerte anzuzeigen:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Screenshot: Durchschnittswerte für alle Contoso-Geräte":::

    Sie können die Ansicht anpassen, den angezeigten Zeitraum ändern und die Daten exportieren.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Gerätegruppen in Ihrer Azure IoT Central-Anwendung verwenden, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Erstellen von Telemetrieregeln](tutorial-create-telemetry-rules.md)
