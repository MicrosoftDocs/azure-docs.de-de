---
title: 'Zusammenarbeit an der Wissensdatenbank: Qna Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker ermöglicht mehreren Personen, an einer Wissensdatenbank zusammenzuarbeiten. Dieses Feature wird im Rahmen der rollenbasierten Zugriffssteuerung in Azure bereitgestellt.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: ca754f197a46fc41b6f1b432611a2177ec0afafa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61374811"
---
# <a name="collaborate-on-your-knowledge-base"></a>Zusammenarbeiten an einer Wissensdatenbank

QnA Maker ermöglicht mehreren Personen, an einer Wissensdatenbank zusammenzuarbeiten. Diese Funktion wird im Rahmen der [rollenbasierten Zugriffssteuerung](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) in Azure bereitgestellt. 

Führen Sie die folgenden Schritte aus, um Ihren QnA Maker-Dienst für andere freizugeben:

1. Melden Sie sich im Azure-Portal an, und gehen Sie zu Ihrer QnA Maker-Ressource.

    ![QnA Maker-Ressourcenliste](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

2. Öffnen Sie die Registerkarte **Zugriffssteuerung (IAM)** .

    ![IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

3. Wählen Sie **Hinzufügen**.

    ![Hinzufügen von IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

4. Wählen Sie die Rolle **Besitzer** oder **Mitwirkender** aus. Durch die rollenbasierte Zugriffssteuerung können Sie keinen schreibgeschützten Zugriff gewähren. Die Rollen „Besitzer“ und „Mitwirkender“ haben Lese-/Schreibzugriffsrechte auf den QnA Maker-Dienst.

    ![Hinzufügen einer IAM-Rolle in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

5. Geben Sie die E-Mail-Adresse der Person ein, für die Sie den Dienst freigeben möchten, und klicken Sie auf „Speichern“.

    ![Hinzufügen einer E-Mail-Adresse für IAM in QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Wenn sich eine Person, für die Sie Ihren QnA Maker-Dienst freigegeben haben, im [QnA Maker-Portal](https://qnamaker.ai) anmeldet, sieht sie nun alle Wissensdatenbanken in diesem Dienst.

Es ist nicht möglich, eine bestimmte Wissensdatenbank in einem QnA Maker-Dienst freizugeben. Wenn Sie eine differenzierte Zugriffssteuerung wünschen, verteilen Sie Ihre Wissensdatenbanken auf verschiedene QnA Maker-Dienste.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Testen einer Wissensdatenbank](./test-knowledge-base.md)
