---
title: Reservierte Azure EA-VM-Instanzen
description: In diesem Artikel erfahren Sie, wie Azure-Reservierungen für reservierte VM-Instanzen zur Senkung der Kosten für Ihre Unternehmensregistrierung beitragen können.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 7c8a35e7c8ecd0952f8e02b66c5071253d9b0445
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348924"
---
# <a name="azure-ea-vm-reserved-instances"></a>Reservierte Azure EA-VM-Instanzen

In diesem Artikel erfahren Sie, wie Azure-Reservierungen für reservierte VM-Instanzen zur Senkung der Kosten für Ihre Unternehmensregistrierung beitragen können. Weitere Informationen zu Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Reservierungen: Umtausch und Rückerstattungen

Sie können eine Reservierung gegen eine andere Reservierung des gleichen Typs umtauschen. Es ist auch möglich, eine Rückerstattung für eine Reservierung zu erhalten, wenn Sie sie nicht mehr benötigen (bis zu 50.000 US-Dollar pro Jahr). Umtausch und Rückerstattung für eine Reservierung können über das Azure-Portal abgewickelt werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Teilrückerstattungen

Wir führen eine Teilrückerstattung durch, wenn von EA-Kunden Reservierungen zurückgegeben werden, die aufgrund einer Überschreitung erworben wurden, und nicht basierend auf dem Mindestverbrauch.

Die Rückerstattung wird im EA Portal als negative Anpassung im vorherigen Monat und positive Anpassung im aktuellen Monat angezeigt. Diese Anzeige ähnelt einem Austausch von Reservierungen. In der Gutschrift ist die Nummer der ursprünglichen Rechnung angegeben. Verwenden Sie beim Abstimmen des ursprünglichen Kaufs mit der Gutschrift daher diese ursprüngliche Rechnungsnummer.

## <a name="reservation-costs-and-usage"></a>Reservierungskosten und Nutzung

Enterprise Agreement-Kunden können Kosten und Nutzungsdaten im Azure-Portal und über REST-APIs anzeigen. Im Zusammenhang mit Reservierungskosten und Nutzung haben Sie folgende Möglichkeiten:

- Abrufen von Daten zum Erwerb von Reservierungen
- Ermitteln des Abonnements bzw. der Ressourcengruppe oder Ressource, von dem bzw. von der eine Reservierung verwendet wurde
- Verbrauchsbasierte Kostenzuteilung für Reservierungsnutzung
- Berechnen von Reservierungseinsparungen
- Abrufen von Daten zur Unterauslastung von Reservierungen
- Amortisieren von Reservierungskosten

Weitere Informationen zu Reservierungskosten und zur Nutzung finden Sie unter [Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).

Preisinformationen finden Sie unter [Virtuelle Linux-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) bzw. unter [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>API-Unterstützung für reservierte Instanzen

Verwenden Sie Azure-APIs, um programmgesteuert Informationen für Ihre Organisation über Azure-Dienst- oder -Softwarereservierungen abzurufen. Verwenden Sie die APIs beispielsweise für Folgendes:

- Suchen nach zu kaufenden Reservierungen
- Kaufen einer Reservierung
- Anzeigen von erworbenen Reservierungen
- Anzeigen und Verwalten des Reservierungszugriffs
- Aufteilen oder Zusammenführen von Reservierungen
- Ändern des Reservierungsumfangs

Weitere Informationen finden Sie unter [APIs für die Automatisierung von Azure-Reservierungen](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Reservierte Azure-VM-Instanzen

Verglichen mit der nutzungsbasierten Bezahlung lassen sich die Kosten für Ihre virtuellen Computer durch reservierte Instanzen um bis zu 72 Prozent senken. In Kombination mit dem Azure-Hybridvorteil lassen sie sich sogar um bis zu 82 Prozent senken. Mit reservierten Instanzen und einer Vorauszahlung für ein Jahr oder drei Jahre können Sie Ihre Workloads, Budgetplanung und Prognosen besser verwalten. Außerdem können Sie Reservierungen umtauschen oder stornieren, um auf veränderte Geschäftsanforderungen zu reagieren.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>So erwerben Sie reservierte VM-Instanzen

Um eine reservierte Azure-VM-Instanz erwerben zu können, muss ein Enterprise Azure-Registrierungsadministrator die Kaufoption _Reserve Instance_ (Instanz reservieren) aktivieren. Die Option befindet sich im [Azure EA-Portal](https://ea.azure.com/) auf der Registerkarte _Registrierung_ im Abschnitt _Registrierungsdetails_.

Nachdem die EA-Registrierung für das Hinzufügen reservierter Instanzen konfiguriert wurde, kann jeder Kontobesitzer mit einem aktiven, der EA-Registrierung zugeordneten Abonnement eine reservierte VM-Instanz über das [Azure-Portal](https://aka.ms/reservations) erwerben. Weitere Informationen finden Sie unter [Vorauszahlen für virtuelle Computer und Sparen von Kosten mit reservierten VM-Instanzen](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Anzeigen von Details zum Kauf reservierter Instanzen

Details zum Kauf Ihrer reservierten Instanzen können links im [Azure-Portal](https://aka.ms/reservations) über das Menü _Reservierungen_ oder im [Azure EA-Portal](https://ea.azure.com/) angezeigt werden. Wählen Sie im linken Menü die Option **Berichte** aus, und scrollen Sie auf der Registerkarte _Verwendungszusammenfassung_ nach unten zum Abschnitt _Charges by Services_ (Gebühren nach Diensten). Scrollen Sie in dem Abschnitt ganz nach unten. Ihre erworbenen reservierten Instanzen und die Nutzung werden am Ende aufgeführt, und der Dienstname ist wie im folgenden Beispiel entweder mit `1 year` oder mit `3 years` gekennzeichnet: `Standard_DS1_v2 eastus 1 year` oder `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Wie kann ich das Abonnement ändern, das der reservierten Instanz zugeordnet ist, oder meine Vorteile durch reservierte Instanzen auf ein Abonnement unter dem gleichen Konto übertragen?

Das Abonnement, für das die Vorteile durch reservierte Instanzen genutzt werden, kann wie folgt geändert werden:

- Melden Sie sich beim [Azure-Portal](https://aka.ms/reservations) an.
- Aktualisieren Sie den angewendeten Abonnementbereich, indem Sie ein anderes Abonnement unter demselben Konto zuordnen.

Weitere Informationen zum Ändern des Bereichs einer Reservierung finden Sie unter [Ändern des Reservierungsumfangs](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Anzeigen von Nutzungsdetails für reservierte Instanzen

Sie können Ihre Nutzungsdetails für reservierte Instanzen im [Azure-Portal](https://aka.ms/reservations) oder im [Azure EA-Portal](https://ea.azure.com/) (für EA-Kunden, die Abrechnungsinformationen anzeigen können) unter _Berichte_ > _Verwendungszusammenfassung_ > _Charges by Services_ (Gebühren nach Diensten) anzeigen. Bei reservierten Instanzen enthalten die Dienstnamen den Begriff „Reserviert“, wie im folgenden Beispiel zu sehen: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Ihre Nutzungsdetails und die herunterladbare CSV-Datei mit dem erweiterten Bericht enthalten zusätzliche Informationen zur Nutzung reservierter Instanzen. Das Feld _Zusätzliche Informationen_ gibt Aufschluss über die Nutzung reservierter Instanzen.

Wenn Sie reservierte Azure-VM-Instanzen ohne den Azure-Hybridvorteil erworben haben, werden für reservierte Instanzen zwei Verbrauchseinheiten (Hardware und Software) ausgegeben. Wenn Sie reservierte Instanzen mit dem Azure-Hybridvorteil erworben haben, ist die Softwareverbrauchseinheit in Ihren Nutzungsdetails für reservierte Instanzen nicht enthalten.

### <a name="reserved-instance-billing"></a>Abrechnung reservierter Instanzen

Bei Unternehmenskunden wird die Azure-Vorauszahlung verwendet, um reservierte Azure-VM-Instanzen zu erwerben. Wenn der Azure-Vorauszahlungssaldo Ihrer Registrierung für den Erwerb der reservierten Instanzen ausreicht, wird der Betrag von Ihrem Azure-Vorauszahlungssaldo abgezogen, und Sie erhalten keine Rechnung für den Erwerb.

Reservierte Instanzen können auch erworben werden, wenn Azure EA-Kunden ihre Azure-Vorauszahlung bereits vollständig ausgeschöpft haben. Diese Käufe werden dann in der nächsten Überschreitungsrechnung abgerechnet. Überschreitungen bei reservierten Instanzen werden, sofern vorhanden, in Ihre reguläre Überschreitungsrechnung aufgenommen.

### <a name="reserved-instance-expiration"></a>Ablauf reservierter Instanzen

30 Tage vor Ablauf der Reservierung sowie zum Zeitpunkt des Ablaufs erhalten Sie jeweils eine E-Mail-Benachrichtigung. Nach Ablauf der Reservierung werden bereitgestellte virtuelle Computer weiterhin ausgeführt und mit dem Satz für nutzungsbasierte Bezahlung in Rechnung gestellt. Weitere Informationen finden Sie unter [Reservierte Azure-VM-Instanzen (RIs)](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](../reservations/save-compute-costs-reservations.md).
- Weitere Informationen zu Reservierungskosten und zur Nutzung für Unternehmen finden Sie unter [Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement](../reservations/understand-reserved-instance-usage-ea.md).
- Preisinformationen finden Sie unter [Virtuelle Linux-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) bzw. unter [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).