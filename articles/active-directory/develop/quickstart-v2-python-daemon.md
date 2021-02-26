---
title: 'Schnellstart: Aufrufen von Microsoft Graph über einen Python-Daemon | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie ein Python-Prozess unter Verwendung der App-eigenen Identität ein Zugriffstoken abrufen und eine API aufrufen kann, die durch Microsoft Identity Platform geschützt ist.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 734fad7d3f4fb7a2a816d9ad10fb6b15e2faf9e2
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820403"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Python-Konsolen-App unter Verwendung der Identität der App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Python-Anwendung mithilfe der App-Identität ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen und eine [Liste mit Benutzern](/graph/api/user-list) im Verzeichnis anzuzeigen. Das Codebeispiel veranschaulicht, wie ein unbeaufsichtigter Auftrag oder Windows-Dienst mit einer Anwendungsidentität anstelle der Identität eines Benutzers ausgeführt werden kann. 

> [!div renderon="docs"]
> ![Funktionsweise der in dieser Schnellstartanleitung generierten Beispiel-App](media/quickstart-v2-python-daemon/python-console-daemon.svg)

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Beispiel benötigen Sie Folgendes:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) oder [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App

> [!div renderon="docs" class="sxs-lookup"]
>
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden: „Express“ (Option 1 unten) und „Manuell“ (Option 2).
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
>
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie unter **Name** einen Namen für Ihre Anwendung ein (beispielsweise `Daemon-console`). Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
> 1. Wählen Sie unter **Geheimer Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus, geben Sie einen Namen ein, und wählen Sie dann **Hinzufügen** aus. Notieren Sie den Wert für den geheimen Schlüssel an einem sicheren Ort, damit Sie ihn in einem späteren Schritt verwenden können.
> 1. Wählen Sie unter **Verwalten** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Wählen Sie **Microsoft Graph**.
> 1. Wählen Sie **Anwendungsberechtigungen**.
> 1. Wählen Sie unter dem Knoten **Benutzer** die Option **User.Read.All** und dann **Berechtigungen hinzufügen** aus.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>Herunterladen und Konfigurieren der Schnellstart-App
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal
> Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die Anwendungsberechtigung **User.Read.All** aus der Graph-API hinzufügen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Make these changes for me]() (Diese Änderungen für mich vornehmen)
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-netcore-daemon/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-python-project"></a>Schritt 2: Herunterladen des Python-Projekts

> [!div renderon="docs"]
> [Laden Sie das Python-Daemon-Projekt herunter.](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip).

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-python-project"></a>Schritt 3: Konfigurieren des Python-Projekts
>
> 1. Extrahieren Sie die ZIP-Datei in einem lokalen Ordner in der Nähe des Datenträger-Stammverzeichnisses (beispielsweise **C:\Azure-Samples**).
> 1. Navigieren Sie zum Unterordner **1-Call-MsGraph-WithSecret**.
> 1. Bearbeiten Sie **parameters.json**, und ersetzen Sie die Felder `authority`, `client_id` und `secret` durch den folgenden Ausschnitt:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Hierbei gilt:
>    - `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.
>    - `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>    - `Enter_the_Client_Secret_Here`: Ersetzen Sie diesen Wert durch den geheimen Clientschlüssel, den Sie in Schritt 1 erstellt haben.
>
> > [!TIP]
> > Die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** finden Sie im Azure-Portal auf der Seite **Übersicht** der App. Navigieren Sie zum Generieren eines neuen Schlüssels zur Seite **Zertifikate & Geheimnisse**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Schritt 3: Administratorzustimmung

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Schritt 4: Administratorzustimmung

Wenn Sie zu diesem Zeitpunkt versuchen, die Anwendung auszuführen, wird der Fehler *HTTP 403 – Verboten* angezeigt: `Insufficient privileges to complete the operation`. Dieser Fehler tritt auf, da für eine *nur für die App geltende Berechtigung* eine Administratoreinwilligung erforderlich ist. Ein globaler Administrator Ihres Verzeichnisses muss der Anwendung also seine Einwilligung geben. Wählen Sie je nach Ihrer Rolle eine der unten angegebenen Optionen aus:

##### <a name="global-tenant-administrator"></a>Globaler Mandantenadministrator

> [!div renderon="docs"]
> Gehen Sie wie folgt vor, wenn Sie ein globaler Mandantenadministrator sind: Navigieren Sie im Azure-Portal unter **App-Registrierungen** zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für „{Mandantenname}“ erteilen** aus. (Hierbei steht „{Mandantenname}“ für den Namen Ihres Verzeichnisses.)

> [!div renderon="portal" class="sxs-lookup"]
> Gehen Sie als globaler Administrator wie folgt vor: Navigieren Sie zur Seite **API-Berechtigungen**, und wählen Sie **Administratorzustimmung für <Mandantennamen_hier_eingeben> erteilen** aus.
> > [!div id="apipermissionspage"]
> > [Navigieren Sie zur Seite „API-Berechtigungen“]().

##### <a name="standard-user"></a>Standardbenutzer

Wenn Sie ein Standardbenutzer Ihres Mandanten sind, bitten Sie einen globalen Administrator, die Administratoreinwilligung für Ihre Anwendung zu erteilen. Übermitteln Sie hierzu die folgende URL an Ihren Administrator:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Hierbei gilt:
>> * `Enter_the_Tenant_Id_Here`: Ersetzen Sie diesen Wert durch die **Mandanten-ID** oder den **Mandantennamen** (z.B. „contoso.microsoft.com“).
>> * `Enter_the_Application_Id_Here` ist die **Anwendungs-ID (Client)** für die von Ihnen registrierte Anwendung.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Schritt 4: Ausführen der Anwendung

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Schritt 5: Ausführen der Anwendung

Sie müssen einmalig die Abhängigkeiten dieses Beispiels installieren.

```console
pip install -r requirements.txt
```

Führen Sie dann die Anwendung über die Eingabeaufforderung oder über die Konsole aus:

```console
python confidential_client_secret_sample.py parameters.json
```

Die Konsolenausgabe sollte ein JSON-Fragment enthalten, das eine Liste der Benutzer in Ihrem Azure AD-Verzeichnis darstellt.

> [!IMPORTANT]
> Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da das Clientgeheimnis Ihren Projektdateien als Nur-Text hinzugefügt wird, wird aus Sicherheitsgründen empfohlen, ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor die Anwendung als Produktionsanwendung eingestuft wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie in [diesen Anweisungen](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) im zweiten Ordner (**2-Call-MsGraph-WithCertificate**) des GitHub-Repositorys für dieses Beispiel.

## <a name="more-information"></a>Weitere Informationen

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden. In dieser Schnellstartanleitung werden wie beschrieben Token angefordert, indem keine delegierten Berechtigungen verwendet werden, sondern die eigene Identität der Anwendung. Der hier genutzte Authentifizierungsablauf wird als *[OAuth-Ablauf mit Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md)* bezeichnet. Weitere Informationen zur Verwendung von MSAL Python mit Daemon-Apps finden Sie in [diesem Artikel](scenario-daemon-overview.md).

 MSAL Python kann mithilfe des folgenden pip-Befehls installiert werden:

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-Initialisierung

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```Python
import msal
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Hierbei gilt: |BESCHREIBUNG |
> |---------|---------|
> | `config["secret"]` | Der geheime Clientschlüssel, der für die Anwendung im Azure-Portal erstellt wird. |
> | `config["client_id"]` | Die **Anwendungs-ID (Client)** für die im Azure-Portal registrierte Anwendung. Dieser Wert befindet sich im Azure-Portal auf der Seite **Übersicht** der App. |
> | `config["authority"]`    | Der STS-Endpunkt für den zu authentifizierenden Benutzer. Normalerweise `https://login.microsoftonline.com/{tenant}` für die öffentliche Cloud, wobei „{tenant}“ der Name Ihres Mandanten bzw. Ihre Mandanten-ID ist.|

Weitere Informationen finden Sie in der [Referenzdokumentation für `ConfidentialClientApplication`](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

### <a name="requesting-tokens"></a>Anfordern von Token

Verwenden Sie die `AcquireTokenForClient`-Methode, um ein Token mit der Identität einer App anzufordern:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `config["scope"]` | Enthält die angeforderten Bereiche. Für vertrauliche Clients sollte ein Format wie `{Application ID URI}/.default` verwendet werden. Hiermit wird angegeben, dass die angeforderten Bereiche diejenigen sind, die im App-Objekt, das im Azure-Portal festgelegt ist, statisch definiert sind (für Microsoft Graph wird für `{Application ID URI}` auf `https://graph.microsoft.com` verwiesen). Für benutzerdefinierte Web-APIs wird `{Application ID URI}` im Azure-Portal unter **App-Registrierungen** im Abschnitt **Eine API verfügbar machen** definiert.|

Weitere Informationen finden Sie in der [Referenzdokumentation für `AcquireTokenForClient`](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Daemon-Anwendungen finden Sie auf der Landing Page des Szenarios.

> [!div class="nextstepaction"]
> [Daemon-App zum Aufrufen von Web-APIs](scenario-daemon-overview.md)
