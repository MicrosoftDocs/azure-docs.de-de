---
title: 'Installationsübersicht: Azure AD Connect und Azure AD Connect Health | Microsoft-Dokumentation'
description: Dieses Dokument bietet eine Übersicht über die Installationsoptionen und -pfade, die für Azure AD Connect und Connect Health verfügbar sind.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae575aa6544a174a70eb8ea4749566e8660280e2
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873266"
---
# <a name="azure-ad-connect-and-azure-ad-connect-health-installation-roadmap"></a>Installationsübersicht: Azure AD Connect und Azure AD Connect Health

## <a name="install-azure-ad-connect"></a>Installieren von Azure AD Connect

> [!IMPORTANT]
> Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Aktionen nicht. Eine dieser Aktionen kann zu einem inkonsistenten oder nicht unterstützten Status der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft auch keinen technischen Support für solche Bereitstellungen leisten.

Den Download für Azure AD Connect finden Sie im [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).

| Lösung | Szenario |
| --- | --- |
| Vorbereitung: [Hardware und Voraussetzungen](how-to-connect-install-prerequisites.md) |<li>Hier erfahren Sie mehr zu den Schritten, die vor dem Installieren von Azure AD Connect ausgeführt werden müssen.</li> |
| [Express-Einstellungen](how-to-connect-install-express.md) |<li>Wenn Sie über eine einzelne Gesamtstruktur-AD-Instanz verfügen, ist dies die empfohlene Option.</li> <li>Benutzer melden Sie sich mit dem gleichen Kennwort mithilfe der Kennwortsynchronisierung an.</li> |
| [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md) |<li>Wird bei mehreren Gesamtstrukturen verwendet. Unterstützt viele lokale [Topologien](plan-connect-topologies.md).</li> <li>Passen Sie die Anmeldeoption an, z.B. Pass-Through-Authentifizierung, ADFS für den Verbund, oder verwenden Sie einen Identitätsanbieter eines Drittanbieters.</li> <li>Passen Sie Synchronisierungsfunktionen an, wie das Filtern und Rückschreiben.</li> |
| [Upgrade von DirSync](how-to-dirsync-upgrade-get-started.md) |<li>Wird bei einem vorhandenen DirSync-Server verwendet, der bereits ausgeführt wird.</li> |
| [Upgrade von Azure AD Sync oder Azure AD Connect](how-to-upgrade-previous-version.md) |<li>Je nach Bedarf gibt es verschiedene Methoden.</li> |

[Nach der Installation](how-to-connect-post-installation.md) sollten Sie überprüfen, ob es wie erwartet funktioniert und den Benutzern Lizenzen zuweisen.

### <a name="next-steps-to-install-azure-ad-connect"></a>Nächste Schritte für die Installation von Azure AD Connect
|Thema |Link|  
| --- | --- |
|Azure AD Connect herunterladen | [Azure AD Connect herunterladen](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Installieren mit den Express-Einstellungen | [Expressinstallation von Azure AD Connect](./how-to-connect-install-express.md)|
|Installieren mit benutzerdefinierten Einstellungen | [Benutzerdefinierte Installation von Azure AD Connect](./how-to-connect-install-custom.md)|
|Upgrade von DirSync | [Upgrade von Azure AD-Synchronisierungstools (DirSync)](./how-to-dirsync-upgrade-get-started.md)|
|Nach der Installation | [Überprüfen der Installation und Zuweisen von Lizenzen](how-to-connect-post-installation.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Weitere Informationen über die Installation von Azure AD Connect
Sie sollten sich auch auf [betriebliche](./how-to-connect-sync-staging-server.md) Probleme vorbereiten. Verwenden Sie ggf. einen Standbyserver, um im [Notfall](how-to-connect-sync-staging-server.md#disaster-recovery) problemlos ein Failover ausführen zu können. Wenn Sie häufig Konfigurationsänderungen vornehmen möchten, sollten Sie einen [Stagingmodus](how-to-connect-sync-staging-server.md) -Server einplanen.

|Thema |Link|  
| --- | --- |
|Unterstützte Topologien | [Topologien für Azure AD Connect](plan-connect-topologies.md)|
|Entwurfskonzepte | [Entwurfskonzepte für Azure AD Connect](plan-connect-design-concepts.md)|
|Für die Installation verwendete Konten | [Weitere Informationen zu den Anmeldeinformationen und Berechtigungen von Azure AD Connect](reference-connect-accounts-permissions.md)|
|Operative Planung | [Azure AD Connect-Synchronisierung: Operative Aufgaben und Überlegungen](./how-to-connect-sync-staging-server.md)|
|Optionen für die Benutzeranmeldung | [Azure AD Connect-Optionen für die Benutzeranmeldung](plan-connect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurieren der Synchronisierungsfunktionen
Azure AD Connect verfügt über mehrere Funktionen, die Sie optional aktivieren können oder die standardmäßig aktiviert sind. Einige Funktionen benötigen möglicherweise eine zusätzliche Konfiguration in bestimmten Szenarien und Topologien.

[Filtern](how-to-connect-sync-configure-filtering.md) wird verwendet, wenn Sie begrenzen möchten, welche Objekte mit Azure AD synchronisiert werden. Standardmäßig werden alle Benutzer, Kontakte, Gruppen und Windows 10-Computer synchronisiert. Sie können die Filterung ausgehend von Domänen, Organisationseinheiten oder Attributen ändern.

[Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) synchronisiert das Kennworthash in Active Directory mit Azure AD. So kann der Endbenutzer das gleiche Kennwort lokal und in der Cloud verwenden, es jedoch nur an einem Ort verwalten. Da es das lokale Active Directory als Autorität verwendet, können Sie auch Ihre eigene Kennwortrichtlinie verwenden.

[Kennwortrückschreiben](../authentication/tutorial-enable-sspr.md) ermöglicht es den Benutzern, ihre Kennwörter in der Cloud zu ändern und zurückzusetzen und die lokale Kennwortrichtlinie anzuwenden.

[Geräterückschreiben](how-to-connect-device-writeback.md) ermöglicht es Ihnen, ein in Azure AD registriertes Gerät wieder in das lokale Active Directory zurückzuschreiben, damit es für bedingten Zugriff verwendet werden kann.

Die Funktion zum [Verhindern eines versehentlichen Löschvorgangs](how-to-connect-sync-feature-prevent-accidental-deletes.md) ist standardmäßig aktiviert und schützt Ihr Cloudverzeichnis vor mehreren gleichzeitigen Löschvorgängen. Standardmäßig sind 500 Löschvorgänge pro Durchlauf zulässig. Diese Einstellung kann abhängig von der Größe Ihres Unternehmens geändert werden.

[automatische Upgrade](how-to-connect-install-automatic-upgrade.md) ist für Installationen mit Expresseinstellungen standardmäßig aktiviert und stellt sicher, dass Ihre Azure AD Connect-Instanz immer mit der neuesten Version aktualisiert wird.

### <a name="next-steps-to-configure-sync-features"></a>Nächste Schritte zum Konfigurieren der Synchronisierungsfunktionen
|Thema |Link|  
| --- | --- |
|Konfigurieren der Filterung | [Azure AD Connect-Synchronisierung: Konfigurieren der Filterung](how-to-connect-sync-configure-filtering.md)|
|Kennworthashsynchronisierung | [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md)|
|Passthrough-Authentifizierung | [Passthrough-Authentifizierung](how-to-connect-pta.md)
|Kennwortrückschreiben | [Erste Schritte mit der Kennwortverwaltung](../authentication/tutorial-enable-sspr.md)|
|Geräterückschreiben | [Aktivieren des Geräterückschreibens in Azure AD Connect](how-to-connect-device-writeback.md)|
|Verhindern eines versehentlichen Löschvorgangs | [Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen](how-to-connect-sync-feature-prevent-accidental-deletes.md)|
|automatische Upgrade | [Azure AD Connect: Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Anpassen der Azure AD Connect-Synchronisierung
Für die Azure AD Connect-Synchronisierung ist eine Standardkonfiguration vorgegeben, die für die meisten Kunden und Topologien konzipiert wurde. Es gibt jedoch immer Situationen, in denen die Standardkonfiguration nicht funktioniert und angepasst werden muss. Sie können Änderungen wie in diesem Abschnitt und in den verknüpften Themen beschrieben vornehmen.

Wenn Sie noch nicht mit einer Synchronisierungstopologie gearbeitet haben, sollten Sie sich zunächst mit den Grundlagen und verwendeten Begriffen vertraut machen, die unter [Technische Konzepte](how-to-connect-sync-technical-concepts.md)beschrieben werden. Azure AD Connect ist die Weiterentwicklung von MIIS2003, ILM2007 und FIM2010. Selbst wenn einige Dinge identisch sind, hat sich doch auch eine Menge verändert.

Bei der [Standardkonfiguration](concept-azure-ad-connect-sync-default-configuration.md) wird davon ausgegangen, dass die Konfiguration möglicherweise mehrere Gesamtstrukturen umfasst. In diesen Topologien kann ein Benutzerobjekt möglicherweise in einer anderen Gesamtstruktur als Kontakt dargestellt werden. Der Benutzer kann in einer anderen Ressourcengesamtstruktur auch über ein verknüpftes Postfach verfügen. Das Verhalten der Standardkonfiguration wird unter [Benutzer und Kontakte](concept-azure-ad-connect-sync-user-and-contacts.md)beschrieben.

Das synchronisierte Konfigurationsmodell wird als [deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)bezeichnet. Die erweiterten Attributflüsse verwenden [Funktionen](reference-connect-sync-functions-reference.md) , um Attributtransformationen auszudrücken. Sie können die gesamte Konfiguration mithilfe von Tools, die im Lieferumfang von Azure AD Connect enthalten sind, anzeigen und überprüfen. Wenn Sie Änderungen an der Konfiguration vornehmen müssen, sollten Sie sicherstellen, dass Sie die [bewährten Methoden](how-to-connect-sync-best-practices-changing-default-configuration.md) befolgen, damit neue Versionen leichter übernommen werden.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Nächste Schritte zur Anpassung der Azure AD Connect-Synchronisierung
|Thema |Link|  
| --- | --- |
|Alle Artikel zur Azure AD Connect-Synchronisierung | [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)|
|Technische Konzepte | [Azure AD Connect-Synchronisierung: Technische Konzepte](how-to-connect-sync-technical-concepts.md)|
|Grundlegendes zur Standardkonfiguration | [Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration](concept-azure-ad-connect-sync-default-configuration.md)|
|Grundlegendes zu Benutzern und Kontakten | [Azure AD Connect-Synchronisierung: Grundlegendes zu Benutzern und Kontakten](concept-azure-ad-connect-sync-user-and-contacts.md)|
|deklarative Bereitstellung | [Azure AD Connect-Synchronisierung: Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)|
|Ändern der Standardkonfiguration | [Bewährte Methoden zum Ändern der Standardkonfiguration](how-to-connect-sync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurieren von Verbundfunktionen

Azure AD Connect bietet mehrere Features, die die Verbunderstellung mit Azure AD unter Verwendung von AD FS sowie die Verwaltung Ihrer Verbundvertrauensstellung vereinfachen. Azure AD Connect unterstützt AD FS ab Windows Server 2012 R2.

[Aktualisieren Sie das TLS/SSL-Zertifikat der AD FS-Farm](how-to-connect-fed-ssl-update.md), auch wenn Sie Azure AD Connect nicht für die Verwaltung Ihrer Verbundvertrauensstellung verwenden.

[Fügen Sie Ihrer Farm einen AD FS-Server hinzu](how-to-connect-fed-management.md#addadfsserver), um die Farm nach Bedarf zu erweitern.

[Reparieren Sie die Vertrauensstellung](how-to-connect-fed-management.md#repairthetrust) mit Azure AD mit einigen wenigen Klicks.

AD FS kann für die Unterstützung von [mehreren Domänen](how-to-connect-install-multiple-domains.md)konfiguriert werden. Beispiel: Sie besitzen mehrere Domänen der obersten Ebene, die Sie für den Verbund verwenden müssen.

Wenn Ihr AD FS-Server nicht für die automatische Aktualisierung von Zertifikaten in Azure AD konfiguriert wurde oder wenn Sie eine Lösung ohne AD FS nutzen, werden Sie benachrichtigt, wenn Sie [Zertifikate aktualisieren](how-to-connect-fed-o365-certs.md) müssen.

### <a name="next-steps-to-configure-federation-features"></a>Nächste Schritte zum Konfigurieren der Verbundfunktionen
|Thema |Link|  
| --- | --- |
|Alle AD FS-Artikel | [Azure AD Connect und Verbund](how-to-connect-fed-whatis.md)|
|Konfigurieren von AD FS mit Unterdomänen | [Unterstützung mehrerer Domänen für den Verbund mit Azure AD](how-to-connect-install-multiple-domains.md)|
|Verwalten der AD FS-Farm | [Verwaltung und Anpassung der Active Directory-Verbunddienste mit Azure AD Connect](how-to-connect-fed-management.md)|
|Manuelles Aktualisieren von Verbundzertifikaten | [Erneuern von Verbundzertifikaten für Microsoft 365 und Azure AD](how-to-connect-fed-o365-certs.md)|


## <a name="get-started-with-azure-ad-connect-health"></a>Erste Schritte mit Azure AD Connect Health
Der Einstieg in Azure AD Connect Health ist einfach:

1. [Rufen Sie Azure AD Premium ab](../fundamentals/active-directory-get-started-premium.md), oder [beginnen Sie mit einer Testaktivierung](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Laden Sie Azure AD Connect Health-Agents herunter, und installieren Sie sie](#download-and-install-azure-ad-connect-health-agent) auf Ihren Identitätsservern.
3. Zeigen Sie das Azure AD Connect Health-Dashboard unter [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) an.

> [!NOTE]
> Denken Sie daran, dass Sie die Azure AD Connect Health-Agents auf Ihren Zielservern installieren müssen, um in Ihrem Azure AD Connect Health-Dashboard Daten anzeigen zu können.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Herunterladen und Installieren des Azure AD Connect Health-Agents
* Vergewissern Sie sich, dass Sie [die Anforderungen für Azure AD Connect Health erfüllen](how-to-connect-health-agent-install.md#requirements).
* Erste Schritte mit Azure AD Connect Health für AD FS
    * [Laden Sie den Azure AD Connect Health-Agent für AD FS herunter.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Zeigen Sie die Installationsanweisungen an](how-to-connect-health-agent-install.md#install-the-agent-for-ad-fs).
* Erste Schritte mit Azure AD Connect Health für die Sychronisierung
    * [Laden Sie die aktuelle Version von Azure AD Connect herunter, und installieren Sie sie](https://go.microsoft.com/fwlink/?linkid=615771). Der Health-Agent für die Synchronisierung wird im Rahmen der Installation von Azure AD Connect installiert (Version 1.0.9125.0 oder höher).
* Erste Schritte mit Azure AD Connect Health für AD DS
    * [Laden Sie den Azure AD Connect Health-Agent für AD DS herunter](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Zeigen Sie die Installationsanweisungen an](how-to-connect-health-agent-install.md#install-the-agent-for-azure-ad-ds).


## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health-Portal
Das Azure AD Connect Health-Portal zeigt Warnungen, Leistungsüberwachungsdaten und Nutzungsanalysen an. Über die URL https://aka.ms/aadconnecthealth gelangen Sie zum Hauptblatt von Azure AD Connect Health. Sie können es sich wie ein Fenster vorstellen. Auf dem Hauptblatt werden die Option **Schnellstart**, die Dienste von Azure AD Connect Health und weitere Konfigurationsoptionen angezeigt. Im folgenden Screenshot sehen Sie diese Elemente und darunter finden Sie ihre Beschreibung. Nachdem Sie die Agents bereitgestellt haben, wird der Integritätsdienst für die Dienste automatisch identifiziert, die von Azure AD Connect Health überwacht werden.

> [!NOTE]
> Informationen zur Lizenzierung finden Sie unter [Häufig gestellte Fragen zu Azure AD Connect Health](reference-connect-health-faq.md) sowie auf der [Seite zur Preisgestaltung von Azure AD](https://aka.ms/aadpricing).
    
![Azure AD Connect Health-Portal](./media/whatis-hybrid-identity-health/portalsidebar.png)

* **Schnellstart**: Wenn Sie diese Option auswählen, wird das Blatt **Schnellstart** geöffnet. Sie können Sie den Azure AD Connect Health-Agent herunterladen, indem Sie **Tools abrufen** auswählen. Sie können auch auf die Dokumentation zugreifen und Feedback geben.
* **Azure Active Directory Connect (Sync)** : Diese Option zeigt Ihre Azure AD Connect-Server an, die derzeit von Azure AD Connect Health überwacht werden. Der Eintrag **Synchronisierungsfehler** zeigt allgemeine Synchronisierungsfehler des ersten integrierten Synchronisierungsdiensts nach Kategorien. Wenn Sie den Eintrag **Synchronisierungsdienste** auswählen, wird ein Blatt mit Informationen zu Ihren Azure AD Connect-Servern geöffnet. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md).
* **Active Directory-Verbunddienste**: Diese Option zeigt alle AD FS-Dienste an, die derzeit von Azure AD Connect Health überwacht werden. Wenn Sie eine Instanz auswählen, wird ein Blatt mit Informationen zu dieser Dienstinstanz geöffnet, darunter beispielsweise eine Übersicht, Eigenschaften, Warnungen, Überwachungsinformationen und eine Nutzungsanalyse. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health mit AD FS](how-to-connect-health-adfs.md).
* **Active Directory Domain Services:** Diese Option zeigt alle AD DS-Gesamtstrukturen an, die derzeit von Azure AD Connect Health überwacht werden. Wenn Sie eine Gesamtstruktur auswählen, wird ein Blatt mit Informationen zu dieser Gesamtstruktur geöffnet. Diese Informationen umfassen eine Übersicht mit den wichtigsten Informationen, das Domänencontroller-Dashboard, das Replikationsstatus-Dashboard, Warnungen und Überwachungsdaten. Weitere Informationen zu den Funktionen finden Sie unter [Verwenden von Azure AD Connect Health mit AD DS](how-to-connect-health-adds.md).
* **Konfigurieren**: In diesem Abschnitt können Sie folgende Optionen aktivieren oder deaktivieren:

   - **Automatisches Update** des Azure AD Connect Health-Agents auf die aktuelle Version: Der Azure AD Connect Health-Agent wird automatisch aktualisiert, wenn neue Versionen verfügbar sind. Diese Option ist standardmäßig aktiviert.
   - **Zugriff auf Daten** vom Azure AD-Verzeichnisintegritätsdienst durch Microsoft nur zur Problembehandlung: Wenn diese Option aktiviert ist, kann Microsoft auf dieselben Daten zugreifen, die vom Benutzer angezeigt werden. Diese Informationen können bei der Problembehandlung hilfreich sein und die nötige Unterstützung bereitstellen. Diese Option ist standardmäßig deaktiviert.
* Im Abschnitt **Rollenbasierte Zugriffssteuerung (IAM)** wird der Zugriff auf Connect Health-Daten auf Rollenbasis verwaltet. 

## <a name="next-steps"></a>Nächste Schritte

- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Express-Einstellungen](how-to-connect-install-express.md)
- [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md)
- [Implementieren der Kennworthashsynchronisierung mit Azure AD Connect](how-to-connect-password-hash-synchronization.md)|
- [Passthrough-Authentifizierung](how-to-connect-pta.md)
- [Azure AD Connect und Verbund](how-to-connect-fed-whatis.md)
- [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md) 
- [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)