---
title: Benachrichtigungen in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Senden Sie Benachrichtigungen an Benutzer von Apps, die auf Azure Communication Services basieren.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 30cb023b8ca78f252dbf087a604a61b8aa5c6659
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577380"
---
# <a name="communication-services-notifications"></a>Communication Services-Benachrichtigungen

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Der Chat von Azure Communication Services und die Bibliotheken für Telefonie-Clients erstellen einen Kanal für Echtzeitnachrichten, der es ermöglicht, Signalisierungsnachrichten per Push effizient und zuverlässig an verbundene Clients zu übermitteln. Dadurch können Sie umfangreiche Echtzeit-Kommunikationsfunktionen in Ihre Anwendungen integrieren, ohne dass Sie eine komplizierte HTTP-Abruflogik implementieren müssen. Bei mobilen Anwendungen bleibt die Verbindung dieses Signalisierungskanals jedoch nur dann bestehen, wenn die Anwendung im Vordergrund aktiv ist. Wenn Sie möchten, dass Ihre Benutzer eingehende Anrufe oder Chatnachrichten empfangen, während sich die Anwendung im Hintergrund befindet, sollten Sie Pushbenachrichtigungen verwenden.

Mithilfe von Pushbenachrichtigungen können Sie Informationen von der Anwendung an die mobilen Geräte der Benutzer senden. Sie können Pushbenachrichtigungen verwenden, um ein Dialogfeld anzuzeigen, einen Ton wiederzugeben oder die Benutzeroberfläche für eingehenden Anrufe anzuzeigen. Azure Communication Services bietet Integrationen in [Azure Event Grid](../../event-grid/overview.md) und [Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md), mit denen Sie zu Ihren Apps Pushbenachrichtigungen hinzufügen können.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Auslösen von Pushbenachrichtigungen über Azure Event Grid

Azure Communication Services ist in [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) integriert, um Echtzeit-Ereignisbenachrichtigungen zuverlässig, skalierbar und sicher bereitzustellen. Sie können diese Integration nutzen, um einen Benachrichtigungsdienst zu erstellen, der mobile Pushbenachrichtigungen an Ihre Benutzer übergibt, indem Sie ein Event Grid-Abonnement erstellen, das eine [Azure-Funktion](../../azure-functions/functions-overview.md) oder einen Webhook auslöst.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagramm, das die Integration von Communication Services in Event Grid zeigt":::

Erfahren Sie mehr über die [Ereignisbehandlung in Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Bereitstellen von Pushbenachrichtigungen über Azure Notification Hubs

Sie können einen Azure Notification Hub mit Ihrer Communication Services-Ressource verbinden, um automatisch Pushbenachrichtigungen an das mobile Gerät eines Benutzers zu senden, wenn er einen eingehenden Anruf erhält. Verwenden Sie diese Pushbenachrichtigungen, um Ihre Anwendung im Hintergrund zu reaktivieren und die Benutzeroberfläche anzuzeigen, über die Benutzer den Anruf annehmen oder ablehnen können. 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagramm: Integration von Communication Services in Azure Notification Hubs":::

Communication Services verwendet Azure Notification Hub als Passthroughdienst, um mit den verschiedenen plattformspezifischen Pushbenachrichtigungsdiensten über die [Direct Send](/rest/api/notificationhubs/direct-send)-API zu kommunizieren. Auf diese Weise können Sie Ihre vorhandenen Azure Notification Hub-Ressourcen und -Konfigurationen wiederverwenden, um geringe Latenzzeiten und zuverlässige Anrufbenachrichtigungen für Ihre Anwendungen bereitzustellen.

> [!NOTE]
> Derzeit wird nur das Aufrufen von Pushbenachrichtigungen unterstützt.

### <a name="notification-hub-provisioning"></a>Bereitstellen von Notification Hub 

Zum Übermitteln von Pushbenachrichtigungen an Clientgeräte mithilfe von Notification Hubs [erstellen Sie einen Notification Hub](../../notification-hubs/create-notification-hub-portal.md) innerhalb desselben Abonnements wie Ihre Communication Services-Ressource. Sie müssen den Azure Notification Hub für das Plattformbenachrichtigungssystem konfigurieren, das Sie verwenden möchten. Informationen zum Empfang von Pushbenachrichtigungen von Notification Hubs in Ihrer Client-App finden Sie unter [Erste Schritte mit Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md). Wählen Sie dort in der Dropdownliste oben auf der Seite die Zielplattform für den Client aus.

> [!NOTE]
> Derzeit werden die APNs- und FCM-Plattformen unterstützt.  
Die APNs-Plattform muss mit dem Tokenauthentifizierungsmodus konfiguriert sein. Der Zertifikatauthentifizierungsmodus wird derzeit nicht unterstützt. 

Nachdem Ihr Notification Hub konfiguriert wurde, können Sie ihn Ihrer Communication Services-Ressource zuweisen, indem Sie für den Hub mithilfe des Azure Resource Manager-Clients oder des Azure-Portals eine Verbindungszeichenfolge bereitstellen. Die Verbindungszeichenfolge muss Berechtigungen vom Typ `Send` enthalten. Wir empfehlen Ihnen, eine andere Zugriffsrichtlinie nur mit Berechtigungen vom Typ `Send` zu erstellen, die speziell für Ihren Hub gilt. Erfahren Sie mehr über die [Sicherheit von Notification Hubs und Zugriffsrichtlinien](../../notification-hubs/notification-hubs-push-notification-security.md).

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Verwenden des Azure Resource Manager-Clients zum Herstellen einer Verknüpfung mit Ihrem Notification Hub

Führen Sie Folgendes aus, um sich bei Azure Resource Manager anzumelden, und melden Sie sich mit Ihren Anmeldeinformationen an.

```console
armclient login
```

 Nach erfolgreicher Anmeldung führen Sie Folgendes aus, um den Notification Hub bereitzustellen:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Verwenden des Azure-Portals zum Herstellen einer Verknüpfung mit Ihrem Notification Hub

Navigieren Sie im Azure-Portal zu Ihrer Azure Communication Services-Ressource. Wählen Sie in der Communication Services-Ressource im linken Menü der Seite „Communication Services“ die Option „Pushbenachrichtigungen“ aus, und stellen Sie eine Verbindung mit dem zuvor bereitgestellten Benachrichtigungshub her. Sie müssen hier die Verbindungszeichenfolge und die Ressourcen-ID (resourceId) angeben:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Screenshot: Einstellungen für Pushbenachrichtigungen im Azure-Portal":::

> [!NOTE]
> Wenn die Azure Notification Hub-Verbindungszeichenfolge aktualisiert wird, muss auch die Communication Services-Ressource aktualisiert werden.  
Jede Änderung im Zusammenhang mit der Art der Hubverknüpfung wird auf der Datenebene (also beim Senden einer Benachrichtigung) innerhalb von maximal ``10`` Minuten widergespiegelt. Das gilt auch, wenn der Hub zum ersten Mal verknüpft wird – **vorausgesetzt**, es wurden zuvor bereits Benachrichtigungen gesendet.

### <a name="device-registration"></a>Geräteregistrierung 

Informationen zum Registrieren Ihres Gerätehandles bei Communication Services finden Sie im [Schnellstart zu Sprachanrufen](../quickstarts/voice-video-calling/getting-started-with-calling.md).

### <a name="troubleshooting-guide-for-push-notifications"></a>Leitfaden zur Problembehandlung für Pushbenachrichtigungen

Falls auf Ihrem Gerät keine Pushbenachrichtigungen angezeigt werden, gibt es drei Punkte, an denen die Benachrichtigungen ggf. verworfen wurden:

- Die Benachrichtigung von Azure Communication Services wurde von Azure Notification Hubs nicht akzeptiert.
- Das Plattformbenachrichtigungssystem (z. B. APNs und FCM) hat die Benachrichtigung von Azure Notification Hubs nicht akzeptiert.
- Das Plattformbenachrichtigungssystem hat die Benachrichtigung nicht an das Gerät übertragen.

Der erste Punkt, an dem eine Benachrichtigung verworfen werden kann (Benachrichtigung von Azure Communication Services wurde von Azure Notification Hubs nicht akzeptiert), ist unten beschrieben. Weitere Informationen finden Sie unter [Diagnostizieren verworfener Benachrichtigungen in Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Eine Möglichkeit zur Ermittlung, ob von Ihrer Communication Services-Ressource Benachrichtigungen an Azure Notification Hubs gesendet werden, ist die Metrik `incoming messages`, die Teil der verknüpften [Azure Notification Hub-Metriken](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs) ist.

Unten sind einige häufige Fehlkonfigurationen angegeben, die der Grund dafür sein können, warum Azure Notification Hub die Benachrichtigungen von Ihrer Communication Services-Ressource nicht akzeptiert.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure Notification Hub ist nicht mit der Communication Services-Ressource verknüpft

Es kann sein, dass Sie Ihren Azure Notification Hub nicht mit Ihrer Communication Services-Ressource verknüpft haben. Informationen zur Vorgehensweise beim Verknüpfen finden Sie im Abschnitt [Bereitstellen von Notification Hub](#notification-hub-provisioning).

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Der verknüpfte Azure Notification Hub wurde nicht konfiguriert

Sie müssen den verknüpften Notification Hub mit den Plattformbenachrichtigungssystem-Anmeldeinformationen für die Plattform (z. B. iOS oder Android) konfigurieren, die Sie verwenden möchten. Weitere Informationen zur Vorgehensweise finden Sie unter [Schnellstart: Einrichten von Pushbenachrichtigungen in einem Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>Der verknüpfte Azure Notification Hub ist nicht vorhanden

Der Azure Notification Hub, der mit Ihrer Communication Services-Ressource verknüpft ist, ist nicht mehr vorhanden. Überprüfen Sie, ob der verknüpfte Notification Hub noch vorhanden ist.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>Für die APNs-Plattform des Azure Notification Hubs wurde der Zertifikatauthentifizierungsmodus konfiguriert

Beachten Sie, dass die Verwendung der APNs-Plattform mit dem Zertifikatauthentifizierungsmodus derzeit nicht unterstützt wird. Konfigurieren Sie für die APNs-Plattform den Tokenauthentifizierungsmodus, wie dies unter [Schnellstart: Einrichten von Pushbenachrichtigungen in einem Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md) beschrieben ist.

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>Die verknüpfte Verbindungszeichenfolge verfügt nicht über die `Send`-Berechtigung

Die Verbindungszeichenfolge, die Sie zum Verknüpfen Ihres Notification Hubs mit Ihrer Communication Services-Ressource verwendet haben, muss über die Berechtigung vom Typ `Send` verfügen. Weitere Informationen dazu, wie Sie eine neue Verbindungszeichenfolge erstellen oder die aktuelle Verbindungszeichenfolge für Ihren Azure Notification Hub anzeigen können, finden Sie unter [Sicherheit von Notification Hubs](../../notification-hubs/notification-hubs-push-notification-security.md).

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>Die verknüpfte Verbindungszeichenfolge oder die Ressourcen-ID des Azure Notification Hubs ist ungültig

Stellen Sie sicher, dass Sie die Communication Services-Ressource mit der richtigen Verbindungszeichenfolge und Ressourcen-ID für den Azure Notification Hub konfigurieren.

#### <a name="the-linked-connection-string-is-regenerated"></a>Die verknüpfte Verbindungszeichenfolge wird erneut generiert

Falls Sie die Verbindungszeichenfolge Ihres verknüpften Azure Notification Hubs erneut generiert haben, müssen Sie diese mit der neuen Verbindungszeichenfolge Ihrer Communication Services-Ressource aktualisieren, indem Sie den Notification Hub neu verknüpfen. Dies ist unter [Bereitstellen von Notification Hub](#notification-hub-provisioning) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](../../event-grid/overview.md).
* Weitere Informationen zu den Azure Notification Hub-Konzepten finden Sie in der [Dokumentation zu Azure Notification Hubs](../../notification-hubs/index.yml).