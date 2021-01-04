---
title: 'Rabatte für Red Hat-Reservierungspläne: Azure'
description: Erfahren Sie, wie Rabatte für Red Hat-Pläne auf Red Hat-Software auf virtuellen Computern angewendet werden.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 8e735d623cc991d2e3720e0ec418eee77aa8c46c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350856"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Grundlegendes zur Anwendung des Rabatts für Reservierungspläne von Red Hat Linux Enterprise-Software für Azure

Nachdem Sie einen Red Hat Linux-Plan erworben haben, wird der Rabatt automatisch auf die bereitgestellten virtuellen Red Hat-Computer (Virtual Machines, VMs) angewendet, die der Reservierung entsprechen. Ein Red Hat Linux-Plan deckt die Kosten für die Ausführung der Red Hat-Software auf einem virtuellen Azure-Computer.

Damit Sie den richtigen Red Hat Linux-Plan erwerben, müssen Sie wissen, welche Red Hat-VMs Sie ausführen und wie viele vCPUs auf diesen VMs vorhanden sind. Verwenden Sie die folgenden Abschnitte, um den zu erwerbenden Plan anhand Ihrer CSV-Datei mit den Nutzungsdaten zu identifizieren.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabatt gilt für verschiedene VM-Größen

Wie reservierte VM-Instanzen bieten Red Hat-Pläne Flexibilität bei der Instanzgröße. Dies bedeutet, dass der Rabatt auch angewendet wird, wenn Sie einen virtuellen Computer mit einer anderen Anzahl von vCPUs bereitstellen. Der Rabatt gilt für verschiedene VM-Größen im Softwareplan.

Der Rabattbetrag hängt von dem in den folgenden Tabellen aufgeführten Verhältnis ab. Das Verhältnis vergleicht den relativen Speicherbedarf für jede VM-Größe in dieser Gruppe. Das Verhältnis richtet sich nach den vCPUs des virtuellen Computers. Anhand des Verhältniswerts können Sie berechnen, wie viele VM-Instanzen den Rabatt des Red Hat Linux-Plans erhalten.

Wenn Sie einen Plan für Red Hat Linux Enterprise Server für eine VM mit einer bis vier vCPUs erwerben, ist der Verhältniswert für diese Reservierung z. B. „1“. Der Rabatt deckt die Red Hat-Softwarekosten für:

- eine bereitgestellte VM mit einer bis vier vCPUs
- oder 0,46 oder ca. 46 % der Red Hat Enterprise Linux-Kosten für eine VM mit mindestens fünf vCPUs.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namen der Marketplace-Angebote im Azure-Portal:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Informieren Sie sich über Red Hat Enterprise Linux-Verbrauchseinheiten, für die dieser Plan gilt.](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Vorauszahlen für Red Hat-Softwarepläne mit Azure-Reservierungen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).