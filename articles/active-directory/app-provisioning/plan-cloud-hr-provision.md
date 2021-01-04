---
title: Planen der HR-Cloudanwendung für die Azure Active Directory-Benutzerbereitstellung
description: In diesem Artikel wird der Bereitstellungsprozess für die Integration von HR-Cloudsystemen wie Workday und Sucessfactors mit Azure Active Directory beschrieben. Die Integration von Azure AD in Ihr HR-Cloudsystem führt zu einem vollständigen Identity Lifecycle Management-System.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 64418a727ecb9a300912a4766a9ea2066328ad31
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174899"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planen der HR-Cloudanwendung für die Azure Active Directory-Benutzerbereitstellung

In der Vergangenheit haben sich IT-Mitarbeiter zum Erstellen, Aktualisieren und Löschen von Mitarbeitern auf manuelle Methoden verlassen. Zum Synchronisieren von Mitarbeiterdaten wurden Methoden wie das Hochladen von CSV-Dateien oder benutzerdefinierte Skripts verwendet. Diese Bereitstellungsprozesse sind fehleranfällig, unsicher und schwer zu verwalten.

Zum Verwalten der Identitätszyklen von Mitarbeitern, Anbietern oder vorübergehend Beschäftigten bietet der [Azure Active Directory (Azure AD)-Benutzerbereitstellungsdienst](../app-provisioning/user-provisioning.md) die Integration mit cloudbasierten Anwendungen für das Personalwesen (Human Resources, HR). Als Beispiele sind hier Workday oder SuccessFactors zu nennen.

Azure AD nutzt diese Integration, um die folgenden Workflows für HR-Cloudanwendungen (Apps) zu ermöglichen:

- **Bereitstellen von Benutzern in Azure Active Directory:** Ausgewählte Benutzergruppen aus einer HR-Cloud-App werden in einer oder mehreren Azure Active Directory-Domänen bereitgestellt.
- **Bereitstellen reiner Cloudbenutzer in Azure AD:** In den Fällen, in denen Azure Active Directory nicht verwendet wird, werden Benutzer direkt aus der HR-Cloud-App in Azure AD bereitgestellt.
- **Rückschreiben in die HR-Cloud-App**: E-Mail-Adressen und Benutzernamenattribute werden aus Azure AD zurück in die HR-Cloud-App geschrieben.

> [!NOTE]
> Dieser Bereitstellungsplan zeigt Ihnen, wie Sie die Workflows Ihrer HR-Cloud-App mithilfe der Azure AD-Benutzerbereitstellung bereitstellen können. Informationen zum Implementieren der automatischen Benutzerbereitstellung für SaaS-Apps (Software as a Service) finden Sie unter [Planen einer automatischen Benutzerbereitstellung](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Unterstützte HR-Szenarien

Der Azure AD-Benutzerbereitstellungsdienst ermöglicht die Automatisierung der folgenden HR-basierten Identity Lifecycle Management-Szenarien:

- **Einstellen neuer Mitarbeiter:** Wenn Sie einen neuen Mitarbeiter zur HR-Cloud-App hinzufügen, wird in Azure Active Directory und Azure AD automatisch ein Benutzerkonto mit der Option erstellt, E-Mail-Adress- und Benutzernamenattribute in die HR-Cloud-App rückzuschreiben.
- **Aktualisieren von Mitarbeiterattributen und -profilen:** Wenn Sie in der HR-Cloud-App einen Mitarbeiterdatensatz (z. B. Name, Titel oder Vorgesetzter) aktualisieren, wird das entsprechende Benutzerkonto automatisch in Azure Active Directory und Azure AD aktualisiert.
- **Kündigen von Mitarbeitern:** Wenn Sie das Arbeitsverhältnis eines Mitarbeiters in der HR-Cloud-App beenden, wird das entsprechende Benutzerkonto in Azure Active Directory und Azure AD automatisch deaktiviert.
- **Erneutes Einstellen von Mitarbeitern:** Wenn Sie einen Mitarbeiter in der HR-Cloud-App erneut einstellen, kann sein altes Konto in Azure Active Directory und Azure AD automatisch reaktiviert bzw. erneut bereitgestellt werden.

## <a name="who-is-this-integration-best-suited-for"></a>Für wen ist diese Integration am besten geeignet?

Die Integration der HR-Cloud-App mit der Azure AD-Benutzerbereitstellung ist ideal für Unternehmen geeignet, für die Folgendes gilt:

- Sie wünschen sich eine vorgefertigte, cloudbasierte Lösung für die HR-Benutzerbereitstellung in der Cloud.
- Benutzer müssen direkt aus der HR-Cloud-App in Azure Active Directory oder Azure AD bereitgestellt werden.
- Benutzer müssen mithilfe von Daten bereitgestellt werden, die aus der HR-Cloud-App abgerufen werden.
- Benutzer müssen bei Einstellungen, Wechseln und Kündigungen nur auf Grundlage von Änderungsinformationen, die in der HR-Cloud-App erkannt werden, mit einer oder mehreren Azure Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden.
- Verwenden Sie Microsoft 365 für E-Mails.

## <a name="learn"></a>Lernen

Die Benutzerbereitstellung bildet die Grundlage für eine fortlaufende Identitätsgovernance. Sie verbessert die Qualität von Geschäftsprozessen, die auf autoritativen Identitätsdaten basieren.

### <a name="terms"></a>Begriffe

In diesem Artikel werden die folgenden Begriffe verwendet:

- **Quellsystem**: Das Repository von Benutzern, aus dem die Azure AD-Bereitstellungen erfolgen, zum Beispiel eine HR-Cloud-App wie Workday oder SuccessFactors.
- **Zielsystem**: Das Repository von Benutzern, in das die Azure AD-Bereitstellungen erfolgen, zum Beispiel Active Directory, Azure AD, Microsoft 365 oder andere SaaS-Apps.
- **Prozess für Einstellungen/Wechsel/Kündigungen**: Ein Begriff, der bei Verwendung einer HR-Cloud-App als Datensatzsystem für Neueinstellungen, Versetzungen und Kündigungen verwendet wird. Der Prozess ist abgeschlossen, wenn der Dienst die erforderlichen Attribute für das Zielsystem erfolgreich bereitstellt.

### <a name="key-benefits"></a>Hauptvorteile

Diese Funktion der vom Personalwesen (HR) gesteuerten IT-Bereitstellung bietet erhebliche Geschäftsvorteile, wie nachfolgend aufgeführt:

- **Produktivitätssteigerung:** Sie können jetzt die Zuweisung von Benutzerkonten und Microsoft 365-Lizenzen automatisieren und den Zugriff auf Schlüsselgruppen ermöglichen. Durch die Automatisierung von Zuweisungen haben Neueinstellungen sofortigen Zugriff auf ihre Arbeitstools, und die Produktivität erhöht sich.
- **Risikomanagement:** Sie können die Sicherheit erhöhen, indem Sie Änderungen anhand von Mitarbeiterstatus oder Gruppenmitgliedschaft mit Daten, die aus der HR-Cloud-App einfließen, automatisieren. Die Automatisierung von Änderungen stellt sicher, dass Benutzeridentitäten und der Zugriff auf wichtige Anwendungen automatisch aktualisiert werden, wenn Benutzer innerhalb des Unternehmen wechseln oder es verlassen.
- **Umsetzen von Compliance und Governance**: Azure AD unterstützt native Überwachungsprotokolle für Benutzerbereitstellungsanforderungen, die von Apps aus dem Quell- und Zielsystem erfolgen. Durch die Überwachung können Sie über einen einzigen Bildschirm nachverfolgen, wer Zugriff auf die Apps hat.
- **Kostenmanagement:** Durch Vermeiden von Ineffizienzen und menschlichen Fehlern, die bei einer manuellen Bereitstellung entstehen, reduziert die automatische Benutzerbereitstellung die Kosten. Sie reduziert auch den Bedarf an benutzerdefiniert entwickelten Lösungen für die Benutzerbereitstellung, die im Laufe der Zeit mithilfe älterer und veralteter Plattformen erstellt wurden.

### <a name="licensing"></a>Lizenzierung

Um die HR-Cloud-App für die Integration der Azure AD-Benutzerbereitstellung zu konfigurieren, benötigen Sie eine gültige [Azure AD Premium-Lizenz](https://azure.microsoft.com/pricing/details/active-directory/) und eine Lizenz für die HR-Cloud-App (z. B. Workday oder SuccessFactors).

Sie benötigen zudem eine gültige Abonnementlizenz für Azure AD Premium P1 oder höher für jeden Benutzer, der aus der HR-Cloud-App bezogen und in einer Azure Active Directory- oder Azure AD-Instanz bereitgestellt wird. Eine nicht ausreichende Anzahl von Lizenzen in der HR-Cloud-App kann zu Fehlern bei der Benutzerbereitstellung führen.

### <a name="prerequisites"></a>Voraussetzungen

- Azure AD-[Hybrididentitätsadministrator](../roles/permissions-reference.md#hybrid-identity-administrator) zum Konfigurieren des Bereitstellungs-Agents von Azure AD Connect
- Die Rolle „Azure AD-[Anwendungsadministrator](../roles/permissions-reference.md#application-administrator)“, um die Bereitstellungs-App im Azure-Portal zu konfigurieren
- Eine Test- und Produktionsinstanz der HR-Cloud-App
- Administratorberechtigungen in der HR-Cloud-App zum Erstellen eines Systemintegrationsbenutzers für Testzwecke und Vornehmen von Änderungen zum Testen von Mitarbeiterdaten
- Ein Server mit mindestens Windows Server 2012 und .NET 4.7.1 Runtime zum Hosten des Bereitstellungs-Agents von Azure AD Connect für die Benutzerbereitstellung in Active Directory
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) für die Synchronisierung von Benutzern zwischen Azure Active Directory und Azure AD

### <a name="training-resources"></a>Schulungsressourcen

| **Ressourcen** | **Link und Beschreibung** |
|:-|:-|
| Videos | [Was ist die Benutzerbereitstellung in Azure Active Directory?](https://youtu.be/_ZjARPpI6NI) |
| | [Implementieren der Benutzerbereitstellung in Azure Active Directory](https://youtu.be/pKzyts6kfrw) |
| Tutorials | [Liste der Tutorials zur Integration von SaaS-Anwendungen in Azure AD](../saas-apps/tutorial-list.md) |
| | [Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Häufig gestellte Fragen | [Automatisierte Benutzerbereitstellung](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Bereitstellung aus Workday in Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Lösungsarchitektur

Im folgenden Beispiel wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für häufige Hybridumgebungen beschrieben, die Folgendes umfasst:

- **Autoritativer HR-Datenflow aus der HR-Cloud-App zu Azure Active Directory.** In diesem Flow wird das HR-Ereignis (Prozess für Einstellungen/Wechsel/Kündigungen) im Mandanten der HR-Cloud-App eingeleitet. Der Azure AD-Bereitstellungsdienst und der Azure AD Connect-Bereitstellungs-Agent stellen die Benutzerdaten aus dem HR-Cloud-App-Mandanten in Azure Active Directory bereit. Abhängig vom Ereignis kann dies dann in Azure Active Directory zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
- **Synchronisieren mit Azure AD und Rückschreiben von E-Mail-Adresse und Benutzername aus dem lokalen Azure Active Directory in die HR-Cloud-App.** Nach dem Aktualisieren der Konten in Azure Active Directory werden sie über Azure AD Connect mit Azure AD synchronisiert. E-Mail-Adressen und Benutzernamenattribute können an den Mandanten der HR-Cloud-App rückgeschrieben werden.

![Workflowdiagramm](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Beschreibung des Workflows

Die wichtigsten Schritte gehen aus dem folgenden Diagramm hervor:  

1. Das **HR-Team** führt die Transaktionen im Mandanten der HR-Cloud-App durch.
2. Der **Azure AD-Bereitstellungsdienst** führt die geplanten Zyklen vom Mandanten der HR-Cloud-App aus und erkennt Änderungen, die zur Synchronisierung mit Azure Active Directory verarbeitet werden müssen.
3. Der **Azure AD-Bereitstellungsdienst** ruft den Azure AD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, welche die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das Azure Active Directory-Konto enthält.
4. Der **Azure AD Connect-Bereitstellungs-Agent** verwendet ein Dienstkonto zum Verwalten der Azure Active Directory-Kontodaten.
5. **Azure AD Connect** führt eine Delta [synchronisierung](../hybrid/how-to-connect-sync-whatis.md) aus, um per Pullvorgang Updates in Azure Active Directory abzurufen.
6. Die **Azure Active Directory**-Updates werden mit Azure AD synchronisiert.
7. Der **Azure AD-Bereitstellungsdienst** schreibt E-Mail-Attribut und Benutzernamen aus Azure AD in den Mandanten der HR-Cloud-App zurück.

## <a name="plan-the-deployment-project"></a>Planen des Bereitstellungsprojekts

Berücksichtigen Sie die Anforderungen Ihrer Organisation, während Sie die Strategie für diese Bereitstellung in Ihrer Umgebung festlegen.

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Fehler bei Technologieprojekten sind in der Regel auf nicht erfüllte Erwartungen in den Bereichen Auswirkung, Ergebnisse und Zuständigkeiten zurückzuführen. Diese können Sie vermeiden, indem Sie [sicherstellen, dass Sie die richtigen Beteiligten](../fundamentals/active-directory-deployment-plans.md) hinzuziehen. Achten Sie auch darauf, die Rolle der Beteiligten am Projekt präzise zu definieren. Dokumentieren Sie die Beteiligten und deren jeweilige Projektbeiträge und Verantwortlichkeiten.

Beziehen Sie einen Mitarbeiter der Personalabteilung ein, der Angaben zu vorhandenen HR-Geschäftsprozessen und zur Mitarbeiteridentität sowie zu Anforderungen an die Auftragsdatenverarbeitung machen kann.

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Teilen Sie Ihren Benutzern proaktiv mit, wann und in welcher Form sich die Umgebung ändert. Lassen Sie Ihre Benutzer wissen, wie sie Unterstützung erhalten, wenn Probleme auftreten.

### <a name="plan-a-pilot"></a>Planen eines Pilotprojekts

Die Integration von HR-Geschäftsprozessen und Identitätsworkflows aus der HR-Cloud-App in Zielsysteme erfordert ein erhebliches Maß an Datenüberprüfung, Datentransformation, Datenbereinigung und End-to-End-Tests, bevor Sie die Lösung in der Produktionsumgebung bereitstellen können.

Führen Sie die anfängliche Konfiguration in einer [Pilotumgebung](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) aus, bevor sie für alle Benutzer in der Produktionsumgebung skaliert wird.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Auswählen von HR-Cloudbereitstellungsconnector-Apps

Um Azure AD-Bereitstellungsworkflows zwischen der HR-Cloud-App und Azure Active Directory zu vereinfachen, können Sie über den Azure AD-App-Katalog mehrere Bereitstellungsconnector-Apps hinzufügen:

- **Benutzerbereitstellung von der HR-Cloud-App in Azure Active Directory**: Diese Bereitstellungsconnector-App vereinfacht die Bereitstellung von Benutzerkonten von der HR-Cloud-App in einer einzelnen Azure Active Directory-Domäne. Wenn Sie über mehrere Domänen verfügen, können Sie eine Instanz dieser App aus dem Azure AD-App-Katalog für jede Active Directory-Domäne hinzufügen, in der die Bereitstellung erfolgen soll.
- **Benutzerbereitstellung von der HR-Cloud-App in Azure AD**: Obwohl Azure AD Connect das Tool ist, das Sie zum Synchronisieren von Azure Active Directory-Benutzern mit Azure AD verwenden sollten, kann auch diese Bereitstellungsconnector-App verwendet werden, um die Bereitstellung reiner Cloudbenutzer von der HR-Cloud-App in einem einzelnen Azure AD-Mandanten zu vereinfachen.
- **Rückschreiben in die HR-Cloud-App**: Diese Bereitstellungsconnector-App ermöglicht das Rückschreiben der E-Mail-Adressen des Benutzers von Azure AD in die HR-Cloud-App.

In der folgenden Abbildung sind z. B. die Workday-Connector-Apps aufgelistet, die im Azure AD-App-Katalog verfügbar sind.

![Azure Active Directory-Portal – App-Katalog](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Flussdiagramm zum Entscheidungsprozess

Verwenden Sie das folgende Flussdiagramm für den Entscheidungsprozess, um zu ermitteln, welche HR-Cloudbereitstellungs-Apps für Ihr Szenario relevant sind.

![Flussdiagramm zum Entscheidungsprozess](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Entwerfen der Bereitstellungstopologie des Azure AD Connect-Bereitstellungs-Agents

Die Integration der Bereitstellung zwischen der HR-Cloud-App und Azure Active Directory setzt die folgenden vier Komponenten voraus:

- Mandant der HR-Cloud-App
- Bereitstellungsconnector-App
- Azure AD Connect-Bereitstellungs-Agent
- Azure Active Directory-Domäne

Die Bereitstellungstopologie des Azure AD Connect-Bereitstellungs-Agents hängt von der Anzahl der Mandanten der HR-Cloud-App und der untergeordneten Azure Active Directory-Domänen ab, die Sie integrieren möchten. Wenn Sie über mehrere Azure Active Directory-Domänen verfügen, hängt es davon ab, ob die Azure Active Directory-Domänen zusammenhängend oder [nicht zusammenhängend](/windows-server/identity/ad-ds/plan/disjoint-namespace) sind.

Wählen Sie auf der Grundlage Ihrer Entscheidung eines der Bereitstellungsszenarien aus:

- Einzelner HR-Cloud-App-Mandant -> ausgerichtet auf eine oder mehrere untergeordnete Azure Active Directory-Domänen in einer vertrauenswürdigen Gesamtstruktur
- Einzelner HR-Cloud-App-Mandant -> ausgerichtet auf mehrere untergeordnete Domänen in einer nicht zusammenhängenden Azure Active Directory-Gesamtstruktur

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Einzelner HR-Cloud-App-Mandant -> ausgerichtet auf eine oder mehrere untergeordnete Azure Active Directory-Domänen in einer vertrauenswürdigen Gesamtstruktur

Folgende Produktionskonfigurationen werden empfohlen:

|Anforderung|Empfehlung|
|:-|:-|
|Anzahl der bereitzustellenden Azure AD Connect-Bereitstellungs-Agents|Zwei (für Hochverfügbarkeit und Failover)
|Anzahl der zu konfigurierenden Bereitstellungsconnector-Apps|Eine App pro untergeordneter Domäne|
|Serverhost für Azure AD Connect-Bereitstellungs-Agent|Windows 2012 R2 oder höher mit „Sichtverbindung“ zur Geolocation von Azure Active Directory-Domänencontrollern</br>Koexistenz mit dem Azure AD Connect-Dienst ist möglich|

![Flow zu lokalen Agents](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Einzelner HR-Cloud-App-Mandant -> ausgerichtet auf mehrere untergeordnete Domänen in einer nicht zusammenhängenden Azure Active Directory-Gesamtstruktur

Dieses Szenario umfasst die Bereitstellung von Benutzern von der HR-Cloud-App in Domänen in nicht zusammenhängenden Azure Active Directory-Gesamtstrukturen.

Folgende Produktionskonfigurationen werden empfohlen:

|Anforderung|Empfehlung|
|:-|:-|
|Anzahl der lokal bereitzustellenden Azure AD Connect-Bereitstellungs-Agents|Zwei pro nicht zusammenhängender Azure Active Directory-Gesamtstruktur|
|Anzahl der zu konfigurierenden Bereitstellungsconnector-Apps|Eine App pro untergeordneter Domäne|
|Serverhost für Azure AD Connect-Bereitstellungs-Agent|Windows 2012 R2 oder höher mit „Sichtverbindung“ zur Geolocation von Azure Active Directory-Domänencontrollern</br>Koexistenz mit dem Azure AD Connect-Dienst ist möglich|

![Einzelner HR-Cloud-App-Mandant, nicht zusammenhängende Azure Active Directory-Gesamtstruktur](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Anforderungen des Azure AD Connect-Bereitstellungs-Agents

Die Lösung für die Benutzerbereitstellung von der HR-Cloud-App in Azure Active Directory erfordert das Bereitstellen von mindestens einem Azure AD Connect-Bereitstellungs-Agent auf Servern unter Windows 2012 R2 oder höher. Die Server müssen über mindestens 4 GB RAM und .NET 4.7.1 Runtime verfügen. Stellen Sie sicher, dass der Hostserver Netzwerkzugriff auf die Azure Active Directory-Zieldomäne hat.

Um die lokale Umgebung vorzubereiten, registriert der Konfigurationsassistent des Azure AD Connect-Bereitstellungs-Agents den Agent bei Ihrem Azure AD-Mandanten, [öffnet Ports](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [gestattet den Zugriff auf URLs](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls) und unterstützt die [HTTPS-Proxy-Konfiguration für den ausgehenden Datenverkehr](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Der Bereitstellungs-Agent verwendet ein Dienstkonto für die Kommunikation mit den Azure Active Directory-Domänen. Erstellen Sie vor der Installation des Agents in „Azure Active Directory-Benutzer und -Computer“ ein Dienstkonto, das folgende Anforderungen erfüllt:

- Ein nicht ablaufendes Kennwort
- Delegierte Steuerungsberechtigungen zum Lesen, Erstellen, Löschen und Verwalten von Benutzerkonten

Sie können Domänencontroller auswählen, die Bereitstellungsanforderungen verarbeiten sollen. Wenn Sie über mehrere geografisch verteilte Domänencontroller verfügen, installieren Sie den Bereitstellungs-Agent am selben Standort wie Ihre bevorzugten Domänencontroller. Damit steigern Sie Zuverlässigkeit und Leistungsfähigkeit der End-to-End-Lösung.

Für die Hochverfügbarkeit können Sie auch mehrere Azure AD Connect-Bereitstellungs-Agents bereitstellen. Registrieren Sie die Agents so, dass sie für den gleichen Satz von lokalen Azure Active Directory-Domänen zuständig sind.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planen von Bereichsfiltern und Attributzuordnung

Wenn Sie die Bereitstellung von der HR-Cloud-App in Azure Active Directory oder Azure AD aktivieren, werden im Azure-Portal die Attributwerte durch Attributzuordnung gesteuert.

### <a name="define-scoping-filters"></a>Definieren von Bereichsfiltern

Verwenden Sie [Bereichsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md), um die attributbasierten Regeln zu definieren, die bestimmen, welche Benutzer von der HR-Cloud-App in Azure Active Directory oder Azure AD bereitgestellt werden sollen.

Wenn Sie den Einstellungsprozess starten, erfassen Sie die folgenden Anforderungen:

- Wird die HR-Cloud-App sowohl für Mitarbeiter als auch für vorübergehend Beschäftigte verwendet?
- Planen Sie, die HR-Cloud-App für die Azure AD-Benutzerbereitstellung zu verwenden, um sowohl Mitarbeiter als auch vorübergehend Beschäftigte zu verwalten?
- Planen Sie den Rollout der HR-Cloud-App für die Azure AD-Benutzerbereitstellung nur für eine Teilmenge der Benutzer der HR-Cloud-App? Zum Beispiel nur für Mitarbeiter?

Abhängig von Ihren Anforderungen können Sie bei der Konfiguration der Attributzuordnungen das Feld **Quellobjektbereich** so festlegen, dass Sie auswählen können, welche Benutzergruppen in der HR-Cloud-App für die Bereitstellung in Azure Active Directory in Frage kommen. Weitere Informationen zu häufig verwendeten Bereichsfiltern finden Sie im Tutorial zur HR-Cloud-App.

### <a name="determine-matching-attributes"></a>Festlegen von übereinstimmenden Attributen

Mit der Bereitstellung erhalten Sie die Möglichkeit, bestehende Konten zwischen Quell- und Zielsystem abzugleichen. Wenn Sie die HR-Cloud-App in den Azure AD-Bereitstellungsdienst integrieren, können Sie die [Attributzuordnung konfigurieren](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) und bestimmen, welche Benutzerdaten von der HR-Cloud-App in Azure Active Directory oder Azure AD fließen sollen.

Wenn Sie den Einstellungsprozess starten, erfassen Sie die folgenden Anforderungen:

- Welche eindeutige ID in dieser HR-Cloud-App soll zum Identifizieren der einzelnen Benutzer verwendet werden?
- Wie gehen Sie aus der Sicht des Identitätslebenszyklus mit Wiedereinstellungen um? Behalten Wiedereinstellungen ihre alte Mitarbeiter-ID?
- Verarbeiten Sie vordatierte Einstellungen, und erstellen Sie bereits im voraus Azure Active Directory-Konten für diese Personen?
- Wie gehen Sie in Hinblick auf den Identitätslebenszyklus mit der Umwandlung von einem Mitarbeiter zu einem vorübergehend Beschäftigten bzw. umgekehrt um?
- Behalten umgewandelte Benutzer ihre alten Azure Active Directory-Konten oder erhalten sie neue?

Je nach Anforderungen unterstützt Azure AD durch die Bereitstellung konstanter Werte oder das [Schreiben von Ausdrücken für Attributzuordnungen](../app-provisioning/functions-for-customizing-application-data.md) die direkte Attributzuordnung (Attribut-Attribut). Mithilfe dieser Flexibilität können Sie genau steuern, was im Attribut der Ziel-App eingetragen wird. Mit der [Microsoft Graph-API](../app-provisioning/export-import-provisioning-configuration.md) und Graph Explorer können Sie die Attributzuordnungen und das Schema Ihrer Benutzerbereitstellung in eine JSON-Datei exportieren und wieder in Azure AD importieren.

Standardmäßig wird das Attribut in der HR-Cloud-App, das die eindeutige Mitarbeiter-ID darstellt, als übereinstimmendes Attribut verwendet, *das dem eindeutigen Attribut in Azure Active Directory zugeordnet wird*. Im Szenario mit der Workday-App wird z. B. das **Workday**-Attribut **WorkerID** dem Azure Active Directory-Attribut **employeeID** zugeordnet.

Sie können mehrere übereinstimmende Attribute festlegen und geeignete Prioritäten zuweisen. Sie werden nach der Rangfolge für den Abgleich ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

Sie können auch die [Standardattributzuordnungen anpassen](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types) und z. B. vorhandene Attributzuordnungen ändern oder löschen. Sie können auch je nach geschäftlichem Bedarf neue Attributzuordnungen erstellen. Weitere Informationen finden Sie im Tutorial zur HR-Cloud-App (z. B. [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)) in der Liste mit den benutzerdefinierten Attributen für die Zuordnung.

### <a name="determine-user-account-status"></a>Bestimmen des Benutzerkontostatus

Standardmäßig ordnet die Bereitstellungsconnector-App den HR-Benutzerprofilstatus dem Benutzerkontostatus in Azure Active Directory oder Azure AD zu, um zu bestimmen, ob das Benutzerkonto aktiviert oder deaktiviert werden soll.

Erfassen Sie die folgenden Anforderungen, wenn Sie den Prozess für Einstellungen/Kündigungen starten.

| Prozess | Requirements (Anforderungen) |
| - | - |
| **Einstellungen** | Wie gehen Sie aus der Sicht des Identitätslebenszyklus mit Wiedereinstellungen um? Behalten Wiedereinstellungen ihre alte Mitarbeiter-ID? |
| | Verarbeiten Sie vordatierte Einstellungen, und erstellen Sie bereits im voraus Azure Active Directory-Konten für diese Personen? Werden diese Konten im aktivierten oder deaktivierten Zustand erstellt? |
| | Wie gehen Sie in Hinblick auf den Identitätslebenszyklus mit der Umwandlung von einem Mitarbeiter zu einem vorübergehend Beschäftigten bzw. umgekehrt um? |
| | Behalten umgewandelte Benutzer ihre alten Azure Active Directory-Konten oder erhalten sie neue? |
| **Kündigungen** | Werden Kündigungen von Mitarbeitern und vorübergehend Beschäftigten in Azure Active Directory unterschiedlich behandelt? |
| | Welche Stichtage werden für die Verarbeitung der Benutzerkündigung berücksichtigt? |
| | Wie wirken sich Umwandlungen von Mitarbeitern und vorübergehend Beschäftigten auf bestehende Azure Active Directory-Konten aus? |
| | Wie verarbeiten Sie den Vorgang „Widerruf“ in Azure Active Directory? Widerrufvorgänge müssen verarbeitet werden, wenn im Rahmen des Einstellungsprozesses in Azure Active Directory vordatierte Einstellungen erstellt werden. |

Abhängig von Ihren Anforderungen können Sie die Zuordnungslogik mithilfe von [Azure AD-Ausdrücken](../app-provisioning/functions-for-customizing-application-data.md) so anpassen, dass das Azure Active Directory-Konto basierend auf einer Kombination von Datenpunkten aktiviert oder deaktiviert wird.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Zuordnen von HR-Cloud-App- zu Azure Active Directory-Benutzerattributen

Jede HR-Cloud-App ist werksseitig mit standardmäßigen Zuordnungen von HR-Cloud-App zu Azure Active Directory ausgestattet.

Erfassen Sie die folgenden Anforderungen, wenn Sie den Prozess für Einstellungen/Wechsel/Kündigungen starten.

| Prozess | Requirements (Anforderungen) |
| - | - |
| **Einstellungen** | Verläuft der Azure Active Directory-Kontoerstellungsprozess manuell, automatisch oder teilweise automatisch? |
| | Planen Sie, benutzerdefinierte Attribute von der HR-Cloud-App an Azure Active Directory weiterzugeben? |
| **Wechsel** | Welche Attribute möchten Sie verarbeiten, wenn in der HR-Cloud-App ein Wechselvorgang stattfindet? |
| | Führen Sie zum Zeitpunkt der Benutzeraktualisierung bestimmte Attributprüfungen durch? Wenn ja, geben Sie Details an. |
| **Kündigungen** | Werden Kündigungen von Mitarbeitern und vorübergehend Beschäftigten in Azure Active Directory unterschiedlich behandelt? |
| | Welche Stichtage werden für die Verarbeitung der Benutzerkündigung berücksichtigt? |
| | Wie wirken sich Umwandlungen von Mitarbeitern und vorübergehend Beschäftigten auf bestehende Azure Active Directory-Konten aus? |

Je nach Ihren Anforderungen können Sie die Zuordnungen an Ihre Integrationsziele anpassen. Weitere Informationen finden Sie im Tutorial zur jeweiligen HR-Cloud-App (z. B. [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)) in der Liste mit den benutzerdefinierten Attributen für die Zuordnung.

### <a name="generate-a-unique-attribute-value"></a>Generieren von eindeutigen Attributwerten

Wenn Sie den Einstellungsprozess starten und Attribute wie „CN“, „samAccountName“ und „UPN“ festlegen, die eindeutige Einschränkungen aufweisen, müssen Sie möglicherweise eindeutige Attributwerte generieren.

Die Azure AD-Funktion [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) wertet jede Regel aus. Anschließend wird der Wert überprüft, der generiert wurde, um die Eindeutigkeit im Zielsystem sicherzustellen. Ein Beispiel finden Sie unter [Generieren eines eindeutigen Werts für das Attribut „userPrincipalName (UPN)“](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Diese Funktion wird derzeit nur für die Benutzerbereitstellung von Workday in Azure Active Directory unterstützt. Sie kann nicht für andere Bereitstellungs-Apps verwendet werden.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurieren der Containerzuweisung für Organisationseinheiten in Azure Active Directory

Häufig müssen Azure Active Directory-Benutzerkonten auf Basis von Geschäftseinheiten, Standorten und Abteilungen in Container platziert werden. Wenn Sie einen Wechselprozess initiieren und damit eine Änderung der Aufsichtsorganisation verbunden ist, müssen Sie den Benutzer möglicherweise in Azure Active Directory von einer Organisationseinheit in eine andere verschieben.

Verwenden Sie die Funktion [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch), um die Geschäftslogik für die Zuordnung der Organisationseinheit zu konfigurieren und sie dem Azure Active Directory-Attribut **parentDistinguishedName** zuzuordnen.

Wenn Sie z. B. Benutzer in der Organisationseinheit auf der Grundlage des Personalabteilungsattributs **Municipality** (Gemeinde) erstellen möchten, können Sie den folgenden Ausdruck verwenden:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

Wenn der Wert für „Municipality“ Dallas, Austin, Seattle oder London entspricht, wird das Benutzerkonto in der entsprechenden Organisationseinheit erstellt. Wenn es keine Übereinstimmung gibt, wird das Konto in der Standardorganisationseinheit erstellt.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planen Sie die Zustellung von Kennwörtern für neue Benutzerkonten.

Wenn Sie den Einstellungsprozess starten, müssen Sie ein temporäres Kennwort für neue Benutzerkonten festlegen und zustellen. Mit der Benutzerbereitstellung von der HR-Cloud-App in Azure AD können Sie am ersten Tag für den Benutzer die SSPR-Funktion ([Self-Service-Kennwortzurücksetzung](../authentication/tutorial-enable-sspr.md)) von Azure AD einführen.

SSPR ist für IT-Administratoren eine einfache Möglichkeit, Benutzern das Zurücksetzen ihrer Kennwörter oder das Entsperren ihrer Konten zu ermöglichen. Sie können das Attribut **Mobiltelefonnummer** von der HR-Cloud-App in Azure Active Directory bereitstellen und mit Azure AD synchronisieren. Sobald Azure AD das Attribut **Mobiltelefonnummer** enthält, können Sie SSPR für das Benutzerkonto aktivieren. Am ersten Tag kann der neue Benutzer dann die registrierte und überprüfte Mobiltelefonnummer zur Authentifizierung verwenden.

## <a name="plan-for-initial-cycle"></a>Planen des erster Zyklus

Bei der ersten Ausführung des Azure AD-Bereitstellungsdiensts führt dieser einen [ersten Zyklus](../app-provisioning/how-provisioning-works.md#initial-cycle) für die HR-Cloud-App durch, um eine Momentaufnahme aller Benutzerobjekte in der HR-Cloud-App zu erstellen. Die für die ersten Zyklen erforderliche Zeit ist direkt davon abhängig, wie viele Benutzer im Quellsystem vorhanden sind. Der erste Zyklus für einige HR-Cloud-Apps mit über 100.000 Benutzern kann sehr lange dauern.

Führen Sie **bei großen HR-Cloud-App-Mandanten (über 30.000 Benutzer)** den ersten Zyklus schrittweise aus. Starten Sie die inkrementellen Updates erst dann, wenn Sie überprüft haben, ob in Azure Active Directory die richtigen Attribute für verschiedene Benutzerbereitstellungsszenarien festgelegt sind. Halten Sie die folgende Reihenfolge ein.

1. Führen Sie den ersten Zyklus nur für eine begrenzte Anzahl von Benutzern durch, indem Sie den [Bereichsfilter](#plan-scoping-filters-and-attribute-mapping) festlegen.
2. Überprüfen Sie die Azure Active Directory-Kontobereitstellung und die für die Benutzer festgelegten Attributwerte, die für die erste Ausführung ausgewählt wurden. Wenn das Ergebnis Ihren Erwartungen entspricht, erweitern Sie den Bereichsfilter, um schrittweise weitere Benutzer einzubeziehen, und überprüfen Sie dann die Ergebnisse für die zweite Ausführung.

Wenn Sie mit den Ergebnissen des ersten Zyklus für Testbenutzer zufrieden sind, starten Sie die [inkrementellen Updates](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planen von Tests und der Sicherheit

Stellen Sie anhand von Tests in jeder Phase der Bereitstellung (vom ersten Pilotprojekt bis zur Aktivierung der Benutzerbereitstellung) sicher, ob die Ergebnisse erwartungsgemäß ausfallen, und überwachen Sie die Bereitstellungszyklen.

### <a name="plan-testing"></a>Planen von Tests

Überprüfen Sie nach der Konfiguration der Benutzerbereitstellung von der HR-Cloud-App in Azure AD anhand von Testfällen, ob diese Lösung den Anforderungen Ihres Unternehmens entspricht.

|Szenarien|Erwartete Ergebnisse|
|:-|:-|
|Einstellung neuer Mitarbeiter in der HR-Cloud-App| – Das Benutzerkonto wird in Azure Active Directory bereitgestellt.</br>– Benutzer können sich bei Azure Active Directory-Domänen-Apps anmelden und die gewünschten Aktionen durchführen.</br>– Bei Konfiguration der Azure AD Connect-Synchronisierung wird das Benutzerkonto auch in Azure AD erstellt.
|Der Benutzer wird in der HR-Cloud-App beendet|– Das Benutzerkonto wird in Azure Active Directory deaktiviert.</br>– Der Benutzer kann sich nicht bei den durch Azure Active Directory geschützten Unternehmens-Apps anmelden.
|Die Aufsichtsorganisation des Benutzers wird in der HR-Cloud-App aktualisiert|Anhand der Attributzuordnung wechselt das Benutzerkonto in Azure Active Directory von einer Organisationseinheit zu einer anderen.|
|Die Personalabteilung (HR) aktualisiert den Vorgesetzten des Benutzers in der HR-Cloud-App|Das Feld für den Vorgesetzten in Azure Active Directory wird aktualisiert und gibt den Namen des neuen Vorgesetzten wieder.|
|Die Personalabteilung stellt einen Mitarbeiter in einer neuen Position ein.|Das Verhalten hängt davon ab, wie die HR-Cloud-App konfiguriert ist, um Mitarbeiter-IDs zu generieren:</br>– Wenn die alte Mitarbeiter-ID für Wiedereinstellungen wiederverwendet wird, aktiviert der Connector das vorhandene Azure Active Directory-Konto für den Benutzer.</br>– Wenn der wieder eingestellte Mitarbeiter eine neue Mitarbeiter-ID erhält, erstellt der Connector ein neues Azure Active Directory-Konto für den Benutzer.|
|Die Personalabteilung wandelt den Mitarbeiter in einen vorübergehend Beschäftigten um oder umgekehrt|Für die neue Persona wird ein neues Azure Active Directory-Konto erstellt, und das alte Konto wird mit Inkrafttreten der Umwandlung deaktiviert.|

Bestimmen Sie anhand der obigen Ergebnisse, wie Sie die automatische Implementierung Ihrer Benutzerbereitstellung in der Produktionsumgebung basierend auf dem festgelegten Zeitrahmen umsetzen können.

> [!TIP]
> Verwenden Sie Verfahren wie Datenreduzierung und Datenbereinigung, wenn die Testumgebung mit Produktionsdaten aktualisiert wird, um vertrauliche Daten mit personenbezogenen Informationen zu entfernen oder zu maskieren und so die Datenschutz- und Sicherheitsstandards einzuhalten. 

### <a name="plan-security"></a>Planen der Sicherheit

Es ist üblich, dass im Rahmen der Bereitstellung eines neuen Diensts eine Sicherheitsüberprüfung erforderlich ist. Wenn eine Sicherheitsüberprüfung erforderlich ist oder noch nicht ausgeführt wurde, überprüfen Sie die zahlreichen Azure AD-[Whitepapers](https://www.microsoft.com/download/details.aspx?id=36391), die eine Übersicht über die Identität als Dienst bereitstellen.

### <a name="plan-rollback"></a>Planen eines Rollbacks

Es kann passieren, dass die Implementierung der HR-Cloud-App-Benutzerbereitstellung in der Produktionsumgebung nicht wie gewünscht funktioniert. In diesem Fall können Ihnen die folgenden Rollbackschritte dabei helfen, einen früheren bekannten fehlerfreien Zustand wiederherzustellen.

1. Überprüfen Sie den [Zusammenfassungsbericht für die Bereitstellung](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) und die [Bereitstellungsprotokolle](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview), um zu ermitteln, welche fehlerhaften Vorgänge für die betroffenen Benutzer oder Gruppen durchgeführt wurden. Weitere Informationen zum Zusammenfassungsbericht für die Bereitstellung und zu den Bereitstellungsprotokollen finden Sie unter [Verwalten der HR-Cloud-App-Benutzerbereitstellung](#manage-your-configuration).
2. Der letzte bekannte fehlerfreie Zustand der betroffenen Benutzer oder Gruppen kann anhand der Überwachungsprotokolle für die Bereitstellung oder durch Überprüfen der Zielsysteme (Azure AD oder Azure Active Directory) ermittelt werden.
3. Arbeiten Sie mit dem App-Besitzer zusammen, um die betroffenen Benutzer oder Gruppen direkt in der App mit den letzten bekannten fehlerfreien Statuswerten zu aktualisieren.

## <a name="deploy-the-cloud-hr-app"></a>Bereitstellen der HR-Cloud-App

Wählen Sie die HR-Cloud-App aus, die Ihren Lösungsanforderungen entspricht.

**Workday**: Informationen zum Importieren von Workerprofilen von Workday in Azure Active Directory and Azure AD finden Sie im [Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Optional können Sie eine Rückschreibung von E-Mail-Adresse, Benutzername und Telefonnummer in Workday vornehmen.

**SAP SuccessFactors**: Informationen zum Importieren von Workerprofilen von SuccessFactors in Azure Active Directory and Azure AD finden Sie im [Tutorial: Konfigurieren von SAP SuccessFactors für die automatische Benutzerbereitstellung](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Optional können Sie eine Rückschreibung von E-Mail-Adresse und Benutzername in SuccessFactors vornehmen.

## <a name="manage-your-configuration"></a>Verwalten Ihrer Konfiguration

Azure AD kann anhand von Überwachungsprotokollen und Berichten zusätzliche Einblicke in die Nutzung der Benutzerbereitstellung und die Betriebsintegrität Ihrer Organisation gewähren.

### <a name="gain-insights-from-reports-and-logs"></a>Gewinnen von Erkenntnissen aus Berichten und Protokollen

Nach einem erfolgreichen [Startzyklus](../app-provisioning/how-provisioning-works.md#initial-cycle) führt der Azure AD-Bereitstellungsdienst so lange aufeinanderfolgende inkrementelle Updates in den Intervallen aus, die in den Tutorials für die einzelnen Apps beschrieben sind, bis eines der folgenden Ereignisse eintritt:

- Der Dienst wird manuell beendet. Über das [Azure-Portal](https://portal.azure.com/) oder mithilfe des entsprechenden [Microsoft Graph-API](/graph/api/resources/synchronization-overview)-Befehls wird ein neuer Startzyklus ausgelöst.
- Aufgrund einer Änderung bei den Attributzuordnungen oder Bereichsfiltern wird ein neuer Startzyklus ausgelöst.
- Aufgrund einer hohen Fehlerquote wird der Bereitstellungsprozess unter Quarantäne gestellt. Er bleibt mehr als vier Wochen in Quarantäne und wird dann automatisch deaktiviert.

Informationen zum Überprüfen dieser Ereignisse und aller anderen vom Bereitstellungsdienst ausgeführten Aktivitäten finden Sie unter [Erfahren Sie, wie Sie Protokolle überprüfen und Bereitstellungsaktivitätsberichte abrufen](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

Alle vom Bereitstellungsdienst ausgeführten Aktivitäten werden in den Azure AD-Überwachungsprotokollen erfasst. Sie können Azure AD-Überwachungsprotokolle zur weiteren Analyse an Azure Monitor-Protokolle weiterleiten. Mit Azure Monitor-Protokollen (auch als Log Analytics-Arbeitsbereich bezeichnet) können Sie Daten abfragen und nach Ereignisse suchen, Trends analysieren und datenquellenübergreifende Korrelationen vornehmen. Sehen Sie sich dieses [Video](https://youtu.be/MP5IaCTwkQg) an, um die Vorteile der Verwendung von Azure Monitor-Protokollen für Azure AD-Protokolle in praktischen Benutzerszenarien kennenzulernen.

Installieren Sie die [Log Analytics-Ansichten für Azure AD-Aktivitätsprotokolle](../reports-monitoring/howto-install-use-log-analytics-views.md), um Zugriff auf [vordefinierte Berichte](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) zu Bereitstellungsereignissen in Ihrer Umgebung zu erhalten.

Weitere Informationen finden Sie unter [Analysieren der Azure AD-Aktivitätsprotokolle mit Azure Monitor-Protokollen](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Verwalten persönlicher Daten

Der auf dem Windows-Server installierte Azure AD Connect-Bereitstellungs-Agent erstellt Protokolle im Windows-Ereignisprotokoll, die abhängig von den Attributzuordnungen zwischen Ihrer HR-Cloud-App und Azure Active Directory persönliche Daten enthalten können. Legen Sie zur Erfüllung der Benutzerdatenschutzverpflichtungen eine geplante Windows-Aufgabe fest, um das Ereignisprotokoll zu löschen und sicherzustellen, dass keine Daten länger als 48 Stunden gespeichert werden.

Der Azure AD-Bereitstellungsdienst generiert weder Berichte noch Analysen und bietet auch keine Einblicke über den Zeitraum von 30 Tagen hinaus, da der Dienst Daten nicht länger als 30 Tage speichert, verarbeitet oder aufbewahrt.

### <a name="troubleshoot"></a>Problembehandlung

In den folgenden Artikeln finden Sie Informationen zum Beheben von Problemen, die bei der Bereitstellung auftreten können:

- [Problem beim Konfigurieren der Benutzerbereitstellung für eine Azure AD-Kataloganwendung](application-provisioning-config-problem.md)
- [Synchronisieren eines Attributs aus lokalen Active Directory Domain Services mit Azure AD für die Bereitstellung einer Anwendung](user-provisioning-sync-attributes-for-mapping.md)
- [Problem saving administrator credentials while configuring user provisioning to an Azure Active Directory Gallery application](./user-provisioning.md) (Probleme beim Speichern von Administratoranmeldeinformationen während des Konfigurierens der Benutzerbereitstellung in einer Anwendung aus dem Azure Active Directory-Katalog)
- [Es werden keine Benutzer für eine Azure AD-Kataloganwendung bereitgestellt](application-provisioning-config-problem-no-users-provisioned.md)
- [Der falsche Satz von Benutzern wird für eine Azure AD-Kataloganwendung bereitgestellt](../manage-apps/add-application-portal-assign-users.md)
- [Einrichten der Windows-Ereignisanzeige für die Problembehandlung bei Agents](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Einrichten von Azure-Portal-Überwachungsprotokollen für die Problembehandlung bei einem Dienst](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Grundlegendes zu Protokollen für Erstellungsvorgänge für ein AD-Benutzerkonto](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Grundlegendes zu Protokollen für Aktualisierungsvorgänge für Manager](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Beheben von häufig auftretenden Fehlern](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Nächste Schritte

- [Schreiben von Ausdrücken für Attributzuordnungen](functions-for-customizing-application-data.md)
- [Azure AD synchronization API overview](/graph/api/resources/synchronization-overview) (Azure AD-Synchronisierung – API-Übersicht)
- [Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs](skip-out-of-scope-deletions.md)
- [Azure AD Connect-Bereitstellungs-Agent: Verlauf der Versionsveröffentlichungen](provisioning-agent-release-version-history.md).