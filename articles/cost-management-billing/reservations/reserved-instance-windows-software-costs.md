---
title: Softwarekosten für Azure-Reservierungen
description: Erfahren Sie, welche Verbrauchseinheiten für Software nicht in den Kosten für reservierte Azure-VM-Instanzen enthalten sind.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 625c40ea17cf20449f1be241a1605a80bf6a73f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352999"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Nicht in reservierten Azure-VM-Instanzen enthaltene Softwarekosten

Rabatte für reservierte VM-Instanzen und SQL-Kapazitäten gelten nur für Infrastrukturkosten und nicht für Softwarekosten. Wenn Sie einen virtuellen Windows-Computer verwenden und für Ihre reservierten VM-Instanzen keinen Azure-Hybridvorteil haben, werden Ihnen die im folgenden Abschnitt aufgeführten Verbrauchseinheiten für Software berechnet. Bei SQL PaaS-Bereitstellungen werden die IP-Kosten weiterhin mit einer separaten Verbrauchseinheit abgerechnet, wenn „Azure-Hybridvorteil“ nicht ausgewählt ist.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Nicht in den Reservierungskosten enthaltene Verbrauchseinheiten für Windows-Software

| MeterId | MeterName in Verwendungsdatei | Von virtuellem Computer verwendet |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Burstzeiträume für reservierte Windows Server-Instanzen (1 Kern) | B-Serie |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Burstzeiträume für reservierte Windows Server-Instanzen (2 Kerne) | B-Serie |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Burstzeiträume für reservierte Windows Server-Instanzen (4 Kerne) | B-Serie |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Burstzeiträume für reservierte Windows Server-Instanzen (8 Kerne) | B-Serie |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservierte Windows Server-Instanzen (1 Kern) | Alle außer B-Serie |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservierte Windows Server-Instanzen (2 Kerne) | Alle außer B-Serie |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservierte Windows Server-Instanzen (4 Kerne) | Alle außer B-Serie |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservierte Windows Server-Instanzen (6 Kerne) | Alle außer B-Serie |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservierte Windows Server-Instanzen (8 Kerne) | Alle außer B-Serie |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservierte Windows Server-Instanzen (12 Kerne) | Alle außer B-Serie |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservierte Windows Server-Instanzen (16 Kerne) | Alle außer B-Serie |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservierte Windows Server-Instanzen (20 Kerne) | Alle außer B-Serie |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservierte Windows Server-Instanzen (24 Kerne) | Alle außer B-Serie |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservierte Windows Server-Instanzen (32 Kerne) | Alle außer B-Serie |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservierte Windows Server-Instanzen (40 Kerne) | Alle außer B-Serie |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservierte Windows Server-Instanzen (64 Kerne) | Alle außer B-Serie |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservierte Windows Server-Instanzen (72 Kerne) | Alle außer B-Serie |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservierte Windows Server-Instanzen (128 Kerne) | Alle außer B-Serie |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservierte Windows Server-Instanzen (256 Kerne) | Alle außer B-Serie |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservierte Windows Server-Instanzen (96 Kerne) | Alle außer B-Serie |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Nicht in den Reservierungskosten enthaltene Verbrauchseinheiten für Software

| MeterId | MeterName in Verwendungsdatei |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU-Lizenz|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU-Lizenz|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU-Lizenz|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU-Lizenz|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU-Lizenz|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU-Lizenz|
|ca1af837-4B35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU-Lizenz|
|dc72ee45-2ab7-4698-B435-e2cf10d1f9f6|Cloud Services 64 vCPU-Lizenz|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU-Lizenz|

## <a name="get-rates-for-azure-meters"></a>Abrufen der Tarife für Azure-Verbrauchseinheiten

Sie können die Kosten jeder dieser Verbrauchseinheiten über die Azure RateCard-API abrufen. Informationen zum Abrufen der Tarife für eine Azure-Verbrauchseinheit finden Sie unter [Abrufen von Preis- und Metadateninformationen für in einem Azure-Abonnement verwendete Ressourcen](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Reservierungen für Azure finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../manage/understand-vm-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).