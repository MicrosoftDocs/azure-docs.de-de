---
title: Visualisieren von simulierten Telemetriedaten mit Time Series Insights – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Time Series Insights-Umgebung konfigurieren und die vom Solution Accelerator für Gerätesimulation generierten Telemetriedaten untersuchen und analysieren.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854562"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Verwenden von Time Series Insights zum Visualisieren der Telemetriedaten, die vom Solution Accelerator für Gerätesimulation gesendet wurden

Mithilfe des Solution Accelerators für Gerätesimulation können Sie Telemetriedaten aus simulierten Geräten generieren, um Ihre IoT-Lösungen zu testen. In dieser Anleitung wird gezeigt, wie Sie die simulierten Telemetriedaten mit einer Time Series Insights-Umgebung visualisieren und analysieren.

## <a name="prerequisites"></a>Voraussetzungen

Um die in dieser Anleitung aufgeführten Schritte ausführen zu können, benötigen Sie ein aktives Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

Bei den Schritten in dieser Anleitung wird angenommen, dass Sie den Solution Accelerator für Gerätesimulation im Rahmen Ihres Azure-Abonnements bereitgestellt haben. Wenn Sie die Gerätesimulation noch nicht bereitgestellt haben, finden Sie weitere Informationen auf GitHub unter [Device Simulation deployment](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Bereitstellen der Gerätesimulation).

In diesem Artikel wird angenommen, dass der Name Ihres Solution Accelerators **Contoso-Simulation** lautet. Ersetzen Sie **Contoso-Simulation** durch den Namen Ihres Solution Accelerators, während Sie die nachstehenden Schritte ausführen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Erstellen einer Consumergruppe

Sie müssen eine dedizierte Consumergruppe in Ihrem IoT Hub erstellen, um Telemetriedaten an Time Series Insights zu streamen. Eine Ereignisquelle in Time Series Insights sollte über die exklusive Nutzung einer IoT Hub-Consumergruppe verfügen.

Bei den nachstehenden Schritten wird die Azure-Befehlszeilenschnittstelle in der Azure Cloud Shell verwendet, um die Consumergruppe zu erstellen:

1. Der IoT Hub ist eine von mehreren Ressourcen, die bei Ihrer Bereitstellung des Solution Accelerators für Gerätesimulation erstellt wurden. Führen Sie den folgenden Befehl aus, um den Namen Ihres IoT Hubs zu suchen. Denken Sie daran, den Namen Ihres Solution Accelerators zu verwenden:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Der IoT Hub ist die Ressource des Typs **Microsoft.Devices/IotHubs**.

1. Fügen Sie dem Hub die Consumergruppe **devicesimulationtsi** hinzu. Verwenden Sie im folgenden Befehl den Namen Ihres Hubs und Solution Accelerators:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Jetzt können Sie die Azure Cloud Shell schließen.

## <a name="create-a-new-time-series-insights-environment"></a>Erstellen einer neuen Time Series Insights-Umgebung

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) ist ein vollständig verwalteter Analyse-, Speicher- und Visualisierungsdienst für die Verwaltung von IoT-Zeitreihendaten in der Cloud. So erstellen Sie eine neue Time Series Insights-Umgebung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **Ressource erstellen** > **Internet der Dinge** > **Time Series Insights** aus:

    ![Neu: Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Wenn Sie Ihre Time Series Insights-Umgebung in derselben Ressourcengruppe wie der Ihres Solution Accelerators erstellen möchten, verwenden Sie die Werte in der nachstehenden Tabelle:

    | Einstellung | value |
    | ------- | ----- |
    | Umgebungsname | Im nachstehenden Screenshot wird der Name **Contoso-TSI** verwendet. Wählen Sie Ihren eigenen eindeutigen Namen, wenn Sie diesen Schritt durchführen. |
    | Subscription | Wählen Sie Ihr Azure-Abonnement in der Dropdownliste aus. |
    | Resource group | **Contoso-Simulation**. Verwenden Sie den Namen Ihres Solution Accelerators. |
    | Location | In diesem Beispiel wird **USA, Osten** verwendet. Erstellen Sie Ihre Umgebung in derselben Region wie Ihren Accelerator für Gerätesimulation. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Time Series Insights erstellen](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Wenn Sie die Time Series Insights-Umgebung zu derselben Ressourcengruppe wie der des Solution Accelerators hinzufügen, bedeutet dies: Sie wird beim Löschen des Solution Accelerators ebenfalls gelöscht.

1. Klicken Sie auf **Erstellen**. Die Erstellung der Umgebung kann ein paar Minuten dauern.

## <a name="create-event-source"></a>Erstellen der Ereignisquelle

Erstellen Sie eine neue Ereignisquelle für die Verbindung mit Ihrem IoT Hub. Verwenden Sie dazu die Consumergruppe, die Sie in den vorherigen Schritten erstellt haben. Eine Time Series Insights-Ereignisquelle erfordert eine dedizierte Consumergruppe, die von keinem anderen Dienst verwendet wird.

1. Navigieren Sie im Azure-Portal zu Ihrer neuen Time Series Insights-Umgebung.

1. Klicken Sie auf der linken Seite auf **Ereignisquellen**:

    ![Anzeigen von Ereignisquellen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klicken Sie auf **Hinzufügen**:

    ![Hinzufügen einer Ereignisquelle](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Verwenden Sie die Werte in der folgenden Tabelle, um Ihren IoT Hub als eine neue Ereignisquelle zu konfigurieren:

    | Einstellung | value |
    | ------- | ----- |
    | Name der Ereignisquelle | Im nachstehenden Screenshot wird der Name **contoso-iot-hub** verwendet. Verwenden Sie Ihren eigenen eindeutigen Namen, wenn Sie diesen Schritt durchführen. |
    | `Source` | **IoT Hub** |
    | Importoption | **Verwenden eines IoT Hub aus verfügbaren Abonnements** |
    | Abonnement-ID | Wählen Sie Ihr Azure-Abonnement in der Dropdownliste aus. |
    | IoT Hub-Name | **contoso-simulation7d894**. Verwenden Sie den Namen Ihres IoT Hubs aus Ihrem Solution Accelerator für Gerätesimulation. |
    | IoT Hub-Richtlinienname | **iothubowner** |
    | IoT Hub-Richtlinienschlüssel | Dieses Feld wird automatisch ausgefüllt. |
    | IoT Hub-Consumergruppe | **devicesimulationtsi** |
    | Ereignisserialisierungsformat | **JSON** |
    | Name der Timestamp-Eigenschaft | Nicht ausfüllen |

    ![Erstellen der Ereignisquelle](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Sie können [weiteren Benutzern Zugriff gewähren](../time-series-insights/concepts-access-policies.md#grant-data-access) auf den Time Series Insights-Explorer.

## <a name="start-a-simulation"></a>Starten einer Simulation

Bevor Sie den Time Series Insights-Explorer verwenden, konfigurieren Sie den Solution Accelerator für Gerätesimulation zum Generieren einiger Telemetriedaten. Der nachstehende Screenshot zeigt eine aktive Simulation mit 10 „Chiller“-Geräten (Kältemaschinen):

![Aktive Gerätesimulation](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights-Explorer

Der Time Series Insights-Explorer ist eine Web-App, mit der Sie Ihre Telemetriedaten visualisieren können.

1. Wählen Sie im Azure-Portal die Time Series Insights-Registerkarte **Übersicht** aus.

1. Um die Web-App Time Series Insights-Explorer zu öffnen, klicken Sie auf **Zur Umgebung wechseln**:

    ![Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Wählen Sie im Zeitauswahlpanel **Letzte 30 Minuten** aus dem Menü für schnelle Zeitangaben aus, und klicken Sie auf **Suchen**:

    ![Suche im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Wählen Sie im Bedingungspanel auf der linken Seite **Temperatur** als **Measure** und **iothub-connection-device-id** als **Teilen nach**-Wert aus:

    ![Der Screenshot zeigt das Time Series Insights-Panel „Bedingung“, in dem die Werte „Measure“ und „Teilen nach“ hervorgehoben sind.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klicken Sie mit der rechten Maustaste auf das Diagramm, und wählen Sie **Ereignisse untersuchen** aus:

    ![Ereignisse im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Die Ereignisdaten werden in einem Raster gezeigt:

    ![Tabelle im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klicken Sie auf die Schaltfläche für die Perspektivenansicht:

    ![Perspektive im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicken Sie auf **+** , um der Perspektive eine neue Abfrage hinzuzufügen:

    ![Hinzufügen einer Abfrage im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Wählen Sie **Letzte 30 Minuten** als Zeitspanne, **Luftfeuchtigkeit** als **Measure** und **iothub-connection-device-id** als **Teilen nach**-Wert aus:

    ![Abfrage im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klicken Sie auf die Schaltfläche für die Perspektivenansicht, um Ihr Dashboard mit Gerätetelemetriedaten anzuzeigen:

    ![Dashboard im Time Series Insights-Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie mehr wissen möchten, lassen Sie den Solution Accelerator für weiterhin bereitgestellt.

Falls Sie den Solution Accelerator nicht mehr benötigen, können Sie ihn auf der Seite [Bereitgestellte Lösungen](https://www.azureiotsolutions.com/Accelerators#dashboard) löschen, indem Sie ihn auswählen und dann auf **Lösung löschen** klicken.

Falls Sie der Ressourcengruppe des Solution Accelerators die Time Series Insights-Umgebung hinzugefügt haben, wird sie beim Löschen des Accelerators automatisch gelöscht. Andernfalls müssen Sie die Time Series Insights-Umgebung manuell aus dem Azure-Portal entfernen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Durchsuchen und Abfragen von Daten im Time Series Insights-Explorer finden Sie unter [Azure Time Series Insights-Explorer](../time-series-insights/time-series-insights-explorer.md).