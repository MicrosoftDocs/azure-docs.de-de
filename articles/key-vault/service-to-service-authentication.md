---
title: Dienst-zu-Dienst-Authentifizierung in Azure Key Vault mithilfe von .NET
description: Verwenden Sie Bibliothek „Microsoft.Azure.Services.AppAuthentication“ zur Authentifizierung beim Azure Key Vault mithilfe von .NET.
keywords: Lokale Anmeldeinformationen für die Authentifizierung bei Azure Key Vault
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 03/05/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: defb67c7e100a50a81d55afee03aa84be8e1e8e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64722471"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Dienst-zu-Dienst-Authentifizierung in Azure Key Vault mithilfe von .NET

Zum Authentifizieren in Azure Key Vault benötigen Sie die Anmeldeinformationen eines Azure Active Directory (AD), und zwar entweder einen gemeinsamen geheimen Schlüssel oder ein Zertifikat. Das Verwalten dieser Anmeldeinformationen kann schwierig sein, und es ist verführerisch, Anmeldeinformationen in Quell- oder Konfigurationsdateien einzuschließen um sie in eine App zu bündeln.

Die `Microsoft.Azure.Services.AppAuthentication` für .NET-Bibliothek vereinfacht dieses Problem. Die Anmeldeinformationen des Entwicklers werden für die Authentifizierung während der lokalen Entwicklung verwendet. Wenn die Lösung später in Azure bereitgestellt wird, wechselt die Bibliothek automatisch zu Anwendungsanmeldeinformationen.  

Während der lokalen Entwicklung sind Entwickleranmeldeinformationen sicherer, da Sie keine Azure AD-Anmeldeinformationen erstellen oder Anmeldeinformationen zwischen Entwicklern freigeben müssen.

Die `Microsoft.Azure.Services.AppAuthentication`-Bibliothek verwaltet die Authentifizierung automatisch, wodurch Sie sich wiederum auf die Lösung anstatt auf Ihre Anmeldeinformationen konzentrieren können.

Die `Microsoft.Azure.Services.AppAuthentication`-Bibliothek unterstützt die lokale Entwicklung mit Microsoft Visual Studio, Azure CLI oder der integrierten Azure AD-Authentifizierung. Bei Bereitstellung für eine Azure-Ressource, die eine verwaltete Identität unterstützt, verwendet die Bibliothek automatisch [verwaltete Identitäten für Azure-Ressourcen](../active-directory/msi-overview.md). Es sind keine Änderungen von Code bzw. Konfiguration erforderlich. Die Bibliothek unterstützt auch die direkte Verwendung von Azure AD-[Clientanmeldeinformationen](../azure-resource-manager/resource-group-authenticate-service-principal.md), wenn keine verwaltete Identität verfügbar ist oder wenn der Sicherheitskontext des Entwicklers bei der lokalen Entwicklung nicht bestimmt werden kann.

## <a name="using-the-library"></a>Verwenden der Bibliothek

Für .NET-Anwendungen ist die einfachste Methode zum Verwenden einer verwalteten Identität ein `Microsoft.Azure.Services.AppAuthentication`-Paket. Erfahren Sie, wie Sie loslegen können:

1. Fügen Sie Ihrer Anwendung einen Verweis auf die NuGet-Pakete [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) und [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) hinzu. 

2. Fügen Sie den folgenden Code hinzu:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Die `AzureServiceTokenProvider`-Klasse speichert das Token im Arbeitsspeicher zwischen und ruft es kurz vor dem Ablaufdatum von Azure AD ab. Folglich brauchen Sie vor dem Aufruf der `GetAccessTokenAsync`-Methode das Ablaufdatum nicht mehr zu überprüfen. Rufen Sie einfach die Methode auf, wenn Sie das Token verwenden möchten. 

Die `GetAccessTokenAsync`-Methode erfordert einen Ressourcenbezeichner. Weitere Informationen finden Sie unter [Azure-Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/msi-overview.md).

## <a name="samples"></a>Beispiele

Die folgenden Beispiele zeigen die `Microsoft.Azure.Services.AppAuthentication`-Bibliothek in Aktion:

1. [Verwenden einer verwalteten Identität zum Abrufen eines Geheimnisses aus Azure Key Vault zur Laufzeit](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programmgesteuerte Bereitstellung einer Azure Resource Manager-Vorlage aus einem virtuellen Azure-Computer mit einer verwalteten Identität](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Verwenden eines .NET Core-Beispiels und einer verwalteten Identität zum Aufrufen der Azure-Dienste aus einer Azure Linux-VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="local-development-authentication"></a>Authentifizierung für die lokale Entwicklung

Für die lokale Entwicklung gibt es zwei primäre Authentifizierungsszenarien:

- [Authentifizierung bei Azure-Diensten](#authenticating-to-azure-services)
- [Authentifizierung bei benutzerdefinierten Diensten](#authenticating-to-custom-services)

### <a name="authenticating-to-azure-services"></a>Authentifizierung bei Azure-Diensten

Lokale Computer unterstützen keine verwalteten Identitäten für Azure-Ressourcen.  Daher verwendet die `Microsoft.Azure.Services.AppAuthentication`-Bibliothek Ihre Entwickleranmeldeinformationen für die Ausführung in Ihrer lokalen Entwicklungsumgebung. Wenn die Lösung in Azure bereitgestellt wird, verwendet die Bibliothek eine verwaltete Identität, um zu einem Flow zum Erteilen der OAuth 2.0-Clientanmeldeinformationen zu wechseln.  Dies bedeutet, dass Sie den gleichen Code ohne Sorge lokal und remote testen können.

Für die lokale Entwicklung ruft `AzureServiceTokenProvider` Token mithilfe von **Visual Studio**, der **Azure-Befehlszeilenschnittstelle** (CLI) oder der **integrierten Azure AD-Authentifizierung** ab. Jede Option wird nacheinander ausprobiert, und die Bibliothek verwendet die erste Option, die erfolgreich ausgeführt wird. Wenn keine Option funktioniert, wird eine `AzureServiceTokenProviderException`-Ausnahme mit detaillierten Informationen ausgelöst.

### <a name="authenticating-with-visual-studio"></a>Authentifizierung mit Visual Studio

Die Authentifizierung mit Visual Studio hat die folgenden Voraussetzungen:

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) oder höher.

2. Die [App-Authentifizierungserweiterung für Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), die als separate Erweiterung für Visual Studio 2017 Update 5 verfügbar ist und mit dem Produkt in Update 6 und höher gebündelt ist. Mit Update 6 oder höher können Sie die Installation der App-Authentifizierungserweiterung überprüfen, indem Sie Azure-Entwicklungstools aus dem Visual Studio-Installationsprogramm auswählen.
 
Melden Sie sich bei Visual Studio an, und verwenden Sie **Tools**&nbsp;>&nbsp;**Optionen**&nbsp;>&nbsp;**Azure-Dienstauthentifizierung**, um ein Konto für die lokale Entwicklung auszuwählen. 

Wenn Probleme bei der Verwendung von Visual Studio, z. B. Fehler im Zusammenhang mit der Tokenanbieterdatei, auftreten, überprüfen Sie sorgfältig die folgende Schritte. 

Es kann auch erforderlich sein, Ihr Entwicklertoken erneut zu authentifizieren. Wechseln Sie zu diesem Zweck zu **Tools**&nbsp;>&nbsp;**Optionen**>**Azure&nbsp;Service&nbsp;Authentifizierung**, und suchen Sie unter dem ausgewählten Konto nach einem Link zum **erneuten Authentifizieren**.  Wählen Sie diesen aus, um eine erneute Authentifizierung durchzuführen. 

### <a name="authenticating-with-azure-cli"></a>Authentifizierung mit Azure CLI

So verwenden Sie Azure CLI für die lokale Entwicklung:

1. Installieren Sie [Azure CLI v2.0.12](/cli/azure/install-azure-cli) oder höher. Aktualisieren Sie frühere Versionen. 

2. Melden Sie sich mit **az login** bei Azure an.

Verwenden Sie `az account get-access-token`, um den Zugriff zu überprüfen.  Wenn Sie eine Fehlermeldung erhalten, vergewissern Sie sich, dass Schritt 1 erfolgreich abgeschlossen wurde. 

Wenn Azure CLI nicht im Standardverzeichnis installiert ist, erhalten Sie möglicherweise eine Fehlermeldung, die angibt, dass `AzureServiceTokenProvider` den Pfad zu Azure CLI nicht finden kann.  Verwenden Sie die Umgebungsvariable **AzureCLIPath**, um den Azure CLI-Installationsordner zu definieren. `AzureServiceTokenProvider` fügt das in der Umgebungsvariable **AzureCLIPath** angegebene Verzeichnis bei Bedarf zur Umgebungsvariable **Path** hinzu.

Wenn Sie mit mehreren Konten bei Azure CLI angemeldet sind, oder wenn Ihr Konto Zugriff auf mehrere Abonnements hat, müssen Sie das zu verwendende Abonnement angeben.  Verwenden Sie dazu:

```
az account set --subscription <subscription-id>
```

Mit diesem Befehl wird nur bei einem Fehler eine Ausgabe generiert.  Verwenden Sie zum Überprüfen der Einstellungen für das aktuelle Konto:

```
az account list
```

### <a name="authenticating-with-azure-ad-integrate-authentication"></a>Authentifizierung mit der integrierten Azure AD-Authentifizierung

Um die Azure AD-Authentifizierung zu verwenden, überprüfen Sie Folgendes:

- Ihr lokales Active Directory [wird mit Azure AD synchronisiert](../active-directory/connect/active-directory-aadconnect.md).

- Ihr Code wird auf einem in die Domäne eingebundenen Computer ausgeführt.


### <a name="authenticating-to-custom-services"></a>Authentifizierung bei benutzerdefinierten Diensten

Wenn ein Dienst Azure-Dienste aufruft, wurden die vorherigen Schritte erfolgreich ausgeführt, da Azure-Dienste den Zugriff auf Benutzer und Anwendungen zuzulassen.  

Beim Erstellen eines Diensts, der einen benutzerdefinierten Dienst aufruft, verwenden Sie Azure AD-Clientanmeldeinformationen für die lokale Entwicklungsauthentifizierung.  Es gibt zwei Optionen: 

1.  Verwenden Sie einen Dienstprinzipal für die Anmeldung bei Azure:

    1.  [Erstellen Sie einen Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Verwenden Sie Azure CLI zum Anmelden:

        ```
        az login --service-principal -u <principal-id> --password <password>
           --tenant <tenant-id> --allow-no-subscriptions
        ```

        Da der Dienstprinzipal möglicherweise keinen Zugriff auf ein Abonnement hat, verwenden Sie das Argument `--allow-no-subscriptions`.

2.  Verwenden Sie Umgebungsvariablen, um Details zum Dienstprinzipal anzugeben.  Weitere Informationen finden Sie unter [Ausführen der Anwendung mithilfe eines Dienstprinzipals](#running-the-application-using-a-service-principal).

Nachdem Sie sich bei Azure angemeldet haben, verwendet `AzureServiceTokenProvider` den Dienstprinzipal zum Abrufen eines Tokens für die lokale Entwicklung.

Dies gilt nur für lokale Entwicklung. Wenn Ihre Lösung in Azure bereitgestellt wird, wechselt die Bibliothek zu einer verwalteten Identität zur Authentifizierung.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Ausführen der Anwendung mithilfe einer verwalteten Identität oder benutzerseitig zugewiesenen Identität 

Wenn Sie den Code auf einer Azure App Service-Instanz oder einem virtuellen Azure-Computer ausführen und eine verwaltete Identität aktiviert ist, verwendet die Bibliothek automatisch die verwaltete Identität. Es sind keine Codeänderungen erforderlich. 

Alternativ können Sie sich auch mit einer benutzerseitig zugewiesenen Identität authentifizieren. Weitere Informationen zu benutzerseitig zugewiesenen Identitäten finden Sie unter [Informationen zu verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Die Verbindungszeichenfolge ist im Abschnitt [Unterstützung der Verbindungszeichenfolge](#connection-string-support) unten angegeben.

## <a name="running-the-application-using-a-service-principal"></a>Ausführen der Anwendung mithilfe eines Dienstprinzipals 

Es ist möglicherweise erforderlich, Azure AD-Clientanmeldeinformationen zum Authentifizieren zu erstellen. Häufige Beispiele sind:

1. Der Code wird in einer lokalen Entwicklungsumgebung, aber nicht unter der Identität des Entwicklers ausgeführt.  Service Fabric verwendet zum Beispiel das [NetworkService-Konto](../service-fabric/service-fabric-application-secret-management.md) für lokale Entwicklung.
 
2. Der Code wird in einer lokalen Entwicklungsumgebung ausgeführt, und Sie authentifizieren sich bei einem benutzerdefinierten Dienst, sodass Sie Ihre Entwickleridentität nicht nutzen können. 
 
3. Ihr Code wird für eine Azure-Computeressource ausgeführt, die verwaltete Identitäten für Azure-Ressourcen noch nicht unterstützt (z.B. Azure Batch).

So verwenden Sie ein Zertifikat für die Anmeldung bei Azure AD:

1. Erstellen Sie ein [Dienstprinzipalzertifikat](../azure-resource-manager/resource-group-authenticate-service-principal.md). 

2. Stellen Sie das Zertifikat entweder dem *LocalMachine*- oder *CurrentUser*-Store bereit. 

3. Legen Sie eine Umgebungsvariable namens **AzureServicesAuthConnectionString** folgendermaßen fest:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Ersetzen Sie *{AppId}* , *{TenantId}* und *{Thumbprint}* durch die in Schritt 1 generierten Werte. Ersetzen Sie *{CertificateStore}* basierend auf Ihrem Bereitstellungsplan entweder durch `LocalMachine` oder `CurrentUser`.

4. Führen Sie die Anwendung aus. 

So melden Sie sich mit einem gemeinsamen geheimen Schlüssel als Azure AD-Anmeldeinformationen an:

1. Erstellen Sie einen [Dienstprinzipal mit einem Kennwort](../azure-resource-manager/resource-group-authenticate-service-principal.md), und gewähren sie diesem Zugriff auf den Key Vault. 

2. Legen Sie eine Umgebungsvariable namens **AzureServicesAuthConnectionString** folgendermaßen fest:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Ersetzen Sie _{AppId}_ , _{TenantId}_ und _{ClientSecret}_ durch die in Schritt 1 generierten Werte.

3. Führen Sie die Anwendung aus. 

Nachdem alles ordnungsgemäß eingerichtet wurde, sind keine weiteren Codeänderungen mehr erforderlich.  `AzureServiceTokenProvider` verwendet die Umgebungsvariable und das Zertifikat zum Authentifizieren bei Azure AD. 

## <a name="connection-string-support"></a>Unterstützung der Verbindungszeichenfolge

Standardmäßig verwendet `AzureServiceTokenProvider` mehrere Methoden zum Abrufen eines Tokens. 

Um den Prozess zu steuern, verwenden Sie eine Verbindungszeichenfolge, die an den `AzureServiceTokenProvider`-Konstruktor übergeben oder in der Umgebungsvariablen *AzureServicesAuthConnectionString* angegeben wurde. 

Die folgenden Optionen werden unterstützt:

| Verbindungs&nbsp;zeichenfolgen&nbsp;option | Szenario | Kommentare|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokale Entwicklung | AzureServiceTokenProvider verwendet Azure CLI zum Abrufen des Tokens. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokale Entwicklung | AzureServiceTokenProvider verwendet Visual Studio zum Abrufen des Tokens. |
| `RunAs=CurrentUser` | Lokale Entwicklung | AzureServiceTokenProvider verwendet die integrierte Azure AD-Authentifizierung zum Abrufen des Tokens. |
| `RunAs=App` | [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider verwendet eine verwaltete Identität zum Abrufen des Tokens. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Benutzerseitig zugewiesene Identität für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider verwendet eine benutzerseitig zugewiesene Identität zum Abrufen des Tokens. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`   | Dienstprinzipal | `AzureServiceTokenProvider` verwendet ein Zertifikat zum Abrufen des Tokens von Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Dienstprinzipal | `AzureServiceTokenProvider` verwendet ein Zertifikat zum Abrufen des Tokens von Azure AD.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Dienstprinzipal |`AzureServiceTokenProvider` verwendet einen geheimen Schlüssel zum Abrufen des Tokens von Azure AD. |



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/index.yml).
- Erfahren Sie mehr über Azure AD-[Authentifizierungsszenarien](../active-directory/develop/active-directory-authentication-scenarios.md).
