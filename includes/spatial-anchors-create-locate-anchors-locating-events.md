---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993034"
---
Nachdem Ihr Watcher erstellt wurde, wird das `AnchorLocated`-Ereignis für jeden angeforderten Anker ausgelöst. Dieses Ereignis wird sogar dann ausgelöst, wenn ein Anker gefunden wird, oder der Anker nicht gefunden werden kann. Wenn dies geschieht, wird die Ursache im Status angegeben. Nachdem alle Anker für einen Watcher – ob gefunden oder nicht gefunden – verarbeitet wurden, wird das `LocateAnchorsCompleted`-Ereignis ausgelöst. Es besteht eine Beschränkung von 35 Bezeichner pro Watcher. 
