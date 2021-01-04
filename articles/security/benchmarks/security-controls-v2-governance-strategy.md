---
title: 'Azure-Sicherheitsvergleichstest V2: Governance und Strategie'
description: 'Azure-Sicherheitsvergleichstest V2: Governance und Strategie'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: af9a86613b3aa29cc01b5ef0daa1f3854940deaf
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96534062"
---
# <a name="security-control-v2-governance-and-strategy"></a>Sicherheitskontrolle V2: Governance und Strategie

Governance und Strategie bietet Leitlinien für die Gewährleistung einer stimmigen Sicherheitsstrategie und eines dokumentierten Governance-Ansatzes zur Lenkung und Aufrechterhaltung der Sicherheitsgarantie, einschließlich Festlegung von Rollen und Verantwortlichkeiten für die verschiedenen Cloudsicherheitsfunktionen, einer einheitlichen technischen Strategie und der Unterstützung von Richtlinien und Standards.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definieren der Strategie für Ressourcenverwaltung und Datenschutz

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Stellen Sie sicher, dass Sie eine klare Strategie für die kontinuierliche Überwachung und den Schutz von Systemen und Daten dokumentieren und kommunizieren. Priorisieren Sie die Ermittlung, die Bewertung, den Schutz und die Überwachung unternehmenskritischer Daten und Systeme. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

- Datenklassifizierungsstandard in Übereinstimmung mit den Geschäftsrisiken

- Sicherheitsorganisationseinblick in Risiken und Ressourcenbestand 

- Genehmigung durch die Sicherheitsorganisation für die Nutzung der Azure-Dienste 

- Sicherheit von Ressourcen durch ihren gesamten Lebenszyklus

- Erforderliche Zugriffssteuerungsstrategie in Übereinstimmung mit der Organisationsdatenklassifizierung

- Verwendung von nativen Azure- und Drittanbieter-Funktionen für den Datenschutz

- Datenverschlüsselungsanforderungen für Anwendungsfälle während der Übertragung und für ruhende Anwendungsfälle

- Geeignete Verschlüsselungsstandards

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Empfehlung für eine Azure-Sicherheitsarchitektur: Speicher, Daten, Verschlüsselung](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundlegende Azure-Sicherheitsinformationen: Sicherheit, Verschlüsselung und Speicherung von Azure-Daten](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: Bewährte Methoden für Datensicherheit und Datenverschlüsselung in Azure](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure-Sicherheitsvergleichstest: Ressorcenverwaltung](security-controls-v2-asset-management.md)

- [Azure-Sicherheitsvergleichstest: Datenschutz](security-controls-v2-data-protection.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definieren der Strategie für Unternehmenssegmentierung

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Erstellen Sie eine unternehmensweite Strategie zum Segmentieren des Zugriffs auf Ressourcen durch eine Kombination aus Identität, Netzwerk, Anwendung, Abonnement, Verwaltungsgruppe und anderen Steuerelementen.

Wägen Sie die Notwendigkeit einer Sicherheitstrennung sorgfältig gegen die Notwendigkeit ab, den täglichen Betrieb der Systeme zu ermöglichen, die miteinander kommunizieren und auf Daten zugreifen müssen.

Stellen Sie sicher, dass die Segmentierungsstrategie einheitlich für alle Steuerelementtypen implementiert wird, einschließlich Netzwerksicherheit, Identitäts- und Zugriffsmodelle, Berechtigungs-/Zugriffsmodelle für Anwendungen sowie Steuerelemente für Benutzerprozesse.

- [Leitfaden für die Segmentierungsstrategie in Azure (Video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Leitfaden für die Segmentierungsstrategie in Azure (Dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Ausrichten der Netzwerksegmentierung an der Segmentierungsstrategie des Unternehmens](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definieren der Strategie für die Sicherheitsstatusverwaltung

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Führen Sie kontinuierliche Messungen durch, und Mindern Sie die Risiken für Ihre individuellen Ressourcen und die Umgebung, in der diese gehostet werden. Priorisieren Sie Ressourcen mit hohem Wert und hoch exponierte Angriffsflächen, wie z. B. veröffentlichte Anwendungen, Eingangs- und Ausgangspunkte für Netzwerke, Benutzer- und Administratorendpunkte usw.

- [Azure-Sicherheitsvergleichstest: Verwaltung von Status und Sicherheitsrisiken](security-controls-v2-posture-vulnerability-management.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Ausrichten von Organisationsrollen, Zuständigkeiten und Verantwortlichkeiten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-4 | – | PL, PM |

Stellen Sie sicher, dass Sie eine klare Strategie für Rollen und Zuständigkeiten in ihrer Sicherheitsorganisation dokumentieren und kommunizieren. Priorisieren Sie durch Zuweisung klarer Verantwortlichkeiten für Sicherheitsentscheidungen, Schulung aller Beteiligten für das Modell der gemeinsamen Verantwortung und Schulung von technischen Teams hinsichtlich der Technologie zum Sichern der Cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen von Teams auf dem Weg zur Cloudsicherheit)

- [Azure Security Best Practice 2 – People: Educate Teams on Cloud Security Technology](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (Bewährte Methoden für die Sicherheit von Azure 1 – Personen: Schulen der Teams in Cloudsicherheitstechnologie)

- [Azure Security Best Practice 3 – Process: Assign Accountability for Cloud Security Decisions](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (Bewährte Methoden für die Sicherheit von Azure 1 – Prozess: Zuweisen von Verantwortlichkeit für Cloudsicherheitsentscheidungen)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Definieren einer Netzwerksicherheitsstrategie

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-5 | 9 | CA, SC |

Richten Sie einen Ansatz für die Azure-Netzwerksicherheit im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

- Zentralisieren der Verantwortlichkeit für Netzwerkverwaltung und -sicherheit

- Segmentierungsmodell für virtuelle Netzwerke, das auf die Segmentierungsstrategie des Unternehmens abgestimmt ist

- Wiederherstellungsstrategie in verschiedenen Bedrohungs- und Angriffsszenarien

- Strategie für Internet-Randpunkte, -Eingangspunkte und -Ausgangspunkte

- Strategie für Konnektivität zwischen Hybrid Cloud und lokalen Systemen

- Aktuelle Netzwerksicherheitsartefakte (z. B. Netzwerkdiagramme, Referenznetzwerkarchitektur)

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](security-controls-v2-network-security.md)

- [Die Netzwerksicherheit in Azure in der Übersicht](../fundamentals/network-overview.md)

- [Strategie für die Unternehmensnetzwerkarchitektur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definieren der Strategie für Identität und privilegierten Zugriff

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Richten Sie einen Ansatz für Azure-Identität und privilegierten Zugriff im Rahmen der Gesamtstrategie Ihrer Organisation für die Sicherheitszugriffssteuerung ein.  

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

- Zentralisiertes Identitäts- und Authentifizierungssystem mit Interkonnektivität zu anderen internen und externen Identitätssystemen

- Starke Authentifizierungsmethoden in verschiedenen Anwendungsfällen und Bedingungen

- Schutz stark privilegierter Benutzer

- Überwachung und Behandlung von Anomalien bei Benutzeraktivitäten  

- Benutzeridentität und Zugriffsüberprüfung und Abstimmungsprozess

Weitere Informationen finden Sie in den folgenden Referenzen:

- [Azure-Sicherheitsvergleichstest: Identitätsverwaltung](security-controls-v2-identity-management.md)

- [Azure-Sicherheitsvergleichstest: Privilegierter Zugriff](security-controls-v2-privileged-access.md)

- [Azure Security Best Practice 11 – Architecture. Single unified security strategy](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (Bewährte Methoden für die Sicherheit von Azure 11 – Architektur: Zentralisierte einheitliche Sicherheitsstrategie)

- [Übersicht über die Sicherheit der Azure-Identitätsverwaltung](../fundamentals/identity-management-overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definieren einer Strategie für Protokollierung und Reaktion auf Bedrohungen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Richten Sie eine Strategie für Protokollierung und Reaktion auf Bedrohungen zur schnellen Erkennung und Behebung von Bedrohungen bei gleichzeitiger Erfüllung von Complianceanforderungen ein. Priorisieren Sie, indem Sie Analysten qualitativ hochwertige Warnungen und nahtlose Umgebungen bereitstellen, sodass sie sich auf Bedrohungen konzentrieren können und sich nicht um Integration und manuelle Schritte kümmern müssen. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

- Rolle und Verantwortlichkeiten der Organisation für Sicherheitsvorgänge (SecOps) 

- Ein klar definierter Prozess zur Reaktion auf Vorfälle gemäß NIST oder einem anderen Branchenframework 

- Erfassung und Aufbewahrung von Protokollen zur Unterstützung von Bedrohungserkennung, Reaktion auf Vorfälle und Complianceanforderungen

- Zentralisierte Sichtbarkeit von und Korrelationsinformationen zu Bedrohungen unter Verwendung von SIEM, nativen Azure-Funktionen und anderen Quellen 

- Kommunikations- und Benachrichtigungsplan mit Ihren Kunden, Lieferanten und öffentlichen Interessengruppen

- Verwendung von nativen Azure-Plattformen und Plattformen von Drittanbietern für die Behandlung von Vorfällen, wie z. B. Protokollierung und Bedrohungserkennung, Forensik sowie Reduzierung und Bekämpfung von Angriffen

- Prozesse für den Umgang mit Vorfällen und Aktivitäten nach Vorfällen, wie z. B. Erkenntnisse und Beweissicherung

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Azure-Sicherheitsvergleichstest: Protokollierung und Bedrohungserkennung](security-controls-v2-logging-threat-detection.md)

- [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4 – Process. Update Incident Response Processes for Cloud](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions) (Bewährte Methoden für die Sicherheit von Azure 4 – Prozess: Aktualisieren der Prozesse zur Reaktion auf Vorfälle für die Cloud)

- [Azure Adoption Framework, Entscheidungshilfe für Protokollierung und Berichterstattung](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure auf Unternehmensebene, Verwaltung und Überwachung](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: Definieren der Sicherungs- und Wiederherstellungsstrategie

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-8 | 10 | CP |

Richten Sie eine Azure-Sicherungs- und Wiederherstellungsstrategie für Ihre Organisation ein. 

Diese Strategie sollte dokumentierte Anleitungen, Richtlinien und Standards für die folgenden Elemente umfassen: 

- Definitionen für Recovery Time Objective (RTO) und Recovery Point Objective (RPO) in Übereinstimmung mit den Resilienzzielen für Ihr Unternehmen

- Redundanzentwurf in Ihren Anwendungen und dem Infrastruktursetup

- Schutz der Sicherung mithilfe von Zugriffssteuerung und Datenverschlüsselung

Weitere Informationen finden Sie in den folgenden Referenzen:
- [Azure-Sicherheitsvergleichstest: Sicherung und Wiederherstellung](security-controls-v2-backup-recovery.md)

- [Microsoft Azure Well-Architected Framework: Sicherung und Notfallwiederherstellung von Azure-Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Azure Adoption Framework: Geschäftskontinuität und Notfallwiederherstellung](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Alle Projektbeteiligten](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)