---
title: Includedatei
description: Includedatei
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file
ms.date: 11/12/2018
ms.author: diberry
ms.openlocfilehash: fd6a4a4e2d779047d252fdc5e3eae8e3fba8b507
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145582"
---
## <a name="request-and-response-json"></a>JSON-Anforderung und -Antwort

Eine Beispielfrage für die REST-API im JSON-Format:

```json
{
    "question": "Is the QnA Maker Service free?",
    "top": 3
}
```

Die Frage enthält eine Eigenschaft zum Zurückgeben der drei häufigsten Antworten. 

Die Antwort wird als JSON-Objekt zurückgegeben:

```json
{
  "answers": [
    {
      "questions": [
        "How do I embed the QnA Maker service in my website?"
      ],
      "answer": "Follow these steps to embed the QnA Maker service as a web-chat control in your website:\n\n\n1.  Create your FAQ bot by following the instructions [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot).\n2.  Enable the web chat by following the steps [here](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-webchat)",
      "score": 70.95,
      "id": 16,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "Do I need to use Bot Framework in order to use QnA Maker?"
      ],
      "answer": "No, you do not need to use the Bot Framework with QnA Maker. However, QnA Maker is offered as one of several templates in Azure Bot Service. Bot Service enables rapid intelligent bot development through Microsoft Bot Framework, and it runs in a server-less environment.",
      "score": 46.94,
      "id": 14,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    },
    {
      "questions": [
        "How can I create a bot with QnA Maker?"
      ],
      "answer": "Follow the instructions in [this](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/create-qna-bot)documentation to create your Bot with Azure Bot Service.",
      "score": 43.25,
      "id": 15,
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "metadata": []
    }
  ]
}
```
