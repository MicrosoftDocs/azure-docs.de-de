---
title: 'Bereitstellen von Modellen in der Produktion: Team Data Science-Prozess'
description: Erfahren Sie, wie Sie Modelle in der Produktion bereitstellen, damit sie eine aktive Rolle bei geschäftlichen Entscheidungen spielen können.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 89ea1e991df46b4e4d23305b6118980b80c2f917
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321191"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Bereitstellen von Modellen in der Produktion, um eine aktive Rolle bei geschäftlichen Entscheidungen zu spielen

Bei einer Produktionsbereitstellung kann ein Modell eine aktive Rolle in einem Unternehmen spielen. Vorhersagen aus einem bereitgestellten Modell können für Geschäftsentscheidungen verwendet werden.

## <a name="production-platforms"></a>Produktionsplattformen

Es gibt verschiedene Ansätze und Plattformen für das Einführen von Modellen in die Produktion. Hier sind einige Optionen angegeben:

- [Bereitstellen von Modellen mit Azure Machine Learning](../how-to-deploy-and-where.md)
- [Bereitstellung eines Modells in SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Vor der Bereitstellung muss sichergestellt werden, dass bei der Bewertung durch das Modell ausreichend geringe Latenzen für eine Verwendung in der Produktion auftreten.
>

>[!NOTE]
>Informationen zur Bereitstellung mit Azure Machine Learning Studio finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-Tests

Wenn sich mehrere Modelle in der Produktion befinden, kann die Modellleistung mithilfe von [A/B-Tests](https://en.wikipedia.org/wiki/A/B_testing) verglichen werden. 
 
## <a name="next-steps"></a>Nächste Schritte

Exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden.