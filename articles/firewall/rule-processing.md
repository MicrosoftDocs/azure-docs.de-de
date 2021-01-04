---
title: Logik für die Azure Firewall-Regelverarbeitung
description: Azure Firewall verfügt über NAT-Regeln, Netzwerkregeln und Anwendungsregeln. Die Regeln werden gemäß dem Regeltyp verarbeitet.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/18/2020
ms.author: victorh
ms.openlocfilehash: 01f7aa61d3bfb3c712320bbf138160a7ff8197c7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95502195"
---
# <a name="configure-azure-firewall-rules"></a>Konfigurieren von Azure Firewall-Regeln
Sie können NAT-Regeln, Netzwerkregeln und Anwendungsregeln in Azure Firewall konfigurieren. Regelsammlungen werden entsprechend dem Regeltyp in Prioritätsreihenfolge verarbeitet – niedrigere Zahlen bis höhere Zahlen von 100 bis 65.000. Der Name einer Regelsammlung darf nur Buchstaben, Ziffern, Unterstriche, Punkte oder Bindestriche enthalten. Er muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben, einer Zahl oder einem Unterstrich enden. Die maximale Namenslänge ist 80 Zeichen.

Es empfiehlt sich, die Prioritätsnummern der Regelsammlung zunächst in Inkrementen von 100 (100, 200, 300 usw.) aufzuteilen, damit Sie bei Bedarf Platz zum Hinzufügen weiterer Regelsammlungen haben.

> [!NOTE]
> Wenn Sie das Threat Intelligence-gestützte Filtern aktivieren, weisen diese Regeln die höchste Priorität auf und werden stets als Erstes verarbeitet. Threat Intelligence-gestütztes Filtern kann den Datenverkehr ablehnen, bevor konfigurierte Regeln verarbeitet werden. Weitere Informationen finden Sie unter [Threat Intelligence-gestütztes Filtern für Azure Firewall](threat-intel.md).

## <a name="outbound-connectivity"></a>Ausgehende Konnektivität

### <a name="network-rules-and-applications-rules"></a>Netzwerkregeln und Anwendungsregeln

Wenn Sie Netzwerkregeln und Anwendungsregeln konfigurieren, werden die Netzwerkregeln in der Prioritätsreihenfolge vor den Anwendungsregeln angewendet. Die Regeln können zur Beendigung von Vorgängen führen. Wenn also eine Netzwerkregel gefunden wird, werden keine anderen Regeln mehr verarbeitet.  Wenn sich keine Übereinstimmung für eine Netzwerkregel ergibt und als Protokoll HTTP, HTTPS oder MSSQL verwendet wird, wird das Paket von den Anwendungsregeln in der Reihenfolge ihrer Priorität ausgewertet. Falls sich immer noch keine Übereinstimmung ergibt, wird das Paket von der [Regelsammlung der Infrastruktur](infrastructure-fqdns.md) ausgewertet. Wenn sich auch hierbei keine Übereinstimmung ergibt, wird das Paket standardmäßig abgelehnt.

#### <a name="network-rule-protocol"></a>Netzwerkregelprotokoll

Netzwerkregeln können für **TCP**, **UDP**, **ICMP** oder ein beliebiges IP-Protokoll (**Any**) konfiguriert werden. Dies bezieht sich auf alle im Dokument [Internet Assigned Numbers Authority (IANA) Protocol Numbers](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) (Internet Assigned Numbers Authority-Protokollnummern (IANA)) definierten IP-Protokolle. Wenn ein Zielport explizit konfiguriert ist, wird die Regel in eine TCP- und UDP-Regel übersetzt.

Vor dem 9. November 2020 bezog sich ein beliebiges IP-Protokoll (**Any**) auf **TCP**, **UDP** oder **ICMP**. Daher haben Sie möglicherweise vor diesem Datum eine Regel mit „Protocol = Any“ und „destination ports = '*'“ konfiguriert. Wenn kein beliebiges IP-Protokoll wie aktuell definiert zugelassen werden soll, ändern Sie die Regel so, dass die gewünschten Protokolle (TCP, UDP oder ICMP) explizit konfiguriert werden.

## <a name="inbound-connectivity"></a>Eingehende Konnektivität

### <a name="nat-rules"></a>NAT-Regeln

Eingehende Internetkonnektivität kann aktiviert werden, indem DNAT (Destination Network Address Translation) konfiguriert wird. Die Vorgehensweise wird unter [Tutorial: Filtern von eingehendem Datenverkehr per Azure Firewall-DNAT im Azure-Portal](tutorial-firewall-dnat.md) beschrieben. NAT-Regeln werden in der Priorität vor Netzwerkregeln angewendet. Wenn sich eine Übereinstimmung ergibt, wird eine implizite entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Sie können dieses Verhalten außer Kraft setzen, indem Sie explizit eine Netzwerkregelsammlung mit Ablehnungsregeln hinzufügen, die für den übersetzten Datenverkehr geeignet sind.

Anwendungsregeln werden nicht für eingehende Verbindungen angewendet. Wenn Sie also eingehenden HTTP/S-Datenverkehr filtern möchten, sollten Sie Web Application Firewall (WAF) verwenden. Weitere Informationen finden Sie unter [Was ist die Azure Web Application Firewall?](../web-application-firewall/overview.md).

## <a name="examples"></a>Beispiele

Die folgenden Beispiele verdeutlichen die Ergebnisse einiger dieser Regelkombinationen.

### <a name="example-1"></a>Beispiel 1

Die Verbindung mit „google.com“ ist aufgrund einer übereinstimmenden Netzwerkregel zulässig.

**Netzwerkregel**

- Aktion: Allow


|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|IP-Adresse|*|IP-Adresse|*|80, 443

**Anwendungsregel**

- Aktion: Verweigern

|name  |Quellentyp  |`Source`  |Protokoll:Port|Ziel-FQDNs|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |IP-Adresse|*|http:80,https:443|google.com

**Ergebnis**

Die Verbindung mit „google.com“ ist zulässig, da das Paket mit der *Allow-web*-Netzwerkregel übereinstimmt. An dieser Stelle wird die Regelverarbeitung angehalten.

### <a name="example-2"></a>Beispiel 2

Der SSH-Datenverkehr wird abgelehnt, da er durch eine *Deny*-Netzwerkregelsammlung mit höherer Priorität blockiert wird.

**Netzwerkregelsammlung 1**

- Name: Allow-collection
- Priorität: 200
- Aktion: Allow

|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|IP-Adresse|*|IP-Adresse|*|22

**Netzwerkregelsammlung 2**

- Name: Deny-collection
- Priorität: 100
- Aktion: Verweigern

|name  |Protocol  |Quellentyp  |`Source`  |Zieltyp  |Zieladresse  |Zielports|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|IP-Adresse|*|IP-Adresse|*|22

**Ergebnis**

SSH-Verbindungen werden abgelehnt, da sie durch eine Netzwerkregelsammlung mit höherer Priorität blockiert werden. An dieser Stelle wird die Regelverarbeitung angehalten.

## <a name="rule-changes"></a>Regeländerungen

Wenn Sie eine Regel ändern, um zuvor zugelassenen Datenverkehr abzulehnen, werden alle relevanten vorhandenen Sitzungen verworfen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie eine [Azure Firewall bereitstellen und konfigurieren](tutorial-firewall-deploy-portal.md).