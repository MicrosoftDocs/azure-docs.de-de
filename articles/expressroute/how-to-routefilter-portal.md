---
title: 'Konfigurieren von Routenfiltern für das Microsoft-Peering: Azure ExpressRoute – Portal | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie mithilfe des Azure-Portals Routenfilter für das Microsoft-Peering konfiguriert werden.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0515b5e85c3bcf56f1f238620d6036d1be0bec7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60839282"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Konfigurieren von Routenfiltern für das Microsoft-Peering: Azure-Portal
> [!div class="op_single_selector"]
> * [Azure-Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-Befehlszeilenschnittstelle](how-to-routefilter-cli.md)
> 

Routenfilter stellen eine Möglichkeit dar, um eine Teilmenge von unterstützten Diensten durch das Microsoft-Peering zu nutzen. Die in diesem Artikel erläuterten Schritte unterstützen Sie bei der Konfiguration und Verwaltung von Routenfiltern für ExpressRoute-Verbindungen.

Das Microsoft-Peering ermöglicht den Zugriff auf Dynamics 365-Dienste und Office 365-Dienste wie Exchange Online, SharePoint Online und Skype for Business sowie Azure-Dienste wie Storage und SQL-Datenbank. Wenn das Microsoft-Peering in einer ExpressRoute-Verbindung konfiguriert ist, werden alle Präfixe im Zusammenhang mit diesen Diensten über BGP-Sitzungen angekündigt, die eingerichtet werden. Jedem Präfix wird zur Identifizierung des Diensts, der über das Präfix angeboten wird, ein BGP-Communitywert angefügt. Eine Liste der BGP-Communitywerte und der Dienste, denen sie zugeordnet sind, finden Sie unter [BGP-Communitys](expressroute-routing.md#bgp).

Wenn Konnektivität mit allen Diensten erforderlich ist, wird eine große Anzahl von Präfixen über BGP angekündigt. Dadurch erhöht sich deutlich die Größe der Routentabellen, die von Routern innerhalb Ihres Netzwerks verwaltet werden. Wenn Sie nur eine Teilmenge der Dienste nutzen möchten, die über das Microsoft-Peering angeboten werden, können Sie die Routentabellen auf zwei Arten verringern. Ihre Möglichkeiten:

- Sie filtern unerwünschte Präfixe heraus, indem Sie Routenfilter auf BGP-Communitys anwenden. Dies ist eine standardmäßige Vorgehensweise bei Netzwerken, die bei zahlreichen Netzwerken zum Einsatz kommt.

- Sie definieren Routenfilter und wenden sie auf Ihre ExpressRoute-Verbindung an. Ein Routenfilter ist eine neue Ressource, mit der Sie die Liste der Dienste, die Sie über das Microsoft-Peering nutzen möchten, auswählen können. ExpressRoute-Router senden lediglich die Liste der Präfixe, die den im Routenfilter identifizierten Diensten zugehörig sind.

### <a name="about"></a>Informationen zu Routenfiltern

Wenn das Microsoft-Peering für Ihre ExpressRoute-Verbindung konfiguriert ist, stellen die Microsoft-Edgerouter ein BGP-Sitzungspaar mit den Edgeroutern (Ihrem Edgerouter oder dem Ihres Konnektivitätsanbieters) her. Ihrem Netzwerk werden keine Routen angekündigt. Um Routenankündigungen für Ihr Netzwerk zu aktivieren, müssen Sie einen Routenfilter zuordnen.

Durch einen Routenfilter können Sie die Dienste identifizieren, die Sie über das Microsoft-Peering Ihrer ExpressRoute-Verbindung nutzen möchten. Im Wesentlichen handelt es sich um eine Whitelist aller BGP-Communitywerte. Sobald eine Routenfilterressource definiert und an eine ExpressRoute-Verbindung angefügt ist, werden Ihrem Netzwerk alle Präfixe angekündigt, die den BGP-Communitywerten zugeordnet sind.

Um Routenfilter mit Office 365-Diensten anfügen zu können, müssen Sie die Autorisierung zur Nutzung von Office 365-Diensten über ExpressRoute besitzen. Wenn Sie nicht zur Nutzung von Office 365-Diensten über ExpressRoute autorisiert sind, tritt beim Vorgang zum Anfügen von Routenfiltern ein Fehler auf. Weitere Informationen zum Autorisierungsvorgang finden Sie unter [Azure ExpressRoute für Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd). Für die Konnektivität mit Dynamics 365-Diensten ist KEINE vorherige Autorisierung erforderlich.

> [!IMPORTANT]
> Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind. Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde.
> 
> 

### <a name="workflow"></a>Workflow

Um mit dem Microsoft-Peering eine Verbindung mit Diensten herstellen zu können, müssen Sie die folgenden Konfigurationsschritte durchführen:

- Sie benötigen eine aktive ExpressRoute-Verbindung, für die das Microsoft-Peering bereitgestellt ist. Zur Durchführung dieser Aufgaben können Sie folgende Anweisungen befolgen:
  - [Erstellen Sie eine ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md), und lassen Sie sie von Ihrem Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss den Zustand „Provisioned“ und „Enabled“ aufweisen.
  - [Erstellen Sie das Microsoft-Peering](expressroute-howto-routing-portal-resource-manager.md), wenn Sie die BGP-Sitzung direkt verwalten. Überlassen Sie die Bereitstellung des Microsoft-Peerings für Ihre Verbindung wahlweise Ihrem Konnektivitätsanbieter.

-  Sie müssen einen Routenfilter erstellen und konfigurieren.
    - Identifizieren Sie die Dienste, die Sie über das Microsoft-Peering nutzen möchten.
    - Identifizieren Sie die Liste der BGP-Communitywerte, die den Diensten zugeordnet sind.
    - Erstellen Sie eine Regel, um die den BGP-Communitywerten entsprechende Präfixliste zuzulassen.

-  Sie müssen den Routenfilter der ExpressRoute-Verbindung anfügen.

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie mit der Konfiguration beginnen, vergewissern Sie sich, dass Sie folgende Kriterien erfüllen:

 - Lesen Sie vor Beginn der Konfiguration die Seiten zu den [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

 - Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss den Zustand „Provisioned“ und „Enabled“ aufweisen.

 - Das Microsoft-Peering muss aktiviert sein. Befolgen Sie die Anweisungen unter [Erstellen und Ändern der Peeringkonfiguration](expressroute-howto-routing-portal-resource-manager.md).


## <a name="prefixes"></a>Schritt 1: Abrufen einer Liste von Präfixen und BGP-Communitywerten

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Abrufen einer Liste von BGP-Communitywerten

BGP-Communitywerte, die Diensten zugeordnet sind, die über Microsoft-Peering verfügbar sind, finden Sie auf der Seite [ExpressRoute-Routinganforderungen](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Erstellen einer Liste der zu verwendenden Werte

Erstellen Sie eine Liste von BGP-Communitywerten, die Sie im Routenfilter verwenden möchten. Der BGP-Communitywert für Dynamics 365-Dienste lautet beispielsweise „12076:5040“.

## <a name="filter"></a>Schritt 2: Erstellen eines Routenfilters und einer Filterregel

Ein Routenfilter kann nur eine Regel aufweisen, die zudem vom Typ „Zulassen“ sein muss. Diese Regel kann eine Liste von BGP-Communitywerten enthalten, die ihr zugeordnet sind.

### <a name="1-create-a-route-filter"></a>1. Erstellen eines Routenfilters
Sie können einen Routenfilter erstellen, indem Sie die Option zum Erstellen einer neuen Ressource auswählen. Klicken Sie auf **Ressource erstellen** > **Netzwerk** > **Routenfilter**, wie in der Abbildung unten gezeigt:

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Sie müssen den Routenfilter in einer Ressourcengruppe platzieren. 

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Erstellen einer Filterregel

Sie können Regeln hinzufügen und aktualisieren, indem Sie die Registerkarte „Regel verwalten“ für Ihren Routenfilter auswählen.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Sie können die Dienste, mit denen Sie eine Verbindung herstellen möchten, aus der Dropdownliste auswählen und abschließend die Regel speichern.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="attach"></a>Schritt 3: Anfügen des Routenfilters zu einer ExpressRoute-Verbindung

Sie können den Routenfilter einer Leitung anfügen, indem Sie die Schaltfläche „Leitung hinzufügen“ und dann die ExpressRoute-Leitung aus der Dropdownliste auswählen.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Wenn der Konnektivitätsanbieter Peering für Ihre ExpressRoute-Leitung konfiguriert, aktualisieren Sie die ExpressRoute-Leitung auf dem entsprechenden Blatt, bevor Sie die Schaltfläche „Leitung hinzufügen“ auswählen.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="tasks"></a>Häufige Aufgaben

### <a name="getproperties"></a>Abrufen der Eigenschaften eines Routenfilters

Sie können Eigenschaften eines Routenfilters anzeigen, wenn Sie die Ressource im Portal öffnen.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="updateproperties"></a>Aktualisieren der Eigenschaften eines Routenfilters

Sie können die Liste der BGP-Communitywerte, die einer Leitung angefügt sind, durch Auswählen der Schaltfläche „Regel verwalten“ aktualisieren.


![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="detach"></a>Trennen eines Routenfilters von einer ExpressRoute-Verbindung

Um eine Verbindung über den Routenfilter zu trennen, klicken Sie mit der rechten Maustaste auf die Verbindung, und klicken Sie dann auf „Zuordnung aufheben“.

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="delete"></a>Löschen eines Routenfilters

Sie können einen Routenfilter durch Auswahl der Schaltfläche „Löschen“ löschen. 

![Erstellen eines Routenfilters](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).