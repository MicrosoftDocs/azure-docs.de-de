---
title: Entwerfen von Hybrididentitäten – Strategie für die Einführung des Lebenszyklus in Azure | Microsoft-Dokumentation
description: Hilft, die Aufgaben der Verwaltung der Hybrididentität gemäß der für die einzelnen Phasen des Lebenszyklus verfügbaren Optionen zu definieren.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997748"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Ermitteln der Strategie für die Übernahme des Hybrididentitätslebenszyklus
In dieser Aufgabe definieren Sie die Identitätsverwaltungsstrategie für Ihre Hybrididentitätslösung, um die geschäftlichen Anforderungen zu erfüllen, die Sie in [Ermitteln von Aufgaben der Hybrididentitätsverwaltung](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)definiert haben.

Zum Definieren der Aufgaben der Hybrididentitätsverwaltung anhand des weiter oben in diesem Schritt vorgestellten End-to-End-Identitätslebenszyklus müssen Sie die für jede Lebenszyklusphase verfügbaren Optionen berücksichtigen.

## <a name="access-management-and-provisioning"></a>Zugriffsverwaltung und Bereitstellung
Mit einer guten Lösung für die Kontozugriffsverwaltung kann Ihre Organisation genau nachverfolgen, wer in der gesamten Organisation Zugriff auf welche Informationen hat.

Zugriffssteuerung ist eine entscheidende Funktion eines zentralisierten, Einzelpunktbereitstellungssystems. Zugriffssteuerungen schützen nicht nur sensible Informationen, sondern decken auch vorhandene Konten auf, die nicht genehmigte Autorisierungen besitzen oder nicht mehr notwendig sind. Um veraltete Konten zu steuern, verknüpft das Bereitstellungssystem Kontoinformationen mit Autorisierungsinformationen über die Benutzer, die die Konten besitzen. Die Autorisierung betreffende Benutzeridentitätsinformationen werden in der Regel in den Datenbanken und Verzeichnissen der Personalabteilung verwaltet.

Konten in anspruchsvollen IT-Unternehmen umfassen Hunderte von Parametern, die die Autorisierungen definieren, und diese Details kann Ihr Bereitstellungssystem steuern. Neue Benutzer können mit den Daten identifiziert werden, die Sie über die autoritative Quelle bereitstellen. Die Funktion zur Genehmigung der Zugriffsanforderung initiiert die Prozesse, die die Ressourcenbereitstellung für sie genehmigen (oder ablehnen).

| Lebenszyklusverwaltungsphase | Lokal | Cloud | Hybrid |
| --- | --- | --- | --- |
| Kontoverwaltung und Bereitstellung |Sie können mithilfe der Active Directory ®-Domänendienste-Serverrolle (Active Directory® Domain Services, AD DS) eine skalierbare, sichere und verwaltbare Infrastruktur für Benutzer- und Ressourcenverwaltung erstellen und Unterstützung für verzeichnisaktivierte Anwendungen wie Microsoft ® Exchange Server bereitstellen. <br><br> [Sie können Gruppen in AD DS über einen Identitäts-Manager bereitstellen.](/previous-versions/mim/ff686261(v=ws.10)) <br>[Sie können Benutzer in AD DS bereitstellen.](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Administratoren können zu Sicherheitszwecken mithilfe der Zugriffssteuerung den Benutzerzugriff auf gemeinsame Ressourcen verwalten. In Active Directory wird die Zugriffsteuerung auf Objektebene durch Festlegen verschiedener Ebenen des Zugriffs auf Objekte oder Berechtigungen verwaltet, wie z. B. „Vollzugriff“, „Schreiben“, „Lesen“ oder „Kein Zugriff“. Die Zugriffsteuerung in Active Directory definiert, wie verschiedene Benutzer Active Directory-Objekte verwenden können. Standardmäßig werden Berechtigungen für Objekte in Active Directory auf die sicherste Einstellung festgelegt. |Sie müssen für jeden Benutzer, der auf einen Microsoft-Clouddienst zugreifen soll, ein eigenes Konto erstellen. Sie können Benutzerkonten auch ändern oder löschen, wenn sie nicht mehr benötigt werden. Benutzer haben standardmäßig keine Administratorberechtigungen, aber Sie können ihnen diese erteilen. <br><br> Eines der wichtigsten Features innerhalb von Azure Active Directory ist die Möglichkeit, den Zugriff auf Ressourcen zu verwalten. Diese Ressourcen können Teil des Verzeichnisses sein, wie im Fall von Berechtigungen zum Verwalten von Objekten mithilfe von Rollen im Verzeichnis, oder externe Ressourcen, z. B. SaaS-Anwendungen, Azure-Dienste und SharePoint-Websites oder lokale Ressourcen. <br><br> Im Mittelpunkt der Lösung zur Zugriffsverwaltung von Azure Active Directory steht die Sicherheitsgruppe. Der Besitzer der Ressource (oder der Administrator des Verzeichnisses) kann eine Gruppe zuweisen, um bestimmte Zugriffsrechte für seine Ressourcen bereitzustellen. Den Mitgliedern der Gruppe wird Zugriff erteilt, und der Besitzer der Ressource kann das Recht zur Verwaltung der Mitgliederliste einer Gruppe an Dritte delegieren – etwa an einen Abteilungsleiter oder Helpdeskadministrator.<br> <br> Der Abschnitt „Verwalten von Gruppen in Azure AD“ enthält weitere Informationen zum Verwalten des Zugriffs über Gruppen. |Erweitern von Active Directory-Identitäten in die Cloud durch Synchronisierung und Verbund |

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung
Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) setzt Rollen- und Bereitstellungsrichtlinien zum Auswerten, Testen und Erzwingen Ihrer Geschäftsprozesse und Regeln für das Gewähren von Zugriffsrechten für Benutzer ein. Hauptadministratoren erstellen Bereitstellungsrichtlinien, weisen Benutzern Rollen zu und definieren für diese Rollen Sätze von Berechtigungen für Ressourcen. Azure RBAC erweitert die Identitätsverwaltungslösung auf die Verwendung softwarebasierter Prozesse und Reduzierung der manuellen Benutzerinteraktion im Bereitstellungsprozess.
Azure RBAC ermöglicht es Unternehmen, die Anzahl von Vorgängen zu beschränken, die eine einzelne Person über das Azure-Portal ausführen kann. Durch Einsatz von Azure RBAC zur Steuerung des Zugriffs auf das Portal können IT-Administratoren den Zugriff mithilfe folgender Ansätze zur Zugriffsverwaltung delegieren:

* **Gruppenbasierte Rollenzuweisung**: Sie können Azure AD-Gruppen den Zugriff zuweisen, die von Ihrem lokalen Active Directory aus synchronisiert werden können. So können Sie die vorhandenen Investitionen nutzen, die Ihre Organisation in Tools und Prozesse zum Verwalten von Gruppen vorgenommen hat. Sie können auch das Feature zur delegierten Gruppenverwaltung von Azure AD Premium nutzen.
* **Nutzen Sie integrierte Rollen in Azure**: Sie können diese drei Rollen verwenden – Besitzer, Mitwirkender und Leser, um sicherzustellen, dass Benutzer und Gruppen berechtigt sind, nur die Aufgaben auszuführen, die sie für ihre Arbeit benötigen.
* **Präziser Zugriff auf Ressourcen**: Sie können Benutzern und Gruppen Rollen für bestimmte Abonnements, Ressourcengruppen oder für eine einzelne Azure-Ressource (beispielsweise eine Website oder Datenbank) zuweisen. Auf diese Weise können Sie sicherstellen, dass Benutzer Zugriff auf alle Ressourcen haben, die sie benötigen, und keinen Zugriff auf Ressourcen, die sie nicht verwalten müssen.

## <a name="provisioning-and-other-customization-options"></a>Bereitstellung und andere Optionen für die Anpassung
Ihr Team kann Geschäftspläne und -anforderungen nutzen, um zu entscheiden, wie stark die Identitätslösung angepasst werden soll. Ein großes Unternehmen könnte z. B. einen schrittweisen Rollout-Plan für Workflows und benutzerdefinierte Adapter benötigen, der auf einer Zeitachse für die inkrementelle Bereitstellung von Anwendungen basiert, die über einen großen geografischen Raum hinweg verwendet werden. Ein anderer Plan zur Anpassung könnte die organisationsweite Bereitstellung von zwei oder mehr Anwendungen nach erfolgreichem Test ermöglichen. Die Interaktion zwischen Benutzer und Anwendung kann angepasst werden, und Verfahren für die Bereitstellung von Ressourcen könnten geändert werden, um der automatisierten Bereitstellung gerecht zu werden.

Sie können die Bereitstellung aufheben, um einen Dienst oder eine Komponente zu entfernen. Das Aufheben der Bereitstellung eines Kontos bedeutet beispielsweise, dass das Konto aus einer Ressource gelöscht wird.

Das Hybridmodell der Bereitstellung von Ressourcen kombiniert anforderungs- und rollenbasierte Ansätze, die beide von Azure AD unterstützt werden. Für eine Teilmenge von Mitarbeitern oder verwalteten Systemen könnte ein Unternehmen beabsichtigen, den Zugriff mit rollenbasierter Zuweisung zu automatisieren. Ein Unternehmen könnte auch alle anderen Zugriffsanforderungen oder Ausnahmen über ein anforderungsbasiertes Modell abwickeln. Manche Unternehmen könnten mit manueller Zuweisung beginnen und sich auf ein Hybridmodell hin entwickeln, mit dem Ziel einer zukünftig vollständig rollenbasierten Bereitstellung.

Andere Unternehmen finden es vielleicht aus geschäftlichen Gründen unpraktisch, eine vollständig rollenbasierte Bereitstellung zu erzielen, und betrachten einen Hybridansatz als wünschenswert. Weitere Unternehmen könnten mit einer ausschließlich anforderungsbasierten Bereitstellung zufrieden sein und nicht bereit sein, zusätzlichen Aufwand zu betreiben, um rollenbasierte, automatisierte Bereitstellungsrichtlinien zu definieren und zu verwalten.

## <a name="license-management"></a>Lizenzverwaltung
Gruppenbasierte Lizenzverwaltung in Azure AD ermöglicht Administratoren, Benutzer einer Sicherheitsgruppe zuzuweisen, und Azure AD weist Lizenzen automatisch allen Mitgliedern der Gruppe zu. Wenn ein Benutzer nachträglich der Gruppe hinzugefügt oder aus der Gruppe entfernt wird, wird automatisch eine Lizenz entsprechend zugewiesen bzw. entfernt.

Sie können Gruppen verwenden, die Sie aus lokalem AD synchronisieren oder in Azure AD verwalten. Wenn Sie dies mit der Self-Service-Gruppenverwaltung von Azure AD Premium verbinden, können Sie die Lizenzzuweisung problemlos an die entsprechenden Entscheidungsträger delegieren. Sie können sicher sein, dass Probleme wie Lizenzkonflikte und fehlende Standortdaten automatisch aussortiert werden.

## <a name="self-regulating-user-administration"></a>Selbstregulierende Benutzerverwaltung
Wenn Ihre Organisation beginnt, Ressourcen für alle internen Organisationen bereitzustellen, implementieren Sie die selbstregulierende Benutzerverwaltung. Sie können die Vorteile der Benutzerbereitstellung über Organisationsgrenzen hinweg realisieren. In dieser Umgebung spiegelt sich eine Änderung im Status eines Benutzers automatisch über die Grenzen der Organisation und geografische Regionen hinweg in Zugriffsrechten wider. Sie können Bereitstellungskosten reduzieren und die Zugriffs- und Genehmigungsprozesse optimieren. Die Implementierung erkennt das volle Potenzial des Implementierens rollenbasierter Zugriffssteuerung für die End-to-End-Zugriffsverwaltung in Ihrer Organisation. Sie können die Verwaltungskosten durch automatisierte Verfahren für die Verwaltung der Benutzerbereitstellung reduzieren. Sie können die Sicherheit durch Automatisierung des Erzwingens von Sicherheitsrichtlinien verbessern und die Verwaltung des Benutzerlebenszyklus und die Bereitstellung von Ressourcen für große Benutzerzahlen optimieren und zentralisieren.

> [!NOTE]
> Weitere Informationen finden Sie unter „Einrichten von Azure AD zur Self-Service-Verwaltung des Anwendungszugriffs“
> 
> 

Lizenzbasierte (berechtigungsbasierte) Azure AD-Dienste funktionieren durch die Aktivierung eines Abonnements im Azure AD-Verzeichnis/-Dienstmandanten. Sobald das Abonnement aktiv ist, können Dienstfunktionen von Verzeichnis-/Dienstadministratoren verwaltet und von lizenzierten Benutzern verwendet werden. 

## <a name="integration-with-other-3rd-party-providers"></a>Integration mit anderen Drittanbietern

Azure Active Directory bietet einmaliges Anmelden und erweiterte Anwendungszugriffssicherheit bei Tausenden von SaaS-Anwendungen und lokalen Webanwendungen. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../develop/quickstart-register-app.md).

## <a name="define-synchronization-management"></a>Definieren der Synchronisierungsverwaltung
Die Integration Ihrer lokalen Verzeichnisse in Azure AD steigert die Produktivität Ihrer Benutzer, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird. Mit dieser Integration können Benutzer und Organisationen die folgenden Vorteile nutzen:

* Organisationen können für Benutzer eine Hybrididentität zum Zugriff auf lokale und cloudbasierte Dienste bereitstellen, die mithilfe von Windows Server Active Directory die Verbindung zu Azure Active Directory ermöglicht.
* Administratoren können basierend auf Anwendungsressourcen, Geräte- und Benutzeridentität, Netzwerkadressen und Multi-Factor Authentication Richtlinien für den bedingten Zugriff erstellen.
* Benutzer können ihre allgemeine Identität über Konten von Azure AD für Microsoft 365, Intune, SaaS-Anwendungen und Anwendungen von Drittanbietern nutzen.
* Entwickler können Anwendungen erstellen, die das allgemeine Identitätsmodell nutzen, und Anwendungen in das lokale Active Directory oder Azure für cloudbasierte Anwendungen integrieren.

Die folgende Abbildung zeigt den Identitätssynchronisierungsprozess im Überblick.

![Synchronisierung](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Identitätssynchronisierungsprozess

Vergleichen Sie die Synchronisierungsoptionen anhand folgender Tabelle:

| Synchronisierungsverwaltungsoption | Vorteile | Nachteile |
| --- | --- | --- |
| Synchronisierungsbasiert (über DirSync oder AADConnect) |Benutzer und Gruppen aus der lokalen Umgebung und aus der Cloud werden synchronisiert. <br>  **Richtliniensteuerung:** Kontorichtlinien können über Active Directory festgelegt werden. Dies ermöglicht es dem Administrator, Richtlinien für Kennwörter, Workstations, Einschränkungen, Sperrsteuerungen und mehr zu verwalten, ohne zusätzliche Aufgaben in der Cloud ausführen zu müssen.  <br>  **Zugriffssteuerung:** Kann den Zugriff auf den Clouddienst einschränken, sodass der Zugriff auf die Dienste über die Unternehmensumgebung, über Onlineserver oder beides erfolgen kann. <br>  Weniger Supportanrufe: Wenn Benutzer sich weniger Kennwörter merken müssen, ist die Wahrscheinlichkeit geringer, dass sie sie vergessen. <br>  Sicherheit: Benutzeridentitäten und -informationen sind geschützt, da alle Server und Dienste mit einmaligem Anmelden verwendet und lokal verwaltet und gesteuert werden. <br>  Unterstützung für strenge Authentifizierung: Sie können strenge Authentifizierung (auch als zweistufige Authentifizierung bezeichnet) mit dem Clouddienst nutzen. Wenn Sie jedoch strenge Authentifizierung verwenden, müssen Sie einmaliges Anmelden verwenden. | |
| Verbundbasiert (über AD FS) |Aktiviert durch Sicherheitstokendienst (Security Token Service, STS). Wenn Sie einen STS konfigurieren, um Zugriff mit einmaligem Anmelden mit einem Microsoft-Clouddienst zu bieten, erstellen Sie eine Verbundvertrauensstellung zwischen Ihrem lokalen STS und der Verbunddomäne, die Sie in Ihrem Azure AD-Mandanten angegeben haben. <br> Ermöglicht Endbenutzern, mit den gleichen Anmeldeinformationen auf mehrere Ressourcen zuzugreifen. <br>Endbenutzer müssen nicht mehrere Sätze von Anmeldeinformationen verwalten. Dennoch müssen die Benutzer ihre Anmeldeinformationen jeder der beteiligten Ressourcen bereitstellen. B2B- und B2C-Szenarien werden unterstützt. |Setzt spezialisierte Mitarbeiter für die Bereitstellung und Wartung von dedizierten lokalen AD FS-Servern voraus. Es gibt Einschränkungen für die Verwendung der starken Authentifizierung, wenn Sie AD FS für Ihren STS verwenden möchten. Weitere Informationen finden Sie unter [Konfigurieren erweiterter Optionen für AD FS 2.0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Weitere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](plan-hybrid-identity-design-considerations-overview.md)