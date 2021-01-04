---
title: 'Azure-Sicherheitsvergleichstest V2: Protokollierung und Bedrohungserkennung'
description: 'Azure-Sicherheitsvergleichstest V2: Protokollierung und Bedrohungserkennung'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 99e7a8fcb34c963cfab366f1913508a7ff3f4a51
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408701"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>Sicherheitskontrolle V2: Protokollierung und Bedrohungserkennung

Die Protokollierung und Bedrohungserkennung umfasst alle Steuerelemente zum Erkennen von Bedrohungen in Azure und zum Aktivieren, Sammeln und Speichern von Überwachungsprotokollen für Azure-Dienste. Dazu gehört das Aktivieren von Erkennungs-, Untersuchungs- und Wiederherstellungsprozessen mit Steuerelementen, um mithilfe von nativer Bedrohungserkennung hochwertige Warnungen in Azure-Diensten zu generieren. Zudem werden mit Azure Monitor Protokolle gesammelt, die Sicherheitsanalyse wird mit Azure Sentinel zentralisiert, die Zeit wird synchronisiert sowie Protokolle aufbewahrt. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Aktivieren der Bedrohungserkennung für Azure-Ressourcen

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

Stellen Sie sicher, dass Sie unterschiedliche Arten von Azure-Ressourcen nach potenziellen Bedrohungen und Anomalien überwachen. Das Ziel sollten möglichst hochwertige Warnungen sein. Damit verringern Sie die Anzahl falsch positiver Ergebnisse, die später von Analysten aussortiert werden müssen. Die Quellen von Warnungen können Protokolldaten, Agents oder andere Daten darstellen.

Verwenden Sie die integrierte Funktion zur Bedrohungserkennung in Azure Security Center, die auf der Überwachung der Telemetrie von Azure-Diensten und der Analyse von Dienstprotokollen basiert. Die Daten werden mit dem Log Analytics-Agent gesammelt. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle im System und kopiert die Daten zur Analyse in den Arbeitsbereich. 

Zusätzlich können Sie mithilfe von Azure Sentinel Analyseregeln erstellen, anhand derer in der gesamten Umgebung nach Bedrohungen gesucht wird, die bestimmten Kriterien entsprechen. Bei Übereinstimmung mit den Kriterien werden durch die Regeln Incidents generiert, die Sie einzeln untersuchen können. Sie können in Azure Sentinel auch Threat Intelligence von Drittanbietern importieren, um dessen Funktion zur Bedrohungserkennung zu verbessern. 

- [Bedrohungsschutz in Azure Security Center](../../security-center/azure-defender.md)

- [Sicherheitswarnungen von Azure Security Center (Referenzhandbuch)](../../security-center/alerts-reference.md)

- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](../../sentinel/tutorial-detect-threats-custom.md)

- [Cyber Threat Intelligence mit Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Aktivieren der Bedrohungserkennung für die Identitäts- und Zugriffsverwaltung in Azure

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-2 | 6,8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD stellt die folgenden Benutzerprotokolle bereit, die Sie in der Azure AD-Berichterstellung anzeigen oder mit Azure Monitor, Azure Sentinel oder anderen SIEM- und Überwachungstools integrieren können, um komplexere Anwendungsfälle für Überwachung und Analyse zu erhalten. 
-   Anmeldungen: Der Bericht „Anmeldungen“ enthält Informationen zur Nutzung von verwalteten Anwendungen und Aktivitäten der Benutzeranmeldung.

-   Überwachungsprotokolle: Ermöglichen die Nachverfolgung sämtlicher Änderungen, die von verschiedenen Features in Azure AD vorgenommen wurden. Hierzu zählen unter anderem Änderungen an Ressourcen in Azure AD, z. B. das Hinzufügen oder Entfernen von Benutzern, Apps, Gruppen, Rollen und Richtlinien.

-   Riskante Anmeldungen: Eine riskante Anmeldung ist ein Indikator für einen Anmeldeversuch von einem Benutzer, der nicht der rechtmäßige Besitzer eines Benutzerkontos ist.

-   Benutzer mit Risikomarkierung: Ein Benutzer mit Risikomarkierung ist ein Indikator für ein ggf. kompromittiertes Benutzerkonto.

Auch Azure Security Center kann bestimmte verdächtige Aktivitäten melden, wie z. B. eine übermäßige Anzahl fehlgeschlagener Authentifizierungsversuche oder veraltete Konten im Abonnement. Zusätzlich zur grundlegenden Sicherheitsüberwachung kann das Threat Protection-Modul von Azure Security Center auch ausführlichere Sicherheitswarnungen von einzelnen Azure-Computeressourcen (z. B. virtuelle Computer, Container, App Service), Datenressourcen (z. B. SQL-Datenbank und SQL-Speicher) und Azure-Dienstebenen sammeln. So erhalten Sie Einblick in Kontoanomalien innerhalb einzelner Ressourcen.

- [Berichte zu Überwachungsaktivitäten in Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Aktivieren von Azure Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)

- [Bedrohungsschutz in Azure Security Center](../../security-center/azure-defender.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivieren der Protokollierung für Azure-Netzwerkaktivitäten

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-3 | 9.3, 12.2, 12.5, 12.8 | AU-3, AU-6, AU-12, SI-4 |

Aktivieren und Sammeln Sie für die Sicherheitsanalyse NSG-Ressourcenprotokolle (Netzwerksicherheitsgruppe), NSG-Datenflussprotokolle, Azure Firewall-Protokolle sowie WAF-Protokolle (Web Application Firewall), um die Untersuchung von Incidents, die Bedrohungssuche und die Generierung von Sicherheitswarnungen zu unterstützen. Sie können die Datenflussprotokolle an einen Log Analytics-Arbeitsbereich von Azure Monitor senden und dann mithilfe von Traffic Analytics Einblicke ermöglichen. Stellen Sie sicher, dass es sich bei den gesammelten Protokollen um DNS-Abfragen handelt, um die Korrelation mit anderen Netzwerkdaten zu unterstützen.

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall-Protokolle und -Metriken](../../firewall/logs-and-metrics.md)

- [Aktivieren und Verwenden von Traffic Analytics](../../network-watcher/traffic-analytics.md)

- [Überwachung mit Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure-Netzwerküberwachungslösungen in Azure Monitor](../../azure-monitor/insights/azure-networking-analytics.md)

- [Sammeln von Erkenntnissen zu Ihrer DNS-Infrastruktur mit der DNS Analytics-Lösung](../../azure-monitor/insights/dns-analytics.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivieren der Protokollierung für Azure-Ressourcen

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-4 | 6.2, 6.3, 8.8 | AU-3, AU-12 |

Aktivieren Sie die Protokollierung für Azure-Ressourcen, um die Anforderungen hinsichtlich Compliance, Bedrohungserkennung und -suche sowie Untersuchung von Incidents zu erfüllen. 

Sie können mithilfe von Azure Security Center und Azure Policy Ressourcenprotokolle und die Erfassung von Protokolldaten für Azure-Ressourcen aktivieren, um Zugriff auf Überwachungs-, Sicherheits- und Ressourcenprotokolle zu erhalten. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente. 

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../../azure-monitor/platform/platform-logs-overview.md)

- [Grundlegendes zur Datensammlung in Azure Security Center](../../security-center/security-center-enable-data-collection.md)

**Verantwortlichkeit** : Shared

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

Infrastruktur- und Endpunktsicherheit 

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Zentralisieren der Verwaltung und Analyse von Sicherheitsprotokollen

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-5 | 6.5, 6.6 | AU-3, SI-4 |

Zentralisieren Sie die Speicherung und Analyse von Protokollen, um eine Korrelation zu ermöglichen. Stellen Sie sicher, dass jeder Protokollquelle ein Datenbesitzer, eine Zugriffsanleitung, ein Speicherort, die für die Verarbeitung und den Zugriff verwendeten Tools sowie Anforderungen zur Datenaufbewahrung zugewiesen werden.

Stellen Sie sicher, dass die Azure-Aktivitätsprotokolle in die zentrale Protokollierung integriert werden. Erfassen von Protokollen über Azure Monitor zum Aggregieren von Sicherheitsdaten, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.

Zusätzlich können Sie auch Daten in Azure Sentinel oder der SIEM-Lösung eines Drittanbieters aktivieren und integrieren.

Viele Unternehmen verwenden Azure Sentinel für „heiße“ Daten, die häufig verwendet werden, und Azure Storage für seltener verwendete „kalte“ Daten. 

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../../azure-monitor/platform/diagnostic-settings.md)

- [Durchführen des Onboardings für Azure Sentinel](../../sentinel/quickstart-onboard.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: Konfigurieren der Aufbewahrungsdauer von Protokollen im Speicher

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

Konfigurieren Sie die Aufbewahrungsdauer der Protokolle entsprechend Ihrer Compliance, Vorschriften und geschäftlichen Anforderungen. 

In Azure Monitor können Sie den Aufbewahrungszeitraum des Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihres Unternehmens festlegen. Verwenden Sie für langfristige Speicherungen und Archivierungen Konten von Azure Storage, Data Lake oder des Log Analytics-Arbeitsbereichs.

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Exportieren von Azure Security Center-Warnungen und -Empfehlungen](../../security-center/continuous-export.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: Verwenden von genehmigten Zeitsynchronisierungsquellen

| Azure-ID | CIS Controls v7.1 ID(s) | ID(s) von NIST SP800-53 r4 |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft verwaltet für die meisten PaaS- und SaaS-Dienste in Azure Zeitquellen. Verwenden Sie für Ihre virtuellen Computer zur Zeitsynchronisierung den NTP-Standardserver (Network Time Protocol) von Microsoft, sofern keine spezifischen Anforderungen vorliegen.  Sollten Sie einen eigenen NTP-Server einrichten müssen, schützen Sie den UDP-Dienstport 123.

Alle Protokolle, die in Azure von Ressourcen generiert werden, enthalten Zeitstempel der angegebenen Standardzeitzone.

- [Konfigurieren der Zeitsynchronisierung für Azure Windows-Computeressourcen](../../virtual-machines/windows/time-sync.md)

- [Konfigurieren der Zeitsynchronisierung für Azure Linux-Computeressourcen](../../virtual-machines/linux/time-sync.md)

- [Deaktivieren eingehender UDP für Azure-Dienste](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**Verantwortlichkeit** : Shared

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Richtlinien und Standards](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [Anwendungssicherheit und DevSecOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)