---
title: Überwachen von Azure Media Services-Ereignissen mit Event Grid
description: In diesem Artikel wird gezeigt, wie Sie ein Abonnement für Event Grid abschließen können, um Azure Media Services-Ereignisse mithilfe der Azure-Befehlszeilenschnittstelle zu überwachen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9608dfd7ac4076e6cd74846c3f52ed2e3645777a
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956003"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-cli"></a>Erstellen und Überwachen von Media Services-Ereignissen mit Event Grid über die Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid ist ein Ereignisdienst für die Cloud. Dieser Dienst verwendet [Ereignisabonnements](../../event-grid/concepts.md#event-subscriptions) zum Weiterleiten von Ereignisnachrichten an Abonnenten. Media Services-Ereignisse enthalten alle Informationen, die Sie zur Reaktion auf Änderungen in Ihren Daten benötigen. Sie können ein Media Services-Ereignis daran erkennen, dass die eventType-Eigenschaft mit „Microsoft.Media“ beginnt. Weitere Informationen finden Sie unter [Media Services-Ereignisschemas](media-services-event-schemas.md).

In diesem Artikel verwenden Sie die Azure CLI, um Ereignisse für Ihr Azure Media Services-Konto zu abonnieren. Anschließend lösen Sie Ereignisse aus, um das Ergebnis anzuzeigen. Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. In diesem Artikel senden Sie Ereignisse an eine Web-App, die die Nachrichten sammelt und anzeigt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- Installieren und verwenden Sie die CLI lokal. Dieser Artikel erfordert mindestens Version 2.0 der Azure CLI. Führen Sie `az --version` aus, um herauszufinden, welche Version Sie haben. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

    Derzeit funktionieren nicht alle Befehle der [CLI von Media Services v3](/cli/azure/ams) in Azure Cloud Shell. Es wird empfohlen, die CLI lokal zu verwenden.

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md)

    Merken Sie sich die Werte, die Sie für den Namen der Ressourcengruppe und des Media Services-Kontos verwendet haben.

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Vor dem Abonnieren der Ereignisse für das Media Services-Konto erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. In diesem Artikel stellen Sie eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignismeldungen anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

1. Wählen Sie **Deploy to Azure** (In Azure bereitstellen), um die Lösung für Ihr Abonnement bereitzustellen. Geben Sie im Azure-Portal Werte für die Parameter an.

   [![Abbildung einer Schaltfläche mit der Bezeichnung „Bereitstellung in Azure“](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

Wenn Sie zur Seite „Azure Event Grid Viewer“ wechseln, werden Sie erkennen, dass sie noch nicht über Ereignisse verfügt.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="set-the-azure-subscription"></a>Einrichten des Azure-Abonnements

Geben Sie im folgenden Befehle die ID des Azure-Abonnements an, das Sie für das Media Services-Konto verwenden möchten. Sie können eine Liste der Abonnements anzeigen, auf die Sie Zugriff haben, indem Sie zu [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) navigieren.

```azurecli
az account set --subscription mySubscriptionId
```

## <a name="subscribe-to-media-services-events"></a>Abonnieren von Media Services-Ereignissen

Sie abonnieren einen Artikel, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten. Im folgenden Beispiel wird das von Ihnen erstellte Media Services-Konto abonniert. Außerdem wird die URL der von Ihnen erstellten Website als Endpunkt für Ereignisbenachrichtigungen übergeben. 

Ersetzen Sie `<event_subscription_name>` durch einen eindeutigen Namen für Ihr Ereignisabonnement. Verwenden Sie für `<resource_group_name>` und `<ams_account_name>` die Werte, die Sie beim Erstellen des Media Services-Kontos verwendet haben. Geben Sie für den `<endpoint_URL>` die URL Ihrer Web-App an, und fügen Sie `api/updates` der URL der Startseite hinzu. Durch die Angabe des Endpunkts beim Abonnieren übernimmt Event Grid die Weiterleitung von Ereignissen an diesen Endpunkt. 

1. Abrufen der Ressourcen-ID

    ```azurecli
    amsResourceId=$(az ams account show --name <ams_account_name> --resource-group <resource_group_name> --query id --output tsv)
    ```

    Beispiel:

    ```
    amsResourceId=$(az ams account show --name amsaccount --resource-group amsResourceGroup --query id --output tsv)
    ```

2. Abonnieren der Ereignisse

    ```azurecli
    az eventgrid event-subscription create \
    --source-resource-id $amsResourceId \
    --name <event_subscription_name> \
    --endpoint <endpoint_URL>
    ```

    Beispiel:

    ```
    az eventgrid event-subscription create --source-resource-id $amsResourceId --name amsTestEventSubscription --endpoint https://amstesteventgrid.azurewebsites.net/api/updates/
    ```    

    > [!TIP]
    > Möglicherweise erhalten Sie eine Warnung über einen Überprüfungshandshake. Warten Sie ein paar Minuten und der Handshake sollte bestätigt werden.

Anschließend werden Ereignisse ausgelöst, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet.

## <a name="send-an-event-to-your-endpoint"></a>Senden eines Ereignisses an Ihren Endpunkt

Sie können Ereignisse für das Media Services-Konto auslösen, indem Sie einen Codierungsauftrag ausführen. Sie können [dieser Schnellstartanleitung](stream-files-dotnet-quickstart.md) folgen, um eine Datei zu codieren und mit dem Senden von Ereignissen zu beginnen. 

Zeigen Sie wieder Ihre Web-App an. Wie Sie sehen, wurde ein Abonnementüberprüfungsereignis an sie gesendet. Event Grid sendet das Überprüfungsereignis, damit der Endpunkt bestätigen kann, dass er Ereignisdaten empfangen möchte. Der Endpunkt muss `validationResponse` auf `validationCode` festlegen. Weitere Informationen finden Sie unter [Event Grid – Sicherheit und Authentifizierung](../../event-grid/security-authentication.md). Sie können den Web-App-Code anzeigen, um zu sehen, wie das Abonnement überprüft wird.

> [!TIP]
> Klicken Sie auf das Augensymbol, um die Ereignisdaten zu erweitern. Aktualisieren Sie die Seite nicht, wenn Sie alle Ereignisse anzeigen möchten.

![Anzeigen des Abonnementereignisses](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>Nächste Schritte

[Hochladen, Codieren und Streamen](stream-files-tutorial-with-api.md)