---
title: include file
description: include file
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 081b9288d091e26d0e6d30aa849ced18a3c2ffb6
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938459"
---
Erstellen Sie die Pizza-App.

1. Wählen Sie [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) aus, um die GitHub-Seite für die Datei `pizza-app-for-luis.json` anzuzeigen.
1. Klicken Sie mit der rechten Maustaste auf die Schaltfläche **Raw** (oder tippen Sie lang darauf), und wählen Sie **Link speichern unter** aus, um `pizza-app-for-luis.json` auf Ihrem Computer zu speichern.
1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an.
1. Wählen Sie [Meine Apps](https://www.luis.ai/applications) aus.
1. Wählen Sie auf der Seite **Meine Apps** **+ Neue App für Unterhaltung** aus.
1. Wählen Sie **Als JSON importieren** aus.
1. Wählen Sie im Dialogfeld **Neue App importieren** die Schaltfläche **Datei auswählen** aus.
1. Wählen Sie die heruntergeladene Datei `pizza-app-for-luis.json` und dann **Öffnen** aus.
1. Geben Sie im Feld **Name** im Dialogfeld **Neue App importieren** einen Namen für Ihre Pizza-App ein, und wählen Sie dann die Schaltfläche **Fertig** aus.

Die App wird importiert.

Wenn ein Dialogfeld **Erstellen einer effektiven LUIS-App** angezeigt wird, schließen Sie das Dialogfeld.

## <a name="train-and-publish-the-pizza-app"></a>Trainieren und Veröffentlichen der Pizza-App

Die Seite **Absichten** sollte mit einer Liste aller Absichten in der Pizza-App angezeigt werden.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Hinzufügen einer Erstellungsressource zur Pizza-App

1. Wählen Sie **VERWALTEN** aus.
1. Wählen Sie **Azure-Ressourcen** aus.
1. Wählen Sie **Erstellungsressource** aus.
1. Wählen Sie **Change authoring resource** (Erstellungsressource ändern) aus.

Wenn Sie über eine Erstellungsressource verfügen, geben Sie die Werte für **Mandantenname**, **Abonnementname** und **Name der LUIS-Ressource** Ihrer Erstellungsressource ein.

Wenn Sie keine Erstellungsressource besitzen:

1. Wählen Sie die Option **Neue Ressource erstellen** aus.
1. Geben Sie Werte für **Mandantenname**, **Ressourcenname**, **Abonnementname** und **Name der Azure-Ressourcengruppe** ein.

Ihre Pizza-App ist jetzt einsatzbereit.

## <a name="record-the-access-values-for-your-pizza-app"></a>Aufzeichnen der Zugriffswerte für Ihre Pizza-App

Um Ihre neue Pizza-App zu verwenden, benötigen Sie die App-ID, den Erstellungsschlüssel und den Erstellungsendpunkt Ihrer Pizza-App. Sie benötigen einen separaten Vorhersageendpunkt und Vorhersageschlüssel, um Vorhersagen zu treffen.

So finden Sie diese Werte:

1. Wählen Sie auf der Seite **Absichten** **VERWALTEN** aus.
1. Notieren Sie auf der Seite **Anwendungseinstellungen** die **App-ID**.
1. Wählen Sie **Azure-Ressourcen** aus.
1. Wählen Sie **Erstellungsressource** aus.
1. Notieren Sie den **Primärschlüssel**, der auf den Registerkarten **Erstellungsressource** und **Vorhersageressourcen** vermerkt ist. Dieser Wert ist der Erstellungsschlüssel.
1. Notieren Sie die **Endpunkt-URL**. Dieser Wert ist der Erstellungsendpunkt.
