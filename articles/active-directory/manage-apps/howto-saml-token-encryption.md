---
title: SAML-Tokenverschlüsselung in Azure Active Directory
description: Erfahren Sie, wie Sie die SAML-Tokenverschlüsselung in Azure Active Directory konfigurieren.
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
ms.date: 03/13/2020
ms.author: kenwith
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 820e2cb0d422597f0e649e6934fd8bb11c1521db
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997476"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Gewusst wie: Konfigurieren der Azure AD-SAML-Tokenverschlüsselung

> [!NOTE]
> Die Tokenverschlüsselung ist ein Premium-Feature von Azure Active Directory (Azure AD). Weitere Informationen zu verfügbaren Editionen, Features und Preisen von Azure AD finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

Die SAML-Tokenverschlüsselung ermöglicht die Verwendung von verschlüsselten SAML-Assertionen mit einer Anwendung, die diese unterstützt. Sofern für eine Anwendung konfiguriert, verschlüsselt Azure AD die für diese Anwendung ausgegebenen SAML-Assertionen mit dem öffentlichen Schlüssel, der aus einem in Azure AD gespeicherten Zertifikat abgerufen wurde. Die Anwendung muss das Token mit dem zugehörigen privaten Schlüssel entschlüsseln, bevor es als Nachweis der Authentifizierung für den angemeldeten Benutzer verwendet werden kann.

Die Verschlüsselung der SAML-Assertionen zwischen Azure AD und der Anwendung bietet zusätzlichen Schutz vor dem Abfangen von Tokeninhalten, sodass personenbezogene Daten oder Geschäftsdaten nicht gefährdet werden.

Selbst ohne Tokenverschlüsselung werden Azure AD-SAML-Token niemals als Klartext über das Netzwerk weitergegeben. In Azure AD muss die Tokenanforderung/-antwort über verschlüsselte HTTPS/TLS-Kanäle durchgeführt werden, sodass die Kommunikation zwischen Identitätsanbieter, Browser und Anwendung über verschlüsselte Verbindungen erfolgt. Wägen Sie die Vorteile der Tokenverschlüsselung und den Aufwand für die Verwaltung zusätzlicher Zertifikate gegeneinander ab.   

Um die Tokenverschlüsselung zu konfigurieren, müssen Sie eine X.509-Zertifikatsdatei mit dem öffentlichen Schlüssel in das Azure AD-Anwendungsobjekt hochladen, das die Anwendung repräsentiert. Um das X.509-Zertifikat zu erhalten, können Sie es aus der Anwendung selbst herunterladen oder vom Anwendungsanbieter beziehen, sofern der Anwendungsanbieter Verschlüsselungsschlüssel bereitstellt. Falls die Anwendung die Bereitstellung eines privaten Schlüssels anfordert, kann dieser mithilfe eines Kryptografietools erstellt werden. Hierbei wird der private Schlüssel in den Schlüsselspeicher der Anwendung hochgeladen, und das zugehörige Zertifikat mit dem öffentlichen Schlüssel wird in Azure AD hochgeladen.

Azure AD verwendet AES-256 zum Verschlüsseln der SAML-Assertionendaten.

## <a name="configure-saml-token-encryption"></a>Konfigurieren der SAML-Tokenverschlüsselung

Führen Sie zur SAML-Tokenverschlüsselung die folgenden Schritte aus:

1. Rufen Sie ein Zertifikat mit einem öffentlichen Schlüssel ab, das einem in der Anwendung konfigurierten privaten Schlüssel entspricht.

    Erstellen Sie ein asymmetrisches Schlüsselpaar für die Verschlüsselung. Wenn die Anwendung einen öffentlichen Schlüssel für die Verschlüsselung bereitstellt, befolgen Sie stattdessen die Anwendungsanweisungen zum Herunterladen des X.509-Zertifikats.

    Der öffentliche Schlüssel sollte in einer X.509-Zertifikatsdatei im CER-Format gespeichert sein.

    Wenn die Anwendung einen Schlüssel verwendet, den Sie für Ihre Instanz erstellen, folgen Sie den Anwendungsanweisungen zum Installieren des privaten Schlüssels, mit dem die Anwendung Token von Ihrem Azure AD-Mandanten entschlüsselt.

1. Fügen Sie das Zertifikat zur Anwendungskonfiguration in Azure AD hinzu.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>So konfigurieren Sie die Tokenverschlüsselung im Azure-Portal

Sie können das öffentliche Zertifikat Ihrer Anwendungskonfiguration im Azure-Portal hinzufügen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Wechseln Sie zum Blatt **Azure Active Directory > Unternehmensanwendungen**, und wählen Sie anschließend die Anwendung aus, für die Sie die Tokenverschlüsselung konfigurieren möchten.

1. Wählen Sie auf der Anwendungsseite die Option **Tokenverschlüsselung** aus.

    ![Option „Tokenverschlüsselung“ im Azure-Portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Die Option **Tokenverschlüsselung** ist nur für SAML-Anwendungen verfügbar, die über das Blatt **Unternehmensanwendungen** im Azure-Portal eingerichtet wurden – entweder über den Anwendungskatalog oder eine Nicht-Katalog-App. Für andere Anwendungen ist diese Menüoption deaktiviert. Für Anwendungen, die über den Abschnitt **App-Registrierungen** im Azure-Portal registriert wurden, können Sie die Verschlüsselung für SAML-Token mithilfe des Anwendungsmanifests, über Microsoft Graph oder über PowerShell konfigurieren.

1. Wählen Sie auf der Seite **Tokenverschlüsselung** die Option **Zertifikat importieren** aus, um die CER-Datei mit Ihrem öffentlichen X.509-Zertifikat zu importieren.

    ![Importieren der CER-Datei mit dem X.509-Zertifikat](./media/howto-saml-token-encryption/import-certificate-small.png)

1. Nachdem das Zertifikat importiert und der private Schlüssel zur Verwendung auf Anwendungsseite konfiguriert wurde, aktivieren Sie die Verschlüsselung, indem Sie neben dem Fingerabdruckstatus auf **...** klicken und anschließend aus dem Dropdownmenü die Option **Tokenverschlüsselung aktivieren** auswählen.

1. Wählen Sie **Ja** aus, um die Aktivierung des Zertifikats zur Tokenverschlüsselung zu bestätigen.

1. Bestätigen Sie, dass die für die Anwendung ausgegebenen SAML-Assertionen verschlüsselt sind.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>So deaktivieren Sie die Tokenverschlüsselung im Azure-Portal

1. Wechseln Sie im Azure-Portal zu **Azure Active Directory > Unternehmensanwendungen**, und wählen Sie anschließend die Anwendung aus, für die die SAML-Tokenverschlüsselung aktiviert ist.

1. Wählen Sie auf der Anwendungsseite die Option **Tokenverschlüsselung** aus, suchen Sie nach dem Zertifikat, und wählen Sie die Option **...** aus, um das Dropdownmenü anzuzeigen.

1. Wählen Sie **Tokenverschlüsselung deaktivieren** aus.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurieren der SAML-Tokenverschlüsselung mit Graph-API, PowerShell oder App-Manifest

Verschlüsselungszertifikate werden im Anwendungsobjekt in Azure AD mit einem `encrypt`-Nutzungstag gespeichert. Sie können mehrere Verschlüsselungszertifikate konfigurieren. Das für die Verschlüsselung von Token aktive Zertifikat wird durch das Attribut `tokenEncryptionKeyID` identifiziert.

Sie benötigen die Anwendungsobjekt-ID, um die Tokenverschlüsselung mit Microsoft Graph-API oder PowerShell zu konfigurieren. Sie können diesen Wert programmgesteuert abrufen, oder Sie wechseln zur Seite **Eigenschaften** der Anwendung im Azure-Portal und notieren sich den Wert für **Objekt-ID**.

Wenn Sie ein keyCredential-Objekt mit Graph, PowerShell oder im Anwendungsmanifest konfigurieren, müssen Sie eine GUID zur Verwendung für die keyId generieren.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>So konfigurieren Sie die Tokenverschlüsselung mit Microsoft Graph

1. Aktualisieren Sie die `keyCredentials` der Anwendung mit einem X.509-Zertifikat für die Verschlüsselung. Das folgende Beispiel zeigt die erforderliche Vorgehensweise.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifizieren Sie das Verschlüsselungszertifikat, das zum Verschlüsseln von Token aktiv ist. Das folgende Beispiel zeigt die erforderliche Vorgehensweise.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>So konfigurieren Sie die Tokenverschlüsselung mit PowerShell

1. Verwenden Sie das neueste Azure AD PowerShell-Modul, um eine Verbindung mit Ihrem Mandanten herzustellen.

1. Legen Sie die Tokenverschlüsselungseinstellungen mit dem Befehl **[Set-AzureApplication](/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** fest.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Lesen Sie die Tokenverschlüsselungseinstellungen mithilfe der folgenden Befehle.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>So konfigurieren Sie die Tokenverschlüsselung mithilfe des Anwendungsmanifests

1. Wechseln Sie im Azure-Portal zu **Azure Active Directory > App-Registrierungen**.

1. Wählen Sie im Dropdownmenü **Alle Apps** aus, um alle Apps anzuzeigen, und wählen Sie dann die Unternehmensanwendung aus, die Sie konfigurieren möchten.

1. Wählen Sie auf der Anwendungsseite **Manifest** aus, um das [Anwendungsmanifest](../develop/reference-app-manifest.md) zu bearbeiten.

1. Legen Sie den Wert für das `tokenEncryptionKeyId`-Attribut fest.

    Das folgende Beispiel zeigt ein Anwendungsmanifest, das mit zwei Verschlüsselungszertifikaten konfiguriert ist. Hierbei wurde das zweite Zertifikat mithilfe des tokenEnryptionKeyId-Attributs als aktives Zertifikat festgelegt.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Verwendung des SAML-Protokolls durch Azure AD](../develop/active-directory-saml-protocol-reference.md).
* Informieren Sie sich über die Formate, Sicherheitsmerkmale und Inhalte von [SAML-Token in Azure AD](../develop/reference-saml-tokens.md).