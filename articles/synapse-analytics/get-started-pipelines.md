---
title: 'Tutorial: Erste Schritte zum Integrieren mit Pipelines'
description: In diesem Tutorial erfahren Sie, wie Pipelines und Aktivitäten mithilfe von Synapse Studio integriert werden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744927"
---
# <a name="integrate-with-pipelines"></a>Integrieren mit Pipelines

In diesem Tutorial erfahren Sie, wie Pipelines und Aktivitäten mithilfe von Synapse Studio integriert werden. 

## <a name="overview"></a>Übersicht

Sie können eine Vielzahl verschiedener Aufgaben in Azure Synapse integrieren.

1. Navigieren Sie in Synapse Studio zum Hub **Integrieren**.
1. Wählen Sie **+**  > **Pipeline** aus, um eine neue Pipeline zu erstellen.
1. Navigieren Sie zum Hub **Entwickeln** , und wählen Sie eins der zuvor erstellten Notebooks aus.
1. Ziehen Sie dieses Notebook in die Pipeline. ( **Hinweis** : Schritt zum Importieren von Modulen im Notebook, wie in [diesem Dokument](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) angegeben. Erforderlich bei der Ausführung über die Pipeline.)
1. Wählen Sie in der Pipeline **Trigger hinzufügen** > **Neu/Bearbeiten** aus.
1. Wählen Sie unter **Choose trigger** (Trigger auswählen) die Option **Neu** aus, und legen Sie **Wiederholung** auf „1 Stunde“ fest.
1. Klicken Sie auf **OK**. 
1. Wählen Sie **Alle veröffentlichen**.
1. Wählen Sie zum sofortigen Ausführen der Pipeline, ohne den Ablauf der Stunde abzuwarten, die Option **Trigger hinzufügen** > **Jetzt auslösen** aus.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Visualisieren von Daten mit Power BI](get-started-visualize-power-bi.md)
                                 
