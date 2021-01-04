---
title: Verwenden eines externen Caches in Azure API Management | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen externen Cache in Azure API Management konfigurieren und verwenden. Durch die Verwendung eines externen Caches lassen sich einige Einschränkungen des integrierten Caches umgehen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 6288a10e111e42629abf5e09b84a6a7791dcfe95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018221"
---
# <a name="use-an-external-redis-compatible-cache-in-azure-api-management"></a>Verwenden eines externen Redis-kompatiblen Caches in Azure API Management

Neben der Nutzung des integrierten Caches ermöglicht Azure API Management das Zwischenspeichern von Antworten in einem externen, Redis-kompatiblen Cache, z. B. eine Azure Cache for Redis.

Durch die Verwendung eines externen Caches lassen sich einige Einschränkungen des integrierten Caches umgehen:

* Vermeidung der regelmäßigen Löschung Ihres Caches im Zuge von API Management-Updates
* Mehr Kontrolle über Ihre Cachekonfiguration
* Zwischenspeicherung von mehr Daten als mit dem API Management-Tarif
* Verwendung der Zwischenspeicherung mit dem Verbrauchstarif von API Management
* Aktivieren der Zwischenspeicherung in den [selbstgehosteten Gateways in API Management](self-hosted-gateway-overview.md)

Ausführlichere Informationen zum Zwischenspeichern finden Sie unter [Cacherichtlinien für API Management](api-management-caching-policies.md) sowie unter [Benutzerdefiniertes Caching in Azure API Management](api-management-sample-cache-by-key.md).

![Verwenden eines eigenen Caches in API Management](media/api-management-howto-cache-external/overview.png)

Sie lernen Folgendes:

> [!div class="checklist"]
> * Hinzufügen eines externen Caches in API Management

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

+ [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Verständnis der [Zwischenspeicherung in Azure API Management](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a>Erstellen von Azure Cache for Redis

In diesem Abschnitt erfahren Sie, wie Sie eine Azure Cache for Redis-Instanz in Azure erstellen. Falls Sie bereits über eine Azure Cache for Redis-Instanz verfügen (innerhalb oder außerhalb von Azure), können Sie <a href="#add-external-cache">direkt mit dem nächsten Abschnitt fortfahren</a>.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="deploy-redis-cache-to-kubernetes"></a><a name="create-cache"> </a> Bereitstellen von Redis Cache in Kubernetes

Bei der Zwischenspeicherung verwenden selbstgehostete Gateways ausschließlich externe Caches. Damit die Zwischenspeicherung effektiv ist, müssen sich selbstgehostete Gateways sowie der Cache, auf dem sie basieren, nah beieinander befinden, um Wartezeiten beim Nachschlagen und der Speicherung zu minimieren. Das Bereitstellen eines Redis Cache im selben Kubernetes-Cluster oder in einem gesonderten Cluster in der Nähe sind die besten Optionen. Folgen Sie diesem [Link](https://github.com/kubernetes/examples/tree/master/guestbook), um zu erfahren, wie Sie Redis Cache in einem Kubernetes-Cluster bereitstellen.

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Hinzufügen eines externen Caches

Gehen Sie wie folgt vor, um in Azure API Management eine externe Azure Cache for Redis-Instanz hinzuzufügen:

![Screenshot des Hinzufügens einer externen Azure Cache for Redis-Instanz in Azure API Management](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> Die Einstellung **Use from** (Verwenden aus) gibt eine Azure-Region oder den Standort eines selbstgehosteten Gateways an, von dem der konfigurierte Cache verwendet wird. Die als **Standard** konfigurierten Caches werden von Caches mit einem spezifischen übereinstimmenden Regions- oder Standortwert außer Kraft gesetzt.
>
> Ein Beispiel: Angenommen, API Management wird in den Regionen „USA, Osten“, „Asien, Südosten“ und „Europa, Westen“ gehostet, und es sind zwei Caches konfiguriert: einer für **Standard** und einer für **Asien, Südosten**. In diesem Fall verwendet API Management in **Asien, Südosten** den eigenen Cache, während in den anderen beiden Regionen der Cacheeintrag **Standard** verwendet wird.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Hinzufügen einer Azure Cache for Redis-Instanz aus dem gleichen Abonnement

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
2. Wählen Sie im Menü auf der linken Seite die Registerkarte **External cache** (Externer Cache) aus.
3. Klicken Sie auf die Schaltfläche **+ Hinzufügen**.
4. Wählen Sie im Dropdownfeld **Cache instance** (Cacheinstanz) Ihren Cache aus.
5. Wählen Sie **Standard** aus, oder geben Sie im Dropdownfeld **Use from** (Verwenden aus) die gewünschte Region an.
6. Klicken Sie auf **Speichern**.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Hinzufügen einer Azure Cache for Redis-Instanz, die außerhalb des aktuellen Azure-Abonnements oder außerhalb von Azure gehostet wird

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
2. Wählen Sie im Menü auf der linken Seite die Registerkarte **External cache** (Externer Cache) aus.
3. Klicken Sie auf die Schaltfläche **+ Hinzufügen**.
4. Wählen Sie im Dropdownfeld **Cache instance** (Cacheinstanz) die Option **Benutzerdefiniert** aus.
5. Wählen Sie **Standard** aus, oder geben Sie im Dropdownfeld **Use from** (Verwenden aus) die gewünschte Region an.
6. Geben Sie im Feld **Verbindungszeichenfolge** die Verbindungszeichenfolge Ihrer Azure Cache for Redis-Instanz an.
7. Klicken Sie auf **Speichern**.

### <a name="add-a-redis-cache-to-a-self-hosted-gateway"></a>Hinzufügen eines Redis Cache zu einem selbstgehosteten Gateway

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
2. Wählen Sie im Menü auf der linken Seite die Registerkarte **External cache** (Externer Cache) aus.
3. Klicken Sie auf die Schaltfläche **+ Hinzufügen**.
4. Wählen Sie im Dropdownfeld **Cache instance** (Cacheinstanz) die Option **Benutzerdefiniert** aus.
5. Geben Sie den gewünschten Standort des selbstgehosteten Gateways oder **Standard** im Dropdownfeld **Use from** (Verwenden aus) an.
6. Geben Sie im Feld **Verbindungszeichenfolge** die Verbindungszeichenfolge Ihres Redis-Caches an.
7. Klicken Sie auf **Speichern**.

## <a name="use-the-external-cache"></a>Verwenden des externen Caches

Nachdem der externe Cache in Azure API Management konfiguriert wurde, kann er über Zwischenspeicherungsrichtlinien verwendet werden. Ausführliche Informationen finden Sie unter [Hinzufügen der Zwischenspeicherung zum Verbessern der Leistung in Azure API Management](api-management-howto-cache.md).

## <a name="next-steps"></a><a name="next-steps"> </a>Nächste Schritte

* Weitere Informationen zu Richtlinien für die Zwischenspeicherung finden Sie unter [Richtlinien für die Zwischenspeicherung][Caching policies] in der [Richtlinienreferenz für API Management][API Management policy reference].
* Informationen zum Zwischenspeichern von Elementen nach Schlüssel mithilfe von Richtlinienausdrücken finden Sie unter [Benutzerdefiniertes Zwischenspeichern in Azure API Management](api-management-sample-cache-by-key.md).

[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-caching-policies.md
