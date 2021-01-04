---
title: Beheben von Problemen mit Geräten mit Hybrid-Azure Active Directory-Einbindung
description: Beheben von Problemen mit Geräten unter Windows 10 und Windows Server 2016 mit Hybrideinbindung in Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e235bf90568a1382a5ecee3ff4d2283aaa32f10b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083215"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Beheben von Problemen mit Geräten mit Hybrid-Azure Active Directory-Einbindung

Der Inhalt dieses Artikels gilt für Geräte, auf denen Windows 10 oder Windows Server 2016 ausgeführt wird.

Informationen zu anderen Windows-Clients finden Sie im Artikel [Beheben von Problemen mit kompatiblen Geräten mit Hybrid-Azure Active Directory-Einbindung](troubleshoot-hybrid-join-windows-legacy.md).

In diesem Artikel wird vorausgesetzt, dass Sie [Geräte mit Hybrideinbindung in Azure Active Directory konfiguriert haben](hybrid-azuread-join-plan.md), um die folgenden Szenarien zu unterstützen:

- Gerätebasierter bedingter Zugriff
- [Unternehmensroaming von Einstellungen](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Dieses Dokument enthält Anleitungen zur Problembehandlung zum Beheben potenzieller Probleme.

Für Windows 10 und Windows Server 2016 unterstützt ein Azure Active Directory-Hybridbeitritt das Windows 10-Update vom November 2015 und höher.

## <a name="troubleshoot-join-failures"></a>Beheben von Einbindungsfehlern

### <a name="step-1-retrieve-the-join-status"></a>Schritt 1: Abrufen des Beitrittsstatus

**So rufen Sie den Beitrittsstatus ab**

1. Öffnen Sie als Administrator eine Eingabeaufforderung
2. Geben Sie `dsregcmd /status` ein

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Schritt 2: Bewerten des Beitrittsstatus

Überprüfen Sie die folgenden Felder, und stellen Sie sicher, dass sie die erwarteten Werte aufweisen:

#### <a name="domainjoined--yes"></a>DomainJoined: YES

Dieses Feld gibt an, ob das Gerät in ein lokales Active Directory eingebunden ist. Wenn der Wert **NO** lautet, kann das Gerät keinen Azure AD-Hybridbeitritt durchführen.

#### <a name="workplacejoined--no"></a>WorkplaceJoined: Nein

Dieses Feld gibt an, ob das Gerät bei Azure AD als privates Gerät registriert ist (markiert als *Workplace Join* ). Dieser Wert sollte für in eine Domäne eingebundene Computer mit Hybrideinbindung in Azure AD **NO** lauten. Wenn der Wert **YES** lautet, wurde vor Abschluss der Hybrideinbindung in Azure AD ein Geschäfts-, Schul- oder Unikonto hinzugefügt. In diesem Fall wird das Konto bei Verwendung von Windows 10-Version 1607 oder höher ignoriert.

#### <a name="azureadjoined--yes"></a>AzureAdJoined: YES

Dieses Feld gibt an, ob das Gerät eingebunden ist. Der Wert ist **YES** , wenn das Gerät entweder in Azure AD oder hybrid in Azure AD eingebunden ist.
Wenn der Wert **NO** lautet, wurde der Azure AD-Beitritt noch nicht abgeschlossen.

Fahren Sie mit den nächsten Schritten zur weiteren Problembehandlung fort.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Schritt 3: Suchen Sie die Phase, in der die Einbindung fehlgeschlagen ist, und den zugehörigen Fehlercode

#### <a name="windows-10-1803-and-above"></a>Windows 10 Version 1803 und höher

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach dem Unterabschnitt „Vorherige Registrierung“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden ist und nicht in Azure AD Hybrid eingebunden werden kann.
Das Feld „Fehlerphase“ bezeichnet die Phase, in der der Einbindungsfehlers aufgetreten ist, während „Client-Fehlercode“ den Fehlercode des Einbindungsvorgangs angibt.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Ältere Windows 10-Versionen

Verwenden Sie die Protokolle der Ereignisanzeige, um die Phase und den Fehlercode für die Einbindungsfehler zu ermitteln.

1. Öffnen Sie die Ereignisprotokolle der **Benutzergeräteregistrierung** in der Ereignisanzeige. Sie finden sie unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung**.
2. Suchen Sie nach Ereignissen mit den folgenden eventIDs: 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Screenshot der Ereignisanzeige. Ein Ereignis mit der ID 304 ist ausgewählt, und die zugehörigen Informationen werden angezeigt. Der Fehlercode und die Phase sind hervorgehoben." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Screenshot der Ereignisanzeige. Ein Ereignis mit der ID 305 ist sichtbar, und die zugehörigen Informationen werden angezeigt. Der Fehlercode ist hervorgehoben." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Schritt 4: Überprüfen Sie die folgenden Listen auf mögliche Ursachen und Lösungen

#### <a name="pre-check-phase"></a>Vorabprüfungsphase

Mögliche Fehlerursachen:

- Das Gerät hat keine „Sichtverbindung“ mit dem Domänencontroller.
   - Das Gerät muss sich im internen Unternehmensnetzwerk oder im VPN mit direkter Netzwerkverbindung zu einem lokalen Active Directory-Domänencontroller befinden.

#### <a name="discover-phase"></a>Ermittlungsphase

Mögliche Fehlerursachen:

- Falsch konfiguriertes Objekt im Dienstverbindungspunkt (SPC)/Dienstverbindungspunktobjekt (SPC) kann nicht vom DC gelesen werden.
   - In der AD-Gesamtstruktur, zu der das Gerät gehört, ist ein gültiges SCP-Objekt erforderlich, das auf einen verifizierten Domänennamen in Azure AD verweist.
   - Ausführliche Informationen finden Sie im Abschnitt [Konfigurieren eines Dienstverbindungspunkts (SPC)](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Fehler beim Herstellen einer Verbindung und beim Abrufen der Ermittlungsmetadaten vom Ermittlungsendpunkt.
   - Das Gerät sollte im SYSTEM-Kontext auf `https://enterpriseregistration.windows.net` zugreifen können, um die Endpunkte für Registrierung und Autorisierung zu ermitteln.
   - Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass das Computerkonto des Geräts in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.
- Fehler beim Herstellen der Verbindung zum Endpunkt des Benutzerbereichs und beim Durchführen der Bereichsermittlung. (Nur Windows 10, Version 1809 und höher)
   - Das Gerät sollte im SYSTEM-Kontext auf `https://login.microsoftonline.com` zugreifen können, um eine Bereichsermittlung für die verifizierte Domäne durchführen und den Domänentyp (verwaltet/Verbund) bestimmen zu können.
   - Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass der SYSTEM-Kontext auf dem Gerät in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.

**Häufige Fehlercodes:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Ursache: Das SCP-Objekt kann nicht gelesen und die Azure AD-Mandanteninformationen können nicht abgerufen werden.
   - Lösung: Informationen finden Sie im Abschnitt [Konfigurieren eines Dienstverbindungspunkts (SPC)](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Ursache: Allgemeiner Ermittlungsfehler. Die Ermittlungsmetadaten konnten vom DRS nicht abgerufen werden.
   - Lösung: Suchen Sie weiter unten nach dem Unterfehler, um weitere Untersuchungen durchzuführen.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Ursache: Beim Ausführen der Ermittlung ist eine Zeitüberschreitung aufgetreten.
   - Lösung: Stellen Sie sicher, dass im SYSTEM-Kontext auf `https://enterpriseregistration.windows.net` zugegriffen werden kann. Weitere Informationen finden Sie im Abschnitt [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Ursache: Allgemeiner Fehler bei der Bereichsermittlung. Der Domänentyp (verwaltet/Verbund) konnte vom Sicherheitstokendienst (STS) nicht bestimmt werden.
   - Lösung: Suchen Sie weiter unten nach dem Unterfehler, um weitere Untersuchungen durchzuführen.

**Häufige Unterfehlercodes:**

Verwenden Sie eine der folgenden Methoden, um den Unterfehlercode für den Code des Ermittlungsfehlers zu finden.

##### <a name="windows-10-1803-and-above"></a>Windows 10 Version 1803 und höher

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach dem Unterabschnitt „DRS-Ermittlungstest“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden ist und nicht in Azure AD Hybrid eingebunden werden kann.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Ältere Windows 10-Versionen

Verwenden Sie die Protokolle der Ereignisanzeige, um die Phase und den Fehlercode für die Einbindungsfehler zu ermitteln.

1. Öffnen Sie die Ereignisprotokolle der **Benutzergeräteregistrierung** in der Ereignisanzeige. Sie finden sie unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung**.
2. Suchen Sie nach Ereignissen mit den folgenden eventIDs: 201

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Screenshot der Ereignisanzeige. Ein Ereignis mit der ID 201 ist ausgewählt, und die zugehörigen Informationen werden angezeigt. Der Fehlercode ist hervorgehoben." border="false":::

###### <a name="network-errors"></a>Netzwerkfehler

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Ursache: Verbindung mit dem Server konnte nicht hergestellt werden
   - Lösung: Stellen Sie die Netzwerkverbindung zu den erforderlichen Microsoft-Ressourcen sicher. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Ursache: Allgemeine Netzwerkzeitüberschreitung.
   - Lösung: Stellen Sie die Netzwerkverbindung zu den erforderlichen Microsoft-Ressourcen sicher. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Ursache: Der Netzwerkstapel konnte die Antwort des Servers nicht entschlüsseln.
   - Lösung: Stellen Sie sicher, dass der Netzwerk-Proxy keine Beeinträchtigung darstellt und die Serverantwort ändert.

###### <a name="http-errors"></a>HTTP-Fehler

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Ursache: SCP-Objekt mit falscher Mandanten-ID konfiguriert. Oder es wurden keine aktiven Abonnements im Mandanten gefunden.
   - Lösung: Stellen Sie sicher, dass das SCP-Objekt mit der korrekten Azure AD-Mandanten-ID und aktiven Abonnements konfiguriert bzw. im Mandanten vorhanden ist.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Ursache: HTTP 503 vom DRS-Server.
   - Lösung: Der Server ist zurzeit nicht verfügbar. Zukünftige Einbindungsversuche sind wahrscheinlich erfolgreich, sobald der Server wieder online ist.

###### <a name="other-errors"></a>Sonstige Fehler

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Ursache: Der JSON-Code der Serverantwort konnte nicht analysiert werden. Dies liegt wahrscheinlich daran, dass der Proxy HTTP 200 mit einer HTML-Authentifizierungsseite zurückgibt.
   - Lösung: Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass der SYSTEM-Kontext auf dem Gerät in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.

#### <a name="authentication-phase"></a>Authentifizierungsphase

Gilt nur für Verbunddomänenkonten.

Fehlerursachen:

- Es ist nicht möglich, einen Zugriffstoken für die DRS-Ressource im Hintergrund abzurufen.
   - Windows 10-Geräte erhalten Authentifizierungstoken vom Verbunddienst über die integrierte Windows-Authentifizierung bei einem aktiven WS-Trust Endpunkt. Details: [Verbundserverkonfiguration](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Häufige Fehlercodes:**

Verwenden Sie die Protokolle der Ereignisanzeige, um Fehlercode, Unterfehlercode, Serverfehlercode und Serverfehlermeldung zu finden.

1. Öffnen Sie die Ereignisprotokolle der **Benutzergeräteregistrierung** in der Ereignisanzeige. Sie finden sie unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung**.
2. Suchen Sie nach Ereignissen mit den folgenden eventIDs: 305

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Screenshot der Ereignisanzeige. Ein Ereignis mit der ID 305 ist sichtbar. Die zugehörigen Informationen werden angezeigt, und die ADAL-Fehlercodes und der Status sind hervorgehoben." border="false":::

##### <a name="configuration-errors"></a>Konfigurationsfehler

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Ursache: Das Authentifizierungsprotokoll ist nicht WS-Trust.
   - Lösung: Der lokale Identitätsanbieter muss WS-Trust unterstützen
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Ursache: Der lokale Verbunddienst hat keine XML-Antwort zurückgegeben.
   - Lösung: Stellen Sie sicher, dass der MEX-Endpunkt eine gültige XML-Antwort zurückgibt. Stellen Sie sicher, dass der Proxy keine Beeinträchtigung darstellt und keine Antworten in einem Format ohne XML zurückgibt.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Ursache: Der Endpunkt für die Authentifizierung von Benutzername/Kennwort konnte nicht ermittelt werden.
   - Lösung: Überprüfen Sie die Einstellungen des lokalen Identitätsanbieters. Stellen Sie sicher, dass die WS-Trust-Endpunkte aktiviert sind und die MEX-Antwort diese richtigen Endpunkte enthält.

##### <a name="network-errors"></a>Netzwerkfehler

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Ursache: Allgemeine Netzwerkzeitüberschreitung.
   - Lösung: Stellen Sie sicher, dass im SYSTEM-Kontext auf `https://login.microsoftonline.com` zugegriffen werden kann. Stellen Sie sicher, dass im SYSTEM-Kontext auf den lokalen Identitätsanbieter zugegriffen werden kann. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Ursache: Die Verbindung mit dem Authentifizierungsendpunkt wurde abgebrochen.
   - Lösung: Versuchen Sie es nach einiger Zeit erneut, oder versuchen Sie, die Einbindung über eine alternative stabile Netzwerkadresse vorzunehmen.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Ursache: Das vom Server gesendete Zertifikat von Transport Layer Security (TLS), zuvor bekannt als Secure Sockets Layer (SSL), konnte nicht überprüft werden.
   - Lösung: Überprüfen Sie die Zeitabweichung des Clients. Versuchen Sie es nach einiger Zeit erneut, oder versuchen Sie, die Einbindung über eine alternative stabile Netzwerkadresse vorzunehmen.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Ursache: Der Versuch, eine Verbindung mit `https://login.microsoftonline.com` herzustellen, ist fehlgeschlagen.
   - Lösung: Überprüfen Sie die Netzwerkverbindung mit `https://login.microsoftonline.com`.

##### <a name="other-errors"></a>Sonstige Fehler

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Ursache: Das SAML-Token vom lokalen Identitätsanbieter wurde von Azure AD nicht akzeptiert.
   - Lösung: Überprüfen Sie die Einstellungen des Verbundservers. Suchen Sie in den Authentifizierungsprotokollen nach dem Serverfehlercode.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Ursache: Die Serverantwort von WS-Trust meldet Fehlerausnahme und konnte keine Assertion abrufen
   - Lösung: Überprüfen Sie die Einstellungen des Verbundservers. Suchen Sie in den Authentifizierungsprotokollen nach dem Serverfehlercode.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Ursache: Beim Versuch, ein Zugriffstoken vom Tokenendpunkt abzurufen, wurde ein Fehler gemeldet.
   - Lösung: Suchen Sie im ADAL-Protokoll nach dem zugrunde liegenden Fehler.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Ursache: Allgemeiner ADAL-Fehler
   - Lösung: Suchen Sie in den Authentifizierungsprotokollen nach dem Unterfehlercode oder dem Serverfehlercode.

#### <a name="join-phase"></a>Einbindungsphase

Fehlerursachen:

Suchen Sie in der folgenden Liste nach dem Registrierungstyp und dem Fehlercode.

#### <a name="windows-10-1803-and-above"></a>Windows 10 Version 1803 und höher

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach dem Unterabschnitt „Vorherige Registrierung“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden ist und nicht in Azure AD Hybrid eingebunden werden kann.
Das Feld „Registrierungstyp“ gibt die Art der durchgeführten Einbindung an.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Ältere Windows 10-Versionen

Verwenden Sie die Protokolle der Ereignisanzeige, um die Phase und den Fehlercode für die Einbindungsfehler zu ermitteln.

1. Öffnen Sie die Ereignisprotokolle der **Benutzergeräteregistrierung** in der Ereignisanzeige. Sie finden sie unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung**.
2. Suchen Sie nach Ereignissen mit den folgenden eventIDs: 204

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Screenshot der Ereignisanzeige. Informationen zu einem Ereignis mit der ID 204 werden angezeigt, und der Fehlercode, der HTTP-Status und die Meldung sind hervorgehoben." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>Vom DRS-Server zurückgegebene HTTP-Fehler

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Ursache: Fehlerantwort vom DRS empfangen mit Fehlercode: „DirectoryError“
   - Lösung: Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Ursache: Fehlerantwort vom DRS empfangen mit Fehlercode: „AuthenticationError“ und Fehleruntercode ist NICHT „DeviceNotFound“.
   - Lösung: Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Ursache: Fehlerantwort vom DRS empfangen mit Fehlercode: „DirectoryError“
   - Lösung: Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode.

##### <a name="tpm-errors"></a>TPM-Fehler

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Ursache: TPM-Vorgang ist fehlgeschlagen oder ungültig
   - Lösung: Wahrscheinlich aufgrund eines ungültigen Sysprep-Images. Stellen Sie sicher, dass der Computer, von dem das Sysprep-Image erstellt wurde, nicht in Azure AD oder hybrid in Azure AD eingebunden oder bei Azure AD registriert ist.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Ursache: Allgemeiner TPM-Fehler.
   - Lösung: Deaktivieren Sie TPM auf Geräten mit diesem Fehler. Windows 10 Version 1809 und höher erkennt automatisch TPM-Fehler und schließt die Hybrid-Azure AD-Einbindung ohne Verwendung des TPM ab.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Ursache: TPM im FIPS-Modus wird derzeit nicht unterstützt.
   - Lösung: Deaktivieren Sie TPM auf Geräten mit diesem Fehler. Windows 10 Version 1809 erkennt automatisch TPM-Fehler und schließt die Hybrid-Azure AD-Einbindung ohne Verwendung des TPM ab.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Ursache: TPM wurde gesperrt.
   - Lösung: Transient error. (Vorübergehender Fehler.) Warten Sie die Abkühlzeit ab. Der Einbindungsversuch sollte nach einiger Zeit erfolgreich sein. Weitere Informationen finden Sie im Artikel [TPM-Grundlagen](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Netzwerkfehler

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Ursache: Allgemeine Netzwerkzeitüberschreitung beim Versuch, das Gerät bei DRS zu registrieren
   - Lösung: Überprüfen Sie die Netzwerkverbindung zu `https://enterpriseregistration.windows.net`.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Ursache: Servername oder Adresse konnte nicht aufgelöst werden.
   - Lösung: Überprüfen Sie die Netzwerkverbindung zu `https://enterpriseregistration.windows.net`. Stellen Sie sicher, dass die DNS-Auflösung für den Hostnamen im Netzwerk und auf dem Gerät korrekt ist.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Ursache: Die Verbindung zum Server wurde abgebrochen.
   - Lösung: Versuchen Sie es nach einiger Zeit erneut, oder versuchen Sie, die Einbindung über eine alternative stabile Netzwerkadresse vorzunehmen.

##### <a name="federated-join-server-errors"></a>Serverfehler bei der Verbundeinbindung

| Serverfehlercode | Serverfehlermeldung | Mögliche Ursachen | Lösung |
| --- | --- | --- | --- |
| DirectoryError | Ihre Anforderung wird vorübergehend gedrosselt. Versuchen Sie es nach 300 Sekunden erneut. | Erwarteter Fehler. Möglicherweise aufgrund von mehreren Registrierungsanfragen in schneller Folge. | Wiederholen Sie die Einbindung nach der Abkühlzeit |

##### <a name="sync-join-server-errors"></a>Serverfehler bei „sync join“

| Serverfehlercode | Serverfehlermeldung | Mögliche Ursachen | Lösung |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: Mandant <UUID> wurde nicht gefunden. Dieser Fehler kann auftreten, wenn es für den Mandanten keine aktiven Abonnements gibt. Wenden Sie sich an Ihren Abonnement-Administrator. | Mandanten-ID im SCP-Objekt ist falsch | Stellen Sie sicher, dass das SCP-Objekt mit der korrekten Azure AD-Mandanten-ID und aktiven Abonnements konfiguriert und im Mandanten vorhanden ist. |
| DirectoryError | Das Geräteobjekt mit der angegebenen ID wurde nicht gefunden. | Erwarteter Fehler bei „sync join“. Das Geräteobjekt wurde nicht von AD zu Azure AD synchronisiert | Warten Sie, bis die Azure AD Connect-Synchronisierung abgeschlossen ist und das Problem beim nächsten Einbindungsversuch nach Abschluss der Synchronisierung gelöst wird |
| AuthenticationError | Die Überprüfung der SID des Zielcomputers | Das Zertifikat auf dem Azure AD-Gerät stimmt nicht mit dem Zertifikat überein, mit dem der Blob während „sync join“ signiert wurde. Dieser Fehler bedeutet in der Regel, dass die Synchronisierung noch nicht abgeschlossen ist. |  Warten Sie, bis die Azure AD Connect-Synchronisierung abgeschlossen ist und das Problem beim nächsten Einbindungsversuch nach Abschluss der Synchronisierung gelöst wird |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Schritt 5: Sammeln Sie Protokolle, und wenden Sie sich an den Microsoft-Support

Laden Sie die Datei „Auth.zip“ von [https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH](https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH) herunter.

1. Entpacken Sie die Datei, und benennen Sie die enthaltenen Dateien **start-auth.txt** und **stop-auth.txt** in **start-auth.cmd** und **stop-auth.cmd** um.
1. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten die Datei **start-auth.cmd** aus.
1. Wechseln Sie über „Konto wechseln“ zu einer anderen Sitzung mit dem Problembenutzer.
1. Reproduzieren Sie das Problem.
1. Wechseln Sie über „Konto wechseln“ wieder zurück zur Administratorsitzung, in der die Ablaufverfolgung ausgeführt wird.
1. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten die Datei **stop-auth.cmd** aus.
1. Komprimieren Sie den Ordner **Authlogs** im Ordner, in dem die Skripts ausgeführt wurden, und übermitteln Sie ihn.

## <a name="troubleshoot-post-join-issues"></a>Beheben von Problemen nach der Einbindung

### <a name="retrieve-the-join-status"></a>Abrufen des Beitrittsstatus

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: JA und AzureADPrt: YES

Diese Felder geben an, ob der Benutzer sich bei der Anmeldung beim Gerät erfolgreich bei Azure AD authentifiziert hat.
Wenn die Werte **NO** lauten, kann dies die folgenden Ursachen haben:

- Dem Gerät wurde bei der Registrierung ein ungültiger Speicherschlüssel im TPM zugewiesen (prüfen Sie den KeySignTest bei der Ausführung mit erhöhten Rechten).
- Alternative Anmelde-ID.
- HTTP-Proxy nicht gefunden.

## <a name="known-issues"></a>Bekannte Probleme
- Unter „Einstellungen -> Konten -> Auf Arbeits- oder Schulkonto zugreifen“ werden für Geräte mit Hybrid-Azure AD-Einbindung möglicherweise zwei verschiedene Konten angezeigt, wenn Sie mit mobilen Hotspots oder externen WLAN-Netzwerken verbunden sind: ein Konto für Azure AD und eins für das lokale AD. Dies ist nur ein Problem mit der Benutzeroberfläche und hat keinerlei Auswirkungen auf die Funktionalität.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit [Problembehandlung von Geräten mit dem Befehl „dsregcmd“](troubleshoot-device-dsregcmd.md) fort.

Bei Fragen lesen Sie unter [Häufig gestellte Fragen zur Geräteverwaltung](faq.md) nach.