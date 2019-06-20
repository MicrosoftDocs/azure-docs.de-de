---
title: Sicherheitsattribute für Azure API Management
description: Prüfliste mit allgemeinen Sicherheitsattributen zur Evaluierung von API Management
services: api-management
author: msmbaldwin
manager: barbkess
ms.service: api-management
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b5826d472b80179c5eb76e0e3a6b1c7ee282487
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001089"
---
# <a name="security-attributes-for-api-management"></a>Sicherheitsattribute für API Management

In diesem Artikel werden die in API Management integrierten Sicherheitsattribute beschrieben.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja (nur serverseitige Verschlüsselung) | Vertrauliche Daten, z. B. Zertifikate, Schlüssel und als geheim gekennzeichnete Werte, werden mit vom Dienst verwalteten Schlüsseln für die jeweilige Dienstinstanz verschlüsselt. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>Verschlüsselung im VNET</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | [ExpressRoute](../expressroute/index.yml)- und VNET-Verschlüsselungen erfolgen über [Azure-Netzwerke](../virtual-network/index.yml). |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein | Alle Verschlüsselungsschlüssel gelten pro Dienstinstanz und werden vom Dienst verwaltet. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | Aufrufe auf Verwaltungsebene erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml) und TLS. Ein gültiges JSON Web Token (JWT) ist erforderlich.  Aufrufe auf Datenebene können mit TLS und einem der unterstützten Authentifizierungsmechanismen (z. B. Clientzertifikat oder JWT) geschützt werden.
 |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Nein | |
| Unterstützung der VNET-Einschleusung| Ja | |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Verwendung von Netzwerksicherheitsgruppen (NSGs) bzw. Azure Application Gateway (oder andere Software-Appliance). |
| Unterstützung der Tunnelerzwingung| Ja | Azure-Netzwerke unterstützen die Tunnelerzwingung. |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | |
| Autorisierung| Ja | |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs-/Verwaltungsebene| Ja | [Azure Monitor-Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md) |
| Datenebene: Protokollierung und Überwachung| Ja | [Azure Monitor-Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md) und (optional) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Verwendung des [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |

## <a name="vulnerability-scans-false-positives"></a>Überprüfungen auf Sicherheitsrisiken: Falsch positive Ergebnisse

In diesem Abschnitt sind häufige Sicherheitsrisiken dokumentiert, die sich nicht auf Azure API Management auswirken.

| Sicherheitsrisiko               | BESCHREIBUNG                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed ist ein Sicherheitsrisiko bei der Implementierung der TLS SessionTicket-Erweiterung in einigen F5-Produkten. Hierbei wird das Abschöpfen („Bleeding“) von bis zu 31 Byte an Daten aus nicht initialisiertem Arbeitsspeicher ermöglicht. Die Ursache ist, dass der TLS-Stapel eine vom Client übergebene Sitzungs-ID mit Daten auffüllt, um eine Länge von 32 Bit zu erzielen. |
