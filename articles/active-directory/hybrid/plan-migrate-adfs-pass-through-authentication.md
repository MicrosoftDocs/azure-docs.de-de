---
title: 'Azure AD Connect: Migrieren vom Verbund zu PTA für Azure AD'
description: Dieser Artikel enthält Informationen zum Umstellen Ihrer Hybrididentitätsumgebung vom Verbund auf die Passthrough-Authentifizierung.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10c4c0e6e40636e4803e054155d6fdaa12a9366
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858551"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrieren vom Verbund zur Passthrough-Authentifizierung für Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie Ihre Organisationsdomänen von Active Directory-Verbunddienste (AD FS) auf die Passthrough-Authentifizierung umstellen.

> [!NOTE]
> Das Ändern der Authentifizierungsmethode erfordert Planung, Tests und potenzielle Ausfallzeiten. Das [gestaffelte Rollout](how-to-connect-staged-rollout.md) ist eine alternative Möglichkeit zum Testen und schrittweisen Migrieren von der Verbund- zur Cloudauthentifizierung mithilfe der Passthrough-Authentifizierung.
> 
> Wenn Sie einen gestaffelten Rollout planen, sollten Sie daran denken, die Features für den gestaffelten Rollout zu deaktivieren, nachdem Sie den Übergang abgeschlossen haben.  Weitere Informationen finden Sie unter [Migrieren zur Cloudauthentifizierung mithilfe eines gestaffelten Rollouts](how-to-connect-staged-rollout.md).


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Voraussetzungen für die Migration zur Passthrough-Authentifizierung

Für die Migration von AD FS zur Passthrough-Authentifizierung gelten die folgenden Voraussetzungen.

### <a name="update-azure-ad-connect"></a>Aktualisieren von Azure AD Connect

Damit Sie die Schritte zur Umstellung auf die Verwendung der Passthrough-Authentifizierung erfolgreich ausführen können, müssen Sie über [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 oder höher verfügen. Mit der Version Azure AD Connect 1.1.819.0 wurde die Durchführung der Anmeldekonvertierung erheblich geändert. Der zeitliche Gesamtaufwand für die Migration von AD FS zur Cloudauthentifizierung beträgt in dieser Version nicht mehr mehrere Stunden, sondern liegt nur noch im Minutenbereich.

> [!IMPORTANT]
> Unter Umständen lesen Sie in veralteten Dokumentationen, Tools und Blogs noch, dass eine Benutzerkonvertierung erforderlich ist, wenn Sie Domänen von der Verbundidentität auf die verwaltete Identität umstellen. Das *Konvertieren von Benutzern* muss aber nicht mehr durchgeführt werden. Microsoft arbeitet an der Aktualisierung der Dokumentation und der Tools, damit diese Veränderung entsprechend widergespiegelt wird.

Führen Sie die Schritte unter [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](./how-to-upgrade-previous-version.md) aus, um Azure AD Connect zu aktualisieren.

### <a name="plan-authentication-agent-number-and-placement"></a>Planen der Anzahl und Platzierung von Authentifizierungs-Agents

Für die Passthrough-Authentifizierung müssen auf dem Azure AD Connect-Server und Ihrem lokalen Computer, auf dem Windows Server ausgeführt wird, einfache Agents bereitgestellt werden. Installieren Sie die Agents so nah wie möglich bei Ihren Active Directory-Domänencontrollern, um die Latenz zu verringern.

Für die meisten Kunden reichen zwei oder drei Authentifizierungs-Agents aus, um Hochverfügbarkeit und die erforderliche Kapazität bereitzustellen. Für einen Mandanten können maximal zwölf Agents registriert werden. Der erste Agent wird immer auf dem Azure AD Connect-Server selbst installiert. Informationen zu Einschränkungen und Bereitstellungsoptionen für Agents finden Sie unter [Azure AD-Passthrough-Authentifizierung: Aktuelle Einschränkungen](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Planen der Migrationsmethode

Sie können zwischen zwei Methoden wählen, um die Migration von der Verbundidentitätsverwaltung zur Passthrough-Authentifizierung und zum einmaligen Anmelden (Single Sign-On, SSO) durchzuführen. Welche Methode Sie verwenden, hängt davon ab, wie Ihre AD FS-Instanz ursprünglich konfiguriert wurde.

* **Azure AD Connect**: Wenn Sie AD FS ursprünglich mit Azure AD Connect konfiguriert haben, *müssen* Sie zur Passthrough-Authentifizierung wechseln, indem Sie den Azure AD Connect-Assistenten verwenden.

   ‎Azure AD Connect führt automatisch das Cmdlet **Set-MsolDomainAuthentication** aus, wenn Sie die Anmeldemethode für Benutzer ändern. Azure AD Connect hebt den Verbund aller verifizierten Verbunddomänen Ihres Azure AD-Mandanten automatisch auf.

   > [!NOTE]
   > Falls Sie ursprünglich Azure AD Connect zum Konfigurieren von AD FS genutzt haben, können Sie es derzeit nicht vermeiden, dass alle Domänen Ihres Mandanten aus dem Verbund entfernt werden, wenn Sie die Benutzeranmeldung auf Passthrough-Authentifizierung umstellen.
‎
* **Azure AD Connect mit PowerShell**: Sie können diese Methode nur verwenden, wenn Sie für die ursprüngliche Konfiguration von AD FS nicht Azure AD Connect verwendet haben. Bei dieser Option müssen Sie noch so vorgehen, dass Sie die Methode für die Benutzeranmeldung über den Azure AD Connect-Assistenten ändern. Der Hauptunterschied bei dieser Option ist, dass der Assistent das Cmdlet **Set-MsolDomainAuthentication** nicht automatisch ausführt. Sie haben bei dieser Option die volle Kontrolle darüber, welche Domänen konvertiert werden und in welcher Reihenfolge dies durchgeführt wird.

Führen Sie die Schritte in den folgenden Abschnitten aus, um sich darüber zu informieren, welche Methode Sie verwenden sollten.

#### <a name="verify-current-user-sign-in-settings"></a>Überprüfen der aktuellen Benutzeranmeldungseinstellungen

1. Melden Sie sich mit einem globalen Administratorkonto am [Azure AD-Portal](https://aad.portal.azure.com/) an.
2. Überprüfen Sie im Abschnitt **Benutzeranmeldung** die folgenden Einstellungen:
   * **Verbund** ist auf **Aktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Deaktiviert** festgelegt.
   * **Passthrough-Authentifizierung** ist auf **Deaktiviert** festgelegt.

   ![Screenshot: Einstellungen im Azure AD Connect-Abschnitt zur Benutzeranmeldung](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Überprüfen der Konfiguration des Verbunds

1. Öffnen Sie auf Ihrem Azure AD Connect-Server die Anwendung Azure AD Connect. Wählen Sie **Konfigurieren** aus.
2. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Aktuelle Konfiguration anzeigen** und anschließend **Weiter**.<br />
 
   ![Screenshot: Option „Aktuelle Konfiguration anzeigen“ auf der Seite „Weitere Aufgaben“](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. Scrollen Sie unter **Zusätzliche Tasks > Verbund verwalten** zu **Active Directory-Verbunddienste (AD FS)** .<br />

   * Wenn die AD FS-Konfiguration in diesem Abschnitt angezeigt wird, können Sie mit Sicherheit davon ausgehen, dass AD FS ursprünglich mit Azure AD Connect konfiguriert wurde. Sie können Ihre Domänen von Verbundidentität auf die verwaltete Identität umstellen, indem Sie die Azure AD Connect-Option **Benutzeranmeldung ändern** verwenden. Weitere Informationen zum Prozess finden Sie im Abschnitt **Option A: Konfigurieren der Passthrough-Authentifizierung mit Azure AD Connect**.
   * Wenn AD FS in den aktuellen Einstellungen nicht aufgeführt ist, müssen Sie Ihre Domänen per PowerShell manuell von der Verbundidentität auf die verwaltete Identität umstellen. Weitere Informationen zu diesem Prozess finden Sie im Abschnitt **Option B: Wechseln vom Verbund zur Passthrough-Authentifizierung mit Azure AD Connect und PowerShell**.

### <a name="document-current-federation-settings"></a>Dokumentieren der aktuellen Verbundeinstellungen

Führen Sie das Cmdlet **Get-MsolDomainFederationSettings** aus, um nach Ihren aktuellen Verbundeinstellungen zu suchen:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Beispiel:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Überprüfen Sie alle Einstellungen, die für Ihren Verbundentwurf ggf. angepasst wurden, und die Bereitstellungsdokumentation. Suchen Sie vor allem nach Anpassungen in **PreferredAuthenticationProtocol**, **SupportsMfa** und **PromptLoginBehavior**.

Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

* [Active Directory Federation Services prompt=login parameter support](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) (Active Directory-Verbunddienste: Unterstützung für Parameter „prompt=login“)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Wenn **SupportsMfa** auf **True** festgelegt ist, verwenden Sie eine lokale Lösung für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), um eine zweite Faktorabfrage in den Benutzerauthentifizierungsflow einzufügen. Diese Einrichtung funktioniert für Azure AD-Authentifizierungsszenarien nicht mehr. 
>
> Verwenden Sie stattdessen den cloudbasierten Dienst Azure AD Multi-Factor Authentication, um dieselbe Funktion zu erzielen. Evaluieren Sie Ihre Anforderungen an die mehrstufige Authentifizierung sorgfältig, bevor Sie fortfahren. Stellen Sie vor dem Konvertieren Ihrer Domänen sicher, dass Sie wissen, wie Sie Azure AD Multi-Factor Authentication nutzen, welche Auswirkungen mit der Lizenzierung verbunden sind und wie der Prozess der Benutzerregistrierung abläuft.

#### <a name="back-up-federation-settings"></a>Sichern von Verbundeinstellungen

Auch wenn während der in diesem Artikel beschriebenen Prozesse keine Änderungen an anderen vertrauenden Seiten Ihrer AD FS-Farm vorgenommen werden, empfehlen wir Ihnen das Anlegen einer aktuellen gültigen Sicherung Ihrer AD FS-Farm, mit der Sie eine Wiederherstellung durchführen können. Sie können eine aktuelle gültige Sicherung erstellen, indem Sie das kostenlose Microsoft-[Tool für die schnelle AD FS-Wiederherstellung](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) (AD FS Rapid Restore Tool) verwenden. Mit dem Tool können Sie AD FS sichern und eine vorhandene Farm wiederherstellen oder eine neue Farm erstellen.

Wenn Sie das „AD FS Rapid Restore Tool“ nicht verwenden möchten, sollten Sie zumindest die Vertrauensstellung der vertrauenden Seite von Microsoft 365 Identity Platform und alle zugeordneten benutzerdefinierten Anspruchsregeln exportieren, die Sie hinzugefügt haben. Sie können die Vertrauensstellung der vertrauenden Seite und die zugehörigen Anspruchsregeln exportieren, indem Sie das folgende PowerShell-Beispiel verwenden:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Überlegungen zur Bereitstellung und Verwendung von AD FS

In diesem Abschnitt werden die Bereitstellungsaspekte und Details zur Verwendung von AD FS beschrieben.

### <a name="current-ad-fs-use"></a>Derzeitige Verwendung von AD FS

Bevor Sie die Umstellung von der Verbundidentität auf die verwaltete Identität durchführen, sollten Sie sich genau ansehen, wie Sie AD FS derzeit für Azure AD, Microsoft 365 und andere Anwendungen (Vertrauensstellungen der vertrauenden Seite) nutzen. Sehen Sie sich vor allem die Szenarien an, die in der folgenden Tabelle beschrieben sind:

| Wenn | Then |
|-|-|
| Sie planen, AD FS weiterhin für andere Anwendungen (als Azure AD und Microsoft 365) zu verwenden. | Nachdem Sie Ihre Domänen konvertiert haben, verwenden Sie sowohl AD FS als auch Azure AD. Berücksichtigen Sie die Benutzerfreundlichkeit. In einigen Szenarien müssen sich Benutzer unter Umständen zweimal authentifizieren: einmal für Azure AD (worüber ein Benutzer SSO-Zugriff auf andere Anwendungen wie Microsoft 365 erhält) und erneut für alle Anwendungen, die noch an AD FS als Vertrauensstellung der vertrauenden Seite gebunden sind. |
| Ihre AD FS-Instanz wurde stark angepasst und nutzt in der Datei „onload.js“ spezifische Anpassungseinstellungen (z.B. wenn Sie die Anmeldung so geändert haben, dass Benutzer nur das Format **SamAccountName** für ihren Benutzernamen verwenden, anstatt einen Benutzerprinzipalnamen (UPN), oder wenn Ihre Organisation die Anmeldung mit umfangreichem Branding versehen hat). Die Datei „onload.js“ kann in Azure AD nicht dupliziert werden. | Vor dem Fortfahren müssen Sie sich vergewissern, dass mit Azure AD Ihre derzeitigen Anpassungsanforderungen erfüllt werden können. Weitere Informationen und Anleitungen finden Sie in den Abschnitten zum AD FS-Branding und zur AD FS-Anpassung.|
| Sie können AD FS zum Blockieren von früheren Versionen von Clients mit Authentifizierung verwenden.| Erwägen Sie die Ersetzung von AD FS-Steuerungen, mit denen frühere Versionen von Authentifizierungsclients blockiert werden, indem Sie eine Kombination aus [Steuerungen des bedingten Zugriffs](../conditional-access/concept-conditional-access-conditions.md) und [Clientzugriffsregeln in Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules) verwenden. |
| Bei Ihnen ist es erforderlich, dass Benutzer eine mehrstufige Authentifizierung über eine entsprechende lokale Serverlösung durchführen, um sich für AD FS zu authentifizieren.| In einer Domäne mit verwalteter Identität können Sie über die lokale Lösung für die mehrstufige Authentifizierung keine MFA-Abfrage in den Authentifizierungsablauf einfügen. Sie können den Dienst Azure AD Multi-Factor Authentication aber für die mehrstufige Authentifizierung nutzen, nachdem die Domäne konvertiert wurde.<br /><br /> Falls Ihre Benutzer Azure AD Multi-Factor Authentication derzeit nicht verwenden, ist ein einmaliger Registrierungsschritt für die Benutzer erforderlich. Sie müssen sich auf die geplante Registrierung für Ihre Benutzer vorbereiten und dies kommunizieren. |
| Sie verwenden derzeit Zugriffssteuerungsrichtlinien (AuthZ-Regeln) in AD FS, um den Zugriff auf Microsoft 365 zu steuern.| Erwägen Sie, die Richtlinien durch die entsprechenden [Azure AD-Richtlinien für den bedingten Zugriff](../conditional-access/overview.md) und die [Clientzugriffsregeln für Exchange Online](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules) zu ersetzen.|

### <a name="common-ad-fs-customizations"></a>Häufige AD FS-Anpassungen

In diesem Abschnitt werden häufige AD FS-Anpassungen beschrieben.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork-Anspruch

Der Anspruch **InsideCorporateNetwork** wird von AD FS ausgegeben, wenn sich der Benutzer, der die Authentifizierung durchführt, innerhalb des Unternehmensnetzwerks befindet. Dieser Anspruch kann dann an Azure AD übergeben werden. Der Anspruch wird verwendet, um die mehrstufige Authentifizierung basierend auf der Netzwerkadresse des Benutzers zu umgehen. Informationen zur Ermittlung, ob diese Funktionalität in AD FS derzeit verfügbar ist, finden Sie unter [Vertrauenswürdige IPs für Partnerbenutzer](../authentication/howto-mfa-adfs.md).

Der Anspruch **InsideCorporateNetwork** ist nicht mehr verfügbar, sobald Sie für Ihre Domänen die Umstellung auf die Passthrough-Authentifizierung durchgeführt haben. Sie können [benannte Orte in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) verwenden, um diese Funktionalität zu ersetzen.

Nach dem Konfigurieren der benannten Orte müssen Sie alle Richtlinien für bedingten Zugriff aktualisieren, die konfiguriert wurden. Schließen Sie die Werte **Alle vertrauenswürdigen Standorte** oder **Für MFA vertrauenswürdige IPs** für das Netzwerk ein oder aus, um die neu benannten Orte widerzuspiegeln.

Weitere Informationen zur Bedingung **Standort** beim bedingten Zugriff finden Sie unter [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Mit Azure AD Hybrid verknüpfte Geräte

Wenn Sie ein Gerät mit Azure AD verknüpfen, können Sie Regeln für den bedingten Zugriff erstellen, mit denen erzwungen wird, dass Geräte Ihre Sicherheits- und Konformitätsstandards für den Zugriff erfüllen. Benutzer können sich auch an einem Gerät anmelden, indem sie anstelle eines persönlichen Kontos ein Geschäfts-, Schul- oder Unikonto einer Organisation nutzen. Bei Verwendung von Geräten, die mit Azure AD Hybrid verknüpft sind, können Sie Ihre in die Active Directory-Domäne eingebundenen Geräte mit Azure AD verknüpfen. Unter Umständen wurde Ihre Verbundumgebung für die Verwendung dieses Features eingerichtet.

Gehen Sie für Windows 10-Clients wie folgt vor, um sicherzustellen, dass die Hybrideinbindung weiterhin für alle in die Domäne eingebundenen Geräte funktioniert, nachdem Ihre Domänen auf die Passthrough-Authentifizierung umgestellt wurden: Verwenden Sie Azure AD Connect, um Active Directory-Computerkonten mit Azure AD zu synchronisieren.

Für Windows 8- und Windows 7-Computerkonten wird für die Hybrideinbindung das nahtlose einmalige Anmelden genutzt, um die Computer in Azure AD zu registrieren. Es ist nicht erforderlich, Windows 8- und Windows 7-Computerkonten zu synchronisieren, wie Sie dies für Windows 10-Geräte durchführen. Allerdings müssen Sie eine aktualisierte Version der Datei „workplacejoin.exe“ (per MSI-Datei) für Windows 8- und Windows 7-Clients bereitstellen, damit diese sich per nahtlosem einmaligem Anmelden registrieren können. [Laden Sie die MSI-Datei herunter](https://www.microsoft.com/download/details.aspx?id=53554).

Weitere Informationen finden Sie unter [Anleitung: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Branding

Wenn Ihre Organisation die [Seiten für die AD FS-Anmeldung so angepasst hat](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization), dass viele organisationsbezogene Informationen angezeigt werden, können Sie erwägen, ähnliche [Anpassungen auch an der Azure AD-Anmeldeseite vorzunehmen](../fundamentals/customize-branding.md).

Es sind zwar ähnliche Anpassungen verfügbar, aber auf Anmeldeseiten sollte mit visuellen Änderungen gerechnet werden, nachdem die Konvertierung durchgeführt wurde. Es kann ratsam sein, in der Benachrichtigung für Ihre Benutzer auch Informationen zu den zu erwartenden Änderungen anzugeben.

> [!NOTE]
> Branding für Organisationen ist nur verfügbar, wenn Sie eine Premium- oder Basic-Lizenz für Azure Active Directory erwerben oder über eine Microsoft 365-Lizenz verfügen.

## <a name="plan-for-smart-lockout"></a>Planen des Smart Lockout

Azure AD Smart Lockout schützt Sie vor Brute-Force-Kennwortangriffen. Mit Smart Lockout wird verhindert, dass ein lokales Active Directory-Konto gesperrt wird, wenn die Passthrough-Authentifizierung verwendet und eine Gruppenrichtlinie für das Sperren von Konten in Active Directory festgelegt wird.

Weitere Informationen finden Sie unter [Azure Active Directory Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="plan-deployment-and-support"></a>Planen der Bereitstellung und des Supports

Führen Sie die in diesem Abschnitt beschriebenen Aufgaben durch, die Ihnen als Hilfe beim Planen der Bereitstellung und des Supports dienen.

### <a name="plan-the-maintenance-window"></a>Planen des Wartungsfensters

Die Domänenkonvertierung erfolgt relativ schnell, aber es kann sein, dass Azure AD noch für einen Zeitraum von bis zu vier Stunden nach Abschluss der Domänenkonvertierung einige Authentifizierungsanforderungen an Ihre AD FS-Server sendet. Während dieses Fensters von vier Stunden und je nach den unterschiedlichen Caches auf Dienstseite besteht die Möglichkeit, dass Azure AD diese Authentifizierungen ggf. nicht akzeptiert. Benutzer erhalten unter Umständen einen Fehler. Der Benutzer kann die Authentifizierung für AD FS trotzdem erfolgreich durchführen, aber Azure AD akzeptiert das ausgestellte Token des Benutzers nicht mehr, weil diese Verbundvertrauensstellung jetzt entfernt wurde.

Hiervon sind nur Benutzer betroffen, die während dieses Fensters nach der Konvertierung über einen Webbrowser auf die Dienste zugreifen, bevor der Cache auf Dienstseite geleert wird. Legacyclients (Exchange ActiveSync, Outlook 2010/2013) sollten nicht betroffen sein, da unter Exchange Online für einen festgelegten Zeitraum ein Cache mit den entsprechenden Anmeldeinformationen angelegt wird. Der Cache wird genutzt, um für den Benutzer im Hintergrund die erneute Authentifizierung durchzuführen. Der Benutzer muss nicht zurück zu AD FS wechseln. Anmeldeinformationen, die für diese Clients auf dem Gerät gespeichert sind, werden verwendet, um nach dem Löschen dieses Caches die erneute Authentifizierung im Hintergrund durchzuführen. Benutzer sollten aufgrund des Prozesses für die Domänenkonvertierung keine Aufforderungen zum Eingeben des Kennworts erhalten.

Moderne Authentifizierungsclients (Office 2016-/Office 2013-, iOS- und Android-Apps) nutzen ein gültiges Aktualisierungstoken, um neue Zugriffstoken für den weiteren Zugriff auf Ressourcen abzurufen, anstatt zurück zu AD FS zu wechseln. Diese Clients sind „immun“ gegen Aufforderungen zur Kennworteingabe, die sich aus dem Prozess der Domänenkonvertierung ergeben. Die Clients funktionieren auch ohne zusätzliche Konfiguration weiterhin.

> [!IMPORTANT]
> Fahren Sie Ihre AD FS-Umgebung nicht herunter, und entfernen Sie die Vertrauensstellung der vertrauenden Seite von Microsoft 365 nicht, bevor Sie überprüft haben, ob alle Benutzer die Cloudauthentifizierung erfolgreich nutzen können.

### <a name="plan-for-rollback"></a>Planen der Zurücksetzung

Falls ein größeres Problem besteht, das Sie nicht schnell lösen können, treffen Sie unter Umständen die Entscheidung, für die Lösung einen Rollback zurück zum Verbund auszuführen. Sie sollten unbedingt planen, was zu tun ist, wenn Ihre Bereitstellung nicht wie gewünscht verläuft. Wenn die Konvertierung von Domänen oder Benutzern während der Bereitstellung fehlschlägt oder Sie zum Verbund zurückkehren müssen, müssen Sie wissen, wie Sie Ausfälle minimieren und die Auswirkungen auf Ihre Benutzer reduzieren können.

#### <a name="to-roll-back"></a>Durchführen eines Rollbacks

Überprüfen Sie zum Planen des Rollbacks den Verbundentwurf und die Bereitstellungsdokumentation auf Ihre jeweiligen Bereitstellungsdetails. Der Prozess sollte die folgenden Aufgaben umfassen:

* Konvertieren von verwalteten Domänen in Verbunddomänen mit dem Cmdlet **Convert-MSOLDomainToFederated**
* Konfigurieren zusätzlicher Anspruchsregeln (falls erforderlich)

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Ein wichtiger Teil der Bereitstellungsplanung und des Supports ist die Sicherstellung, dass Ihre Benutzer vorab über anstehende Änderungen informiert werden. Benutzer sollten im Voraus wissen, was passiert und was von ihnen erwartet wird.

Nachdem sowohl die Passthrough-Authentifizierung als auch das nahtlose einmalige Anmelden bereitgestellt wurden, ändert sich die Anmeldeerfahrung für den Benutzer beim Zugriff auf Microsoft 365 und andere Ressourcen, die über Azure AD authentifiziert werden. Benutzer, die sich außerhalb des Netzwerks befinden, sehen nur die Azure AD-Anmeldeseite. Diese Benutzer werden nicht auf die formularbasierte Seite umgeleitet, die von den Proxyservern für externe Webanwendungen angezeigt wird.

Binden Sie die folgenden Elemente in Ihre Kommunikationsstrategie ein:

* Benachrichtigen Sie Benutzer wie folgt über bevorstehende und veröffentlichte Funktionen:
   * E-Mails und andere Kanäle für die interne Kommunikation
   * Visuelle Aushänge, z.B. Poster
   * Direkte oder andere Kommunikation der Geschäftsleitung
* Legen Sie fest, wer die Benachrichtigungen anpasst und wer sie wann versendet.

## <a name="implement-your-solution"></a>Implementieren Ihrer Lösung

Sie haben die Planung Ihrer Lösung abgeschlossen. Jetzt können Sie sie implementieren. Die Implementierung umfasst die folgenden Komponenten:

* Vorbereiten auf nahtloses einmaliges Anmelden
* Ändern der Anmeldemethode in „Passthrough-Authentifizierung“ und Aktivieren des nahtlosen einmaligen Anmeldens

### <a name="step-1-prepare-for-seamless-sso"></a>Schritt 1: Vorbereiten des nahtlosen einmaligen Anmeldens

Damit Ihre Geräte das nahtlose einmalige Anmelden verwenden können, müssen Sie den Intranetzoneneinstellungen der Benutzer mithilfe einer Gruppenrichtlinie in Active Directory eine Azure AD-URL hinzufügen.

Webbrowser berechnen standardmäßig anhand der URL automatisch die richtige Zone (Internet oder Intranet). Beispielsweise ist **http:\/\/contoso/** der Intranetzone und **http:\/\/intranet.contoso.com** der Internetzone zugeordnet (da die URL einen Punkt enthält). Browser senden keine Kerberos-Tickets an Cloudendpunkte wie die Azure AD-URL, sofern Sie die URL nicht explizit der Intranetzone des Browsers hinzufügen.

Führen Sie die [Schritte](./how-to-connect-sso-quick-start.md) aus, um mit der Veröffentlichung der erforderlichen Änderungen für Ihre Geräte zu beginnen.

> [!IMPORTANT]
> Wenn Sie diese Änderung vornehmen, ergeben sich keine Änderungen der Art und Weise, wie sich Benutzer bei Azure AD anmelden. Es ist aber wichtig, dass Sie diese Konfiguration vor dem Fortfahren auf alle Ihre Geräte anwenden. Benutzer, die sich an Geräten ohne diese Konfiguration anmelden, müssen einfach einen Benutzernamen und ein Kennwort eingeben, um sich an Azure AD anzumelden.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Schritt 2: Ändern der Anmeldemethode in „Passthrough-Authentifizierung“ und Aktivieren des nahtlosen einmaligen Anmeldens

Sie haben zwei Möglichkeiten, um die Anmeldemethode in die Passthrough-Authentifizierung zu ändern und das nahtlose einmalige Anmelden zu aktivieren.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Option A: Konfigurieren der Passthrough-Authentifizierung mit Azure AD Connect

Verwenden Sie diese Methode, wenn Sie Ihre AD FS-Umgebung ursprünglich mit Azure AD Connect konfiguriert haben. Sie können diese Methode nicht verwenden, wenn Sie für die ursprüngliche Konfiguration Ihrer AD FS-Umgebung *nicht* Azure AD Connect verwendet haben.

> [!IMPORTANT]
> Nachdem Sie die folgenden Schritte ausgeführt haben, werden Ihre gesamten Domänen von der Verbundidentität auf verwaltete Identität umgestellt. Weitere Informationen finden Sie unter [Planen der Migrationsmethode](#plan-the-migration-method).

Ändern Sie zuerst die Anmeldemethode:

1. Öffnen Sie auf dem Azure AD Connect-Server den Azure AD Connect-Assistenten.
2. Wählen Sie **Benutzeranmeldung ändern** und dann **Weiter**. 
3. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort eines globalen Administratorkontos ein.
4. Wählen Sie auf der Seite **Benutzeranmeldung** die Schaltfläche **Passthrough-Authentifizierung** und dann **Einmaliges Anmelden aktivieren** und **Weiter**.
5. Geben Sie auf der Seite **Einmaliges Anmelden aktivieren** die Anmeldeinformationen eines Domänenadministratorkontos ein, und wählen Sie dann **Weiter**.

   > [!NOTE]
   > Die Kontoanmeldeinformationen des Domänenadministrators sind erforderlich, um nahtloses einmaliges Anmelden zu aktivieren. Im Rahmen des Prozesses werden die folgenden Aktionen durchgeführt, für die diese erhöhten Rechte benötigt werden. Die Kontoanmeldeinformationen des Domänenadministrators werden weder in Azure AD Connect noch in Azure AD gespeichert. Die Kontoanmeldeinformationen des Domänenadministrators werden nur verwendet, um das Feature zu aktivieren. Die Anmeldeinformationen werden verworfen, nachdem der Prozess erfolgreich abgeschlossen wurde.
   >
   > 1. Ein Computerkonto mit dem Namen „AZUREADSSOACC“ (steht für Azure AD) wird auf Ihrer lokalen Active Directory-Instanz erstellt.
   > 2. Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird auf sichere Weise für Azure AD freigegeben.
   > 3. Es werden zwei Kerberos-Dienstprinzipalnamen (SPNs) erstellt, um die zwei URLs darzustellen, die während der Azure AD-Anmeldung verwendet werden.

6. Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist. Wählen Sie anschließend **Konfigurieren**.<br />

   ![Screenshot: Seite „Bereit zur Konfiguration“](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. Wählen Sie im Azure AD-Portal die Option **Azure Active Directory** und dann **Azure AD Connect**.
8. Überprüfen Sie diese Einstellungen:
   * **Verbund** ist auf **Deaktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
   * **Passthrough-Authentifizierung** ist auf **Aktiviert** festgelegt.<br />

   ![Screenshot: Einstellungen im Abschnitt „Benutzeranmeldung“](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Stellen Sie als Nächstes zusätzliche Authentifizierungsmethoden bereit:

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Azure AD Connect**, und wählen Sie dann die Option **Passthrough-Authentifizierung**.
2. Wählen Sie auf der Seite **Passthrough-Authentifizierung** die Schaltfläche **Herunterladen**.
3. Wählen Sie auf der Seite **Agent herunterladen** die Option **Bedingungen akzeptieren > Herunterladen**.

   Der Download der zusätzlichen Authentifizierungs-Agents beginnt. Installieren Sie den sekundären Authentifizierungs-Agent auf einem mit einer Domäne verknüpften Server. 

   > [!NOTE]
   > Der erste Agent wird im Rahmen der Konfigurationsänderungen, die im Abschnitt **Benutzeranmeldung** von Azure AD Connect vorgenommen werden, immer auf dem Azure AD Connect-Server installiert. Installieren Sie alle zusätzlichen Authentifizierungs-Agents auf einem separaten Server. Es wird empfohlen, zwei oder drei zusätzliche Authentifizierungs-Agents zu verwenden. 

4. Führen Sie die Installation des Authentifizierungs-Agents durch. Während der Installation müssen Sie die Anmeldeinformationen eines Kontos vom Typ „Globaler Administrator“ eingeben.

   ![Screenshot: Schaltfläche „Installieren“, mit der Sie das Authentifizierungs-Agent-Paket für Microsoft Azure AD Connect ausführen](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Screenshot: Microsoft-Anmeldeseite](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Nachdem der Authentifizierungs-Agent installiert wurde, können Sie zu der Seite zurückkehren, auf der die Integrität der Passthrough-Authentifizierungs-Agents angezeigt wird, um den Status der zusätzlichen Agents zu überprüfen.

Springen Sie zu [Testen und nächste Schritte](#testing-and-next-steps).

> [!IMPORTANT]
> Überspringen Sie den Abschnitt **Option B: Wechseln vom Verbund zur Passthrough-Authentifizierung mit Azure AD Connect und PowerShell**. Die Schritte in diesem Abschnitt gelten nicht, wenn Sie Option A gewählt haben, um die Anmeldemethode in die Passthrough-Authentifizierung zu ändern und nahtloses einmaliges Anmelden zu aktivieren. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Option B: Wechseln vom Verbund zur Passthrough-Authentifizierung mit Azure AD Connect und PowerShell

Verwenden Sie diese Option, wenn Sie Ihre Verbunddomänen ursprünglich nicht mit Azure AD Connect konfiguriert haben.

Aktivieren Sie zunächst die Passthrough-Authentifizierung:

1. Öffnen Sie auf dem Azure AD Connect-Server den Azure AD Connect-Assistenten.
2. Wählen Sie **Benutzeranmeldung ändern** und dann **Weiter**.
3. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort eines globalen Administratorkontos ein.
4. Wählen Sie auf der Seite **Benutzeranmeldung** die Schaltfläche **Passthrough-Authentifizierung**. Wählen Sie **Einmaliges Anmelden aktivieren** und dann **Weiter**.
5. Geben Sie auf der Seite **Einmaliges Anmelden aktivieren** die Anmeldeinformationen eines Domänenadministratorkontos ein, und wählen Sie dann **Weiter**.

   > [!NOTE]
   > Die Kontoanmeldeinformationen des Domänenadministrators sind erforderlich, um nahtloses einmaliges Anmelden zu aktivieren. Im Rahmen des Prozesses werden die folgenden Aktionen durchgeführt, für die diese erhöhten Rechte benötigt werden. Die Kontoanmeldeinformationen des Domänenadministrators werden weder in Azure AD Connect noch in Azure AD gespeichert. Die Kontoanmeldeinformationen des Domänenadministrators werden nur verwendet, um das Feature zu aktivieren. Die Anmeldeinformationen werden verworfen, nachdem der Prozess erfolgreich abgeschlossen wurde.
   > 
   > 1. Ein Computerkonto mit dem Namen „AZUREADSSOACC“ (steht für Azure AD) wird auf Ihrer lokalen Active Directory-Instanz erstellt.
   > 2. Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird auf sichere Weise für Azure AD freigegeben.
   > 3. Es werden zwei Kerberos-Dienstprinzipalnamen (SPNs) erstellt, um die zwei URLs darzustellen, die während der Azure AD-Anmeldung verwendet werden.

6. Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist. Wählen Sie anschließend **Konfigurieren**.<br />

   ![Screenshot: Schaltfläche „Konfigurieren“ auf der Seite „Bereit zur Konfiguration“](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Wenn Sie die Option **Konfigurieren** wählen, werden die folgenden Schritte ausgeführt:

   1. Der erste Agent für die Passthrough-Authentifizierung wird installiert.
   2. Das Passthrough-Feature wird aktiviert.
   3. Das nahtlose einmalige Anmelden wird aktiviert.

7. Überprüfen Sie diese Einstellungen:
   * **Verbund** ist auf **Aktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
   * **Passthrough-Authentifizierung** ist auf **Aktiviert** festgelegt.
   
   ![Screenshot: Zu überprüfende Einstellungen im Abschnitt „Benutzeranmeldung“](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Klicken Sie auf **Passthrough-Authentifizierung**, und überprüfen Sie, ob der Status **Aktiv** lautet.<br />
   
   Wenn der Authentifizierungs-Agent nicht aktiv ist, sollten Sie einige [Problembehandlungsschritte](./tshoot-connect-pass-through-authentication.md) ausführen, bevor Sie im nächsten Schritt mit dem Prozess zum Konvertieren der Domäne fortfahren. Sie riskieren einen Ausfall der Authentifizierung, wenn Sie Ihre Domänen konvertieren, bevor Sie überprüft haben, ob Ihre Agents für die Passthrough-Authentifizierung erfolgreich installiert wurden und ob ihr Status im Azure-Portal **Aktiv** lautet.

Stellen Sie als Nächstes zusätzliche Authentifizierungs-Agents bereit:

1. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Azure AD Connect**, und wählen Sie dann die Option **Passthrough-Authentifizierung**.
2. Wählen Sie auf der Seite **Passthrough-Authentifizierung** die Schaltfläche **Herunterladen**. 
3. Wählen Sie auf der Seite **Agent herunterladen** die Option **Bedingungen akzeptieren > Herunterladen**.
 
   Der Download des Authentifizierungs-Agents beginnt. Installieren Sie den sekundären Authentifizierungs-Agent auf einem mit einer Domäne verknüpften Server.

   > [!NOTE]
   > Der erste Agent wird im Rahmen der Konfigurationsänderungen, die im Abschnitt **Benutzeranmeldung** von Azure AD Connect vorgenommen werden, immer auf dem Azure AD Connect-Server installiert. Installieren Sie alle zusätzlichen Authentifizierungs-Agents auf einem separaten Server. Es wird empfohlen, zwei oder drei zusätzliche Authentifizierungs-Agents zu verwenden.
 
4. Führen Sie die Installation des Authentifizierungs-Agents durch. Während der Installation müssen Sie die Anmeldeinformationen eines Kontos vom Typ „Globaler Administrator“ eingeben.<br />

   ![Screenshot: Schaltfläche „Installieren“ auf der Seite mit dem Authentifizierungs-Agent-Paket für Microsoft Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Screenshot: Anmeldeseite](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Nachdem der Authentifizierungs-Agent installiert wurde, können Sie zu der Seite zurückkehren, auf der die Integrität der Passthrough-Authentifizierungs-Agents angezeigt wird, um den Status der zusätzlichen Agents zu überprüfen.

An diesem Punkt ist die Verbundauthentifizierung noch aktiv und für Ihre Domänen betriebsbereit. Damit Sie mit der Bereitstellung fortfahren können, müssen Sie jede Domäne von der Verbundidentität auf die verwaltete Identität umstellen, damit Authentifizierungsanforderungen für die Domäne über die Passthrough-Authentifizierung verarbeitet werden.

Sie müssen nicht alle Domänen gleichzeitig konvertieren. Sie können auch mit einer Testdomäne auf Ihrem Produktionsmandanten oder mit der Domäne beginnen, die bei Ihnen die niedrigste Anzahl von Benutzern aufweist.

Führen Sie die Konvertierung durch, indem Sie das Azure AD PowerShell-Modul verwenden:

1. Melden Sie sich in PowerShell an Azure AD an, indem Sie ein Konto vom Typ „Globaler Administrator“ verwenden.
2. Führen Sie folgenden Befehl aus, um die erste Domäne zu konvertieren:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. Wählen Sie im Azure AD-Portal die Optionen **Azure Active Directory** > **Azure AD Connect**.
4. Überprüfen Sie diese Einstellungen, nachdem Sie alle Verbunddomänen konvertiert haben:
   * **Verbund** ist auf **Deaktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
   * **Passthrough-Authentifizierung** ist auf **Aktiviert** festgelegt.<br />

   ![Screenshot mit den Einstellungen im Abschnitt „Benutzeranmeldung“ im Azure AD-Portal](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testen und nächste Schritte

Führen Sie die folgenden Aufgaben durch, um die Passthrough-Authentifizierung zu überprüfen und den Konvertierungsvorgang abzuschließen.

### <a name="test-pass-through-authentication"></a>Testen der Passthrough-Authentifizierung 

Als für Ihren Mandanten die Verbundidentität verwendet wurde, wurden Benutzer von der Azure AD-Anmeldeseite an Ihre AD FS-Umgebung umgeleitet. Nachdem der Mandant jetzt für die Passthrough-Authentifizierung konfiguriert wurde und nicht mehr die Verbundauthentifizierung verwendet wird, werden Benutzer nicht mehr an AD FS umgeleitet. Stattdessen melden sich Benutzer direkt auf der Azure AD-Anmeldeseite an.

Testen Sie die Passthrough-Authentifizierung wie folgt:

1. Öffnen Sie Internet Explorer im InPrivate-Modus, damit Sie nicht per einmaligem Anmelden automatisch angemeldet werden.
2. Navigieren Sie zur Office 365-Anmeldeseite ([https://portal.office.com](https://portal.office.com/)).
3. Geben Sie den Benutzerprinzipalnamen (UPN) eines Benutzers ein, und wählen Sie anschließend **Weiter**. Achten Sie darauf, dass Sie den UPN eines Hybridbenutzers eingeben, der von Ihrer lokalen Active Directory-Instanz synchronisiert und für den zuvor die Verbundauthentifizierung verwendet wurde. Es wird eine Seite angezeigt, auf der Sie den Benutzernamen und das Kennwort eingeben können:

   ![Screenshot: Anmeldeseite, auf der Sie den Benutzernamen eingeben](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Screenshot: Anmeldeseite, auf der Sie ein Kennwort eingeben](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Nachdem Sie das Kennwort eingegeben und **Anmelden** gewählt haben, werden Sie an das Office 365-Portal umgeleitet.

   ![Screenshot: Office 365-Portal](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Testen des nahtlosen einmaligen Anmeldens

Testen Sie das nahtlose einmalige Anmelden wie folgt:

1. Melden Sie sich an einem in die Domäne eingebundenen Computer an, der mit dem Unternehmensnetzwerk verbunden ist.
2. Navigieren Sie in Internet Explorer oder Chrome zu einer der folgenden URLs (ersetzen Sie „contoso“ durch Ihre Domäne):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Der Benutzer wird kurz auf die Azure AD-Anmeldeseite umgeleitet, auf der die Meldung „Anmeldung wird versucht“ angezeigt wird. Der Benutzer wird nicht zum Eingeben eines Benutzernamens oder Kennworts aufgefordert.<br />

   ![Screenshot: Azure AD-Anmeldeseite und Meldung](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. Der Benutzer wird umgeleitet, und die Anmeldung am Zugriffsbereich ist erfolgreich:

   > [!NOTE]
   > Das nahtlose einmalige Anmelden funktioniert für Microsoft 365-Dienste, die Domänenhinweise unterstützen (z. B. myapps.microsoft.com/contoso.com). Derzeit unterstützt das Microsoft 365-Portal (portal.office.com) keine Domänenhinweise. Benutzer müssen einen Benutzerprinzipalnamen (UPN) eingeben. Nachdem ein UPN eingegeben wurde, ruft der Prozess zum nahtlosen einmaligen Anmelden das Kerberos-Ticket im Namen des Benutzers ab. Der Benutzer wird angemeldet, ohne ein Kennwort einzugeben.

   > [!TIP]
   > Erwägen Sie die Bereitstellung der [Azure AD-Hybrideinbindung unter Windows 10](../devices/overview.md), um das einmalige Anmelden zu verbessern.

### <a name="remove-the-relying-party-trust"></a>Entfernen der Vertrauensstellung der vertrauenden Seite

Nachdem Sie überprüft haben, ob alle Benutzer und Clients erfolgreich über Azure AD authentifiziert werden konnten, können Sie die Vertrauensstellung der vertrauenden Seite von Microsoft 365 entfernen.

Falls Sie AD FS nicht für andere Zwecke nutzen (für andere Vertrauensstellungen der vertrauenden Seite), können Sie AD FS nun außer Betrieb nehmen.

### <a name="rollback"></a>Rollback

Wenn ein größeres Problem besteht, das nicht schnell behoben werden kann, können Sie die Entscheidung treffen, für die Lösung einen Rollback auf „Verbund“ auszuführen.

Überprüfen Sie den Verbundentwurf und die Bereitstellungsdokumentation auf Ihre jeweiligen Bereitstellungsdetails. Der Prozess sollte die folgenden Aufgaben umfassen:

* Konvertieren von verwalteten Domänen in die Verbundauthentifizierung mit dem Cmdlet **Convert-MSOLDomainToFederated**
* Konfigurieren zusätzlicher Anspruchsregeln (falls erforderlich)

### <a name="sync-userprincipalname-updates"></a>Synchronisieren von userPrincipalName-Updates

Updates des Attributs **UserPrincipalName**, für das der Synchronisierungsdienst aus der lokalen Umgebung verwendet wird, werden generell blockiert, sofern nicht die beiden folgenden Bedingungen erfüllt sind:

* Der Benutzer befindet sich in einer Domäne mit verwalteter Identität (kein Verbund).
* Dem Benutzer wurde keine Lizenz zugewiesen.

Informationen dazu, wie Sie dieses Feature überprüfen oder aktivieren, finden Sie im Artikel zur [Synchronisierung von userPrincipalName-Updates](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Durchführen des Rollovers des Kerberos-Entschlüsselungsschlüssels für das einmalige Anmelden

Es ist wichtig, häufig einen Rollover für den Kerberos-Entschlüsselungsschlüssel des AZUREADSSOACC-Computerkontos (steht für Azure AD) durchzuführen. Das Computerkonto AZUREADSSOACC wird in Ihrer lokalen AD-Gesamtstruktur erstellt. Es wird empfohlen, den Rollover für den Entschlüsselungsschlüssel von Kerberos mindestens alle 30 Tage durchzuführen, um anzupassen, wie die Mitglieder der Active Directory-Domäne Kennwortänderungen übermitteln. Da an das Computerkontoobjekt AZUREADSSOACC kein zugeordnetes Gerät angefügt ist, müssen Sie den Rollover manuell durchführen.

Initiieren Sie den Rollover des Kerberos-Entschlüsselungsschlüssels für das nahtlose einmalige Anmelden auf dem lokalen Server, auf dem Azure AD Connect ausgeführt wird.

Weitere Informationen finden Sie unter [Wie kann ich einen Rollover des Kerberos-Entschlüsselungsschlüssels des AZUREADSSOACC-Computerkontos durchführen?](./how-to-connect-sso-faq.md).

## <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

Überwachen Sie die Server, auf denen die Authentifizierungs-Agents ausgeführt werden, um die Verfügbarkeit der Lösung aufrechtzuerhalten. Die Authentifizierungs-Agents stellen nicht nur allgemeine Leistungsindikatoren für die Server bereit, sondern machen auch Leistungsobjekte verfügbar, die Ihnen beim Nachvollziehen der Authentifizierungsstatistiken und -fehler als Hilfe dienen.

Authentifizierungs-Agents protokollieren Vorgänge in den Windows-Ereignisprotokollen, die unter „Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin“ gespeichert werden.

Sie können auch die Protokollierung für die Problembehandlung aktivieren.

Weitere Informationen finden Sie unter [Behandlung von Problemen bei der Azure Active Directory-Passthrough-Authentifizierung](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Entwurfskonzepte für Azure AD Connect](plan-connect-design-concepts.md).
* Wählen Sie die [richtige Authentifizierung](./choose-ad-authn.md).
* Erfahren Sie mehr über [unterstützte Topologien](plan-connect-design-concepts.md).
