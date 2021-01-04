---
title: 'Beheben von Problemen mit der Netzwerkkonnektivität: Azure Event Grid | Microsoft-Dokumentation'
description: Dieser Artikel bietet Informationen zum Beheben von Netzwerkkonnektivitätsproblemen mit Azure Event Grid.
author: batrived
ms.topic: article
ms.date: 06/21/2020
ms.author: batrived
ms.openlocfilehash: fa119784715b8c88ef3c9f2700b2cac1cc467234
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339744"
---
# <a name="troubleshoot-connectivity-issues---azure-event-grid"></a>Behandeln von Konnektivitätsproblemen: Azure Event Grid

Es gibt verschiedene Gründe dafür, dass Clientanwendungen keine Verbindung mit einem Event Grid-Thema bzw. einer -Domäne herstellen können. Die auftretenden Konnektivitätsprobleme können dauerhaft oder vorübergehend sein. Wenn das Problem immer (dauerhaft) auftritt, können Sie die Firewalleinstellungen Ihrer Organisation, IP-Firewalleinstellungen, Diensttags, private Endpunkte usw. überprüfen. Bei zeitweiligen Problemen kann das Ausführen von Befehlen zum Überprüfen von gelöschten Paketen und das Abrufen von Netzwerkablaufverfolgungen bei der Behebung von Problemen hilfreich sein.

Dieser Artikel bietet Tipps zum Beheben von Konnektivitätsproblemen mit Azure Event Grid.

## <a name="troubleshoot-permanent-connectivity-issues"></a>Behandeln dauerhafter Konnektivitätsprobleme

Wenn die Anwendung überhaupt keine Verbindung mit Event Grid herstellen kann, führen Sie die in diesem Abschnitt aufgeführten Schritte aus, um das Problem zu beheben.

### <a name="check-if-theres-a-service-outage"></a>Überprüfen, ob ein Dienstausfall vorliegt

Überprüfen Sie auf der [Azure-Dienststatuswebsite](https://azure.microsoft.com/status/), ob ein Azure Event Grid-Dienstausfall vorliegt.

### <a name="check-if-the-ports-required-to-communicate-with-event-grid-arent-blocked-by-organizations-firewall"></a>Überprüfen, ob die für die Kommunikation mit Event Grid erforderlichen Ports durch die Firewall der Organisation blockiert werden

Überprüfen Sie, ob die für die Kommunikation mit Azure Event Grid verwendeten Ports in der Firewall Ihrer Organisation blockiert sind. In der folgenden Tabelle finden Sie die ausgehenden Ports, die Sie öffnen müssen, um mit Azure Event Grid kommunizieren zu können.

| Protokoll | Ports |
| -------- | ----- |
| HTTPS    | 443   |

Dies ist ein Beispielbefehl, mit dem überprüft wird, ob Port 443 blockiert ist.

```powershell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Unter Linux:

```shell
telnet {sampletopicname}.{region}-{suffix}.eventgrid.azure.net 443
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Überprüfen, ob die IP-Adressen in der Unternehmensfirewall zulässig sind

Wenn Sie mit Azure arbeiten, müssen Sie manchmal bestimmten IP-Adressbereichen oder URLs in Ihrer Unternehmensfirewall oder Ihrem Proxy erlauben, auf alle Azure-Dienste zuzugreifen, die Sie verwenden oder zu verwenden versuchen. Überprüfen Sie, ob der Datenverkehr für die von Event Grid verwendeten IP-Adressen zulässig ist. Weitere Informationen zu den von Azure Event Grid verwendeten IP-Adressen finden Sie unter: [Azure-IP-Bereiche und Diensttags – öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519) und [Diensttag – AzureEventGrid](network-security.md#service-tags).

Im Dokument [Azure-IP-Bereiche und Diensttags – öffentliche Cloud](https://www.microsoft.com/download/details.aspx?id=56519) werden IP-Adressen auch **nach Region** aufgeführt. Sie können Adressbereiche für die **Region des Themas** und die **Regionspaare** in Ihrer Unternehmensfirewall oder Ihrem Proxy zulassen. Das Regionspaar für eine Region finden Sie unter [Geschäftskontinuität und Notfallwiederherstellung (BCDR): Azure-Regionspaare](../best-practices-availability-paired-regions.md). 

> [!NOTE]
> Neue IP-Adressen können dem AzureEventGrid-diensttag hinzugefügt werden, obwohl dies nicht üblich ist. Es ist also ratsam, eine wöchentliche Prüfung der Diensttags durchzuführen.

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Sicherstellen, dass das AzureEventGrid-Diensttag in Ihren Netzwerksicherheitsgruppen zulässig ist

Wenn Ihre Anwendung in einem Subnetz ausgeführt wird und eine zugeordnete Netzwerksicherheitsgruppe vorhanden ist, überprüfen Sie, ob ausgehender Internetdatenverkehr oder das AzureEventGrid-Diensttag zulässig ist. Weitere Informationen finden Sie unter [Diensttags](../virtual-network/service-tags-overview.md).

### <a name="check-the-ip-firewall-settings-for-your-topicdomain"></a>Überprüfen der IP-Firewalleinstellungen für Ihr Thema/Ihre Domäne

Überprüfen Sie, ob die öffentliche IP-Adresse des Computers, auf dem die Anwendung ausgeführt wird, nicht durch die EventGrid-Thema- bzw. -Domänen-IP-Firewall blockiert wird.

Standardmäßig kann über das Internet auf Event Grid-Themen bzw. -Domänen zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Die IP-Firewallregeln werden auf der Event Grid-Thema- bzw. -Domänenebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch von einer IP-Adresse, die nicht mit einer zulässigen IP-Regel im Event Grid-Thema bzw. in der Domäne übereinstimmt, wird als unzulässig abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt.

Weitere Informationen finden Sie unter [Konfigurieren von IP-Firewallregeln für ein Azure Event Grid-Thema bzw. eine -Domäne](configure-firewall.md).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Ermitteln der durch die IP-Firewall blockierten IP-Adressen

Aktivieren Sie Diagnoseprotokolle für das Event Grid-Thema bzw. die -Domäne [Diagnoseprotokolle aktivieren](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-custom-topic). Es wird die IP-Adresse der Verbindung angezeigt, die verweigert wird.

```json
{
  "time": "2019-11-01T00:17:13.4389048Z",
  "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME",
  "category": "PublishFailures",
  "operationName": "Post",
  "message": "inputEventsCount=null, requestUri=https://SAMPLE-TOPIC-NAME.region-suffix.eventgrid.azure.net/api/events, publisherInfo=PublisherInfo(category=User, inputSchema=EventGridEvent, armResourceId=/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME), httpStatusCode=Forbidden, errorType=ClientIPRejected, errorMessage=Publishing to SAMPLE-TOPIC-NAME.{region}-{suffix}.EVENTGRID.AZURE.NET by client {clientIp} is rejected due to IpAddress filtering rules."
}
```

### <a name="check-if-the-eventgrid-topicdomain-can-be-accessed-using-only-a-private-endpoint"></a>Überprüfen, ob auf das EventGrid-Thema bzw. die -Domäne nur mit einem privaten Endpunkt zugegriffen werden kann

Wenn das Event Grid-Thema bzw. die -Domäne so konfiguriert ist, dass darauf nur über einen privaten Endpunkt zugegriffen werden kann, vergewissern Sie sich, dass die Clientanwendung über den privaten Endpunkt auf das Thema bzw. die Domäne zugreift. Um dies zu bestätigen, überprüfen Sie, ob die Clientanwendung in einem Subnetz ausgeführt wird und ob ein privater Endpunkt für das Event Grid-Thema bzw. die -Domäne in diesem Subnetz vorhanden ist.

Mit dem [Azure Private Link-Dienst](../private-link/private-link-overview.md) können Sie über einen **privaten Endpunkt** in Ihrem virtuellen Netzwerk auf Azure Event Grid zugreifen. Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte](configure-private-endpoints.md).

## <a name="troubleshoot-transient-connectivity-issues"></a>Beheben zeitweiliger Konnektivitätsprobleme

Wenn vorübergehend Konnektivitätsprobleme auftreten, finden Sie in den folgenden Abschnitten Tipps zur Problembehandlung.

### <a name="run-the-command-to-check-dropped-packets"></a>Ausführen der Befehle zum Überprüfen gelöschter Pakete

Führen Sie bei zeitweiligen Konnektivitätsproblemen den folgenden Befehl aus, um zu überprüfen, ob gelöschte Pakete vorhanden sind. Mit diesem Befehl wird versucht, jede Sekunde 25 verschiedene TCP-Verbindungen mit dem Dienst herzustellen. Anschließend können Sie überprüfen, wie viele davon erfolgreich/fehlerhaft waren, und außerdem die Latenz der TCP-Verbindung anzeigen. Sie können das `psping`-Tool [hier](/sysinternals/downloads/psping) herunterladen.

```shell
.\psping.exe -n 25 -i 1 -q {sampletopicname}.{region}-{suffix}.eventgrid.azure.net:443 -nobanner
```

Sie können äquivalente Befehle verwenden, wenn Sie andere Tools wie `tcpping` [tcpping.exe](https://www.elifulkerson.com/projects/tcping.php) nutzen.

Rufen Sie eine Netzwerkablaufverfolgung ab, wenn die vorherigen Schritte nicht hilfreich sind, und analysieren Sie diese mit Tools wie [Wireshark](https://www.wireshark.org/). Wenden Sie sich bei Bedarf an den [Microsoft-Support](https://support.microsoft.com/).

### <a name="service-upgradesrestarts"></a>Dienstupgrades/-neustarts

Vorübergehende Konnektivitätsprobleme können aufgrund von Upgrades und Neustarts des Back-End-Diensts auftreten. Wenn solche Probleme vorliegen, treten möglicherweise die folgenden Symptome auf:

- Es gehen möglicherweise weniger Nachrichten/Anforderungen ein.
- Die Protokolldatei enthält unter Umständen Fehlermeldungen.
- Die Verbindung zwischen Anwendungen und dem Dienst wird möglicherweise für einige Sekunden getrennt.
- Anforderungen werden ggf. vorübergehend gedrosselt.

Das Abfangen dieser vorübergehenden Fehler, das Zurücksetzen und anschließende Wiederholen des Aufrufs stellen sicher, dass der Code bei diesen vorübergehenden Probleme stabil ist.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Hilfe benötigen, veröffentlichen Sie Ihr Problem im [Stack Overflow-Forum](https://stackoverflow.com/questions/tagged/azure-eventgrid), oder öffnen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).