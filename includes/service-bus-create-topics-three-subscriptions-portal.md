---
title: Includedatei
description: Includedatei
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ace42278269ff6af31902dbecead81329815af12
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66111096"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Erstellen eines Themas mit dem Azure-Portal
1. Wählen Sie auf der Seite **Service Bus-Namespace** im linken Menü die Option **Themen** aus.
2. Wählen Sie auf der Symbolleiste die Option **+ Thema** aus. 
4. Geben Sie unter **Name** einen Namen für das Thema ein. Behalten Sie bei den anderen Optionen die Standardwerte bei.
5. Klicken Sie auf **Erstellen**.

    ![Thema erstellen](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Erstellen von Abonnements für das Thema
1. Wählen Sie das **Thema** aus, das Sie im vorherigen Abschnitt erstellt haben. 
    
    ![Auswählen des Themas](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Wählen Sie auf der Seite **Service Bus-Thema** im linken Menü die Option **Abonnements** und anschließend auf der Symbolleiste die Option **+ Abonnement** aus. 
    
    ![Schaltfläche „Abonnement hinzufügen“](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Geben Sie auf der Seite **Abonnement erstellen** für das Abonnement unter **Name** die Zeichenfolge **S1** ein, und wählen Sie anschließend **Erstellen** aus. 

    ![Seite „Abonnement erstellen“](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Wiederholen Sie den vorherigen Schritt zweimal, um die Abonnements mit den Namen **S2** und **S3** zu erstellen.