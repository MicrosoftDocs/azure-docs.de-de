---
title: Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API mit .NET | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie die Azure Active Directory (Azure AD)-Authentifizierung verwendet wird, um auf die Azure Media Services (AMS)-API mit .NET zuzugreifen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ecb704253597bf4eb5672fe924a0dafc4c1b3fd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726554"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API mit .NET

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Ab windowsazure.mediaservices 4.0.0.4 unterstützt Azure Media Services auf Azure Active Directory (Azure AD) basierende Authentifizierung. In diesem Thema wird gezeigt, wie Sie die Azure AD-Authentifizierung verwenden, um auf die Azure Media Services-API mit Microsoft .NET zuzugreifen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
- Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen eines Azure Media Services-Kontos mithilfe des Azure-Portals](media-services-portal-create-account.md).
- Das neueste [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices)-Paket.
- Vertrautheit mit dem Thema [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

Bei Verwendung der Azure AD-Authentifizierung mit Azure Media Services können Sie auf zwei Arten authentifizieren:

- Die **Benutzerauthentifizierung** dient der Authentifizierung einer Person, die die App zum Interagieren mit Azure Media Services-Ressourcen verwendet. Die interaktive Anwendung sollte den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird. 
- Die **Dienstprinzipalauthentifizierung** dient der Authentifizierung eines Diensts. Anwendungen, für die diese Authentifizierungsmethode normalerweise verwendet wird, sind Apps, die Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge wie Web-Apps, Funktions-Apps, Logik-Apps, APIs oder Microservices ausführen.

>[!IMPORTANT]
>Derzeit unterstützt Azure Media Services ein Azure Access Control Service-Authentifizierungsmodell. Die Access Control-Authentifizierung gilt aber ab dem 22. Juni 2018 als veraltet. Es wird empfohlen, möglichst bald zu einem Azure Active Directory-Authentifizierungsmodell zu migrieren.

## <a name="get-an-azure-ad-access-token"></a>Anfordern eines Azure AD-Zugriffstokens

Zur Herstellung einer Verbindung zur Azure Media Services-API mit der Azure AD-Authentifizierung muss die Client-App ein Azure AD-Zugriffstoken anfordern. Wenn Sie das Media Services-.NET-Client-SDK verwenden, werden viele der Details zum Abrufen eines Azure AD-Zugriffstokens von den Klassen [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) und [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) umschlossen und vereinfacht. 

So müssen Sie z.B. keine Azure AD-Autorität, Media Services-Ressourcen-URI oder Details der nativen Azure AD-Anwendung angeben. Hierbei handelt es sich um bekannte Werte, die bereits von der Providerklasse für das Azure AD-Zugriffstoken konfiguriert wurden. 

Wenn Sie das Azure Media Services-.NET-SDK nicht verwenden, empfehlen wir die [Azure AD-Authentifizierungsbibliothek](../../active-directory/develop/active-directory-authentication-libraries.md). Informationen zu den Werten der Parameter, die Sie mit der Azure AD-Authentifizierungsbibliothek verwenden müssen, finden Sie unter [Get started with Azure AD authentication by using the Azure portal (Erste Schritte mit der Azure AD-Authentifizierung mit dem Azure-Portal)](media-services-portal-get-started-with-aad.md).

Sie haben auch die Option, die standardmäßige Implementierung von **AzureAdTokenProvider** durch Ihre eigene Implementierung zu ersetzen.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Installieren und Konfigurieren des Azure Media Services-.NET-SDK

>[!NOTE] 
>Um die Azure AD-Authentifizierung mit dem Media Services-.NET-SDK verwenden zu können, müssen Sie das aktuellste [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices)-Paket installieren. Darüber hinaus müssen Sie einen Verweis zur Assembly **Microsoft.IdentityModel.Clients.ActiveDirectory** hinzufügen. Wenn Sie eine vorhandene App verwenden, müssen Sie die Assembly **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** einschließen. 

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio.
2. Verwenden Sie das NuGet-Paket [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices), um das **Azure Media Services .NET SDK** zu installieren. 

    Gehen Sie wie folgt vor, um mit NuGet Verweise hinzuzufügen: Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus. Suchen Sie dann nach **windowsazure.mediaservices**, und klicken Sie auf **Installieren**.
    
    Oder

    Führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** in Visual Studio aus.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. Fügen Sie **using** zu Ihrem Quellcode hinzu.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>Verwenden der Benutzerauthentifizierung

Zum Herstellen einer Verbindung zur Azure Media Services-API mit der Option der Benutzerauthentifizierung muss die Client-App ein Azure AD-Token anfordern und dabei folgende Parameter verwenden:  

- Azure AD-Mandantenendpunkt. Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Zeigen Sie auf den angemeldeten Benutzer in der oberen rechten Ecke.
- Media Services-Ressourcen-URI.
- Client-ID für (native) Media Services-Anwendungen. 
- Umleitungs-URI für (native) Media Services-Anwendungen. 

Die Werte für diese Parameter finden Sie in **AzureEnvironments.AzureCloudEnvironment**. Die Konstante **AzureEnvironments.AzureCloudEnvironment** ist ein Hilfsprogramm im .NET-SDK, um die richtigen Einstellungen der Umgebungsvariablen für ein öffentliches Azure-Rechenzentrum abzurufen. 

Es enthält vordefinierte Umgebungseinstellungen für den Zugriff auf Media Services ausschließlich in den öffentlichen Rechenzentren. Für unabhängige Cloudregionen oder Cloudregionen von Behörden können Sie **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** bzw. **AzureGermanCloudEnvironment** verwenden.

Im folgenden Codebeispiel wird ein Token erstellt:
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
Wenn Sie mit der Programmierung für Media Services beginnen möchten, müssen Sie eine **CloudMediaContext**-Instanz erstellen, die den Serverkontext darstellt. **CloudMediaContext** umfasst Verweise auf wichtige Sammlungen, z.B. Jobs, Medienobjekte, Dateien, Zugriffsrichtlinien und Locators. 

Sie müssen zudem die **Ressourcen-URI für Media REST Services** an den Konstruktor **CloudMediaContext** übergeben. Um die Ressourcen-URI für Media REST Services zu erhalten, melden Sie sich im Azure-Portal an, wählen Ihr Azure Media Services-Konto, **API-Zugriff** und dann **Connect to Azure Media Services with user authentication** (Herstellen einer Verbindung mit Azure Media Services mit Benutzerauthentifizierung) aus. 

Im folgenden Codebeispiel wird eine **CloudMediaContext**-Instanz erstellt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

Das folgende Beispiel zeigt, wie das Azure AD-Token und der Kontext erstellt werden:

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>Wenn eine Ausnahme mit dem Text „Der Remoteserver hat einen Fehler zurückgegeben: 401 – Nicht autorisiert“ zurückgegeben wird, finden Sie weitere Informationen im Abschnitt [Zugriffssteuerung](media-services-use-aad-auth-to-access-ams-api.md#access-control) von „Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung – Übersicht“.

## <a name="use-service-principal-authentication"></a>Verwenden der Dienstprinzipalauthentifizierung
    
Zum Herstellen einer Verbindung zur Azure Media Services-API mit der Option des Dienstprinzipals muss Ihre Anwendung der mittleren Ebene (Web-API oder Webanwendung) ein Azure AD-Token mit den folgenden Parametern anfordern:  

- Azure AD-Mandantenendpunkt. Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Zeigen Sie auf den angemeldeten Benutzer in der oberen rechten Ecke.
- Media Services-Ressourcen-URI.
- Werte der Azure AD-Anwendung: **Client-ID** und **Clientgeheimnis**.

Die Werte für die Parameter **Client-ID** und **Clientgeheimnis** können im Azure-Portal abgerufen werden. Weitere Informationen finden Sie unter [Getting started with Azure AD authentication using the Azure portal (Erste Schritte mit der Azure AD-Authentifizierung mit dem Azure-Portal)](media-services-portal-get-started-with-aad.md).

Im folgenden Codebeispiel wird ein Token erstellt, indem der Konstruktor **AzureAdTokenCredentials** verwendet wird, der **AzureAdClientSymmetricKey** als Parameter akzeptiert: 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

Sie können auch den Konstruktor **AzureAdTokenCredentials** angeben, der **AzureAdClientCertificate** als Parameter akzeptiert. 

Anweisungen zum Erstellen und Konfigurieren eines Zertifikats in einer Form, die von Azure AD verwendet werden kann, finden Sie unter [Authenticating to Azure AD in daemon apps with certificates – manual configuration steps (Authentifizierung bei Azure AD in Daemon-Apps mit Zertifikaten – Manuelle Konfigurationsschritte)](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md).

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

Wenn Sie mit der Programmierung für Media Services beginnen möchten, müssen Sie eine **CloudMediaContext**-Instanz erstellen, die den Serverkontext darstellt. Sie müssen zudem die **Ressourcen-URI für Media REST Services** an den Konstruktor **CloudMediaContext** übergeben. Sie können auch den Wert **Ressourcen-URI für Media REST Services** aus dem Azure-Portal abrufen.

Im folgenden Codebeispiel wird eine **CloudMediaContext**-Instanz erstellt:

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
Das folgende Beispiel zeigt, wie das Azure AD-Token und der Kontext erstellt werden:

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit dem [Hochladen von Dateien in Ihr Konto](media-services-dotnet-upload-files.md).
