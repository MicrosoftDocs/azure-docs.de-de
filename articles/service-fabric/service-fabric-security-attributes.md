---
title: Allgemeine Sicherheitsattribute für Azure Service Fabric
description: Eine Prüfliste allgemeiner Sicherheitsattribute für die Auswertung von Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 7c1718298c3f7c3fea28fa0b18569085f071696f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003055"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Sicherheitsattribute für Azure Service Fabric

In diesem Artikel werden die in Azure Service Fabric integrierten Sicherheitsattribute beschrieben. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Der Cluster und die VM-Skalierungsgruppe, auf der der Cluster basiert, befinden sich im Besitz des Kunden. Azure Disk Encryption kann für die VM-Skalierungsgruppe aktiviert werden. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja |  |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Der Cluster und die VM-Skalierungsgruppe, auf der der Cluster basiert, befinden sich im Besitz des Kunden. Azure Disk Encryption kann für die VM-Skalierungsgruppe aktiviert werden. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – |  |
| Verschlüsselte API-Aufrufe| Ja | Service Fabric-API-Aufrufe erfolgen über Azure Resource Manager. Ein gültiges JSON Web Token (JWT) ist erforderlich. |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja |  |
| Unterstützung der VNet-Einschleusung| Ja |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Verwendung von Netzwerksicherheitsgruppen (NSG) |
| Unterstützung der Tunnelerzwingung| Ja | Azure-Netzwerke unterstützen die Tunnelerzwingung. |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Durch die Unterstützung der Azure-Überwachung und von Drittanbietern. |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Die Authentifizierung erfolgt über Azure Active Directory. |
| Autorisierung| Ja | Identitäts- und Zugriffsverwaltung (IAM) für Aufrufe über SFRP. Direkte Aufrufe von Clusterendpunkten unterstützen zwei Rollen: „Benutzer“ und „Administrator“. Der Kunde kann die APIs einer der beiden Rollen zuordnen. |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs-/Verwaltungsebene| Ja | Alle Vorgänge auf Steuerungsebene durchlaufen Überwachungs- und Genehmigungsprozesse. |
| Protokollierung und Überwachung auf Datenebene| – | Der Cluster befindet sich im Besitz des Kunden.  |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | |