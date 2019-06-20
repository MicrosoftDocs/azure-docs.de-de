---
title: include file
description: include file
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: c426df2293cfb2d8ba4dc02e8fc5519c3d822168
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178474"
---
Durch Anwenden von Tags auf Ihre Azure-Ressourcen erzeugen Sie Metadaten, um sie logisch in einer Taxonomie zu organisieren. Jedes Tag besteht aus einem Paar mit einem Namen und einem Wert. So können Sie beispielsweise den Namen „Umgebung“ und den Wert „Produktion“ auf alle Ressourcen in der Produktion anwenden.

Nach dem Anwenden von Tags können Sie alle Ressourcen in Ihrem Abonnement abrufen, die diesen Tagnamen und -wert besitzen. Mit Tags können Sie verwandte Ressourcen aus verschiedenen Ressourcengruppen abrufen. Das ist hilfreich, wenn Sie Ressourcen für die Abrechnung oder Verwaltung organisieren müssen.

Ihre Taxonomie sollte zusätzlich zu einer automatischen Kennzeichhnungsstrategie eine Self-Service-Metadatenkennzeichnungsstrategie berücksichtigen, um die Belastung für Benutzer zu verringern und die Genauigkeit zu verbessern.

Für Tags gelten folgende Einschränkungen:

* Nicht alle Ressourcentypen unterstützen Tags. Um festzustellen, ob Sie ein Tag auf einen Ressourcentyp anwenden können, lesen Sie [Tagunterstützung für Azure-Ressourcen](../articles/azure-resource-manager/tag-support.md).
* Jede Ressource oder Ressourcengruppe kann maximal 15 Tagname-Wert-Paare besitzen. Diese Einschränkung gilt nur für Tags, die direkt auf die Ressourcengruppe oder die Ressource angewendet werden. Eine Ressourcengruppe kann zahlreiche Ressourcen mit jeweils 15 Tagname-Wert-Paaren enthalten. Wenn Sie einer Ressource mehr als 15 Werte zuordnen müssen, verwenden Sie eine JSON-Zeichenfolge für den Tagwert. Die JSON-Zeichenfolge kann zahlreiche Werte enthalten, die auf einen einzelnen Tagnamen angewendet werden. Dieser Artikel enthält ein Beispiel für die Zuweisung einer JSON-Zeichenfolge zum Tag.
* Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen. Bei Speicherkonten ist der Tagname auf 128 Zeichen beschränkt und der Tagwert auf 256 Zeichen.
* Auf virtuellen Computern und VM-Skalierungsgruppen gilt für alle Tagnamen und -werte ein Gesamtgrenzwert von maximal 2048 Zeichen.
* Auf die Ressourcengruppe angewendete Tags werden nicht von den in dieser Ressourcengruppe enthaltenen Ressourcen geerbt.
* Tags können nicht auf klassische Ressourcen wie Cloud Services angewendet werden.
* Tag-Namen dürfen die folgenden Zeichen nicht enthalten: `<`, `>`, `%`, `&`, `\`, `?`, `/`
