---
title: Serverkonzepte in Azure Database for PostgreSQL (Einzelserver)
description: Dieser Artikel enthält Überlegungen und Richtlinien zum Konfigurieren und Verwalten von Azure Database for PostgreSQL (Einzelserver).
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: bc135e58d0fbabc809f3718915e9f4e35b8ed875
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067155"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (Einzelserver)
Dieser Artikel enthält Überlegungen und Richtlinien für die Arbeit mit Azure Database for PostgreSQL (Einzelserver).

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Was ist ein Azure-Datenbank für PostgreSQL-Server?
Ein Server der Bereitstellungsoption Azure Database for PostgreSQL (Einzelserver) fungiert als zentraler Verwaltungspunkt für mehrere Datenbanken. Sie weist dasselbe PostgreSQL-Serverkonstrukt auf, das Sie möglicherweise aus lokalen Umgebungen kennen. Im Besonderen ist der PostgreSQL-Dienst verwaltet, bietet Leistungsgarantien und stellt Zugriff sowie Funktionen auf Serverebene bereit.

Ein Azure-Datenbank für PostgreSQL-Server weist folgende Eigenschaften auf:

- Sie wird im Rahmen eines Azure-Abonnements erstellt.
- Sie stellt die übergeordnete Ressource für Datenbanken dar.
- Sie stellt einen Namespace für Datenbanken bereit.
- Sie ist ein Container mit einer Semantik von hoher Lebensdauer. Beim Löschen eines Servers werden die enthaltenen Datenbanken gelöscht.
- Sie stellt Ressourcen in einer Region zusammen.
- Sie stellt einen Verbindungsendpunkt für den Server- und Datenbankzugriff bereit. 
- Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (Anmeldungen, Firewalls, Benutzer, Rollen, Konfigurationen etc.).
- Sie ist in mehreren Versionen verfügbar. Weitere Informationen finden Sie unter [Unterstützte PostgreSQL-Datenbankversionen](concepts-supported-versions.md).
- Sie kann von Benutzern erweitert werden. Weitere Informationen finden Sie im Artikel zu [PostgreSQL-Erweiterungen](concepts-extensions.md).

Sie können in einer Instanz von Azure-Datenbank für PostgreSQL-Server eine oder mehrere Datenbanken erstellen. Sie können eine Einzeldatenbank pro Server erstellen, die alle Ressourcen nutzt, oder Sie können mehrere Datenbanken erstellen, um die Ressourcen gemeinsam zu verwenden. Die Preise gelten pro Server und basieren auf der Konfiguration des Tarifs, der virtuellen Kerne und des Speichers (GB). Weitere Informationen finden Sie unter [Tarife](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Wie stelle ich eine Verbindung zu einem Azure-Datenbank für PostgreSQL-Server her und authentifiziere diesen?
Mithilfe der folgenden Elemente kann ein sicherer Zugriff auf Ihre Datenbank sichergestellt werden:

|||
|:--|:--|
| **Authentifizierung und Autorisierung** | Der Azure-Datenbank für PostgreSQL-Server unterstützt die systemeigene PostgreSQL-Authentifizierung. Mithilfe der Anmeldeinformationen für Serveradministrator können Sie eine Verbindung zum Server herstellen und diesen authentifizieren. |
| **Protokoll** | Der Dienst unterstützt ein nachrichtenbasiertes Protokoll, das von PostgreSQL verwendet wird. |
| **TCP/IP** | Das Protokoll wird über TCP/IP- und Unix-Domänensockets unterstützt. |
| **Firewall** | Zum Schutz Ihrer Daten verhindert eine Firewallregel jeglichen Zugriff auf Ihren Server und auf dessen Datenbanken, solange Sie nicht angeben, welche Computer zugriffsberechtigt sind. Weitere Informationen finden Sie unter  [Firewallregeln für Azure Database for PostgreSQL-Server](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>Verwalten Ihres Servers
Sie können Azure Database for PostgreSQL-Server mithilfe des [Azure-Portals](https://portal.azure.com) oder der [Azure CLI](/cli/azure/postgres) verwalten.

Beim Erstellen eines Servers richten Sie die Anmeldeinformationen für Ihren Administratorbenutzer ein. Der Administratorbenutzer ist der Benutzer mit den höchsten Berechtigungen für den Server. Er gehört der Rolle „azure_pg_admin“ an. Diese Rolle verfügt nicht über vollständige Administratorrechte. 

Das PostgreSQL-Superuser-Attribut ist dem „azure_superuser“ zugewiesen, der dem verwalteten Dienst angehört. Sie haben keinen Zugriff auf diese Rolle.

Ein Azure Database for PostgreSQL-Server verfügt über Standarddatenbanken: 
- **postgres**: Eine Standarddatenbank, mit der Sie nach der Erstellung Ihres Servers eine Verbindung herstellen können.
- **azure_maintenance**: Diese Datenbank wird verwendet, um die Prozesse, die den verwalteten Dienst bereitstellen, von den Benutzeraktionen zu trennen. Sie haben keinen Zugriff auf diese Datenbank.
- **azure_sys**: Eine Datenbank für den Abfragespeicher. Diese Datenbank sammelt keine Daten, wenn der Abfragespeicher deaktiviert ist (Standardeinstellung). Weitere Informationen finden Sie in der [Übersicht über den Abfragespeicher](concepts-query-store.md).


## <a name="server-parameters"></a>Serverparameter
Die PostgreSQL-Serverparameter bestimmen die Konfiguration des Servers. In Azure Database for PostgreSQL kann die Liste der Parameter über das Azure-Portal oder die Azure CLI angezeigt und bearbeitet werden. 

Da es sich um einen verwalteten Dienst für Postgres handelt, stellen die konfigurierbaren Parameter in Azure Database for PostgreSQL eine Teilmenge der Parameter in einer lokalen Postgres-Instanz dar. (Weitere Informationen zu Postgres-Parametern finden Sie in der [PostgreSQL-Dokumentation](https://www.postgresql.org/docs/9.6/static/runtime-config.html).) Bei der Erstellung wird der Azure Database for PostgreSQL-Server mit Standardwerten für die einzelnen Parameter konfiguriert. Einige Parameter, die einen Serverneustart oder Administratorenzugriff erfordern, damit die Änderungen wirksam werden, können nicht vom Benutzer konfiguriert werden.


## <a name="next-steps"></a>Nächste Schritte
- Eine Übersicht über den Dienst finden Sie unter  [Azure Database for PostgreSQL – Übersicht](overview.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer  **Dienstebene** finden Sie unter  [Dienstebenen](concepts-pricing-tiers.md).
- Informationen zum Herstellen einer Verbindung mit dem Dienst finden Sie unter  [Datenverbindungsbibliotheken für Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Sie können Serverparameter über das [Azure-Portal](howto-configure-server-parameters-using-portal.md) oder die [Azure CLI](howto-configure-server-parameters-using-cli.md) anzeigen und bearbeiten.
