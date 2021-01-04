---
title: Überwachungsprotokolle für Azure Web Application Firewall
description: Erfahren Sie, wie Sie Protokolle für Azure Web Application Firewall aktivieren und verwalten.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 262987b5d5cdccec967193d855b17c5c74e16575
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563391"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Ressourcenprotokolle für Azure Web Application Firewall

Sie können Web Application Firewall-Ressourcen mithilfe von Protokollen überwachen. Sie ermöglichen das Speichern und Nutzen von Leistungs-, Zugriffs- und anderen Daten einer Ressource zu Überwachungszwecken.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>Diagnoseprotokolle

Sie können in Azure verschiedene Protokolltypen verwenden, um Anwendungsgateways zu verwalten und eventuelle Fehler zu beheben. Sie können auf einige dieser Protokolle über das Portal zugreifen. Alle Protokolle können aus Azure Blob Storage extrahiert und in anderen Tools wie [Azure Monitor-Protokollen](../../azure-monitor/insights/azure-networking-analytics.md), Excel und Power BI angezeigt werden. In der folgenden Liste finden Sie weitere Informationen über die verschiedenen Typen von Protokollen:

* **Aktivitätsprotokoll:** Mit dem Feature [Azure-Aktivitätsprotokolle](../../azure-resource-manager/management/view-activity-logs.md) können Sie sämtliche an Ihr Azure-Abonnement übermittelten Vorgänge sowie deren Status anzeigen. Aktivitätsprotokolleinträge werden standardmäßig gesammelt und können im Azure-Portal angezeigt werden.
* **Zugriffsressourcenprotokoll**: Mithilfe dieses Protokolls können Sie Application Gateway-Zugriffsmuster anzeigen und wichtige Informationen analysieren. Dazu gehören die IP des Aufrufers, die angeforderte URL, die Antwortlatenz, der Rückgabecode sowie die ein- und ausgehenden Bytes. Ein Zugriffsprotokoll wird alle 300 Sekunden erstellt. Dieses Protokoll enthält einen Datensatz pro Instanz von Application Gateway. Die Application Gateway-Instanz wird anhand der InstanceId-Eigenschaft identifiziert.
* **Leistungsressourcenprotokoll**: Mithilfe dieses Protokolls können Sie die Leistung von Application Gateway-Instanzen anzeigen. In diesem Protokoll werden Leistungsinformationen für jede Instanz erfasst, z.B. insgesamt bereitgestellte Anforderungen, Durchsatz in Byte, Anzahl von Anforderungen mit Fehlern und die Anzahl von fehlerfreien und fehlerhaften Back-End-Instanzen. Ein Leistungsprotokoll wird alle 60 Sekunden erstellt. Das Leistungsprotokoll ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](../../application-gateway/application-gateway-metrics.md) für die Leistungsdaten.
* **Firewallressourcenprotokoll**: Mithilfe dieses Protokolls können Sie die Anforderungen anzeigen, die entweder über den Erkennungs- oder über den Schutzmodus eines Anwendungsgateways protokolliert werden, das mit der Web Application Firewall konfiguriert wurde.

> [!NOTE]
> Protokolle sind nur für Ressourcen verfügbar, die über das Azure Resource Manager-Bereitstellungsmodell bereitgestellt werden. Sie können Protokolle nicht für Ressourcen im klassischen Bereitstellungsmodell verwenden. Ein besseres Verständnis der beiden Modelle können Sie entwickeln, indem Sie den Artikel [Grundlegendes zur Bereitstellung über den Resource Manager im Vergleich zur klassischen Bereitstellung](../../azure-resource-manager/management/deployment-models.md) lesen.

Sie haben drei Möglichkeiten, um Ihre Protokolle zu speichern:

* **Speicherkonto**: Speicherkonten eignen sich am besten für Protokolle, die eine längere Zeit gespeichert und bei Bedarf überprüft werden.
* **Event Hubs:** Event Hubs-Instanzen sind eine hervorragende Möglichkeit für die Integration in andere SIEM-Tools (Security Information and Event Management), um Warnungen für Ihre Ressourcen zu erhalten.
* **Azure Monitor-Protokolle:** Azure Monitor-Protokolle eignen sich am besten für eine allgemeine Echtzeitüberwachung Ihrer Anwendung oder zum Beobachten von Trends.

### <a name="enable-logging-through-powershell"></a>Ermöglichen der Protokollierung mit PowerShell

Die Aktivitätsprotokollierung ist automatisch für alle Resource Manager-Ressourcen aktiviert. Sie müssen die Zugriffs- und Leistungsprotokollierung aktivieren, um mit der Erfassung von Daten aus diesen Protokollen zu beginnen. Führen Sie zum Aktivieren der Protokollierung die folgenden Schritte aus:

1. Notieren Sie sich die Ressourcen-ID Ihres Speicherkontos, unter dem die Protokolldaten gespeichert werden. Dieser Wert hat das folgende Format: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>. Sie können jedes Speicherkonto Ihres Abonnements verwenden. Sie können das Azure-Portal verwenden, um nach diesen Informationen zu suchen.

    ![Portal: Ressourcen-ID für Speicherkonto](../media/web-application-firewall-logs/diagnostics1.png)

2. Notieren Sie sich die Ressourcen-ID Ihres Anwendungsgateways, für das die Protokollierung aktiviert ist. Dieser Wert hat das folgende Format: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>. Sie können das Portal verwenden, um nach diesen Informationen zu suchen.

    ![Portal: Ressourcen-ID für das Anwendungsgateway](../media/web-application-firewall-logs/diagnostics2.png)

3. Aktivieren Sie die Ressourcenprotokollierung mit dem folgenden PowerShell-Cmdlet:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Für Aktivitätsprotokolle ist kein separates Speicherkonto erforderlich. Für die Nutzung des Speichers für die Zugriffs- und Leistungsprotokollierung fallen Dienstgebühren an.

### <a name="enable-logging-through-the-azure-portal"></a>Ermöglichen der Protokollierung über das Azure-Portal

1. Suchen Sie im Azure-Portal nach Ihrer Ressource, und wählen Sie **Diagnoseeinstellungen** aus.

   Für Application Gateway sind drei Protokolle verfügbar:

   * Zugriffsprotokoll
   * Leistungsprotokoll
   * Firewallprotokoll

2. Wählen Sie **Diagnose aktivieren** aus, um die Erfassung von Daten zu starten.

   ![Aktivieren der Diagnose][1]

3. Auf der Seite **Diagnoseeinstellungen** befinden sich die Einstellungen für die Ressourcenprotokolle. In diesem Beispiel werden die Protokolle in Log Analytics gespeichert. Sie können auch Event Hubs und ein Speicherkonto verwenden, um die Ressourcenprotokolle zu speichern.

   ![Starten des Konfigurationsprozesses][2]

5. Geben Sie einen Namen für die Einstellungen ein, bestätigen Sie die Einstellungen, und wählen Sie **Speichern** aus.

### <a name="activity-log"></a>Aktivitätsprotokoll

Das Aktivitätsprotokoll wird von Azure standardmäßig generiert. Die Protokolle werden 90 Tage lang im Azure-Ereignisprotokollspeicher aufbewahrt. Weitere Informationen zu diesen Protokollen finden Sie im Artikel [Anzeigen von Ereignis- und Aktivitätsprotokollen](../../azure-resource-manager/management/view-activity-logs.md).

### <a name="access-log"></a>Zugriffsprotokoll

Das Zugriffsprotokoll wird nur generiert, wenn Sie es auf jeder Application Gateway-Instanz gemäß den obigen Schritten aktiviert haben. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Jeder Application Gateway-Zugriff wird wie im folgenden Beispiel für v1 im JSON-Format protokolliert:

|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, von der die Anforderung bereitgestellt wurde        |
|clientIP     | Ursprungs-IP für die Anforderung        |
|clientPort     | Ursprungsport für die Anforderung       |
|httpMethod     | Von der Anforderung verwendete HTTP-Methode       |
|requestUri     | URI der empfangenen Anforderung        |
|RequestQuery     | **Server-Routed:** Back-End-Poolinstanz, an die die Anforderung gesendet wurde.</br>**X-AzureApplicationGateway-LOG-ID:** Korrelations-ID, die für die Anforderung verwendet wurde. Kann für die Behandlung von Datenverkehrsproblemen auf den Back-End-Servern verwendet werden. </br>**SERVER-STATUS:** HTTP-Antwortcode, den Application Gateway vom Back-End empfangen hat.       |
|UserAgent     | Benutzer-Agent aus dem HTTP-Anforderungsheader        |
|httpStatus     | HTTP-Statuscode, der vom Application Gateway an den Client zurückgegeben wurde       |
|httpVersion     | HTTP-Version der Anforderung        |
|receivedBytes     | Größe des empfangenen Pakets in Byte        |
|sentBytes| Größe des gesendeten Pakets in Byte|
|timeTaken| Dauer (in Millisekunden), bis eine Anforderung verarbeitet und die dazugehörige Antwort gesendet wurde. Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Hierbei ist der Hinweis wichtig, dass das Feld „Time-Taken“ normalerweise die Zeitdauer enthält, die von den Anforderungs- und Antwortpaketen für die Übermittlung über das Netzwerk benötigt wird. |
|sslEnabled| Gibt an, ob für die Kommunikation mit den Back-End-Pools TLS/SSL verwendet wurde. Gültige Werte sind „on“ und „off“.|
|host| Der Hostname, mit dem die Anforderung an den Back-End-Server gesendet wurde. Wenn der Back-End-Hostname überschrieben wird, zeigt sich dies in diesem Namen.|
|originalHost| Der Hostname, mit dem die Anforderung vom Client von der Application Gateway-Instanz empfangen wurde.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Für Application Gateway und WAF v2 zeigen die Protokolle noch einige zusätzliche Informationen an:

|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, von der die Anforderung bereitgestellt wurde        |
|clientIP     | Ursprungs-IP für die Anforderung        |
|clientPort     | Ursprungsport für die Anforderung       |
|httpMethod     | Von der Anforderung verwendete HTTP-Methode       |
|requestUri     | URI der empfangenen Anforderung        |
|UserAgent     | Benutzer-Agent aus dem HTTP-Anforderungsheader        |
|httpStatus     | HTTP-Statuscode, der vom Application Gateway an den Client zurückgegeben wurde       |
|httpVersion     | HTTP-Version der Anforderung        |
|receivedBytes     | Größe des empfangenen Pakets in Byte        |
|sentBytes| Größe des gesendeten Pakets in Byte|
|timeTaken| Dauer (in Millisekunden), bis eine Anforderung verarbeitet und die dazugehörige Antwort gesendet wurde. Dies wird als Intervall zwischen dem Zeitpunkt, zu dem Application Gateway das erste Byte einer HTTP-Anforderung empfängt, bis zu dem Zeitpunkt berechnet, zu dem der Vorgang zum Senden der Antwort abgeschlossen ist. Hierbei ist der Hinweis wichtig, dass das Feld „Time-Taken“ normalerweise die Zeitdauer enthält, die von den Anforderungs- und Antwortpaketen für die Übermittlung über das Netzwerk benötigt wird. |
|sslEnabled| Gibt an, ob für die Kommunikation mit den Back-End-Pools TLS verwendet wurde. Gültige Werte sind „on“ und „off“.|
|sslCipher| Verschlüsselungssammlung, die für die TLS-Kommunikation verwendet wird (sofern TLS aktiviert ist).|
|sslProtocol| Das verwendete TLS-Protokoll (sofern TLS aktiviert ist).|
|serverRouted| Back-End-Server, an den das Anwendungsgateway die Anforderung weiterleitet|
|serverStatus| HTTP-Statuscode des Back-End-Servers|
|serverResponseLatency| Wartezeit für die Antwort vom Back-End-Server|
|host| Adresse im host-Header der Anforderung|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Leistungsprotokoll

Das Leistungsprotokoll wird nur generiert, wenn Sie es auf jeder Application Gateway-Instanz gemäß den obigen Schritten aktiviert haben. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die Daten für das Leistungsprotokoll werden in Intervallen von einer Minute generiert. Diese Funktion ist nur für die v1-SKU verfügbar. Verwenden Sie für die v2-SKU [Metriken](../../application-gateway/application-gateway-metrics.md) für die Leistungsdaten. Die folgenden Daten werden protokolliert:


|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     |  Application Gateway-Instanz, für die Leistungsdaten generiert werden. Für ein Anwendungsgateway mit mehreren Instanzen ist eine Zeile pro Instanz vorhanden.        |
|healthyHostCount     | Anzahl von fehlerfreien Hosts im Back-End-Pool        |
|unHealthyHostCount     | Anzahl von fehlerhaften Hosts im Back-End-Pool        |
|requestCount     | Anzahl von bereitgestellten Anforderungen        |
|latency | Durchschnittliche Wartezeit (in Millisekunden) für Anforderungen von der Instanz an das Back-End, das die Anforderungen verarbeitet. |
|failedRequestCount| Anzahl von fehlerhaften Anforderungen|
|throughput| Durchschnittlicher Durchsatz seit dem letzten Protokoll, gemessen in Bytes pro Sekunde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latenz wird von dem Zeitpunkt, zu dem das erste Byte der HTTP-Anforderung empfangen wird, bis zu dem Zeitpunkt berechnet, zu dem das letzte Byte der HTTP-Antwort gesendet wird. Dies ist die Summe der Application Gateway-Verarbeitungszeit plus Netzwerkkosten zum Back-End plus die Zeit, die das Back-End zum Verarbeiten der Anforderung benötigt.

### <a name="firewall-log"></a>Firewallprotokoll

Das Firewallprotokoll wird nur generiert, wenn Sie es für jedes Anwendungsgateway gemäß den obigen Schritten aktiviert haben. Für dieses Protokoll muss zudem die Web Application Firewall auf einem Anwendungsgateway konfiguriert sein. Die Daten werden in dem Speicherkonto gespeichert, das Sie beim Aktivieren der Protokollierung angegeben haben. Die folgenden Daten werden protokolliert:


|Wert  |BESCHREIBUNG  |
|---------|---------|
|instanceId     | Application Gateway-Instanz, für die Firewalldaten generiert werden. Für ein Anwendungsgateway mit mehreren Instanzen ist eine Zeile pro Instanz vorhanden.         |
|clientIp     |   Ursprungs-IP für die Anforderung      |
|clientPort     |  Ursprungsport für die Anforderung       |
|requestUri     | URI der empfangenen Anforderung       |
|ruleSetType     | Regelsatztyp: Der verfügbare Wert ist OWASP.        |
|ruleSetVersion     | Verwendete Regelsatzversion. Verfügbare Werte sind 2.2.9 und 3.0.     |
|ruleId     | Regel-ID des auslösenden Ereignisses        |
|message     | Benutzerfreundliche Meldung für das auslösende Ereignis. Weitere Details im Abschnitt „Details“.        |
|action     |  Aktion, die für die Anforderung durchgeführt wird. Verfügbare Werte sind „Blocked“ und „Allowed“.      |
|site     | Standort, für den das Protokoll generiert wurde. Derzeit ist nur „Global“ aufgeführt, da Regeln global sind.|
|Details     | Details zum auslösenden Ereignis        |
|details.message     | Beschreibung der Regel        |
|details.data     | Spezifische Daten in der Anforderung, die eine Übereinstimmung mit der Regel ergeben         |
|details.file     | Konfigurationsdatei, die die Regel enthalten hat        |
|details.line     | Nummer der Zeile in der Konfigurationsdatei, die das Ereignis ausgelöst hat       |
|hostname   | Hostname oder IP-Adresse für Application Gateway.    |
|transactionId  | Eindeutige ID für eine bestimmte Transaktion, die Sie beim Gruppieren mehrerer Regelverstöße innerhalb derselben Anforderung unterstützt.   |
|policyId   | Eindeutige ID der Firewallrichtlinie, die Application Gateway, dem Listener oder dem Pfad zugeordnet ist.   |
|policyScope    | Der Speicherort der Richtlinie. Die Werte können „Global“, „Listener“ oder „Location“ lauten.   |
|policyScopeName   | Der Name des Objekts, auf das die Richtlinie angewendet wird.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Anzeigen und Analysieren des Aktivitätsprotokolls

Mit einer der folgenden Methoden können Sie die Aktivitätsprotokolldaten anzeigen und analysieren:

* **Azure-Tools:** Rufen Sie Informationen aus dem Aktivitätsprotokoll über Azure PowerShell, über die Azure-Befehlszeilenschnittstelle, mithilfe der Azure-REST-API oder über das Azure-Portal ab. Detaillierte Anleitungen für die einzelnen Methoden finden Sie im Artikel [Überwachen von Vorgängen mit dem Ressourcen-Manager](../../azure-resource-manager/management/view-activity-logs.md) .
* **Power BI**: Falls Sie noch kein [Power BI](https://powerbi.microsoft.com/pricing)-Konto besitzen, können Sie es kostenlos testen. Mithilfe der [Power BI-Vorlagen-Apps](/power-bi/service-template-apps-overview) können Sie Ihre Daten analysieren.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>Anzeigen und Analysieren der Zugriffs-, Leistungs- und Firewallprotokolle

[Azure Monitor-Protokolle](../../azure-monitor/insights/azure-networking-analytics.md) können die Indikator- und Ereignisprotokolldateien aus Ihrem Blobspeicherkonto erfassen. Die Anwendung umfasst Visualisierungen und leistungsfähige Suchfunktionen zum Analysieren Ihrer Protokolle.

Sie können auch eine Verbindung mit Ihrem Speicherkonto herstellen und die JSON-Protokolleinträge für Zugriffs- und Leistungsprotokolle abrufen. Nachdem Sie die JSON-Dateien heruntergeladen haben, können Sie diese in das CSV-Format konvertieren oder in Excel, Power BI oder einem anderen Datenvisualisierungstool anzeigen.

> [!TIP]
> Wenn Sie mit Visual Studio und den grundlegenden Konzepten zum Ändern der Werte für Konstanten und Variablen in C# vertraut sind, können Sie die [Protokollkonvertierungstools](https://github.com/Azure-Samples/networking-dotnet-log-converter) von GitHub verwenden.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Analysieren der Zugriffsprotokolle mit GoAccess

Wir haben eine Resource Manager-Vorlage veröffentlicht, die die beliebte [GoAccess](https://goaccess.io/)-Protokollanalyse für Application Gateway-Zugriffsprotokolle installiert und ausführt. GoAccess stellt wertvolle HTTP-Datenverkehrsstatistiken bereit, z.B. eindeutige Besucher, angeforderte Dateien, Hosts, Betriebssysteme, Browser, HTTP-Statuscodes und vieles mehr. Weitere Informationen finden Sie in der [Infodatei im Resource Manager-Vorlagenordner auf GitHub](https://aka.ms/appgwgoaccessreadme).

## <a name="next-steps"></a>Nächste Schritte

* Visualisieren von Indikator- und Ereignisprotokollen mit [Azure Monitor-Protokollen](../../azure-monitor/insights/azure-networking-analytics.md)
* Blogbeitrag [Visualize your Azure activity log with Power BI](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) (Visualisieren von Azure-Aktivitätsprotokollen mit Power BI)
* Blogbeitrag [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Anzeigen und Analysieren von Azure-Aktivitätsprotokollen in Power BI und mehr)

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png