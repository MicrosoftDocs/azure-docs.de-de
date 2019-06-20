---
title: Firewallregeln in Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel werden die Firewallregeln für Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 40a675fbefe9743f5de1f9766cf33ae7dba9e5a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073588"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Firewallregeln in Azure Database for PostgreSQL – Einzelserver
Die Azure Database for PostgreSQL-Serverfirewall verhindert jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Die Firewall gewährt den Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
Zum Konfigurieren der Firewall erstellen Sie Firewallregeln, die Bereiche zulässiger IP-Adressen festlegen. Sie können Firewallregeln auf Serverebene erstellen.

**Firewallregeln**: Diese Regeln ermöglichen es Clients, auf den gesamten Azure Database for PostgreSQL-Server zuzugreifen (also auf alle Datenbanken innerhalb desselben logischen Servers). Firewallregeln auf Serverebene können über das Azure-Portal oder mithilfe von Azure CLI-Befehlen konfiguriert werden. Zum Erstellen von Firewallregeln auf Serverebene müssen Sie der Besitzer oder ein Mitwirkender des Abonnements sein.

## <a name="firewall-overview"></a>Firewallübersicht
Jeglicher Datenbankzugriff auf Ihren Azure-Datenbank für PostgreSQL-Server wird standardmäßig von der Firewall blockiert. Damit Sie Ihren Server über einen anderen Computer verwenden können, müssen Sie eine oder mehrere Firewallregeln auf Serverebene angeben, die Zugriff auf Ihren Server ermöglichen. Legen Sie mithilfe der Firewallregeln fest, welche IP-Adressbereiche aus dem Internet zugelassen werden sollen. Der Zugriff auf die Website des Azure-Portals selbst wird durch die Firewallregeln nicht beeinträchtigt.
Verbindungsversuche über das Internet und über Azure müssen zunächst die Firewall passieren, bevor sie Ihre PostgreSQL-Datenbank erreichen (wie im folgenden Diagramm dargestellt):

![Beispielfluss zur Funktionsweise der Firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Herstellen einer Verbindung über das Internet
Firewallregeln auf Serverebene wirken sich auf alle Datenbanken auf dem gleichen Azure Database for PostgreSQL-Server aus. Liegt die IP-Adresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung gewährt.
Liegt die IP-Adresse der Anforderung nicht innerhalb der in den Firewallregeln auf Serverebene angegebenen Bereiche, tritt ein Fehler bei der Verbindungsanforderung auf.
Wenn Ihre Anwendung beispielsweise eine Verbindung mit dem JDBC-Treiber für PostgreSQL herstellt, kann bei dem Versuch eines Verbindungsaufbaus möglicherweise folgender Fehler auftreten, wenn die Verbindung durch die Firewall blockiert wird.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: KRITISCH: kein Pg\_hba.conf-Eintrag für Host „123.45.67.890“, Benutzer „Adminuser“, Datenbank „postgresql“, SSL

## <a name="connecting-from-azure"></a>Herstellen einer Verbindung über Azure
Um Anwendungen von Azure die Verbindung mit dem Azure Database for PostgreSQL-Server zu ermöglichen, müssen Azure-Verbindungen ermöglicht werden. Beispiele: Hosten einer Azure-Web-Apps-Anwendung oder einer auf einem virtuellen Azure-Computer ausgeführten Anwendung und Herstellen einer Verbindung über ein Azure Data Factory-Datenverwaltungsgateway. Die Ressourcen müssen sich nicht im gleichen virtuellen Netzwerk (VNET) oder in der gleichen Ressourcengruppe für die Firewallregel befinden, um diese Verbindungen zu ermöglichen. Wenn eine Azure-Anwendung versucht, eine Verbindung mit dem Datenbankserver herzustellen, prüft die Firewall, ob Azure-Verbindungen zulässig sind. Diese Arten von Verbindungen können auf unterschiedliche Weise ermöglicht werden. Eine Firewalleinstellung, bei der die Start- und Endadresse den Wert 0.0.0.0 aufweist, gibt an, dass diese Verbindungen zulässig sind. Alternativ können Sie im Portal im Bereich **Verbindungssicherheit** die Option **Zugriff auf Azure-Dienste erlauben** auf **EIN** festlegen und dann **Speichern** auswählen. Ist der Verbindungsversuch nicht zulässig, erreicht die Anforderung den Azure Database for PostgreSQL-Server nicht.

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

![Konfigurieren von „Zugriff auf Azure-Dienste erlauben“ im Portal](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Programmgesteuertes Verwalten von Firewallregeln
Zusätzlich zum Azure-Portal können Firewallregeln programmgesteuert mithilfe der Azure CLI verwaltet werden.
Weitere Informationen finden Sie unter [Erstellen und Verwalten von Firewallregeln für Azure-Datenbank für PostgreSQL mithilfe der Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="troubleshooting-the-database-server-firewall"></a>Problembehandlung der Datenbankserverfirewall
Wenn der Zugriff auf den Microsoft Azure-Datenbank für PostgreSQL-Serverdienst nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

* **Änderungen an der Zulassungsliste sind noch nicht wirksam**: Änderungen der Firewallkonfiguration für den Azure Database for PostgreSQL-Server werden möglicherweise erst nach fünf Minuten wirksam.

* **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet**: Wenn eine Anmeldung nicht über Berechtigungen für den Azure Database for PostgreSQL-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure Database for PostgreSQL-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, eine Verbindung mit dem Server herzustellen. Jeder Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

Beispielsweise kann bei der Verwendung eines JDBC-Clients folgende Fehlermeldung angezeigt werden.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: KRITISCH: Fehler bei der Kennwortauthentifizierung für Benutzer „IhrBenutzername“

* **Dynamische IP-Adresse**: Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:

* Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure-Datenbank für PostgreSQL-Server zugegriffen wird, und fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.

* Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die statische IP-Adresse als Firewallregel hinzu.

## <a name="next-steps"></a>Nächste Schritte
Artikel zum Erstellen von Firewallregeln auf Serverebene und auf Datenbankebene finden Sie hier:
* [Erstellen und Verwalten von Firewallregeln für Azure-Datenbank für PostgreSQL mithilfe des Azure-Portals](howto-manage-firewall-using-portal.md)
* [Erstellen und Verwalten von Firewallregeln für Azure-Datenbank für PostgreSQL mithilfe der Azure CLI](howto-manage-firewall-using-cli.md)
