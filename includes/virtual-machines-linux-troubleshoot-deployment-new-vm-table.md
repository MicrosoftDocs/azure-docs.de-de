---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d93de4ed758afb5e951bb5e19f4f7adb290e461c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66160340"
---
In der folgenden Tabelle sind die möglichen Upload- und Erfassungskombinationen von generalisierten und spezialisierten Linux-Betriebssystem-Images aufgeführt. Die Kombinationen, die ohne Fehler verarbeitet werden, sind durch ein „J“ gekennzeichnet, und die Kombinationen, bei denen Fehler ausgelöst werden, durch ein „N“. Die Ursachen und Lösungen für die verschiedenen auftretenden Fehler werden unterhalb der Tabelle beschrieben.

| Betriebssystem | Upload (spez.) | Upload (gen.) | Erfassung (spez.) | Erfassung (gen.) |
| --- | --- | --- | --- | --- |
| Linux (gen.) |N<sup>1</sup> |J |N<sup>3</sup> |J |
| Linux (spez.) |J |N<sup>2</sup> |J |N<sup>4</sup> |

