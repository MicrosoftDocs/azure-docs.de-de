---
title: Verwalten von Konten und Schlüsseln
titleSuffix: Language Understanding - Azure Cognitive Services
description: Die zwei entscheidenden Informationen für ein LUIS-Konto sind das Benutzerkonto und der Authoringschlüssel. Ihre Anmeldeinformationen werden auf „account.microsoft.com“ verwaltet. Ihr Authoringschlüssel wird auf der Seite „Einstellungen“ des LUIS-Portals verwaltet.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: d5a1d7ee3b8b16631f7b919f3aece0848d662e62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523519"
---
# <a name="manage-account-and-authoring-key"></a>Verwalten von Konto und Authoringschlüssel

Die zwei entscheidenden Informationen für ein LUIS-Konto sind das Benutzerkonto und der Authoringschlüssel. Ihre Anmeldeinformationen werden unter [account.microsoft.com](https://account.microsoft.com) verwaltet. Ihr Authoringschlüssel wird auf der Seite **Einstellungen** des [LUIS](luis-reference-regions.md)-Portals verwaltet.

## <a name="authoring-key"></a>Erstellungsschlüssel

Dieser eindeutige, regionsspezifische Authoringschlüssel auf der Seite **Einstellungen** ermöglicht Ihnen das Authoring Ihrer sämtlichen Apps über das [LUIS](luis-reference-regions.md)-Portal sowie mithilfe der [Authoring-APIs](https://go.microsoft.com/fwlink/?linkid=2092087). Zu Ihrer Bequemlichkeit ist es dem Authoringschlüssel gestattet, eine [beschränkte](luis-boundaries.md) Anzahl Endpunktabfragen im Monat auszuführen.

[![LUIS-Seite „Einstellungen“](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Der Authoringschlüssel wird für alle Apps verwendet, die Sie besitzen, sowie für alle Apps, für die Sie als Mitwirkender gelistet sind.

## <a name="authoring-key-regions"></a>Regionen von Authoringschlüsseln

Der Authoringschlüssel ist für die [Authoringregion](luis-reference-regions.md#publishing-regions) spezifisch. Der Schlüssel funktioniert in anderen Regionen nicht.

## <a name="reset-authoring-key"></a>Zurücksetzen des Authoringschlüssels

Wenn Ihr Authoringschlüssel beschädigt wurde, setzen Sie den Schlüssel zurück. Der Schlüssel wird für all Ihre Apps im [LUIS](luis-reference-regions.md)-Portal zurückgesetzt. Wenn Sie Ihre Apps mithilfe der Authoring-APIs verfassen, müssen Sie den Wert von `Ocp-Apim-Subscription-Key` auf den neuen Schlüssel ändern.

## <a name="delete-account"></a>Konto löschen

Informationen zu den Daten, die beim Löschen Ihres Kontos gelöscht werden, finden Sie unter [Datenspeicherung und -löschung](luis-concept-data-storage.md#accounts).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über Ihren [Authoringschlüssel](luis-concept-keys.md#authoring-key).

