---
title: Häufig verwendete Sicherheitsattribute für den Azure Key Vault
description: Eine Prüfliste allgemeiner Sicherheitsattribute für die Auswertung von Azure Key Vault
services: key-vault
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c2265ff5f4c444121bf70c35145703f1b9fe981
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66000192"
---
# <a name="security-attributes-for-azure-key-vault"></a>Sicherheitsattribute für den Azure Key Vault

In diesem Artikel werden die in Azure Key Vault integrierten Sicherheitsattribute beschrieben. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>| Ja | Alle Objekte werden verschlüsselt. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Die gesamte Kommunikation erfolgt über verschlüsselte API-Aufrufe. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Ja | Der Kunde überwacht alle Schlüssel im Schlüsseltresor. Wenn vom Hardwaresicherheitsmodul (HSM) unterstützte Schlüssel angegeben werden, bietet ein FIPS Level 2 HSM Schutz für Schlüssel, Zertifikate oder Geheimnisse. |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – |  |
| Verschlüsselte API-Aufrufe| Ja | Mithilfe von HTTPS |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja | Mithilfe von Dienstendpunkten im virtuellen Netzwerk (VNET) |
| Unterstützung der VNet-Einschleusung| Nein |  |
| Unterstützung von Netzwerkisolation und Firewall| Ja | Mithilfe von VNET-Firewallregeln |
| Unterstützung der Tunnelerzwingung| Nein |  |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Mithilfe von Log Analytics |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Die Authentifizierung erfolgt über Azure Active Directory. |
| Autorisierung| Ja | Mithilfe der Schlüsseltresor-Zugriffsrichtlinie |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene – Protokollierung und Überwachung| Ja | Mithilfe von Log Analytics |
| Protokollierung und Überwachung auf Datenebene| Ja | Mithilfe von Log Analytics |

## <a name="access-controls"></a>Zugriffssteuerung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene – Zugriffskontrollen | Ja | Rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) im Rahmen von Azure Resource Manager |
| Datenebene – Zugriffskontrollen (auf jeder Dienstebene) | Ja | Schlüsseltresor-Zugriffsrichtlinie |