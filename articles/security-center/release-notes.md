---
title: Versionshinweise für Azure Security Center
description: Enthält eine Beschreibung der Neuerungen und Änderungen in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: fe031fa6de86b8059ba175fc4e1df6385ca7e796
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551024"
---
# <a name="whats-new-in-azure-security-center"></a>Neuerungen in Azure Security Center

Azure Security Center befindet sich in der aktiven Entwicklung und wird ständig verbessert. Damit Sie bezüglich der aktuellen Entwicklungen immer auf dem neuesten Stand sind, enthält diese Seite Informationen zu neuen Features, Fehlerbehebungen und veralteten Funktionen.

Es ist ratsam, diese Seite regelmäßig zu besuchen, da sie immer wieder aktualisiert wird. 

Weitere Informationen zu den *geplanten* Änderungen, die demnächst im Security Center anstehen, finden Sie unter [Wichtige bevorstehende Änderungen in Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Elemente, die älter als sechs Monate sind, finden Sie im [Archiv zu den Neuerungen in Azure Security Center](release-notes-archive.md).


## <a name="february-2021"></a>Februar 2021

Updates im Februar:

- [Empfehlungen zum Schutz von Kubernetes-Workloads veröffentlicht (allgemeine Verfügbarkeit)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Direkter Link zur Richtlinie auf der Seite mit den Empfehlungsdetails](#direct-link-to-policy-from-recommendation-details-page)
- [Empfehlung zur SQL-Datenklassifizierung hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden (Vorschau)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview)

### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Empfehlungen zum Schutz von Kubernetes-Workloads veröffentlicht (Allgemeine Verfügbarkeit)

Wir freuen uns, ankündigen zu können, dass die Empfehlungen für den Schutz von Kubernetes-Workloads jetzt allgemein verfügbar sind.

Um sicherzustellen, dass Kubernetes-Workloads standardmäßig sicher sind, wurden über Security Center Härtungsempfehlungen auf Kubernetes-Ebene hinzugefügt, z. B. auch Erzwingungsoptionen mit Kubernetes-Zugangskontrolle.

Wenn das Azure Policy-Add-On für Kubernetes in Ihrem AKS-Cluster (Azure Kubernetes Service) installiert ist, wird jede Anforderung an den Kubernetes-API-Server anhand der vordefinierten bewährten Methoden (Anzeige in Form von 13 Sicherheitsempfehlungen) überwacht, bevor sie im Cluster gespeichert wird. Anschließend können Sie das Erzwingen der bewährten Methoden konfigurieren und auf zukünftige Workloads anwenden.

Beispielsweise können Sie vorschreiben, dass keine privilegierten Container erstellt werden sollen und dass zukünftige Anforderungen für diese Aktion blockiert werden sollen.

Weitere Informationen finden Sie unter [Bewährte Methoden zum Schutz von Workloads mithilfe der Kubernetes-Zugangssteuerung](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Während sich die Empfehlungen in der Vorschauphase befunden haben, haben diese nicht dazu geführt, dass eine AKS-Clusterressource als fehlerhaft gekennzeichnet wurde, und sind nicht in die Berechnung Ihrer Sicherheitsbewertung eingegangen. Ab dem Zeitpunkt dieser Ankündigung zur allgemeinen Verfügbarkeit wirken sie sich aber auf die Bewertungsberechnung aus. Falls Sie sie noch nicht umgesetzt haben, kann sich dies geringfügig auf Ihre Sicherheitsbewertung auswirken. Setzen Sie sie nach Möglichkeit um, wie dies unter [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md) beschrieben ist.


### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Direkter Link zur Richtlinie auf der Seite mit den Empfehlungsdetails

Wenn Sie die Details einer Empfehlung überprüfen, ist es häufig hilfreich, die zugrunde liegende Richtlinie zu kennen. Für jede Empfehlung, die von einer Richtlinie unterstützt wird, enthält die Seite mit den Empfehlungsdetails einen neuen Link:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link zur Azure Policy-Seite für eine bestimmte Richtlinie, die eine Empfehlung unterstützt":::

Verwenden Sie diesen Link, um die Richtliniendefinition anzuzeigen und die Bewertungslogik zu überprüfen. 

In der Liste mit den Empfehlungen in unserem [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) finden Sie auch Links zu den Seiten mit Richtliniendefinitionen:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Zugriff auf die Azure Policy-Seite für eine bestimmte Richtlinie direkt über die Seite mit der Azure Security Center-Empfehlungsreferenz" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Empfehlung zur SQL-Datenklassifizierung hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr

Die Empfehlung **Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden** hat keine Auswirkung auf Ihre Sicherheitsbewertung mehr. Da dies die einzige Empfehlung unter der Sicherheitskontrolle **Datenklassifizierung anwenden** ist, gilt für diese Kontrolle in Bezug auf die Sicherheitsbewertung jetzt der Wert „0“.


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-preview"></a>Workflowautomatisierungen können durch Änderungen an Bewertungen der Einhaltung gesetzlicher Bestimmungen ausgelöst werden (Vorschau)

Wir haben den Triggeroptionen für Ihre Workflowautomatisierungen jetzt einen dritten Datentyp hinzugefügt: Änderungen an Bewertungen zur Einhaltung gesetzlicher Bestimmungen.

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Verwenden von Änderungen bei Bewertungen der Einhaltung gesetzlicher Bestimmungen zum Auslösen der Workflowautomatisierung" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


## <a name="january-2021"></a>Januar 2021

Die Updates im Januar umfassen Folgendes:

- [Der Azure-Sicherheitsvergleichstest ist jetzt die Standardrichtlinieninitiative für Azure Security Center.](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Die Sicherheitsrisikobewertung für lokale Computer und Multi-Cloud-Computer ist jetzt allgemein verfügbar.](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Die Sicherheitsbewertung für Verwaltungsgruppen ist jetzt als Vorschau verfügbar.](#secure-score-for-management-groups-is-now-available-in-preview)
- [Die Sicherheitsbewertungs-API ist jetzt allgemein verfügbar.](#secure-score-api-is-released-for-general-availability-ga)
- [Azure Defender für App Service wurde Schutz vor verwaisten DNS-Einträgen hinzugefügt.](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Multi-Cloud-Connectors sind jetzt allgemein verfügbar.](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Möglichkeit, bei Ihrer Sicherheitsbewertung für Abonnements und Verwaltungsgruppen ganze Empfehlungen auszunehmen](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Benutzer können beim globalen Administrator jetzt mandantenweite Sichtbarkeit anfordern.](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 Vorschauempfehlungen werden hinzugefügt, um die Abdeckung des Azure-Sicherheitsvergleichstests zu erhöhen](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-Export der gefilterten Liste mit Empfehlungen](#csv-export-of-filtered-list-of-recommendations)
- [Nicht anwendbare Ressourcen werden in Azure Policy-Bewertungen jetzt als „Konform“ gemeldet.](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportieren wöchentlicher Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen per fortlaufendem Export (Vorschau)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Der Azure-Sicherheitsvergleichstest ist jetzt die Standardrichtlinieninitiative für Azure Security Center.

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz Azure-spezifischer Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit.

In den letzten Monaten wurde die Liste der integrierten Sicherheitsempfehlungen von Security Center erheblich erweitert, um unsere Abdeckung dieser Benchmark auszudehnen.

Ab diesem Release bildet die Benchmark die Grundlage für Security Center-Empfehlungen und ist vollständig als Standardrichtlinieninitiative integriert. 

Die Dokumentation jedes Azure-Diensts enthält eine Seite mit der Sicherheitsbaseline. [Hier](security-baseline.md) finden Sie beispielsweise die Baseline von Security Center. Diese Baselines basieren auf dem Vergleichstest für die Azure-Sicherheit.

Auf dem Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen werden während eines Übergangszeitraums zwei Instanzen der Benchmark angezeigt:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen mit dem Vergleichstest für die Azure-Sicherheit":::

Auf bereits vorhandene Empfehlungen hat dies keine Auswirkungen, und im Zuge des weiteren Ausbaus der Benchmark werden Änderungen automatisch in Security Center berücksichtigt. 

Weitere Informationen finden Sie auf den folgenden Seiten:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](../security/benchmarks/introduction.md)
- [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Die Sicherheitsrisikobewertung für lokale Computer und Multi-Cloud-Computer ist jetzt allgemein verfügbar.

Im Oktober haben wir eine Vorschauversion für die Überprüfung von Azure Arc-fähigen Servern mit der Lösung für die Überprüfung auf Sicherheitsrisiken (von Qualys) angekündigt, die in [Azure Defender für Server](defender-for-servers-introduction.md) integriert ist.

Dieses Feature ist jetzt allgemein verfügbar.

Wenn Sie Azure Arc auf Ihren Nicht-Azure-Computern aktiviert haben, bietet Security Center eine manuelle und skalierbare Bereitstellung des integrierten Sicherheitsrisikoscanners auf diesen Computern.

Mit diesem Update können Sie die Leistungsfähigkeit von **Azure Defender für Server** nutzen, um Ihr Programm zur Verwaltung von Sicherheitsrisiken auf allen Azure- und Nicht-Azure-Ressourcen zu konsolidieren.

Hauptfunktionen:

- Überwachen des Bereitstellungsstatus des Scanners für die Sicherheitsrisikobewertung auf Azure Arc-Computern
- Bereitstellen des integrierten Agents für die Sicherheitsrisikobewertung auf ungeschützten Azure Arc-Computern unter Windows und Linux (manuell und skalierbar)
- Empfangen und Analysieren ermittelter Sicherheitsrisiken von bereitgestellten Agents (manuell und skalierbar)
- Einheitliche Benutzeroberfläche für Azure-VMs und Azure Arc-Computer

[Erfahren Sie mehr über das Bereitstellen des integrierten Sicherheitsrisikoscanners auf Ihren Hybridcomputern](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Erfahren Sie mehr über Server mit Azure Arc-Unterstützung](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Die Sicherheitsbewertung für Verwaltungsgruppen ist jetzt als Vorschau verfügbar.

Zusätzlich zur Abonnementebene werden auf der Seite „Sicherheitsbewertung“ nun auch die aggregierten Sicherheitsbewertungen für Ihre Verwaltungsgruppen angezeigt. Nun können Sie sich also die Liste der Verwaltungsgruppen in Ihrer Organisation sowie die Bewertung für die jeweilige Verwaltungsgruppe ansehen.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Anzeigen der Sicherheitsbewertungen für Ihre Verwaltungsgruppen":::

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Die Sicherheitsbewertungs-API ist jetzt allgemein verfügbar.

Sie können jetzt über die [Sicherheitsbewertungs-API](/rest/api/securitycenter/securescores/) auf Ihre Bewertung zugreifen. Die API-Methoden bieten die Flexibilität, die Daten abzufragen und im Laufe der Zeit einen eigenen Berichtsmechanismus für Ihre Sicherheitsbewertungen zu erstellen. Beispiel:

- Verwenden Sie die API für **Sicherheitsbewertungen**, um die Bewertung für ein bestimmtes Abonnement zu erhalten.
- Verwenden Sie die API für **Sicherheitsbewertungs-Steuerelemente**, um die Sicherheitssteuerelemente und die aktuelle Bewertung Ihrer Abonnements aufzulisten.

Informieren Sie sich im [Bereich zu den Sicherheitsbewertungen in unserer GitHub-Community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) über die externen Tools, die mit der Sicherheitsbewertungs-API verwendet werden können.

Weitere Informationen finden Sie unter [Erweiterter Secure Score (Vorschau) in Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Azure Defender für App Service wurde Schutz vor verwaisten DNS-Einträgen hinzugefügt.

Unterdomänenübernahmen sind eine weit verbreitete Bedrohung mit hohem Schweregrad für Organisationen. Eine Unterdomänenübernahme ist möglich, wenn Sie über einen DNS-Eintrag verfügen, der auf Website verweist, deren Bereitstellung aufgehoben wurde. Solche DNS-Einträge werden auch als „verwaiste DNS“-Einträge bezeichnet. CNAME-Einträge sind besonders anfällig für diese Bedrohung. 

Durch die Übernahme von Unterdomänen können Bedrohungsakteure Datenverkehr, der für die Domäne eines Unternehmens bestimmt ist, an eine Website für schädliche Aktivitäten umleiten.

Von Azure Defender für App Service werden nun verwaiste DNS-Einträge erkannt, wenn eine App Service-Website eingestellt wird. Dies ist der Zeitpunkt, zu dem der DNS-Eintrag auf eine nicht vorhandene Ressource verweist und Ihre Website für eine Unterdomänenübernahme anfällig ist. Diese Schutzmaßnahmen sind unabhängig davon verfügbar, ob Ihre Domänen mit Azure DNS oder mit einer externen Domänenregistrierungsstelle verwaltet werden, und sie gelten sowohl für App Service unter Windows als auch für App Service unter Linux.

Weitere Informationen:

- Referenztabelle [Warnungen für Azure App Service](alerts-reference.md#alerts-azureappserv): Enthält zwei neue Azure Defender-Warnungen, die ausgelöst werden, wenn ein verwaister DNS-Eintrag erkannt wird.
- [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../security/fundamentals/subdomain-takeover.md): Hier finden Sie Informationen zur Gefahr von Unterdomänenübernahmen sowie zu verwaisten DNS-Einträgen.
- [Einführung in Azure Defender für App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Multi-Cloud-Connectors sind jetzt allgemein verfügbar.

Cloudworkloads umfassen in der Regel mehrere Cloudplattformen, daher muss das auch für Cloudsicherheitsdienste gelten.

Azure Security Center schützt Workloads in Azure, Amazon Web Services (AWS) und Google Cloud Platform (GCP).

Wenn Sie eine Verbindung mit Ihren AWS- oder GCP-Konten herstellen, werden ihre nativen Sicherheitstools wie AWS Security Hub und GCP Security Command Center in Azure Security Center integriert.

Das bedeutet, dass Security Center Transparenz und Schutz für alle gängigen Cloudumgebungen bereitstellt. Diese Integration hat unter anderem folgende Vorteile:

- Automatische Agent-Bereitstellung: Security Center verwendet Azure Arc, um den Log Analytics-Agent für Ihre AWS-Instanzen bereitzustellen.
- Richtlinienverwaltung
- Verwaltung von Sicherheitsrisiken
- Integrierte Endpunkterkennung und -antwort (Endpoint Detection and Response, EDR)
- Erkennung von Sicherheitsfehlkonfigurationen
- Eine zentrale Ansicht mit Sicherheitsempfehlungen von allen Cloudanbietern
- Einbindung all Ihrer Ressourcen in die Berechnung von Sicherheitsbewertungen durch Security Center
- Bewertung der Einhaltung gesetzlicher Bestimmungen für Ihre AWS- und GPC-Ressourcen

Wählen Sie im Menü von Security Center die Option **Multi cloud connectors** (Connectors mehrerer Clouds) aus, um die Optionen zum Erstellen neuer Connectors anzuzeigen:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Schaltfläche zum Hinzufügen eines AWS-Kontos auf der Seite für Connectors mehrerer Clouds in Security Center":::

Weitere Informationen finden Sie hier:
- [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md)
- [Herstellen einer Verbindung zwischen Ihren GCP-Konten und Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Möglichkeit, bei Ihrer Sicherheitsbewertung für Abonnements und Verwaltungsgruppen ganze Empfehlungen auszunehmen

Wir erweitern die Ausnahmenfunktion um die Möglichkeit, vollständige Empfehlungen einzuschließen. Dazu stellen wir weitere Optionen für die Feinabstimmung der Sicherheitsempfehlungen zur Verfügung, die Security Center für Ihre Abonnements, Verwaltungsgruppe oder Ressourcen bereitstellt.

Gelegentlich kann es vorkommen, dass eine Ressource als fehlerhaft aufgeführt wird, obwohl Sie wissen, dass das Problem durch ein Drittanbietertool behoben und dies von Security Center nicht erkannt wurde. Auch kann es bisweilen passieren, dass eine Empfehlung in einem Bereich angezeigt wird, zu dem sie Ihrer Meinung nach nicht gehört. Möglicherweise ist die Empfehlung für ein bestimmtes Abonnement nicht geeignet. Oder vielleicht hat Ihr Unternehmen auch die Entscheidung getroffen, die Risiken im Zusammenhang mit der jeweiligen Ressource oder Empfehlung zu akzeptieren.

Mit dieser Previewfunktion können Sie jetzt eine Ausnahme für eine Empfehlung erstellen. Dabei haben Sie folgende Möglichkeiten:

- Sie können **eine Ressource ausnehmen**, um sicherzustellen, dass sie in Zukunft nicht mehr als fehlerhafte Ressourcen aufgeführt wird und keine Auswirkung auf Ihre Sicherheitsbewertung hat. Die Ressource wird als nicht anwendbar aufgeführt, und als Grund wird „Ausgenommen“ mit der spezifischen, von Ihnen ausgewählten Begründung angezeigt.

- Sie können **ein Abonnement oder eine Verwaltungsgruppe ausnehmen**, um sicherzustellen, dass die Empfehlung keine Auswirkung auf Ihre Sicherheitsbewertung hat und in Zukunft nicht mehr für das Abonnement oder die Verwaltungsgruppe angezeigt wird. Das gilt sowohl für bereits vorhandene Ressourcen als auch für alle zukünftig erstellten Ressourcen. Die Empfehlung wird mit der spezifischen Begründung gekennzeichnet, die Sie für den ausgewählten Bereich auswählen.

Weitere Informationen finden Sie unter [Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Benutzer können beim globalen Administrator jetzt mandantenweite Sichtbarkeit anfordern.

Wenn ein Benutzer nicht über Berechtigungen zum Anzeigen von Security Center-Daten verfügt, wird ihm jetzt ein Link angezeigt, über den er Berechtigungen vom globalen Administrator der Organisation anfordern kann. Die Anforderung enthält die gewünschte Rolle sowie eine Begründung, warum die Rolle erforderlich ist.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner mit der Information, dass der Benutzer mandantenweite Berechtigungen anfordern kann":::

Weitere Informationen finden Sie unter [Anfordern mandantenweiter Berechtigungen, wenn die eigenen Berechtigungen nicht ausreichen](security-center-management-groups.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 Vorschauempfehlungen werden hinzugefügt, um die Abdeckung des Azure-Sicherheitsvergleichstests zu erhöhen

Der Azure-Sicherheitsvergleichstest ist die Standardrichtlinieninitiative in Azure Security Center. 

Die folgenden 35 Vorschauempfehlungen wurden zu Security Center hinzugefügt, um die Abdeckung dieser Benchmark zu erhöhen.

> [!TIP]
> Empfehlungen der Vorschau versetzen keine Ressourcen in einen fehlerhaften Zustand, und sie werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen. Setzen Sie sie trotzdem um, wann immer möglich, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen. Informationen zum Umgang mit diesen Empfehlungen finden Sie unter [Umsetzen von Empfehlungen in Azure Security Center](security-center-remediate-recommendations.md).

| Sicherheitskontrolle                     | Neue Empfehlungen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aktivieren der Verschlüsselung ruhender Daten            | - Azure Cosmos DB-Konten sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Azure Machine Learning-Arbeitsbereiche sollten mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) verschlüsselt werden.<br>- BYOK-Datenschutz (Bring Your Own Key) sollte für MySQL-Server aktiviert sein.<br>- BYOK-Datenschutz (Bring Your Own Key) sollte für PostgreSQL-Server aktiviert sein.<br>- Cognitive Services-Konten sollten die Datenverschlüsselung mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) aktivieren.<br>- Containerregistrierungen sollten mit einem kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) verschlüsselt werden.<br>- Verwaltete SQL-Instanzen sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Computer mit SQL Server sollten kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden.<br>- Speicherkonten sollten einen kundenseitig verwalteten Schlüssel (Customer-Managed Key, CMK) zur Verschlüsselung verwenden.                                                                                                                                                              |
| Bewährte Sicherheitsmethoden implementieren    | - In Abonnements sollte eine Kontakt-E-Mail-Adresse für Sicherheitsprobleme angegeben sein.<br> - Für Ihr Abonnement sollte die automatische Bereitstellung des Log Analytics-Agents aktiviert sein.<br> - E-Mail-Benachrichtigungen bei Warnungen mit hohem Schweregrad sollten aktiviert sein.<br> - Das Senden von E-Mail-Benachrichtigungen an den Abonnementbesitzers bei Warnungen mit hohem Schweregrad sollte aktiviert sein.<br> - Für Schlüsseltresore sollte der Löschschutz aktiviert sein.<br> - Für Schlüsseltresore sollte vorläufiges Löschen aktiviert sein. |
| Zugriff und Berechtigungen verwalten        | - Für Funktions-Apps sollte „Clientzertifikate (eingehende Clientzertifikate)“ aktiviert sein. |
| Anwendungen vor DDoS-Angriffen schützen | - Web Application Firewall (WAF) sollte für Application Gateway aktiviert sein.<br> - Web Application Firewall (WAF) sollte für Azure Front Door Service aktiviert sein. |
| Nicht autorisierten Netzwerkzugriff einschränken | - Für Key Vault sollte eine Firewall aktiviert sein.<br> - Für Key Vault sollte ein privater Endpunkt konfiguriert werden.<br> - App Configuration sollte eine private Verbindung verwenden.<br> - Azure Cache for Redis sollte sich in einem virtuellen Netzwerk befinden.<br> - Azure Event Grid-Domänen sollten eine private Verbindung verwenden.<br> - Azure Event Grid-Themen sollten eine private Verbindung verwenden.<br> - Azure Machine Learning-Arbeitsbereiche sollten eine private Verbindung verwenden.<br> - Azure SignalR Service sollte eine private Verbindung verwenden.<br> - Azure Spring Cloud sollte Netzwerkinjektion verwenden.<br> - Containerregistrierungen sollten keinen uneingeschränkten Netzwerkzugriff zulassen.<br> - Containerregistrierungen sollten eine private Verbindung verwenden.<br> - Öffentlicher Netzwerkzugriff sollte für MariaDB-Server deaktiviert sein.<br> - Öffentlicher Netzwerkzugriff sollte für MySQL-Server deaktiviert sein.<br> - Öffentlicher Netzwerkzugriff sollte für PostgreSQL-Server deaktiviert sein.<br> - Das Speicherkonto sollte eine Private Link-Verbindung verwenden.<br> - Speicherkonten sollten den Netzwerkzugriff mithilfe von VNET-Regeln einschränken.<br> - VM Image Builder-Vorlagen sollten eine private Verbindung verwenden.|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Verwandte Links:

- [Weitere Informationen zum Vergleichstest für die Azure-Sicherheit](../security/benchmarks/introduction.md)
- [Weitere Informationen zu Azure Database for MariaDB](../mariadb/overview.md)
- [Weitere Informationen zu Azure Database for MySQL](../mysql/overview.md)
- [Weitere Informationen zu Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-Export der gefilterten Liste mit Empfehlungen 

Im November 2020 haben wir der Seite mit den Empfehlungen Filter hinzugefügt ([Die Empfehlungsliste beinhaltet nun Filter](#recommendations-list-now-includes-filters)). Im Dezember haben wir diese Filter erweitert ([Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Mit dieser Ankündigung wird das Verhalten der Schaltfläche für den **Download als CSV** so geändert, dass der CSV-Export nur die Empfehlungen enthält, die derzeit in der gefilterten Liste angezeigt werden. 

In der Abbildung unten ist beispielsweise erkennbar, dass die Liste nach zwei Empfehlungen gefiltert wurde. Die generierte CSV-Datei enthält die Statusdetails für jede Ressource, auf die sich diese beiden Empfehlungen auswirken.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Exportieren von gefilterten Empfehlungen als CSV-Datei":::

Weitere Informationen finden Sie unter [Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Nicht anwendbare Ressourcen werden in Azure Policy-Bewertungen jetzt als „Konform“ gemeldet.

Bislang wurden Ressourcen, die für eine Empfehlung ausgewertet und als **nicht anwendbar** befunden wurden, in Azure Policy als „Nicht konform“ angezeigt. Ihr Zustand kann durch keine Benutzeraktion in „Konform“ geändert werden. Mit dieser Änderung werden sie zur besseren Verständlichkeit als „Konform“ gemeldet.

Die einzige Auswirkung wird in Azure Policy zu sehen sein, weil die Anzahl der konformen Ressourcen dort steigt. Ihre Sicherheitsbewertung in Azure Security Center wird dadurch nicht beeinflusst.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportieren wöchentlicher Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen per fortlaufendem Export (Vorschau)

Wir haben den Tools für den [fortlaufenden Export](continuous-export.md) eine neue Previewfunktion hinzugefügt, mit der wöchentliche Momentaufnahmen der Daten für die Sicherheitsbewertung und die Einhaltung gesetzlicher Bestimmungen exportiert werden können.

Legen Sie beim Definieren eines Vorgangs für den fortlaufenden Export die Exporthäufigkeit fest:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Auswählen der Häufigkeit des fortlaufenden Exports":::

- **Streaming**: Bewertungen werden in Echtzeit gesendet, wenn der Integritätszustand einer Ressource aktualisiert wird (wenn keine Updates durchgeführt werden, werden keine Daten gesendet).
- **Momentaufnahmen**: Einmal pro Woche wird eine Momentaufnahme des aktuellen Zustands aller Bewertungen der Einhaltung gesetzlicher Bestimmungen gesendet. (Dies ist eine Previewfunktion für wöchentliche Momentaufnahmen der Daten für Sicherheitsbewertungen und die Einhaltung gesetzlicher Bestimmungen.)

Erfahren Sie mehr zu allen Funktionen dieses Features unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md).

## <a name="december-2020"></a>Dezember 2020

Zu den Updates im Dezember gehören:

- [Azure Defender für SQL Server-Instanzen auf Computern ist allgemein verfügbar.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender für SQL-Unterstützung für dedizierte SQL-Pools von Azure Synapse Analytics ist allgemein verfügbar.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globale Administratoren können sich jetzt selbst Berechtigungen auf Mandantenebene erteilen.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Zwei neue Azure Defender-Pläne: Azure Defender für DNS und Azure Defender für Resource Manager (in der Vorschauphase)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Neue Seite „Sicherheitswarnungen“ im Azure-Portal (Vorschau)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Überarbeitete Security Center-Benutzeroberfläche in Azure SQL-Datenbank und SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Aktualisierung von Tools und Filtern des Ressourcenbestands](#asset-inventory-tools-and-filters-updated)
- [Empfehlung zu Web-Apps, die SSL-Zertifikate anfordern, ist nicht mehr Teil der Sicherheitsbewertung](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Neue Datentypen und verbesserte deployifnotexist-Richtlinien für fortlaufenden Export](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


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
    - [Einführung in Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für Resource Manager](defender-for-resource-manager-usage.md)
    - [Warnungen für Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender für DNS**: Überwacht kontinuierlich alle DNS-Abfragen Ihrer Azure-Ressourcen. Weitere Informationen finden Sie unter
    - [Einführung in Azure Defender für DNS](defender-for-dns-introduction.md)
    - [Reagieren auf Warnungen von Azure Defender für DNS](defender-for-dns-usage.md)
    - [Warnungen für DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Neue Seite „Sicherheitswarnungen“ im Azure-Portal (Vorschau)

Die Seite „Sicherheitswarnungen“ von Azure Security Center wurde neu gestaltet. Sie bietet jetzt Folgendes:

- **Eine verbesserte Selektierungsoberfläche für Warnungen**: Die Liste enthält anpassbare Filter und Gruppierungsoptionen und trägt dadurch zur Reduzierung der „Warnungsmüdigkeit“ bei, sodass Sie sich einfacher auf die relevantesten Bedrohungen konzentrieren können.
- **Weitere Informationen in der Warnungsliste**, beispielsweise MITRE ATT&CK-Taktiken
- **Schaltfläche zum Erstellen von Beispielwarnungen**: Sie können Beispielwarnungen aus allen Azure Defender-Plänen erstellen, um Azure Defender-Funktionen auszuwerten und Ihre Warnungskonfiguration zu testen (SIEM-Integration, E-Mail-Benachrichtigungen und Workflowautomatisierungen).
- **Angleichung an die Azure Sentinel-Incidentoberfläche**: Für Kunden, die beide Produkte verwenden, ist der Wechsel zwischen den Produkten jetzt unkomplizierter, und es ist einfach, das eine vom anderen zu übernehmen.
- **Bessere Leistung** für große Warnungslisten
- **Tastaturnavigation** durch die Warnungsliste
- **Warnungen aus Azure Resource Graph**: Sie können Warnungen in Azure Resource Graph abfragen, der Kusto-ähnlichen API für alle Ihre Ressourcen. Dies ist auch nützlich, wenn Sie eigene Warnungsdashboards erstellen. [Erfahren Sie mehr über Azure Resource Graph](../governance/resource-graph/index.yml).

Um auf die neue Oberfläche zuzugreifen, verwenden Sie oben auf der Seite „Sicherheitswarnungen“ den Link „Jetzt ausprobieren“ im Banner.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner mit dem Link zur neuen Benutzeroberfläche für Warnungen in der Vorschauversion":::

Informationen zum Erstellen von Beispielwarnungen über die neue Oberfläche für Warnungen finden Sie unter [Generieren von Azure Defender-Beispielwarnungen](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Überarbeitete Security Center-Benutzeroberfläche in Azure SQL-Datenbank und SQL Managed Instance 

Die Security Center-Benutzeroberfläche in SQL ermöglicht den Zugriff auf die folgenden Security Center- und Azure Defender für SQL-Features:

- **Sicherheitsempfehlungen**: Security Center führt eine regelmäßige Analyse des Sicherheitsstatus aller verbundenen Azure-Ressourcen durch, um potenzielle Fehlkonfigurationen in Bezug auf die Sicherheit zu identifizieren. Anschließend werden Empfehlungen dazu angezeigt, wie Sie diese Sicherheitsrisiken beseitigen und den Sicherheitsstatus von Organisationen verbessern können.
- **Sicherheitswarnungen**: Bei diesem Erkennungsdienst werden Azure SQL-Aktivitäten fortlaufend auf Bedrohungen wie Einschleusung von SQL-Befehlen, Brute-Force-Angriffe und Berechtigungsmissbrauch überwacht. Dieser Dienst löst ausführliche und aktionsorientierte Sicherheitswarnungen in Security Center aus und zeigt Optionen für die weitere Untersuchung mit Azure Sentinel, der nativen SIEM-Lösung von Microsoft, an.
- **Ergebnisse**: Bei diesem Dienst für die Sicherheitsrisikobewertung werden die Azure SQL-Konfigurationen fortlaufend überwacht, und es wird Hilfestellung bei der Eindämmung von Sicherheitsrisiken geleistet. Es werden Bewertungsüberprüfungen durchgeführt, um eine Übersicht über die einzelnen Azure SQL-Sicherheitsstatus und die zugehörigen ausführlichen Ergebnisse zur Sicherheit anzeigen zu können.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Sicherheitsfunktionen von Azure Security Center für SQL sind in Azure SQL verfügbar":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Aktualisierung von Tools und Filtern des Ressourcenbestands

Die Bestandsseite in Azure Security Center wurde mit den folgenden Änderungen aktualisiert:

- Der Symbolleiste wurde eine Option für **Anleitungen und Feedback** hinzugefügt. Ein Bereich mit Links zu verwandten Informationen und Tools wurde hinzugefügt. 
- Den verfügbaren Standardfiltern für Ihre Ressourcen wurde ein **Abonnementfilter** hinzugefügt.
- Link **Abfrage öffnen** zum Öffnen der aktuellen Filteroptionen als Azure Resource Graph-Abfrage (früher als „Im Resource Graph-Explorer anzeigen“ bezeichnet).
- **Operatoroptionen** für jeden Filter. Sie können jetzt auch andere logische Operatoren als „=“ auswählen. Beispielsweise können Sie nach allen Ressourcen mit aktiven Empfehlungen suchen, deren Titel die Zeichenfolge „encrypt“ enthält. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Steuerelemente für die Operatoroption in Filtern des Ressourcenbestands":::

Weitere Informationen zum Bestand finden Sie unter [Untersuchen und Verwalten Ihrer Ressourcen mit dem Ressourcenbestand und Verwaltungstools](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Empfehlung zu Web-Apps, die SSL-Zertifikate anfordern, ist nicht mehr Teil der Sicherheitsbewertung

Die Empfehlung „Web-Apps sollten ein SSL-Zertifikat für alle eingehenden Anforderungen anfordern“ wurde aus der Sicherheitssteuerung **Zugriff und Berechtigungen verwalten** (mit einem Wert von maximal 4 Punkten) in **Bewährte Sicherheitsmethoden implementieren** (mit einem Wert von 0 Punkten) verschoben. 

Wenn sichergestellt wird, dass eine Web-App ein Zertifikat anfordert, erhöht dies auf jeden Fall die Sicherheit. Für öffentliche Web-Apps ist dies aber irrelevant. Wenn Sie über HTTP und nicht HTTPS auf Ihre Website zugreifen, erhalten Sie kein Clientzertifikat. Wenn Ihre Anwendung also Clientzertifikate erfordert, sollten Sie keine Anforderungen an Ihre Anwendung über HTTP zulassen. Weitere Informationen finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

Mit dieser Änderung ist die Empfehlung jetzt eine empfohlene bewährte Methode, die keine Auswirkung auf die Bewertung hat. 

Informationen dazu, welche Empfehlungen in den einzelnen Sicherheitssteuerungen enthalten sind, finden Sie unter [Sicherheitskontrollen und deren Empfehlungen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Empfehlungsseite enthält neue Filter für Umgebung, Schweregrad und verfügbare Antworten

Azure Security Center überwacht alle verbundenen Ressourcen und generiert Sicherheitsempfehlungen. Verwenden Sie diese Empfehlungen, um den Sicherheitsstatus Ihrer Hybrid Cloud zu verbessern und die Konformität mit den relevanten Richtlinien und Standards für Ihre Organisation, Ihre Branche und Ihr Land nachzuverfolgen.

Während die Abdeckung und die Features von Security Center erweitert werden, werden der Liste mit den Sicherheitsempfehlungen jeden Monat neue Einträge hinzugefügt. Informieren Sie sich beispielsweise unter [29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Da der Liste ständig neue Einträge hinzugefügt werden, ist es erforderlich, dass sie nach den für Sie interessanten Empfehlungen gefiltert werden kann. Im November haben wir der Seite mit den Empfehlungen Filter hinzugefügt (siehe [Die Empfehlungsliste beinhaltet nun Filter](#recommendations-list-now-includes-filters)).

Die in diesem Monat hinzugefügten Filter verfügen über Optionen, mit denen Sie den Umfang der Liste mit den Empfehlungen wie folgt eingrenzen können:

- **Umgebung**: Es werden Empfehlungen für Ihre AWS-, GCP- oder Azure-Ressourcen (oder eine beliebige Kombination) angezeigt.
- **Schweregrad**: Es werden Empfehlungen gemäß der Klassifizierung nach Schweregrad angezeigt, die für Security Center festgelegt wurden.
- **Antwortaktionen**: Es werden Empfehlungen gemäß der Verfügbarkeit von Security Center-Antwortoptionen angezeigt: „Schnelle Problembehebung“, „Verweigern“ und „Erzwingen“.

    > [!TIP]
    > Der Filter für Antwortaktionen ersetzt den Filter vom Typ **Schnelle Problembehebung verfügbar (Ja/Nein)** . 
    > 
    > Informieren Sie sich weiter über diese Antwortoptionen:
    > - [Schnelle Problembehebung](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Nach Sicherheitssteuerung gruppierte Empfehlungen" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Neue Datentypen und verbesserte deployifnotexist-Richtlinien für fortlaufenden Export

Mit den Azure Security Center-Tools für den fortlaufenden Export können Sie die Empfehlungen und Warnungen für die Verwendung mit anderen Überwachungstools in Ihrer Umgebung exportieren.

Mit dem fortlaufenden Export können Sie umfassend anpassen, was exportiert wird und wohin. Ausführliche Informationen finden Sie unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md).

Diese Tools wurden wie folgt verbessert und erweitert:

- **Verbesserung der deployifnotexist-Richtlinien für den fortlaufenden Export:** Mit den Richtlinien wird nun Folgendes durchgeführt:

    - **Überprüfen, ob die Konfiguration aktiviert ist:** Wenn nicht, wird die Richtlinie als nicht konform angezeigt und eine konforme Ressource erstellt. Weitere Informationen zu den bereitgestellten Azure Policy-Vorlagen finden Sie unter [Einrichten eines fortlaufenden Exports](continuous-export.md#set-up-a-continuous-export) auf der Registerkarte „Bereitstellung im großen Stil mit Azure Policy“.

    - **Unterstützen des Exports von Sicherheitsergebnissen:** Bei Verwendung der Azure Policy-Vorlagen können Sie Ihren fortlaufenden Export so konfigurieren, dass er Ergebnisse enthält. Dies ist beim Exportieren von Empfehlungen relevant, die über untergeordnete Empfehlungen verfügen. Beispiele hierfür sind Ergebnisse aus Sicherheitsrisikobewertungen oder spezifische Systemupdates für die übergeordnete Empfehlung „Systemupdates sollten auf Ihren Computern installiert sein“.
    
    - **Unterstützen des Exports der Daten von Sicherheitsbewertungen**

- **Daten zur Bewertung der Einhaltung gesetzlicher Bestimmungen hinzugefügt (Vorschauphase):** Sie können Updates für Bewertungen der Einhaltung gesetzlichen Bestimmungen jetzt fortlaufend in einen Log Analytics-Arbeitsbereich oder eine Event Hub-Instanz exportieren (gilt auch für benutzerdefinierte Initiativen). Diese Funktion ist in nationalen Clouds und Sovereign Clouds nicht verfügbar.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Optionen zum Einbinden von Bewertungen der Einhaltung gesetzlicher Bestimmungen in Ihre Daten des fortlaufenden Exports":::


## <a name="november-2020"></a>November 2020

Updates im November:

- [29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 in das Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen aufgenommen](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Die Empfehlungsliste beinhaltet nun Filter.](#recommendations-list-now-includes-filters)
- [Die Umgebung für die automatische Bereitstellung wurde verbessert und erweitert.](#auto-provisioning-experience-improved-and-expanded)
- [Die Sicherheitsbewertung ist jetzt im fortlaufenden Export (Vorschauversion) verfügbar.](#secure-score-is-now-available-in-continuous-export-preview)
- [Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Die Seite „Richtlinienverwaltung“ im Azure-Portal zeigt jetzt den Zuweisungsstatus von Standardrichtlinien an.](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 Vorschauempfehlungen hinzugefügt, um die Abdeckung des Azure Security-Vergleichstests zu erhöhen

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. [Weitere Informationen zum Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md)

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

Da Azure Security Center wächst, wurden mehr Erweiterungen entwickelt, und Security Center kann eine umfangreichere Liste von Ressourcentypen überwachen. Die Tools für die automatische Bereitstellung wurden nun erweitert, um durch die Nutzung der Funktionen von Azure Policy weitere Erweiterungen und Ressourcentypen zu unterstützen.

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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Die Empfehlung „Auf Ihren Computern sollten Systemupdates installiert werden“ enthält jetzt Unterempfehlungen.

Die Empfehlung **Auf Ihren Computern sollten Systemupdates installiert werden** wurde erweitert. Die neue Version enthält Unterempfehlungen für jedes fehlende Update und bietet folgende Verbesserungen:

- Eine neu gestaltete Umgebung auf den Azure Security Center-Seiten des Azure-Portals. Die Seite mit den Empfehlungsdetails für **Auf Ihren Computern sollten Systemupdates installiert werden** enthält die unten gezeigte Liste der Ergebnisse. Wenn Sie ein einzelnes Ergebnis auswählen, wird die Detailansicht mit einem Link zu den Informationen zur Risikominderung und einer Liste der betroffenen Ressourcen geöffnet.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Öffnen einer der Unterempfehlungen auf der Benutzeroberfläche des Portals für die aktualisierte Empfehlung":::

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

Das Dashboard enthält einen Standardsatz gesetzlicher Standards. Falls einer der angegebenen Standards für Ihre Organisation nicht relevant ist, ist es nun problemlos möglich, diesen aus der Benutzeroberfläche für ein Abonnement zu entfernen. Standards können nur auf der Ebene des *Abonnements* entfernt werden, nicht der für den Verwaltungsgruppenbereich.

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

Wir haben eine aktualisierte Benutzeroberfläche für die Portalseiten von Security Center veröffentlicht. Die neuen Seiten enthalten eine neue Übersicht und Dashboards für Sicherheitsbewertung, Ressourcenbestand und Azure Defender.

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

Ab dem 1. Oktober 2020 beginnen wir damit, den Schutz von Ressourcen für diese Dienste abzurechnen.

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

Sicherheitsbezogene Fehlkonfigurationen sind eine häufige Ursache für Sicherheitsincidents. In Security Center ist jetzt ein Feature verfügbar, mit dem Sie Fehlkonfigurationen neuer Ressourcen dank bestimmter Empfehlungen *verhindern* können. 

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

Das Feature „Podsicherheitsrichtlinie“ (Vorschauversion) wird als veraltet eingestuft und steht nach dem 15. Oktober 2020 zugunsten von Azure Policy für AKS nicht mehr zur Verfügung.

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