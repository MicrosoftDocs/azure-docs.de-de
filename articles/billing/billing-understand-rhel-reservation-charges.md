---
title: Grundlegendes zum Rabatt für und zur Verwendung von Red Hat-Reservierungspläne(n) | Microsoft-Dokumentation
description: Erfahren Sie, wie Rabatte für Red Hat-Pläne auf Red Hat-Software auf virtuellen Computern angewendet werden.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370201"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Grundlegendes zur Anwendung des Rabatts für Reservierungspläne von Red Hat Linux Enterprise-Software für Azure

Nachdem Sie einen Red Hat Linux-Plan erworben haben, wird der Rabatt automatisch auf die bereitgestellten virtuellen Red Hat-Computer (Virtual Machines, VMs) angewendet, die der Reservierung entsprechen. Ein Red Hat Linux-Plan deckt die Kosten für die Ausführung der Red Hat-Software auf einem virtuellen Azure-Computer.

Damit Sie den richtigen Red Hat Linux-Plan erwerben, müssen Sie wissen, welche Red Hat-VMs Sie ausführen und wie viele vCPUs auf diesen VMs vorhanden sind. Verwenden Sie die folgenden Abschnitte, um den zu erwerbenden Plan anhand Ihrer CSV-Datei mit den Nutzungsdaten zu identifizieren.

## <a name="discount-applies-to-different-vm-sizes"></a>Rabatt gilt für verschiedene VM-Größen

Wie reservierte VM-Instanzen bieten Red Hat-Pläne Flexibilität bei der Instanzgröße. Dies bedeutet, dass der Rabatt auch angewendet wird, wenn Sie einen virtuellen Computer mit einer anderen Anzahl von vCPUs bereitstellen. Der Rabatt gilt für verschiedene VM-Größen im Softwareplan.

Der Rabattbetrag hängt von dem in den folgenden Tabellen aufgeführten Verhältnis ab. Das Verhältnis vergleicht den relativen Speicherbedarf für jede Verbrauchseinheit in dieser Gruppe. Das Verhältnis richtet sich nach den vCPUs des virtuellen Computers. Anhand des Verhältniswerts können Sie berechnen, wie viele VM-Instanzen den Rabatt des Red Hat Linux-Plans erhalten.

Wenn Sie einen Plan für Red Hat Linux Enterprise Server für einen virtuellen Computer mit 3 oder 4 vCPUs erwerben, ist der Verhältniswert für diese Reservierung z. B. „2“. Der Rabatt deckt die Red Hat-Softwarekosten für:

- 2 bereitgestellte VMs mit einer vCPU oder 2 vCPUs,
- einen bereitgestellten VM mit 3 oder 4 vCPUs
- oder 0,77 oder ca. 77 % eines virtuellen Computers mit mindestens 5 vCPUs.

Der Verhältniswert für mindestens 5 vCPUs ist „2,6“. Eine Reservierung für Red Hat mit einem VM mit mindestens 5 vCPUs deckt also nur einen Teil der Softwarekosten, nämlich ca. 77 %.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>Grundlegendes zur Nutzung von Red Hat-VMs vor dem Kauf

Die folgenden Tabellen enthalten die Softwarepläne, für die Sie eine Reservierung erwerben können, deren zugeordnete Verbrauchseinheiten und das jeweilige Verhältnis.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Namen der Marketplace-Angebote im Azure-Portal:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (aktuellste lvm)

|Red Hat-VM | MeterId| Verhältnis| Beispiel-VM-Größe|
| -------| ------------------------| --- |--- |
|1 bis 4 vCPU-VM-Lizenz|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1 bis 4 vCPU-VM-Lizenz|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1 bis 4 vCPU-VM-Lizenz|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 + vCPU-VM-Lizenz|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|037eddc0-fedd-4d73-b5d8-92fba9edb831|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|794dcb90-0793-43e6-9909-70d29974e56d|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|86c35ec3-0a48-426a-9625-22d80e6ea55b|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2,166666667|D8s_v3|
|5 + vCPU-VM-Lizenz|d09f877e-03b4-48b2-b11a-782b965cff19|2,166666667|D8s_v3|
|44 vCPU-VM-Lizenz|6f44ae85-a70e-44be-83ec-153a0bc23979|2,166666667||
|60 vCPU-VM-Lizenz|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2,166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP mit HANA

Name des Marketplace-Angebots im Azure-Portal:

|Red Hat-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- | --- | ------------------------| --- | --- |
|1 bis 4 vCPU-VM-Lizenz |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 + vCPU-VM-Lizenz|6dfb482b-23ea-487f-810c-e66360f025de|2,333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux mit Hochverfügbarkeit

Namen der Marketplace-Angebote im Azure-Portal:

|Red Hat-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- | --- |
|1 bis 4 vCPU-VM-Lizenz|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 + vCPU-VM-Lizenz|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Namen der Marketplace-Angebote im Azure-Portal:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- |--- |
|1 vCPU-VM-Lizenz|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU-VM-Lizenz|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU-VM-Lizenz|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 vCPU-VM-Lizenz|69a140fa-e08e-415c-85f2-48158e4c73a0|2,166666667||
|8 vCPU-VM-Lizenz|777a5a74-22d6-48c9-9705-ac38fe05a278|2,166666667|D8s_v3|
|12 vCPU-VM-Lizenz|d6b8917a-5127-497a-9f48-1e959df98812|2,166666667||
|16 vCPU-VM-Lizenz|03667e82-e009-425a-83f7-8ebddbca5af4|2,166666667|D16s_v3|
|20 vCPU-VM-Lizenz|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2,166666667||
|24 vCPU-VM-Lizenz|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2,166666667|ND24s|
|32 vCPU-VM-Lizenz|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2,166666667|D32s_v3|
|40 vCPU-VM-Lizenz|737142c3-8e4f-4fc1-aa41-05b1661edff8|2,166666667||
|44 vCPU-VM-Lizenz|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2,166666667||
|60 vCPU-VM-Lizenz|a22bb342-ba9a-4529-a178-39a92ce770b6|2,166666667||
|64 vCPU-VM-Lizenz|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2,166666667|64s_v3|
|72 vCPU-VM-Lizenz|14341b96-e92c-4dca-ba66-322c88a79aa6|2,166666667|F72s_v2|
|96 vCPU-VM-Lizenz|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2,166666667||
|128 vCPU-VM-Lizenz|9b198a68-974a-47a7-9013-49169ac0f2e9|2,166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Namen der Marketplace-Angebote im Azure-Portal:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat-VM | MeterId | Verhältnis|Beispiel-VM-Größe|
| ------- |------------------------| --- |--- |
|1 vCPU-VM-Lizenz|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU-VM-Lizenz|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU-VM-Lizenz|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 vCPU-VM-Lizenz|a5963812-0f5a-4053-8ace-2b5babd15ed8|2,166666667||
|8 vCPU-VM-Lizenz|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2,166666667|D8s_v3|
|12 vCPU-VM-Lizenz|0e3bc72d-a888-4bcf-8437-119f763a3215|2,166666667||
|16 vCPU-VM-Lizenz|b40e95d8-3176-42f0-967c-497785c031b2|2,166666667|D16s_v3|
|20 vCPU-VM-Lizenz|81f34277-499d-40a3-a634-99adc08e2d45|2,166666667||
|24 vCPU-VM-Lizenz|e03f1906-d35d-4084-b2cd-63281869c8ee|2,166666667|ND24s|
|32 vCPU-VM-Lizenz|0a58c082-ceb8-4327-9b64-887c30dddb23|2,166666667|D32s_v3|
|40 vCPU-VM-Lizenz|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2,166666667||
|44 vCPU-VM-Lizenz|378b8125-d8a5-4e09-99bc-c1462534ffb0|2,166666667||
|60 vCPU-VM-Lizenz|5d7db11a-54e9-404e-aaa8-509fac7c0638|2,166666667||
|64 vCPU-VM-Lizenz|3c8157b2-a57d-45ce-ba02-bd86e9209795|2,166666667|64s_v3|
|72 vCPU-VM-Lizenz|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2,166666667|F72s_v2|
|96 vCPU-VM-Lizenz|b13895fc-0d06-4de9-b860-627c471cd247|2,166666667||
|128 vCPU-VM-Lizenz|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2,166666667| M128ms|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)
- [Vorauszahlen für Red Hat-Softwarepläne mit Azure-Reservierungen](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
