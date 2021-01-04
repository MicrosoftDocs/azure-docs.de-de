---
title: 'Erstellen einer Umgebung: Azure Time Series Insights | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie eine neue Azure Time Series Insights-Umgebung über das Azure-Portal erstellen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 61af7922318514a7b86a349d1970c59d4d168d85
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023325"
---
# <a name="create-a-new-azure-time-series-insights-gen1-environment-in-the-azure-portal"></a>Erstellen einer neuen Azure Time Series Insights Gen1-Umgebung über das Azure-Portal

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel wird beschrieben, wie Sie eine neue Azure Time Series Insights-Umgebung über das Azure-Portal erstellen.

Azure Time Series Insights ermöglicht Ihnen den Einstieg in die Visualisierung und Abfrage des Dateneingangs in Azure IoT Hubs und Event Hubs in wenigen Minuten, sodass Sie große Mengen an Zeitreihendaten innerhalb von Sekunden abfragen können.  Dieser Dienst wurde für die Skalierung des Internets der Dinge (Internet of Things, IoT) entwickelt und kann Daten im Terabytebereich verarbeiten.

## <a name="steps-to-create-the-environment"></a>Schritte zum Erstellen der Umgebung

Führen Sie zum Erstellen einer Umgebung die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie die Schaltfläche **+ Ressource erstellen** aus.

1. Wählen Sie die Kategorie **Internet der Dinge (IoT)** und dann **Time Series Insights** aus.

   [![Erstellen der Azure Time Series Insights-Umgebung](media/time-series-insights-get-started/tsi-create-new-environment.png)](media/time-series-insights-get-started/tsi-create-new-environment.png#lightbox)

1. Wählen Sie auf der Seite **Time Series Insights** die Option **Erstellen** aus.

1. Füllen Sie die erforderlichen Parameter aus. In der folgenden Tabelle werden die einzelnen Parameter erläutert:

   [![Erstellen der Azure Time Series Insights-Ressourcengruppe](media/time-series-insights-get-started/tsi-configure-and-create.png)](media/time-series-insights-get-started/tsi-configure-and-create.png#lightbox)

   Einstellung|Vorgeschlagener Wert|BESCHREIBUNG
   ---|---|---
   Umgebungsname | Ein eindeutiger Name | Unter diesem Namen wird die Umgebung im [Time Series-Explorer](https://insights.timeseries.azure.com) dargestellt.
   Subscription | Ihr Abonnement | Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das die Ereignisquelle vorzugsweise enthält. Azure Time Series Insights kann Azure IoT Hub- und Event Hub-Ressourcen im gleichen Abonnement automatisch erkennen.
   Resource group | Erstellen einer neuen oder Verwenden einer vorhandenen Ressourcengruppe | Eine Ressourcengruppe ist eine Sammlung von Azure-Ressourcen, die zusammen verwendet werden. Sie können eine vorhandene Ressourcengruppe auswählen, z.B. die Gruppe, die Ihren Event Hub oder IoT Hub enthält. Oder Sie können eine neue Ressourcengruppe erstellen, wenn die Ressource nicht mit den anderen Ressourcen verbunden ist.
   Location | Am nächsten zu Ihrer Ereignisquelle gelegen | Wählen Sie vorzugsweise denselben Rechenzentrumsstandort aus, der die Daten Ihrer Ereignisquelle enthält, um so beim Verschieben von Daten außerhalb der Region zusätzliche regionsübergreifende und zonenübergreifende Breitbandkosten und eine erhöhte Latenz zu vermeiden.
   Tarif | S1 | Wählen Sie den erforderlichen Durchsatz aus. Wählen Sie für die niedrigsten Kosten und die Anfangskapazität „S1“ aus.
   Capacity | 1 | Die Kapazität ist der Multiplikator, der auf die Eingangsrate, Speicherkapazität und Kosten der ausgewählten SKU angewendet wird.  Die Kapazität einer Umgebung kann nach der Erstellung geändert werden. Wählen Sie für die niedrigsten Kosten die Kapazität „1“ aus.
  
1. Wählen Sie **Erstellen** aus, um den Bereitstellungsprozess zu starten. Dies kann einige Minuten dauern.

1. Wählen Sie das Symbol **Benachrichtigungen** (Glockensymbol) aus, um den Bereitstellungsprozess zu überwachen.

   [![Überwachen der Benachrichtigungen](media/time-series-insights-get-started/tsi-deploy-notifications.png)](media/time-series-insights-get-started/tsi-deploy-notifications.png#lightbox)

1. Überprüfen Sie in der **Übersicht** der Ressource die Einstellungen der Bereitstellungskonfiguration.

   [![Anheften von Azure Time Series Insights an das Dashboard](media/time-series-insights-get-started/tsi-verify-deployment.png)](media/time-series-insights-get-started/tsi-verify-deployment.png#lightbox)

1. **(Optional:)** Wählen Sie in der rechten oberen Ecke das **Stecknadelsymbol** aus, um künftig einfacher auf Ihre Azure Time Series Insights-Umgebung zugreifen zu können.

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Datenzugriffsrichtlinien](./concepts-access-policies.md) zum Schützen Ihrer Umgebung

* [Hinzufügen einer Event Hub-Ereignisquelle ](./how-to-ingest-data-event-hub.md) zu Ihrer Azure Time Series Insights-Umgebung

* [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle

* Anzeigen Ihrer Umgebung im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com).