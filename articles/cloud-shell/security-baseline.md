---
title: Azure-Sicherheitsbaseline für Cloud Shell
description: Die Cloud Shell-Sicherheitsbaseline enthält Anleitungen und Ressourcen für die Vorgehensweise bei der Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit definiert sind.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: fde7c7fa619c6d0a36ef0890f31375ac3933e6ee
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807664"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Azure-Sicherheitsbaseline für Cloud Shell

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 1.0](../security/benchmarks/overview-v1.md) auf Cloud Shell an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure.
Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Azure-Sicherheitsvergleichstest und die entsprechenden für Cloud Shell geltenden Empfehlungen definiert werden. Nicht auf Cloud Shell anwendbare **Kontrollen** wurden ausgeschlossen.

 
Die vollständige Zuordnung von Cloud Shell zum Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Cloud Shell-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Kunden können Azure Cloud Shell in einem kundeneigenen virtuellen Netzwerk bereitstellen.

Wenn Sie Azure Cloud Shell in einem kundeneigenen virtuellen Netzwerk bereitstellen, müssen Sie ein virtuelles Netzwerk erstellen oder ein bestehendes verwenden. Stellen Sie sicher, dass das gewählte virtuelle Netzwerk über eine Netzwerksicherheitsgruppe verfügt, die auf seine Subnetze angewendet wird, sowie über Netzwerkzugriffssteuerungen, die speziell für die vertrauenswürdigen Ports und Quellen Ihrer Anwendung konfiguriert sind.

- [Bereitstellen von Cloud Shell in einem Azure Virtual Network](private-vnet.md)

- [Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal](../virtual-network/tutorial-filter-network-traffic.md)

- [Bereitstellen und Konfigurieren von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die dieselbe Autorisierung verwendet, die auch für den Zugriff auf das Azure-Portal verwendet wird. In diesem Fall wird Sie das einmalige Anmelden (SSO) beim Azure-Portal auch für Cloud Shell authentifizieren. 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die dieselbe Autorisierung verwendet, die auch für den Zugriff auf das Azure-Portal verwendet wird. In diesem Fall wird jede MFA, die für die Verbindung mit dem Azure-Portal erforderlich ist, auch für Cloud Shell benötigt. 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages werden vom Cloud Shell-Team überwacht und aktualisiert.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind für die Ausführung automatisierter Scantools für Sicherheitsrisiken für Software verantwortlich, die in der Umgebung ausgeführt wird.  

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht verfügbar. Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages werden vom Cloud Shell-Team überwacht und aktualisiert.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind verantwortlich für die Verwaltung von Softwarepatches, die in ihrer Umgebung ausgeführt wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages werden vom Cloud Shell-Team überwacht und aktualisiert.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, Sicherheitsrisiken zu beheben, die durch ihre Sicherheitsrisikoscans für Software entdeckt werden. Exportieren Sie die Scanergebnisse in regelmäßigen Abständen und vergleichen Sie die Ergebnisse mit vorherigen Scans, um zu überprüfen, ob die Sicherheitsrisiken behoben wurden. Wenn Sie die von Azure Security Center vorgeschlagenen Empfehlungen zur Verwaltung von Sicherheitsrisiken verwenden, können Sie in das Portal der ausgewählten Lösung wechseln, um die historischen Scandaten anzuzeigen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages werden vom Cloud Shell-Team überwacht und aktualisiert.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, Sicherheitsrisiken zu beheben, die durch ihre Sicherheitsrisikoscans für Software entdeckt werden.  Verwenden Sie ein gängiges Risikobewertungsprogramm (z. B. Common Vulnerability Scoring System) oder die von Ihrem Scan-Tool eines Drittanbieters bereitgestellten Standardrisikobewertungen. 

- [NIST-Veröffentlichung: Common Vulnerability Scoring System](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert.  Der Kunde ist in der Lage, seine eigenen Tools in seinem eigenen Image entsprechend seinen organisatorischen Anforderungen zu installieren, und die Tools benötigen während der Installation keine `sudo`-Berechtigungen.

Kunden wird empfohlen, einen Bestand an genehmigter Software zu erstellen, die über Azure Cloud Shell gemäß den Anforderungen Ihres Unternehmens installiert wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Azure Cloud Shell ist ein kostenloser Dienst ohne kundeneigene Ressourcen.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert. 

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, in der Umgebung ausgeführte Softwareanwendungen zu überwachen, um sicherzustellen, dass sie gemäß den Richtlinien des Unternehmens genehmigt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Azure Cloud Shell ist ein kostenloser Dienst ohne kundeneigene Ressourcen.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert. 

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, in der Umgebung ausgeführte Softwareanwendungen zu überwachen, um sicherzustellen, dass nicht genehmigte Software gemäß den Richtlinien des Unternehmens verwaltet wird.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Azure Cloud Shell ist ein kostenloser Dienst ohne kundeneigene Ressourcen.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert.  Bestimmte Tools dürfen vom Kunden nicht entfernt werden.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Anwendungen in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, in der Umgebung ausgeführte Anwendungen zu überwachen, um sicherzustellen, dass sie gemäß den Richtlinien des Unternehmens genehmigt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht verfügbar. Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages werden vom Cloud Shell-Team überwacht und aktualisiert.

Azure Cloud Shell ermöglicht es Kunden, ihre eigenen Tools oder Software in ihrem eigenen Image nach ihren organisatorischen Anforderungen zu installieren.

Kunden sind dafür verantwortlich, einen Bestand an genehmigter Software zu führen, die in der Umgebung ausgeführt wird, um sicherzustellen, dass es sich um genehmigte Software gemäß den Richtlinien des Unternehmens handelt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Aktionen, die innerhalb von Cloud Shell ausgeführt werden, funktionieren genauso wie Aktionen, die mit denselben Tools oder Sprachen in einer lokalen Umgebung ausgeführt werden.  Aktionen von einzelnen Tools und Sprachen sollten eingeschränkt werden, Kunden können den Zugriff auf Cloud Shell oder die Optionen nicht einschränken, die einem Benutzer zur Verfügung stehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Azure Cloud Shell kann in einem virtuellen Netzwerk des Kunden isoliert werden.

- [Bereitstellen von Cloud Shell in einem Azure Virtual Network](private-vnet.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Cloud Shell gestattet Folgendes in der Cloud Shell-Umgebung: Ausführen, Erstellen und Hochladen von Skripts.  Es wird empfohlen, Anmeldeinformationen nach Azure Key Vault zu verschieben.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert.  Der Kunde ist in der Lage, seine eigenen Tools in seinem eigenen Image entsprechend seinen organisatorischen Anforderungen zu installieren, und die Tools benötigen während der Installation keine `sudo`-Berechtigungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Azure Cloud Shell ist eine browserbasierte Befehlszeilenumgebung, die für die interaktive Verwaltung von Cloudressourcen verwendet wird.  Jeder Kundencontainer ist kurzlebig, für jede Sitzung wird ein neuer Container verwendet.  Die Containerimages und Tools werden vom Cloud Shell-Team überwacht und aktualisiert.  Der Kunde ist in der Lage, seine eigenen Tools in seinem eigenen Image entsprechend seinen organisatorischen Anforderungen zu installieren, und die Tools benötigen während der Installation keine `sudo`-Berechtigungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Anleitung:** Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle für Ihre Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.
- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md) 
- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 
- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie sicher Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.
Kennzeichnen Sie außerdem die Abonnements (z. B. Produktion, Nicht-Produktion), und erstellen Sie ein Namenssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren.
- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md) 
- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.
- [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf die Kundendaten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.
- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.
- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md) 
- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.
- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.
- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).