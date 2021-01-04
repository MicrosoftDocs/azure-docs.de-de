---
title: Erweiterte Zertifikatsignaturoptionen im SAML-Token für Azure AD-Apps
description: Hier erfahren Sie, wie Sie erweiterte Optionen für die Zertifikatsignatur im SAML-Token für bereits in Azure Active Directory integrierte Apps verwenden.
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
ms.date: 03/25/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79bc43bb2fa99f95a462dcc4c68d27840199b45c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658535"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Erweiterte Optionen für die Zertifikatsignatur im SAML-Token für Katalog-Apps in Azure Active Directory

Azure Active Directory (Azure AD) unterstützt im Anwendungskatalog von Azure Active Directory mittlerweile Tausende von vorab integrierten Anwendungen. Über 500 Anwendungen (etwa die Anwendung [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite)) unterstützen einmaliges Anmelden mit dem [SAML](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0-Protokoll. Wenn sich ein Kunde mithilfe von SAML über Azure AD bei einer Anwendung authentifiziert, sendet Azure AD ein Token an die Anwendung (per HTTP POST). Die Anwendung überprüft und verwendet dann das Token, um den Benutzer anzumelden, anstatt den Benutzernamen und das Kennwort anzufordern. Diese SAML-Token werden mit dem eindeutigen Zertifikat, das in Azure AD generiert wird, und mit spezifischen Standardalgorithmen signiert.

Azure AD verwendet einige der Standardeinstellungen für die Kataloganwendungen. Die Standardwerte werden auf der Grundlage der Anwendungsanforderungen eingerichtet.

In Azure AD können Sie die Zertifikatsignaturoptionen und den Zertifikatsignaturalgorithmus einrichten.

## <a name="certificate-signing-options"></a>Optionen für die Zertifikatsignatur

Azure AD unterstützt drei Optionen für die Zertifikatsignatur:

* **SAML-Assertion signieren**: Diese Standardoption ist für die meisten Kataloganwendungen festgelegt. Bei Auswahl dieser Option signiert Azure AD als Identitätsanbieter (IdP) die SAML-Assertionen und das Zertifikat mit dem [X509](https://wikipedia.org/wiki/X.509)-Zertifikat der Anwendung.

* **SAML-Antwort signieren**: Bei Auswahl dieser Option signiert Azure AD als IdP die SAML-Antwort mit dem X509-Zertifikat der Anwendung.

* **SAML-Antwort und -Assertion signieren**: Bei Auswahl dieser Option signiert Azure AD als IdP das gesamte SAML-Token mit dem X509-Zertifikat der Anwendung.

## <a name="certificate-signing-algorithms"></a>Zertifikatsignaturalgorithmen

Azure AD unterstützt zwei Signaturalgorithmen bzw. sichere Hashalgorithmen (SHAs) zum Signieren der SAML-Antwort:

* **SHA-256**: Azure AD verwendet diesen Standardalgorithmus zum Signieren der SAML-Antwort. Hierbei handelt es sich um den neuesten Algorithmus, der sicherer als SHA-1 ist. Die meisten Anwendungen unterstützen den SHA-256-Algorithmus. Wenn eine Anwendung nur SHA-1 als Signaturalgorithmus unterstützt, können Sie dies ändern. Andernfalls wird empfohlen, den SHA-256-Algorithmus zum Signieren der SAML-Antwort zu verwenden.

* **SHA-1**: Dieser Algorithmus ist älter und gilt als weniger sicher als SH-256. Wenn eine Anwendung nur diesen Signaturalgorithmus unterstützt, können Sie diese Option in der Dropdownliste **Signaturalgorithmus** auswählen. Azure AD signiert die SAML-Antwort dann mit dem SHA-1-Algorithmus.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Ändern der Zertifikatsignaturoptionen und des -signaturalgorithmus

Wählen Sie die gewünschte Anwendung aus, um ihre SAML-Zertifikatsignaturoptionen und den Zertifikatsignaturalgorithmus zu ändern:

1. Melden Sie sich im [Azure Active Directory-Portal](https://aad.portal.azure.com/) bei Ihrem Konto an. Die Seite **Azure Active Directory Admin Center** wird angezeigt.
1. Wählen Sie im linken Bereich **Unternehmensanwendungen** aus. Eine Liste der Unternehmensanwendungen in Ihrem Konto wird angezeigt.
1. Wählen Sie eine Anwendung aus. Eine Übersichtsseite für die Anwendung wird angezeigt.

   ![Beispiel: Übersichtsseite der Anwendung](./media/certificate-signing-options/application-overview-page.png)

Ändern Sie als Nächstes die Zertifikatsignaturoptionen im SAML-Token für diese Anwendung:

1. Wählen Sie im linken Bereich der Anwendungsübersichtsseite **Einmaliges Anmelden** aus.
1. Gehen Sie auf der angezeigten Seite **Einmaliges Anmelden (SSO) mit SAML einrichten – Vorschau** zu Schritt 5.
1. Wird die Seite **SSO-Methode auswählen** nicht angezeigt, wählen Sie **Modi für einmaliges Anmelden ändern**, um diese Seite aufzurufen.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus, sofern verfügbar. (Ist **SAML** nicht verfügbar, wird SAML von der Anwendung nicht unterstützt, und Sie können den Rest dieses Verfahrens und Artikels ignorieren.)
1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten – Vorschau** zur Überschrift **SAML-Signaturzertifikat**, und wählen Sie das Symbol **Bearbeiten** (Bleistift) aus. Die Seite **SAML-Signaturzertifikat** wird angezeigt.

   ![Beispiel: Seite „SAML-Signaturzertifikat“](./media/certificate-signing-options/saml-signing-page.png)

1. Wählen Sie in der Dropdownliste **Signaturoption** die Option **SAML-Anwort signieren**, **SAML-Assertion signieren** oder **SAML-Antwort und-Assertion signieren** aus. Beschreibungen dieser Optionen finden Sie weiter oben in diesem Artikel unter [Optionen für die Zertifikatsignatur](#certificate-signing-options).
1. Wählen Sie in der Dropdownliste **Signaturalgorithmus** die Option **SHA-1** oder **SHA-256** aus. Beschreibungen dieser Optionen finden Sie weiter oben in diesem Artikel im Abschnitt [Zertifikatsignaturalgorithmen](#certificate-signing-algorithms).
1. Wenn Sie mit Ihrer Auswahl zufrieden sind, wählen Sie **Speichern** aus, um die neuen SAML-Signaturzertifikateinstellungen zu übernehmen. Wählen Sie andernfalls **X** aus, um die Änderungen zu verwerfen.

## <a name="next-steps"></a>Nächste Schritte

* [Konfigurieren des einmaligen Anmeldens für Anwendungen, die nicht im Azure Active Directory-Anwendungskatalog enthalten sind](./configure-saml-single-sign-on.md)
* [Problembehandlung bei SAML-basiertem einmaligem Anmelden](./debug-saml-sso-issues.md)