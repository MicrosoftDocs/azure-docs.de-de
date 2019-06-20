---
title: Rabatt für Softwarepläne – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Rabatte für Softwarepläne auf Software auf virtuellen Computern angewandt werden.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: bcbf5ab48f3476a911fc4ade1eb0c395fb335d43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370225"
---
# <a name="azure-software-plan-discount"></a>Rabatt für Azure-Softwarepläne

Azure-Softwarepläne für SUSE und Red Hat sind Reservierungen, die für bereitgestellte virtuelle Computer gelten. Der Rabatt für Softwarepläne wird auf die Softwarenutzung von bereitgestellten virtuellen Computern angewandt, die der Reservierung entsprechen.

Wenn Sie einen virtuellen Computer beenden, wird der Rabatt automatisch auf einen anderen entsprechenden virtuellen Computer (sofern verfügbar) angewandt. Ein Softwareplan deckt die Kosten für die Ausführung der Software auf einem virtuellen Computer. Andere Gebühren, z. B. Compute-, Speicher- und Netzwerkgebühren, werden separat abgerechnet.

Für den Kauf eines geeigneten Plans müssen Sie die Nutzung der virtuellen Computer und die Anzahl der vCPUs auf diesen virtuellen Computern berücksichtigen. Anhand der Informationen in den folgenden Abschnitten können Sie den zu erwerbenden Plan basierend auf Ihren Nutzungsdaten ermitteln.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, *verfallen* die reservierten Stunden.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Überprüfen der Nutzung von virtuellen Red Hat-Computern vor dem Kauf

Rufen Sie den Produktnamen Ihrer Nutzungsdaten ab, und kaufen Sie den Red Hat-Plan mit der gleichen Art und Größe.

Wenn Ihre Nutzung z. B. das Produkt **Red Hat Enterprise Linux – 1 bis 4 vCPU-VM-Lizenz** betrifft, sollten Sie **Red Hat Enterprise Linux** für **VM mit 1–4 vCPUs** erwerben.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Überprüfen der Nutzung von virtuellen SUSE-Computern vor dem Kauf

Rufen Sie den Produktnamen Ihrer Nutzungsdaten ab, und kaufen Sie den SUSE-Plan mit der gleichen Art und Größe.

Wenn Ihre Nutzung z. B. das Produkt **SUSE Linux Enterprise Server Priority – 2 bis 4 vCPU-VM-Unterstützung** betrifft, sollten Sie **SUSE Linux Enterprise Server Priority** für **VM mit 2–4 vCPUs** erwerben.

![Beispiel für die Auswahl des zu erwerbenden Produkts](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabatte gelten für verschiedene VM-Größen für SUSE-Pläne

Wie reservierte VM-Instanzen bieten SUSE-Pläne Flexibilität bei der Instanzgröße. Dies bedeutet, dass der Rabatt auch angewendet wird, wenn Sie einen virtuellen Computer mit einer anderen Anzahl von vCPUs bereitstellen. Der Rabatt gilt für verschiedene VM-Größen im Softwareplan.

Der Rabattbetrag hängt von dem in den folgenden Tabellen aufgeführten Verhältnis ab. Das Verhältnis vergleicht den relativen Speicherbedarf für jede Verbrauchseinheit in dieser Gruppe. Das Verhältnis richtet sich nach den vCPUs des virtuellen Computers. Anhand des Verhältniswerts können Sie berechnen, wie viele VM-Instanzen den Rabatt des SUSE Linux-Plans erhalten.

Wenn Sie einen Plan für SUSE Linux Enterprise Server for HPC Priority für einen virtuellen Computer mit 3 oder 4 vCPUs erwerben, ist der Verhältniswert für diese Reservierung z. B. „2“. Der Rabatt deckt die SUSE-Softwarekosten für:

- 2 bereitgestellte VMs mit einer vCPU oder 2 vCPUs,
- einen bereitgestellten VM mit 3 oder 4 vCPUs
- oder 0,77 oder ca. 77 % eines virtuellen Computers mit mindestens 5 vCPUs.

Der Verhältniswert für mindestens 5 vCPUs ist „2,6“. Eine Reservierung für SUSE mit einem VM mit mindestens 5 vCPUs deckt also nur einen Teil der Softwarekosten, nämlich ca. 77 %.

Die folgenden Tabellen enthalten die Softwarepläne, für die Sie eine Reservierung erwerben können, deren zugeordnete Verbrauchseinheiten und das jeweilige Verhältnis.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Name des Marketplace-Angebots im Azure-Portal:

- SLES 12 SP3 for HPC (Priority)

|SUSE-VM | MeterId| Verhältnis| Beispiel-VM-Größe|
| -------| ------------------------| --- |--- |
|SLES for HPC (1-2 vCPUs)|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC (3-4 vCPUs)|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC (mindestens 5 vCPUs)|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Name des Marketplace-Angebots im Azure-Portal:

- SLES 12 SP3 for HPC

|SUSE-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- | --- | ------------------------| --- |
|SLES for HPC (1-2 vCPUs) |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC (3-4 vCPUs)|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES for HPC (mindestens 5 vCPUs) |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Namen der Marketplace-Angebote im Azure-Portal:

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|SUSE-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority (1-2 vCPUs)|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority (3-4 vCPUs) |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority (mindestens 5 vCPUs) |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Namen der Marketplace-Angebote im Azure-Portal:

- SLES 15 (Priority)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|SUSE-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- |--- |
|SLES (1 vCPU)|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES (2-4 vCPUs) |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES (2-4 vCPUs) |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES (6 vCPUs) |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES (8 vCPUs) |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES (vCPUs mit 12 Kernen) |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES (16 vCPUs) |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES (20 vCPUs) |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES (vCPUs mit 24 Kernen) |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES (32 vCPUs) |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES (vCPUs mit 40 Kernen) |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES (64 vCPUs) |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES (vCPUs mit 72 Kernen) |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES (vCPUs mit 96 Kernen) |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES (vCPUs mit 128 Kernen) |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Namen der Marketplace-Angebote im Azure-Portal:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- |--- |
|SLES (vCPUs mit 1-2 Kernen) |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES (vCPUs mit 3-4 Kernen) |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES (mindestens 5 vCPUs) |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Vorauszahlen für SUSE-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
