---
title: include file
description: include file
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a95f5ee5105c45ba9e5b1705e83d60bf24b1dc12
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178320"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Erstellen eines Namespace im Azure-Portal
Um mit der Verwendung von Service Bus-Nachrichtenentitäten in Azure beginnen zu können, müssen Sie zuerst einen Namespace mit einem in Azure eindeutigen Namen erstellen. Ein Namespace ist ein Bereichscontainer für die Adressierung von Service Bus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Namespace

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
2. Wählen Sie im linken Navigationsbereich des Portals die Optionen **+ Ressource erstellen** > **Integration** > **Service Bus**.

    ![Ressource erstellen > Integration > Service Bus](./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png)
3. Gehen Sie im Dialogfeld **Namespace erstellen** wie folgt vor: 
    1. Geben Sie einen **Namen für den Namespace** ein. Das System überprüft sofort, ob dieser Name verfügbar ist. Eine Liste der Regeln für die Benennung von Namespaces finden Sie unter [REST-API zum Erstellen von Namespaces](/rest/api/servicebus/create-namespace).
    2. Wählen Sie den Tarif (Basic, Standard oder Premium) für den Namespace aus. Wenn Sie [Themen und Abonnements](../articles/service-bus-messaging/service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) nutzen möchten, wählen Sie entweder Standard oder Premium aus. Themen/Abonnements werden für den Basic-Tarif nicht unterstützt.
    3. Wenn Sie den Tarif **Premium** auswählen, führen Sie die folgenden Schritte aus: 
        1. Geben Sie die Anzahl der **Messagingeinheiten** an. Der Premium-Tarif bietet Ressourcenisolierung auf CPU- und Arbeitsspeicherebene, sodass die einzelnen Workloads voneinander isoliert ausgeführt werden. Dieser Ressourcencontainer wird als Messagingeinheit bezeichnet. Ein Premium-Namespace verfügt mindestens über eine Messagingeinheit. Sie können 1, 2 oder 4 Messagingeinheiten für jeden Service Bus Premium-Namespace erwerben. Weitere Informationen finden Sie unter [Service Bus Premium- und Standard-Preisstufe für Messaging](../articles/service-bus-messaging/service-bus-premium-messaging.md).
        2. Geben Sie an, ob der Namespace **zonenredundant** sein soll. Die Zonenredundanz bietet verbesserte Verfügbarkeit durch eine Verteilung der Replikate auf Verfügbarkeitszonen innerhalb einer Region ohne zusätzliche Kosten. Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?](../articles/availability-zones/az-overview.md).
    4. Wählen Sie unter **Abonnement** ein Azure-Abonnement aus, in dem der Namespace erstellt werden soll.
    5. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den Namespace aus, oder erstellen Sie eine neue Ressourcengruppe.      
    6. Wählen Sie unter **Standort** die Region aus, in der Ihr Namespace gehostet werden soll.
    7. Klicken Sie auf **Erstellen**. Ihr Dienstnamespace wird nun erstellt und aktiviert. Ggf. müssen Sie einige Minuten warten, bis die Ressourcen für Ihr Konto durch das System bereitgestellt werden.
   
        ![Namespace erstellen](./media/service-bus-create-namespace-portal/create-namespace.png)
4. Vergewissern Sie sich, dass der Service Bus-Namespace erfolgreich bereitgestellt wurde. Wählen Sie auf der Symbolleiste das **Glockensymbol (Warnungen)** , um entsprechende Benachrichtigungen anzuzeigen. Wählen Sie in der Benachrichtigung den **Namen der Ressourcengruppe** aus, wie in der folgenden Abbildung gezeigt. Sie sehen die Ressourcengruppe, die den Service Bus-Namespace enthält.

    ![Bereitstellungswarnung](./media/service-bus-create-namespace-portal/deployment-alert.png)
5. Wählen Sie auf der Seite **Ressourcengruppe** den **Service Bus-Namespace** für Ihre Ressourcengruppe aus. 

    ![Seite „Ressourcengruppe“: Auswählen Ihres Service Bus-Namespace](./media/service-bus-create-namespace-portal/resource-group-select-service-bus.png)
6. Die Startseite für Ihren Service Bus-Namespace wird angezeigt. 

    ![Startseite für Ihren Service Bus-Namespace](./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png)

## <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge 
Beim Erstellen eines neuen Namespace wird automatisch eine SAS-Regel (Shared Access Signature) mit einem zugeordneten Paar aus primären und sekundären Schlüsseln generiert, mit denen Sie jeweils die volle Kontrolle über sämtliche Aspekte des Namespace haben. Unter [Service Bus-Authentifizierung und -Autorisierung](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) erfahren Sie, wie Sie Regeln mit stärker eingeschränkten Rechten für reguläre Absender und Empfänger erstellen. Führen Sie zum Kopieren der Primär- und Sekundärschlüssel für Ihren Namespace die folgenden Schritte aus: 

1. Klicken Sie auf **Alle Ressourcen** und dann auf den neu erstellten Namespacenamen.
2. Klicken Sie im Namespacefenster auf **Richtlinien für gemeinsamen Zugriff**.
3. Klicken Sie im Bildschirm **Richtlinien für gemeinsamen Zugriff** auf **RootManageSharedAccessKey**.
   
    ![connection-info](./media/service-bus-create-namespace-portal/connection-info.png)
4. Klicken Sie im Fenster **Richtlinie: RootManageSharedAccessKey** neben **Primäre Verbindungszeichenfolge** auf die Schaltfläche „Kopieren“, um die Verbindungszeichenfolge zur späteren Verwendung in die Zwischenablage zu kopieren. Fügen Sie diesen Wert in den Editor oder an einem anderen temporären Speicherort ein.
   
    ![connection-string](./media/service-bus-create-namespace-portal/connection-string.png)
5. Wiederholen Sie den vorherigen Schritt, um den Wert von **Primärschlüssel** zu kopieren und zur späteren Verwendung an einem temporären Speicherort einzufügen.

<!--Image references-->

