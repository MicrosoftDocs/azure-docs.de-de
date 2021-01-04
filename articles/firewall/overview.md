---
title: Was ist Azure Firewall?
description: Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 12/03/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 3bfc8704919fc26db692701eaca526dd5c333b6f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033492"
---
# <a name="what-is-azure-firewall"></a>Was ist Azure Firewall?

![ICSA-Zertifizierung](media/overview/icsa-cert-firewall-small.png)

Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Es ist eine vollständig zustandsbehaftete Firewall-as-a-Service mit integrierter Hochverfügbarkeit und uneingeschränkter Cloudskalierbarkeit.

![Firewallübersicht](media/overview/firewall-threat.png)

Sie können Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren. Azure Firewall verwendet eine statische öffentliche IP-Adresse für Ihre virtuellen Netzwerkressourcen, die es außenstehenden Firewalls ermöglicht, Datenverkehr aus Ihrem virtuellen Netzwerk zu identifizieren.  Der Dienst ist für Protokollierung und Analyse vollständig in Azure Monitor integriert.

## <a name="features"></a>Features

Informationen zu Features von Azure Firewall finden Sie unter [Azure Firewall-Features](features.md).

## <a name="pricing-and-sla"></a>Preise und SLA

Preisinformationen zu Azure Firewall finden Sie unter [Azure Firewall – Preise](https://azure.microsoft.com/pricing/details/azure-firewall/).

SLA-Informationen zu Azure Firewall finden Sie unter [Azure Firewall – SLA](https://azure.microsoft.com/support/legal/sla/azure-firewall/).

## <a name="whats-new"></a>Neues

Informationen zu den Neuerungen in Azure Firewall finden Sie unter [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall).


## <a name="known-issues"></a>Bekannte Probleme

Azure Firewall weist die folgenden bekannten Probleme auf:

|Problem  |BESCHREIBUNG  |Minderung  |
|---------|---------|---------|
Netzwerkfilterregeln für andere Protokolle als TCP/UDP (z.B. ICMP) funktionieren nicht für den Internetdatenverkehr|Netzwerkfilterregeln für andere Protokolle als TCP/UDP funktionieren nicht mit SNAT für Ihre öffentliche IP-Adresse. Nicht-TCP/UDP-Protokolle werden zwischen Spoke-Subnetzen und VNets unterstützt.|Azure Firewall verwendet Standard Load Balancer, [das SNAT für IP-Protokolle derzeit nicht unterstützt](../load-balancer/load-balancer-overview.md). Wir prüfen Möglichkeiten, um dieses Szenario in einer zukünftigen Version zu unterstützen.|
|Fehlende PowerShell- und CLI-Unterstützung für ICMP|Azure PowerShell und CLI weisen keine Unterstützung von ICMP als gültiges Protokoll in Netzwerkregeln auf.|Es ist weiterhin möglich, ICMP über das Portal und die REST-API als Protokoll zu verwenden. Wir arbeiten daran, ICMP in Kürze in PowerShell und CLI hinzuzufügen.|
|Für FQDN-Tags muss ein Protokoll/Port festgelegt werden|Für Anwendungsregeln mit FQDN-Tags ist eine „Port: Protokoll“-Definition erforderlich.|Sie können **https** als port:-Protokollwert verwenden. Wir arbeiten daran, dieses Feld optional zu machen, wenn FQDN-Tags verwendet werden.|
|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt|Das Verlagern einer Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement wird nicht unterstützt.|Die Unterstützung dieser Funktionalität ist Teil unserer Roadmap. Um eine Firewall in eine andere Ressourcengruppe oder ein anderes Abonnement zu verschieben, müssen Sie die aktuelle Instanz löschen und in der neuen Ressourcengruppe bzw. im Abonnement neu erstellen.|
|Threat Intelligence-Warnungen sind möglicherweise maskiert.|Netzwerkregeln mit Ziel 80/443 für ausgehende Filterung maskieren Threat Intelligence-Warnungen, wenn diese für den Modus „Alert only“ (Nur Warnen) konfiguriert sind.|Erstellen Sie die ausgehende Filterung für 80/443 mithilfe von Anwendungsregeln. Oder ändern Sie den Threat Intelligence-Modus zu **Alert and Deny** (Warnen und Verweigern).|
|Azure Firewall-DNAT funktioniert für private IP-Ziele nicht.|Die Azure Firewall-DNAT-Unterstützung ist auf eingehenden/ausgehenden Internetdatenverkehr beschränkt. DNAT funktioniert derzeit nicht für private IP-Ziele. Beispiel: Spoke zu Spoke.|Dies ist eine aktuelle Beschränkung.|
|Erste Konfiguration der öffentlichen IP-Adresse kann nicht entfernt werden|Jede öffentliche Azure Firewall-IP-Adresse ist einer *IP-Konfiguration* zugewiesen.  Die erste IP-Konfiguration wird während der Bereitstellung der Firewall zugewiesen und enthält in der Regel auch einen Verweis auf das Firewallsubnetz (sofern dies nicht über eine Vorlagenbereitstellung explizit anders konfiguriert wurde). Sie können diese IP-Konfiguration nicht löschen, weil damit die Zuordnung der Firewall aufgehoben würde. Sie können die öffentliche IP-Adresse, die dieser IP-Konfiguration zugeordnet ist, weiterhin ändern oder entfernen, wenn der Firewall mindestens eine andere öffentliche IP-Adresse zur Verwendung zur Verfügung steht.|Dies ist beabsichtigt.|
|Verfügbarkeitszonen können nur während der Bereitstellung konfiguriert werden.|Verfügbarkeitszonen können nur während der Bereitstellung konfiguriert werden. Sie können keine Verfügbarkeitszonen konfigurieren, nachdem eine Firewall bereitgestellt wurde.|Dies ist beabsichtigt.|
|SNAT für eingehende Verbindungen|Zusätzlich zu DNAT werden Verbindungen über die öffentliche IP-Adresse der Firewall (eingehend) per SNAT in eine der privaten öffentlichen IP-Adressen übersetzt. Diese Anforderung gilt derzeit (auch für Aktiv/Aktiv-NVAs), um symmetrisches Routing sicherzustellen.|Um die ursprüngliche Quelle für HTTP/S beizubehalten, können Sie [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For)-Header verwenden. Verwenden Sie beispielsweise einen Dienst wie [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) oder [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) vor der Firewall. Sie können auch WAF als Teil von Azure Front Door verwenden und mit der Firewall verketten.
|SQL-FQDN-Filterung wird nur im Proxymodus unterstützt (Port 1433)|Für Azure SQL-Datenbank, Azure Synapse Analytics und Azure SQL Managed Instance:<br><br>Die SQL-FQDN-Filterung wird nur im Proxymodus unterstützt (Port 1433).<br><br>Für Azure SQL-IaaS gilt:<br><br>Wenn Sie keine Standardports verwenden, können Sie diese Ports in den Anwendungsregeln angeben.|Für SQL im Umleitungsmodus (die Standardeinstellung beim Herstellen von Verbindungen innerhalb von Azure) können Sie den Zugriff stattdessen mit dem SQL-Diensttag in den Azure Firewall-Netzwerkregeln filtern.
|Ausgehender Datenverkehr an TCP-Port 25 ist nicht zulässig.| Ausgehende SMTP-Verbindungen, die den TCP-Port 25 verwenden, werden blockiert. Der Port 25 wird in erster Linie für die E-Mail-Zustellung ohne Authentifizierung verwendet. Dies ist das standardmäßige Plattformverhalten für virtuelle Computer. Weitere Informationen finden Sie unter [Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Anders als bei virtuellen Computern kann diese Funktion für Azure Firewall derzeit nicht aktiviert werden. Hinweis: Wenn Sie authentifiziertes SMTP (Port 587) oder SMTP über einen anderen Port als 25 zulassen möchten, konfigurieren Sie unbedingt eine Netzwerkregel und nicht eine Anwendungsregel, da die SMTP-Überprüfung zu diesem Zeitpunkt nicht unterstützt wird.|Verwenden Sie die empfohlene Methode für den E-Mail-Versand, die im Artikel zur SMTP-Problembehandlung dokumentiert ist. Sie können auch den virtuellen Computer, der ausgehenden SMTP-Zugriff benötigt, von Ihrer Standardroute zur Firewall ausschließen. Konfigurieren Sie stattdessen direkten ausgehenden Zugriff auf das Internet.
|Aktives FTP wird nicht unterstützt.|Zum Schutz vor FTP-Bounce-Angriffen ist aktives FTP in Azure Firewall deaktiviert.|Sie können stattdessen passives FTP verwenden. Die TCP-Ports 20 und 21 müssen weiterhin explizit in der Firewall geöffnet werden.
|Metrik für SNAT-Portnutzung zeigt 0 % an|Für die Metrik zur SNAT-Portnutzung der Azure Firewall wird ggf. auch dann 0 % angezeigt, wenn SNAT-Ports verwendet werden. In diesem Fall führt die Verwendung der Metrik als Teil der Metriken für die Firewallintegrität zu einem falschen Ergebnis.|Dieses Problem wurde behoben, und der Rollout für die Produktion ist für Mai 2020 geplant. In einigen Fällen wird das Problem durch die erneute Bereitstellung der Firewall behoben, aber dies ist nicht garantiert. Verwenden Sie den Integritätszustand der Firewall als zwischenzeitliche Problemumgehung nur, um nach *status=degraded* zu suchen (nicht nach *status=unhealthy*). Die Portauslastung wird als *degraded* (heruntergestuft) angezeigt. Der Status *not healthy* (fehlerhaft) ist für zukünftige Zwecke reserviert, wenn mehr Metriken zur Firewallintegrität verfügbar sind.
|DNAT wird bei aktivierter Tunnelerzwingung nicht unterstützt.|Mit aktivierter Tunnelerzwingung bereitgestellte Firewalls können aufgrund des asymmetrischen Routings keinen eingehenden Zugriff über das Internet unterstützen.|Dies ist systembedingt und auf das asymmetrische Routing zurückzuführen. Der Rückgabepfad für eingehende Verbindungen verläuft über die lokale Firewall, der noch nicht bekannt ist, dass die Verbindung hergestellt wurde.
|Abhängig von Ihrer FTP-Serverkonfiguration kann ausgehendes passives FTP für Firewalls mit mehreren öffentlichen IP-Adressen unter Umständen nicht verwendet werden.|Durch passives FTP werden unterschiedliche Verbindungen für Steuerungs- und Datenkanäle hergestellt. Wenn eine Firewall mit mehreren öffentlichen IP-Adressen ausgehende Daten sendet, wird nach dem Zufallsprinzip eine der öffentlichen IP-Adressen als Quell-IP-Adresse ausgewählt. Abhängig von Ihrer FTP-Serverkonfiguration funktioniert FTP möglicherweise nicht, wenn für Daten- und Steuerungskanäle unterschiedliche Quell-IP-Adressen verwendet werden.|Eine explizite SNAT-Konfiguration ist geplant. In der Zwischenzeit können Sie den FTP-Server so konfigurieren, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert (siehe ein [Beispiel für IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). Erwägen Sie in einem solchen Fall alternativ die Verwendung einer einzelnen IP-Adresse.|
|Abhängig von Ihrer FTP-Serverkonfiguration kann eingehendes passives FTP möglicherweise nicht verwendet werden. |Durch passives FTP werden unterschiedliche Verbindungen für Steuerungs- und Datenkanäle hergestellt. Eingehende Verbindungen an Azure Firewall werden per SNAT in eine der privaten Firewall-IP-Adressen übersetzt, um symmetrisches Routing sicherzustellen. Abhängig von Ihrer FTP-Serverkonfiguration funktioniert FTP möglicherweise nicht, wenn für Daten- und Steuerungskanäle unterschiedliche Quell-IP-Adressen verwendet werden.|Die Beibehaltung der ursprünglichen Quell-IP-Adresse wird zurzeit untersucht. In der Zwischenzeit können Sie den FTP-Server so konfigurieren, dass er Daten- und Steuerungskanäle von verschiedenen Quell-IP-Adressen akzeptiert.|
|Für die Metrik „NetworkRuleHit“ fehlt eine Protokolldimension.|Die Metrik „ApplicationRuleHit“ ermöglicht protokollbasiertes Filtern, diese Funktion fehlt jedoch in der entsprechenden Metrik vom Typ „NetworkRuleHit“.|Es wird bereits nach einer Lösung gesucht.|
|NAT-Regeln mit Ports zwischen 64000 und 65535 werden nicht unterstützt.|Azure Firewall lässt beliebige Ports im Bereich 1-65535 in Netzwerk- und Anwendungsregeln zu. NAT-Regeln unterstützen jedoch nur Ports im Bereich 1-63999.|Dies ist eine aktuelle Beschränkung.
|Konfigurationsaktualisierungen können durchschnittlich fünf Minuten dauern.|Eine Aktualisierung der Azure Firewall-Konfiguration kann durchschnittlich zwischen drei und fünf Minuten dauern, und parallele Aktualisierungen werden nicht unterstützt.|Es wird bereits nach einer Lösung gesucht.|
|Azure Firewall verwendet SNI-TLS-Header zum Filtern von HTTPS- und MSSQL-Datenverkehr.|Wenn die Browser- oder Serversoftware die Erweiterung Server Name Indication (SNI) nicht unterstützt, können Sie keine Verbindung über Azure Firewall herstellen.|Wenn die Browser- oder Serversoftware SNI nicht unterstützt, können Sie möglicherweise die Verbindung mithilfe einer Netzwerkregel anstelle einer Anwendungsregel steuern. Eine Liste der Software, die SNI unterstützt, finden Sie unter [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication).|
|Benutzerdefiniertes DNS kann nicht mit Tunnelerzwingung verwendet werden.|Ist Tunnelerzwingung aktiviert, funktioniert benutzerdefiniertes DNS nicht.|Es wird bereits nach einer Lösung gesucht.|
|Unterstützung für neue öffentliche IP-Adressen für mehrere Verfügbarkeitszonen|Sie können keine neue IP-Adresse hinzufügen, wenn Sie eine Firewall mit zwei Verfügbarkeitszonen bereitstellen (entweder 1 und 2, 2 und 3 oder 1 und 3).|Dies ist eine Einschränkung bei öffentlichen IP-Adressressourcen.|
|Starten/Beenden funktioniert nicht bei einer Firewall, für die der Tunnelerzwingungsmodus konfiguriert ist.|Starten/Beenden funktioniert nicht bei einer Azure Firewall-Instanz, für die der Tunnelerzwingungsmodus konfiguriert ist. Der Versuch, Azure Firewall mit konfigurierter Tunnelerzwingung zu starten, führt zu folgendem Fehler:<br><br>*Set-AzFirewall: Es ist nicht möglich, die Verwaltungs-IP-Konfiguration für AzureFirewall "FW-xx" einer vorhandenen Firewall hinzuzufügen. Wiederholen Sie die Bereitstellung mit einer Verwaltungs-IP-Konfiguration, wenn Sie eine Tunnelerzwingung unterstützen möchten.<br>Statuscode: 400<br>ReasonPhrase: Ungültige Anforderung*|Wird untersucht<br><br>Um das Problem zu umgehen, können Sie die vorhandene Firewall löschen und eine neue mit denselben Parametern erstellen.|
|Tags der Firewallrichtlinie können nicht über das Portal hinzugefügt werden.|Die Azure Firewall-Richtlinie verfügt über eine Patchunterstützungseinschränkung, die verhindert, dass Sie über das Azure-Portal ein Tag hinzufügen können. Der folgende Fehler wird generiert: *Die Tags für die Ressource konnten nicht gespeichert werden*.|Es wird bereits nach einer Lösung gesucht. Alternativ können Sie das Azure PowerShell-Cmdlet `Set-AzFirewallPolicy` verwenden, um Tags zu aktualisieren.|
|Noch keine IPv6-Unterstützung|Wenn Sie einer Regel eine IPv6-Adresse hinzufügen, tritt ein Firewallfehler auf.|Verwenden Sie nur IPv4-Adressen. Die IPv6-Unterstützung wird geprüft.|


## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md)
- [Bereitstellen von Azure Firewall mit einer Vorlage](deploy-template.md)
- [Erstellen einer Azure Firewall-Testumgebung](scripts/sample-create-firewall-test.md)