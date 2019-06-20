---
title: Web-API-Apps in Azure Active Directory
description: Hier wird beschrieben, worum es sich bei Web-API-Anwendungen handelt, und Sie erfahren mehr über die Grundlagen des Protokollflusses, der Registrierung und des Tokenablaufs für diesen App-Typ.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 484e6b4c5f0e064254c957b07b8ba15ef98f2634
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545211"
---
# <a name="web-api"></a>Web-API

Web-API-Apps sind Webanwendungen, die Ressourcen von einer Web-API abrufen müssen. In diesem Szenario kann die Webanwendung für die Authentifizierung und den Aufruf der Web-API zwei Identitätstypen verwenden:

- **Anwendungsidentität:** In diesem Szenario werden OAuth 2.0-Clientanmeldeinformationen verwendet, um die Anwendung zu authentifizieren und auf die Web-API zuzugreifen. Da die Web-API bei Verwendung einer Anwendungsidentität keine Informationen zum Benutzer erhält, kann sie nur erkennen, dass sie von der Webanwendung aufgerufen wird. Wenn die Anwendung Informationen zum Benutzer erhält, werden diese über das Anwendungsprotokoll gesendet und nicht von Azure AD signiert. Die Web-API vertraut darauf, dass die Webanwendung den Benutzer authentifiziert hat. Aus diesem Grund wird dieses Modell als vertrauenswürdiges Subsystem bezeichnet.
- **Delegierte Benutzeridentität:** Dieses Szenario lässt sich auf zwei Arten realisieren: mit OpenID Connect oder mit der Gewährung über einen OAuth 2.0-Autorisierungscode und einem vertraulichen Client. Die Webanwendung ruft ein Zugriffstoken für den Benutzer ab, um gegenüber der Web-API zu belegen, dass der Benutzer erfolgreich bei der Webanwendung authentifiziert wurde und die Webanwendung für den Aufruf der Web-API eine delegierte Benutzeridentität erhalten hat. Dieses Zugriffstoken wird in der Anforderung an die Web-API gesendet, die den Benutzer autorisiert und die gewünschte Ressource zurückgibt.

Die Anwendungsidentität und die delegierte Benutzeridentität werden im folgenden Ablauf veranschaulicht. Der Hauptunterschied zwischen den beiden Typen besteht darin, dass bei der delegierten Benutzeridentität zuerst ein Autorisierungscode abgerufen werden muss, damit sich der Benutzer anmelden und auf die Web-API zugreifen kann.

## <a name="diagram"></a>Diagramm

![Webanwendung zu Web-API – Diagramm](./media/authentication-scenarios/web_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokollfluss

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen

1. Ein Benutzer ist in der Webanwendung bei Azure AD angemeldet (siehe den Abschnitt zu **Web-Apps**).
1. Die Webanwendung muss ein Zugriffstoken abrufen, damit sie sich gegenüber der Web-API authentifizieren und die gewünschte Ressource abrufen kann. Sie sendet eine Anforderung an den Tokenendpunkt von Azure AD. Diese Anforderung enthält die Anmeldeinformationen, die Anwendungs-ID und den Anwendungs-ID-URI der Web-API.
1. Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.
1. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

### <a name="delegated-user-identity-with-openid-connect"></a>Delegierte Benutzeridentität mit OpenID Connect

1. Ein Benutzer ist mit Azure AD bei einer Webanwendung angemeldet (siehe „Webbrowser zu Webanwendung“ im Abschnitt weiter oben). Wenn der Benutzer der Webanwendung noch keine Zustimmung für den Aufruf der Web-API erteilt hat, muss er diesen Schritt noch durchführen. Die Anwendung zeigt die erforderlichen Berechtigungen an. Handelt es sich bei einer dieser Berechtigungen um eine Berechtigung auf Administratorebene, kann die Zustimmung nicht durch einen normalen Benutzer im Verzeichnis erteilt werden. Dieser Zustimmungsprozess gilt nur für mehrinstanzenfähige Anwendungen. Einzelinstanzanwendungen verfügen bereits über die erforderlichen Berechtigungen. Bei der Anmeldung des Benutzers hat die Webanwendung ein ID-Token mit Benutzerinformationen sowie einen Autorisierungscode erhalten.
1. Auf der Grundlage des Autorisierungscodes von Azure AD sendet die Webanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Anwendungs-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).
1. Der Autorisierungscode sowie die Informationen zu Webanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken.
1. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Delegierte Benutzeridentität mit OAuth 2.0-Autorisierungscode

1. Ein Benutzer ist bereits bei einer Webanwendung angemeldet, und der Authentifizierungsmechanismus ist von Azure AD unabhängig.
1. Die Webanwendung benötigt einen Autorisierungscode, um ein Zugriffstoken abzurufen. Daher sendet sie über den Browser eine Anforderung an den Autorisierungsendpunkt von Azure AD. Diese Anforderung enthält die Anwendungs-ID und den Umleitungs-URI für die Webanwendung nach erfolgreicher Authentifizierung. Der Benutzer meldet sich bei Azure AD an.
1. Wenn der Benutzer der Webanwendung noch keine Zustimmung für den Aufruf der Web-API erteilt hat, muss er diesen Schritt noch durchführen. Die Anwendung zeigt die erforderlichen Berechtigungen an. Handelt es sich bei einer dieser Berechtigungen um eine Berechtigung auf Administratorebene, kann die Zustimmung nicht durch einen normalen Benutzer im Verzeichnis erteilt werden. Diese Zustimmung gilt für einzel- und mehrinstanzenfähige Anwendungen. Bei einem einzelnen Mandanten kann ein Administrator die Zustimmung des Administrators durchführen, um im Auftrag der jeweiligen Benutzer eine Zustimmung durchzuführen. Dies ist über die Schaltfläche `Grant Permissions` im [Azure-Portal](https://portal.azure.com) möglich. 
1. Wenn der Benutzer seine Zustimmung gegeben hat, erhält die Webanwendung den Autorisierungscode, den sie zum Abrufen eines Zugriffstokens benötigt.
1. Auf der Grundlage des Autorisierungscodes von Azure AD sendet die Webanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Anwendungs-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).
1. Der Autorisierungscode sowie die Informationen zu Webanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken.
1. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

## <a name="code-samples"></a>Codebeispiele

Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Webanwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Es kommen immer wieder neue Beispiele hinzu. [Webanwendung zu Web-API](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity)

## <a name="app-registration"></a>App-Registrierung

Informationen zum Registrieren einer Anwendung beim Azure AD v1.0-Endpunkt finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md).

* Einzelinstanzanwendung: Webanwendung und Web-API müssen sowohl bei Verwendung der Anwendungsidentität als auch bei Verwendung der delegierten Benutzeridentität im gleichen Verzeichnis in Azure AD registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der Webanwendung beschränken. Bei Verwendung einer delegierten Benutzeridentität muss die Webanwendung die gewünschten Berechtigungen aus dem Dropdownmenü **Berechtigungen für andere Anwendungen** im Azure-Portal auswählen. Bei Verwendung der Anwendungsidentität ist dieser Schritt nicht erforderlich.
* Mehrinstanzenfähige Anwendung: Die Webanwendung wird so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, werden die Webanwendung und die Web-API in dessen Verzeichnis registriert.

## <a name="token-expiration"></a>Tokenablauf

Wenn die Webanwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne sich erneut anmelden zu müssen. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu anderen [Anwendungstypen und -szenarien](app-types.md)
- Weitere Informationen zu den [Authentifizierungsgrundlagen](authentication-scenarios.md) von Azure AD
