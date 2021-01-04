---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: 874d76bebdfd3bd0daba1f83cb1f06c093f192ec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021279"
---
## <a name="get-the-training-and-prediction-keys"></a>Abrufen der Trainings- und Vorhersageschlüssel

Das Projekt benötigt gültige Abonnementschlüssel, um mit dem Dienst zu interagieren. Sie finden die Elemente auf der [Custom Vision-Website](https://customvision.ai). Melden Sie sich mit dem Konto an, das dem Azure-Konto zugeordnet ist, mit dem Ihre Custom Vision-Ressourcen erstellt wurden. Wählen Sie rechts oben auf der Startseite (die Seite mit der Option zum Hinzufügen eines neuen Projekts) das __Zahnradsymbol__ aus. Suchen Sie in der Liste nach Ihren Trainings- und Vorhersageressourcen, und erweitern Sie sie. Hier finden Sie Ihren Trainingsschlüssel, Ihren Vorhersageschlüssel und Ihre Vorhersageressourcen-ID. Speichern Sie diese Werte an einem temporären Speicherort.

> [!NOTE]
> Wenn Sie einen All-in-One-Schlüssel für Cognitive Services verwenden, um auf Custom Vision zuzugreifen, wird auf dem Einstellungsbildschirm nur ein Schlüssel angezeigt. In diesem Fall verwenden Sie denselben Schlüssel für Trainings- und Vorhersagevorgänge.

![Abbildung der Schlüsselbenutzeroberfläche](../media/csharp-tutorial/training-prediction-keys.png)

Alternativ können Sie diese Schlüssel und die ID über das [Azure-Portal](https://www.portal.azure.com) abrufen, indem Sie Ihre Custom Vision-Trainings- und Vorhersageressourcen anzeigen und zur Registerkarte __Schlüssel__ navigieren. Dort finden Sie Ihren Trainingsschlüssel und Ihren Vorhersageschlüssel. Navigieren Sie zur Registerkarte __Eigenschaften__ Ihrer Vorhersageressource, um Ihre Vorhersageressourcen-ID zu erhalten.

