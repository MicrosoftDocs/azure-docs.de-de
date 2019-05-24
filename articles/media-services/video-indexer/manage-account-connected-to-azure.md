---
title: Verwalten eines Video Indexer-Kontos
titlesuffix: Azure Media Services
description: In diesem Artikel wird veranschaulicht, wie Sie ein Video Indexer-Konto verwalten, das über eine Verbindung mit Azure verfügt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 37aef7c0ae06aefc69b8a72a80c8ade3951e8b0d
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799520"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Verwalten eines Video Indexer-Kontos mit Azure-Verbindung

In diesem Artikel wird veranschaulicht, wie Sie ein Video Indexer-Konto verwalten, das mit Ihrem Azure-Abonnement und einem Azure Media Services-Konto verbunden ist.

> [!NOTE]
> Sie müssen der Inhaber des Video Indexer-Kontos sein, um die hier beschriebenen Anpassungen an der Kontokonfiguration vornehmen zu können.

## <a name="prerequisites"></a>Voraussetzungen

Verbinden Sie Ihr Video Indexer-Konto mit Azure, wie in [Erstellen eines Video Indexer-Kontos mit Azure-Verbindung](connect-to-azure.md) beschrieben. 

Beachten Sie die [Voraussetzungen](connect-to-azure.md#prerequisites), und machen Sie sich mit den [Überlegungen](connect-to-azure.md#considerations) in diesem Artikel vertraut.

## <a name="examine-account-settings"></a>Untersuchen der Kontoeinstellungen

In diesem Abschnitt werden die Einstellungen für Ihr Video Indexer-Konto untersucht.

So zeigen Sie die Einstellungen an:

1. Klicken Sie auf das Benutzersymbol in der oberen rechten Ecke, und wählen Sie **Einstellungen** aus.

    ![Einstellungen](./media/manage-account-connected-to-azure/select-settings.png)

2. Wählen Sie auf der Seite **Einstellungen** die Registerkarte **Weiter** aus.

Wenn Ihr Videos Indexer-Konto mit Azure verbunden ist, sehen Sie Folgendes:

* Der Name des zugrunde liegenden Azure Media Services-Kontos.
* Die Anzahl der aktuell ausgeführten und sich in der Warteschlange befindlichen Indizierungsaufträge.
* Die Anzahl und den Typ der zugeordneten reservierten Einheiten.

Wenn für Ihr Konto einige Anpassungen erforderlich ist, werden auf der Seite **Einstellungen** entsprechende Fehler- und Warnmeldungen zu Ihrer Kontokonfiguration angezeigt. Die Nachrichten enthalten Links zu den genauen Stellen im Azure-Portal, in denen Sie Änderungen vornehmen müssen. Weitere Informationen finden Sie im folgenden Abschnitt [Fehler und Warnungen](#errors-and-warnings).

## <a name="auto-scale-reserved-units"></a>Automatische Skalierung reservierter Einheiten

Auf der Seite **Einstellungen** können Sie die automatische Skalierung von reservierten Einheiten (RU) für Medien festlegen. Wenn die Option **Ein** lautet, können Sie die maximale Anzahl der RUs zuweisen und sicherstellen, dass der Video Indexer die RUs automatisch stoppt/startet. Mit dieser Option zahlen Sie nicht zusätzlich für Leerlaufzeiten zahlen, aber warten auch nicht darauf, dass die Indizierungsaufträge über einen längeren Zeitraum ausgeführt werden, wenn die Indizierungslast hoch ist.

Bei der automatischen Skalierung ist keine Skalierung unter 1 RU oder über dem Standardlimit das Media Services-Konto möglich. Erstellen Sie einen Service Request, um dieses Limit zu erhöhen. Informationen zu Kontingenten und Einschränkungen sowie zum Öffnen von Supporttickets finden Sie unter [Kontingente und Einschränkungen](../../media-services/previous/media-services-quotas-and-limitations.md).

![Registrieren](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Fehler und Warnungen

Wenn für Ihr Konto einige Anpassungen erforderlich ist, werden auf der Seite **Einstellungen** entsprechende Fehler- und Warnmeldungen zu Ihrer Kontokonfiguration angezeigt. Die Nachrichten enthalten Links zu den genauen Stellen im Azure-Portal, in denen Sie Änderungen vornehmen müssen. Dieser Abschnitt enthält weitere Informationen zu Fehler- und Warnmeldungen.

* Event Grid

    Sie müssen den EventGrid-Ressourcenanbieter über das Azure-Portal registrieren. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu **Abonnements** > [Abonnement] > **ResourceProviders** > **Microsoft.EventGrid**. Klicken Sie auf **Registrieren**, falls der Status nicht **Registriert** lautet. Die Registrierung nimmt einige Minuten in Anspruch. 

* Streamingendpunkt

    Stellen Sie sicher, dass sich der standardmäßige **Streamingendpunkt** des zugrunde liegenden Media Services-Kontos im Status „Gestartet“ befindet. Andernfalls ist es für Sie nicht möglich, Videos über dieses Media Services-Konto oder in Video Indexer anzusehen.

* Reservierte Einheiten für Medien 

    Sie müssen reservierte Einheiten für Medien für Ihre Media Services-Ressource zuordnen, um Video indizieren zu können. Für die optimale Indizierungsleistung wird empfohlen, mindestens 10 reservierte S3-Einheiten zuzuordnen. Preisinformationen finden Sie im Abschnitt mit den häufig gestellten Fragen auf der Seite [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).   

## <a name="next-steps"></a>Nächste Schritte

Sie können programmgesteuert mit Ihrem Testkonto bzw. Ihren Video Indexer-Konten interagieren, die mit Azure verbunden sind. Befolgen Sie hierzu die Anweisungen unter [Verwenden von APIs](video-indexer-use-apis.md).

Sie sollten hierfür denselben Azure AD-Benutzer verwenden, den Sie beim Herstellen der Verbindung mit Azure genutzt haben.
