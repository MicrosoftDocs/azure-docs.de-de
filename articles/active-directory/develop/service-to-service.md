---
title: Dienst-zu-Dienst-Apps in Azure Active Directory
description: Hier wird beschrieben, worum es sich bei Dienst-zu-Dienst-Anwendungen handelt, und Sie erfahren mehr über die Grundlagen des Protokollflusses, der Registrierung und des Tokenablaufs für diesen App-Typ.
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
ms.openlocfilehash: 683664b3172cb12ba6adf6c8006e9685a6d1ec35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540326"
---
# <a name="service-to-service-apps"></a>Dienst-zu-Dienst-Apps

Dienst-zu-Dienst-Anwendungen können eine Daemon- oder Serveranwendung sein, die Ressourcen von einer Web-API abruft. Der Abschnitt beinhaltet zwei untergeordnete Szenarien:

- Eine Daemon-Anwendung, die auf der Grundlage der Gewährung von OAuth 2.0-Clientanmeldeinformationen eine Web-API aufruft

    In diesem Szenario ist es wichtig, einige Dinge zu verstehen. Da mit einer Daemon-Anwendung keine Benutzerinteraktion möglich ist, benötigt die Anwendung ihre eigene Identität. Ein Beispiel für eine Daemon-Anwendung wäre ein Batchauftrag oder ein im Hintergrund ausgeführter Betriebssystemdienst. Diese Art von Anwendung fordert auf der Grundlage ihrer Anwendungsidentität ein Zugriffstoken an und gibt dabei gegenüber Azure AD ihre Anwendungs-ID, ihre Anmeldeinformationen (Kennwort oder Zertifikat) sowie ihren Anwendungs-ID-URI an. Nach erfolgreicher Authentifizierung erhält die Daemon-Anwendung ein Zugriffstoken von Azure AD, mit dem sie dann die Web-API aufrufen kann.

- Eine Serveranwendung (z. B. eine Web-API), die auf der Grundlage der OAuth 2.0-Entwurfsspezifikation „Im Auftrag von“ eine Web-API aufruft.

    In diesem Szenario wird davon ausgegangen, dass sich ein Benutzer bei einer systemeigenen Anwendung authentifiziert hat und diese native Anwendung eine Web-API aufrufen muss. Azure AD stellt ein JWT-Zugriffstoken für den Web-API-Aufruf aus. Wenn die Web-API ihrerseits eine nachgelagerte Web-API aufrufen muss, kann sie den On-Behalf-Of-Fluss verwenden, um die Identität des Benutzers zu delegieren und sich gegenüber der Web-API der zweiten Ebene zu authentifizieren.

## <a name="diagram"></a>Diagramm

![Daemon- oder Serveranwendung zu Web-API – Diagramm](./media/authentication-scenarios/daemon_server_app_to_web_api.png)

## <a name="dprotocol-flow"></a>DProtocol-Fluss

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Anwendungsidentität mit OAuth 2.0-Clientanmeldeinformationen

1. Zuerst muss sich die Serveranwendung mit Azure AD ohne Benutzerinteraktion (also beispielsweise ohne interaktives Anmeldedialogfeld) als sie selbst authentifizieren. Sie sendet eine Anfrage an den Token-Endpunkt von Azure AD. Diese Anfrage enthält die Anmeldeinformationen, die Anwendungs-ID und den Anwendungs-ID-URI.
1. Azure AD authentifiziert die Anwendung und gibt ein JWT-Zugriffstoken zurück, das zum Aufrufen der Web-API verwendet wird.
1. Die Webanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Delegierte Benutzeridentität mit OAuth 2.0-Entwurfsspezifikation „On-Behalf-Of“

Im folgenden Ablauf wird davon ausgegangen, dass ein Benutzer in einer anderen Anwendung (beispielsweise in einer systemeigenen Anwendung) authentifiziert und auf der Grundlage der Benutzeridentität ein Zugriffstoken für die Web-API der ersten Ebene abgerufen wurde.

1. Die systemeigene Anwendung sendet das Zugriffstoken an die Web-API der ersten Ebene.
1. Die Web-API der ersten Ebene sendet eine Anforderung an den Token-Endpunkt von Azure AD. Diese enthält die Anwendungs-ID und die Anmeldeinformationen sowie das Zugriffstoken des Benutzers. Die Anforderung wird zudem mit einem Parameter vom Typ „on_behalf_of“ versehen, der angibt, dass die Web-API neue Token anfordert, um im Auftrag des ursprünglichen Benutzers eine nachgelagerte Web-API aufzurufen.
1. Azure AD überprüft, ob die Web-API der ersten Ebene über Zugriffsberechtigungen für die Web-API der zweiten Ebene verfügt, und validiert die Anforderung. Dabei werden an die Web-API der ersten Ebene ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken zurückgegeben.
1. Anschließend ruft die Web-API der ersten Ebene per HTTPS die Web-API der zweiten Ebene auf, indem sie die Tokenzeichenfolge an den Autorisierungsheader der Anforderung anfügt. Die Web-API der ersten Ebene kann die Web-API der zweiten Ebene aufrufen, solange das Zugriffstoken und die Aktualisierungstoken gültig sind.

## <a name="code-samples"></a>Codebeispiele

Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Daemon- oder Serveranwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Es kommen immer wieder neue Beispiele hinzu. [Server- oder Daemon-Anwendung zu Web-API](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>App-Registrierung

* Einzelinstanzanwendung: Die Daemon- oder Serveranwendung muss sowohl bei Verwendung der Anwendungsidentität als auch bei Verwendung der delegierten Benutzeridentität im gleichen Verzeichnis in Azure AD registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der Daemon- oder Serveranwendung beschränken. Bei Verwendung einer delegierten Benutzeridentität muss die Serveranwendung die gewünschten Berechtigungen aus dem Dropdownmenü „Berechtigungen für andere Anwendungen“ des Azure-Portals auswählen. Bei Verwendung der Anwendungsidentität ist dieser Schritt nicht erforderlich.
* Mehrinstanzenfähige Anwendung: Die Daemon- oder Serveranwendung wird so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, werden die beiden Web-APIs in seinem Verzeichnis registriert.

## <a name="token-expiration"></a>Tokenablauf

Wenn die erste Anwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne zur Eingabe seiner Anmeldeinformationen aufgefordert zu werden. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu anderen [Anwendungstypen und -szenarien](app-types.md)
- Weitere Informationen zu den [Authentifizierungsgrundlagen](authentication-scenarios.md) von Azure AD
