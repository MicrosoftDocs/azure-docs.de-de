---
title: Includedatei
description: Includedatei
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/06/2019
ms.openlocfilehash: cf35651f7dd839e8792029851b9bfe278036624c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66397273"
---
Ein Experiment wird auf einem Computeziel ausgeführt, d. h. auf einer an Ihren Arbeitsbereich angefügten Computeressource.  Wenn Sie ein Computeziel erstellen, können Sie es für künftige Ausführungen wiederverwenden.

1. Wählen Sie unten **Run** (Ausführen) aus, um das Experiment auszuführen.

     ![Ausführen des Experiments](./media/aml-ui-create-training-compute/run-experiment.png)

1. Wenn das Dialogfeld **Setup Compute Targets** (Computeziele einrichten) angezeigt wird und Ihr Arbeitsbereich bereits eine Computeressource enthält, können Sie sie jetzt auswählen.  Klicken Sie andernfalls auf **Neu erstellen**.

    > [!NOTE]
    > Auf der grafischen Benutzeroberfläche können nur Experimente für Machine Learning Compute-Ziele ausgeführt werden. Andere Computeziele werden nicht angezeigt.

1. Geben Sie einen Namen für die Computeressource an.

1. Klicken Sie auf **Run** (Ausführen).

    ![Computeziele einrichten](./media/aml-ui-create-training-compute/set-compute.png)

    Die Computeressource wird nun erstellt. Zeigen Sie den Status in der rechten oberen Ecke des Experiments an. 

    > [!NOTE]
    > Die Erstellung einer Computeressource dauert etwa fünf Minuten. Nach der Erstellung der Ressource können Sie sie wiederverwenden und diese Wartezeit in künftigen Ausführungen vermeiden.
    >
    > Eine im Leerlauf befindliche Computeressource wird automatisch auf 0 Knoten skaliert, um Kosten zu sparen.  Wenn Sie sie nach einer Verzögerung erneut verwenden, müssen Sie unter Umständen etwa fünf Minuten warten, bis sie wieder hochskaliert wurde.
