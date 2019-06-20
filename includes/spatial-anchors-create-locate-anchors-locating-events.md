---
ms.openlocfilehash: c4339aa8548ef66c862200ad61b6aaca90332ad0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177810"
---
Nachdem Ihr Watcher erstellt wurde, wird das `AnchorLocated`-Ereignis für jeden angeforderten Anker ausgelöst. Dieses Ereignis wird sogar dann ausgelöst, wenn ein Anker gefunden wird, oder der Anker nicht gefunden werden kann. Wenn dies geschieht, wird die Ursache im Status angegeben. Nachdem alle Anker für einen Watcher – ob gefunden oder nicht gefunden – verarbeitet wurden, wird das `LocateAnchorsCompleted`-Ereignis ausgelöst.
