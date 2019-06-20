---
title: Vorauszahlen für Softwarepläne – Azure-Reservierungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Softwarepläne im Voraus bezahlen und Geld gegenüber den Kosten beim nutzungsbasierten Modell sparen können.
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: e15dcdbbcaed32d836bb751ef93ce17e90bd6905
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771242"
---
# <a name="prepay-for-azure-software-plans"></a>Vorauszahlen für Azure-Softwarepläne

Wenn Sie für Ihre Nutzung von SUSE- und Red Hat-Software in Azure im Voraus bezahlen, können Sie gegenüber dem nutzungsbasierten Modell Geld sparen. Die Rabatte gelten nur für SUSE- und Red Hat-Verbrauchseinheiten und nicht für die Nutzung virtueller Computer. Sie können für zusätzliche Einsparungen Reservierungen für virtuelle Computer separat erwerben.

Sie können SUSE- und Red Hat-Softwarepläne im Azure-Portal erwerben. Gehen Sie wie folgt vor, um einen Plan zu erwerben:

- Sie müssen über die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein Abonnement mit nutzungsbasierter Bezahlung verfügen.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das Cloud Solution Provider-Programm (CSP) können die Administratoren oder Vertriebsmitarbeiter die Softwarepläne erwerben.

## <a name="buy-a-software-plan"></a>Kaufen eines Softwareplans

1. Melden Sie sich beim Azure-Portal an, und wechseln Sie zu [Reservierungen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
2. Klicken Sie auf **Hinzufügen**, und wählen Sie dann den Softwareplan aus, den Sie erwerben möchten.
Füllen Sie die erforderlichen Felder aus. Alle SUSE Linux- oder Red Hat-VMs, die den Attributen des erworbenen Plans entsprechen, erhalten den Rabatt. Die tatsächliche Anzahl der Bereitstellungen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und von der ausgewählten Menge ab.
3. Wählen Sie ein Abonnement aus. Es wird zum Bezahlen des Plans verwendet.
Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Nutzungsbasierte Zahlung“ (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein.
    - Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet.
    - Bei einem Abonnement mit nutzungsbasierter Zahlung wird die Kreditkarte des Abonnements mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt.
4. Wählen Sie einen Bereich aus. Der Bereich kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen.
    - Einzelabonnement: Der Rabatt für den Plan wird auf die übereinstimmende Nutzung im Abonnement angewandt.
    - Freigegeben: Der Rabatt für den Plan wird auf übereinstimmende Instanzen in jedem Abonnement in Ihrem Abrechnungskontext angewandt. Für Enterprise-Kunden entspricht der Abrechnungskontext der Registrierung und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung umfasst der Abrechnungskontext alle Abonnements mit nutzungsbasierter Zahlung, die vom Kontoadministrator erstellt wurden.
5. Wählen Sie ein Produkt aus, um die VM-Größe und den Imagetyp auszuwählen. Der Rabatt gilt nur für die ausgewählte VM-Größe.
6. Wählen Sie als Laufzeit ein Jahr oder drei Jahre aus.
7. Wählen Sie eine Menge aus, die Anzahl der im Voraus bezahlten VM-Instanzen, auf die der Abrechnungsrabatt angewandt werden kann.
8. Fügen Sie das Produkt dem Einkaufswagen hinzu, überprüfen Sie diesen, und schließen Sie den Kauf ab.

Der Reservierungsrabatt wird automatisch auf die Softwareverbrauchseinheit angewandt, für die Sie im Voraus bezahlt haben. VM-Computegebühren sind durch den Plan nicht abgedeckt. Sie können die VM-Reservierungen separat erwerben.

## <a name="discount-applies-to-different-suse-vm-sizes"></a>Der Rabatt gilt für verschiedene SUSE-VM-Größen

Wie reservierte VM-Instanzen bieten SUSE Linux-Pläne Flexibilität bei der Instanzgröße. Ihr Rabatt gilt auch dann, wenn Sie einen virtuellen Computer bereitstellen, der eine andere Größe aufweist als in dem erworbenen SUSE-Plan. Weitere Informationen finden Sie unter [Grundlegendes zur Anwendung des Rabatts für den Softwareplan](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="redhat-plan-discount"></a>Rabatt auf Red Hat-Plan

Pläne sind nur für Red Hat Enterprise Linux-VMs verfügbar. Der Rabatt gilt nicht für Red Hat Enterprise Linux SAP HANA-VMs oder Red Hat Enterprise Linux SAP Business Apps-VMs.

Rabatte für Red Hat-Pläne gelten nur für die VM-Größe, die Sie zum Zeitpunkt des Kaufs auswählen. RHEL-Pläne können nach dem Kauf nicht mehr rückerstattet oder umgetauscht werden.


## <a name="cancellation-and-exchanges-not-allowed"></a>Stornierungen und Wechsel sind unzulässig

Sie können einen erworbenen SUSE- oder Red Hat-Plan nicht mehr stornieren oder umtauschen. Überprüfen Sie Ihre Nutzung, um sicherzustellen, dass Sie den richtigen Plan erwerben. Hilfe beim Identifizieren der zu kaufenden Pläne finden Sie unter [Grundlegendes zur Anwendung des Rabatts für den Softwareplan](../../billing/billing-understand-suse-reservation-charges.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von reservierten Instanzen in Azure](../../billing/billing-manage-reserved-vm-instance.md).

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](../../billing/billing-save-compute-costs-reservations.md)
- [Verwalten von Reservierungen in Azure](../../billing/billing-manage-reserved-vm-instance.md)
- [Grundlegendes zur Anwendung des Rabatts für SUSE-Reservierungen](../../billing/billing-understand-suse-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../../billing/billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../../billing/billing-understand-reserved-instance-usage-ea.md)
