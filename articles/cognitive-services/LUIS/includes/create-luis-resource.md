---
title: Erstellen einer LUIS-Ressource
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972510"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>Erstellen von LUIS-Ressourcen im Azure-Portal

1. Unter [diesem Link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) können Sie mit der Erstellung von LUIS-Ressourcen im Azure-Portal beginnen.

1. Geben Sie alle erforderlichen Einstellungen ein:

    |Name|Zweck|
    |--|--|
    |Subscription | Das Abonnement, unter dem die Ressource abgerechnet wird|
    |Resource group| Ein benutzerdefinierter Ressourcengruppenname, den Sie auswählen oder erstellen. Mit Ressourcengruppen können Sie Azure-Ressourcen für den Zugriff und die Verwaltung gruppieren.|
    |Name| ein von Ihnen gewählter benutzerdefinierter Name. Er wird als benutzerdefinierte Unterdomäne für Ihre Erstellungs- und Vorhersageendpunktabfragen verwendet.|
    |Authoring location (Erstellungsstandort)|Die Region, die Ihrem Modell zugeordnet ist.|
    |Authoring pricing tier (Erstellungstarif)|Bestimmt die maximale Anzahl von Transaktionen pro Sekunde und Monat.|
    |Vorhersagestandort|Die Region, die Ihrer veröffentlichten Vorhersageendpunkt-Laufzeitumgebung zugeordnet ist.|
    |Tarif für Vorhersagen|Bestimmt die maximale Anzahl von Transaktionen pro Sekunde und Monat.|

    > [!div class="mx-imgBorder"]
    > [![Screenshot: Registerkarte „Grundlagen“ unter „Erstellen“](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Wählen Sie **Überprüfen + erstellen** aus, und warten Sie, bis die Ressource erstellt wird.
1. Nachdem beide Ressourcen erstellt wurden, wählen Sie im Azure-Portal die neue Erstellungsressource aus. Wählen Sie anschließend **Schlüssel und Endpunkt** aus, um die **Erstellungsendpunkt-URL** und den **Schlüssel** programmgesteuert für die Erstellung abzurufen.

> [!TIP]
> Um die Ressourcen verwenden zu können, müssen Sie sie im LUIS-Portal [zuweisen](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
