---
title: 'Präzise Antworten mithilfe der Erkennung von Antwortspannen: QnA Maker'
description: Erfahren Sie mehr über das Feature für präzise Antworten, das in QnA Maker verwaltet verfügbar ist.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 4f64bab698cb87e26fa4fd1587c4269acf99fa59
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94385196"
---
# <a name="precise-answering"></a>Präzise Antworten

Das Feature „Präzise Antwort“ ermöglicht es Ihnen, für jede Benutzerabfrage eine präzise Kurzantwort von der geeigneten Antwortpassage zu erhalten, die in der Wissensdatenbank vorhanden ist. Dieses Feature verwendet ein Deep Learning-Modell für die Runtime, das die Absicht der Benutzerabfrage versteht und die präzise Kurzantwort aus der Antwortpassage erkennt, wenn eine Kurzantwort als Fakt in der Antwortpassage vorhanden ist. 

Dieses Feature ist im Testbereich standardmäßig aktiviert, sodass Sie die für Ihr Szenario bestimmte Funktionalität testen können. Dieses Feature ist sowohl für Contententwickler als auch für Benutzer von großem Vorteil. Jetzt müssen Contententwickler nicht mehr manuell bestimmte QnA-Paare für jeden in der Wissensdatenbank vorhandenen Fakt kuratieren, und der Endbenutzer muss nicht mehr die gesamte vom Dienst zurückgegebene Antwortpassage überprüfen, um den tatsächlichen Fakt zu finden, der die Abfrage des Benutzers beantwortet. 

## <a name="precise-answering-on-qna-maker-portal"></a>Präzise Antworten im QnA Maker-Portal

Wenn Sie im QnA Maker-Portal den Testbereich öffnen, sehen Sie oben die Option zum **Anzeigen der Kurzantwort**. Diese Option wird standardmäßig aktiviert. Wenn Sie eine Frage in den Testbereich eingeben, sehen Sie eine Kurzantwort zusammen mit der Antwortpassage, wenn in der Antwortpassage eine Kurzantwort vorhanden ist. 
 
![Verwalteter aktivierter Testbereich](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Sie können die Option zum **Anzeigen der Kurzantwort** deaktivieren, wenn Sie nur die Antwortpassage im Testbereich anzeigen möchten. 

Der Dienst gibt auch die Zuverlässigkeitsbewertung der genauen Antwort als **Bewertung der Antwortspanne** zurück, die Sie überprüfen können, indem Sie die Option **Überprüfen** auswählen, die sich direkt unter der Abfrage im Testbereich befindet.

![Bewertung der verwalteten Antwortspanne](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Veröffentlichen eines QnA Maker-Bots

Wenn Sie einen Bot veröffentlichen, erhalten Sie in Ihrer Anwendung standardmäßig die Umgebung mit aktivierten präzisen Antworten, wobei eine Kurzantwort zusammen mit der Antwortpassage angezeigt wird. Benutzer haben die Flexibilität, andere Umgebungen auszuwählen, indem sie die Vorlage über den eBot-App-Dienst aktualisieren. 

## <a name="language-support"></a>Sprachunterstützung

Derzeit wird das Feature für präzise Antworten nur für Englisch unterstützt.
