---
title: Allgemeine Sicherheitsattribute für Azure Service Bus Messaging
description: Eine Prüfliste allgemeiner Sicherheitsattribute für die Auswertung von Azure Service Bus Messaging
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: d68ffe6561da6a23c288dfabd1d3eb6b34099bb3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003113"
---
# <a name="security-attributes-for-azure-service-bus-messaging"></a>Sicherheitsattribute für Azure Service Bus Messaging

In diesem Artikel werden die in Azure Service Bus Messaging integrierten Sicherheitsattribute beschrieben.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten:<ul><li>Serverseitige Verschlüsselung</li><li>Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln</li><li>Weitere Verschlüsselungsfunktionen (z. B. clientseitig, immer verschlüsselt usw.)</ul>|  Ja, standardmäßig für serverseitige Verschlüsselung ruhender Daten. | Von Kunden verwaltete Schlüssel und BYOK werden noch nicht unterstützt. Clientseitige Verschlüsselung liegt in der Verantwortung des Clients. |
| Verschlüsselung während der Übertragung:<ul><li>ExpressRoute-Verschlüsselung</li><li>In VNet-Verschlüsselung</li><li>VNet-VNet-Verschlüsselung</ul>| Ja | Unterstützt HTTPS/TLS-Standardverfahren. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein |   |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| – | |
| Verschlüsselte API-Aufrufe| Ja | API-Aufrufe erfolgen über [Azure Resource Manager](../azure-resource-manager/index.yml) und HTTPS. |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Dienstendpunktunterstützung| Ja (nur Premium-Tarif) | VNet-Dienstendpunkte werden nur für den [Premium-Tarif von Service Bus](service-bus-premium-messaging.md) unterstützt. |
| Unterstützung der VNet-Einschleusung| Nein | |
| Unterstützung von Netzwerkisolation und Firewall| Ja (nur Premium-Tarif) |  |
| Unterstützung der Tunnelerzwingung| Nein |  |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Unterstützt über [Azure Monitor und -Warnungen](service-bus-metrics-azure-monitor.md). |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Verwaltet durch [verwaltete Dienstidentität von Azure Active Directory](service-bus-managed-service-identity.md); siehe [Service Bus-Authentifizierung und -Autorisierung](service-bus-authentication-and-authorization.md).|
| Autorisierung| Ja | Unterstützt Autorisierung über [rollenbasierte Zugriffssteuerung (RBAC)](service-bus-role-based-access-control.md) und SAS-Token; siehe [Service Bus-Authentifizierung und -Autorisierung](service-bus-authentication-and-authorization.md). |



## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Protokollierung und Überwachung auf Steuerungs- und Verwaltungsebene| Ja | Vorgangsprotokolle sind verfügbar; siehe [Service Bus-Diagnoseprotokolle](service-bus-diagnostic-logs.md).  |
| Protokollierung und Überwachung auf Datenebene| Nein |  |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Ja | Unterstützt Versionsverwaltung durch den Ressourcenanbieter über [Azure Resource Manager-API](/rest/api/resources/).|
