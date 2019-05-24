---
title: 'Tutorial: Prognostizieren von Ausgaben mit Cloudyn in Azure | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie die Ausgaben prognostizieren, indem Sie die Daten zum Nutzungsverlauf und zu den Ausgaben verwenden.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: ef9bb41c36e4bfa59f30e2666a26a7e200bfd17b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967134"
---
# <a name="tutorial-forecast-future-spending"></a>Tutorial: Vorhersage zukünftiger Ausgaben

Cloudyn ermöglicht es Ihnen, zukünftige Ausgaben mit Daten zum Nutzungsverlauf und zu Ausgaben zu prognostizieren. Sie verwenden Cloudyn-Berichte, um alle Kostenprognosedaten anzuzeigen. Die Beispiele dieses Tutorials führen Sie in einer exemplarischen Vorgehensweise durch das Überprüfen der Kostenprognosen mithilfe der Berichte. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Vorhersage zukünftiger Ausgaben

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto.
- Sie müssen entweder über eine Registrierung für die Testversion oder über ein kostenpflichtiges Abonnement für Cloudyn verfügen.

## <a name="forecast-future-spending"></a>Vorhersage zukünftiger Ausgaben

Cloudyn enthält Kostenprognoseberichte, anhand derer Sie Ausgaben basierend auf Ihrer Nutzung im Laufe der Zeit vorhersagen können. Der Hauptzweck besteht darin, Sie dabei zu unterstützen, sicherzustellen, dass Ihre Kostentrends die Erwartungen Ihrer Organisation nicht überschreiten. Die Berichte, die Sie verwenden, beziehen sich auf die aktuellen monatlichen vorhergesagten und jährlichen vorhergesagten Kosten. Beide zeigen, welche Ausgaben zu erwarten sind, wenn Ihre Nutzung mit der Nutzung innerhalb der letzten 30 Tage relativ konsistent bleibt.

Der Bericht über die aktuellen monatlichen vorhergesagten Kosten zeigt die Kosten für Ihre Dienste. Er bezieht sich auf Kosten ab Anfang des Monats und des vorhergehenden Monats, um die voraussichtlichen Kosten anzuzeigen. Klicken Sie im Berichtsmenü am oberen Rand des Portals auf **Costs** > **Projection and Budget** > **Current Month Projected Cost** (Kosten > Prognose und Budget > Vorhergesagte Kosten für den aktuellen Monat). Die folgende Abbildung zeigt ein Beispiel.

![Beispielinformationen, die im Bericht „Vorhergesagte Kosten für den aktuellen Monat“ angezeigt werden](./media/tutorial-forecast-spending/project-month01.png)

Im Beispiel können Sie sehen, für welche Dienste die höchsten Kosten entstanden sind. Azure-Kosten waren niedriger als AWS Kosten. Wenn Sie die Kostenprognose für Azure-VMs im Detail sehen möchten, wählen Sie in der Liste **Filter** **Azure/VM**.

![Beispiel zu „Vorhergesagte Kosten für Azure-VM im aktuellen Monat“](./media/tutorial-forecast-spending/project-month02.png)

Führen Sie die gleichen grundlegenden Schritte aus, um die Prognose der monatlichen Kosten für andere Dienste anzuzeigen, die Sie interessieren.

Der Bericht über die vorhergesagten jährlichen Kosten zeigt die extrapolierten Kosten für Ihre Dienste für die nächsten 12 Monate an.

Klicken Sie im Berichtsmenü am oberen Rand des Portals auf **Costs** > **Projection and Budget** > **Annual Projected Cost** (Kosten > Prognose und Budget > Vorhergesagte jährliche Kosten). Die folgende Abbildung zeigt ein Beispiel.

![Beispiel für Bericht „Vorhergesagte jährliche Kosten“](./media/tutorial-forecast-spending/project-annual01.png)

Im Beispiel können Sie sehen, für welche Dienste die höchsten Kosten entstanden sind. Wie im monatlichen Beispiel waren die Azure-Kosten niedriger als die AWS Kosten. Wenn Sie die Kostenprognose für Azure-VMs im Detail sehen möchten, wählen Sie in der Liste **Filter** **Azure/VM**.

![Beispiel für „Vorhergesagte jährliche VM-Kosten“](./media/tutorial-forecast-spending/project-annual02.png)

In der Abbildung oben belaufen sich die vorhergesagten jährlichen Kosten der Azure-VMs auf 28.374 US-Dollar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Vorhersage zukünftiger Ausgaben


Wechseln Sie zum nächsten Tutorial, um zu erfahren, wie Sie Kosten mit Kostenzuteilung und Showbackberichten verwalten.

> [!div class="nextstepaction"]
> [Manage costs by using Azure Cost Management](tutorial-manage-costs.md) (Verwalten von Kosten mit Azure Cost Management)
