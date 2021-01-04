---
title: Verwaltetes Datenträgerbursting
description: Erfahren Sie mehr über Datenträgerbursting für Azure-Datenträger und virtuelle Azure-Computer.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a8d1a69070628248fb710a614369ff9311252c05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102675"
---
# <a name="managed-disk-bursting"></a>Verwaltetes Datenträgerbursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting auf VM-Ebene
Burstingunterstützung auf VM-Ebene ist in allen Regionen in der öffentlichen Cloud für die folgenden unterstützten Größen aktiviert: 
- [Lsv2-Serie](lsv2-series.md)

Bursting auf VM-Ebene ist auch in „USA, Westen-Mitte“ für die folgenden unterstützten Größen erhältlich:
- [Dsv3-Serie](dv3-dsv3-series.md)
- [Esv3-Serie](ev3-esv3-series.md)

Bursting ist für virtuelle Computer, die diese Technologie unterstützen, standardmäßig aktiviert.

## <a name="disk-level-bursting"></a>Bursting auf Datenträgerebene
Bursting steht auch für unsere [Premium-SSDs](disks-types.md#premium-ssd) für die Datenträgergrößen P20 und kleiner in allen Regionen („Azure, öffentlich“, Government und China-Clouds) zur Verfügung. Das Datenträgerbursting ist standardmäßig in allen neuen und vorhandenen Bereitstellungen der unterstützten Datenträgergrößen aktiviert. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]