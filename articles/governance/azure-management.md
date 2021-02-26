---
title: Übersicht über die Verwaltung in Azure – Azure Governance
description: Dieser Artikel enthält eine Übersicht über die Verwaltungsbereiche für Azure-Anwendungen und -Ressourcen sowie Links zu Inhalten zu Azure-Verwaltungstools.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593924"
---
# <a name="what-are-the-azure-management-areas"></a>Was sind die Azure-Verwaltungsbereiche?

Governance in Azure ist ein Aspekt der Azure-Verwaltung. Dieser Artikel behandelt die verschiedenen Verwaltungsbereiche für die Bereitstellung und Verwaltung Ihrer Ressourcen in Azure.

„Verwaltung“ bezieht sich auf die Aufgaben und Prozesse, die zur Wartung Ihrer Geschäftsanwendungen erforderlich sind, sowie die Ressourcen, die sie unterstützen. Azure verfügt über viele Dienste und Tools, die zusammenwirken, um eine vollständige Verwaltung zu gewährleisten. Diese Dienste sind nicht nur für Ressourcen in Azure, sondern auch in anderen Clouds und lokal verfügbar. Das Verständnis der verschiedenen Tools und deren Zusammenspiel ist der erste Schritt bei der Gestaltung einer vollständigen Verwaltungsumgebung.

Das folgende Diagramm veranschaulicht die verschiedenen Bereiche der Verwaltung, die zum Warten einer Anwendung oder Ressource erforderlich sind. Diese unterschiedlichen Bereiche können als ein Lebenszyklus betrachtet werden. Jeder Bereich wird während der gesamten Lebensdauer einer Ressource in kontinuierlicher Folge benötigt. Dieser Ressourcenlebenszyklus beginnt mit der anfänglichen Bereitstellung, anschließend erfolgt der fortgesetzte Betrieb und zuletzt die Außerkraftsetzung.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Diagramm: Verwaltungsdisziplinen in Azure" border="false":::
   Das Diagramm zeigt die Elemente „Migrieren“, „Schützen“, „Absichern“, „Überwachen“, „Konfigurieren“ und „Steuern“ der kreisförmig angeordneten Dienstleistungen, die die Verwaltung und Governance in Azure unterstützen. Unter „Schützen“ werden die Unterelemente „Sicherheitsverwaltung“ und „Bedrohungsschutz“ aufgelistet. Unter „Absichern“ werden die Unterelemente „Sicherung“ und „Notfallwiederherstellung“ aufgelistet. Unter „Überwachen“ werden die Unterelemente „App-Überwachung, Infrastrukturüberwachung und Netzwerküberwachung“ und „Log Analytics und Diagnose“ aufgelistet. Unter „Konfigurieren“ werden die Unterelemente „Konfiguration“, „Updateverwaltung“, „Automatisierung“ und „Skripterstellung“ aufgelistet. Unter „Steuern“ werden die Unterelemente „Richtlinienverwaltung“ und „Kostenverwaltung“ aufgelistet.
:::image-end:::

Kein einzelner Azure-Dienst erfüllt die Anforderungen eines bestimmten Verwaltungsbereichs vollständig. Stattdessen wird jeder Bereich von mehreren Diensten umgesetzt, die zusammenarbeiten. Einige Dienste (z.B. Application Insights) bieten gezielte Überwachungsfunktionen für Webanwendungen. Andere (wie Azure Monitor-Protokolle) speichern die Verwaltungsdaten für andere Dienste. Diese Funktion ermöglicht es Ihnen, Daten verschiedener Typen zu analysieren, die von verschiedenen Diensten erfasst werden.

In den folgenden Abschnitten werden verschiedenen Verwaltungsbereiche kurz beschrieben und Links zu ausführlichen Inhalten zu den Azure-Hauptdiensten bereitgestellt, die für die jeweiligen Bereiche vorgesehen sind.

## <a name="monitor"></a>Überwachen

„Überwachung“ ist das Erfassen und Analysieren von Daten, um die Leistung, Integrität und Verfügbarkeit Ihrer Ressourcen zu überwachen. Eine effektive Überwachungsstrategie hilft Ihnen, den Betrieb von Komponenten zu verstehen und die Verfügbarkeit durch Benachrichtigungen zu steigern. Eine Übersicht über die Überwachung, die die verschiedenen verwendeten Dienste abdeckt, finden Sie unter [Überwachen von Azure-Anwendungen und -Ressourcen](../azure-monitor/overview.md).

## <a name="configure"></a>Konfigurieren

„Konfigurieren“ bezieht sich auf die ursprüngliche Bereitstellung und Konfiguration von Ressourcen sowie die laufende Wartung.
Die Automatisierung dieser Aufgaben ermöglicht Ihnen das Beseitigen von Redundanz, das Minimieren des (Zeit-) Aufwands und die Steigerung der Genauigkeit und Effizienz. [Azure Automation](../automation/automation-intro.md) stellt den Großteil der Dienste für die Automatisierung von Konfigurationsaufgaben bereit. Während Runbooks die Prozessautomatisierung übernehmen, unterstützt die Konfigurations- und Updateverwaltung das Verwalten der Konfiguration.

## <a name="govern"></a>Steuern

Governance stellt Mechanismen und Prozesse zum Beibehalten der Kontrolle über Ihre Anwendungen und Ressourcen in Azure bereit. Sie umfasst die Planung von Initiativen und das Setzen von strategischen Prioritäten.
Governance in Azure wird in erster Linie mit zwei Diensten implementiert. [Azure-Richtlinien](./policy/overview.md) ermöglicht es Ihnen, Richtliniendefinitionen zu erstellen, zuzuweisen und zu verwalten, um Regeln für Ihre Ressourcen durchzusetzen.
Dieses Feature gewährleistet die Konformität dieser Ressourcen mit Ihren Unternehmensstandards.
[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) ermöglicht es Ihnen, die Cloudnutzung und Ausgaben für Ihre Azure-Ressourcen und andere Cloudanbieter nachzuverfolgen.

## <a name="secure"></a>Sicher

Verwalten Sie die Sicherheit Ihrer Ressourcen und Daten. Ein Sicherheitsprogramm umfasst die Bewertung von Bedrohungen, das Erfassen und Analysieren von Daten und die Einhaltung von Vorschriften für Ihre Anwendungen und Ressourcen. Sicherheitsüberwachung und Bedrohungsanalysen werden von [Azure Security Center](../security-center/security-center-introduction.md) bereitgestellt. Dieses umfasst einheitliche Funktionen für die Sicherheitsverwaltung und den erweiterten Schutz vor Bedrohungen für Hybrid Cloud-Workloads. Umfassende Informationen zur Sicherheit in Azure sowie eine Anleitung zum sicheren Konfigurieren von Azure-Ressourcen finden Sie unter [Einführung in Azure Security](../security/fundamentals/overview.md).

## <a name="protect"></a>Schützen

„Schutz“ bezieht sich auf das Bereitstellen Ihrer Anwendungen und Daten auch bei Ausfällen, die außerhalb Ihrer Einflussmöglichkeiten liegen. Schutz in Azure wird durch zwei Dienste bereitgestellt. [Azure Backup](../backup/backup-overview.md) bietet die Sicherung und Wiederherstellung Ihrer Daten entweder in der Cloud oder lokal. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) bietet Geschäftskontinuität und sofortige Wiederherstellung im Katastrophenfall.

## <a name="migrate"></a>Migrieren

„Migration“ bezieht sich auf den Übergang von Workloads, die derzeit lokal ausgeführt werden, in die Azure-Cloud.
[Azure Migrate](../migrate/migrate-services-overview.md) ist ein Dienst für die Bewertung der Migrationseignung von lokalen virtuellen Computern zu Azure. Azure Site Recovery migriert virtuelle Computer [aus einer lokalen Umgebung](../site-recovery/migrate-tutorial-on-premises-azure.md) oder aus [Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) hilft Ihnen bei der Migration von Datenbankenquellen zu Azure Data-Plattformen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Governance finden Sie in den folgenden Artikeln:

- [Azure Governance-Hub](./index.yml)
- [Governance für die Einführung der Microsoft Cloud für Azure (Microsoft Cloud Adoption Framework)](/azure/cloud-adoption-framework/govern/)