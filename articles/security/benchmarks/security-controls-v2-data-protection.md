---
title: 'Azure-Sicherheitsvergleichstest V2: Datenschutz'
description: Datenschutz im Azure-Sicherheitsvergleichstest V2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8e12a74ee689ba7b013b1c4a6881deac78680be0
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701047"
---
# <a name="security-control-v2-data-protection"></a>Sicherheitskontrolle V2: Datenschutz

Datenschutz deckt die Steuerung des Schutzes von Daten im Ruhezustand, während der Übertragung und über autorisierte Zugriffsmechanismen ab. Dies umfasst das Ermitteln, Klassifizieren, Schützen und Überwachen vertraulicher Datenressourcen über Zugriffssteuerung, Verschlüsselung und Protokollierung in Azure.

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Daten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-1 | 13.1, 14.5, 14.7 | SC-28 |

Ermitteln, klassifizieren und bezeichnen Sie Ihre vertraulichen Daten, sodass Sie die entsprechenden Steuerungen entwerfen können, um sicherzustellen, dass vertrauliche Daten von den Technologiesystemen der Organisation sicher gespeichert, verarbeitet und übertragen werden. 

Verwenden Sie Azure Information Protection (und das zugehörige Überprüfungstool) für vertrauliche Informationen in Office-Dokumenten in Azure-, lokalen, Office 365- und anderen Speicherorten. 

Sie können Azure SQL Information Protection verwenden, um die Klassifizierung und Bezeichnung von Informationen zu vereinfachen, die in Azure SQL-Datenbanken gespeichert sind.

- [Markieren vertraulicher Informationen mit Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementieren von Azure SQL Data Discovery](../../azure-sql/database/data-discovery-and-classification-overview.md)

**Verantwortlichkeit**: Shared

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [Datensicherheit](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2: Schützen von vertraulichen Daten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-2 | 13.2, 2.10 | SC-7, AC-4 |

Schützen Sie vertrauliche Daten, indem Sie den Zugriff mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC), der netzwerkbasierten Zugriffssteuerungen und bestimmter Steuerungen in Azure-Diensten (z. B. Verschlüsselung in SQL und anderen Datenbanken) einschränken. 

Um eine konsistente Zugriffssteuerung sicherzustellen, sollten alle Zugriffssteuerungstypen an der Segmentierungsstrategie des Unternehmens ausgerichtet werden. Bei der Segmentierungsstrategie des Unternehmens sollte auch der Speicherort vertraulicher und unternehmenskritischer Daten und Systeme berücksichtigt werden.

Für die zugrunde liegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Gefährdung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von Standard-Datenschutzsteuerungen und -funktionen implementiert.

- [Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/overview.md)

- [Grundlegendes zum Schutz von Kundendaten in Azure](../fundamentals/protection-customer-data.md)

**Verantwortlichkeit**: Shared

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Datensicherheit](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Überwachung auf nicht autorisierte Übertragung vertraulicher Daten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-3 | 13.3 | AC-4, SI-4 |

Führen Sie eine Überwachung auf nicht autorisierte Übertragung von Daten an Speicherorte außerhalb der Sichtbarkeit und Kontrolle des Unternehmens durch. Dies umfasst in der Regel die Überwachung anomaler Aktivitäten (große oder ungewöhnliche Übertragungen), die auf eine nicht autorisierte Datenexfiltration hindeuten können. 

Azure Storage Advanced Threat Protection (ATP) und Azure SQL ATP können bei einer anomalen Übertragung von Informationen, die möglicherweise auf nicht autorisierte Übertragungen vertraulicher Informationen hindeutet, eine Warnung ausgeben. 

Azure Information Protection (AIP) bietet Überwachungsfunktionen für Informationen, die klassifiziert und bezeichnet wurden. 

Wenn Compliance hinsichtlich Datenverlust (Data Loss Prevention, DLP) erforderlich ist, können Sie eine hostbasierte DLP-Lösung verwenden, um Erkennungs- und/oder präventive Steuerungsmaßnahmen zu erzwingen und so die Datenexfiltration verhindern.

- [Aktivieren von Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [Aktivieren von Azure Storage ATP](../../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Verantwortlichkeit**: Shared

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security) 

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Verschlüsseln vertraulicher Informationen während der Übertragung

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

Als Ergänzung zu Zugriffssteuerungen sollten die Daten während der Übertragung durch Verschlüsselung vor Out-of-Band-Angriffen (z. B. Erfassung des Datenverkehrs) geschützt werden, um sicherzustellen, dass Angreifer die Daten nicht einfach lesen oder ändern können. 

Obwohl dies bei Datenverkehr in privaten Netzwerken optional ist, ist es für den Datenverkehr in externen und öffentlichen Netzwerken von entscheidender Bedeutung. Stellen Sie bei HTTP-Datenverkehr sicher, dass alle Clients, die Verbindungen mit Ihren Azure-Ressourcen herstellen, TLS 1.2 oder höher aushandeln können. Verwenden Sie für die Remoteverwaltung SSH (für Linux) bzw. RDP/TLS (für Windows) anstelle eines unverschlüsselten Protokolls. Veraltete SSL-, TLS- und SSH-Versionen und -Protokolle sowie schwache Verschlüsselungsverfahren sollten deaktiviert werden.  

Azure ermöglicht standardmäßig die Verschlüsselung von Daten während der Übertragung zwischen Azure-Rechenzentren. 

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informationen zur TLS-Sicherheit](/security/engineering/solving-tls1-problem)

- [Doppelte Verschlüsselung für Azure-Daten während der Übertragung](../fundamentals/double-encryption.md#data-in-transit)

**Verantwortlichkeit**: Shared

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [Datensicherheit](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: Verschlüsseln vertraulicher ruhender Daten

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| DP-5 | 14.8 | SC-28, SC-12 |

Als Ergänzung zu Zugriffssteuerungen sollten ruhende Daten durch Verschlüsselung vor Out-of-Band-Angriffen (z. B. Zugriffen auf den zugrunde liegenden Speicher) geschützt werden. Dadurch wird sichergestellt, dass die Daten von Angreifern nicht einfach gelesen oder geändert werden können. 

Azure bietet standardmäßig Verschlüsselung für ruhende Daten. Bei streng vertraulichen Daten stehen Ihnen ggf. Optionen zur Verfügung, um eine zusätzliche Verschlüsselung im Ruhezustand für alle Azure-Ressourcen zu implementieren. Azure verwaltet standardmäßig Ihre Verschlüsselungsschlüssel, Azure bietet jedoch auch Optionen, mit denen Sie Ihre Schlüssel für bestimmte Azure-Dienste selbst verwalten können (kundenseitig verwaltete Schlüssel).

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [Konfigurieren der von Kunden verwalteten Verschlüsselungsschlüssel](../../storage/common/customer-managed-keys-configure-key-vault.md)

- [Tabelle für Verschlüsselungsmodell und Schlüsselverwaltung](../fundamentals/encryption-models.md)

- [Doppelte Verschlüsselung für ruhende Daten in Azure](../fundamentals/double-encryption.md#data-at-rest)

**Verantwortlichkeit**: Shared

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsarchitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Anwendungssicherheit und DevOps-Funktionen](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Datensicherheit](/azure/cloud-adoption-framework/organize/cloud-security-data-security)