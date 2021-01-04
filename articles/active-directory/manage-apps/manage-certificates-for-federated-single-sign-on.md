---
title: Verwalten von Verbundzertifikaten in Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Ablaufdatum für Verbundzertifikate anpassen und Zertifikate erneuern, die in Kürze ablaufen.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87190e9a7aa3c3a26b77220dd0332d65c640bc2e
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658994"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory

In diesem Artikel werden häufig gestellte Fragen sowie Informationen zu den Zertifikaten behandelt, die Azure Active Directory (Azure AD) erstellt, um die einmalige Verbundanmeldung (Single Sign-On, SSO) für Ihre Software-as-a-Service-Anwendungen (SaaS-Anwendungen) einzurichten. Fügen Sie Anwendungen aus dem Azure AD-App-Katalog oder über Anwendungsvorlagen hinzu, die nicht aus dem Katalog stammen. Konfigurieren der Anwendung mit der Option für Verbund-SSO.

Dieser Artikel gilt nur für Apps, die für die Verwendung von Azure AD-SSO über den Verbund mit [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) konfiguriert sind.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatisch generiertes Zertifikat für Katalog- und Nicht-Kataloganwendungen

Wenn Sie eine neue Anwendung aus dem Katalog hinzufügen und eine SAML-basierte Anmeldung konfigurieren (indem Sie auf der Anwendungsübersichtsseite die Optionen **Einmaliges Anmelden** > **SAML** auswählen), generiert Azure AD ein drei Jahre lang gültiges Zertifikat für die Anwendung. Wenn Sie das aktive Zertifikat als Sicherheitszertifikatsdatei (**CER-Datei**) herunterladen möchten, kehren Sie zu dieser Seite (**SAML-basierte Anmeldung**) zurück, und wählen Sie unter der Überschrift **SAML-Signaturzertifikat** einen Downloadlink aus. Sie können zwischen dem binären Zertifikat, der Option „Zertifikat (Rohdaten)“, und dem Zertifikat im Base64-codierten Textformat, der Option „Zertifikat (Base64)“, auswählen. Bei Kataloganwendungen wird in diesem Abschnitt möglicherweise auch ein Link angezeigt, über den Sie je nach Anforderung der Anwendung das Zertifikat als Verbundmetadaten-XML (**XML-Datei**) herunterladen können.

![Downloadoptionen für aktives SAML-Signaturzertifikat](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Sie können auch ein aktives oder inaktives Zertifikat herunterladen, indem Sie neben der Überschrift **SAML-Signaturzertifikat** das Symbol **Bearbeiten** (Stiftsymbol) auswählen, wodurch die Seite **SAML-Signaturzertifikat** angezeigt wird. Wählen Sie die Auslassungspunkte ( **...** ) neben dem Zertifikat aus, das Sie herunterladen möchten, und wählen Sie dann das gewünschte Zertifikatformat aus. Als zusätzliche Option können Sie das Zertifikat im Privacy Enhanced Mail-Format (PEM) herunterladen. Dieses Format ist mit Base64 identisch, weist jedoch die Dateinamenerweiterung **PEM** auf, die in Windows nicht als Zertifikatformat erkannt wird.

![Downloadoptionen für SAML-Signaturzertifikat (aktives und inaktives Zertifikat)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassen des Ablaufdatums für das Verbundzertifikat und Rollover zu einem neuen Zertifikat

Standardmäßig konfiguriert Azure ein Zertifikat so, dass es nach drei Jahren abläuft, wenn es automatisch während der SAML-SSO-Konfiguration erstellt wird. Weil Sie das Datum eines Zertifikats nach dem Speichern des Zertifikats nicht ändern können, müssen Sie die folgenden Schritte ausführen:

1. Erstellen Sie ein neues Zertifikat mit dem gewünschten Datum.
1. Speichern Sie das neue Zertifikat.
1. Laden Sie das neue Zertifikat im richtigen Format herunter.
1. Laden Sie das neue Zertifikat in die Anwendung hoch.
1. Legen Sie das neue Zertifikat im Azure Active Directory-Portal als aktiv fest.

Die zwei folgenden Abschnitte unterstützen Sie beim Ausführen dieser Schritte.

### <a name="create-a-new-certificate"></a>Erstellen eines neuen Zertifikats

Zuerst müssen Sie ein neues Zertifikat mit einem anderen Ablaufdatum erstellen und speichern:

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) an. Die Seite **Azure Active Directory Admin Center** wird angezeigt.
1. Wählen Sie im linken Bereich **Unternehmensanwendungen** aus. Eine Liste der Unternehmensanwendungen in Ihrem Konto wird angezeigt.
1. Wählen Sie die entsprechende Anwendung aus. Eine Übersichtsseite für die Anwendung wird angezeigt.
1. Wählen Sie im linken Bereich der Anwendungsübersichtsseite **Einmaliges Anmelden** aus.
1. Wenn die Seite **SSO-Methode auswählen** angezeigt wird, wählen Sie **SAML** aus.
1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten – Vorschau** zur Überschrift **SAML-Signaturzertifikat**, und wählen Sie das Symbol **Bearbeiten** (Bleistift) aus. Die Seite **SAML-Signaturzertifikat** wird geöffnet. Auf dieser Seite werden der Status (**Aktiv** oder **Inaktiv**), das Ablaufdatum und der Fingerabdruck (eine Hashzeichenfolge) für jedes Zertifikat angezeigt.
1. Wählen Sie **Neues Zertifikat** aus. Unterhalb der Zertifikatliste wird eine neue Zeile angezeigt, in der das Ablaufdatum standardmäßig auf genau drei Jahre nach dem aktuellen Datum festgelegt ist. (Da Ihre Änderungen noch nicht gespeichert wurden, können Sie das Ablaufdatum noch ändern.)
1. Zeigen Sie in der neuen Zertifikatzeile mit dem Mauszeiger auf die Spalte „Ablaufdatum“, und wählen Sie das Symbol **Datum auswählen** (einen Kalender) aus. Daraufhin wird ein Kalendersteuerelement mit den Tagen eines Monats des aktuellen Ablaufdatums in der neuen Zeile angezeigt.
1. Verwenden Sie das Kalendersteuerelement, um ein neues Datum festzulegen. Sie können ein beliebiges Datum zwischen dem aktuellen Datum und drei Jahre nach dem aktuellen Datum festlegen.
1. Wählen Sie **Speichern** aus. Das neue Zertifikat wird jetzt mit dem Status **Inaktiv**, dem von Ihnen ausgewählten Ablaufdatum und einem Fingerabdruck angezeigt.
1. Wählen Sie das **X** aus, um zur Seite **Einmaliges Anmelden (SSO) mit SAML einrichten – Vorschau** zurückzukehren.

### <a name="upload-and-activate-a-certificate"></a>Hochladen und Aktivieren eines Zertifikats

Als Nächstes müssen Sie das neue Zertifikat im richtigen Format herunterladen, in die Anwendung hochladen und in Azure Active Directory als aktiv festlegen:

1. Zeigen Sie die zusätzlichen Konfigurationsanweisungen für die SAML-basierte Anmeldung für die Anwendung an, indem Sie entweder:

   - den Link **Konfigurationshandbuch** auswählen, um die Anweisungen in einem separaten Browserfenster oder auf einer separaten Browserregisterkarte anzuzeigen, oder
   - zur Überschrift **Einrichten** wechseln und **Schrittanleitung anzeigen** auswählen, um die Anweisungen auf einer Randleiste anzuzeigen.

1. Beachten Sie in den Anweisungen das für den Zertifikatupload erforderliche Codierungsformat.
1. Befolgen Sie die weiter oben im Abschnitt [Automatisch generiertes Zertifikat für Katalog- und Nicht-Kataloganwendungen](#auto-generated-certificate-for-gallery-and-non-gallery-applications) aufgeführten Anweisungen. In diesem Schritt wird das Zertifikat in dem für den Upload durch die Anwendung erforderlichen Codierungsformat heruntergeladen.
1. Wenn Sie einen Rollover auf das neue Zertifikat ausführen möchten, wechseln Sie zurück zur Seite **SAML-Signaturzertifikat**, und wählen Sie in der neu gespeicherten Zertifikatzeile die Auslassungspunkte ( **...** ) aus, und wählen Sie dann **Zertifikat als aktiv festlegen** aus. Der Status des neuen Zertifikats ändert sich in **Aktiv**, und der Status des zuvor aktiven Zertifikats ändert sich in **Inaktiv**.
1. Fahren Sie mit den zuvor angezeigten Konfigurationsanweisungen für die SAML-basierte Anmeldung für die Anwendung fort, um das SAML-Signaturzertifikat im richtigen Codierungsformat hochzuladen.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Hinzufügen von E-Mail-Adressen für Benachrichtigungen für den Zertifikatablauf

Azure AD sendet 60, 30 und 7 Tage vor Ablauf des SAML-Zertifikats eine Benachrichtigung per E-Mail. Sie können mehrere E-Mail-Adressen für den Empfang von Benachrichtigungen hinzufügen. Gehen Sie wie folgt vor, um die E-Mail-Adressen anzugeben, an die Benachrichtigungen gesendet werden sollen:

1. Navigieren Sie auf der Seite **SAML-Signaturzertifikat** zur Überschrift **E-Mail-Adressen für Benachrichtigungen**. Für diese Überschrift wird standardmäßig nur die E-Mail-Adresse des Administrators verwendet, der die Anwendung hinzugefügt hat.
1. Geben Sie unter der letzten E-Mail-Adresse die E-Mail-Adresse ein, an die eine Benachrichtigung über den Ablauf des Zertifikats gesendet werden soll, und drücken Sie dann die EINGABETASTE.
1. Wiederholen Sie den vorherigen Schritt für jede E-Mail-Adresse, die Sie hinzufügen möchten.
1. Wählen Sie für jede zu löschende E-Mail-Adresse das Symbol **Löschen** (den Papierkorb) neben der jeweiligen E-Mail-Adresse aus.
1. Wählen Sie **Speichern** aus.

Sie erhalten die Benachrichtigungs-E-Mail von aadnotification@microsoft.com. Um zu verhindern, dass die E-Mail in Ihrem Spamordner landet, müssen Sie die E-Mail-Adresse zu Ihren Kontakten hinzufügen.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Erneuern eines in Kürze ablaufenden Zertifikats

Wenn ein Zertifikat in Kürze abläuft, können Sie es mithilfe eines Verfahrens erneuern, das zu keinen wesentlichen Ausfallzeiten für Ihre Benutzer führt. Gehen Sie wie folgt vor, um ein ablaufendes Zertifikat zu erneuern:

1. Befolgen Sie die weiter oben im Abschnitt [Erstellen eines neuen Zertifikats](#create-a-new-certificate) aufgeführten Anweisungen, und verwenden Sie dabei ein Datum, das sich mit dem des vorhandenen Zertifikats überschneidet. Das Datum beschränkt die durch den Ablauf des Zertifikats verursachten Ausfallzeiten.
1. Wenn die Anwendung einen automatischen Rollover für das Zertifikat ausführen kann, legen Sie das neue Zertifikat durch Ausführen der folgenden Schritte als aktiv fest:
   1. Wechseln Sie zurück zur Seite **SAML-Signaturzertifikat**.
   1. Wählen Sie in der neu gespeicherten Zertifikatzeile die Auslassungspunkte ( **...** ) aus, und wählen Sie dann **Zertifikat als aktiv festlegen** aus.
   1. Überspringen Sie die nächsten zwei Schritte.

1. Wenn die App jeweils nur ein Zertifikat verarbeiten kann, wählen Sie ein Intervall für Ausfallzeiten aus, um den nächsten Schritt ausführen zu können. (Andernfalls, wenn die Anwendung das neue Zertifikat nicht automatisch übernimmt, aber mehrere Signaturzertifikate gleichzeitig verarbeiten kann, können Sie den nächsten Schritt jederzeit auszuführen.)
1. Führen Sie vor Ablauf des alten Zertifikats die weiter oben im Abschnitt [Hochladen und Aktivieren eines Zertifikats](#upload-and-activate-a-certificate) beschriebenen Schritte aus.
1. Melden Sie sich bei der Anwendung an, um sicherzustellen, dass das Zertifikat ordnungsgemäß funktioniert.

## <a name="related-articles"></a>Verwandte Artikel

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](../saas-apps/tutorial-list.md)
- [Anwendungsverwaltung mit Azure Active Directory](what-is-application-management.md)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](what-is-single-sign-on.md)
- [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](./debug-saml-sso-issues.md)