---
title: Trusona und Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie Trusona als Identitätsanbieter in Azure AD B2C hinzufügen, um die kennwortlose Authentifizierung zu ermöglichen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573686"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrieren von Trusona mit Azure Active Directory B2C

Trusona ist ein unabhängiger Softwareanbieter (Independent Software Vendor, ISV), der für eine sichere Anmeldung sorgt, indem die kennwortlose Authentifizierung, mehrstufige Authentifizierung und das Scannen digitaler Lizenzen ermöglicht werden. In diesem Artikel wird beschrieben, wie Sie Trusona als Identitätsanbieter in Azure AD B2C hinzufügen, um die kennwortlose Authentifizierung zu ermöglichen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
* Ein [Testkonto](https://www.trusona.com/) bei Trusona.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Szenario fungiert Trusona als Identitätsanbieter für Azure AD B2C, um die kennwortlose Authentifizierung zu ermöglichen. Die Lösung besteht aus den folgenden Komponenten:

* Einer kombinierten Azure AD B2C-Richtlinie für Anmeldung und Registrierung
* Hinzufügung von Trusona als Identitätsanbieter zu Azure AD B2C
* Herunterladbare Trusona-App

![Architekturdiagramm für Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Schritt | BESCHREIBUNG |
|------|------|
|1     | Ein Benutzer versucht, mit der Anwendung die Anmeldung oder Registrierung durchzuführen. Der Benutzer wird über die Azure AD B2C-Richtlinie für die Registrierung und Anmeldung authentifiziert. Während der Registrierung wird die zuvor verifizierte E-Mail-Adresse des Benutzers aus der Trusona-App verwendet.     |
|2     | Azure B2C leitet den Benutzer über den impliziten Datenfluss an den Trusona-OpenID Connect-Identitätsanbieter (OIDC) um.     |
|3     | Für Desktop-PC-Anmeldungen zeigt Trusona einen eindeutigen, zustandslosen, animierten und dynamischen QR-Code an, der mit der Trusona-App gescannt werden kann. Bei mobilen Anmeldungen verwendet Trusona einen „Deep-Link“, um die Trusona-App zu öffnen. Diese beiden Methoden werden für das Gerät und letztendlich auch für die Benutzerermittlung verwendet.     |
|4     | Der Benutzer scannt den angezeigten QR-Code mit der Trusona-App.     |
|5     | Das Benutzerkonto befindet sich im Trusona-Clouddienst, und die Authentifizierung wird vorbereitet.     |
|6     | Der Trusona-Clouddienst gibt eine Authentifizierungsaufforderung für den Benutzer aus, indem eine Pushbenachrichtigung an die Trusona-App gesendet wird:<br>a. Die Authentifizierungsaufforderung wird dem Benutzer angezeigt. <br> b. Der Benutzer akzeptiert die Aufforderung oder lehnt sie ab. <br> c. Der Benutzer wird aufgefordert, zur Bestätigung das Sicherheitsverfahren des Betriebssystems (z. B. biometrisch, Passcode, PIN oder Muster) durchzuführen und die Aufforderung mit einem privaten Schlüssel in der Umgebung für Secure Enclave/Trusted Execution zu signieren. <br> d. Die Trusona-App generiert in Echtzeit basierend auf den Parametern der Authentifizierung eine dynamische Anti-Replay-Nutzlast. <br> e. Die gesamte Antwort wird (zum zweiten Mal) mit einem privaten Schlüssel in der Umgebung für Secure Enclave/Trusted Execution signiert und zur Überprüfung an den Trusona-Clouddienst zurückgegeben.      |
|7     |  Der Trusona-Clouddienst leitet den Benutzer mit einem ID-Token (id_token) zurück an die initiierende Anwendung. Azure AD B2C überprüft das ID-Token mit der veröffentlichten OpenID-Konfiguration von Trusona, die beim Einrichten des Identitätsanbieters konfiguriert wurde.    |
|  |  |

## <a name="onboard-with-trusona"></a>Durchführen des Onboardings mit Trusona

1. Füllen Sie das [Formular](https://www.trusona.com/) aus, um ein Trusona-Konto zu erstellen und zu beginnen.

2. Laden Sie die mobile Trusona-App aus dem App Store herunter. Installieren Sie die App, und registrieren Sie Ihre E-Mail-Adresse.

3. Verifizieren Sie Ihre E-Mail-Adresse mit dem sicheren „magischen Link“, der von der Software gesendet wurde.  

4. Navigieren Sie zum [Trusona Developer-Dashboard](https://dashboard.trusona.com), um das Self-Service-Verfahren durchzuführen.

5. Wählen Sie **I’m Ready** (Ich bin bereit) aus, und authentifizieren Sie sich mit Ihrer Trusona-App.

6. Wählen Sie im linken Navigationsbereich die Option **OIDC Integrations** (OIDC-Integrationen) aus.

7. Wählen Sie **Create OpenID Connect Integration** (OpenID Connect-Integration erstellen) aus.

8. Geben Sie im Feld **Name** einen Namen Ihrer Wahl an, und verwenden Sie die zuvor angegebenen Domäneninformationen (z. B. Contoso) im Feld **Client Redirect Host** (Clientumleitungshost).  

   > [!NOTE]
   > Der ursprüngliche Domänenname von Azure Active Directory wird als Clientumleitungshost verwendet.

9. Befolgen Sie die Anleitung im [Leitfaden zur Trusona-Integration](https://docs.trusona.com/integrations/aad-b2c-integration/). Verwenden Sie nach entsprechender Aufforderung den ursprünglichen Domänennamen (z. B. Contoso) aus dem vorherigen Schritt.  

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Hinzufügen eines neuen Identitätsanbieters

> [!NOTE]
> Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.

2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.

3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.

4. Navigieren Sie zu **Dashboard** > **Azure Active Directory B2C** > **Identitätsanbieter**.

3. Wählen Sie **Identitätsanbieter** aus.

4. Wählen Sie **Hinzufügen**.

### <a name="configure-an-identity-provider"></a>Konfigurieren eines Identitätsanbieters  

1. Wählen Sie **Identitätsanbietertyp** > **OpenID Connect (Vorschau)** aus.

2. Füllen Sie das Formular aus, um den Identitätsanbieter einzurichten:  

   | Eigenschaft | Wert  |
   | :--- | :--- |
   | Metadaten-URL | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | Client-ID | Wird von Trusona per E-Mail gesendet |
   | `Scope` | E-Mail-Adresse aus OpenID-Profil |
   | Antworttyp | Id_token |
   | Antwortmodus  | Form_post |

3. Klicken Sie auf **OK**.  

4. Wählen Sie **Ansprüche dieses Identitätsanbieters zuordnen** aus.  

5. Füllen Sie das Formular aus, um den Identitätsanbieter zuzuordnen:

   | Eigenschaft | Wert  |
   | :--- | :--- |
   | UserID | Sub  |
   | `Display name` | nickname |
   | Vorname | given_name |
   | Surname | Family_name |
   | Antwortmodus | email |

6. Wählen Sie **OK** aus, um die Einrichtung für Ihren neuen OIDC-Identitätsanbieter abzuschließen.

### <a name="create-a-user-flow-policy"></a>Erstellen einer Benutzerflowrichtlinie

Trusona sollte jetzt als **neuer OpenID Connect-Identitätsanbieter** unter Ihren B2C-Identitätsanbietern aufgeführt sein.

1. Wählen Sie auf Ihrem Azure AD B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.

1. Wählen Sie die Option **Neuer Benutzerflow** aus.

1. Wählen Sie **Registrierung und Anmeldung**, dann eine Version und anschließend **Erstellen** aus.

1. Geben Sie einen **Namen** für die Richtlinie ein.

1. Wählen Sie im Abschnitt **Identitätsanbieter** den neu erstellten **Trusona-Identitätsanbieter** aus.

   > [!NOTE]
   > Da es sich bei Trusona quasi um eine mehrstufige Authentifizierung handelt, sollten Sie die Option für die mehrstufige Authentifizierung deaktiviert lassen.

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie unter **Benutzerattribute und Ansprüche** die Option **Mehr anzeigen** aus. Wählen Sie im Formular mindestens ein Attribut aus, das Sie oben bei der Einrichtung Ihres Identitätsanbieters angegeben haben.

1. Klicken Sie auf **OK**.  

### <a name="test-the-policy"></a>Testen der Richtlinie

1. Wählen Sie Ihre neu erstellte Richtlinie aus.

2. Wählen Sie **Benutzerflow ausführen** aus.

3. Geben Sie im Formular die Antwort-URL ein.

4. Wählen Sie **Benutzerflow ausführen** aus. Sie sollten zum Trusona-OIDC-Gateway umgeleitet werden. Scannen Sie auf dem Trusona-Gateway den angezeigten Secure-QR-Code mit der Trusona-App oder mit einer benutzerdefinierten App, indem Sie das Trusona Mobile SDK verwenden.

5. Nach dem Scannen des Secure-QR-Codes sollten Sie zur Antwort-URL umgeleitet werden, die Sie in Schritt 3 definiert haben.

## <a name="next-steps"></a>Nächste Schritte  

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-get-started.md?tabs=applications)
