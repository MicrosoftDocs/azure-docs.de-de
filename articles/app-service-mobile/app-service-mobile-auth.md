---
title: Authentifizierung und Autorisierung in Azure App Service für mobile Apps | Microsoft-Dokumentation
description: Enthält eine grundlegende Übersicht über das Authentifizierungs-/Autorisierungsfeature für Azure App Service, wobei der Schwerpunkt auf mobilen Apps liegt.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 87bdfcc827155e5dd0a02ffb1640bf7e9cd4e479
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859123"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Authentifizierung und Autorisierung in Azure App Service für mobile Apps

In diesem Artikel wird beschrieben, wie die Authentifizierung und Autorisierung funktioniert, wenn Sie native mobile Apps mit einem App Service-Back-End entwickeln. App Service umfasst eine integrierte Authentifizierung und Autorisierung, und Ihre mobilen Apps können die Anmeldung für Benutzer durchführen, ohne dass in App Service hierfür Code geändert werden muss. Es stellt eine einfache Möglichkeit zum Schutz Ihrer Anwendung und für die Arbeit mit benutzerspezifischen Daten bereit. 

In diesem Artikel geht es um die Entwicklung von mobilen Apps. Beim schnellen Einstieg in die App Service-Authentifizierung und -Autorisierung für Ihre mobile App helfen Ihnen die folgenden Tutorials weiter: [Hinzufügen der Authentifizierung zu Ihrer iOS-App][iOS] (bzw. [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms] oder [Cordova]). 

Informationen zur Funktionsweise der Authentifizierung und Autorisierung in App Service finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Authentifizierung mit Anbieter-SDK

Nachdem alles in App Service konfiguriert wurde, können Sie mobile Clients für die Anmeldung mit App Service anpassen. Dazu gibt es zwei Ansätze:

* Verwenden Sie ein von einem bestimmten Identitätsanbieter veröffentlichtes SDK, um zuerst die Identität und anschließend den Zugriff auf App Service einzurichten.
* Verwenden Sie eine einzelne Codezeile, um Benutzeranmeldungen über das Mobile Apps-Client-SDK zu ermöglichen.

> [!TIP]
> Bei den meisten Anwendungen empfiehlt sich die Verwendung eines Anbieter-SDK, um eine möglichst konsistente Benutzeranmeldung zu erhalten. Außerdem profitieren Sie bei dieser Variante von der Unterstützung der Tokenaktualisierung und weiteren Vorteilen des jeweiligen Anbieters.
> 
> 

Bei Verwendung eines Anbieter-SDKs können sich die Benutzer in einer Umgebung anmelden, die stärker in das Betriebssystem eingebunden ist, unter dem die Anwendung ausgeführt wird. Bei diesem Verfahren erhalten Sie außerdem ein Anbietertoken und bestimmte Benutzerinformationen auf dem Client, sodass die Nutzung von Graph-APIs und die Anpassung der Benutzerumgebung wesentlich vereinfacht wird. Dies wird in Blogs und Foren gelegentlich als „Clientfluss“ oder „clientgesteuerter Datenfluss“ bezeichnet, da Benutzer durch auf dem Client befindlichen Code angemeldet werden und der Clientcode Zugriff auf ein Anbietertoken erhält.

Das abgerufene Anbietertoken muss zur Überprüfung an App Service gesendet werden. Nach der Tokenüberprüfung durch App Service wird ein neues App Service-Token erstellt und an den Client zurückgegeben. Im Mobile Apps-Client-SDK stehen Hilfsmethoden zur Verfügung, um diesen Austausch zu verwalten und das Token automatisch an alle Anforderungen anzufügen, die an das Anwendungs-Back-End gerichtet werden. Entwickler können auch einen Verweis auf das Anbietertoken speichern.

Weitere Informationen zum Authentifizierungsablauf finden Sie unter [Authentication flow](../app-service/overview-authentication-authorization.md#authentication-flow) (Authentifizierungsablauf). 

## <a name="authentication-without-provider-sdk"></a>Authentifizierung ohne Anbieter-SDK

Wenn Sie kein Anbieter-SDK einrichten möchten, können Sie die Anmeldung dem Mobile Apps-Feature von Azure App Service überlassen. Das Mobile Apps-Client-SDK öffnet eine Webansicht für den Anbieter Ihrer Wahl und meldet den Benutzer an. Dies wird in Blogs und Foren gelegentlich als „Serverfluss“ oder „servergesteuerter Datenfluss“ bezeichnet, da der Server den Prozess für die Benutzeranmeldung verwaltet und das Client-SDK das Anbietertoken niemals erhält.

Den Code zum Starten dieses Datenflusses finden Sie im entsprechenden plattformspezifischen Authentifizierungstutorial. Am Ende des Datenflusses verfügt das Client-SDK über ein App Service-Token, und das Token wird automatisch an alle Anforderungen angefügt, die an das Anwendungs-Back-End gerichtet werden.

Weitere Informationen zum Authentifizierungsablauf finden Sie unter [Authentication flow](../app-service/overview-authentication-authorization.md#authentication-flow) (Authentifizierungsablauf). 
## <a name="more-resources"></a>Weitere Ressourcen

In den folgenden Tutorials erfahren Sie, wie Sie Ihren mobilen Clients die Authentifizierung mit [servergesteuertem Datenfluss](../app-service/overview-authentication-authorization.md#authentication-flow) hinzufügen:

* [Hinzufügen der Authentifizierung zu Ihrer iOS-App][iOS]
* [Hinzufügen der Authentifizierung zu Ihrer Android-App][Android]
* [Hinzufügen der Authentifizierung zu Ihrer Windows-App][Windows]
* [Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App][Xamarin.iOS]
* [Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App][Xamarin.Android]
* [Hinzufügen der Authentifizierung zu Ihrer Xamarin.Forms-App][Xamarin.Forms]
* [Hinzufügen der Authentifizierung zu Ihrer Cordova-App][Cordova]

Informationen zur Verwendung des [clientgesteuerten Datenflusses](../app-service/overview-authentication-authorization.md#authentication-flow) für Azure Active Directory finden Sie hier:

* [Verwenden der Active Directory-Authentifizierungsbibliothek für iOS][ADAL-iOS]
* [Verwenden der Active Directory-Authentifizierungsbibliothek für Android][ADAL-Android]
* [Verwenden der Active Directory-Authentifizierungsbibliothek für Windows und Xamarin][ADAL-dotnet]

Informationen zur Verwendung des [clientgesteuerten Datenflusses](../app-service/overview-authentication-authorization.md#authentication-flow) für Facebook finden Sie in den folgenden Ressourcen:

* [Verwenden des Facebook-SDK für iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Informationen zur Verwendung des [clientgesteuerten Datenflusses](../app-service/overview-authentication-authorization.md#authentication-flow) für Twitter finden Sie in den folgenden Ressourcen:

* [Verwenden von Twitter Fabric für iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Informationen zur Verwendung des [clientgesteuerten Datenflusses](../app-service/overview-authentication-authorization.md#authentication-flow) für Google finden Sie in den folgenden Ressourcen:

* [Verwenden des Google-Anmelde-SDK für iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
