---
title: Aktualisieren eines Befehls aus einer Client-App
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie einen Befehl aus einer Client-App heraus aktualisieren.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: nitinme
ms.openlocfilehash: 08c674a7a7ec060a4273836064cb1c21e979e725
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560286"
---
# <a name="update-a-command-from-a-client-app"></a>Aktualisieren eines Befehls aus einer Client-App

In diesem Artikel erfahren Sie, wie Sie einen laufenden Befehl von einer Clientanwendung aus aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen
> [!div class = "checklist"]
> * Eine zuvor [erstellte Anwendung für benutzerdefinierte Befehle](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>Aktualisieren des Zustands eines Befehls

Wenn Ihre Clientanwendung erfordert, dass Sie den Zustand eines laufenden Befehls ohne Spracheingabe aktualisieren, können Sie ein Ereignis senden, um den Befehl zu aktualisieren.

Um dieses Szenario zu veranschaulichen, senden Sie die folgende Ereignisaktivität, um den Zustand eines laufenden Befehls (`TurnOnOff`) zu aktualisieren: 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

Lassen Sie uns die Schlüsselattribute dieser Aktivität überprüfen:

| attribute | Erklärung |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Typ** | Die Aktivität ist vom Typ `"event"`, und der Name des Ereignisses muss `"RemoteUpdate"` lauten. |
| **value** | Das Attribut `"value"` enthält die zur Aktualisierung des aktuellen Befehls erforderlichen Attribute. |
| **updatedCommand** | Das Attribut `"updatedCommand"` enthält den Namen des Befehls. Innerhalb dieses Attributs ist `"updatedParameters"` eine Zuordnung mit den Namen der Parameter und deren aktualisierten Werten. |
| **cancel** | Wenn der laufende Befehl abgebrochen werden muss, legen Sie das Attribut `"cancel"` auf `true` fest. |
| **updatedGlobalParameters** | Das Attribut `"updatedGlobalParameters"` ist eine Zuordnung wie `"updatedParameters"`, wird jedoch für globale Parameter verwendet. |
| **processTurn** | Wenn der Durchgang nach dem Senden der Aktivität verarbeitet werden muss, legen Sie das Attribut `"processTurn"` auf `true` fest. |

Sie können dieses Szenario im Portal „Benutzerdefinierte Befehle“ testen:

1. Öffnen Sie die Anwendung „Benutzerdefinierte Befehle“, die Sie zuvor erstellt haben. 
1. Wählen Sie **Trainieren** und dann **Testen** aus.
1. Senden Sie `turn`.
1. Öffnen Sie die Seitenleiste, und wählen Sie **Aktivitäts-Editor** aus.
1. Geben Sie das im vorigen Abschnitt angegebene `RemoteCommand`-Ereignis ein, und senden Sie es.
    > [!div class="mx-imgBorder"]
    > ![Screenshot, der das Ereignis für einen Remotebefehl zeigt.](media/custom-commands/send-remote-command-activity.png)

Beachten Sie, wie der Wert für den Parameter `"OnOff"` mithilfe einer Aktivität des Clients anstelle von Sprache oder Text auf `"on"` festgelegt wurde.

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>Aktualisieren des Katalogs des Parameters für einen Befehl

Wenn Sie die Liste der gültigen Optionen für einen Parameter konfigurieren, werden die Werte für den Parameter global für die Anwendung definiert. 

In unserem Beispiel verfügt der Parameter `SubjectDevice` unabhängig von der Unterhaltung über eine feste Liste von unterstützten Werten.

Falls Sie dem Katalog des Parameters pro Unterhaltung neue Einträge hinzufügen möchten, können Sie die folgende Aktivität senden:

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
Mit dieser Aktivität haben Sie dem Katalog des Parameters `"SubjectDevice"` im Befehl `"TurnOnOff"` einen Eintrag für `"stereo"` hinzugefügt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="Screenshot, der eine Katalogaktualisierung zeigt.":::

Beachten Sie ein paar Punkte:
- Sie müssen diese Aktivität nur einmal senden (idealerweise gleich nach dem Herstellen einer Verbindung).
- Nachdem Sie diese Aktivität gesendet haben, sollten Sie darauf warten, dass das Ereignis `ParameterCatalogsUpdated` an den Client zurückgesendet wird.

## <a name="add-more-context-from-the-client-application"></a>Hinzufügen von weiterem Kontext aus der Clientanwendung

Sie können von der Clientanwendung aus pro Konversation zusätzlichen Kontext festlegen, der später in Ihrer Anwendung für benutzerdefinierte Befehle verwendet werden kann. 

Denken Sie z. B. an das Szenario, in dem Sie die ID und den Namen des Geräts senden möchten, das mit der Anwendung für benutzerdefinierte Befehle verbunden ist.

Um dieses Szenario zu testen, erstellen wir einen neuen Befehl in der aktuellen Anwendung:
1. Erstellen Sie einen neuen Befehl namens `GetDeviceInfo`.
1. Hinzufügen einer Beispieläußerung `get device info`.
1. Fügen Sie in der Vervollständigungsregel **Fertig** eine Aktion **Sprachantwort senden** hinzu, die die Attribute von `clientContext` enthält.
   ![Screenshot, der eine Antwort zum Senden von Sprache mit Kontext zeigt.](media/custom-commands/send-speech-response-context.png)
1. Speichern, trainieren und testen Sie Ihre Anwendung.
1. Senden Sie im Testfenster eine Aktivität, um den Clientkontext zu aktualisieren.

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. Senden Sie den Text `get device info`.
   ![Screenshot, der eine Aktivität für das Senden von Clientkontext zeigt.](media/custom-commands/send-client-context-activity.png)

Beachten Sie einige Punkte:
- Sie müssen diese Aktivität nur einmal senden (idealerweise gleich nach dem Herstellen einer Verbindung).
- Sie können komplexe Objekte für `clientContext` verwenden.
- Sie können `clientContext` in Sprachantworten, zum Senden von Aktivitäten und zum Aufrufen von Webendpunkten verwenden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktualisieren eines Befehls über einen Webendpunkt](./how-to-custom-commands-update-command-from-web-endpoint.md)
