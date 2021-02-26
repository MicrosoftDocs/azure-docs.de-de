---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 9d5bae1aedbd031a0a3c08ba5141aebc22f1c674
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493381"
---
Nun können Sie den neuen Parameter `msg` verwenden, um von Ihrem Funktionscode aus in die Ausgabebindung zu schreiben. Fügen Sie vor der Erfolgsantwort die folgende Codezeile hinzu, um der Ausgabebindung `msg` den Wert `name` hinzuzufügen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="34":::

Bei Verwendung einer Ausgabebindung müssen Sie weder den Azure Storage SDK-Code für die Authentifizierung verwenden noch einen Warteschlangenverweis abrufen oder Daten schreiben. Die Functions-Runtime und die Warteschlangenausgabebindung übernehmen diese Aufgaben für Sie.

Die Methode `run` sollte bei Ihnen nun wie im folgenden Beispiel aussehen:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="16-38":::