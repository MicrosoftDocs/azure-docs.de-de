---
title: Aktualisieren eines Befehls über einen Webendpunkt
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie den Zustand eines Befehls aktualisieren, indem Sie einen Aufruf an einen Webendpunkt verwenden.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: d0b77e6af36f0a71405f6c032bfdd121abeb0071
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560269"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Aktualisieren eines Befehls über einen Webendpunkt

Wenn Ihre Clientanwendung die Aktualisierung des Zustands eines laufenden Befehls ohne Spracheingabe erfordert, können Sie einen Aufruf für einen Webendpunkt verwenden, um den Befehl zu aktualisieren.

In diesem Artikel erfahren Sie, wie Sie einen laufenden Befehl über einen Webendpunkt aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen
> [!div class = "checklist"]
> * Eine zuvor [erstellte Anwendung für benutzerdefinierte Befehle](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion 

Für dieses Beispiel benötigen Sie eine über HTTP ausgelöste [Azure-Funktion](../../azure-functions/index.yml), die die folgende Eingabe (oder eine Teilmenge dieser Eingabe) unterstützt:

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Lassen Sie uns die Schlüsselattribute dieser Eingabe überprüfen:

| attribute | Erklärung |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | Der eindeutige Bezeichner der Unterhaltung. Beachten Sie, dass diese ID aus der Client-App generiert werden kann. |
| **currentCommand** | Der aktuell aktive Befehl in der Unterhaltung. |
| **name** | Der Name des Befehls. Das Attribut `parameters` ist eine Zuordnung mit den aktuellen Werten der Parameter. |
| **currentGlobalParameters** | Eine Zuordnung wie `parameters`, aber für globale Parameter verwendet. |

Die Ausgabe der Azure-Funktion muss das folgende Format unterstützen:

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Möglicherweise erkennen Sie dieses Format, da es dasselbe ist, das Sie beim [Aktualisieren eines Befehls vom Client](./how-to-custom-commands-update-command-from-client.md) verwendet haben. 

Erstellen Sie jetzt eine Azure-Funktion auf der Basis von NodeJS. Kopieren Sie diesen Code, und fügen Sie ihn ein:

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

Wenn Sie diese Azure-Funktion aus „Benutzerdefinierte Befehle“ aufrufen, senden Sie die aktuellen Werte der Unterhaltung. Sie geben die Parameter zurück, die Sie aktualisieren möchten, oder ob Sie den aktuellen Befehl abbrechen möchten.

## <a name="update-the-existing-custom-commands-app"></a>Aktualisieren der vorhandenen App „Benutzerdefinierte Befehle“

Jetzt wollen wir die Azure-Funktion mit der vorhandenen App „Benutzerdefinierte Befehle“ verbinden:

1. Fügen Sie einen neuen Befehl namens `IncrementCounter` hinzu.
1. Fügen Sie nur einen Beispielsatz mit dem Wert `increment` hinzu.
1. Fügen Sie einen neuen Parameter namens `Counter` (selber Name wie in der Azure-Funktion angegeben) vom Typ `Number` mit einem Standardwert von `0` hinzu.
1. Fügen Sie einen neuen Webendpunkt namens `IncrementEndpoint` mit der URL Ihrer Azure-Funktion und mit auf **Aktiviert** festgelegten **Remoteupdates** hinzu.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Screenshot, der das Festlegen eines Webendpunkts mit Remoteupdates zeigt.":::
1. Erstellen Sie eine neue Interaktionsregel namens **IncrementRule**, und fügen Sie eine Aktion **Webendpunkt aufrufen** hinzu.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Screenshot, der das Erstellen einer Interaktionsregel zeigt.":::
1. Wählen Sie in der Aktionskonfiguration `IncrementEndpoint` aus. Konfigurieren Sie **Bei Erfolg** auf **Sprachantwort senden** mit dem Wert `Counter`, und konfigurieren Sie **Bei Fehler** mit einer Fehlermeldung.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Screenshot, der das Festlegen eines Inkrementzählers für das Aufrufen eines Webendpunkts zeigt.":::
1. Legen Sie den Zustand nach der Ausführung der Regel auf **Auf Benutzereingabe warten** fest.

## <a name="test-it"></a>Testen

1. Speichern und trainieren Sie Ihre App.
1. Klicken Sie auf **Test**.
1. Senden Sie mehrmals `increment` (dies ist der Beispielsatz für den Befehl `IncrementCounter`).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Screenshot, der ein Beispiel für einen Inkrementzähler zeigt.":::

Beachten Sie, wie der Wert des `Counter`-Parameters bei jedem Durchgang von der Azure-Funktion inkrementiert wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktivieren eines CI/CD-Prozesses für die Anwendung für benutzerdefinierte Befehle](./how-to-custom-commands-deploy-cicd.md)