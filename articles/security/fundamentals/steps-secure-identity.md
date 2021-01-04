---
title: Sichern Ihrer Azure AD-Identitätsinfrastruktur
titleSuffix: Azure Active Directory
description: Dieses Dokument beschreibt eine Reihe von wichtigen Aktionen, die Administratoren implementieren sollten, um ihre Organisation mithilfe von Azure AD-Funktionen zu schützen.
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: 4703a76ff0a0b55a438f9f0d164cd7f2d3242a02
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842021"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Fünf Schritte zum Sichern Ihrer Identitätsinfrastruktur

Wenn Sie dieses Dokument lesen, ist Ihnen bereits bewusst, welchen Stellenwert das Thema Sicherheit innehat. Sie sind wahrscheinlich bereits für die Sicherheit Ihrer Organisation verantwortlich. Wenn Sie andere Personen davon überzeugen müssen, wie wichtig Sicherheit ist, geben Sie ihnen die letzte Ausgabe des [Microsoft Intelligence Security-Berichts](https://www.microsoft.com/security/business/security-intelligence-report) zu lesen.

Dieses Dokument unterstützt Sie dabei, mithilfe der Funktionen von Azure Active Directory die Sicherheit in Ihrer Organisation zu verbessern. Es enthält eine Checkliste mit fünf Schritten, mit denen Sie Ihre Organisation gegen Cyberangriffe impfen können.

Diese Checkliste erläutert folgende Aspekte und hilft Ihnen dabei, empfohlene wichtige Aktionen zum Schutz Ihrer Organisation sofort auszuführen:

* Verstärken Sie Ihre Anmeldeinformationen.
* Verringern Sie die Angriffsfläche.
* Automatisieren Sie die Reaktion auf Bedrohungen.
* Nutzen Sie Cloud Intelligence.
* Aktivieren Sie den Self-Service für Endbenutzer.

Achten Sie darauf, dass Sie beim Lesen dieser Prüfliste den Überblick behalten, welche Funktionen und Schritte vollständig sind.

> [!NOTE]
> Viele der Empfehlungen in diesem Dokumentation gelten nur für Anwendungen, die zur Verwendung von Azure Active Directory als Identitätsanbieter konfiguriert sind. Durch Konfigurieren der Apps für das einmalige Anmelden profitieren Sie von folgenden Vorteilen: Richtlinien für Anmeldeinformationen, Bedrohungsermittlung, Überwachung, Protokollierung sowie weiteren Features. Grundlage all dieser Empfehlungen ist die [Azure AD-Anwendungsverwaltung](../../active-directory/manage-apps/what-is-application-management.md).

Die Empfehlungen in diesem Dokument beziehen sich auf die [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), eine automatisierte Bewertung der Identitätssicherheitskonfiguration Ihres Azure AD-Mandanten. Organisationen können über die Seite „Identity Secure Score“ im Azure AD-Portal Lücken in ihrer aktuellen Sicherheitskonfiguration aufdecken, um sicherzustellen, dass sie die aktuellen [bewährten Methoden](identity-management-best-practices.md) zur Sicherheit von Microsoft befolgen. Durch die Implementierung der einzelnen Empfehlungen auf der Seite „Secure Score“ erhöht sich Ihre Bewertung. Zudem können Sie Ihren Fortschritt nachverfolgen und Ihre Implementierung mit denen anderer Organisationen von ähnlicher Größe oder innerhalb Ihrer Branche vergleichen.

![Identity Secure Score](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Viele der hier beschriebenen Funktionen erfordern ein Azure AD Premium-Abonnement, während manche auch kostenlos sind. Weitere Informationen finden Sie unter [Azure Active Directory (AD) – Preise](https://azure.microsoft.com/pricing/details/active-directory/) und [Prüfliste für die Azure AD-Bereitstellung](../../active-directory/fundamentals/active-directory-deployment-checklist-p2.md).

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Vorbereitungen Schützen privilegierter Konten mit MFA

Bevor Sie damit beginnen, diese Checkliste durchzuarbeiten, stellen Sie sicher, dass Ihre Organisation nicht bereits gefährdet ist, bevor Sie mit der Checkliste fertig sind. Als Erstes müssen Sie privilegierte Konten schützen.

Angreifer, die Kontrolle über privilegierte Konten erhalten, können enormen Schaden anrichten. Daher ist es ausgesprochen wichtig, zuerst diese Konten zu schützen. Verwenden Sie die [Azure AD-Sicherheitsstandards](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) oder den [bedingten Zugriff](../../active-directory/conditional-access/plan-conditional-access.md), um die [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (mehrstufige Authentifizierung, MFA) für alle Administratoren in Ihrer Organisation zu aktivieren und zu erzwingen. Wenn Sie MFA noch nicht implementiert haben, tun Sie das sofort! Dieses Feature hat oberste Priorität.

Alles fertig? Dann beginnen wir mit der Checkliste.

## <a name="step-1---strengthen-your-credentials"></a>Schritt 1: Verstärken Sie Ihre Anmeldeinformationen.

Die meisten Sicherheitsverletzungen in Unternehmen rühren daher, dass ein Konto mithilfe einer von nur wenigen Methoden gefährdet wurde, beispielsweise durch Kennwortspraying, Breach Replay und Phishing. Mehr über diese Angriffsmethoden erfahren Sie in diesem Video (45 Min.):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Sicherstellen, dass Ihre Organisation eine starke Authentifizierung verwendet

Angesichts der Häufigkeit, mit der Kennwörter erraten, per Phishing herausgefunden, mithilfe von Schadsoftware gestohlen oder einfach wiederverwendet werden, ist es von entscheidender Bedeutung, Kennwörter mit sicheren Anmeldeinformationen zu stärken. Erfahren Sie mehr über die [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md).

Die allgemeine Ebene der Identitätssicherheit können Sie mit den [Azure AD-Sicherheitsstandards](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) mit nur einem Klick aktivieren. Sicherheitsstandards erzwingen Azure AD MFA für alle Benutzer in einem Mandanten und blockieren mandantenweite Anmeldungen über ältere Protokolle.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Schluss mit herkömmlichen Komplexitätsanforderungen und Ablaufregeln – sperren Sie stattdessen angriffsanfällige Kennwörter.

Viele Organisationen verwenden die herkömmliche Kennwortkomplexität (die Erzwingung von Sonderzeichen, Zahlen sowie Groß- und Kleinschreibung) und Regeln für den Ablauf von Kennwörtern. [Forschungsergebnisse von Microsoft](https://aka.ms/passwordguidance) zeigen, dass diese Richtlinien Benutzer dazu veranlassen, Kennwörter auszuwählen, die verhältnismäßig einfach zu erraten sind.

Das Azure AD-Feature der [dynamisch gesperrten Kennwörter](../../active-directory/authentication/concept-password-ban-bad.md) nutzt aktuelle Informationen zum Angreiferverhalten, um Benutzer daran zu hindern, leicht zu erratende Kennwörter festzulegen. Diese Funktion ist immer aktiviert, wenn Benutzer in der Cloud erstellt werden, steht ab sofort auch für Hybridorganisationen bei der Bereitstellung des [Kennwortschutzes für Azure AD für Windows Server Active Directory](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) zur Verfügung. Der Azure AD-Kennwortschutz hindert Benutzer daran, diese allgemeinen Kennwörtern auszuwählen und kann erweitert werden, um Kennwörter, die von Ihnen angegebene benutzerdefinierte Schlüsselwörter enthalten, zu blockieren. Beispielsweise können Sie Ihre Benutzer daran hindern, Kennwörter mit Produktnamen Ihres Unternehmens oder Namen eines regionalen Sportteams auszuwählen.

Microsoft empfiehlt, die folgende moderne Kennwortrichtlinie basierend auf dem [NIST-Leitfaden](https://pages.nist.gov/800-63-3/sp800-63b.html) anzuwenden:

1. Fordern Sie Kennwörter mit mindestens 8 Zeichen. Längere Kennwörter sind nicht unbedingt besser, da sie dazu führen, dass Benutzer vorhersagbare Kennwörter verwenden oder ihre Kennwörter in Dateien speichern oder sich aufschreiben.
2. Deaktivieren Sie Ablaufregeln – diese veranlassen Benutzer dazu, einfach zu erratende Kennwörter wie **Frühling2019!** zu verwenden.
3. Deaktivieren Sie Anforderungen an die Zeichenzusammensetzung, und hindern Sie Benutzer daran, angriffsanfällige Kennwörter zu verwenden – dies führt dazu, dass Benutzer vorhersagbare Zeichenersetzungen in Kennwörtern verwenden.

Wenn Sie Identitäten direkt in Azure AD erstellen, können Sie den [Kennwortablauf mithilfe von PowerShell für Benutzer verhindern](../../active-directory/authentication/concept-sspr-policy.md). Hybridorganisationen sollten diese Richtlinien mit [Domänengruppen-Richtlinieneinstellungen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) oder [Windows PowerShell](/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy) implementieren.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Schutz vor kompromittierten Anmeldeinformationen und Verstärken der Ausfallsicherung

Wenn Ihre Organisation eine Hybrididentitätslösung mit Pass-Through-Authentifizierung oder Verbundauthentifizierung verwendet, sollten Sie aus den beiden folgenden Gründen die Kennworthashsynchronisierung aktivieren:

* Der Bericht zu [Benutzern mit kompromittierten Anmeldeinformationen](../../active-directory/identity-protection/overview-identity-protection.md) in der Azure AD-Verwaltung warnt Sie vor Kombinationen aus Benutzername und Kennwort, die im „Dark Web“ verbreitet wurden. Eine schier unglaubliche Menge an Kennwörtern wird über drei Wege kompromittiert: Phishing, Schadsoftware und die Wiederverwendung von Kennwörtern auf Drittanbieterwebsites, die später gehackt werden. Microsoft findet eine Vielzahl dieser kompromittierten Anmeldeinformationen und teilt Ihnen in diesem Bericht mit, wenn sie Anmeldeinformationen in Ihrer Organisation entsprechen – aber nur, wenn Sie die [Kennworthashsynchronisierung aktivieren](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)!
* Wenn ein lokales System ausfällt (beispielsweise aufgrund eines Ransomware-Angriffs), können Sie zur [Cloudauthentifizierung mit Kennworthashsynchronisierung](../../active-directory/hybrid/choose-ad-authn.md) wechseln. Diese sekundäre Authentifizierungsmethode ermöglicht es, weiterhin auf Apps zuzugreifen, die für die Authentifizierung mit Azure Active Directory konfiguriert sind, einschließlich Microsoft 365. In diesem Fall müssen IT-Mitarbeiter nicht auf persönliche E-Mail-Konten zurückgreifen, um bis zur Behebung des lokalen Ausfalls Daten freizugeben.

Informieren Sie sich, wie die [Kennworthashsynchronisierung](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) funktioniert.

> [!NOTE]
> Wenn Sie die Kennworthashsynchronisierung aktivieren und Azure AD Domain Services verwenden, werden Kerberos-Hashes (AES-256) und optional NTLM-Hashes (RC4, kein Salt) ebenfalls verschlüsselt und mit Azure AD synchronisiert.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementieren der intelligenten AD FS-Extranetsperre

Organisationen, die Anwendungen für die direkte Authentifizierung in Azure AD konfigurieren, profitieren von der [intelligenten Kennwortsperrung von Azure AD](../../active-directory/authentication/concept-sspr-howitworks.md). Wenn Sie AD FS in Windows Server 2012 R2 verwenden, implementieren Sie den [Extranetsperrschutz](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection) von AD FS. Wenn Sie AD FS in Windows Server 2016 verwenden, implementieren Sie die [intelligente Extranetsperre](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Die intelligente Extranetsperre von AD FS schützt vor Brute-Force-Angriffen auf AD FS und verhindert gleichzeitig, dass Benutzer aus Active Directory ausgesperrt werden.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Die Vorteile von in sich sicheren, einfacher verwendbaren Anmeldeinformationen

Über [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) können Sie Kennwörter durch eine starke zweistufige Authentifizierung auf PCs und Mobilgeräten ersetzen. Diese Authentifizierung besteht aus einer neuen Form von Anmeldeinformationen, die sicher an ein Gerät gebunden sind und biometrische Daten oder eine PIN verwenden.

## <a name="step-2---reduce-your-attack-surface"></a>Schritt 2: Verringern Sie die Angriffsfläche.

Angesichts des Ausmaßes an Schaden, den gehackte Kennwörter anrichten, ist es von entscheidender Bedeutung, die Angriffsfläche in Ihrer Organisation so klein wie möglich zu halten. Folgende Maßnahmen helfen dabei, die Angriffsfläche zu verringern: Außerbetriebnahme älterer, unsicherer Protokolle, Beschränken des Zugriffs auf Einstiegspunkte und eine schärfere Kontrolle des administrativen Zugriffs auf Ressourcen.

### <a name="block-legacy-authentication"></a>Blockieren älterer Authentifizierungsmethoden

Apps, die eigene, veraltete Methoden zur Authentifizierung bei Azure AD und zum Zugriff auf Unternehmensdaten verwenden, stellen für Organisationen ebenfalls ein Risiko dar. POP3-, IMAP4- oder SMTP-Clients sind einige Beispiele für Apps, die ältere Authentifizierungsmethoden verwenden. Solche Apps authentifizieren sich im Namen des Benutzers und hindern Azure AD daran, moderne Sicherheitsauswertungen durchzuführen. Moderne Authentifizierungsmethoden dagegen senken das Sicherheitsrisiko, weil sie die mehrstufige Authentifizierung und den bedingten Zugriff unterstützen. Wir empfehlen die folgenden drei Maßnahmen:

1. Blockieren Sie [ältere Authentifizierungsmethoden, wenn Sie AD FS verwenden](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. Richten Sie [SharePoint Online und Exchange Online für moderne Authentifizierungsverfahren](../../active-directory/conditional-access/block-legacy-authentication.md) ein.
3. Wenn Sie über Azure AD Premium verfügen, verwenden Sie Richtlinien für den bedingten Zugriff, um [ältere Authentifizierungsmethoden zu blockieren](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md). Verwenden Sie andernfalls [Azure AD-Sicherheitsstandards](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Blockieren von ungültigen Authentifizierungseinstiegspunkten

Sie müssen grundsätzlich davon ausgehen, dass Sicherheitsverletzungen passieren. Vor diesem Hintergrund müssen Sie die Auswirkungen gehackter Benutzeranmeldeinformationen begrenzen, sobald die Sicherheitsverletzung aufgetreten ist. Berücksichtigen Sie für jede App in Ihrer Umgebung die gültigen Anwendungsfälle: Welche Gruppen, welche Netzwerke, welche Geräte und welche weiteren Elemente sind autorisiert? Blockieren Sie den Rest. Mit dem [bedingten Zugriff von Azure AD](../../active-directory/conditional-access/overview.md) können Sie bestimmte Bedingungen definieren und so steuern, wie autorisierte Benutzer auf ihre Apps und Ressourcen zugreifen.

### <a name="restrict-user-consent-operations"></a>Einschränken von Benutzerzustimmungsvorgängen

Es ist wichtig, dass Sie die verschiedenen [Einwilligungserfahrungen für Azure AD-Anwendungen](../../active-directory/develop/application-consent-experience.md), die [Arten von Berechtigungen und Zustimmungen](../../active-directory/develop/v2-permissions-and-consent.md) sowie ihre Auswirkungen auf den Sicherheitsstatus ihrer Organisation kennen. Standardmäßig können alle Benutzer in Azure AD Anwendungen zulassen, die die Microsoft Identity Platform nutzen, um auf die Daten Ihrer Organisation zuzugreifen. Zwar können Benutzer durch die Möglichkeit, eine Zustimmung selbst zu erteilen, mühelos nützliche Anwendungen erwerben, die in Microsoft 365, Azure und andere Dienste integriert werden können, dies kann jedoch bei nachlässiger Nutzung und Überwachung ein Risiko darstellen.

Microsoft empfiehlt eine Einschränkung der Benutzereinwilligung, um die Angriffsfläche und dieses Risiko zu verringern. Sie können auch [App-Einwilligungsrichtlinien (Vorschau)](../../active-directory/manage-apps/configure-user-consent.md) verwenden, um die Endbenutzereinwilligung auf verifizierte Herausgeber und nur auf die von Ihnen ausgewählten Berechtigungen einzuschränken. Wenn die Endbenutzereinwilligung eingeschränkt ist, werden zuvor erteilte Einwilligungen nach wie vor berücksichtigt, alle zukünftigen Einwilligungsvorgänge müssen jedoch von einem Administrator ausgeführt werden. Bei einer Einschränkung können Benutzer die Administratoreinwilligung über einen integrierten [Workflow zum Anfordern der Administratoreinwilligung](../../active-directory/manage-apps/configure-admin-consent-workflow.md) oder über Ihre eigenen Supportprozesse anfordern. Bevor Sie die Endbenutzereinwilligung deaktivieren, lesen Sie unsere [Empfehlungen](../../active-directory/manage-apps/manage-consent-requests.md) für die Planung dieser Änderung in Ihrer Organisation. Ziehen Sie für Anwendungen, auf die Sie allen Benutzern Zugriff gewähren möchten, in Betracht, [die Zustimmung im Namen aller Benutzer zu erteilen](../../active-directory/develop/v2-admin-consent.md) und dadurch sicherzustellen, dass Benutzer, die noch nicht einzeln zugestimmt haben, auf die App zugreifen können. Wenn Sie nicht möchten, dass diese Anwendungen allen Benutzern in allen Szenarien zur Verfügung stehen, verwenden Sie [Anwendungszuweisung](../../active-directory/manage-apps/assign-user-or-group-access-portal.md) und bedingten Zugriff, um den Benutzerzugriff auf [bestimmte Apps](../../active-directory/conditional-access/concept-conditional-access-cloud-apps.md) zu beschränken.

Stellen Sie sicher, dass Benutzer eine Administratorgenehmigung für neue Anwendungen anfordern können, um die Spannungen zu vermeiden und den Supportaufwand zu minimieren sowie Benutzer daran zu hindern, sich bei Anwendungen mit Nicht Azure AD-Anmeldeinformationen anzumelden. Sobald Zustimmungsvorgänge reguliert werden, sollten Administratoren die App und die erteilten Berechtigungen regelmäßig überprüfen.


### <a name="implement-azure-ad-privileged-identity-management"></a>Implementieren von Azure AD Privileged Identity Management

Die grundsätzliche Annahme, dass Sicherheitsverletzungen passieren, führt auch dazu, dass Sie die Wahrscheinlichkeit reduzieren müssen, dass ein gehacktes Konto mit einer privilegierten Rolle ausgeführt werden kann.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) unterstützt Sie dabei, folgende Aktionen auszuführen, um Kontoberechtigungen so weit wie möglich einzuschränken:

* Identifizieren und verwalten Sie Benutzer, denen Administratorrollen zugewiesen sind.
* Ermitteln Sie nicht verwendete Rollen oder Rollen mit übermäßigen Berechtigungen, die Sie entfernen sollten.
* Richten Sie Regeln ein, die sicherstellen, dass privilegierte Rollen durch mehrstufige Authentifizierung geschützt sind.
* Richten Sie Regeln ein, die sicherstellen, dass privilegierte Rollen nur so lange gewährt werden, wie zum Ausführen der entsprechenden Aufgabe erforderlich ist.

Aktivieren Sie Azure AD PIM, sehen Sie sich die Benutzer an, denen Administratorrollen zugewiesen sind, und entfernen Sie nicht benötigte Konten in diesen Rollen. Ändern Sie die Berechtigungen für die verbleibenden privilegierten Benutzer von „dauerhaft“ zu „berechtigt“. Zum Schluss richten Sie geeignete Richtlinien ein, um sicherzustellen, dass Benutzer mit der erforderlichen Änderungssteuerung sicher auf diese privilegierten Rollen zugreifen können, wenn es erforderlich ist.

Wenn Sie den Prozess für die Bereitstellung privilegierter Konten einrichten, befolgen Sie die [bewährten Methoden zum Einrichten von mindestens zwei Notfallkonten](../../active-directory/roles/security-planning.md), um sicherzustellen, dass Sie weiterhin Zugriff auf Azure AD haben, falls Sie sich selbst ausgesperrt haben.

## <a name="step-3---automate-threat-response"></a>Schritt 3: Automatisieren Sie die Reaktion auf Bedrohungen.

Azure Active Directory verfügt über eine Vielzahl von Funktionen, die Angriffe automatisch abfangen, sodass zwischen Erkennung und Reaktion keinerlei Zeit vergeht. Sie können Kosten und Risiken verringern, indem Sie den Zeitraum verkürzen, in dem sich Angreifer in Ihrer Umgebung einnisten können. Im Folgenden finden Sie die konkreten Schritte, die Sie ausführen sollten.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Implementieren einer Sicherheitsrichtlinie in Bezug auf das Benutzerrisiko mithilfe von Azure AD Identity Protection

Das Benutzerrisiko bezeichnet die Wahrscheinlichkeit, dass die Identität eines Benutzers gefährdet ist, und wird anhand der [Benutzerrisikoerkennungen](../../active-directory/identity-protection/overview-identity-protection.md) berechnet, die der Identität eines Benutzers zugeordnet sind. Bei einer Benutzerrisikorichtlinie handelt es sich um eine Richtlinie für bedingten Zugriff, die die Risikostufe eines bestimmten Benutzers oder einer bestimmten Gruppe auswertet. Basierend auf den Risikostufen „Niedrig“, „Mittel“ oder „Hoch“ kann eine Richtlinie so konfiguriert werden, dass sie den Zugriff blockiert oder eine sichere Kennwortänderung über die mehrstufige Authentifizierung erfordert. Microsoft empfiehlt, bei Benutzern mit hohem Risiko eine sichere Kennwortänderung anzufordern.

![Der Screenshot zeigt nach Risikotyp gekennzeichnete Benutzer, wobei ein Benutzer ausgewählt ist.](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Implementieren einer Richtlinie in Bezug auf das Anmelderisiko mithilfe von Azure AD Identity Protection

Das Anmelderisiko bezeichnet die Wahrscheinlichkeit, dass eine Person, bei der es sich nicht um den Kontobesitzer handelt, versucht, sich mit dieser Identität anzumelden. Bei einer [Richtlinie in Bezug auf das Anmelderisiko](../../active-directory/identity-protection/overview-identity-protection.md) handelt es sich um eine Richtlinie für bedingten Zugriff, die die Risikostufe für einen bestimmten Benutzer oder eine bestimmte Gruppe auswertet. Basierend auf der Risikostufe (hoch/mittel/niedrig) kann eine Richtlinie konfiguriert werden, die den Zugriff blockiert oder die mehrstufige Authentifizierung erzwingt. Stellen Sie sicher, dass Sie für Anmeldevorgänge mit der Stufe „mittel“ oder höher die mehrstufige Authentifizierung erzwingen.

![Anmelden von anonymen IP-Adressen](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Schritt 4: Nutzen Sie Cloud Intelligence.

Die Überwachung und Protokollierung von sicherheitsbezogenen Ereignissen und die entsprechenden Warnungen sind grundlegende Komponenten einer effektiven Schutzstrategie. Sicherheitsprotokolle und Berichte bieten eine elektronische Aufzeichnung verdächtiger Aktivitäten und helfen Ihnen bei der Erkennung von Mustern, die auf versuchte oder erfolgreiche externe Eindringversuche in das Netzwerk und auf interne Angriffe hinweisen können. Sie können im Rahmen der Überwachung entsprechende Benutzeraktivitäten überwachen, die Einhaltung gesetzlicher Bestimmungen dokumentieren, forensische Analysen durchführen und vieles mehr. Warnungen benachrichtigen Sie bei Sicherheitsereignissen.

### <a name="monitor-azure-ad"></a>Überwachen von Azure AD

Die Dienste und Features von Microsoft Azure bieten Ihnen konfigurierbare Optionen für die Überwachung und Protokollierung der Sicherheit, damit Sie Lücken in Ihren Sicherheitsrichtlinien und -mechanismen identifizieren und schließen können, um Sicherheitsverletzungen zu vermeiden. Sie können die [Azure-Protokollierung und -Überwachung](log-audit.md) sowie [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../../active-directory/reports-monitoring/concept-audit-logs.md) verwenden.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Überwachen von Azure AD Connect Health in hybriden Umgebungen

Unter [Überwachen von AD FS mithilfe von Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) finden Sie umfassende Einblicke in mögliche Probleme und die Sichtbarkeit von Angriffen auf Ihre AD FS-Infrastruktur. Azure AD Connect Health bietet Warnungen mit detaillierten Informationen, Lösungsschritten und Links zur entsprechenden Dokumentation sowie Nutzungsanalysen für verschiedene Metriken zum Authentifizierungsdatenverkehr, Funktionen für die Leistungsüberwachung und Berichte.

![Azure AD Connect Health](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Überwachen von Azure AD Identity Protection-Ereignissen

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) ist ein Benachrichtigungs-, Überwachungs- und Berichterstattungstool, mit dem Sie potenzielle Sicherheitslücken erkennen können, die Auswirkungen auf die Identitäten Ihrer Organisation haben können. Das Tool erkennt Risiken, wie z. B. kompromittierte Anmeldeinformationen, unmögliche Ortswechsel und Anmeldungen von infizierten Geräten sowie anonyme IP-Adressen, IP-Adressen in Zusammenhang mit der verdächtigen Aktivität und unbekannte Standorte. Aktivieren Sie die Benachrichtigungen, um E-Mails zu gefährdeten Benutzern und/oder eine wöchentliche Zusammenfassung zu erhalten.

Azure AD Identity Protection bietet zwei wichtige Berichte, die Sie täglich überwachen sollten:
1. Berichte zu riskanten Anmeldungen behandeln Benutzeranmeldeaktivitäten, denen Sie nachgehen sollten, weil die Anmeldung möglicherweise nicht durch den rechtmäßigen Besitzer erfolgt ist.
2. In Berichten zu riskanten Benutzern werden Benutzerkonten ermittelt, die möglicherweise kompromittiert sind. Dies kann etwa Ereignisse betreffen, bei denen offengelegte Anmeldeinformationen erkannt wurden oder bei denen sich der Benutzer von verschiedenen Standorten aus angemeldet hat, bei denen ein Ortswechsel durch einen Benutzer unmöglich erscheint.

![Der Screenshot zeigt den Bereich Azure AD Identity Protection mit Benutzern und deren Risikostufen.](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Überwachen von Apps und Berechtigungen, denen zugestimmt wurde

Benutzer können zu einer gefährdeten Website oder App weitergeleitet werden, die Zugriff auf ihre Profilinformationen und Benutzerdaten (z. B. die E-Mail-Adresse) erlangt. Ein böswilliger Akteur kann mithilfe der Berechtigungen, denen zugestimmt wurde, Postfachdaten verschlüsseln und ein Lösegeld für die Herausgabe der Postfachdaten fordern. Die von Benutzern erteilten [Berechtigungen sollten von Administratoren überprüft und überwacht werden](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) bzw. die Möglichkeit der Benutzer, die Zustimmung standardmäßig zu erteilen, sollte deaktiviert werden.

Zusätzlich zum Überwachen der von Benutzern erteilten Berechtigungen können Sie [risikobehaftete oder unerwünschte OAuth-Anwendungen](/cloud-app-security/investigate-risky-oauth) in Premium-Umgebungen suchen.

## <a name="step-5---enable-end-user-self-service"></a>Schritt 5: Aktivieren des Self-Service für Endbenutzer

Ein wichtiges Ziel ist es, Sicherheit und Produktivität sorgfältig gegeneinander abzuwägen. Der Grundgedanke ist hier – ebenso wie beim Aufbau eines langfristig tragfähigen Fundaments für die Sicherheit –, dass Sie den Betriebsablauf für Ihre Organisation so reibungslos wie möglich gestalten möchten, indem Sie Ihren Benutzern mehr Selbstständigkeit bieten, aber gleichzeitig immer wachsam bleiben.

### <a name="implement-self-service-password-reset"></a>Implementieren der Self-Service-Kennwortzurücksetzung

Die [Self-Service-Kennwortzurücksetzung](../../active-directory/authentication/tutorial-enable-sspr.md) (Self-Service Password Reset, SSPR) von Azure AD bietet für IT-Administratoren eine einfache Methode, Benutzern das Zurücksetzen oder Entsperren ihrer Kennwörter oder Konten zu ermöglichen, ohne dass das Helpdesk oder ein Administrator eingreifen muss. Das System verfügt über eine ausführliche Berichterstellung, mit der die Kennwortzurücksetzung durch Benutzer nachverfolgt wird, sowie über eine Benachrichtigungsfunktion, mit der Sie über eine fehlerhafte oder missbräuchliche Nutzung informiert werden.

### <a name="implement-self-service-group-and-application-access"></a>Implementieren von Self-Service-Gruppen und Anwendungszugriff

In Azure AD haben Benutzer ohne Administratorrechte die Möglichkeit, den Zugriff auf Ressourcen mithilfe von Sicherheitsgruppen, Microsoft 365-Gruppen, Anwendungsrollen und Zugriffspaketkatalogen zu verwalten.  Über die [Self-Service-Gruppenverwaltung](../../active-directory/enterprise-users/groups-self-service-management.md) können Gruppenbesitzer ihre eigenen Gruppen verwalten, ohne dass ihnen eine Administratorrolle zugewiesen sein muss. Benutzer können auch Microsoft 365-Gruppen erstellen und verwalten, ohne dass Administratoren ihre Anforderungen verarbeiten müssen. Nicht verwendete Gruppen laufen automatisch ab.  Ferner ermöglicht die [Azure AD-Berechtigungsverwaltung](../../active-directory/governance/entitlement-management-overview.md) die Delegierung und Sichtbarkeit durch umfassende Workflows für Zugriffsanforderungen und durch automatischen Ablauf.  Sie können an Benutzer ohne Administratorrechte die Möglichkeit delegieren, ihre eigenen Zugriffspakete für Gruppen, Teams, Anwendungen und SharePoint Online-Websites in ihrem Besitz zu konfigurieren. Dazu verwenden Sie benutzerdefinierte Richtlinien für Benutzer, die den Zugriff genehmigen müssen, darunter die Konfiguration von Managern von Mitarbeitern und Sponsoren von Geschäftspartnern als genehmigende Personen.

### <a name="implement-azure-ad-access-reviews"></a>Implementieren von Azure AD-Zugriffsüberprüfungen

Mit [Azure AD-Zugriffsüberprüfungen](../../active-directory/governance/access-reviews-overview.md) können Sie Zugriffspakete und Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und die Zuweisungen privilegierter Rollen verwalten, um einen Sicherheitsstandard einzurichten.  Mit der regelmäßigen Überwachung durch die Benutzer selbst, durch Ressourcenbesitzer und durch andere Prüfer wird sichergestellt, dass Benutzer den Zugriff nicht über längere Zeiträume beibehalten, wenn sie ihn nicht mehr benötigen.

## <a name="summary"></a>Zusammenfassung

Bei sicheren Identitätsinfrastrukturen gilt es eine Vielzahl von Aspekten zu berücksichtigen, aber mit den fünf Schritten dieser Checkliste können Sie schnell eine sicherere Infrastruktur erzielen:

* Verstärken Sie Ihre Anmeldeinformationen.
* Verringern Sie die Angriffsfläche.
* Automatisieren Sie die Reaktion auf Bedrohungen.
* Nutzen Sie Cloud Intelligence.
* Ermöglichen Sie mit Funktionen zur Selbsthilfe eine besser vorhersagbare und umfassendere Sicherheit für Endbenutzer.

Wir freuen uns, dass Sie die Sicherheit von Identitäten so ernst nehmen, und hoffen, dass Sie mithilfe des vorliegenden Dokuments den Sicherheitsstatus Ihrer Organisation stärken können.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Unterstützung bei der Planung und Umsetzung der Empfehlungen benötigen, sehen Sie sich die [Azure AD-Projektbereitstellungspläne](../../active-directory/fundamentals/active-directory-deployment-plans.md) an.

Wenn Sie sicher sind, dass alle diese Schritte abgeschlossen sind, verwenden Sie den [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md) von Microsoft, der die [neuesten bewährten Methoden](identity-management-best-practices.md) und aktuellen Sicherheitsbedrohungen enthält.