---
title: Bereitstellungsphase des Team Data Science-Prozesslebenszyklus
description: Die Ziele, Aufgaben und Projektleistungen für die Bereitstellungsphase Ihrer Data Science-Projekte
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c3bf8e5f81ae7bf35ff34039fa1e81c9fd4a406b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324552"
---
# <a name="deployment-stage-of-the-team-data-science-process-lifecycle"></a>Bereitstellungsphase des Team Data Science-Prozesslebenszyklus

In diesem Artikel werden die Ziele, Aufgaben und Projektleistungen im Zusammenhang mit der Bereitstellung des Team Data Science-Prozesses (TDSP) behandelt. Dieser Prozess bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können. Der Lebenszyklus beschreibt die wichtigsten Phasen, die Projekte typischerweise, oft iterativ, durchlaufen:

   1. **Geschäftliche Aspekte**
   2. **Datenerfassung und -auswertung**
   3. **Modellierung**
   4. **Bereitstellung**
   5. **Kundenakzeptanz**

Dies ist eine visuelle Darstellung des TDSP-Lebenszyklus: 

![TDSP-Lebenszyklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Zielsetzung
Stellen Sie Modelle mit einer Datenpipeline in einer Produktionsumgebung oder einer produktionsähnlichen Umgebung bereit, um die endgültige Benutzerakzeptanz zu testen. 

## <a name="how-to-do-it"></a>Vorgehensweise
In dieser Phase geht es um die folgende Hauptaufgabe:

**Operationalisieren des Modells** : Stellen Sie das Modell und die Pipeline in einer Produktionsumgebung oder produktionsähnlichen Umgebung zur Nutzung der Anwendung bereit.

### <a name="operationalize-a-model"></a>Operationalisieren eines Modells
Nachdem wir eine Gruppe von Modellen mit adäquater Leistung haben, können diese für die Nutzung durch andere Anwendungen operationalisiert werden. Abhängig von den Geschäftsanforderungen erfolgen Vorhersagen entweder in Echtzeit oder auf Batchbasis. Um Modelle bereitzustellen, machen Sie diese mit einer offenen API-Schnittstelle verfügbar. Die Schnittstelle ermöglicht die einfache Verwendung des Modells durch unterschiedliche Anwendungen wie:

   * Onlinewebsites
   * Kalkulationstabellen 
   * Dashboards
   * Branchenanwendungen 
   * Back-End-Anwendungen 

Beispiele für die Operationalisierung von Modellen mit einem Azure Machine Learning-Webdienst finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](../classic/deploy-a-machine-learning-web-service.md). Es gilt als bewährte Methode, in das bereitgestellte Produktionsmodell und die bereitgestellte Datenpipeline Telemetriedaten und Überwachung zu integrieren. Diese Vorgehensweise erleichtert nachfolgende Systemstatus-Berichtserstellung und Problembehandlung.  

## <a name="artifacts"></a>Artefakte

* Ein Statusdashboard mit Systemintegrität und wichtigen Metriken
* Ein finaler Modellierungsbericht mit Bereitstellungsdetails
* Ein Dokument mit der endgültigen Lösungsarchitektur


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie Links zu jedem Schritt im Lebenszyklus des TDSP:

   1. [Geschäftliche Aspekte](lifecycle-business-understanding.md)
   2. [Datenerfassung und -auswertung](lifecycle-data.md)
   3. [Modellierung](lifecycle-modeling.md)
   4. [Bereitstellung](lifecycle-deployment.md)
   5. [Kundenakzeptanz](lifecycle-acceptance.md)

Wir bieten vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für bestimmte Szenarien gezeigt werden. Der Artikel mit [exemplarischen Vorgehensweisen](walkthroughs.md) enthält eine Liste der Szenarien mit Links und Kurzbeschreibungen. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools sowie Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

Beispiele für die Ausführung der Schritte in TDSPs mit Azure Machine Learning Studio finden Sie unter [Verwenden des TDSP mit Azure Machine Learning](./index.yml).