---
title: Was ist der Team Data Science-Prozess (TDSP)?
description: Bietet eine Data Science-Methodik zur Bereitstellung von Predictive Analytics-Lösungen und intelligenten Anwendungen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4a341d33c957af8fe7423bda80f1d34a7f59cdd6
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94737998"
---
# <a name="what-is-the-team-data-science-process"></a>Was ist der Team Data Science-Prozess (TDSP)?

Der Team Data Science-Prozess (TDSP) ist eine flexible, iterative Data Science-Methodik zur effizienten Bereitstellung von Predictive Analytics-Lösungen und intelligenten Anwendungen. Der TDSP kann die Zusammenarbeit und das Lernen im Team verbessern, indem er Vorschläge zur optimalen Zusammenarbeit von Teamrollen macht. Der TDSP umfasst Best Practices und Strukturen von Microsoft und anderen Branchenführern, die die erfolgreiche Implementierung von Data Science-Initiativen erleichtern. Ziel ist die Unterstützung von Unternehmen bei der umfassenden Nutzung ihrer Analyseprogramme.

Dieser Artikel bietet eine Übersicht über den TDSP und seine Hauptkomponenten. Hier finden Sie eine allgemeine Beschreibung des Prozesses, der mit verschiedenen Tools implementiert werden kann. Eine ausführlichere Beschreibung der Projektaufgaben und Rollen im Lebenszyklus des Prozesses erfolgt in weiteren verknüpften Themen. Zudem wird eine Anleitung zum Implementieren des TDSP mithilfe bestimmter Microsoft-Tools sowie die Infrastruktur, die wir zur Implementierung des TDSP in unseren Teams verwenden, bereitgestellt.

## <a name="key-components-of-the-tdsp"></a>Hauptkomponenten des TDSP

TDSP umfasst die folgenden Hauptkomponenten:

- Eine Definition des **Data Science-Lebenszyklus**
- Eine **standardisierte Projektstruktur**
- **Infrastruktur und Ressourcen**, die für Data Science-Projekte empfohlen werden
- **Tools und Hilfsprogramme**, die für die Projektausführung empfohlen werden


## <a name="data-science-lifecycle"></a>Data Science-Lebenszyklus

Der Team Data Science-Prozess (TDSP) umfasst einen Lebenszyklus zum Strukturieren der Entwicklung Ihrer Data Science-Projekte. Der Lebenszyklus beschreibt sämtliche Schritte für erfolgreiche Projekte.

Wenn Sie einen anderen Data Science-Lebenszyklus verwenden (beispielsweise [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) oder einen eigenen benutzerdefinierten Prozess Ihrer Organisation), können Sie den aufgabenbasierten TDSP auch im Kontext dieser Entwicklungslebenszyklen verwenden. Auf allgemeiner Ebene haben diese verschiedenen Methodiken viel gemeinsam. 

Dieser Lebenszyklus wurde für Data Science-Projekte entworfen, die im Lieferumfang von intelligenten Anwendungen enthalten sind. Mit diesen Anwendungen werden Modelle vom Typ „Machine Learning“ oder „Künstliche Intelligenz“ für Predictive Analytics bereitgestellt. Explorative Data Science-Projekte oder improvisierte Analyseprojekte können von diesem Prozess ebenfalls profitieren. Aber in diesen Fällen sind einige der beschriebenen Schritte unter Umständen nicht erforderlich.    

Der Lebenszyklus beschreibt die wichtigsten Phasen, die Projekte typischerweise, oft iterativ, durchlaufen:

* **Geschäftliche Aspekte**
* **Datenerfassung und -auswertung**
* **Modellierung**
* **Bereitstellung**

Hier ist der **Team Data Science-Prozesslebenszyklus** grafisch dargestellt. 

![Diagramm: Data Science-Lebenszyklus, einschließlich geschäftlicher Aspekte, Datenerfassung/-auswertung, Modellierung und Bereitstellung](./media/overview/tdsp-lifecycle2.png) 

Die Ziele, Aufgaben und Dokumentationsartefakte für die einzelnen Phasen des Lebenszyklus im TDSP werden im Thema [Team Data Science-Prozesslebenszyklus](lifecycle.md) beschrieben. Diese Aufgaben und Artefakte sind Projektrollen zugeordnet:

- Lösungsarchitekt
- Projektmanager
- Datentechniker
- Data Scientist
- Anwendungsentwickler
- Projektleiter 

Im folgenden Diagramm ist eine Rasteransicht der Aufgaben (blau) und Artefakte (grün) dargestellt, die den einzelnen Phasen des Lebenszyklus (auf der horizontalen Achse) für diese Rollen (auf der vertikalen Achse) zugeordnet sind. 

[![TDSP-roles-and-tasks](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardisierte Projektstruktur

Wenn in allen Projekten die Verzeichnisstruktur gleich ist und Vorlagen für Projektdokumente verwendet werden, können Teammitglieder Informationen zu ihren Projekten mühelos finden. Der gesamte Code und alle Dokumente werden im Hinblick auf die Zusammenarbeit im Team in einem Versionskontrollsystem, z.B. Git, TFS oder Subversion, gespeichert. Die Erfassung von Aufgaben und Features in einem flexiblen Projektnachverfolgungssystem wie Jira, Rally und Azure DevOps ermöglicht die detaillierte Nachverfolgung des Codes für einzelne Funktionen. Durch eine solche Nachverfolgung können Teams bessere Kostenschätzungen erreichen. Der TDSP empfiehlt zur Versionsverwaltung, Informationssicherheit und Zusammenarbeit die Erstellung eines separaten Repositorys für jedes Projekt im Versionskontrollsystem. Die standardisierte Struktur für alle Projekte trägt zum Aufbau des institutionellen Wissens in der gesamten Organisation bei.

Wir stellen Vorlagen für die Ordnerstruktur und erforderliche Dokumente in Standardspeicherorten bereit. In dieser Ordnerstruktur werden die Dateien organisiert, die Code zur Datenuntersuchung und Featureextraktion enthalten und in denen Modelliterationen erfasst werden. Anhand dieser Vorlagen können Teammitglieder einfacher die Arbeit von anderen Mitgliedern nachvollziehen und neue Mitglieder zu Teams hinzufügen. Die Dokumentvorlagen im Markdown-Format lassen sich einfach anzeigen und aktualisieren. Verwenden Sie Vorlagen zum Bereitstellen von Prüflisten mit den wichtigsten Fragen für jedes Projekt, um sicherzustellen, dass das Problem klar definiert ist und die Ergebnisse den erwarteten Qualitätsanforderungen entsprechen. Beispiele:

- Projektauftrag zum Dokumentieren des Geschäftsproblems und des Projektumfangs
- Datenberichte zum Dokumentieren der Struktur und Statistik der Rohdaten
- Modellberichte zum Dokumentieren der abgeleiteten Features
- Modellleistungsmetriken, z.B. ROC-Kurven oder MSE


[![TDSP-directories](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Die Verzeichnisstruktur kann von[GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate) geklont werden.

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktur und Ressourcen für Data Science-Projekte  

Der TDSP umfasst Empfehlungen zum Verwalten der freigegebenen Analyse- und Speicherinfrastruktur, beispielsweise:

- Clouddateisysteme zum Speichern von Datasets 
- databases
- Big Data-Cluster (SQL oder Spark) 
- Machine Learning-Dienst 

Die Analyse- und Speicherinfrastruktur, in der unverarbeitete und verarbeitete Datasets gespeichert werden, kann sich in der Cloud oder in der lokalen Umgebung befinden. Diese Infrastruktur ermöglicht reproduzierbare Analysen. Sie verhindert außerdem die Duplizierung, die zu Inkonsistenzen und unnötigen Infrastrukturkosten führen kann. Tools werden bereitgestellt, um freigegebene Ressourcen bereitzustellen und nachzuverfolgen sowie jedem Teammitglied zu ermöglichen, eine sichere Verbindung mit diesen Ressourcen herzustellen. Es empfiehlt sich außerdem, dass Projektmitglieder eine einheitliche Computeumgebung erstellen. Verschiedene Teammitglieder können dann Experimente replizieren und überprüfen.

Es folgt ein Beispiel für ein Team, das an mehreren Projekten arbeitet und verschiedene freigegebene Analyseinfrastrukturkomponenten in der Cloud nutzt.

[![TDSP-infrastructure](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Tools und Hilfsprogramme für die Projektausführung

Die Einführung von Prozessen stellt in den meisten Organisationen eine große Herausforderung dar. Durch bereitgestellte Tools zur Implementierung des Data Science-Prozesses und Data Science-Lebenszyklus lassen sich die Hürden reduzieren und die Konsistenz der Einführung erhöhen. Der TDSP umfasst ein anfängliches Set von Tools und Skripts für den Schnelleinstieg in die Einführung des TDSP in einem Team. Darüber hinaus können einige der allgemeinen Aufgaben im Data Science-Lebenszyklus, z.B. die Datenuntersuchung und Basismodellierung, automatisiert werden. Es ist eine klar definierte Struktur für Einzelpersonen festgelegt, die freigegebene Tools und Hilfsprogramme im freigegebenen Coderepository ihres Teams hinzufügen. Diese Ressourcen können dann in anderen Projekten des Teams oder der Organisation genutzt werden.  Microsoft bietet in [Azure Machine Learning](../index.yml) umfassende Tools, die sowohl Open-Source-Tools (Python, R, ONNX und allgemeine Deep Learning-Frameworks) als auch Microsoft-Tools (AutoML) unterstützen.


## <a name="next-steps"></a>Nächste Schritte

[Team Data Science-Prozess: Rollen und Aufgaben](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md): Beschreibung der wichtigsten Mitarbeiterrollen und der zugeordneten Aufgaben für ein Data Science-Team, das eine Standardisierung entsprechend diesem Prozess durchführt. 
