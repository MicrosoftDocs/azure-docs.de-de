---
title: Sicherheitsattribute für Azure App Service
description: Eine Prüfliste der Sicherheitsattribute für die Auswertung von Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475080"
---
# <a name="security-attributes-for-azure-app-service"></a>Sicherheitsattribute für Azure App Service

In diesem Artikel werden die allgemeinen in Azure App Service integrierten Sicherheitsattribute beschrieben.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten (z. B. serverseitige Verschlüsselung, serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln und andere Verschlüsselungsfunktionen) | Ja | Die Inhalte von Websitedateien werden in Azure Storage gespeichert, das die ruhenden Inhalte automatisch verschlüsselt. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md).<br><br>Vom Kunden bereitgestellte Geheimnisse werden im Ruhezustand verschlüsselt. Die Geheimnisse werden im Ruhezustand verschlüsselt, während sie in App Service-Konfigurationsdatenbanken gespeichert sind.<br><br>Lokal angefügte Datenträger können von Websites als temporärer Speicher verwendet werden („D:\local“ und %TMP%). Lokal angefügte Datenträger werden im Ruhezustand nicht verschlüsselt. |
| Verschlüsselung während der Übertragung (z. B. ExpressRoute-Verschlüsselung, Verschlüsselung im VNET und VNET-zu-VNET-Verschlüsselung)| Ja | Kunden können Websites so konfigurieren, dass für sie HTTPS für eingehenden Datenverkehr gefordert und verwendet wird. Weitere Informationen finden Sie im Blogbeitrag [How to make an Azure App Service HTTPS only](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (Erzwingen von HTTPS für Azure App Service). |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Kunden können wählen, dass Anwendungsgeheimnisse in Key Vault gespeichert werden, und können diese zur Laufzeit abrufen. Weitere Informationen finden Sie unter [Verwenden von Key Vault-Verweisen in App Service und Azure Functions (Vorschauversion)](app-service-key-vault-references.md).|
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | Verwaltungsaufrufe zum Konfigurieren von App Service werden mit [Azure Resource Manager](../azure-resource-manager/index.yml)-Aufrufen über HTTPS gesendet. |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Derzeit in der Vorschauversion für App Service verfügbar. Weitere Informationen finden Sie unter [Azure App Service – Zugriffseinschränkungen](app-service-ip-restrictions.md). |
| Unterstützung der VNet-Einschleusung| Ja | App Service-Umgebungen sind private Implementierungen von App Service, die für einen einzelnen Kunden dediziert sind, der in das virtuelle Netzwerk eines Kunden eingefügt wurde. Weitere Informationen finden Sie unter [Einführung in die App Service-Umgebungen](environment/intro.md). |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Für die öffentliche mehrinstanzenfähige Variante von App Service können Kunden Netzwerk-Zugriffssteuerungslisten (IP-Einschränkungen) konfigurieren, um zulässigen eingehenden Datenverkehr zu sperren.  Weitere Informationen finden Sie unter [Azure App Service – Zugriffseinschränkungen](app-service-ip-restrictions.md).  App Service-Umgebungen werden direkt in virtuellen Netzwerken bereitgestellt und können daher mit Netzwerksicherheitsgruppen geschützt werden. |
| Unterstützung der Tunnelerzwingung| Ja | App Service-Umgebungen können im virtuellen Netzwerk eines Kunden bereitgestellt werden, in dem Tunnelerzwingung konfiguriert ist. Kunden müssen die Anweisungen unter [Konfigurieren Ihrer App Service-Umgebung mit erzwungenem Tunneling](environment/forced-tunnel-support.md) befolgen. |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | App Service ist in Application Insights eingebunden für Sprachen, die Application Insights (Vollversion von .NET Framework, .NET Core, Java und Node.JS) unterstützen.  Weitere Informationen finden Sie unter [Überwachen der Leistung von Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service sendet außerdem Anwendungsmetriken an Azure Monitor. Weitere Informationen finden Sie unter [Überwachen von Apps in Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Kunden können Anwendungen in App Service erstellen, die automatisch in [Azure Active Directory (Azure AD)](../active-directory/index.yml) sowie in andere OAuth-kompatible Identitätsanbieter eingebunden werden. Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](overview-authentication-authorization.md). Für Verwaltungszugriff auf App Service-Ressourcen werden sämtliche Zugriffe mit einer Kombination aus Azure AD-authentifizierten Prinzipal- und Azure Resource Manager-RBAC-Rollen gesteuert. |
| Autorisierung| Ja | Für Verwaltungszugriff auf App Service-Ressourcen werden sämtliche Zugriffe mit einer Kombination aus Azure AD-authentifizierten Prinzipal- und Azure Resource Manager-RBAC-Rollen gesteuert.  |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Alle Verwaltungsvorgänge, die für App Service-Objekte ausgeführt werden, erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml). Verlaufsprotokolle für diese Vorgänge sind sowohl im Portal als auch über die CLI verfügbar. Weitere Informationen finden Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftweb) und [az monitor activity-log](/cli/azure/monitor/activity-log). |
| Protokollierung und Überwachung auf Datenebene | Nein | Die Datenebene für App Service ist eine Remotedateifreigabe, die die bereitgestellten Websiteinhalte eines Kunden enthält.  Es gibt keine Überwachung der Remotedateifreigabe. |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Für Verwaltungsvorgänge kann der Status einer App Service-Konfiguration als eine Azure Resource Manager-Vorlage exportiert und im Lauf der Zeit versioniert werden. Für Runtimevorgänge können Kunden App Service-Bereitstellungsslots verwenden, um mehrere verschiedene aktive Versionen einer Anwendung zu verwalten. | 

