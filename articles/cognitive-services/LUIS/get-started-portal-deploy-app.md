---
title: 'Schnellstart: Bereitstellen einer App im LUIS-Portal'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine App bereitstellen, indem Sie eine Vorhersageendpunktressource erstellen, die Ressource zuweisen, ein Training durchführen und die App veröffentlichen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/06/2020
ms.openlocfilehash: b051f349716b5597adee4509cfe774978a8f61aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95972509"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Schnellstart: Bereitstellen einer App im LUIS-Portal

Wenn Ihre LUIS-App bereit ist, Äußerungsvorhersagen an eine Clientanwendung (beispielsweise einen Chatbot) zurückzugeben, müssen Sie die App für den Vorhersageendpunkt bereitstellen.

In diesem Schnellstart erfahren Sie, wie eine Anwendung bereitgestellt wird. Sie erstellen eine Vorhersageendpunktressource, weisen die Ressource der App zu, trainieren die App und veröffentlichen sie.

## <a name="prerequisites"></a>Voraussetzungen

* Erwerben Sie ein [Azure-Abonnement](https://azure.microsoft.com/free).
* Führen Sie den vorherigen [Schnellstart: Erstellen einer neuen App im LUIS-Portal](get-started-portal-build-app.md) durch, oder [laden Sie die App herunter, und importieren Sie die App](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Wenn Sie über Apps verfügen, die vor der Azure-Ressourcenauthentifizierung erstellt wurden, [migrieren Sie zu einer Azure-Ressource](luis-migration-authoring.md). Einige Portalseiten sehen anders aus, wenn die E-Mail-Authentifizierung verwendet wird.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Zuweisen des Ressourcenschlüssels für die LUIS-App im LUIS-Portal

Jedes Mal, wenn Sie eine neue Dokumenterstellungs- oder Vorhersageabfrageressource für LUIS erstellen, müssen Sie der LUIS-App die Ressource zuweisen. Nachdem Sie die Ressource zugewiesen haben, müssen Sie diesen Schritt nur dann erneut ausführen, wenn Sie eine neue Ressource erstellen. Sie können beispielsweise eine neue Ressource erstellen, um die Regionen Ihrer App zu erweitern oder eine größere Anzahl von Vorhersageabfragen zu unterstützen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie in der Liste der Apps die App **myEnglishApp** aus.

1. Wählen Sie im Menü oben rechts die Option **Verwalten** aus, und wählen Sie dann **Azure-Ressourcen** aus.

1. Um LUIS hinzuzufügen, wählen Sie **Add prediction resource** (Vorhersageressource hinzufügen) aus.

    ![Hinzufügen der LUIS-Vorhersageressource durch Auswählen von „Vorhersageressource hinzufügen“](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Wählen Sie Ihren Mandanten, Ihr Abonnement und den Namen Ihrer Ressource aus. Wählen Sie **Ressource zuweisen** aus.

   > [!div class="mx-imgBorder"]
   > ![Zuweisen einer Ressource zu Ihrer App](./media/get-started-portal-deploy-app/assign-resource.png)

1. Führen Sie die gleichen Schritte aus, um Ihrer App den Erstellungsschlüssel hinzuzufügen.

1. Suchen Sie die neue Zeile in der Tabelle für die neue Vorhersageressource, und kopieren Sie die Endpunkt-URL. Sie ist ordnungsgemäß aufgebaut, um eine `HTTP GET`-Anforderung an den LUIS-API-Endpunkt zum Abrufen einer Vorhersage zu senden.

> [!TIP]
> Wenn Sie Ihre LUIS-App mithilfe von aktivem Lernen verbessern möchten, wählen Sie **Abfrageparameter ändern** und anschließend **Protokolle speichern** aus. Dadurch wird der Beispiel-URL der Abfragezeichenfolgenparameter `log=true` hinzugefügt. Kopieren und verwenden Sie die geänderte Beispielabfrage-URL, wenn Sie Vorhersageabfragen an den Runtimeendpunkt richten.

## <a name="train-the-app"></a>Trainieren der App

Wenn Sie die App seit dem letzten Training geändert haben, [trainieren](get-started-portal-build-app.md) Sie die App.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Veröffentlichen der App für den Vorhersageendpunkt

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Anforderung für Vorhersageendpunkt

Die Äußerung des Benutzers wird im Portal am Ende der URL bei `query=` an die GET-Anforderung angefügt. Geben Sie nach `query=` die gleiche Benutzeräußerung ein, die am Ende des vorherigen Schnellstarts verwendet wurde:

```Is there a form named hrf-234098```

Stellen Sie sicher, dass die Abfragezeichenfolge folgende Paare enthält:

* `show-all-intents=true`
* `verbose=true`

Der Browser zeigt die folgende Antwort an:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Um im Testbereich die gleiche Informationsmenge anzuzeigen, müssen Sie die App veröffentlichen. Wählen Sie nach dem Veröffentlichen der App im Testbereich **Compare with published** (Mit veröffentlichtem Element vergleichen) aus. Verwenden Sie im Testbereich für die Veröffentlichung die Option **JSON-Ansicht anzeigen**, um dieselbe JSON wie im vorherigen Schritt anzuzeigen. Auf diese Weise können Sie Änderungen an der aktuell bearbeiteten App mit der App vergleichen, die für den Endpunkt veröffentlicht wird.

[![Aktuell bearbeitete Version mit der veröffentlichten Version der App vergleichen](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wählen Sie nach Abschluss dieses Schnellstarts im oberen Navigationsmenü die Option **Meine Apps** aus. Aktivieren Sie in der Liste das Kontrollkästchen der App, und wählen Sie auf der Kontextsymbolleiste oberhalb der Liste **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Identifizieren häufiger Absichten und Entitäten](./tutorial-machine-learned-entity.md)