---
title: 'Gewusst wie: Hinzufügen einer IoT-Hub-Ereignisquelle zu Azure Time Series Insights | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie eine Ereignisquelle hinzugefügt wird, die mit einem IoT-Hub in Ihrer Time Series Insights-Umgebung verbunden ist.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 12e86e2a47698d7f736b5f66a92819a4621d5e96
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165685"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Hinzufügen einer IoT Hub-Ereignisquelle zu einer Time Series Insights-Umgebung

In diesem Artikel wird beschrieben, wie im Azure-Portal eine Ereignisquelle hinzugefügt wird, die Daten aus einem Azure IoT Hub in Ihre Azure Time Series Insights-Umgebung einliest.

> [!NOTE]
> Die Anweisungen in diesem Artikel gelten sowohl für Azure Time Series Insights (allgemein verfügbar)- als auch für Time Series Insights Preview-Umgebungen.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen einer [Azure Time Series Insights-Umgebung](time-series-insights-update-create-environment.md).
* Erstellen eines [IoT-Hubs über das Azure-Portal](../iot-hub/iot-hub-create-through-portal.md).
* An den IoT-Hub müssen aktive Nachrichtenereignisse gesendet werden.
* Erstellen Sie eine dedizierte Consumergruppe in dem IoT-Hub, die die Time Series Insight-Umgebung verwenden kann. Jede Time Series Insights-Ereignisquelle benötigt eine eigene dedizierte Consumergruppe, die nicht mit anderen Consumern gemeinsam genutzt wird. Wenn mehrere Leser Ereignisse aus der gleichen Consumergruppe nutzen, werden allen Lesern wahrscheinlich Fehler angezeigt. Weitere Details finden Sie im [Azure IoT Hub-Entwicklerhandbuch](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Hinzufügen einer Consumergruppe zu Ihrem IoT Hub

Anwendungen verwenden Consumergruppen, um Daten aus Azure IoT Hub abzurufen. Geben Sie eine dedizierte Consumergruppe an, die nur von dieser Time Series Insights-Umgebung verwendet wird, um zuverlässig Daten aus Ihrem IoT-Hub zu lesen.

So fügen Sie Ihrem IoT-Hub eine neue Consumergruppe hinzu

1. Suchen Sie im Azure-Portal nach Ihrem IoT-Hub, und öffnen Sie ihn.

1. Wählen Sie unter **Einstellungen** die Option **Integrierte Endpunkte** und dann den Endpunkt **Ereignisse** aus.

   [![Auswählen der Schaltfläche „Ereignisse“ auf der Seite „Integrierte Endpunkte“](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png#lightbox)

1. Geben Sie unter **Consumergruppen** einen eindeutigen Namen für die Consumergruppe ein. Verwenden Sie diesen Namen, wenn Sie eine neue Ereignisquelle in Ihrer Time Series Insights-Umgebung erstellen.

1. Wählen Sie **Speichern** aus.

## <a name="add-a-new-event-source"></a>Hinzufügen einer neuen Ereignisquelle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen** aus. Wählen Sie Ihre Time Series Insights-Umgebung aus.

1. Wählen Sie unter **Umgebungstopologie** den Eintrag **Ereignisquellen** und dann **Hinzufügen** aus.

   [![Auswählen von „Ereignisquellen“ und anschließendes Klicken auf die Schaltfläche „Hinzufügen“](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png#lightbox)

1. Geben Sie im Bereich **Neue Ereignisquelle** für **Ereignisquellenname** einen Namen ein, der in dieser Time Series Insights-Umgebung eindeutig ist. Geben Sie z. B. **event-stream** ein.

1. Wählen Sie als **Quelle** **IoT-Hub** aus.

1. Wählen Sie einen Wert für **Importoption** aus:

   * Wenn Sie in einem Ihrer Abonnements bereits über einen IoT-Hub verfügen, wählen Sie **IoT-Hub aus verfügbaren Abonnements verwenden** aus. Diese Option stellt den einfachsten Ansatz dar.
   
     [![Auswählen von Optionen im Bereich „Neue Ereignisquelle“](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png#lightbox)

    * In der folgenden Tabelle werden die Eigenschaften beschrieben, die für die Option **IoT Hub aus verfügbaren Abonnements verwenden** erforderlich sind:

       [![Bereich „Neue Ereignisquelle“: In der Option „IoT Hub aus verfügbaren Abonnements verwenden“ festzulegende Eigenschaften](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png#lightbox)

       | Eigenschaft | BESCHREIBUNG |
       | --- | --- |
       | Abonnement-ID | Wählen Sie das Abonnement aus, in dem dieser IoT-Hub erstellt wurde.
       | IoT Hub-Name | Wählen Sie den Namen des IoT-Hubs aus.
       | IoT Hub-Richtlinienname | Wählen Sie die SAS-Richtlinie aus. Sie finden die SAS-Richtlinie auf der Registerkarte „IoT-Hub-Einstellungen“. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung zum Herstellen einer **Dienstverbindung** verfügen.
       | IoT Hub-Richtlinienschlüssel | Der Schlüssel ist bereits angegeben.
       | IoT Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem IoT-Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden.
       | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Die Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
       | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den IoT-Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

    * Wenn sich der IoT-Hub außerhalb Ihrer Abonnements befindet oder Sie erweiterte Optionen auswählen möchten, wählen Sie **IoT-Hub-Einstellungen manuell angeben** aus.

      In der folgenden Tabelle werden die für die Option **IoT Hub-Einstellungen manuell angeben** erforderlichen Eigenschaften beschrieben:

       | Eigenschaft | BESCHREIBUNG |
       | --- | --- |
       | Abonnement-ID | Das Abonnement, in dem der IoT-Hub erstellt wurde.
       | Ressourcengruppe | Der Name der Ressourcengruppe, in der der IoT-Hub erstellt wurde.
       | IoT Hub-Name | Der Name Ihres IoT-Hubs. Bei der Erstellung Ihres IoT-Hubs haben Sie einen Namen für den IoT-Hub eingegeben.
       | IoT Hub-Richtlinienname | Die SAS-Richtlinie. Sie können die SAS-Richtlinie auf der Registerkarte „IoT-Hub-Einstellungen“ erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung zum Herstellen einer **Dienstverbindung** verfügen.
       | IoT Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Azure Service Bus-Namespace verwendet wird. Geben Sie hier den primären oder sekundären Schlüssel ein.
       | IoT Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem IoT-Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden.
       | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Die Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
       | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den IoT-Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

1. Fügen Sie den dedizierten Time Series Insights-Consumergruppennamen hinzu, den Sie Ihrem IoT-Hub hinzugefügt haben.

1. Klicken Sie auf **Erstellen**.

   [![Schaltfläche „Erstellen“](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png)](media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png#lightbox)

1. Nach der Erstellung der Ereignisquelle beginnt Time Series Insights automatisch damit, Daten in Ihre Umgebung zu streamen.

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Datenzugriffsrichtlinien](time-series-insights-data-access.md) zum Schützen der Daten

* [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle

* Zugreifen auf Ihre Umgebung über den [Time Series Insights-Explorer](https://insights.timeseries.azure.com)
