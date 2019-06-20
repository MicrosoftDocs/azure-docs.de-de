---
title: 'Apply Transformation: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Apply Transformation“ (Anwenden einer Transformation) in Azure Machine Learning Service einen Eingabedatensatz basierend auf einer zuvor berechneten Transformation ändern können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027712"
---
# <a name="apply-transformation-module"></a>Apply Transformation-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um einen Eingabedatensatz basierend auf einer zuvor berechneten Transformation zu ändern.  
  
Wenn Sie beispielsweise Z-Ergebnisse zum Normalisieren Ihrer Trainingsdaten mit dem Modul **Normalize Data** (Normalisieren Sie Daten) verwendet haben, möchten Sie auch den Z-Ergebniswert verwenden, der für das Training während der Bewertungsphase berechnet wurde. In Azure Machine Learning können Sie die Normalisierungsmethode als Transformation speichern und dann **Apply Transformation** (Anwenden einer Transformation) verwenden, um das Z-Ergebnis vor der Bewertung auf die Eingabedaten anzuwenden.
  
Azure Machine Learning unterstützt das Erstellen und anschließende Anwenden vieler verschiedener Arten benutzerdefinierter Transformationen. Angenommen, Sie möchten Transformationen speichern und anschließend für die folgenden Zwecke wiederverwenden:  
  
- Entfernen oder Ersetzen fehlender Werte mit **Clean Missing Data** (Bereinigen fehlender Daten)
- Normalisieren von Daten mithilfe von **Normalize Data** (Normalisieren von Daten)
  

## <a name="how-to-use-apply-transformation"></a>Verwenden des Moduls „Apply Transformation“  
  
1. Fügen Sie das Modul **Apply Transformation** (Transformation anwenden) Ihrem Experiment hinzu. Sie finden dieses Modul unter **Machine Learning** in der Kategorie **Score**. 
  
2. Suchen Sie eine vorhandene Transformation, die als Eingabe verwendet werden soll.  Zuvor gespeicherte Transformationen finden Sie im linken Navigationsbereich in der Gruppe **Transforms**.  
  
   
  
3. Stellen Sie eine Verbindung mit dem Dataset her, das Sie transformieren möchten. Der Datensatz muss genau das gleiche Schema (Anzahl der Spalten, Spaltennamen, Datentypen) wie der Datensatz haben, für den die Transformation zuvor vorgesehen war.  
  
4. Es müssen keine weiteren Parameter festgelegt werden, da alle Anpassungen bei der Definition der Transformation vorgenommen werden.  
  
5. Um eine Transformation auf das neue Dataset anzuwenden, führen Sie das Experiment durch.  

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 