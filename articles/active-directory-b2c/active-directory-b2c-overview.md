---
title: Was ist Azure Active Directory B2C? | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie in Ihrer Anwendung mithilfe von Azure Active Directory B2C Identitätsumgebungen für die Registrierung, Anmeldung und Profilverwaltung erstellen und verwalten.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cceac260979b4322d41843038eab0998c8e8ba4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509749"
---
# <a name="what-is-azure-active-directory-b2c"></a>Was ist Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C ist ein Dienst für die Business-to-Consumer-Identitätsverwaltung. Dieser Dienst ermöglicht die Anpassung und Steuerung der sicheren Benutzerinteraktion mit Ihren Webanwendungen, Desktopanwendungen, mobilen Anwendungen oder Single-Page-Webanwendungen. Mit Azure AD B2C können Benutzer Registrierungs- und Anmeldevorgänge ausführen, Kennwörter zurücksetzen und Profile bearbeiten. Azure AD B2C implementiert eine Form von OpenID Connect- und OAuth 2.0-Protokollen. Der zentrale Aspekt bei der Implementierung dieser Protokolle sind die Sicherheitstoken und deren Ansprüche, die einen sicheren Zugriff auf Ressourcen ermöglichen.

Eine *User Journey* ist eine Anforderung, die eine Richtlinie angibt, um zu steuern, wie der Benutzer und Ihre Anwendung mit Azure AD B2C interagieren. User Journeys können in Azure AD B2C auf zwei Arten definiert werden: 

Als Anwendungsentwickler können Sie unabhängig von Ihren Identitätskenntnissen allgemeine Identitätsbenutzerabläufe über das Azure-Portal definieren. Wenn Sie Identitätsexperte, Systemintegrator, Berater oder Mitglied eines internen Identitätsteams sind, sich mit OpenID Connect-Abläufen auskennen und mit Identitätsanbietern sowie mit der anspruchsbasierten Authentifizierung vertraut sind, können Sie XML-basierte benutzerdefinierte Richtlinien verwenden.

Bevor Sie eine User Journey definieren, müssen Sie einen Azure AD B2C-Mandanten erstellen und Ihre Anwendung und Ihre API in dem Mandanten registrieren. Anschließend können Sie damit beginnen, eine User Journey mit Benutzerabläufen oder benutzerdefinierten Richtlinien zu definieren. Darüber hinaus können Sie optional Identitätsanbieter hinzufügen/ändern oder den Ablauf der User Journey anpassen.

## <a name="protocols-and-tokens"></a>Protokolle und Token

Azure AD B2C unterstützt für User Journeys das [OpenID Connect- und das OAuth 2.0-Protokolle](active-directory-b2c-reference-protocols.md). In der Azure AD B2C-Implementierung von OpenID Connect startet Ihre Anwendung die User Journey, indem sie Authentifizierungsanforderungen an Azure AD B2C ausgibt. 

Das Ergebnis einer an Azure AD B2C gerichteten Anforderung ist ein Sicherheitstoken (beispielsweise ein [ID-Token oder Zugriffstoken](active-directory-b2c-reference-tokens.md)). Dieses Sicherheitstoken definiert die Identität des Benutzers. Token werden von Azure AD B2C-Endpunkten (beispielsweise `/token` oder `/authorize`) empfangen. Über diese Token können Sie auf Ansprüche zugreifen, die wiederum verwendet werden können, um eine Identität zu überprüfen den Zugriff auf sichere Ressourcen zuzulassen.

## <a name="tenants-and-applications"></a>Mandanten und Anwendungen

Ein *Mandant* in Azure AD B2C stellt Ihre Organisation dar und ist ein Verzeichnis von Benutzern. Jeder Azure AD B2C-Mandant unterscheidet sich von anderen Azure AD B2C-Mandanten und ist von diesen getrennt. Möglicherweise verfügen Sie bereits über einen Azure Active Directory-Mandanten. Der Azure AD B2C-Mandant ist jedoch ein anderer Mandant. Ein Mandant enthält Informationen zu den Benutzern, die sich zur Verwendung Ihrer Anwendung registriert haben. Dazu gehören beispielsweise Kennwörter, Profildaten und Berechtigungen. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](tutorial-create-tenant.md) ausführen.

Damit Sie Ihre Anwendung für die Verwendung von Azure AD B2C konfigurieren können, müssen Sie sie zunächst über das Azure-Portal in dem Mandanten registrieren. Beim Registrierungsprozess werden Werte erfasst und der Anwendung zugewiesen. Dazu gehören eine Anwendungs-ID, um die Anwendung eindeutig zu identifizieren, und ein Umleitungs-URI, um Antworten direkt an die Anwendung zurückzuleiten.

Die Interaktion der einzelnen Anwendungen folgt einem ähnlichen allgemeinen Muster:

1. Der Benutzer wird in der Anwendung angewiesen, eine Richtlinie auszuführen.
2. Der Benutzer schließt die Richtlinie gemäß Richtliniendefinition ab.
3. Die Anwendung empfängt ein Token.
4. Die Anwendung versucht, unter Verwendung des Tokens auf eine Ressource zuzugreifen.
5. Der Ressourcenserver überprüft das Token, um sich zu vergewissern, dass der Zugriff gewährt werden kann.
6. Die Anwendung aktualisiert das Token von Zeit zu Zeit.

Führen Sie zum Registrieren einer Webanwendung die Schritte unter [Tutorial: Registrieren einer Anwendung zum Aktivieren der Registrierung und Anmeldung mit Azure AD B2C](tutorial-register-applications.md) aus. Darüber hinaus können Sie [Ihrem Azure Active Directory B2C-Mandanten eine Web-API-Anwendung hinzufügen](add-web-application.md) oder [Ihrem Active Directory B2C-Mandanten eine native Clientanwendung hinzufügen](add-native-application.md).

## <a name="user-journeys"></a>User Journeys

Die Richtlinie in einer User Journey kann als [Benutzerablauf](active-directory-b2c-reference-policies.md) oder als [benutzerdefinierte Richtlinie](active-directory-b2c-overview-custom.md) definiert werden. Für die gängigsten Identitätsaufgaben (wie Registrierung, Anmeldung und Profilbearbeitung) stehen im Azure-Portal vordefinierte Benutzerabläufe zur Verfügung.

Mit User Journeys können Sie zur Steuerung des Verhaltens folgende Einstellungen konfigurieren:

- Konten für soziale Netzwerke, mit denen sich der Benutzer für die Anwendung registrieren kann
- Vom Benutzer erfasste Daten wie Vorname oder Postleitzahl
- Multi-Factor Authentication
- Erscheinungsbild von Seiten
- Informationen, die an die Anwendung zurückgegeben werden

Benutzerdefinierte Richtlinien sind Konfigurationsdateien, die das Verhalten des [Identity Experience Framework](trustframeworkpolicy.md) in Ihrem Azure AD B2C-Mandanten definieren. Das Identity Experience Framework ist die zugrunde liegende Plattform, die die Vertrauensstellung zwischen mehreren Seiten herstellt und die Schritte in einer User Journey abwickelt. 

Benutzerdefinierte Richtlinien können zur Durchführung zahlreicher Aufgaben geändert werden. Bei benutzerdefinierten Richtlinien handelt es sich um einzelne oder mehrere Dateien im XML-Format, die in einer hierarchischen Kette aufeinander verweisen. Für benutzerdefinierte Richtlinien steht ein [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) zur Verfügung, um gängige Identitätsaufgaben zu ermöglichen. 

Benutzerdefinierte Richtlinien oder Benutzerabläufe verschiedener Art werden in Ihrem Azure AD B2C-Mandanten nach Bedarf verwendet und können anwendungsübergreifend wiederverwendet werden. Durch diese Flexibilität können Sie Benutzeroberflächen im Zusammenhang mit der Benutzeridentität mit minimalen oder ganz ohne Änderungen am Code definieren und ändern. Richtlinien werden durch Hinzufügen eines speziellen Abfrageparameters in HTTP-Authentifizierungsanforderungen verwendet. Informationen zum Erstellen Ihrer eigenen benutzerdefinierten Richtlinie finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="identity-providers"></a>Identitätsanbieter 

Sie können Benutzern in Ihren Anwendungen die Anmeldung mit verschiedenen Identitätsanbietern ermöglichen. Ein *Identitätsanbieter* erstellt und verwaltet die Identitätsinformationen und stellt gleichzeitig Authentifizierungsdienste für Anwendungen bereit. Von Azure AD B2C unterstützte Identitätsanbieter können über das Azure-Portal hinzugefügt werden. 

In der Regel wird in einer Anwendung nur ein einzelner Identitätsanbieter verwendet. Sie haben aber die Möglichkeit, weitere hinzuzufügen. Um einen Identitätsanbieter in Ihrem Azure AD B2C-Mandanten zu konfigurieren, müssen Sie zunächst auf der Entwicklerwebsite des Identitätsanbieters eine Anwendung erstellen und dann den Anwendungs- oder Clientbezeichner und das Kennwort oder Clientgeheimnis der Identitätsanbieteranwendung erfassen, die Sie erstellen. Mit diesem Bezeichner und Kennwort wird die Anwendung dann konfiguriert. 

In den folgenden Artikeln erfahren Sie, wie Sie einige der gängigen Identitätsanbieter zu Benutzerabläufen hinzufügen:

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft-Konto](active-directory-b2c-setup-msa-app.md)

In den folgenden Artikeln erfahren Sie, wie Sie einige der gängigen Identitätsanbieter zu benutzerdefinierten Richtlinien hinzufügen:
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft-Konto](active-directory-b2c-custom-setup-msa-idp.md)

Weitere Informationen finden Sie unter [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md).


## <a name="page-customization"></a>Seitenanpassung

Die meisten der HTML- und CSS-Inhalte, die Kunden im Rahmen einer User Journey angezeigt werden, sind steuerbar. Mithilfe der Seitenanpassung können Sie das Erscheinungsbild von benutzerdefinierten Richtlinien oder Benutzerabläufen anpassen. Darüber hinaus dient dieses Anpassungsfeature zur Steuerung der Konsistenz von Marken und visuellen Elementen zwischen Ihrer Anwendung und Azure AD B2C. 

Azure AD B2C führt den Code im Browser des Benutzers aus und verwendet einen modernen Ansatz namens CORS (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen). Zuerst legen Sie eine URL in einer Richtlinie mit benutzerdefiniertem HTML-Inhalt fest. Azure AD B2C führt die Benutzeroberflächenelemente mit dem HTML-Inhalt, der über Ihre URL geladen wird, zusammen und zeigt anschließend die Seite für den Benutzer an.

Sie senden Parameter in einer Abfragezeichenfolge an Azure AD B2C. Durch Übergeben des Parameters an Ihren HTML-Endpunkt wird der Seiteninhalt dynamisch geändert. So können Sie beispielsweise das Hintergrundbild auf der Registrierungs- oder Anmeldeseite auf der Grundlage eines Parameters ändern, den Sie über Ihre Web- oder Mobilanwendung übergeben.

Informationen zum Anpassen von Seiten in einem Benutzerablauf finden Sie unter [Tutorial: Anpassen der Benutzeroberfläche in Azure Active Directory B2C](tutorial-customize-ui.md). Informationen zum Anpassen von Seiten in einer benutzerdefinierten Richtlinie finden Sie unter [Anpassen der Benutzeroberfläche einer Anwendung mithilfe einer benutzerdefinierten Richtlinie in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md) sowie unter [Azure Active Directory B2C: Konfigurieren der Benutzeroberfläche mit dynamischen Inhalten mithilfe von benutzerdefinierten Richtlinien](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="developer-resources"></a>Entwicklerressourcen

### <a name="client-applications"></a>Clientanwendungen

Zur Auswahl stehen unter anderem Anwendungen für [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) und .NET. Azure AD B2C ermöglicht diese Aktionen und schützt gleichzeitig die Identitäten Ihrer Benutzer.

Wenn Sie ASP.NET-Webanwendungen entwickeln, richten Sie Ihre Anwendung zum Authentifizieren von Konten anhand der Schritte unter [Tutorial: Aktivieren der Authentifizierung von Web-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-web-app.md) ein.

Wenn Sie Desktopanwendungen entwickeln, richten Sie Ihre Anwendung zum Authentifizieren von Konten anhand der Schritte unter [Tutorial: Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) ein.

Wenn Sie Single-Page-Webanwendungen mit Node.js entwickeln, richten Sie Ihre Anwendung zum Authentifizieren von Konten anhand der Schritte unter [Tutorial: Aktivieren der Authentifizierung einseitiger Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md) ein.

### <a name="apis"></a>APIs
Wenn Ihre Client- oder Webanwendungen APIs aufrufen müssen, können Sie den sicheren Zugriff auf diese Ressourcen in Azure AD B2C einrichten.

Wenn Sie ASP.NET-Webanwendungen entwickeln, richten Sie Ihre Anwendung zum Aufrufen einer geschützten API anhand der Schritte unter [Tutorial: Gewähren des Zugriffs auf eine ASP.NET-Web-API unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-web-api.md) ein.

Wenn Sie Desktopanwendungen entwickeln, richten Sie Ihre Anwendung zum Aufrufen einer geschützten API anhand der Schritte unter [Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app-webapi.md) ein.

Wenn Sie Single-Page-Webanwendung mit Node.js entwickeln, richten Sie Ihre Anwendung zum Authentifizieren von Konten anhand der Schritte unter [Tutorial: Gewähren des Zugriffs auf eine ASP.NET Core-Web-API über eine Single-Page-Webanwendung mithilfe von Azure Active Directory B2C](active-directory-b2c-tutorials-spa-webapi.md) ein.

### <a name="javascript"></a>JavaScript

Sie können Ihren Anwendungen in Azure AD B2C Ihren eigenen clientseitigen JavaScript-Code hinzufügen. Wenn Sie JavaScript in Ihrer Anwendung einrichten möchten, definieren Sie einen [Seitenvertrag](page-contract.md), und aktivieren Sie [JavaScript](javascript-samples.md) in Ihren Benutzerabläufen oder benutzerdefinierten Richtlinien.

### <a name="user-accounts"></a>Benutzerkonten

Viele allgemeine Aufgaben zur Mandantenverwaltung müssen programmgesteuert ausgeführt werden. Ein gutes Beispiel ist die Benutzerverwaltung. Unter Umständen müssen Sie einen vorhandenen Benutzerspeicher zu einem Azure AD B2C-Mandanten migrieren. Sie möchten die Benutzerregistrierung vielleicht auf Ihrer eigenen Seite hosten und Benutzerkonten im Azure AD B2C-Verzeichnis im Hintergrund erstellen. Diese Arten von Aufgaben erfordern die Fähigkeit zum Erstellen, Lesen, Aktualisieren und Löschen von Benutzerkonten. Hierfür können Sie die [Azure AD Graph-API](active-directory-b2c-devquickstarts-graph-dotnet.md) verwenden.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Tutorial fort, und beginnen Sie mit der Konfiguration Ihrer Anwendung für die Registrierung und Anmeldung.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](tutorial-create-tenant.md)
