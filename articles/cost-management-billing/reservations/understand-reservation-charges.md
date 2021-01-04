---
title: Grundlegendes zu Rabatten für Reservierungen für Azure SQL-Datenbank | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Rabatt für Reservierungen auf ausgeführte Azure SQL-Datenbank-Instanzen angewendet wird. Der Rabatt wird auf Stundenbasis auf diese Datenbanken angewendet.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: banders
ms.openlocfilehash: 99f2c9c16f2ca2147a6e6da333ff64116e42aecc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338083"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Anwendung eines Rabatts für Reservierungen auf Azure SQL-Datenbank

Nachdem Sie eine reservierte Azure SQL-Datenbank-Kapazität erworben haben, wird der Reservierungsrabatt automatisch auf SQL-Datenbank-Instanzen angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung wird auf die Computekosten von SQL-Datenbank angewendet, einschließlich des primären Replikats und aller abrechenbarer sekundären Replikate. Ihnen werden die regulären Gebühren für Software, Speicher und Netzwerke berechnet. Die Lizenzkosten für SQL-Datenbank-Instanzen können Sie mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) abdecken.

Beachten Sie, dass Reservierungsrabatte nicht für „Azure SQL-Datenbank – serverlos“ gelten.

Weitere Informationen zu reservierten VM-Instanzen finden Sie unter [Grundlegendes zum Rabatt für Azure-Reservierungen auf virtuelle Computer](../manage/understand-vm-reservation-charges.md).

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

## <a name="discount-applied-to-running-sql-databases"></a>Anwendung des Rabatts auf ausgeführte von SQL-Datenbank-Instanzen

Der Rabatt für reservierte SQL-Datenbank-Kapazitäten wird auf ausgeführte SQL-Datenbank-Instanzen, die auf Stundenbasis abgerechnet werden, angewendet. Die Reservierung, die Sie kaufen, wird der Computenutzung zugeordnet, die von den ausgeführten SQL-Datenbank-Instanzen ausgegeben wird. Bei SQL-Datenbank-Instanzen, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird die Reservierung automatisch auf andere SQL-Datenbank-Instanzen angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auf SQL-Datenbank-Instanzen angewendet werden, die gleichzeitig ausgeführt werden. Wenn die den Reservierungsattributen entsprechenden SQL-Datenbank-Instanzen nicht über die gesamte Stunde hinweg ausgeführt werden, profitieren Sie nicht im vollen Umfang vom Reservierungsrabatt für diese Stunde.

Die folgenden Beispiele zeigen, wie der Rabatt für reservierte SQL-Datenbank-Kapazitäten in Abhängigkeit von der Anzahl der Kerne, die Sie erworben haben, und der Dauer ihrer Ausführung angewendet wird.

- Szenario 1: Sie erwerben eine reservierte SQL-Datenbank-Kapazität für eine SQL-Datenbank-Instanz mit 8 Kernen. Sie führen eine SQL-Datenbank-Instanz mit 16 Kernen aus, die den restlichen Attributen der Reservierung entspricht. Ihnen wird der Preis für die nutzungsbasierte Bezahlung für 8 Kerne der SQL-Datenbank-Computenutzung in Rechnung gestellt. Sie erhalten den Reservierungsrabatt für eine Stunde der SQL-Datenbank-Computenutzung (8 Kerne).

Bei den übrigen Beispielen wird davon ausgegangen, dass die reservierte SQL-Datenbank-Kapazität, die Sie erwerben, für eine SQL-Datenbank-Instanz mit 16 Kernen gilt und die restlichen Reservierungsattribute den ausgeführten SQL-Datenbank-Instanzen entsprechen.

- Szenario 2: Sie führen zwei SQL-Datenbank-Instanzen mit 8 Kernen jeweils eine Stunde lang aus. Der Reservierungsrabatt für 16 Kerne wird auf die Computenutzung für beide SQL-Datenbank-Instanzen mit 8 Kernen angewendet.
- Szenario 3: Sie führen von 13 bis 13:30 Uhr eine SQL-Datenbank-Instanz mit 16 Kernen aus. Sie führen von 13:30 bis 14 Uhr eine weitere SQL-Datenbank-Instanz mit 16 Kernen aus. Beide Instanzen sind durch den Reservierungsrabatt abgedeckt.
- Szenario 4: Sie führen von 13 bis 13:45 Uhr eine SQL-Datenbank-Instanz mit 16 Kernen aus. Sie führen von 13:30 bis 14 Uhr eine weitere SQL-Datenbank-Instanz mit 16 Kernen aus. Die zusätzlichen 15 Minuten werden Ihnen zu den Preisen der nutzungsbasierten Bezahlung in Rechnung gestellt. Für den restlichen Zeitraum wird der Reservierungsrabatt auf die Computenutzung angewendet.
- Szenario 5: Sie führen eine SQL Hyperscale-Datenbank mit vier Kernen und drei sekundären Replikaten aus, die jeweils vier Kerne besitzen. Die Reservierung wird auf die Computenutzung für das primäre Replikat und für alle sekundären Replikate angewendet.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../../azure-sql/database/reserved-capacity-overview.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
- [Grundlegendes zur Verwendung von Azure-Reservierungen für CSP-Abonnements](/partner-center/azure-reservations)