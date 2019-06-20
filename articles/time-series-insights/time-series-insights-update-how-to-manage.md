---
title: Bereitstellen und Verwalten der Vorschauversion von Azure Time Series Insights | Microsoft-Dokumentation
description: Grundlagen zum Bereitstellen und Verwalten von Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6251df2317ceff9dded92f2d829bfab0503fdf1b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237589"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Bereitstellen und Verwalten der Vorschauversion von Azure Time Series Insights

In diesem Artikel wird beschrieben, wie Sie eine Azure Time Series Insights-Umgebung (Vorschauversion) mit dem [Azure-Portal](https://portal.azure.com/) erstellen.

## <a name="overview"></a>Übersicht

Azure Time Series Insights-Umgebungen (Vorschauversion) sind Umgebungen mit nutzungsbasierter Bezahlung.

Wenn Sie eine Azure Time Series Insights Preview-Umgebung bereitstellen, erstellen Sie zwei Azure-Ressourcen:

* Eine Azure Time Series Insights Preview-Umgebung  
* Ein Azure Storage-Konto vom Typ „Allgemein v1“
  
Erfahren Sie, [wie Sie Ihre Umgebung planen](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Stellen Sie für die Vorschauversion sicher, dass Sie ein GPv1-Konto (Azure Storage Universell V1) verwenden.

Sie können jede Azure Time Series Insights-Umgebung (Vorschauversion) optional mit einer Ereignisquelle verknüpfen. Weitere Informationen finden Sie unter [Hinzufügen einer Event Hub-Quelle](./time-series-insights-how-to-add-an-event-source-eventhub.md) und [Hinzufügen einer IoT Hub-Quelle](./time-series-insights-how-to-add-an-event-source-iothub.md). Sie geben in diesem Schritt eine „Timestamp ID“-Eigenschaft sowie eine eindeutige Consumergruppe an. Auf diese Weise wird sichergestellt, dass die Umgebung Zugriff auf die entsprechenden Ereignisse hat.

Nach Abschluss der Bereitstellung können Sie Ihre Zugriffsrichtlinien und andere Umgebungsattribute gemäß Ihren geschäftlichen Anforderungen ändern.

## <a name="create-the-environment"></a>Erstellen der Umgebung

Die folgenden Schritte beschreiben, wie Sie eine Azure Time Series Insights Preview-Umgebung erstellen:

1. Wählen Sie die Schaltfläche **PAYG** im Menü **SKU** aus. Geben Sie einen Umgebungsnamen an, und wählen Sie aus, welche Abonnementgruppe und welche Ressourcengruppe verwendet werden sollen. Wählen Sie dann einen unterstützten Ort für die Umgebung aus, wo sie gehostet werden soll.

   [![Erstellen Sie eine Azure Time Series Insights-Instanz.](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

1. Geben Sie eine Time Series-ID ein.

    >[!NOTE]
    > * Bei der Time Series-ID muss die Groß-/Kleinschreibung beachtet werden. Sie ist zudem unveränderlich. (Nach dem Festlegen kann sie nicht mehr geändert werden.)
    > * Time Series-IDs können aus bis zu drei Schlüsseln bestehen.
    > * Weitere Informationen zum Auswählen einer Time Series-ID finden Sie unter [Auswählen einer Time Series-ID](./time-series-insights-update-how-to-id.md).

1. Erstellen Sie ein Azure Storage-Konto, indem Sie einen Namen für das Konto auswählen und eine Replikationsauswahl treffen. Hierdurch wird automatisch ein Azure Storage-Konto vom Typ „Allgemein v1“ erstellt. Es wird in derselben Region wie die Azure Time Series Insights-Umgebung (Vorschauversion) erstellt, die Sie zuvor ausgewählt haben.

    [![Erstellen eines Azure Storage-Kontos für Ihre Instanz](media/v2-update-manage/manage_five.PNG)](media/v2-update-manage/manage_five.PNG#lightbox)

1. Optional können Sie eine Ereignisquelle hinzufügen.

   * Time Series Insights unterstützt [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) und [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) als Optionen. Sie können bei der Erstellung der Umgebung nur eine einzelne Ereignisquelle hinzufügen, später jedoch eine weitere Ereignisquelle hinzufügen. Es wird empfohlen, eine eindeutige Consumergruppe zu erstellen, um sicherzustellen, dass alle Ereignisse für Ihre Azure Time Series Insights-Instanz (Vorschauversion) sichtbar sind. Sie können eine vorhandene Consumergruppe auswählen oder eine neue Consumergruppe erstellen, wenn Sie die Ereignisquelle hinzufügen.

   * Sie sollten auch die entsprechende Timestamp-Eigenschaft auswählen. Standardmäßig verwendet Azure Time Series Insights die in der Warteschlange verbrachte Zeit einer Nachricht für jede Ereignisquelle.

     > [!TIP]
     > Die in der Warteschlange verbrachte Zeit einer Nachricht ist möglicherweise nicht die beste konfigurierte Einstellung, um sie in Uploadszenarien mit Batch-Ereignissen oder Verlaufsdaten zu verwenden. Stellen Sie sicher, dass Sie Ihre Entscheidung überprüfen, ob in solchen Fällen eine „Timestamp“-Eigenschaft verwendet werden soll oder nicht.

     [![Registerkarte „Ereignisquelle“](media/v2-update-manage/manage_two.PNG)](media/v2-update-manage/manage_two.PNG#lightbox)

1. Vergewissern Sie sich, dass Ihre Umgebung mit den gewünschten Einstellungen bereitgestellt wurde.

    [![Registerkarte „Bewerten + erstellen“](media/v2-update-manage/manage_three.PNG)](media/v2-update-manage/manage_three.PNG#lightbox)

## <a name="manage-the-environment"></a>Verwalten der Umgebung

Sie können Ihre Azure Time Series Insights-Umgebung (Vorschauversion) über das Azure-Portal verwalten. Im Folgenden finden Sie die wichtigsten Unterschiede bei der Verwaltung einer Azure Time Series Insights-Umgebung (Vorschauversion) mit nutzungsbasierter Bezahlung im Vergleich mit einer S1- oder S2-Umgebung über das Azure-Portal:

* Das Blatt **Übersicht** im Azure-Portal ist in Azure Time Series Insights unverändert, mit Ausnahme der folgenden Unterschiede:
  * „Kapazität“ wird entfernt, weil dieses Konzept für Umgebungen mit nutzungsbasierter Bezahlung nicht relevant ist.
  * Die Eigenschaft „Time Series-ID“ wurde hinzugefügt. Sie bestimmt, wie Ihre Daten partitioniert werden.
  * Verweisdatasets wurden entfernt.
  * Die angezeigte URL leitet Sie zum [Azure Time Series Insights Preview-Explorer](./time-series-insights-update-explorer.md) weiter.
  * Der Name Ihres Azure Storage-Kontos wird aufgeführt.

* Das Blatt **Konfigurieren** im Azure-Portal wurde in der Vorschauversion von Azure Time Series Insights entfernt, da Umgebungen mit nutzungsbasierter Bezahlung nicht konfigurierbar sind.

* Das Datenblatt **Verweisdaten** im Azure-Portal wurde in der Vorschauversion von Azure Time Series Insights entfernt, da Verweisdaten keine Komponente von Umgebungen mit nutzungsbasierter Bezahlung sind.

[![Time Series Insights-Umgebung (Vorschauversion) im Azure-Portal](media/v2-update-manage/manage_four.PNG)](media/v2-update-manage/manage_four.PNG#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Planen der Umgebung](./time-series-insights-update-plan.md).

- Informieren Sie sich über das [Hinzufügen einer Event Hub-Quelle](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurieren [einer IoT-Hub-Quelle](./time-series-insights-how-to-add-an-event-source-iothub.md).