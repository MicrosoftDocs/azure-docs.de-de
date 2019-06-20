---
title: Anzeigen von Reservierungen für Azure-Ressourcen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure-Reservierungen im Azure-Portal anzeigen.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 872837c774368820527b12778b1a7dd4ddc5c7af
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369170"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Anzeigen von Azure-Reservierungen im Azure-Portal

Je nach Abonnementtyp und Berechtigungen gibt es mehrere Möglichkeiten, um Reservierungen für Azure anzuzeigen.

## <a name="view-purchased-reservations"></a>Anzeigen von erworbenen Reservierungen

Standardmäßig können Sie und der Kontoadministrator die Reservierung anzeigen, wenn Sie eine Reservierung kaufen. Ihnen und dem Kontoadministrator wird automatisch die Rolle „Besitzer“ für den Reservierungsauftrag und die Reservierung zugewiesen. Damit andere Personen die Reservierung anzeigen können, müssen Sie sie als **Besitzer** oder **Leser** für den Reservierungsauftrag und die Reservierung hinzufügen.

Weitere Informationen finden Sie unter [Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Gehen Sie folgendermaßen vor, um eine Reservierung als Besitzer oder Leser anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Reservierungen**.
    ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-view-reservation/portal-reservation-search.png)  
3. Diese Liste zeigt alle Reservierungen, für die Sie die Rolle „Besitzer“ oder „Leser“ ausüben. Jede Reservierung zeigt den letzten bekannten Nutzungsprozentsatz.  
    ![Beispiel für eine Liste von Reservierungen](./media/billing-view-reservation/view-reservations.png)
4. Wählen Sie eine Reservierung aus, und zeigen Sie den Nutzungstrend für die letzten fünf Tage an.  
    ![Beispiel mit Nutzungstrend für die Reservierung](./media/billing-view-reservation/reservation-utilization.png)
5. Sie können die [Reservierungsnutzung](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) auch über die API für die Nutzung der reservierten Instanz und mit dem [Microsoft Azure Consumption Insights Power BI-Inhaltspaket](/power-bi/service-connect-to-azure-consumption-insights) abrufen.

Wenn Sie den Bereich einer Reservierung ändern, eine Reservierung aufteilen oder ändern möchten, wer eine Reservierung verwalten kann, lesen Sie [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Anzeigen von Reservierungstransaktionen für Enterprise-Registrierungen

 Wenn Sie über einen partnergesteuerte Enterprise-Registrierung verfügen, zeigen Sie Reservierungen an, indem Sie im EA-Portal zu **Berichte** navigieren. Bei anderen Enterprise-Registrierungen können Sie Reservierungen im EA-Portal und im Azure-Portal anzeigen. Sie müssen ein EA-Administrator sein, um Reservierungstransaktionen anzeigen zu können.

Gehen Sie folgendermaßen vor, um Reservierungstransaktionen im Azure-Portal anzuzeigen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Wählen Sie **Reservierungstransaktionen** aus.
1. Wählen Sie zum Filtern der Ergebnisse **Zeitraum**, **Typ** oder **Beschreibung** aus.
1. Wählen Sie **Übernehmen**.

    ![Screenshot, der Ergebnisse für Reservierungstransaktionen zeigt](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Wenn Sie die Daten mithilfe einer API abrufen möchten, lesen Sie [Abrufen von Gebühren für reservierte Instanzen für Unternehmenskunden](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)

Kaufen eines Serviceplans:

- [Vorauszahlen für reservierte Azure Cosmos DB-Kapazität](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Kaufen eines Softwareplans:

- [Vorauszahlen für Red Hat-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Vorauszahlen für SUSE-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-suse-software-charges.md)

Grundlegendes zur Nutzung:

- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
- [Grundlegendes zur Verwendung von Azure-Reservierungen für CSP-Abonnements](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
