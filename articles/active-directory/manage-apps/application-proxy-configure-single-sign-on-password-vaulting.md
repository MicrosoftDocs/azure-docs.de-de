---
title: Einmaliges Anmelden bei Apps mit dem Azure AD-Anwendungsproxy | Microsoft-Dokumentation
description: Aktivieren Sie das einmalige Anmelden für Ihre veröffentlichten lokalen Anwendungen mit dem Azure AD-Anwendungsproxy im Azure-Portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c19550adf500ba91462af12b4c5f7f5e38240e67
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783518"
---
# <a name="password-vaulting-for-single-sign-on-with-application-proxy"></a>Kennworttresore (Password Vaulting) für einmaliges Anmelden mit Anwendungsproxy

Mit dem Azure Active Directory-Anwendungsproxy können Sie die Produktivität steigern, indem Sie lokale Anwendungen so veröffentlichen, dass auch Remotemitarbeiter sicher darauf zugreifen können. Im Azure-Portal können Sie auch einmaliges Anmelden (Single Sign-On, SSO) für diese Apps einrichten. Ihre Benutzer müssen sich nur bei Azure AD authentifizieren. Dann können sie auf die Unternehmensanwendung zugreifen, ohne sich erneut anzumelden.

Der Anwendungsproxy unterstützt mehrere [Modi für einmaliges Anmelden](what-is-single-sign-on.md#choosing-a-single-sign-on-method). Die kennwortbasierte Anmeldung ist für Anwendungen bestimmt, bei denen für die Authentifizierung eine Kombination aus Benutzername und Kennwort verwendet wird. Wenn Sie die kennwortbasierte Anmeldung für Ihre Anwendung konfigurieren, müssen sich Ihre Benutzer nur einmal an der lokalen Anwendung anmelden. Danach speichert Azure Active Directory die Anmeldeinformationen und stellt sie automatisch für die Anwendung bereit, wenn Ihre Benutzer von einem Remotestandort aus darauf zugreifen. 

Sie sollten Ihre App bereits mit dem Anwendungsproxy veröffentlicht und getestet haben. Führen Sie andernfalls die Schritte unter [Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md) aus, und fahren Sie dann hier fort. 

## <a name="set-up-password-vaulting-for-your-application"></a>Einrichten von Kennworttresoren für Ihre Anwendung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen** aus.
3. Wählen Sie in der Liste die App aus, für die SSO einrichten möchten.  
4. Wählen Sie **Einmaliges Anmelden**.

   ![„Einmaliges Anmelden“ auswählen](./media/application-proxy-configure-single-sign-on-password-vaulting/select-sso.png)

5. Wählen Sie als SSO-Modus **Kennwortbasierte Anmeldung** aus.
6. Geben Sie als Anmelde-URL die URL der Seite ein, auf der die Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung bei Ihrer App von außerhalb des Unternehmensnetzwerks eingeben. Dies kann die externe URL sein, die Sie erstellt haben, als Sie die App über den Anwendungsproxy veröffentlicht haben. 

   ![Kennwortbasierte Anmeldung auswählen und URL eingeben](./media/application-proxy-configure-single-sign-on-password-vaulting/password-sso.png)

7. Wählen Sie **Speichern** aus.

<!-- Need to repro?
7. The page should tell you that a sign-in form was successfully detected at the provided URL. If it doesn't, select **Configure [your app name] Password Single Sign-on Settings** and choose **Manually detect sign-in fields**. Follow the instructions to point out where the sign-in credentials go. 
-->

## <a name="test-your-app"></a>Testen Ihrer App

Navigieren Sie zu der externen URL, die Sie für den Remotezugriff auf Ihre Anwendung konfiguriert haben. Melden Sie sich mit Ihren Anmeldeinformationen für die App an (oder mit den Anmeldeinformationen für ein Testkonto, für das Sie den Zugriff eingerichtet haben). Nachdem Sie sich erfolgreich angemeldet haben, sollten Sie die App verlassen und wieder aufrufen können, ohne Ihre Anmeldeinformationen erneut eingeben zu müssen. 

## <a name="next-steps"></a>Nächste Schritte

- Lernen Sie andere Möglichkeiten zum Implementieren des [einmaligen Anmeldens](what-is-single-sign-on.md) kennen.
- Informieren Sie sich über die [Sicherheitsaspekte beim Remotezugriff auf Apps mit dem Azure AD-Anwendungsproxy](application-proxy-security.md).
