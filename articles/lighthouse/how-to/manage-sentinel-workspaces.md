---
title: Verwalten von Azure Sentinel-Arbeitsbereichen im großen Maßstab
description: Erfahren Sie, wie Sie Azure Sentinel auf delegierten Kundenressourcen effektiv verwalten.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 91e2f875aa2fc067420c0c6eda4e7dd56bd2b088
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424102"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Verwalten von Azure Sentinel-Arbeitsbereichen im großen Maßstab

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von Vorgängen für mehrere Azure Active Directory-Mandanten (Azure AD) im großen Maßstab, wodurch Verwaltungsaufgaben effizienter werden.

Azure Sentinel bietet Sicherheits- und Bedrohungsanalysen und stellt eine zentrale Lösung für die Warnungs- und Bedrohungserkennung, die proaktive Suche sowie die Reaktion auf Bedrohungen bereit. Mit Azure Lighthouse können Sie mehrere Azure Sentinel-Arbeitsbereiche mandantenübergreifend im großen Maßstab verwalten. Dies ermöglicht Szenarien wie das Ausführen von Abfragen über mehrere Arbeitsbereiche hinweg oder das Erstellen von Arbeitsmappen, um Daten aus Ihren verbundenen Datenquellen zu visualisieren und zu überwachen und so Erkenntnisse zu gewinnen. IP-Informationen, wie etwa Abfragen und Playbooks, verbleiben in Ihrem verwaltenden Mandanten, können aber verwendet werden, um Sicherheitsverwaltung in den Mandanten des Kunden durchzuführen.

Dieses Thema bietet eine Übersicht zur Verwendung von [Azure Sentinel](../../sentinel/overview.md) in skalierbarer Weise, um mandantenübergreifende Transparenz und verwaltete Sicherheitsdienste zu erreichen.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch gelten diese Anweisungen auch für [Unternehmen, die Azure Lighthouse zum Verwalten mehrerer Mandanten verwenden](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Architekturaspekte

Für einen Anbieter von verwalteten Sicherheitsdiensten (Managed Security Service Provider, MSSP), der mithilfe von Azure Sentinel ein Security-as-a-Service-Angebot entwickeln möchte, ist möglicherweise die Konzentration in einem einzelnen Security Operations Center (SOC) erforderlich, um mehrere Azure Sentinel-Arbeitsbereiche, die in einzelnen Mandanten von Kunden bereitgestellt sind, zentral zu überwachen, verwalten und konfigurieren. Analog dazu kann es auch für Unternehmen mit mehreren Azure AD-Mandanten sinnvoll sein, mehrere Azure Sentinel-Arbeitsbereiche, die über ihre Mandanten verteilt bereitgestellt sind, zentral zu verwalten.

Das zentrale Bereitstellungsmodell bietet folgende Vorzüge:

- Der Besitz der Daten verbleibt bei den einzelnen verwalteten Mandanten.
- Anforderung zur Speicherung von Daten innerhalb geografischer Grenzen wird unterstützt.
- Die Isolation der Daten ist sichergestellt, da Daten für mehrere Kunden nicht im gleichen Arbeitsbereich gespeichert werden.
- Datenexfiltration aus den verwalteten Mandanten wird verhindert, was die Sicherstellung von Datencompliance erleichtert.
- Mit den verbundenen Kosten werden die einzelnen verwalteten Mandanten belastet, nicht der verwaltende Mandant.
- Daten aus allen Datenquellen und Datenconnectors, die in Azure Sentinel integriert sind (wie etwa Azure AD-Aktivitätsprotokolle, Office 365-Protokolle oder Benachrichtigungen von Microsoft Threat Protection), verbleiben in den einzelnen Kundenmandanten.
- Die Netzwerklatenz wird verringert.
- Neue Niederlassungen oder Kunden lassen sich einfach hinzufügen oder entfernen.

> [!NOTE]
> Sie können delegierte Ressourcen verwalten, die sich in unterschiedlichen [Regionen](../../availability-zones/az-overview.md#regions) befinden. Die Delegierung von Abonnements auf eine [nationale Cloud](../../active-directory/develop/authentication-national-cloud.md) und die öffentliche Azure-Cloud oder zwei separate nationale Clouds wird nicht unterstützt.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Granulare rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)

Für jedes von MSSP verwaltete Kundenabonnement muss ein [Onboarding in Azure Lighthouse](onboard-customer.md) durchgeführt werden. Dies ermöglicht es den dafür bestimmten Benutzern im Verwaltungsmandanten, auf Verwaltungsvorgänge in Azure Sentinel-Arbeitsbereichen zuzugreifen, die in Kundenmandanten bereitgestellt wurden.

Beim Erstellen Ihrer Autorisierungen können Sie die in Azure Sentinel integrierten Rollen an Benutzer, Gruppen oder Dienstprinzipale in Ihrem Verwaltungsmandanten zuweisen:

- [Azure Sentinel-Leser](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-Antwortender](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-Mitwirkender](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Es kann außerdem sinnvoll sein, zusätzliche integrierte Rollen zuzuweisen, um zusätzliche Funktionen auszuführen. Informationen zu spezifischen Rollen, die zusammen mit Azure Sentinel verwendet werden können, finden Sie unter [Berechtigungen in Azure Sentinel](../../sentinel/roles.md).

Nach dem Onboarding Ihrer Kunden können sich dafür bestimmte Benutzer bei Ihrem Verwaltungsmandanten anmelden und mithilfe der zugewiesenen Rollen [direkt auf den Azure Sentinel-Arbeitsbereich des Kunden zugreifen](../../sentinel/multiple-tenants-service-providers.md).

## <a name="view-and-manage-incidents-across-workspaces"></a>Anzeigen und Verwalten von Vorfällen über die Grenzen von Arbeitsbereichen hinweg

Wenn Sie Azure Sentinel-Ressourcen für mehrere Kunden verwalten, können Sie Vorfälle in mehreren Arbeitsbereichen, die sich über mehrere Mandanten verteilen, zugleich anzeigen. Weitere Informationen finden Sie unter [Arbeiten mit Vorfällen in vielen Arbeitsbereichen gleichzeitig](../../sentinel/multiple-workspace-view.md) und [Erweitern von Azure Sentinel auf Arbeitsbereiche und Mandanten](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Achten Sie darauf, dass den Benutzern in Ihrem Verwaltungsmandanten Lese- und Schreibberechtigungen für alle verwalteten Arbeitsbereiche zugewiesen wurden. Wenn ein Benutzer für einige Arbeitsbereiche nur Leseberechtigungen besitzt, werden möglicherweise Warnmeldungen angezeigt, wenn er Vorfälle in diesen Arbeitsbereichen auswählt, und der Benutzer ist dann nicht in der Lage, diese Vorfälle oder andere, die zusammen mit ihnen ausgewählt wurden, zu bearbeiten (auch wenn der Benutzer für die anderen Vorfälle über Berechtigungen verfügt).

## <a name="configure-playbooks-for-mitigation"></a>Konfigurieren von Playbooks zur Entschärfung

[Playbooks](../../sentinel/tutorial-respond-threats-playbook.md) können zur automatischen Entschärfung verwendet werden, wenn ein Alarm ausgelöst wird. Diese Playbooks können manuell ausgeführt werden, oder aber automatisch, wenn bestimmte Warnungen ausgelöst werden. Die Playbooks können wahlweise im Verwaltungsmandanten oder im Kundenmandanten bereitgestellt werden, wobei sich die Konfiguration der Antwortprozeduren danach richtet, Benutzer welches Mandanten in Reaktion auf eine Sicherheitsbedrohung aktiv werden müssen.

## <a name="create-cross-tenant-workbooks"></a>Erstellen von mandantenübergreifenden Arbeitsmappen

Mithilfe von [Azure Monitor-Arbeitsmappen in Azure Sentinel](../../sentinel/overview.md#workbooks) können Sie Daten aus Ihren verbundenen Datenquellen visualisieren und überwachen, um Erkenntnisse zu gewinnen. Sie können die integrierten Arbeitsmappenvorlagen in Azure Sentinel verwenden oder eigene, benutzerdefinierte Arbeitsmappen für Ihre Szenarien erstellen.

Sie können Arbeitsmappen in Ihrem Verwaltungsmandanten bereitstellen und in großem Maßstab Dashboards erstellen, um Daten übergreifend über Kundenmandanten zu überwachen und abzufragen. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Überwachung](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). 

Darüber hinaus können Sie Arbeitsmappen direkt in einem einzelnen von Ihnen verwalteten Mandanten bereitstellen, um für diesen Kunden spezifische Szenarien zu behandeln.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Ausführen von Log Analytics- und Hunting-Abfragen für Azure Sentinel-Arbeitsbereiche

Erstellen und speichern Sie Log Analytics-Abfragen zur Bedrohungserkennung einschließlich [Hunting-Abfragen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting) zentral im Verwaltungsmandanten. Diese Abfragen können dann mit dem Vereinigungsoperator und dem Arbeitsbereichsausdruck () übergreifend in allen Azure Sentinel-Arbeitsbereichen Ihrer Kunden ausgeführt werden. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Abfragen](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Verwenden von Automation für die arbeitsbereichsübergreifende Verwaltung

Sie können Automation verwenden, um mehrere Azure Sentinel-Arbeitsbereiche zu verwalten und [Hunting-Abfragen](../../sentinel/hunting.md), Playbooks und Arbeitsmappen zu konfigurieren. Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Verwaltung mithilfe von Automatisierung](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="manage-security-of-office-365-environments"></a>Verwalten der Sicherheit von Office 365-Umgebungen

Verwenden Sie Azure Lighthouse in Verbindung mit Azure Sentinel, um die Sicherheit von Office 365-Umgebungen mandantenübergreifend zu verwalten. Zunächst müssen [im verwalteten Mandanten vorkonfigurierte Office 365-Datenconnectors aktiviert werden](../../sentinel/connect-office-365.md), damit Informationen zu Benutzer- und Verwaltungsaktivitäten in Exchange und SharePoint (einschließlich OneDrive) in einen Azure Sentinel-Arbeitsbereich innerhalb des verwalteten Mandanten erfasst werden können. Dies umfasst Details zu Aktionen wie z. B. Dateidownloads, gesendeten Zugriffsanforderungen, Änderungen an Gruppenereignissen und Postfachvorgängen sowie Informationen zu dem Benutzer, der die Aktionen ausgeführt hat. [Office 365-DLP-Warnungen](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) werden auch als Teil des integrierten Office 365-Connectors unterstützt.

Sie können den [MCAS-Connector (Microsoft Cloud App Security)](../../sentinel/connect-cloud-app-security.md) zum Streamen von Warnungen und Cloud Discovery-Protokollen in Azure Sentinel aktivieren. So erhalten Sie Einblicke in Cloud-Apps, erweiterte Analysen zur Erkennung und Abwehr von Cyberbedrohungen und Kontrolle darüber, wie Daten übertragen werden. Aktivitätsprotokolle für MCAS können [mithilfe von CEF (Common Event Format) genutzt werden](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

Nach dem Einrichten von Office 365-Datenconnectors können Sie mandantenübergreifende Azure Sentinel-Funktionen verwenden, z. B. Anzeigen und Analysieren von Daten in Arbeitsmappen, Verwenden von Abfragen zum Erstellen von benutzerdefinierten Warnungen und Konfigurieren von Playbooks als Reaktion auf Bedrohungen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Azure Sentinel](../../sentinel/overview.md).
- Sehen Sie sich die Seite [Azure Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel/) an.
- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).

