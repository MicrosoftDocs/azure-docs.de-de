---
title: Bewährte Methoden für die Azure-Identitäts- und Zugriffssicherheit | Microsoft Docs
description: In diesem Artikel werden einige bewährte Methoden zur Identitätsverwaltung und Zugriffssteuerung mit integrierten Azure-Funktionen beschrieben.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: bf593eab54233ab65435585485dd8e3940f5addb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489818"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung – Bewährte Methoden

In diesem Artikel werden bewährte Methoden für die Azure-Identitätsverwaltung und die Sicherheit der Zugriffssteuerung beschrieben. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) und den Erfahrungen von Kunden wie Ihnen abgeleitet.

Für jede bewährte Methode wird Folgendes beschrieben:

* Wobei es bei der bewährten Methode geht
* Warum Sie die bewährte Methode nutzen sollten
* Was die Folge sein könnte, wenn Sie die bewährte Methode nicht aktivieren
* Mögliche Alternativen zur bewährten Methode
* Wie Sie erfahren können, wie Sie die empfohlenen Vorgehensweisen aktivieren

Dieser Artikel zu den bewährten Methoden für die Azure-Identitätsverwaltung und Zugriffssteuerung basiert auf einer Konsensmeinung und den Fähigkeiten und Funktionssätzen der Azure-Plattform, wie sie zum Erstellungszeitpunkt dieses Artikels existierten.

Mit diesem Artikel möchten wird eine allgemeine Roadmap für einen robusteren Sicherheitsstatus nach der Bereitstellung zur Verfügung stellen, indem Sie mit der Checkliste [5 Schritte zum Sichern Ihrer Identitätsinfrastruktur](steps-secure-identity.md) durch einige unserer wichtigsten Features und Dienste geführt werden.

Meinungen und Technologien ändern sich im Laufe der Zeit. Dieser Artikel wird daher regelmäßig aktualisiert, um diese Änderungen widerzuspiegeln.

Der Artikel enthält bewährte Methoden zur Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung für die folgenden Bereiche:

* Behandeln von Identität als primären Sicherheitsbereich
* Zentralisieren der Identitätsverwaltung
* Verwalten von verbundenen Mandanten
* Einmaliges Anmelden aktivieren
* Aktivieren des bedingten Zugriffs
* Planen routinemäßiger Sicherheitsverbesserungen
* Aktivieren der Kennwortverwaltung
* Erzwingen der mehrstufigen Überprüfung für Benutzer
* Verwenden der rollenbasierten Zugriffssteuerung
* Senken der Gefährdung privilegierter Konten
* Steuern von Standorten, wo sich Ressourcen befinden
* Verwenden von Azure AD für Speicherauthentifizierung

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Behandeln von Identität als primären Sicherheitsbereich

Viele betrachten Identität als primären Sicherheitsbereich. Dies ist eine Abkehr vom traditionellen Schwerpunkt der Netzwerksicherheit. Netzwerkumkreise werden immer durchlässiger, und die Verteidigung des Umkreises kann nicht so effektiv sein wie vor der explosionsartigen Verbreitung von [BYOD](/mem/intune/fundamentals/byod-technology-decisions)-Geräten und Cloudanwendungen.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) ist die Lösung für Identitäts- und Zugriffsverwaltung von Azure. Azure AD ist ein mehrinstanzenfähiger, cloudbasierter Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Azure AD kombiniert grundlegende Verwaltungsdienste, Zugriffsverwaltung für Anwendungen und Identitätsgovernance in einer einzigen Lösung.

In den folgenden Abschnitten werden die Best Practices für die Identitäts- und Zugriffssicherheit mit Azure AD aufgeführt.

**Bewährte Methode**: Zentrieren Sie Sicherheitskontrollen und Ermittlungen um Benutzer- und Dienstidentitäten.
**Detail**: Verwenden Sie Azure AD, um Steuerelemente und Identitäten anzuordnen.

## <a name="centralize-identity-management"></a>Zentralisieren der Identitätsverwaltung

In einem [Hybrididentitätsszenario](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) sollten Sie Ihre lokalen und Cloudverzeichnisse integrieren. Die Integration ermöglicht Ihrem IT-Team, Konten von einem Ort aus zu verwalten, unabhängig davon, wo ein Konto erstellt wird. Die Integration steigert auch die Produktivität Ihrer Benutzer, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird.

**Bewährte Methode**: Einrichten einer einzelnen Azure AD-Instanz. Konsistenz und eine einzige autoritative Quelle verbessern die Übersichtlichkeit und vermindern Sicherheitsrisiken, die auf menschliche Fehler und Komplexität der Konfiguration zurückzuführen sind.
**Detail**: Legen Sie ein einziges Azure AD-Verzeichnis als autoritative Quelle für Unternehmens- und Organisationskonten fest.

**Bewährte Methode**: Integrieren Sie Ihre lokalen Verzeichnisse mit Azure AD.  
**Detail**: Synchronisieren Sie per [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) Ihr lokales Verzeichnis mit Ihrem Cloudverzeichnis.

> [!Note]
> Es gibt [Faktoren, die die Leistung von Azure AD Connect beeinflussen](../../active-directory/hybrid/plan-connect-performance-factors.md). Stellen Sie sicher, dass Azure AD Connect über eine ausreichende Kapazität verfügt, um zu verhindern dass leistungsschwache Systeme Sicherheit und Produktivität beeinträchtigen. Große und komplexe Organisationen (Organisationen mit über 100.000 bereitgestellten Objekten) können die [Empfehlungen](../../active-directory/hybrid/whatis-hybrid-identity.md) nutzen, um ihre Azure AD Connect-Implementierung zu optimieren.

**Bewährte Methode**: Synchronisieren Sie keine Konten mit Azure AD, die über hohe Berechtigungen in Ihrer vorhandenen Active Directory-Instanz verfügen.
**Detail**: Ändern Sie nicht die standardmäßige [Azure AD Connect-Konfiguration](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md), die diese Konten herausfiltert. Eine solche Konfiguration mindert das Risiko, dass Unbefugte aus der Cloud heraus auf lokale Objekte zugreifen (was einen schwerwiegenden Vorfall darstellen könnte).

**Bewährte Methode**: Aktivieren Sie die Kennworthashsynchronisierung.  
**Detail**: Die Kennworthashsynchronisierung ist ein Feature zum Synchronisieren von Benutzerkennworthashes aus einer lokalen Active Directory-Instanz nach einer cloudbasierten Azure AD-Instanz. Diese Synchronisierung schützt davor, dass kompromittierte Anmeldeinformationen aus früheren Angriffe wiedergegeben werden.

Auch wenn Sie den Verbund mit Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS) oder anderen Identitätsanbietern verwenden möchten, können Sie die Kennworthashsynchronisierung optional als eine Sicherung für den Fall einrichten, dass bei Ihren lokalen Servern ein Fehler auftritt oder sie vorübergehend nicht verfügbar sind. Diese Synchronisierung ermöglicht Benutzern, sich bei dem Dienst mit dem gleichen Kennwort anzumelden, das sie zur Anmeldung bei Ihrer lokalen Active Directory-Instanz verwenden. Außerdem ermöglicht es Identity Protection das Erkennen gefährdeter Anmeldeinformationen durch Vergleichen synchronisierter Kennworthashes mit Kennwörtern, die bekanntermaßen gefährdet sind, wenn ein Benutzer dieselbe E-Mail-Adresse und dasselbe Kennwort bei anderen Diensten genutzt hat, die nicht mit Azure AD verbunden sind.

Weitere Informationen finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Bewährte Methode**: Verwenden Sie beim Entwickeln neuer Anwendungen für die Authentifizierung Azure AD.
**Detail**: Verwenden Sie die richtigen Funktionen zum Unterstützen der Authentifizierung:

  - Azure AD für Mitarbeiter
  - [Azure AD B2B](../../active-directory/external-identities/index.yml) für Gastbenutzer und externe Partner
  - [Azure AD B2C](../../active-directory-b2c/index.yml), um zu steuern, wie sich Kunden beim Nutzen Ihrer Anwendungen registriere und, anmelden sowie ihre Profile verwalten

Organisationen, die ihre lokale Identität nicht in ihre Cloudidentität integrieren, haben mehr Aufwand beim Verwalten der Konten. Dieser Aufwand steigert die Wahrscheinlichkeit von Fehlern und Sicherheitsverletzungen.

> [!Note]
> Sie müssen auswählen, in welchen Verzeichnissen sich wichtige Konten befinden und ob die verwendete Administrator-Arbeitsstation von neuen Clouddiensten oder vorhandenen Prozessen verwaltet wird. Die Verwendung vorhandener Verwaltungs- und Identitätsbereitstellungsprozesse kann einige Risiken mindern, jedoch auch die Gefahr mit sich bringen, dass ein Angreifer ein lokales Konto manipuliert und Daten in die Cloud überträgt. Für verschiedene Rollen können sich unterschiedliche Strategien empfehlen (z.B. für IT-Administratoren und Geschäftsbereichsadministratoren). Sie haben zwei Möglichkeiten. Die erste Möglichkeit besteht darin, Azure AD-Konten zu erstellen, die nicht mir Ihrer lokalen Active Directory-Instanz synchronisiert sind. Binden Sie Ihre Administrator-Arbeitsstation in Azure AD ein, und Sie können sie mit Microsoft Intune verwalten und patchen. Die zweite Möglichkeit: Verwenden Sie vorhandene Administratorkonten, indem Sie die Synchronisierung mit Ihrer lokalen Active Directory-Instanz ausführen. Verwenden Sie vorhandene Arbeitsstationen in Ihrer Active Directory-Domäne für Verwaltung und Sicherheit.

## <a name="manage-connected-tenants"></a>Verwalten von verbundenen Mandanten
In Ihrer Sicherheitsorganisation muss Sichtbarkeit gegeben sein, um Risiken zu bewerten und zu bestimmen, ob die Richtlinien Ihrer Organisation und geltende gesetzliche Anforderungen eingehalten werden. Sie müssen dafür sorgen, dass Ihre Sicherheitsorganisation über Sichtbarkeit hinsichtlich aller Abonnements verfügt, die mit Ihrer Produktionsumgebung und Ihrem Produktionsnetzwerk verbunden sind (über [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) oder [Site-to-Site-VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Ein [globaler Administrator/Unternehmensadministrator](../../active-directory/roles/permissions-reference.md#company-administrator-permissions) in Azure AD kann die Zugriffsrechte auf die Rolle [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md#user-access-administrator) erweitern und alle Abonnements und verwalteten Gruppen anzeigen, die mit Ihrer Umgebung verbunden sind.

Lesen Sie den Artikel [Erhöhen der Zugriffsrechte zum Verwalten aller Azure-Abonnements und Verwaltungsgruppen](../../role-based-access-control/elevate-access-global-admin.md) durch, und stellen Sie sicher, dass Sie und Ihre Sicherheitsgruppe alle Abonnements oder Verwaltungsgruppen einsehen können, die mit Ihrer Umgebung verbunden sind. Sie sollten diese erweiterten Zugriffsrechte nach dem Bewerten von Risiken wieder aufheben.

## <a name="enable-single-sign-on"></a>Einmaliges Anmelden aktivieren

In einer primär auf Mobil- und Cloudtechnologie ausgelegten Welt möchten Sie einmaliges Anmelden (Single Sign-On, SSO) bei Geräten, Apps und Diensten von jedem Ort ermöglichen, damit Ihre Benutzer jederzeit und überall produktiv sein können. Wenn Sie mehrere Identitätslösungen verwalten müssen, wird dies nicht nur für die IT-Abteilung zu einem Verwaltungsproblem, sondern auch für die Benutzer, die sich mehrere Kennwörter merken müssen.

Indem Sie für alle Ihre Apps und Ressourcen die gleiche Identitätslösung verwenden, können Sie SSO erreichen. Ihre Benutzer können die gleiche Gruppe von Anmeldeinformationen zum Anmelden und Zugreifen auf die benötigten Ressourcen nutzen. Dabei spielt es keine Rolle, ob eine Ressource lokal ist oder sich in der Cloud befindet.

**Bewährte Methode**: Aktivieren Sie einmaliges Anmelden.  
**Detail**: Azure AD [erweitert das lokale Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md) auf die Cloud. Benutzer können ihr primäres Geschäfts-, Schul- oder Unikonto für ihre in die Domäne eingebundenen Geräte und Unternehmensressourcen sowie alle Web-und SaaS-Anwendungen verwenden, die sie benötigen, um ihre Arbeit zu erledigen. Benutzer müssen sich keine Vielzahl von Benutzernamen und Kennwörtern mehr merken, und ihr Anwendungszugriff kann basierend auf ihrer Gruppenmitgliedschaft in der Organisation sowie ihrem Mitarbeiterstatus automatisch bereitgestellt (oder deaktiviert) werden. Und Sie können diesen Zugriff für Katalog-Apps oder für Ihre eigenen lokalen Apps steuern, die Sie über den [Azure AD-Anwendungsproxy](../../active-directory/manage-apps/application-proxy.md) entwickelt und veröffentlicht haben.

Verwenden Sie SSO, um Benutzern zu ermöglichen, basierend auf ihrem Geschäfts-, Schul- oder Unikonto in Azure AD auf ihre [SaaS-Anwendungen](../../active-directory/manage-apps/what-is-single-sign-on.md) zuzugreifen. Dies gilt nicht nur für Microsoft SaaS-Apps, sondern auch für andere Apps, z.B. [Google-Apps](../../active-directory/saas-apps/google-apps-tutorial.md) und [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md). Sie können Ihre Anwendung so konfigurieren, dass Azure AD als [SAML-Identitätsanbieter](../../active-directory/fundamentals/active-directory-whatis.md) verwendet wird. Als Sicherheitsmaßnahme stellt Azure AD nur dann ein Token aus, das Benutzern die Anmeldung bei der Anwendung erlaubt, wenn ihnen zuvor mit Azure AD Zugriff gewährt wurde. Sie können den Zugriff direkt oder über eine Gruppe gewähren, deren Mitglied die Benutzer sind.

Organisationen, die keine gemeinsame Identität zum Einrichten von SSO für ihre Benutzer und Anwendungen erstellen, haben häufiger mit Szenarien zu tun, in denen Benutzer mehrere Kennwörter haben. Diese Szenarien erhöhen die Wahrscheinlichkeit, dass Benutzer Kennwörter wiederverwenden oder unsichere Kennwörter verwenden.

## <a name="turn-on-conditional-access"></a>Aktivieren des bedingten Zugriffs

Benutzer können mithilfe einer Vielzahl von Geräten und Apps von überall aus auf Ressourcen in Ihrer Organisation zugreifen. Als IT-Administrator möchten Sie sicherstellen, dass diese Geräte Ihren Standards für Sicherheit und Konformität entsprechen. Eine Konzentration darauf, wer auf Ihre Ressourcen Zugriff hat, reicht nicht mehr aus.

Um das Gleichgewicht zwischen Sicherheit und Produktivität zu bewahren, müssen Sie bei der Entscheidung über die Zugriffssteuerung berücksichtigen, auf welche Weise auf eine Ressource zugegriffen wird. Mit dem bedingten Zugriff von Azure AD können Sie diese Anforderung erfüllen. Mit dem bedingten Zugriff können Sie basierend auf bestimmten Bedingungen für den Zugriff auf Ihre Cloud-Apps automatisierte Entscheidungen hinsichtlich der Zugriffssteuerung treffen.

**Bewährte Methode**: Verwalten und steuern Sie den Zugriff auf Unternehmensressourcen.  
**Detail**: Konfigurieren Sie allgemeine [Richtlinien für den bedingten Zugriff](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) in Azure AD basierend auf der Gruppen-, Standort- und Anwendungsvertraulichkeit für SaaS-Apps und über Azure AD verbundene Apps.

**Bewährte Methode**: Blockieren Sie ältere Authentifizierungsprotokolle.
**Detail**: Angreifer nutzen tagtäglich Schwachstellen in älteren Protokollen aus, insbesondere für Kennwort-Spray-Angriffe. Konfigurieren Sie den bedingten Zugriff, um [ältere Protokolle zu blockieren](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Planen routinemäßiger Sicherheitsverbesserungen

Sicherheit wird ständig weiterentwickelt, und es ist wichtig, dass Sie in Ihr Framework für Cloud- und Identitätsverwaltung eine Möglichkeit integrieren, das Wachstum regelmäßig anzuzeigen und neue Methoden zum Absichern Ihrer Umgebung zu finden.

Die Identitätssicherheitsbewertung basiert auf einer Reihe empfohlener Sicherheitskontrollen, die von Microsoft veröffentlicht werden und eine numerische Bewertung bereitstellen, um Ihren Sicherheitsstatus objektiv zu messen und zukünftige Sicherheitsverbesserungen zu planen. Sie können Ihre Bewertung auch mit Bewertungen in anderen Branchen und mit Ihren eigenen Trends im Zeitverlauf vergleichen.

**Bewährte Methode**: Planen Sie routinemäßige Sicherheitsüberprüfungen und -verbesserungen basierend auf bewährten Methoden in ihrer Branche.
**Detail**: Verwenden Sie das Feature Identity Secure Score, um die Verbesserungen im Lauf der Zeit zu bewerten.

## <a name="enable-password-management"></a>Aktivieren der Kennwortverwaltung

Wenn Sie mehrere Mandanten verwenden oder Benutzern das [Zurücksetzen ihres Kennworts](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md) ermöglichen möchten, ist es wichtig, dass Sie zur Verhinderung von Missbrauch geeignete Sicherheitsrichtlinien verwenden.

**Bewährte Methode**: Richten Sie Self-Service-Kennwortzurücksetzung (SSPR, Self-Service Password Reset) für Ihre Benutzer ein.  
**Detail**: Verwenden Sie das Feature [Self-Service-Kennwortzurücksetzung](../../active-directory-b2c/user-flow-self-service-password-reset.md) in Azure AD.

**Bewährte Methode**: Überwachen Sie, wie oder ob SSPR tatsächlich verwendet wird.  
**Detail**: Überwachen Sie die Benutzer, die sich registrieren, mit dem [Bericht zur Registrierung für die Kennwortzurücksetzung](../../active-directory/authentication/howto-sspr-reporting.md) von Azure AD. Über die Berichtsfunktion, die von Azure AD bereitgestellt wird, können Sie Fragen beantworten, indem Sie vordefinierte Berichte verwenden. Wenn Sie eine ordnungsgemäße Lizenz haben, können Sie auch benutzerdefinierte Abfragen erstellen.

**Bewährte Methode**: Weiten Sie cloudbasierte Kennwortrichtlinien auf Ihre lokale Infrastruktur aus.
**Detail**: Weiten Sie Kennwortrichtlinien in Ihrer Organisation aus, indem Sie für Änderungen lokaler Kennwörter dieselben Prüfungen ausführen wie für Änderungen cloudbasierter Kennwörter. Installieren Sie [Azure AD-Kennwortschutz](../../active-directory/authentication/concept-password-ban-bad.md) für Windows Server Active Directory-Agents lokal, um die Liste gesperrter Kennwörter auf Ihre vorhandene Infrastruktur auszudehnen. Benutzer und Administratoren, die Kennwörter lokal ändern, festlegen oder zurücksetzen, müssen die gleiche Kennwortrichtlinie einhalten wie ausschließliche Cloudbenutzer.

## <a name="enforce-multi-factor-verification-for-users"></a>Erzwingen der mehrstufigen Überprüfung für Benutzer

Sie sollten die zweistufige Überprüfung für alle Benutzer fordern. Dies schließt Administratoren und andere Personen in Ihrer Organisation ein, bei denen die Gefährdung ihrer Konten einen besonders schädlichen Einfluss haben kann (z.B. Finanzchefs).

Es gibt mehrere Optionen, um eine zweistufige Überprüfung zu erzwingen. Die beste Option für Sie hängt von Ihren Zielen ab, der Azure AD-Edition, die Sie ausführen, und Ihrem Lizenzierungsprogramm. Bestimmen Sie mithilfe des Artikels [Vorgehensweise zum Erzwingen einer zweistufigen Überprüfung für einen Benutzer](../../active-directory/authentication/howto-mfa-userstates.md) die beste Option für Sie. Weitere Informationen zu Lizenzen und Preisen finden Sie auf den Preisgestaltungsseiten von [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) und [Azure AD Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Im Folgenden werden Optionen und Vorteile der zweistufigen Überprüfung beschrieben:

**Option 1**: Aktivieren Sie MFA für alle Benutzer und Anmeldemethoden mit Azure AD Sicherheitsstandards. **Vorteil**: Mit dieser Option können Sie MFA für alle Benutzer in Ihrer Umgebung problemlos und schnell erzwingen und einer strenge Richtlinie für folgende Aktionen anwenden:

* Abfragen von Administratorkonten und administrativen Anmeldemechanismen
* Erzwingen von MFA über Microsoft Authenticator für alle Benutzer
* Einschränken älterer Authentifizierungsprotokolle.

Diese Methode ist für alle Lizenzierungsstufen verfügbar, kann jedoch nicht mit vorhandenen Richtlinien für den bedingten Zugriff kombiniert werden. Weitere Informationen finden Sie unter [Azure AD-Sicherheitsstandardwerte](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

**Option 2**: [Aktivieren Sie Azure Multi-Factor Authentication durch Ändern des Benutzerstatus](../../active-directory/authentication/howto-mfa-userstates.md).   
**Vorteil**: Dies ist die herkömmliche Methode, die zweistufige Überprüfung zu anzufordern. Sie funktioniert sowohl mit der [mehrstufigen Azure AD-Authentifizierung in der Cloud als auch mit Azure Multi-Factor Authentication Server](../../active-directory/authentication/concept-mfa-howitworks.md). Bei dieser Methode müssen sich Benutzer jedes Mal, wenn sie sich anmelden, die zweistufige Überprüfung durchführen. Die Richtlinien für bedingten Zugriff werden außer Kraft gesetzt.

Um zu bestimmen, wann Multi-Factor Authentication aktiviert werden muss, lesen Sie den Artikel [Welche Version von Azure AD MFA ist für meine Organisation die richtige?](../../active-directory/authentication/concept-mfa-howitworks.md).

**Option 3**: [Aktivieren Sie Multi-Factor Authentication mithilfe einer Richtlinie für bedingten Zugriff](../../active-directory/authentication/howto-mfa-getstarted.md).
**Vorteil**: Mit dieser Option können Sie die zweistufige Überprüfung unter bestimmten Bedingungen mithilfe des [bedingten Zugriffs](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) anfordern. Bestimmte Bedingungen können sein: Benutzeranmeldung von verschiedenen Standorten, nicht vertrauenswürdige Geräte oder Anwendungen, die Sie als risikoreich betrachten. Durch Definieren von bestimmten Bedingungen, in denen Sie die zweistufige Überprüfung erfordern, können Sie die konstante Aufforderung Ihrer Benutzer vermeiden, die eine unangenehme Benutzererfahrung sein kann.

Dies ist die flexibelste Möglichkeit, die zweistufige Überprüfung für Ihre Benutzer zu aktivieren. Die Aktivierung einer Richtlinie für bedingten Zugriff funktioniert jedoch nur bei Azure AD Multi-Factor Authentication in der Cloud und ist eine Premium-Funktion von Azure AD. Weitere Informationen zu dieser Methode finden Sie unter [Bereitstellen von cloudbasierter Azure AD Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md).

**Option 4**: Aktivieren Sie die Multi-Factor Authentication mit Richtlinien für den bedingten Zugriff durch Auswertung von [risikobasierten Richtlinien für den bedingten Zugriff](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Vorteil**: Diese Option ermöglicht Ihnen Folgendes:

* Ermitteln potenzieller Sicherheitsrisiken für Identitäten Ihrer Organisation.
* Konfigurieren automatischer Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit den Identitäten Ihrer Organisation.
* Untersuchen verdächtiger Vorfälle und Ergreifen entsprechender Maßnahmen zu deren Behebung.

Diese Methode nutzt die Risikobewertung von Azure AD Identity Protection, um die Notwendigkeit der zweistufigen Überprüfung auf Basis des Benutzers und Anmelderisikos für alle Cloudanwendungen zu bestimmen. Diese Methode erfordert die Azure Active Directory P2-Lizenzierung. Weitere Informationen zu dieser Methode finden Sie unter [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md).

> [!Note]
> Option 2, Aktivierung von Multi-Factor Authentication durch Ändern des Benutzerstatus, überschreibt die Richtlinien für bedingten Zugriff. Da die Optionen 3 und 4 Richtlinien für bedingten Zugriff verwenden, können Sie Option 2 nicht zusammen mit ihnen verwenden.

Organisationen, die keine zusätzliche Schicht zur Identitätssicherung hinzufügen, etwa die zweistufige Überprüfung, sind anfälliger für Angriffe mit dem Ziel des Diebstahls von Anmeldeinformationen. Ein Angriff mit gestohlenen Anmeldeinformationen kann zum Kompromittieren der Daten führen.

## <a name="use-role-based-access-control"></a>Verwenden der rollenbasierten Zugriffssteuerung

Die Zugriffsverwaltung für Cloudressourcen ist wichtig für jede Organisation, die die Cloud nutzt. Mit der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/overview.md) können Sie verwalten, welche Benutzer Zugriff auf Azure-Ressourcen haben, welche Aktionen die Benutzer für diese Ressourcen ausführen können und auf welche Bereiche die Benutzer zugreifen können.

Durch Festlegen von Gruppen oder individuellen Rollen, die für bestimmte Funktionen zuständig sind, kann Verwirrung ausgeschlossen werden, die zu menschlichen Fehlern und Automatisierungsfehlern und letztendlich zu Sicherheitsrisiken führen kann. Das Einschränken des Zugriffs auf der Grundlage der Sicherheitsprinzipien [Need to know](https://en.wikipedia.org/wiki/Need_to_know) und [Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) ist für Organisationen unerlässlich, die Sicherheitsrichtlinien für den Datenzugriff erzwingen möchten.

Ihrem Sicherheitsteam benötigt Einblicke in Ihre Azure-Ressourcen, um Risiken zu bewerten und auszuräumen. Wenn die Mitarbeiter des Sicherheitsteams operative Pflichten haben, benötigen sie für ihre Arbeit zusätzliche Berechtigungen.

Sie können [Azure RBAC](../../role-based-access-control/overview.md) verwenden, um Benutzern, Gruppen und Anwendungen Berechtigungen für einen bestimmten Bereich zu erteilen. Der Bereich einer Rollenzuweisung kann ein Abonnement, eine Ressourcengruppe oder eine einzelne Ressource sein.

**Bewährte Methode**: Verteilen Sie Aufgaben in Ihrem Team, und gewähren Sie Benutzern nur den Zugriff, den sie zur Ausführung ihrer Aufgaben benötigen. Anstatt allen uneingeschränkte Berechtigungen in Ihrem Azure-Abonnement oder Ihren Ressourcen zu gewähren, beschränken Sie die Berechtigungen auf bestimmte Aktionen in einem bestimmten Bereich.
**Detail**: Verwenden Sie [in Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md), um Benutzern Berechtigungen zuzuweisen.

> [!Note]
> Bestimmte Berechtigungen bringen unnötige Komplexität und Verwirrung und letztendlich eine „veraltete“ Konfiguration mit sich, die nur schwer korrigiert werden, ohne größere Schäden anzurichten. Vermeiden Sie ressourcenspezifische Berechtigungen. Verwenden Sie statt dessen Verwaltungsgruppen für unternehmensweite Berechtigungen und Ressourcengruppen für Berechtigungen innerhalb von Abonnements. Vermeiden Sie benutzerspezifische Berechtigungen. Weisen Sie stattdessen Gruppen Zugriffsberechtigungen in Azure AD zu.

**Bewährte Methode**: Gewähren Sie Sicherheitsteams mit Azure-Aufgaben die Berechtigung, Azure-Ressourcen anzuzeigen, damit sie diese bewerten und Risiken beheben können.
**Detail**: Gewähren Sie Sicherheitsteams die Azure RBAC-Rolle [Sicherheitsleseberechtigter](../../role-based-access-control/built-in-roles.md#security-reader). Sie können je nach Umfang der Pflichten die Stammverwaltungsgruppe oder die Segmentverwaltungsgruppe verwenden:

* **Stammverwaltungsgruppe** für Teams, die für alle Unternehmensressourcen zuständig sind
* **Segmentverwaltungsgruppe** für Teams mit beschränkter Zuständigkeit (häufig aufgrund gesetzlicher oder sonstiger organisatorischer Beschränkungen)

**Bewährte Methode**: Gewähren Sie Sicherheitsteams mit direkten operativen Pflichten die entsprechenden Berechtigungen.
**Detail**: Überprüfen Sie die integrierten Azure-Rollen hinsichtlich der geeigneten Rollenzuweisung. Wenn die integrierten Rollen den Ansprüchen Ihrer Organisation nicht entsprechen, können Sie [benutzerdefinierte Azure-Rollen](../../role-based-access-control/custom-roles.md) erstellen. Genau wie integrierte Rollen können auch benutzerdefinierte Rollen Benutzern, Gruppen und Dienstprinzipalen auf Abonnement-, Ressourcengruppen- und Ressourcenebene zugewiesen werden.

**Bewährte Methoden:** Gewähren Sie den Sicherheitsrollen Azure Security Center-Zugriff, die diesen benötigen. Security Center ermöglicht Sicherheitsteams das schnelle Identifizieren und Beheben von Risiken.
**Detail**: Fügen Sie Sicherheitsteams mit diesem Bedarf der Azure RBAC-Rolle [Sicherheitsadministrator](../../role-based-access-control/built-in-roles.md#security-admin) hinzu, sodass sie Sicherheitsrichtlinien und -zustände anzeigen, Sicherheitsrichtlinien bearbeiten sowie Warnungen und Empfehlungen anzeigen und verwerfen können. Sie können hierzu je nach Umfang der Pflichten die Stammverwaltungsgruppe oder die Segmentverwaltungsgruppe verwenden.

Organisationen, die keine Datenzugriffssteuerung mithilfe von Funktionen wie Azure RBAC erzwingen, erteilen Ihren Benutzern möglicherweise mehr Berechtigungen als erforderlich. Dies kann zur Gefährdung von Daten führen, indem Benutzern erlaubt wird, auf bestimmte Arten von Daten zuzugreifen (z.B. Daten mit hoher geschäftlicher Bedeutung), auf die sie eigentlich keinen Zugriff haben dürften.

## <a name="lower-exposure-of-privileged-accounts"></a>Senken der Gefährdung privilegierter Konten

Das Sichern des privilegierten Zugriffs ist ein entscheidender erster Schritt, um die geschäftlichen Ressourcen zu schützen. Halten Sie die Anzahl von Personen mit Zugriff auf sichere Informationen oder Ressourcen möglichst gering, da sich so das Risiko verringert, dass ein böswilliger Benutzer Zugriff darauf erhält, oder ein autorisierter Benutzer versehentlich eine sensible Ressource kompromittiert.

Mit privilegierten Konten werden IT-Systeme verwaltet. Cyberkriminelle versuchen diese Konten anzugreifen, um Zugriff auf die Daten und Systeme eines Unternehmens zu erhalten. Zum Schutz des privilegierten Zugriffs empfiehlt es sich, Konten und Systeme zu isolieren, um sie vor dem Zugriff durch böswillige Benutzer zu schützen.

Wir empfehlen Ihnen, eine Roadmap zum Schützen des privilegierten Zugriffs gegenüber Cyberangreifern zu entwickeln und zu befolgen. Informationen zum Erstellen eines detaillierten Plans zum Schützen von Identitäten und Zugriff, die in Azure AD, Microsoft Azure, Microsoft 365 und anderen Clouddiensten verwaltet oder berichtet werden, finden Sie unter [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../../active-directory/roles/security-planning.md).

Im Folgenden werden die Best Practices in [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../../active-directory/roles/security-planning.md) zusammengefasst:

**Bewährte Methode**: Verwalten, steuern und überwachen Sie den Zugriff auf privilegierte Konten.   
**Detail**: Aktivieren Sie [Azure AD Privileged Identity Management](../../active-directory/roles/security-planning.md). Nach der Aktivierung von Privileged Identity Management erhalten Sie Benachrichtigungs-E-Mails zu Rollenänderungen bei privilegiertem Zugriff. Diese Benachrichtigungen sind eine frühzeitige Warnung, wenn zusätzliche Benutzer hoch privilegierten Rollen in Ihrem Verzeichnis hinzugefügt werden.

**Bewährte Methode**: Stellen Sie sicher, dass alle wichtigen Administratorkonten verwaltete Azure AD-Konten sind.
**Detail**: Entfernen Sie alle Consumer-Konten aus wichtigen Administratorrollen (z.B. Microsoft-Konten wie „hotmail.com“, „live.com“ und „outlook.com“).

**Bewährte Methode**: Stellen Sie sicher, dass alle wichtigen Administratorrollen ein separates Konto für administrative Aufgaben haben, um Phishing und andere Angriffe zu verhindern, bei denen Administratorrechte ausgenutzt werden.
**Detail**: Erstellen Sie ein separates Administratorkonto an, dem die erforderlichen Berechtigungen für administrative Aufgaben zugewiesen sind. Blockieren Sie die Verwendung dieser Administratorkonten für gängige Produktivitätstools wie Microsoft 365-E-Mail und willkürliches Browsen im Web.

**Bewährte Methode**: Identifizieren und kategorisieren Sie Konten in stark privilegierten Rollen.   
**Detail**: Zeigen Sie nach dem Aktivieren von Azure AD Privileged Identity Management die Benutzer an, die die Rollen globaler Administrator, privilegierter Rollenadministrator und andere hoch privilegierte Rollen einnehmen. Entfernen Sie alle Konten, die nicht mehr in diesen Rollen benötigt werden, und kategorisieren Sie die restlichen Konten, die Administratorrollen zugewiesen sind:

* Einzeln Benutzern mit Administratorrechten zugewiesen, und kann nicht zu administrativen Zwecken (z.B. persönliche E-Mail) verwendet werden
* Einzeln Benutzern mit Administratorrechten zugewiesen und nur für Verwaltungszwecke vorgesehen
* Für mehrere Benutzer freigegeben
* Für Notfallzugriffs-Szenarios
* Für automatisierte Skripts
* Für externe Benutzer

**Bewährte Methode**: Implementieren Sie Just-in-Time-Zugriff (JIT) zur weiteren Reduzierung der Offenlegungszeit von Berechtigungen und Steigern Ihres Einblicks in die Verwendung privilegierter Konten.   
**Detail**: Azure AD Privileged Identity Management ermöglicht Ihnen Folgendes:

* Beschränken der Nutzung von Benutzerberechtigungen auf JIT.
* Zuweisen von Rollen für eine verkürzte Dauer mit der Gewissheit, dass die Berechtigungen automatisch widerrufen werden.

**Bewährte Methode**: Definieren Sie mindestens zwei Notfallzugriffskonten.   
**Detail**: Notfallzugriffskonten unterstützen Organisationen dabei, den privilegierten Zugriff innerhalb einer vorhandenen Azure Active Directory-Umgebung einzuschränken. Diese Konten verfügen über weitreichende Privilegien und werden keinen Einzelpersonen zugewiesen. Konten für den Notfallzugriff sind auf Szenarios beschränkt, in denen normale Administratorkonten nicht verwendet werden können. Organisationen müssen die Notfallkontennutzung auf die erforderliche Zeitspanne begrenzen.

Bewerten Sie die Konten, die zugewiesen werden oder für die Rolle „globaler Administrator“ berechtigt sind. Wenn Ihnen keine ausschließlichen Cloudkonten mit der `*.onmicrosoft.com`-Domäne (vorgesehen für den Notfallzugriff) angezeigt werden, erstellen Sie sie. Weitere Informationen finden Sie unter [Verwalten von Administratorkonten für den Notfallzugriff in Azure AD](../../active-directory/roles/security-emergency-access.md).

**Bewährte Methode**: Richten Sie einen Notfallprozess für Notfallsituationen ein.
**Detail**: Führen Sie die Schritte in [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](../../active-directory/roles/security-planning.md) aus.

**Bewährte Methode**: Fordern Sie, dass alle wichtigen Administratorkonten kennwortlos sind (bevorzugt), oder fordern Sie die Verwendung von Multi-Factor Authentication.
**Detail**: Melden Sie sich mit der [Microsoft Authenticator-App](../../active-directory/authentication/howto-authentication-passwordless-phone.md) bei jedem beliebigen Azure AD-Konto an, ohne ein Kennwort zu verwenden. Wie die Technologie von [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) nutzt Microsoft Authenticator die schlüsselbasierte Authentifizierung, um die Verwendung von Benutzeranmeldeinformationen zu ermöglichen, die an ein Gerät gebunden sind und auf biometrischer Authentifizierung oder einer PIN beruhen.

Schreiben Sie bei der Anmeldung die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) von Azure AD für alle Benutzer vor, die dauerhaft einzelnen oder mehreren der Azure AD-Administratorrollen zugewiesen sind: globaler Administrator, privilegierter Rollenadministrator, Exchange Online-Administrator und SharePoint Online-Administrator. Aktivieren Sie [Multi-Factor Authentication für Ihre Administratorkonten](../../active-directory/authentication/howto-mfa-userstates.md), und stellen Sie sicher, dass alle Benutzer registriert sind.

**Bewährte Methode**: Richten Sie für wichtige Administratorkonten eine Administrator-Arbeitsstation ein, auf der keine Produktionsaufgaben (wie Webbrowsen und E-Mail) ausgeführt werden dürfen. Dies schützt Ihre Administratorkonten vor Angriffsvektoren, die Webbrowsen und E-Mail ausnutzen, und Sie mindern das Risiken schwerwiegender Vorfälle erheblich.
**Detail**: Verwenden Sie eine Administrator-Arbeitsstation. Wählen Sie ein Sicherheitsniveau für die Arbeitsstation aus:

- Hochsichere Produktivitätsgeräte bieten umfassende Sicherheit für Webbrowsen und anderen Produktivitätsaufgaben.
- [Privileged Access Workstations (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) bieten für sensible Aufgaben ein dediziertes Betriebssystem, das vor Internetangriffen und Bedrohungsvektoren geschützt ist.

**Bewährte Methode**: Heben Sie die Bereitstellung von Administratorkonten auf, wenn Mitarbeiter Ihre Organisation verlassen.
**Detail**: Pflegen Sie einen Prozess, mit dem Administratorkonten deaktiviert oder gelöscht werden, wenn Mitarbeiter Ihre Organisation verlassen.

**Bewährte Methode**: Testen Sie Administratorkonten regelmäßig mit aktuellen Angriffstechniken.
**Detail**: Verwenden Sie den Angriffssimulator von Microsoft 365 oder ein Angebot eines Drittanbieters, um realistische Angriffsszenarien in Ihrer Organisation durchzuspielen. Damit können Sie gefährdete Benutzer identifizieren, bevor ein tatsächlicher Angriff stattfindet.

**Bewährte Methode**: Führen Sie Maßnahmen zum Abwehren der am häufigsten verwendeten Angriffstechniken durch.  
**Detail**: [Identifizieren Sie Microsoft-Konten in Administratorrollen, die auf Geschäfts-, Schul- oder Unikonten umgestellt werden müssen.](../../active-directory/roles/security-planning.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Stellen Sie separate Benutzerkonten und E-Mail-Weiterleitung für globale Administratorkonten sicher.](../../active-directory/roles/security-planning.md)  

[Stellen Sie sicher, dass die Kennwörter von Administratorkonten kürzlich geändert wurden.](../../active-directory/roles/security-planning.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Aktivieren Sie die Kennworthashsynchronisierung.](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)  

[Fordern Sie die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) sowohl für Benutzer in allen privilegierten Rollen als auch für Benutzer mit hoher Gefährdung.](../../active-directory/roles/security-planning.md#require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users)  

[Abrufen Ihres Microsoft 365 Secure Score (bei Verwendung von Microsoft 365)](../../active-directory/roles/security-planning.md#obtain-your-microsoft-365-secure-score-if-using-microsoft-365)  

[Überprüfen des Microsoft 365-Sicherheitsleitfadens (bei Verwendung von Microsoft 365)](../../active-directory/roles/security-planning.md#review-the-microsoft-365-security-and-compliance-guidance-if-using-microsoft-365)  

[Konfigurieren der Microsoft 365-Aktivitätsüberwachung (bei Verwendung von Microsoft 365)](../../active-directory/roles/security-planning.md#configure-microsoft-365-activity-monitoring-if-using-microsoft-365)  

[Richten Sie Notfallreaktionsplan-Besitzer ein.](../../active-directory/roles/security-planning.md#establish-incidentemergency-response-plan-owners)  

[Schützen Sie lokale privilegierte Administratorkonten.](../../active-directory/roles/security-planning.md#turn-on-password-hash-synchronization)

Wenn Sie privilegierten Zugriff nicht schützen, verfügen Sie möglicherweise über zu viele Benutzer in stark privilegierten Rollen und sind anfälliger für Angriffe. Böswillige Akteure einschließlich Cyberangreifern haben häufig Administratorkonten und andere Elemente des privilegierten Zugriffs im Visier, um mit Angriffen, deren Ziel der Diebstahl von Anmeldeinformationen ist, Zugriff auf sensible Daten und Systeme zu erhalten.

## <a name="control-locations-where-resources-are-created"></a>Steuern von Standorten, wo Ressourcen erstellt werden

Es ist sehr wichtig, für Cloudbediener die Durchführung von Aufgaben zu ermöglichen und gleichzeitig die Nichteinhaltung von Konventionen zu verhindern, die zum Verwalten der Ressourcen einer Organisation erforderlich sind. Organisationen, die die Orte steuern möchten, an denen Ressourcen erstellt werden, sollten diese Orte hartcodieren.

Sie können mit dem [Azure Resource Manager](../../azure-resource-manager/management/overview.md) Sicherheitsrichtlinien erstellen, mit deren Definitionen die jeweils verweigerten Aktionen oder Ressourcen beschrieben werden. Sie weisen diese Richtliniendefinitionen dem gewünschten Ziel zu, z.B. einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource.

> [!NOTE]
> Sicherheitsrichtlinien sind nicht das gleiche wie Azure RBAC. Tatsächlich nutzen sie Azure RBAC, um Benutzer zum Erstellen dieser Ressourcen zu autorisieren.
>
>

Organisationen, die die Erstellung von Ressourcen nicht steuern, sind anfälliger dafür, dass Benutzer den Dienst durch die Erstellung von mehr Ressourcen als benötigt missbrauchen. Das Absichern der Ressourcenerstellung ist ein wichtiger Schritt zum Schützen eines Szenarios mit mehreren Mandanten.

## <a name="actively-monitor-for-suspicious-activities"></a>Aktives Überwachen auf verdächtige Aktivitäten

Ein aktives Identitätsüberwachungssystem kann schnell verdächtiges Verhalten erkennen und eine Warnung zur weiteren Untersuchung auslösen. Die folgende Tabelle enthält zwei Azure AD-Funktionen, mit denen Organisationen ihre Identitäten überwachen können:

**Bewährte Methode**: Verwenden Sie eine Methode, um Folgendes zu identifizieren:

- Versuch einer Anmeldung [ohne Ablaufverfolgung](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- [Brute-Force](../../active-directory/reports-monitoring/howto-find-activity-reports.md)-Angriffe gegen ein bestimmtes Konto.
- Versuch der Anmeldung von mehreren Standorten aus.
- Anmeldungen von [infizierten Geräten](../../active-directory/reports-monitoring/howto-find-activity-reports.md).
- Verdächtige IP-Adressen.

**Detail**: Verwenden Sie [Anomalieberichte](../../active-directory/reports-monitoring/overview-reports.md) von Azure AD Premium. Sie müssen über Prozesse und Verfahren verfügen, mit denen IT-Administratoren diese Berichte täglich oder bei Bedarf ausführen können (normalerweise in einem Szenario mit Reaktionen auf Zwischenfälle).

**Bewährte Methode**: Sie haben ein aktives Überwachungssystem, das Sie über Risiken informiert, und können die Risikostufe (hoch, mittel oder niedrig) an Ihre geschäftlichen Anforderungen anpassen.   
**Detail**: Verwenden Sie [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), das die aktuellen Risiken im eigenen Dashboard markiert und tägliche Zusammenfassungen per E-Mail sendet. Zum Schutz der Identitäten Ihrer Organisation können Sie risikobasierte Richtlinien konfigurieren, die automatisch auf erkannte Probleme reagieren, wenn eine angegebene Risikostufe erreicht ist.

Für Organisationen, die ihre Identitätssysteme nicht aktiv überwachen, besteht das Risiko, dass Anmeldeinformationen kompromittiert werden. Ohne das Wissen, dass diese Anmeldeinformationen für verdächtige Aktivitäten genutzt werden, können Organisationen dieser Art von Bedrohung nicht begegnen.

## <a name="use-azure-ad-for-storage-authentication"></a>Verwenden von Azure AD für Speicherauthentifizierung
[Azure Storage](../../storage/common/storage-auth-aad.md) unterstützt Authentifizierung und Autorisierung mit Azure AD für Blob-Speicher und Warteschlangenspeicher. Mit der Azure AD-Authentifizierung können Sie die rollenbasierte Zugriffssteuerung von Azure nutzen, um Benutzern, Gruppen und Anwendungen Berechtigungen bis hin zum Bereich eines einzelnen Blob-Containers oder einer einzelnen Warteschlange zu gewähren.

Wir empfehlen, [Azure AD für die Authentifizierung des Speicherzugriffs](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/) zu verwenden.

## <a name="next-step"></a>Nächster Schritt

Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).