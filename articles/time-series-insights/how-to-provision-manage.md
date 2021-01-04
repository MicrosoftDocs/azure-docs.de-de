---
title: Bereitstellen und Verwalten einer Gen2-Umgebung – Azure Time Series | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure Time Series Insights Gen2-Umgebung bereitstellen und verwalten.
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 7c38c57a8480ef2addde494b94d70bd2eb679373
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016767"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Bereitstellen und Verwalten von Azure Time Series Insights Gen2

In diesem Artikel wird beschrieben, wie Sie eine Azure Time Series Insights Gen2-Umgebung mit dem [Azure-Portal](https://portal.azure.com/) erstellen.

## <a name="overview"></a>Übersicht

Wenn Sie eine Azure Time Series Insights Gen2-Umgebung bereitstellen, erstellen Sie diese Azure-Ressourcen:

* Eine Azure Time Series Insights Gen2-Umgebung, die das nutzungsbasierte Bezahlmodell verwendet
* Ein Azure-Speicherkonto
* Einen optionalen warmen Speicher für schnellere und unbegrenzte Abfragen

> [!TIP]
>
> * Erfahren Sie, [wie Sie Ihre Umgebung planen](./how-to-plan-your-environment.md).
> * Hier finden Sie weitere Informationen zum [Hinzufügen einer Event Hub-Quelle](./how-to-ingest-data-event-hub.md) und zum [Hinzufügen einer IoT Hub-Quelle](./how-to-ingest-data-iot-hub.md).

Sie lernen Folgendes:

1. Ordnen Sie jede Azure Time Series Insights Gen2-Umgebung einer Ereignisquelle zu. Außerdem geben Sie eine Timestamp-ID-Eigenschaft und eine eindeutige Consumergruppe an, um sicherzustellen, dass die Umgebung auf die entsprechenden Ereignisse zugreifen kann.

1. Nach Abschluss der Bereitstellung können Sie Ihre Zugriffsrichtlinien und andere Umgebungsattribute gemäß Ihren geschäftlichen Anforderungen ändern.

   > [!NOTE]
   > Der erste Schritt ist bei der Bereitstellung einer Umgebung optional. Wenn Sie diesen Schritt überspringen, müssen Sie der Umgebung später eine Ereignisquelle anfügen, damit der Datenfluss in Ihre Umgebung beginnen und mittels Abfragen auf diese Daten zugegriffen werden kann.

## <a name="create-the-environment"></a>Erstellen der Umgebung

So erstellen Sie eine Azure Time Series Insights Gen2-Umgebung

1. Erstellen Sie eine Azure Time Series Insights-Ressource unter *Internet der Dinge* im [Azure-Portal](https://portal.azure.com/).

1. Wählen Sie **Gen2(L1)** als **Ebene** aus. Geben Sie einen Umgebungsnamen an, und wählen Sie die zu verwendende Abonnementgruppe und Ressourcengruppe aus. Wählen Sie dann einen unterstützten Ort zum Hosten der Umgebung aus.

   [![Erstellen Sie eine Azure Time Series Insights-Instanz.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Geben Sie eine Time Series-ID ein.

    > [!NOTE]
    >
    > * Bei der Time Series-ID muss die *Groß-/Kleinschreibung* beachtet werden. Sie ist zudem *unveränderlich*. (Nach dem Festlegen kann sie nicht mehr geändert werden.)
    > * Time Series-IDs können aus bis zu *drei* Schlüsseln bestehen. Stellen Sie sich dies als einen Primärschlüssel in einer Datenbank vor, der eindeutig jeden Gerätesensor darstellt, der Daten an Ihre Umgebung senden würde. Dabei könnte es sich um eine Eigenschaft oder eine Kombination aus bis zu drei Eigenschaften handeln.
    > * Hier finden Sie weitere Informationen zum [Auswählen einer Time Series-ID](./how-to-select-tsid.md)

1. Erstellen Sie ein Azure-Speicherkonto, indem Sie einen Speicherkontonamen und Kontotyp auswählen und eine [Replikations](../storage/common/redundancy-migration.md?tabs=portal)auswahl treffen. Hierdurch wird automatisch ein Azure Storage-Konto erstellt. Standardmäßig wird ein Konto vom Typ [Universell v2](../storage/common/storage-account-overview.md) erstellt. Das Konto wird in derselben Region wie die Azure Time Series Insights Gen2-Umgebung erstellt, die Sie zuvor ausgewählt haben.
Alternativ können Sie auch Ihren eigenen Speicher (BYOS) über eine [ARM-Vorlage](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) verwenden, wenn Sie eine neue Azure Time Series Gen2-Umgebung erstellen.

1. **(Optional)** Aktivieren Sie den warmen Speicher für Ihre Umgebung, wenn Sie schnellere und unbegrenzte Abfragen über die neuesten Daten in Ihrer Umgebung wünschen. Sie können auch einen warmen Speicher über die Option **Speicherkonfiguration** im linken Navigationsbereich erstellen oder löschen, nachdem Sie eine Azure Time Series Insights Gen2-Umgebung erstellt haben.

1. **(Optional)** Sie können jetzt eine Ereignisquelle hinzufügen. Sie können aber auch warten, bis die Instanz bereitgestellt wurde.

   * Azure Time Series Insights unterstützt [Azure IoT Hub](./how-to-ingest-data-iot-hub.md) und [Azure Event Hubs](./how-to-ingest-data-event-hub.md) als Ereignisquellenoptionen. Zwar können Sie bei der Erstellung der Umgebung nur eine einzelne Ereignisquelle hinzufügen, doch können Sie später eine weitere Ereignisquelle hinzufügen.

     Sie können eine vorhandene Consumergruppe auswählen oder eine neue Consumergruppe erstellen, wenn Sie die Ereignisquelle hinzufügen. Beachten Sie, dass die Ereignisquelle immer eine eindeutige Consumergruppe erfordert, damit Ihre Umgebung Daten in diese einlesen kann.

   * Wählen Sie die entsprechende Timestamp-Eigenschaft aus. Standardmäßig verwendet Azure Time Series Insights die in der Warteschlange verbrachte Zeit einer Nachricht für jede Ereignisquelle.

     > [!TIP]
     > Die in der Warteschlange verbrachte Zeit einer Nachricht ist möglicherweise nicht die beste konfigurierte Einstellung, um sie in Batchereignisszenarien oder Uploadszenarien mit Verlaufsdaten zu verwenden. Stellen Sie in solchen Fällen sicher, dass Sie Ihre Entscheidung überprüfen, ob eine „Timestamp“-Eigenschaft verwendet werden soll oder nicht.

     [![Registerkarte „Event Source configuration“](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox) (Ereignisquellen-Konfiguration)

1. Vergewissern Sie sich, dass Ihre Umgebung in der gewünschten Weise bereitgestellt und konfiguriert wurde.

    [![Registerkarte „Bewerten + erstellen“](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Verwalten der Umgebung

Sie können Ihre Azure Time Series Insights Gen2-Umgebung über das Azure-Portal verwalten. Wenn Sie Ihre Umgebung über das Azure-Portal verwalten, müssen Sie einige wichtige Unterschiede zwischen einer Gen2-Umgebung und Gen1 S1- oder Gen1 S2-Umgebungen berücksichtigen:

* Das Blatt **Übersicht** für Gen2 im Azure-Portal weist die folgenden Änderungen auf:

  * Die Kapazität wird entfernt, da sie nicht für Gen2-Umgebungen gilt.
  * Die Eigenschaft **Time Series-ID** wird hinzugefügt. Sie bestimmt, wie Ihre Daten partitioniert werden.
  * Verweisdatasets wurden entfernt.
  * Die angezeigte URL leitet Sie zum [Azure Time Series Insights-Explorer](./concepts-ux-panels.md) weiter.
  * Der Name Ihres Azure-Speicherkontos wird aufgeführt.

* Das Blatt **Konfigurieren** im Azure-Portal wurde entfernt, weil Skalierungseinheiten nicht für Azure Time Series Insights Gen2-Umgebungen gelten. Sie können jedoch **Speicherkonfiguration** verwenden, um den neu eingeführten warmen Speicher zu konfigurieren.

* Das Blatt **Verweisdaten** im Azure-Portal wurde in Azure Time Series Insights Gen2 entfernt, da das Konzept von Verweisdaten durch das [Zeitreihenmodell (Time Series Model, TSM)](./concepts-model-overview.md) ersetzt wurde.

[![Azure Time Series Insights Gen2-Umgebung im Azure-Portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über allgemein verfügbare Umgebungen sowie Gen2-Umgebungen von Azure Time Series Insights, indem Sie [Planen Ihrer Umgebung](./how-to-plan-your-environment.md) lesen.

* Informieren Sie sich über das [Hinzufügen einer Event Hub-Quelle](./how-to-ingest-data-event-hub.md).

* Konfigurieren [einer IoT-Hub-Quelle](./how-to-ingest-data-iot-hub.md).