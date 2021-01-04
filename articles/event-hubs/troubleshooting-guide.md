---
title: 'Beheben von Konnektivitätsproblemen: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel bietet Informationen zum Beheben von Konnektivitätsproblemen mit Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 8eddc0e8c598e4553b30759d179fecb6ae880829
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012679"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Behandeln von Konnektivitätsproblemen: Azure Event Hubs
Es gibt verschiedene Gründe dafür, dass Clientanwendungen keine Verbindung mit einem Event Hub herstellen können. Die auftretenden Konnektivitätsprobleme können dauerhaft oder vorübergehend sein. Wenn das Problem immer (dauerhaft) auftritt, können Sie die Verbindungszeichenfolge, die Firewalleinstellungen Ihrer Organisation, IP-Firewalleinstellungen, Netzwerksicherheitseinstellungen (Dienstendpunkte, private Endpunkte usw.) überprüfen. Bei zeitweiligen Problemen kann ein Upgrade auf die neueste Version des SDK, das Ausführen von Befehlen zum Überprüfen von gelöschten Paketen und das Abrufen von Netzwerkablaufverfolgungen bei der Behebung von Problemen hilfreich sein. 

Dieser Artikel bietet Tipps zum Beheben von Konnektivitätsproblemen mit Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Behandeln dauerhafter Konnektivitätsprobleme
Wenn die Anwendung überhaupt keine Verbindung mit Event Hub herstellen kann, führen Sie die in diesem Abschnitt aufgeführten Schritte aus, um das Problem zu beheben. 

### <a name="check-if-there-is-a-service-outage"></a>Überprüfen, ob ein Dienstausfall vorliegt
Überprüfen Sie auf der [Azure-Dienststatuswebsite](https://azure.microsoft.com/status/), ob ein Azure Event Hubs-Dienstausfall vorliegt.

### <a name="verify-the-connection-string"></a>Überprüfen der Verbindungszeichenfolge 
Vergewissern Sie sich, dass die verwendete Verbindungszeichenfolge richtig ist. Weitere Informationen zum Abrufen der Verbindungszeichenfolge mit dem Azure-Portal, der CLI oder PowerShell finden Sie unter [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md). 

Vergewissern Sie sich bei Kafka-Clients, dass die Dateien „producer.config“ oder „consumer.config“ ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten mit Kafka in Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Sicherstellen, dass das AzureEventGrid-Diensttag in Ihren Netzwerksicherheitsgruppen zulässig ist
Wenn Ihre Anwendung in einem Subnetz ausgeführt wird und eine zugeordnete Netzwerksicherheitsgruppe vorhanden ist, stellen Sie sicher, dass ausgehender Internetdatenverkehr oder das AzureEventGrid-Diensttag zulässig ist. Lesen Sie [Diensttags des virtuellen Netzwerks](../virtual-network/service-tags-overview.md), und suchen Sie nach `EventHub`.

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Überprüfen, ob die Anwendung in einem bestimmten Subnetz eines VNET ausgeführt werden muss
Vergewissern Sie sich, dass Ihre Anwendung in einem Subnetz des virtuellen Netzwerks ausgeführt wird, das Zugriff auf den Namespace besitzt. Wenn dies nicht der Fall ist, führen Sie die Anwendung in dem Subnetz aus, das Zugriff auf den Namespace besitzt, oder fügen Sie die IP-Adresse des Computers, auf dem die Anwendung ausgeführt wird, der [IP-Firewall](event-hubs-ip-filtering.md) hinzu. 

Wenn Sie einen Dienstendpunkt für ein virtuelles Netzwerk für einen Event Hub-Namespace erstellen, akzeptiert der Namespace nur Datenverkehr aus dem Subnetz, das an den Dienstendpunkt gebunden ist. Es gibt bei diesem Verhalten eine Ausnahme. Sie können bestimmte IP-Adressen in der IP-Firewall hinzufügen, um den Zugriff auf den öffentlichen Endpunkt des Event Hub zu ermöglichen. Weitere Informationen finden Sie unter [Netzwerkdienstendpunkte](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Überprüfen der IP-Firewalleinstellungen für Ihren Namespace
Überprüfen Sie, ob die öffentliche IP-Adresse des Computers, auf dem die Anwendung ausgeführt wird, durch die IP-Firewall blockiert wird.  

Standardmäßig kann über das Internet auf Event Hubs-Namespaces zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Die IP-Firewallregeln werden auf der Event Hubs-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch von einer IP-Adresse, die nicht mit einer zulässigen IP-Regel im Event Hubs-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt. IP-Filterregeln werden der Reihe nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

Weitere Informationen finden Sie unter [Konfigurieren von IP-Firewallregeln für ein Azure Event Hubs-Namespace](event-hubs-ip-filtering.md). Informationen zur Überprüfung, ob Probleme mit der IP-Filterung, dem virtuellen Netzwerk oder der Zertifikatkette vorliegen, finden Sie unter [Beheben von netzwerkbezogenen Problemen](#troubleshoot-network-related-issues).

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Überprüfen, ob auf den Namespace nur mit einem privaten Endpunkt zugegriffen werden kann
Wenn der Event Hubs-Namespace so konfiguriert ist, dass darauf nur über einen privaten Endpunkt zugegriffen werden kann, vergewissern Sie sich, dass die Clientanwendung über den privaten Endpunkt auf den Namespace zugreift. 

Mit dem [Azure Private Link-Dienst](../private-link/private-link-overview.md) können Sie über einen **privaten Endpunkt** in Ihrem virtuellen Netzwerk auf Azure Event Hubs zugreifen. Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem virtuellen Netzwerk und bindet den Dienst dadurch in Ihr virtuelles Netzwerk ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte](private-link-service.md). Weitere Informationen zum Bestätigen, dass ein privater Endpunkt verwendet wird, finden Sie unter **Überprüfen, ob die private Endpunktverbindung funktioniert**. 

### <a name="troubleshoot-network-related-issues"></a>Beheben von netzwerkbezogenen Problemen
Führen Sie die folgenden Schritte aus, um netzwerkbezogene Probleme mit Event Hubs zu beheben: 

Navigieren Sie zu `https://<yournamespacename>.servicebus.windows.net/`, oder verwenden Sie [wget](https://www.gnu.org/software/wget/). Dies hilft bei der Überprüfung, ob Probleme mit der IP-Filterung oder dem virtuellen Netzwerk bzw. der Zertifikatkette vorliegen (häufiges Problem bei Verwendung des Java SDK).

Beispiel für eine **erfolgreiche Meldung**:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Beispiel für eine **Fehlermeldung**:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Beheben zeitweiliger Konnektivitätsprobleme
Wenn vorübergehend Konnektivitätsprobleme auftreten, finden Sie in den folgenden Abschnitten Tipps zur Problembehandlung. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Verwenden der neuesten Version des SDK
Einige der vorübergehenden Konnektivitätsprobleme wurden möglicherweise in neueren Versionen des SDK als der von Ihnen verwendeten behoben. Stellen Sie sicher, dass Sie die neueste Version der Client-SDKs in Ihren Anwendungen verwenden. SDKs werden durch neue/aktualisierte Features und Fehlerbehebungen kontinuierlich verbessert. Testen Sie daher immer mit dem neuesten Paket. Überprüfen Sie die Versionshinweise auf behobene Probleme und hinzugefügte/aktualisierte Features. 

Weitere Informationen zu Client-SDKs finden Sie im Artikel [Azure Event Hubs – Client-SDKs](sdks.md). 

### <a name="run-the-command-to-check-dropped-packets"></a>Ausführen der Befehle zum Überprüfen gelöschter Pakete
Führen Sie bei zeitweiligen Konnektivitätsproblemen den folgenden Befehl aus, um zu überprüfen, ob gelöschte Pakete vorhanden sind. Mit diesem Befehl wird versucht, jede Sekunde 25 verschiedene TCP-Verbindungen mit dem Dienst herzustellen. Anschließend können Sie überprüfen, wie viele davon erfolgreich/fehlerhaft waren, und außerdem die Latenz der TCP-Verbindung anzeigen. Sie können das `psping`-Tool [hier](/sysinternals/downloads/psping) herunterladen.

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Sie können äquivalente Befehle verwenden, wenn Sie andere Tools wie `tnc`, `ping` usw. nutzen. 

Rufen Sie eine Netzwerkablaufverfolgung ab, wenn die vorherigen Schritte nicht hilfreich sind, und analysieren Sie diese mit Tools wie [Wireshark](https://www.wireshark.org/). Wenden Sie sich bei Bedarf an den [Microsoft-Support](https://support.microsoft.com/). 

### <a name="service-upgradesrestarts"></a>Dienstupgrades/-neustarts
Vorübergehende Konnektivitätsprobleme können aufgrund von Upgrades und Neustarts des Back-End-Diensts auftreten. Wenn solche Probleme vorliegen, treten möglicherweise die folgenden Symptome auf: 

- Es gehen möglicherweise weniger Nachrichten/Anforderungen ein.
- Die Protokolldatei enthält unter Umständen Fehlermeldungen.
- Die Verbindung zwischen Anwendungen und dem Dienst wird möglicherweise für einige Sekunden getrennt.
- Anforderungen werden ggf. vorübergehend gedrosselt.

Wenn im Anwendungscode das SDK verwendet wird, ist die Wiederholungsrichtlinie bereits integriert und aktiv. Die Verbindung wird ohne nennenswerte Auswirkungen auf die Anwendung bzw. den Workflow wiederhergestellt. Das Abfangen dieser vorübergehenden Fehler, das Zurücksetzen und anschließende Wiederholen des Aufrufs stellen sicher, dass der Code bei diesen vorübergehenden Probleme stabil ist.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

* [Behandeln von Authentifizierungs- und Autorisierungsproblemen](troubleshoot-authentication-authorization.md)
