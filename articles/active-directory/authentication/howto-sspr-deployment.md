---
title: Überlegungen zur Bereitstellung der Self-Service-Kennwortzurücksetzung von Azure Active Directory
description: Überlegungen zur Bereitstellung und Strategie für eine erfolgreiche Implementierung der Self-Service-Kennwortzurücksetzung von Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 765bfe0f805ae4219110d689e8f7f8fd54a26cf3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173935"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Planen der Bereitstellung einer Self-Service-Kennwortzurücksetzung (SSPR) von Azure Active Directory

> [!IMPORTANT]
> Dieser Bereitstellungsplan bietet Anleitungen und bewährte Methoden für die Bereitstellung der Self-Service-Kennwortzurücksetzung (SSPR) von Azure AD.
>
> **Wenn Sie ein Endbenutzer sind und keinen Zugriff mehr auf Ihr Konto haben, wechseln Sie zu [https://aka.ms/sspr](https://aka.ms/sspr)** .

Die [Self-Service-Kennwortzurücksetzung (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) ist eine Funktion von Azure Active Directory (AD), die es Benutzern ermöglicht, ihre Kennwörter ohne die Hilfe von IT-Mitarbeitern zurückzusetzen. Die Benutzer können ungeachtet von Tages- oder Nachtzeit und Standort schnell ihre Sperre aufheben und weiterarbeiten. Indem eine Organisation ihren Mitarbeitern erlaubt, die Sperre selbst aufzuheben, kann sie unproduktive Zeit und hohe Supportkosten für die meisten allgemeinen Probleme im Zusammenhang mit Kennwörtern reduzieren.

Folgende SSPR-Hauptfunktionen sind verfügbar:

* Mit der Self-Service-Kennwortzurücksetzung können Endbenutzer ihre abgelaufenen (oder auch noch nicht abgelaufenen) Kennwörter ohne Unterstützung eines Administrators oder des Helpdesks zurücksetzen.
* Die [Kennwortrückschreibung](./concept-sspr-writeback.md) ermöglicht die Verwaltung lokaler Kennwörter und die Aufhebung von Kontosperren über die Cloud.
* Berichte zu Kennwortverwaltungsaktivitäten gewähren Administratoren Einblicke in die Aktivitäten, die sich in Bezug auf Kennwortzurücksetzung und -registrierung in ihrer Organisation ereignen.

In diesem Bereitstellungsleitfaden wird gezeigt, wie Sie einen SSPR-Rollout (Self-Service-Kennwortzurücksetzung) planen und anschließend testen.

Um die Self-Service-Kennwortzurücksetzung in Aktion zu sehen und dann zurückzukehren, um weitere Überlegungen zur Bereitstellung zu verstehen, gehen Sie wie folgt vor:

> [!div class="nextstepaction"]
> [Aktivieren der Self-Service-Kennwortzurücksetzung (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Informationen zu SSPR

Weitere Informationen zu SSPR. Lesen Sie [So funktioniert‘s: Self-Service-Kennwortzurücksetzung in Azure AD](./concept-sspr-howitworks.md).

### <a name="key-benefits"></a>Hauptvorteile

Die Aktivierung der Self-Service-Kennwortzurücksetzung (SSPR) bietet die folgenden wichtigen Vorteile:

* **Kostenmanagement**. SSPR reduziert die Kosten für den IT-Support, da die Benutzer die Möglichkeit erhalten, ihre Kennwörter selbst zurückzusetzen. Reduziert werden auch die Kosten für den Zeitaufwand aufgrund vergessener Kennwörter und Kontosperren. 

* **Intuitive Benutzerumgebung**. Durch den intuitiven einmaligen Benutzerregistrierungsprozess können Benutzer bei Bedarf und von jedem Gerät oder Standort aus ihre Kennwörter zurücksetzen und Konten entsperren. Mit SSPR können Benutzer schneller wieder an die Arbeit gehen und produktiver sein.

* **Flexibilität und Sicherheit**. SSPR ermöglicht Unternehmen den Zugang zu der Sicherheit und Flexibilität, die eine Cloudplattform bietet. Administratoren können Einstellungen ändern, neue Sicherheitsanforderungen aufnehmen und diese Änderungen ohne Unterbrechung des Anmeldevorgangs an die Benutzer weitergeben.

* **Zuverlässige Überwachung und Nutzungsverfolgung**. Auch wenn die Benutzer ihre eigenen Kennwörter zurücksetzen, kann eine Organisation die Sicherheit für ihre Geschäftssysteme gewährleisten. Zuverlässige Überwachungsprotokolle enthalten Informationen zu jedem Schritt des Kennwortzurücksetzungsprozesses. Diese Protokolle sind über eine API verfügbar, und der Benutzer hat die Möglichkeit, die Daten in ein SIEM-System (Security Incident and Event Monitoring) seiner Wahl zu importieren.

### <a name="licensing"></a>Lizenzierung

Azure Active Directory wird pro Benutzer lizenziert. Dies bedeutet, dass jeder Benutzer über eine entsprechende Lizenz für die von ihm genutzten Features verfügen muss. Für die Self-Service-Kennwortzurücksetzung empfehlen wir die gruppenbasierte Lizenzierung. 

Einen Vergleich der Editionen und Funktionen sowie Informationen zum Aktivieren der gruppen- oder benutzerdefinierten Lizenzierung finden Sie unter [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](./concept-sspr-licensing.md).

Weitere Informationen zur Preisgestaltung finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Voraussetzungen

* Ein funktionierender Azure AD-Mandant mit mindestens einer aktivierten Testlizenz. Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ein Konto mit Berechtigungen vom Typ „Globaler Administrator“.


### <a name="training-resources"></a>Schulungsressourcen

| Ressourcen| Link und Beschreibung |
| - | - |
| Videos| [Handlungsfähigere Benutzer mit besserer IT-Skalierbarkeit](https://youtu.be/g9RpRnylxS8) 
| |[Was ist Self-Service-Kennwortzurücksetzung?](https://youtu.be/hc97Yx5PJiM)|
| |[Bereitstellen der Self-Service-Kennwortzurücksetzung](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Konfigurieren der Self-Service-Kennwortzurücksetzung für Benutzer in Azure AD](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[[Vorbereiten von Benutzern zum] Registrieren von Sicherheitsinformationen für Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Onlinekurse|[Verwalten von Identitäten in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Verwenden Sie SSPR, um Ihren Benutzern eine moderne, geschützte Umgebung bereitzustellen. Achten Sie besonders auf das Modul [Verwalten von Benutzern und Gruppen in Azure Active Directory](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents). |
|Kostenpflichtige Pluralsight-Kurse |[Probleme der Identitäts- und Zugriffsverwaltung (IAM)](https://www.pluralsight.com/courses/identity-access-management-issues) Erfahren Sie mehr über IAM und Sicherheitsprobleme, auf die Sie in Ihrer Organisation achten sollten. Beachten Sie besonders das Modul „Weitere Authentifizierungsmethoden“.|
| |[Erste Schritte mit Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Lernen Sie die besten Methoden für die Ausweitung von lokalen Ressourcen in die Cloud kennen, die Authentifizierung, Autorisierung, Verschlüsselung und eine sichere Umgebung für Mobilgeräte gewährleisten. Beachten Sie besonders das Modul „Konfigurieren erweiterter Funktionen von Microsoft Azure Active Directory Premium“.
|Tutorials |[Ausführen eines Rollouts der Azure AD-Self-Service-Kennwortzurücksetzung für eine Pilotgruppe](./tutorial-enable-sspr.md) |
| |[Aktivieren des Kennwortrückschreibens](./tutorial-enable-sspr-writeback.md) |
| |[Azure AD-Kennwortzurücksetzung über den Anmeldebildschirm unter Windows 10](./howto-sspr-windows.md) |
| Häufig gestellte Fragen|[Häufig gestellte Fragen zur Kennwortverwaltung](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>Lösungsarchitektur

Im folgenden Beispiel wird die Lösungsarchitektur der Kennwortzurücksetzung für häufige Hybridumgebungen beschrieben.

![Diagramm der Lösungsarchitektur](./media/howto-sspr-deployment//solutions-architecture.png)

Beschreibung des Workflows

Zum Zurücksetzen des Kennworts müssen Benutzer zum [Kennwortzurücksetzungs-Portal](https://aka.ms/sspr) wechseln. Sie müssen die zuvor registrierte(n) Authentifizierungsmethode(n) bestätigen, um ihre Identität nachzuweisen. Wenn sie das Kennwort erfolgreich zurücksetzen, beginnt der Zurücksetzungsprozess.

* Bei reinen Cloudbenutzern speichert SSPR das neue Kennwort in Azure AD. 

* Bei Hybridbenutzern schreibt SSPR das Kennwort über den Azure AD Connect-Dienst zurück in das lokale Active Directory. 

Hinweis: Bei Benutzern, bei denen die [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) deaktiviert ist, speichert SSPR die Kennwörter nur im lokalen Active Directory.

### <a name="best-practices"></a>Bewährte Methoden

Sie können Benutzer bei der schnellen Registrierung unterstützen, indem Sie SSPR zusammen mit einer anderen gängigen Anwendung oder einem anderen Dienst in der Organisation bereitstellen. Diese Aktion wird zu einer großen Anzahl von Anmeldungen führen und fördert die Registrierung.

Bevor Sie die Self-Service-Kennwortzurücksetzung bereitstellen, können Sie wahlweise auch die Anzahl und die durchschnittlichen Kosten für jeden Anruf zur Kennwortzurücksetzung bestimmen. Anhand dieser Daten können Sie nach der Bereitstellung den Mehrwert von SSPR für die Organisation darstellen.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Aktivieren der kombinierten Registrierung für SSPR und MFA

Microsoft empfiehlt Organisationen, die kombinierte Registrierungs-Benutzeroberfläche für SSPR und mehrstufige Authentifizierung zu aktivieren. Wenn Sie diese kombinierte Registrierungs-Benutzeroberfläche aktivieren, brauchen Benutzer ihre Registrierung nur einmal auszuwählen, um beide Funktionen zu aktivieren.

Durch die kombinierte Registrierungsumgebung sind Organisationen nicht gezwungen, sowohl SSPR als auch Azure AD Multi-Factor Authentication zu aktivieren. Die kombinierte Registrierung sorgt in Organisationen für eine höhere Benutzerfreundlichkeit. Weitere Informationen finden Sie unter [Kombinierte Registrierung von Sicherheitsinformationen](concept-registration-mfa-sspr-combined.md).

## <a name="plan-the-deployment-project"></a>Planen des Bereitstellungsprojekts

Berücksichtigen Sie die Anforderungen Ihrer Organisation, während Sie die Strategie für diese Bereitstellung in Ihrer Umgebung festlegen.

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Fehler in Technologieprojekten sind in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, [achten Sie darauf, die richtigen Beteiligten einzubeziehen](../fundamentals/active-directory-deployment-plans.md) und die Rolle der Beteiligten präzise zu definieren, indem Sie die Beteiligten, ihren Projektbeitrag und ihre Zuständigkeiten dokumentieren.

#### <a name="required-administrator-roles"></a>Erforderliche Administratorrollen


| Geschäftliche Rolle/Persona| Azure AD-Rolle (sofern erforderlich) |
| - | - |
| Helpdesk Ebene 1| Kennwortadministrator |
| Helpdesk Ebene 2| Benutzeradministrator |
| SSPR-Administrator| Globaler Administrator |


### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Sie sollten proaktiv mit Ihren Benutzern darüber kommunizieren, wie sich die Nutzung ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen. Lesen Sie die [Self-service password reset rollout materials on the Microsoft download center](https://www.microsoft.com/download/details.aspx?id=56768) (Rolloutmaterialien zur Self-Service-Kennwortzurücksetzung im Microsoft-Downloadcenter), um Ideen zum Planen Ihrer Endbenutzer-Kommunikationsstrategie zu erhalten.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Wir empfehlen, die erste Konfiguration von SSPR in einer Testumgebung vorzunehmen. Beginnen Sie mit einer Pilotgruppe, indem Sie SSPR für eine Teilmenge von Benutzern in Ihrer Organisation aktivieren. Lesen Sie hierzu [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md).

Informationen zum Erstellen einer Gruppe finden Sie unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md). 

## <a name="plan-configuration"></a>Planen der Konfiguration

Die folgenden Einstellungen sind erforderlich, um SSPR mit den empfohlenen Werten zu aktivieren.

| Bereich | Einstellung | Wert |
| --- | --- | --- |
| **SSPR-Eigenschaften** | Self-Service-Kennwortzurücksetzung aktiviert | **Ausgewählte** Gruppe für das Pilotprojekt/**Alle** für die Produktion |
| **Authentifizierungsmethoden** | Zum Registrieren erforderliche Authentifizierungsmethoden | Immer 1 mehr als für das Zurücksetzen erforderlich |
|   | Zum Zurücksetzen erforderliche Authentifizierungsmethoden | Eine oder zwei |
| **Registrierung** | Registrierung von Benutzern bei der Anmeldung verlangen | Ja |
|   | Anzahl der Tage, bevor Benutzer aufgefordert werden, ihre Authentifizierungsinformationen erneut zu bestätigen | 90–180 Tage |
| **Benachrichtigungen** | Benutzer über Kennwortzurücksetzungen benachrichtigen? | Ja |
|   | Sollen alle Administratoren benachrichtigt werden, wenn andere Administratoren ihr Kennwort zurücksetzen? | Ja |
| **Anpassung** | Helpdesklink anpassen | Ja |
|   | Benutzerdefinierte Helpdesk-E-Mail oder URL | Support-Website oder E-Mail-Adresse |
| **Lokale Integration** | Zurückschreiben von Kennwörtern in das lokale AD | Ja |
|   | Benutzern das Entsperren des Kontos ohne Zurücksetzen des Kennworts erlauben | Ja |

### <a name="sspr-properties"></a>SSPR-Eigenschaften

Wählen Sie beim Aktivieren von SSPR eine entsprechende Sicherheitsgruppe in der Pilotumgebung aus.

* Um die SSPR-Registrierung für alle Gruppen zu erzwingen, empfehlen wir die Verwendung der Option **Alle**.
* Wählen Sie ansonsten die entsprechende Azure AD- oder AD-Sicherheitsgruppe aus.

### <a name="authentication-methods"></a>Authentifizierungsmethoden

Wenn SSPR aktiviert ist, können Benutzer ihr Kennwort nur dann zurücksetzen, wenn für die Authentifizierungsmethoden, die der Administrator aktiviert hat, Benutzerdaten vorliegen. Zu den Methoden zählen Telefon, Benachrichtigung in Authenticator-App, Sicherheitsfrage usw. Weitere Informationen finden Sie unter [Authentifizierungsmethoden](./concept-authentication-methods.md).

Als Authentifizierungsmethoden empfehlen wir die folgenden Einstellungen:

* Legen Sie unter **Zum Registrieren erforderliche Authentifizierungsmethoden** mindestens eine Methode mehr fest, als zum Zurücksetzen erforderlich sind. Das Zulassen mehrerer Authentifizierungsmethoden gibt Benutzern Flexibilität beim Zurücksetzen.

* Legen Sie **Anzahl der zum Zurücksetzen erforderlichen Methoden**  auf ein für Ihre Organisation angemessenes Maß fest. Eine bringt das geringste Maß an Reibung mit sich, während zwei den Sicherheitsstatus Ihrer Organisation verbessern können. 

Hinweis: Der Benutzer muss über die Authentifizierungsmethoden verfügen, die in [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](./concept-sspr-policy.md) konfiguriert wurden.

### <a name="registration-settings"></a>Registrierungseinstellungen

Legen Sie **Registrierung von Benutzern bei der Anmeldung verlangen** auf **Ja** fest. Bei dieser Einstellung müssen sich die Benutzer beim Anmelden registrieren. Dadurch ist gewährleistet, dass alle Benutzer geschützt sind.

Legen Sie **Anzahl der Tage, bevor Benutzer aufgefordert werden, ihre Authentifizierungsinformationen erneut zu bestätigen** auf einen Wert zwischen **90** und **180** Tagen fest, sofern in Ihrer Organisation aus geschäftlichen Gründen kein kürzerer Zeitraum erforderlich ist.

### <a name="notifications-settings"></a>Benachrichtigungseinstellungen

Konfigurieren Sie beide Einstellungen **Benutzer über Kennwortzurücksetzungen benachrichtigen?** und **Sollen alle Administratoren benachrichtigt werden, wenn andere Administratoren ihr Kennwort zurücksetzen?** mit **Ja**. Durch die Auswahl von **Ja** bei beiden Optionen erhöht sich die Sicherheit, da gewährleistet ist, dass Benutzer von der Zurücksetzung ihres Kennworts Kenntnis erhalten. Außerdem ist gewährleistet, dass alle Administratoren Kenntnis erhalten, wenn ein Administrator ein Kennwort ändert. Wenn Benutzer oder Administratoren eine derartige Benachrichtigung erhalten und die Änderung nicht veranlasst haben, können sie sofort ein mögliches Sicherheitsproblem melden.

### <a name="customization-settings"></a>Anpassungseinstellungen

Es ist wichtig, die E-Mail-Adresse oder URL des Helpdesks anzupassen, um sicherzustellen, dass Benutzer, bei denen Probleme auftreten, sofort Hilfe erhalten. Legen Sie diese Option auf eine allgemeine E-Mail-Adresse oder Website fest, die Ihren Benutzern vertraut ist. 

Weitere Informationen finden Sie unter [Anpassen der Azure AD-Funktionalität für die Self-Service-Kennwortzurücksetzung](./howto-sspr-customization.md).

### <a name="password-writeback"></a>Rückschreiben von Kennwörtern

Die Funktion **Kennwortrückschreiben** wird mit [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) aktiviert und ermöglicht es, Kennwortzurücksetzungen in der Cloud in Echtzeit in ein vorhandenes lokales Verzeichnis zurückzuschreiben. Weitere Informationen finden Sie unter [Was ist Kennwortrückschreiben?](./concept-sspr-writeback.md)

Wir empfehlen die folgenden Einstellungen:

* Stellen Sie sicher, dass **Zurückschreiben von Kennwörtern in das lokale AD** auf **Ja** festgelegt ist. 
* Legen Sie **Benutzern das Entsperren des Kontos ohne Zurücksetzen des Kennworts erlauben** auf **Ja** fest.

Standardmäßig werden bei einer Kennwortzurücksetzung Konten von Azure AD entsperrt.

### <a name="administrator-password-setting"></a>Einstellung für Administratorkennwort

Administratorkonten verfügen über erhöhte Berechtigungen. Lokale Unternehmens- oder Domänenadministratoren können ihr Kennwort nicht mithilfe von SSPR zurücksetzen. Für lokale Administratorkonten gelten die folgenden Einschränkungen:

* Kennwörter können nur in der lokalen Umgebung des Administrators geändert werden.
* Sicherheitsfragen und -antworten können in keinem Fall als Methode zum Zurücksetzen des Kennworts verwendet werden.

Wir empfehlen, lokale Active Directory-Administratorkonten nicht mit Azure AD zu synchronisieren.

### <a name="environments-with-multiple-identity-management-systems"></a>Umgebungen mit mehreren Identitätsverwaltungssystemen

Einige Umgebungen verfügen über mehrere Identitätsverwaltungssysteme. Lokale Identitätsverwaltungssysteme wie Oracle AM und SiteMinder erfordern die Kennwortsynchronisierung mit AD. Dafür können Sie ein Tool wie Password Change Notification Service (PCNS) mit Microsoft Identity Manager (MIM) verwenden. Informationen zu diesem komplexeren Szenario finden Sie im Artikel [Bereitstellen des MIM-Benachrichtigungsdiensts für Kennwortänderungen auf einem Domänencontroller](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-testing-and-support"></a>Planen von Tests und Support

Stellen Sie auf jeder Bereitstellungsstufe (von anfänglichen Pilotgruppen bis hin zur organisationsweiten Bereitstellung) sicher, das die Ergebnisse den Erwartungen entsprechen.

### <a name="plan-testing"></a>Planen von Tests

Um sicherzustellen, dass Ihre Bereitstellung erwartungsgemäß funktioniert, sollten Sie eine Reihe von Testfällen zum Überprüfen der Implementierung planen. Um auf die Testfälle zuzugreifen, benötigen Sie einen Testbenutzer (ohne Administratorrechte) mit einem Kennwort. Falls Sie einen Benutzer erstellen müssen, finden Sie unter [Hinzufügen neuer Benutzer in Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) entsprechende Informationen.

Die folgende Tabelle enthält praktische Testszenarien, die Sie zum Dokumentieren der auf der Grundlage Ihrer Richtlinien erwarteten Ergebnisse für Ihre Organisation verwenden können.
<br>


| Geschäftsszenario| Erwartete Ergebnisse |
| - | - |
| Auf das SSPR-Portal kann aus dem Unternehmensnetzwerk zugegriffen werden| Von Ihrer Organisation bestimmt |
| Auf das SSPR-Portal kann von außerhalb des Unternehmensnetzwerks zugegriffen werden| Von Ihrer Organisation bestimmt |
| Benutzerkennwort im Browser zurücksetzen, wenn für den Benutzer keine Kennwortzurücksetzung aktiviert ist| Der Benutzer kann nicht auf den Workflow zur Kennwortzurücksetzung zugreifen |
| Benutzerkennwort im Browser zurücksetzen, wenn sich der Benutzer nicht für die Kennwortzurücksetzung registriert hat| Der Benutzer kann nicht auf den Workflow zur Kennwortzurücksetzung zugreifen |
| Der Benutzer meldet sich an, wenn er zur Registrierung für die Kennwortzurücksetzung aufgefordert wird| Fordert den Benutzer zum Registrieren von Sicherheitsinformationen auf |
| Der Benutzer meldet sich an, wenn die Registrierung für die Kennwortzurücksetzung abgeschlossen ist| Fordert den Benutzer zum Registrieren von Sicherheitsinformationen auf |
| Auf das SSPR-Portal kann zugegriffen werden, wenn der Benutzer keine Lizenz besitzt| Zugriff möglich |
| Zurücksetzen des Benutzerkennworts über den Sperrbildschirm eines in Azure AD oder hybrid in Azure AD eingebundenen Geräts unter Windows 10| Benutzer kann Kennwort zurücksetzen |
| SSPR-Registrierungs- und Nutzungsdaten stehen Administratoren nahezu in Echtzeit zur Verfügung| Über die Überwachungsprotokolle verfügbar |

Siehe auch [Ausführen eines Rollouts der Azure AD-Self-Service-Kennwortzurücksetzung für eine Pilotgruppe](./tutorial-enable-sspr.md). In diesem Tutorial aktivieren Sie ein Rollout der Self-Service-Kennwortzurücksetzung (SSPR) für eine Pilotgruppe in Ihrer Organisation und führen Tests mithilfe eines Kontos ohne Administratorrechte aus.

### <a name="plan-support"></a>Planen von Support

Auch wenn SSPR normalerweise keine Benutzerprobleme mit sich bringt, ist es wichtig, Supportmitarbeiter auf die Behandlung von möglicherweise auftretenden Problemen vorzubereiten. Auch wenn ein Administrator das Kennwort für Endbenutzer im Azure AD-Portal zurücksetzen kann, ist es sinnvoller, das Problem durch einen Self-Service-Supportprozess zu lösen.

Damit ihr Supportteam erfolgreich tätig werden kann, können Sie anhand der von Benutzer gestellten Fragen ein Dokument mit häufig gestellten Fragen erstellen. Hier sind einige Beispiele:

| Szenarien| BESCHREIBUNG |
| - | - |
| Dem Benutzer stehen keine registrierten Authentifizierungsmethoden zur Verfügung| Ein Benutzer versucht, sein Kennwort zurückzusetzen, es ist aber keine der für ihn registrierten Authentifizierungsmethoden verfügbar (Beispiel: Handy zu Hause vergessen und kein Zugriff auf E-Mail) |
| Der Benutzer erhält keine SMS und keinen Anruf auf seinem Geschäfts- oder Mobiltelefon| Ein Benutzer versucht, seine Identität per SMS oder Anruf zu bestätigen, erhält aber keine SMS/keinen Anruf. |
| Benutzer kann nicht auf das Portal für die Kennwortzurücksetzung zugreifen| Ein Benutzer möchte sein Kennwort zurücksetzen, ist aber nicht für die Kennwortzurücksetzung aktiviert und kann daher nicht auf die Seite zum Aktualisieren von Kennwörtern zugreifen. |
| Benutzer kann kein neues Kennwort festlegen| Ein Benutzer schließt die Überprüfung während des Kennwortzurücksetzungsflows ab, kann aber kein neues Kennwort festlegen. |
| Dem Benutzer wird auf einem Gerät mit Windows 10 kein Link „Kennwort zurücksetzen“ angezeigt| Ein Benutzer versucht, sein Kennwort über den Sperrbildschirm eines Geräts mit Windows 10 zurückzusetzen, das Gerät ist aber nicht in Azure AD eingebunden, oder die Intune-Geräterichtlinie ist nicht aktiviert |

### <a name="plan-rollback"></a>Planen eines Rollbacks

So führen Sie ein Rollback für die Bereitstellung aus

* Für einen einzelnen Benutzer: Entfernen Sie den Benutzer aus der Sicherheitsgruppe 

* Für eine Gruppe: Entfernen Sie die Gruppe aus der SSPR-Konfiguration

* Für alle Benutzer: Deaktivieren Sie SSPR für den Azure AD-Mandanten

## <a name="deploy-sspr"></a>Bereitstellen von SSPR

Stellen Sie sicher, dass Sie vor der Bereitstellung folgende Aufgaben ausgeführt haben:

1. Sie haben einen [Kommunikationsplan](#plan-communications) erstellt und mit der Umsetzung begonnen.

1. Sie haben die entsprechenden [Konfigurationseinstellungen](#plan-configuration) festgelegt.

1. Sie haben Benutzer und Gruppen für den [Pilotversuch](#plan-a-pilot) und die Produktionsumgebungen definiert.

1. Sie haben [Konfigurationseinstellungen](#plan-configuration) für Registrierung und Self-Service bestimmt.

1. Sie haben die [Kennwortrückschreibung konfiguriert](#password-writeback), wenn Sie eine Hybridumgebung haben.


**Jetzt können Sie mit der Bereitstellung von SSPR beginnen!**

Unter [Aktivieren der Self-Service-Kennwortzurücksetzung](./tutorial-enable-sspr.md#enable-self-service-password-reset) finden Sie eine vollständige Schritt-für-Schritt-Anleitung zum Konfigurieren der folgenden Bereiche.

1. [Authentifizierungsmethoden](./concept-authentication-methods.md)

1. [Registrierungseinstellungen](./concept-registration-mfa-sspr-combined.md)

1. [Benachrichtigungseinstellungen](#notifications-settings)

1. [Anpassungseinstellungen](./howto-sspr-customization.md)

1. [Lokale Integration](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Aktivieren von SSPR in Windows
Bei Computern, auf denen Windows 7, 8, 8.1 und 10 ausgeführt wird, können Sie [Benutzern das Zurücksetzen ihres Kennworts über den Windows-Anmeldebildschirm ermöglichen](./howto-sspr-windows.md).

## <a name="manage-sspr"></a>Verwalten von SSPR

Azure AD kann durch Überprüfungen und Berichte weitere Informationen zur SSPR-Leistung bereitstellen.

### <a name="password-management-activity-reports"></a>Aktivitätsberichte der Kennwortverwaltung 

Mithilfe von vordefinierten Berichten im Azure-Portal können Sie die SSPR-Leistung messen. Wenn Sie eine ordnungsgemäße Lizenz haben, können Sie auch benutzerdefinierte Abfragen erstellen. Weitere Informationen finden Sie unter [Berichtsoptionen für die Azure AD-Kennwortverwaltung](./howto-sspr-reporting.md).

> [!NOTE]
>  Sie müssen [ein globaler Administrator](../roles/permissions-reference.md) sein und zustimmen, dass diese Daten für Ihr Unternehmen erfasst werden. Als Bestätigung Ihrer Zustimmung müssen Sie die Registerkarte „Berichterstellung“ oder die Überwachungsprotokolle im Azure-Portal mindestens ein Mal aufrufen. Vorher werden die Daten für Ihre Organisation nicht gesammelt.

Überwachungsprotokolle für die Registrierung und die Kennwortzurücksetzung sind 30 Tage lang verfügbar. Wenn die Sicherheitsüberwachung in Ihrem Unternehmen eine längere Aufbewahrungszeit erfordert, müssen die Protokolle exportiert und in einem SIEM-Tool wie etwa [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk oder ArcSight genutzt werden.

![Screenshot der SSPR-Berichterstellung](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Authentifizierungsmethoden: Nutzung und Erkenntnisse

Mithilfe der Funktion [Nutzung und Erkenntnisse](./howto-authentication-methods-usage-insights.md) können Sie besser verstehen, wie Authentifizierungsmethoden für Funktionen wie Azure AD MFA und SSPR in Ihrem Unternehmen funktionieren. Dank dieser Berichtsfunktion kann Ihr Unternehmen erkennen, welche Methoden registriert werden und wie sie verwendet werden.

### <a name="troubleshoot"></a>Problembehandlung

* Siehe [Problembehandlung für die Self-Service-Kennwortzurücksetzung](./troubleshoot-sspr.md) 

* Folgen Sie [Häufig gestellte Fragen zur Kennwortverwaltung](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>Hilfreiche Dokumentation

* [Authentifizierungsmethoden](./concept-authentication-methods.md)

* [So funktioniert's: Azure AD-Self-Service-Kennwortzurücksetzung](./concept-sspr-howitworks.md)

* [Anpassen der Azure AD-Funktionalität für die Self-Service-Kennwortzurücksetzung](./howto-sspr-customization.md)

* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](./concept-sspr-policy.md)

* [Was ist Kennwortrückschreiben?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>Nächste Schritte

* Um mit der Bereitstellung von SSPR zu beginnen, lesen Sie [Aktivieren der Self-Service-Kennwortzurücksetzung in Azure AD](tutorial-enable-sspr.md).

* [Überlegungen zur Implementierung von Azure AD-Kennwortschutz](./concept-password-ban-bad.md)

* [Überlegungen zur Implementierung von Smart Lockout für Azure AD](./howto-password-smart-lockout.md)