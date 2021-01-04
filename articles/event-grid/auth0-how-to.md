---
title: Senden von Ereignissen von Auth0 an Azure mithilfe von Azure Event Grid
description: Erfahren Sie, wie Ereignisse mithilfe von Azure Event Grid von Auth0 an Azure gesendet werden.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077862"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integrieren von Azure Event Grid und Auth0

In diesem Artikel wird beschrieben, wie Sie Ihre Auth0- und Azure-Konten durch Erstellen eines Event Grid-Partnerthemas verbinden können.

Eine vollständige Liste der von Auth0 unterstützten Ereignisse finden Sie im Artikel zu den [Codes von Auth0-Ereignistypen](https://auth0.com/docs/logs/references/log-event-type-codes).

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Senden von Ereignissen von Auth0 an Azure Event Grid
So senden Sie Auth0-Ereignisse an Azure

1. Aktivieren des Event Grid-Ressourcenanbieters
1. Einrichten eines Auth0-Partnerthemas im Auth0-Dashboard
1. Aktivieren des Partnerthemas in Azure
1. Abonnieren von Ereignissen aus Auth0

Weitere Informationen zu diesen Konzepten finden Sie unter [Begriffe in Azure Event Grid](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Aktivieren des Event Grid-Ressourcenanbieters
Sofern Sie Event Grid nicht bereits zuvor genutzt haben, müssen Sie den Event Grid-Ressourcenanbieter registrieren. Wenn Sie Event Grid bereits eingesetzt haben, fahren Sie mit dem nächsten Abschnitt fort.

Führen Sie im Azure-Portal die folgenden Schritte aus:
1. Wählen Sie im linken Menü „Abonnements“ aus.
1. Wählen Sie das Abonnement aus, das Sie für Event Grid verwenden.
1. Wählen Sie im linken Menü unter „Einstellungen“ die Option „Ressourcenanbieter“ aus.
1. Suchen Sie nach „Microsoft.EventGrid“.
1. Wählen Sie „Registrieren“ aus.
1. Aktualisieren Sie, um sicherzustellen, dass sich der Status in „Registriert“ geändert hat.

### <a name="set-up-an-auth0-partner-topic"></a>Einrichten eines Auth0-Partnerthemas
Teil des Integrationsprozesses ist es, Auth0 als Ereignisquelle einzurichten (dieser Schritt erfolgt in Ihrem [Auth0-Dashboard](https://manage.auth0.com/)).

1. Melden Sie sich beim [Auth0-Dashboard](https://manage.auth0.com/) an.
1. Navigieren Sie zu „Logs > Streams“ (Protokolle > Streams).
1. Klicken Sie auf „+ Create Stream“ (Stream erstellen).
1. Wählen Sie „Azure Event Grid“ aus, und geben Sie einen eindeutigen Namen für Ihren neuen Stream ein.
1. Erstellen Sie die Ereignisquelle, indem Sie Ihre Azure-Abonnement-ID, die Azure-Region und einen Ressourcengruppennamen angeben. 
1. Klicken Sie auf „Speichern“.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Aktivieren des Auth0-Partnerthemas in Azure
Durch Aktivieren des Auth0-Themas in Azure können Ereignisse von Auth0 nach Azure übertragen werden.

1. Melden Sie sich beim Azure-Portal an.
1. Suchen Sie oben nach `Partner Topics`, und klicken Sie unter „Dienste“ auf `Event Grid Partner Topics`.
1. Klicken Sie auf das Thema, das dem Stream entspricht, den Sie in Ihrem Auth0-Dashboard erstellt haben.
1. Bestätigen Sie, dass das Feld `Source` Ihrem Auth0-Konto entspricht.
1. Klicken Sie auf „Aktivieren“.

### <a name="subscribe-to-auth0-events"></a>Abonnieren von Auth0-Ereignissen

#### <a name="create-an-event-handler"></a>Erstellen eines Ereignishandlers
Zum Testen Ihres Partnerthemas benötigen Sie einen Ereignishandler. Wechseln Sie zu Ihrem Azure-Abonnement, und starten Sie einen Dienst, der als [Ereignishandler](event-handlers.md) unterstützt wird, wie z. B. eine [Azure-Funktion](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Abonnieren des Auth0-Partnerthemas
Wenn Sie Ihr Auth0-Partnerthema abonnieren, können Sie Event Grid mitteilen, wohin Ihre Auth0-Ereignisse übertragen werden sollen.

1. Wählen Sie oben auf dem Blatt „Partnerthema“ für Ihre Auth0-Integration „+ Ereignisabonnement“ aus.
1. Gehen Sie auf der Seite „Ereignisabonnement erstellen“ so vor
    1. Geben Sie einen Namen für das Ereignisabonnement an.
    1. Wählen Sie den Azure-Dienst oder Webhook aus, den Sie für den Endpunkttyp erstellt haben.
    1. Befolgen Sie die Anweisungen für den jeweiligen Dienst.
    1. Klicken Sie auf „Erstellen“.

## <a name="testing"></a>Testen
Die Integration Ihres Auth0-Partnerthemas in Azure sollte sofort einsatzbereit sein.

### <a name="verify-the-integration"></a>Überprüfen der Integration
So überprüfen Sie, ob die Integration erwartungsgemäß funktioniert

1. Melden Sie sich beim Auth0-Dashboard an.
1. Navigieren Sie zu „Logs > Streams“ (Protokolle > Streams).
1. Klicken Sie auf den Event Grid-Stream.
1. Klicken Sie im Stream auf die Registerkarte „Health“ (Integrität). Der Stream sollte aktiv sein. Solange keine Fehler gemeldet werden, funktioniert der Stream.

Versuchen Sie [eine der Auth0-Aktionen aufzurufen, die ein zu veröffentlichendes Ereignis auslösen](https://auth0.com/docs/logs/references/log-event-type-codes), um zu sehen, wie Ereignisse sich vollziehen.

## <a name="delivery-attempts-and-retries"></a>Übermittlungsversuche und Wiederholungen
Auth0-Ereignisse werden über einen Streamingmechanismus an Azure übermittelt. Jedes Ereignis wird nach seiner Auslösung in Auth0 gesendet. Wenn Event Grid das Ereignis nicht empfangen kann, versucht Auth0 maximal dreimal, das Ereignis zu übermitteln. Andernfalls protokolliert Auth0 den Übermittlungsfehler in seinem System.

## <a name="next-steps"></a>Nächste Schritte

- [Auth0-Partnerthema](auth0-overview.md)
- [Übersicht über Partnerthemen](partner-events-overview.md)
- [Registrieren als Event Grid-Partner](partner-onboarding-overview.md)