---
title: Rabatt für Softwarepläne – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Rabatte für Softwarepläne auf Software auf virtuellen Computern angewandt werden.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: banders
ms.openlocfilehash: 2c17b2f9dc12fa7bff2dae8603bcf95a3a8ddfa0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350839"
---
# <a name="azure-software-plan-discount"></a>Rabatt für Azure-Softwarepläne

Azure-Softwarepläne für SUSE und Red Hat sind Reservierungen, die für bereitgestellte virtuelle Computer gelten. Der Rabatt für Softwarepläne wird auf die Softwarenutzung von bereitgestellten virtuellen Computern angewandt, die der Reservierung entsprechen.

Wenn Sie einen virtuellen Computer beenden, wird der Rabatt automatisch auf einen anderen entsprechenden virtuellen Computer (sofern verfügbar) angewandt. Ein Softwareplan deckt die Kosten für die Ausführung der Software auf einem virtuellen Computer. Andere Gebühren, z. B. Compute-, Speicher- und Netzwerkgebühren, werden separat abgerechnet.

Für den Kauf eines geeigneten Plans müssen Sie die Nutzung der virtuellen Computer und die Anzahl der vCPUs auf diesen virtuellen Computern berücksichtigen. Anhand der Informationen in den folgenden Abschnitten können Sie den zu erwerbenden Plan basierend auf Ihren Nutzungsdaten ermitteln.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Überprüfen der Nutzung von virtuellen Red Hat-Computern vor dem Kauf

Rufen Sie den Produktnamen Ihrer Nutzungsdaten ab, und kaufen Sie den Red Hat-Plan mit der gleichen Art und Größe.

Wenn Ihre Nutzung z. B. das Produkt **Red Hat Enterprise Linux – 1 bis 4 vCPU-VM-Lizenz** betrifft, sollten Sie **Red Hat Enterprise Linux** für **VM mit 1–4 vCPUs** erwerben.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Überprüfen der Nutzung von virtuellen SUSE-Computern vor dem Kauf

Rufen Sie den Produktnamen Ihrer Nutzungsdaten ab, und kaufen Sie den SUSE-Plan mit der gleichen Art und Größe.

Wenn Ihre Nutzung z. B. das Produkt **SUSE Linux Enterprise Server Priority – 2 bis 4 vCPU-VM-Unterstützung** betrifft, sollten Sie **SUSE Linux Enterprise Server Priority** für **VM mit 2–4 vCPUs** erwerben.

![Beispiel für die Auswahl des zu erwerbenden Produkts](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

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

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Namen der Marketplace-Angebote im Azure-Portal:

- SLES 15
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

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Vorauszahlen für SUSE-Softwarepläne aus Azure-Reservierungen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)