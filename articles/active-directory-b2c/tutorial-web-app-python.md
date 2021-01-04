---
title: 'Tutorial: Aktivieren der Authentifizierung in einer Python-Webanwendung'
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C verwenden, um eine Benutzeranmeldung für eine Python Flask-Webanwendung bereitzustellen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: b4455f21ae7243ab7a15e8d746d6674289f9fdb5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953014"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Tutorial: Aktivieren der Authentifizierung in einer Python-Webanwendung mit Azure AD B2C

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C (Azure AD B2C) für die Registrierung und Anmeldung von Benutzern in einer Python Flask-Webanwendung verwenden.

Dieses Tutorial umfasst folgende Punkte:

> [!div class="checklist"]
> * Hinzufügen einer Antwort-URL zu einer in Ihrem Azure AD B2C-Mandanten registrierten Anwendung
> * Herunterladen eines Codebeispiels von GitHub
> * Ändern des Codes der Beispielanwendung zur Verwendung mit Ihrem Mandanten
> * Registrieren mithilfe Ihres Benutzerablaufs für die Registrierung/Anmeldung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen die folgenden Azure AD B2C-Ressourcen, bevor Sie mit den Schritten dieses Tutorials fortfahren können:

* [Azure AD B2C-Mandant](tutorial-create-tenant.md)
* Bei Ihrem Mandaten [registrierte Anwendung](tutorial-register-applications.md) sowie deren *Anwendungs-ID (Client)* und *geheimen Clientschlüssel*
* Im Mandanten [erstellte Benutzerflows](tutorial-create-user-flows.md)

Darüber hinaus benötigen Sie in Ihrer lokalen Entwicklungsumgebung Folgendes:

* [Visual Studio Code](https://code.visualstudio.com/) oder ein anderer Code-Editor
* [Python](https://nodejs.org/en/download/) 2.7+ oder 3+

## <a name="add-a-redirect-uri"></a>Hinzufügen eines Umleitungs-URI

Im zweiten Tutorial, das Sie zur Vorbereitung absolviert haben, wurde eine Webanwendung unter Azure AD B2C registriert. Um die Kommunikation mit dem Codebeispiel in diesem Tutorial zu ermöglichen, muss der Anwendungsregistrierung eine Antwort-URL (auch Umleitungs-URI genannt) hinzugefügt werden.

Zum Aktualisieren einer Anwendung in Ihrem Azure AD B2C-Mandanten können Sie unsere neue einheitliche Benutzeroberfläche **App-Registrierungen** oder unsere alte Benutzeroberfläche **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **App-Registrierungen**, die Registerkarte **Anwendungen mit Besitzer** und dann die Anwendung *webapp1* aus.
1. Wählen Sie unter **Verwalten** die Option **Authentifizierung** aus.
1. Wählen Sie unter **Web** den Link **URI hinzufügen** aus, geben Sie `http://localhost:5000/getAToken` in das Textfeld ein.
1. Wählen Sie **Speichern** aus.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Anwendungen (Legacy)** und anschließend die Anwendung *webapp1* aus.
1. Fügen Sie unter **Antwort-URL** Folgendes hinzu: `http://localhost:5000/getAToken`.
1. Wählen Sie **Speichern** aus.
* * *

## <a name="get-the-sample-code"></a>Laden Sie den Beispielcode herunter

In diesem Tutorial wird ein von GitHub heruntergeladenes Codebeispiel für die Verwendung mit Ihrem B2C-Mandanten konfiguriert. Das Beispiel veranschaulicht, wie Azure AD B2C von einer Python Flask-Webanwendung für die Benutzerregistrierung und -anmeldung genutzt werden kann.

[Laden Sie ein ZIP-Archiv herunter](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip), oder klonen Sie das [Repository mit dem Codebeispiel](https://github.com/Azure-Samples/ms-identity-python-webapp) von GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Aktualisieren des Beispiels

Nachdem Sie das Beispiel abgerufen haben, konfigurieren Sie die Anwendung für die Verwendung Ihres Azure AD B2C-Mandanten, Ihrer Anwendungsregistrierung und Ihrer Benutzerflows.

Führen Sie im Stammverzeichnis des Projekts die folgenden Schritte aus:

1. Benennen Sie die Datei *app_config.py* in *app_config.py.OLD* um.
1. Benennen Sie die Datei *app_config_b2c.py* in *app_config.py* um.

Aktualisieren Sie die neu umbenannte Datei *app_config.py* mit Werten für Ihren Azure AD B2C-Mandanten und für die Anwendungsregistrierung, die Sie im Rahmen der Vorbereitung erstellt haben.

1. Öffnen Sie die Datei *app_config.py* im Editor.
1. Aktualisieren Sie den Wert `b2c_tenant` mit dem Namen Ihres Azure AD B2C-Mandanten (beispielsweise *contosob2c*).
1. Aktualisieren Sie die Werte vom Typ `*_user_flow` jeweils so, dass sie den Namen der Benutzerflows entsprechen, die Sie zur Vorbereitung erstellt haben.
1. Aktualisieren Sie den Wert `CLIENT_ID` mit der **Anwendungs-ID (Client)** der Webanwendung, die Sie im Rahmen der Vorbereitung erstellt haben.
1. Aktualisieren Sie den Wert `CLIENT_SECRET` mit dem Wert des **geheimen Clientschlüssels**, den Sie im Rahmen der Vorbereitung erstellt haben. Aus Sicherheitsgründen empfiehlt es sich gegebenenfalls, den Wert in einer **Umgebungsvariablen** zu speichern, wie in den Kommentaren empfohlen.

Der obere Bereich der Datei *app_config.py* sollte nun in etwa wie der folgende Codeausschnitt aussehen:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Wie in den Kommentaren des Codeausschnitts angemerkt, empfiehlt es sich, Geheimnisse **nicht** im Klartext Ihres Anwendungscodes zu speichern. Die hartcodierte Variable in dem Codebeispiel dient *ausschließlich* zur Vereinfachung. Verwenden Sie ggf. eine Umgebungsvariable oder einen Geheimnisspeicher wie Azure Key Vault.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Wechseln Sie in Ihrer Konsole oder in Ihrem Terminal zum Verzeichnis mit dem Beispiel. Beispiel:

    ```console
    cd ms-identity-python-webapp
    ```
1. Führen Sie die folgenden Befehle aus, um die erforderlichen Pakete aus PyPi zu installieren und die Web-App auf Ihrem lokalen Computer auszuführen:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    Im Konsolenfenster wird die Portnummer der lokal ausgeführten Anwendung angezeigt:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Navigieren Sie zu `http://localhost:5000`, um die auf Ihrem lokalen Computer ausgeführte Webanwendung anzuzeigen.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Webbrowser mit lokal ausgeführter Python Flask-Webanwendung":::

### <a name="sign-up-using-an-email-address"></a>Registrieren mit einer E-Mail-Adresse

Diese Beispielanwendung unterstützt Registrierung, Anmeldung und Kennwortzurücksetzung. In diesem Tutorial registrieren Sie sich mit einer E-Mail-Adresse.

1. Wählen Sie **Anmelden** aus, um den in einem vorherigen Schritt angegebenen Benutzerablauf *B2C_1_signupsignin1* zu initiieren.
1. Von Azure AD B2C wird eine Anmeldeseite mit einem Registrierungslink angezeigt. Da Sie noch nicht über ein Konto verfügen, klicken Sie auf den Link **Jetzt registrieren**.
1. Der Registrierungsworkflow zeigt eine Seite an, über die die Identität des Benutzers anhand einer E-Mail-Adresse erfasst und überprüft wird. Darüber hinaus werden im Rahmen des Registrierungsworkflows auch das Kennwort des Benutzers sowie die angeforderten Attribute erfasst, die im Benutzerablauf definiert wurden.

    Verwenden Sie eine gültige E-Mail-Adresse, und bestätigen Sie sie mithilfe des Prüfcodes. Legen Sie ein Kennwort fest. Geben Sie Werte für die angeforderten Attribute ein.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Vom Azure AD B2C-Benutzerablauf angezeigte Registrierungsseite":::

1. Wählen Sie **Erstellen** aus, um im Azure AD B2C-Verzeichnis ein lokales Konto zu erstellen.

Wenn Sie **Erstellen** auswählen, wird von der Anwendung der Name des angemeldeten Benutzers angezeigt.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Webbrowser mit Python Flask-Webanwendung und angemeldetem Benutzer":::

Wählen Sie zum Testen der Anmeldung den Link **Abmelden** und anschließend **Anmelden** aus, und melden Sie sich mit der bei der Registrierung eingegebenen E-Mail-Adresse und dem entsprechenden Kennwort an.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Python Flask-Webanwendung so konfiguriert, dass sie mit einem Benutzerflow in Ihrem Azure AD B2C-Mandanten verwendet werden kann, um eine Registrierungs- und Anmeldefunktion bereitzustellen. Dazu haben Sie folgende Schritte ausgeführt:

> [!div class="checklist"]
> * Hinzufügen einer Antwort-URL zu einer in Ihrem Azure AD B2C-Mandanten registrierten Anwendung
> * Herunterladen eines Codebeispiels von GitHub
> * Ändern des Codes der Beispielanwendung zur Verwendung mit Ihrem Mandanten
> * Registrieren mithilfe Ihres Benutzerablaufs für die Registrierung/Anmeldung

Im nächsten Tutorial erfahren Sie, wie Sie die Benutzeroberfläche der Benutzerflowseiten anpassen, die Benutzern durch Azure AD B2C angezeigt werden:

> [!div class="nextstepaction"]
> [Tutorial: Anpassen der Benutzeroberfläche in Azure Active Directory B2C >](tutorial-customize-ui.md)