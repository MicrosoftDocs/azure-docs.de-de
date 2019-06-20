---
title: Löschen von Ressourcengruppen und Ressourcen – Azure Resource Manager
description: Beschreibt, in welcher Reihenfolge Azure Resource Manager das Löschen von Ressourcen beim Löschen einer Ressourcengruppe ausführt. Sie erhalten Informationen zu den Antwortcodes und dazu, wie Resource Manager anhand der Codes bestimmt, ob die Löschung erfolgreich war.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61363596"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Azure Resource Manager: Löschvorgang von Ressourcengruppen

In diesem Artikel wird beschrieben, in welcher Reihenfolge Azure Resource Manager das Löschen von Ressourcen ausführt, wenn Sie eine Ressourcengruppe löschen.

## <a name="determine-order-of-deletion"></a>Bestimmen der Reihenfolge des Löschvorgangs

Wenn Sie eine Ressourcengruppe löschen, bestimmt Resource Manager die Reihenfolge, in der Ressourcen gelöscht werden. Die folgende Reihenfolge wird verwendet:

1. Alle untergeordneten (geschachtelten) Ressourcen werden gelöscht.

2. Ressourcen, die andere Ressourcen verwalten, werden im nächsten Schritt gelöscht. Bei einer Ressource kann die Eigenschaft `managedBy` festgelegt sein, um anzuzeigen, dass eine andere Ressource sie verwaltet. Wenn diese Eigenschaft festgelegt ist, wird die Ressource, die die andere Ressource verwaltet, vor den anderen Ressourcen gelöscht.

3. Die verbleibenden Ressourcen werden im Anschluss an die oben genannten beiden Kategorien gelöscht.

## <a name="resource-deletion"></a>Ressourcenlöschung

Nachdem die Reihenfolge ermittelt wurde, gibt Resource Manager einen DELETE-Vorgang für jede Ressource aus. Es wird gewartet, bis alle Abhängigkeiten beendet wurden, bevor der Vorgang fortgesetzt wird.

Für synchrone Vorgänge lauten die erwarteten Antwortcodes bei Erfolg folgendermaßen:

* 200
* 204
* 404

Für asynchrone Vorgänge lautet die erwartete Antwort bei Erfolg 202. Resource Manager verfolgt den Location-Header oder den azure-async-Vorgangsheader nach, um den Status des asynchronen Löschvorgangs zu ermitteln.
  
### <a name="errors"></a>Errors

Wenn ein Löschvorgang einen Fehler zurückgibt, wiederholt Resource Manager den DELETE-Aufruf. Wiederholungsversuche finden für die Statuscodes 5xx, 429 und 408 statt. Standardmäßig beträgt der Zeitraum für Wiederholungsversuche 15 Minuten.

## <a name="after-deletion"></a>Nach dem Löschen

Resource Manager gibt einen GET-Aufruf für jede Ressource aus, für die ein Löschversuch stattgefunden hat. Als Antwort auf diesen GET-Aufruf wird 404 erwartet. Wenn Resource Manager eine 404-Antwort erhält, wird davon ausgegangen, dass der Löschvorgang erfolgreich war. Resource Manager entfernt die Ressource aus dem Cache.

Wenn der GET-Aufruf für die Ressource jedoch 200 oder 201 zurückgibt, erstellt Resource Manager die Ressource erneut.

### <a name="errors"></a>Errors

Wenn der GET-Vorgang einen Fehler zurückgibt, wiederholt Resource Manager die GET-Anforderung für die folgenden Fehlercodes:

* Kleiner als 100
* 408
* 429
* Größer als 500

Bei anderen Fehlercodes kann Resource Manager das Löschen der Ressource nicht durchführen.

## <a name="next-steps"></a>Nächste Schritte

* Um die Konzepte von Resource Manager zu verstehen, lesen Sie [Azure Resource Manager: Übersicht](resource-group-overview.md).
* Informationen zu Löschbefehlen finden Sie unter [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) und [REST-API](/rest/api/resources/resourcegroups/delete).
