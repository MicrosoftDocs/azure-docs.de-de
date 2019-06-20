---
title: Hinzufügen einer Event Hubs-Ereignisquelle zu Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie eine Ereignisquelle hinzugefügt wird, die mit Azure Event Hubs in Ihrer Time Series Insights-Umgebung verbunden ist.
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
ms.openlocfilehash: 21e054aefab0ee5535376ac86ebbaf1316e671b5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165694"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Hinzufügen einer Event Hub-Ereignisquelle zu einer Time Series Insights-Umgebung

In diesem Artikel wird beschrieben, wie im Azure-Portal eine Ereignisquelle hinzugefügt wird, die Daten aus Azure Event Hubs in Ihre Azure Time Series Insights-Umgebung einliest.

> [!NOTE]
> Die in diesem Artikel beschriebenen Schritte gelten sowohl für Azure Time Series Insights (allgemein verfügbar)- als auch für Time Series Insights Preview-Umgebungen.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie eine Time Series Insights-Umgebung, wie dies im Artikel [Erstellen einer Azure Time Series Insights-Umgebung](./time-series-insights-update-create-environment.md) beschrieben wird.
- Erstellen einer Event Hub-Instanz Weitere Informationen hierzu finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md).
- An den Event Hub müssen aktive Nachrichtenereignisse gesendet werden. Im Artikel [Senden von Ereignissen an Azure Event Hubs mithilfe von .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md) wird beschrieben, wie Sie hierzu vorgehen.
- Erstellen Sie eine dedizierte Consumergruppe in dem Event Hub, die die Time Series Insight-Umgebung verwenden kann. Jede Time Series Insights-Ereignisquelle benötigt eine eigene dedizierte Consumergruppe, die nicht mit anderen Consumern gemeinsam genutzt wird. Wenn mehrere Leser Ereignisse aus der gleichen Consumergruppe nutzen, werden allen Lesern wahrscheinlich Fehler angezeigt. Es gilt ein Grenzwert von 20 Consumergruppen pro Event Hub. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Hinzufügen einer Consumergruppe zum Event Hub

Anwendungen verwenden Consumergruppen, um Daten aus Azure Event Hubs abzurufen. Für ein zuverlässiges Lesen von Daten aus Ihrem Event Hub geben Sie eine dedizierte Consumergruppe an, die nur von dieser Time Series Insights-Umgebung verwendet wird.

So fügen Sie Ihrem Event Hub eine neue Consumergruppe hinzu

1. Suchen Sie im Azure-Portal den Event Hub, und öffnen Sie ihn.

1. Wählen Sie unter **Entitäten** **Consumergruppen** aus, und wählen Sie dann **Consumergruppe** aus.

   [![Event hub - Add a consumer group](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox) (Event Hub – Consumergruppe hinzufügen)

1. Geben Sie auf der Seite **Consumergruppen** einen neuen eindeutigen Wert für **Name** an.  Verwenden Sie diesen Namen, wenn Sie eine neue Ereignisquelle in der Time Series Insights-Umgebung erstellen.

1. Klicken Sie auf **Erstellen**.

## <a name="add-a-new-event-source"></a>Hinzufügen einer neuen Ereignisquelle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie die vorhandene Time Series Insights-Umgebung. Wählen Sie im linken Menü **Alle Ressourcen**, und wählen Sie dann Ihre Time Series Insights-Umgebung aus.

1. Wählen Sie unter **Umgebungstopologie** den Eintrag **Ereignisquellen** und dann **Hinzufügen** aus.

   [![Wählen Sie unter „Ereignisquellen“ die Schaltfläche „Hinzufügen“ aus](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox).

1. Geben Sie einen **Namen der Ereignisquelle** ein, der eindeutig Namen für diese Time Series Insights-Umgebung ist, z. B. **event-stream**.

1. Wählen Sie für **Quelle** die Option **Event Hub** aus.

1. Wählen Sie die geeigneten Werte für **Importoption** aus:
   - Wenn Sie in einem Ihrer Abonnements bereits über einen Event Hub verfügen, wählen Sie **Verwenden Sie einen Event Hub aus verfügbaren Abonnements** aus. Diese Option stellt den einfachsten Ansatz dar.

       [![Geben Sie im Bereich „Neue Ereignisquelle“ Werte für die ersten drei Parameter ein](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox).


       [![Details zu Abonnements und Event Hub](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     In der folgenden Tabelle werden die Eigenschaften beschrieben, die für die Option **Event Hub aus verfügbaren Abonnements verwenden** erforderlich sind:

     | Eigenschaft | BESCHREIBUNG |
     | --- | --- |
     | Abonnement-ID | Wählen Sie das Abonnement, in dem dieser Event Hub erstellt wurde.
     | Service Bus-Namespace | Wählen Sie den Azure Service Bus-Namespace, der den Event Hub enthält.
     | Event Hub-Name | Wählen Sie den Namen des Event Hubs.
     | Event Hub-Richtlinienname | Wählen Sie die SAS-Richtlinie aus. Sie können die Richtlinie für den gemeinsamen Zugriff auf der Event Hub-Registerkarte **Konfigurieren** erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung **Lesen** verfügen.
     | Event Hub-Richtlinienschlüssel | Der Schlüsselwert ist möglicherweise bereits angegeben.
     | Event Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem Event Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden. |
     | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
     | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den Event Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

    - Wenn sich der Event Hub außerhalb Ihrer Abonnements befindet oder Sie erweiterte Optionen auswählen möchten, wählen Sie **Event Hub-Einstellungen manuell angeben** aus.

      In der folgenden Tabelle werden die für die Option **Event Hub-Einstellungen manuell angeben** erforderlichen Eigenschaften beschrieben:
 
      | Eigenschaft | BESCHREIBUNG |
      | --- | --- |
      | Abonnement-ID | Das Abonnement, in dem dieser Event Hub erstellt wurde.
      | Ressourcengruppe | Die Ressourcengruppe, in der dieser Event Hub erstellt wurde.
      | Service Bus-Namespace | Ein Service Bus-Namespace ist ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt.
      | Event Hub-Name | Der Name Ihres Event Hubs. Bei der Erstellung des Event Hubs haben Sie ihm auch einen bestimmten Namen zugewiesen.
      | Event Hub-Richtlinienname | Die SAS-Richtlinie. Sie können eine Richtlinie für den gemeinsamen Zugriff auf der Event Hub-Registerkarte **Konfigurieren** erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung **Lesen** verfügen.
      | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. Geben Sie hier den primären oder sekundären Schlüssel ein.
      | Event Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem Event Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden.
      | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
      | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den Event Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

1. Fügen Sie den dedizierten Time Series Insights-Consumergruppennamen hinzu, den Sie Ihrem Event Hub hinzugefügt haben.

1. Klicken Sie auf **Erstellen**.

   [![Wählen Sie „Erstellen“ aus](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox).

   Nach der Erstellung der Ereignisquelle beginnt Time Series Insights automatisch damit, Daten in Ihre Umgebung zu streamen.

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Datenzugriffsrichtlinien](time-series-insights-data-access.md) zum Schützen der Daten

* [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle

* Zugreifen auf Ihre Umgebung über den [Time Series Insights-Explorer](https://insights.timeseries.azure.com)
