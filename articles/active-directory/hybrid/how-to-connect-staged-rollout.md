---
title: 'Azure AD Connect: Cloudauthentifizierung über gestaffelten Rollout | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, wie Sie mithilfe eines gestaffelten Rollouts eine Migration von der Verbundauthentifizierung zur Cloudauthentifizierung durchführen.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd1c7f5dec57127f92da52be908bd6faa2c90e85
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500222"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrieren zur Cloudauthentifizierung mithilfe eines gestaffelten Rollouts (Vorschau)

Gestaffelte Rollouts ermöglichen Ihnen das selektive Testen von Benutzergruppen mit Cloudauthentifizierungsfunktionen (wie Azure AD Multi-Factor Authentication (MFA), bedingter Zugriff, Identity Protection für kompromittierte Anmeldeinformationen, Identity Governance usw.) vor der Umstellung Ihrer Domänen.  In diesem Artikel wird erläutert, wie Sie den Umstieg vornehmen. Bevor Sie mit dem gestaffelten Rollout beginnen, sollten Sie jedoch die Auswirkungen berücksichtigen, wenn mindestens eine der folgenden Bedingungen zutrifft:
    
-  Sie verwenden zurzeit einen lokalen Server für mehrstufige Authentifizierung. 
-  Sie verwenden Smartcards für die Authentifizierung. 
-  Ihr aktueller Server bietet bestimmte Features, die sich ausschließlich auf Verbundauthentifizierung beziehen.

Bevor Sie dieses Feature testen, empfehlen wir Ihnen, unseren Leitfaden zum Auswählen der richtigen Authentifizierungsmethode zu lesen. Weitere Informationen finden Sie in der Tabelle „Methodenvergleich“ unter [Auswählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](./choose-ad-authn.md#comparing-methods).

Einen Überblick über das Feature finden Sie hier: „Azure Active Directory: Was sind gestaffelte Rollouts?“ (Video):

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Voraussetzungen

-   Sie verfügen über einen Azure AD-Mandanten (Azure Active Directory) mit Verbunddomänen.

-   Sie haben sich entschieden, zu einer von zwei Optionen zu wechseln:
    - **Option A** - *Kennworthashsynchronisierung* + *Nahtloses einmaliges Anmelden (Single Sign-On, SSO)* .  Weitere Informationen finden Sie unter [Was ist Kennworthashsynchronisierung?](whatis-phs.md) und [Nahtlose einmalige Anmeldung mit Azure Active Directory](how-to-connect-sso.md).
    - **Option B** - *Passthrough-Authentifizierung* + *Nahtloses SSO*.  Weitere Informationen finden Sie unter [Worum handelt es sich bei der Passthrough-Authentifizierung?](how-to-connect-pta.md).  
    
    Obwohl *nahtloses SSO* optional ist, empfiehlt es sich, das nahtlose einmalige Anmelden zu aktivieren, damit Benutzer, die in die Domäne eingebundene Computer innerhalb des Unternehmensnetzwerks verwenden, von einer automatischen Anmeldung profitieren.

-   Sie haben alle geeigneten Richtlinien für Mandantenbranding und für bedingten Zugriff konfiguriert, die Sie für Benutzer benötigen, die zur Cloudauthentifizierung migriert werden.

-   Wenn Sie Azure AD Multi-Factor Authentication verwenden möchten, wird empfohlen, die [kombinierte Registrierung für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) und Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) zu verwenden, damit Ihre Benutzer ihre Authentifizierungsmethoden einmalig registrieren können. Hinweis: Bei Verwendung von SSPR zum Zurücksetzen oder Ändern des Kennworts mithilfe der Seite MyProfile im gestaffeltem Rollout muss Azure AD Connect den neuen Kennworthash synchronisieren, was nach dem Zurücksetzen bis zu 2 Minuten dauern kann.

-   Um dieses Feature für gestaffelten Rollout verwenden zu können, müssen Sie globaler Administrator für Ihren Mandanten sein.

-   Um *nahtloses einmaliges Anmelden* für eine bestimmte Active Directory-Gesamtstruktur aktivieren zu können, müssen Sie Domänenadministrator sein.

-  Wenn Sie Azure AD Hybrid oder Azure AD Join bereitstellen, müssen Sie ein Upgrade auf das Update Windows 10 1903 durchführen.


## <a name="supported-scenarios"></a>Unterstützte Szenarios

Die folgenden Szenarien werden für gestaffelten Rollout unterstützt. Das Feature funktioniert nur für:

- Benutzer, die für Azure AD mithilfe von Azure AD Connect bereitgestellt werden. Es gilt nicht für reine Cloudbenutzer.

- Datenverkehr bei der Benutzeranmeldung in Browsern und auf Clients mit *moderner Authentifizierung*. Für Anwendungen oder Clouddienste mit Legacyauthentifizierung werden wieder Verbundauthentifizierungsflows verwendet. Ein Beispiel ist Exchange Online mit deaktivierter moderner Authentifizierung oder Outlook 2010, das keine moderne Authentifizierung unterstützt.
- Die Gruppengröße ist derzeit auf 50.000 Benutzer beschränkt.  Wenn Sie über Gruppen verfügen, die größer als 50.000 Benutzer sind, empfiehlt es sich, diese Gruppe für ein gestaffeltes Rollout in mehrere Gruppen aufzuteilen.

## <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Die folgenden Szenarien werden für gestaffelten Rollout nicht unterstützt:

- Für Anwendungen oder Clouddienste wird die Legacyauthentifizierung verwendet, z. B. POP3 und SMTP.

- Bestimmte Anwendungen senden bei der Authentifizierung den Abfrageparameter „domain_hint“ an Azure AD. Diese Flows werden fortgesetzt, und für den gestaffelten Rollout aktivierte Benutzer verwenden weiterhin den Verbund für die Authentifizierung.

<!-- -->

- Administratoren können den Rollout der Cloudauthentifizierung mithilfe von Sicherheitsgruppen ausführen. Um Synchronisierungslatenzen zu vermeiden, wenn Sie lokale Active Directory-Sicherheitsgruppen verwenden, empfehlen wir Ihnen, Cloudsicherheitsgruppen zu verwenden. Die folgenden Bedingungen gelten:

    - Pro Feature können maximal 10 Gruppen verwendet werden. Das heißt, Sie können jeweils 10 Gruppen für *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* und *nahtloses SSO* verwenden.
    - Geschachtelte Gruppen werden *nicht unterstützt*. Die öffentliche Vorschau weist den gleichen Geltungsbereich auf.
    - Dynamische Gruppen werden für den gestaffelten Rollout *nicht unterstützt*.
    - Kontaktobjekte innerhalb der Gruppe blockieren das Hinzufügen der Gruppe.

- Die endgültige Umstellung von Verbundauthentifizierung auf Cloudauthentifizierung muss weiterhin mithilfe von Azure AD Connect oder PowerShell erfolgen. Ein gestaffelter Rollout stellt Domänen nicht von Verbunddomänen auf verwaltete Domänen um.  Weitere Informationen zur Domänenumstellung finden Sie unter [Migrieren vom Verbund zur Kennworthashsynchronisierung für Azure Active Directory](plan-migrate-adfs-password-hash-sync.md) und [Migrieren vom Verbund zur Passthrough-Authentifizierung für Azure Active Directory](plan-migrate-adfs-pass-through-authentication.md).



- Wenn Sie zum ersten Mal eine Sicherheitsgruppe für den gestaffelten Rollout hinzufügen, besteht eine Einschränkung auf 200 Benutzer, um ein UX-Timeout zu vermeiden. Nachdem Sie die Gruppe hinzugefügt haben, können Sie ihr nach Bedarf weitere Benutzer hinzufügen.

- Wenn Benutzer sich im gestaffelten Rollout befinden und EnforceCloudPasswordPolicyForPasswordSyncedUsers aktiviert ist, ist die Kennwortablaufrichtlinie auf 90 Tage festgelegt und kann nicht angepasst werden. 


## <a name="get-started-with-staged-rollout"></a>Erste Schritte mit gestaffeltem Rollout

Zum Testen der *Kennworthashsynchronisierung* melden Sie sich mithilfe des gestaffelten Rollouts an und befolgen die Anweisungen für die Vorbereitung im nächsten Abschnitt.

Weitere Informationen dazu, welche PowerShell-Cmdlets verwendet werden sollten, finden Sie unter [Azure AD 2.0 Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Vorbereitende Schritte für die Kennworthashsynchronisierung

1. Aktivieren Sie die *Kennworthashsynchronisierung* über die Seite [Optionale Features](how-to-connect-install-custom.md#optional-features) in Azure AD Connect. 

   ![Screenshot der Seite „Optionale Features“ in Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Stellen Sie sicher, dass ein vollständiger Zyklus der *Kennworthashsynchronisierung* ausgeführt wurde, damit alle Kennworthashes der Benutzer mit Azure AD synchronisiert wurden. Zum Überprüfen des Status der *Kennworthashsynchronisierung* können Sie die PowerShell-Diagnose unter [Problembehandlung der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](tshoot-connect-password-hash-synchronization.md) verwenden.

   ![Screenshot des AADConnect-Problembehandlungsprotokolls](./media/how-to-connect-staged-rollout/sr2.png)

Wenn Sie *Passthrough-Authentifizierung* testen möchten, melden Sie sich mithilfe des gestaffelten Rollouts an, und aktivieren Sie diese dann, indem Sie die Anweisungen zu den vorbereitenden Schritten im nächsten Abschnitt befolgen.

## <a name="pre-work-for-pass-through-authentication"></a>Vorbereitende Schritte für Passthrough-Authentifizierung

1. Identifizieren Sie einen Server unter Windows Server 2012 R2 (oder höher), auf dem der *Passthrough-Authentifizierungs-Agent* ausgeführt werden soll. 

   Wählen Sie *nicht* den Azure AD Connect-Server aus.  Stellen Sie sicher, dass der Server in die Domäne eingebunden ist, die ausgewählten Benutzer bei Active Directory authentifizieren und mit Azure AD über ausgehende Ports/URLs kommunizieren kann. Weitere Informationen finden Sie im Abschnitt „Schritt 1: Überprüfen der Voraussetzungen“ in [Schnellstart: Nahtloses einmaliges Anmelden mit Azure AD](how-to-connect-sso-quick-start.md).

1. [Laden Sie den Microsoft Azure AD Connect-Authentifizierungs-Agent herunter](https://aka.ms/getauthagent), und installieren Sie ihn auf dem Server. 

1. Installieren Sie zusätzliche Authentifizierungs-Agents auf anderen Servern, um [Hochverfügbarkeit](how-to-connect-sso-quick-start.md) zu ermöglichen.

1. Stellen Sie sicher, dass Sie die Einstellungen für die intelligente Sperre ([Smart Lockout](../authentication/howto-password-smart-lockout.md)) entsprechend konfiguriert haben. Dadurch wird sichergestellt, dass die lokalen Active Directory-Konten Ihrer Benutzer nicht von böswilligen Akteuren gesperrt werden.

Es wird empfohlen, *nahtloses einmaliges Anmelden* unabhängig von der für den gestaffelten Rollout ausgewählten Anmeldemethode (*Kennworthashsynchronisierung* oder *Passthrough-Authentifizierung*) zu aktivieren. Um *nahtlose einmaliges Anmelden* zu aktivieren, befolgen Sie die Anweisungen zu den vorbereitenden Schritten im nächsten Abschnitt.

## <a name="pre-work-for-seamless-sso"></a>Vorbereitende Schritte für nahtloses einmaliges Anmelden

Aktivieren Sie *nahtloses einmaliges Anmelden* für die Active Directory-Gesamtstrukturen mithilfe von PowerShell. Wenn Sie über mehrere Active Directory-Gesamtstrukturen verfügen, aktivieren Sie das gleiche Feature für jede Gesamtstruktur einzeln. *Nahtloses einmaliges Anmelden* wird nur für Benutzer ausgelöst, die für das gestaffelte Rollout ausgewählt wurden. Dies wirkt sich nicht auf Ihre vorhandene Verbundeinrichtung aus.

Aktivieren Sie *nahtloses einmaliges Anmelden*, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich beim Azure AD Connect-Server an.

2. Navigieren Sie zum Ordner *%Programme%\\Microsoft Azure Active Directory Connect*.

3. Importieren Sie das PowerShell-Modul für *nahtloses SSO*, indem Sie den folgenden Befehl ausführen: 

   `Import-Module .\AzureADSSO.psd1`

4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem wird ein Bereich geöffnet, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.

5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dieser Befehl zeigt eine Liste der Active Directory-Gesamtstrukturen (siehe Liste „Domänen“) an, für die diese Funktion aktiviert wurde. Standardmäßig ist die Option auf der Mandantenebene auf „false“ festgelegt.

   ![Beispiel der Windows PowerShell-Ausgabe](./media/how-to-connect-staged-rollout/sr3.png)

6. Rufen Sie `$creds = Get-Credential` auf. Geben Sie an der Eingabeaufforderung die Anmeldeinformationen des Domänenadministrators für die vorgesehene Active Directory-Gesamtstruktur ein.

7. Rufen Sie `Enable-AzureADSSOForest -OnPremCredentials $creds` auf. Mit diesem Befehl wird das Computerkonto „AZUREADSSOACC“ vom lokalen Domänencontroller für die Active Directory-Gesamtstruktur erstellt, das für *nahtloses SSO* erforderlich ist.

8. *Nahtloses SSO* erfordert, dass sich URLs in der Intranetzone befinden. Informationen zum Bereitstellen dieser URLs mithilfe von Gruppenrichtlinien finden Sie unter [Schnellstart: Nahtloses einmaliges Anmelden mit Azure AD](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature).

9. Sie können auch unsere [Bereitstellungspläne](https://aka.ms/SeamlessSSODPDownload) für *nahtloses SSO* herunterladen.

## <a name="enable-staged-rollout"></a>Aktivieren von gestaffeltem Rollout

Um den Rollout eines bestimmten Features (*Passthrough-Authentifizierung*, *Kennworthashsynchronisierung* oder *nahtloses SSO*) für ausgewählte Benutzer in einer Gruppe auszuführen, befolgen Sie die Anweisungen in den nächsten Abschnitten.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Aktivieren eines gestaffelten Rollouts eines bestimmten Features für Ihren Mandanten

Sie können Rollouts für eine der folgenden Optionen ausführen:

- **Option A** - *Kennworthashsynchronisierung* + *Nahtloses SSO*
- **Option B** - *Passthrough-Authentifizierung* + *Nahtloses SSO*
- **Nicht unterstützt** - *Kennworthashsynchronisierung* + *Passthrough-Authentifizierung* + *Nahtloses SSO*

Gehen Sie folgendermaßen vor:

1. Melden Sie sich am [Azure AD Portal](https://aka.ms/stagedrolloutux) an, um auf die Vorschau-UX zuzugreifen.

2. Klicken Sie auf den Link **Gestaffelten Rollout für verwaltete Benutzeranmeldung aktivieren (Vorschau)** .

   Wenn Sie z.B. *Option A* aktivieren möchten, stellen Sie den Schieberegler für die Steuerelemente **Kennworthashsynchronisierungs** und **Nahtloses SSO** auf die Position **Ein**, wie in den folgenden Abbildungen gezeigt.

   ![Die Azure AD Connect-Seite](./media/how-to-connect-staged-rollout/sr4.png)

   ![Die Seite „Features für gestaffelten Rollout aktivieren (Vorschau)“](./media/how-to-connect-staged-rollout/sr5.png)

3. Fügen Sie dem Feature die Gruppen hinzu, um *Passthrough-Authentifizierung* und *nahtloses SSO* zu aktivieren. Um ein UX-Timeout zu vermeiden, stellen Sie sicher, dass die Sicherheitsgruppen anfangs nicht mehr als 200 Mitglieder enthalten.

   ![Die Seite „Gruppen für Kennworthashsynchronisierung verwalten (Vorschau)“](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Die Mitglieder einer Gruppe werden automatisch für den gestaffelten Rollout aktiviert. Geschachtelte und dynamische Gruppen werden für gestaffelte Rollouts nicht unterstützt.
   >Wenn Sie eine neue Gruppe hinzufügen, werden die Benutzer in der Gruppe (bis zu 200 Benutzer für eine neue Gruppe) aktualisiert, damit die verwaltete Authentifizierung sofort verwendet werden kann. Wenn Sie eine Gruppe bearbeiten (Benutzer hinzufügen oder entfernen), kann es bis zu 24 Stunden dauern, bis die Änderungen wirksam werden.
   >Das nahtlose einmalige Anmelden gilt nur, wenn sich Benutzer in der Gruppe „Nahtloses einmaliges Anmelden“ und auch in einer PTA- oder Kennworthashsynchronisierungs-Gruppe befinden.

## <a name="auditing"></a>Überwachung

Wir haben Überwachungsereignisse für die verschiedenen Aktionen aktiviert, die wir für gestaffelte Rollouts ausführen:

- Überwachungsereignis: Sie aktivieren den gestaffelten Rollout für *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* oder *nahtloses SSO*.

  >[!NOTE]
  >Es wird ein Überwachungsereignis protokolliert, wenn *nahtloses SSO* mithilfe des gestaffelten Rollouts aktiviert wird.

  ![Bereich „Rolloutrichtlinie für Feature erstellen“, Registerkarte „Aktivität“](./media/how-to-connect-staged-rollout/sr7.png)

  ![Bereich „Rolloutrichtlinie für Feature erstellen“, Registerkarte „Geänderte Eigenschaften“](./media/how-to-connect-staged-rollout/sr8.png)

- Überwachungsereignis: Eine Gruppe wird dem Feature *Kennworthashsynchronisierung*, *Passthrough-Authentifizierung* oder *nahtloses SSO* hinzugefügt.

  >[!NOTE]
  >Es wird ein Überwachungsereignis protokolliert, wenn eine Gruppe der *Kennworthashsynchronisierung* für den gestaffelten Rollout hinzugefügt wird.

  ![Bereich „Gruppe zum Featurerollout hinzufügen“, Registerkarte „Aktivität“](./media/how-to-connect-staged-rollout/sr9.png)

  ![Bereich „Gruppe zum Featurerollout hinzufügen“, Registerkarte „Geänderte Eigenschaften“](./media/how-to-connect-staged-rollout/sr10.png)

- Überwachungsereignis: Ein der Gruppe hinzugefügter Benutzer wird für den gestaffelten Rollout aktiviert.

  ![Bereich „Benutzer zum Featurerollout hinzufügen“, Registerkarte „Aktivität“](media/how-to-connect-staged-rollout/sr11.png)

  ![Bereich „Benutzer zum Featurerollout hinzufügen“, Registerkarte „Ziel(e)“](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Überprüfen

Gehen Sie wie folgt vor, um die Anmeldung mit *Kennworthashsynchronisierung* oder *Passthrough-Authentifizierung* (Anmeldung mit Benutzername/Kennwort) zu testen:

1. Navigieren Sie im Extranet in einer privaten Browsersitzung zur Seite [Apps](https://myapps.microsoft.com), und geben Sie dann den UserPrincipalName (UPN) des Benutzerkontos ein, das für den gestaffelten Rollout ausgewählt wurde.

   Benutzer, die für einen gestaffelten Rollout vorgesehen sind, werden nicht auf Ihre Verbundanmeldeseite umgeleitet. Stattdessen werden sie aufgefordert, sich auf der Azure AD-Anmeldeseite mit Mandantenbranding anzumelden.

1. Stellen Sie sicher, dass die Anmeldung im [Azure AD-Bericht zu Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) erfolgreich angezeigt wird, indem Sie nach dem Benutzerprinzipalnamen (UserPrincipalName) filtern.

Gehen Sie wie folgt vor, um die Anmeldung mit *nahtlosem SSO* zu testen:

1. Navigieren Sie im Intranet in einer privaten Browsersitzung zur Seite [Apps](https://myapps.microsoft.com), und geben Sie dann den UserPrincipalName (UPN) des Benutzerkontos ein, das für den gestaffelten Rollout ausgewählt wurde.

   Benutzern, die für gestaffelten Rollout von *nahtlosem SSO* vorgesehen ist, wird die Meldung „Anmeldung wird versucht“ angezeigt, bevor sie automatisch angemeldet werden.

1. Stellen Sie sicher, dass die Anmeldung im [Azure AD-Bericht zu Anmeldeaktivitäten](../reports-monitoring/concept-sign-ins.md) erfolgreich angezeigt wird, indem Sie nach dem Benutzerprinzipalnamen (UserPrincipalName) filtern.

   Um Benutzeranmeldungen nachzuverfolgen, die weiterhin für Active Directory-Verbunddienste (AD FS) für ausgewählte Benutzer mit gestaffeltem Rollout auftreten, befolgen Sie die Anweisungen unter [Problembehandlung von AD FS: Ereignisse und Protokollierung](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Lesen Sie in der Dokumentation des Herstellers nach, wie dies für Drittanbieter von Verbunddiensten überprüft wird.

## <a name="remove-a-user-from-staged-rollout"></a>Entfernen eines Benutzers aus dem gestaffelten Rollout

Durch Entfernen eines Benutzers aus der Gruppe wird der gestaffelte Rollout für diesen Benutzer deaktiviert. Um das Feature für gestaffelten Rollout zu deaktivieren, stellen Sie den Schieberegler für das Steuerelement zurück in die Position **Aus**.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Kann ich dieses Feature in der Produktionsumgebung verwenden?**

A: Ja, Sie können dieses Feature in Ihrem Produktionsmandanten verwendet werden. Wir empfehlen Ihnen jedoch, es zunächst in Ihrem Testmandanten auszuprobieren.

**F: Kann dieses Feature verwendet werden, um eine permanente „Koexistenz“ aufrechtzuerhalten, bei der einige Benutzer Verbundauthentifizierung und andere Cloudauthentifizierung verwenden?**

A: Nein, dieses Feature ist für das Testen der Cloudauthentifizierung konzipiert. Nachdem Sie einige Gruppen von Benutzern erfolgreich getestet haben, sollten Sie zur Cloudauthentifizierung wechseln. Von der Verwendung eines dauerhaften gemischten Zustands wird abgeraten, weil dieser Ansatz zu unerwarteten Authentifizierungsflows führen könnte.

**F: Kann PowerShell verwendet werden, um einen gestaffelten Rollout auszuführen?**

A: Ja. Informationen zum Verwenden von PowerShell zum Ausführen eines gestaffelten Rollouts finden Sie unter [Azure AD Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD 2.0 Vorschau](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
