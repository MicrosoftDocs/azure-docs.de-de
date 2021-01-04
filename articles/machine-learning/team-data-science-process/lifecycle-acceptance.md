---
title: Phase „Kundenakzeptanz“ des Team Data Science-Prozesslebenszyklus
description: Die Ziele, Aufgaben und Projektleistungen für die Kundenakzeptanzphase Ihrer Data Science-Projekte
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
ms.openlocfilehash: f2294ccb1d958b229a71e45bb502b8134d8d5c7f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305667"
---
# <a name="customer-acceptance-stage-of-the-team-data-science-process-lifecycle"></a>Phase „Kundenakzeptanz“ des Team Data Science-Prozesslebenszyklus

In diesem Artikel werden die Ziele, Aufgaben und Projektleistungen im Zusammenhang mit der Kundenakzeptanzphase des Team Data Science-Prozesses (TDSP) behandelt. Dieser Prozess bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können. Der Lebenszyklus beschreibt die wichtigsten Phasen, die Projekte typischerweise, oft iterativ, durchlaufen:

   1. **Geschäftliche Aspekte**
   2. **Datenerfassung und -auswertung**
   3. **Modellierung**
   4. **Bereitstellung**
   5. **Kundenakzeptanz**

Dies ist eine visuelle Darstellung des TDSP-Lebenszyklus: 

![TDSP-Lebenszyklus](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goal"></a>Zielsetzung
**Abschließen der Projektergebnisse:** Vergewissern Sie sich, dass die Pipeline, das Modell und die Bereitstellung in einer Produktionsumgebung die Anforderungen an die Kundenziele erfüllen.

## <a name="how-to-do-it"></a>Vorgehensweise
In dieser Phase werden zwei Hauptaufgaben durchgeführt:

   * **Systemüberprüfung** : Vergewissern Sie sich, dass das bereitgestellte Modell und die Pipeline die Kundenanforderungen erfüllen.
   * **Projektübergabe** : Übergeben Sie das Projekt an die Entität, die das System in der Produktion ausführen soll.

Der Kunde muss überprüfen, ob das System seine geschäftlichen Anforderungen erfüllt und ob die Fragen mit akzeptabler Genauigkeit beantwortet werden, damit das System in der Produktion zur Nutzung durch die Clientanwendung bereitgestellt werden kann. Die gesamte Dokumentation wird abgeschlossen und überprüft. Die Übergabe des Projekts an die Entität, die für den Betrieb zuständig ist, wird vollzogen. Diese Entität kann beispielsweise ein IT-Team bzw. Data Science-Team des Kunden oder ein Agent des Kunden sein, das bzw. der für die Ausführung des Systems in der Produktion zuständig ist. 

## <a name="artifacts"></a>Artefakte
Das Hauptartefakt, das in dieser letzten Phase produziert wird, ist **Exit report of the project for the customer** (Abschlussbericht des Projekts für den Kunden). Dieser technische Bericht enthält alle Details des Projekts, die für den Betrieb des Systems hilfreich sind. TDSP bietet dazu die Vorlage [Abschlussbericht](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Exit%20Report.md) an. Sie können die Vorlage ohne Änderungen verwenden oder sie an bestimmte Clientanforderungen anpassen. 


## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie Links zu jedem Schritt im Lebenszyklus des TDSP:

   1. [Geschäftliche Aspekte](lifecycle-business-understanding.md)
   2. [Datenerfassung und -auswertung](lifecycle-data.md)
   3. [Modellierung](lifecycle-modeling.md)
   4. [Bereitstellung](lifecycle-deployment.md)
   5. [Kundenakzeptanz](lifecycle-acceptance.md)

Wir bieten vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für bestimmte Szenarien gezeigt werden. Der Artikel mit [exemplarischen Vorgehensweisen](walkthroughs.md) enthält eine Liste der Szenarien mit Links und Kurzbeschreibungen. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools sowie Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

Beispiele für die Ausführung der Schritte in TDSPs mit Azure Machine Learning Studio finden Sie unter [Verwenden des TDSP mit Azure Machine Learning](./index.yml).