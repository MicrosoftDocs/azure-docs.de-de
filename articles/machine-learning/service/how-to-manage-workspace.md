---
title: Erstellen und Verwalten von Arbeitsbereichen
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie Azure Machine Learning Service-Arbeitsbereiche im Azure-Portal erstellen, anzeigen und löschen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "66016527"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Erstellen und Verwalten von Azure Machine Learning Service-Arbeitsbereichen

In diesem Artikel wird erläutert, wie Sie [**Azure Machine Learning Service-Arbeitsbereiche**](concept-workspace.md) im Azure-Portal für [Azure Machine Learning Service](overview-what-is-azure-ml.md) erstellen, anzeigen und löschen.  Sie können Arbeitsbereiche auch über die [CLI](reference-azure-machine-learning-cli.md) oder mit [Python-Code](https://aka.ms/aml-sdk) oder [über die VS Code-Erweiterung](how-to-vscode-tools.md#get-started-with-azure-machine-learning) erstellen und löschen.

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Zum Erstellen eines Arbeitsbereichs benötigen Sie ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Anzeigen eines Arbeitsbereichs

1. Wählen Sie links oben im Portal die Option **Alle Dienste** aus.

1. Geben Sie **Machine Learning Service** in das Filterfeld **Alle Dienste** ein.  

1. Wählen Sie **Machine Learning Service-Arbeitsbereiche** aus.

   ![Suchen nach dem Azure Machine Learning Service-Arbeitsbereich](media/how-to-manage-workspace/all-services.png)

1. Durchsuchen Sie die Liste der gefundenen Arbeitsbereiche. Sie können basierend auf Abonnement, Ressourcengruppen und Standorten filtern.  

1. Wählen Sie einen Arbeitsbereich aus, um seine Eigenschaften anzuzeigen.
   ![Arbeitsbereichseigenschaften](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Löschen eines Arbeitsbereichs

Verwenden Sie die Schaltfläche „Löschen“ oben in dem Arbeitsbereich, den Sie löschen möchten.

  ![Schaltfläche „Löschen“](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Schließen Sie das gesamte Tutorial ab, um zu erfahren, wie Sie einen Arbeitsbereich zum Erstellen, Trainieren und Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst verwenden.

> [!div class="nextstepaction"]
> [Tutorial: Trainieren von Modellen](tutorial-train-models-with-aml.md)
