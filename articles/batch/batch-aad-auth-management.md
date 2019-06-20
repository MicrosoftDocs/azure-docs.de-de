---
title: Verwenden von Azure Active Directory zum Authentifizieren von Batch Management-Lösungen | Microsoft-Dokumentation
description: Mit Azure Resource Manager erstellte Anwendungen und der Batch-Ressourcenanbieter werden mit Azure AD authentifiziert.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/27/2017
ms.author: lahugh
ms.openlocfilehash: 0f6db6d9c86e6da047c45ae7b1c43cf5f55c7e2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922857"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Authentifizieren von Batch Management-Lösungen mit Active Directory

Anwendungen, die den Azure Batch Management-Dienst aufrufen, werden mit [Azure Active Directory][aad_about] (Azure AD) authentifiziert. Azure AD ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Azure selbst verwendet Azure AD für die Authentifizierung seiner Kunden, Dienstadministratoren und Organisationsbenutzer.

Über die Batch Management .NET-Bibliothek werden Typen zur Verwendung von Batch-Konten, -Kontoschlüsseln, -Anwendungen und -Anwendungspaketen verfügbar gemacht. Die Batch Management .NET-Bibliothek ist ein Azure-Ressourcenanbieterclient, der zusammen mit [Azure Resource Manager][resman_overview] zum programmgesteuerten Verwalten dieser Ressourcen verwendet wird. Azure AD ist erforderlich, um Anforderungen über beliebige Azure-Ressourcenanbieterclients, einschließlich der Batch Management .NET-Bibliothek, und über [Azure Resource Manager][resman_overview] zu authentifizieren.

In diesem Artikel wird beschrieben, wie Azure AD zum Authentifizieren von Anwendungen verwendet werden kann, für die die Batch Management .NET-Bibliothek genutzt wird. Es wird veranschaulicht, wie Sie Azure AD zum Authentifizieren eines Administrators oder Co-Administrators eines Abonnements per integrierter Authentifizierung einsetzen können. Wir nutzen das [AccountManagement][acct_mgmt_sample]-Beispielprojekt auf GitHub, um die Schritte der Verwendung von Azure AD mit der Batch Management .NET-Bibliothek zu veranschaulichen.

Weitere Informationen zur Nutzung der Batch Management .NET-Bibliothek und des AccountManagement-Beispiels finden Sie unter [Verwalten von Batch-Konten und -Kontingenten mit der Batch Management-Clientbibliothek für .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Registrieren Ihrer Anwendung bei Azure AD

Die Azure [Active Directory Authentication Library][aad_adal] (ADAL) stellt eine programmgesteuerte Schnittstelle für Azure AD bereit, die Sie in Ihren Anwendungen einsetzen können. Zum Aufrufen von ADAL aus Ihrer Anwendung müssen Sie Ihre Anwendung in einem Azure AD-Mandanten registrieren. Beim Registrieren Ihrer Anwendung stellen Sie für Azure AD Informationen zu Ihrer Anwendung bereit, einschließlich eines entsprechenden Namens im Azure AD-Mandanten. Azure AD stellt dann eine Anwendungs-ID bereit, die Sie verwenden, um Ihre Anwendung zur Laufzeit Azure AD zuzuordnen. Weitere Informationen zur Anwendungs-ID finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Führen Sie die Schritte im Abschnitt [Adding an Application](../active-directory/develop/quickstart-register-app.md) (Hinzufügen einer Anwendung) des Artikels [Integrating applications with Azure Active Directory][aad_integrate] (Integrieren von Anwendungen in Azure Active Directory) aus, um die AccountManagement-Beispielanwendung zu registrieren. Geben Sie als Typ der Anwendung **Systemeigene Clientanwendung** an. Der OAuth 2.0-URI nach Industriestandard für den **Umleitungs-URI** ist `urn:ietf:wg:oauth:2.0:oob`. Für den **Umleitungs-URI** können Sie einen beliebigen gültigen URI angeben (z. B. `http://myaccountmanagementsample`), da dies kein echter Endpunkt sein muss:

![](./media/batch-aad-auth-management/app-registration-management-plane.png)

Nachdem Sie den Registrierungsprozess abgeschlossen haben, werden die Anwendungs-ID und die Objekt-ID (Dienstprinzipal) angezeigt, die für Ihre Anwendung aufgeführt sind.  

![](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Gewähren des Zugriffs auf Ihre Anwendung für die Azure Resource Manager-API

Als Nächstes müssen Sie den Zugriff auf Ihre Anwendung an die Azure Resource Manager-API delegieren. Der Azure AD-Bezeichner für die Resource Manager-API lautet **Windows Azure-Service-Verwaltungs-API**.

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Wählen Sie im linken Navigationsbereich des Azure-Portals **Alle Dienste** aus, klicken Sie auf **App-Registrierungen** und dann auf **Hinzufügen**.
2. Suchen Sie in der Liste mit den App-Registrierungen nach dem Namen Ihrer Anwendung:

    ![Suchen nach Ihrem Anwendungsnamen](./media/batch-aad-auth-management/search-app-registration.png)

3. Zeigen Sie das Blatt **Einstellungen** an. Wählen Sie im Abschnitt **API-Zugriff** die Option **Erforderliche Berechtigungen**.
4. Klicken Sie auf **Hinzufügen**, um eine neue erforderliche Berechtigung hinzuzufügen. 
5. Geben Sie in Schritt 1 **Windows Azure-Service-Verwaltungs-API** ein, wählen Sie diese API in der Liste mit den Ergebnissen aus, und klicken Sie auf die Schaltfläche **Auswählen**.
6. Aktivieren Sie in Schritt 2 das Kontrollkästchen neben **Access Azure classic deployment model as organization users** (Als Organisationsbenutzer auf das klassische Azure-Bereitstellungsmodell zugreifen), und klicken Sie auf die Schaltfläche **Auswählen**.
7. Klicken Sie auf die Schaltfläche **Fertig**.

Auf dem Blatt **Erforderliche Berechtigungen** wird jetzt angezeigt, dass Berechtigungen für Ihre Anwendung sowohl für die ADAL- als auch für die Resource Manager-APIs gewährt werden. Berechtigungen für ADAL werden standardmäßig gewährt, wenn Sie Ihre App zum ersten Mal mit Azure AD registrieren.

![Delegieren von Berechtigungen für die Azure Resource Manager-API](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Azure AD-Endpunkte

Um Ihre Batch Management-Lösungen mit Azure AD authentifizieren zu können, benötigen Sie zwei bekannte Endpunkte.

- Der **allgemeine Azure AD-Endpunkt** bietet eine generische Schnittstelle für die Erfassung von Anmeldeinformationen, wenn wie bei der integrierten Authentifizierung kein bestimmter Mandant bereitgestellt wird:

    `https://login.microsoftonline.com/common`

- Der **Azure Resource Manager-Endpunkt** wird verwendet, um ein Token zum Authentifizieren von Anforderungen für den Batch Management-Dienst abzurufen:

    `https://management.core.windows.net/`

Die Beispielanwendung „AccountManagement“ definiert Konstanten für diese Endpunkte. Lassen Sie diese Konstanten unverändert:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint 
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Verweisen Sie auf Ihre Anwendungs-ID. 

Ihre Clientanwendung verwendet die Anwendungs-ID (auch als Client-ID bezeichnet) zum Zugreifen auf Azure AD zur Laufzeit. Nachdem Sie die Anwendung im Azure-Portal registriert haben, können Sie Ihren Code aktualisieren, damit die Anwendungs-ID verwendet wird, die von Azure AD für Ihre registrierte Anwendung bereitgestellt wurde. Kopieren Sie Ihre Anwendungs-ID in der AccountManagement-Beispielanwendung aus dem Azure-Portal in die entsprechende Konstante:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Azure AD Graph API. For information
// about registering an application in Azure Active Directory, please see "Adding an Application" here:
// https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
private const string ClientId = "<application-id>";
```
Kopieren Sie außerdem den Umleitungs-URI, den Sie während des Registrierungsprozesses angegeben haben. Der im Code angegebene Umleitungs-URI muss mit dem Umleitungs-URI übereinstimmen, den Sie beim Registrieren der Anwendung bereitgestellt haben.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Abrufen eines Azure AD-Authentifizierungstokens

Nach dem Registrieren des AccountManagement-Beispiels im Azure AD-Mandanten und Aktualisieren des Quellcodes des Beispiels mit Ihren Werten ist das Beispiel für die Authentifizierung mit Azure AD bereit. Wenn Sie das Beispiel ausführen, wird für ADAL versucht, ein Authentifizierungstoken abzurufen. In diesem Schritt werden Sie zum Eingeben Ihrer Microsoft-Anmeldeinformationen aufgefordert: 

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Nach dem Angeben der Anmeldeinformationen kann die Beispielanwendung damit fortfahren, authentifizierte Anforderungen für den Batch Management-Dienst auszustellen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Ausführen der [AccountManagement-Beispielanwendung][acct_mgmt_sample] finden Sie unter [Verwalten von Batch-Konten und -Kontingenten mit der Batch Management-Clientbibliothek für .NET](batch-management-dotnet.md).

Weitere Informationen zu Azure AD finden Sie unter [Dokumentation zu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/). Ausführliche Beispiele zur ADAL-Verwendung finden Sie in der Bibliothek mit den [Azure-Codebeispielen](https://azure.microsoft.com/resources/samples/?service=active-directory).

Informationen zum Authentifizieren von Batch-Dienstanwendungen mit Azure AD finden Sie unter [Authentifizieren von Batch-Dienstlösungen mit Active Directory](batch-aad-auth.md). 


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Was ist Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Authentifizierungsszenarien für Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrieren von Anwendungen in Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[azure_portal]: https://portal.azure.com
[resman_overview]: ../azure-resource-manager/resource-group-overview.md
