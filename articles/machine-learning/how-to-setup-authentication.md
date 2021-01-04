---
title: Einrichten der Authentifizierung
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Authentifizierung für verschiedene Ressourcen und Workflows in Azure Machine Learning einrichten und konfigurieren.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/05/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: 27c8a0b80068124613af15565f387f15ac6b8e57
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027253"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows


Erfahren Sie, wie Sie die Authentifizierung für Ihren Azure Machine Learning-Arbeitsplatz einrichten. Die Authentifizierung für Ihren Azure Machine Learning-Arbeitsbereich basiert größtenteils auf __Azure Active Directory__ (Azure AD). Im Allgemeinen gibt es drei Authentifizierungsworkflows, die Sie beim Herstellen einer Verbindung mit dem Arbeitsbereich verwenden können:

* __Interaktiv:__ Sie verwenden Ihr Konto in Azure Active Directory, um sich entweder direkt zu authentifizieren oder ein Token zu erhalten, das für die Authentifizierung verwendet wird. Die interaktive Authentifizierung wird beim _Experimentieren und bei der iterativen Entwicklung_ verwendet. Mit der interaktiven Authentifizierung können Sie den Zugriff auf Ressourcen (z. B. einen Webdienst) pro Benutzer steuern.

* __Dienstprinzipal__: Sie erstellen ein Dienstprinzipalkonto in Azure Active Directory und verwenden es, um sich zu authentifizieren oder ein Token zu erhalten. Ein Dienstprinzipal wird verwendet, wenn ein _automatisierter Prozess sich beim Dienst authentifizieren_ soll, ohne dass eine Benutzerinteraktion erforderlich ist. Ein Beispiel hierfür wäre ein Continuous-Integration- und Bereitstellungsskript, das ein Modell bei jeder Änderung des Trainingscodes trainiert und testet.

* __Verwaltete Identität__: Wenn Sie das Azure Machine Learning SDK _auf einem virtuellen Azure-Computer_ nutzen, können Sie eine verwaltete Identität für Azure verwenden. Dieser Workflow ermöglicht es der VM, mithilfe der verwalteten Identität eine Verbindung mit dem Arbeitsbereich herzustellen, ohne Anmeldeinformationen im Python-Code zu speichern oder den Benutzer zur Authentifizierung aufzufordern. Azure Machine Learning-Computecluster können auch so konfiguriert werden, dass sie _beim Trainieren von Modellen_ mithilfe einer verwalteten Identität auf den Arbeitsbereich zugreifen.

> [!IMPORTANT]
> Unabhängig vom verwendeten Authentifizierungsworkflow wird die rollenbasierte Zugriffssteuerung von Azure (Role-Based Access Control, RBAC) verwendet, um die zulässige Zugriffsebene (Autorisierung) für die Ressourcen zu beschränken. Beispielsweise könnte ein Administrator oder ein Automatisierungsprozess Zugriff haben, um eine Compute-Instanz zu erstellen, sie aber nicht verwenden, während eine wissenschaftliche Fachkraft für Daten sie verwenden, aber nicht löschen oder erstellen könnte. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md).

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie einen [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).
* [Konfigurieren Sie Ihre Entwicklungsumgebung](how-to-configure-environment.md) für die Installation des Azure Machine Learning SDK, oder verwenden Sie eine [Azure Machine Learning-Compute-Instanz](concept-azure-machine-learning-architecture.md#compute-instance) mit bereits installiertem SDK.

## <a name="azure-active-directory"></a>Azure Active Directory

Alle Authentifizierungsworkflows für Ihren Arbeitsbereich basieren auf Azure Active Directory. Wenn Sie möchten, dass Benutzer sich unter Verwendung individueller Konten authentifizieren, müssen sie über Konten in Ihrem Azure AD verfügen. Wenn Sie Dienstprinzipale verwenden möchten, müssen diese in Ihrem Azure AD vorhanden sein. Verwaltete Identitäten sind ebenfalls ein Feature von Azure AD. 

Weitere Informationen zu Azure AD finden Sie unter [Was ist die Azure Active Directory-Authentifizierung?](..//active-directory/authentication/overview-authentication.md).

Nachdem Sie die Azure AD-Konten erstellt haben, finden Sie unter [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md) Informationen zum Erteilen des Zugriffs auf den Arbeitsbereich und andere Vorgänge in Azure Machine Learning.

## <a name="configure-a-service-principal"></a>Konfigurieren eines Dienstprinzipals

Wenn Sie einen Dienstprinzipal (SP) verwenden möchten, müssen Sie zunächst den Dienstprinzipal erstellen und ihm Zugriff auf Ihren Arbeitsbereich gewähren. Wie bereits erwähnt, wird die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) verwendet, um den Zugriff zu steuern. Sie müssen daher auch entscheiden, welcher Zugriff dem Dienstprinzipal erteilt werden soll.

> [!IMPORTANT]
> Wenn Sie einen Dienstprinzipal verwenden, erteilen Sie ihm den __Mindestzugriff, der für den Task erforderlich ist,__ für den er verwendet wird. Beispielsweise wird einem Dienstprinzipal kein Besitzer- oder Mitwirkendenzugriff erteilt, wenn er lediglich für das Lesen des Zugriffstokens für eine Webbereitstellung verwendet wird.
>
> Der Grund für das Erteilen der geringstmöglichen Zugriffsberechtigungen ist, dass ein Dienstprinzipal ein Kennwort für die Authentifizierung verwendet und dieses möglicherweise als Teil eines Automatisierungsskripts gespeichert wird. Wenn das Kennwort offengelegt wird, wird durch die Beschränkung auf den für einen bestimmten Task erforderlichen Mindestzugriff die mögliche böswillige Verwendung des Dienstprinzipals auf ein Minimum beschränkt.

Die einfachste Art der Erstellung eines Dienstprinzipals und Gewährung von Zugriff auf Ihren Arbeitsbereich ist die Verwendung der [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest). Führen Sie die folgenden Schritte aus, um einen Dienstprinzipal zu erstellen und ihm Zugriff auf Ihren Arbeitsbereich zu gewähren:

> [!NOTE]
> Sie müssen ein Administrator für das Abonnement sein, um alle Schritte ausführen zu können.

1. Authentifizieren Sie sich bei Ihrem Azure-Abonnement:

    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie einen Browser öffnen und die Anweisungen in der Befehlszeile befolgen. Die Anweisungen umfassen das Navigieren zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin) und Eingeben eines Autorisierungscodes.

    Wenn Sie über mehrere Azure-Abonnements verfügen, können Sie den Befehl `az account set -s <subscription name or ID>` verwenden, um das Abonnement festzulegen. Weitere Informationen finden Sie unter [Verwenden mehrerer Azure-Abonnements](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    Andere Methoden zur Authentifizierung finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

1. Installieren Sie die Azure Machine Learning-Erweiterung:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Erstellen Sie den Dienstprinzipal. Im folgenden Beispiel wird ein Dienstprinzipal mit dem Namen **ml-auth** erstellt:

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Die Ausgabe ist eine JSON-Datei wie etwa die folgende. Notieren Sie sich die Werte in den Feldern `clientId`, `clientSecret`und `tenantId`, da Sie diese für andere Schritte in diesem Artikel benötigen.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Rufen Sie mithilfe des Werts für `clientId`, der im vorherigen Schritt zurückgegeben wurde, die Details für den Dienstprinzipal ab:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Der folgende JSON-Code ist ein vereinfachtes Beispiel für die Ausgabe des Befehls. Notieren Sie sich den Wert in Feld `objectId`, da Sie diesen für den nächsten Schritt benötigen.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Erlauben Sie dem Dienstprinzipal, auf Ihren Azure Machine Learning-Arbeitsbereich zuzugreifen. Sie benötigen den Namen Ihres Arbeitsbereichs und den Namen der Ressourcengruppe für die Parameter `-w` und `-g`. Verwenden Sie für den Parameter `--user` den Wert `objectId` aus dem vorherigen Schritt. Über den Parameter `--role` können Sie die Zugriffsrolle für den Dienstprinzipal festlegen. Im folgenden Beispiel wird dem Dienstprinzipal die Rolle **Besitzer** zugewiesen. 

    > [!IMPORTANT]
    > Der Besitzerzugriff ermöglicht es dem Dienstprinzipal, so gut wie jeden Vorgang in Ihrem Arbeitsbereich durchzuführen. Er wird in diesem Dokument verwendet, um zu veranschaulichen, wie Zugriff gewährt wird. In einer Produktionsumgebung empfiehlt Microsoft, dem Dienstprinzipal nur den für die von Ihnen vorgesehene Rolle erforderlichen Mindestzugriff zu erteilen. Informationen zum Erstellen einer benutzerdefinierten Rolle mit dem für Ihr Szenario erforderlichen Zugriff finden Sie unter [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Dieser Aufruf erzeugt bei Erfolg keine Ausgabe.

## <a name="configure-a-managed-identity"></a>Konfigurieren einer verwalteten Identität

> [!IMPORTANT]
> Die verwaltete Identität wird nur unterstützt, wenn das Azure Machine Learning SDK von einem virtuellen Azure-Computer oder mit einem Azure Machine Learning-Computecluster verwendet wird. Die Verwendung einer verwalteten Identität mit einem Computecluster befindet sich derzeit in der Vorschau.

### <a name="managed-identity-with-a-vm"></a>Verwaltete Identität mit einem virtuellen Computer

1. Aktivieren Sie eine vom [System zugewiesene verwaltete Identität für Azure-Ressourcen auf dem virtuellen Computer](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren Arbeitsbereich und dann __Zugriffssteuerung (IAM)__ und __Rollenzuweisung hinzufügen__ aus. Wählen Sie anschließend __Virtueller Computer__ aus der Dropdownliste __Zugriff zuweisen zu__ aus. Wählen Sie schließlich die Identität Ihrer VM aus.

1. Wählen Sie die Rolle aus, die dieser Identität zugewiesen werden soll. Beispiel: Mitwirkender oder eine benutzerdefinierte Rolle. Weitere Informationen finden Sie unter [Steuern des Zugriffs auf Ressourcen](how-to-assign-roles.md).

### <a name="managed-identity-with-compute-cluster"></a>Verwaltete Identität mit Computecluster

Weitere Informationen finden Sie unter [Einrichten der verwalteten Identität für Computecluster](how-to-create-attach-compute-cluster.md#managed-identity).

<a id="interactive-authentication"></a>

## <a name="use-interactive-authentication"></a>Verwenden der interaktiven Authentifizierung

> [!IMPORTANT]
> Die interaktive Authentifizierung verwendet Ihren Browser und erfordert Cookies (einschließlich Cookies von Drittanbietern). Wenn Sie Cookies deaktiviert haben, erhalten Sie möglicherweise eine Fehlermeldung wie „Wir konnten Sie nicht anmelden“. Dieser Fehler kann auch auftreten, wenn Sie [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) aktiviert haben.

In den meisten Beispielen in der Dokumentation und Verwendungsbeispielen wird interaktive Authentifizierung verwendet. Bei Verwendung des SDK gibt es z. B. zwei Funktionsaufrufe, bei denen Sie automatisch über einen auf einer Benutzeroberfläche basierenden Authentifizierungsflow eine Aufforderung erhalten:

* Wenn Sie die Funktion `from_config()` aufrufen, wird die Aufforderung ausgegeben.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Die Funktion `from_config()` sucht nach einer JSON-Datei, die ihre Informationen zur Arbeitsbereichsverbindung enthält.

* Bei Verwendung des Konstruktors `Workspace` für das Angeben von Abonnement-, Ressourcengruppen- und Arbeitsbereichsinformationen wird ebenfalls zur interaktiven Authentifizierung aufgefordert.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Wenn Sie Zugriff auf mehrere Mandanten haben, müssen Sie möglicherweise die-Klasse importieren und explizit definieren, auf welchen Mandanten Sie abzielen. Wenn Sie den Konstruktor für `InteractiveLoginAuthentication` aufrufen, werden Sie außerdem aufgefordert, sich ähnlich wie bei den oben aufgeführten Aufrufen anzumelden.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

Bei Verwendung der Azure CLI wird der Befehl `az login` zur Authentifizierung der CLI-Sitzung verwendet. Weitere Informationen finden Sie unter [Erste Schritte mit der Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli).

> [!TIP]
> Wenn Sie das SDK von einer Umgebung aus verwenden, in der Sie sich zuvor interaktiv mit der Azure CLI authentifiziert haben, können Sie die Klasse `AzureCliAuthentication` verwenden, um sich beim Arbeitsbereich mithilfe der von der CLI zwischengespeicherten Anmeldeinformationen zu authentifizieren:
>
> ```python
> from azureml.core.authentication import AzureCliAuthentication
> cli_auth = AzureCliAuthentication()
> ws = Workspace(subscription_id="your-sub-id",
>                resource_group="your-resource-group-id",
>                workspace_name="your-workspace-name",
>                auth=cli_auth
>                )
> ```

<a id="service-principal-authentication"></a>

## <a name="use-service-principal-authentication"></a>Verwenden der Dienstprinzipalauthentifizierung

Verwenden Sie zum Authentifizieren bei Ihrem Arbeitsbereich mit einem Dienstprinzipal über das SDK den Klassenkonstruktor `ServicePrincipalAuthentication`. Geben Sie dabei die Werte an, die Sie beim Erstellen des Dienstanbieters als Parameter erhalten haben. Der Parameter `tenant_id` wird `tenantId` oben zugeordnet, `service_principal_id` wird `clientId` zugeordnet und `service_principal_password` `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Die Variable `sp` enthält jetzt ein Authentifizierungsobjekt, das Sie direkt im SDK verwenden können. Im Allgemeinen empfiehlt es sich, die oben verwendeten IDs/Geheimnisse in Umgebungsvariablen zu speichern, wie im folgenden Code gezeigt. Das Speichern in Umgebungsvariablen verhindert, dass die Informationen versehentlich in ein GitHub-Repository eingecheckt werden.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Bei automatisierten Workflows, die in Python ausgeführt werden und das SDK primär verwenden, können Sie dieses Objekt in den meisten Fällen unverändert für die Authentifizierung verwenden. Der folgende Code dient zur Authentifizierung bei Ihrem Arbeitsbereich mit dem von Ihnen erstellten Authentifizierungsobjekt.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Verwenden eines Dienstprinzipals in der Azure CLI

Sie können einen Dienstprinzipal für Azure CLI-Befehle verwenden. Weitere Informationen finden Sie unter [Anmelden mithilfe eines Dienstprinzipals](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Verwenden eines Dienstprinzipals mit der REST-API (Vorschau)

Der Dienstprinzipal kann auch zum Authentifizieren bei der Azure Machine Learning-[REST-API](/rest/api/azureml/) (Vorschau) verwendet werden. Sie verwenden den [Fluss zum Gewähren von Clientanmeldeinformationen](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) von Azure Active Directory, der dienstübergreifende Aufrufe für die monitorlose Authentifizierung in automatisierten Workflows ermöglicht. Die Beispiele sind mit der [ADAL-Bibliothek](../active-directory/azuread-dev/active-directory-authentication-libraries.md) sowohl in Python als auch in Node.js implementiert. Sie können aber auch jede Open-Source-Bibliothek verwenden, die OpenID Connect 1.0 unterstützt.

> [!NOTE]
> MSAL.js ist eine neuere Bibliothek als ADAL. Allerdings können Sie mit MSAL.js keine dienstübergreifende Authentifizierung mit Clientanmeldeinformationen durchführen, da es sich in erster Linie um eine clientseitige Bibliothek handelt, die für die an einen bestimmten Benutzer gebundene interaktive Authentifizierung/Authentifizierung über die Benutzeroberfläche gedacht ist. Wir empfehlen für die Erstellung automatisierter Workflows mit der REST-API die Verwendung von ADAL wie unten gezeigt.

#### <a name="nodejs"></a>Node.js

Verwenden Sie die folgenden Schritte, um ein Authentifizierungstoken mithilfe von Node.js zu generieren. Führen Sie in Ihrer Umgebung `npm install adal-node` aus. Verwenden Sie dann `tenantId`, `clientId` und `clientSecret` aus dem Dienstprinzipal, den Sie in den obigen Schritten erstellt haben, als Werte für die übereinstimmenden Variablen im folgenden Skript.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Die Variable `tokenResponse` ist ein Objekt, das das Token und zugehörige Metadaten enthält, wie die Ablaufzeit. Token sind 1 Stunde gültig und können durch erneutes Ausführen desselben Anrufs aktualisiert werden, um ein neues Token zu erhalten. Der folgende Ausschnitt zeigt eine Beispielantwort.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Verwenden Sie die Eigenschaft `accessToken`, um das Authentifizierungstoken abzurufen. In der [Rest-API-Dokumentation](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) finden Sie Beispiele dazu, wie Sie das Token zum Ausführen von API-Aufrufen verwenden.

#### <a name="python"></a>Python

Verwenden Sie die folgenden Schritte, um ein Authentifizierungstoken mithilfe von Python zu generieren. Führen Sie in Ihrer Umgebung `pip install adal` aus. Verwenden Sie dann `tenantId`, `clientId` und `clientSecret` aus dem Dienstprinzipal, den Sie in den obigen Schritten erstellt haben, als Werte für die entsprechenden Variablen im folgenden Skript.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Die Variable `token_response` ist ein Wörterbuch, das das Token und zugehörige Metadaten enthält, wie die Ablaufzeit. Token sind 1 Stunde gültig und können durch erneutes Ausführen desselben Anrufs aktualisiert werden, um ein neues Token zu erhalten. Der folgende Ausschnitt zeigt eine Beispielantwort.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Verwenden Sie `token_response["accessToken"]`, um das Authentifizierungstoken abzurufen. In der [Rest-API-Dokumentation](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) finden Sie Beispiele dazu, wie Sie das Token zum Ausführen von API-Aufrufen verwenden.

#### <a name="java"></a>Java

Rufen Sie in Java über einen standardmäßigen REST-Aufruf das Bearertoken ab:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Der oben stehende Code müsste Ausnahmen und andere Statuscodes als `200 OK` verarbeiten, aber er veranschaulicht das Muster: 

- Sie verwenden Client-ID und Clientgeheimnis, um zu überprüfen, ob Ihr Programm Zugriff haben sollte.
- Sie verwenden die Mandanten-ID, um anzugeben, wo `login.microsoftonline.com` suchen soll.
- Sie verwenden Azure Resource Manager als Quelle des Autorisierungstokens.

## <a name="use-managed-identity-authentication"></a>Verwenden von Authentifizierung der verwalteten Identität

Zur Authentifizierung beim Arbeitsbereich von einer VM oder einem Computecluster, die bzw. der mit einer verwalteten Identität konfiguriert ist, verwenden Sie die Klasse `MsiAuthentication`. Das folgende Beispiel zeigt, wie diese Klasse zur Authentifizierung bei einem Arbeitsbereich verwendet werden kann:

```python
from azureml.core.authentication import MsiAuthentication

msi_auth = MsiAuthentication()

ws = Workspace(subscription_id="your-sub-id",
                resource_group="your-resource-group-id",
                workspace_name="your-workspace-name",
                auth=msi_auth
                )
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Geheimnissen in Trainingsausführungen](how-to-use-secrets-in-runs.md)
* [Konfigurieren der Authentifizierung für als Webdienste bereitgestellte Modelle](how-to-authenticate-web-service.md)
* [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](how-to-consume-web-service.md)
