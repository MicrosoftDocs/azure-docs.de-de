---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177812"
---
## <a name="pause-reset-or-stop-the-session"></a>Anhalten, Zurücksetzen oder Beenden einer Sitzung

Um eine Sitzung vorübergehend anzuhalten, können Sie `Stop()` aufrufen. Damit werden alle Überwachungselemente und sämtliche Prozesse der Umgebungsverarbeitung angehalten, auch wenn Sie „ProcessFrame()“ aufrufen. Danach können Sie `Start()` aufrufen, um die Verarbeitung fortzusetzen. Beim Fortsetzen werden alle in der Sitzung bereits erfassten Umgebungsdaten beibehalten.
