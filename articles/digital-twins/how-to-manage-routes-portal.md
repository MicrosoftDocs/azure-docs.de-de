---
title: Verwalten von Endpunkten und Routen (Portal)
titleSuffix: Azure Digital Twins
description: Es wird beschrieben, wie Sie Endpunkte und Ereignisrouten für Azure Digital Twins-Daten im Azure-Portal einrichten und verwalten.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0f705aa61f1fe627dc0c8227242538e01ffce1d5
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070835"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-portal"></a>Verwalten von Endpunkten und Routen in Azure Digital Twins (Portal)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

In Azure Digital Twins können Sie [Ereignisbenachrichtigungen](how-to-interpret-event-data.md) an Downstreamdienste oder verbundene Computeressourcen weiterleiten. Dies erfolgt, indem zunächst **Endpunkte** eingerichtet werden, die die Ereignisse empfangen können. Anschließend können Sie [**Ereignisrouten**](concepts-route-events.md) erstellen, die angeben, welche von Azure Digital Twins generierten Ereignisse an welche Endpunkte übermittelt werden.

In diesem Artikel wird der Vorgang zum Erstellen von Endpunkten und Routen mit dem [Azure-Portal](https://portal.azure.com) Schritt für Schritt beschrieben.

Sie können Endpunkte und Routen alternativ auch mit den [Ereignisrouten-APIs](/rest/api/digital-twins/dataplane/eventroutes), den [SDKs](how-to-use-apis-sdks.md#overview-data-plane-apis) oder der [CLI von Azure Digital Twins](how-to-use-cli.md) verwalten. Eine Version dieses Artikels, in der diese Mechanismen anstelle des Portals verwendet werden, finden Sie unter [*Anleitung: Verwalten von Endpunkten und Routen in Azure Digital Twins (APIs und CLI)*](how-to-manage-routes-apis-cli.md).

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen ein **Azure-Konto** (Sie können [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ein kostenloses Konto einrichten).
* Sie benötigen eine **Azure Digital Twins-Instanz** in Ihrem Azure-Abonnement. Falls Sie noch keine Instanz besitzen, können Sie zum Erstellen die Schritte unter [*Vorgehensweise: Einrichten einer Instanz und von Authentifizierung*](how-to-set-up-instance-portal.md) verwenden. Notieren Sie sich die folgenden Werte aus dem Setup, um sie später in diesem Artikel zu verwenden:
    - Instanzname
    - Ressourcengruppe

Nachdem Sie Ihre Instanz eingerichtet haben, können Sie diese Details im [Azure-Portal](https://portal.azure.com) anzeigen. Melden Sie sich beim Portal an, und suchen Sie in der Suchleiste des Portals nach dem Namen Ihrer Instanz.
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Screenshot: Suchleiste im Azure-Portal" lightbox="media/how-to-manage-routes-portal/search-field-portal.png":::

Wählen Sie Ihre Instanz aus den Ergebnissen aus, damit diese Details auf der Seite „Übersicht“ für Ihre Instanz angezeigt werden:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Screenshot der Seite „Übersicht“ für eine Azure Digital Twins-Instanz. Der Name und die Ressourcengruppe sind hervorgehoben.":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Erstellen eines Endpunkts für Azure Digital Twins

Dies sind die unterstützten Typen von Endpunkten, die Sie für Ihre Instanz erstellen können:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Weitere Informationen zu den verschiedenen Endpunkttypen finden Sie unter [*Wählen zwischen Azure-Messagingdiensten*](../event-grid/compare-messaging-services.md).

In diesem Abschnitt wird erläutert, wie Sie einen dieser Endpunkte im [Azure-Portal](https://portal.azure.com) erstellen.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Erstellen des Endpunkts 

Sobald Sie die Endpunktressourcen erstellt haben, können Sie sie für einen Azure Digital Twins-Endpunkt verwenden. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Seite Ihrer Instanz, um einen neuen Endpunkt zu erstellen. Die Instanz finden Sie, indem Sie ihren Namen in die Suchleiste im Portal eingeben.

1. Wählen Sie im Instanzmenü die Option _Endpunkte_ aus. Wählen Sie anschließend auf der darauffolgenden Seite *Endpunkte* die Option *+ Endpunkt erstellen* aus. Dadurch wird die Seite *Endpunkt erstellen* geöffnet. Dort füllen Sie in den nächsten Schritten die Felder aus.

    :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Event Grid“" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-grid.png":::

1. Geben Sie einen **Namen** für Ihren Endpunkt ein, und wählen Sie den **Endpunkttyp**.

1. Geben Sie die restlichen für Ihren Endpunkttyp erforderlichen Informationen an, einschließlich Ihres Abonnements und der [oben](#prerequisite-create-endpoint-resources) beschriebenen Endpunktressourcen.
    1. Nur für Event Hub- und Service Bus-Endpunkte müssen Sie einen **Authentifizierungstyp** auswählen. Sie können die schlüsselbasierte Authentifizierung mit einer vorab erstellten Autorisierungsregel verwenden. Sie können auch die identitätsbasierte Authentifizierung verwenden, wenn Sie den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) für Ihre Azure Digital Twins-Instanz nutzen. 

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Event Hub“" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. Schließen Sie die Erstellung Ihres Endpunkts ab, indem Sie auf _Speichern_ klicken.

>[!IMPORTANT]
> Damit Sie die identitätsbasierte Authentifizierung erfolgreich für Ihren Endpunkt verwenden können, müssen Sie eine verwaltete Identität für Ihre Instanz erstellen, indem Sie die Schritte unter [*Aktivieren einer verwalteten Identität für die Weiterleitung von Azure Digital Twins-Ereignissen (Vorschau)* ](how-to-enable-managed-identities.md) befolgen.

Nach Erstellung Ihres Endpunkts können Sie überprüfen, ob der Endpunkt erfolgreich erstellt wurde, indem Sie das Benachrichtigungssymbol oben in der Leiste im Azure-Portal überprüfen: 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Screenshot: Benachrichtigung zur Überprüfung der Erstellung eines Endpunkts. Das Symbol in Form einer Glocke in der oberen Leiste des Portals ist ausgewählt, und es gibt eine Benachrichtigung, die „Endpoint ADT-eh-endpoint successfully created“ (Der Endpunkt „ADT-eh-endpoint“ wurde erfolgreich erstellt) lautet.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Falls bei der Erstellung des Endpunkts ein Fehler auftritt, sollten Sie die Fehlermeldung beachten und den Vorgang nach einigen Minuten wiederholen.

Sie können auch den Endpunkt anzeigen, der erstellt wurde, indem Sie auf der Seite *Endpunkte* für Ihre Azure Digital Twins-Instanz nachsehen.

Nun ist das Event Grid-, Event Hub- oder Service Bus-Thema als Endpunkt innerhalb von Azure Digital Twins unter dem Namen verfügbar, den Sie für den Endpunkt ausgewählt haben. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

### <a name="create-an-endpoint-with-dead-lettering"></a>Erstellen eines Endpunkts mit unzustellbaren Nachrichten

Wenn ein Endpunkt innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet.

Zum Erstellen eines Endpunkts mit aktivierten unzustellbaren Nachrichten müssen Sie die [CLI-Befehle](how-to-use-cli.md) oder die [APIs der Steuerungsebene](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) anstelle des Azure-Portals verwenden, um den Endpunkt zu erstellen.

Anweisungen dazu, wie dies mit diesen Tools funktioniert, finden Sie in der [*APIs und CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering)-Version dieses Artikels.

## <a name="create-an-event-route"></a>Erstellen einer Ereignisroute

Wenn Sie tatsächlich Daten von Azure Digital Twins an einen Endpunkt senden möchten, müssen Sie eine **Ereignisroute** definieren. Diese Routen ermöglichen es Entwicklern, den Ereignisdatenfluss im gesamten System und zu Downstreamdiensten einzurichten. Weitere Informationen zu Ereignisrouten finden Sie unter [*Konzepte: Weiterleiten von Azure Digital Twins-Ereignissen*](concepts-route-events.md).

**Voraussetzung**: Sie müssen wie weiter oben in diesem Artikel beschrieben Endpunkte erstellen, bevor Sie mit dem Erstellen einer Route fortfahren können. Nachdem die Einrichtung Ihrer Endpunkte abgeschlossen ist, können Sie mit dem Erstellen einer Ereignisroute fortfahren.

>[!NOTE]
>Wenn Sie Ihre Endpunkte gerade erst bereitgestellt haben, vergewissern Sie sich, dass die Bereitstellung abgeschlossen ist, **bevor** Sie versuchen, die Endpunkte für eine neue Ereignisroute zu verwenden. Falls Sie die Route nicht einrichten können, weil die Endpunkte nicht betriebsbereit sind, sollten Sie einige Minuten warten und den Vorgang dann wiederholen.

### <a name="creation-steps-with-the-azure-portal"></a>Erstellungsschritte mit dem Azure-Portal

Eine Ereignisroutendefinition enthält die folgenden Elemente:
* Den Routennamen, den Sie verwenden möchten.
* Den Namen des Endpunkts, den Sie verwenden möchten.
* Ein Filter, der definiert, welche Ereignisse an den Endpunkt gesendet werden.
    - Um die Route zu deaktivieren, damit keine Ereignisse gesendet werden, verwenden Sie den Filterwert `false`.
    - Um eine Route zu aktivieren, für die keine bestimmte Filterung gilt, verwenden Sie den Filterwert `true`.
    - Ausführliche Informationen zu allen anderen Filtertypen finden Sie unten im Abschnitt [*Filtern von Ereignissen*](#filter-events).

Eine einzelne Route kann es ermöglichen, dass mehrere Benachrichtigungen und Ereignistypen ausgewählt werden.

Navigieren Sie zum Erstellen einer Ereignisroute im [Azure-Portal](https://portal.azure.com) zur Detailseite für Ihre Azure Digital Twins-Instanz. (Sie können auf die Instanz zugreifen, indem Sie ihren Namen im Portal in der Suchleiste eingeben.)

Wählen Sie im Instanzmenü die Option _Ereignisrouten_ aus. Wählen Sie anschließend auf der Seite *Ereignisrouten* die Option *+ Create an event route* (+ Ereignisroute erstellen) aus. 

Wählen Sie auf der Seite *Create an event route* (Ereignisroute erstellen) mindestens Folgendes aus:
* Einen Namen für Ihre Route im Feld _Name_.
* Den _Endpunkt_, den Sie zum Erstellen der Route verwenden möchten. 

Zum Aktivieren der Route müssen Sie auch einen **Ereignisroutenfilter hinzufügen**, für den mindestens `true` festgelegt ist. (Wenn Sie den Standardwert `false` beibehalten, wird die Route erstellt, aber es werden keine Ereignisse dafür gesendet.) Verwenden Sie hierfür den Umschalter für _Erweiterter Editor_, um ihn zu aktivieren, und geben Sie im Feld *Filter* die Zeichenfolge `true` ein.

:::image type="content" source="media/how-to-manage-routes-portal/create-event-route-no-filter.png" alt-text="Screenshot: Erstellen einer Ereignisroute für Ihre Instanz" lightbox="media/how-to-manage-routes-portal/create-event-route-no-filter.png":::

Klicken Sie nach Abschluss des Vorgangs auf die Schaltfläche _Speichern_, um Ihre Ereignisroute zu erstellen.

## <a name="filter-events"></a>Filtern von Ereignissen

Wie oben beschrieben, verfügen Routen über das Feld **Filter**. Wenn der Filterwert für Ihre Route `false` lautet, werden keine Ereignisse an Ihren Endpunkt gesendet. 

Nachdem mindestens `true` als Filterwert ausgewählt wurde, erhalten Endpunkte verschiedene Ereignisse von Azure Digital Twins:
* Telemetrie, die von [digitalen Zwillingen](concepts-twins-graph.md) unter Verwendung der Azure Digital Twins-Dienst-API ausgelöst wird.
* Änderungsbenachrichtigungen für Zwillingseigenschaften, ausgelöst bei Eigenschaftsänderungen für einen Zwilling in der Azure Digital Twins-Instanz.
* Lebenszyklusereignisse, die ausgelöst werden, wenn Zwillinge oder Beziehungen erstellt oder gelöscht werden.

Sie können die Ereignistypen beschränken, die gesendet werden, indem Sie einen spezifischeren Filter definieren.

Verwenden Sie auf der Seite *Create an event route* (Ereignisroute erstellen) den Abschnitt _Add an event route filter_ (Ereignisroutenfilter hinzufügen), um beim Erstellen einer Ereignisroute einen Ereignisfilter hinzuzufügen. 

Sie können entweder eine Auswahl aus einigen grundlegenden allgemeinen Filteroptionen treffen oder die erweiterten Filteroptionen verwenden, um Ihre eigenen benutzerdefinierten Filter zu schreiben.

#### <a name="use-the-basic-filters"></a>Verwenden der grundlegenden Filter

Erweitern Sie zur Verwendung der grundlegenden Filter die Option _Ereignistypen_, und wählen Sie die Kontrollkästchen für die Ereignisse aus, die Sie an Ihren Endpunkt senden möchten. 

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-1.png" alt-text="Screenshot: Erstellen einer Ereignisroute mit einem einfachen Filter. Aktivieren der Kontrollkästchen für die Ereignisse.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dies führt dazu, dass das Filtertextfeld automatisch mit dem Text des von Ihnen ausgewählten Filters gefüllt wird:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-basic-2.png" alt-text="Screenshot: Erstellen einer Ereignisroute mit einem einfachen Filter. Automatisch eingefügter Filtertext nach der Auswahl der Ereignisse.":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

#### <a name="use-the-advanced-filters"></a>Verwenden der erweiterten Filter

Alternativ können Sie die erweiterte Filteroption verwenden, um Ihre eigenen benutzerdefinierten Filter zu schreiben.

Aktivieren Sie _Erweiterter Editor_ mit dem Umschalter, um ihn zu aktivieren, wenn Sie eine Ereignisroute mit erweiterten Filteroptionen erstellen möchten. Sie können im Feld *Filter* dann Ihre eigenen Ereignisfilter schreiben:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-event-route-filter-advanced.png" alt-text="Screenshot: Erstellen einer Ereignisroute mit einem erweiterten Filter":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Dies sind die unterstützten Routenfilter. Die Details in der Spalte *Filtertextschema* sind der Text, der in das Filterfeld eingegeben werden kann.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die verschiedenen Arten von Ereignismeldungen, die Sie erhalten können:
* [*Gewusst wie: Interpretieren von Ereignisdaten*](how-to-interpret-event-data.md)
