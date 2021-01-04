---
title: Versionshinweise für Azure Security Center
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: 3272212a857f24916acce7da5a4ee31561ec0c5a
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922934"
---
# <a name="whats-new-in-azure-security-center"></a>Neuerungen in Azure Security Center

Azure Security Center befindet sich in der aktiven Entwicklung und wird ständig verbessert. Damit Sie bezüglich der aktuellen Entwicklungen immer auf dem neuesten Stand sind, enthält diese Seite Informationen zu neuen Features, Fehlerbehebungen und veralteten Funktionen.

Es ist ratsam, diese Seite regelmäßig zu besuchen, da sie immer wieder aktualisiert wird. 

Weitere Informationen zu den *geplanten* Änderungen, die demnächst im Security Center anstehen, finden Sie unter [Wichtige bevorstehende Änderungen in Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Elemente, die älter als sechs Monate sind, finden Sie im [Archiv zu den Neuerungen in Azure Security Center](release-notes-archive.md).


## <a name="december-2020"></a>Dezember 2020

Zu den Updates im Dezember gehören:

- [Azure Defender für SQL Server-Instanzen auf Computern ist allgemein verfügbar.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender für SQL-Unterstützung für dedizierte SQL-Pools von Azure Synapse Analytics ist allgemein verfügbar.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globale Administratoren können sich jetzt selbst Berechtigungen auf Mandantenebene erteilen.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Zwei neue Azure Defender-Pläne: Azure Defender für DNS und Azure Defender für Resource Manager (in der Vorschauphase)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)

### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender für SQL Server-Instanzen auf Computern ist allgemein verfügbar.

Azure Security Center bietet zwei Azure Defender-Pläne für SQL Server:

- **Azure Defender für Azure SQL-Datenbankserver** – schützt Ihre nativen Azure SQL Server. 
- **Azure Defender für SQL Server auf Computern** – erweitert denselben Schutz auf Ihre SQL Server-Instanzen in Hybrid-, Multicloud- und lokalen Umgebungen.

Mit dieser Ankündigung schützt **Azure Defender für SQL** jetzt Ihre Datenbanken und deren Daten überall, wo sie sich befinden.

Azure Defender für SQL umfasst Funktionen zur Sicherheitsrisikobewertung. Das Tool zur Sicherheitsrisikobewertung umfasst die folgenden erweiterten Funktionen:

- **Baselinekonfiguration** (neu!), um die Ergebnisse von Sicherheitsrisikoscans auf intelligente Weise auf jene einzuschränken, die echte Sicherheitsprobleme darstellen könnten. Nach dem Festlegen des Baseline-Sicherheitsstatus meldet die das Tool zur Sicherheitsrisikobewertung nur noch Abweichungen von diesem Baselinestatus. Wenn Ergebnisse in nachfolgenden Überprüfungen mit der Baseline übereinstimmen, wird dies als Bestehen gewertet. Auf diese Weise können Sie und ihre Analysten Ihre Aufmerksamkeit darauf konzentrieren, wo sie gefordert ist.
- **Ausführliche Informationen**, die Ihnen helfen, die erkannten Ergebnisse und deren Zusammenhang mit ihren Ressourcen zu *verstehen*.
- **Wiederherstellungsskripts**  zum Verringern identifizierter Risiken.

Weitere Informationen zu [Azure Defender für SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender für SQL-Unterstützung für dedizierte SQL-Pools von Azure Synapse Analytics ist allgemein verfügbar.

Azure Synapse Analytics (früher SQL DW) ist ein Analysedienst, der Data Warehousing für Unternehmen mit Big Data-Analysen vereint. Dedizierte SQL-Pools sind die Data Warehousing-Funktionen für Unternehmen von Azure Synapse. Weitere Informationen finden Sie unter [Was ist Azure Synapse Analytics (früher SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender für SQL schützt Ihre dedizierten SQL-Pools wie folgt:

- **Erweiterter Schutz vor Bedrohungen** zur Erkennung von Bedrohungen und Angriffen 
- **Funktionen zur Sicherheitsrisikobewertung** zum Identifizieren und Beheben von Sicherheitsfehlkonfigurationen

Die Azure Defender für SQL-Unterstützung für Azure Synapse Analytics-SQL-Pools wird in Azure Security Center automatisch dem Paket „Azure SQL-Datenbanken“ hinzugefügt. Sie finden die entsprechende neue Registerkarte „Azure Defender für SQL“ im Azure-Portal auf der Seite „Synapse-Arbeitsbereich“.

Weitere Informationen zu [Azure Defender für SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globale Administratoren können sich jetzt selbst Berechtigungen auf Mandantenebene erteilen.

Ein Benutzer mit der Azure Active Directory-Rolle **Globaler Administrator** hat möglicherweise mandantenweite Aufgaben, aber keine Azure-Berechtigungen zum Anzeigen der organisationsweiten Informationen in Azure Security Center. 

Unter [Erteilen mandantenweiter Berechtigungen für sich selbst](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself) erfahren Sie, wie Sie sich selbst Berechtigungen auf Mandantenebene erteilen.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Zwei neue Azure Defender-Pläne: Azure Defender für DNS und Azure Defender für Resource Manager (in der Vorschauphase)

Wir haben zwei neue cloudnative und breit gefächerte Bedrohungsschutzfunktionen für Ihre Azure-Umgebung hinzugefügt.

Diese neuen Schutzmaßnahmen sorgen für eine erhebliche Verbesserung der Resilienz gegenüber Angriffen von Bedrohungsakteuren sowie für eine deutliche Erhöhung der Anzahl von Azure-Ressourcen, die durch Azure Defender geschützt werden.

- **Azure Defender für Resource Manager**: Überwacht automatisch alle in Ihrer Organisation ausgeführten Ressourcenverwaltungsvorgänge. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md).
    - [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
    - [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender für DNS**: Überwacht kontinuierlich alle DNS-Abfragen Ihrer Azure-Ressourcen. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für DNS](defender-for-dns-usage.md)
    - [Warnungen für DNS](alerts-reference.md#alerts-dns)


## <a name="november-2020"></a>November 2020

Updates im November:

- [29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 in das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen aufgenommen](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Die Empfehlungsliste beinhaltet nun Filter.](#recommendations-list-now-includes-filters)
- [Die Umgebung für die automatische Bereitstellung wurde verbessert und erweitert.](#auto-provisioning-experience-improved-and-expanded)
- [Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar.](#secure-score-is-now-available-in-continuous-export-preview)
- [Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [Die Seite „Richtlinienverwaltung“ im Azure-Portal zeigt jetzt den Zuweisungsstatus von Standardrichtlinien an.](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz Azure-spezifischer Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. [Weitere Informationen zum Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md)

Die folgenden 29 Vorschauempfehlungen wurden zu Security Center hinzugefügt, um die Abdeckung dieses Vergleichstests zu erhöhen.

Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md).

| Sicherheitskontrolle                     | Neue Empfehlungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Verschlüsseln von Daten während der Übertragung              | – Erzwingen einer SSL-Verbindung sollte für PostgreSQL-Datenbankserver aktiviert sein<br>– Erzwingen einer SSL-Verbindung sollte für MySQL-Datenbankserver aktiviert sein<br>– TLS sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– TLS sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– TLS sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– FTPS sollte in API-App erforderlich sein<br>– FTPS sollte in Funktions-App erforderlich sein<br>– FTPS sollte in Web-App erforderlich sein                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Zugriff und Berechtigungen verwalten        | – Web-Apps sollten ein SSL-Zertifikat für alle eingehenden Anforderungen anfordern<br>– API-App sollte verwaltete Identität verwenden<br>– Funktions-App sollte verwaltete Identität verwenden<br>– Web-App sollte verwaltete Identität verwenden                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Nicht autorisierten Netzwerkzugriff einschränken | – Privater Endpunkt sollte für PostgreSQL-Server aktiviert sein<br>– Privater Endpunkt sollte für MariaDB-Server aktiviert sein<br>– Privater Endpunkt sollte für MySQL-Server aktiviert sein                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Überwachung und Protokollierung aktivieren          | - In App Services müssen Diagnoseprotokolle aktiviert sein                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Bewährte Sicherheitsmethoden implementieren    | – Azure Backup sollte für virtuelle Computer aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for MariaDB aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for MySQL aktiviert sein<br>– Georedundante Sicherung sollte für Azure Database for PostgreSQL aktiviert sein<br>– PHP sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– PHP sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– Java sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre API-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre Funktions-App auf die aktuelle Version aktualisiert werden<br>– Python sollte für Ihre Web-App auf die aktuelle Version aktualisiert werden<br>– Die Aufbewahrung der Überprüfung für SQL-Server sollte auf mindestens 90 Tage festgelegt sein |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Verwandte Links:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](../security/benchmarks/introduction.md)
- [Weitere Informationen zu API-Apps in Azure](../app-service/app-service-web-tutorial-rest-api.md)
- [Weitere Informationen zu Funktions-Apps in Azure](../azure-functions/functions-overview.md)
- [Weitere Informationen zu Web-Apps in Azure](../app-service/overview.md)
- [Weitere Informationen zu Azure Database for MariaDB](../mariadb/overview.md)
- [Weitere Informationen zu Azure Database for MySQL](../mysql/overview.md)
- [Weitere Informationen zu Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 in das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen aufgenommen

Der Standard „NIST SP 800-171 R2“ ist jetzt als integrierte Initiative zur Verwendung mit dem Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen verfügbar. Die Zuordnungen für die Steuerelemente werden in [Details zur integrierten Initiative zur Einhaltung der gesetzlichen Bestimmungen gemäß NIST SP 800-171 R2](../governance/policy/samples/nist-sp-800-171-r2.md) beschrieben. 

Um den Standard auf Ihre Abonnements anzuwenden und Ihren Compliancestatus kontinuierlich zu überwachen, verwenden Sie die Anweisungen unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="Standard „NIST SP 800-171 R2“ im Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen":::

Weitere Informationen zu diesem Compliancestandard finden Sie unter [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Die Empfehlungsliste beinhaltet nun Filter.

Sie können die Liste der Sicherheitsempfehlungen nun nach verschiedenen Kriterien filtern. Im folgenden Beispiel wurde die Empfehlungsliste zur Anzeige von Empfehlungen gefiltert, für die Folgendes gilt:

- Sie sind **allgemein verfügbar** (also nicht in der Vorschauphase).
- Sie gelten für **Speicherkonten**.
- Sie unterstützen eine **schnelle Problembehebung**.

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filter für die Empfehlungsliste":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Die Umgebung für die automatische Bereitstellung wurde verbessert und erweitert.

Mit der Funktion zur automatischen Bereitstellung können Sie den Verwaltungsaufwand verringern, indem Sie die erforderlichen Erweiterungen auf neuen und vorhandenen virtuellen Azure-Computern installieren, damit diese von den Security Center-Schutzmöglichkeiten profitieren können. 

Da Azure Security Center wächst, wurden mehr Erweiterungen entwickelt, und Security Center kann eine umfangreichere Liste von Ressourcentypen überwachen. Die Tools für die automatische Bereitstellung wurden nun erweitert, um durch die Nutzung der Funktionen von Azure Policy zusätzliche Erweiterungen und Ressourcentypen zu unterstützen.

Sie können jetzt die automatische Bereitstellung der folgenden Komponenten konfigurieren:

- Log Analytics-Agent
- (Neu) Azure Policy-Add-On für Kubernetes
- (Neu) Microsoft Dependency-Agent

Weitere Informationen finden Sie unter [Automatische Bereitstellung von Agents und Erweiterungen aus Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar.

Mit dem fortlaufenden Export der Sicherheitsbewertung können Sie Änderungen an Ihrer Bewertung in Echtzeit an Azure Event Hubs oder an einen Log Analytics-Arbeitsbereich streamen. Diese Funktion ermöglicht Folgendes:

- Nachverfolgen Ihrer Sicherheitsbewertung im Laufe der Zeit mit dynamischen Berichten
- Exportieren von Sicherheitsbewertungsdaten an Azure Sentinel (oder an eine beliebige andere SIEM-Lösung)
- Integrieren dieser Daten in beliebige Prozesse, die in Ihrer Organisation ggf. bereits zur Überwachung der Sicherheitsbewertung verwendet werden

Weitere Informationen zum fortlaufenden Exportieren von Security Center-Daten finden Sie [hier](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.

Die Empfehlung **Auf Ihren Computern sollten Systemupdates installiert werden** wurde erweitert. Die neue Version enthält Unterempfehlungen für jedes fehlende Update und bietet folgende Verbesserungen:

- Eine neu gestaltete Umgebung auf den Azure Security Center-Seiten des Azure-Portals. Die Seite mit den Empfehlungsdetails für **Auf Ihren Computern sollten Systemupdates installiert werden** enthält die unten gezeigte Liste der Ergebnisse. Wenn Sie ein einzelnes Ergebnis auswählen, wird die Detailansicht mit einem Link zu den Informationen zur Risikominderung und einer Liste der betroffenen Ressourcen geöffnet.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öffnen einer der Unterempfehlungen in der Benutzeroberfläche des Portals für die aktualisierte Empfehlung":::

- Erweiterte Daten für die Empfehlung aus Azure Resource Graph (ARG). ARG ist ein Azure-Dienst zur effizienten Untersuchung von Ressourcen. Sie können ARG verwenden, um über einen bestimmten Satz von Abonnements Abfragen im großen Stil durchzuführen und Ihre Umgebung so effektiv zu verwalten. 

    Für Azure Security Center können Sie ARG und die [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) verwenden, um eine Vielzahl von Daten zum Sicherheitsstatus abzufragen.

    Zuvor galt: Wenn Sie diese Empfehlung in ARG abgefragt haben, bestand die einzige verfügbare Information darin, dass die Empfehlung auf einem Computer behandelt werden muss. Die folgende Abfrage der aktualisierten Version gibt die einzelnen fehlenden Systemupdates zurück (gruppiert nach Computer):

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Die Seite „Richtlinienverwaltung“ im Azure-Portal zeigt jetzt den Zuweisungsstatus von Standardrichtlinien an.

Auf der Seite **Sicherheitsrichtlinie** von Security Center im Azure-Portal sehen Sie nun, ob ihren Abonnements die standardmäßige Security Center-Richtlinie zugewiesen ist.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Seite „Richtlinienverwaltung“, die die Standardrichtlinienzuweisung anzeigt":::

## <a name="october-2020"></a>Oktober 2020

Updates im Oktober:
- [Sicherheitsrisikobewertung für lokale und Multi-Cloud-Computer (Vorschau)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Empfehlung für Azure Firewall hinzugefügt (Vorschau)](#azure-firewall-recommendation-added-preview)
- [Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ wurde mit einer schnellen Problembehebung aktualisiert](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Das Dashboard zur Einhaltung gesetzlicher Bestimmungen enthält jetzt die Option, Standards zu entfernen](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Microsoft.Security/securityStatuses-Tabelle aus Azure Resource Graph (ARG) entfernt](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Sicherheitsrisikobewertung für lokale und Multi-Cloud-Computer (Vorschau)

Der in [Azure Defender für Server](defender-for-servers-introduction.md) integrierte Scanner für die Sicherheitsrisikobewertung (von Qualys) überprüft jetzt Server mit Azure Arc-Unterstützung.

Wenn Sie Azure Arc auf Ihren Nicht-Azure-Computern aktiviert haben, bietet Security Center eine manuelle und skalierbare Bereitstellung des integrierten Sicherheitsrisikoscanners auf diesen Computern.

Mit diesem Update können Sie die Leistungsfähigkeit von **Azure Defender für Server** nutzen, um Ihr Programm zur Verwaltung von Sicherheitsrisiken auf allen Azure- und Nicht-Azure-Ressourcen zu konsolidieren.

Hauptfunktionen:

- Überwachen des Bereitstellungsstatus des Scanners für die Sicherheitsrisikobewertung auf Azure Arc-Computern
- Bereitstellen des integrierten Agents für die Sicherheitsrisikobewertung auf ungeschützten Azure Arc-Computern unter Windows und Linux (manuell und skalierbar)
- Empfangen und Analysieren ermittelter Sicherheitsrisiken von bereitgestellten Agents (manuell und skalierbar)
- Einheitliche Benutzeroberfläche für Azure-VMs und Azure Arc-Computer

[Erfahren Sie mehr über das Bereitstellen des integrierten Sicherheitsrisikoscanners auf Ihren Hybridcomputern](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Erfahren Sie mehr über Server mit Azure Arc-Unterstützung](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Empfehlung für Azure Firewall hinzugefügt (Vorschau)

Es wurde eine neue Empfehlung zum Schützen aller virtuellen Netzwerke mit Azure Firewall hinzugefügt.

In der Empfehlung **Virtuelle Netzwerke müssen durch Azure Firewall geschützt werden** wird Ihnen geraten, mithilfe der Azure-Firewall den Zugriff auf Ihre virtuellen Netzwerke einzuschränken und potenzielle Bedrohungen zu verhindern.

Erfahren Sie mehr über [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ wurde mit einer schnellen Problembehebung aktualisiert

Für die Empfehlung **Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden** gibt es jetzt eine Option zur schnellen Problembehebung.

Weitere Informationen zu dieser Empfehlung und allen anderen Security Center-Empfehlungen finden Sie unter [Sicherheitsempfehlungen: Referenzhandbuch](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Die Empfehlung „Für Kubernetes-Dienste sollten autorisierte IP-Adressbereiche definiert werden“ mit einer Option zur schnellen Problembehebung":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Das Dashboard zur Einhaltung gesetzlicher Bestimmungen enthält jetzt die Option, Standards zu entfernen

Das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen bietet Erkenntnisse zu Ihrem Compliancestatus basierend auf der Erfüllung bestimmter Compliancevorgaben und -anforderungen.

Das Dashboard enthält einen Standardsatz gesetzlicher Standards. Falls einer der angegebenen Standards für Ihre Organisation nicht relevant ist, ist es nun problemlos möglich, ihn einfach aus der Benutzeroberfläche für ein Abonnement zu entfernen. Standards können nur auf der Ebene des *Abonnements* entfernt werden, nicht der für den Verwaltungsgruppenbereich.

Weitere Informationen finden Sie unter [Entfernen eines Standards aus Ihrem Dashboard](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft.Security/securityStatuses-Tabelle aus Azure Resource Graph (ARG) entfernt

Azure Resource Graph ist ein Dienst in Azure, der eine effiziente Ressourcenuntersuchung mit der Fähigkeit bereitstellt, übergreifend für eine bestimmte Menge von Abonnements nach Bedarf Abfragen durchzuführen, sodass Sie Ihre Umgebung effektiv beherrschen können. 

Für Azure Security Center können Sie ARG und die [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/) verwenden, um eine Vielzahl von Daten zum Sicherheitsstatus abzufragen. Zum Beispiel:

- Asset Inventory nutzt (ARG)
- Wir haben eine ARG-Beispielabfrage zum [Identifizieren von Konten ohne aktivierte mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled) dokumentiert.

In ARG gibt es Datentabellen, die Sie in Ihren Abfragen verwenden können.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph-Explorer und die verfügbaren Tabellen":::

> [!TIP]
> In der ARG-Dokumentation sind alle verfügbaren Tabellen unter [Azure Resource Graph-Tabelle und Ressourcentypreferenz](../governance/resource-graph/reference/supported-tables-resources.md) aufgelistet.

Bei diesem Update wurde die **Microsoft.Security/securityStatuses**-Tabelle entfernt. Die securityStatuses-API ist weiterhin verfügbar.

Zum Datenersatz kann die Microsoft.Security/Assessments-Tabelle verwendet werden.

Der Hauptunterschied zwischen Microsoft.Security/securityStatuses und Microsoft.Security/Assessments besteht darin, dass Erstere die Aggregation von Bewertungen zeigt, während in der Zweiten jeweils ein einzelner Datensatz enthalten ist.

Beispielsweise gibt Microsoft.Security/securityStatuses ein Ergebnis mit einem Array von zwei Richtlinienbewertungen (policyAssessments) zurück:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Microsoft.Security/Assessments enthält hingegen einen Datensatz für jede Richtlinienbewertung:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Beispiel für das Konvertieren einer vorhandenen ARG-Abfrage mit securityStatuses für die Verwendung der Assessments-Tabelle:**

Abfrage, die auf SecurityStatuses verweist:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Ersatzabfrage für die Assessments-Tabelle:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Weitere Informationen finden Sie unter den folgenden Links:
- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)
- [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>September 2020

Updates im September:
- [Security Center erhält ein neues Aussehen!](#security-center-gets-a-new-look)
- [Azure Defender veröffentlicht](#azure-defender-released)
- [Azure Defender für Key Vault ist allgemein verfügbar.](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender für Storage ist für Files und ADLS Gen2 allgemein verfügbar.](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Asset Inventory-Tools sind jetzt allgemein verfügbar.](#asset-inventory-tools-are-now-generally-available)
- [Deaktivieren der Erkennung eines bestimmten Sicherheitsrisikos bei Scans von Containerregistrierungen und virtuellen Computern](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Ausschließen einer Ressource aus einer Empfehlung](#exempt-a-resource-from-a-recommendation)
- [AWS- und GCP-Connectors in Security Center für Szenarien mit mehreren Clouds](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Empfehlungsbündel für Kubernetes-Workloadschutz](#kubernetes-workload-protection-recommendation-bundle)
- [Ergebnisse der Sicherheitsrisikobewertung sind jetzt im fortlaufenden Export verfügbar](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Verhindern von sicherheitsbezogenen Fehlkonfigurationen durch Erzwingen von Empfehlungen beim Erstellen neuer Ressourcen](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Empfehlungen für Netzwerksicherheitsgruppen verbessert](#network-security-group-recommendations-improved)
- [Veraltete AKS-Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-Mail-Benachrichtigungen von Azure Security Center verbessert](#email-notifications-from-azure-security-center-improved)
- [Sicherheitsbewertung ohne Vorschauempfehlungen](#secure-score-doesnt-include-preview-recommendations)
- [Empfehlungen jetzt mit Schweregradindikator und Aktualisierungsintervall](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center erhält ein neues Aussehen!

Wir haben eine aktualisierte Benutzeroberfläche für die Portalseiten von Security Center veröffentlicht. Die neuen Seiten enthalten eine neue Übersicht sowie Dashboards für Sicherheitsbewertung, Ressourcenbestand und Azure Defender.

Die neu gestaltete Übersicht verfügt jetzt über eine Kachel für den Zugriff auf die Sicherheitsbewertung, den Ressourcenbestand und Azure Defender-Dashboards. Außerdem bietet eine neue Kachel eine Verknüpfung mit dem Compliance-Dashboard.

Erfahren Sie mehr über die [Übersicht](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender veröffentlicht

**Azure Defender** ist die in Security Center integrierte Cloud Workload Protection Platform (CWPP), die einen erweiterten, intelligenten Schutz Ihrer Azure- und Hybridworkloads bietet. Sie ersetzt den Standard-Tarif von Security Center. 

Wenn Sie Azure Defender über den Bereich **Preise und Einstellungen** von Azure Security Center aktivieren, werden alle folgenden Defender-Pläne aktiviert und bieten umfassende Schutzmechanismen für die Compute-, Daten- und Dienstebenen Ihrer Umgebung:

- [Azure Defender für Server](defender-for-servers-introduction.md)
- [Azure Defender für App Service](defender-for-app-service-introduction.md)
- [Azure Defender für Storage](defender-for-storage-introduction.md)
- [Azure Defender für SQL](defender-for-sql-introduction.md)
- [Azure Defender für Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)

Jeder dieser Pläne wird in der Security Center-Dokumentation einzeln erläutert.

Über ein dediziertes Dashboard bietet Azure Defender Sicherheitswarnungen und erweiterten Bedrohungsschutz für virtuelle Computer, SQL-Datenbanken, Container, Webanwendungen, Ihr Netzwerk u. v. m.

Weitere Informationen zu [Azure Defender](azure-defender.md).

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender für Key Vault ist allgemein verfügbar

Der Azure Key Vault-Clouddienst schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter). 

**Azure Defender für Key Vault** bietet in Azure nativen erweiterten Bedrohungsschutz für Azure Key Vault und dadurch eine zusätzliche Ebene der Sicherheitsintelligenz. Durch die Erweiterung schützt Azure Defender für Key Vault daher viele der Ressourcen, die von Ihren Key Vault-Konten abhängig sind.

Der optionale Plan ist nun allgemein verfügbar. Dieses Feature wurde in der Vorschau als „Advanced Threat Protection für Azure Key Vault“ bezeichnet.

Außerdem enthalten die Key Vault-Seiten im Azure-Portal nun eine dedizierte Seite **Sicherheit** für Empfehlungen und Warnungen von **Security Center**.

Weitere Informationen finden Sie unter [Azure Defender für Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender für Storage ist für Files und ADLS Gen2 allgemein verfügbar 

**Azure Defender für Storage** erkennt potenziell schädliche Aktivitäten in Ihren Azure Storage-Konten. Ihre Daten können geschützt werden, und zwar unabhängig davon, ob Sie als Blobcontainer, Dateifreigaben oder Data Lakes gespeichert werden.

Die Unterstützung für [Azure Files](../storage/files/storage-files-introduction.md) und [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) ist jetzt allgemein verfügbar.

Ab dem 1. Oktober 2020 beginnen wir damit, den Schutz von Ressourcen für diese Dienste abzurechnen.

Weitere Informationen finden Sie unter [Azure Defender für Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Asset Inventory-Tools sind jetzt allgemein verfügbar

Auf der Seite „Ressourcenbestand“ von Azure Security Center können Sie auf einer Seite den gesamten Sicherheitsstatus der Ressourcen anzeigen, die Sie mit Azure Security Center verbunden haben.

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können.

Wenn eine Ressource ausstehende Empfehlungen hat, werden diese im Inventar angezeigt.

Weitere Informationen finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Deaktivieren der Erkennung eines bestimmten Sicherheitsrisikos bei Scans von Containerregistrierungen und virtuellen Computern

Azure Defender enthält Sicherheitsrisikoscanner, mit denen Sie Images in Azure Container Registry und auf Ihren virtuellen Computern scannen können.

Wenn in Ihrer Organisation eine Suche ignoriert werden muss, anstatt sie zu beheben, können Sie sie optional deaktivieren. Deaktivierte Ergebnisse haben keine Auswirkung auf Ihre Sicherheitsbewertung und erzeugen kein unerwünschtes Rauschen.

Wenn eine Suche mit den in Ihren Deaktivierungsregeln definierten Kriterien übereinstimmt, wird sie nicht in der Liste der Ergebnisse angezeigt.

Diese Option ist auf den Detailseiten der Empfehlungen verfügbar:

- **Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden**
- **Sicherheitsrisiken für VMs müssen behoben werden**

Weitere Informationen finden Sie unter [Deaktivieren bestimmter Ergebnisse für Containerimages](defender-for-container-registries-usage.md#disable-specific-findings-preview) und [Deaktivieren bestimmter Ergebnisse für virtuelle Computer](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Ausschließen einer Ressource aus einer Empfehlung

Gelegentlich wird eine Ressource in Bezug auf eine bestimmte Empfehlung als fehlerhaft aufgeführt (und damit Ihre Sicherheitsbewertung gesenkt), obwohl Sie anderer Ansicht sind. Unter Umständen wurde dafür eine Lösungsmaßnahme mit einem Prozess durchgeführt, der von Security Center nicht nachverfolgt wird. Es kann auch sein, dass Ihre Organisation die Entscheidung getroffen hat, das Risiko für die entsprechende Ressource zu akzeptieren. 

In diesen Fällen können Sie eine Ausnahmeregel erstellen und sicherstellen, dass die Ressource in Zukunft nicht mehr in der Liste mit den fehlerhaften Ressourcen enthalten ist. Diese Regeln können dokumentierte Begründungen enthalten, wie unten beschrieben.

Weitere Informationen finden Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- und GCP-Connectors in Security Center für Szenarien mit mehreren Clouds

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt nun Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Beim Onboarding Ihrer AWS- und GCP-Konten in Security Center werden AWS Security Hub, GCP Security Command und Azure Security Center integriert. 

Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen Ihren AWS-Konten und Azure Security Center](quickstart-onboard-aws.md) und [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Empfehlungsbündel für Kubernetes-Workloadschutz

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, fügt Security Center Härtungsempfehlungen auf Kubernetes-Ebene hinzu, einschließlich Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Wenn Sie das Azure Policy-Add-On für Kubernetes in Ihrem AKS-Cluster installiert haben, wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden überwacht, bevor sie im Cluster persistent gespeichert wird. Anschließend können Sie das Erzwingen der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden.

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Ergebnisse der Sicherheitsrisikobewertung sind jetzt im fortlaufenden Export verfügbar

Verwenden Sie den fortlaufenden Export, um Warnungen und Empfehlungen in Echtzeit an Azure Event Hubs, Log Analytics-Arbeitsbereiche oder Azure Monitor zu streamen. Von dort aus können Sie diese Daten in SIEM-Lösungen integrieren (z. B. Azure Sentinel, Power BI, Azure Data Explorer usw.).

Die integrierten Tools für die Sicherheitsrisikobewertung von Security Center geben Ergebnisse zu Ihren Ressourcen als handlungsrelevante Empfehlungen innerhalb einer übergeordneten Empfehlung zurück, z. B. als „Sicherheitsrisiken für VMs müssen behoben werden“. 

Die sicherheitsrelevanten Ergebnisse sind jetzt über den fortlaufenden Export verfügbar, wenn Sie Empfehlungen auswählen und die Option **Sicherheitsrelevante Ergebnisse einbeziehen** aktivieren.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Umschalter „Sicherheitsrelevante Ergebnisse einbeziehen“ in der Konfiguration für den fortlaufenden Export" :::

Verwandte Seiten:

- [Integrierte Security Center-Lösung zur Sicherheitsrisikobewertung für virtuelle Computer](deploy-vulnerability-assessment-vm.md)
- [Integrierte Security Center-Lösung zur Sicherheitsrisikobewertung für Azure Container Registry-Images](defender-for-container-registries-usage.md)
- [Fortlaufendem Export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Verhindern von sicherheitsbezogenen Fehlkonfigurationen durch Erzwingen von Empfehlungen beim Erstellen neuer Ressourcen

Sicherheitsbezogene Fehlkonfigurationen sind eine häufige Ursache für Sicherheitsincidents. Security Center verfügt jetzt über ein Feature zum *Verhindern* von Fehlkonfigurationen neuer Ressourcen für bestimmte Empfehlungen. 

Dieses Feature kann dazu beitragen, dass Ihre Workloads geschützt sind und Ihre Sicherheitsbewertung stabil bleibt.

Für die Erzwingung einer sicheren Konfiguration basierend auf einer bestimmten Empfehlung gibt es zwei Modi:

- Mit dem Effekt **Deny** (Ablehnen) von Azure Policy können Sie verhindern, dass fehlerhafte Ressourcen erstellt werden.

- Mit der Option **Erzwingen** können Sie den Effekt **DeployIfNotExist** von Azure Policy nutzen und nicht konforme Ressourcen bei der Erstellung automatisch korrigieren.
 
Diese sind für ausgewählte Sicherheitsempfehlungen oben auf der Seite mit den Ressourcendetails verfügbar.

Weitere Informationen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Empfehlungen für Netzwerksicherheitsgruppen verbessert

Die folgenden Sicherheitsempfehlungen im Zusammenhang mit Netzwerksicherheitsgruppen wurden verbessert, um einige Arten von False Positives zu verringern.

- Alle Netzwerkports sollten auf NSGs eingeschränkt werden, die ihrer VM zugeordnet sind.
- Verwaltungsports sollten auf Ihren virtuellen Computern geschlossen werden.
- Virtuelle Computer mit Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.
- Subnetze sollten einer Netzwerksicherheitsgruppe zugeordnet werden


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Veraltete AKS-Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“

Die Vorschauempfehlung „Für Kubernetes Service müssen Podsicherheitsrichtlinien definiert werden“ ist veraltet. Eine Beschreibung finden Sie in der Dokumentation zu [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

Das Feature Podsicherheitsrichtlinie (Vorschauversion) wird als veraltet eingestuft und steht nach dem 15. Oktober 2020 zugunsten von Azure Policy für AKS nicht mehr zur Verfügung.

Wenn die Podsicherheitsrichtlinie (Vorschauversion) veraltet ist, müssen Sie das Feature für alle vorhandenen Cluster deaktivieren, die das veraltete Feature verwenden, um zukünftige Clusterupgrades ausführen und weiterhin Azure-Support erhalten zu können.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-Mail-Benachrichtigungen von Azure Security Center verbessert

Die folgenden Bereiche von E-Mails zu Sicherheitswarnungen wurden verbessert: 

- Möglichkeit zum Senden von E-Mail-Benachrichtigungen zu Warnungen für alle Schweregrade hinzugefügt
- Möglichkeit zum Benachrichtigen von Benutzern mit unterschiedlichen Azure-Rollen für das Abonnement hinzugefügt
- Wir benachrichtigen standardmäßig Abonnementbesitzer bei Warnungen mit hohem Schweregrad (bei denen es sich mit hohen Wahrscheinlichkeit um echte Verstöße handelt).
- Das Feld für die Telefonnummer wurde von der Konfigurationsseite für E-Mail-Benachrichtigungen entfernt.

Weitere Informationen finden Sie unter [Einrichten von E-Mail-Benachrichtigungen für Sicherheitswarnungen](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Sicherheitsbewertung ohne Vorschauempfehlungen 

Security Center führt eine ständige Bewertung Ihrer Ressourcen, Abonnements und Organisation in Bezug auf Sicherheitsprobleme durch. Anschließend werden alle Ergebnisse in einer einzigen Bewertung zusammengefasst, sodass Sie auf einen Blick Ihre aktuelle Sicherheitssituation erkennen können: je höher die Bewertung, desto geringer das ermittelte Risiko.

Wenn neue Bedrohungen erkannt werden, werden neue Sicherheitsempfehlungen in Security Center über neue Empfehlungen verfügbar gemacht. Um unerwartete Änderungen an Ihrer Sicherheitsbewertung zu vermeiden und eine Toleranzperiode zu gewähren, in der Sie neue Empfehlungen erkunden können, bevor diese sich auf Ihre Bewertungen auswirken, werden Empfehlungen, die als **Vorschau** gekennzeichnet sind, nicht mehr in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Sie sollten nach Möglichkeit weiterhin korrigiert werden, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen.

Außerdem führen Empfehlungen in der **Vorschauphase** nicht dazu, dass eine Ressource als „Fehlerhaft“ gekennzeichnet wird.

Beispiel für eine Vorschauempfehlung:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Empfehlung mit dem Flag „Vorschau“":::

[Weitere Informationen zur Sicherheitsbewertung.](secure-score-security-controls.md)


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Empfehlungen jetzt mit Schweregradindikator und Aktualisierungsintervall

Die Detailseite für Empfehlungen enthält jetzt einen Indikator für das Aktualisierungsintervall (sofern relevant) und eine deutliche Anzeige des Schweregrads der Empfehlung.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Empfehlungsseite mit Aktualität und Schweregrad":::



## <a name="august-2020"></a>August 2020

Updates im August:

- [Ressourcenbestand – leistungsstarke neue Ansicht des Sicherheitsstatus ihrer Ressourcen](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Unterstützung für Azure Active Directory-Sicherheitsstandards (für die mehrstufige Authentifizierung) hinzugefügt](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Empfehlung zu Dienstprinzipale hinzugefügt](#service-principals-recommendation-added)
- [Sicherheitsrisikobewertung bei virtuellen Computern: Empfehlungen und Richtlinien wurden konsolidiert](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Der Initiative ASC_default wurden neue AKS-Sicherheitsrichtlinien hinzugefügt – nur für Kunden der privaten Vorschau.](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Ressourcenbestand – leistungsstarke neue Ansicht des Sicherheitsstatus ihrer Ressourcen

Der Ressourcenbestand in Security Center (derzeit in der Vorschauphase) bietet eine Möglichkeit, den Sicherheitsstatus der Ressourcen anzuzeigen, die Sie mit Security Center verbunden haben.

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können. Wenn eine Ressource ausstehende Empfehlungen hat, werden diese im Inventar angezeigt.

Sie können die Ansicht und ihre Filter verwenden, um Ihre Daten zum Sicherheitsstatus zu untersuchen und auf der Grundlage der Ergebnisse weitere Maßnahmen zu ergreifen.

Weitere Informationen: [Ressourcenbestand](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Unterstützung für Azure Active Directory-Sicherheitsstandards (für die mehrstufige Authentifizierung) hinzugefügt

Azure Security Center bietet vollständige Unterstützung für [Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), die kostenlosen Schutzfunktionen von Microsoft für die Identitätssicherheit.

Die Sicherheitsstandards bieten vorkonfigurierte Identitätssicherheitseinstellungen, um Ihre Organisation vor häufigen identitätsbezogenen Angriffen zu schützen. Sicherheitsstandards schützen bereits mehr als 5 Millionen Mandanten insgesamt; 50.000 Mandanten werden zusätzlich durch Azure Security Center geschützt.

Azure Security Center stellt jetzt eine Sicherheitsempfehlung bereit, sobald ein Azure-Abonnement ohne aktivierte Sicherheitsstandards identifiziert wird. Bisher empfahl Azure Security Center die Aktivierung der mehrstufigen Authentifizierung (MFA) mittels bedingtem Zugriff, was Teil der Lizenz von Azure Active Directory (AD) Premium ist. Für Kunden, die Azure AD Free verwenden, empfehlen wir nun, die Sicherheitsstandards zu aktivieren. 

Unser Ziel ist es, mehr Kunden zu ermutigen, ihre Cloudumgebungen mit MFA abzusichern und damit eines der höchsten Risiken zu mindern, das sich auch am stärksten auf ihre [Sicherheitsbewertung](secure-score-security-controls.md) auswirkt.

Weitere Informationen: [Sicherheitsstandards](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Empfehlung zu Dienstprinzipale hinzugefügt

Es wurde eine neue Empfehlung hinzugefügt, die Azure Security Center-Kunden, die Verwaltungszertifikate zum Verwalten ihrer Abonnements verwenden, empfiehlt, zu Dienstprinzipalen zu wechseln.

Die Empfehlung, **Zum Schutz Ihrer Abonnements Dienstprinzipale anstelle von Verwaltungszertifikaten verwenden** besagt, für eine sicherere Verwaltung Ihrer Abonnements Dienstprinzipale oder Azure Resource Manager zu verwenden. 

Weitere Informationen: [Anwendungs- und Dienstprinzipalobjekten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sicherheitsrisikobewertung bei virtuellen Computern: Empfehlungen und Richtlinien wurden konsolidiert

Security Center untersucht Ihre virtuellen Computer, um festzustellen, ob darauf eine Lösung zur Sicherheitsrisikobewertung ausgeführt wird. Wenn keine Lösung zur Sicherheitsrisikobewertung gefunden wird, gibt Security Center eine Empfehlung zur Vereinfachung der Bereitstellung.

Werden Sicherheitsrisiken gefunden, gibt Security Center eine Empfehlung, die die Ergebnisse zusammenfasst, damit Sie die Sicherheitsrisiken untersuchen und gegebenenfalls beheben können.

Um für alle Benutzer, unabhängig vom verwendeten Scannertyp, eine einheitliche Umgebung zu gewährleisten, haben wir vier Empfehlungen zu den folgenden beiden Empfehlungen vereinheitlicht:

|Vereinheitlichte Empfehlung|Änderungsbeschreibung|
|----|:----|
|**Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden**|Ersetzt die folgenden beiden Empfehlungen:<br> **•** Integrierte Lösung zur Sicherheitsrisikobewertung auf virtuellen Computern aktivieren (unterstützt von Qualys – jetzt veraltet) (Empfehlung wird in Standard-Tarifen angezeigt)<br> **•** Die Lösung zur Sicherheitsrisikobewertung sollte auf Ihren virtuellen Computern installiert werden (jetzt veraltet) (Empfehlung wird sowohl in Standard- als auch Free-Tarifen angezeigt)|
|**Sicherheitsrisiken für VMs müssen behoben werden**|Ersetzt die folgenden beiden Empfehlungen:<br>**•** Auf Ihren virtuellen Computern gefundene Sicherheitsrisiken beheben (unterstützt von Qualys – jetzt veraltet)<br>**•** Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung entschärft werden (unterstützt von Qualys – jetzt veraltet)|
|||

Nun wenden Sie dieselbe Empfehlung an, um die Security Center-Erweiterung für die Sicherheitsrisikobewertung oder eine privat lizenzierte Lösung („BYOL“) von einem Partner wie Qualys oder Rapid7 bereitzustellen.

Wurden Sicherheitsrisiken gefunden und an Security Center gemeldet, werden Sie unabhängig von der Lösung zur Sicherheitsrisikobewertung, die diese Sicherheitsrisiken identifiziert hat, mit einer einzigen Empfehlung auf die Ergebnisse aufmerksam gemacht.

#### <a name="updating-dependencies"></a>Aktualisieren von Abhängigkeiten

Wenn Sie über Skripts, Abfragen oder Automatisierungen verfügen, die sich auf die früheren Empfehlungen oder Richtlinienschlüssel/-namen beziehen, verwenden Sie die folgenden Tabellen, um die Verweise zu aktualisieren:

##### <a name="before-august-2020"></a>Vor August 2020

|Empfehlung|`Scope`|
|----|:----|
|**Integrierte Lösung zur Sicherheitsrisikobewertung (unterstützt von Qualys) auf virtuellen Computern aktivieren**<br>Schlüssel: 550e890b-e652-4d22-8274-60b3bdb24c63|Integriert|
|**Auf Ihren virtuellen Computern gefundene Sicherheitsrisiken beheben (unterstützt von Qualys)**<br>Schlüssel: 1195afff-c881-495e-9bc5-1486211ae03f|Integriert|
|**Die Lösung zur Sicherheitsrisikobewertung sollte auf Ihren virtuellen Computern installiert werden**<br>Schlüssel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden**<br>Schlüssel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Richtlinie|`Scope`|
|----|:----|
|**Sicherheitsrisikobewertung für VMs muss aktiviert sein**<br>Richtlinien-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integriert|
|**Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung entschärft werden**<br>Richtlinien-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Ab August 2020

|Empfehlung|`Scope`|
|----|:----|
|**Auf Ihren virtuellen Computern muss eine Lösung zur Sicherheitsrisikobewertung installiert werden**<br>Schlüssel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Integriert + BYOL|
|**Sicherheitsrisiken für VMs müssen behoben werden**<br>Schlüssel: 1195afff-c881-495e-9bc5-1486211ae03f|Integriert + BYOL|
||||

|Richtlinie|`Scope`|
|----|:----|
|[**Sicherheitsrisikobewertung für virtuelle Computer muss aktiviert sein**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Richtlinien-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Integriert + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Der Initiative ASC_default wurden neue AKS-Sicherheitsrichtlinien hinzugefügt – nur für Kunden der privaten Vorschau.

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, fügt Security Center Richtlinien auf Kubernetes-Ebene und Härtungsempfehlungen hinzu, einschließlich Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Die frühe Phase dieses Projekts schließt eine private Vorschau und neue (standardmäßig deaktivierte) Richtlinien für die Initiative ASC_default ein.

Sie können diese Richtlinien gefahrlos und ohne Auswirkungen auf Ihre Umgebung ignorieren. Wenn Sie sie aktivieren möchten, registrieren Sie sich unter https://aka.ms/SecurityPrP für die Vorschauversion, und wählen Sie eine der folgenden Optionen aus:

1. **Einzelne Vorschau**, um nur dieser privaten Vorschau beizutreten. Nennen Sie „ASC Continuous Scan“ ausdrücklich als Vorschauversion, der Sie beitreten möchten.
1. **Fortlaufendes Programm**, um dieser und zukünftigen privaten Vorschauversionen hinzugefügt zu werden. Sie müssen ein Profil und eine Datenschutzvereinbarung ausfüllen.


## <a name="july-2020"></a>Juli 2020

Zu den Updates im Juli gehören:
- [Sicherheitsrisikobewertung für virtuelle Computer jetzt verfügbar für Nicht-Marketplace-Images](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Erweiterung des Bedrohungsschutzes für Azure Storage durch Einbeziehen von Azure Files und Azure Data Lake Storage Gen2 (Vorschau)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht neue Empfehlungen zum Aktivieren von Bedrohungsschutzfeatures](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbesserungen der Containersicherheit: schnellere Überprüfung der Registrierung und aktualisierte Dokumentation](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Neue Empfehlung zum Aktualisieren Ihrer Regeln für die adaptive Anwendungssteuerung](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sechs Richtlinien für SQL Advanced Data Security als veraltet markiert](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Sicherheitsrisikobewertung für virtuelle Computer jetzt verfügbar für Nicht-Marketplace-Images

Beim Bereitstellen einer Lösung zur Sicherheitsrisikobewertung führte Security Center bisher vorab eine Überprüfung durch. Die Überprüfung diente der Bestätigung einer Marketplace-SKU des virtuellen Zielcomputers. 

Ab diesem Update wurde die Überprüfung entfernt, und Sie können nun Tools zur Sicherheitsrisikobewertung auf benutzerdefinierten Windows- und Linux-Computern bereitstellen. Benutzerdefinierte Images sind solche, in denen Sie die Marketplace-Standardeinstellungen geändert haben.

Auch wenn Sie nun die integrierte Erweiterung für die Sicherheitsrisikobewertung (bereitgestellt durch Qualys) auf vielen weiteren Computern bereitstellen können, ist Support nur verfügbar, wenn Sie ein Betriebssystem verwenden, das unter [Bereitstellen der integrierten für Überprüfung auf Sicherheitsrisiken auf VMs im Standard-Tarif](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) aufgeführt ist.

Erfahren Sie mehr über die [integrierte Lösung für die Überprüfung auf Sicherheitsrisiken für virtuelle Computer (erfordert Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Erfahren Sie mehr über das Verwenden Ihrer eigenen privat lizenzierten Lösung zur Sicherheitsrisikobewertung von Qualys oder Rapid7 unter [Bereitstellen einer Partnerlösung zur Überprüfung auf Sicherheitsrisiken](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Erweiterung des Bedrohungsschutzes für Azure Storage durch Einbeziehen von Azure Files und Azure Data Lake Storage Gen2 (Vorschau)

Der Bedrohungsschutz für Azure Storage erkennt potenziell schädliche Aktivitäten in Ihren Azure Storage-Konten. Security Center zeigt Warnungen an, wenn Versuche erkannt werden, auf Ihre Speicherkonten zuzugreifen oder diese auszunutzen. 

Ihre Daten können geschützt werden, und zwar unabhängig davon, ob Sie als Blobcontainer, Dateifreigaben oder Data Lakes gespeichert werden.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht neue Empfehlungen zum Aktivieren von Bedrohungsschutzfeatures

Es wurden acht neue Empfehlungen hinzugefügt, um eine einfache Möglichkeit zum Aktivieren der Bedrohungsschutzfeatures von Azure Security Center für die folgenden Ressourcentypen bereitzustellen: virtuelle Computer, App Service-Pläne, Azure SQL-Datenbank-Server, SQL-Server auf Computern, Azure Storage-Konten, Azure Kubernetes Service-Cluster, Azure Container Registry-Registrierungen und Azure Key Vault-Tresore.

Die neuen Empfehlungen sind:

- **Advanced Data Security muss für Azure SQL-Datenbank-Server aktiviert sein**
- **Advanced Data Security muss für SQL Server-Instanzen auf Computern aktiviert sein.**
- **Advanced Threat Protection muss in Azure App Service-Plänen aktiviert sein.**
- **Advanced Threat Protection muss in Azure Container Registry-Instanzen aktiviert sein**
- **Advanced Threat Protection muss in Azure Key Vault-Instanzen aktiviert sein**
- **Advanced Threat Protection muss in Azure Kubernetes Service-Clustern aktiviert sein**
- **Advanced Threat Protection muss für Azure Storage-Konten aktiviert sein**
- **Advanced Threat Protection muss für virtuelle Computer aktiviert sein.**

Diese neuen Empfehlungen gehören zur Sicherheitssteuerung **Azure Defender aktivieren**.

Die Empfehlungen schließen auch die Möglichkeit schneller Korrekturen ein. 

> [!IMPORTANT]
> Wenn Sie eine dieser Empfehlungen umsetzen, fallen Gebühren für den Schutz der relevanten Ressourcen an. Diese Kosten beginnen sofort, wenn Sie über zugehörige Ressourcen im aktuellen Abonnement verfügen. Das gilt auch, wenn Sie sie zu einem späteren Zeitpunkt hinzufügen.
> 
> Wenn Sie z. B. nicht über Azure Kubernetes Service-Cluster in Ihrem Abonnement verfügen und den Bedrohungsschutz aktivieren, fallen keine Gebühren an. Wenn Sie in Zukunft einen Cluster im selben Abonnement hinzufügen, wird dieser automatisch geschützt, und die Gebühren beginnen ab diesem Zeitpunkt.

Weitere Informationen zu den einzelnen Angaben finden Sie auf der [Referenzseite zu Sicherheitsempfehlungen](recommendations-reference.md).

Erfahren Sie mehr über [Bedrohungsschutz in Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbesserungen bei der Containersicherheit: schnellere Überprüfung der Registrierung und aktualisierte Dokumentation

Im Rahmen der kontinuierlichen Investitionen im Bereich Containersicherheit geben wir gerne eine bedeutende Leistungsverbesserung im Security Center-Feature für dynamische Scans von Containerimages in Azure Container Registry weiter. Scans werden nun in der Regel in ungefähr zwei Minuten abgeschlossen. In einigen Fällen kann es bis zu 15 Minuten dauern.

Zur Verbesserung der Klarheit und der Beschreibungen zu den Sicherheitsfunktionen von Azure Security Center haben wir auch die Dokumentationsseiten für die Containersicherheit aktualisiert. 

Weitere Informationen zur Containersicherheit in Security Center finden Sie in den folgenden Artikeln:

- [Übersicht über die Containersicherheitsfeatures von Security Center](container-security.md)
- [Ausführliche Informationen zur Integration mit Azure Container Registry](defender-for-container-registries-introduction.md)
- [Ausführliche Informationen zur Integration mit Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Überprüfen Ihrer Registrierungen und Härten Ihrer Docker-Hosts](container-security.md)
- [Sicherheitswarnungen von den Features zum Schutz vor Bedrohungen für Azure Kubernetes Service-Cluster](alerts-reference.md#alerts-akscluster)
- [Sicherheitswarnungen von den Features zum Schutz vor Bedrohungen für Azure Kubernetes Service-Hosts](alerts-reference.md#alerts-containerhost)
- [Sicherheitsempfehlungen für Container](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Neue Empfehlung zum Aktualisieren Ihrer Regeln für die adaptive Anwendungssteuerung

Das Feature für die adaptive Anwendungssteuerung hat zwei bedeutende Updates erhalten:

* Eine neue Empfehlung identifiziert potenziell legitimes Verhalten, das bisher noch nicht zulässig war. Die neue Empfehlung **Zulassungslistenregeln in der Richtlinie für die adaptive Anwendungssteuerung müssen aktualisiert werden** fordert Sie auf, der vorhandenen Richtlinie neue Regeln hinzuzufügen, um die Anzahl der falsch positiven Ergebnisse bei adaptiven Warnungen zu Anwendungssteuerungen zu verringern.

* Pfadregeln unterstützen jetzt Platzhalter. Mit diesem Update können Sie zulässige Pfadregeln mithilfe von Platzhaltern konfigurieren. Es werden zwei Szenarios unterstützt:

    * Verwenden eines Platzhalters am Ende eines Pfads, um alle ausführbaren Dateien in diesem Ordner und Unterordnern zuzulassen

    * Verwenden eines Platzhalters in der Mitte eines Pfads zum Aktivieren des Namens einer bekannten ausführbaren Datei mit einem sich ändernden Ordnernamen (z. B. persönliche Benutzerordner mit einer bekannten ausführbaren Datei, automatisch generierte Ordnernamen usw.)


[Weitere Informationen über Adaptive Anwendungssteuerungen](security-center-adaptive-application.md)



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sechs Richtlinien für SQL Advanced Data Security als veraltet markiert

Sechs Richtlinien im Zusammenhang mit Advanced Data Security für SQL-Computer werden als veraltet markiert:

- Advanced Threat Protection-Typen müssen in den Advanced Data Security-Einstellungen von SQL Managed Instance auf „Alle“ festgelegt werden
- Advanced Threat Protection-Typen müssen in den Advanced Data Security-Einstellungen der SQL Server-Instanz auf „Alle“ festgelegt werden
- Advanced Data Security-Einstellungen für verwaltete SQL-Instanzen sollten eine E-Mail-Adresse für den Empfang von Sicherheitswarnungen enthalten.
- Advanced Data Security-Einstellungen für SQL Server sollten eine E-Mail-Adresse für den Empfang von Sicherheitswarnungen enthalten.
- In den Advanced Data Security-Einstellungen für die verwaltete SQL-Instanz müssen E-Mail-Benachrichtigungen an Administratoren und Abonnementbesitzer aktiviert sein
- E-Mail-Benachrichtigungen an Administratoren und Abonnementbesitzer sollten in den erweiterten Einstellungen für Datensicherheit in SQL Server aktiviert werden.

Weitere Informationen zu [integrierten Richtlinien](./policy-reference.md)
