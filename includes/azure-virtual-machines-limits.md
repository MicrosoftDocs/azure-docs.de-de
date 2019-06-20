---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 083d59a2d8720a08abc7ea8998c1fbe048db3f28
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178449"
---
| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| [Virtuelle Computer](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pro Clouddienst<sup>1</sup> |50 |50 |
| Eingabeendpunkte pro Clouddienst<sup>2</sup> |150 |150 |

<sup>1</sup>Virtuelle Computer, die mithilfe des klassischen Bereitstellungsmodells anstelle von Azure Resource Manager erstellt werden, werden automatisch in einem Clouddienst gespeichert. Für den Lastenausgleich und die Verfügbarkeit können Sie diesem Clouddienst weitere virtuelle Computer hinzufügen. 

<sup>2</sup>Eingabeendpunkte ermöglichen die Kommunikation mit einem virtuellen Computer von außerhalb des Clouddiensts des virtuellen Computers. Virtuelle Computer im gleichen Clouddienst oder virtuellen Netzwerk können automatisch miteinander kommunizieren. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
