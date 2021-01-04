---
title: Konfigurieren Ihres Dashboards in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie als Ersteller das Standarddashboard Ihrer Azure IoT Central-Anwendung mit Kacheln konfigurieren.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 2ea2dd18cd5816c7c1406ac201421a2983e431a3
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491997"
---
# <a name="configure-the-application-dashboard"></a>Konfigurieren des Anwendungsdashboards

Nach dem Herstellen einer Verbindung zu einer Azure IoT Central-Anwendung wird als Erstes das **Dashboard** angezeigt. Wenn Sie die Anwendung auf Grundlage einer der branchenbezogenen [Anwendungsvorlagen](./concepts-app-templates.md) erstellt haben, startet Ihre Anwendung mit einem vordefinierten Dashboard. Wenn Sie die Anwendung über die Anwendungsvorlage [Benutzerdefinierte Anwendung](./concepts-app-templates.md) erstellt haben, werden auf dem Dashboard Tipps für den Einstieg angezeigt.

> [!TIP]
> Zusätzlich zum Standarddashboard der Anwendung können Benutzer [mehrere Dashboards erstellen](howto-create-personal-dashboards.md). Diese Dashboards können nur für den Benutzer persönlich bestimmt sein oder für alle Benutzer der Anwendung freigegeben werden.  

## <a name="add-tiles"></a>Hinzufügen von Kacheln

Der folgende Screenshot zeigt das Dashboard in einer Anwendung, die über die Vorlage **Benutzerdefinierte Anwendung** erstellt wurde. Wenn Sie das aktuelle Dashboard anpassen möchten, klicken Sie auf **Bearbeiten**. Wenn Sie ein benutzerdefiniertes privates oder freigegebenes Dashboard hinzufügen möchten, klicken Sie auf **Neu**:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Dashboard für Anwendungen, die auf der Vorlage „Benutzerdefinierte Anwendung“ basieren":::

Nachdem Sie auf **Bearbeiten** oder **Neu** geklickt haben, befindet sich das Dashboard im *Bearbeitungsmodus*. Sie können die Tools im Bereich **Dashboard bearbeiten** verwenden, um dem Dashboard Kacheln hinzuzufügen und Kacheln auf dem Dashboard selbst anzupassen und von dort zu entfernen. Wenn Sie beispielsweise eine Kachel mit **Telemetriedaten** hinzufügen möchten, auf der die von einem oder mehreren Geräten gemeldete aktuelle Temperatur angezeigt wird, gehen Sie folgendermaßen vor:

1. Wählen Sie eine **Gerätegruppe** und dann Ihre Geräte im Dropdown **Geräte** aus, damit sie auf der Kachel angezeigt werden. Nun werden die verfügbare Telemetrie, die Eigenschaften und die Befehle dieser Geräte angezeigt.

1. Verwenden Sie bei Bedarf das Dropdown, um einen Telemetriewert auszuwählen, der auf der Kachel angezeigt werden soll. Sie können der Kachel weitere Elemente hinzufügen, indem Sie auf **+ Telemetrie**, **+ Eigenschaft** oder **+ Cloudeigenschaft** klicken.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Hinzufügen einer Kachel mit Temperaturtelemetriedaten zum Dashboard":::

Wenn Sie alle Werte ausgewählt haben, die auf der Kachel angezeigt werden sollen, klicken Sie auf **Kachel hinzufügen**. Die Kachel wird nun auf dem Dashboard angezeigt. Dort können Sie die Visualisierung ändern, die Größe der Kachel ändern, sie verschieben und sie konfigurieren.

Wenn Sie das Hinzufügen und Anpassen von Kacheln zu Ihrem Dashboard abgeschlossen haben, klicken Sie auf **Speichern**, damit die Änderungen am Dashboard gespeichert werden. Dadurch verlassen Sie den Bearbeitungsmodus.

## <a name="customize-tiles"></a>Anpassen von Kacheln

Wenn Sie eine Kachel bearbeiten möchten, müssen Sie sich im Bearbeitungsmodus befinden.  Die verfügbaren Anpassungsoptionen hängen vom [Kacheltyp](#tile-types) ab:

* Über das Linealsymbol auf einer Kachel können Sie die Visualisierung ändern. Zu den Visualisierungen gehören Liniendiagramme, Balkendiagramme, Kreisdiagramme, letzte bekannte Werte, Key Performance Indicators (KPIs), Wärmebilder und Karten.

* Über das quadratische Symbol können Sie die Größe der Kachel ändern.

* Das Zahnradsymbol ermöglicht die Konfiguration der Visualisierung. Bei einer Liniendiagrammvisualisierung können Sie beispielsweise die Legende und Achsen anzeigen und einen Zeitbereich auswählen, der dargestellt werden soll.


## <a name="tile-types"></a>Kacheltypen

Die folgende Tabelle beschreibt die verschiedenen Typen von Kacheln, die Sie einem Dashboard hinzufügen können:

| Kachel             | BESCHREIBUNG |
| ---------------- | ----------- |
| Markdown         | Kacheln mit Unterstützung für Markdown sind Kacheln, auf die geklickt werden kann und in denen Überschriften und Beschreibungstext mithilfe von Markdown angezeigt werden. Die URL kann ein relativer Link zu einer anderen Seite der Anwendung sein, oder ein absoluter Link zu einer externen Website.|
| Image            | Auf Bildkacheln, auf denen ein benutzerdefiniertes Bild angezeigt wird, kann geklickt werden. Die URL kann ein relativer Link zu einer anderen Seite der Anwendung sein, oder ein absoluter Link zu einer externen Website.|
| Bezeichnung            | Auf Kacheln mit einer Beschriftung auf einem Dashboard wird benutzerdefinierter Text angezeigt. Sie können die Größe des Texts festlegen. Auf einer Kachel mit Beschriftung können Sie dem Dashboard relevante Informationen wie Beschreibungen, Kontaktinformationen oder Hilfe hinzufügen.|
| Anzahl            | Diese Kachel zeigt die Anzahl der Geräte in einer Gerätegruppe an.|
| Karte              | Kartenkacheln zeigen den Standort eines oder mehrerer Geräte auf einer Karte an. Sie können bis zu 100 Punkte im Standortverlauf eines Geräts anzeigen. Sie können beispielsweise ein Routenmuster der Orte anzeigen, an denen sich ein Gerät in der vergangenen Woche befand.|
| KPI              |  KPI-Kacheln zeigen aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Damit können Sie beispielsweise die Maximaltemperatur und den Druck eines oder mehrerer Geräte in der letzten Stunde anzeigen.|
| Liniendiagramm       | Liniendiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Sie können beispielsweise ein Liniendiagramm anzeigen, das die durchschnittliche Temperatur und den durchschnittlichen Druck eines oder mehrere Geräte in der letzten Stunde anzeigt.|
| Balkendiagramm        | Balkendiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an. Sie können beispielsweise ein Balkendiagramm anzeigen, das die durchschnittliche Temperatur und den durchschnittlichen Druck eines oder mehrerer Geräte in der letzten Stunde anzeigt.|
| Kreisdiagramm        | Kreisdiagrammkacheln zeigen einen oder mehrere aggregierte Telemetriewerte für ein oder mehrere Geräte über einen bestimmten Zeitraum hinweg an.|
| Heat Map         | Auf Wärmebildkacheln werden Informationen zu einem oder mehreren Geräten farblich dargestellt.|
| Letzter bekannter Wert | Kacheln mit letzten bekannten Werten zeigen die aktuellen Telemetriewerte für ein oder mehrere Geräte an. Auf dieser Kachel können Sie beispielsweise die aktuellen Messungen von Temperatur, Druck und Feuchtigkeit für ein oder mehrere Gerät anzeigen. |
| Ereignisverlauf    | Auf einer Kachel mit dem Ereignisverlauf werden die Ereignisse für ein Gerät in einem Zeitraum gezeigt. Auf dieser Kachel können Sie beispielsweise alle Öffnungs- und Schließereignisse der Ventile für ein oder mehrere Geräte während der letzten Stunde anzeigen.|
| Eigenschaft         |  Auf Eigenschaftenkacheln wird der aktuelle Wert für Eigenschaften und Cloudeigenschaften eines oder mehrerer Geräts angezeigt. Sie können diese Kachel z. B. verwenden, um Geräteeigenschaften wie den Hersteller oder die Firmwareversion eines Geräts anzuzeigen. |

Aktuell können Sie Kacheln, die mehrere Geräte unterstützen, bis zu zehn Geräte hinzufügen.

### <a name="customizing-visualizations"></a>Anpassen von Visualisierungen

Liniendiagramme zeigen standardmäßig Daten über einen Zeitraum hinweg. Der gewählte Zeitraum wird in 50 Buckets gleicher Größe aufgeteilt, und die Gerätedaten werden dann pro Bucket aggregiert, sodass für den ausgewählten Zeitbereich 50 Datenpunkte entstehen. Wenn Sie Rohdaten anzeigen möchten, können Sie Ihre Auswahl so ändern, dass die letzten 100 Werte angezeigt werden. Wenn Sie den Zeitbereich ändern oder eine Visualisierung für Rohdaten auswählen möchten, verwenden Sie das Dropdown „Anzeigebereich“ des Panel **Diagramm konfigurieren**.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/display-range.png" alt-text="Ändern des Anzeigebereichs eines Liniendiagramms":::

Klicken Sie bei Kacheln, die aggregierte Werte anzeigen, auf das Zahnradsymbol neben dem Telemetrietyp im Bereich **Diagramm konfigurieren**, um die Aggregation auszuwählen. Zur Verfügung stehen die Aggregationen für den Durchschnitt, die Summe, das Maximum, das Minimum und die Anzahl.

Bei Liniendiagrammen, Balkendiagrammen und Kreisdiagrammen können Sie die Farbe der verschiedenen Telemetriewerte anpassen. Klicken Sie hierzu auf das Farbpalettensymbol neben den Telemetriedaten, die Sie anpassen möchten:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Ändern der Farbe eines Telemetriewerts":::

Bei Kacheln, die Zeichenfolgeneigenschaften oder Telemetriewerte zeigen, können Sie auswählen, wie der Text angezeigt werden soll. Wenn das Gerät beispielsweise eine URL in einer Zeichenfolgeneigenschaft speichert, können Sie sie als Link anzeigen, auf den geklickt werden kann. Wenn die URL auf ein Bild verweist, können Sie das Bild in einer Kachel mit dem letzten bekannten Wert oder einer Eigenschaftenkachel rendern. Wenn Sie ändern möchten, wie eine Zeichenfolge angezeigt wird, klicken Sie in der Kachelkonfiguration auf das Zahnradsymbol neben dem Telemetrietyp oder der Eigenschaft:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Ändern der Anzeige einer Zeichenfolge auf einer Kachel":::

Für die numerischen Kacheln **KPI**, **Letzter bekannter Wert** und **Eigenschaften** können Sie mithilfe von bedingter Formatierung die Farbe der jeweiligen Kachel basierend auf dem aktuellen Wert anpassen. Um eine bedingte Formatierung anzuwenden, wählen Sie die Kachel **Konfigurieren** und dann neben dem anzupassenden Wert das Symbol **Bedingte Formatierung** aus:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="Screenshot der Option „Konfiguration“ für eine Kachel und des Symbols für die bedingte Formatierung":::

Fügen Sie Ihre Regeln für bedingte Formatierung hinzu:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Screenshot von Regeln für bedingte Formatierung für den durchschnittlichen Fluss. Es gibt drei Regeln: „Kleiner als 20“ wird grün dargestellt, „Kleiner als 50“ gelb und „Größer als 50“ rot.":::
   
Der folgende Screenshot zeigt die Auswirkung der Regel für bedingte Formatierung:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Screenshot der Kachel für durchschnittlichen Wasserfluss mit roter Hintergrundfarbe. Die Zahl auf der Kachel lautet 50,54.":::

### <a name="tile-formatting"></a>Kachelformatierung
Mit diesem Feature, das in KPI, LKV und auf Eigenschaftenkacheln verfügbar ist, können Benutzer den Schriftgrad anpassen, die dezimale Genauigkeit auswählen, numerische Werte abkürzen (z. B. kann 1.700 als 1,7 Tsd. abgekürzt werden) oder Zeichenfolgenwerte in den dazugehörigen Kacheln einschließen.

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/tile-format.png" alt-text="Kachelformat":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie gelernt haben, wie Sie das Standarddashboard der Azure IoT Central-Anwendung konfigurieren, [erfahren Sie nun, wie Sie ein persönliches Dashboard erstellen](howto-create-personal-dashboards.md).
