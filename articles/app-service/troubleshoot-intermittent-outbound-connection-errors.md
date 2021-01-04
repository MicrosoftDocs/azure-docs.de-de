---
title: Beheben zeitweiliger Fehler bei ausgehenden Verbindungen in Azure App Service
description: Beheben von zeitweiligen Verbindungsfehlern und zugehörigen Leistungsproblemen in Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 989f47c0ff60865a8e8be15e089cdcf96ab2550c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968297"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Beheben zeitweiliger Fehler bei ausgehenden Verbindungen in Azure App Service

In diesem Artikel erfahren Sie, wie Sie zeitweilige Verbindungsfehler und zugehörige Leistungsprobleme in [Azure App Service](./overview.md) beheben. Dieses Thema enthält weitere Informationen zu sowie Problembehandlungsmethoden für die Auslastung von SNAT-Ports (Source Address Network Translation). Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, wenden Sie sich an die Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/). Alternativ dazu können Sie eine Azure-Supportanfrage erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie die Option **Support erhalten**.

## <a name="symptoms"></a>Symptome

Im Azure-App-Dienst gehostete Anwendungen und Funktionen weisen möglicherweise eines oder mehrere der folgenden Symptome auf:

* Langsame Reaktionszeiten bei allen oder einigen der Instanzen in einem Dienstplan.
* Zeitweilig auftretende 5xx- oder **Ungültiges Gateway**-Fehler.
* Timeoutfehlermeldungen
* Es konnte keine Verbindung mit externen Endpunkten hergestellt werden (z. B. SQLDB, Service Fabric, andere App-Dienste usw.).

## <a name="cause"></a>Ursache

Die Hauptursache für zeitweilig auftretende Verbindungsprobleme ist das Erreichen einer Grenze, während neue ausgehende Verbindungen hergestellt werden. Folgende Grenzwerte können erreicht werden:

* TCP-Verbindungen: Die Anzahl herstellbarer ausgehender Verbindungen ist begrenzt. Der Grenzwert für ausgehende Verbindungen ist der Größe des verwendeten Workers zugeordnet.
* SNAT-Ports: [Verwenden von SNAT für ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md) beschreibt SNAT-Port-Einschränkungen und deren Auswirkung auf ausgehende Verbindungen. Azure verwendet die Quell-Netzwerkadressenübersetzung (Source Network Address Translation, SNAT) und Load Balancer (nicht für Kunden verfügbar gemacht), um mit öffentlichen IP-Adressen zu kommunizieren. Jeder Instanz im Azure-App-Dienst wird zunächst eine vorab zugeordnete Anzahl von **128** SNAT-Ports zugewiesen. Dieses SNAT-Port-Limit wirkt sich auf das Öffnen von Verbindungen mit derselben Adress- und Portkombination aus. Wenn Ihre App Verbindungen mit einer Mischung aus Adress- und Portkombinationen herstellt, werden Sie Ihre SNAT-Ports nicht aufbrauchen. Die SNAT-Ports werden aufgebraucht, wenn Sie wiederholten Aufrufe an dieselbe Kombination aus Adresse und Port vornehmen. Nach der Freigabe eines Ports kann er bei Bedarf erneut verwendet werden. Der Azure-Netzwerklastenausgleich gibt SNAT-Ports von geschlossenen Verbindungen erst nach einer Wartezeit von 4 Minuten zurück.

Wenn Anwendungen oder Funktionen schnell eine neue Verbindung öffnen, können Sie ihr vorab zugewiesenes Kontingent von 128 Ports schnell erschöpfen. Sie werden dann blockiert, bis ein neuer SNAT-Port verfügbar wird, entweder durch dynamisches Zuweisen zusätzlicher SNAT-Ports oder durch Wiederverwendung eines freigegebenen SNAT-Ports. Wenn Ihre App nicht mehr über SNAT-Ports verfügt, werden zeitweilig Probleme bei ausgehenden Verbindungen auftreten. 

## <a name="avoiding-the-problem"></a>Vermeiden des Problems

Es gibt einige Lösungen, mit denen Sie SNAT-Port-Einschränkungen vermeiden können. Dazu gehören:

* Verbindungspools: Wenn Sie Ihre Verbindungen bündeln, vermeiden Sie das Öffnen neuer Netzwerkverbindungen für Aufrufe derselben Adresse und desselben Ports.
* Dienstendpunkte: Sie unterliegen keiner SNAT-Port-Einschränkung für die Dienste, die durch Dienstendpunkte gesichert sind.
* Private Endpunkte: Sie unterliegen keiner SNAT-Port-Einschränkung für die Dienste, die durch private Endpunkte gesichert sind.
* NAT Gateway: Mit einem NAT Gateway verfügen Sie über 64 KB ausgehende SNAT-Ports, die von den Ressourcen genutzt werden können, die Datenverkehr darüber senden.

Das Vermeiden des SNAT-Portproblems bedeutet, das wiederholte Erstellen neuer Verbindungen mit demselben Host und Port zu vermeiden. Verbindungspools zählen zu den offensichtlicheren Möglichkeiten, dieses Problem zu lösen.

Wenn Ihr Ziel ein Azure-Dienst ist, der Dienstendpunkte unterstützt, können Sie Probleme durch eine Überlastung von SNAT-Ports vermeiden, indem Sie die [regionale VNET-Integration](./web-sites-integrate-with-vnet.md) und Dienstendpunkte oder private Endpunkte verwenden. Wenn Sie die regionale VNET-Integration verwenden und Endpunkte im Integrationssubnetz platzieren, gelten für den ausgehenden Datenverkehr Ihrer App an diese Dienste keine Einschränkungen für ausgehende SNAT-Ports. Ebenso treten keine Probleme bei ausgehenden SNAT-Ports an dieses Ziel auf, wenn Sie die regionale VNET-Integration und private Endpunkte verwenden. 

Wenn Ihr Ziel ein externer Endpunkt außerhalb von Azure ist, erhalten Sie mithilfe eines NAT Gateways 64 KB ausgehende SNAT-Ports. Außerdem erhalten Sie eine dedizierte ausgehende Adresse, die Sie mit niemandem teilen. 

Wenn möglich, verbessern Sie Ihren Code für die Verwendung von Verbindungspools, und vermeiden Sie die gesamte Situation. Es ist nicht immer möglich, Code schnell genug zu ändern, um diese Situation zu entschärfen. Wenn Sie den Code nicht rechtzeitig ändern können, nutzen Sie die anderen Lösungen. Die beste Lösung des Problems ist die bestmögliche Kombination aller Lösungen. Versuchen Sie, Dienstendpunkte und private Endpunkte für Azure-Dienste zu verwenden und das NAT Gateway für den Rest. 

Allgemeine Strategien zum Verringern der SNAT-Portauslastung finden Sie im Abschnitt zur [Problembehebung](../load-balancer/load-balancer-outbound-connections.md) der Dokumentation **Ausgehende Verbindungen von Azure**. Von diesen Strategien sind die folgenden anwendbar auf Apps und Funktionen, die im Azure-App-Dienst gehostet werden.

### <a name="modify-the-application-to-use-connection-pooling"></a>Ändern der Anwendung für die Verwendung von Verbindungspooling

* Lesen Sie zum Pooling von HTTP-Verbindungen [Poolen von HTTP-Verbindungen mit HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Informationen zum SQL Server-Verbindungspooling finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Informationen zum Implementieren von Pooling mit Entity Framework-Anwendungen finden Sie unter [DBContext-Pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Im Folgenden finden Sie eine Auflistung von Links zum Implementieren von Verbindungspooling nach unterschiedlichem Lösungsstapel.

#### <a name="node"></a>Node

Standardmäßig werden Verbindungen für NodeJS nicht aufrechterhalten. Unten sind die gängigen Datenbanken und Pakete für das Verbindungspooling angegeben, die auch Beispiele zur Implementierung enthalten.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP-Keep-Alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0: Dokumentation](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Unten sind die gängigen Bibliotheken aufgeführt, die für das JDBC-Verbindungspooling verwendet werden und Beispiele für die Implementierung enthalten: JDBC-Verbindungspooling.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP-Verbindungspooling

* [Apache-Verbindungsverwaltung](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager-Klasse](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Von PHP wird das Verbindungspooling zwar nicht unterstützt, aber Sie können versuchen, dauerhafte Datenbankverbindungen auf Ihrem Back-End-Server zu verwenden.
 
* MySQL-Server

   * [MySQLi-Verbindungen](https://www.php.net/manual/mysqli.quickstart.connections.php) für neuere Versionen
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) für ältere Versionen von PHP

* Weitere Datenquellen

   * [PHP-Verbindungsverwaltung](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Ändern der Anwendung für die Wiederverwendung von Verbindungen

*  Weitere Hinweise und Beispiele zum Verwalten von Verbindungen in Azure Functions finden Sie unter [Verwalten von Verbindungen in Azure Functions](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Ändern der Anwendung für die Verwendung weniger aggressiver Wiederholungslogik

* Weitere Anleitungen und Beispiele finden Sie unter [Wiederholungsmuster](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Verwenden von Keep-Alives zum Zurücksetzen des Leerlauftimeouts für ausgehende Verbindungen

* Informationen zum Implementieren von Keep-Alives für Node.js-Apps finden Sie unter [Meine Node-Anwendung führt zu viele ausgehende Aufrufe durch](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Zusätzliche Anleitungen speziell für App Service:

* Ein [Auslastungstest](/azure/devops/test/load-test/app-service-web-app-performance-test) sollte reale Daten mit einer konstanten Zuführungsgeschwindigkeit simulieren. Mit dem Testen von Apps und Funktionen unter realen Belastungsbedingungen können Probleme mit der SNAT-Portauslastung vorzeitig erkannt und behoben werden.
* Stellen Sie sicher, dass die Back-End-Dienste Antworten schnell zurückgeben können. Informationen zum Behandeln von Problemen mit der Azure SQL-Datenbank finden Sie unter [Behandeln von Problemen mit der Leistung von Azure SQL-Datenbank mithilfe von Intelligent Insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Erweitern Sie den App Service-Plan auf weitere Instanzen. Weitere Informationen zur Skalierung finden Sie unter [Skalieren einer App in Azure App Service](./manage-scale-up.md). Jeder Workerinstanz in einem App Service-Plan wird eine Anzahl von SNAT-Ports zugeordnet. Wenn Sie Ihre Nutzung auf mehrere Instanzen verteilen, können Sie eventuell eine SNAT-Portnutzung pro Instanz unterhalb des empfohlenen Limits von 100 ausgehenden Verbindungen pro eindeutigem Remoteendpunkt erreichen.
* Erwägen Sie die Umstellung auf [App Service-Umgebung (ASE)](./environment/using-an-ase.md), wobei Ihnen eine einzelne ausgehende IP-Adresse zugewiesen wird und die Grenzwerte für Verbindungen und SNAT-Ports weitaus höher sind. In einer ASE basiert die Anzahl von SNAT-Ports pro Instanz auf der [Azure Load Balancer-Vorabzuordnungentabelle](../load-balancer/load-balancer-outbound-connections.md#snatporttable). So verfügt z. B. eine ASE mit 1-50 Workerinstanzen über 1024 vorab zugeordnete Ports pro Instanz, während eine ASE mit 51-100 Workerinstanzen über 512 vorab zugeordnete Ports pro Instanz verfügt.

Die Vermeidung der ausgehenden TCP-Limits lässt sich leichter erreichen, weil die Limits durch die Größe Ihres Workers festgelegt werden. Sie finden die Limits in [Numerische Limits für sandboxübergreifende VM: TCP-Verbindungen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

|Limitname|BESCHREIBUNG|Klein (A1)|Mittel (A2)|Groß (A3)|Dienstebene „Isoliert“ (ASE)|
|---|---|---|---|---|---|
|Verbindungen|Anzahl von Verbindungen in der gesamten VM|1920|3968|8064|16.000|

Um ausgehende TCP-Limits zu vermeiden, können Sie entweder die Größe Ihres Workers erhöhen oder horizontal erweitern.

## <a name="troubleshooting"></a>Problembehandlung

Die Kenntnis der beiden Arten von Limits für ausgehende Verbindungen sowie des Verhaltens Ihrer App sollte die Problembehandlung erleichtern. Wenn Sie wissen, dass Ihre App viele Aufrufe an dasselbe Speicherkonto durchführt, könnten Sie ein SNAT-Limit vermuten. Wenn Ihre App viele Aufrufe an Endpunkte über das Internet erstellt, könnten Sie vermuten, dass Sie das VM-Limit erreichen werden.

Wenn Sie das Anwendungsverhalten nicht gut genug kennen, um die Ursache schnell zu ermitteln, gibt es einige Tools und Methoden, die in App Service zur Unterstützung dieser Ermittlungsaufgabe verfügbar sind.

### <a name="find-snat-port-allocation-information"></a>Auffinden von SNAT-Portzuordnungsinformationen

Sie können die [App Service-Diagnose](./overview-diagnostics.md) verwenden, um SNAT-Portzuordnungsinformationen zu suchen, und die Zuordnungsmetrik von SNAT-Ports für eine App Service-Site beobachten. Gehen Sie wie folgt vor, um SNAT-Portzuordnungsinformationen zu finden:

1. Navigieren Sie für den Zugriff auf die App Service-Diagnose zu Ihrer App Service-Web-App oder App Service-Umgebung im [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Navigationsbereich **Diagnose und Problembehandlung** aus.
2. Wählen Sie die Kategorie „Verfügbarkeit und Leistung“ aus.
3. Wählen Sie in der Liste der verfügbaren Kacheln unter der Kategorie die Kachel „SNAT-Portauslastung“ aus. Die Vorgehensweise besteht darin, den Wert unter 128 zu halten.
Wenn Sie dies benötigen, können Sie immer noch ein Supportticket öffnen, und der Supporttechniker ruft die Metrik vom Back-End für Sie ab.

Da die SNAT-Portauslastung nicht als Metrik verfügbar ist, ist es nicht möglich, entweder automatisch auf Grundlage der SNAT-Portauslastung zu skalieren oder die automatische Skalierung auf Grundlage der SNAT-Portzuordnungsmetrik zu konfigurieren.

### <a name="tcp-connections-and-snat-ports"></a>TCP-Verbindungen und SNAT-Ports

TCP-Verbindungen und SNAT-Ports stehen in keinem direkten Zusammenhang. Eine Auslastungserkennung für TCP-Verbindungen befindet sich auf dem Blatt „Diagnose und Problembehandlung“ jeder App Service-Site. Suchen Sie nach dem Ausdruck „TCP-Verbindungen“, um sie zu finden.

* Die SNAT-Ports werden nur für externe Netzwerkflows verwendet, während die gesamten TCP-Verbindungen lokale Loopbackverbindungen enthalten.
* Ein SNAT-Port kann von verschiedenen Flows gemeinsam genutzt werden, wenn sich die Flows im Protokoll, bei der IP-Adresse oder dem Port unterscheiden. Die TCP-Verbindungenmetrik zählt jede TCP-Verbindung.
* Das Limit für TCP-Verbindungen gilt auf Workerinstanzebene. Der ausgehende Lastenausgleich im Azure-Netzwerk verwendet die TCP-Verbindungenmetrik nicht für die SNAT-Portbegrenzung.
* Sie finden die TCP-Verbindungslimits in [Numerische Limits für sandboxübergreifende VM: TCP-Verbindungen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits).

|Limitname|BESCHREIBUNG|Klein (A1)|Mittel (A2)|Groß (A3)|Dienstebene „Isoliert“ (ASE)|
|---|---|---|---|---|---|
|Verbindungen|Anzahl von Verbindungen in der gesamten VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>WebJobs und Datenbankverbindungen
 
Wenn WebJobs wegen erschöpfter SNAT-Ports keine Verbindung mehr mit der SQL-Datenbank herstellen können, gibt es keine Metrik, die anzeigt, wie viele Verbindungen von den einzelnen Webanwendungsprozessen geöffnet sind. Um den problematischen WebJob zu finden, verschieben Sie mehrere WebJobs in einen anderen App Service-Plan, um festzustellen, ob sich die Situation verbessert, oder ob ein Problem in einem der Pläne bestehen bleibt. Wiederholen Sie den Vorgang, bis Sie den problematischen WebJob gefunden haben.

### <a name="using-snat-ports-sooner"></a>Früheres Verwenden von SNAT-Ports

Sie können keine Azure-Einstellungen dahingehend ändern, dass die verwendeten SNAT-Ports früher freigegeben werden, da alle SNAT-Ports gemäß den folgenden Bedingungen freigegeben werden, wobei dieses Verhalten entwurfsbedingt ist.
 
* Wenn entweder der Server oder der Client FINACK sendet, wird der [SNAT-Port nach 240 Sekunden freigegeben](../load-balancer/load-balancer-outbound-connections.md).
* Tritt ein RST auf, wird der SNAT-Port nach 15 Sekunden freigegeben.
* Wird das Leerlauftimeout erreicht, wird der Port freigegeben.
 
## <a name="additional-information"></a>Zusätzliche Informationen

* [SNAT mit App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Problembehandlung bei niedriger App-Leistung in Azure App Service](./troubleshoot-performance-degradation.md)
