---
title: Schnellstart – Behandeln von SMS-Ereignissen
titleSuffix: An Azure Communication Services quickstart
description: Erfahren Sie, wie Sie SMS-Ereignisse mithilfe von Azure Communication Services verarbeiten.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: d6409c005e006372f55e77aeb4d977e6b1c45832
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936273"
---
# <a name="quickstart-handle-sms-events"></a>Schnellstart: Behandeln von SMS-Ereignissen

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Machen Sie Ihre ersten Schritte mit Azure Communication Services, indem Sie Azure Event Grid zum Behandeln von SMS-Ereignissen von Communication Services verwenden. 

## <a name="about-azure-event-grid"></a>Informationen zu Azure Event Grid

[Azure Event Grid](../../../event-grid/overview.md) ist ein cloudbasierter Ereignisdienst. In diesem Artikel erfahren Sie, wie Sie Ereignisse für [Communication Service-Ereignisse](../../concepts/event-handling.md) abonnieren und ein Ereignis zum Anzeigen des Ergebnisses auslösen. Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. In diesem Artikel senden wir Ereignisse an eine Web-App, die die Nachrichten sammelt und anzeigt.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Eine Azure Communication Service-Ressource. Weitere Informationen finden Sie im Schnellstart [Erstellen einer Azure Communication Services-Ressource](../create-communication-resource.md).
- Eine SMS-aktivierte Telefonnummer. [Erhalten Sie eine Telefonnummer](./get-phone-number.md).

## <a name="setting-up"></a>Einrichten

### <a name="enable-event-grid-resource-provider"></a>Aktivieren des Event Grid-Ressourcenanbieters

Wenn Sie Event Grid bisher nicht in Ihrem Azure-Abonnement verwendet haben, registrieren Sie den Event Grid-Ressourcenanbieter, indem Sie die folgenden Schritte ausführen:

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im linken Menü **Abonnements** aus.
2. Wählen Sie das Abonnement aus, das Sie für Event Grid verwenden.
3. Wählen Sie im linken Menü unter **Einstellungen** die Option **Ressourcenanbieter** aus.
4. Suchen Sie nach **Microsoft.EventGrid**.
5. Wenn Sie nicht registriert sind, klicken Sie auf **Registrieren**. 

Es kann einen Moment dauern, bis die Registrierung abgeschlossen ist. Wählen Sie zum Aktualisieren des Status die Option **Aktualisieren** aus. Wenn der **Status** **Registriert** lautet, können Sie den Vorgang fortsetzen.

### <a name="event-grid-viewer-deployment"></a>Bereitstellen von Event Grid Viewer

In diesem Schnellstart verwenden wir das [Azure Event Grid Viewer-Beispiel](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/), um Ereignisse nahezu in Echtzeit anzuzeigen. Dadurch erhält der Benutzer einen Echtzeitfeed. Außerdem muss die Payload jedes Ereignisses auch zur Überprüfung verfügbar sein.  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Abonnieren von SMS-Ereignissen mithilfe von Webhooks

Navigieren Sie im Azure-Portal zur Azure Communication Services-Ressource, die Sie erstellt haben. Wählen Sie in der Communication Services-Ressource **Ereignisse** im linken Menü der Seite **Communication Services** aus.

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Screenshot, der die Auswahl der Schaltfläche „Ereignisabonnement“ auf der Ereignisseite einer Ressource zeigt":::

Drücken Sie **Add Event Subscription** (Ereignisabonnement hinzufügen), um den Erstellungs-Assistenten zu starten.

Geben Sie auf der Seite **Ereignisabonnement erstellen** einen **Namen** für das Ereignisabonnement ein.

Sie können spezifische Ereignisse abonnieren, um Event Grid mitzuteilen, welche SMS-Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen. Wählen Sie im Dropdownmenü die Ereignisse aus, die Sie abonnieren möchten. Für SMS können Sie `SMS Received` (SMS empfangen) und `SMS Delivery Report Received` (SMS-Zustellberichte empfangen) auswählen. 

Wenn Sie aufgefordert werden, einen **Namen für das Systemthema** anzugeben, können Sie eine eindeutige Zeichenfolge angeben. Dieses Feld wirkt sich nicht auf Ihre Benutzeroberflächen aus und wird für interne Telemetriezwecke verwendet.

Sehen Sie sich die vollständige Liste der [von Azure Communication Services unterstützten Ereignisse](../../concepts/event-handling.md) an.

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Screenshot, der die Auswahl der Ereignistypen „SMS Received“ (SMS empfangen) und „SMS Delivery Report Received“ (SMS-Zustellberichte empfangen) zeigt":::

Wählen Sie unter **Endpunkttyp** die Option **Webhook** aus. 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Screenshot, der das Feld „Endpunkttyp“ zeigt, das auf „Webhook“ eingestellt ist":::

Klicken Sie für **Endpunkt** auf **Endpunkt auswählen**, und geben Sie die URL Ihrer Web-App ein.

In diesem Fall verwenden wir die URL aus dem [Azure Event Grid Viewer-Beispiel](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/), das wir zuvor im Schnellstart eingerichtet haben. Die URL für das Beispiel weist das folgende Format auf: `https://{{site-name}}.azurewebsites.net/api/updates`

Wählen Sie anschließend **Auswahl bestätigen** aus.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Screenshot, der das Bestätigen eines Webhookendpunkts zeigt":::

## <a name="viewing-sms-events"></a>Anzeigen von SMS-Ereignissen

### <a name="triggering-sms-events"></a>Auslösen von SMS-Ereignissen

Zum Anzeigen von Ereignisauslösern müssen wir zuerst Ereignisse generieren.

- `SMS Received`-Ereignisse (SMS empfangen) werden generiert, wenn die Communication Services-Telefonnummer eine Textnachricht empfängt. Um ein Ereignis auszulösen, senden Sie einfach eine Nachricht von Ihrem Telefon an die Telefonnummer, die an die Communication Services-Ressource angefügt ist.
- `SMS Delivery Report Received`-Ereignisse (SMS-Zustellberichte empfangen) werden generiert, wenn Sie mit einer Communication Services-Nummer eine SMS an einen Benutzer senden. Zum Auslösen eines Ereignisses müssen Sie in den Optionen der [gesendeten SMS](../telephony-sms/send.md) die Option `Delivery Report` (Zustellbericht) aktivieren. Senden Sie eine Nachricht an Ihr Mobiltelefon mit einem Zustellbericht (`Delivery Report`). Für diese Aktion fallen in Ihrem Azure-Konto ggf. geringfügige Kosten im Centbereich an.

Sehen Sie sich die vollständige Liste der [von Azure Communication Services unterstützten Ereignisse](../../concepts/event-handling.md) an.

### <a name="receiving-sms-events"></a>Empfangen von SMS-Ereignissen

Nachdem Sie die obigen Aktionen durchgeführt haben, stellen Sie fest, dass die Ereignisse `SMS Received` (SMS empfangen) und `SMS Delivery Report Received` (SMS-Zustellbericht empfangen) an Ihren Endpunkt gesendet werden. Diese Ereignisse werden im [Azure Event Grid Viewer-Beispiel](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) angezeigt, das wir am Anfang eingerichtet haben. Wählen Sie das Augensymbol neben dem Ereignis aus, um die gesamte Payload anzuzeigen. Ereignisse sehen wie folgt aus:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Screenshot, der das Event Grid-Schema für ein „SMS Received“-Ereignis (SMS empfangen) zeigt":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Screenshot, der das Event Grid-Schema für ein „SMS Delivery Report Received“-Ereignis (SMS-Zustellberichte empfangen) zeigt":::

Erfahren Sie mehr über [Ereignisschemas und andere Ereigniskonzepte](../../concepts/event-handling.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, SMS-Ereignisse zu verwenden. Sie können SMS-Nachrichten empfangen, indem Sie ein Event Grid-Abonnement erstellen.

> [!div class="nextstepaction"] 
> [Senden einer SMS](../telephony-sms/send.md)

Das könnte Sie auch interessieren:

 - [Weitere Informationen zu Ereignisbehandlungskonzepten](../../concepts/event-handling.md)
 - [Weitere Informationen zu Azure Event Grid](../../../event-grid/overview.md)