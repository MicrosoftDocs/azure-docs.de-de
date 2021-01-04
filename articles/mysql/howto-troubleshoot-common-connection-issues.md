---
title: Beheben von Verbindungsproblemen mit Azure Database for MySQL
description: Erfahren Sie, wie Sie Verbindungsprobleme mit Azure Database for MySQL beheben können, einschließlich vorübergehender Fehler, die Wiederholungsversuche erfordern, Firewallprobleme und Ausfälle.
keywords: MySQL-Verbindung, Verbindungszeichenfolge, Verbindungsprobleme, vorübergehender Fehler, Verbindungsfehler
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: da500087aeb4ad5e2bf516c3d0ce3cceaf8396af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000282"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Beheben von Verbindungsproblemen mit Azure Database for MySQL

Verbindungsprobleme können verschiedene Ursachen haben:

* Firewalleinstellungen
* Verbindungstimeout
* Falsche Anmeldeinformationen
* Maximales Limit für einige Azure Database for MySQL-Ressourcen erreicht
* Probleme mit der Infrastruktur des Diensts
* Derzeit ausgeführte Wartung des Diensts
* Computezuteilung des Servers wird durch das Skalieren der Anzahl von virtuellen Kernen oder den Wechsel in eine andere Dienstebene geändert.

Im Allgemeinen können Verbindungsprobleme mit Azure Database for MySQL wie folgt klassifiziert werden:

* Vorübergehende Fehler (kurzlebige oder zeitweilige Fehler)
* Dauerhafte oder nicht vorübergehende Fehler (Fehler, die sich regelmäßig wiederholen)

## <a name="troubleshoot-transient-errors"></a>Problembehandlung bei vorübergehenden Fehlern

Vorübergehende Fehler treten auf, wenn eine Wartung durchgeführt wird, wenn das System einen Hardware- oder Softwarefehler findet, wenn Sie die virtuellen Kerne oder die Dienstebene des Servers ändern. Der Azure Database for MySQL-Dienst bietet integrierte Hochverfügbarkeit und ist dafür ausgelegt, derartige Probleme automatisch zu umgehen. Allerdings wird für eine kurze Zeit die Verbindung Ihrer Anwendung mit dem Server getrennt – in der Regel für weniger als 60 Sekunden. Das Beenden dieser Ereignisse kann manchmal länger dauern, z.B. wenn eine große Transaktion eine lang andauernde Wiederherstellung auslöst.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Schritte zum Beheben vorübergehender Verbindungsprobleme

1. Überprüfen Sie das [Microsoft Azure-Dienstdashboard](https://azure.microsoft.com/status) auf alle bekannten Ausfälle, die während der Fehlermeldung durch die Anwendung aufgetreten sind.
2. Für Anwendungen, die Verbindungen mit einem Clouddienst wie Azure Database for MySQL herstellen, sollten Sie vorübergehende Fehler erwarten. Daher sollten Sie eine Wiederholungslogik zur Fehlerbehandlung implementieren, anstatt Benutzern diese Anwendungsfehler anzuzeigen. Bewährte Methoden und Entwurfsrichtlinien für das Behandeln vorübergehender Fehler finden Sie unter [Behandeln vorübergehender Konnektivitätsfehler für Azure Database for MySQL](concepts-connectivity.md).
3. Wenn sich ein Server seinen Ressourcenlimits nähert, kann dies wie ein vorübergehendes Verbindungsproblem aussehen. Weitere Informationen finden Sie unter [Beschränkungen in Azure Database for MySQL](concepts-limits.md).
4. Wenn Verbindungsprobleme weiterhin bestehen, die Fehlerdauer 60 Sekunden überschreitet oder der Fehler an einem Tag mehrfach auftritt, schicken Sie eine Azure-Supportanfrage, indem Sie auf der **Azure-Support** -Website [Support erhalten](https://azure.microsoft.com/support/options) auswählen.

## <a name="troubleshoot-persistent-errors"></a>Problembehandlung bei dauerhaften Fehlern

Wenn die Anwendung dauerhaft keine Verbindung mit Azure Database for MySQL herstellen kann, weist dies in der Regel auf Probleme der folgenden Art hin:

* Konfiguration der Serverfirewall: Stellen Sie sicher, dass die Azure Database for MySQL-Serverfirewall so konfiguriert ist, dass Verbindungen von Ihrem Client zulässig sind, einschließlich Proxyserver und Gateways.
* Konfiguration der Clientfirewall: Die Firewall auf dem Client muss Verbindungen mit Ihrem Datenbankserver zulassen. IP-Adressen und Ports des Servers, mit dem Sie eine Verbindung herstellen, sowie Anwendungsnamen wie MySQL müssen in einigen Firewalls zugelassen sein.
* Benutzerfehler: Sie haben möglicherweise Verbindungsparameter wie den Servernamen in der Verbindungszeichenfolge falsch geschrieben, oder es fehlt das Suffix *\@Servername* im Benutzernamen.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Schritte zum Beheben dauerhafter Verbindungsprobleme

1. Richten Sie [Firewallregeln](howto-manage-firewall-using-portal.md) so ein, dass die IP-Adresse des Clients zugelassen wird. Richten Sie nur zu Testzwecken vorübergehend eine Firewallregel mit 0.0.0.0 als erster IP-Adresse und 255.255.255.255 als letzter IP-Adresse fest. Dadurch wird der Server für alle IP-Adressen geöffnet. Wird Ihr Konnektivitätsproblem dadurch behoben, entfernen Sie die Regel, und erstellen Sie eine Firewallregel für eine entsprechend eingeschränkte IP-Adresse bzw. einen entsprechend eingeschränkten IP-Adressbereich.
2. Stellen Sie für alle Firewalls zwischen Client und Internet sicher, dass Port 3306 für ausgehende Verbindungen geöffnet ist.
3. Überprüfen Sie die Verbindungszeichenfolge und andere Verbindungseinstellungen. Lesen Sie unter [Herstellen einer Verbindung zwischen Anwendungen und Azure Database for MySQL](howto-connection-string.md) nach.
4. Überprüfen Sie im Dashboard den Dienststatus. Wenn Sie denken, dass es sich um einen regionalen Ausfall handelt, finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure Database for MySQL](concepts-business-continuity.md) Schritte zur Wiederherstellung in einer neuen Region.

## <a name="next-steps"></a>Nächste Schritte

* [Behandeln vorübergehender Konnektivitätsfehler für Azure Database for MySQL](concepts-connectivity.md)
