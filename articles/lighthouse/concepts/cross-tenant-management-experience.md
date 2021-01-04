---
title: Mandantenübergreifende Verwaltungsmöglichkeiten
description: Die delegierte Azure-Ressourcenverwaltung ermöglicht eine mandantenübergreifende Verwaltungserfahrung.
ms.date: 11/19/2020
ms.topic: conceptual
ms.openlocfilehash: 95b9bcf6bb9530afc9f6a23c323b6931e8975ad1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95905122"
---
# <a name="cross-tenant-management-experiences"></a>Mandantenübergreifende Verwaltungsmöglichkeiten

Als Dienstanbieter können Sie mit [Azure Lighthouse](../overview.md) Ressourcen für mehrere Kunden in Ihrem eigenen Azure AD-Mandanten (Azure Active Directory) verwalten. Die meisten Aufgaben und Dienste können mit der [delegierten Azure-Ressourcenverwaltung](../concepts/azure-delegated-resource-management.md) über verwaltete Mandanten ausgeführt werden.

> [!TIP]
> Die delegierte Azure-Ressourcenverwaltung kann auch [in einem Unternehmen verwendet werden, das über mehrere eigene Mandanten verfügt](enterprise.md), um die mandantenübergreifende Verwaltung zu vereinfachen.

## <a name="understanding-tenants-and-delegation"></a>Grundlegendes zu Mandanten und Delegierung

Ein Azure AD-Mandant ist eine Darstellung einer Organisation. Es handelt sich um eine dedizierte Instanz von Azure AD, die Organisationen bereitgestellt wird, wenn diese sich für Azure, Microsoft 365 oder andere Dienste registrieren und damit eine Geschäftsbeziehung mit Microsoft eingehen. Jeder Azure AD-Mandant ist eindeutig und von anderen Azure AD-Mandanten getrennt und verfügt über eine eigene Mandanten-ID (eine GUID). Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

Um Azure-Ressourcen für einen Kunden zu verwalten, müssten sich Dienstanbieter in der Regel mit einem Konto beim Azure-Portal anmelden, das dem Mandanten dieses Kunden zugeordnet ist, wofür ein Administrator im Mandanten des Kunden benötigt wird, um Benutzerkonten für den Dienstanbieter zu erstellen und zu verwalten.

Mit Azure Lighthouse gibt das Onboardingverfahren Benutzer im Mandanten des Dienstanbieters an, die mit delegierten Abonnements und Ressourcengruppen im Mandanten des Kunden arbeiten können. Diese Benutzer können sich dann beim Azure-Portal mit ihren eigenen Anmeldeinformationen anmelden. Innerhalb des Azure-Portals können Sie Ressourcen verwalten, die zu allen Kunden gehören, auf die sie Zugriff haben. Hierzu können Sie die Seite [Meine Kunden](../how-to/view-manage-customers.md) im Azure-Portal besuchen oder direkt im Kontext des Abonnements dieses Kunden arbeiten, entweder im Azure-Portal oder mittels APIs.

Azure Lighthouse ermöglicht größere Flexibilität bei der Verwaltung von Ressourcen für mehrere Kunden, ohne sich bei verschiedenen Konten in unterschiedlichen Mandanten anmelden zu müssen. So kann ein Dienstanbieter beispielsweise zwei Kunden mit unterschiedlichen Zuständigkeiten und Zugriffsebenen haben. Mithilfe von Azure Lighthouse können sich autorisierte Benutzer beim Mandanten des Dienstanbieters anmelden, um auf diese Ressourcen zuzugreifen.

![Diagramm zu Kundenressourcen, die über einen Dienstanbietermandanten verwaltet werden](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Unterstützung für APIs und Verwaltungstools

Sie können Verwaltungsaufgaben für delegierte Ressourcen direkt im Portal oder mithilfe von APIs und Verwaltungstools (z. B. Azure-Befehlszeilenschnittstelle und Azure PowerShell) durchführen. Alle vorhandenen APIs können für die Arbeit mit delegierten Ressourcen verwendet werden, solange die Funktionalität für mandantenübergreifende Verwaltung unterstützt wird und der Benutzer über die entsprechenden Berechtigungen verfügt.

Das Azure PowerShell-Cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) zeigt die Attribute `HomeTenantId` und `ManagedByTenantIds` für jedes Abonnement, sodass Sie ermitteln können, ob ein zurückgegebenes Abonnement zu einem verwalteten Mandanten oder Ihrem verwaltenden Mandanten gehört.

Ebenso zeigen Azure CLI-Befehle wie [az account list](/cli/azure/account#az-account-list) die Attribute `homeTenantId` und `managedByTenants` an. Wenn diese Werte bei Verwendung der Azure-Befehlszeilenschnittstelle nicht angezeigt werden, löschen Sie den Cache, indem Sie `az account clear` gefolgt von `az login --identity` ausführen.

In der Azure-REST-API enthalten die Befehle [Subscriptions – Get](/rest/api/resources/subscriptions/get) und [Subscriptions – List](/rest/api/resources/subscriptions/list) das Element `ManagedByTenant`.

> [!NOTE]
> Zusätzlich zu den Mandanteninformationen im Zusammenhang mit Azure Lighthouse können Mandanten, die von diesen APIs angezeigt werden, auch Partnermandanten für Azure Databricks oder verwaltete Azure-Anwendungen darstellen.

Wir bieten außerdem APIs, die speziell für Azure Lighthouse-Aufgaben ausgelegt sind. Weitere Informationen finden Sie im Abschnitt **Referenz**.

## <a name="enhanced-services-and-scenarios"></a>Verbesserte Dienste und Szenarien

Die meisten Aufgaben und Dienste können auf delegierten Ressourcen über verwaltete Mandanten ausgeführt werden. Im Folgenden finden Sie einige wichtige Szenarien, in denen die mandantenübergreifende Verwaltung besonders effektiv sein kann.

[Azure Arc:](../../azure-arc/index.yml)

- Verwalten von Hybridservern im großen Stil: [Server mit Azure Arc-Unterstützung](../../azure-arc/servers/overview.md):
  - [Verwalten von verbundenen Windows Server- oder Linux-Computern außerhalb von Azure](../../azure-arc/servers/onboard-portal.md) mit delegierten Abonnements oder Ressourcengruppen in Azure
  - Verwalten von verbundenen Computern mithilfe von Azure-Konstrukten, z. B. Azure Policy und Tagging
  - Sicherstellen der Anwendung derselben Richtlinien für die Hybridumgebungen von Kunden
  - Verwenden von Azure Security Center zum Überwachen der Compliance der Hybridumgebungen von Kunden
- Verwalten von Kubernetes-Hybridclustern im großen Stil – [Kubernetes mit Azure Arc-Aktivierung (Vorschauversion):](../../azure-arc/kubernetes/overview.md)
  - [Verwalten von verbundenen Kubernetes-Clustern](../../azure-arc/kubernetes/connect-cluster.md) mit delegierten Abonnements und/oder Ressourcengruppen in Azure
  - [Verwenden von GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) für verbundene Cluster
  - Erzwingen von Richtlinien für verbundene Cluster

[Azure Automation](../../automation/index.yml):

- Verwenden von Automation-Konten für den Zugriff auf und die Arbeit mit delegierten Ressourcen

[Azure Backup](../../backup/index.yml):

- Sichern und Wiederherstellen von Kundendaten [aus lokalen Workloads, Azure VMs, Azure-Dateifreigaben und mehr](../..//backup/backup-overview.md#what-can-i-back-up)
- Verwenden des [Backup-Explorers](../../backup/monitor-azure-backup-with-backup-explorer.md) zum Anzeigen von Betriebsinformationen zu Sicherungselementen (einschließlich noch nicht für die Sicherung konfigurierten Azure-Ressourcen) und Überwachungsinformationen (Aufträge und Warnungen) zu delegierten Abonnements. Der Backup-Explorer ist zurzeit nur für Azure-VM-Daten verfügbar.
- Verwenden Sie übergreifende [Sicherungsberichte](../../backup/configure-reports.md) für delegierte Abonnements, um historische Trends nachzuverfolgen, den Sicherungsspeicherverbrauch zu analysieren und Sicherungen/Wiederherstellungen zu überwachen.

[Azure Blueprints](../../governance/blueprints/index.yml):

- Verwenden Sie Azure Blueprints zur Orchestrierung der Bereitstellung von Ressourcenvorlagen und anderen Artefakten (erfordert [zusätzlichen Zugriff](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) zur Vorbereitung des Kundenabonnements).

[Azure-Kostenverwaltung und -Abrechnung](../../cost-management-billing/index.yml):

- Über den verwaltenden Mandanten können CSP-Partner für Kunden, die dem Azure-Plan unterliegen, Verbrauchskosten vor Steuern anzeigen, verwalten und analysieren. (Käufe werden hierbei nicht berücksichtigt.) Die Kosten basieren auf Einzelhandelspreisen sowie auf dem Umfang des Azure RBAC-Zugriffs (Role-Based Access Control) des Partners für das Abonnement des Kunden.

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- Verwalten gehosteter Kubernetes-Umgebungen und Bereitstellen und Verwalten von Containeranwendungen innerhalb von Kundenmandanten
- Bereitstellen und Verwalten von Clustern in Kundenmandanten
-   Verwenden von Azure Monitor für Container zum Überwachen der Leistung in Kundenmandanten

[Azure Migrate](../../migrate/index.yml):

- Erstellen von Migrationsprojekten im Kundenmandanten und Migrieren von VMs

[Azure Monitor](../../azure-monitor/index.yml):

- Anzeigen von Warnungen für delegierte Abonnements mit der Möglichkeit, Warnungen in allen Abonnements anzuzeigen
- Anzeigen von Aktivitätsprotokolldetails für delegierte Abonnements
- Log Analytics: Abfragen von Daten aus Remotearbeitsbereichen in mehreren Mandanten
- Erstellen von Warnungen in Kundenmandanten, die eine Automatisierung auslösen, wie z. B. Azure Automation-Runbooks oder Azure Functions im verwaltenden Mandanten über Webhooks
- Erstellen von Diagnoseeinstellungen in Kundenmandanten zum Senden von Ressourcenprotokollen an Arbeitsbereiche im verwaltenden Mandanten
- Für SAP-Workloads sollten Sie [die Metriken von SAP-Lösungen mit einer aggregierten Sicht über Kundenmandanten hinweg überwachen](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293).

[Azure-Netzwerkoptionen](../../networking/networking-overview.md):

- Bereitstellen und Verwalten einer [Azure Virtual Network](../../virtual-network/index.yml)-Instanz und virtueller Netzwerkschnittstellenkarten (vNICs) innerhalb verwalteter Mandanten
- Bereitstellen und Konfigurieren von [Azure Firewall](../../firewall/overview.md), um die Virtual Network-Ressourcen der Kunden zu schützen
- Verwalten von Konnektivitätsdiensten wie [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md) und [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Verwenden von Azure Lighthouse zur Unterstützung wichtiger Szenarien für das [Azure Networking MSP-Programm](../../networking/networking-partners-msp.md)

[Azure Policy](../../governance/policy/index.yml):

- Erstellen und Bearbeiten von Richtliniendefinitionen innerhalb delegierter Abonnements
- Zuweisen der von Kunden definierten Richtliniendefinitionen innerhalb delegierter Abonnements
- Kunden werden Richtlinien, die vom Dienstanbieter erstellt wurden, zusammen mit allen Richtlinien angezeigt, die sie selbst erstellt haben.
- [Beheben von „deployIfNotExists“ oder Ändern von Zuweisungen innerhalb des verwalteten Mandanten](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Enthält jetzt die Mandanten-ID in zurückgegebenen Abfrageergebnissen, sodass Sie ermitteln können, ob ein Abonnement zu einem verwalteten Mandanten gehört.

[Azure Security Center](../../security-center/index.yml):

- Mandantenübergreifende Sichtbarkeit
  - Überwachen der Einhaltung von Sicherheitsrichtlinien und Sicherstellen der Sicherheitsabdeckung für alle Ressourcen der Mandanten
  - Kontinuierliche Überwachung der Einhaltung gesetzlicher Bestimmungen für mehrere Mandanten in einer einzigen Ansicht
  - Überwachen, Selektieren und Priorisieren von umsetzbaren Sicherheitsempfehlungen mit Sicherheitsbewertungsberechnung
- Mandantenübergreifende Sicherheitsstatusverwaltung
  - Verwalten von Sicherheitsrichtlinien
  - Ergreifen von Maßnahmen für Ressourcen, die nicht mit umsetzbaren Sicherheitsempfehlungen konform sind
  - Erfassen und Speichern von sicherheitsbezogenen Daten
- Mandantenübergreifend Bedrohungserkennung und entsprechender Schutz davor
  - Erkennen von Bedrohungen über Ressourcen von Mandanten hinweg
  - Anwenden von Advanced Threat Protection-Kontrollen, wie z. B. JIT-VM-Zugriff (Just-in-Time)
  - Verstärkung des Schutzes der Konfiguration von Netzwerksicherheitsgruppen mit adaptiver Netzwerkhärtung
  - Sicherstellung mittels adaptiver Anwendungssteuerung, dass auf Servern nur die Anwendungen und Prozesse ausgeführt werden, die ausgeführt werden sollten
  - Überwachen von Änderungen an wichtigen Dateien und Registrierungseinträgen mittels Überwachung der Dateiintegrität (FIM)
- Beachten Sie, dass das gesamte Abonnement an den Verwaltungsmandanten delegiert werden muss. Azure Security Center-Szenarien werden bei delegierten Ressourcengruppen nicht unterstützt.

[Azure Sentinel:](../../sentinel/multiple-tenants-service-providers.md)

- Verwalten von Azure Sentinel-Ressourcen [in Kundenmandanten](../../sentinel/multiple-tenants-service-providers.md)
- [Verfolgen von Angriffen und Anzeigen von Sicherheitswarnungen für mehrere Mandanten](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Anzeigen von Vorfällen](../../sentinel/multiple-workspace-view.md) in mehreren Sentinel-Arbeitsbereichen in verschiedenen Mandanten

[Azure Service Health](../../service-health/index.yml):

- Überwachen der Integrität von Kundenressourcen mittels Azure Resource Health
- Verfolgen der Integrität der Azure-Dienste, die von ihren Kunden verwendet werden

[Azure Site Recovery](../../site-recovery/index.yml):

- Verwalten von Notfallwiederherstellungsoptionen für Azure-VMs in Kundenmandanten (beachten Sie, dass Sie keine `RunAs`-Konten zum Kopieren von VM-Erweiterungen verwenden können)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- Verwenden von VM-Erweiterungen, um nach der Bereitstellung Konfigurations- und Automatisierungsaufgaben auf Azure-VMs auszuführen
- Verwenden der Startdiagnose zur Problembehandlung von Azure-VMs
- Zugreifen auf VMs mit der seriellen Konsole
- Integrieren von VMs in Azure Key Vault mit einer [verwalteten Identität über Richtlinien](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), um Kennwörter, Geheimnisse oder kryptografische Schlüssel für die Datenträgerverschlüsselung zu verwenden und dabei sicherzustellen, dass Geheimnisse in einer Key Vault-Instanz in den verwalteten Mandanten gespeichert werden
- Beachten Sie, dass Sie Azure Active Directory nicht für die Remoteanmeldung bei VMs verwenden können.

Supportanfragen:

- [Öffnen von Supportanfragen über das Blatt **Hilfe und Support**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) im Azure-Portal für delegierte Ressourcen (durch Auswahl des für den delegierten Bereich verfügbaren Supportplans)

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Beachten Sie bei allen Szenarios die folgenden aktuellen Einschränkungen:

- Von Azure Resource Manager verarbeitete Anforderungen können mithilfe von Azure Lighthouse durchgeführt werden. Die Vorgangs-URIs für diese Anforderungen beginnen mit `https://management.azure.com`. Anforderungen, die von einer Instanz eines Ressourcentyps verarbeitet werden (etwa Zugriff auf Key Vault-Geheimnisse oder auf Speicherdaten), werden von Azure Lighthouse nicht unterstützt. Die Vorgangs-URIs für diese Anforderungen beginnen in der Regel mit einer Adresse, die für Ihre Instanz eindeutig ist, z. B. `https://myaccount.blob.core.windows.net` oder `https://mykeyvault.vault.azure.net/`. Letzteres sind in der Regel auch eher Datenvorgänge als Verwaltungsvorgänge.
- Rollenzuweisungen müssen [integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md) verwenden. Alle integrierten Rollen werden derzeit mit der delegierten Azure-Ressourcenverwaltung unterstützt, ausgenommen „Besitzer“ und alle integrierten Rollen mit der Berechtigung [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions). Die Rolle „Benutzerzugriffsadministrator“ wird nur für die eingeschränkte Verwendung beim [Zuweisen von Rollen zu verwalteten Identitäten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) unterstützt.  Benutzerdefinierte Rollen und [klassische Abonnementadministratorrollen](../../role-based-access-control/classic-administrators.md) werden nicht unterstützt.
- Sie können Abonnements, die Azure Databricks verwenden, zwar integrieren, Benutzer im Verwaltungsmandanten können jedoch derzeit keine Azure Databricks-Arbeitsbereiche für ein delegiertes Abonnement starten.
- Sie können zwar ein Onboarding für Abonnements und Ressourcengruppen mit Ressourcensperren durchführen, diese Sperren verhindern jedoch nicht die Ausführung von Aktionen durch Benutzer im Verwaltungsmandanten. [Ablehnungszuweisungen](../../role-based-access-control/deny-assignments.md), die systemseitig verwaltete Ressourcen schützen – beispielsweise solche, die von verwalteten Azure-Anwendungen oder von Azure Blueprints erstellt wurden (systemseitig zugewiesene Ablehnungszuweisungen) –, verhindern, dass Benutzer im Verwaltungsmandanten Aktionen für diese Ressourcen ausführen. Benutzer im Kundenmandanten können gegenwärtig allerdings keine eigenen Ablehnungszuweisungen (benutzerseitig zugewiesene Ablehnungszuweisungen) erstellen.

## <a name="next-steps"></a>Nächste Schritte

- Onboarding Ihrer Kunden in Azure Lighthouse, entweder unter [Verwendung von Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) oder mittels [Veröffentlichung eines privaten oder öffentlichen Angebots für verwaltete Dienste im Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Anzeigen und Verwalten von Kunden](../how-to/view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
