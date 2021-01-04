---
title: 'Azure Active Directory Connect: Problembehandlung beim nahtlosen einmaligen Anmelden | Microsoft-Dokumentation'
description: In diesem Thema wird beschrieben, wie Sie Probleme beim nahtlosen einmaligen Anmelden in Azure Active Directory behandeln können.
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60f23efa4f46849e1fe8b0ebe05cdd83ec16f49e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997680"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Problembehandlung beim nahtlosen einmaligen Anmelden mit Azure Active Directory

In diesem Artikel finden Sie Informationen zur Problembehandlung bei bekannten Problemen beim nahtlosen einmaligen Anmelden (nahtloses SSO) in Azure Active Directory (Azure AD).

## <a name="known-issues"></a>Bekannte Probleme

- Die Aktivierung der nahtlosen einmaligen Anmeldung kann in seltenen Fällen bis zu 30 Minuten dauern.
- Wenn Sie die nahtlose einmalige Anmeldung für Ihren Mandanten deaktivieren und dann erneut aktivieren, steht die Benutzeroberfläche zum einmaligen Anmelden Benutzern erst wieder zur Verfügung, nachdem ihre zwischengespeicherten Kerberos-Tickets, die im Normalfall 10 Stunden gültig sind, abgelaufen sind.
- Wenn nahtloses einmaliges Anmelden erfolgreich ausgeführt wurde, hat der Benutzer nicht die Möglichkeit, die Option **Angemeldet bleiben** auszuwählen. Aufgrund dieses Verhaltens funktionieren [Zuordnungsszenarien für SharePoint und OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nicht.
- Win32-Clients für Microsoft 365 (Outlook, Word, Excel usw.) ab Version 16.0.8730.xxxx werden mit einem nicht interaktiven Flow unterstützt. Andere Versionen werden nicht unterstützt. In diesen Versionen geben die Benutzer zur Anmeldung ihren Benutzernamen, aber kein Kennwort ein. Bei OneDrive müssen Sie das [OneDrive-Feature zur automatischen Konfiguration](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) aktivieren, um von einer automatischen Anmeldung profitieren zu können.
- Das nahtlose einmalige Anmelden funktioniert in Firefox nicht im privaten Modus.
- Dies gilt auch für den Internet Explorer, wenn der erweiterte Schutzmodus aktiviert ist.
- Das nahtlose einmalige Anmelden funktioniert nicht in mobilen Browsern unter iOS und Android.
- Wenn ein Benutzer in Active Directory einer zu großen Zahl von Gruppen angehört, ist das Kerberos-Ticket des Benutzers wahrscheinlich zu groß für die Verarbeitung. Dies führt dazu, dass das nahtlose einmalige Anmelden nicht erfolgreich ist. Azure AD-HTTPS-Anforderungen können Header mit einer maximalen Größe von 50 KB enthalten. Kerberos-Tickets müssen unter diesem Grenzwert liegen, um andere Azure AD-Artefakte (in der Regel 2-5 KB), z.B. Cookies, aufnehmen zu können. Wir empfehlen Ihnen, die Gruppenmitgliedschaften des Benutzers zu reduzieren und es erneut zu versuchen.
- Wenn Sie 30 oder mehr Active Directory-Gesamtstrukturen synchronisieren, kann die nahtlose einmalige Anmeldung nicht mit Azure AD Connect aktiviert werden. Zur Problembehebung können Sie die Funktion auf Ihrem Mandanten [manuell aktivieren](#manual-reset-of-the-feature).
- Wenn Sie die Dienst-URL von Azure AD (`https://autologon.microsoftazuread-sso.com`) nicht der Zone „Lokales Intranet“ hinzufügen, sondern der Zone „Vertrauenswürdige Sites“ *, können sich Benutzer nicht anmelden*.
- Das nahtlose einmalige Anmelden unterstützt die Verschlüsselungstypen AES256_HMAC_SHA1, AES128_HMAC_SHA1 und RC4_HMAC_MD5 für Kerberos. Es wird empfohlen, den Verschlüsselungstyp für das Konto „AzureADSSOAcc$“ auf „AES256_HMAC_SHA1“ oder einen der AES-Typen vs. RC4 festzulegen, um die Sicherheit zu erhöhen. Der Verschlüsselungstyp wird im „msDS-SupportedEncryptionTypes“-Attribut des Kontos in Ihrer Active Directory-Instanz gespeichert.  Wenn der Verschlüsselungstyp des Kontos „AzureADSSOAcc$“ auf „RC4_HMAC_MD5“ festgelegt ist und Sie ihn in einen der AES-Verschlüsselungstypen ändern möchten, stellen Sie sicher, dass Sie zuerst einen Rollover für den Kerberos-Entschlüsselungsschlüssel des Kontos „AzureADSSOAcc$“ (wie im [FAQ-Dokument](how-to-connect-sso-faq.md) unter der relevanten Frage erläutert) ausführen, weil andernfalls kein nahtloses einmaliges Anmelden erfolgt.
-  Wenn Sie über mehrere Gesamtstrukturen mit Gesamtstruktur-Vertrauensstellung verfügen und in einer der Gesamtstrukturen einmaliges Anmelden aktivieren, wird einmaliges Anmelden in allen vertrauenswürdigen Gesamtstrukturen aktiviert. Wenn Sie einmaliges Anmelden in einer Gesamtstruktur aktivieren, in der einmaliges Anmelden bereits aktiviert ist, erhalten Sie eine Fehlermeldung, die besagt, dass einmaliges Anmelden in der Gesamtstruktur bereits aktiviert ist.

## <a name="check-status-of-feature"></a>Überprüfen des Status des Features

Stellen Sie sicher, dass das nahtlose einmalige Anmelden für Ihren Mandanten noch **Aktiviert** ist. Um den Status zu überprüfen, können Sie zum Bereich **Azure AD Connect** im [Azure Active Directory Admin Center](https://aad.portal.azure.com/) navigieren.

![Azure Active Directory Admin Center: Bereich „Azure AD Connect“](./media/tshoot-connect-sso/sso10.png)

Klicken Sie, um alle AD-Gesamtstrukturen anzuzeigen, die für nahtloses SSO aktiviert wurden.

![Azure Active Directory Admin Center: Bereich für nahtloses einmaliges Anmelden](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Gründe für Anmeldefehler im Azure Active Directory Admin Center (Premium-Lizenz erforderlich)

Wenn Ihrem Mandanten eine Azure AD Premium-Lizenz zugeordnet ist, können Sie sich im [Azure Active Directory Admin Center](https://aad.portal.azure.com/) auch den [Bericht zu den Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) ansehen.

![Azure Active Directory Admin Center: Bericht zu Anmeldeaktivitäten](./media/tshoot-connect-sso/sso9.png)

Navigieren Sie im [Azure Active Directory Admin Center](https://aad.portal.azure.com/) zu **Azure Active Directory** > **Anmeldungen**, und klicken Sie auf die Anmeldeaktivität eines bestimmten Benutzers. Suchen Sie nach dem Feld **Code des Anmeldefehlers**. Ordnen Sie den Wert in diesem Feld mithilfe der folgenden Tabelle einer Ursache und einer Lösung zu:

|Anmeldefehler|Grund des Anmeldefehlers|Lösung
| --- | --- | ---
| 81001 | Das Kerberos-Ticket des Benutzers ist zu groß. | Reduzieren Sie die Gruppenmitgliedschaften des Benutzers, und versuchen Sie es erneut.
| 81002 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81003 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81004 | Fehler beim Versuch, die Kerberos-Authentifizierung durchzuführen. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81008 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81009 | Das Kerberos-Ticket des Benutzers kann nicht überprüft werden. | Informationen hierzu finden Sie in der [Checkliste zur Problembehandlung](#troubleshooting-checklist).
| 81010 | Fehler beim nahtlosen einmaligen Anmelden, da das Kerberos-Ticket des Benutzers abgelaufen oder ungültig ist. | Der Benutzer muss sich über ein in eine Domäne eingebundenes Gerät in Ihrem Unternehmensnetzwerk anmelden.
| 81011 | Das Benutzerobjekt konnte anhand der Informationen im Kerberos-Ticket des Benutzers nicht gefunden werden. | Verwenden Sie Azure AD Connect, um Benutzerinformationen in Azure AD zu synchronisieren.
| 81012 | Der Benutzer, der versucht, sich bei Azure AD anzumelden, unterscheidet sich von dem Benutzer, der beim Gerät angemeldet ist. | Der Benutzer muss sich über ein anderes Gerät anmelden.
| 81013 | Das Benutzerobjekt konnte anhand der Informationen im Kerberos-Ticket des Benutzers nicht gefunden werden. |Verwenden Sie Azure AD Connect, um Benutzerinformationen in Azure AD zu synchronisieren. 

## <a name="troubleshooting-checklist"></a>Checkliste zur Problembehandlung

Verwenden Sie die folgende Checkliste zur Behebung von Problemen in Bezug auf die nahtlose einmalige Anmeldung:

- Überprüfen Sie, ob die Funktion zum nahtlosen einmaligen Anmelden in Azure AD Connect aktiviert ist. Wenn Sie die Funktion (z.B. aufgrund eines blockierten Ports) nicht aktivieren können, vergewissern Sie sich, dass alle [Voraussetzungen](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) erfüllt sind.
- Wenn Sie für Ihren Mandanten sowohl [Azure AD Join](../devices/overview.md) als auch nahtlose einmalige Anmeldung aktiviert haben, vergewissern Sie sich, dass das Problem nicht bei Azure AD Join liegt. Einmaliges Anmelden von Azure AD Join hat Vorrang vor der nahtlosen einmaligen Anmeldung, wenn das Gerät sowohl bei Azure AD registriert als auch Mitglied einer Domäne ist. Bei einmaligem Anmelden von Azure AD Join wird dem Benutzer eine Anmeldekachel mit dem Text „Mit Windows verbunden“ angezeigt.
- Stellen Sie sicher, dass die Azure AD-URL (`https://autologon.microsoftazuread-sso.com`) Teil der Zoneneinstellung „Intranet“ des Benutzers ist.
- Stellen Sie sicher, dass das Unternehmensgerät mit der Active Directory-Domäne verknüpft ist. Das Gerät muss _nicht_[In Azure AD eingebunden](../devices/overview.md) sein, damit das nahtlose einmalige Anmelden funktioniert.
- Stellen Sie sicher, dass der Benutzer über ein Active Directory-Domänenkonto beim Gerät angemeldet ist.
- Stellen Sie sicher, dass das Benutzerkonto aus einer Active Directory-Gesamtstruktur stammt, in der nahtloses einmaliges Anmelden eingerichtet wurde.
- Stellen Sie sicher, dass das Gerät mit dem Unternehmensnetzwerk verbunden ist.
- Stellen Sie sicher, dass die Uhrzeit des Geräts mit der Uhrzeit von Active Directory und den Domänencontrollern synchronisiert ist und diese nicht mehr als fünf Minuten voneinander abweichen.
- Stellen Sie sicher, dass das Computerkonto `AZUREADSSOACC` in jeder AD-Gesamtstruktur, die SSO-fähig sein soll, vorhanden und aktiviert ist. Wenn das Computerkonto gelöscht wurde oder nicht vorhanden ist, können Sie [PowerShell-Cmdlets](#manual-reset-of-the-feature) verwenden, um es erneut zu erstellen.
- Listen Sie vorhandene Kerberos-Tickets auf dem Gerät mit dem Befehl `klist` über eine Eingabeaufforderung auf. Stellen Sie sicher, dass die für das Computerkonto `AZUREADSSOACC` ausgestellten Tickets vorhanden sind. Die Kerberos-Tickets von Benutzern sind normalerweise 10 Stunden gültig. Sie haben in Active Directory unter Umständen andere Einstellungen festgelegt.
- Wenn Sie die nahtlose einmalige Anmeldung für Ihren Mandanten deaktiviert und dann erneut aktiviert haben, steht die Benutzeroberfläche zum einmaligen Anmelden Benutzern erst wieder zur Verfügung, nachdem ihre zwischengespeicherten Kerberos-Tickets abgelaufen sind.
- Löschen Sie vorhandene Kerberos-Tickets auf dem Gerät mit dem Befehl `klist purge`, und wiederholen Sie den Vorgang.
- Überprüfen Sie die Konsolenprotokolle des Browsers (unter **Entwicklertools**), um zu ermitteln, ob Probleme vorliegen, die sich auf JavaScript beziehen.
- Sehen Sie sich außerdem die [Domänencontrollerprotokolle](#domain-controller-logs) an.

### <a name="domain-controller-logs"></a>Domänencontrollerprotokolle

Wenn die erfolgreiche Überwachung auf Ihrem Domänencontroller aktiviert ist, wird bei jeder nahtlosen einmaligen Anmeldung durch einen Benutzer ein Sicherheitseintrag im Ereignisprotokoll erfasst. Sie können diese Sicherheitsereignisse mithilfe der folgenden Abfrage abrufen. (Suchen Sie nach Ereignis **4769** in Verbindung mit dem Computerkonto **AzureADSSOAcc$** .)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Manuelle Zurücksetzung des Features

Wenn die Problembehandlung nicht hilft, können Sie die Funktion auf Ihrem Mandanten manuell zurücksetzen. Führen Sie diese Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Schritt 1: Importieren Sie das PowerShell-Modul „Nahtlose SSO“

1. Laden Sie zuerst [Azure AD PowerShell](/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Importieren Sie das PowerShell-Modul für nahtloses SSO mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Schritt 2: Rufen Sie die Liste der Active Directory-Gesamtstrukturen ab, für die nahtloses einmaliges Anmelden aktiviert wurde.

1. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Geben Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten an, wenn Sie dazu aufgefordert werden.
2. Rufen Sie `Get-AzureADSSOStatus` auf. Dadurch erhalten Sie die Liste der Active Directory-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Schritt 3: Deaktivieren Sie nahtloses SSO für jede Active Directory-Gesamtstruktur, in der Sie die Funktion eingerichtet haben.

1. Rufen Sie `$creds = Get-Credential` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene Active Directory-Gesamtstruktur ein.

   > [!NOTE]
   >Der Benutzername der Anmeldeinformationen des Domänenadministrators muss im Namensformat für das SAM-Konto („contoso\johndoe“ oder „contoso.com\johndoe“) eingegeben werden. Wir verwenden den Domänenteil des Benutzernamens, um den Domänencontroller des Domänenadministrators mithilfe von DNS zu suchen.

   >[!NOTE]
   >Das verwendete Domänenadministratorkonto darf kein Mitglied der Gruppe „Geschützte Benutzer“ sein. Andernfalls schlägt der Vorgang fehl.

2. Rufen Sie `Disable-AzureADSSOForest -OnPremCredentials $creds` auf. Mit diesem Befehl wird das Computerkonto `AZUREADSSOACC` vom lokalen Domänencontroller für diese spezifische Active Directory-Gesamtstruktur entfernt.
3. Wiederholen Sie die vorhergehenden Schritte für Active Directory-Gesamtstruktur, in der Sie die Funktion eingerichtet haben.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Schritt 4: Aktivieren Sie nahtloses SSO für jede Active Directory-Gesamtstruktur.

1. Rufen Sie `Enable-AzureADSSOForest` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene Active Directory-Gesamtstruktur ein.

   > [!NOTE]
   >Der Benutzername der Anmeldeinformationen des Domänenadministrators muss im Namensformat für das SAM-Konto („contoso\johndoe“ oder „contoso.com\johndoe“) eingegeben werden. Wir verwenden den Domänenteil des Benutzernamens, um den Domänencontroller des Domänenadministrators mithilfe von DNS zu suchen.

   >[!NOTE]
   >Das verwendete Domänenadministratorkonto darf kein Mitglied der Gruppe „Geschützte Benutzer“ sein. Andernfalls schlägt der Vorgang fehl.

2. Wiederholen Sie die vorhergehenden Schritte für Active Directory-Gesamtstruktur, in der Sie die Funktion einrichten möchten.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Schritt 5: Aktivieren Sie das Feature für Ihren Mandanten.

Zum Aktivieren des Features für Ihren Mandanten rufen Sie `Enable-AzureADSSO -Enable $true` auf.
