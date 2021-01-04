---
title: Erstellen eines Zuordnungsdatenflusses
description: Erfahren Sie, wie Sie einen Datenfluss mit Azure Data Factory Mapping Data Flow erstellen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: eaf36cc2690b3c0f8922c05432b3197b4ff30d9a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026053"
---
# <a name="create-azure-data-factory-data-flow"></a>Erstellen eines Azure Data Factory-Datenflusses

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mit Mapping Data Flow in ADF können Sie Daten nach Maß transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag im Datenfluss-Designer entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie dann Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen.

Erstellen Sie zuerst im Azure-Portal eine neue Data Factory (V2). Klicken Sie nach dem Erstellen der neuen Factory auf die Kachel „Erstellen und überwachen“, um die Data Factory-Benutzeroberfläche zu starten.

![Screenshot: Bereich „Neue Data Factory“, in dem als Version „V2“ ausgewählt ist](media/data-flow/v2portal.png "Erstellen eines Datenflusses")

Sobald Sie sich auf der Data Factory-Benutzeroberfläche befinden, können Sie die Beispieldatenflüsse verwenden. Die Beispiele sind im ADF-Vorlagenkatalog verfügbar. Erstellen Sie in ADF eine „Pipeline aus Vorlage“, und wählen Sie im Vorlagenkatalog die Kategorie „Datenfluss“ aus.

![Screenshot: Registerkarte „Datenfluss“, auf der „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren) ausgewählt ist](media/data-flow/template.png "Erstellen eines Datenflusses")

Sie werden zur Eingabe Ihrer Azure Blob Storage-Kontoinformationen aufgefordert.

![Screenshot: Bereich „Transform data using data flow“ (Daten mithilfe des Datenflusses transformieren), in dem Sie Benutzereingaben eingeben können](media/data-flow/template2.png "Erstellen eines Datenflusses 2")

[Die für diese Beispiele verwendeten Daten finden Sie hier](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Laden Sie die Beispieldaten herunter, und speichern Sie die Dateien in Ihren Azure Blob Storage-Konten, damit Sie die Beispiele ausführen können.

## <a name="create-new-data-flow"></a>Erstellen des neuen Datenflusses

Verwenden Sie auf der ADF-Benutzeroberfläche die Schaltfläche „Ressource erstellen“ mit dem Pluszeichen (+), um Datenflüsse zu erstellen.

![Screenshot: Option „Datenfluss“, die im Menü „Factory Resources“ (Factoryressourcen) ausgewählt ist](media/data-flow/newresource.png "Neue Ressource")

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie die Erstellung Ihrer Datentransformation mit einer [Quelltransformation](data-flow-source.md).
