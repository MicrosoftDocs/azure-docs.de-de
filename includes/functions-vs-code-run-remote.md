---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809563"
---
## <a name="run-the-function-in-azure"></a>Ausführen der Funktion in Azure

1. Erweitern Sie im Bereich **Azure: Funktionen** auf der Seitenleiste Ihr Abonnement, die neue Funktions-App und **Funktionen**. Klicken Sie unter Windows mit der rechten Maustaste (unter macOS <kbd>STRG</kbd>+Mausklick) auf die Funktion `HttpExample`, und wählen Sie **Funktion jetzt ausführen...** aus.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="„Funktion jetzt ausführen“ in Azure über Visual Studio Code":::

1. Unter **Enter request body** (Anforderungstext eingeben) wird `{ "name": "Azure" }` als Wert für den Text der Anforderungsnachricht angezeigt. Drücken Sie die EINGABETASTE, um diese Anforderungsnachricht an Ihre Funktion zu senden.  

1. Wenn die Funktion in Azure ausgeführt wird und eine Antwort zurückgibt, wird in Visual Studio Code eine Benachrichtigung ausgelöst.
