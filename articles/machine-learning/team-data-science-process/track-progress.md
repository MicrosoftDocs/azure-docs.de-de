---
title: Nachverfolgen des Fortschritts von Data Science-Projekten – Team Data Science-Prozess
description: Wie Data Science-Gruppenmanager, Teamleiter und Projektleiter den Status eines Data Science-Projekts nachverfolgen können.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60252578"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>Nachverfolgen des Fortschritts von Data Science-Projekten

Data Science-Gruppenmanager, Teamleiter und Projektleiter müssen den Fortschritt ihrer Projekte nachverfolgen: welche Arbeit von welchen Personen ausgeführt wurde und was in den Aufgabenlisten verbleibt. 

## <a name="azure-devops-dashboards"></a>Azure DevOps-Dashboards
Bei Verwendung von Azure DevOps können Sie zum Nachverfolgen der Aktivitäten und der einem bestimmten Agile-Projekt zugeordneten Arbeitselemente Dashboards erstellen. 

Weitere Informationen zum Erstellen und Anpassen von Dashboards und Widgets in Azure DevOps finden Sie in den folgenden Anweisungen:

- [Hinzufügen und Verwalten von Dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Hinzufügen von Widgets zu einem Dashboard](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Beispieldashboard

Dies ist ein einfaches Beispieldashboard, das zum Nachverfolgen der Sprintaktivitäten in einem Agile Data Science-Projekt sowie der Anzahl von Commits in den zugeordneten Repositorys erstellt wird. Im Bereich **links oben** wird Folgendes angezeigt:

- Der Countdown des aktuellen Sprints 
- Die Anzahl von Commits für jedes Repository in den letzten sieben Tagen
- Das Arbeitselement für bestimmte Benutzer 

Die verbleibenden Elemente zeigen das kumulative Flussdiagramm (CFD), den Burndown und den Burnup für ein Projekt:

- **Links unten:**  CFD der Menge an Arbeit in einem bestimmten Zustand mit genehmigten Elementen in Grau, committeten in Blau und erledigten in Grün
- **Rechts oben:** Burndowndiagramm der Arbeit, die noch abgeschlossen werden muss, gegenüber der verbleibenden Zeit
- **Rechts unten:** Burnupdiagramm der abgeschlossenen Arbeit gegenüber der Gesamtmenge der Arbeit

![Dashboard](./media/track-progress/dashboard.png)

Eine Beschreibung der Erstellung dieser Diagramme finden Sie in den Schnellstarts und Tutorials unter [Dashboards](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>Nächste Schritte

Exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 
