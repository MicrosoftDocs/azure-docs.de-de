---
title: 'Ausführen von Data Science-Aufgaben: Team Data Science-Prozess'
description: Erfahren Sie, wie ein Datenanalyst ein Data Science-Projekt auf nachverfolgbare Weise mit Versionskontrolle und Zusammenarbeit ausführen kann.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100371956"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Ausführen von Data Science-Aufgaben: Durchsuchen, Modellieren und Bereitstellen

Zu den typischen Data Science-Aufgaben gehören das Durchsuchen, Modellieren und Bereitstellen. In diesem Artikel wird beschrieben, wie Sie verschiedene allgemeine Data Science-Aufgaben wie interaktive Datenerkundung, Datenanalysen, Berichterstellung und Modellerstellung ausführen. Zu den Optionen für die Bereitstellung eines Modells in einer Produktionsumgebung können die folgenden gehören:

- [Azure Machine Learning](../index.yml)
- [SQL Server mit ML-Diensten](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Durchsuchen 

Datenanalysten haben verschiedene Möglichkeiten für das Durchsuchen und die Berichterstellung: mit verfügbaren Bibliotheken und Paketen für Python (z.B. matplotlib) oder mit R (z.B. ggplot oder lattice). Solchen Code können Datenanalysten entsprechend den Anforderungen an Datendurchsuchung für spezifische Szenarien anpassen. Die Anforderungen für den Umgang mit strukturierten Daten unterscheiden sich von denen für unstrukturierte Daten wie Texte oder Bilder. 

Produkte wie Azure Machine Learning bieten auch eine [erweiterte Datenaufbereitung](../how-to-create-register-datasets.md) für Data Wrangling und das Erkunden von Daten, einschließlich der Erstellung von Features. Der Benutzer sollte entscheiden, welche Tools, Bibliotheken und Pakete seinen Anforderungen am besten entsprechen. 

Das Ergebnis dieser Phase ist ein Bericht zur Datendurchsuchung. Der Bericht sollte eine umfassende Ansicht der Daten für die Modellierung und eine Bewertung, ob die Daten für den Modellierungsschritt geeignet sind, enthalten. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modellierung

Es gibt zahlreiche Toolkits und Pakete für das Trainieren von Modellen in einer Vielzahl von Sprachen. Datenanalysten können die verwenden, mit denen sie gut umgehen können, solange die Leistungsaspekte bezüglich der Genauigkeit und Latenz für die relevanten geschäftlichen Anwendungsfälle und Produktionsszenarien zufriedenstellend sind.

### <a name="model-management"></a>Modellverwaltung
Nachdem mehrere Modelle erstellt wurden, benötigen Sie in der Regel ein System zum Registrieren und Verwalten der Modelle. Normalerweise benötigen Sie eine Kombination aus Skripts oder APIs und einer Back-End-Datenbank oder einem Versionsverwaltungssystem. Für diese Verwaltungsaufgaben stehen Ihnen unter anderem die folgenden Optionen zur Verfügung:

1. [Azure Machine Learning-Modellverwaltungsdienst](../index.yml)
2. [ModelDB von MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL Server als ein Modellverwaltungssystem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Bereitstellung

Bei einer Produktionsbereitstellung kann ein Modell eine aktive Rolle in einem Unternehmen spielen. Vorhersagen aus einem bereitgestellten Modell können für Geschäftsentscheidungen verwendet werden.

### <a name="production-platforms"></a>Produktionsplattformen
Es gibt verschiedene Ansätze und Plattformen für das Einführen von Modellen in die Produktion. Hier sind einige Optionen angegeben:


- [Modellentwicklung in Azure Machine Learning](../how-to-deploy-and-where.md)
- [Bereitstellung eines Modells in SQL Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Vor der Bereitstellung muss sichergestellt werden, dass bei der Bewertung durch das Modell ausreichend geringe Latenzen für eine Verwendung in der Produktion auftreten.
>
>

Weitere Beispiele, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind in Form von exemplarischen Vorgehensweisen verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden.

> [!NOTE]
> Informationen zur Bereitstellung mit Azure Machine Learning Studio finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B-Tests
Wenn sich mehrere Modelle in der Produktion befinden, kann es nützlich sein, [A/B-Tests](https://en.wikipedia.org/wiki/A/B_testing) zum Vergleichen der Leistung der Modelle durchzuführen. 

 
## <a name="next-steps"></a>Nächste Schritte

In [Nachverfolgen des Fortschritts von Data Science-Projekten](track-progress.md) wird gezeigt, wie ein Datenanalyst den Status eines Data Science-Projekts nachverfolgen kann.

Unter [Modellvorgang und CI-CD](ci-cd-flask.md) wird gezeigt, wie CI/CD mit entwickelten Modellen ausgeführt werden kann.
