---
title: Schritte zum Importieren von Apps
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130732"
---
1. Wählen Sie im [LUIS-Portal](https://www.luis.ai) auf der Seite **Meine Apps** die Option **+ New app for conversation** (+ Neue App für die Konversation) und dann **Als JSON importieren** aus. Suchen Sie die gespeicherte JSON-Datei aus dem vorherigen Schritt. Sie müssen den Namen der App nicht ändern. Wählen Sie **Fertig** aus.

1. Wählen Sie auf der Registerkarte **Versionen** im Abschnitt **Verwalten** die Version `0.1` aus. Wählen Sie dann **Klonen** aus, um die Version zu klonen. Geben Sie anschließend einen Namen (`ml-entity`) ein, und wählen Sie **Fertig** aus, um den Klonvorgang abzuschließen. Da der Versionsname als Teil der URL-Route verwendet wird, darf er keine Zeichen enthalten, die in einer URL ungültig sind.

    > [!TIP]
    > Das Klonen in eine neue Version ist eine bewährte Methode vor dem Ändern einer App. Wenn Sie den Vorgang mit einer geänderten Version abschließen, exportieren Sie die Version (als JSON- oder LU-Datei), und checken Sie diese in Ihre Quellcodeverwaltung ein.

1. Wählen Sie im oberen Bereich die Option **Erstellen** aus, und klicken Sie dann im linken Navigationsmenü auf **Absichten**. Die Absichten der App werden angezeigt. Dies sind die Hauptbausteine einer LUIS-App.
