---
title: 'Tutorial: Moderieren von Facebook-Inhalten: Content Moderator'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird beschrieben, wie Sie Content Moderator auf Machine Learning-Basis verwenden, um Facebook-Posts und -Kommentare zu moderieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: e930e5d125a8f1ee90448e293e2e0ca2c5c28465
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913669"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Tutorial: Moderieren von Facebook-Posts und -Kommentaren mit Azure Content Moderator

In diesem Tutorial wird beschrieben, wie Sie Azure Content Moderator verwenden, um Posts und Kommentare auf einer Facebook-Seite zu moderieren. Facebook sendet die von Besuchern geposteten Inhalte an den Content Moderator-Dienst. Über Ihre Content Moderator-Workflows werden entweder die Inhalte veröffentlicht, oder es werden mit dem entsprechenden Tool Bewertungen erstellt. Dies richtet sich nach den Inhaltspunktzahlen und Schwellenwerten. Das [Demovideo von der Build 2017](https://channel9.msdn.com/Events/Build/2017/T6033) enthält ein funktionsfähiges Beispiel für dieses Szenario.

> [!IMPORTANT]
> Im Jahr 2018 wurde von Facebook eine strengere Überprüfungsrichtlinie für Facebook-Apps eingeführt. Die Schritte in diesem Tutorial können nur ausgeführt werden, wenn Ihre App vom Facebook-Überprüfungsteam überprüft und genehmigt wurde.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Erstellen eines Content Moderator-Teams
> * Erstellen von Azure-Funktionen, die auf HTTP-Ereignisse von Content Moderator und Facebook lauschen
> * Verknüpfen einer Facebook-Seite mit Content Moderator über eine Facebook-Anwendung

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/) erstellen, bevor Sie beginnen.

In diesem Diagramm sind die einzelnen Komponenten dieses Szenarios dargestellt:

![Diagramm: Content Moderator empfängt Informationen von Facebook über „FBListener“ und sendet Informationen über „CMListener“](images/tutorial-facebook-moderation.png)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Content Moderator-Abonnementschlüssel. Gehen Sie wie unter [Schnellstart: Erstellen eines Cognitive Services-Kontos im Azure-Portal](../cognitive-services-apis-create-account.md) beschrieben vor, um den Content Moderator-Dienst zu abonnieren und Ihren Schlüssel zu erhalten.
- Ein [Facebook-Konto](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Erstellen eines Prüfungsteams

Informationen zum Registrieren für das [Content Moderator-Prüfungstool](https://contentmoderator.cognitive.microsoft.com/) sowie zum Erstellen eines Prüfungsteams finden Sie unter [Schnellstart: Testen von Content Moderator im Web](quick-start.md). Notieren Sie sich auf der Seite **Anmeldeinformationen** die **Team-ID** .

## <a name="configure-image-moderation-workflow"></a>Konfigurieren des Workflows für die Bildmoderation

Informationen zur Erstellung eines benutzerdefinierten Bildworkflows finden Sie unter [Definieren, Testen und Verwenden von Workflows](review-tool-user-guide/workflows.md). Mithilfe dieses Workflows überprüft Content Moderator automatisch Bilder auf Facebook und sendet einige davon an das Prüfungstool. Notieren Sie sich den **Namen** des Workflows.

## <a name="configure-text-moderation-workflow"></a>Konfigurieren des Workflows für die Textmoderation

Verwenden Sie erneut den Leitfaden [Definieren, Testen und Verwenden von Workflows](review-tool-user-guide/workflows.md), und erstellen Sie nun einen benutzerdefinierten Textworkflow. Dieser Workflow wird von Content Moderator zur automatischen Überprüfung von Textinhalten verwendet. Notieren Sie sich den **Namen** des Workflows.

![Konfigurieren des Textworkflows](images/text-workflow-configure.PNG)

Testen Sie Ihren Workflow mit der Schaltfläche **Workflow ausführen** .

![Testen des Textworkflows](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Erstellen von Azure-Funktionen

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und führen Sie diese Schritte aus:

1. Erstellen Sie eine Azure-Funktionen-App gemäß der Anleitung auf der Seite [Erstellen einer Funktionen-App im Azure-Portal](../../azure-functions/functions-create-function-app-portal.md).
1. Navigieren Sie zur neu erstellten Funktions-App.
1. Navigieren Sie in der App zur Registerkarte **Plattformfeatures** , und wählen Sie **Konfiguration** aus. Wählen Sie auf der nächsten Seite im Abschnitt **Anwendungseinstellungen** die Option **Neue Anwendungseinstellung** aus, um die folgenden Schlüssel-Wert-Paare hinzuzufügen:
    
    | Name der App-Einstellung | value   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | Ihre Content Moderator-Team-ID  | 
    | `cm:SubscriptionKey` | Ihr Content Moderator-Abonnementschlüssel – Siehe [Anmeldeinformationen](./review-tool-user-guide/configure.md#credentials) |
    | `cm:Region` | Ihr Content Moderator-Regionsname, ohne Leerzeichen. Sie finden diesen Namen auf der Registerkarte **Übersicht** Ihrer Azure-Ressource im Feld **Standort** .|
    | `cm:ImageWorkflow` | Name des Workflows für Bilder |
    | `cm:TextWorkflow` | Name des Workflows für Text |
    | `cm:CallbackEndpoint` | URL für die CMListener-Funktionen-App, die Sie später in diesem Leitfaden erstellen |
    | `fb:VerificationToken` | Ein von Ihnen erstelltes geheimes Token, das zum Abonnieren der Facebook-Feedereignisse verwendet wird |
    | `fb:PageAccessToken` | Das Zugriffstoken für die Facebook-Graph-API läuft nicht ab und lässt die Funktion zum Ausblenden/Löschen von Posts in Ihrem Namen zu Dieses Token erhalten Sie in einem späteren Schritt. |

    Klicken Sie oben auf der Seite auf die Schaltfläche **Speichern** .

1. Navigieren Sie zurück zur Registerkarte **Plattformfeatures** . Verwenden Sie die Schaltfläche **+** im linken Bereich, um den Bereich **Neue Funktion** anzuzeigen. Die Funktion, die Sie nun erstellen, empfängt Ereignisse von Facebook.

    ![Azure Functions-Bereich mit hervorgehobener Schaltfläche „Funktion hinzufügen“](images/new-function.png)

    1. Klicken Sie auf die Kachel **HTTP-Trigger** .
    1. Geben Sie den Namen **FBListener** ein. Das Feld **Autorisierungsstufe** sollte auf **Funktion** festgelegt sein.
    1. Klicken Sie auf **Erstellen** .
    1. Ersetzen Sie den Inhalt von **run.csx** durch den Inhalt von **FbListener/run.csx** .

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Erstellen Sie eine neue Funktion vom Typ **HTTP-Trigger** mit dem Namen **CMListener** . Diese Funktion empfängt Ereignisse von Content Moderator. Ersetzen Sie den Inhalt von **run.csx** durch den Inhalt von **CMListener/run.csx** .

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurieren der Facebook-Seite und der App

1. Erstellen Sie eine Facebook-App.

    ![Seite für Facebook-Entwickler](images/facebook-developer-app.png)

    1. Navigieren Sie zur [Facebook-Entwicklerwebsite](https://developers.facebook.com/).
    1. Klicken Sie auf **My Apps** (Meine Apps).
    1. Fügen Sie eine neue App hinzu.
    1. Geben Sie ihr einen Namen.
    1. Wählen Sie **Webhooks > Set Up** (Webhooks > Einrichten).
    1. Wählen Sie im Dropdownmenü die Option **Page** (Seite), und wählen Sie **Subscribe to this object** (Objekt abonnieren).
    1. Geben Sie die **FBListener Url** als Rückruf-URL und das von Ihnen in den **Funktionen-App-Einstellungen** konfigurierte **Überprüfungstoken** an.
    1. Scrollen Sie nach dem Abonnieren nach unten zum Feed, und wählen Sie **Subscribe** (Abonnieren).
    1. Klicken Sie in der Zeile **Feed** auf die Schaltfläche **Test** , um eine Testnachricht an Ihre Azure-Funktion „FBListener“ zu senden. Klicken Sie anschließend auf die Schaltfläche **Send to My Server** (An meinen Server senden). Daraufhin sollten Sie sehen, wie die Anforderung von FBListener empfangen wird.

1. Erstellen Sie eine Facebook-Seite.

    > [!IMPORTANT]
    > Im Jahr 2018 wurde von Facebook eine strengere Überprüfung von Facebook-Apps eingeführt. Die Abschnitte 2, 3 und 4 können nur ausgeführt werden, wenn Ihre App vom Facebook-Überprüfungsteam überprüft und genehmigt wurde.

    1. Navigieren Sie zu [Facebook](https://www.facebook.com/bookmarks/pages), und erstellen Sie eine **neue Facebook-Seite** .
    1. Lassen Sie für die Facebook-App den Zugriff auf diese Seite zu, indem Sie die folgenden Schritte ausführen:
        1. Navigieren Sie zum [Graph-API-Explorer](https://developers.facebook.com/tools/explorer/).
        1. Wählen Sie die Option **Anwendung** .
        1. Wählen Sie die Option **Page Access Token** (Seitenzugriffstoken), und senden Sie eine **Get** -Anforderung.
        1. Klicken Sie in der Antwort auf die **Page ID** (Seiten-ID).
        1. Fügen Sie jetzt **/subscribed_apps** an die URL an, und senden Sie eine **Get** -Anforderung (leere Antwort).
        1. Übermitteln Sie eine **POST** -Anforderung. Sie erhalten eine Antwort der Form **success: true** .

3. Erstellen Sie ein nicht ablaufendes Graph-API-Zugriffstoken.

    1. Navigieren Sie zum [Graph-API-Explorer](https://developers.facebook.com/tools/explorer/).
    2. Wählen Sie die Option **Application** (Anwendung).
    3. Wählen Sie die Option **Get User Access Token** (Benutzerzugriffstoken abrufen).
    4. Wählen Sie unter **Select Permissions** (Berechtigungen auswählen) die Optionen **manage_pages** und **publish_pages** .
    5. Wir verwenden das **Zugriffstoken** (kurzlebiges Token) im nächsten Schritt.

4. Für die nächsten Schritte verwenden wir Postman.

    1. Öffnen Sie **Postman** (oder rufen Sie die Anwendung [hier](https://www.getpostman.com/) ab).
    2. Importieren Sie diese beiden Dateien:
        1. [Postman-Sammlung](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman-Umgebung](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Aktualisieren Sie diese Umgebungsvariablen:
    
        | Schlüssel | Wert   | 
        | -------------------- |-------------|
        | appId   | Fügen Sie hier Ihre Facebook-App-ID ein.  | 
        | appSecret | Fügen Sie hier Ihr Facebook-App-Geheimnis ein. | 
        | short_lived_token | Fügen Sie das kurzlebige Benutzerzugriffstoken ein, das Sie im vorherigen Schritt generiert haben. |
    4. Führen Sie nun die drei APIs aus, die in der Sammlung aufgeführt sind: 
        1. Wählen Sie **Generate Long-Lived Access Token** (Langlebiges Zugriffstoken generieren), und klicken Sie auf **Send** (Senden).
        2. Wählen Sie **Get User ID** (Benutzer-ID abrufen), und klicken Sie auf **Send** (Senden).
        3. Wählen Sie **Get Permanent Page Access Token** (Dauerhaftes Seitenzugriffstoken abrufen), und klicken Sie auf **Send** (Senden).
    5. Kopieren Sie den Wert für **access_token** aus der Antwort, und weisen Sie ihn der App-Einstellung **fb:PageAccessToken** zu.

Die Lösung sendet alle Bilder und Texte, die auf Ihrer Facebook-Seite gepostet werden, an Content Moderator. Jetzt werden die Workflows aufgerufen, die Sie zuvor konfiguriert haben. Die Inhalte, die die in den Workflows definierten Kriterien nicht erfüllen, werden an Überprüfungsfunktionen im Prüfungstool übergeben. Der Rest des Inhalts wird automatisch veröffentlicht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein Programm für die Produktbildanalyse eingerichtet, um sie mit produkttypspezifischen Tags zu versehen und ein Prüfungsteam beim Treffen fundierter Entscheidungen für die Inhaltsmoderation zu unterstützen. Machen Sie sich als Nächstes mit den Details der Bildmoderation vertraut.

> [!div class="nextstepaction"]
> [Bildmoderation](./image-moderation-api.md)