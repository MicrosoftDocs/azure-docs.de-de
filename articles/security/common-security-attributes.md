---
title: Sicherheitsattribute für Azure-Dienste
description: Eine Prüfliste allgemeiner Sicherheitsattribute für die Auswertung von Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66474538"
---
# <a name="security-attributes-for-azure-services"></a>Sicherheitsattribute für Azure-Dienste

In diesem Artikel werden allgemeine Sicherheitsattribute für ausgewählte Azure-Dienste beschrieben. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja (nur serverseitige Verschlüsselung) | Vertrauliche Daten, z. B. Zertifikate, Schlüssel und als geheim gekennzeichnete Werte, werden mit vom Dienst verwalteten Schlüsseln für die jeweilige Dienstinstanz verschlüsselt. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | [ExpressRoute](../expressroute/index.yml)- und VNET-Verschlüsselung erfolgen über [Azure-Netzwerke](../virtual-network/index.yml). |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein | Alle Verschlüsselungsschlüssel gelten pro Dienstinstanz und werden vom Dienst verwaltet. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | Aufrufe auf Verwaltungsebene erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml) und TLS. Ein gültiges JSON Web Token (JWT) ist erforderlich.  Aufrufe auf Datenebene können mit TLS und einem der unterstützten Authentifizierungsmechanismen (z. B. Clientzertifikat oder JWT) geschützt werden.
 |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Nein | |
| Unterstützung der VNET-Einschleusung| Ja | |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Mithilfe von Netzwerksicherheitsgruppen (NSGs) bzw. Azure Application Gateway (oder anderen Softwareappliances) |
| Unterstützung der Tunnelerzwingung| Ja | Azure-Netzwerke unterstützen die Tunnelerzwingung. |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | |
| Autorisierung| Ja | |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | [Azure Monitor-Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md) |
| Protokollierung und Überwachung auf Datenebene| Ja | [Azure Monitor-Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md) und (optional) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Verwendung des [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten (z. B. serverseitige Verschlüsselung, serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln und andere Verschlüsselungsfunktionen) | Ja | Die Inhalte von Websitedateien werden in Azure Storage gespeichert, das die ruhenden Inhalte automatisch verschlüsselt. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md).<br><br>Vom Kunden bereitgestellte Geheimnisse werden im Ruhezustand verschlüsselt. Die Geheimnisse werden im Ruhezustand verschlüsselt, während sie in App Service-Konfigurationsdatenbanken gespeichert sind.<br><br>Lokal angefügte Datenträger können von Websites als temporärer Speicher verwendet werden („D:\local“ und %TMP%). Lokal angefügte Datenträger werden im Ruhezustand nicht verschlüsselt. |
| Verschlüsselung während der Übertragung (z. B. ExpressRoute-Verschlüsselung, Verschlüsselung im VNET und VNET-zu-VNET-Verschlüsselung)| Ja | Kunden können Websites so konfigurieren, dass für sie HTTPS für eingehenden Datenverkehr gefordert und verwendet wird. Weitere Informationen finden Sie im Blogbeitrag [How to make an Azure App Service HTTPS only](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (Erzwingen von HTTPS für Azure App Service). |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Kunden können wählen, dass Anwendungsgeheimnisse in Key Vault gespeichert werden, und können diese zur Laufzeit abrufen. Weitere Informationen finden Sie unter [Verwenden von Key Vault-Verweisen in App Service und Azure Functions (Vorschauversion)](../app-service/app-service-key-vault-references.md).|
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | Verwaltungsaufrufe zum Konfigurieren von App Service werden mit [Azure Resource Manager](../azure-resource-manager/index.yml)-Aufrufen über HTTPS gesendet. |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Derzeit in der Vorschauversion für App Service verfügbar. Weitere Informationen finden Sie unter [Azure App Service – Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md). |
| Unterstützung der VNet-Einschleusung| Ja | App Service-Umgebungen sind private Implementierungen von App Service, die für einen einzelnen Kunden dediziert sind, der in das virtuelle Netzwerk eines Kunden eingefügt wurde. Weitere Informationen finden Sie unter [Einführung in die App Service-Umgebungen](../app-service/environment/intro.md). |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Für die öffentliche mehrinstanzenfähige Variante von App Service können Kunden Netzwerk-Zugriffssteuerungslisten (IP-Einschränkungen) konfigurieren, um zulässigen eingehenden Datenverkehr zu sperren.  Weitere Informationen finden Sie unter [Azure App Service – Zugriffseinschränkungen](../app-service/app-service-ip-restrictions.md).  App Service-Umgebungen werden direkt in virtuellen Netzwerken bereitgestellt und können daher mit Netzwerksicherheitsgruppen geschützt werden. |
| Unterstützung der Tunnelerzwingung| Ja | App Service-Umgebungen können im virtuellen Netzwerk eines Kunden bereitgestellt werden, in dem Tunnelerzwingung konfiguriert ist. Kunden müssen die Anweisungen unter [Konfigurieren Ihrer App Service-Umgebung mit erzwungenem Tunneling](../app-service/environment/forced-tunnel-support.md) befolgen. |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | App Service ist in Application Insights eingebunden für Sprachen, die Application Insights (Vollversion von .NET Framework, .NET Core, Java und Node.JS) unterstützen.  Weitere Informationen finden Sie unter [Überwachen der Leistung von Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service sendet außerdem Anwendungsmetriken an Azure Monitor. Weitere Informationen finden Sie unter [Überwachen von Apps in Azure App Service](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Kunden können Anwendungen in App Service erstellen, die automatisch in [Azure Active Directory (Azure AD)](../active-directory/index.yml) sowie in andere OAuth-kompatible Identitätsanbieter eingebunden werden. Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../app-service/overview-authentication-authorization.md). Für Verwaltungszugriff auf App Service-Ressourcen werden sämtliche Zugriffe mit einer Kombination aus Azure AD-authentifizierten Prinzipal- und Azure Resource Manager-RBAC-Rollen gesteuert. |
| Autorisierung| Ja | Für Verwaltungszugriff auf App Service-Ressourcen werden sämtliche Zugriffe mit einer Kombination aus Azure AD-authentifizierten Prinzipal- und Azure Resource Manager-RBAC-Rollen gesteuert.  |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Alle Verwaltungsvorgänge, die für App Service-Objekte ausgeführt werden, erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml). Verlaufsprotokolle für diese Vorgänge sind sowohl im Portal als auch über die CLI verfügbar. Weitere Informationen finden Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftweb) und [az monitor activity-log](/cli/azure/monitor/activity-log). |
| Protokollierung und Überwachung auf Datenebene | Nein | Die Datenebene für App Service ist eine Remotedateifreigabe, die die bereitgestellten Websiteinhalte eines Kunden enthält.  Es gibt keine Überwachung der Remotedateifreigabe. |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Für Verwaltungsvorgänge kann der Status einer App Service-Konfiguration als eine Azure Resource Manager-Vorlage exportiert und im Lauf der Zeit versioniert werden. Für Runtimevorgänge können Kunden App Service-Bereitstellungsslots verwenden, um mehrere verschiedene aktive Versionen einer Anwendung zu verwalten. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja |  |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | HTTPS/TLS. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| – | Azure Resource Manager speichert keine Kundeninhalte, sondern nur Steuerdaten. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| Ja | |
| Verschlüsselte API-Aufrufe| Ja | |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Nein | |
| Unterstützung der VNET-Einschleusung| Ja | |
| Unterstützung von Netzwerkisolation und Firewall| Nein |  |
| Unterstützung der Tunnelerzwingung| Nein |  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Nein | |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Basierend auf [Azure Active Directory](/azure/active-directory).|
| Autorisierung| Ja | |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Aktivitätsprotokolle machen alle Schreibvorgänge (PUT, POST, DELETE) sichtbar, die auf Ihre Ressourcen angewendet werden. Siehe [Anzeigen von Aktivitätsprotokollen zu Überwachungsaktionen für Ressourcen](../azure-resource-manager/resource-group-audit.md). |
| Protokollierung und Überwachung auf Datenebene| – | |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Verwenden der Speicherdienstverschlüsselung für Speicherkonten |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Nein | Mithilfe von HTTPS |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein |  |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| Nein |  |
| Verschlüsselte API-Aufrufe| Ja |  |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Nein |  |
| Unterstützung der VNet-Einschleusung| Nein |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Die Tunnelerzwingung wird für VM-Sicherungen unterstützt. Die Tunnelerzwingung wird für Workloads in virtuellen Computern nicht unterstützt. |
| Unterstützung der Tunnelerzwingung| Nein |  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Log Analytics wird über Diagnoseprotokolle unterstützt. Weitere Informationen finden Sie unter „Überwachen von Azure Backup-geschützten Workloads mit Log Analytics“ (unter https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) ). |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Die Authentifizierung erfolgt über Azure Active Directory. |
| Autorisierung| Ja | Vom Kunden erstellte und integrierte RBAC-Rollen werden verwendet. Weitere Informationen finden Sie unter „Verwenden der rollenbasierten Zugriffssteuerung zum Verwalten von Azure Backup-Wiederherstellungspunkten“ (unter /azure/backup/backup-rbac-rs-vault). |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Alle vom Kunden über das Azure-Portal ausgelösten Aktionen werden in Aktivitätsprotokollen protokolliert. |
| Protokollierung und Überwachung auf Datenebene| Nein | Die Azure Backup-Datenebene kann nicht direkt aufgerufen werden.  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Alle Objekte werden verschlüsselt. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Die gesamte Kommunikation erfolgt über verschlüsselte API-Aufrufe. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Der Kunde überwacht alle Schlüssel im Schlüsseltresor. Wenn vom Hardwaresicherheitsmodul (HSM) unterstützte Schlüssel angegeben werden, bietet ein FIPS Level 2 HSM Schutz für Schlüssel, Zertifikate oder Geheimnisse. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – |  |
| Verschlüsselte API-Aufrufe| Ja | Mithilfe von HTTPS |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Mithilfe von Dienstendpunkten im virtuellen Netzwerk (VNET) |
| Unterstützung der VNet-Einschleusung| Nein |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Mithilfe von VNET-Firewallregeln |
| Unterstützung der Tunnelerzwingung| Nein |  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Mithilfe von Log Analytics |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Die Authentifizierung erfolgt über Azure Active Directory. |
| Autorisierung| Ja | Mithilfe der Schlüsseltresor-Zugriffsrichtlinie |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene – Protokollierung und Überwachung| Ja | Mithilfe von Log Analytics |
| Protokollierung und Überwachung auf Datenebene| Ja | Mithilfe von Log Analytics |

### <a name="access-controls"></a>Zugriffssteuerung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene – Zugriffskontrollen | Ja | Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) im Rahmen von Azure Resource Manager |
| Datenebene – Zugriffskontrollen (auf jeder Dienstebene) | Ja | Schlüsseltresor-Zugriffsrichtlinie |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus-Messaging](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>|  Ja, standardmäßig für serverseitige Verschlüsselung ruhender Daten. | Von Kunden verwaltete Schlüssel und BYOK werden noch nicht unterstützt. Clientseitige Verschlüsselung liegt in der Verantwortung des Clients. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Unterstützt HTTPS/TLS-Standardverfahren. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein |   |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | API-Aufrufe erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml) und HTTPS. |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja (nur Premium-Tarif) | VNET-Dienstendpunkte werden nur für den [Premium-Tarif von Service Bus](../service-bus-messaging/service-bus-premium-messaging.md) unterstützt. |
| Unterstützung der VNet-Einschleusung| Nein | |
| Unterstützung von Netzwerkisolation und Firewall| Ja (nur Premium-Tarif) |  |
| Unterstützung der Tunnelerzwingung| Nein |  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Unterstützt über [Azure Monitor und -Warnungen](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Verwaltet durch [verwaltete Dienstidentität von Azure Active Directory](../service-bus-messaging/service-bus-managed-service-identity.md); siehe [Service Bus-Authentifizierung und -Autorisierung](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Autorisierung| Ja | Unterstützt Autorisierung über [rollenbasierte Zugriffssteuerung (RBAC)](../service-bus-messaging/service-bus-role-based-access-control.md) und SAS-Token; siehe [Service Bus-Authentifizierung und -Autorisierung](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Vorgangsprotokolle sind verfügbar; siehe [Service Bus-Diagnoseprotokolle](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Protokollierung und Überwachung auf Datenebene| Nein |  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Unterstützt Versionsverwaltung durch den Ressourcenanbieter über [Azure Resource Manager-API](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus-Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>|  – | Relay ist ein Websocket, das Daten nicht beibehält. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Der Dienst erfordert TLS. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein | Verwendet nur Microsoft-TLS-Zertifikate.  |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | HTTPS. |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Nein |  |
| Unterstützung von Netzwerkisolation und Firewall| Nein |  |
| Unterstützung der Tunnelerzwingung| – | Relay ist der TLS-Tunnel  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Über SAS. |
| Autorisierung|  Ja | Über SAS. |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Über [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Protokollierung und Überwachung auf Datenebene| Ja | Verbindungserfolg/-fehler und Fehler werden protokolliert.  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Über [Azure Resource Manager](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Der Cluster und die VM-Skalierungsgruppe, auf der der Cluster basiert, befinden sich im Besitz des Kunden. Azure Disk Encryption kann für die VM-Skalierungsgruppe aktiviert werden. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja |  |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Der Cluster und die VM-Skalierungsgruppe, auf der der Cluster basiert, befinden sich im Besitz des Kunden. Azure Disk Encryption kann für die VM-Skalierungsgruppe aktiviert werden. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – |  |
| Verschlüsselte API-Aufrufe| Ja | Service Fabric-API-Aufrufe erfolgen über Azure Resource Manager. Ein gültiges JSON Web Token (JWT) ist erforderlich. |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja |  |
| Unterstützung der VNet-Einschleusung| Ja |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Verwendung von Netzwerksicherheitsgruppen (NSG) |
| Unterstützung der Tunnelerzwingung| Ja | Azure-Netzwerke unterstützen die Tunnelerzwingung. |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Durch die Unterstützung der Azure-Überwachung und von Drittanbietern. |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Die Authentifizierung erfolgt über Azure Active Directory. |
| Autorisierung| Ja | Identitäts- und Zugriffsverwaltung (IAM) für Aufrufe über SFRP. Direkte Aufrufe von Clusterendpunkten unterstützen zwei Rollen: „Benutzer“ und „Administrator“. Der Kunde kann die APIs einer der beiden Rollen zuordnen. |

### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Alle Vorgänge auf Steuerungsebene durchlaufen Überwachungs- und Genehmigungsprozesse. |
| Protokollierung und Überwachung auf Datenebene| – | Der Cluster befindet sich im Besitz des Kunden.  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Die Dienstkonfiguration unterliegt der Versionsverwaltung und wird mit Azure Deploy bereitgestellt. Der Code (Anwendungs- und Laufzeitcode) unterliegt der Versionsverwaltung durch Azure Build.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL-Datenbank](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Wird als „Verschlüsselung während der Verwendung“ bezeichnet, wie im Artikel [Always Encrypted](../sql-database/sql-database-always-encrypted.md) beschrieben. Dienstseitige Verschlüsselung verwendet die [transparente Datenverschlüsselung](../sql-database/transparent-data-encryption-azure-sql.md) (Transparent Data Encryption, TDE).|
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Mithilfe von HTTPS |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Es wird sowohl die Verarbeitung von vom Dienst verwalteten Schlüsseln als auch die Verarbeitung von vom Kunden verwalteten Schlüsseln angeboten (die letztgenannte Option durch [Azure Key Vault](../key-vault/index.yml). |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| Ja | Durch [Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Verschlüsselte API-Aufrufe| Ja | Mithilfe von HTTPS/SSL. |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Gilt nur für eine [Einzeldatenbank](../sql-database/sql-database-single-index.yml). |
| Unterstützung der VNet-Einschleusung| Ja | Gilt nur für eine [verwaltete Instanz](../sql-database/sql-database-managed-instance.md). |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Firewall auf Datenbank- und Serverebene; Netzwerkisolation nur für eine [verwaltete Instanz](../sql-database/sql-database-managed-instance.md) |
| Unterstützung der Tunnelerzwingung| Ja | [Verwaltete Instanz](../sql-database/sql-database-managed-instance.md) über [Azure ExpressRoute](../expressroute/index.yml)-VPN |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Die SIEM-Drittanbieterlösung von Imperva (SecureSphere) wird auch unterstützt, und zwar mittels [Azure Event Hubs](../event-hubs/index.yml)-Integration über [SQL-Überwachung](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Azure Active Directory |
| Autorisierung| Ja |  |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Ja, für einige Ereignisse. |
| Protokollierung und Überwachung auf Datenebene | Ja | Über [SQL-Überwachung](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Nein  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Zusätzliche Sicherheitsattribute für SQL-Datenbank

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Prävention: Sicherheitsrisikobewertung | Ja | Weitere Informationen finden Sie unter [Mit dem Dienst zur SQL-Sicherheitsrisikobewertung können Sie Datenbankschwachstellen erkennen](../sql-database/sql-vulnerability-assessment.md). |
| Prävention: Datenermittlung und -klassifizierung  | Ja | Weitere Informationen finden Sie unter [Azure SQL-Datenbank und SQL Data Warehouse: Datenermittlung und -klassifizierung](../sql-database/sql-database-data-discovery-and-classification.md). |
| Erkennung: Bedrohungserkennung | Ja | Weitere Informationen finden Sie unter [Advanced Threat Protection für Azure SQL-Datenbank](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage (in englischer Sprache)](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja |  |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Unterstützung für die Standardverfahren HTTPS/TLS.  Benutzer können Daten auch verschlüsseln, bevor sie mit dem Dienst übertragen werden. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Weitere Informationen finden Sie unter [Speicherdienstverschlüsselung mit von Kunden verwalteten Schlüsseln im Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – |  |
| Verschlüsselte API-Aufrufe| Ja |  |

### <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja |  |
| Unterstützung der VNet-Einschleusung| – |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | |
| Unterstützung der Tunnelerzwingung| – |  |

### <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Azure Monitor-Metriken bereits verfügbar, Azure Monitor-Protokolle in der Vorschauversion |

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Azure Active Directory, gemeinsam verwendeter Schlüssel, SAS-Token. |
| Autorisierung| Ja | Unterstützung für die Autorisierung über rollenbasierte Zugriffssteuerung (RBAC), POSIX-Zugriffssteuerungslisten (ACLs) und SAS-Token |


### <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene | Ja | Azure Resource Manager-Aktivitätsprotokoll |
| Protokollierung und Überwachung auf Datenebene| Ja | Diagnoseprotokolle des Diensts, Azure Monitor-Protokolle in der Vorschauversion  |

### <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Unterstützung für die Versionierung durch den Ressourcenanbieter über Azure Resource Manager-APIs |