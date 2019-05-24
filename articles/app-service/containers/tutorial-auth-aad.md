---
title: Umfassendes Authentifizieren und Autorisieren von Benutzern auf Linux – Azure App Service | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die App Service-Authentifizierung und -Autorisierung zum Schützen Ihrer auf Linux ausgeführten App Service-Apps verwenden, einschließlich des Zugriffs auf Remote-APIs.
keywords: App Service, Azure App Service, AuthN, AuthZ, sicher, Sicherheit, mehrstufig, Azure Active Directory, Azure AD
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/26/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ed056bf28881f391ed1ba16a875259e8e420b39d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138091"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service unter Linux

[App Service unter Linux](app-service-linux-intro.md) bietet einen hochgradig skalierbaren Webhostingdienst mit Self-Patching unter Linux-Betriebssystemen. Außerdem verfügt App Service über integrierte Unterstützung für die [Benutzerauthentifizierung und -autorisierung](../overview-authentication-authorization.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). In diesem Tutorial wird beschrieben, wie Sie Ihre Apps per App Service-Authentifizierung und -Autorisierung schützen. Es wird eine ASP.NET Core-App mit einem Angular.js-Front-End verwendet (aber nur zu Beispielzwecken). Für die App Service-Authentifizierung und -Autorisierung werden alle Sprachlaufzeiten unterstützt, und Sie erfahren im Tutorial, wie Sie diese auf Ihre bevorzugte Sprache anwenden.

Im Tutorial wird anhand der Beispiel-App veranschaulicht, wie Sie eine eigenständige App schützen (unter [Aktivieren der Authentifizierung und Autorisierung für die Back-End-App](#enable-authentication-and-authorization-for-back-end-app)).

![Einfache Authentifizierung und Autorisierung](./media/tutorial-auth-aad/simple-auth.png)

Außerdem wird veranschaulicht, wie Sie eine mehrstufige App schützen, indem Sie im Namen des authentifizierten Benutzers auf eine geschützte Back-End-API zugreifen – sowohl [über Servercode](#call-api-securely-from-server-code) als auch [über Browsercode](#call-api-securely-from-browser-code).

![Erweiterte Authentifizierung und Autorisierung](./media/tutorial-auth-aad/advanced-auth.png)

Dies sind nur einige mögliche Authentifizierungs- und Autorisierungsszenarien in App Service. 

Hier ist eine umfassendere Liste mit den Kenntnissen angegeben, die im Tutorial vermittelt werden:

> [!div class="checklist"]
> * Aktivieren der integrierten Authentifizierung und Autorisierung
> * Schützen von Apps vor nicht authentifizierten Anforderungen
> * Verwenden von Azure Active Directory als Identitätsanbieter
> * Zugreifen auf eine Remote-App im Namen des angemeldeten Benutzers
> * Schützen von Dienst-zu-Dienst-Aufrufen per Tokenauthentifizierung
> * Verwenden von Zugriffstoken aus Servercode
> * Verwenden von Zugriffstoken aus Clientcode (Browser)

Die Schritte in diesem Tutorial können unter macOS, Linux und Windows ausgeführt werden.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* [Git installieren](https://git-scm.com/).
* [Installieren von .NET Core 2.0](https://www.microsoft.com/net/core/)

## <a name="create-local-net-core-app"></a>Erstellen einer lokalen .NET Core-App

In diesem Schritt richten Sie das lokale .NET Core-Projekt ein. Sie verwenden dasselbe Projekt, um eine Back-End-API-App und eine Front-End-Web-App bereitzustellen.

### <a name="clone-and-run-the-sample-application"></a>Klonen und Ausführen der Beispielanwendung

Führen Sie die folgenden Befehle aus, um das Beispielrepository zu klonen und auszuführen.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Navigieren Sie zu `http://localhost:5000`, und versuchen Sie, Todo-Elemente hinzuzufügen, zu bearbeiten und zu entfernen. 

![Lokal ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/local-run.png)

Sie können ASP.NET Core jederzeit beenden, indem Sie im Terminal `Ctrl+C` drücken.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Bereitstellen von Apps für Azure

In diesem Schritt stellen Sie das Projekt für zwei App Service-Apps bereit. Eine ist das Front-End-App und die andere ist die Back-End-App.

### <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Führen Sie in Cloud Shell die folgenden Befehle aus, um zwei App Service-Apps zu erstellen. Ersetzen Sie _&lt;front\_end\_app\_name>_ und _&lt;back\_end\_app\_name>_ durch zwei global eindeutige App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`). Weitere Informationen zu den einzelnen Befehlen finden Sie unter [Erstellen einer .NET Core-App in App Service für Linux](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku B1 --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back_end_app_name> --runtime "dotnetcore|2.0" --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> Speichern Sie die URLs der Git-Remotespeicherorte für Ihre Front-End-App und Back-End-App, die in der Ausgabe von `az webapp create` angezeigt werden.
>

### <a name="configure-cors"></a>Konfigurieren von CORS

Dieser Schritt bezieht sich nicht auf die Authentifizierung und Autorisierung. Sie benötigen diesen jedoch später zum [Aufrufen der Back-End-API über den Front-End-Browsercode](#call-api-securely-from-browser-code), sodass Ihr Browser domänenübergreifende API-Aufrufe über Ihre App „Angular.js“ ausführen kann. App Service unter Linux weist keine integrierten CORS-Funktionen wie [sein Pendant für Windows](../app-service-web-tutorial-rest-api.md#add-cors-functionality) auf, weshalb Sie diese manuell für die Back-End-App hinzufügen müssen.

Öffnen Sie im lokalen Repository die Datei _Startup.cs_. Fügen Sie der `ConfigureServices(IServiceCollection services)`-Methode die folgende Codezeile hinzu:

```csharp
services.AddCors();
```

Fügen Sie der `Configure(IApplicationBuilder app)`-Methode die folgende Codezeile zu Beginn hinzu (ersetzen Sie  *\<Name_der_Front-End-App>*):

```csharp
app.UseCors(builder =>
    builder.WithOrigins("http://<front_end_app_name>.azurewebsites.net"));
```

Speichern Sie die Änderungen. Führen Sie im _lokalen Terminalfenster_ die folgenden Befehle aus, um die Änderungen im Git-Repository zu committen.

```bash
git add .
git commit -m "add CORS to back end"
```

> [!NOTE]
> Dieser Code muss nicht zwischen den Front-End- und Back-End-Apps freigegeben werden. Er zielt nicht auf CORS-Anforderungen für die Front-End-App ab.
> 

### <a name="push-to-azure-from-git"></a>Übertragen von Git an Azure mithilfe von Push

Führen Sie im lokalen Terminalfenster die folgenden Git-Befehle aus, um die Bereitstellung für die Back-End-App durchzuführen. Ersetzen Sie _&lt;deploymentLocalGitUrl-of-back-end-app>_ durch die URL des Git-Remotespeicherorts, die Sie in [Erstellen von Azure-Ressourcen](#create-azure-resources) gespeichert haben. Wenn Sie von der Git-Anmeldeinformationsverwaltung zur Eingabe von Anmeldeinformationen aufgefordert werden, müssen Sie [Ihre Anmeldeinformationen für die Bereitstellung](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) eingeben (nicht die Anmeldeinformationen, die Sie für die Anmeldung am Azure-Portal verwenden).

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Führen Sie im lokalen Terminalfenster die folgenden Git-Befehle aus, um den gleichen Code für die Front-End-App bereitzustellen. Ersetzen Sie _&lt;deploymentLocalGitUrl-of-front-end-app>_ durch die URL des Git-Remotespeicherorts, die Sie in [Erstellen von Azure-Ressourcen](#create-azure-resources) gespeichert haben.

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-azure-apps"></a>Navigieren zu den Azure-Apps

Navigieren Sie in einem Browser zu den folgenden URLs, um verfolgen zu können, wie die beiden Apps ausgeführt werden.

```
http://<back_end_app_name>.azurewebsites.net
http://<front_end_app_name>.azurewebsites.net
```

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Wenn Ihre App neu gestartet wird, fällt Ihnen ggf. auf, dass neue Daten gelöscht wurden. Dieses Verhalten ist erwünscht, da für die ASP.NET Core-Beispiel-App eine In-Memory Database verwendet wird.
>
>

## <a name="call-back-end-api-from-front-end"></a>Aufrufen der Back-End-API vom Front-End

In diesem Schritt legen Sie für den Servercode der App fest, dass auf die Back-End-API zugegriffen werden soll. Später können Sie den authentifizierten Zugriff vom Front-End auf das Back-End aktivieren.

### <a name="modify-front-end-code"></a>Ändern des Front-End-Codes

Öffnen Sie _Controllers/TodoController.cs_ im lokalen Repository. Fügen Sie am Anfang der `TodoController`-Klasse die folgenden Zeilen hinzu, und ersetzen Sie _&lt;back\_end\_app\_name>_ durch den Namen Ihrer Back-End-App:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back_end_app_name>.azurewebsites.net";
```

Suchen Sie nach der `GetAll()`-Methode, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

In der ersten Zeile wird für die Back-End-API-App der Aufruf `GET /api/Todo` durchgeführt.

Suchen Sie anschließend nach der `GetById(long id)`-Methode, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

In der ersten Zeile wird für die Back-End-API-App der Aufruf `GET /api/Todo/{id}` durchgeführt.

Suchen Sie anschließend nach der `Create([FromBody] TodoItem item)`-Methode, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

In der ersten Zeile wird für die Back-End-API-App der Aufruf `POST /api/Todo` durchgeführt.

Suchen Sie anschließend nach der `Update(long id, [FromBody] TodoItem item)`-Methode, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

In der ersten Zeile wird für die Back-End-API-App der Aufruf `PUT /api/Todo/{id}` durchgeführt.

Suchen Sie anschließend nach der `Delete(long id)`-Methode, und ersetzen Sie den Code in den geschweiften Klammern durch Folgendes:

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

In der ersten Zeile wird für die Back-End-API-App der Aufruf `DELETE /api/Todo/{id}` durchgeführt.

Speichern Sie Ihre gesamten Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Überprüfen der Änderungen

Navigieren Sie zu `http://<front_end_app_name>.azurewebsites.net`, und fügen Sie einige Elemente hinzu, z.B. `from front end 1` und `from front end 2`.

Navigieren Sie zu `http://<back_end_app_name>.azurewebsites.net`, um die aus der Front-End-App hinzugefügten Elemente anzuzeigen. Fügen Sie außerdem einige Elemente hinzu, z.B. `from back end 1` und `from back end 2`. Aktualisieren Sie anschließend die Front-End-App, um zu prüfen, ob die Änderungen widergespiegelt werden.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Konfigurieren der Authentifizierung

In diesem Schritt aktivieren Sie die Authentifizierung und Autorisierung für die beiden Apps. Außerdem konfigurieren Sie die Front-End-App so, dass ein Zugriffstoken generiert wird, mit dem Sie authentifizierte Aufrufe für die Back-End-App durchführen können.

Sie verwenden Azure Active Directory als Identitätsanbieter. Weitere Informationen finden Sie unter [Konfigurieren der Azure Active Directory-Authentifizierung für die App Services-Anwendung](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Aktivieren der Authentifizierung und Autorisierung für die Back-End-App

Öffnen Sie im [Azure-Portal](https://portal.azure.com) die Verwaltungsseite Ihrer Back-End-App, indem Sie im Menü auf der linken Seite auf Folgendes klicken: **Resource groups** > **myAuthResourceGroup** > _\<back\_end\_app\_name>_.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/portal-navigate-back-end.png)

Klicken Sie in Ihrer Back-End-App im Menü auf der linken Seite auf **Authentifizierung/Autorisierung**, und aktivieren Sie anschließend die App Service-Authentifizierung, indem Sie auf **Ein** klicken.

Wählen Sie unter **Die auszuführende Aktion, wenn die Anforderung nicht authentifiziert ist** die Option **Mit Azure Active Directory anmelden**.

Klicken Sie unter **Authentifizierungsanbieter** auf **Azure Active Directory**. 

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/configure-auth-back-end.png)

Klicken Sie auf **Express**, und übernehmen Sie die Standardeinstellungen, um eine neue AD-App zu erstellen. Klicken Sie anschließend auf **OK**.

Klicken Sie auf der Seite **Authentifizierung/Autorisierung** auf **Speichern**. 

Aktualisieren Sie die Seite, nachdem die Benachrichtigung mit der Meldung `Successfully saved the Auth Settings for <back_end_app_name> App` angezeigt wird.

Klicken Sie erneut auf **Azure Active Directory** und dann auf **Anwendung verwalten**.

Kopieren Sie die **Anwendungs-ID** von der Verwaltungsseite der AD-Anwendung in einen Editor. Sie benötigen diesen Wert später noch.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Aktivieren der Authentifizierung und Autorisierung für die Front-End-App

Führen Sie die gleichen Schritte für die Front-End-App aus, aber lassen Sie den letzten Schritt weg. Für die Front-End-App ist die **Anwendungs-ID** nicht erforderlich. Lassen Sie die Seite **Azure Active Directory-Einstellungen** geöffnet.

Wenn Sie möchten, können Sie zu `http://<front_end_app_name>.azurewebsites.net` navigieren. Sie sollten auf eine Anmeldeseite geleitet werden. Nachdem Sie sich angemeldet haben, können Sie trotzdem noch nicht auf die Daten der Back-End-App zugreifen, da Sie noch drei weitere Schritte ausführen müssen:

- Gewähren des Zugriffs auf das Back-End für das Front-End
- Konfigurieren von App Service für die Rückgabe eines verwendbaren Tokens
- Verwenden des Tokens im Code

> [!TIP]
> Falls Fehler auftreten und Sie die Einstellungen für die Authentifizierung/Autorisierung Ihrer App neu konfigurieren, können die Token im Tokenspeicher aus den neuen Einstellungen ggf. nicht neu generiert werden. Um sicherzustellen, dass Ihre Token neu generiert werden können, müssen Sie sich abmelden und neu an der App anmelden. Eine einfache Möglichkeit hierfür ist die Verwendung Ihres Browsers im privaten Modus und das Schließen und erneute Öffnen des Browsers im privaten Modus, nachdem Sie die Einstellungen in Ihren Apps geändert haben.

### <a name="grant-front-end-app-access-to-back-end"></a>Gewähren des Zugriffs auf das Back-End für die Front-End-App

Nachdem Sie die Authentifizierung und Autorisierung für beide Apps aktiviert haben, verfügen beide über Unterstützung durch eine AD-Anwendung. In diesem Schritt gewähren Sie für die Front-End-App Berechtigungen zum Zugreifen auf das Back-End im Namen des Benutzers. (In technischer Hinsicht erteilen Sie der _AD-Anwendung_ des Front-Ends die Berechtigungen zum Zugreifen auf die _AD-Anwendung_ des Back-Ends im Namen des Benutzers.)

An diesem Punkt sollten Sie sich auf der Seite mit den **Azure Active Directory-Einstellungen** für die Front-End-App befinden. Wechseln Sie zurück zu dieser Seite, falls sie nicht bereits angezeigt wird. 

Klicken Sie auf **Berechtigungen verwalten** > **Hinzufügen** > **Select an API** (API auswählen).

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/add-api-access-front-end.png)

Geben Sie auf der Seite **Select an API** (API auswählen) den AD-Anwendungsnamen Ihrer Back-End-App ein. Standardmäßig entspricht dieser Name dem Namen Ihrer Back-End-App. Wählen Sie ihn in der Liste aus, und klicken Sie auf **Auswählen**.

Aktivieren Sie das Kontrollkästchen neben **Access _&lt;AD\_application\_name>_** (Auf <Name der AD-Anwendung> zugreifen). Klicken Sie auf **Auswählen** > **Fertig**.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurieren von App Service für die Rückgabe eines verwendbaren Zugriffstokens

Die Front-End-App verfügt jetzt über die erforderlichen Berechtigungen. In diesem Schritt konfigurieren Sie die App Service-Authentifizierung und -Autorisierung, um ein verwendbares Zugriffstoken für den Zugriff auf das Back-End zu erhalten. Für diesen Schritt benötigen Sie die Anwendungs-ID des Back-Ends, die Sie unter [Aktivieren der Authentifizierung und Autorisierung für die Back-End-App](#enable-authentication-and-authorization-for-back-end-app) kopiert haben.

Melden Sie sich am [Azure-Ressourcen-Explorer](https://resources.azure.com) an. Klicken Sie am oberen Rand der Seite auf **Lesen/Schreiben**, um die Bearbeitung Ihrer Azure-Ressourcen zu ermöglichen.

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/resources-enable-write.png)

Klicken Sie im linken Browser auf **subscriptions** > **_&lt;Ihr\_Abonnement>_** > **resourceGroups** > **myAuthResourceGroup** > **providers** > **Microsoft.Web** > **sites** > **_\<Front-\_End-\_App-\_Name>_** > **config** > **authsettings**.

Klicken Sie in der Ansicht **authsettings** auf **Bearbeiten**. Legen Sie `additionalLoginParams` auf die folgende JSON-Zeichenfolge fest, indem Sie die kopierte Anwendungs-ID verwenden. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back_end_application_id>"],
```

![In Azure App Service ausgeführte ASP.NET Core-API](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Speichern Sie Ihre Einstellungen, indem Sie auf **PUT** klicken.

Ihre Apps sind nun konfiguriert. Das Front-End kann jetzt mit einem geeigneten Zugriffstoken auf das Back-End zugreifen.

Informationen dazu, wie Sie dies für andere Anbieter konfigurieren, finden Sie unter [Refresh access tokens](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens) (Aktualisieren von Zugriffstoken).

## <a name="call-api-securely-from-server-code"></a>Sicheres Aufrufen der API aus Servercode

In diesem Schritt passen Sie den zuvor geänderten Servercode so an, dass authentifizierte Aufrufe der Back-End-API durchgeführt werden können.

Ihre Front-End-App verfügt jetzt über die erforderliche Berechtigung und fügt den Anmeldeparametern die Anwendungs-ID des Back-Ends hinzu. So kann ein Zugriffstoken für die Authentifizierung bei der Back-End-App abgerufen werden. App Service stellt dieses Token für Ihren Servercode bereit, indem in jede authentifizierte Anforderung ein `X-MS-TOKEN-AAD-ACCESS-TOKEN`-Header eingefügt wird (siehe [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) (Abrufen von Token im App-Code)).

> [!NOTE]
> Diese Header werden für alle unterstützten Sprachen eingefügt. Sie greifen darauf zu, indem Sie jeweils das Standardmuster für eine Sprache verwenden.

Öffnen Sie _Controllers/TodoController.cs_ erneut im lokalen Repository. Fügen Sie unter dem Konstruktor `TodoController(TodoContext context)` den folgenden Code hinzu:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["x-ms-token-aad-access_token"]);
}
```

Mit diesem Code wird der HTTP-Standardheader `Authorization: Bearer <access_token>` allen API-Remoteaufrufen hinzugefügt. In der ASP.NET Core-Pipeline für die MVC-Anforderungsausführung wird `OnActionExecuting` direkt vor der jeweiligen Aktionsmethode (z.B. `GetAll()`) ausgeführt, sodass alle ausgehenden API-Aufrufe jetzt über das Zugriffstoken verfügen.

Speichern Sie Ihre gesamten Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Führen Sie die Anmeldung an `http://<front_end_app_name>.azurewebsites.net` erneut durch. Klicken Sie auf der Seite mit der Vereinbarung zur Nutzung der Benutzerdaten auf **Akzeptieren**.

Es sollte nun möglich sein, dass Sie wie zuvor Daten für die Back-End-App erstellen, lesen, aktualisieren und löschen. Der einzige Unterschied ist, dass beide Apps jetzt per App Service-Authentifizierung und -Autorisierung geschützt sind (einschließlich Dienst-zu-Dienst-Aufrufe).

Glückwunsch! Ihr Servercode greift jetzt im Namen des authentifizierten Benutzers auf die Back-End-Daten zu.

## <a name="call-api-securely-from-browser-code"></a>Sicheres Aufrufen der API aus Browsercode

In diesem Schritt legen Sie fest, dass die Angular.js-App des Front-Ends auf die Back-End-API verweist. Auf diese Weise erfahren Sie, wie Sie das Zugriffstoken abrufen und damit API-Aufrufe für die Back-End-App durchführen.

Der Servercode hat Zugriff auf die Anforderungsheader, aber der Clientcode kann auf `GET /.auth/me` zugreifen, um die gleichen Zugriffstoken abzurufen (siehe [Retrieve tokens in app code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code) (Abrufen von Token im App-Code)).

> [!TIP]
> In diesem Abschnitt werden die HTTP-Standardmethoden verwendet, um sichere HTTP-Aufrufe zu demonstrieren. Sie können aber die [Active Directory Authentication Library (ADAL) für JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) verwenden, um das Angular.js-Anwendungsmuster zu vereinfachen.
>

### <a name="point-angularjs-app-to-back-end-api"></a>Verweisen auf die Back-End-API für die Angular.js-App

Öffnen Sie _wwwroot/index.html_ im lokalen Repository.

Legen Sie die Variable `apiEndpoint` in Zeile 51 auf die URL Ihrer Back-End-App (`http://<back_end_app_name>.azurewebsites.net`) fest. Ersetzen Sie _\<back\_end\_app\_name>_ durch Ihren App-Namen in App Service.

Öffnen Sie _wwwroot/app/scripts/todoListSvc.js_ im lokalen Repository, und stellen Sie sicher, dass `apiEndpoint` allen API-Aufrufen vorangestellt wird. Ihre Angular.js-App ruft jetzt die Back-End-APIs auf. 

### <a name="add-access-token-to-api-calls"></a>Hinzufügen des Zugriffstokens zu API-Aufrufen

Fügen Sie der Liste in _wwwroot/app/scripts/todoListSvc.js_ oberhalb der Liste mit den API-Aufrufen (über der Zeile `getItems : function(){`) die folgende Funktion hinzu:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Diese Funktion wird aufgerufen, um den Standardheader `Authorization` mit dem Zugriffstoken festzulegen. Sie rufen sie im nächsten Schritt auf.

Öffnen Sie _wwwroot/app/scripts/app.js_ im lokalen Repository, und suchen Sie nach dem folgenden Code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Ersetzen Sie den gesamten Codeblock durch den folgenden Code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Mit der neuen Änderung wird die Zuordnung `revolve` hinzugefügt, die `/.auth/me` aufruft, und das Zugriffstoken wird festgelegt. Es wird sichergestellt, dass Sie über das Zugriffstoken verfügen, bevor Sie den Controller `todoListCtrl` instanziieren. Auf diese Weise ist das Token in allen API-Aufrufen vom Controller enthalten.

### <a name="deploy-updates-and-test"></a>Bereitstellen von Updates und Durchführen von Tests

Speichern Sie Ihre gesamten Änderungen. Stellen Sie Ihre Änderungen im lokalen Terminalfenster für die Front-End-App bereit, indem Sie die folgenden Git-Befehle verwenden:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navigieren Sie erneut zu `http://<front_end_app_name>.azurewebsites.net`. Es sollte nun möglich sein, dass Sie Daten für die Back-End-App direkt in der Angular.js-App erstellen, lesen, aktualisieren und löschen.

Glückwunsch! Ihr Clientcode greift jetzt im Namen des authentifizierten Benutzers auf die Back-End-Daten zu.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

<a name="next"></a>
## <a name="next-steps"></a>Nächste Schritte

Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren der integrierten Authentifizierung und Autorisierung
> * Schützen von Apps vor nicht authentifizierten Anforderungen
> * Verwenden von Azure Active Directory als Identitätsanbieter
> * Zugreifen auf eine Remote-App im Namen des angemeldeten Benutzers
> * Schützen von Dienst-zu-Dienst-Aufrufen per Tokenauthentifizierung
> * Verwenden von Zugriffstoken aus Servercode
> * Verwenden von Zugriffstoken aus Clientcode (Browser)

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihrer App einen benutzerdefinierten DNS-Namen zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
