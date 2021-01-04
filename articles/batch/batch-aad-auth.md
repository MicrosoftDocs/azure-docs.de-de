---
title: Authentifizieren von Azure Batch-Diensten mit Azure Active Directory
description: Batch unterstützt Azure AD für die Authentifizierung vom Batch-Dienst aus. Erfahren Sie, wie Sie sich auf eine von zwei Arten authentifizieren.
ms.topic: how-to
ms.date: 10/20/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 784e92eaa2cd672d511177066befcfd7effc7ca4
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95252639"
---
# <a name="authenticate-batch-service-solutions-with-active-directory"></a>Authentifizieren von Lösungen des Azure Batch-Diensts mit Active Directory

Azure Batch unterstützt die Authentifizierung mit [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Azure selbst verwendet Azure AD für die Authentifizierung seiner Kunden, Dienstadministratoren und Organisationsbenutzer.

Bei Verwendung der Azure AD-Authentifizierung mit Azure Batch können Sie auf zwei Arten authentifizieren:

- Mit **integrierter Authentifizierung**, um einen Benutzer zu authentifizieren, der mit der Anwendung interagiert. Eine Anwendung, die die integrierte Authentifizierung verwendet, erfasst die Anmeldeinformationen eines Benutzers und verwendet diese Anmeldeinformationen zum Authentifizieren des Zugriffs auf Batch-Ressourcen.
- Mithilfe eines **Dienstprinzipals**, um eine unbeaufsichtigte Anwendung zu authentifizieren. Ein Dienstprinzipal definiert die Richtlinie und Berechtigungen für eine Anwendung, um die Anwendung zu repräsentieren, wenn zur Laufzeit auf Ressourcen zugegriffen wird.

Weitere Informationen zu Azure AD finden Sie unter [Dokumentation zu Azure Active Directory](../active-directory/index.yml).

## <a name="endpoints-for-authentication"></a>Endpunkte für die Authentifizierung

Um Batch-Anwendungen bei Azure AD zu authentifizieren, müssen Sie einige gut bekannte Endpunkte im Code einbeziehen.

### <a name="azure-ad-endpoint"></a>Azure AD-Endpunkt

Der Basis-Autoritätsendpunkt von Azure AD ist:

`https://login.microsoftonline.com/`

Verwenden Sie zur Authentifizierung bei Azure AD diesen Endpunkt zusammen mit der Mandanten-ID (Verzeichnis-ID). Die Mandanten-ID identifiziert den zur Authentifizierung zu verwendenden Azure AD-Mandanten. Führen Sie zum Abrufen der Mandanten-ID die Schritte unter [Abrufen der Mandanten-ID für Ihr Azure Active Directory](#get-the-tenant-id-for-your-active-directory) aus:

`https://login.microsoftonline.com/<tenant-id>`

> [!NOTE]
> Der mandantenspezifische Endpunkt ist erforderlich, wenn Sie einen Dienstprinzipal zur Authentifizierung verwenden.
>
> Der mandantenspezifische Endpunkt ist optional, wenn Sie mithilfe der integrierten Authentifizierung authentifizieren, wird jedoch empfohlen. Sie können jedoch auch den gemeinsamen Endpunkt für Azure AD verwenden. Der allgemeine Endpunkt bietet eine generische Schnittstelle für die Erfassung von Anmeldeinformationen, wenn kein bestimmter Mandant bereitgestellt wird. Der gemeinsame Endpunkt ist `https://login.microsoftonline.com/common`.

Weitere Informationen zu Azure AD-Endpunkten finden Sie unter [Authentifizierung und Autorisierung]()../active-directory/develop/authentication-vs-authorization.md).

### <a name="batch-resource-endpoint"></a>Batch-Ressourcenendpunkt

Verwenden Sie den **Azure Batch-Ressourcenendpunkt**, um ein Token zum Authentifizieren von Anforderungen für den Batch-Dienst abzurufen:

`https://batch.core.windows.net/`

## <a name="register-your-application-with-a-tenant"></a>Registrieren Ihrer Anwendung bei einem Mandanten

Der erste Schritt bei der Verwendung von Azure AD zum Authentifizieren ist die Registrierung Ihrer Anwendung bei dem Azure AD-Mandanten. Die Registrierung Ihrer Anwendung ermöglicht es Ihnen, die Azure [Active Directory-Authentifizierungsbibliothek](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (Active Directory Authentication Library, ADAL) aus dem Code aufzurufen. Die ADAL stellt eine API für die Authentifizierung bei Azure AD über Ihre Anwendung bereit. Die Registrierung Ihrer Anwendung ist unabhängig davon erforderlich, ob Sie planen, integrierte Authentifizierung oder einen Dienstprinzipal zu verwenden.

Wenn Sie Ihre Anwendung registrieren, liefern Sie Azure AD Informationen über Ihre Anwendung. Azure AD stellt dann eine Anwendungs-ID (auch als *Client-ID* bezeichnet) bereit, mit der Sie Ihre Anwendung zur Laufzeit Azure AD zuordnen. Weitere Informationen zur Anwendungs-ID finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Führen Sie die Schritte aus dem Abschnitt **Registrieren einer Anwendung** in folgendem Artikel aus: [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md). Wenn Sie Ihre Anwendung als native Anwendung registrieren, können Sie jeden gültigen URI als **Umleitungs-URI** angeben. Es muss sich nicht um einen echten Endpunkt handeln.

Nachdem Sie Ihre Anwendung registriert haben, wird die Anwendungs-ID angezeigt:

![Screenshot der Anwendungs-ID im Azure-Portal.](./media/batch-aad-auth/app-registration-data-plane.png)

## <a name="get-the-tenant-id-for-your-active-directory"></a>Abrufen der Mandanten-ID für Ihr Azure Active Directory

Die Mandanten-ID identifiziert den Azure AD-Mandanten, der Authentifizierungsdienste für Ihre Anwendung bereitstellt. Um die Mandanten-ID abzurufen, gehen Sie folgendermaßen vor:

1. Wählen Sie im Azure-Portal Ihr Active Directory aus.
1. Wählen Sie **Eigenschaften** aus.
1. Kopieren Sie den GUID-Wert, der für die **Verzeichnis-ID** bereitgestellt wird. Dieser Wert wird auch als Mandanten-ID bezeichnet.

![Screenshot der Verzeichnis-ID im Azure-Portal.](./media/batch-aad-auth/aad-directory-id.png)

## <a name="use-integrated-authentication"></a>Verwenden integrierter Authentifizierung

Zum Authentifizieren mit integrierter Authentifizierung müssen Sie Ihrer Anwendung Berechtigungen zum Herstellen der Verbindung mit der Batch-Dienst-API erteilen. Dieser Schritt ermöglicht der Anwendung, Aufrufe der Batch-Dienst-API bei Azure AD zu authentifizieren.

Sobald Sie die Anwendung registriert haben, befolgen Sie diese Schritte im Azure-Portal, um ihr Zugriff auf den Batchdienst zu gewähren:

1. Klicken Sie im Azure-Portal auf **Alle Dienste**, und wählen Sie dann **App-Registrierungen** aus.
1. Suchen Sie in der Liste mit den App-Registrierungen nach dem Namen Ihrer Anwendung.
1. Wählen Sie die Anwendung und dann die Option **API-Berechtigungen** aus.
1. Wählen Sie im Abschnitt **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Suchen Sie unter **Hiermit wählen Sie eine API aus.** nach der Batch API. Suchen Sie diese Zeichenfolgen, bis Sie die API gefunden haben:
    1. **Microsoft Azure Batch**
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** ist die ID für die Batch-API.
1. Wenn Sie die Batch-API gefunden haben, wählen Sie diese aus, und klicken Sie dann auf **Auswählen**.
1. Aktivieren Sie unter **Berechtigungen auswählen** das Kontrollkästchen neben **Access Azure Batch Service** (Auf Azure Batch-Dienst zugreifen), und wählen Sie dann **Berechtigungen hinzufügen** aus.

Im Abschnitt **API-Berechtigungen** wird jetzt angezeigt, dass Ihre Azure AD-Anwendung sowohl Zugriff auf Microsoft Graph als auch auf die Batch-Dienst-API hat. Berechtigungen für Microsoft Graph werden automatisch gewährt, wenn Sie Ihre App zum ersten Mal bei Azure AD registrieren.

## <a name="use-a-service-principal"></a>Verwenden eines Dienstprinzipals

Um eine Anwendung zu authentifizieren, die unbeaufsichtigt ausgeführt wird, verwenden Sie einen Dienstprinzipal. Sobald Sie die Anwendung registriert haben, befolgen Sie diese Schritte im Azure-Portal, um einen Dienstprinzipal zu konfigurieren:

1. Fordern Sie ein Geheimnis für Ihre Anwendung an.
1. Weisen Sie der Anwendung die rollenbasierte Zugriffssteuerung von Azure (role-bases access control, Azure-RBAC) zu.

### <a name="request-a-secret-for-your-application"></a>Anfordern eines Geheimnisses für Ihre Anwendung

Bei der Authentifizierung Ihrer Anwendung bei einem Dienstprinzipal werden die Anwendungs-ID und ein Geheimnis an Azure AD gesendet. Sie müssen den zu verwendenden geheimen Schlüssel in Ihrem Code erstellen und daraus kopieren.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Klicken Sie im Azure-Portal auf **Alle Dienste**. Klicken Sie auf **App-Registrierungen**.
1. Wählen Sie in der Liste mit den App-Registrierungen Ihre Anwendung aus.
1. Wählen Sie die Anwendung und dann **Certificates & secrets** (Zertifikate und Geheimnisse) aus. Wählen Sie im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus.
1. Geben Sie eine Beschreibung für das Geheimnis ein, um ein Geheimnis zu erstellen. Wählen Sie für das Geheimnis dann eine Ablaufzeit von einem oder zwei Jahren bzw. keine Ablaufzeit aus.
1. Wählen Sie **Hinzufügen**, um das Geheimnis zu erstellen und anzuzeigen. Kopieren Sie den Geheimniswert an einen sicheren Ort. Sie können nicht mehr darauf zugreifen, nachdem Sie die Seite verlassen haben.

### <a name="assign-azure-rbac-to-your-application"></a>Zuweisen von Azure-RBAC in Ihrer Anwendung

Zur Authentifizierung bei einem Dienstprinzipal müssen Sie Ihrer Anwendung Azure-RBAC zuweisen. Folgen Sie diesen Schritten:

1. Navigieren Sie im Azure-Portal zu dem Batch-Konto, das von Ihrer Anwendung verwendet wird.
1. Wählen Sie im Abschnitt **Einstellungen** des Batch-Kontos die Option **Zugriffssteuerung (IAM)** aus.
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie aus der Dropdownliste **Rolle** entweder die Rolle *Mitwirkender* oder *Leser* für Ihre Anwendung. Weitere Informationen zu diesen Rollen finden Sie unter [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md).
1. Geben Sie im Feld **Auswählen** den Namen Ihrer Anwendung ein. Wählen Sie die Anwendung in der Liste und anschließend die Option **Speichern** aus.

Ihre Anwendung sollte jetzt in Ihren Einstellungen für die Zugriffssteuerung mit einer zugewiesenen Azure-Rolle angezeigt werden.

![Zuweisen einer Azure-Rolle zu Ihrer Anwendung](./media/batch-aad-auth/app-rbac-role.png)

### <a name="assign-a-custom-role"></a>Zuweisen einer benutzerdefinierten Rolle

Eine benutzerdefinierte Rolle gewährt einem Benutzer eine präzise Berechtigung zum Einreichen von Aufträgen, Aufgaben und mehr. Dadurch können Benutzer daran gehindert werden, kostenrelevante Vorgänge auszuführen, wie z. B. das Erstellen von Pools oder das Ändern von Knoten.

Sie können eine benutzerdefinierte Rolle verwenden, um einem Benutzer, einer Gruppe oder einem Dienstprinzipal in Azure AD Berechtigungen für die folgenden Azure-RBAC-Vorgänge zu erteilen:

- Microsoft.Batch/batchAccounts/pools/write
- Microsoft.Batch/batchAccounts/pools/delete
- Microsoft.Batch/batchAccounts/pools/read
- Microsoft.Batch/batchAccounts/jobSchedules/write
- Microsoft.Batch/batchAccounts/jobSchedules/delete
- Microsoft.Batch/batchAccounts/jobSchedules/read
- Microsoft.Batch/batchAccounts/jobs/write
- Microsoft.Batch/batchAccounts/jobs/delete
- Microsoft.Batch/batchAccounts/jobs/read
- Microsoft.Batch/batchAccounts/certificates/write
- Microsoft.Batch/batchAccounts/certificates/delete
- Microsoft.Batch/batchAccounts/certificates/read
- Microsoft.Batch/batchAccounts/read (für beliebige Lesevorgänge)
- Microsoft.Batch/batchAccounts/listKeys/action (für beliebige Vorgänge)

Benutzerdefinierte Rollen sind für Benutzer bestimmt, die von Azure AD und nicht durch Batch-Konto-Anmeldeinformationen (gemeinsam verwendeter Schlüssel) authentifiziert wurden. Beachten Sie, dass die Anmeldeinformationen für das Batch-Konto die volle Berechtigung für das Batch-Konto gewähren. Beachten Sie auch, dass Aufträge, die den automatischen Pool ([autopool](nodes-and-pools.md#autopools)) verwenden, Berechtigungen auf Poolebene erfordern.

> [!NOTE]
> Bestimmte Rollenzuweisungen müssen im Feld „actions“ bzw. im Feld „dataActions“ angegeben werden. Weitere Informationen finden Sie unter [Vorgänge mit dem Azure-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftbatch).

Hier ist ein Beispiel für eine benutzerdefinierte Rollendefinition:

```json
{
 "properties":{
    "roleName":"Azure Batch Custom Job Submitter",
    "type":"CustomRole",
    "description":"Allows a user to submit jobs to Azure Batch but not manage pools",
    "assignableScopes":[
      "/subscriptions/88888888-8888-8888-8888-888888888888"
    ],
    "permissions":[
      {
        "actions":[
          "Microsoft.Batch/*/read",
          "Microsoft.Authorization/*/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*",
          "Microsoft.Insights/alertRules/*"
        ],
        "notActions":[

        ],
        "dataActions":[
          "Microsoft.Batch/batchAccounts/jobs/*",
          "Microsoft.Batch/batchAccounts/jobSchedules/*"
        ],
        "notDataActions":[

        ]
      }
    ]
  }
}
```

Weitere Informationen zum Erstellen einer benutzerdefinierten Rolle finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md).

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Abrufen der Mandanten-ID für Ihr Azure Active Directory

Die Mandanten-ID identifiziert den Azure AD-Mandanten, der Authentifizierungsdienste für Ihre Anwendung bereitstellt. Um die Mandanten-ID abzurufen, gehen Sie folgendermaßen vor:

1. Wählen Sie im Azure-Portal Ihr Active Directory aus.
1. Wählen Sie **Eigenschaften** aus.
1. Kopieren Sie den GUID-Wert, der für die **Verzeichnis-ID** bereitgestellt wird. Dieser Wert wird auch als Mandanten-ID bezeichnet.

![Kopieren der Verzeichnis-ID](./media/batch-aad-auth/aad-directory-id.png)

## <a name="code-examples"></a>Codebeispiele

Die Codebeispiele in diesem Abschnitt zeigen das Authentifizieren bei Azure AD mit der integrierten Authentifizierung und einem Dienstprinzipal. Die meisten dieser Codebeispiele verwenden .NET, aber die Konzepte sind für andere Sprachen ähnlich.

> [!NOTE]
> Ein Azure AD-Authentifizierungstoken läuft nach einer Stunde ab. Bei Verwendung eines langlebigen **BatchClient**-Objekts empfehlen wir Ihnen, bei jeder Anforderung ein Token aus ADAL abzurufen, um sicherzustellen, dass Sie immer über ein gültiges Token verfügen.
>
> Schreiben Sie in .NET hierfür eine Methode, mit der das Token aus Azure AD abgerufen wird, und übergeben Sie diese Methode als Delegat an ein **BatchTokenCredentials**-Objekt. Die Delegatmethode wird bei jeder Anforderung an den Batch-Dienst aufgerufen, um sicherzustellen, dass ein gültiges Token bereitgestellt wird. Standardmäßig werden Token von ADAL zwischengespeichert, sodass nur dann ein neues Token aus Azure AD abgerufen wird, wenn dies erforderlich ist. Weitere Informationen zu Token in Azure AD finden Sie unter [Sicherheitstoken](../active-directory/develop/security-tokens.md).

### <a name="code-example-using-azure-ad-integrated-authentication-with-batch-net"></a>Codebeispiel: Verwenden der integrierten Authentifizierung von Azure AD mit Batch .NET

Verweisen Sie zum Authentifizieren mit der integrierten Authentifizierung von Batch .NET auf das [Azure Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/)-Paket und das [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)-Paket.

Fügen Sie die folgenden `using`-Anweisungen in den Code ein:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Verweisen Sie auf den Azure AD-Endpunkt in Ihrem Code einschließlich der Mandanten-ID. Führen Sie zum Abrufen der Mandanten-ID die Schritte unter [Abrufen der Mandanten-ID für Ihr Azure Active Directory](#get-the-tenant-id-for-your-active-directory) aus:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Verweisen Sie auf den Ressourcenendpunkt für den Batch-Dienst:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Verweisen Sie auf Ihr Batch-Konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geben Sie die Anwendungs-ID (Client-ID) für Ihre Anwendung an. Die Anwendungs-ID ist über Ihre App-Registrierung im Azure-Portal verfügbar:

```csharp
private const string ClientId = "<application-id>";
```

Kopieren Sie außerdem den Umleitungs-URI, den Sie bei der Registrierung Ihrer Anwendung als native Anwendung angegeben haben. Der im Code angegebene Umleitungs-URI muss mit dem Umleitungs-URI übereinstimmen, den Sie beim Registrieren der Anwendung bereitgestellt haben:

```csharp
private const string RedirectUri = "http://mybatchdatasample";
```

Schreiben Sie eine Callbackmethode, um das Authentifizierungstoken aus Azure AD abzurufen. In der hier gezeigten **GetAuthenticationTokenAsync**-Rückrufmethode wird ADAL für die Authentifizierung eines Benutzers genutzt, der mit der Anwendung interagiert. Die von ADAL bereitgestellte **AcquireTokenAsync**-Methode fragt die Anmeldeinformationen des Benutzers ab, und die Anwendung setzt den Vorgang fort, nachdem der Benutzer die Informationen angegeben hat (sofern sie nicht bereits Anmeldeinformationen zwischengespeichert hat):

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    var authContext = new AuthenticationContext(AuthorityUri);

    // Acquire the authentication token from Azure AD.
    var authResult = await authContext.AcquireTokenAsync(BatchResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        new PlatformParameters(PromptBehavior.Auto));

    return authResult.AccessToken;
}
```

Erstellen Sie ein **BatchTokenCredentials**-Objekt, in dem der Delegat als Parameter verwendet wird. Verwenden Sie diese Anmeldeinformationen, um ein **BatchClient**-Objekt zu öffnen. Sie können dieses **BatchClient**-Objekt für nachfolgende Vorgänge für den Batch-Dienst verwenden:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-net"></a>Codebeispiel: Verwenden eines Azure AD-Dienstprinzipals mit Batch .NET

Verweisen Sie zum Authentifizieren mit einem Dienstprinzipal von Batch .NET auf das [Azure Batch .NET](https://www.nuget.org/packages/Azure.Batch/)-Paket und das [ADAL](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)-Paket.

Fügen Sie die folgenden `using`-Anweisungen in den Code ein:

```csharp
using Microsoft.Azure.Batch;
using Microsoft.Azure.Batch.Auth;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Verweisen Sie auf den Azure AD-Endpunkt in Ihrem Code einschließlich der Mandanten-ID. Wenn Sie einen Dienstprinzipal verwenden, müssen Sie einen mandantenspezifischen Endpunkt angeben. Führen Sie zum Abrufen der Mandanten-ID die Schritte unter [Abrufen der Mandanten-ID für Ihr Azure Active Directory](#get-the-tenant-id-for-your-active-directory) aus:

```csharp
private const string AuthorityUri = "https://login.microsoftonline.com/<tenant-id>";
```

Verweisen Sie auf den Ressourcenendpunkt für den Batch-Dienst:

```csharp
private const string BatchResourceUri = "https://batch.core.windows.net/";
```

Verweisen Sie auf Ihr Batch-Konto:

```csharp
private const string BatchAccountUrl = "https://myaccount.mylocation.batch.azure.com";
```

Geben Sie die Anwendungs-ID (Client-ID) für Ihre Anwendung an. Die Anwendungs-ID ist über Ihre App-Registrierung im Azure-Portal verfügbar:

```csharp
private const string ClientId = "<application-id>";
```

Geben Sie den geheimen Schlüssel an, den Sie aus dem Azure-Portal kopiert haben:

```csharp
private const string ClientKey = "<secret-key>";
```

Schreiben Sie eine Callbackmethode, um das Authentifizierungstoken aus Azure AD abzurufen. Die hier gezeigte **GetAuthenticationTokenAsync**-Rückrufmethode ruft ADAL für die unbeaufsichtigte Authentifizierung auf:

```csharp
public static async Task<string> GetAuthenticationTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync(BatchResourceUri, new ClientCredential(ClientId, ClientKey));

    return authResult.AccessToken;
}
```

Erstellen Sie ein **BatchTokenCredentials**-Objekt, in dem der Delegat als Parameter verwendet wird. Verwenden Sie diese Anmeldeinformationen, um ein **BatchClient**-Objekt zu öffnen. Verwenden Sie dann dieses **BatchClient**-Objekt für nachfolgende Vorgänge für den Batch-Dienst:

```csharp
public static void PerformBatchOperations()
{
    Func<Task<string>> tokenProvider = () => GetAuthenticationTokenAsync();

    using (var client = BatchClient.Open(new BatchTokenCredentials(BatchAccountUrl, tokenProvider)))
    {
        client.JobOperations.ListJobs();
    }
}
```

### <a name="code-example-using-an-azure-ad-service-principal-with-batch-python"></a>Codebeispiel: Verwenden eines Azure AD-Dienstprinzipals mit Batch Python

Verweisen Sie zum Authentifizieren mit einem Dienstprinzipal von Batch Python auf die Module [azure-batch](https://pypi.org/project/azure-batch/) und [azure-common](https://pypi.org/project/azure-common/).

```python
from azure.batch import BatchServiceClient
from azure.common.credentials import ServicePrincipalCredentials
```

Wenn Sie einen Dienstprinzipal verwenden, müssen Sie die Mandanten-ID angeben. Führen Sie zum Abrufen der Mandanten-ID die Schritte unter [Abrufen der Mandanten-ID für Ihr Azure Active Directory](#get-the-tenant-id-for-your-active-directory) aus:

```python
TENANT_ID = "<tenant-id>"
```

Verweisen Sie auf den Ressourcenendpunkt für den Batch-Dienst:

```python
RESOURCE = "https://batch.core.windows.net/"
```

Verweisen Sie auf Ihr Batch-Konto:

```python
BATCH_ACCOUNT_URL = "https://myaccount.mylocation.batch.azure.com"
```

Geben Sie die Anwendungs-ID (Client-ID) für Ihre Anwendung an. Die Anwendungs-ID ist über Ihre App-Registrierung im Azure-Portal verfügbar:

```python
CLIENT_ID = "<application-id>"
```

Geben Sie den geheimen Schlüssel an, den Sie aus dem Azure-Portal kopiert haben:

```python
SECRET = "<secret-key>"
```

Erstellen Sie ein **ServicePrincipalCredentials**-Objekt:

```python
credentials = ServicePrincipalCredentials(
    client_id=CLIENT_ID,
    secret=SECRET,
    tenant=TENANT_ID,
    resource=RESOURCE
)
```

Verwenden Sie die Dienstprinzipal-Anmeldeinformationen, um ein **BatchServiceClient**-Objekt zu öffnen. Verwenden Sie dieses **BatchServiceClient**-Objekt dann für nachfolgende Vorgänge für den Batch-Dienst.

```python
    batch_client = BatchServiceClient(
    credentials,
    batch_url=BATCH_ACCOUNT_URL
)
```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Dokumentation zu Azure Active Directory](../active-directory/index.yml). Ausführliche Beispiele zur ADAL-Verwendung finden Sie in der Bibliothek mit den [Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?service=active-directory).
- Erfahren Sie mehr über [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) und [wie Sie eine Azure AD-Anwendung und -Dienstprinzipal erstellen, die auf Ressourcen zugreifen können](../active-directory/develop/howto-create-service-principal-portal.md).
- Erfahren Sie mehr zum [Authentifizieren von Batchverwaltungslösungen mit Active Directory](batch-aad-auth-management.md).
- Ein Python-Beispiel zum Erstellen eines Batch-Clients, der mithilfe eines Azure AD-Token authentifiziert wird, finden Sie im Beispiel [Deploying Azure Batch Custom Image with a Python Script](https://github.com/azurebigcompute/recipes/blob/master/Azure%20Batch/CustomImages/CustomImagePython.md) (Bereitstellen eines benutzerdefinierten Azure Batch-Images mit einem Python-Skript).

