---
title: 'Apply Transformation: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Apply Transformation in Azure Machine Learning ein Eingabedataset basierend auf einer zuvor berechneten Transformation ändern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003904"
---
# <a name="apply-transformation-module"></a>Apply Transformation-Modul

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um einen Eingabedatensatz basierend auf einer zuvor berechneten Transformation zu ändern. Dieses Modul ist erforderlich, wenn Sie Transformationen in Rückschlusspipelines aktualisieren müssen.

Wenn Sie beispielsweise Z-Ergebnisse zum Normalisieren Ihrer Trainingsdaten mit dem Modul **Normalize Data** (Normalisieren Sie Daten) verwendet haben, möchten Sie auch den Z-Ergebniswert verwenden, der für das Training während der Bewertungsphase berechnet wurde. In Azure Machine Learning können Sie die Normalisierungsmethode als Transformation speichern und dann **Apply Transformation** (Anwenden einer Transformation) verwenden, um das Z-Ergebnis vor der Bewertung auf die Eingabedaten anzuwenden.

## <a name="how-to-save-transformations"></a>Speichern von Transformationen

Mit dem Designer können Sie Datentransformationen als **Datasets** speichern, damit Sie sie in anderen Pipelines verwenden können.

1. Wählen Sie ein Datentransformationsmodul aus, das erfolgreich ausgeführt wurde.

1. Wählen Sie die Registerkarte **Ausgaben + Protokolle** aus.

1. Suchen Sie die Transformationsausgabe, und klicken Sie auf **Register dataset** (Dataset registrieren), um sie als Modul in der Kategorie **Datasets** der Modulpalette zu speichern.

## <a name="how-to-use-apply-transformation"></a>Verwenden des Moduls „Apply Transformation“  
  
1. Fügen Sie das Modul **Apply Transformation** (Transformation anwenden) Ihrer Pipeline hinzu. Sie finden dieses Modul im Abschnitt zur **Modellbewertung und -auswertung** der Modulpalette. 
  
1. Suchen Sie die gespeicherte Transformation, die Sie verwenden möchten, unter **Datasets** in der Modulpalette.

1. Verbinden Sie die Ausgabe der gespeicherten Transformation mit dem linken Eingangsport des Moduls **Apply Transformation** (Transformation anwenden).

    Der Datensatz muss genau das gleiche Schema (Anzahl der Spalten, Spaltennamen, Datentypen) wie der Datensatz haben, für den die Transformation zuvor vorgesehen war.  
  
1. Verbinden Sie die Datasetausgabe des gewünschten Moduls mit dem rechten Eingangsport des Moduls **Apply Transformation** (Transformation anwenden).
  
1. Um eine Transformation auf das neue Dataset anzuwenden, übermitteln Sie die Pipeline.  

> [!IMPORTANT]
> Um sicherzustellen, dass die aktualisierte Transformation in Trainingspipelines auch für Rückschlusspipelines möglich ist, befolgen Sie die nachstehenden Schritte jedes Mal, wenn in der Trainingspipeline eine Transformation aktualisiert wird:
> 1. Registrieren Sie in der Trainingspipeline die Ausgabe von [Select Columns Transform](select-columns-transform.md) als Dataset.
> ![Registrieren eines Datasets einer Modulausgabe](media/module/select-columns-transform-register-dataset.png)
> 1. Entfernen Sie in der Rückschlusspipeline das **TD-** Modul, und ersetzen Sie es durch das im vorherigen Schritt registrierte Dataset.
> ![Ersetzen des TD-Moduls](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 