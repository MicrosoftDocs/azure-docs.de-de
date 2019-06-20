---
title: Anwendung von Reservierungsrabatten auf Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Erfahren Sie, wie Reservierungsrabatte auf Azure SQL Data Warehouse angewandt werden und Sie Geld sparen können.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918387"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Anwendung von Reservierungsrabatten auf Azure SQL Data Warehouse

Nach dem Kauf reservierter Kapazität für Azure SQL Data Warehouse wird der Reservierungsrabatt automatisch auf die in der entsprechenden Region vorhandenen Data Warehouses angewandt. Der Reservierungsrabatt gilt für die über die cDWU-Verbrauchseinheit von SQL Data Warehouse ausgegebene Nutzung. Die Speicher- und Netzwerknutzung wird entsprechend der nutzungsbasierten Bezahlung abgerechnet.

## <a name="reservation-discount-application"></a>Anwendung des Reservierungsrabatts

Der Rabatt für reservierte Kapazitäten für SQL Data Warehouse wird auf ausgeführte Warehouses auf Stundenbasis angewandt. Wenn Sie in einer Stunde kein Warehouse bereitgestellt haben, verfällt die reservierte Kapazität für diese Stunde. Sie wird nicht übertragen.

Nach dem Kauf wird die erworbene Reservierung auf die durch ausgeführte Warehouses zu einem bestimmten Zeitpunkt ausgegebene SQL Data Warehouse-Nutzung abgestimmt. Wenn Sie einige Warehouses beenden, werden Reservierungsrabatte automatisch auf andere entsprechende Warehouses angewandt.

Für Warehouses, die eine volle Stunde nicht ausgeführt werden, wird die Reservierung automatisch auf andere entsprechende Instanzen in dieser Stunde angewandt.

## <a name="discount-examples"></a>Beispiele für die Anwendung des Rabatts

Die folgenden Beispiele zeigen, wie der Rabatt für die reservierte Kapazität für SQL Data Warehouse abhängig von den jeweiligen Bereitstellungen angewandt wird.

- **Beispiel 1:** Sie erwerben als reservierte Kapazität 5 Einheiten mit jeweils 100 cDWU. Sie führen eine DW1500c SQL Data Warehouse-Instanz eine Stunde lang aus. In diesem Fall wird die Nutzung für 15 Einheiten mit 100-cDWU-Nutzung ausgegeben. Der Reservierungsrabatt gilt für die 5 Einheiten, die Sie verwendet haben. Die restlichen 10 Einheiten mit 100-cDWU-Nutzung, die Sie verwendet haben, werden zu Preisen der nutzungsbasierten Bezahlung abgerechnet.

- **Beispiel 2:** Sie erwerben als reservierte Kapazität 5 Einheiten mit jeweils 100 cDWU. Sie führen zwei DW100c SQL Data Warehouse-Instanzen eine Stunde lang aus. In diesem Fall werden zwei Nutzungsereignisse für 1 Einheit mit 100-cDWU-Nutzung ausgegeben. Der Rabatt der reservierten Kapazität wird auf beide Nutzungsereignisse angewandt. Die verbleibenden 3 Einheiten der reservierten Kapazität mit jeweils 100 cDWU verfallen und werden nicht auf die zukünftige Verwendung übertragen.

- **Beispiel 3:** Sie erwerben für die reservierte Kapazität 1 Einheit mit 100 cDWU. Sie führen zwei DW100c SQL Data Warehouse-Instanzen aus. Jede Instanz wird jeweils 30 Minuten ausgeführt. In diesem Fall wird der Rabatt der reservierten Kapazität auf beide Nutzungsereignisse angewandt. Keine Nutzung wird zu Preisen der nutzungsbasierten Bezahlung abgerechnet.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

- Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Anzeigen von Reservierungstransaktionen](billing-view-reservations.md)
- [Abrufen von Reservierungstransaktionen und Nutzung über die API](billing-reservation-apis.md)
- [Verwalten von Reservierungen](billing-manage-reserved-vm-instance.md)
