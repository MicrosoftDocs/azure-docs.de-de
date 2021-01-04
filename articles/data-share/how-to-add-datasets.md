---
title: Hinzufügen von Datasets zu einer vorhandenen Azure Data Share-Instanz
description: In diesem Artikel wird erläutert, wie Sie einer vorhandenen Datenfreigabe in Azure Data Share Datasets hinzufügen und für dieselben Empfänger freigeben.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92910541"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Hinzufügen von Datasets zu einer vorhandenen Datenfreigabe in Azure Data Share

In diesem Artikel wird erläutert, wie Sie einer vorhandenen Datenfreigabe mithilfe von Azure Data Share Datasets hinzufügen. Auf diese Weise können Sie mehr Daten für dieselben Empfänger freigeben, ohne eine neue Freigabe zu erstellen.

Informationen zum Hinzufügen von Datasets beim Erstellen einer Freigabe finden Sie im Tutorial [Freigeben von Daten](share-your-data.md).

## <a name="navigate-to-a-sent-data-share"></a>Navigation zu einer gesendeten Datenfreigabe

Navigieren Sie in Azure Data Share zur gesendeten Freigabe, und wählen Sie die Registerkarte **Datasets** aus. Klicken Sie auf die Schaltfläche **+ Datasets hinzufügen** , um weitere Datasets hinzuzufügen.

![Screenshot mit ausgewählter Option „Datasets hinzufügen“.](./media/how-to/how-to-add-datasets/add-datasets.png)

Wählen Sie im Panel auf der rechten Seite den Datasettyp aus, den Sie hinzufügen möchten, und klicken Sie dann auf **Weiter**. Wählen Sie das Abonnement und die Ressourcengruppe der Daten aus, die Sie hinzufügen möchten. Suchen Sie mithilfe der nach unten weisenden Pfeile nach den Daten, die hinzugefügt werden sollen, und aktivieren Sie dann das Feld neben den Daten.

![Screenshot des Bereichs „Blob Storage hinzufügen“, in dem Sie Daten auswählen können.](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Sobald Sie auf **Datasets hinzufügen** klicken, werden die Datasets zu Ihrer Freigabe hinzugefügt. Hinweis: Ihre Consumer müssen eine Momentaufnahme auslösen, damit ihnen die neuen Datasets angezeigt werden. Wenn Einstellungen für Momentaufnahmen konfiguriert sind, sehen die Consumer die neuen Datasets, sobald die nächste geplante Momentaufnahme abgeschlossen wurde. Sind keine Einstellungen für Momentaufnahmen konfiguriert, muss der Consumer manuell eine vollständige oder inkrementelle Kopie der Daten auslösen, um die Aktualisierungen zu erhalten. Weitere Informationen zu Momentaufnahmen finden Sie unter [Momentaufnahmen](terminology.md).

## <a name="next-steps"></a>Nächste Schritte
Informationen zum [Hinzufügen von Empfängern zu einer vorhandenen Datenfreigabe](how-to-add-recipients.md)