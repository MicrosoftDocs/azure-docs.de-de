---
title: Microsoft Identity Platform – Glossar für Entwickler | Azure
description: Eine Liste von Begriffen für häufig verwendete Konzepte und Features für Entwickler von Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c989b690e9537dcaaf3710996474a1b8b99826b
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962735"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft Identity Platform – Glossar für Entwickler

Dieser Artikel enthält Definitionen für einige der zentralen Entwicklerkonzepte sowie Terminologie als Hilfe beim Kennenlernen der Anwendungsentwicklung mithilfe von Microsoft Identity Platform.

## <a name="access-token"></a>Zugriffstoken

Eine Art von [Sicherheitstoken](#security-token). Wird von einem [Autorisierungsserver](#authorization-server) ausgestellt und von einer [Clientanwendung](#client-application) für den Zugriff auf einen [geschützten Ressourcenserver](#resource-server) verwendet. Das Token liegt in der Regel in Form eines [JSON Web Token (JWT)][JWT] vor und stellt die Autorisierung dar, die dem Client durch den [Ressourcenbesitzer](#resource-owner) für eine angeforderte Zugriffsstufe gewährt wurde. Das Token enthält alle geltenden [Ansprüche](#claim) für den Antragsteller und kann von der Clientanwendung beim Zugriff auf eine bestimmte Ressource als eine Art von Anmeldeinformationen verwendet werden. Der Ressourcenbesitzer muss somit gegenüber dem Client keine Anmeldeinformationen angeben.

Zugriffstoken werden abhängig von den vorgelegten Anmeldeinformationen gelegentlich auch als „Benutzer- und App-Token“ oder als „ App-exklusives Token“ bezeichnet. Beispielverwendungsszenarien für die Clientanwendung:

* [Autorisierungsgewährung mit Autorisierungscode](#authorization-grant): In diesem Fall wird der Endbenutzer zunächst als Ressourcenbesitzer authentifiziert, und die Autorisierung für den Ressourcenzugriff wird an den Client delegiert. Der Client wird später beim Beziehen des Zugriffstokens authentifiziert. Das Token wird gelegentlich spezifischer als Benutzer- und App-Token bezeichnet, da es sowohl den Benutzer, der die Clientanwendung autorisiert hat, als auch die Anwendung darstellt.
* [Autorisierungsgewährung mit Clientanmeldeinformationen](#authorization-grant): In diesem Fall stellt der Client die einzige Authentifizierung (ohne Authentifizierung/Autorisierung des Ressourcenbesitzers) bereit, weshalb das Token gelegentlich als App-exklusives Token bezeichnet wird.

Weitere Details finden Sie unter [Microsoft Identity Platform – Tokenreferenz][AAD-Tokens-Claims].

## <a name="application-id-client-id"></a>Anwendungs-ID (Client-ID)

Der eindeutige Bezeichner, den Azure AD für eine Anwendungsregistrierung ausgibt, die eine bestimmte Anwendung und die zugehörigen Konfigurationen kennzeichnet. Diese Anwendungs-ID ([Client-ID](https://tools.ietf.org/html/rfc6749#page-15)) wird verwendet, wenn Authentifizierungsanforderungen ausgeführt werden. Sie wird den Authentifizierungsbibliotheken in der Entwicklungszeit bereitgestellt. Die Anwendungs-ID (Client-ID) ist kein Geheimnis.

## <a name="application-manifest"></a>Anwendungsmanifest

Ein Feature des [Azure-Portals][AZURE-portal], das eine JSON-Darstellung der Identitätskonfiguration der Anwendung generiert. Diese wird als Mechanismus für die Aktualisierung der zugehörigen Entitäten [Anwendung][AAD-Graph-App-Entity] und [Dienstprinzipal][AAD-Graph-Sp-Entity] verwendet. Ausführlichere Informationen finden Sie unter [Grundlegendes zum Azure Active Directory-Anwendungsmanifest][AAD-App-Manifest].

## <a name="application-object"></a>Anwendungsobjekt

Wenn Sie eine Anwendung im [Azure-Portal][AZURE-portal] registrieren bzw. aktualisieren, erstellt bzw. aktualisiert das Portal sowohl ein [Anwendungsobjekt](#service-principal-object) als auch ein entsprechendes Dienstprinzipalobjekt für den Mandanten. Das Anwendungsobjekt *definiert* die Identitätskonfiguration der Anwendung global (also für alle Mandanten, auf die es Zugriff hat) und stellte eine Vorlage bereit, von der die entsprechenden Dienstprinzipalobjekte für die lokale Verwendung zur Laufzeit (in einem bestimmten Mandanten) *abgeleitet* werden.

Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte][AAD-App-SP-Objects].

## <a name="application-registration"></a>Anwendungsregistrierung

Um einer Anwendung die Integration in Azure AD sowie die Delegierung von Identitäts- und Zugriffsverwaltungsfunktionen an Azure AD zu ermöglichen, muss sie bei einem Azure AD-[Mandanten](#tenant) registriert werden. Wenn Sie Ihre Anwendung bei Azure AD registrieren, stellen Sie eine Identitätskonfiguration für Ihre Anwendung bereit. Dies ermöglicht neben der Integration in Azure AD unter anderem auch die Verwendung folgender Features:

* Stabile Verwaltung von einmaligem Anmelden mit Azure AD-Identitätsverwaltung und [OpenID Connect][OpenIDConnect]-Protokollimplementierung
* Vermittelter Zugriff auf [geschützte Ressourcen](#resource-server) durch [Clientanwendungen](#client-application) über den OAuth 2.0-[Autorisierungsserver](#authorization-server)
* [Consent Framework](#consent) zum Verwalten des Clientzugriffs auf geschützte Ressourcen auf der Grundlage der Ressourcenbesitzerautorisierung

Ausführlichere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory][AAD-Integrating-Apps].

## <a name="authentication"></a>authentication

Die Anforderung ordnungsgemäßer Anmeldeinformationen von einer Partei. Bildet die Grundlage für die Erstellung eines Sicherheitsprinzipals für die Identitäts- und Zugriffssteuerung. Im Rahmen einer [OAuth2-Autorisierungsgewährung](#authorization-grant) übernimmt die authentifizierende Partei beispielsweise abhängig von der verwendeten Gewährung entweder die Rolle des [Ressourcenbesitzers](#resource-owner) oder die Rolle der [Clientanwendung](#client-application).

## <a name="authorization"></a>authorization

Die Gewährung einer Handlungsberechtigung für einen authentifizierten Sicherheitsprinzipal. Im Programmiermodell von Azure AD sind im Wesentlichen zwei Anwendungsfälle vorgesehen:

* Im Rahmen einer [OAuth2-Autorisierungsgewährung](#authorization-grant): Wenn der [Ressourcenbesitzer](#resource-owner) die [Clientanwendung](#client-application) autorisiert und dem Client dadurch Zugriff auf Ressourcen der Ressourcenbesitzers gewährt.
* Im Rahmen des Ressourcenzugriffs durch den Client: Gemäß Implementierung durch den [Ressourcenserver](#resource-server) unter Verwendung der Werte des [Anspruchs](#claim) aus dem [Zugriffstoken](#access-token), die als Grundlage für Zugriffssteuerungsentscheidungen herangezogen werden.

## <a name="authorization-code"></a>Autorisierungscode

Ein kurzlebiges Token, das einer [Clientanwendung](#client-application) vom [Autorisierungsendpunkt](#authorization-endpoint) im Rahmen des Autorisierungscodeablaufs bereitgestellt wird (eine der vier OAuth2-[Autorisierungsgewährungen](#authorization-grant)). Der Code wird als Antwort auf die Authentifizierung eines [Ressourcenbesitzers](#resource-owner)an die Clientanwendung zurückgegeben und gibt an, dass der Ressourcenbesitzer die Autorisierung delegiert hat, um den Ressourcenzugriff zu ermöglichen. Im Rahmen des Ablaufs wird der Code gegen ein [Zugriffstoken](#access-token) eingetauscht.

## <a name="authorization-endpoint"></a>Autorisierungsendpunkt

Einer der vom [Autorisierungsserver](#authorization-server) implementierten Endpunkte. Wird für die Interaktion mit dem [Ressourcenbesitzer](#resource-owner) verwendet, um bei einem OAuth2-Autorisierungsgewährungsablauf eine [Autorisierungsgewährung](#authorization-grant) bereitzustellen. Die tatsächliche Gewährung kann je nach verwendetem Autorisierungsgewährungsablauf variieren (einschließlich [Autorisierungscode](#authorization-code) oder [Sicherheitstoken](#security-token)).

Ausführlichere Informationen finden Sie in der OAuth2-Spezifikation in den Abschnitten zu [Autorisierungsgewährungstypen][OAuth2-AuthZ-Grant-Types] und zum [Autorisierungsendpunkt][OAuth2-AuthZ-Endpoint] sowie in der [OpenIDConnect-Spezifikation][OpenIDConnect-AuthZ-Endpoint].

## <a name="authorization-grant"></a>Autorisierungsgewährung

Anmeldeinformationen, die die [Autorisierung](#authorization) des [Ressourcenbesitzers](#resource-owner) für den Zugriff auf seine geschützten Ressourcen durch eine [Clientanwendung](#client-application) darstellen. Eine Clientanwendung kann abhängig von Clienttyp/Anforderungen einen der [vier durch das OAuth2-Autorisierungsframework definierten Gewährungstypen][OAuth2-AuthZ-Grant-Types] verwenden, um eine Gewährung zu beziehen. Zur Auswahl stehen Autorisierungscodegewährung, Clientanmeldeinformations-Gewährung, implizite Gewährung und Ressourcenbesitzerkennwort-Anmeldeinformationsgewährung. Bei den an den Client zurückgegebenen Anmeldeinformationen handelt es sich je nach Art des verwendeten Autorisierungsgewährungsablaufs entweder um ein [Zugriffstoken](#access-token) oder um einen [Autorisierungscode](#authorization-code) (der später gegen ein Zugriffstoken eingetauscht wird).

## <a name="authorization-server"></a>Autorisierungsserver

Gemäß Definition des [OAuth2-Autorisierungsframeworks][OAuth2-Role-Def] der Server für die Ausstellung von Zugriffstoken für den [Client](#client-application) nach erfolgreicher Authentifizierung des [Ressourcenbesitzers](#resource-owner) und Einholung seiner Autorisierung. Eine [Clientanwendung](#client-application) interagiert mit dem Autorisierungsserver zur Laufzeit über dessen [Autorisierungsendpunkte](#authorization-endpoint) und [Tokenendpunkte](#token-endpoint) (in Einklang mit den durch OAuth2 definierten [Autorisierungsgewährungen](#authorization-grant)).

Bei der Microsoft Identity Platform-Anwendungsintegration implementiert Microsoft Identity Platform die Autorisierungsserverrolle für Azure AD-Anwendungen und Microsoft-Dienst-APIs, z.B. [Microsoft Graph-APIs][Microsoft-Graph].

## <a name="claim"></a>Anspruch

Ein [Sicherheitstoken](#security-token) enthält Ansprüche, die Assertionen zu einer Entität (beispielsweise zu einer [Clientanwendung](#client-application) oder zu einem [Ressourcenbesitzer](#resource-owner)) für eine andere Entität (beispielsweise für den [Ressourcenserver](#resource-server)) bereitstellen. Ansprüche sind Name-Wert-Paare zur Weitergabe von Informationen über den Tokenantragsteller (beispielsweise der vom [Autorisierungsserver](#authorization-server) authentifizierte Sicherheitsprinzipal). Welche Ansprüche in einem Token enthalten sind, hängt von verschiedenen Variablen ab. Hierzu zählen unter anderem die Art des Tokens, die Art der Anmeldeinformationen für die Authentifizierung des Antragstellers und die Anwendungskonfiguration.

Weitere Details finden Sie unter [Microsoft Identity Platform – Tokenreferenz][AAD-Tokens-Claims].

## <a name="client-application"></a>Clientanwendung

Gemäß Definition des [OAuth2-Autorisierungsframeworks][OAuth2-Role-Def] eine Anwendung, die im Auftrag des [Ressourcenbesitzers](#resource-owner) geschützte Ressourcen anfordert. Der Begriff „Client“ impliziert keine bestimmte Hardwareimplementierung (also beispielsweise, ob die Anwendung auf einem Server, Desktopcomputer oder anderen Gerät ausgeführt wird).

Eine Clientanwendung fordert eine [Autorisierung](#authorization) von einem Ressourcenbesitzer an, um an einer [OAuth2-Autorisierungsgewährung](#authorization-grant) teilzunehmen, und greift ggf. im Auftrag des Ressourcenbesitzers auf APIs/Daten zu. Das OAuth2-Autorisierungsframework [definiert zwei Arten von Clients][OAuth2-Client-Types]: „vertraulich“ und „öffentlich“ – abhängig davon, ob der Client die Vertraulichkeit seiner Anmeldeinformationen gewährleisten kann. Anwendungen können einen auf einem Webserver ausgeführten [Webclient (vertraulich)](#web-client), einen auf einem Gerät installierten [nativen Client (öffentlich)](#native-client) oder einen im Browser eines Geräts ausgeführten [Benutzer-Agent-basierten Client (öffentlich)](#user-agent-based-client) implementieren.

## <a name="consent"></a>Zustimmung

Der Vorgang, bei dem ein [Ressourcenbesitzer](#resource-owner) einer [Clientanwendung](#client-application) durch spezifische [Berechtigungen](#permissions) die Autorisierung für den Zugriff auf geschützte Ressourcen (im Auftrag des Ressourcenbesitzers) gewährt. Abhängig von den vom Client angeforderten Berechtigungen wird die Zustimmung eines Administrators oder Benutzers angefordert, um den Zugriff auf die entsprechenden Organisationsdaten oder individuellen Daten zuzulassen. In einem Szenario mit [mehreren Mandanten](#multi-tenant-application) wird der [Dienstprinzipal](#service-principal-object) der Anwendung auch im Mandanten des zustimmenden Benutzers erfasst.

Weitere Informationen finden Sie unter [Consent Framework](consent-framework.md).

## <a name="id-token"></a>ID-Token

Ein vom [Autorisierungsendpunkt](#authorization-endpoint) eines [Autorisierungsservers](#authorization-server) bereitgestelltes [OpenID Connect][OpenIDConnect-ID-Token]-[Sicherheitstoken](#security-token) mit [Ansprüchen](#claim) in Verbindung mit der Authentifizierung eines Endbenutzers vom Typ [Ressourcenbesitzer](#resource-owner). ID-Token werden genau wie Zugriffstoken als digital signiertes [JSON Web Token (JWT)][JWT] dargestellt. Im Gegensatz zu einem Zugriffstoken werden die Ansprüche eines ID-Tokens allerdings nicht im Zusammenhang mit dem Zugriff auf Ressourcen und speziell mit der Zugriffssteuerung verwendet.

Weitere Details finden Sie unter [Microsoft Identity Platform – Tokenreferenz][AAD-Tokens-Claims].

## <a name="microsoft-identity-platform"></a>Microsoft Identity Platform

Die Microsoft Identity Platform ist eine Weiterentwicklung des Azure AD-Identitätsdiensts (Azure Active Directory) und der zugehörigen Entwicklerplattform. Sie ermöglicht Entwicklern das Erstellen von Anwendungen, mit denen alle Microsoft-Identitäten angemeldet werden, und das Erhalten von Token zum Aufrufen von Microsoft Graph, anderen Microsoft-APIs oder von Entwicklern erstellten APIs. Es handelt sich um eine Plattform mit vollem Funktionsumfang, die einen Authentifizierungsdienst, Bibliotheken, Anwendungsregistrierung und -konfiguration, eine vollständige Entwicklerdokumentation, Codebeispiele und andere Inhalte für Entwickler umfasst. Microsoft Identity Platform unterstützt die branchenüblichen Protokolle, z.B. OAuth 2.0 und OpenID Connect. Weitere Details finden Sie unter [Informationen zu Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="multi-tenant-application"></a>Mehrinstanzenfähige Anwendung

Eine Anwendung, die Anmeldung und [Zustimmung](#consent) von Benutzern in beliebigen Azure AD-[Mandanten](#tenant) ermöglicht, auch auf anderen Mandanten als dem, auf dem der Client registriert ist. [Native Clientanwendungen](#native-client) sind standardmäßig mehrinstanzenfähig. Bei [Webclientanwendungen](#web-client) und [Webressourcen-/API-Anwendungen](#resource-server) kann es sich um Anwendungen mit einem einzelnen Mandanten oder um mehrinstanzenfähige Anwendungen handeln. Im Gegensatz dazu ermöglicht eine als einzelner Mandant registrierte Webanwendung nur Anmeldungen über Benutzerkonten, die unter dem gleichen Mandanten bereitgestellt wurden, bei dem auch die Anwendung registriert ist.

Ausführlichere Informationen finden Sie unter [Anmelden von Azure Active Directory-Benutzern (AD) mit dem mehrinstanzenfähigen Anwendungsmuster][AAD-Multi-Tenant-Overview].

## <a name="native-client"></a>Nativer Client

Eine Art von [Clientanwendung](#client-application) , die nativ auf einem Gerät installiert ist. Da der gesamte Code auf einem Gerät ausgeführt wird und keine private/vertrauliche Speicherung von Anmeldeinformationen möglich ist, wird dieser Client als „öffentlich“ betrachtet. Ausführlichere Informationen finden Sie in den [OAuth2-Clienttypen und -profilen][OAuth2-Client-Types].

## <a name="permissions"></a>Berechtigungen

Eine [Clientanwendung](#client-application) erhält Zugriff auf einen [Ressourcenserver](#resource-server), indem sie Berechtigungsanforderungen deklariert. Zwei Arten sind verfügbar:

* Delegierte Berechtigungen: Geben [bereichsbasierten](#scopes) Zugriff mit der delegierten Autorisierung des angemeldeten [Ressourcenbesitzers](#resource-owner) an und werden der Ressource zur Laufzeit als [SCP-Ansprüche](#claim) im [Zugriffstoken](#access-token) des Clients präsentiert.
* Anwendungsberechtigungen: Geben [rollenbasierten](#roles) Zugriff mit den Anmeldeinformationen und der Identität der Anwendung an und werden der Ressource zur Laufzeit als [Rollenansprüche](#claim) im Zugriffstoken des Clients präsentiert.

Darüber hinaus werden sie im Rahmen des [Zustimmungsprozesses](#consent) verwendet, um dem Administrator oder Ressourcenbesitzer die Möglichkeit zu geben, den Clientzugriff auf Ressourcen in seinem Mandanten zu gewähren oder zu verweigern.

Berechtigungsanforderungen werden im [Azure-Portal][AZURE-portal] unter „Einstellungen“ auf der Registerkarte „Anwendungen“ unter „Erforderliche Berechtigungen“ konfiguriert. Hier wählen Sie die gewünschten delegierten Berechtigungen sowie die gewünschten Anwendungsberechtigungen aus (für Letzteres ist die globale Administratorrolle erforderlich). Da ein [öffentlicher Client](#client-application) die Anmeldeinformationen nicht sicher verwalten kann, kann er nur delegierte Berechtigungen anfordern. Ein [vertraulicher Client](#client-application) kann dagegen sowohl delegierte Berechtigungen als auch Anwendungsberechtigungen anfordern. Das [Anwendungsobjekt](#application-object) des Clients speichert die deklarierten Berechtigungen in der [requiredResourceAccess-Eigenschaft][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>Ressourcenbesitzers

Gemäß Definition des [OAuth2-Autorisierungsframeworks][OAuth2-Role-Def] eine Entität, die Zugriff auf eine geschützte Ressource gewähren kann. Handelt es sich bei dem Ressourcenbesitzer um eine Person, wird er als Endbenutzer bezeichnet. Wenn also beispielsweise eine [Clientanwendung](#client-application) über die [Microsoft Graph-API][Microsoft-Graph] auf das Postfach eines Benutzers zugreifen möchte, benötigt sie die Berechtigung des Ressourcenbesitzers für das Postfach.

## <a name="resource-server"></a>Ressourcenserver

Gemäß Definition des[ OAuth2-Autorisierungsframeworks][OAuth2-Role-Def] ein Server, der geschützte Ressourcen hostet und von [Clientanwendungen](#client-application), die ein [Zugriffstoken](#access-token) vorlegen, Anforderungen für geschützte Ressourcen akzeptieren und darauf reagieren kann. Wird auch als geschützter Ressourcenserver oder als Ressourcenanwendung bezeichnet.

Ein Ressourcenserver macht APIs verfügbar und steuert den Zugriff auf seine geschützten Ressourcen über [Bereiche](#scopes) und [Rollen](#roles) (unter Verwendung des OAuth 2.0-Autorisierungsframeworks). Beispiele wären etwa die Azure AD Graph-API (bietet Zugriff auf Azure AD-Mandantendaten) und die Office 365-APIs (bieten Zugriff auf Daten wie E-Mails, Kalender und Dokumente). Beide stehen auch über die [Microsoft Graph-API][Microsoft-Graph] zur Verfügung.

Genau wie bei einer Clientanwendung wird auch die Identitätskonfiguration einer Ressourcenanwendung mittels [Registrierung](#application-registration) bei einem Azure AD-Mandanten eingerichtet und sowohl ein Anwendungs- als auch ein Dienstprinzipalobjekt bereitgestellt. Einige von Microsoft bereitgestellte APIs (etwa die Azure AD Graph-API) verfügen über vorab registrierte Dienstprinzipale, die bei der Bereitstellung in allen Mandanten verfügbar gemacht wurden.

## <a name="roles"></a>Rollen

Mithilfe von Rollen kann ein [Ressourcenserver](#resource-server) ähnlich wie mit [Bereichen](#scopes) den Zugriff auf seine geschützten Ressourcen steuern. Zwei Arten stehen zur Verfügung: Eine Benutzerrolle implementiert eine rollenbasierte Zugriffssteuerung für Benutzer/Gruppen, die Zugriff auf die Ressource benötigen. Eine Anwendungsrolle implementiert das Gleiche für [Clientanwendungen](#client-application), die Zugriff benötigen.

Bei Rollen handelt es sich um ressourcendefinierte Zeichenfolgen (wie etwa „Ausgabengenehmiger“, „Schreibgeschützt“, „Directory.ReadWrite.All“). Sie werden im [Azure-Portal][AZURE-portal] über das [Anwendungsmanifest](#application-manifest) der Ressource verwaltet und in der [appRoles-Eigenschaft][AAD-Graph-Sp-Entity] der Ressource gespeichert. Über das Azure-Portal können Benutzer zu Benutzerrollen zugewiesen und [Clientanwendungsberechtigungen](#permissions) für den Zugriff auf eine Anwendungsrolle konfiguriert werden.

Ausführliche Informationen zu den Anwendungsrollen, die von der Graph-API von Azure AD verfügbar gemacht werden, finden Sie unter [Graph-API-Berechtigungsbereiche][AAD-Graph-Perm-Scopes]. Ein Beispiel einer schrittweisen Implementierung finden Sie unter [Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen][AAD-RBAC].

## <a name="scopes"></a>Bereiche

Mithilfe von Bereichen kann ein [Ressourcenserver](#resource-server) ähnlich wie mit [Rollen](#roles) den Zugriff auf seine geschützten Ressourcen steuern. Bereiche dienen zum Implementieren einer [bereichsbasierten][OAuth2-Access-Token-Scopes] Zugriffssteuerung für eine [Clientanwendung](#client-application), der durch den Ressourcenbesitzer delegierter Zugriff auf die Ressource gewährt wurde.

Bei Bereichen handelt es sich um ressourcendefinierte Zeichenfolgen (wie etwa „Mail.Read“, „Directory.ReadWrite.All“). Sie werden im [Azure-Portal][AZURE-portal] über das [Anwendungsmanifest](#application-manifest) der Ressource verwaltet und in der [oauth2Permissions-Eigenschaft][AAD-Graph-Sp-Entity] der Ressource gespeichert. Über das Azure-Portal können zudem [delegierte Berechtigungen](#permissions) für Clientanwendungen konfiguriert werden, die Zugriff auf einen Bereich ermöglichen.

Als Benennungskonvention hat sich das Format „Ressource.Vorgang.Einschränkung“ bewährt. Ausführliche Informationen zu den Bereichen, die von der Azure AD Graph-API verfügbar gemacht werden, finden Sie unter [Graph-API-Berechtigungsbereiche][AAD-Graph-Perm-Scopes]. Informationen zu von Office 365-Diensten verfügbar gemachten Bereichen finden Sie unter [Office 365 API permissions reference][O365-Perm-Ref] (API-Berechtigungsreferenz für Office 365).

## <a name="security-token"></a>Sicherheitstoken

Ein signiertes Dokument mit Ansprüchen (etwa ein OAuth2-Token oder eine SAML 2.0-Assertion). Im Falle einer OAuth2-[Autorisierungsgewährung](#authorization-grant) handelt es sich bei einem [Zugriffstoken](#access-token) (OAuth2) und einem [ID-Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) um Arten von Sicherheitstoken, und beide werden jeweils als [JSON Web Token (JWT)][JWT] implementiert.

## <a name="service-principal-object"></a>Dienstprinzipalobjekt

Wenn Sie eine Anwendung im [Azure-Portal][AZURE-portal] registrieren bzw. aktualisieren, erstellt bzw. aktualisiert das Portal sowohl ein [Anwendungsobjekt](#application-object) als auch ein entsprechendes Dienstprinzipalobjekt für den Mandanten. Das Anwendungsobjekt *definiert* die Identitätskonfiguration der Anwendung global (also für alle Mandanten, für die der zugeordneten Anwendung Zugriff gewährt wurde) und fungiert als Vorlage, von der die entsprechenden Dienstprinzipalobjekte für die lokale Verwendung zur Laufzeit (in einem bestimmten Mandanten) *abgeleitet* werden.

Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte][AAD-App-SP-Objects].

## <a name="sign-in"></a>Anmeldung

Die Initiierung einer Endbenutzerauthentifizierung durch eine [Clientanwendung](#client-application) sowie die Erfassung des entsprechenden Zustands mit dem Ziel, ein [Sicherheitstoken](#security-token) zu beziehen und die Anwendungssitzung auf den Zustand abzustimmen. Der Zustand kann Artefakte (beispielsweise Benutzerprofilinformationen) und aus Tokenansprüchen abgeleitete Informationen enthalten.

Die Anmeldefunktion wird in der Regel zum Implementieren von Single-Sign-on (SSO) verwendet. Ihr kann auch eine Registrierungsfunktion vorangestellt werden, die als Einstiegspunkt für einen Endbenutzer dient, um ihm (bei der ersten Anmeldung) Zugriff auf eine Anwendung zu gewähren. Die Registrierungsfunktion dient zum Sammeln und Beibehalten eines zusätzlichen, benutzerspezifischen Zustands und erfordert unter Umständen die [Zustimmung des Benutzers](#consent).

## <a name="sign-out"></a>Abmeldung

Die Aufhebung der Authentifizierung eines Benutzers, wobei der Benutzerzustand, der der Sitzung der [Clientanwendung](#client-application) bei der [Anmeldung](#sign-in) zugeordnet wurde, getrennt wird.

## <a name="tenant"></a>tenant

Eine Instanz eines Azure AD-Verzeichnisses wird als Azure AD-Mandant bezeichnet. Sie bietet verschiedene Funktionen einschließlich:

* einen Registrierungsdienst für integrierte Anwendungen
* Authentifizierung von Benutzerkonten und registrierten Anwendungen
* REST-Endpunkte, die zur Unterstützung verschiedener Protokolle (einschließlich OAuth2 und SAML) erforderlich sind, z.B. der [Autorisierungsendpunkt](#authorization-endpoint), der [Tokenendpunkt](#token-endpoint) und der allgemeine, von [mehrinstanzenfähigen Anwendungen](#multi-tenant-application) verwendete Endpunkt.

Azure AD-Mandanten werden mit Azure- und Office 365-Abonnements während der Registrierung erstellt/zugewiesen und stellen Identity & Access Management-Features für das Abonnement bereit. Azure-Abonnementadministratoren können auch zusätzliche Azure AD-Mandanten über das Azure-Portal erstellen. Ausführliche Informationen zu den verschiedenen Möglichkeiten für den Zugriff auf einen Mandanten finden Sie unter [Einrichten eines Azure Active Directory-Mandanten][AAD-How-To-Tenant]. Unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory][AAD-How-Subscriptions-Assoc] erfahren Sie mehr über die Beziehung zwischen Abonnements und Azure AD-Mandanten.

## <a name="token-endpoint"></a>Tokenendpunkt

Einer der vom [Autorisierungsserver](#authorization-server) implementierten Endpunkte zur Unterstützung von OAuth2-[Autorisierungsgewährungen](#authorization-grant). Kann je nach verwendeter Gewährung zum Abrufen eines [Zugriffstokens](#access-token) (und eines zugehörigen „Aktualisierungstokens“) für einen [Client](#client-application) oder eines [ID-Tokens](#id-token) verwendet werden, wenn die Gewährung in Kombination mit dem [OpenID Connect][OpenIDConnect]-Protokoll verwendet wird.

## <a name="user-agent-based-client"></a>Benutzer-Agent-basierter Client

Ein Typ von [Clientanwendung](#client-application), der Code von einem Webserver herunterlädt und innerhalb eines Benutzer-Agents (z.B. in einem Webbrowser) ausgeführt wird. Ein Beispiel wäre eine Single-Page-Webanwendung (SPA). Da der gesamte Code auf einem Gerät ausgeführt wird und keine private/vertrauliche Speicherung von Anmeldeinformationen möglich ist, wird dieser Client als „öffentlich“ betrachtet. Ausführlichere Informationen finden Sie in den [OAuth2-Clienttypen und -profilen][OAuth2-Client-Types].

## <a name="user-principal"></a>Benutzerprinzipal

Ein Benutzerprinzipalobjekt ist (ähnlich wie ein Dienstprinzipalobjekt, das eine Anwendungsinstanz darstellt) eine weitere Art von Sicherheitsprinzipal und stellt einen Benutzer dar. Die Azure AD Graph-[Benutzerentität][AAD-Graph-User-Entity] definiert das Schema für ein Benutzerobjekt, einschließlich benutzerbezogener Eigenschaften wie Vorname und Nachname, Benutzerprinzipalname, Verzeichnisrollenmitgliedschaft usw. Dadurch kann die Konfiguration der Benutzeridentität für Azure AD bei der Ausführung einen Benutzerprinzipalnamen erstellen. Der Benutzerprinzipal stellt bei der Erfassung der Delegierung der [Zustimmung](#consent) sowie bei Zugriffssteuerungsentscheidungen und Ähnlichem einen authentifizierten Benutzer für das einmalige Anmelden dar.

## <a name="web-client"></a>Webclient

Eine Art von [Clientanwendung](#client-application) , die sämtlichen Code auf einem Webserver ausführt und als vertraulicher Client fungieren kann, da er Anmeldeinformationen sicher auf dem Server speichert. Ausführlichere Informationen finden Sie in den [OAuth2-Clienttypen und -profilen][OAuth2-Client-Types].

## <a name="next-steps"></a>Nächste Schritte

Das [Entwicklerhandbuch zu Microsoft Identity Platform][AAD-Dev-Guide] ist die Einstiegsseite für alle relevanten Themen rund um die Microsoft Identity Platform-Entwicklung. Es enthält unter anderem eine Übersicht über die [Anwendungsintegration][AAD-How-To-Integrate] sowie grundlegende Informationen zur [Authentifizierung in Microsoft Identity Platform und zu unterstützten Authentifizierungsszenarien][AAD-Auth-Scenarios]. Codebeispiele und Tutorials zur schnellen Einrichtung und Ausführung finden Sie auch auf [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Senden Sie uns im folgenden Abschnitt für Kommentare Ihr Feedback, und unterstützen Sie uns bei der Verbesserung dieser Inhalte. Dabei sind Anregungen für neue Definitionen oder zur Aktualisierung bereits vorhandener Definitionen willkommen.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
