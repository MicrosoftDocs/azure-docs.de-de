---
title: 'Azure AD Connect: Migrieren vom Verbund zu PHS für Azure AD | Microsoft-Dokumentation'
description: Dieser Artikel enthält Informationen zum Umstellen Ihrer Hybrididentitätsumgebung vom Verbund auf die Kennworthashsynchronisierung.
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
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861815"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrieren vom Verbund zur Kennworthashsynchronisierung für Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie Ihre Organisationsdomänen von Active Directory-Verbunddienste (AD FS) auf die Kennworthashsynchronisierung umstellen.

> [!NOTE]
> Das Ändern der Authentifizierungsmethode erfordert Planung, Tests und potenzielle Ausfallzeiten. Das [gestaffelte Rollout](how-to-connect-staged-rollout.md) ist eine alternative Möglichkeit zum Testen und schrittweisen Migrieren von der Verbund- zur Cloudauthentifizierung mithilfe der Kennworthashsynchronisierung.
>
> Wenn Sie einen gestaffelten Rollout verwenden möchten, sollten Sie daran denken, die Features für den gestaffelten Rollout zu deaktivieren, nachdem Sie den Übergang abgeschlossen haben.  Weitere Informationen finden Sie unter [Migrieren zur Cloudauthentifizierung mithilfe eines gestaffelten Rollouts](how-to-connect-staged-rollout.md).


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Voraussetzungen für die Migration zur Kennworthashsynchronisierung

Für die Migration von AD FS zur Kennworthashsynchronisierung gelten die folgenden Voraussetzungen.


### <a name="update-azure-ad-connect"></a>Aktualisieren von Azure AD Connect

Für die erfolgreiche Durchführung der Schritte für die Migration zur Kennworthashsynchronisierung sollte mindestens [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) Version 1.1.819.0 vorhanden sein. Diese Version enthält wesentliche Änderungen an der Art und Weise, wie die Konvertierung der Anmeldung durchgeführt wird, und reduziert die Gesamtzeit für die Migration vom Verbund zur Cloudauthentifizierung von Stunden auf Minuten.


> [!IMPORTANT]
> Unter Umständen lesen Sie in veralteten Dokumentationen, Tools und Blogs noch, dass eine Benutzerkonvertierung erforderlich ist, wenn Sie Domänen von der Verbundidentität auf die verwaltete Identität umstellen. Das *Konvertieren von Benutzern* muss aber nicht mehr durchgeführt werden. Microsoft arbeitet an der Aktualisierung der Dokumentation und der Tools, damit diese Veränderung entsprechend widergespiegelt wird.

Führen Sie die Schritte unter [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](./how-to-upgrade-previous-version.md) aus, um Azure AD Connect zu aktualisieren.

### <a name="password-hash-synchronization-required-permissions"></a>Für die Kennworthashsynchronisierung erforderliche Berechtigungen

Sie können Azure AD Connect über die Expresseinstellungen oder eine benutzerdefinierte Installation konfigurieren. Wenn Sie die Option „Benutzerdefinierte Installation“ verwendet haben, sind die [benötigten Berechtigungen](./reference-connect-accounts-permissions.md) für die Kennworthashsynchronisierung unter Umständen nicht vorhanden.

Für das AD DS-Dienstkonto (Active Directory Domain Services) von Azure AD Connect werden die folgenden Berechtigungen benötigt, um Kennworthashes zu synchronisieren:

* Verzeichnisänderungen replizieren
* Verzeichnisänderungen replizieren: Alle

Jetzt ist ein guter Zeitpunkt, um zu überprüfen, ob diese Berechtigungen für alle Bereiche vorhanden sind.

### <a name="plan-the-migration-method"></a>Planen der Migrationsmethode

Sie können zwischen zwei Methoden wählen, um die Migration von der Verbundidentitätsverwaltung zur Kennworthashsynchronisierung und zum einmaligen Anmelden (Single Sign-On, SSO) durchzuführen. Welche Methode Sie verwenden, hängt davon ab, wie Ihre AD FS-Instanz ursprünglich konfiguriert wurde.

* **Azure AD Connect**: Wenn Sie AD FS ursprünglich mit Azure AD Connect konfiguriert haben, *müssen* Sie zur Kennworthashsynchronisierung wechseln, indem Sie den Azure AD Connect-Assistenten verwenden.

   ‎Azure AD Connect führt automatisch das Cmdlet **Set-MsolDomainAuthentication** aus, wenn Sie die Anmeldemethode für Benutzer ändern. Azure AD Connect hebt den Verbund aller verifizierten Verbunddomänen Ihres Azure AD-Mandanten automatisch auf.

   > [!NOTE]
   > Falls Sie zum Konfigurieren von AD FS ursprünglich Azure AD Connect genutzt haben, können Sie es derzeit nicht vermeiden, dass alle Domänen Ihres Mandanten aus dem Verbund entfernt werden, wenn Sie die Benutzeranmeldung auf Kennworthashsynchronisierung umstellen. ‎
* **Azure AD Connect mit PowerShell**: Sie können diese Methode nur verwenden, wenn Sie für die ursprüngliche Konfiguration von AD FS nicht Azure AD Connect verwendet haben. Bei dieser Option müssen Sie noch so vorgehen, dass Sie die Methode für die Benutzeranmeldung über den Azure AD Connect-Assistenten ändern. Der Hauptunterschied bei dieser Option ist, dass der Assistent das Cmdlet **Set-MsolDomainAuthentication** nicht automatisch ausführt. Sie haben bei dieser Option die volle Kontrolle darüber, welche Domänen konvertiert werden und in welcher Reihenfolge dies durchgeführt wird.

Führen Sie die Schritte in den folgenden Abschnitten aus, um sich darüber zu informieren, welche Methode Sie verwenden sollten.

#### <a name="verify-current-user-sign-in-settings"></a>Überprüfen der aktuellen Benutzeranmeldungseinstellungen

Überprüfen Sie die aktuellen Benutzeranmeldungseinstellungen wie folgt:

1. Melden Sie sich mit einem globalen Administratorkonto am [Azure AD-Portal](https://aad.portal.azure.com/) an.
2. Überprüfen Sie im Abschnitt **Benutzeranmeldung** die folgenden Einstellungen:
   * **Verbund** ist auf **Aktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Deaktiviert** festgelegt.
   * **Passthrough-Authentifizierung** ist auf **Deaktiviert** festgelegt.

   ![Screenshot: Einstellungen im Azure AD Connect-Abschnitt zur Benutzeranmeldung](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Überprüfen der Azure AD Connect-Konfiguration

1. Öffnen Sie auf Ihrem Azure AD Connect-Server die Anwendung Azure AD Connect. Wählen Sie **Konfigurieren** aus.
2. Wählen Sie auf der Seite **Weitere Aufgaben** die Option **Aktuelle Konfiguration anzeigen** und anschließend **Weiter**.<br />

   ![Screenshot: Auswahl der Option „Aktuelle Konfiguration anzeigen“ auf der Seite „Weitere Aufgaben“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Notieren Sie sich auf der Seite **Lösung prüfen** den Status unter **Kennworthashsynchronisierung**.<br /> 

   * Führen Sie die Schritte in diesem Artikel aus, wenn **Kennworthashsynchronisierung** auf **Deaktiviert** festgelegt ist, um die Option zu aktivieren.
   * Wenn **Kennworthashsynchronisierung** auf **Aktiviert** festgelegt ist, können Sie zum Abschnitt **Schritt 1: Aktivieren der Kennworthashsynchronisierung** in diesem Artikel springen.
4. Scrollen Sie auf der Seite **Lösung prüfen** zu **Active Directory-Verbunddienste (AD FS)** .<br />

   * ‎Wenn die AD FS-Konfiguration in diesem Abschnitt angezeigt wird, können Sie mit Sicherheit davon ausgehen, dass AD FS ursprünglich mit Azure AD Connect konfiguriert wurde. Sie können Ihre Domänen von Verbundidentität auf die verwaltete Identität umstellen, indem Sie die Azure AD Connect-Option **Benutzeranmeldung ändern** verwenden. Der Prozess wird im Abschnitt **Option A: Wechseln vom Verbund zur Kennworthashsynchronisierung mit Azure AD Connect** ausführlich beschrieben.
   * Wenn AD FS in den aktuellen Einstellungen nicht aufgeführt ist, müssen Sie Ihre Domänen per PowerShell manuell von der Verbundidentität auf die verwaltete Identität umstellen. Weitere Informationen zu diesem Prozess finden Sie im Abschnitt **Option B: Wechseln vom Verbund zur Kennworthashsynchronisierung mit Azure AD Connect und PowerShell**.

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
> Wenn **SupportsMfa** auf **True** festgelegt ist, verwenden Sie eine lokale Lösung für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), um eine zweite Faktorabfrage in den Benutzerauthentifizierungsflow einzufügen. Dieses Setup funktioniert nach der Konvertierung dieser Domäne von der Verbund- zur verwalteten Authentifizierung nicht mehr bei Azure AD-Authentifizierungsszenarien. Nach der Deaktivierung des Verbunds lösen Sie die Beziehung zu Ihrem lokalen Verbund, und dazu zählen auch lokale MFA-Adapter. 
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

Der Anspruch **InsideCorporateNetwork** wird von AD FS ausgegeben, wenn sich der Benutzer, der die Authentifizierung durchführt, innerhalb des Unternehmensnetzwerks befindet. Dieser Anspruch kann dann an Azure AD übergeben werden. Der Anspruch wird verwendet, um die mehrstufige Authentifizierung basierend auf der Netzwerkadresse des Benutzers zu umgehen. Informationen zur Ermittlung, ob diese Funktionalität in AD FS derzeit aktiviert ist, finden Sie unter [Vertrauenswürdige IPs für Partnerbenutzer](../authentication/howto-mfa-adfs.md).

Der Anspruch **InsideCorporateNetwork** ist nicht mehr verfügbar, sobald Sie für Ihre Domänen die Konvertierung in die Kennworthashsynchronisierung durchgeführt haben. Sie können [benannte Orte in Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) verwenden, um diese Funktionalität zu ersetzen.

Nach dem Konfigurieren der benannten Orte müssen Sie alle Richtlinien für bedingten Zugriff aktualisieren, die konfiguriert wurden. Schließen Sie die Werte **Alle vertrauenswürdigen Standorte** oder **Für MFA vertrauenswürdige IPs** für das Netzwerk ein oder aus, um die neu benannten Orte widerzuspiegeln.

Weitere Informationen zur Bedingung **Standort** beim bedingten Zugriff finden Sie unter [Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/location-condition.md).

#### <a name="hybrid-azure-ad-joined-devices"></a>Mit Azure AD Hybrid verknüpfte Geräte

Wenn Sie ein Gerät mit Azure AD verknüpfen, können Sie Regeln für den bedingten Zugriff erstellen, mit denen erzwungen wird, dass Geräte Ihre Sicherheits- und Konformitätsstandards für den Zugriff erfüllen. Benutzer können sich auch an einem Gerät anmelden, indem sie anstelle eines persönlichen Kontos ein Geschäfts-, Schul- oder Unikonto einer Organisation nutzen. Bei Verwendung von Geräten, die mit Azure AD Hybrid verknüpft sind, können Sie Ihre in die Active Directory-Domäne eingebundenen Geräte mit Azure AD verknüpfen. Unter Umständen wurde Ihre Verbundumgebung für die Verwendung dieses Features eingerichtet.

Gehen Sie für Windows 10-Clients wie folgt vor, um sicherzustellen, dass die Hybrideinbindung weiterhin für alle in die Domäne eingebundenen Geräte funktioniert, nachdem Ihre Domänen auf die Kennworthashsynchronisierung umgestellt wurden: Verwenden Sie Azure AD Connect-Geräteoptionen, um Active Directory-Computerkonten mit Azure AD zu synchronisieren. 

Für Windows 8- und Windows 7-Computerkonten wird für die Hybrideinbindung das nahtlose einmalige Anmelden genutzt, um die Computer in Azure AD zu registrieren. Es ist nicht erforderlich, Windows 8- und Windows 7-Computerkonten zu synchronisieren, wie Sie dies für Windows 10-Geräte durchführen. Allerdings müssen Sie eine aktualisierte Version der Datei „workplacejoin.exe“ (per MSI-Datei) für Windows 8- und Windows 7-Clients bereitstellen, damit diese sich per nahtlosem einmaligem Anmelden registrieren können. [Laden Sie die MSI-Datei herunter](https://www.microsoft.com/download/details.aspx?id=53554).

Weitere Informationen finden Sie unter [Anleitung: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](../devices/hybrid-azuread-join-plan.md).

#### <a name="branding"></a>Branding

Wenn Ihre Organisation die [Seiten für die AD FS-Anmeldung so angepasst hat](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization), dass viele organisationsbezogene Informationen angezeigt werden, können Sie erwägen, ähnliche [Anpassungen auch an der Azure AD-Anmeldeseite vorzunehmen](../fundamentals/customize-branding.md).

Es sind zwar ähnliche Anpassungen verfügbar, aber auf Anmeldeseiten sollte mit visuellen Änderungen gerechnet werden, nachdem die Konvertierung durchgeführt wurde. Es kann ratsam sein, in der Benachrichtigung für Ihre Benutzer auch Informationen zu den zu erwartenden Änderungen anzugeben.

> [!NOTE]
> Branding für Organisationen ist nur verfügbar, wenn Sie eine Premium- oder Basic-Lizenz für Azure Active Directory erwerben oder über eine Microsoft 365-Lizenz verfügen.

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

Nachdem sowohl die Kennworthashsynchronisierung als auch das nahtlose einmalige Anmelden bereitgestellt wurden, ändert sich die Anmeldeerfahrung für den Benutzer beim Zugriff auf Microsoft 365 und andere Ressourcen, die per Azure AD authentifiziert werden. Benutzer, die sich außerhalb des Netzwerks befinden, sehen nur die Azure AD-Anmeldeseite. Diese Benutzer werden nicht auf die formularbasierte Seite umgeleitet, die von den Proxyservern für externe Webanwendungen angezeigt wird.

Binden Sie die folgenden Elemente in Ihre Kommunikationsstrategie ein:

* Benachrichtigen Sie Benutzer wie folgt über bevorstehende und veröffentlichte Funktionen:
   * E-Mails und andere Kanäle für die interne Kommunikation
   * Visuelle Aushänge, z.B. Poster
   * Direkte oder andere Kommunikation der Geschäftsleitung
* Legen Sie fest, wer die Benachrichtigungen anpasst und wer sie wann versendet.

## <a name="implement-your-solution"></a>Implementieren Ihrer Lösung

Sie haben die Planung Ihrer Lösung abgeschlossen. Jetzt können Sie sie implementieren. Die Implementierung umfasst die folgenden Komponenten:

* Aktivieren der Kennworthashsynchronisierung
* Vorbereiten auf nahtloses einmaliges Anmelden
* Ändern der Anmeldemethode in die Kennworthashsynchronisierung und Aktivieren des nahtlosen einmaligen Anmeldens

### <a name="step-1-enable-password-hash-synchronization"></a>Schritt 1: Aktivieren der Kennworthashsynchronisierung

Der erste Schritt zur Implementierung dieser Lösung ist die Aktivierung der Kennworthashsynchronisierung mit dem Azure AD Connect-Assistenten. Die Kennworthashsynchronisierung ist ein optionales Feature, das Sie in Umgebungen mit Verbund aktivieren können. Es ergibt sich keine Auswirkung auf den Authentifizierungsablauf. In diesem Fall startet Azure AD Connect das Synchronisieren von Kennworthashes ohne Auswirkungen auf Benutzer, die sich per Verbund anmelden.

Aus diesem Grund empfehlen wir Ihnen, diesen Schritt rechtzeitig vor dem Ändern der Anmeldemethode Ihrer Domäne als Vorbereitung auszuführen. Sie haben dann genügend Zeit, um zu überprüfen, ob die Kennworthashsynchronisierung richtig funktioniert.

Aktivieren Sie die Kennworthashsynchronisierung wie folgt:

1. Öffnen Sie auf dem Azure AD Connect-Server den Azure AD Connect-Assistenten, und wählen Sie dann **Konfigurieren**.
2. Wählen Sie **Synchronisierungsoptionen anpassen** und anschließend **Weiter**.
3. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort eines globalen Administratorkontos ein.
4. Wählen Sie auf der Seite **Verzeichnisse verbinden** die Option **Weiter**.
5. Wählen Sie auf der Seite **Filtern von Domänen und Organisationseinheiten** die Option **Weiter**.
6. Wählen Sie auf der Seite **Optionale Features** die Option **Kennwortsynchronisierung** und dann **Weiter**.
 
   ![Screenshot: Auswahl der Option „Kennwortsynchronisierung“ auf der Seite „Optionale Features“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Wählen Sie auf den verbleibenden Seiten jeweils **Weiter**. Wählen Sie auf der letzten Seite die Option **Konfigurieren**.
8. Beim nächsten Synchronisierungsvorgang beginnt Azure AD Connect mit dem Synchronisieren von Kennworthashes.

Nachdem die Kennworthashsynchronisierung aktiviert wurde, werden die Kennworthashes für alle Benutzer im Azure AD Connect Synchronisationsbereich neu gehasht und in Azure AD geschrieben. Je nach Anzahl von Benutzern kann dieser Vorgang einige Minuten oder auch einige Stunden dauern.

Für Planungszwecke sollten Sie davon ausgehen, dass ca. 20.000 Benutzer in einer Stunde verarbeitet werden können.

Führen Sie die Aufgabe **Problembehandlung** im Azure AD Connect-Assistenten durch, um zu überprüfen, ob die Kennworthashsynchronisierung richtig funktioniert:

1. Öffnen Sie mit der Option „Als Administrator ausführen“ eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.
2. Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.
3. Starten Sie den Azure AD Connect-Assistenten.
4. Navigieren Sie zur Seite **Weitere Aufgaben**, und wählen Sie **Problembehandlung** und dann **Weiter**.
5. Wählen Sie auf der Seite **Problembehandlung** die Option **Starten**, um das Menü für die Problembehandlung in PowerShell zu starten.
6. Wählen Sie im Hauptmenü die Option **Problembehandlung bei der Kennworthashsynchronisierung**.
7. Wählen Sie im Untermenü die Option **Die Kennworthashsynchronisierung funktioniert überhaupt nicht**.

Informationen zu Problemen bei der Problembehandlung finden Sie unter [Problembehandlung für die Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Schritt 2: Vorbereiten des nahtlosen einmaligen Anmeldens

Damit Ihre Geräte das nahtlose einmalige Anmelden verwenden können, müssen Sie den Intranetzoneneinstellungen der Benutzer mithilfe einer Gruppenrichtlinie in Active Directory eine Azure AD-URL hinzufügen.

Webbrowser berechnen standardmäßig anhand der URL automatisch die richtige Zone (Internet oder Intranet). Beispielsweise ist **http:\/\/contoso/** der Intranetzone und **http:\/\/intranet.contoso.com** der Internetzone zugeordnet (da die URL einen Punkt enthält). Browser senden keine Kerberos-Tickets an Cloudendpunkte wie die Azure AD-URL, sofern Sie die URL nicht explizit der Intranetzone des Browsers hinzufügen.

Führen Sie die [Schritte](./how-to-connect-sso-quick-start.md) aus, um mit der Veröffentlichung der erforderlichen Änderungen für Ihre Geräte zu beginnen.

> [!IMPORTANT]
> Wenn Sie diese Änderung vornehmen, ergeben sich keine Änderungen der Art und Weise, wie sich Benutzer bei Azure AD anmelden. Es ist aber wichtig, dass Sie diese Konfiguration vor dem Fortfahren auf alle Ihre Geräte anwenden. Benutzer, die sich an Geräten ohne diese Konfiguration anmelden, müssen einfach einen Benutzernamen und ein Kennwort eingeben, um sich an Azure AD anzumelden.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Schritt 3: Ändern der Anmeldemethode in die Kennworthashsynchronisierung und Aktivieren des nahtlosen einmaligen Anmeldens

Sie haben zwei Möglichkeiten, um die Anmeldemethode in die Kennworthashsynchronisierung zu ändern und das nahtlose einmalige Anmelden zu aktivieren.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Option A: Wechseln vom Verbund zur Kennworthashsynchronisierung mit Azure AD Connect

Verwenden Sie diese Methode, wenn Sie Ihre AD FS-Umgebung ursprünglich mit Azure AD Connect konfiguriert haben. Sie können diese Methode nicht verwenden, wenn Sie für die ursprüngliche Konfiguration Ihrer AD FS-Umgebung *nicht* Azure AD Connect verwendet haben.

Ändern Sie zuerst die Anmeldemethode:

1. Öffnen Sie auf dem Azure AD Connect-Server den Azure AD Connect-Assistenten.
2. Wählen Sie **Benutzeranmeldung ändern** und dann **Weiter**. 

   ![Screenshot: Option „Benutzeranmeldung ändern“ auf der Seite „Weitere Aufgaben“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort eines globalen Administratorkontos ein.
4. Wählen Sie auf der Seite **Benutzeranmeldung** die Schaltfläche **Kennworthashsynchronisierung**. Aktivieren Sie das Kontrollkästchen **Benutzerkonten nicht konvertieren**. Diese Option ist veraltet. Wählen Sie **Einmaliges Anmelden aktivieren** und dann **Weiter**.

   ![Screenshot: Seite „Einmaliges Anmelden aktivieren“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Ab Azure AD Connect-Version 1.1.880.0 ist das Kontrollkästchen **Nahtloses einmaliges Anmelden** standardmäßig aktiviert.

   > [!IMPORTANT]
   > Sie können die Warnungen, dass die Benutzerkonvertierung und die vollständige Kennworthashsynchronisierung für die Konvertierung von Verbund- zu Cloudauthentifizierung erforderlich sind, ignorieren. Beachten Sie, dass diese Schritte nicht mehr erforderlich sind. Wenn diese Warnungen weiterhin angezeigt werden, sollten Sie überprüfen, ob Sie jeweils die neueste Version von Azure AD Connect und dieser Anleitung verwenden. Weitere Informationen hierzu finden Sie im Abschnitt [Aktualisieren von Azure AD](#update-azure-ad-connect).

5. Geben Sie auf der Seite **Einmaliges Anmelden aktivieren** die Anmeldeinformationen des Domänenadministratorkontos ein, und wählen Sie dann **Weiter**.

   ![Screenshot der Seite „Einmaliges Anmelden aktivieren“, auf der Sie die Anmeldeinformationen des Domänenadministratorkontos eingeben können](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Die Kontoanmeldeinformationen des Domänenadministrators sind erforderlich, um nahtloses einmaliges Anmelden zu aktivieren. Im Rahmen des Prozesses werden die folgenden Aktionen durchgeführt, für die diese erhöhten Rechte benötigt werden. Die Kontoanmeldeinformationen des Domänenadministrators werden weder in Azure AD Connect noch in Azure AD gespeichert. Die Kontoanmeldeinformationen des Domänenadministrators werden nur verwendet, um das Feature zu aktivieren. Die Anmeldeinformationen werden verworfen, nachdem der Prozess erfolgreich abgeschlossen wurde.
   >
   > 1. Ein Computerkonto mit dem Namen „AZUREADSSOACC“ (steht für Azure AD) wird auf Ihrer lokalen Active Directory-Instanz erstellt.
   > 2. Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird auf sichere Weise für Azure AD freigegeben.
   > 3. Es werden zwei Kerberos-Dienstprinzipalnamen (SPNs) erstellt, um die zwei URLs darzustellen, die während der Azure AD-Anmeldung verwendet werden.

6. Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist. Wählen Sie anschließend **Konfigurieren**.

      ![Screenshot: Seite „Bereit zur Konfiguration“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > An diesem Punkt werden Ihre gesamten Verbunddomänen auf die verwaltete Authentifizierung umgestellt. Die neue Authentifizierungsmethode ist nun die Kennworthashsynchronisierung.

7. Wählen Sie im Azure AD-Portal die Optionen **Azure Active Directory** > **Azure AD Connect**.
8. Überprüfen Sie diese Einstellungen:
   * **Verbund** ist auf **Deaktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
   * **Kennwortsynchronisierung** ist auf **Aktiviert** festgelegt.<br /> 

   ![Screenshot mit den Einstellungen im Abschnitt „Benutzeranmeldung“ im Azure AD-Portal](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Springen Sie zu [Testen und nächste Schritte](#testing-and-next-steps).

   > [!IMPORTANT]
   > Überspringen Sie den Abschnitt **Option B: Wechseln vom Verbund zur Kennworthashsynchronisierung mit Azure AD Connect und PowerShell**. Die Schritte in diesem Abschnitt gelten nicht, wenn Sie Option A gewählt haben, um die Anmeldemethode in die Kennworthashsynchronisierung zu ändern und nahtloses einmaliges Anmelden zu aktivieren.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Option B: Wechseln vom Verbund zur Kennworthashsynchronisierung mit Azure AD Connect und PowerShell

Verwenden Sie diese Option, wenn Sie Ihre Verbunddomänen ursprünglich nicht mit Azure AD Connect konfiguriert haben. Während dieses Prozesses aktivieren Sie das nahtlose einmalige Anmelden und stellen Ihre Domänen von „Verbund“ auf „Verwaltet“ um.

1. Öffnen Sie auf dem Azure AD Connect-Server den Azure AD Connect-Assistenten.
2. Wählen Sie **Benutzeranmeldung ändern** und dann **Weiter**.
3. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort für ein globales Administratorkonto ein.
4. Wählen Sie auf der Seite **Benutzeranmeldung** die Schaltfläche **Kennworthashsynchronisierung**. Wählen Sie **Einmaliges Anmelden aktivieren** und dann **Weiter**.

   Vor dem Aktivieren der Kennworthashsynchronisierung: ![Screenshot: Option „Nicht konfigurieren“ auf der Seite „Benutzeranmeldung“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Nach dem Aktivieren der Kennworthashsynchronisierung: ![Screenshot: Neue Optionen auf der Seite „Benutzeranmeldung“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Ab Azure AD Connect-Version 1.1.880.0 ist das Kontrollkästchen **Nahtloses einmaliges Anmelden** standardmäßig aktiviert.

5. Geben Sie auf der Seite **Einmaliges Anmelden aktivieren** die Anmeldeinformationen für ein Domänenadministratorkonto ein, und wählen Sie dann **Weiter**.

   > [!NOTE]
   > Die Kontoanmeldeinformationen des Domänenadministrators sind erforderlich, um nahtloses einmaliges Anmelden zu aktivieren. Im Rahmen des Prozesses werden die folgenden Aktionen durchgeführt, für die diese erhöhten Rechte benötigt werden. Die Kontoanmeldeinformationen des Domänenadministrators werden weder in Azure AD Connect noch in Azure AD gespeichert. Die Kontoanmeldeinformationen des Domänenadministrators werden nur verwendet, um das Feature zu aktivieren. Die Anmeldeinformationen werden verworfen, nachdem der Prozess erfolgreich abgeschlossen wurde.
   >
   > 1. Ein Computerkonto mit dem Namen „AZUREADSSOACC“ (steht für Azure AD) wird auf Ihrer lokalen Active Directory-Instanz erstellt.
   > 2. Der Kerberos-Entschlüsselungsschlüssel des Computerkontos wird auf sichere Weise für Azure AD freigegeben.
   > 3. Es werden zwei Kerberos-Dienstprinzipalnamen (SPNs) erstellt, um die zwei URLs darzustellen, die während der Azure AD-Anmeldung verwendet werden.

6. Vergewissern Sie sich auf der Seite **Bereit zur Konfiguration**, dass das Kontrollkästchen **Starten Sie den Synchronisierungsvorgang, nachdem die Konfiguration abgeschlossen wurde** aktiviert ist. Wählen Sie anschließend **Konfigurieren**.

   ![Screenshot: Schaltfläche „Konfigurieren“ auf der Seite „Bereit zur Konfiguration“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Wenn Sie die Schaltfläche **Konfigurieren** wählen, wird nahtloses einmaliges Anmelden wie im vorherigen Schritt beschrieben konfiguriert. Die Konfiguration der Kennworthashsynchronisierung wird nicht geändert, da sie früher aktiviert wurde.

   > [!IMPORTANT]
   > Derzeit werden keine Änderungen daran vorgenommen, wie sich die Benutzer anmelden.

7. Überprüfen Sie im Azure AD-Portal diese Einstellungen:
   * **Verbund** ist auf **Aktiviert** festgelegt.
   * **Nahtloses einmaliges Anmelden** ist auf **Aktiviert** festgelegt.
   * **Kennwortsynchronisierung** ist auf **Aktiviert** festgelegt.

   ![Screenshot: Einstellungen im Abschnitt „Benutzeranmeldung“](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Konvertieren von Domänen von Verbunddomänen in verwaltete Domänen

An dieser Stelle ist „Verbund“ immer noch aktiviert und kann für Ihre Domänen verwendet werden. Um mit der Bereitstellung fortzufahren, muss jede Domäne von „Verbund“ in „Verwaltet“ konvertiert werden, um die Benutzerauthentifizierung über Kennworthashsynchronisierung zu erzwingen.

> [!IMPORTANT]
> Sie müssen nicht alle Domänen gleichzeitig konvertieren. Sie können auch mit einer Testdomäne auf Ihrem Produktionsmandanten oder mit der Domäne beginnen, die bei Ihnen die niedrigste Anzahl von Benutzern aufweist.

Führen Sie die Konvertierung durch, indem Sie das Azure AD PowerShell-Modul verwenden:

1. Melden Sie sich in PowerShell an Azure AD an, indem Sie ein Konto vom Typ „Globaler Administrator“ verwenden.
2. Führen Sie folgenden Befehl aus, um die erste Domäne zu konvertieren:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Wählen Sie im Azure AD-Portal die Optionen **Azure Active Directory** > **Azure AD Connect**.
4. Stellen Sie mithilfe des folgenden Befehls sicher, dass die Domäne in eine verwaltete Domäne konvertiert wurde:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testen und nächste Schritte

Führen Sie die folgenden Aufgaben durch, um die Kennworthashsynchronisierung zu überprüfen und den Konvertierungsvorgang abzuschließen.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testen der Authentifizierung mit der Kennworthashsynchronisierung 

Als für Ihren Mandanten die Verbundidentität verwendet wurde, wurden Benutzer von der Azure AD-Anmeldeseite an Ihre AD FS-Umgebung umgeleitet. Nachdem der Mandant jetzt für die Kennworthashsynchronisierung konfiguriert wurde und nicht mehr die Verbundauthentifizierung verwendet wird, werden Benutzer nicht mehr an AD FS umgeleitet. Stattdessen melden sich Benutzer direkt auf der Azure AD-Anmeldeseite an.

Testen Sie die Kennworthashsynchronisierung wie folgt:

1. Öffnen Sie Internet Explorer im InPrivate-Modus, damit Sie nicht per einmaligem Anmelden automatisch angemeldet werden.
2. Navigieren Sie zur Office 365-Anmeldeseite ([https://portal.office.com](https://portal.office.com/)).
3. Geben Sie den Benutzerprinzipalnamen (UPN) eines Benutzers ein, und wählen Sie anschließend **Weiter**. Achten Sie darauf, dass Sie den UPN eines Hybridbenutzers eingeben, der von Ihrer lokalen Active Directory-Instanz synchronisiert und für den zuvor die Verbundauthentifizierung verwendet wurde. Es wird eine Seite angezeigt, auf der Sie den Benutzernamen und das Kennwort eingeben können:

   ![Screenshot: Anmeldeseite, auf der Sie den Benutzernamen eingeben](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Screenshot: Anmeldeseite, auf der Sie ein Kennwort eingeben](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Nachdem Sie das Kennwort eingegeben und **Anmelden** gewählt haben, werden Sie an das Office 365-Portal umgeleitet.

   ![Screenshot: Office 365-Portal](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testen des nahtlosen einmaligen Anmeldens

1. Melden Sie sich an einem in die Domäne eingebundenen Computer an, der mit dem Unternehmensnetzwerk verbunden ist.
2. Navigieren Sie in Internet Explorer oder Chrome zu einer der folgenden URLs (ersetzen Sie „contoso“ durch Ihre Domäne):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   Der Benutzer wird kurz auf die Azure AD-Anmeldeseite umgeleitet, auf der die Meldung „Anmeldung wird versucht“ angezeigt wird. Der Benutzer wird nicht zum Eingeben eines Benutzernamens oder Kennworts aufgefordert.<br />

   ![Screenshot: Azure AD-Anmeldeseite und Meldung](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
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

### <a name="troubleshooting"></a>Problembehandlung

Ihr Supportteam muss wissen, wie jegliche Authentifizierungsprobleme behoben werden können, die entweder während oder nach dem Wechsel von „Verbund“ zu „Verwaltet“ auftreten können. Verwenden Sie die folgende Dokumentation zur Problembehandlung, um Ihrem Supportteam zu helfen, sich mit den allgemeinen Schritten zur Problembehandlung und den entsprechenden Maßnahmen vertraut zu machen, die Ihnen helfen können, das Problem zu isolieren und zu beheben.

[Problembehandlung bei der Azure Active Directory-Kennworthashsynchronisierung](./tshoot-connect-password-hash-synchronization.md)

[Problembehandlung beim nahtlosen einmaligen Anmelden mit Azure Active Directory](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Durchführen des Rollovers des Kerberos-Entschlüsselungsschlüssels für das einmalige Anmelden

Es ist wichtig, häufig einen Rollover für den Kerberos-Entschlüsselungsschlüssel des AZUREADSSOACC-Computerkontos (steht für Azure AD) durchzuführen. Das Computerkonto AZUREADSSOACC wird in Ihrer lokalen AD-Gesamtstruktur erstellt. Es wird empfohlen, den Rollover für den Entschlüsselungsschlüssel von Kerberos mindestens alle 30 Tage durchzuführen, um anzupassen, wie die Mitglieder der Active Directory-Domäne Kennwortänderungen übermitteln. Da an das Computerkontoobjekt AZUREADSSOACC kein zugeordnetes Gerät angefügt ist, müssen Sie den Rollover manuell durchführen.

Initiieren Sie den Rollover des Kerberos-Entschlüsselungsschlüssels für das nahtlose einmalige Anmelden auf dem lokalen Server, auf dem Azure AD Connect ausgeführt wird.

Weitere Informationen finden Sie unter [Wie kann ich einen Rollover des Kerberos-Entschlüsselungsschlüssels des AZUREADSSOACC-Computerkontos durchführen?](./how-to-connect-sso-faq.md).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Entwurfskonzepte für Azure AD Connect](plan-connect-design-concepts.md).
* Wählen Sie die [richtige Authentifizierung](./choose-ad-authn.md).
* Erfahren Sie mehr über [unterstützte Topologien](plan-connect-design-concepts.md).
