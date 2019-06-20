---
title: Azure Application Gateway – Konfigurationsübersicht
description: In diesem Artikel wird das Konfigurieren der Komponenten von Azure Application Gateway beschrieben.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 55c7670821ee6c6f5b924bf18b5f7ad01d4b6d51
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431303"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway – Konfigurationsübersicht

Azure Application Gateway besteht aus mehreren Komponenten, die Sie auf verschiedene Weise für verschiedene Szenarien konfigurieren können. In diesem Artikel wird das Konfigurieren der einzelnen Komponenten veranschaulicht.

![Flussdiagramm der Application Gateway-Komponenten](./media/configuration-overview/configuration-overview1.png)

Dieses Bild zeigt eine Anwendung mit drei Listenern. Die ersten zwei sind Listener für mehrere Standorte für `http://acme.com/*` bzw. `http://fabrikam.com/*`. Beide lauschen auf Port 80. Der dritte ist ein grundlegender Listener mit End-to-End-Secure Sockets Layer-Beendigung (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuelles Azure-Netzwerk und dediziertes Subnetz

Ein Application Gateway ist eine dedizierte Bereitstellung in Ihrem virtuellen Netzwerk. Innerhalb Ihres virtuellen Netzwerks ist ein dediziertes Subnetz für das Application Gateway erforderlich. Sie können in einem Subnetz mehrere Instanzen einer bestimmten Application Gateway-Bereitstellung betreiben. Außerdem können Sie weitere Application Gateway-Instanzen im Subnetz bereitstellen. Aber Sie können keine andere Ressource im Application Gateway-Subnetz bereitstellen.

> [!NOTE]
> Sie können ein Standard_v2- und ein standardmäßiges Azure Application Gateway nicht parallel in demselben Subnetz betreiben.

#### <a name="size-of-the-subnet"></a>Subnetzgröße

Application Gateway nutzt 1 private IP-Adresse pro Instanz sowie eine weitere private IP-Adresse, wenn eine private Front-End-IP konfiguriert ist.

Azure reserviert auch 5 IP-Adressen in jedem Subnetz für die interne Verwendung: die ersten 4 und die letzte IP-Adresse. Stellen Sie sich beispielsweise 15 Application Gateway-Instanzen ohne private Front-End-IP vor. Sie benötigen mindestens 20 IP-Adressen für dieses Subnetz: 5 für die interne Verwendung und 15 für die Application Gateway-Instanzen. Sie benötigen also mindestens ein /27-Subnetz.

Stellen Sie sich ein Subnetz mit 27 Application Gateway-Instanzen und einer privaten Front-End-IP vor. In diesem Fall benötigen Sie 33 IP-Adressen: 27 für Application Gateway-Instanzen, 1 für das private Front-End und 5 für die interne Verwendung. Sie benötigen also mindestens ein /26-Subnetz.

Sie sollten mindestens ein /28-Subnetz verwenden. Diese Größe bietet Ihnen 11 nutzbare IP-Adressen. Wenn für Ihre Anwendungslast mehr als 10 IP-Adressen erforderlich sind, sollten Sie eine Subnetzgröße von /27 oder /26 in Betracht ziehen.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Netzwerksicherheitsgruppen im Application Gateway-Subnetz

Netzwerksicherheitsgruppen (NSG) werden im Application Gateway-Subnetz unterstützt. Aber es gibt einige Einschränkungen:

- Sie müssen Ausnahmen für eingehenden Datenverkehr an den Ports 65503-65534 für die Application Gateway v1-SKU und an den Ports 65200-65535 für die v2-SKU einbeziehen. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von Azure-Zertifikaten geschützt (gesperrt). Externe Entitäten einschließlich der Kunden dieser Gateways können ohne entsprechende Zertifikate keine Änderungen an diesen Endpunkten vornehmen.

- Die ausgehende Internetverbindung kann nicht blockiert sein. Standardausgangsregeln in der NSG ermöglichen Internetkonnektivität. Wir empfehlen Folgendes:

  - Entfernen Sie die Standardausgangsregeln nicht.
  - Erstellen Sie keine anderen Ausgangsregeln, die ausgehende Internetkonnektivität verweigern.

- Datenverkehr vom **AzureLoadBalancer**-Tag muss zulässig sein.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Zulassen des Application Gateway-Zugriffs auf einige wenige Quell-IPs

Verwenden Sie für dieses Szenario Netzwerksicherheitsgruppen im Application Gateway-Subnetz. Legen Sie die folgenden Einschränkungen für das Subnetz in dieser Priorität fest:

1. Lassen Sie eingehenden Datenverkehr von einem Quell-IP-/IP-Adressbereich zu.
2. Lassen Sie eingehende Anforderungen aus allen Quellen an den Ports 65503-65534 für die [Back-End-Integrität-Kommunikation](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) zu. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von Azure-Zertifikaten geschützt (gesperrt). Ohne entsprechende Zertifikate können externe Entitäten keine Änderungen an diesen Endpunkten vornehmen.
3. Lassen Sie eingehende Azure Load Balancer-Tests (*AzureLoadBalancer-Tag*) und eingehenden virtuellen Netzwerkdatenverkehr (*VirtualNetwork*-Tag) für die [Netzwerksicherheitsgruppe](https://docs.microsoft.com/azure/virtual-network/security-overview) zu.
4. Blockieren Sie den gesamten übrigen eingehenden Datenverkehr mit einer Alle-verweigern-Regel.
5. Zulassen von ausgehendem Datenverkehr an das Internet für alle Ziele.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Im Application Gateway-Subnetz unterstützte benutzerdefinierte Routen

Für die v1 SKU werden benutzerdefinierte Routen (User-Defined Routes, UDRs) im Application Gateway-Subnetz unterstützt, solange sie die End-to-End-Anforderung/Antwort-Kommunikation nicht ändern. Beispielsweise können Sie eine benutzerdefinierte Route im Application Gateway-Subnetz einrichten, um auf eine Firewallappliance für die Paketüberprüfung zu verweisen. Sie müssen jedoch sicherstellen, dass das Paket nach der Überprüfung das vorgesehene Ziel erreichen kann. Ein Unterlassen kann zu einem falschen Integritätstest oder Datenverkehrsrouting-Verhalten führen. Dies schließt gelernte Routen oder standardmäßige 0.0.0.0/0-Routen ein, die durch Azure ExpressRoute oder VPN-Gateways im virtuellen Netzwerk verteilt werden.

UDRs im Application Gateway-Subnetz werden vom v2 SKU nicht unterstützt. Weitere Informationen finden Sie unter [Azure Application Gateway v2-SKU](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> Das Verwenden benutzerdefinierter Routen im Application Gateway-Subnetz führt dazu, dass der Integritätsstatus in der [Ansicht der Back-End-Integrität](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) als „Unbekannt“ angezeigt wird. Außerdem treten dabei bei der Generierung von Application Gateway-Protokollen und Metriken Fehler auf. Sie sollten keine benutzerdefinierten Routen im Application Gateway-Subnetz verwenden, damit Sie die Back-End-Integrität, Protokolle und Metriken anzeigen können.

## <a name="front-end-ip"></a>Front-End-IP

Sie können das Application Gateway mit einer öffentlichen IP-Adresse, mit einer privaten IP-Adresse oder mit beidem konfigurieren. Eine öffentliche IP-Adresse ist erforderlich, wenn Sie ein Back-End hosten, auf das Clients über eine für den Internetzugriff verfügbare virtuelle IP (VIP) über das Internet zugreifen müssen. 

Eine öffentliche IP-Adresse ist nicht für einen internen Endpunkt erforderlich, der nicht für den Internetzugriff verfügbar ist. Dieser wird als *Interner Lastenausgleich*-Endpunkt (Internal Load-Balancer, ILB) bezeichnet. Ein ILB-Application Gateway ist für interne Branchenanwendungen nützlich, die nicht für das Internet verfügbar gemacht werden. Es ist auch hilfreich für Dienste und Ebenen in einer Anwendung mit mehreren Ebenen innerhalb einer Sicherheitsgrenze, die nicht für das Internet verfügbar gemacht werden, aber eine Roundrobin-Lastverteilung, Sitzungs-Stickiness oder SSL-Beendigung erfordern.

Es wird nur 1 öffentliche oder 1 private IP-Adresse unterstützt. Sie wählen die Front-End-IP beim Erstellen des Application Gateways aus.

- Für eine öffentliche IP-Adresse können Sie eine neue öffentliche IP-Adresse erstellen oder eine vorhandene öffentliche IP-Adresse am gleichen Speicherort wie das Application Gateway verwenden. Wenn Sie eine neue öffentliche IP-Adresse erstellen, kann der von Ihnen gewählte Typ der IP-Adresse (statisch oder dynamisch) später nicht mehr geändert werden. Weitere Informationen finden Sie unter [Statische und dynamische öffentliche IP-Adresse im Vergleich](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Für eine private IP-Adresse können Sie eine private IP-Adresse aus dem Subnetz angeben, in dem das Application Gateway erstellt wird. Wenn Sie keine angeben, wird automatisch eine beliebige IP-Adresse aus dem Subnetz ausgewählt. Weitere Informationen finden Sie unter [Erstellen eines Application Gateways mit einem internen Lastenausgleich (ILB)](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Eine Front-End-IP-Adresse ist einem *Listener* zugeordnet, der auf über die Front-End-IP eingehende Anforderungen prüft.

## <a name="listeners"></a>Listener

Ein Listener ist eine logische Entität, die mithilfe von Port, Protokoll, Host und IP-Adresse auf eingehende Verbindungsanforderungen prüft. Wenn Sie den Listener konfigurieren, müssen Sie Werte für diese eingeben, die den entsprechenden Werten in der eingehenden Anforderung auf dem Gateway entsprechen.

Wenn Sie ein Application Gateway mithilfe des Azure-Portals erstellen, erstellen Sie außerdem einen Standardlistener durch Auswählen von Protokoll und Port für den Listener. Sie können wahlweise HTTP2-Unterstützung auf dem Listener aktivieren. Nach der Erstellung des Application Gateways können Sie die Einstellungen dieses Standardlisteners (*appGatewayHttpListener*/*appGatewayHttpsListener*) bearbeiten oder neue Listener erstellen.

### <a name="listener-type"></a>Listenertyp

Wenn Sie einen neuen Listener erstellen, wählen Sie zwischen [*grundlegend* und *mehreren Standorten*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) aus.

- Wenn Sie hinter einem Application Gateway eine einzelne Website hosten, wählen Sie „grundlegend“. Erfahren Sie, [wie Sie ein Application Gateway mit einem grundlegenden Listener erstellen](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Wenn Sie mehr als eine Webanwendung oder mehrere Unterdomänen der gleichen übergeordneten Domäne auf der gleichen Application Gateway-Instanz konfigurieren, wählen Sie den Listener für mehrere Standorte. Für einen Listener mit mehreren Standorten müssen Sie auch einen Hostnamen eingeben. Das hat den Grund, dass Application Gateway HTTP 1.1-Hostheader verwendet, um mehrere Websites an der gleichen öffentlichen IP-Adresse und dem gleichen Port zu hosten.

#### <a name="order-of-processing-listeners"></a>Verarbeitungsreihenfolge von Listenern

Für die v1 SKU werden Listener gemäß der Auflistungsreihenfolge verarbeitet. Wenn eine eingehende Anforderung zu einem grundlegenden Listener passt, verarbeitet der Listener diese zuerst. Konfigurieren Sie also Listener für mehrere Standorte vor grundlegenden Listenern, um zu gewährleisten, dass der Datenverkehr an das richtige Back-End weitergeleitet wird.

Für v2 SKUs werden Listener für mehrere Standorte vor grundlegenden Listenern verarbeitet.

### <a name="front-end-ip"></a>Front-End-IP

Wählen Sie die Front-End-IP-Adresse aus, die Sie diesem Listener zuordnen möchten. Der Listener lauscht auf eingehende Anforderungen für diese IP-Adresse.

### <a name="front-end-port"></a>Front-End-Port

Wählen Sie den Front-End-Port aus. Wählen Sie einen vorhandenen Port aus, oder erstellen Sie einen neuen. Wählen Sie einen beliebigen Wert aus dem [zulässigen Portbereich](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports) aus. Sie können nicht nur die bekannten Ports wie 80 und 443 verwenden, sondern jeden geeigneten zulässigen benutzerdefinierten Port. Ein Port kann für öffentlich zugängliche Listener oder für nur privat zugängliche Listener verwendet werden.

### <a name="protocol"></a>Protocol

Wählen Sie HTTP oder HTTPS aus:

- Wenn Sie sich für HTTP entscheiden, ist der Datenverkehr zwischen dem Client und dem Application Gateway unverschlüsselt.

- Wählen Sie HTTPS aus, wenn Sie [SSL-Beendigung](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) oder [End-to-End-SSL-Verschlüsselung](https://docs.microsoft.com/azure/application-gateway/ssl-overview) wünschen. Der Datenverkehr zwischen dem Client und dem Application Gateway ist verschlüsselt. Außerdem wird die SSL-Verbindung am Application Gateway getrennt. Wenn Sie die End-to-End-SSL-Verschlüsselung wünschen, müssen Sie HTTPS auswählen und die Einstellung des **Back-End-HTTP** konfigurieren. Dadurch wird sichergestellt, dass der Datenverkehr auf dem Weg vom Application Gateway zum Back-End erneut verschlüsselt wird.

Zum Konfigurieren von SSL-Beendigung und End-to-End-SSL-Verschlüsselung müssen Sie dem Listener ein Zertifikat hinzufügen, damit das Application Gateway einen symmetrischen Schlüssel ableiten kann. Dies wird durch die SSL-Protokollspezifikation vorgegeben. Der symmetrische Schlüssel wird zum Verschlüsseln und Entschlüsseln des an das Gateway gesendeten Datenverkehrs verwendet. Das Gatewayzertifikat muss im PFX-Format (Personal Information Exchange, privater Informationsaustausch) vorliegen. Mit diesem Format können Sie den privaten Schlüssel exportieren, den das Gateway zum Verschlüsseln und Entschlüsseln von Datenverkehr verwendet.

#### <a name="supported-certificates"></a>Unterstützte Zertifikate

Informationen finden Sie unter [Unterstützte Zertifikate für die SSL-Terminierung](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Unterstützung zusätzlicher Protokolle

#### <a name="http2-support"></a>HTTP2-Unterstützung

Die Unterstützung des HTTP/2-Protokolls ist nur für Clients verfügbar, die mit Application Gateway-Listenern verbunden sind. Die Kommunikation mit Back-End-Serverpools erfolgt über HTTP/1.1. Die HTTP/2-Unterstützung ist standardmäßig deaktiviert. Der folgende Azure PowerShell-Codeausschnitt zeigt, wie Sie sie aktivieren:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket-Unterstützung

Die WebSocket-Unterstützung ist standardmäßig aktiviert. Sie kann von Benutzern nicht aktiviert oder deaktiviert werden. Sie können das WebSocket-Protokoll sowohl mit HTTP- als auch mit HTTPS-Listenern verwenden.

### <a name="custom-error-pages"></a>Benutzerdefinierte Fehlerseiten

Sie können benutzerdefinierte Fehler auf globaler Ebene oder Listenerebene definieren. Doch das Erstellen von benutzerdefinierten Fehlerseiten auf globaler Ebene über das Azure-Portal wird derzeit nicht unterstützt. Sie können auf Listenerebene eine benutzerdefinierte Fehlerseite für einen 403-WAF-Fehler oder eine 502-Wartungsseite konfigurieren. Außerdem müssen Sie eine öffentlich zugängliche Blob-URL für den bestimmten Fehlerstatuscode angeben. Weitere Informationen finden Sie unter [Create Application Gateway custom error pages](https://docs.microsoft.com/azure/application-gateway/custom-error) (Erstellen von benutzerdefinierten Application Gateway-Fehlerseiten).

![Application Gateway-Fehlercodes](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Wie Sie eine globale benutzerdefinierte Fehlerseite konfigurieren, erfahren Sie unter [Azure PowerShell-Konfiguration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>SSL-Richtlinie

Sie können die SSL-Zertifikatverwaltung zentralisieren sowie den Ver- und Entschlüsselungsaufwand für eine Back-End-Serverfarm verringern. Diese zentralisierte SSL-Behandlung ermöglicht auch die Angabe einer zentralen SSL-Richtlinie, die auf Ihre Sicherheitsanforderungen abgestimmt ist. Sie können zwischen einer *standardmäßigen*, *vordefinierten* oder *benutzerdefinierten* SSL-Richtlinie wählen.

Sie konfigurieren die SSL-Richtlinie, um die SSL-Protokollversionen zu steuern. Sie können ein Application Gateway konfigurieren, um TLS1.0, TLS1.1 und TLS1.2 abzuweisen. Standardmäßig sind SSL 2.0 und 3.0 deaktiviert und nicht konfigurierbar. Weitere Informationen finden Sie unter [Application Gateway SSL policy overview](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview) (Application Gateway SSL-Richtlinie – Übersicht).

Nachdem Sie einen Listener erstellt haben, ordnen Sie ihm eine Anforderungsroutingregel zu. Diese Regel bestimmt, wie vom Listener empfangene Anforderungen an das Back-End weitergeleitet werden.

## <a name="request-routing-rules"></a>Anforderungsroutingregeln

Wenn Sie ein Application Gateway mithilfe des Azure-Portals erstellen, erstellen Sie eine Standardregel (*rule1*). Mit dieser Regel wird der Standardlistener (*appGatewayHttpListener*) an den Standard-Back-End-Pool (*appGatewayBackendPool*) und die Back-End-HTTP-Standardeinstellungen ( *appGatewayBackendHttpSettings*) gebunden. Nachdem Sie das Gateway erstellt haben, können Sie die Einstellungen der Standardregel bearbeiten oder neue Regeln erstellen.

### <a name="rule-type"></a>Regeltyp

Wenn Sie eine Regel erstellen, wählen Sie zwischen [*grundlegend* und *pfadbasiert*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) aus.

- Wählen Sie „grundlegend“ aus, wenn Sie alle an den zugeordneten Listener gerichteten Anforderungen (z.B. *blog<i></i>.contoso.com/\*)* an einen einzelnen Back-End-Pool weiterleiten möchten.
- Wählen Sie „pfadbasiert“ aus, wenn Sie Anforderungen von bestimmten URL-Pfaden an bestimmte Back-End-Pools weiterleiten möchten. Das Pfadmuster wird nur auf den Pfad der URL angewendet, nicht auf ihre Abfrageparameter.

#### <a name="order-of-processing-rules"></a>Verarbeitungsreihenfolge von Regeln

Für die v1 SKU wird die Musterzuordnung eingehender Anforderungen in der Reihenfolge verarbeitet, in der die Pfade in der URL-Pfadzuordnung der pfadbasierten Regel aufgelistet sind. Wenn eine Anforderung mit dem Muster in mindestens zwei Pfaden in der Pfadzuordnung übereinstimmt, wird der zuerst aufgelistete Pfad abgeglichen. Die Anforderung wird an das Back-End weitergeleitet, das diesem Pfad zugeordnet ist.

Für die v2 SKU hat eine genaue Übereinstimmung höhere Priorität als die Pfadreihenfolge in der URL-Pfadzuordnung. Wenn eine Anforderung mit dem Muster in mindestens zwei Pfaden übereinstimmt, wird sie an das Back-End weitergeleitet, das dem Pfad zugeordnet ist, der exakt mit der Anforderung übereinstimmt. Wenn der Pfad in der eingehenden Anforderung mit keinem Pfad in der Zuordnung genau übereinstimmt, wird der Musterabgleich der Anforderung in der Reihenfolge der Pfadzuordnungsliste für die pfadbasierte Regel verarbeitet.

### <a name="associated-listener"></a>Zugeordneter Listener

Ordnen Sie der Regel einen Listener zu, damit die *Anforderungsroutingregel*, die dem Listener zugeordnet ist, ausgewertet wird, um den Back-End-Pool zu bestimmen, an den die Anforderung weitergeleitet werden soll.

### <a name="associated-back-end-pool"></a>Zugeordneter Back-End-Pool

Ordnen Sie der Regel den Back-End-Pool zu, der die Back-End-Ziele enthält, die Anforderungen verarbeiten, die der Listener empfängt.

 - Für eine grundlegende Regel ist nur ein einziger Back-End-Pool zulässig. Alle Anforderungen auf dem zugeordneten Listener werden an diesen Back-End-Pool weitergeleitet.

 - Fügen Sie für eine pfadbasierte Regel mehrere Back-End-Pools hinzu, die den einzelnen URL-Pfaden entsprechen. Mit dem eingegebenen URL-Pfad übereinstimmende Anforderungen werden an den entsprechenden Back-End-Pool weitergeleitet. Fügen Sie darüber hinaus einen standardmäßigen Back-End-Pool hinzu. Anforderungen, die keinem URL-Pfad in der Regel entsprechen, werden an diesen Pool weitergeleitet.

### <a name="associated-back-end-http-setting"></a>Zugeordnete Back-End-HTTP-Einstellung

Fügen Sie für jede Regel eine Back-End-HTTP-Einstellung hinzu. Anforderungen werden vom Application Gateway mithilfe der Portnummer, des Protokolls und anderer in dieser Einstellung festgelegten Informationen an die Back-End-Ziele weitergeleitet.

Für eine grundlegende Regel ist nur eine einzige Back-End-HTTP-Einstellung zulässig. Alle Anforderungen auf dem zugeordneten Listener werden mit dieser HTTP-Einstellung an die entsprechenden Back-End-Ziele weitergeleitet.

Fügen Sie für jede Regel eine Back-End-HTTP-Einstellung hinzu. Anforderungen werden vom Application Gateway mithilfe der Portnummer, des Protokolls und anderer in dieser Einstellung festgelegten Informationen an die Back-End-Ziele weitergeleitet.

Für eine grundlegende Regel ist nur eine einzige Back-End-HTTP-Einstellung zulässig. Alle Anforderungen auf dem zugeordneten Listener werden mit dieser HTTP-Einstellung an die entsprechenden Back-End-Ziele weitergeleitet.

Fügen Sie für eine pfadbasierte Regel mehrere Back-End-HTTP-Einstellungen hinzu, die den einzelnen URL-Pfaden entsprechen. Anforderungen, die mit dem URL-Pfad in dieser Einstellung übereinstimmen, werden mithilfe der den einzelnen URL-Pfaden entsprechenden HTTP-Einstellungen an die entsprechenden Back-End-Ziele weitergeleitet. Fügen Sie auch eine Standard-HTTP-Einstellung hinzu. Anforderungen, die keinem URL-Pfad in dieser Regel entsprechen, werden mit der Standard-HTTP-Einstellung an den Standard-Back-End-Pool weitergeleitet.

### <a name="redirection-setting"></a>Umleitungseinstellung

Wenn die Umleitung für eine grundlegende Regel konfiguriert ist, werden alle Anforderungen auf dem zugeordneten Listener an das Ziel umgeleitet. Dies ist eine *globale Umleitung*. Wenn die Umleitung für eine pfadbasierte Regel konfiguriert ist, werden nur Anforderungen in einem bestimmten Websitebereich umgeleitet. Ein Beispiel ist ein Warenkorbbereich, der durch */cart/\** gekennzeichnet ist. Dies ist eine *pfadbasierte Umleitung*.

Weitere Informationen zu Umleitungen finden Sie unter [Übersicht über die Umleitung in Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Umleitungstyp

Wählen Sie den erforderlichen Umleitungstyp aus: *Dauerhaft(301)* , *Temporär(307)* , *Gefunden(302)* oder *Verweis(303)* .

#### <a name="redirection-target"></a>Umleitungsziel

Wählen Sie einen anderen Listener oder eine externe Website als Umleitungsziel aus.

##### <a name="listener"></a>Listener

Wählen Sie einen Listener als Umleitungsziel aus, um Datenverkehr von einem Listener zu einem anderen auf dem Gateway umzuleiten. Diese Einstellung ist erforderlich, wenn Sie HTTP-zu-HTTPS-Umleitung aktivieren möchten. Sie leitet Datenverkehr vom Quelllistener, der prüft, ob HTTP-Anforderungen eingehen, an den Ziellistener um, der prüft, ob HTTPS-Anforderungen eingehen. Sie können auch wählen, die Abfragezeichenfolge und den Pfad der ursprünglichen Anforderung in die Anforderung einzubeziehen, die an das Umleitungsziel weitergeleitet wird.

![Dialogfeld mit Application Gateway-Komponenten](./media/configuration-overview/configure-redirection.png)

Weitere Informationen zur HTTP-zu-HTTPS-Umleitung finden Sie unter:
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über das Azure-Portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS mithilfe von Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Erstellen eines Anwendungsgateways mit Umleitung von HTTP zu HTTPS über die Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Externe Website

Wählen Sie „Externe Website“ aus, wenn Sie den Datenverkehr am Listener, der dieser Regel zugeordnet ist, auf eine externe Website umleiten möchten. Sie können wählen, die Abfragezeichenfolge der ursprünglichen Anforderung in die Anforderung einzubeziehen, die an das Umleitungsziel weitergeleitet wird. Sie können den Pfad zu der externen Website in der ursprünglichen Anforderung nicht weiterleiten.

Weitere Informationen zur Umleitung finden Sie unter:
- [Erstellen eines Anwendungsgateways mit externer Umleitung durch Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Erstellen eines Anwendungsgateways mit externer Umleitung durch die Azure CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Umschreiben der HTTP-Headereinstellung

Diese Einstellung fügt HTTP-Anforderungs- und -Antwortheader hinzu, entfernt oder aktualisiert sie, während die Anforderungs-/Antwortpakete zwischen dem Client und den Back-End-Pools verschoben werden. Sie können diese Funktion nur mithilfe von PowerShell konfigurieren. Unterstützung durch Azure-Portal und CLI sind noch nicht verfügbar. Weitere Informationen finden Sie unter

 - [Erneutes Generieren von HTTP-Headern in Application Gateway (Public Preview)](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Angeben Ihrer Regelkonfiguration für das erneute Generieren eines HTTP-Headers](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>HTTP-Einstellungen

Das Application Gateway leitet Datenverkehr mithilfe der Konfiguration, die Sie hier festlegen, an die Back-End-Server weiter. Nachdem Sie eine HTTP-Einstellung erstellt haben, müssen Sie sie einer oder mehreren Regeln für das Routing von Anforderungen zuordnen.

### <a name="cookie-based-affinity"></a>Cookiebasierte Affinität

Dieses Feature ist nützlich, wenn eine Benutzersitzung auf dem gleichen Server bleiben soll. Durch das Gateway verwaltete Cookies ermöglichen dem Application Gateway, weiteren Datenverkehr einer Benutzersitzung zur Verarbeitung an den gleichen Server weiterzuleiten. Dies ist wichtig, wenn der Sitzungsstatus für eine Benutzersitzung lokal auf dem Server gespeichert wird. Wenn die Anwendung cookiebasierte Affinität nicht verarbeiten kann, können Sie diese Funktion nicht verwenden. Um sie zu verwenden, stellen Sie sicher, dass die Clients Cookies unterstützen.

### <a name="connection-draining"></a>Verbindungsausgleich

Mit dem Verbindungsausgleich können Sie Elemente des Back-End-Pools bei geplanten Dienstupdates korrekt entfernen. Sie können diese Einstellung bei der Erstellung einer Regel auf alle Elemente eines Back-End-Pools anwenden. Dadurch wird sichergestellt, dass alle Instanzen eines Back-End-Pools, deren Registrierung aufgehoben wird, keine neuen Anforderungen erhalten. Vorhandene Anforderungen können in der Zwischenzeit innerhalb eines konfigurierten Zeitlimits abgeschlossen werden. Verbindungsausgleich gilt für Back-End-Instanzen, die explizit durch einen API-Aufruf aus dem Back-End-Pool entfernt werden. Dies gilt auch für Back-End-Instanzen, die von den Integritätstests als *fehlerhaft* gemeldet werden.

### <a name="protocol"></a>Protocol

Application Gateway unterstützt sowohl HTTP als auch HTTPS für das Routing von Anforderungen an die Back-End-Server. Bei Auswahl von HTTP ist Datenverkehr an die Back-End-Server unverschlüsselt. Wenn unverschlüsselte Kommunikation nicht akzeptabel ist, wählen Sie HTTPS.

Diese Einstellung unterstützt zusammen mit HTTPS im Listener [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Damit können Sie vertrauliche Daten sicher verschlüsselt an das Back-End übertragen. Jeder Back-End-Server im Back-End-Pool mit aktiviertem End-to-End-SSL muss mit einem Zertifikat konfiguriert sein, um die sichere Kommunikation zu erlauben.

### <a name="port"></a>Port

Diese Einstellung gibt den Port an, auf dem die Back-End-Server auf Datenverkehr vom Application Gateway lauschen. Sie können Ports im Bereich von 1 bis 65535 konfigurieren.

### <a name="request-timeout"></a>Anforderungszeitlimit

Diese Einstellung ist die Anzahl der Sekunden, die das Application Gateway auf eine Antwort vom Back-End-Pool wartet, bevor es eine „Verbindungstimeout“-Fehlermeldung zurückgibt.

### <a name="override-back-end-path"></a>Back-End-Pfad außer Kraft setzen

Mit dieser Einstellung können Sie einen optionalen benutzerdefinierten Weiterleitungspfad konfigurieren, der für die Weiterleitung der Anforderung an das Back-End verwendet werden soll. Ein beliebiger Teil des Eingangspfads, der mit dem benutzerdefinierten Pfad im Feld **Back-End-Pfad außer Kraft setzen** übereinstimmt, wird in den weitergeleiteten Pfad kopiert. Die folgende Tabelle zeigt die Funktionsweise dieses Features:

- Wenn die HTTP-Einstellung einer einfachen Routingregel für Anforderungen zugeordnet ist:

  | Ursprüngliche Anforderung  | Back-End-Pfad außer Kraft setzen | An das Back-End weitergeleitete Anforderung |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Wenn die HTTP-Einstellung einer pfadbasierten Routingregel für Anforderungen zugeordnet ist:

  | Ursprüngliche Anforderung           | Pfadregel       | Back-End-Pfad außer Kraft setzen | An das Back-End weitergeleitete Anforderung |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>Für App Service verwenden

Dies ist eine Benutzeroberflächenverknüpfung, die die zwei erforderlichen Einstellungen für das Azure App Service-Back-End auswählt. Sie aktiviert **Hostnamen aus Back-End-Adresse auswählen** und erstellt einen neuen benutzerdefinierten Test. (Weitere Informationen finden Sie im Abschnitt [Hostnamen aus Back-End-Adresse auswählen](#pick) dieses Artikels.) Es wird ein neuer Test erstellt, dessen Header aus der Adresse des Back-End-Elements ausgewählt wird.

### <a name="use-custom-probe"></a>Benutzerdefinierten Test verwenden

Diese Einstellung ordnet einen [benutzerdefinierten Test](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) einer HTTP-Einstellung zu. Einer HTTP-Einstellung kann nur ein benutzerdefinierter Test zugeordnet werden. Wenn Sie nicht explizit einen benutzerdefinierten Test zuordnen, wird der [Standardtest](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) zur Überwachung der Integrität des Back-Ends verwendet. Sie sollten einen benutzerdefinierten Test erstellen, um größere Kontrolle über die Integritätsüberwachung Ihrer Back-Ends zu haben.

> [!NOTE]
> Der benutzerdefinierte Test überwacht die Integrität des Back-End-Pools nicht, sofern die entsprechende HTTP-Einstellung nicht explizit einem Listener zugeordnet ist.

### <a id="pick"/></a>Auswählen eines Hostnamens aus der Back-End-Adresse

Diese Funktion legt den *Host*-Header in der Anforderung dynamisch auf den Hostnamen des Back-End-Pools fest. Sie verwendet dazu eine IP-Adresse oder einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN ).

Dieses Feature ist nützlich, wenn der Domänenname des Back-Ends vom DNS-Namen des Application Gateways abweicht und sich das Back-End für die Auflösung des richtigen Endpunkts auf einen bestimmten Hostheader oder eine SNI-Erweiterung (Server Name Indication, Servernamensanzeige) stützt.

Ein Beispielfall sind mehrinstanzenfähige Dienste auf dem Back-End. Ein App Service ist ein mehrinstanzenfähiger Dienst, der einen gemeinsam genutzten Speicherplatz mit einer einzigen IP-Adresse verwendet. Daher kann der Zugriff auf einen App Service nur über die Hostnamen erfolgen, die in den Einstellungen der benutzerdefinierten Domäne konfiguriert werden.

Standardmäßig ist der benutzerdefinierte Domänenname *example.azurewebsites.<i></i>net*. Um mit einem Application Gateway über einen Hostnamen, der nicht explizit im App Service registriert ist, oder über den FQDN des Application Gateways auf Ihren App Service zuzugreifen, überschreiben Sie den Hostnamen in der ursprünglichen Anforderung mit dem App Service-Hostnamen. Aktivieren Sie zu diesem Zweck die Einstellung **Hostnamen aus Back-End-Adresse auswählen**.

Für eine benutzerdefinierte Domäne, deren bestehender benutzerdefinierter DNS-Name dem App Service zugeordnet ist, müssen Sie diese Einstellung nicht aktivieren.

> [!NOTE]
> Diese Einstellung ist für die App Service-Umgebung für PowerApps nicht erforderlich, da es sich dabei um eine dedizierte Bereitstellung handelt.

### <a name="host-name-override"></a>Hostnamen außer Kraft setzen

Diese Funktion ersetzt den *Host*-Header in der beim Application Gateway eingehenden Anforderung durch den Hostnamen, den Sie angeben.

Wenn z.B. *www.contoso<i></i>.com* in der Einstellung **Hostname** angegeben ist, wird die ursprüngliche Anforderung *https:/<i></i>/appgw.eastus.cloudapp.net/path1* in *https:/<i></i>/www.contoso.com/path1* geändert, wenn die Anforderung an den Back-End-Server weitergeleitet wird.

## <a name="back-end-pool"></a>Back-End-Pool

Sie können vier Typen von Back-End-Elementen festlegen, auf die ein Back-End-Pool verweisen kann: einen bestimmten virtuellen Computer, eine VM-Skalierungsgruppe, eine IP-Adresse/einen FQDN oder einen App Service. Jeder Back-End-Pool kann auf mehrere Elemente des gleichen Typs verweisen. Das Verweisen auf Elemente verschiedenen Typs im gleichen Back-End-Pool wird nicht unterstützt.

Nachdem Sie einen Back-End-Pool erstellt haben, müssen Sie ihn einer oder mehreren Regeln für das Routing von Anforderungen zuordnen. Ferner müssen Sie Integritätstests für jeden Back-End-Pool in Ihrem Application Gateway konfigurieren. Wenn eine Bedingung einer Anfoderungsroutingregel erfüllt ist, leitet das Application Gateway den Datenverkehr an die fehlerfreien Server (die durch die Integritätstests bestimmt werden) im entsprechenden Back-End-Pool weiter.

## <a name="health-probes"></a>Integritätstests

Ein Application Gateway überwacht standardmäßig die Integrität aller Ressourcen in seinem Back-End. Sie sollten aber unbedingt für jede Back-End-HTTP-Einstellung einen benutzerdefinierten Test erstellen, um größere Kontrolle über die Überwachung der Integrität zu erhalten. Informationen zum Konfigurieren eines benutzerdefinierten Tests finden Sie unter [Einstellungen für die benutzerdefinierte Integritätsüberprüfung](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Nachdem Sie einen benutzerdefinierten Integritätstest erstellt haben, müssen Sie ihn einer Back-End-HTTP-Einstellung zuordnen. Ein benutzerdefinierter Test überwacht die Integrität des Back-End-Pools nicht, sofern die entsprechende HTTP-Einstellung nicht explizit einem Listener zugeordnet ist.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun über Application Gateway-Komponenten informiert haben, können Sie folgende Aktionen ausführen:

- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)
- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway: Azure CLI](quick-create-cli.md)
