---
title: Verwenden des Moduls „Score Image Model“
titleSuffix: Azure Machine Learning
description: In diesem Artikel erfahren Sie, wie Sie das Modul „Score Image Model“ (Bildmodell bewerten) in Azure Machine Learning zum Generieren von Vorhersagen mit einem trainierten Klassifizierungs- oder Regressionsmodell verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324893"
---
# <a name="score-image-model"></a>Bewerten eines Bildmodells

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Mit diesem Modul können Sie mithilfe eines trainierten Bildmodells Vorhersagen zu eingegebenen Bilddaten treffen.

## <a name="how-to-configure-score-image-model"></a>Konfigurieren des Moduls „Score Image Model“ (Bildmodell bewerten)

1. Fügen Sie das Modul **Score Image Model** (Bildmodell bewerten) Ihrer Pipeline hinzu.

2. Fügen Sie ein trainiertes Bildmodell und ein Dataset mit neuen Eingabebilddaten an. 

    Die Daten sollten den Typ „ImageDirectory“ aufweisen. Im Artikel zum Modul [Convert to Image Directory](convert-to-image-directory.md) (In Bildverzeichnis konvertieren) finden Sie weitere Informationen zum Abrufen eines Bildverzeichnisses. Das Schema des Eingabedatasets sollte auch generell mit dem Schema der zum Trainieren des Modells verwendeten Daten übereinstimmen.

3. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nachdem Sie eine Reihe von Bewertungen mithilfe des Moduls [Score Image Model](score-image-model.md) (Bildmodell bewerten) generiert haben, können Sie einige Metriken für die Auswertung der Modellgenauigkeit (Leistung) generieren, indem Sie das Modell und das bewertete Dataset mit dem Modul [Evaluate Model](evaluate-model.md) (Modell auswerten) verbinden. 

### <a name="publish-scores-as-a-web-service"></a>Veröffentlichen eines Modells als Webdienst

Eine häufige Verwendung der Bewertung ist die Rückgabe der Ausgabe als Teil eines Vorhersagewebdiensts. Weitere Informationen finden Sie in [diesem Tutorial](../tutorial-designer-automobile-price-deploy.md) zum Bereitstellen eines Echtzeitendpunkts basierend auf einer Pipeline im Azure Machine Learning-Designer.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.