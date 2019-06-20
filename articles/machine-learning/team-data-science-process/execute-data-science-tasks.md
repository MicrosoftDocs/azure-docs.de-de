---
title: 'Ausführen von Data Science-Aufgaben: Team Data Science-Prozess'
description: Erfahren Sie, wie ein Datenanalyst ein Data Science-Projekt auf nachverfolgbare Weise mit Versionskontrolle und Zusammenarbeit ausführen kann.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9d8ae3a95262b1554e7e97fac8375a44743bf4df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344664"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Ausführen von Data Science-Aufgaben: Durchsuchen, Modellieren und Bereitstellen

Zu den typischen Data Science-Aufgaben gehören das Durchsuchen, Modellieren und Bereitstellen. In diesem Artikel wird gezeigt, wie mit den Hilfsprogrammen **IDEAR (Interactive Data Exploration, Analysis, and Reporting)** und **AMAR (Automated Modeling and Reporting)** verschiedene häufige Data Science-Aufgaben wie interaktive Datendurchsuchung, Datenanalyse, Berichterstellung und Modellbildung ausgeführt werden. Es werden auch Optionen zum Bereitstellen eines Modells in einer Produktionsumgebung mit einer Vielzahl von Toolkits und Datenplattformen vorgestellt, etwa mit den folgenden:

- [Azure Machine Learning](../service/index.yml)
- [SQL Server mit ML-Diensten](https://docs.microsoft.com/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server)


## 1. <a name='DataQualityReportUtility-1'></a> Durchsuchen 

Datenanalysten haben verschiedene Möglichkeiten für das Durchsuchen und die Berichterstellung: mit verfügbaren Bibliotheken und Paketen für Python (z.B. matplotlib) oder mit R (z.B. ggplot oder lattice). Solchen Code können Datenanalysten entsprechend den Anforderungen an Datendurchsuchung für spezifische Szenarien anpassen. Die Anforderungen für den Umgang mit strukturierten Daten unterscheiden sich von denen für unstrukturierte Daten wie Texte oder Bilder. 

Produkte wie der Azure Machine Learning-Dienst bieten auch eine [erweiterte Datenvorbereitung](../service/how-to-transform-data.md) für den Umgang mit Daten und das Durchsuchen von diesen, einschließlich der Erstellung von Features. Der Benutzer sollte entscheiden, welche Tools, Bibliotheken und Pakete seinen Anforderungen am besten entsprechen. 

Das Ergebnis dieser Phase ist ein Bericht zur Datendurchsuchung. Der Bericht sollte eine umfassende Ansicht der Daten für die Modellierung und eine Bewertung, ob die Daten für den Modellierungsschritt geeignet sind, enthalten. Die TDSP-Hilfsprogramme (Team Data Science-Prozess), die in den folgenden Abschnitten für die teilautomatisierte Durchsuchung, Modellierung und Berichterstellung erläutert werden, bieten auch standardisierte Datendurchsuchungs- und Modellierungsberichte. 

### <a name="interactive-data-exploration-analysis-and-reporting-using-the-idear-utility"></a>Interaktive Datendurchsuchung, Analyse und Berichterstellung mithilfe des Hilfsprogramms IDEAR

Dieses auf R Markdown oder einem Python-Notebook basierende Hilfsprogramm stellt ein flexibles und interaktives Tool zum Auswerten und Durchsuchen von DataSets dar. Benutzer können aus dem DataSet mit minimaler Codierung schnell Berichte generieren. Benutzer können auf Schaltflächen klicken, um die im interaktiven Tool angezeigten Untersuchungsergebnisse in einen abschließenden Bericht zu exportieren. Dieser kann an Kunden geliefert oder für Entscheidungen, welche Variablen in den nachfolgenden Modellierungsschritt eingeschlossen werden sollen, herangezogen werden.

Zurzeit funktioniert das Tool nur mit Datenrahmen im Arbeitsspeicher. Zum Angeben der zu durchsuchenden Parameter des DataSets ist eine YAML-Datei erforderlich. Weitere Informationen finden Sie unter [IDEAR in TDSP Data Science-Hilfsprogrammen](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


## 2. <a name='ModelingUtility-2'></a> Modellierung

Es gibt zahlreiche Toolkits und Pakete für das Trainieren von Modellen in einer Vielzahl von Sprachen. Datenanalysten können die verwenden, mit denen sie gut umgehen können, solange die Leistungsaspekte bezüglich der Genauigkeit und Latenz für die relevanten geschäftlichen Anwendungsfälle und Produktionsszenarien zufriedenstellend sind.

Im nächsten Abschnitt wird gezeigt, wie Sie die teilautomatisierte Modellierung mithilfe eines auf R basierenden TDSP-Hilfsprogramms erledigen. Mit diesem Hilfsprogramm, AMAR, können Sie Grundmodelle einschließlich der Parameter, die zum Bereitstellen eines Modells mit besserer Leistung optimiert werden müssen, schnell erstellen.
Im folgenden Abschnitt zur Modellverwaltung wird ein System zum Registrieren und Verwalten mehrerer Modelle veranschaulicht.


### <a name="model-training-modeling-and-reporting-using-the-amar-utility"></a>Modelltraining: Modellierung und Berichterstellung mit dem Hilfsprogramm AMAR

Das Hilfsprogramm [Automated Modeling and Reporting (AMAR)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling) ist ein anpassbares, teilautomatisiertes Tool zur Modellerstellung mit Hyperparametersweeping und zum Vergleichen der Genauigkeit dieser Modelle. 

Das Hilfsprogramm für die Modellerstellung ist eine R Markdown-Datei, die ausgeführt werden kann und dann eine eigenständige HTML-Ausgabe mit einem Inhaltsverzeichnis für einfache Navigation durch die verschiedenen Abschnitte erzeugt. Beim Ausführen der Markdowndatei werden drei Algorithmen ausgeführt: standardisierte Regression mit dem Paket glmnet, zufällige Gesamtstruktur mit dem Paket randomForest und Verstärkung von Strukturen mit dem Paket xgboost. Jeder dieser Algorithmen erzeugt ein trainiertes Modell. Anschließend wird die Genauigkeit dieser Modelle verglichen, und Plots für die relative Wichtigkeit dieser Features werden gemeldet. Derzeit gibt es zwei Hilfsprogramme: eines für eine Aufgabe der binären Klassifikation und eines für eine Regressionsaufgabe. Der wichtigste Unterschied zwischen ihnen ist die Art, wie Steuerparameter und Genauigkeitsmetriken für diese Lernaufgaben angegeben werden. 

Mit YAML-Dateien wird Folgendes angegeben:

- die Dateneingabe (eine SQL-Quelle oder eine R-Datendatei) 
- welcher Teil der Daten für Training und welcher Teil zu Testzwecken verwendet wird
- welche Algorithmen ausgeführt werden 
- die Auswahl der Steuerparameter für die Modelloptimierung:
    - Kreuzvalidierung 
    - Bootstrapping
    - Falten der Kreuzvalidierung
- Hyperparametersätze für jeden Algorithmus 

Die Anzahl der Algorithmen, die Anzahl der Falten für die Optimierung, die Hyperparameter und die Anzahl der Hyperparametersätze für Sweeping können auch in der YAML-Datei geändert werden, um die Modelle schnell ausführen zu können. Beispielsweise können sie mit einer geringeren Anzahl von CV-Falten und einer geringeren Anzahl von Parametersätzen ausgeführt werden. Sie können auch umfassender mit einer höheren Anzahl von CV-Falten oder einer höheren Anzahl von Parametersätzen ausgeführt werden, wenn dies notwendig ist.

Weitere Informationen finden Sie unter [Automatisiertes Modellierungs- und Berichterstellungs-Hilfsprogramm in TDSP Data Science-Hilfsprogrammen](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).

### <a name="model-management"></a>Modellverwaltung
Nachdem mehrere Modelle erstellt wurden, benötigen Sie in der Regel ein System zum Registrieren und Verwalten der Modelle. Normalerweise benötigen Sie eine Kombination aus Skripts oder APIs und einer Back-End-Datenbank oder einem Versionsverwaltungssystem. Für diese Verwaltungsaufgaben stehen Ihnen unter anderem die folgenden Optionen zur Verfügung:

1. [Azure Machine Learning-Modellverwaltungsdienst](../service/index.yml)
2. [ModelDB von MIT](https://mitdbg.github.io/modeldb/) 
3. [SQL Server als ein Modellverwaltungssystem](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

## 3. <a name='Deployment-3'></a> Bereitstellung

Bei einer Produktionsbereitstellung kann ein Modell eine aktive Rolle in einem Unternehmen spielen. Vorhersagen aus einem bereitgestellten Modell können für Geschäftsentscheidungen verwendet werden.

### <a name="production-platforms"></a>Produktionsplattformen
Es gibt verschiedene Ansätze und Plattformen für das Einführen von Modellen in die Produktion. Hier sind einige Optionen angegeben:


- [Modellimplementierung in Azure Machine Learning Service](../service/how-to-deploy-and-where.md)
- [Bereitstellung eines Modells in SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Vor der Bereitstellung muss sichergestellt werden, dass bei der Bewertung durch das Modell ausreichend geringe Latenzen für eine Verwendung in der Produktion auftreten.
>
>

Weitere Beispiele, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind in Form von exemplarischen Vorgehensweisen verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden.

> [!NOTE]
> Informationen zur Bereitstellung mit Azure Machine Learning Studio finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](../studio/publish-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B-Tests
Wenn sich mehrere Modelle in der Produktion befinden, kann es nützlich sein, [A/B-Tests](https://en.wikipedia.org/wiki/A/B_testing) zum Vergleichen der Leistung der Modelle durchzuführen. 

 
## <a name="next-steps"></a>Nächste Schritte

In [Nachverfolgen des Fortschritts von Data Science-Projekten](track-progress.md) wird gezeigt, wie ein Datenanalyst den Status eines Data Science-Projekts nachverfolgen kann.

Unter [Modellvorgang und CI-CD](ci-cd-flask.md) wird gezeigt, wie CI/CD mit entwickelten Modellen ausgeführt werden kann.


