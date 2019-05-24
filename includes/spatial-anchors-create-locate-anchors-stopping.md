---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110612"
---
## <a name="pause-reset-or-stop-the-session"></a>Anhalten, Zurücksetzen oder Beenden einer Sitzung

Um eine Sitzung vorübergehend anzuhalten, können Sie `Stop()` aufrufen. Damit werden alle Überwachungselemente und sämtliche Prozesse der Umgebungsverarbeitung angehalten, auch wenn Sie „ProcessFrame()“ aufrufen. Danach können Sie `Start()` aufrufen, um die Verarbeitung fortzusetzen. Beim Fortsetzen werden alle in der Sitzung bereits erfassten Umgebungsdaten beibehalten.
