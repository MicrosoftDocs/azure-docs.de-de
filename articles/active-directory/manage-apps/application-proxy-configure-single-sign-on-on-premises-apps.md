---
title: Einmaliges Anmelden über SAML für lokale Apps mit dem Azure AD-Anwendungsproxy
description: Es wird beschrieben, wie Sie das einmalige Anmelden für lokale Anwendungen bereitstellen, die per SAML-Authentifizierung geschützt sind. Ermöglichen Sie den Remotezugriff auf lokale Apps per Anwendungsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d51aa7e75d7e94d1c2ac66d7edb92a3ef9395b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657464"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>SAML-SSO (Single Sign-On, einmaliges Anmelden) für lokale Anwendungen mit dem Anwendungsproxy

Sie können das einmalige Anmelden (SSO) für lokale Anwendungen bereitstellen, die mit der SAML-Authentifizierung gesichert werden, und für diese Anwendungen über das Anwendungsproxy Remotezugriff gewähren. Mit SAML-SSO nimmt Azure Active Directory (Azure AD) die Authentifizierung bei der Anwendung mithilfe des Azure AD-Kontos des Benutzers vor. Azure AD gibt die Informationen für das einmalige Anmelden über ein Verbindungsprotokoll an die Anwendung weiter. Sie können Benutzer auch basierend auf Regeln, die Sie in Ihren SAML-Ansprüchen definieren, bestimmten Anwendungsrollen zuordnen. Wenn Sie zusätzlich zu SAML-SSO das Anwendungsproxy aktivieren, profitieren Ihre Benutzer vom externen Zugriff auf die Anwendung sowie von nahtlosem SSO.

Die Anwendungen müssen in der Lage sein, SAML-Token zu nutzen, die von **Azure Active Directory** ausgestellt wurden. Diese Konfiguration gilt nicht für Anwendungen, die einen lokalen Identitätsanbieter verwenden. Wir empfehlen Ihnen für solche Szenarien die Informationen unter [Ressourcen zum Migrieren von Anwendungen zu Azure Active Directory](migration-resources.md).

SAML-SSO mit dem Anwendungsproxy kann auch mit dem Feature SAML-Tokenverschlüsselung verwendet werden. Weitere Informationen finden Sie unter [Configure Azure AD SAML token encryption (Vorgehensweise: Konfigurieren der Azure AD-SAML-Tokenverschlüsselung)](howto-saml-token-encryption.md).

In den Protokolldiagrammen unten sind die SSO-Sequenzen für einen vom Dienstanbieter initiierten (SP-initiiert) und einen vom Identitätsanbieter initiierten Datenfluss (IdP-initiiert) beschrieben. Der Anwendungsproxy funktioniert mit SAML-SSO, indem die SAML-Anforderung und -Antwort für die lokale Anwendung zwischengespeichert wird.

  ![Das Diagramm zeigt die Interaktionen von Anwendung, Anwendungsproxy, Client und Azure AD beim SP-initiierten einmaligen Anmelden.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![Das Diagramm zeigt die Interaktionen von Anwendung, Anwendungsproxy, Client und Azure AD beim IDP-initiierten einmaligen Anmelden.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>Erstellen einer Anwendung und Einrichten von SAML-SSO

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory > Unternehmensanwendungen**, und wählen Sie die Option **Neue Anwendung**.

2. Geben Sie den Anzeigenamen für die neue Anwendung ein, wählen Sie **Beliebige andere, nicht im Katalog zu findende Anwendung integrieren** und dann **Erstellen** aus.

3. Wählen Sie auf der **Übersichtsseite** der App die Option **Einmaliges Anmelden**.

4. Wählen Sie als SSO-Methode die Option **SAML** aus.

5. Richten Sie zunächst das SAML-basierte einmalige Anmelden, das im Unternehmensnetzwerk verwendet werden soll. Informationen zum Konfigurieren der SAML-basierten Authentifizierung für die Anwendung finden Sie unter „Grundlegende SAML-Konfiguration“ im Abschnitt [Konfigurieren des SAML-basierten einmaligen Anmeldens](configure-saml-single-sign-on.md).

6. Fügen Sie der Anwendung mindestens einen Benutzer hinzu, und stellen Sie sicher, dass das Testkonto Zugriff auf die Anwendung hat. Verwenden Sie bei bestehender Verbindung mit dem Unternehmensnetzwerk das Testkonto, um zu ermitteln, ob Sie über einmaliges Anmelden für die Anwendung verfügen. 

   > [!NOTE]
   > Nachdem Sie den Anwendungsproxy eingerichtet haben, wechseln Sie zurück und aktualisieren die **Antwort-URL** für SAML.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>Veröffentlichen der lokalen Anwendung mit dem Anwendungsproxy

Bevor Sie SSO für lokale Anwendungen bereitstellen können, müssen Sie den Anwendungsproxy aktivieren und einen Connector installieren. Im Tutorial [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md) wird beschrieben, wie Sie Ihre lokale Umgebung vorbereiten, einen Connector installieren und registrieren und diesen dann testen. Führen Sie anschließend diese Schritte aus, um Ihre neue Anwendung mit dem Anwendungsproxy zu veröffentlichen. Andere Einstellungen, die unten nicht beschrieben sind, finden Sie im Abschnitt [Hinzufügen einer lokalen App zu Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).

1. Wählen Sie bei geöffneter Anwendung im Azure-Portal die Option **Anwendungsproxy**. Geben Sie die **interne URL** für die Anwendung an. Bei Verwendung einer benutzerdefinierten Domäne müssen Sie auch das TLS/SSL-Zertifikat für Ihre Anwendung hochladen. 
   > [!NOTE]
   > Es hat sich bewährt, für eine optimierte Benutzerfreundlichkeit nach Möglichkeit benutzerdefinierte Domänen zu verwenden. Weitere Informationen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).

2. Wählen Sie **Azure Active Directory** als Methode für die **Vorauthentifizierung** Ihrer Anwendung.

3. Kopieren Sie die **externe URL** für die Anwendung. Sie benötigen diese URL, um die SAML-Konfiguration durchzuführen.

4. Versuchen Sie mit dem Testkonto, die Anwendung mit der **externen URL** zu öffnen. So können Sie überprüfen, ob der Anwendungsproxy richtig eingerichtet ist. Bei Problemen helfen Ihnen die Informationen unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md) weiter.

## <a name="update-the-saml-configuration"></a>Aktualisieren der SAML-Konfiguration

1. Wählen Sie bei geöffneter Anwendung im Azure-Portal die Option **Einmaliges Anmelden**. 

2. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** zur Überschrift **Grundlegende SAML-Konfiguration**, und wählen Sie das Symbol **Bearbeiten** (Stift). Stellen Sie sicher, dass die **Externe URL**, die Sie im Anwendungsproxy konfiguriert haben, in die Felder **Bezeichner**, **Antwort-URL** und **Abmelde-URL** eingetragen wird. Diese URLs sind erforderlich, damit der Anwendungsproxy richtig funktioniert. 

3. Bearbeiten Sie die zuvor konfigurierte **Antwort-URL**, damit die zugehörige Domäne im Internet über den Anwendungsproxy erreichbar ist. Wenn beispielsweise Ihre **externe URL**`https://contosotravel-f128.msappproxy.net` lautet und `https://contosotravel.com/acs` die ursprüngliche **Antwort-URL** war, müssen Sie die ursprüngliche **Antwort-URL** auf `https://contosotravel-f128.msappproxy.net/acs` aktualisieren.

    ![Eingabe der SAML-Basiskonfigurationsdaten](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. Aktualisieren Sie das Kontrollkästchen neben der aktualisierten **Antwort-URL**, um sie als Standardwert zu kennzeichnen.

   * Nachdem Sie die erforderliche **Antwort-URL** als Standard markiert haben, können Sie auch die zuvor konfigurierte **Antwort-URL** löschen, welche die interne URL verwendet hat.

   * Stellen Sie bei einem SP-initiierten Datenfluss sicher, dass in der Back-End-Anwendung die richtige **Antwort-URL** oder Assertionsverbraucherdienst-URL für den Empfang des Authentifizierungstokens angegeben ist.

    > [!NOTE]
    > Wenn die Back-End-Anwendung erwartet, dass es sich bei der **Antwort-URL** um die interne URL handelt, müssen Sie entweder [benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md) verwenden, um übereinstimmende interne und externe URLs zu erhalten, oder Sie müssen auf den Geräten der Benutzer die Erweiterung zur sicheren Anmeldung bei „Meine Apps“ installieren. Diese Erweiterung leitet automatisch zum geeigneten Anwendungsproxydienst weiter. Informationen zur Installation der Erweiterung finden Sie unter [Download and install the My Apps Secure Sign-in Extension (Herunterladen und Installieren der Erweiterung zur sicheren Anmeldung bei „Meine Apps“)](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension).
    
## <a name="test-your-app"></a>Testen Ihrer App

Nachdem Sie alle Schritte abgeschlossen haben, sollte Ihre App betriebsbereit sein. So testen Sie die App:

1. Öffnen Sie einen Browser, und navigieren Sie zur **externen URL**, die Sie beim Veröffentlichen der App erstellt haben. 
1. Melden Sie sich mit dem Testkonto an, das Sie der App zugewiesen haben. Es sollte möglich sein, dass Sie die Anwendung laden und sich per einmaligem Anmelden bei der Anwendung anmelden.

## <a name="next-steps"></a>Nächste Schritte

- [Wie stellt der Azure AD-Anwendungsproxy das einmalige Anmelden bereit?](./what-is-single-sign-on.md)
- [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md)