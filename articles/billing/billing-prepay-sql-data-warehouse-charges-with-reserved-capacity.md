---
title: Vorauszahlen für SQL Data Warehouse-Gebühren mit reservierter Azure-Kapazität | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie für SQL Data Warehouse-Gebühren mit reservierter Kapazität vorauszahlen und so Geld sparen können.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371188"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Vorauszahlen für SQL Data Warehouse-Gebühren mit reservierter Kapazität

Mit Azure SQL Data Warehouse können Sie Geld sparen, indem Sie für Ihre cDWU-Nutzung für einen Zeitraum von einem oder drei Jahren vorauszahlen. Um reservierte Kapazität für SQL Data Warehouse zu erwerben, müssen Sie die Azure-Region und die Laufzeit auswählen. Fügen Sie dann die SQL Data Warehouse-SKU Ihrem Warenkorb hinzu, und wählen Sie die Menge der cDWU-Einheiten aus, die Sie erwerben möchten.

Wenn Sie eine Reservierung erwerben, wird die SQL Data Warehouse-Nutzung, die den Reservierungsattributen entspricht, nicht mehr zu den Preisen für die nutzungsbasierte Bezahlung abgerechnet.

Eine Reservierung deckt keine mit der SQL Data Warehouse-Nutzung verbundenen Speicher- oder Netzwerkgebühren ab.

Nach Ablauf der reservierten Kapazität werden SQL Data Warehouse-Instanzen weiterhin ausgeführt, jedoch zu den Preisen für die nutzungsbasierte Bezahlung abgerechnet. Reservierungen werden nicht automatisch verlängert.

Informationen zu den Preisen finden Sie unter [SQL Data Warehouse – Preise](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Sie können die reservierte Kapazität für Azure SQL Data Warehouse über das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) erwerben. So erwerben Sie reservierte Kapazität:

- Sie müssen über die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein Abonnement mit nutzungsbasierter Bezahlung verfügen.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie EA-Administrator sein.
- Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Kapazität für SQL Data Warehouse erwerben.

Weitere Informationen zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md) und [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Auswählen der passenden Größe vor dem Kauf

Die Reservierungsgröße für SQL Data Warehouse sollte auf der Grundlage der gesamten Compute Data Warehouse Units (cDWU), die Sie nutzen, berechnet werden. Der Kauf erfolgt in Schritten von je 100 cDWU.

Angenommen, für Ihre Gesamtnutzung von SQL Data Warehouse benötigen Sie DW3000c. Dafür möchten Sie reservierte Kapazität erwerben. Sie sollten also eine reservierte Kapazität von 30 cDWU-Einheiten erwerben.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Erwerben reservierter Kapazität für SQL Data Warehouse

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie ein Abonnement aus. Wählen Sie in der Abonnementliste das Abonnement aus, das für den Kauf der reservierten Kapazität verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die reservierte Kapazität belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Nutzungsbasierte Zahlung“ (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein.
  - Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet.
  - Bei einem Abonnement mit nutzungsbasierter Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für die Zahlung auf Rechnung in Rechnung gestellt.
4. Wählen Sie einen Bereich aus. Wählen Sie in der Bereichsliste einen Abonnementbereich aus.
  - Mit der Option **Einzeln** gilt der Reservierungsrabatt für die im ausgewählten Abonnement bereitgestellte SQL Data Warehouse-Instanz.
  - Mit der Option **Gemeinsam** gilt der Reservierungsrabatt für Instanzen in allen Abonnements, die im Abrechnungskontext ausgeführt werden.
    - Für Enterprise-Kunden ist der Abrechnungskontext die EA-Registrierung.
    - Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.
5. Wählen Sie eine Azure-Region aus, die durch die reservierte Kapazität abgedeckt ist.
6. Wählen Sie die Menge aus. Geben Sie als Menge die 100 Data Warehouse-Einheiten (cDWU) ein, die Sie erwerben möchten.    
  Bei einer Menge von 30 erhalten Sie beispielsweise 3.000 cDWU-Einheiten reservierter Kapazität pro Stunde.
7. Überprüfen Sie die Kosten für die reservierte Kapazität für SQL Data Warehouse im Abschnitt **Kosten**.
8. Wählen Sie die Option **Kaufen**.
9. Wählen Sie **Diese Reservierung anzeigen** aus, um den Status des Kaufs anzuzeigen.

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Wenn Sie die reservierte Kapazität für SQL Data Warehouse stornieren möchten, wird unter Umständen eine Gebühr für die vorzeitige Kündigung in Höhe von 12 % berechnet. Rückerstattungen basieren auf dem niedrigsten Preis (entweder Ihrem Kaufpreis oder dem aktuellen Preis für die Reservierung). Rückerstattungen sind auf 50.000 US-Dollar pro Jahr beschränkt. Die Rückerstattung, die Sie erhalten, umfasst den verbleibenden anteiligen Saldo abzüglich der Gebühr für die vorzeitige Kündigung in Höhe von 12 %. Navigieren Sie zum Anfordern einer Stornierung im Azure-Portal zu der Reservierung, und wählen Sie **Erstattung** aus, um eine Supportanfrage zu erstellen.

Wenn Sie die reservierte Kapazität für SQL Data Warehouse in eine andere Region oder eine andere Laufzeit ändern möchten, können Sie sie gegen eine andere Reservierung austauschen, die den gleichen oder einen höheren Wert hat. Das Startdatum der Laufzeit für die neue Reservierung wird nicht von der umgetauschten Reservierung übernommen. Die Laufzeit von einem oder drei Jahren beginnt ab der Erstellung der neuen Reservierung. Öffnen Sie zum Anfordern eines Umtauschs im Azure-Portal die Reservierung, und wählen Sie **Umtausch** aus, um eine Supportanfrage zu erstellen.

Weitere Informationen zum Umtausch oder zur Rückerstattung von Reservierungen finden Sie unter [Reservierungsumtausch und -rückerstattung](billing-azure-reservations-self-service-exchange-and-refund.md).

Der Reservierungsrabatt wird automatisch auf die Anzahl der SQL Data Warehouse-Instanzen angewandt, die dem Umfang und den Attributen der reservierten Kapazität für SQL Data Warehouse entsprechen. Sie können den Umfang der reservierten Kapazität für SQL Data Warehouse über das [Azure-Portal](https://portal.azure.com/), PowerShell, die Befehlszeilenschnittstelle oder die API aktualisieren.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Reservierungsrabatten für Azure SQL Data Warehouse finden Sie unter [Anwendung von Reservierungsrabatten auf Azure SQL Data Warehouse](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
  - [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
  - [Grundlegendes zum Rabatt für Azure-Reservierungen](billing-understand-reservation-charges.md)
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
