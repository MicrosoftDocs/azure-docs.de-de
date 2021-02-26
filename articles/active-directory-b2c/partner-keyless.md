---
title: Tutorial zum Konfigurieren von Keyless mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Keyless mit Azure Active Directory B2C für die kennwortlose Authentifizierung
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c8a5666d373852da5ff79490f435b2d66d5cc6e0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090365"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Keyless mit Azure Active Directory B2C

Dieses Beispieltutorial bietet einen Leitfaden zum Konfigurieren von Azure Active Directory B2C mit [Keyless](https://keyless.io/). Mit Azure AD B2C als Identitätsanbieter können Sie Keyless in jede Ihrer Kundenanwendungen integrieren, um Ihren Benutzern eine echte kennwortlose Authentifizierung zu bieten.

Die Lösung **Keyless Zero-Knowledge Biometric (ZKB™)** von Keyless ermöglicht eine kennwortlose mehrstufige Authentifizierung, die Betrug, Phishing und die Wiederverwendung von Anmeldeinformationen ausschließt und gleichzeitig Benutzerfreundlichkeit und Schutz der Privatsphäre von Kunden sicherstellt.

## <a name="pre-requisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Der Mandant muss mit Ihrem Azure-Abonnement verknüpft sein.

- Einen Keyless-Cloudmandanten. Registrieren Sie sich für ein kostenloses [Testkonto](https://keyless.io/go).

- Die Keyless Authenticator-App muss auf dem Gerät des Benutzers installiert sein.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Keyless-Integration umfasst die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver ist für die Überprüfung der Anmeldeinformationen des Benutzers verantwortlich. Er wird auch als Identitätsanbieter bezeichnet.

- Web- und mobile Anwendungen: Ihre mobilen oder Webanwendungen, die Sie mit Keyless und Azure AD B2C schützen möchten.

- Die mobile Keyless-App: Diese wird für die Authentifizierung bei Anwendungen mit Azure AD B2C-Unterstützung verwendet.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Abbildung des Keyless-Architekturdiagramms](./media/partner-keyless/keyless-architecture-diagram.png)

|Schritt | Beschreibung |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt Anmeldung oder Registrierung aus und gibt den Benutzernamen ein.
| 2. | Die Anwendung sendet die Benutzerattribute an Azure AD B2C zum Überprüfen der Identität.
| 3. | Azure AD B2C sammelt die Benutzerattribute und sendet die Attribute an Keyless, um den Benutzer über die mobile Keyless-App zu authentifizieren.
| 4. | Keyless sendet eine Pushbenachrichtigung an das mobile Gerät des registrierten Benutzers, um die Authentifizierung unter Einhaltung des Datenschutzes in Form eines biometrischen Gesichtsscans durchzuführen.
| 5. | Nachdem der Benutzer auf die Pushbenachrichtigung reagiert hat, wird ihm anhand der Prüfergebnisse entweder der Zugriff auf die Kundenanwendung gewährt oder verweigert.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Hinzufügen eines neuen Identitätsanbieters

Führen Sie zum Hinzufügen eines neuen Identitätsanbieters die folgenden Schritte aus:

1. Melden Sie sich beim **[Azure-Portal](https://portal.azure.com/#home)** als globaler Administrator Ihres Azure AD B2C-Mandanten an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü den **Verzeichnis- und Abonnementfilter** und das entsprechende Verzeichnis auswählen.

3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.

4. Navigieren Sie zu **Dashboard** > **Azure Active Directory B2C** >  **Identitätsanbieter**.

5. Wählen Sie **Identitätsanbieter** aus.

6. Wählen Sie **Hinzufügen**.

### <a name="configure-an-identity-provider"></a>Konfigurieren eines Identitätsanbieters

Führen Sie zum Konfigurieren eines Identitätsanbieters die folgenden Schritte aus:

1. Wählen Sie **Identitätsanbietertyp** > **OpenID Connect (Vorschau)** aus.
2. Füllen Sie das Formular aus, um den Identitätsanbieter einzurichten:

   |Eigenschaft | Wert |
   |:-----| :-----------|
   | Name   | Keyless |
   | Metadaten-URL | Fügen Sie den URI der gehosteten Keyless-Authentifizierungs-App gefolgt vom spezifischen Pfad ein, z. B. „https://keyless.auth/.well-known/openid-configuration“. |
   | Geheimer Clientschlüssel | Das Geheimnis, das der Keyless-Authentifizierungsinstanz zugeordnet ist (nicht mit dem zuvor konfigurierten identisch). Fügen Sie eine komplexe Zeichenfolge Ihrer Wahl ein. Dieses Geheimnis wird später bei der Konfiguration des Keyless-Containers verwendet.|
   | Client-ID | Die ID des Clients. Diese ID wird später bei der Konfiguration des Keyless-Containers verwendet.|
   | Bereich | openid |
   | Antworttyp | id_token |
   | Antwortmodus | form_post|

3. Klicken Sie auf **OK**.

4. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus.

5. Füllen Sie das Formular aus, um den Identitätsanbieter zuzuordnen:

   |Eigenschaft | Wert |
   |:-----| :-----------|
   | UserID    | Aus Abonnement |
   | `Display name` | Aus Abonnement |
   | Antwortmodus | Aus Abonnement |

6. Wählen Sie **Speichern** aus, um die Einrichtung für Ihren neuen Open ID Connect-Identitätsanbieter (OIDC) abzuschließen.

### <a name="create-a-user-flow-policy"></a>Erstellen einer Benutzerflowrichtlinie

Keyless sollte jetzt als neuer OIDC-Identitätsanbieter unter Ihren B2C-Identitätsanbietern aufgeführt sein.

1. Wählen Sie auf Ihrem Azure AD B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.

2. Wählen Sie **Neuer Benutzerflow** aus.

3. Wählen Sie **Registrierung und Anmeldung**, anschließend eine **Version** und dann **Erstellen** aus.

4. Geben Sie einen **Namen** für die Richtlinie ein.

5. Wählen Sie im Abschnitt „Identitätsanbieter“ den neu erstellten Keyless-Identitätsanbieter aus.

6. Richten Sie die Parameter für den Benutzerflow ein. Fügen Sie einen Namen ein, und wählen Sie den von Ihnen erstellten Identitätsanbieter aus. Sie können auch eine E-Mail-Adresse hinzufügen. In diesem Fall leitet Azure die Anmeldeprozedur nicht direkt an Keyless um, sondern zeigt stattdessen einen Bildschirm an, auf dem der Benutzer die gewünschte Option auswählen kann.

7. Belassen Sie das Feld **Multi-Factor Authentication** unverändert.

8. Auswählen von **Richtlinien für bedingten Zugriff erzwingen**

9. Wählen Sie unter **Benutzerattribute und Tokenansprüche** für „Attribut sammeln“ die Option **E-Mail-Adresse** aus. Sie können alle Attribute hinzufügen, die Azure Active Directory über den Benutzer sammeln kann, sowie alle Ansprüche, die Azure AD B2C an die Clientanwendung zurückgeben kann.

10. Wählen Sie **Erstellen** aus.

11. Wählen Sie nach der erfolgreichen Erstellung den neuen **Benutzerflow** aus.

12. Wählen Sie im linken Bereich **Anwendungsansprüche** aus. Aktivieren Sie unter den Optionen das Kontrollkästchen **E-Mail**, und wählen Sie **Speichern** aus.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option Identity Experience Framework aus.

2. Wählen Sie den zuvor erstellten Flow SignUpSignIn aus.

3. Wählen Sie Benutzerflow ausführen und dann die Einstellungen aus:

   a. Anwendung: Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. Antwort-URL: Wählen Sie die Umleitungs-URL aus.

   c. Wählen Sie Benutzerflow ausführen aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. Keyless wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
