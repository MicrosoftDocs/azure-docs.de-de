---
title: Pläne und Abrechnung – Microsoft Azure Scheduler
description: Erfahren Sie mehr über die Pläne und Abrechnung für Microsoft Azure Scheduler.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 3a8664497d3d082ec1c7f584188854991e872d50
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720435"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Pläne und Abrechnung für Microsoft Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt Azure Scheduler, der eingestellt wird. Zum Planen von Aufträgen sollten Sie stattdessen [Azure Logic Apps ausprobieren](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>Pläne für Auftragssammlungen

In Azure Scheduler enthält eine Auftragssammlung eine bestimmte Anzahl von Aufträgen. Die Auftragssammlung ist die fakturierbare Entität und ist in Standard-, P10 Premium- sowie P20 Premium-Plänen verfügbar, die hier beschrieben werden: 

| Auftragssammlungsplan | Max. Aufträge pro Sammlung | Max. Wiederholungen | Max. Auftragssammlungen pro Abonnement | Einschränkungen | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 Aufträge pro Sammlung | Eine pro Minute. Aufträge können nicht häufiger als einmal pro Minute ausgeführt werden. | Jedes Azure-Abonnement kann bis zu 100 Standard-Auftragssammlungen enthalten. | Zugriff auf sämtliche Scheduler-Features | 
| **P10 Premium** | 50 Aufträge pro Sammlung | Eine pro Minute. Aufträge können nicht häufiger als einmal pro Minute ausgeführt werden. | Jedes Azure-Abonnement kann bis zu 10.000 P10 Premium-Auftragssammlungen enthalten. <a href="mailto:wapteams@microsoft.com">Wenden Sie sich an uns</a>, wenn Sie mehr Sammlungen benötigen. | Zugriff auf sämtliche Scheduler-Features |
| **P20 Premium** | 1\.000 Aufträge pro Sammlung | Eine pro Minute. Aufträge können nicht häufiger als einmal pro Minute ausgeführt werden. | Jedes Azure-Abonnement kann bis zu 5.000 P20 Premium-Auftragssammlungen enthalten. <a href="mailto:wapteams@microsoft.com">Wenden Sie sich an uns</a>, wenn Sie mehr Sammlungen benötigen. | Zugriff auf sämtliche Scheduler-Features |
|||||| 

## <a name="pricing"></a>Preise

Ausführliche Preisinformationen finden Sie unter [Scheduler – Preise](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Ausführen eines Upgrades oder Downgrades für Pläne

Sie können für Auftragssammlungspläne jederzeit ein Upgrade oder Downgrade zwischen den Standard-, P10 Premium- und P20 Premium-Plänen durchführen.

## <a name="active-status-and-billing"></a>Aktiver Status und Abrechnung

Auftragssammlungen sind immer aktiv, es sei denn, Ihr gesamtes Azure-Abonnement wird aufgrund von Problemen bei der Abrechnung vorübergehend deaktiviert. Sie können zwar mit einem einzigen Vorgang alle Aufträge innerhalb einer Auftragssammlung deaktivieren, der Abrechnungsstatus der Auftragssammlung wird dadurch jedoch nicht geändert. Die Auftragssammlung wird also *weiterhin* in Rechnung gestellt. Leere Auftragssammlungen gelten als aktiv und werden berechnet.

Um sicherzustellen, dass eine Auftragssammlung nicht in Rechnung gestellt wird, müssen Sie die Auftragssammlung löschen.

## <a name="standard-billable-units"></a>Standard-Abrechnungseinheiten

Eine Standard-Abrechnungseinheit kann bis zu zehn Standard-Auftragssammlungen umfassen. Da eine Standard-Auftragssammlung bis zu 50 Aufträge pro Sammlung enthalten kann, sind mit einer Standard-Abrechnungseinheit für Ihr Azure-Abonnement bis zu 500 Aufträge bzw. nahezu 22 *Millionen* Auftragsausführungen pro Monat möglich. In der folgenden Liste wird erläutert, wie Ihnen unterschiedliche Anzahlen von Standard-Auftragssammlungen in Rechnung gestellt werden:

* Bis zu einer Anzahl von zehn Standard-Auftragssammlungen wird eine Standard-Abrechnungseinheit berechnet. 

* Bei 11 bis 20 Standard-Auftragssammlungen werden zwei Standard-Abrechnungseinheiten berechnet. 

* Bei 21 bis 30 Standard-Auftragssammlungen werden drei Standard-Abrechnungseinheiten berechnet usw.

## <a name="p10-premium-billable-units"></a>P10 Premium-Abrechnungseinheiten

Eine P10 Premium-Abrechnungseinheit kann bis zu 10.000 P10 Premium-Auftragssammlungen umfassen. Da eine P10 Premium-Auftragssammlung bis zu 50 Aufträge pro Sammlung enthalten kann, sind mit einer P10 Premium-Abrechnungseinheit für Ihr Azure-Abonnement bis zu 500.000 Aufträge bzw. nahezu 22 *Milliarden* Auftragsausführungen pro Monat möglich. 

P10 Premium-Auftragssammlungen haben den gleichen Funktionsumfang wie Standard-Auftragssammlungen, sind preislich aber interessanter für Apps, die viele Auftragssammlungen erfordern, und bieten eine höhere Skalierbarkeit. In der folgenden Liste wird erläutert, wie Ihnen unterschiedliche Anzahlen von P10 Premium-Auftragssammlungen in Rechnung gestellt werden:

* Bis zu einer Anzahl von 10.000 P10 Premium-Auftragssammlungen wird eine P10 Premium-Abrechnungseinheit berechnet. 

* Bei 10.001 bis 20.000 P10 Premium-Auftragssammlungen werden zwei P10 Premium-Abrechnungseinheiten berechnet usw.

## <a name="p20-premium-billable-units"></a>P20 Premium-Abrechnungseinheiten

Eine P20 Premium-Abrechnungseinheit kann bis zu 5.000 P20 Premium-Auftragssammlungen umfassen. Da eine P20 Premium-Auftragssammlung bis zu 1.000 Aufträge pro Sammlung enthalten kann, sind mit einer P20 Premium-Abrechnungseinheit für Ihr Azure-Abonnement bis zu 5.000.000 Aufträge bzw<<<<<<<. nahezu 220 *Milliarden* Auftragsausführungen pro Monat möglich.

P20 Premium-Auftragssammlungen haben den gleichen Funktionsumfang wie P10 Premium-Auftragssammlungen, unterstützen aber eine größere Anzahl von Aufträgen pro Sammlung und eine größere Gesamtanzahl von Aufträgen als P10 Premium, sodass sie eine höhere Skalierbarkeit bieten.

## <a name="plan-comparison"></a>Vergleich der Pläne

* Bei mehr als 100 Standard-Auftragssammlungen (entspricht zehn Standard-Abrechnungseinheiten) lohnt sich die Verlagerung aller Auftragssammlungen in einen Premium-Plan.

* Wenn Sie eine Standard-Auftragssammlung und eine Premium-Auftragssammlung haben, werden Ihnen eine Standard-Abrechnungseinheit *und* eine Premium-Abrechnungseinheit in Rechnung gestellt.

  Die Abrechnung des Scheduler-Diensts basiert auf der Anzahl aktiver Standard- oder Premium-Auftragssammlungen.

## <a name="see-also"></a>Weitere Informationen

* [Was ist der Azure Scheduler?](scheduler-intro.md)
* [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)
* [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)