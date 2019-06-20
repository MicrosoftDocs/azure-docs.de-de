---
title: Firewallregeln für den Azure Database for MariaDB-Server
description: Beschreibt die Firewallregeln für den Azure Database for MariaDB-Server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6fb9099ebfe884fc6eee58882ee23e46ba550e13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60734418"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Firewallregeln für den Azure Database for MariaDB-Server
Firewalls verhindern jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

Zum Konfigurieren einer Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Serverebene erstellen.

**Firewallregeln**: Diese Regeln ermöglichen es Clients, auf den gesamten Azure Database for MariaDB-Server zuzugreifen, also auf alle Datenbanken im selben logischen Server. Firewallregeln auf Serverebene können über das Azure-Portal oder mithilfe von Azure CLI-Befehlen konfiguriert werden. Zum Erstellen von Firewallregeln auf Serverebene müssen Sie der Besitzer oder ein Mitwirkender des Abonnements sein.

## <a name="firewall-overview"></a>Firewallübersicht
Jeglicher Datenbankzugriff auf Ihren Azure Database for MariaDB-Server wird standardmäßig von der Firewall blockiert. Damit Sie Ihren Server über einen anderen Computer verwenden können, müssen Sie eine oder mehrere Firewallregeln auf Serverebene angeben, die Zugriff auf Ihren Server ermöglichen. Legen Sie mithilfe der Firewallregeln fest, welche IP-Adressbereiche aus dem Internet zugelassen werden sollen. Der Zugriff auf die Website des Azure-Portals selbst wird durch die Firewallregeln nicht beeinträchtigt.

Verbindungsversuche über das Internet und Azure müssen zunächst die Firewall passieren, bevor sie Ihren Azure Database for MariaDB-Server erreichen (wie im folgenden Diagramm dargestellt):

![Beispielfluss zur Funktionsweise der Firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Herstellen einer Verbindung über das Internet
Firewallregeln auf Serverebene wirken sich auf alle Datenbanken auf dem Azure Database for MariaDB-Server aus.

Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung gewährt.

Liegt die IP-Adresse der Anforderung außerhalb der in den Firewallregeln auf Datenbankebene oder Serverebene angegebenen Bereiche, schlägt die Verbindungsanforderung fehl.

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure Database for MariaDB-Server zu ermöglichen, müssen Azure-Verbindungen ermöglicht werden. Beispiele: Hosten einer Azure-Web-Apps-Anwendung oder einer auf einem virtuellen Azure-Computer ausgeführten Anwendung und Herstellen einer Verbindung über ein Azure Data Factory-Datenverwaltungsgateway. Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Diese Arten von Verbindungen können auf unterschiedliche Weise ermöglicht werden. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Alternativ können Sie im Portal im Bereich **Verbindungssicherheit** die Option **Zugriff auf Azure-Dienste erlauben** auf **EIN** festlegen und auf **Speichern** klicken. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung den Azure Database for MariaDB-Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

![Konfigurieren von „Zugriff auf Azure-Dienste erlauben“ im Portal](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programmgesteuertes Verwalten von Firewallregeln
Außer im Azure-Portal können Firewallregeln programmgesteuert mithilfe der Azure CLI verwaltet werden. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-the-database-firewall"></a>Problembehandlung der Datenbankfirewall
Wenn der Zugriff auf den Microsoft Azure Database for MariaDB-Serverdienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

* **Änderungen an der Zulassungsliste sind noch nicht wirksam**: Änderungen der Firewallkonfiguration für den Azure Database for MariaDB-Server werden möglicherweise erst nach fünf Minuten wirksam.

* **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet**: Wenn eine Anmeldung nicht über Berechtigungen für den Azure Database for MariaDB-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure Database for MariaDB-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, eine Verbindung mit dem Server herzustellen. Jeder Client muss die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

* **Dynamische IP-Adresse**: Wenn Sie über eine Internetverbindung mit dynamischer IP-Adresszuweisung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:

* Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure Database for MariaDB-Server zugegriffen wird, und fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.

* Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die IP-Adressen als Firewallregeln hinzu.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MariaDB mithilfe des Microsoft Azure-Portals](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->
