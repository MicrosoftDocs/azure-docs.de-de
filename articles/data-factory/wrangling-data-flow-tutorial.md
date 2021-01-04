---
title: Erste Schritte mit Wranglingdatenflüssen in Azure Data Factory
description: Tutorial zum Vorbereiten von Daten in Azure Data Factory mithilfe von Wranglingdatenflüssen
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5ee1069ca24dc16a52d30450c0f2163130f549cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494901"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Vorbereiten von Daten mit Wranglingdatenflüssen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> Wranglingdatenflüsse sind derzeit in der öffentlichen Vorschau verfügbar

## <a name="create-a-wrangling-data-flow"></a>Erstellen eines Wranglingdatenflusses

Ein Wranglingdatenfluss kann in Azure Data Factory auf zwei Arten erstellt werden. Bei der einen Methode klicken Sie auf das Plussymbol und wählen im Bereich mit den Factory-Ressourcen die Option **Datenfluss** aus.

![Screenshot: Option „Data Flow“ (Datenfluss) im Bereich „Factory Resources“ (Factoryressourcen)](media/wrangling-data-flow/tutorial7.png)

Die andere Möglichkeit befindet sich im Bereich „Aktivitäten“ der Pipelinecanvas. Öffnen Sie das Accordion-Element **Verschieben und transformieren**, und ziehen Sie die Aktivität **Datenfluss** auf die Canvas.

Wählen Sie bei beiden Methoden im daraufhin geöffneten Seitenbereich die Option **Neuen Datenfluss erstellen** und dann **Wranglingdatenfluss** aus. Klicken Sie auf OK.

![Screenshot: Hervorhebung der Option „Wrangling data flow“ (Wranglingdatenfluss)](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Definieren eines Wranglingdatenflusses

Fügen Sie ein **Quelldataset** für Ihren Wranglingdatenfluss hinzu. Wählen Sie entweder ein vorhandenes Dataset aus, oder erstellen Sie ein neues. Sie können auch ein Senkendataset auswählen. Sie können ein oder mehrere Quelldatasets auswählen, doch ist derzeit nur eine Senke zulässig. Die Auswahl eines Senkendatasets ist optional, aber es ist mindestens ein Quelldataset erforderlich.

> [!NOTE]
> Nur Text mit Trennzeichen aus ADLS Gen 2 wird für die eingeschränkte Vorschau unterstützt. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Klicken Sie auf **Erstellen**, um den Power Query Online-Mashup-Editor zu öffnen.

![Screenshot: Schaltfläche „Erstellen“ zum Öffnen des Power Query Online-Mashup-Editors](media/wrangling-data-flow/tutorial5.png)

Erstellen Sie den Wranglingdatenfluss mithilfe der codefreien Datenvorbereitung. Die Liste der verfügbaren Funktionen finden Sie unter [Transformationsfunktionen](wrangling-data-flow-functions.md).

![Screenshot: Prozess zum Erstellen des Wranglingdatenflusses](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Ausführen und Überwachen eines Wranglingdatenflusses

Wenn Sie einen Lauf zum Debuggen der Pipeline eines Wranglingdatenflusses ausführen möchten, klicken Sie auf der Pipelinecanvas auf **Debuggen**. Nachdem Sie den Datenfluss veröffentlicht haben, erfolgt mit **Jetzt auslösen** eine bedarfsgesteuerte Ausführung der letzten veröffentlichten Pipeline. Wranglingdatenflüsse können mit allen vorhandenen Azure Data Factory-Triggern geplant werden.

![Screenshot: Hinzufügen eines Wranglingdatenflusses](media/wrangling-data-flow/tutorial3.png)

Wechseln Sie zur Registerkarte **Überwachen**, um die Ausgabe einer ausgelösten Aktivitätsausführung für den Wranglingdatenfluss zu visualisieren.

![Screenshot: Ausgabe der ausgelösten Aktivitätsausführung für einen Wranglingdatenfluss](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [einen Zuordnungsdatenfluss erstellen](tutorial-data-flow.md).