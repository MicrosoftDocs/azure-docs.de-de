---
title: include file
description: include file
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178508"
---
>[!IMPORTANT]
>Sie können die von Ihnen bei der Vorbereitung erstellten Ressourcen auch in anderen Tutorials und Anleitungen für Azure Machine Learning Service verwenden.


### <a name="delete-everything"></a>Alles löschen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie die gesamte Ressourcengruppe, damit Ihnen keine Kosten entstehen.

1. Wählen Sie im Azure-Portal links im Fenster **Ressourcengruppen** aus.
 
   ![Löschen der Ressourcengruppe im Azure-Portal](./media/aml-ui-cleanup/delete-resources.png)

1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.

1. Wählen Sie auf der rechten Seite des Fensters die Schaltfläche mit den Auslassungspunkten ( **...** ) aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

Durch das Löschen einer Ressourcengruppe werden auch alle auf der grafischen Benutzeroberfläche erstellten Ressourcen gelöscht.  

### <a name="delete-only-the-compute-target"></a>Ausschließliches Löschen des Computeziels

Das hier erstellte Computeziel wird *automatisch auf null Knoten skaliert*, wenn es nicht verwendet wird. Dadurch werden Kosten minimiert. Wenn Sie das Computeziel löschen möchten, führen Sie die folgenden Schritte aus:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren Arbeitsbereich.

    ![Löschen des Computeziels](./media/aml-ui-cleanup/delete-compute-target.png)

1. Wählen Sie im Abschnitt **Compute** Ihres Arbeitsbereichs die Ressource aus.

1. Klicken Sie auf **Löschen**.

### <a name="delete-individual-assets"></a>Löschen einzelner Objekte

Löschen Sie einzelne Ressourcen auf der grafischen Benutzeroberfläche, auf der Sie das Experiment erstellt haben. Wählen Sie dazu die gewünschten Ressourcen und anschließend die Schaltfläche **Löschen** aus.

![Löschen von Experimenten](./media/aml-ui-cleanup/delete-experiment.png)
