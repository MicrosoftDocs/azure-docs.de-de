---
title: APIs für die Automatisierung von Azure-Reservierungen | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Azure-APIs, mit denen Sie programmgesteuert Reservierungsinformationen abrufen können.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/25/2019
ms.author: banders
ms.openlocfilehash: 6d63f9a393dbb40c3b0952eba9ab9449fd7b558d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702623"
---
# <a name="apis-for-azure-reservation-automation"></a>APIs für die Automatisierung von Azure-Reservierungen

Verwenden Sie Azure-APIs, um programmgesteuert Informationen für Ihre Organisation über Azure-Dienst- oder -Softwarereservierungen abzurufen.

## <a name="find-reservation-plans-to-buy"></a>Suchen nach zu kaufenden Reservierungsplänen

Verwenden Sie die Reservierungsempfehlungs-API, um Empfehlungen zu erhalten, welchen Reservierungsplan Sie entsprechend der Nutzung Ihrer Organisation kaufen sollten. Weitere Informationen finden Sie unter [Abrufen von Empfehlungen zu reservierter Instanz](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Sie können auch Ihre Ressourcennutzung analysieren, indem Sie die Verbrauchs-API „Nutzungsdetails“ verwenden. Weitere Informationen finden Sie unter [Nutzungsdetails – Liste für den Abrechnungszeitraum nach Abrechnungskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). Die Azure-Ressourcen, die Sie durchgängig nutzen, sind in der Regel die besten Kandidaten für eine Reservierung.

## <a name="buy-a-reservation"></a>Kaufen einer Reservierung

Sie können Azure-Reservierungen und Softwarepläne programmgesteuert über REST-APIs erwerben. Weitere Informationen finden Sie unter [Reservierungsauftrag – Kauf-API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Hier sehen Sie eine Beispielanforderung, mit der Sie einen Kauf über die REST-API tätigen können:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Anforderungstext:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Sie können eine Reservierung auch im Azure-Portal erwerben. Weitere Informationen finden Sie in den folgenden Artikeln:

Servicepläne:
- [Virtueller Computer](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL-Datenbank](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Softwarepläne:
- [SUSE Linux-Software](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Abrufen von Reservierungen

Wenn Sie ein Azure-Kunde mit einem Enterprise Agreement (EA-Kunde) sind, können Sie die Reservierungen, die Ihre Organisation erworben hat, über die API abrufen, die unter [Abrufen von Gebühren für reservierte Instanzen für Unternehmenskunden](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) beschrieben ist. Für andere Abonnements können Sie die Liste der Reservierungen, die Sie erworben und für die Sie Berechtigungen zum Anzeigen haben, über die API [Reservierungsauftrag – Liste](/rest/api/reserved-vm-instances/reservationorder/list) abrufen. Standardmäßig hat der Kontobesitzer oder die Person, die die Reservierung erworben hat, Berechtigungen zum Anzeigen der Reservierung.

## <a name="see-reservation-usage"></a>Anzeigen der Reservierungsnutzung

Wenn Sie EA-Kunde sind, können Sie programmgesteuert anzeigen, wie die Reservierungen in Ihrer Organisation verwendet werden. Weitere Informationen finden Sie unter [Abrufen der Nutzung von reservierten Instanzen für Unternehmenskunden](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Verwenden Sie für andere Abonnements die API [Reservierungszusammenfassungen – Liste nach Reservierungsauftrag und Reservierung](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Wenn Sie feststellen, dass die Reservierungen Ihrer Organisation zu wenig genutzt werden:

- Vergewissern Sie sich, dass die virtuellen Computer, die für Ihre Organisation erstellt werden, mit der VM-Größe übereinstimmen, die zur Reservierung gehört.
- Vergewissern Sie sich, dass Instanzgrößenflexibilität aktiviert ist. Weitere Informationen finden Sie unter [Verwalten von Reservierungen – Ändern der Optimierungseinstellung für reservierte VM-Instanzen](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Ändern Sie den Bereich der Reservierung in „Freigegeben“, sodass sie umfassender gilt. Weitere Informationen finden Sie unter [Verwalten von Reservierungen – Ändern des Bereichs für eine Reservierung](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Tauschen Sie die nicht verwendete Menge. Weitere Informationen finden Sie unter [Verwalten von Reservierungen – Stornierungen und Umtausch](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Gewähren von Zugriff auf Reservierungen

Rufen Sie die Liste alle Reservierungen, auf die ein Benutzer Zugriff hat, über die API [Reservierungsauftrag – Liste](/rest/api/reserved-vm-instances/reservationorder/list) ab. Informationen, wie Sie Zugriff auf eine Reservierung programmgesteuert erteilen, finden Sie in den folgenden Artikeln:

- [Verwalten des Zugriffs mithilfe der RBAC und der REST-API](../role-based-access-control/role-assignments-rest.md)
- [Verwalten des Zugriffs mit der RBAC und Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Verwalten des Zugriffs mithilfe der RBAC und der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Aufteilen oder Zusammenführen einer Reservierung

Wenn Sie mehrere Ressourceninstanzen innerhalb einer Reservierung erworben haben, können Sie Instanzen innerhalb dieser Reservierung verschiedenen Abonnements zuweisen. Sie können den Reservierungsbereich ändern, sodass er für alle Abonnements im selben Abrechnungskontext gilt. Für Kostenverwaltungs- oder für Budgetierungszwecke können Sie den Bereich (Umfang) jedoch als „Einzelabonnement“ beibehalten und Reservierungsinstanzen einem bestimmten Abonnement zuordnen.

Um eine Reservierung aufzuteilen, verwenden Sie die API [Reservierung – Aufteilen](/rest/api/reserved-vm-instances/reservation/split). Sie können eine Reservierung auch über PowerShell aufteilen. Weitere Informationen finden Sie unter [Verwalten von Reservierungen – Aufteilten einer einzelnen Reservierung in zwei Reservierungen](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Um zwei Reservierungen in eine Reservierung zusammenzuführen, verwenden Sie die API [Reservierung – Zusammenführen](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Ändern des Bereichs für eine Reservierung

Der Bereich einer Reservierung können ein einzelnes Abonnement oder alle Abonnements in Ihrem Abrechnungskontext sein. Wenn Sie den Umfang auf ein Einzelabonnement festlegen, wird die Reservierung mit den ausgeführten Ressourcen im ausgewählten Abonnement abgestimmt. Wenn Sie den Umfang auf „Freigegeben“ festlegen, ordnet Azure die Reservierung Ressourcen zu, die in allen Abonnements innerhalb des Abrechnungskontexts ausgeführt werden. Der Abrechnungskontext ist abhängig von dem Abonnement, das Sie verwendet haben, um die Reservierung zu erwerben. Weitere Informationen finden Sie unter [Verwalten von Reservierungen – Ändern des Bereichs](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Um den Bereich programmgesteuert zu ändern, verwenden Sie die API [Reservierung – Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Weitere Informationen

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Grundlegendes zur Anwendung des Rabatts für VM-Reservierungen](billing-understand-vm-reservation-charges.md)
- [Grundlegendes zur Anwendung des Rabatts für den SUSE Linux Enterprise-Softwareplan](billing-understand-suse-reservation-charges.md)
- [Grundlegendes zur Anwendung anderer Reservierungsrabatte](billing-understand-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md)
- [Verkaufen Microsoft Azure Reserved Instances](https://docs.microsoft.com/partner-center/azure-reservations)
