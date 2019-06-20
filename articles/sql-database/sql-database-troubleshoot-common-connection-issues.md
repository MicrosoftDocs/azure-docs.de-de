---
title: Behandeln von häufigen Verbindungsproblemen mit Azure SQL-Datenbank
description: Schritte zum Ermitteln und Behandeln von häufigen Verbindungsproblemen für Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d278fd6ed06b58db052154e632e565de36853e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331433"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Beheben von Verbindungsproblemen mit der Azure SQL-Datenbank

Wenn keine Verbindung zur Azure SQL-Datenbank hergestellt werden kann, erhalten Sie [Fehlermeldungen](sql-database-develop-error-messages.md). Bei diesem Artikel handelt es sich um eine zentrale Informationsquelle, die Sie bei der Behebung von Problemen mit der Verbindung mit Azure SQL-Datenbank unterstützt. In dem Artikel werden [die häufigsten Ursachen](#cause) von Verbindungsproblemen dargestellt, [ein Tool zur Problembehandlung](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) empfohlen, das Sie bei der Ermittlung des Problems unterstützt, sowie Problembehandlungsschritte zur Behebung von [vorübergehenden Fehlern](#troubleshoot-transient-errors) und [dauerhaften oder nicht vorübergehenden Fehlern](#troubleshoot-persistent-errors) bereitgestellt. 

Führen Sie bei Verbindungsproblemen die in diesem Artikel beschriebenen Schritte zur Problembehandlung aus.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Ursache

Verbindungsprobleme können folgende Ursachen haben:

* Fehler beim Anwenden von bewährten Methoden und Designrichtlinien während des Anwendungsdesignprozesses.  Hilfreiche Informationen für den Einstieg finden Sie in der [Übersicht über die Entwicklung von SQL-Datenbanken](sql-database-develop-overview.md) .
* Neukonfiguration der Azure SQL-Datenbank
* Firewalleinstellungen
* Verbindungstimeout
* Falsche Anmeldeinformationen
* Einige Azure SQL-Datenbank-Ressourcen haben ihr maximales Limit erreicht

Im Allgemeinen können Verbindungsprobleme mit der Azure SQL-Datenbank wie folgt klassifiziert werden:

* [Vorübergehende Fehler (kurzlebige oder zeitweilige Fehler)](#troubleshoot-transient-errors)
* [Dauerhafte oder nicht vorübergehende Fehler (Fehler, die sich regelmäßig wiederholen)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Verwenden der Problembehandlung für Verbindungsprobleme mit der Azure SQL-Datenbank

Wenn Sie einen bestimmten Verbindungsfehler erkennen, können Sie das Problem mit [diesem Tool](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)schnell ermitteln und beheben.

## <a name="troubleshoot-transient-errors"></a>Problembehandlung bei vorübergehenden Fehlern

Wenn eine Anwendung eine Verbindung zu einer Azure SQL-Datenbank herstellt, wird die folgende Fehlermeldung angezeigt:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Diese ist eine in der Regel vorübergehende (kurzlebige) Fehlermeldung.
> 
> 

Dieser Fehler tritt auf, wenn die Datenbank verschoben (oder neu konfiguriert) wird und Ihre Anwendung die Verbindung zur Datenbank verliert. Ereignisse bei der Neukonfiguration von Datenbanken werden durch geplante Ereignisse (z.B. Softwareupgrades) oder ungeplante Ereignisse (z.B. einen Prozessabsturz oder einen Lastenausgleich) hervorgerufen. Die meisten bei einer Neukonfiguration auftretenden Ereignisse sind normalerweise kurzlebig und sollten innerhalb von höchstens 60 Sekunden beendet werden. Trotzdem kann das Beenden dieser Ereignisse beizeiten länger dauern, so z.B., wenn eine große Transaktion eine lang andauernde Wiederherstellung auslöst.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Schritte zum Beheben vorübergehender Verbindungsprobleme

1. Ziehen Sie das [Microsoft Azure Service-Dashboard](https://azure.microsoft.com/status) für alle bekannten Ausfälle zu Rat, die während der Fehlermeldung durch die Anwendung aufgetreten sind.
2. Für Anwendungen, die Verbindungen zu einem Clouddienst wie Azure SQL-Datenbank herstellen, sollten Sie regelmäßige Rekonfigurationsereignisse erwarten. Daher sollten Sie eine Wiederholungslogik zur Fehlerbehandlung implementieren, anstatt Benutzern diese Anwendungsfehler anzuzeigen. Weitere Informationen und allgemeine Wiederholungsstrategien finden Sie im Abschnitt [Vorübergehende Fehler](sql-database-connectivity-issues.md) sowie in den bewährten Methoden und Designrichtlinien der [Übersicht über die Entwicklung von SQL-Datenbanken](sql-database-develop-overview.md). Tiefer gehende Informationen finden Sie anschließend in den Codebeispielen in den [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md).
3. Wenn sich eine Datenbank ihren Ressourcenbegrenzungen nähert, kann dies wie ein vorübergehendes Verbindungsproblem aussehen. Sie [Ressourceneinschränkungen](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Wenn Verbindungsprobleme weiterhin bestehen, die Fehlerdauer 60 Sekunden überschreitet oder der Fehler an einem Tag mehrfach auftritt, schicken Sie eine Azure-Supportanfrage, indem Sie auf der **Azure-Support** -Website [Support erhalten](https://azure.microsoft.com/support/options) auswählen.

## <a name="troubleshoot-persistent-errors"></a>Problembehandlung bei dauerhaften Fehlern
Wenn die Anwendung dauerhaft keine Verbindung mit der Azure SQL-Datenbank herstellen kann, weist dies in der Regel auf Probleme der folgenden Art hin:

* Firewall-Konfiguration Die Firewall von Azure SQL-Datenbank oder eine clientseitige Firewall blockiert Verbindungen mit Azure SQL-Datenbank.
* Netzwerkneukonfiguration auf Clientseite: z.B. eine neue IP-Adresse oder ein Proxyserver.
* Benutzerfehler: z.B. falsch geschriebene Verbindungsparameter wie der Servername in der Verbindungszeichenfolge.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Schritte zum Beheben dauerhafter Verbindungsprobleme
1. Richten Sie [Firewallregeln](sql-database-configure-firewall-settings.md) so ein, dass die IP-Adresse des Clients zugelassen wird. Richten Sie zu Testzwecken vorübergehend eine Firewallregel mit 0.0.0.0 als Beginn des IP-Adressbereichs und 255.255.255.255 als Ende des IP-Adressbereichs fest. Dadurch wird der Server für alle IP-Adressen geöffnet. Wird Ihr Konnektivitätsproblem dadurch behoben, entfernen Sie die Regel, und erstellen Sie eine Firewallregel für eine entsprechend eingeschränkte IP-Adresse bzw. einen entsprechend eingeschränkten IP-Adressbereich. 
2. Stellen Sie für alle Firewalls zwischen Client und Internet sicher, dass Port 1433 für ausgehende Verbindungen geöffnet ist. Zusätzliche Anhaltspunkte zu weiteren Ports, die für die Azure Active Directory-Authentifizierung geöffnet werden müssen, finden Sie unter [Konfigurieren der Windows-Firewall, um SQL Server-Zugriff zuzulassen](https://msdn.microsoft.com/library/cc646023.aspx) und [Erforderliche Ports und Protokolle für die Hybrid-Identität](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports).
3. Überprüfen Sie die Verbindungszeichenfolge und andere Verbindungseinstellungen. Siehe im Thema [Verbindungsprobleme](sql-database-connectivity-issues.md#connections-to-sql-database)den Abschnitt „Verbindungszeichenfolge“.
4. Überprüfen Sie im Dashboard den Dienststatus. Wenn Sie glauben, dass es sich um einen regionalen Ausfall handelt, finden Sie unter [Wiederherstellen nach einem Ausfall](sql-database-disaster-recovery.md) Schritte zum Wiederherstellen in einer neuen Region.

## <a name="next-steps"></a>Nächste Schritte
* [Durchsuchen der Dokumentation zu Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [Anzeigen der neuesten Updates zum Azure SQL-Datenbank-Dienst](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Übersicht über die Entwicklung von SQL-Datenbanken](sql-database-develop-overview.md)
* [Allgemeiner Leitfaden zur Behandlung vorübergehender Fehler](../best-practices-retry-general.md)
* [Verbindungsbibliotheken für SQL-Datenbank und SQL Server](sql-database-libraries.md)

