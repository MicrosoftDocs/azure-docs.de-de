---
title: Serverkonzepte in Azure-Datenbank für MySQL
description: Dieses Thema enthält Aspekte und Richtlinien für die Arbeit mit Azure-Datenbank für MySQL-Servern.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 565e1bf7a4972e230b3cf56232ebd24519fcab5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525848"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Serverkonzepte in Azure-Datenbank für MySQL

Dieser Artikel enthält Erwägungen und Richtlinien für die Arbeit mit Azure Database for MySQL-Servern.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Was ist ein Azure-Datenbank für MySQL-Server?

Ein Azure-Datenbank für MySQL-Server fungiert als zentraler Verwaltungspunkt für mehrere Datenbanken. Sie weist dasselbe MySQL-Serverkonstrukt auf, das Sie möglicherweise aus lokalen Umgebungen kennen. Im Besonderen ist der Azure Database for MySQL-Dienst verwaltet, bietet Leistungsgarantien und stellt Zugriff sowie Funktionen auf Serverebene bereit.

Ein Azure-Datenbank für MySQL-Server zeichnet sich durch folgende Eigenschaften aus:

- Sie wird im Rahmen eines Azure-Abonnements erstellt.
- Sie stellt die übergeordnete Ressource für Datenbanken dar.
- Sie stellt einen Namespace für Datenbanken bereit.
- Sie ist ein Container mit einer Semantik von hoher Lebensdauer. Beim Löschen eines Servers werden die enthaltenen Datenbanken gelöscht.
- Sie stellt Ressourcen in einer Region zusammen.
- Sie stellt einen Verbindungsendpunkt für den Server- und Datenbankzugriff bereit.
- Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (Anmeldungen, Firewalls, Benutzer, Rollen, Konfigurationen etc.).
- Sie ist in mehreren Versionen verfügbar. Weitere Informationen finden Sie unter [Unterstützte Azure-Datenbank für MySQL-Datenbankversionen](./concepts-supported-versions.md).

Sie können in einer Instanz von Azure-Datenbank für MySQL-Server eine oder mehrere Datenbanken erstellen. Sie können eine Einzeldatenbank pro Server erstellen, die alle Ressourcen nutzt, oder Sie können mehrere Datenbanken erstellen, um die Ressourcen gemeinsam zu verwenden. Die Preise gelten pro Server und basieren auf der Konfiguration des Tarifs, der virtuellen Kerne und des Speichers (GB). Weitere Informationen finden Sie unter [Tarife](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Wie stelle ich eine Verbindung zu einem Azure-Datenbank für MySQL-Server her und authentifiziere diesen?

Mithilfe der folgenden Elemente kann ein sicherer Zugriff auf Ihre Datenbank sichergestellt werden.

|     |     |
| :-- | :-- |
| **Authentifizierung und Autorisierung** | Azure-Datenbank für MySQL-Server unterstützen die systemeigene MySQL-Authentifizierung. Mithilfe der Anmeldeinformationen des Serveradministrators können Sie eine Verbindung mit dem Server herstellen und sich authentifizieren. |
| **Protokoll** | Der Dienst unterstützt ein nachrichtenbasiertes Protokoll, das von MySQL verwendet wird. |
| **TCP/IP** | Das Protokoll wird über TCP/IP- und Unix-Domänensockets unterstützt. |
| **Firewall** | Zum Schutz Ihrer Daten verhindert eine Firewallregel jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Weitere Informationen finden Sie unter [Firewallregeln für Azure-Datenbank für MySQL-Server](./concepts-firewall-rules.md). |
| **SSL** | Der Dienst unterstützt die Erzwingung von SSL-Verbindungen zwischen Ihrer Anwendung und Ihrem Datenbankserver.  Weitere Informationen finden Sie unter [Konfigurieren von SSL-Konnektivität in der Anwendung für eine sichere Verbindung mit Azure-Datenbank für MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Wie verwalte ich einen Server?

Sie können Azure-Datenbank für MySQL-Server mithilfe des Azure-Portals oder der Azure CLI verwalten.

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über den Dienst finden Sie unter [Azure-Datenbank für MySQL – Überblick](./overview.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer **Dienstebene** finden Sie unter [Dienstebenen](./concepts-service-tiers.md).
- Informationen zum Herstellen einer Verbindung mit dem Dienst finden Sie unter [Datenverbindungsbibliotheken für Azure-Datenbank für MySQL](./concepts-connection-libraries.md).
