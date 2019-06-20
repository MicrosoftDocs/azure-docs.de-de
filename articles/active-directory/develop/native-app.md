---
title: Native Apps in Azure Active Directory
description: Beschreibt, worum es sich bei nativen Apps handelt, und stellt die Grundlagen des Protokollflusses, der Registrierung und des Tokenablaufs für diesen App-Typ vor.
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
ms.openlocfilehash: a6bf24124c4b072a64ef59500b2f723ff6abbb0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545842"
---
# <a name="native-apps"></a>Native Apps

Native Apps sind Anwendungen, die eine Web-API im Namen eines Benutzers aufrufen. Dieses Szenario basiert auf dem OAuth 2.0-Autorisierungscode-Gewährungstyp mit einem öffentlichen Client (siehe Abschnitt 4.1 der [OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749)). Die systemeigene Anwendung ruft unter Verwendung des OAuth 2.0-Protokolls ein Zugriffstoken für den Benutzer ab. Dieses Zugriffstoken wird dann in der Anforderung an die Web-API gesendet, die den Benutzer autorisiert und die gewünschte Ressource zurückgibt.

## <a name="diagram"></a>Diagramm

![Systemeigene Anwendung zu Web-API – Diagramm](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Protokollfluss

Bei Verwendung der AD-Authentifizierungsbibliotheken werden Ihnen die meisten der hier beschriebenen Protokolldetails abgenommen (so etwa das Browser-Popup, die Tokenzwischenspeicherung und die Behandlung von Aktualisierungstoken).

1. Über ein Browser-Popup sendet die systemeigene Anwendung eine Anforderung an den Autorisierungsendpunkt in Azure AD. Diese Anforderung enthält die Anwendungs-ID und den Umleitungs-URI der nativen Anwendung gemäß Angabe im Azure-Portal sowie den Anwendungs-ID-URI für die Web-API. Falls sich der Benutzer noch nicht angemeldet hat, wird er dazu aufgefordert.
1. Azure AD authentifiziert den Benutzer. Wenn es sich um eine mehrinstanzenfähige Anwendung handelt und für die Verwendung der Anwendung eine Zustimmung erforderlich ist, muss der Benutzer zustimmen, sofern nicht bereits geschehen. Nach der Zustimmung und einer erfolgreichen Authentifizierung gibt Azure AD eine Antwort mit einem Autorisierungscode an den Umleitungs-URI der Clientanwendung zurück.
1. Wenn Azure AD einen Autorisierungscode an den Umleitungs-URI zurückgibt, beendet die Clientanwendung die Interaktion mit dem Browser und extrahiert den Autorisierungscode aus der Antwort. Auf der Grundlage dieses Autorisierungscodes sendet die Clientanwendung eine Anforderung an den Token-Endpunkt von Azure AD. Diese Anforderung enthält den Autorisierungscode, Details zur Clientanwendung (Anwendungs-ID und Umleitungs-URI) sowie die gewünschte Ressource (Anwendungs-ID-URI für die Web-API).
1. Der Autorisierungscode sowie die Informationen zu Clientanwendung und Web-API werden von Azure AD überprüft. Bei erfolgreicher Validierung gibt Azure AD zwei Token zurück: ein JWT-Zugriffstoken und ein JWT-Aktualisierungstoken. Darüber hinaus gibt Azure AD grundlegende Benutzerinformationen wie Anzeigename und Mandanten-ID zurück.
1. Die Clientanwendung fügt der Web-API über HTTPS und unter Verwendung des zurückgegebenen JWT-Zugriffstokens die JWT-Zeichenfolge mit der Angabe „Bearer“ (Träger) im Autorisierungsheader der Anforderung hinzu. Daraufhin überprüft die Web-API das JWT-Token und gibt bei erfolgreicher Validierung die gewünschte Ressource zurück.
1. Wenn das Zugriffstoken abläuft, erhält die Clientanwendung eine Fehlermeldung mit dem Hinweis, dass sich der Benutzer erneut authentifizieren muss. Wenn die Anwendung über ein gültiges Aktualisierungstoken verfügt, kann damit ohne erneute Anmeldeaufforderung ein neues Zugriffstoken abgerufen werden. Wenn das Aktualisierungstoken abläuft, muss die Anwendung den Benutzer interaktiv neu authentifizieren.

> [!NOTE]
> Das von Azure AD ausgestellte Aktualisierungstoken kann für den Zugriff auf mehrere Ressourcen verwendet werden. Wenn Sie beispielsweise über eine Clientanwendung verfügen, die zum Aufrufen zweier Web-APIs berechtigt ist, kann mit dem Aktualisierungstoken auch ein Zugriffstoken für die andere Web-API abgerufen werden.

## <a name="code-samples"></a>Codebeispiele

Sehen Sie sich die Codebeispiele für Szenarien vom Typ „Systemeigene Anwendung zu Web-API“ an. Schauen Sie außerdem regelmäßig vorbei: Wir fügen immer wieder neue Beispiele hinzu. [Systemeigene Anwendung zu Web-API](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)

## <a name="app-registration"></a>App-Registrierung

Informationen zum Registrieren einer Anwendung beim Azure AD v1.0-Endpunkt finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md).

* Einzelinstanzenfähige Anwendung: Sowohl die native Anwendung als auch die Web-API müssen in Azure AD im gleichen Verzeichnis registriert werden. Die Web-API kann so konfiguriert werden, dass sie einen Satz von Berechtigungen verfügbar macht, die den Ressourcenzugriff der systemeigenen Anwendung beschränken. Die Clientanwendung wählt daraufhin die gewünschten Berechtigungen aus dem Dropdownmenü „Berechtigungen für andere Anwendungen“ des Azure-Portals aus.
* Mehrinstanzenfähige Anwendung: Die native Anwendung wird zum einen immer nur im Verzeichnis des Entwicklers oder des Herausgebers registriert. Zum anderen ist die systemeigene Anwendung so konfiguriert, dass sie die Berechtigungen angibt, die für eine ordnungsgemäße Verwendung erforderlich sind. Die Liste mit den erforderlichen Berechtigungen wird in einem Dialogfeld angezeigt, wenn ein Benutzer oder Administrator im Zielverzeichnis der Anwendung zustimmt. Dadurch wird die Anwendung in ihrer Organisation verfügbar. Einige Anwendungen benötigen nur Berechtigungen auf Benutzerebene. Diesen kann jeder Benutzer in der Organisation zustimmen. Andere Anwendungen benötigen Berechtigungen auf Administratorebene. Diesen kann ein Benutzer in der Organisation nicht zustimmen. Nur ein Verzeichnisadministrator kann seine Zustimmung für Anwendungen geben, die diese Berechtigungsebene erfordern. Wenn der Benutzer oder Administrator seine Zustimmung gibt, wird nur die Web-API in seinem Verzeichnis registriert. 

## <a name="token-expiration"></a>Tokenablauf

Wenn die systemeigene Anwendung mithilfe ihres Autorisierungscodes ein JWT-Zugriffstoken abruft, erhält sie auch ein JWT-Aktualisierungstoken. Dank des Aktualisierungstokens kann der Benutzer bei Ablauf des Zugriffstokens erneut authentifiziert werden, ohne sich erneut anmelden zu müssen. Das Aktualisierungstoken wird dann zum Authentifizieren des Benutzers verwendet, und ein neues Zugriffstoken sowie ein neues Aktualisierungstoken werden erstellt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu anderen [Anwendungstypen und -szenarien](app-types.md)
- Weitere Informationen zu den [Authentifizierungsgrundlagen](authentication-scenarios.md) von Azure AD
