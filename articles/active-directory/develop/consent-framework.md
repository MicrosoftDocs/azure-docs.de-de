---
title: Azure Active Directory-Zustimmungsframework
description: Hier erfahren Sie mehr über das Zustimmungsframework in Azure Active Directory und darüber, wie es die Entwicklung von mehrinstanzenfähigen Webanwendungen sowie nativen Clientanwendungen vereinfacht.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edbf5548f5e230986f0a1786d67fb4580e574e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235442"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory-Zustimmungsframework

Das Azure Active Directory-Zustimmungsframework (Azure AD) vereinfacht die Entwicklung von mehrinstanzenfähigen Webanwendungen und nativen Clientanwendungen. Diese Anwendungen ermöglichen die Anmeldung mit Benutzerkonten über einen Azure AD-Mandanten, der sich von dem Mandanten unterscheidet, unter dem die Anwendung registriert ist. Außerdem kann der Zugriff auf Web-APIs, etwa die Microsoft Graph-API (zum Zugreifen auf Azure AD, Intune und Office 365-Dienste), und andere APIs von Microsoft-Diensten zusätzlich zu Ihren eigenen Web-APIs erforderlich sein.

Das Framework basiert darauf, dass Benutzer oder Administratoren ihre Zustimmung zur Registrierung einer Anwendung in ihrem Verzeichnis erteilen. Diese Zustimmung kann auch den Zugriff auf Verzeichnisdaten umfassen. Wenn beispielsweise eine Webclientanwendung Kalenderinformationen über einen Benutzer aus Office 365 lesen muss, muss dieser Benutzer der Clientanwendung zuerst seine Zustimmung erteilen. Anschließend kann die Clientanwendung die Microsoft Graph-API im Namen des Benutzers aufrufen und die Kalenderinformationen nach Bedarf verwenden. Die [Microsoft Graph-API](https://developer.microsoft.com/graph) ermöglicht den Zugriff auf Daten in Office 365 (z.B. Kalender und Nachrichten aus Exchange, Websites und Listen aus SharePoint, Dokumente aus OneDrive, OneNote-Notizbücher, Aufgaben aus dem Planer, Arbeitsmappen aus Excel usw.) sowie auf Benutzer und Gruppen von Azure AD und andere Datenobjekte aus weiteren Microsoft Cloud Services.

Das Consent Framework basiert auf OAuth 2.0 und seinen verschiedenen Datenflüssen, z. B. Authorization Code Grant und Client Credentials Grant. Dabei kommen öffentliche oder vertrauliche Clients zum Einsatz. Durch die Verwendung von OAuth 2.0 ermöglicht Azure AD die Entwicklung zahlreicher verschiedener Typen von Clientanwendungen, beispielsweise für Telefon, Tablet, Server oder Web, und ermöglicht den Zugriff auf die erforderlichen Ressourcen.

Weitere Informationen zur Verwendung des Zustimmungsframeworks mit OAuth2.0-Autorisierungsgewährungen finden Sie unter [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](v1-protocols-oauth-code.md) und [Authentifizierungsszenarien für Azure AD](authentication-scenarios.md). Informationen dazu, wie Sie autorisierten Zugriff auf Office 365 über Microsoft Graph erhalten, finden Sie auf der Seite zur [App-Authentifizierung mit Microsoft Graph](https://developer.microsoft.com/graph/docs/authorization/auth_overview).

## <a name="consent-experience---an-example"></a>Oberfläche für die Zustimmung: ein Beispiel

Die folgenden Schritte zeigen, wie das Zustimmungsframework auf der Benutzeroberfläche für den Anwendungsentwickler und für den Benutzer umgesetzt wird.

1. Angenommen, Sie verfügen über eine Webclientanwendung, die für den Zugriff auf eine Ressource oder API bestimmte Berechtigungen anfordern muss. Im nächsten Abschnitt wird beschrieben, wie Sie diese Konfiguration durchführen. Im Wesentlichen wird aber das Azure-Portal verwendet, um zur Konfigurationszeit Berechtigungsanforderungen zu deklarieren. Wie andere Konfigurationseinstellungen auch, werden diese Teil der Azure AD-Registrierung der Anwendung:

    ![Berechtigungen für andere Anwendungen](./media/consent-framework/permissions.png)

1. Angenommen, die Berechtigungen für Ihre Anwendung wurden aktualisiert, die Anwendung wird ausgeführt, und ein Benutzer möchte die Anwendung zum ersten Mal verwenden. Zuerst muss die Anwendung vom `/authorize`-Endpunkt von Azure AD einen Autorisierungscode erhalten. Der Autorisierungscode kann dann genutzt werden, um ein neues Zugriffs- und Aktualisierungstoken zu beschaffen.

1. Wenn der Benutzer noch nicht authentifiziert ist, wird er vom `/authorize`-Endpunkt von Azure AD zum Anmelden aufgefordert.

    ![Benutzer- oder Administratoranmeldung bei Azure AD](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. Nachdem der Benutzer sich angemeldet hat, ermittelt Azure AD, ob für den Benutzer eine Seite für die Zustimmungserteilung angezeigt werden muss. Das Ergebnis dieser Ermittlung ist davon abhängig, ob der Benutzer (oder der Administrator seiner Organisation) die Anwendungszustimmung bereits erteilt hat. Wenn die Zustimmung noch nicht erteilt wurde, fordert Azure AD den Benutzer zur Zustimmung auf und zeigt an, welche Berechtigungen für die Funktionalität benötigt werden. Der Berechtigungssatz, der im Dialogfeld für die Zustimmung angezeigt wird, ist identisch mit der Auswahl unter **Delegierte Berechtigungen** im Azure-Portal.

    ![Oberfläche für die Benutzerzustimmung](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. Nachdem der Benutzer seine Zustimmung erteilt hat, wird ein Autorisierungscode an Ihre Anwendung zurückgegeben, mit dem ein Zugriffs- und Aktualisierungstoken abgerufen wird. Weitere Informationen zu diesem Ablauf finden Sie unter [Web API](web-api.md) (Web-API).

1. Als Administrator können Sie auch für alle Benutzer in Ihrem Mandanten den delegierten Berechtigungen einer Anwendung zustimmen. Durch die Administratorzustimmung wird verhindert, dass das Zustimmungsdialogfeld für jeden Benutzer im Mandanten angezeigt wird. Dies kann im [Azure-Portal](https://portal.azure.com) von Benutzern durchgeführt werden, die über die Administratorrolle verfügen. Um zu erfahren, welche Administratorrollen delegierten Berechtigungen zustimmen können, lesen Sie [Berechtigungen der Administratorrolle in Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

    **So gewähren Sie delegierte App-Berechtigungen**

   1. Rufen Sie für Ihre Anwendung die Seite **API-Berechtigungen** auf.
   1. Klicken Sie auf die Schaltfläche **Administratoreinwilligung gewähren**.

      ![Erteilen von Berechtigungen für explizite Administratorzustimmung](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Das explizite Gewähren der Zustimmung über die Schaltfläche **Berechtigungen erteilen** ist derzeit für Single-Page-Webanwendungen (Single-Page Applications, SPAs) erforderlich, die „ADAL.js“ nutzen. Andernfalls tritt für die Anwendung ein Fehler auf, wenn das Zugriffstoken angefordert wird.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die Informationen unter [Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](howto-convert-app-to-be-multi-tenant.md).
* Ausführlichere Informationen zur [OAuth 2.0-Protokollunterstützung für Zustimmung während des Flows zum Gewähren des Autorisierungscodes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)
