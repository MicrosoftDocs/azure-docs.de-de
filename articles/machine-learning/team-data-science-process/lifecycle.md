---
title: Lebenszyklus des Team Data Science-Prozesses
description: Der Team Data Science-Prozess (TDSP) bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können.
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
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305593"
---
# <a name="the-team-data-science-process-lifecycle"></a>Lebenszyklus des Team Data Science-Prozesses

Der Team Data Science-Prozess (TDSP) bietet einen empfohlenen Lebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können. Der Lebenszyklus beschreibt sämtliche Schritte, denen erfolgreiche Projekte folgen. Wenn Sie einen anderen Data Science-Lebenszyklus verwenden, z.B. [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) (Cross Industry Standard Process for Data Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) (Knowledge Discovery in Databases) oder einen benutzerdefinierten Prozess Ihrer Organisation, können Sie trotzdem den aufgabenbasierten TDSP verwenden. 

Dieser Lebenszyklus wurde für Data Science-Projekte entworfen, die im Lieferumfang von intelligenten Anwendungen enthalten sein sollen. Mit diesen Anwendungen werden Modelle vom Typ „Machine Learning“ oder „Künstliche Intelligenz“ für Predictive Analytics bereitgestellt. Explorative Data Science-Projekte und improvisierte Analyseprojekte können von diesem Prozess ebenfalls profitieren. Für solche Projekte sind aber möglicherweise einige der hier beschriebenen Schritte nicht erforderlich. 

## <a name="five-lifecycle-stages"></a>Fünf Lebenszyklusphasen

Der TDSP-Lebenszyklus besteht aus fünf Hauptphasen, die iterativ ausgeführt werden. Diese Phasen umfassen:

   1. [Geschäftliche Aspekte](lifecycle-business-understanding.md)
   2. [Datenerfassung und -auswertung](lifecycle-data.md)
   3. [Modellierung](lifecycle-modeling.md)
   4. [Bereitstellung](lifecycle-deployment.md)
   5. [Kundenakzeptanz](lifecycle-acceptance.md)

Dies ist eine visuelle Darstellung des TDSP-Lebenszyklus: 

![TDSP-Lebenszyklus](./media/lifecycle/tdsp-lifecycle2.png) 


Der TDSP-Lebenszyklus wird als Sequenz der durchlaufenen Schritte modelliert, die als Anleitung für die erforderlichen Aufgaben zur Verwendung von Vorhersagemodellen dienen. Sie stellen die Vorhersagemodelle in der Produktionsumgebung, in der Sie die intelligenten Anwendungen erstellen möchten, bereit. Das Ziel dieses Prozesslebenszyklus besteht darin, ein Data Science-Projekt auf dem Weg zu einem eindeutigen Vernetzungsziel voranzubringen. Data Science ist eine Aufgabe bei Forschung und Entwicklung. Die Fähigkeit der Kommunikation von Aufgaben an das Team und Ihre Kunden mit einem genau definierten Satz von Artefakten und standardisierten Vorlagen hilft dabei, Missverständnisse zu vermeiden. Mithilfe dieser Vorlagen wird auch die Wahrscheinlichkeit eines erfolgreichen Abschlusses bei komplexen Data Science-Projekten gesteigert.

Für jede Phase werden die folgenden Informationen bereitgestellt:

   * **Ziele** : Die einzelnen Ziele
   * **Vorgehensweise** : Eine Übersicht über die spezifischen Aufgaben und Anleitungen für diese.
   * **Artefakte** : Aufstellung der Endergebnisse und Unterstützung für ihre Erreichung.

## <a name="next-steps"></a>Nächste Schritte

Vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für bestimmte Szenarien gezeigt werden, sind ebenfalls verfügbar. Der Artikel mit [exemplarischen Vorgehensweisen](walkthroughs.md) enthält eine Liste der Szenarien mit Links und Kurzbeschreibungen. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools sowie Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

Beispiele für die Ausführung der Schritte in TDSPs mit Azure Machine Learning Studio finden Sie unter [Verwenden des TDSP mit Azure Machine Learning](./index.yml).