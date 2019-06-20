---
title: 'Tutorial: Einrichten einer Azure Time Series Insights Preview-Umgebung | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Ihre Umgebung in Azure Time Series Insights Preview einrichten.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: 77b7b90b63ffebc14498183fc179b9c8ae76a722
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237848"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>Tutorial: Einrichten einer Azure Time Series Insights Preview-Umgebung

Dieses Tutorial führt Sie durch das Erstellen einer Azure Time Series Insights Preview-Umgebung mit nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG). 

In diesem Tutorial lernen Sie Folgendes:

* Erstellen einer Azure Time Series Insights Preview-Umgebung.
* Herstellen einer Verbindung der Azure Time Series Insights Preview-Umgebung mit einem Event Hub in Azure Event Hubs.
* Ausführen eines Solution Accelerator-Beispiels zum Streamen von Daten in die Azure Time Series Insights Preview-Umgebung.
* Durchführen einer grundlegenden Analyse für die Daten
* Definieren eines Zeitreihenmodell-Typs und einer Hierarchie und Zuordnen zu Ihren Instanzen

## <a name="create-a-device-simulation"></a>Erstellen einer Gerätesimulation

In diesem Abschnitt erstellen Sie drei simulierte Geräte, die Daten an eine Instanz von Azure IoT Hub senden.

1. Öffnen Sie die Seite [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators). Auf dieser Seite finden Sie mehrere vorgefertigte Beispiele. Melden Sie sich mit Ihrem Azure-Konto an. Wählen Sie dann **Gerätesimulation** aus.

   [![Seite „Azure IoT Solution Accelerators“](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   Wählen Sie **Jetzt testen** aus.

1. Legen Sie auf der Seite **Create Device Simulation solution** (Gerätesimulationslösung erstellen) die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | --- |
    | **Lösungsname** | Geben Sie für eine neue Ressourcengruppe einen eindeutigen Wert ein. Die aufgelisteten Azure-Ressourcen werden erstellt und der Ressourcengruppe zugewiesen. |
    | **Abonnement** | Wählen Sie das Abonnement aus, das Sie zum Erstellen Ihrer Time Series Insights-Umgebung verwendet haben. |
    | **Region** | Wählen Sie die Region aus, die Sie zum Erstellen Ihrer Time Series Insights-Umgebung verwendet haben. |
    | **Optionale Azure-Ressourcen bereitstellen** | Lassen Sie das Kontrollkästchen **IoT Hub** aktiviert. Die simulierten Geräte verwenden IoT Hub, um eine Verbindung herzustellen und Daten zu streamen. |
 
    Wählen Sie **Lösung erstellen** aus. Warten Sie 10 bis 15 Minuten, bis Ihre Lösung bereitgestellt wurde.

    [![Seite zum Erstellen der Gerätesimulationslösung](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. Wählen Sie in Ihrem Solution Accelerator-Dashboard **Starten** aus:

    [![Starten der Gerätesimulationslösung](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. Sie werden zur Seite **Microsoft Azure IoT-Gerätesimulation** weitergeleitet. Wählen Sie rechts oben auf der Seite die Option **Neue Simulation** aus.

    [![Azure IoT-Simulationsseite](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. Legen Sie im Bereich **Simulation setup** (Simulationssetup) die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | --- |
    | **Name** | Geben Sie einen eindeutigen Namen für einen Simulator ein. |
    | **Beschreibung** | Geben Sie eine Definition ein. |
    | **Simulationsdauer** | Verwenden Sie die Option **Run indefinitely** (Unbegrenzt). |
    | **Gerätemodell** | **Name**: Geben Sie **Chiller** ein. <br />**Menge**: Geben Sie **3** ein. |
    | **IoT-Zielhub** | Verwenden Sie die Option **Use pre-provisioned IoT Hub** (Vorab bereitgestellten IoT-Hub verwenden). |

    [![Festzulegende Parameter](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    Wählen Sie **Simulation starten** aus.

    Beachten Sie die Informationen, die im Gerätesimulationsdashboard für **aktive Geräte** und die Anzahl von **Nachrichten pro Sekunde** angezeigt werden.

    [![Azure IoT-Simulationsdashboard](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>Auflisten der Gerätesimulationseigenschaften

Für die Erstellung einer Azure Time Series Insights-Umgebung benötigen Sie den Namen Ihres IoT-Hubs, Ihres Abonnements und Ihrer Ressourcengruppe.

1. Wechseln Sie zum Solution Accelerator-Dashboard. Melden Sie sich mit demselben Azure-Abonnementkonto an. Suchen Sie nach der Gerätesimulation, die Sie im vorherigen Abschnitt erstellt haben.

1. Wählen Sie Ihren Gerätesimulator und anschließend **Starten** aus. Wählen Sie rechts im Solution Accelerator-Bereich für die Gerätesimulatorlösung die Option **Azure-Verwaltungsportal** aus.

    [![Simulatorauflistungen](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. Notieren Sie sich die Namen des IoT-Hubs, des Abonnements und der Ressourcengruppe.

    [![Details im Gerätesimulatordashboard im Azure-Portal](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>Erstellen einer Time Series Insights Preview-PAYG-Umgebung

In diesem Abschnitt wird beschrieben, wie Sie eine Azure Time Series Insights Preview-Umgebung mit dem [Azure-Portal](https://portal.azure.com/) erstellen.

1. Melden Sie sich unter Verwendung Ihres Abonnementkontos beim Azure-Portal an.

1. Wählen Sie **Ressource erstellen** > **Internet der Dinge** > **Time Series Insights**.

   [![„Internet der Dinge“ auswählen und dann „Time Series Insights“](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. Legen Sie im Bereich **Time Series Insights-Umgebung erstellen** auf der Registerkarte **Grundlagen** die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | ---|
    | **Umgebungsname** | Geben Sie einen eindeutigen Namen für die Azure Time Series Insights-Umgebung (Vorschauversion) ein. |
    | **Abonnement** | Geben Sie das Abonnement ein, in dem Sie die Azure Time Series Insights-Umgebung (Vorschauversion) erstellen möchten. Eine bewährte Methode ist die Verwendung des Abonnements, das Sie auch für die anderen vom Gerätesimulator erstellten IoT-Ressourcen verwenden. |
    | **Ressourcengruppe** | Wählen Sie eine vorhandene Ressourcengruppe für die Ressource der Azure Time Series Insights-Umgebung (Vorschauversion) aus, oder erstellen Sie eine neue Ressourcengruppe. Eine Ressourcengruppe ist ein Container für Azure-Ressourcen. Eine bewährte Methode ist die Verwendung der Ressourcengruppe, die Sie auch für die anderen vom Gerätesimulator erstellten IoT-Ressourcen verwenden. |
    | **Location** | Wählen Sie eine Datencenterregion für Ihre Azure Time Series Insights-Umgebung (Vorschauversion) aus. Um zusätzliche Bandbreitenkosten und Wartezeiten zu vermeiden, empfiehlt es sich, die Azure Time Series Insights-Umgebung (Vorschauversion) in derselben Region wie die anderen IoT-Ressourcen zu erstellen. |
    | **Tier** |  Wählen Sie **Nutzungsbasierte Zahlung** aus ** . Dies ist die SKU für das Azure Time Series Insights Preview-Produkt. |
    | **Eigenschafts-ID** | Geben Sie einen Wert ein, der Ihre Zeitreiheninstanz eindeutig identifiziert. Der Wert, den Sie in das Feld **Eigenschafts-ID** eingeben, ist unveränderlich. Sie können ihn später nicht mehr ändern. Geben Sie für dieses Tutorial **iothub-connection-device-id** ein. Weitere Informationen zur Zeitreihen-ID finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](./time-series-insights-update-how-to-id.md). |
    | **Speicherkontoname** | Geben Sie einen global eindeutigen Namen ein, um ein neues Speicherkonto zu erstellen. |
   
   Klicken Sie auf **Weiter: Ereignisquelle**.

   [![Bereich zum Erstellen einer Time Series Insights-Umgebung](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. Legen Sie auf der Registerkarte **Ereignisquelle** die folgenden Parameter fest:

   | Parameter | Aktion |
   | --- | --- |
   | **Ereignisquelle erstellen?** | Wählen Sie **Ja** aus.|
   | **Name** | Geben Sie einen eindeutigen Wert für den Namen der Ereignisquelle ein. |
   | **Quellentyp** | Wählen Sie **IoT Hub** aus. |
   | **Hub auswählen** | Wählen Sie **Vorhandenen auswählen** aus. |
   | **Abonnement** | Wählen Sie das Abonnement aus, das Sie für den Gerätesimulator verwendet haben. |
   | **IoT Hub-Name** | Wählen Sie den Namen des IoT-Hubs aus, den Sie für den Gerätesimulator erstellt haben. |
   | **Zugriffsrichtlinie für IoT Hub** | Wählen Sie **iothubowner** aus. |
   | **IoT Hub-Consumergruppe** | Wählen Sie **Neu** aus, geben Sie einen eindeutigen Namen ein, und wählen Sie dann **Hinzufügen** aus. Bei der Consumergruppe muss es sich um einen eindeutigen Wert in Azure Time Series Insights (Vorschauversion) handeln. |
   | **Timestamp property** (Timestamp-Eigenschaft) | Mit diesem Wert wird die Eigenschaft **Zeitstempel** in Ihren eingehenden Telemetriedaten identifiziert. Lassen Sie das Feld für dieses Tutorial leer. Dieser Simulator verwendet den aus IoT Hub eingehenden Zeitstempel, der standardmäßig von Time Series Insights verwendet wird. |

   Klicken Sie auf **Überprüfen + erstellen**.

   [![Konfigurieren einer Ereignisquelle](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. Überprüfen Sie auf der Registerkarte **Bewerten + erstellen** Ihre Auswahl, und wählen Sie dann **Erstellen** aus.

    [![Seite „Überprüfen + erstellen“ mit Schaltfläche „Erstellen“](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    Sie können den Status Ihrer Bereitstellung anzeigen:

    [![Benachrichtigung, dass die Bereitstellung abgeschlossen ist](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. Wenn Sie der Besitzer des Mandanten sind, haben Sie Zugriff auf Ihre Azure Time Series Insights-Umgebung (Vorschauversion). So stellen Sie sicher, dass Sie über Zugriff verfügen:

   1. Suchen Sie nach Ihrer Ressourcengruppe, und wählen Sie dann Ihre Azure Time Series Insights-Umgebung (Vorschauversion) aus:

      [![Ausgewählte Umgebung](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. Wählen Sie auf der Seite für die Azure Time Series Insights-Vorschauversion die Option **Datenzugriffsrichtlinien** aus:

      [![Datenzugriffsrichtlinien](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. Stellen Sie sicher, dass Ihre Anmeldeinformationen aufgelistet sind:

      [![Aufgeführte Anmeldeinformationen](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   Falls Ihre Anmeldeinformationen nicht aufgelistet sind, müssen Sie sich selbst die Zugriffsberechtigung für die Umgebung erteilen. Weitere Informationen zum Festlegen von Berechtigungen finden Sie unter [Gewähren von Datenzugriff für eine Umgebung](./time-series-insights-data-access.md).

## <a name="analyze-data-in-your-environment"></a>Analysieren von Daten in Ihrer Umgebung

In diesem Abschnitt führen Sie mit dem [Azure Time Series Insights Preview-Explorer](./time-series-insights-update-explorer.md) grundlegende Analysen für Ihre Zeitreihendaten durch.

1. Navigieren Sie zu Ihrem Azure Time Series Insights Preview-Explorer, indem Sie im [Azure-Portal](https://portal.azure.com/) auf der Ressourcenseite die URL auswählen.

    [![Die URL des Time Series Insights Preview-Explorers](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. Wählen Sie im Explorer den Knoten **Time Series-Instanzen**  aus, um die gesamten Azure Time Series Insights Preview-Instanzen in der Umgebung anzuzeigen.

    [![Liste von Instanzen ohne übergeordnete Elemente](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. Wählen Sie die erste Zeitreiheninstanz aus. Wählen Sie dann **Show pressure** (Druck anzeigen) aus.

    [![Ausgewählte Zeitreiheninstanz mit Menübefehl zum Anzeigen des durchschnittlichen Drucks](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    Ein Diagramm mit Zeitreihen wird angezeigt. Ändern Sie das **Intervall** in **15 Sekunden**.

    [![Zeitreihendiagramm](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. Wiederholen Sie Schritt 3 mit den anderen beiden Zeitreiheninstanzen. Sie können alle Zeitreihen wie im folgenden Diagramm dargestellt anzeigen:

    [![Diagramm mit allen Zeitreihen](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. Ändern Sie im Optionsfeld **Zeitraum** den Zeitbereich, um Zeitreihentrends innerhalb der letzten Stunde anzuzeigen:

    [![Legen Sie den Zeitbereich auf eine Stunde fest](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>Definieren und Anwenden eines Modells

In diesem Abschnitt wenden Sie ein Modell zum Strukturieren der Daten an. Um das Modell zu vervollständigen, definieren Sie Typen, Hierarchien und Instanzen. Weitere Informationen zur Datenmodellierung finden Sie unter [Time Series-Modell](./time-series-insights-update-tsm.md).

1. Wählen Sie im Explorer die Registerkarte **Modell** aus:

   [![Registerkarte „Modell“ im Explorer](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. Wählen Sie **Hinzufügen** aus, um einen Typ hinzuzufügen:

   [![Schaltfläche „Hinzufügen“ für Typen](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. Definieren Sie anschließend drei Variablen für den Typ: *pressure* (Druck), *temperature* (Temperatur) und *humidity* (Luftfeuchtigkeit). Legen Sie im Bereich **Typ hinzufügen** die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | ---|
    | **Name** | Geben Sie **Chiller** ein. |
    | **Beschreibung** | Geben Sie **This is a type definition of Chiller** ein. |

   * Legen Sie unter **Variablen** die folgenden Parameter fest, um *pressure* (Druck) zu definieren:

     | Parameter | Aktion |
     | --- | ---|
     | **Name** | Geben Sie **Avg Pressure** ein. |
     | **Wert** | Wählen Sie **pressure (Double) (Druck (Double))** aus. Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights (Vorschauversion) damit beginnt, Ereignisse zu empfangen. |
     | **Aggregationsvorgang** | Wählen Sie **AVG** aus. |

      [![Auswahloptionen zum Definieren des Drucks](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      Wählen Sie **Variable hinzufügen** aus, um die nächste Variable hinzuzufügen.

   * Definieren Sie *temperature* (Temperatur):

     | Parameter | Aktion |
     | --- | ---|
     | **Name** | Geben Sie **Avg Temperature** ein. |
     | **Wert** | Wählen Sie **temperature (Double) (Temperatur (Double))** aus. Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights (Vorschauversion) damit beginnt, Ereignisse zu empfangen. |
     | **Aggregationsvorgang** | Wählen Sie **AVG** aus.|

      [![Auswahloptionen zum Definieren der Temperatur](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

      Wählen Sie **Variable hinzufügen** aus, um die nächste Variable hinzuzufügen.

   * Definieren Sie *humidity* (Luftfeuchtigkeit):

      | | |
      | --- | ---|
      | **Name** | Geben Sie **Max Humidity** ein. |
      | **Wert** | Wählen Sie **humidity (Double) (Luftfeuchtigkeit (Double))** aus. Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights (Vorschauversion) damit beginnt, Ereignisse zu empfangen. |
      | **Aggregationsvorgang** | Wählen Sie **MAX** aus.|

      [![Auswahloptionen zum Definieren der Temperatur](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

    Klicken Sie auf **Erstellen**.

    Sie können den Typ, den Sie hinzugefügt haben, anzeigen:

    [![Informationen zum hinzugefügten Typ](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. Im nächsten Schritt wird eine Hierarchie hinzugefügt. Wählen Sie unter **Hierarchien** die Option **Hinzufügen** aus:

    [![Registerkarte „Hierarchien“ mit Schaltfläche „Hinzufügen“](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. Legen Sie im Bereich **Hierarchie bearbeiten** die folgenden Parameter fest:

   | Parameter | Aktion |
   | --- | ---|
   | **Name** | Geben Sie **Location Hierarchy** ein. |
   | **Ebene 1** | Geben Sie **Land** ein. |
   | **Ebene 2** | Geben Sie **Ort** ein. |
   | **Ebene 3** | Geben Sie **Gebäude** ein. |

   Wählen Sie **Speichern** aus.

    [![Hierarchiefelder mit Schaltfläche „Erstellen“](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   Die erstellte Hierarchie wird angezeigt:

    [![Informationen zur Hierarchie](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. Wählen Sie **Instanzen** aus. Wählen Sie die erste Instanz und anschließend **Bearbeiten** aus:

    [![Auswählen der Schaltfläche „Bearbeiten“ für eine Instanz](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. Legen Sie im Bereich **Edit instances** (Instanzen bearbeiten) die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | --- |
    | **Typ** | Wählen Sie **Chiller** aus. |
    | **Beschreibung** | Geben Sie **Instance for Chiller-01.1** ein. |
    | **Hierarchien** | Wählen Sie **Location Hierarchy** aus. |
    | **Country** | Geben Sie **USA** ein. |
    | **City (Ort)** | Geben Sie **Seattle** ein. |
    | **Building (Gebäude)** | Geben Sie **Space Needle** ein. |

    [![Instanzfelder mit der Schaltfläche „Speichern“](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

   Wählen Sie **Speichern** aus.

1. Wiederholen Sie den obigen Schritt für die anderen Sensoren. Aktualisieren Sie die folgenden Werte:

   * Für Chiller 01.2:

     | Parameter | Aktion |
     | --- | --- |
     | **Typ** | Wählen Sie **Chiller** aus. |
     | **Beschreibung** | Geben Sie **Instance for Chiller-01.2** ein. |
     | **Hierarchien** | Wählen Sie **Location Hierarchy** aus. |
     | **Country** | Geben Sie **USA** ein. |
     | **City (Ort)** | Geben Sie **Seattle** ein. |
     | **Building (Gebäude)** | Geben Sie **Pacific Science Center** ein. |

   * Für Chiller 01.3:

     | Parameter | Aktion |
     | --- | --- |
     | **Typ** | Wählen Sie **Chiller** aus. |
     | **Beschreibung** | Geben Sie **Instance for Chiller-01.3** ein. |
     | **Hierarchien** | Wählen Sie **Location Hierarchy** aus. |
     | **Country** | Geben Sie **USA** ein. |
     | **City (Ort)** | Geben Sie **New York** ein. |
     | **Building (Gebäude)** | Geben Sie **Empire State Building** ein. |

1. Wählen Sie die Registerkarte **Analysieren** aus, und aktualisieren Sie dann die Seite. Erweitern Sie unter **Location Hierarchy** alle Hierarchieebenen, um die Zeitreiheninstanzen anzuzeigen:

   [![Registerkarte „Analysieren“](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. Um die Zeitreiheninstanzen der letzten Stunde zu untersuchen, ändern Sie **Kurze Zeitangaben** in **Letzte Stunde**:

    [![Feld „Kurze Zeitangaben“ mit ausgewählter Option „Letzte Stunde“](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. Wählen Sie unter **Pacific Science Center** die Zeitreiheninstanz und anschließend **Show Max Humidity** (Max. Luftfeuchtigkeit anzeigen) aus.

    [![Ausgewählte Zeitreiheninstanz mit der Menüauswahl „Show Max Humidity“ (Max. Luftfeuchtigkeit anzeigen)](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. Die Zeitreihe für **Max Humidity** (Max. Luftfeuchtigkeit) mit einer Intervallgröße von **1 Minute** wird geöffnet. Wählen Sie eine Region aus, um einen Bereich zu filtern. Klicken Sie mit der rechten Maustaste auf das Diagramm, und wählen Sie **Zoom** aus, um Ereignisse innerhalb des Zeitrahmens zu analysieren:

   [![Ausgewählter Bereich mit Zoombefehl in einem Kontextmenü](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. Um Details zu einem Ereignis anzuzeigen, wählen Sie eine Region aus, und klicken Sie dann mit der rechten Maustaste auf das Diagramm:

   [![Detaillierte Ereignisliste](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:  

> [!div class="checklist"]
> * Erstellen und Verwenden eines Accelerators zur Gerätesimulation.
> * Erstellen einer Azure Time Series Insights Preview-PAYG-Umgebung.
> * Herstellen einer Verbindung der Azure Time Series Insights Preview-Umgebung mit einer Event Hub-Instanz.
> * Ausführen eines Solution Accelerator-Beispiels zum Streamen von Daten in die Azure Time Series Insights Preview-Umgebung.
> * Durchführen einer grundlegenden Analyse der Daten.
> * Definieren eines Zeitreihenmodell-Typs und einer Hierarchie und deren Zuordnung zu Ihren Instanzen.

Da Sie nun Ihre eigene Azure Time Series Insights Preview-Umgebung erstellen können, erfahren Sie mehr über die wichtigsten Konzepte in Azure Time Series Insights.

Informieren Sie sich über die Azure Time Series Insights-Speicherkonfiguration:

> [!div class="nextstepaction"]
> [Speicherung und Datenerfassung in Azure Time Series Insights Preview](./time-series-insights-update-storage-ingress.md)

Erfahren Sie mehr über Zeitreihenmodelle:

> [!div class="nextstepaction"]
> [Datenmodellierung in Azure Time Series Insights](./time-series-insights-update-tsm.md)