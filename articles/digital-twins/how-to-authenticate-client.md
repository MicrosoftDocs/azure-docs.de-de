---
title: Schreiben von App-Authentifizierungscode
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Authentifizierungscode in einer Clientanwendung geschrieben wird.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bf7b829d70af27850affe619d47ed4a4f5ec1bea
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279915"
---
# <a name="write-client-app-authentication-code"></a>Schreiben von Authentifizierungscode für die Client-App

Nachdem Sie eine [Azure Digital Twins-Instanz und -Authentifizierung eingerichtet](how-to-set-up-instance-portal.md) haben, können Sie eine Clientanwendung erstellen, die Ihnen die Interaktion mit der Instanz ermöglicht. Nachdem Sie ein Clientstartprojekt eingerichtet haben, müssen Sie **Code in dieser Client-App schreiben, um die App für die Azure Digital Twins-Instanz zu authentifizieren**.

Azure Digital Twins führt die Authentifizierung mithilfe von [Azure AD-Sicherheitstoken basierend auf OAuth 2.0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims) aus. Zum Authentifizieren Ihres SDK müssen Sie ein Bearertoken mit den richtigen Berechtigungen für Azure Digital Twins abrufen und es zusammen mit ihren API-Aufrufen übergeben. 

In diesem Artikel wird beschrieben, wie Sie mithilfe der `Azure.Identity`-Clientbibliothek Anmeldeinformationen abrufen. Dieser Artikel zeigt Codebeispiele in C#, z. B. Codebeispiele für das [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Sie können jedoch unabhängig vom verwendeten SDK eine Version von `Azure.Identity` verwenden (weitere Informationen zu den für Azure Digital Twins verfügbaren SDKs finden Sie unter [*Vorgehensweise: Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zunächst die Schritte zur Einrichtung unter [*Vorgehensweise: Einrichten einer Instanz und der Authentifizierung*](how-to-set-up-instance-portal.md) aus. Dadurch wird sichergestellt, dass Sie über eine Azure Digital Twins-Instanz verfügen und der Benutzer Zugriffsberechtigungen besitzt. Nachdem Sie diese Einrichtung abgeschlossen haben, können Sie Client-App-Code schreiben.

Als Nächstes benötigen Sie ein Client-App-Projekt, in dem Sie den Code schreiben. Wenn Sie nicht bereits ein Client-App-Projekt eingerichtet haben, erstellen Sie ein einfaches Projekt in der Sprache Ihrer Wahl zur Verwendung in diesem Tutorial.

## <a name="common-authentication-methods-with-azureidentity"></a>Allgemeine Authentifizierungsmethoden mit Azure.Identity

`Azure.Identity` ist eine Clientbibliothek, die mehrere Methoden zum Abrufen von Anmeldeinformationen bereitstellt, die Sie verwenden können, um ein Bearertoken abzurufen und sich mit Ihrem SDK zu authentifizieren. Obwohl in diesem Artikel Beispiele in C# aufgeführt werden, können Sie `Azure.Identity` für verschiedene Sprachen anzeigen, beispielsweise...

* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

In `Azure.Identity` gibt es drei gängige Methoden zum Abrufen von Anmeldeinformationen:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) stellt einen `TokenCredential`-Standardauthentifizierungsfluss für Anwendungen bereit, die in Azure bereitgestellt werden, und ist **die empfohlene Wahl für lokale Entwicklung**. Außerdem kann DefaultAzureCredential aktiviert werden, um die beiden anderen Methoden zu testen, die in diesem Artikel empfohlen werden. DefaultAzureCredential dient als Wrapper für `ManagedIdentityCredential` und kann mit einer Konfigurationsvariablen auf `InteractiveBrowserCredential` zugreifen.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) funktioniert hervorragend in Fällen, in denen Sie [verwaltete Identitäten (MSI)](../active-directory/managed-identities-azure-resources/overview.md) benötigen, und eignet sich gut für das Arbeiten mit Azure Functions und Bereitstellungen in Azure-Diensten.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) ist für interaktive Anwendungen vorgesehen und kann zum Erstellen eines authentifizierten SDK-Clients verwendet werden.

Im folgenden Beispiel wird gezeigt, wie jede dieser Möglichkeiten mit dem .NET (C#) SDK verwendet wird.

## <a name="authentication-examples-net-c-sdk"></a>Beispiele für die Authentifizierung: .NET (C#) SDK

In diesem Abschnitt wird ein Beispiel in C# für die Verwendung des bereitgestellten .NET SDK zum Schreiben von Authentifizierungscode gezeigt.

Binden Sie zunächst das SDK-Paket `Azure.DigitalTwins.Core` und das `Azure.Identity`-Paket in Ihr Projekt ein. Je nachdem, welche Tools Sie verwenden, können Sie die Pakete mit dem Visual Studio-Paket-Manager oder mit dem Befehlszeilentool `dotnet` einbinden. 

Außerdem müssen Sie dem Projektcode die folgenden using-Anweisungen hinzufügen:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Fügen Sie dann Code zum Abrufen von Anmeldeinformationen mithilfe einer der Methoden in `Azure.Identity` hinzu.

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential-Methode

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) stellt einen `TokenCredential`-Standardauthentifizierungsfluss für Anwendungen bereit, die in Azure bereitgestellt werden, und ist **die empfohlene Wahl für lokale Entwicklung**.

Wenn Sie die standardmäßigen Azure-Anmeldeinformationen verwenden möchten, benötigen Sie die URL der Azure Digital Twins-Instanz ([Anweisungen für die Ermittlung](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Das folgende Codebeispiel fügt Ihrem Projekt `DefaultAzureCredential` hinzu:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>Einrichten lokaler Azure-Anmeldeinformationen

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential-Methode

Die [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet)-Methode eignet sich für Fälle, in denen [verwaltete Identitäten (MSI)](../active-directory/managed-identities-azure-resources/overview.md) benötigt werden, beispielsweise bei der Verwendung von Azure Functions.

Dies bedeutet, dass Sie `ManagedIdentityCredential` im selben Projekt wie `DefaultAzureCredential` oder `InteractiveBrowserCredential` verwenden können, um einen anderen Teil des Projekts zu authentifizieren.

Wenn Sie die standardmäßigen Azure-Anmeldeinformationen verwenden möchten, benötigen Sie die URL der Azure Digital Twins-Instanz ([Anweisungen für die Ermittlung](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Sie können die Anmeldeinformationen für die verwaltete Identität wie folgt in einer Azure-Funktion verwenden:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential-Methode

Die [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)-Methode ist für interaktive Anwendungen gedacht und startet einen Webbrowser für die Authentifizierung. Sie können dies anstelle von `DefaultAzureCredential` in Fällen verwenden, in denen Sie interaktive Authentifizierung benötigen.

Wenn Sie die interaktiven Browseranmeldeinformationen verwenden möchten, benötigen Sie eine **App-Registrierung** , die über Berechtigungen für die Azure Digital Twins-APIs verfügt. Schritte zum Einrichten dieser App-Registrierung finden Sie unter [*Vorgehensweise: Erstellen einer App-Registrierung*](how-to-create-app-registration.md). Nachdem Sie die App-Registrierung eingerichtet haben, benötigen Sie Folgendes...
* Die *Anwendungs-ID (Client-ID)* der App-Registrierung ( [Anleitungen zur Ermittlung](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Die *Verzeichnis-ID (Mandanten-ID)* der App-Registrierung ( [Anleitungen zur Ermittlung](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Die URL der Azure Digital Twins-Instanz ([Anweisungen für die Ermittlung](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Im Folgenden finden Sie ein Beispiel für den Code zum Erstellen eines authentifizierten SDK-Clients mit `InteractiveBrowserCredential`.

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Die Client-ID, die Mandanten-ID und die URL der Instanz können zwar wie oben dargestellt direkt in den Code eingefügt werden, es empfiehlt sich jedoch, den Code die Werte stattdessen aus einer Konfigurationsdatei oder Umgebungsvariablen abrufen zu lassen.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Weitere Hinweise zum Authentifizieren von Azure Functions

Weitere Informationen finden Sie unter [*Vorgehensweise: Einrichten einer Azure-Funktion zum Verarbeiten von Daten*](how-to-create-azure-function.md). Hier finden Sie ein ausführlicheres Beispiel, anhand dessen einige wichtige Konfigurationsoptionen im Zusammenhang mit Funktionen erläutert werden.

Wenn Sie die Authentifizierung in einer Funktion verwenden möchten, müssen Sie zudem Folgendes beachten:
* [Aktivieren der verwalteten Identität](../app-service/overview-managed-identity.md?tabs=dotnet)
* Verwenden von [Umgebungsvariablen](/sandbox/functions-recipes/environment-variables?tabs=csharp) nach Bedarf
* Zuweisen von Berechtigungen zur Funktions-App, um dieser den Zugriff auf die Digital Twins-APIs zu ermöglichen. Weitere Informationen zu Azure Functions-Prozessen finden Sie unter [*Vorgehensweise: von Azure Functions-Apps für die Verarbeitung von Daten*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Andere Methoden für Anmeldeinformationen

Wenn die oben genannten Authentifizierungsszenarien nicht die Anforderungen Ihrer App abdecken, können Sie andere Arten von Authentifizierung untersuchen, die auf der [**Microsoft Identity Platform**](../active-directory/develop/v2-overview.md#getting-started) angeboten werden. Die Dokumentation für diese Plattform behandelt zusätzliche Authentifizierungsszenarien, geordnet nach Anwendungstyp.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zum Thema Sicherheit in Azure Digital Twins:
* [*Konzepte: Sicherheit für Azure Digital Twins-Lösungen*](concepts-security.md)

Oder informieren Sie sich über die Erstellung von Modellen in einer Instanz, wenn Sie die Authentifizierung eingerichtet haben:
* [*Verwenden Verwalten von Azure Digital Twins-Modellen*](how-to-manage-model.md)