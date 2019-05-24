---
title: Includedatei
description: Includedatei
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 0f1decae5fb3ec4a07f01c5bff7475f3d73a3cbb
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132857"
---
1. Melden Sie sich zum Erstellen eines Cache zunächst beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie anschließend die Option **Ressource erstellen** > **Datenbanken** > **Azure Cache for Redis**.

    ![Neuer Cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Konfigurieren Sie unter **New Azure Cache for Redis** (Azure Cache for Redis – Neu) die Einstellungen für Ihren neuen Cache.

    | Einstellung      | Empfohlener Wert  | BESCHREIBUNG |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-Name** | Global eindeutiger Name | Der Cachename. Er muss zwischen 1 und 63 Zeichen lang sein und darf nur Zahlen, Buchstaben und das Zeichen `-` enthalten. Der Cachename darf weder mit dem Zeichen `-` beginnen oder enden, noch mehrere aufeinanderfolgende Zeichen vom Typ `-` enthalten.  | 
    | **Abonnement** | Ihr Abonnement | Das Abonnement, unter dem diese neue Azure Cache for Redis-Instanz erstellt wird. | 
    | **Ressourcengruppe** |  *TestResources* | Der Name der neuen Ressourcengruppe, in der Ihr Cache erstellt wird. Indem Sie alle Ressourcen für eine App in einer Gruppe zusammenfassen, können Sie sie zusammen verwalten. Wenn Sie beispielsweise die Ressourcengruppe löschen, werden alle Ressourcen gelöscht, die der App zugeordnet sind. | 
    | **Location** | USA (Ost) | Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in der Nähe anderer Dienste aus, die Ihren Cache verwenden. |
    | **[Preisstufe](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB Cache) |  Der Tarif bestimmt Größe, Leistung und verfügbare Features für den Cache. Weitere Informationen finden Sie unter [What is Azure Cache for Redis](../articles/azure-cache-for-redis/cache-overview.md) (Was ist Azure Cache for Redis?). |
    | **An Dashboard anheften** |  Aktiviert | Indem Sie den neuen Cache an Ihr Dashboard anheften, können Sie ihn leichter wiederfinden. |

    ![Cache erstellen](media/redis-cache-create/redis-cache-cache-create.png) 

3. Klicken Sie nach der Konfiguration der Einstellungen für den neuen Cache auf **Erstellen**. 

    Die Erstellung des Caches kann einige Minuten dauern. Sie können den Fortschritt im Dashboard überwachen, um den Status zu überprüfen. Nach der Erstellung des Caches hat er den Status **Wird ausgeführt** und kann verwendet werden.

    ![Cache erstellt](media/redis-cache-create/redis-cache-cache-created.png)

