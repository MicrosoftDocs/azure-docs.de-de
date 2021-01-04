---
title: 'Azure-Sicherheitsvergleichstest V2: Reaktion auf Vorfälle'
description: 'Azure-Sicherheitsvergleichstest V2: Reaktion auf Vorfälle'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 1ba7391b3b524f7532101083a444aab6059e8a10
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408754"
---
# <a name="security-control-v2-incident-response"></a>Sicherheitskontrolle V2: Reaktion auf Vorfälle

Reaktion auf Vorfälle umfasst Kontrollen im Lebenszyklus der Reaktion auf Vorfälle: Vorbereitung, Erkennung und Analyse, Eindämmung und Aktivitäten nach dem Vorfall. Dazu gehört die Verwendung von Azure-Diensten wie Azure Security Center und Sentinel, um den Prozess der Reaktion auf Vorfälle zu automatisieren.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Vorbereitung – Aktualisieren des Prozesses zur Reaktion auf Vorfälle für Azure

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Stellen Sie sicher, dass Ihre Organisation über Prozesse verfügt, um auf Sicherheitsvorfälle zu reagieren, dass diese Prozesse für Azure aktualisiert wurden und dass sie regelmäßig trainiert werden, um die Bereitschaft zu gewährleisten.

- [Implement security across the enterprise environment](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions) (Implementieren von Sicherheit in der gesamten Unternehmensumgebung)

- [Referenzleitfaden für die Reaktion auf Vorfälle](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Vorbereitung – Einrichten von Ereignisbenachrichtigungen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-2 | 19.5 | IR-4, IR-5, IR-6, IR-8 |

Richten Sie Kontaktinformationen für Sicherheitsvorfälle im Azure Security Center ein. Microsoft wendet sich unter den angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Sie haben auch die Möglichkeit, die Warnungen und Benachrichtigungen bei Vorfällen in verschiedenen Azure-Diensten entsprechend Ihrer Anforderungen bezüglich der Reaktion auf Vorfälle anzupassen. 

- [Festlegen der Kontaktinformationen in Azure Security Center](../../security-center/security-center-provide-security-contact-details.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Erkennung und Analyse – Erstellen von Vorfällen basierend auf Warnungen mit hoher Qualität

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-3 | 19.6 | IR-4, IR-5 |

Stellen Sie sicher, dass Sie über einen Prozess zum Erstellen hochwertiger Warnungen und zum Messen der Qualität von Warnungen verfügen. Auf diese Weise können Sie Lehren aus vergangenen Vorfällen ziehen und Warnungen für Analysten priorisieren, damit diese keine Zeit mit falsch-positiven Ergebnissen verschwenden. 

Qualitativ hochwertige Warnungen können auf der Grundlage von Erfahrungen aus früheren Vorfällen, validierten Communityquellen und Tools zur Generierung und Bereinigung von Warnmeldungen durch Verschmelzung und Korrelation verschiedener Signalquellen erstellt werden. 

Das Azure Security Center bietet qualitativ hochwertige Warnungen für viele Azure-Ressourcen. Sie können den ASC-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen. Mit Azure Sentinel können Sie erweiterte Warnregeln erstellen, um Vorfälle automatisch für eine Untersuchung zu generieren. 

Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über das Exportfeature, um Risiken für Azure-Ressourcen zu ermitteln. Exportieren Sie Warnungen und Empfehlungen entweder manuell oder fortlaufend, kontinuierlich.

- [Konfigurieren des Exports](../../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Erkennung und Analyse – Untersuchen eines Vorfalls

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Stellen Sie sicher, dass Analysten bei der Untersuchung potenzieller Vorfälle verschiedene Datenquellen abfragen und verwenden können, um eine vollständige Übersicht über das Geschehnis zu erhalten. Es empfiehlt sich, verschiedene Protokolle zu sammeln, um die Aktivitäten eines potenziellen Angreifers über das gesamte Kill Chain-Spektrum hinweg nachzuverfolgen und Schwachpunkte zu vermeiden.  Stellen Sie außerdem sicher, dass Erkenntnisse und Erfahrungen für andere Analysten und als zukünftige Referenzen erfasst werden.  

Zu den zu untersuchenden Datenquellen gehören die zentralisierten Protokollierungsquellen, die bereits von den zum Umfang gehörige Diensten und ausgeführten Systemen gesammelt werden, aber auch andere:

- Netzwerkdaten: Verwenden Sie die Datenflussprotokolle von Netzwerksicherheitsgruppen sowie Azure Network Watcher und Azure Monitor, um Netzwerkflussprotokolle und andere Analyseinformationen zu erfassen. 

- Momentaufnahmen von laufenden Systemen: 

    - Verwenden Sie die Momentaufnahmenfunktion des virtuellen Azure-Computers, um eine Momentaufnahme der Festplatte des laufenden Systems zu erstellen. 

    - Verwenden Sie die native Speicherabbildfunktion des Betriebssystems, um eine Momentaufnahme des Arbeitsspeichers des laufenden Systems zu erstellen.

    - Verwenden Sie das Momentaufnahmenfeature der Azure-Dienste oder die Funktion Ihrer Software, um Momentaufnahmen der laufenden Systeme zu erstellen.

Azure Sentinel bietet umfangreiche Datenanalysen über praktisch jede Protokollquelle sowie ein Fallverwaltungsportal zum Verwalten des gesamten Lebenszyklus von Vorfällen. Intelligenceinformationen während einer Untersuchung können zu Verfolgungs- und Berichtszwecken mit einem Vorfall verknüpft werden. 

- [Momentaufnahme des Datenträgers eines Windows-Computers](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Momentaufnahme des Datenträgers eines Linux-Computers](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure-Supportdiagnoseinformationen und Speicherabbilderfassung](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- Lesen Sie [Untersuchen von Incidents mit Azure Sentinel](../../sentinel/tutorial-investigate-cases.md).

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Erkennung und Analyse – Priorisieren von Vorfällen

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Stellen Sie den Analysten Kontext bereit, auf welche Vorfälle sie sich zuerst konzentrieren sollten, je nach Schweregrad und Ressourcensensitivität. 

Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Ressourcen außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren – insbesondere solche, von denen vertrauliche Daten verarbeitet werden.  Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Sicherheitswarnungen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../../azure-resource-manager/management/tag-resources.md)

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Eindämmung, Ausmerzung und Wiederherstellung – Automatisieren der Behandlung von Vorfällen

| Azure-ID | ID(s) von CIS-Steuerelementen v7.1 | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatisieren Sie manuelle, sich wiederholende Aufgaben, um die Antwortzeit zu beschleunigen und die Belastung der Analysten zu verringern. Manuelle Aufgaben dauern länger, verlangsamen jeden Vorfall und reduzieren die Anzahl der Vorfälle, die ein Analyst bewältigen kann. Manuelle Aufgaben erhöhen auch die Ermüdung der Analytiker. Dadurch erhöht sich das Risiko menschlicher Fehler, die zu Verzögerungen führen, und es verschlechtert sich die Fähigkeit der Analytiker, sich effektiv auf komplexe Aufgaben zu konzentrieren. Verwenden Sie die Workflowautomatisierungsfeatures in Azure Security Center und Azure Sentinel, um automatisch Aktionen auszulösen oder ein Playbook auszuführen, um auf eingehende Sicherheitswarnungen zu reagieren. Das Playbook führt Aktionen aus, wie das Versenden von Benachrichtigungen, das Deaktivieren von Konten und das Isolieren problematischer Netzwerke. 

- [Konfigurieren der Workflowautomatisierung in Security Center](../../security-center/workflow-automation.md)

- [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- Machen Sie sich mit dem [Einrichten automatisierter Reaktionen auf Bedrohungen in Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md) vertraut.

**Verantwortlichkeit** : Kunde

**Sicherheitsverantwortliche beim Kunden** ( [weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitsvorgänge](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Vorbereitung auf Vorfälle](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Threat Intelligence](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)