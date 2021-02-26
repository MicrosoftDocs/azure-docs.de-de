---
title: 'Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal'
description: Verwenden Sie Azure Event Grid, um Azure Cache for Redis-Ereignisse zu abonnieren, an einen Webhook zu senden und in einer Webanwendung zu verarbeiten.
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
author: curib
ms.author: cauribeg
ms.openlocfilehash: 5bdd6b0e6f97f7e5a738ab17d68282cf402004b0
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056483"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-the-azure-portal"></a>Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit dem Azure-Portal

Azure Event Grid ist ein Ereignisdienst für die Cloud. In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um eine Azure Cache for Redis-Instanz zu erstellen, Ereignisse für diese Instanz zu abonnieren, ein Ereignis auszulösen und die Ergebnisse anzuzeigen. Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. Der Einfachheit halber senden Sie Ereignisse im Rahmen dieser Schnellstartanleitung an eine Web-App, mit der die Nachrichten gesammelt und angezeigt werden. 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Nach Abschluss des Vorgangs können Sie sehen, dass die Ereignisdaten an die Web-App gesendet wurden.

:::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid-Viewer: Skalierung im JSON-Format":::

## <a name="create-an-azure-cache-for-redis-cache-instance"></a>Erstellen einer Instanz des Azure Cache for Redis-Caches 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Vor dem Abonnieren der Ereignisse für die Cache-Instanz erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. Der Einfachheit halber stellen Sie in dieser Schnellstartanleitung eine [vordefinierte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

1. Wählen Sie in der GitHub-Infodatei die Option **Deploy to Azure** (In Azure bereitstellen) aus, um die Lösung für Ihr Abonnement bereitzustellen. 

:::image type="content" source="media/cache-event-grid-portal/deploy-to-azure.png" alt-text="Schaltfläche „Deploy to Azure“ (In Azure bereitstellen)":::

2. Gehen Sie auf der Seite **Benutzerdefinierte Bereitstellung** wie folgt vor: 
    1. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe aus, die Sie beim Erstellen der Cache-Instanz erstellt haben. Dadurch muss zur Bereinigung nach Abschluss des Tutorials einfach nur die Ressourcengruppe gelöscht werden.  
    2. Geben Sie unter **Websitename** einen Namen für die Web-App ein.
    3. Geben Sie unter **Name des Hostingplans** einen Namen für den App Service-Plan zum Hosten der Web-App ein.
    4. Aktivieren Sie das Kontrollkästchen **Ich stimme den oben genannten Geschäftsbedingungen zu**. 
    5. Wählen Sie die Option **Kaufen**. 
    
    | Einstellung      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Öffnen Sie die Dropdownliste, und wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese Web-App erstellt werden soll. | 
    | **Ressourcengruppe** | Öffnen Sie die Dropdownliste, und wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
    | **Standortname** | Geben Sie einen Namen für Ihre Web-App ein. | Dieser Wert muss angegeben sein. | 
    | **Name des Hostingplans** | Geben Sie einen Namen für den App Service-Plan ein, der zum Hosten der Web-App verwendet werden soll. | Dieser Wert muss angegeben sein. | 

1. Wählen Sie im Portal die Option „Warnungen“ (Glockensymbol) und anschließend **Zu Ressourcengruppe wechseln** aus. 

    :::image type="content" source="media/cache-event-grid-portal/deployment-notification.png" alt-text="Benachrichtigung über Bereitstellung im Azure-Portal":::

4. Wählen Sie auf der Seite **Ressourcengruppe** in der Ressourcenliste die von Ihnen erstellte Web-App aus. Darüber hinaus werden in dieser Liste der App Service-Plan und die Cache-Instanz angezeigt. 

5. Wählen Sie auf der Seite **App Service** für Ihre Web-App die URL aus, um zur entsprechenden Website zu navigieren. Die URL muss folgendes Format haben: `https://<your-site-name>.azurewebsites.net`.

6. Vergewissern Sie sich, dass die Website angezeigt wird, aber noch keine Ereignisse bereitgestellt wurden.

    :::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Event Grid-Viewer: Leere Website":::

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-azure-cache-for-redis-instance"></a>Abonnieren der Azure Cache for Redis-Instanz

In diesem Schritt abonnieren Sie ein Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen.

1. Navigieren Sie im Portal zu Ihrer Cache-Instanz, die Sie zuvor erstellt haben. 
2. Wählen Sie auf der Seite **Azure Cache for Redis** im Menü auf der linken Seite die Option **Ereignisse** aus. 
3. Wählen Sie **Webhook** aus. Sie verwenden einen Webhook für den Endpunkt, um Ereignisse an Ihre Viewer-App zu senden. 

     :::image type="content" source="media/cache-event-grid-portal/event-grid-web-hook.png" alt-text="Azure-Portal: Seite „Ereignisse“":::

4. Geben Sie auf der Seite **Ereignisabonnement erstellen** Folgendes ein: 

    | Einstellung      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Geben Sie einen Namen für das Ereignisabonnement an. | Der Wert muss eine Länge von 3 bis 64 Zeichen haben. Er darf nur Buchstaben, Zahlen und Bindestriche enthalten. | 
    | **Ereignistypen** | Wählen Sie in der Dropdownliste aus, welche Ereignistypen per Pushvorgang auf Ihr Ziel übertragen werden sollen. Für diese Schnellstartanleitung skalieren wir unsere Cache-Instanz. | „Patchen“, „Skalierung“, „Import“ und „Export“ sind die verfügbaren Optionen. | 
    | **Endpunkttyp** | Wählen Sie **Webhook** aus. | Der Ereignishandler für den Empfang Ihrer Ereignisse. | 
    | **Endpunkt** | Klicken Sie auf **Endpunkt auswählen**, geben Sie die URL Ihrer Web-App ein, und fügen Sie `api/updates` der URL für die Startseite hinzu (z. B. `https://cache.azurewebsites.net/api/updates`). Wählen Sie anschließend die Option **Auswahl bestätigen** aus. | Dies ist die URL Ihrer Web-App, die Sie zuvor erstellt haben. | 

5. Wählen Sie als Nächstes auf der Seite **Ereignisabonnement erstellen** die Option **Erstellen** aus, um das Ereignisabonnement zu erstellen. 

6. Zeigen Sie wieder Ihre Web-App an. Wie Sie sehen, wurde ein Abonnementüberprüfungsereignis an sie gesendet. Klicken Sie auf das Augensymbol, um die Ereignisdaten zu erweitern. Event Grid sendet das Überprüfungsereignis, damit der Endpunkt bestätigen kann, dass er Ereignisdaten empfangen möchte. Die Web-App enthält Code zur Überprüfung des Abonnements.

    :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid-Viewer":::

## <a name="send-an-event-to-your-endpoint"></a>Senden eines Ereignisses an Ihren Endpunkt

Als Nächstes lösen wir ein Ereignis aus, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Wir skalieren Ihre Azure Cache for Redis-Instanz.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Cache for Redis-Instanz, und wählen Sie im Menü auf der linken Seite die Option **Skalieren** aus.

1. Wählen Sie auf der Seite **Skalieren** den gewünschten Tarif aus, und klicken Sie auf **Auswählen**. 

    Sie können mit den folgenden Einschränkungen zu einem anderen Tarif wechseln:
    
    * Sie können keine Skalierung von einem höheren Tarif auf einen niedrigeren Tarif vornehmen.
      * Eine Skalierung von einem **Premium**-Cache auf einen niedrigeren **Standard**- oder **Basic**-Cache ist nicht möglich.
      * Ein **Standard**-Cache kann nicht auf einen niedrigeren **Basic**-Cache skaliert werden.
    * Ein **Basic**-Cache kann auf einen **Standard**-Cache skaliert werden, die Größe kann jedoch nicht gleichzeitig geändert werden. Wenn Sie eine andere Größe benötigen, können Sie anschließend einen Skalierungsvorgang auf die gewünschte Größe durchführen.
    * Ein **Basic**-Cache kann nicht direkt auf einen **Premium**-Cache skaliert werden. Skalieren Sie zunächst in einem ersten Skalierungsvorgang von **Basic** auf **Standard** und dann in einem nachfolgenden Skalierungsvorgang von **Standard** auf **Premium**.
    * Von einer größeren Größe kann nicht auf **C0 (250 MB)** herunterskaliert werden.
 
    Während der Cache in den neuen Tarif skaliert wird, wird auf dem Blatt **Azure Cache for Redis** der Status **Wird skaliert** angezeigt. Wenn die Skalierung abgeschlossen ist, ändert sich der Status von **Wird skaliert** zu **Wird ausgeführt**.

1. Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Die Nachricht weist das JSON-Format auf und enthält ein Array mit mindestens einem Ereignis. Im folgenden Beispiel enthält die JSON-Nachricht ein Array mit einem Ereignis. Zeigen Sie Ihre Web-App an. Sie sehen, dass das Ereignis **ScalingCompleted** empfangen wurde. 

    :::image type="content" source="media/cache-event-grid-portal/event-grid-scaling.png" alt-text="Azure Event Grid-Viewer: Skalierung im JSON-Format":::

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

Wenn Sie dieses Ereignis weiterverwenden möchten, sollten Sie keine Bereinigung für die in dieser Schnellstartanleitung erstellten Ressourcen durchführen. Löschen Sie andernfalls die Ressourcen, die Sie im Rahmen dieser Schnellstartanleitung erstellt haben.

Wählen Sie die Ressourcengruppe aus, und klicken Sie auf **Ressourcengruppe löschen**.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie benutzerdefinierte Themen und Ereignisabonnements erstellen. Nun können Sie sich ausführlicher darüber informieren, welche Möglichkeiten Event Grid bietet:

- [Azure Cache for Redis: Event Grid-Übersicht](cache-event-grid.md)
- [Einführung in Azure Event Grid](../event-grid/overview.md)

