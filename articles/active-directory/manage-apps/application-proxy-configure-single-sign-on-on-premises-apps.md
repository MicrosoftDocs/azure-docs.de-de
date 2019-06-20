---
title: SAML-SSO (einmaliges Anmelden) für lokale Anwendungen mit dem Azure Active Directory-Anwendungsproxy (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das einmalige Anmelden für lokale Anwendungen bereitstellen, die über den Anwendungsproxy veröffentlicht und mit der SAML-Authentifizierung gesichert werden.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907cb598d708bfa26f53d2e43fef5456258c21b1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393042"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML-SSO (einmaliges Anmelden) für lokale Anwendungen mit dem Anwendungsproxy (Vorschauversion)

Sie können das einmalige Anmelden (SSO) für lokale Anwendungen bereitstellen, die mit der SAML-Authentifizierung gesichert werden, und für diese Anwendungen über das Anwendungsproxy Remotezugriff gewähren. Mit SAML-SSO nimmt Azure Active Directory (Azure AD) die Authentifizierung bei der Anwendung mithilfe des Azure AD-Kontos des Benutzers vor. Azure AD gibt die Informationen für das einmalige Anmelden über ein Verbindungsprotokoll an die Anwendung weiter. Sie können Benutzer auch basierend auf Regeln, die Sie in Ihren SAML-Ansprüchen definieren, bestimmten Anwendungsrollen zuordnen. Wenn Sie zusätzlich zu SAML-SSO das Anwendungsproxy aktivieren, profitieren Ihre Benutzer vom externen Zugriff auf die Anwendung sowie von nahtlosem SSO.

Die Anwendungen müssen in der Lage sein, SAML-Token zu nutzen, die von **Azure Active Directory** ausgestellt wurden. Diese Konfiguration gilt nicht für Anwendungen, die einen lokalen Identitätsanbieter verwenden. Wir empfehlen Ihnen für solche Szenarien die Informationen unter [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](migration-resources.md).

SAML-SSO mit dem Anwendungsproxy kann auch mit dem Feature SAML-Tokenverschlüsselung verwendet werden. Weitere Informationen finden Sie unter [Configure Azure AD SAML token encryption (Vorgehensweise: Konfigurieren der Azure AD-SAML-Tokenverschlüsselung)](howto-saml-token-encryption.md).

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Veröffentlichen der lokalen Anwendung mit dem Anwendungsproxy

Bevor Sie SSO für lokale Anwendungen bereitstellen können, stellen Sie sicher, dass das Anwendungsproxy aktiviert ist und Sie über einen installierten Connector verfügen. Die Vorgehensweise hierzu wird unter [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md) erläutert.

Bedenken Sie bei der Ausführung des Tutorials Folgendes:

* Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen im Tutorial. Wählen Sie **Azure Active Directory** als **Vorauthentifizierungsmethode** für Ihre Anwendung aus (Schritt 4 des Abschnitts [Hinzufügen einer lokalen App zu Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* Kopieren Sie die **externe URL** für die Anwendung.
* Es hat sich bewährt, für eine optimierte Benutzerfreundlichkeit nach Möglichkeit benutzerdefinierte Domänen zu verwenden. Weitere Informationen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).
* Fügen Sie mindestens einen Benutzer zur Anwendung hinzu, und stellen Sie sicher, dass das Testkonto Zugriff auf die lokale Anwendung hat. Testen Sie, ob das Testkonto Zugriff auf die Anwendung hat, indem Sie die **externe URL** aufrufen und überprüfen, ob der Anwendungsproxy ordnungsgemäß eingerichtet ist. Informationen zur Problembehandlung finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md).

## <a name="set-up-saml-sso"></a>Einrichten von SAML-SSO

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory > Unternehmensanwendungen**, und wählen Sie die Anwendung aus der Liste aus.
1. Wählen Sie auf der **Übersichtsseite** der App die Option **Einmaliges Anmelden** aus.
1. Wählen Sie als SSO-Methode die Option **SAML** aus.
1. Bearbeiten Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Daten zu **Grundlegende SAML-Konfiguration**, und befolgen Sie die Anweisungen unter [Eingabe der SAML-Basiskonfiguration](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on), um die SAML-basierte Authentifizierung für die Anwendung zu konfigurieren.

   * Stellen Sie sicher, dass die **Antwort-URL** mit der **externen URL** für die lokale Anwendung übereinstimmt, die Sie über den Anwendungsproxy veröffentlicht haben, oder dass es sich bei der Antwort-URL um ein Pfad unter der **externen URL** handelt.
   * Bei einem IDP-initiierten Flow, in dem Ihre Anwendung eine andere **Antwort-URL** für die SAML-Konfiguration erfordert, fügen Sie diese URL als **zusätzliche** URL zur Liste hinzu, und aktivieren Sie das Kontrollkästchen daneben, um diese URL als primäre **Antwort-URL** festzulegen.
   * Stellen Sie bei einem SP-initiierten Flow sicher, dass in der Back-End-Anwendung die richtige **Antwort-URL** oder Assertionsverbraucherdienst-URL für den Empfang des Authentifizierungstokens angegeben ist.

     ![Eingabe der SAML-Basiskonfigurationsdaten](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > Wenn die Back-End-Anwendung erwartet, dass es sich bei der **Antwort-URL** um die interne URL handelt, müssen Sie entweder [benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md) verwenden, um übereinstimmende interne und externe URLs zu erhalten, oder Sie müssen auf den Geräten der Benutzer die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ installieren. Diese Erweiterung leitet automatisch zum geeigneten Anwendungsproxydienst weiter. Informationen zur Installation der Erweiterung finden Sie unter [Download and install the My Apps Secure Sign-in Extension (Herunterladen und Installieren der Erweiterung zur sicheren Anmeldung bei „Meine Apps“)](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).

## <a name="test-your-app"></a>Testen Ihrer App

Nachdem Sie alle Schritte abgeschlossen haben, sollte Ihre App betriebsbereit sein. So testen Sie die App:

1. Öffnen Sie einen Browser, und navigieren Sie zur externen URL, die Sie erstellt haben, als Sie die App veröffentlicht haben. 
1. Melden Sie sich mit dem Testkonto an, das Sie der App zugewiesen haben. Es sollte möglich sein, dass Sie die Anwendung laden und sich per einmaligem Anmelden bei der Anwendung anmelden.

## <a name="next-steps"></a>Nächste Schritte

- [Wie stellt der Azure AD-Anwendungsproxy das einmalige Anmelden bereit?](application-proxy-single-sign-on.md)
- [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)
