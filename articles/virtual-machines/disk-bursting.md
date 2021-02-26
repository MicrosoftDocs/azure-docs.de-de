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
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576192"
---
# <a name="managed-disk-bursting"></a>Verwaltetes Datenträgerbursting
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Bursting auf VM-Ebene
Das Bursting auf VM-Ebene ist für die folgenden VM-Serien in allen Regionen aktiviert, in denen dies unterstützt wird:
- [Lsv2-Serie](lsv2-series.md)
- [Dsv3-Serie](dv3-dsv3-series.md)
- [Esv3-Serie](ev3-esv3-series.md)

Bursting ist für virtuelle Computer, die diese Technologie unterstützen, standardmäßig aktiviert.

## <a name="disk-level-bursting"></a>Bursting auf Datenträgerebene
Bursting steht auch für unsere [Premium-SSDs](disks-types.md#premium-ssd) für die Datenträgergrößen P20 und kleiner in allen Regionen („Azure, öffentlich“, Government und China-Clouds) zur Verfügung. Das Datenträgerbursting ist standardmäßig in allen neuen und vorhandenen Bereitstellungen der unterstützten Datenträgergrößen aktiviert. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
