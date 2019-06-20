---
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 14abae6f6f72d724fffb1ccaa12f56fb6976f7a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178091"
---
In der folgenden Tabelle sind die möglichen Upload- und Erfassungskombinationen von generalisierten (gen.) und spezialisierten (spez.) Windows-Betriebssystem-Images aufgeführt. Die Kombinationen, die ohne Fehler verarbeitet werden, sind durch ein „J“ gekennzeichnet, und die Kombinationen, bei denen Fehler ausgelöst werden, durch ein „N“. Die Ursachen und Lösungen für die verschiedenen auftretenden Fehler werden unterhalb der Tabelle beschrieben.

| Betriebssystem | Upload (spez.) | Upload (gen.) | Erfassung (spez.) | Erfassung (gen.) |
| --- | --- | --- | --- | --- |
| Windows (gen.) |N<sup>1</sup> |J |N<sup>3</sup> |J |
| Windows (spez.) |J |N<sup>2</sup> |J |N<sup>4</sup> |

