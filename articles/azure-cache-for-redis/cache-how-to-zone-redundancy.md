---
title: Aktivieren der Zonenredundanz für Azure Cache for Redis (Vorschau)
description: Hier erfahren Sie, wie Sie Zonenredundanz für Ihre Azure Cache for Redis-Instanzen im Premium- und Enterprise-Tarif einrichten.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 0cb7ee5b9fa02e726d03bf1ae9935c07ded6e4a6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088011"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis-preview"></a>Aktivieren der Zonenredundanz für Azure Cache for Redis (Vorschau)
In diesem Artikel erfahren Sie, wie Sie eine zonenredundante Azure Cache-Instanz über das Azure-Portal konfigurieren.

Azure Cache for Redis im Standard-, Premium- und Enterprise-Tarif bietet integrierte Redundanz durch das Hosting jedes Caches auf zwei dedizierten virtuellen Computern (VMs). Obwohl sich diese VMs in separaten [Azure-Fehler- und Updatedomänen befinden](../virtual-machines/manage-availability.md) und hoch verfügbar sind, sind Sie anfällig für Ausfälle auf Rechenzentrumsebene. Azure Cache for Redis unterstützt auch Zonenredundanz im Premium- und Enterprise-Tarif. Ein zonenredundanter Cache wird auf VMs ausgeführt, die auf mehrere [Verfügbarkeitszonen](../virtual-machines/manage-availability.md#use-availability-zones-to-protect-from-datacenter-level-failures) verteilt sind. Dies bietet höhere Resilienz und Verfügbarkeit.

## <a name="prerequisites"></a>Voraussetzungen
* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)

> [!NOTE]
> Diese Funktion befindet sich zurzeit in der Vorschauphase. [Wenden Sie sich an uns](mailto:azurecache@microsoft.com), wenn Sie daran interessiert sind.
>

## <a name="create-a-cache"></a>Erstellen eines Caches
Führen Sie die folgenden Schritte aus, um einen Cache zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie dann **Ressource erstellen** aus.
  
1. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann **Azure Cache for Redis** aus.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Auswählen von „Azure Cache for Redis“.":::
   
1. Konfigurieren Sie auf der Seite **Grundlagen** die Einstellungen für den neuen Cache.
   
    | Einstellung      | Vorgeschlagener Wert  | Beschreibung |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Wählen Sie Ihr Abonnement aus. | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
    | **Ressourcengruppe** | Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für eine neue Ressourcengruppe ein. | Der Name der Ressourcengruppe, in der Ihr Cache und weitere Ressourcen erstellt werden. Wenn Sie alle Ihre App-Ressourcen in einer Ressourcengruppe zusammenfassen, können Sie sie einfacher gemeinsam verwalten oder löschen. | 
    | **DNS-Name** | Geben Sie einen global eindeutigen Namen ein. | Der Cachename muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und Bindestriche enthalten. Der Name muss mit einer Zahl oder einem Buchstaben beginnen und enden und darf keine aufeinanderfolgenden Bindestriche enthalten. Der *Hostname* Ihrer Cache-Instanz lautet *\<DNS name>.redis.cache.windows.net*. | 
    | **Location** | Wählen Sie einen Standort aus. | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
    | **Cachetyp** | Wählen Sie einen Cache im [Premium- oder Enterprise-Tarif](https://azure.microsoft.com/pricing/details/cache/) aus. |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](cache-overview.md) (Was ist Azure Cache for Redis?). |
   
1. Wählen Sie auf der Seite **Erweitert** für einen Cache im Premium-Tarif die Option **Replikatanzahl** aus.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Replikatanzahl":::

1. Wählen Sie **Verfügbarkeitszonen** aus. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Verfügbarkeitszonen":::

1. Behalten Sie bei den anderen Optionen die Standardeinstellungen bei. 

    > [!NOTE]
    > Die Unterstützung der Zonenredundanz funktioniert zurzeit nur mit nicht gruppierten und nicht georeplizierten Caches. Außerdem werden Private Link, Skalierung, Datenpersistenz und Import/Export nicht unterstützt.
    >

1. Klicken Sie auf **Erstellen**. 
   
    Es dauert eine Weile, bis der Cache erstellt wird. Sie können den Fortschritt auf der Seite **Übersicht** von Azure Cache for Redis überwachen. Wenn **Wird ausgeführt** als **Status** angezeigt wird, ist der Cache einsatzbereit.
   
    > [!NOTE]
    > Verfügbarkeitszonen können nicht mehr geändert werden, nachdem ein Cache erstellt wurde.
    >

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über Azure Cache for Redis-Features.

> [!div class="nextstepaction"]
> [Premium-Dienstebenen für Azure Cache for Redis](cache-overview.md#service-tiers)