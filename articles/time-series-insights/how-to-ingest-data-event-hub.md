---
title: Hinzufügen einer Event Hubs-Ereignisquelle – Azure Time Series Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihrer Azure Time Series Insights-Umgebung eine Azure Event Hubs-Ereignisquelle hinzufügen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 7d672da30a4d81ead9115fc6b829c35aeab3ee6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016818"
---
# <a name="add-an-event-hub-event-source-to-your-azure-time-series-insights-environment"></a>Hinzufügen einer Event Hub-Ereignisquelle zu Ihrer Azure Time Series Insights-Umgebung

In diesem Artikel wird beschrieben, wie im Azure-Portal eine Ereignisquelle hinzugefügt wird, die Daten aus Azure Event Hubs in Ihre Azure Time Series Insights-Umgebung einliest.

> [!NOTE]
> Die in diesem Artikel beschriebenen Schritte gelten sowohl für Azure Time Series Insights Gen1- als auch für Azure Time Series Insights Gen2-Umgebungen.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie eine Azure Time Series Insights-Umgebung, wie im Artikel [Erstellen einer Azure Time Series Insights-Umgebung](./tutorials-set-up-tsi-environment.md) beschrieben.
- Erstellen einer Event Hub-Instanz Weitere Informationen hierzu finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](../event-hubs/event-hubs-create.md).
- An den Event Hub müssen aktive Nachrichtenereignisse gesendet werden. Im Artikel [Senden von Ereignissen an Azure Event Hubs mithilfe von .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md) wird beschrieben, wie Sie hierzu vorgehen.
- Erstellen Sie eine dedizierte Consumergruppe in dem Event Hub, den die Azure Time Series Insights-Umgebung verwenden kann. Jede Azure Time Series Insights-Ereignisquelle benötigt eine eigene dedizierte Consumergruppe, die nicht mit anderen Consumern gemeinsam genutzt wird. Wenn mehrere Leser Ereignisse aus der gleichen Consumergruppe nutzen, geben alle Leser wahrscheinlich Fehler aus. Es gilt ein Grenzwert von 20 Consumergruppen pro Event Hub. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Hinzufügen einer Consumergruppe zum Event Hub

Anwendungen verwenden Consumergruppen, um Daten aus Azure Event Hubs abzurufen. Geben Sie für ein zuverlässiges Lesen von Daten aus Ihrem Event Hub eine dedizierte Consumergruppe an, die nur von dieser Azure Time Series Insights-Umgebung verwendet wird.

So fügen Sie Ihrem Event Hub eine neue Consumergruppe hinzu

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) Ihre Event Hub-Instanz im Bereich **Übersicht** des Namespace Ihres Event-Hubs, und öffnen Sie sie. Wählen Sie **Entitäten > Event-Hubs** aus, oder suchen Sie unter **Name** nach Ihrer Instanz.

    [![Öffnen Ihres Event Hub-Namespace](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. Wählen Sie in der Event Hub-Instanz **Entitäten > Consumergruppen** aus. Wählen Sie dann **+ Consumergruppe** aus, um eine neue Consumergruppe hinzuzufügen.

   [![Event hub - Add a consumer group](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox) (Event Hub – Consumergruppe hinzufügen)

   Wählen Sie andernfalls eine vorhandene Consumergruppe aus, und fahren Sie mit dem nächsten Abschnitt fort.

1. Geben Sie auf der Seite **Consumergruppen** einen neuen eindeutigen Wert für **Name** an.  Verwenden Sie diesen Namen, wenn Sie eine neue Ereignisquelle in der Azure Time Series Insights-Umgebung erstellen.

1. Klicken Sie auf **Erstellen**.

## <a name="add-a-new-event-source"></a>Hinzufügen einer neuen Ereignisquelle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie die vorhandene Azure Time Series Insights-Umgebung. Wählen Sie im linken Menü **Alle Ressourcen** und dann Ihre Azure Time Series Insights-Umgebung aus.

1. Wählen Sie **Ereignisquellen** und dann **Hinzufügen** aus.

   [![Wählen Sie unter „Ereignisquellen“ die Schaltfläche „Hinzufügen“ aus](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox).

1. Geben Sie einen Wert für **Name der Ereignisquelle** ein, der für diese Azure Time Series Insights-Umgebung eindeutig ist, z. B. `Contoso-TSI-Gen 1-Event-Hub-ES`.

1. Wählen Sie für **Quelle** die Option **Event Hub** aus.

1. Wählen Sie die geeigneten Werte für **Importoption** aus:

   - Wenn Sie in einem Ihrer Abonnements bereits über einen Event Hub verfügen, wählen Sie **Verwenden Sie einen Event Hub aus verfügbaren Abonnements** aus. Diese Option stellt den einfachsten Ansatz dar.

     [![Auswählen einer Importoption für Ereignisquellen](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

   - In der folgenden Tabelle werden die Eigenschaften beschrieben, die für die Option **Event Hub aus verfügbaren Abonnements verwenden** erforderlich sind:

       [![Details zu Abonnements und Event Hub](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

       | Eigenschaft | BESCHREIBUNG |
       | --- | --- |
       | Subscription | Das Abonnement, zu dem die gewünschte Event Hub-Instanz und der Namespace gehören. |
       | Event Hub-Namespace | Der Event Hub-Namespace, zu dem die gewünschte Event Hub-Instanz gehört. |
       | Event Hub-Name | Der Name der gewünschten Event Hub-Instanz. |
       | Event Hub-Richtlinienwert | Wählen Sie die gewünschte SAS-Richtlinie aus. Sie können die Richtlinie für den gemeinsamen Zugriff auf der Event Hub-Registerkarte **Konfigurieren** erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung **Lesen** verfügen. |
       | Event Hub-Richtlinienschlüssel | Voraufgefüllt aus dem ausgewählten Event Hub-Richtlinienwert. |

   - Wenn sich der Event Hub außerhalb Ihrer Abonnements befindet oder Sie erweiterte Optionen auswählen möchten, wählen Sie **Event Hub-Einstellungen manuell angeben** aus.

       In der folgenden Tabelle werden die für die Option **Event Hub-Einstellungen manuell angeben** erforderlichen Eigenschaften beschrieben:

       | Eigenschaft | BESCHREIBUNG |
       | --- | --- |
       | Abonnement-ID | Das Abonnement, zu dem die gewünschte Event Hub-Instanz und der Namespace gehören. |
       | Resource group | Die Ressourcengruppe, zu der die gewünschte Event Hub-Instanz und der Namespace gehören. |
       | Event Hub-Namespace | Der Event Hub-Namespace, zu dem die gewünschte Event Hub-Instanz gehört. |
       | Event Hub-Name | Der Name der gewünschten Event Hub-Instanz. |
       | Event Hub-Richtlinienwert | Wählen Sie die gewünschte SAS-Richtlinie aus. Sie können die Richtlinie für den gemeinsamen Zugriff auf der Event Hub-Registerkarte **Konfigurieren** erstellen. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Die SAS-Richtlinie für die Ereignisquelle *muss* über die Berechtigung **Lesen** verfügen. |
       | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Service Bus-Namespace verwendet wird. Geben Sie hier den primären oder sekundären Schlüssel ein. |

   - Beide Optionen teilen die folgenden Konfigurationsoptionen:

       | Eigenschaft | BESCHREIBUNG |
       | --- | --- |
       | Event Hub-Consumergruppe | Die Consumergruppe, die Ereignisse aus dem Event Hub liest. Wir empfehlen ausdrücklich, dass Sie eine dedizierte Consumergruppe für Ihre Ereignisquelle verwenden. |
       | Ereignisserialisierungsformat | Zurzeit ist JSON das einzige verfügbare Serialisierungsformat. Ereignismeldungen müssen in diesem Format vorliegen, damit Daten gelesen werden können. |
       | Name der Timestamp-Eigenschaft | Um diesen Wert ermitteln, müssen Sie das Nachrichtenformat der Nachrichtendaten kennen, die an den Event Hub gesendet werden. Dieser Wert entspricht **name** der spezifischen Ereigniseigenschaft in den Nachrichtendaten, die Sie als Ereigniszeitstempel verwenden möchten. Bei dem Wert wird die Groß-/Kleinschreibung beachtet. Wenn dieser Wert nicht angegeben wird, wird der Zeitpunkt der **Einreihung des Ereignisses** in die Warteschlange in der Ereignisquelle als Ereigniszeitstempel verwendet. |

1. Fügen Sie den Namen der dedizierten Azure Time Series Insights-Consumergruppe hinzu, die Sie Ihrem Event Hub hinzugefügt haben.

1. Klicken Sie auf **Erstellen**.

   Nach dem Erstellen der Ereignisquelle beginnt Azure Time Series Insights automatisch damit, Daten in Ihre Umgebung zu streamen.

## <a name="next-steps"></a>Nächste Schritte

- [Definieren von Datenzugriffsrichtlinien](./concepts-access-policies.md) zum Schützen der Daten

- [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle

- Zugreifen auf Ihre Umgebung im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com).