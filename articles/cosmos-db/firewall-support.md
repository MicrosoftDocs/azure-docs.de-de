---
title: IP-Firewall für Azure Cosmos-Konten
description: Erfahren Sie, wie Sie Azure Cosmos DB-Daten mit Richtlinien für die IP-Zugriffssteuerung für die Firewallunterstützung einrichten.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241078"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>IP-Firewall in Azure Cosmos DB

Zum Sichern von in Ihrem Konto gespeicherten Daten unterstützt Azure Cosmos DB ein auf Geheimnissen basierendes Autorisierungsmodell, das einen starken hashbasierten Nachrichtenauthentifizierungscode (HMAC) nutzt. Darüber hinaus unterstützt Azure Cosmos DB die IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Dieses Modell ähnelt den Firewallregeln eines herkömmlichen Datenbanksystems und bietet zusätzliche Sicherheit für Ihr Konto. Mit Firewalls können Sie nun Ihr Azure Cosmos-Konto so konfigurieren, dass es nur über eine genehmigte Gruppe von Computern und/oder Clouddiensten zugänglich ist. Für den Zugriff auf Daten, die in Ihrer Azure Cosmos-Datenbank gespeichert sind, über diese genehmigten Gruppen von Computern und Diensten muss der Aufrufer weiterhin ein gültiges Autorisierungstoken vorlegen.

## <a id="ip-access-control-overview"></a>Übersicht über die IP-Zugriffssteuerung

Standardmäßig ist ein Azure Cosmos-Konto über das Internet zugänglich, solange die Anforderung zusammen mit einem gültigen Autorisierungstoken erfolgt. Zum Konfigurieren der auf IP-Richtlinien basierenden Zugriffssteuerung muss der Benutzer die Gruppe der IP-Adressen oder IP-Adressbereiche im CIDR-Format (Classless Inter-Domain Routing, klassenloses domänenübergreifendes Routing) angeben, die als Liste der zulässigen Client-IPs für den Zugriff auf ein bestimmtes Azure Cosmos-Konto aufgenommen wird. Nachdem diese Konfiguration angewendet wurde, erhalten alle Anforderungen von Computern, die nicht in dieser Zulassungsliste enthalten sind, die Antwort „403 (Nicht zulässig)“. Wenn Sie eine IP-Firewall verwenden, empfiehlt es sich, dem Azure-Portal Zugriff auf Ihr Konto zu gewähren. Zugriff ist erforderlich, um den Daten-Explorer verwenden zu können und um Metriken für Ihr Konto, die im Azure-Portal angezeigt werden, abzurufen. Bei Verwendung des Daten-Explorers müssen Sie nicht nur dem Azure-Portal den Zugriff auf Ihr Konto gestatten, sondern auch Ihre Firewalleinstellungen aktualisieren, um den Firewallregeln Ihre aktuelle IP-Adresse hinzuzufügen. Beachten Sie, dass die Verteilung von Änderungen an der Firewall bis zu 15 Minuten dauern kann. 

Sie können die IP-basierte Firewall mit Subnetz- und VNET-Zugriffssteuerung kombinieren. Durch diese Kombination können Sie den Zugriff auf eine beliebigen Quelle beschränken, die eine öffentliche IP-Adresse aufweist bzw. zu einem bestimmten Subnetz im VNET gehört. Weitere Informationen zur Verwendung von Subnetz- und VNET-basierter Zugriffssteuerung finden Sie unter [Zugreifen auf Azure Cosmos DB-Ressourcen über virtuelle Netzwerke](vnet-service-endpoint.md).

Zusammenfassend lässt sich sagen, dass für den Zugriff auf ein Azure Cosmos-Konto immer ein Autorisierungstoken erforderlich ist. Wenn IP-Firewall- und VNET-Zugriffssteuerungslisten (ACLs) nicht eingerichtet sind, kann mit dem Authentifizierungstoken auf das Azure Cosmos-Konto zugegriffen werden. Nachdem IP-Firewall- oder VNET-ACLs oder beides für das Azure Cosmos-Konto eingerichtet wurden, erhalten nur Anforderungen, die aus den von Ihnen angegebenen Quellen stammen (und das Autorisierungstoken aufweisen), gültige Antworten. 

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie mit folgenden Dokumenten IP-Firewall- oder VNET-Dienstendpunkte für Ihr Konto konfigurieren:

* [Konfigurieren der IP-Firewall für Ihr Azure Cosmos-Konto](how-to-configure-firewall.md)
* [Zugreifen auf Azure Cosmos DB-Ressourcen über virtuelle Netzwerke](vnet-service-endpoint.md)
* [Konfigurieren von VNET-Dienstendpunkten für Ihr Azure Cosmos-Konto](how-to-configure-vnet-service-endpoint.md)




