---
title: Erstellen einer iOS-App, die sich in Azure AD für die Anmeldung integriert und geschützte APIs mit OAuth 2.0 aufruft | Microsoft Docs
description: Erfahren Sie, wie Sie Benutzer anmelden und die Microsoft Graph-API aus einer iOS-App aufrufen können.
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6274557ede35d7640eba37e5777cb0cb67d459a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497097"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Schnellstart: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer iOS-App

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Für iOS-Clients, die auf geschützte Ressourcen zugreifen müssen, bietet Azure Active Directory (Azure AD) die Active Directory-Authentifizierungsbibliothek (ADAL). ADAL vereinfacht den Prozess Ihrer App zum Abrufen von Zugriffstoken. 

In diesem Schnellstart erstellen Sie eine Objective C-Aufgabenlistenanwendung, die die folgenden Aufgaben ausführt:

* Abrufen von Zugriffstoken zum Aufrufen der Azure AD Graph-API mit dem OAuth 2.0-Authentifizierungsprotokoll
* Durchsuchen eines Verzeichnisses nach Benutzern mit einem bestimmten Alias

Zur Entwicklung einer vollständigen, funktionstüchtigen Anwendung müssen Sie folgende Schritte ausführen:

1. Registrieren Ihrer Anwendung bei Azure AD
1. Installieren und Konfigurieren der ADAL
1. Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie zum Einstieg die folgenden erforderlichen Schritte aus:

* Laden Sie das [Anwendungsgerüst](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) oder [vollständige Beispiel](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) herunter.
* Stellen Sie einen Azure AD-Mandanten bereit, in dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](quickstart-create-new-tenant.md).

> [!TIP]
> Testen Sie das [Entwicklerportal](https://identity.microsoft.com/Docs/iOS), um mit Azure AD in wenigen Minuten starten zu können. Im Entwicklerportal werden Sie durch den Vorgang zum Registrieren einer App und die Integration von Azure AD in Ihren Code geleitet. Nach dem Durchführen dieser Schritte verfügen Sie über eine einfache Anwendung zur Authentifizierung von Benutzern in Ihrem Mandanten sowie über ein Back-End, das Token akzeptieren und eine Überprüfung durchführen kann.

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>Schritt 1: Festlegen des Umleitungs-URI für iOS

Um Ihre Anwendungen in bestimmten SSO-Szenarios sicher zu starten, müssen Sie einen *Umleitungs-URI* in einem bestimmten Format erstellen. Ein Umleitungs-URI wird verwendet, um sicherzustellen, dass die Token an die richtige Anwendung zurückgegeben werden, die sie angefordert hat.

Das iOS-Format für einen Umleitungs-URI lautet:

```
<app-scheme>://<bundle-id>
```

* **app-scheme**: Ist in Ihrem XCode-Projekt registriert und gibt an, wie Sie durch andere Anwendungen aufgerufen werden können. Sie finden „app-scheme“ unter **Info.plist > URL-Typen > URL-Bezeichner**. Erstellen Sie ein „app-scheme“, sofern Sie nicht bereits mindestens ein Schema konfiguriert haben.
* **bundle-id**: Dies ist die Paket-ID, die sich unter **identity** in Ihren XCode-Projekteinstellungen befindet.

Dies ist ein Beispiel für diesen Schnellstartcode:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>Schritt 2: Registrieren der Anwendung DirectorySearcher

Damit Ihre Anwendung Token abrufen kann, müssen Sie sie zunächst in Ihrem Azure AD-Mandanten registrieren und ihr die Berechtigung für den Zugriff auf die Azure AD Graph-API erteilen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie in der oberen Leiste Ihr Konto aus. Wählen Sie in der **Verzeichnisliste** den Active Directory-Mandanten aus, bei dem Sie Ihre Anwendung registrieren möchten.
3. Wählen Sie im linken Navigationsbereich **Alle Dienste** aus, und wählen Sie dann **Azure Active Directory** aus.
4. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
5. Folgen Sie den Eingabeaufforderungen zum Erstellen einer neuen Clientanwendung.
    * **Name** ist der Anwendungsname und beschreibt Ihre Anwendung für Endbenutzer.
    * **Umleitungs-URI** ist eine Kombination aus einem Schema und einer Zeichenfolge, die Azure AD für die Rückgabe der Tokenantworten verwendet. Geben Sie einen Wert ein, der spezifisch für Ihre Anwendung ist und auf den vorherigen Umleitungs-URI-Informationen basiert. Wählen Sie außerdem in der Dropdownliste den Eintrag **Öffentlicher Client (Mobilgerät und Desktop)** aus.
6. Nach Abschluss der Registrierung weist Azure AD Ihrer App eine eindeutige Anwendungs-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten. Daher sollten Sie ihn von der Registerkarte „Anwendung“ kopieren.
7. Wählen Sie auf der Seite **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus. Wählen Sie in **Hiermit wählen Sie eine API aus** die Option ***Microsoft Graph*** aus.
8. Wählen Sie unter **Delegierte Berechtigungen** die Berechtigung **User.Read** aus, und klicken Sie dann auf **Hinzufügen**, um den Vorgang zu speichern. Mit dieser Berechtigung kann die Anwendung die Azure AD Graph-API nach Benutzern abfragen.

## <a name="step-3-install-and-configure-adal"></a>Schritt 3: Installieren und Konfigurieren der ADAL

Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie ADAL installieren und Ihren identitätsbezogenen Code schreiben. Damit ADAL mit Azure AD kommunizieren kann, müssen Sie einige Informationen zur App-Registrierung bereitstellen.

1. Fügen Sie dazu zunächst ADAL mithilfe von CocoaPods dem Projekt „DirectorySearcher“ hinzu.

    ```
    $ vi Podfile
    ```
1. Fügen Sie diesem Podfile folgenden Code hinzu:

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADAL'
    ```

1. Laden Sie die Podfile-Datei mithilfe von CocoaPods. Dieser Schritt erstellt einen neuen XCode-Arbeitsbereich, den Sie laden.

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. Öffnen Sie im Projekt "QuickStart" die PLIST-Datei `settings.plist`.
1. Ersetzen Sie die Werte der Elemente in diesem Abschnitt durch die Werte, die Sie im Azure-Portal eingegeben haben. Sobald Ihr Code die ADAL verwendet, verweist er auf diese Werte.
    * `tenant` ist die Domäne Ihres Azure AD-Mandanten, z.B. contoso.onmicrosoft.com.
    * `clientId` ist die Client-ID der Anwendung, die Sie aus dem Portal kopiert haben.
    * `redirectUri` ist die Umleitungs-URL, die Sie im Portal registriert haben.

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>Schritt 4: Verwenden von ADAL zum Abrufen von Tokens aus Azure AD

Das Grundprinzip von ADAL ist wie folgt: Wann immer Ihre Anwendung ein Zugriffstoken benötigt, ruft sie einen completionBlock `+(void) getToken :` auf, und ADAL erledigt alles Weitere.

1. Öffnen Sie im Projekt `QuickStart` die Datei `GraphAPICaller.m`, und suchen Sie den Kommentar `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` oben in der Datei.

    Dort übergeben Sie die zur Kommunikation mit Azure AD notwendigen Koordinaten über einen CompletionBlock an ADAL und weisen sie an, wie Token zwischengespeichert werden sollen.

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. Mit diesem Token müssen Sie nach Benutzern in Graph suchen. Suchen Sie den `// TODO: implement SearchUsersList`-Kommentar. Diese Methode übergibt der Azure AD Graph-API eine GET-Anforderung für die Suche nach Benutzern, deren UPNs mit dem angegebenen Suchbegriff beginnen. 

    Für die Abfrage der Azure ID Graph-API müssen Sie dem `Authorization`-Header der Anforderung ein access_token (Zugriffstoken) hinzufügen. An dieser Stelle tritt die ADAL auf den Plan.

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```

3. Wenn Ihre App über einen `getToken(...)`-Aufruf ein Token anfordert, versucht die ADAL, ein Token zurückzugeben, ohne den Benutzer nach seinen Anmeldeinformationen zu fragen. Stellt ADAL fest, dass sich der Benutzer zum Abrufen eines Tokens anmelden muss, zeigt es einen Anmeldedialog an, erfasst die Anmeldeinformationen des Benutzers und gibt nach erfolgreicher Authentifizierung ein Token zurück. Wenn ADAL kein Token zurückgeben kann, löst es eine `AdalException` aus.

> [!NOTE]
> Das Objekt `AuthenticationResult` enthält ein `tokenCacheStoreItem`-Objekt, mit dem von Ihrer App benötigte Informationen erfasst werden können. Im Projekt „QuickStart“ wird mithilfe von `tokenCacheStoreItem` ermittelt, ob die Authentifizierung bereits erfolgt ist.

## <a name="step-5-build-and-run-the-application"></a>Schritt 5: Erstellen und Ausführen der Anwendung

Glückwunsch! Sie haben nun eine funktionierende iOS-Anwendung, die Benutzer authentifizieren, Web-APIs über OAuth 2.0 sicher aufrufen und grundlegende Benutzerinformationen abfragen kann.

Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen.

1. Führen Sie die QuickStart-Anwendung aus, und melden Sie sich mit einem der Benutzernamen an.
1. Suchen Sie anhand des UPN nach anderen Benutzern.
1. Beenden Sie die Anwendung, und starten Sie sie erneut. Wie Sie sehen, bleibt die Benutzersitzung erhalten.

ADAL erleichtert Ihnen die Integration all dieser allgemeinen Identitätsfunktionen in Ihrer Anwendung. Sie übernimmt die unangenehmen Verwaltungsarbeiten für Sie, wie z.B. die Cacheverwaltung, Unterstützung des OAuth-Protokolls, Anzeige einer Anmeldeschnittstelle für den Benutzer und Aktualisierung abgelaufener Token. Das Einzige, womit Sie sich noch beschäftigen müssen, ist der API-Aufruf `getToken`.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) auf [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) bereit.

## <a name="next-steps"></a>Nächste Schritte

Sie können sich nun weiteren Szenarien zuwenden. Es wird empfohlen, mit den folgenden Themen fortzufahren:

* [Schützen einer Node.JS-Web-API mit Azure AD](quickstart-v1-nodejs-webapi.md)
* Lesen Sie [Vorgehensweise: Aktivieren von App-übergreifendem SSO unter iOS mit ADAL](howto-v1-enable-sso-ios.md)  
