---
title: Verwalten von Endpunkten und Routen (Portal)
titleSuffix: Azure Digital Twins
description: Es wird beschrieben, wie Sie Endpunkte und Ereignisrouten für Azure Digital Twins-Daten im Azure-Portal einrichten und verwalten.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 083d868f2d2652be9480227c29dfb289564056d6
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533785"
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
 
:::image type="content" source="media/how-to-manage-routes-portal/search-field-portal.png" alt-text="Screenshot: Suchleiste im Azure-Portal":::

Wählen Sie Ihre Instanz in den Ergebnissen aus, um die Detailseite für Ihre Instanz anzuzeigen:

:::image type="content" source="media/how-to-manage-routes-portal/instance-details.png" alt-text="Screenshot: Details zur ADT-Instanz" border="false":::

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Erstellen eines Endpunkts für Azure Digital Twins

Dies sind die unterstützten Typen von Endpunkten, die Sie für Ihre Instanz erstellen können:
* [Event Grid](../event-grid/overview.md) 
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Weitere Informationen zu den verschiedenen Endpunkttypen finden Sie unter [*Wählen zwischen Azure-Messagingdiensten*](../event-grid/compare-messaging-services.md).

Um einen Endpunkt mit Azure Digital Twins zu verknüpfen, muss das für den Endpunkt verwendete Event Grid-Thema, der Event Hub oder Service Bus bereits vorhanden sein. 

### <a name="create-an-event-grid-endpoint"></a>Erstellen eines Event Grid-Endpunkts

**Voraussetzung**: Erstellen Sie ein Event Grid-Thema, indem Sie die folgenden Schritte im Abschnitt [*Erstellen eines benutzerdefinierten Themas* der Schnellstartanleitung](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)*Benutzerdefinierte Ereignisse* für die Event Grid-Instanz befolgen.

Nachdem Sie das Thema erstellt haben, können Sie im [Azure-Portal](https://portal.azure.com) auf der Seite für Ihre Azure Digital Twins-Instanz dafür einen Link einrichten. (Sie finden die Instanz, indem Sie ihren Namen in die Suchleiste des Portals eingeben.)

Wählen Sie im Instanzmenü die Option _Endpunkte_ aus. Wählen Sie anschließend auf der darauffolgenden Seite *Endpunkte* die Option *+ Endpunkt erstellen* aus. 

Auf der geöffneten Seite *Endpunkt erstellen* können Sie einen Endpunkt vom Typ _Event Grid_ erstellen, indem Sie das entsprechende Optionsfeld auswählen. Geben Sie die anderen Details an: Geben Sie im Feld _Name_ einen Namen für Ihren Endpunkt ein, wählen Sie in der Dropdownliste Ihr _Abonnement_ aus, und wählen Sie in der dritten Dropdownliste dann Ihr vorab erstelltes _Event Grid-Thema_ aus.

Erstellen Sie anschließend Ihren Endpunkt, indem Sie _Speichern_ auswählen.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-grid.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Event Grid“":::

Sie können überprüfen, ob die Erstellung des Endpunkts erfolgreich war, indem Sie sich in der obersten Leiste im Azure-Portal das Benachrichtigungssymbol ansehen: 

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-notifications.png" alt-text="Screenshot: Benachrichtigung zur Überprüfung der Erstellung eines Endpunkts" border="false":::

Sie können auch den Endpunkt anzeigen, der erstellt wurde, indem Sie auf der Seite *Endpunkte* für Ihre Azure Digital Twins-Instanz nachsehen.

Falls bei der Erstellung des Endpunkts ein Fehler auftritt, sollten Sie die Fehlermeldung beachten und den Vorgang nach einigen Minuten wiederholen.

Nun ist das Event Grid-Thema als Endpunkt innerhalb von Azure Digital Twins unter dem Namen verfügbar, der im Feld _Name_ angegeben ist. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

### <a name="create-an-event-hubs-endpoint"></a>Erstellen eines Event Hubs-Endpunkts

**Voraussetzungen:** 
* Sie benötigen einen _Event Hubs-Namespace_ und einen _Event Hub_. Erstellen Sie diese beiden Komponenten, indem Sie die Schritte in der Event Hubs-Schnellstartanleitung unter [*Erstellen eines Event Hubs mithilfe des Azure-Portals*](../event-hubs/event-hubs-create.md) befolgen.
* Sie benötigen eine _Autorisierungsregel_. Informationen zur Erstellung finden Sie im Event Hubs-Artikel [*Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signatures*](../event-hubs/authorize-access-shared-access-signature.md).

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Detailseite für Ihre Azure Digital Twins-Instanz. (Sie können darauf zugreifen, indem Sie den Namen im Portal in der Suchleiste eingeben.)

Wählen Sie im Instanzmenü die Option _Endpunkte_ aus. Wählen Sie anschließend auf der darauffolgenden Seite *Endpunkte* die Option *+ Endpunkt erstellen* aus. 

Auf der geöffneten Seite *Endpunkt erstellen* können Sie einen Endpunkt vom Typ _Event Hub_ erstellen, indem Sie das entsprechende Optionsfeld auswählen. Geben Sie im Feld _Name_ einen Namen für Ihren Endpunkt ein. Wählen Sie anschließend Ihr _Abonnement_ und in den entsprechenden Dropdownlisten die vorab erstellten Elemente _Event Hub-Namespace_, _Event Hub_ und _Autorisierungsregel_ aus.

Erstellen Sie anschließend Ihren Endpunkt, indem Sie _Speichern_ auswählen.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Event Hub“":::

Sie können überprüfen, ob die Erstellung des Endpunkts erfolgreich war, indem Sie sich in der obersten Leiste im Azure-Portal das Benachrichtigungssymbol ansehen. 

Falls bei der Erstellung des Endpunkts ein Fehler auftritt, sollten Sie die Fehlermeldung beachten und den Vorgang nach einigen Minuten wiederholen.

Nun ist das Event Hub-Thema als Endpunkt innerhalb von Azure Digital Twins unter dem Namen verfügbar, der im Feld _Name_ angegeben ist. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

### <a name="create-a-service-bus-endpoint"></a>Erstellen eines Service Bus-Endpunkts

**Voraussetzungen:** 
* Sie benötigen einen _Service Bus-Namespace_ und ein _Service Bus-Thema_. Erstellen Sie diese beiden Komponenten, indem Sie die Schritte in der Service Bus-Schnellstartanleitung unter [*Erstellen von Themen und Abonnements*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) befolgen. Es ist nicht erforderlich, dass Sie den Abschnitt [*Erstellen von Abonnements für das Thema*](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md#create-subscriptions-to-the-topic) durcharbeiten.
* Sie benötigen eine _Autorisierungsregel_. Informationen zur Erstellung finden Sie im Service Bus-Artikel zur [*Authentifizierung und Autorisierung*](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Detailseite für Ihre Azure Digital Twins-Instanz. (Sie können darauf zugreifen, indem Sie den Namen im Portal in der Suchleiste eingeben.)

Wählen Sie im Instanzmenü die Option _Endpunkte_ aus. Wählen Sie anschließend auf der darauffolgenden Seite *Endpunkte* die Option *+ Endpunkt erstellen* aus. 

Auf der Seite *Endpunkt erstellen*, die geöffnet wird, können Sie einen Endpunkt vom Typ _Service Bus_ erstellen, indem Sie das entsprechende Optionsfeld auswählen. Geben Sie im Feld _Name_ einen Namen für Ihren Endpunkt ein. Wählen Sie anschließend Ihr _Abonnement_ und in den entsprechenden Dropdownlisten die vorab erstellten Elemente _Service Bus-Namespace_, _Service Bus-Thema_ und _Autorisierungsregel_ aus.

Erstellen Sie anschließend Ihren Endpunkt, indem Sie _Speichern_ auswählen.

:::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-service-bus.png" alt-text="Screenshot: Erstellen eines Endpunkts vom Typ „Service Bus“":::

Sie können überprüfen, ob die Erstellung des Endpunkts erfolgreich war, indem Sie sich in der obersten Leiste im Azure-Portal das Benachrichtigungssymbol ansehen. 

Falls bei der Erstellung des Endpunkts ein Fehler auftritt, sollten Sie die Fehlermeldung beachten und den Vorgang nach einigen Minuten wiederholen.

Nun ist das Service Bus-Thema als Endpunkt innerhalb von Azure Digital Twins unter dem Namen verfügbar, der im Feld _Name_ angegeben ist. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

### <a name="create-an-endpoint-with-dead-lettering"></a>Erstellen eines Endpunkts mit unzustellbaren Nachrichten

Wenn ein Endpunkt innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet.

Zum Erstellen eines Endpunkts mit aktivierten unzustellbaren Nachrichten müssen Sie die [ARM-APIs](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) anstelle des Azure-Portals verwenden, um den Endpunkt zu erstellen.

Anweisungen dazu, wie dies mit den APIs funktioniert, finden Sie in der Version [*APIs und CLI*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering) dieses Artikels.

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
