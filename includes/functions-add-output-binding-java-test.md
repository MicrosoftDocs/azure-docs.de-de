---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739812"
---
Da der Archetyp auch eine Reihe von Tests erstellt, müssen Sie diese Tests aktualisieren, um den neuen Parameter `msg` in der Signatur der Methode `run` zu behandeln.  

Navigieren Sie zum Speicherort Ihres Testcodes (unter _src/test/java_), öffnen Sie die Projektdatei *Function.java*, und ersetzen Sie die Codezeile unter `//Invoke` durch den folgenden Code:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
