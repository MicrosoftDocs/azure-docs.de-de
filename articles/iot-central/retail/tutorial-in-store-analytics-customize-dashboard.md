---
title: 'Tutorial: Anpassen des Dashboards für Bediener in Azure IoT Central'
description: Dieses Tutorial zeigt, wie Sie das Bedienerdashboard in einer IoT Central-Anwendung anpassen und Geräte verwalten.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831711"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Tutorial:  Anpassen des Dashboards für Bediener und Verwalten von Geräten in Azure IoT Central


In diesem Tutorial erfahren Sie, wie Sie als Ersteller das Bedienerdashboard in Ihrer Azure IoT Central-Anwendung für die In-Store-Analyse anpassen können. Anwendungsbediener können das angepasste Dashboard verwenden, um die Anwendung auszuführen und die angeschlossenen Geräte zu verwalten.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Ändern des Dashboardnamens
> * Anpassen von Bildkacheln auf dem Dashboard
> * Anordnen von Kacheln zum Ändern des Layouts
> * Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen
> * Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails
> * Hinzufügen von Befehlskacheln zum Ausführen von Befehlen

## <a name="prerequisites"></a>Voraussetzungen

Der Ersteller muss das Tutorial absolvieren, um die Azure IoT Central-Anwendung für die In-Store-Analyse zu erstellen und Geräte hinzuzufügen:

* [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) (erforderlich)

## <a name="change-the-dashboard-name"></a>Ändern des Dashboardnamens
Zum Anpassen des Bedienerdashboards müssen Sie das Standarddashboard in Ihrer Anwendung bearbeiten. Sie können auch neue Dashboards erstellen. Der erste Schritt beim Anpassen des Dashboards in Ihrer Anwendung besteht darin, den Namen zu ändern.

1. Navigieren Sie zur Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral).

1. Öffnen Sie die Anwendung für die Überwachung von Bedingungen, die Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) erstellt haben.

1. Klicken Sie auf der Symbolleiste des Dashboards auf **Bearbeiten**. Im Bearbeitungsmodus können Sie Darstellung, Layout und Inhalt des Dashboards anpassen.

    ![Azure IoT Central-Dashboard: Bearbeiten](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Sie können den linken Bereich ausblenden. Durch Ausblenden des linken Bereichs erhalten Sie eine größere Arbeitsfläche zum Bearbeiten des Dashboards.

1. Geben Sie im Feld **Dashboardname** einen Anzeigenamen für das Dashboard ein. In diesem Tutorial wird ein fiktives Unternehmen namens „Contoso“ verwendet, und der Name des Beispieldashboards lautet *Contoso-Dashboard*. 

1. Wählen Sie **Speichern** aus. Die Änderungen werden auf dem Dashboard gespeichert, und der Bearbeitungsmodus ist deaktiviert.

    ![Azure IoT Central-Dashboard: Ändern des Namens](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Anpassen von Bildkacheln auf dem Dashboard
Ein Azure IoT Central-Anwendungsdashboard besteht aus einer oder mehreren Kacheln. Eine Kachel ist ein rechteckiger Container zum Anzeigen von Inhalt auf einem Dashboard. Sie können den Kacheln verschiedene Arten von Inhalten zuordnen, und Sie können Kacheln ziehen, ablegen und ihre Größe ändern, um das Layout des Dashboards anzupassen. Es gibt verschiedene Arten von Kacheln für die Anzeige von Inhalten. Bildkacheln enthalten Bilder, und Sie können eine URL hinzufügen, die es Benutzern ermöglicht, auf das Bild zu klicken. Bezeichnungskacheln enthalten nur Text. Markdownkacheln enthalten formatierten Inhalt und ermöglichen es Ihnen, ein Bild, eine URL, einen Titel sowie Markdowncode festzulegen, der als HTML gerendert wird. Telemetrie-, Eigenschaften- und Befehlskacheln zeigen gerätespezifische Daten an. 

In diesem Abschnitt erfahren Sie, wie Sie Bildkacheln auf dem Dashboard anpassen.

Um die Bildkachel anzupassen, die ein Markenbild auf dem Dashboard anzeigt, gehen Sie folgendermaßen vor:

1. Klicken Sie auf der Symbolleiste des Dashboards auf **Bearbeiten**. 

1. Wählen Sie auf der Bildkachel, auf der das Northwind-Markenbild angezeigt wird, die Option **Konfigurieren** aus. 

    ![Azure IoT Central-Dashboard: Bearbeiten des Markenbilds](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Ändern Sie den **Titel**. Der Titel wird angezeigt, wenn ein Benutzer auf das Bild zeigt.

1. Klicken Sie auf **Bild**. Ein Dialogfeld wird geöffnet, in dem Sie ein benutzerdefiniertes Bild hochladen können. 

1. Optional können Sie eine URL für das Bild angeben.

1. Klicken Sie auf **Konfiguration aktualisieren**. Mit der Schaltfläche **Konfiguration aktualisieren** werden Änderungen am Dashboard gespeichert, und der Bearbeitungsmodus bleibt aktiviert.

    ![Azure IoT Central-Dashboard: Speichern des Markenbilds](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Optional können Sie auch auf der Kachel namens **Dokumentation** die Option **Konfigurieren** auswählen und eine URL zu unterstützenden Inhalten angeben. 

So passen Sie die Bildkachel an, die einen Plan der Sensorzonen im Laden anzeigt:

1. Wählen Sie auf der Bildkachel, auf der der standardmäßige Zonenplan des Ladens angezeigt wird, die Option **Konfigurieren** aus. 

1. Klicken Sie auf **Bild**, und verwenden Sie das Dialogfeld, um ein benutzerdefiniertes Bild des Ladenzonenplans im Laden hochzuladen. 

1. Klicken Sie auf **Konfiguration aktualisieren**.

    ![Azure IoT Central-Dashboard: Speichern des Ladenplans](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    Der Beispielplan für das Contoso-Ladengeschäft zeigt vier Zonen: zwei Kassenzonen, eine Zone für Waschmittel und Körperpflegeprodukte und eine Zone für Lebensmittel und Getränke. In diesem Tutorial ordnen Sie diesen Zonen Sensoren zu, um Telemetriedaten bereitzustellen.

    ![Azure IoT Central: Ladenzonen](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Wählen Sie **Speichern** aus. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Anordnen von Kacheln zum Ändern des Layouts
Ein wichtiger Schritt beim Anpassen eines Dashboards besteht darin, die Kacheln so anzuordnen, dass sie eine nützliche Ansicht bieten. Anwendungsbediener verwenden das Dashboard, um Telemetriedaten zu Geräten zu visualisieren, Geräte zu verwalten und Bedingungen in einem Ladengeschäft zu überwachen. Azure IoT Central erleichtert Anwendungserstellern die Arbeit beim Erstellen eines Dashboards. Mit dem Bearbeitungsmodus für Dashboards können Sie Kacheln schnell hinzufügen, verschieben, löschen und ihre Größe ändern. Die Anwendungsvorlage **In-store analytics - checkout** vereinfacht die Erstellung eines Dashboards ebenfalls. Sie bietet ein funktionierendes Dashboardlayout mit verbundenen Sensoren und Kacheln, die Zähler für Warteschlangen an der Kasse sowie Umgebungsbedingungen anzeigen.

In diesem Abschnitt ordnen Sie das Dashboard in der Anwendungsvorlage **In-store analytics - checkout** neu an, um ein benutzerdefiniertes Layout zu erstellen.

So entfernen Sie Kacheln, die Sie in Ihrer Anwendung nicht verwenden möchten:

1. Klicken Sie auf der Symbolleiste des Dashboards auf **Bearbeiten**. 

1. Klicken Sie auf den folgenden Kacheln auf das Symbol **X** (Löschen), um sie zu entfernen: **Back to all zones**, **Visit store dashboard**, **Wait time** sowie alle drei Kacheln zu **Checkout 3**. Das Dashboard für das Ladengeschäft von Contoso verwendet diese Kacheln nicht. 

    ![Azure IoT Central: Löschen von Kacheln](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Scrollen Sie, um die verbleibenden Dashboardkacheln anzuzeigen.

1. Klicken Sie auf den folgenden Kacheln auf das Symbol **X** (Löschen), um sie zu entfernen: **Warm-up checkout zone**, **Cool-down checkout zone**, **Occupancy sensor settings**, **Thermostat sensor settings** und **Environment conditions**. 

   ![Azure IoT Central: Löschen der verbleibenden Kacheln](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Wählen Sie **Speichern** aus. Das Entfernen nicht verwendeter Kacheln gibt Platz auf der Bearbeitungsseite frei und vereinfacht die Dashboardansicht für Bediener.

1. Zeigen Sie die Änderungen des Dashboards an.

   ![Azure IoT Central nach dem Löschen der Kacheln](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

Nachdem Sie nicht verwendete Kacheln entfernt haben, ordnen Sie die verbleibenden Kacheln neu an, um eine übersichtliches Layout zu erstellen. Das neue Layout bietet Platz für Kacheln, die Sie in einem späteren Schritt hinzufügen.

So ordnen Sie die verbleibenden Kacheln neu an:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie die Kachel **Occupancy firmware** aus, und ziehen Sie sie rechts neben die Akkukachel **Occupancy**.

1. Wählen Sie die Kachel **Thermostat firmware** aus, und ziehen Sie sie rechts neben die Akkukachel **Thermostat**.

1. Wählen Sie **Speichern** aus.

1. Sehen Sie sich die Layoutänderungen an. 

    ![Azure IoT Central: Akkukacheln für Firmware](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen
Nachdem Sie das Dashboardlayout angepasst haben, können Sie Kacheln zur Anzeige von Telemetrie hinzufügen. Um eine Telemetriekachel zu erstellen, wählen Sie eine Gerätevorlage und eine Geräteinstanz aus, und wählen Sie dann die gerätespezifische Telemetrie aus, die auf der Kachel angezeigt werden soll. Die Anwendungsvorlage **In-store analytics - checkout** enthält verschiedene Telemetriekacheln auf dem Dashboard. Die vier Kacheln in den beiden Kassenzonen zeigen Telemetriedaten des simulierten Sensors für die Zonenauslastung an. Die Kachel **People traffic** zeigt Zähler in den beiden Kassenzonen an. 

In diesem Abschnitt fügen Sie zwei weitere Telemetriekacheln hinzu, um Umgebungstelemetrie der RuuviTag-Sensoren anzuzeigen, die Sie im Tutorial [Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central](./tutorial-in-store-analytics-create-app.md) hinzugefügt haben. 

So fügen Sie Kacheln zum Anzeigen von Umgebungsdaten aus den RuuviTag-Sensoren hinzu:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `RuuviTag` aus. 

1. Wählen Sie als **Geräteinstanz** einen der beiden RuuviTag-Sensoren aus. Klicken Sie im Contoso-Ladengeschäft auf `Zone 1 Ruuvi`, um eine Telemetriekachel für Zone 1 zu erstellen. 

1. Wählen Sie in der Liste **Telemetrie** die Einträge `Relative humidity` und `temperature` aus. Dies sind die Telemetrieelemente, die für jede Zone auf der Kachel Daten anzeigen.

1. Wählen Sie **Kombinieren** aus. 

    ![Azure IoT Central: Hinzufügen von RuuviTag-Kachel 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Es wird eine neue Kachel hinzugefügt, die kombinierte Telemetriedaten für Luftfeuchtigkeit und Temperatur für den ausgewählten Sensor anzeigt. 

1. Klicken Sie auf der neuen Kachel für den RuuviTag-Sensor auf **Konfigurieren**. 

1. Ändern Sie den **Titel** zu *Umgebung Zone 1*. 

1. Klicken Sie auf **Konfiguration aktualisieren**.

1. Wiederholen Sie diese Schritte, um eine Kachel für die zweite Sensorinstanz zu erstellen. Legen Sie den **Titel** auf *Umgebung Zone 2* fest, und wählen Sie dann **Konfiguration aktualisieren** aus.

1. Ziehen Sie die Kachel mit dem Titel **Umgebung Zone 2** unter die Kachel **Thermostat firmware**. 

1. Ziehen Sie die Kachel mit dem Titel **Umgebung Zone 1** unter die Kachel **People traffic**. 

1. Wählen Sie **Speichern** aus. Das Dashboard zeigt die Zonentelemetrie auf den beiden neuen Kacheln an.

    ![Azure IoT Central: alle RuuviTag-Kacheln](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

So bearbeiten Sie die Kachel **People traffic** so, dass nur Telemetriedaten für zwei Kassenzonen angezeigt werden:

1. Wählen Sie **Bearbeiten** aus. 

1. Klicken Sie auf der Kachel **People traffic** auf **Konfigurieren**.

1. Wählen Sie unter **Telemetrie** die Einträge **Zähler 1**, **Zähler 2** und **Zähler 3** aus. 

1. Klicken Sie auf **Konfiguration aktualisieren**. Dadurch wird die vorhandene Konfiguration auf der Kachel gelöscht. 

1. Klicken Sie auf der Kachel **People traffic** erneut auf **Konfigurieren**.

1. Wählen Sie unter **Telemetrie** die Einträge **Zähler 1** und **Zähler 2** aus. 

1. Klicken Sie auf **Konfiguration aktualisieren**. 

1. Wählen Sie **Speichern** aus.  Das aktualisierte Dashboard zeigt nur für Ihre beiden Kassenzonen Zähler an, die auf dem simulierten Sensor für die Zonenauslastung basieren.

    ![Azure IoT Central: Zonennutzung an zwei Kassenschlangen](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails
Anwendungsbediener verwenden das Dashboard zum Verwalten von Geräten und zur Statusüberwachung. Fügen Sie für jedes RuuviTag eine Kachel hinzu, um Bedienern die Anzeige der Softwareversion zu ermöglichen. 

So fügen Sie eine Eigenschaftenkachel für jedes RuuviTag hinzu:

1. Wählen Sie **Bearbeiten** aus.

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `RuuviTag` aus. 

1. Wählen Sie als **Geräteinstanz** einen der beiden RuuviTag-Sensoren aus. Klicken Sie im Contoso-Ladengeschäft auf `Zone 1 Ruuvi`, um eine Telemetriekachel für Zone 1 zu erstellen. 

1. Klicken Sie auf **Eigenschaften > Softwareversion**.

1. Wählen Sie **Kombinieren** aus. 

1. Klicken Sie auf der neu erstellten Kachel namens **Softwareversion** auf **Konfigurieren**. 

1. Ändern Sie den **Titel** zu *Softwareversion von Ruuvi 1*.

1. Klicken Sie auf **Konfiguration aktualisieren**. 

1. Ziehen Sie die Kachel namens **Softwareversion von Ruuvi 1** unter die Kachel **Umgebung Zone 1**.

1. Wiederholen Sie diese Schritte, um eine Eigenschaftenkachel für die Softwareversion für das zweite RuuviTag zu erstellen. 

1. Wählen Sie **Speichern** aus.  

    ![Azure IoT Central: RuuviTag-Eigenschaftenkacheln](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Hinzufügen von Befehlskacheln zum Ausführen von Befehlen
Anwendungsbediener verwenden das Dashboard auch zum Verwalten von Geräten durch Ausführen von Befehlen. Sie können Befehlskacheln zum Dashboard hinzufügen, mit denen vordefinierte Befehle auf einem Gerät ausgeführt werden. In diesem Abschnitt fügen Sie eine Befehlskachel hinzu, um es Bedienern zu ermöglichen, das Rigado-Gateway neu zu starten. 

So fügen Sie eine Befehlskachel für einen Neustart des Gateways hinzu:

1. Wählen Sie **Bearbeiten** aus. 

1. Wählen Sie in der Liste **Gerätevorlage** den Eintrag `C500` aus. Dies ist die Vorlage für das Rigado C500-Gateway. 

1. Wählen Sie in **Geräteinstanz** die Gatewayinstanz aus.

1. Wählen Sie **Befehl > Neustart** aus, und ziehen Sie diesen Eintrag auf das Dashboard neben den Ladenplan. 

1. Wählen Sie **Speichern** aus. 

1. Zeigen Sie Ihr vollständiges Contoso-Dashboard an. 

    ![Azure IoT Central: abgeschlossene Dashboardanpassung](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Optional können Sie die Kachel **Neustart** auswählen, um den Neustartbefehl auf Ihrem Gateway auszuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Ändern des Dashboardnamens
* Anpassen von Bildkacheln auf dem Dashboard
* Anordnen von Kacheln zum Ändern des Layouts
* Hinzufügen von Telemetriekacheln zum Anzeigen von Bedingungen
* Hinzufügen von Eigenschaftenkacheln zum Anzeigen von Gerätedetails
* Hinzufügen von Befehlskacheln zum Ausführen von Befehlen

Nachdem Sie das Dashboard in Ihrer Azure IoT Central-Anwendung für die In-Store-Analyse angepasst haben, wird als Nächstes der folgende Schritt empfohlen:

> [!div class="nextstepaction"]
> [Exportieren von Daten und Visualisieren von Erkenntnissen](./tutorial-in-store-analytics-export-data-visualize-insights.md)
