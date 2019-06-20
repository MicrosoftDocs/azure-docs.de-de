---
title: Zuordnung von Diensten zu Verbrauchseinheiten für das kostenlose Azure-Konto | Microsoft Docs
description: Grundlegendes zur Zuordnung von Diensten zu Verbrauchseinheiten für Dienste, die im kostenlosen Konto enthalten sind.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370729"
---
# <a name="understand-free-service-to-meter-mapping"></a>Grundlegendes zur Zuordnung kostenloser Dienste zu Verbrauchseinheiten

Die Nutzung jedes Azure-Diensts wird in Verbrauchseinheiten gemessen, die das Azure-Abrechnungssystem nutzt, um Benutzern Dienste in Rechnung zu stellen. Um die Verwendung kostenloser Dienste besser zu verstehen, betrachten wir Zuordnung von Diensten zu Verbrauchseinheiten für diese Dienste. Wie Sie kostenlose Dienste erstellen, erfahren Sie unter [Create services included with Azure free account](billing-create-free-services-included-free-account.md) (Erstellen kostenloser Dienste mit dem kostenlosen Azure-Konto).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Zuordnung von Diensten zu Verbrauchseinheiten für Dienste, die im kostenlosen Konto enthalten sind 

|    Dienst   | Name der Verbrauchseinheit im Azure-Portal | Name der Verbrauchseinheit in der Nutzungsdatei/API | Meter ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S-Linux-VM | Computestunden – Standard_B1-VM | Computestunden – kostenlos | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S-Windows-VM | Computestunden – Standard_B1-VM (Windows) | Computestunden – kostenlos | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S-VM – öffentliche IP-Adressen  | IP-Adressstunden – öffentliche IP-Adressen | IP-Adressstunden – kostenlos | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Speicher (GB) – Cosmos-DB | Speicher (GB) – kostenlos | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 Anforderungseinheiten (Stunden) – Cosmos-DB | 100 Anforderungseinheiten (Stunden) – kostenlos | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standard-E/A – Dateien (GB) – lokal redundant | Standard-E/A – Dateien (GB) – kostenlos | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard-E/A – Dateilesevorgangseinheiten (in 10.000) | Standard-E/A – Dateilesevorgangseinheiten (in 10.000) – kostenlos | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard-E/A – Dateischreibvorgangseinheiten (in 10.000) | Standard-E/A – Dateischreibvorgangseinheiten (in 10.000) – kostenlos | 223d8004-d29a-46CF-b4f4-d2d34b12548b
| File Storage | Standard-E/A – Dateiprotokoll-Vorgangseinheiten (in 10.000) | Standard-E/A – Dateiprotokoll-Vorgangseinheiten (in 10.000) – kostenlos | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard-E/A – Dateiauflistungsvorgangseinheiten (in 10.000) | Standard-E/A – Dateiauflistungsvorgangseinheiten (in 10.000) – kostenlos | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Speicher für heiße Blockblobs | Standard-E/A – Lesevorgänge für heiße Blockblobs (in 10.000) | Standard-E/A – Lesevorgänge für heiße Blockblobs (in 10.000) – kostenlos |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Speicher für heiße Blockblobs | Standard-E/A – heißes Blockblob (GB) – lokal redundant | Standard-E/A – heißes Blockblob (GB) – kostenlos | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Speicher für heiße Blockblobs | Standard-E/A – Schreibvorgänge für heiße Blockblobs (in 10.000) | Standard-E/A – Schreibvorgänge für heiße Blockblobs (in 10.000) – kostenlos | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Speicher für heiße Blockblobs  | Standard-E/A – Schreib-/Listvorgänge für heiße Blockblobs (in 10.000) | Standard-E/A – Schreib-/Listvorgänge für heiße Blockblobs (in 10.000) – kostenlos | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Verwalteter Datenträger *  | Verwalteter Datenträger Standard/Snapshots (GB) – lokal redundant | Verwalteter Datenträger Standard/Snapshots (GB) – kostenlos | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Verwalteter Datenträger *  | Managed Disks Standard-Vorgänge (in 10.000) | Managed Disks Standard-Vorgänge (in 10.000) – kostenlos | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| Verwalteter Datenträger *  | Storage Premium – Seitenblob/P6 (Einheiten) – lokal redundant | Storage Premium – Seitenblob/P6 (Einheiten) – kostenlos | 2b98c168-27CA-4cc1-b509-e887dec87657
| SQL-Datenbank | Standard-S0-Datenbanktage – SQL-Datenbank | Standard-S0-Datenbanktage – kostenlos | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Shared – Bandbreite ** | Datenübertragung ausgehend (GB) | Datenübertragung ausgehend (GB) – kostenlos | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Wenn Sie einen virtuellen Windows-Computer erstellen und „Verwalteter Datenträger“ auswählen, nutzen Sie die Verbrauchseinheit „Verwalteter Datenträger“ als Teil des virtuellen Computers.

\** Shared-Verbrauchseinheiten können von mehreren Diensten verwendet werden. Beispielsweise wird die Verbrauchseinheit „Datenübertragung ausgehend (GB)“ sowohl für die Nutzung von virtuellen Computern als auch von Speicher verwendet.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
