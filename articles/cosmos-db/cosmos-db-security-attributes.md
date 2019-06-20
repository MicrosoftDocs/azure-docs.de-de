---
title: Sicherheitsattribute für Azure Cosmos DB
description: Eine Prüfliste der Sicherheitsattribute für die Auswertung von Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480465"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Sicherheitsattribute für Azure Cosmos DB

In diesem Artikel werden die in Azure Cosmos DB integrierten allgemeinen Sicherheitsattribute beschrieben.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prävention

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Verschlüsselung ruhender Daten (z. B. serverseitige Verschlüsselung, serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln und andere Verschlüsselungsfunktionen) | Ja | Alle Cosmos DB-Datenbanken und -Sicherungen werden standardmäßig verschlüsselt. Weitere Informationen finden Sie unter [Datenverschlüsselung in Azure Cosmos DB](database-encryption-at-rest.md). Die serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln wird nicht unterstützt. |
| Verschlüsselung während der Übertragung (z. B. ExpressRoute-Verschlüsselung, VNET-Verschlüsselung und VNET-VNET-Verschlüsselung)| Ja | Alle Azure Cosmos DB-Daten werden während der Übertragung verschlüsselt. |
| Verarbeitung von Verschlüsselungsschlüsseln (CMK, BYOK usw.)| Nein |  |
| Verschlüsselung auf Spaltenebene (Azure Data Services)| Ja | Nur in der Tabellen-API (Premium). Diese Funktion wird nicht von allen APIs unterstützt. Siehe [Einführung in Azure Cosmos DB: Tabellen-API](table-introduction.md). |
| Verschlüsselte API-Aufrufe| Ja | Alle Verbindungen mit Azure Cosmos DB unterstützen HTTPS. Azure Cosmos DB unterstützt auch TLS 1.2-Verbindungen, dies ist jedoch noch keine zwingende Voraussetzung. Wenn Kunden Low-Level-TLS auf ihrer Seite deaktivieren, können sie sicherstellen, dass Verbindungen mit Cosmos DB hergestellt werden.  |

## <a name="network-segmentation"></a>Netzwerksegmentierung

| Sicherheitsattribut | Ja/Nein | Notizen |
|---|---|--|
| Unterstützung des Dienstendpunkts| Ja |  |
| vNET Injection-Unterstützung| Ja | Bei einem VNET-Dienstendpunkt können Sie ein Azure Cosmos DB-Konto so konfigurieren, dass der Zugriff nur aus einem bestimmten Subnetz eines virtuellen Netzwerks (VNET) zugelassen wird. Sie können den VNET-Zugriff auch mit Firewallregeln kombinieren.  Siehe [Zugreifen auf Azure Cosmos DB über virtuelle Netzwerke](vnet-service-endpoint.md). |
| Netzwerkisolation und Firewallunterstützung| Ja | Durch die Firewallunterstützung können Sie Ihr Azure Cosmos-Konto so konfigurieren, dass der Zugriff nur über eine genehmigte Gruppe von IP-Adressen, einen IP-Adressbereich und/oder Clouddienste zugelassen wird. Siehe [Konfigurieren der IP-Firewall in Azure Cosmos DB](how-to-configure-firewall.md).|
| Unterstützung für Tunnelerzwingung | Ja | Kann auf der Clientseite für das VNET konfiguriert werden, in dem sich die virtuellen Computer befinden.   |

## <a name="detection"></a>Erkennung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Azure-Überwachung (Log Analytics, Application Insights usw.)| Ja | Alle an Azure Cosmos DB gesendeten Anforderungen werden protokolliert. Die [Azure-Überwachung](../azure-monitor/overview.md), Azure-Metriken und die Azure-Überwachungsprotokollierung werden unterstützt.  Sie können Informationen protokollieren, die Datenebenenanforderungen, Abfragelaufzeitstatistiken, Abfragetext oder MongoDB-Anforderungen entsprechen. Sie können auch Benachrichtigungen einrichten. |

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Authentication| Ja | Ja, auf Ebene des Datenbankkontos und der Datenebene. Cosmos DB verwendet Ressourcentoken und Schlüsselzugriff. |
| Autorisierung| Ja | Für das Azure Cosmos-Konto mit (primärem und sekundärem) Hauptschlüssel und Ressourcentoken unterstützt. Sie können Hauptschlüssel verwenden, um Lese-/Schreibzugriff oder schreibgeschützten Zugriff auf Daten zu erhalten. Ressourcentoken ermöglichen den zeitlich begrenzten Zugriff auf Ressourcen wie Dokumente und Container. |


## <a name="audit-trail"></a>Überwachungspfad

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Steuerungs-/Verwaltungsebene – Protokollierung und Überwachung| Ja | Azure-Aktivitätsprotokoll für Vorgänge auf Kontoebene, z. B. Firewalls, VNETs, Schlüsselzugriff und IAM. |
| Datenebene – Protokollierung und Überwachung | Ja | Diagnoseüberwachungsprotokolle für Vorgänge auf Containerebene, z. B. das Erstellen von Containern, Bereitstellen von Durchsatz, Indizieren von Richtlinien sowie für CRUD-Vorgänge für Dokumente. |

## <a name="configuration-management"></a>Konfigurationsverwaltung

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Unterstützung der Konfigurationsverwaltung (Versionsverwaltung der Konfiguration usw.)| Nein  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Zusätzliche Sicherheitsattribute für Cosmos DB

| Sicherheitsattribut | Ja/Nein | Notizen|
|---|---|--|
| Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS) | Ja | Siehe [Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](how-to-configure-cross-origin-resource-sharing.md). |
