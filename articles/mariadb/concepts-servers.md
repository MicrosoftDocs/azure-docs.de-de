---
title: Server in Azure Database for MariaDB
description: Dieses Thema enthält Aspekte und Richtlinien für die Arbeit mit Azure Database for MariaDB-Servern.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4d8293258083ea3e8d0172f510e5b41e91328736
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541061"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Serverkonzepte in Azure Database for MariaDB
Dieser Artikel enthält Aspekte und Richtlinien für die Arbeit mit Azure Database for MariaDB-Servern.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Was ist ein Azure Database for MariaDB-Server?

Ein Azure Database for MariaDB-Server fungiert als zentraler Verwaltungspunkt für mehrere Datenbanken. Er weist dasselbe MariaDB-Serverkonstrukt auf, das Sie möglicherweise aus lokalen Umgebungen kennen. Im Besonderen ist der Azure Database for MariaDB-Dienst verwaltet, bietet Leistungsgarantien und stellt Zugriff sowie Funktionen auf Serverebene bereit.

Für einen Azure Database for MariaDB-Server gilt Folgendes:

- Sie wird im Rahmen eines Azure-Abonnements erstellt.
- Sie stellt die übergeordnete Ressource für Datenbanken dar.
- Sie stellt einen Namespace für Datenbanken bereit.
- Sie ist ein Container mit einer Semantik von hoher Lebensdauer. Beim Löschen eines Servers werden die enthaltenen Datenbanken gelöscht.
- Sie stellt Ressourcen in einer Region zusammen.
- Sie stellt einen Verbindungsendpunkt für den Server- und Datenbankzugriff bereit.
- Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (Anmeldungen, Firewalls, Benutzer, Rollen, Konfigurationen etc.).
- Er steht in MariaDB-Engine-Version 10.2 zur Verfügung. Weitere Informationen finden Sie unter [Unterstützte Azure Database for MariaDB-Datenbankversionen](./concepts-supported-versions.md).

Sie können in einer Instanz des Azure Database for MariaDB-Servers eine oder mehrere Datenbanken erstellen. Sie können eine Einzeldatenbank pro Server erstellen, die alle Ressourcen nutzt, oder Sie können mehrere Datenbanken erstellen, um die Ressourcen gemeinsam zu verwenden. Die Preise gelten pro Server und basieren auf der Konfiguration des Tarifs, der virtuellen Kerne und des Speichers (GB). Weitere Informationen finden Sie unter [Tarife](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Wie schütze ich einen Azure Database for MariaDB-Server?

Mithilfe der folgenden Elemente kann ein sicherer Zugriff auf Ihre Datenbank sichergestellt werden.

|||
| :--| :--|
| **Authentifizierung und Autorisierung** | Azure Database for MariaDB-Server unterstützen die systemeigene MySQL-Authentifizierung. Mithilfe der Anmeldeinformationen des Serveradministrators können Sie eine Verbindung mit dem Server herstellen und sich authentifizieren. |
| **Protokoll** | Der Dienst unterstützt ein nachrichtenbasiertes Protokoll, das von MySQL verwendet wird. |
| **TCP/IP** | Das Protokoll wird über TCP/IP- und Unix-Domänensockets unterstützt. |
| **Firewall** | Zum Schutz Ihrer Daten verhindert eine Firewallregel jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Informationen hierzu finden Sie unter [Firewallregeln für den Azure Database for MariaDB-Server](./concepts-firewall-rules.md). |
| **SSL** | Der Dienst unterstützt die Erzwingung von SSL-Verbindungen zwischen Ihrer Anwendung und Ihrem Datenbankserver. Siehe [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit Azure Database for MariaDB](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mariadb-preview"></a>Anhalten/Starten einer Azure Database for MariaDB-Instanz (Vorschau)
Azure Database for MariaDB bietet Ihnen die Möglichkeit, den Server **anzuhalten**, wenn er nicht genutzt wird, und ihn zu **starten**, wenn Sie die Aktivität wieder aufnehmen. Dies erfolgt im Wesentlichen, um Kosten auf den Datenbankservern zu sparen und für die Ressource nur dann zu bezahlen, wenn sie in Gebrauch ist. Dies wird noch wichtiger bei Dev-Test-Workloads und wenn Sie den Server nur für einen Teil des Tages benutzen. Wenn Sie den Server anhalten, werden alle aktiven Verbindungen beendet. Wenn Sie den Server später wieder online schalten möchten, können Sie dies entweder über das [Azure-Portal](../mysql/how-to-stop-start-server.md) oder die [CLI](../mysql/how-to-stop-start-server.md) erledigen.

Wenn sich der Server im Status **Angehalten** befindet, wird das Compute des Servers nicht in Rechnung gestellt. Der Speicher wird jedoch weiterhin in Rechnung gestellt, da der Speicher des Servers verbleibt, um sicherzustellen, dass die Datendateien verfügbar sind, wenn der Server wieder gestartet wird.

> [!IMPORTANT]
> Wenn Sie den Server **anhalten**, bleibt er für die nächsten sieben Tage in diesem Zustand. Wenn Sie ihn während dieser Zeit nicht manuell **starten**, wird der Server nach Ablauf von sieben Tagen automatisch gestartet. Wenn Sie den Server nicht verwenden, können Sie ihn wieder **anhalten**.

Während der Zeit, in der der Server angehalten ist, können keine Verwaltungsvorgänge auf dem Server durchgeführt werden. Sie müssen [den Server starten](../mysql/how-to-stop-start-server.md), um Konfigurationseinstellungen auf dem Server zu ändern.

### <a name="limitations-of-stopstart-operation"></a>Einschränkungen beim Vorgang „Anhalten/Starten“
- Nicht unterstützt mit Lesereplikatkonfigurationen (sowohl Quelle als auch Replikate).

## <a name="how-do-i-manage-a-server"></a>Wie verwalte ich einen Server?
Sie können Azure Database for MariaDB-Server mithilfe des Azure-Portals oder der Azure CLI verwalten.

## <a name="next-steps"></a>Nächste Schritte
- Eine Dienstübersicht finden Sie im [Überblick über Azure Database for MariaDB](./overview.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer **Dienstebene** finden Sie unter [Dienstebenen](./concepts-pricing-tiers.md).

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
