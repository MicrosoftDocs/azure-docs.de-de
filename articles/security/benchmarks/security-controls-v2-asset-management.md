---
title: 'Azure-Sicherheitsvergleichstest V2: Ressorcenverwaltung'
description: 'Azure-Sicherheitsvergleichstest V2: Ressorcenverwaltung'
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8602450ef7df7f728fc5bdcda4f46ae30058fc94
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487795"
---
# <a name="security-control-v2-asset-management"></a>Sicherheitskontrolle V2: Ressourcenverwaltung

Die Ressourcenverwaltung deckt Kontrollen ab, mit denen die Sichtbarkeit und Governance der Sicherheit von Azure-Ressourcen sichergestellt werden soll. Dies umfasst Empfehlungen zu Berechtigungen für Sicherheitspersonal, den sicheren Zugriff auf den Ressourcenbestand und das Verwalten von Genehmigungen für Dienste und Ressourcen (Bestand, Nachverfolgung und Richtigkeit).

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Sicherstellen, dass das Sicherheitsteam Risiken für Ressourcen einsehen kann

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-1 | 1.1, 1.2 | CM-8, PM-5 |

Stellen Sie sicher, dass Sicherheitsteams die Berechtigungen „Sicherheitsleseberechtigter“ in Ihrem Azure-Mandanten und Ihren Abonnements erhalten, damit sie mit Azure Security Center Sicherheitsrisiken überwachen können. 

Abhängig davon, wie die Verantwortungsbereiche des Sicherheitsteams strukturiert sind, kann die Überwachung auf Sicherheitsrisiken unter die Verantwortung eines zentralen Sicherheitsteams oder eines lokalen Teams fallen. Aus diesem Grund müssen Sicherheitserkenntnisse und -risiken immer innerhalb einer Organisation zentral aggregiert werden. 

Die Berechtigungen der Rolle „Sicherheitsleseberechtigter“ können weit gefasst auf einen gesamten Mandanten (Stammverwaltungsgruppe) angewandt oder auf Verwaltungsgruppen oder bestimmte Abonnements beschränkt werden. 

Hinweis: Möglicherweise sind zusätzliche Berechtigungen erforderlich, um Einblick in Workloads und Dienste zu erhalten. 

- [Übersicht über die Rolle „Sicherheitsleseberechtigter“](../../role-based-access-control/built-in-roles.md#security-reader)

- [Übersicht über Azure-Verwaltungsgruppen](../../governance/management-groups/overview.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Sicherstellen, dass das Sicherheitsteam Zugriff auf den Ressourcenbestand und die Metadaten hat

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-2 | 1.1, 1.2,  1.4, 1.5,  9.1, 12.1 | CM-8, PM-5 |

Stellen Sie sicher, dass Sicherheitsteams Zugriff auf einen fortlaufend aktualisierten Bestand der Ressourcen in Azure haben. Sicherheitsteams benötigen diesen Bestand häufig, um die potenziellen Angriffsflächen ihrer Organisation im Hinblick auf neue Risiken zu evaluieren, sowie als Quelle für die kontinuierliche Verbesserung der Sicherheit. 

Das Azure Security Center-Bestandsfeature und Azure Resource Graph können alle Ressourcen in Ihren Abonnements abfragen und ermitteln, einschließlich Azure-Diensten, Anwendungen und Netzwerkressourcen.  

Organisieren Sie Ressourcen logisch gemäß der Taxonomie Ihrer Organisation mithilfe von Tags und anderen Metadaten in Azure (Name, Beschreibung und Kategorie).  

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md)

- [Verwalten des Ressourcenbestands in Azure Security Center](../../security-center/asset-inventory.md)

- [Weitere Informationen zum Taggen von Ressourcen finden Sie im „Leitfaden zur Entscheidungsfindung für Ressourcenbenennung und -markierung“.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Ausschließliche Verwendung genehmigter Azure-Dienste

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-3 | 2.3, 2.4 | CM-7, CM-8 |

Verwenden Sie Azure Policy, um die Dienste, die von Benutzern in Ihrer Umgebung bereitgestellt werden, zu überwachen und einzuschränken. Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Sie können auch mit Azure Monitor Regeln erstellen, mit denen Warnungen ausgelöst werden, wenn ein nicht genehmigter Dienst erkannt wird.

- [Konfigurieren und Verwalten von Azure Policy](../../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../../governance/policy/samples/index.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../../governance/resource-graph/first-query-portal.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Gewährleisten der Sicherheit der bei der Lebenszyklusverwaltung von Ressourcen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-4 | 2.3, 2.4, 2.5 | CM-7, CM-8, CM-10, CM-11 |

Richten Sie Sicherheitsrichtlinien ein, mit denen die Prozesse der Lebenszyklusverwaltung von Ressourcen auf potenziell schwerwiegende Änderungen überwacht werden, oder aktualisieren Sie sie. Diese Änderungen umfassen Änderungen an Identitätsanbietern und dem Zugriff, der Vertraulichkeit von Daten, der Netzwerkkonfiguration und der Zuweisung von Administratorberechtigungen.

Entfernen Sie Azure-Ressourcen, wenn diese nicht mehr benötigt werden.

- [Löschen von Azure-Ressourcengruppen und -Ressourcen](../../azure-resource-manager/management/delete-resource-group.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-5 | 2,9 | AC-3 |

Mithilfe des bedingten Azure AD-Zugriffs können Sie die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Ausschließliche Verwendung genehmigter Anwendungen in Computeressourcen

| Azure-ID | CIS Controls v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| AM-6 | 2.6, 2.7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Stellen Sie sicher, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

Verwenden Sie die adaptiven Anwendungssteuerungen in Azure Security Center (ASC), um eine Positivliste der Anwendungen zu ermitteln und zu generieren. Sie können die adaptiven Anwendungssteuerungen in Azure Security Center auch dazu verwenden sicherzustellen, dass nur autorisierte Software ausgeführt und die Ausführung jeglicher nicht autorisierter Software in Azure Virtual Machines blockiert wird.

Verwenden Sie das Feature Änderungsnachverfolgung und Bestand von Azure Automation, um die Sammlung von Bestandsinformationen von Ihren Windows- und Linux-VMs zu automatisieren. Softwarename, Version, Herausgeber und Aktualisierungszeit sind im Azure-Portal verfügbar. Um das Installationsdatum und andere Informationen abzurufen, aktivieren Sie die Diagnose auf Gastebene, und leiten Sie die Windows-Ereignisprotokolle an einen Log Analytics-Arbeitsbereich um.

Abhängig vom Skripttyp können Sie betriebssystemspezifische Konfigurationen oder Ressourcen von Drittanbietern verwenden, um die Möglichkeit der Benutzer zur Skriptausführung in Azure-Computeressourcen einzuschränken. 

Sie können auch eine Drittanbieterlösung verwenden, um nicht genehmigte Software zu ermitteln und zu identifizieren.

- [Verwenden der adaptiven Anwendungssteuerungen in Azure Security Center](../../security-center/security-center-adaptive-application.md)

- [Grundlegendes zu Azure Automation-Änderungsnachverfolgung und -Bestand](../../automation/change-tracking/overview.md)

- [Steuern der PowerShell-Skriptausführung in Windows-Umgebungen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Verantwortlichkeit**: Kunde

**Sicherheitsverantwortliche beim Kunden** ([weitere Informationen](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastruktur- und Endpunktsicherheit](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Statusverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [Sicherheitscomplianceverwaltung](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)