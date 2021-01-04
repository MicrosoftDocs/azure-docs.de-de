---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26a5537496d9e881ece135437c403baf4a4fd67c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016630"
---
Derzeit gibt es für Ultra-Datenträger weitere Einschränkungen, die wie folgt lauten:

Die einzigen Optionen für die Infrastrukturredundanz, die derzeit für Disk Ultra-Datenträger zur Verfügung stehen, sind Verfügbarkeitszonen. Virtuelle Computer, die andere Redundanzoptionen verwenden, können keinen Disk Ultra-Datenträger anfügen.

In der folgenden Tabelle sind die Regionen, in denen Disk Ultra-Datenträger verfügbar sind, sowie die entsprechenden Verfügbarkeitsoptionen aufgeführt:

> [!NOTE]
> Wenn eine Region in der folgenden Liste keine Verfügbarkeitszonen für Ultradatenträger enthält, müssen die virtuellen Computer in dieser Region ohne Optionen für die Infrastrukturredundanz bereitgestellt werden, um einen Ultradatenträger anzufügen.

|Regions  |Redundanzoptionen  |
|---------|---------|
|Brasilien, Süden     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|Indien, Mitte     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|Asien, Osten     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|Deutschland, Westen-Mitte     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|Korea, Mitte     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|USA Süd Mitte    |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|US Gov Arizona     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|US Government, Virginia     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|US Gov Texas     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|USA (Westen)     |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)        |
|Australien, Mitte    |Nur einzelne VMs (Verfügbarkeitsgruppen und VM-Skalierungsgruppen werden nicht unterstützt)|
|Australien (Osten)     |Drei Verfügbarkeitszonen         |
|Asien, Südosten    |Drei Verfügbarkeitszonen        |
|Kanada, Mitte*     |Drei Verfügbarkeitszonen          |
|USA (Mitte)     |Drei Verfügbarkeitszonen          |
|East US     |Drei Verfügbarkeitszonen          |
|USA (Ost) 2     |Drei Verfügbarkeitszonen         |
|Frankreich, Mitte    |Zwei Verfügbarkeitszonen        |
|Japan, Osten    |Drei Verfügbarkeitszonen        |
|Nordeuropa    |Drei Verfügbarkeitszonen        |
|UK, Süden    |Drei Verfügbarkeitszonen        |
|Europa, Westen    | Drei Verfügbarkeitszonen|
|USA, Westen 2    |Drei Verfügbarkeitszonen|

\* Wenden Sie sich an den Azure-Support, um Zugriff auf Verfügbarkeitszonen für diese Region zu erhalten.

- Werden nur auf den folgenden VM-Serien unterstützt:
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)
    - [Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series)
    - [Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Nicht jede VM-Größe ist in allen unterstützten Regionen mit Ultra-Datenträgern verfügbar.
- Nur als Datenträger verfügbar. 
- Unterstützt standardmäßig die physische Sektorgröße von 4K. Die Sektorgröße 512E ist als allgemein verfügbares Angebot verfügbar (keine Registrierung erforderlich), ist aber derzeit nur über die CLI oder PowerShell verfügbar. Die meisten Anwendungen sind mit der Sektorgröße 4K kompatibel, einige benötigen jedoch eine Sektorgröße von 512 Byte. Für Oracle Database ist beispielsweise die Version 12.2 oder höher erforderlich, um native Datenträger mit 4K zu unterstützen. Ältere Oracle DB-Versionen benötigen eine Sektorgröße von 512 Byte.
- Können nur als leere Datenträger erstellt werden.
- Unterstützt derzeit keine Momentaufnahmen von Datenträgern, VM-Images, Verfügbarkeitsgruppen, Azure Dedicated Hosts oder Azure Disk Encryption.
- Unterstützt derzeit keine Integration in Azure Backup oder Azure Site Recovery.
- Unterstützt nur Lese- und Schreibvorgänge, die nicht zwischengespeichert sind.
- Die IOPS-Obergrenze liegt bei allgemein verfügbaren virtuellen Computern derzeit bei 80.000.

Azure Ultra-Datenträger bieten standardmäßig bis zu 16 TiB pro Region pro Abonnement, aber Disk Ultra-Datenträger unterstützen auf Anfrage auch höhere Kapazitäten. Wenden Sie sich an den Azure-Support, um eine Erhöhung der Kapazität anzufordern.
