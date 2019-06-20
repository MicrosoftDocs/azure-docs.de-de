---
title: Ausgangsregeln in Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Verwenden Sie Ausgangsregeln, um ausgehende Netzwerkadressenübersetzungen zu definieren.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2018
ms.author: kumud
ms.openlocfilehash: 52fafa7e9dd46b6c78af3776797bae48b22ea8df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698433"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer-Ausgangsregeln

Azure Load Balancer unterstützt zusätzlich zu eingehenden Verbindungen auch ausgehende Verbindungen eines virtuellen Netzwerks.  Mit Ausgangsregeln können Sie die Netzwerkadressenübersetzung (NAT) einer öffentlichen [Load Balancer Standard](load-balancer-standard-overview.md)-Instanz für ausgehenden Datenverkehr ganz einfach konfigurieren.  Sie verfügen über die vollständige deklarative Steuerung über die ausgehenden Verbindungen, um diese Funktionalität nach Belieben zu skalieren und anzupassen.

![Load Balancer-Ausgangsregeln](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Mithilfe von Ausgangsregeln können Sie mit Load Balancer: 
- Die NAT für ausgehenden Datenverkehr von Grund auf neu definieren
- Das Verhalten vorhandener NAT für ausgehenden Datenverkehr skalieren und optimieren 

Mit Ausgangsregeln können Sie steuern:
- Welchen IP-Adressen von VMs in welche öffentlichen IP-Adressen übersetzt werden 
- Wie [ausgehende SNAT-Ports](load-balancer-outbound-connections.md#snat) zugeordnet werden
- Welche Protokolle zum Übersetzen ausgehenden Datenverkehrs verwendet werden
- Wie lange das Leerlauftimeout für ausgehende Verbindungen dauert (4-120 Minuten)
- Ob eine TCP-Zurücksetzung bei Leerlauftimeout gesendet wird (in der Public Preview) 

Ausgangsregeln erweitern das in Artikel [Ausgehende Verbindungen](load-balancer-outbound-connections.md) beschriebene [Szenario 2](load-balancer-outbound-connections.md#lb). Die Rangfolge der Szenarien bleibt unverändert.

## <a name="outbound-rule"></a>Ausgehende Regel

Wie alle Load Balancer-Regeln folgen auch die Ausgangsregeln der gleichen vertrauten Syntax wie Lastenausgleichsregeln und NAT-Eingangsregeln:

**Front-End** + **Parameter** + **Back-End-Pool**

Eine Ausgangsregel konfiguriert die NAT für ausgehenden Datenverkehr für _alle VMs, die vom Back-End-Pool_ für die _Front-End_-Übersetzung identifiziert wurden.  _Parameter_ ermöglichen eine zusätzliche differenzierte Steuerung des NAT-Algorithmus für ausgehenden Datenverkehr.

Die API-Version „2018-07-01“ lässt eine Ausgangsregeldefinition mit der folgenden Struktur zu:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Die effektive NAT-Konfiguration für ausgehenden Datenverkehr ist eine Kombination aller Ausgangs- und Lastenausgleichsregeln. Ausgangsregeln verhalten sich inkrementell zu Lastenausgleichsregeln. In Artikel [Deaktivieren von NAT für ausgehenden Datenverkehr für eine Lastenausgleichsregel](#disablesnat) finden Sie weitere nützliche Informationen, um die effektive NAT für ausgehenden Datenverkehr zu verwalten, wenn mehrere Regeln für eine VM gelten. Sie müssen [SNAT für ausgehenden Datenverkehr deaktivieren](#disablesnat), wenn Sie eine Ausgangsregel definieren, die die gleiche öffentliche IP-Adresse wie eine Lastenausgleichsregel verwendet.

### <a name="scale"></a> Skalieren der NAT für ausgehenden Datenverkehr mit mehreren IP-Adressen

Ausgangsregeln können jeweils mit nur einer einzigen öffentlichen IP-Adresse verwendet werden und erleichtern die Konfiguration beim Skalieren der NAT für ausgehenden Datenverkehr. Sie können mehrere IP-Adressen verwenden, um umfangreiche Szenarien zu planen. Mithilfe von Ausgangsregeln lassen sich außerdem die für die [SNAT-Überlastung](load-balancer-outbound-connections.md#snatexhaust) anfälligen Muster reduzieren.  

Jede zusätzliche IP-Adresse, die von einem Front-End bereitgestellt wird, stellt 51.200 kurzlebige Ports zur Verfügung, die Load Balancer als SNAT-Ports verwenden kann. Beim Lastenausgleich und bei NAT-Eingangsregeln steht ein einziges Front-End zur Verfügung. Die Ausgangsregel erweitert das Front-End-Konzept und lässt mehrere Front-Ends pro Regel zu.  Bei mehreren Front-Ends pro Regel wird die Anzahl von verfügbaren SNAT-Ports mit jeder öffentlichen IP-Adresse multipliziert. So können auch umfangreiche Szenarien unterstützt werden.

Darüber hinaus können Sie ein [Präfix für öffentliche IP-Adressen](https://aka.ms/lbpublicipprefix) direkt mit einer Ausgangsregel verwenden.  Durch die Verwendung von öffentlichen IP-Präfixen ist es einfacher, Datenflüsse aus Ihrer Azure-Bereitstellung zu skalieren und auf die Whitelist zu setzen. Sie können eine Front-End-IP-Konfiguration innerhalb der Load Balancer-Ressource konfigurieren, um direkt auf den Präfix einer öffentlichen IP-Adresse zu verweisen.  Dies ermöglicht Load Balancer die exklusive Steuerung über das Präfix für öffentliche IP-Adressen, und die Ausgangsregel verwendet automatisch alle öffentlichen IP-Adressen, die im Präfix für öffentliche IP-Adressen für ausgehende Verbindungen enthalten sind.  Jede IP-Adresse im Präfixbereich für öffentliche IP-Adressen bietet 51.200 kurzlebige Ports pro IP-Adresse, die Load Balancer als SNAT-Ports verwenden kann.   

Wenn Sie diese Option nutzen, können Sie keine einzelnen Ressourcen für öffentliche IP-Adressen aus dem Präfix für öffentliche IP-Adressen erstellen, da die Ausgangsregel die vollständige Kontrolle über das Präfix haben muss.  Wenn Sie eine differenziertere Steuerung wünschen, können Sie aus dem Präfix für öffentliche IP-Adressen eine individuelle öffentliche IP-Adressressource erstellen und mehrere öffentliche IP-Adressen einzeln dem Front-End einer Ausgangsregel zuweisen.

### <a name="snatports"></a> Anpassen der SNAT-Portzuordnung

Sie können Ausgangsregeln verwenden, um die [automatische SNAT-Portzuordnung basierend auf der Back-End-Poolgröße](load-balancer-outbound-connections.md#preallocatedports) anzupassen und mehr oder weniger Ressourcen zuzuweisen, als die automatische SNAT-Portzuordnung bereitstellt.

Verwenden Sie den folgenden Parameter, um 10.000 SNAT-Ports pro VM zuzuordnen (NIC-IP-Konfiguration).
 

          "allocatedOutboundPorts": 10000

Jede öffentliche IP-Adresse aller Front-Ends einer Ausgangsregel stellt bis zu 51.200 kurzlebige Ports als SNAT-Ports bereit.  Load Balancer weist SNAT-Ports als ein Vielfaches von 8 zu. Wenn Sie einen Wert angeben, der nicht durch 8 teilbar ist, wird der Konfigurationsvorgang abgelehnt.  Wenn Sie versuchen, mehr SNAT-Ports zuzuordnen als öffentliche IP-Adressen vorhanden sind, wird der Konfigurationsvorgang abgelehnt.  Wenn Sie beispielsweise 10.000 Ports pro VM zuweisen und 7 VMs in einem Back-End-Pool eine einzige öffentliche IP-Adresse teilen würden, wird die Konfiguration zurückgewiesen (7 x 10.000 SNAT-Ports > 51.200 SNAT-Ports).  Dem Front-End der Ausgangsregel lassen sich weitere öffentliche IP-Adressen hinzufügen, um das Szenario zu ermöglichen.

Sie können die [automatische SNAT-Portzuordnung basierend auf der Back-End-Poolgröße](load-balancer-outbound-connections.md#preallocatedports) wiederherstellen, indem Sie als Portanzahl „0“ angeben.

### <a name="idletimeout"></a> Steuern des Leerlauftimeouts für ausgehenden Datenfluss

Ausgangsregeln stellen einen Konfigurationsparameter bereit, um das Leerlauftimeout für den ausgehenden Datenfluss zu steuern und ihn an die Anforderungen Ihrer Anwendung anzupassen.  Das Leerlauftimeout für ausgehenden Datenverkehr tritt standardmäßig nach 4 Minuten ein.  Der Parameter akzeptiert einen Wert von 4 bis 120, um die Minutenanzahl für das Leerlauftimeout für Datenflüsse festzulegen, die dieser speziellen Regel entsprechen.

Verwenden Sie den folgenden Parameter, um dieses Leerlauftimeout auf 1 Stunde festzulegen:

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Aktivieren der TCP-Zurücksetzung bei Leerlauftimeout (Vorschau)

Das Standardverhalten von Load Balancer ist es, den Datenfluss allmählich zu entfernen, wenn das Leerlauftimeout für ausgehenden Datenverkehr erreicht wurde.  Mit dem Parameter „enableTCPReset“ können Sie ein besser vorhersagbares Anwendungsverhalten aktivieren und steuern, ob bidirektionale TCP-Zurücksetzung (TCP RST) außerhalb des Leerlauftimeouts für ausgehenden Datenverkehr gesendet wird. 

Verwenden Sie den folgenden Parameter, um die TCP-Zurücksetzung für eine Ausgangsregel zu aktivieren:

           "enableTcpReset": true

Unter [TCP-Zurücksetzung bei Leerlauftimeout (Vorschau)](https://aka.ms/lbtcpreset) finden Sie weitere Informationen, unter anderem zur regionalen Verfügbarkeit.

### <a name="proto"></a> Unterstützen von TCP- und UDP-Transportprotokollen mit einer einzigen Regel

Sie werden wahrscheinlich die Option „Alle“ für das Ausgangsregel-Transportprotokoll verwenden wollen, doch Sie können die Ausgangsregel auch auf ein bestimmtes Transportprotokoll anwenden.

Verwenden Sie den folgenden Parameter, um das Protokoll auf TCP und UDP festzulegen:

          "protocol": "All"

### <a name="disablesnat"></a> Deaktivieren der NAT für ausgehenden Datenverkehr für eine Lastenausgleichsregel

Wie bereits erwähnt, bieten Lastenausgleichsregeln eine automatische Programmierung der NAT für ausgehenden Datenverkehr. In einigen Szenarien müssen Sie jedoch die automatische Programmierung der NAT für ausgehenden Datenverkehr durch die Lastenausgleichsregel deaktivieren, um das Verhalten zu steuern oder zu verfeinern.  Ausgangsregeln umfassen Szenarien, in denen es wichtig ist, die automatische Programmierung der NAT für ausgehenden Datenverkehr zu stoppen.

Sie haben zwei Möglichkeiten, diesen Parameter zu verwenden:
- Sie können das Verwenden der eingehenden IP-Adresse für die NAT für ausgehenden Datenverkehr unterdrücken.  Ausgangsregeln verhalten sich inkrementell zu Lastenausgleichsregeln. Wenn dieser Parameter festgelegt ist, übernimmt die Ausgangsregel die Kontrolle.
  
- Passen Sie die NAT-Parameter für ausgehenden Datenverkehr einer IP-Adresse an, die zugleich für eingehenden und ausgehenden Datenverkehr verwendet wird.  Die automatische NAT-Programmierung für ausgehenden Datenverkehr muss deaktiviert werden, damit eine Ausgangsregel die Kontrolle übernehmen kann.  Um beispielsweise die SNAT-Portzuordnung einer Adresse zu ändern, die auch für den eingehenden Datenverkehr verwendet wird, muss dieser Parameter TRUE entsprechen.  Wenn Sie versuchen, mit einer Ausgangsregel die Parameter einer IP-Adresse neu zu definieren, die auch für eingehenden Datenverkehr verwendet wird, und die NAT-Programmierung für ausgehenden Datenverkehr für die Lastenausgleichsregel nicht freigegeben wurde, schlägt die Konfiguration einer Ausgangsregel fehl.

>[!IMPORTANT]
> Ihre VM hat keine ausgehenden Verbindungen, wenn Sie diesen Parameter auf TRUE setzen und keine Ausgangsregel (oder kein [Szenario mit einer öffentlichen IP-Adresse auf Instanzebene](load-balancer-outbound-connections.md#ilpip)) zum Definieren ausgehender Verbindungen haben.  Einige Vorgänge Ihrer VM oder Ihrer Anwendung können davon abhängen, ob ausgehende Verbindungen verfügbar sind. Stellen Sie sicher, dass Sie die Abhängigkeiten Ihres Szenarios verstehen und die Auswirkungen von Änderungen berücksichtigt haben.

Mit diesem Konfigurationsparameter können Sie SNAT für ausgehenden Datenverkehr für die Lastenausgleichsregel deaktivieren:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Der Parameter „disableOutboundSNAT“ **ist** standardmäßig auf FALSE eingestellt. Das bedeutet, dass die Lastenausgleichsregel automatisch NAT für ausgehenden Datenverkehr als Spiegelbild der Konfiguration der Lastenausgleichsregel bereitstellt.  

Wenn Sie „disableOutboundSnat“ in der Lastenausgleichsregel auf TRUE setzen, gibt die Lastenausgleichsregel die Kontrolle über die ansonsten automatische NAT-Programmierung für ausgehenden Datenverkehr frei.  SNAT für ausgehenden Datenverkehr als Ergebnis der Lastenausgleichsregel ist deaktiviert.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Wiederverwenden vorhandener oder Definieren neuer Back-End-Pools

Ausgangsregeln führen kein neues Konzept zum Definieren der VM-Gruppe ein, für die die Regel gelten soll.  Stattdessen wird das Konzept eines Back-End-Pools verwendet, das auch für Lastenausgleichsregeln gilt. So lässt sich die Konfiguration vereinfachen, indem eine vorhandene Back-End-Pooldefinition wiederverwendet oder eine neue speziell für eine Ausgangsregel erstellt wird.

## <a name="scenarios"></a>Szenarien

### <a name="groom"></a> Konfigurieren ausgehender Verbindungen für einen bestimmten Satz öffentlicher IP-Adressen

Sie können eine Ausgangsregel verwenden, um ausgehende Verbindungen so zu konfigurieren, dass sie scheinbar von einem bestimmten Satz öffentlicher IP-Adressen stammen, um Whitelistszenarien zu vereinfachen.  Diese öffentliche Quell-IP-Adresse kann die gleiche sein, die von einer Lastenausgleichsregel verwendet wird, oder ein anderer Satz öffentlicher IP-Adressen, als der von einer Lastenausgleichsregel verwendete.  

1. Erstellen Sie ein [Präfix für öffentliche IP-Adressen](https://aka.ms/lbpublicipprefix) (oder öffentliche IP-Adressen aus einem Präfix für öffentliche IP-Adressen).
2. Erstellen einer öffentlichen Load Balancer Standard-Instanz
3. Erstellen Sie Front-Ends, die auf das gewünschte Präfix für öffentliche IP-Adressen (oder öffentliche IP-Adressen) verweisen.
4. Verwenden Sie einen vorhandenen Back-End-Pool erneut, oder erstellen Sie einen neuen Back-End-Pool, und stellen Sie die VMs in einem Back-End-Pool der öffentlichen Load Balancer-Instanz bereit.
5. Konfigurieren Sie eine Ausgangsregel in der öffentlichen Load Balancer-Instanz, um NAT für ausgehenden Datenverkehr für diese VMs über die Front-Ends zu programmieren.
   
Wenn die Lastenausgleichsregel nicht für den ausgehenden Datenverkehr verwendet werden soll, deaktivieren Sie [SNAT für ausgehenden Datenverkehr](#disablesnat) für die Lastenausgleichsregel.

### <a name="modifysnat"></a> Ändern der SNAT-Portzuordnung

Sie können Ausgangsregeln verwenden, um die [automatische SNAT-Portzuweisung basierend auf der Back-End-Poolgröße](load-balancer-outbound-connections.md#preallocatedports) anzupassen.

Wenn Sie beispielsweise zwei VMs haben, die sich eine einzige öffentliche IP-Adresse für NAT für ausgehenden Datenverkehr teilen, können Sie im Fall einer SNAT-Überlastung die Anzahl von SNAT-Ports, die von den standardmäßigen 1.024 Ports zugeordnet wurden, erhöhen. Jede öffentliche IP-Adresse kann bis zu 51.200 kurzlebige Ports bereitstellen.  Wenn Sie eine Ausgangsregel mit einem einzigen öffentlichen IP-Adressen-Front-End konfigurieren, können Sie insgesamt 51.200 SNAT-Ports auf VMs im Back-End-Pool verteilen.  Bei zwei VMs können maximal 25.600 SNAT-Ports mit einer Ausgangsregel (2 × 25.600 = 51.200) zugeordnet werden.

Erfahren Sie mehr über [ausgehende Verbindungen](load-balancer-outbound-connections.md) und darüber, wie [SNAT](load-balancer-outbound-connections.md#snat)-Ports zugeordnet und verwendet werden.

### <a name="outboundonly"></a> Ausschließliches Aktivieren des ausgehenden Datenverkehrs

Sie können eine öffentliche Load Balancer Standard-Instanz verwenden, um NAT für ausgehenden Datenverkehr für eine Gruppe von VMs bereitzustellen. In diesem Szenario können Sie eine Ausgangsregel alleine ohne zusätzliche Regeln verwenden.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT für ausgehenden Datenverkehr nur für VMs (kein eingehender Datenverkehr)

Definieren Sie eine öffentliche Load Balancer Standard-Instanz, stellen Sie die VMs im Back-End-Pool bereit, und konfigurieren Sie eine Ausgangsregel, um NAT für ausgehenden Datenverkehr zu programmieren und die ausgehenden Verbindungen so zu konfigurieren, dass sie von einer bestimmten öffentlichen IP-Adresse stammen. Sie können auch ein Präfix für öffentliche IP-Adressen verwenden. Das vereinfacht, die Quelle ausgehender Verbindungen auf die Whitelist zu setzen.

1. Erstellen Sie eine öffentliche Load Balancer Standard-Instanz.
2. Erstellen Sie einen Back-End-Pool, und stellen Sie die VMs in einem Back-End-Pool der öffentlichen Load Balancer-Instanz bereit.
3. Konfigurieren Sie eine Ausgangsregel in der öffentlichen Load Balancer-Instanz, um die NAT für ausgehenden Datenverkehr für diese VMs zu programmieren.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT für ausgehenden Datenverkehr für interne Load Balancer Standard-Szenarien

Beim Verwenden einer internen Load Balancer Standardinstanz ist die NAT für ausgehenden Datenverkehr erst verfügbar, nachdem die ausgehende Konnektivität explizit deklariert wurde. Sie können die ausgehende Konnektivität mit einer Ausgangsregel definieren, indem Sie mithilfe dieser Schritte ausgehende Konnektivität für VMs hinter einer internen Load Balancer-Standardinstanz erstellen:

1. Erstellen Sie eine öffentliche Load Balancer Standard-Instanz.
2. Erstellen Sie einen Back-End-Pool, und stellen Sie die VMs über die interne Load Balancer-Instanz hinaus in einem Back-End-Pool der öffentlichen Load Balancer-Instanz bereit.
3. Konfigurieren Sie eine Ausgangsregel in der öffentlichen Load Balancer-Instanz, um die NAT für ausgehenden Datenverkehr für diese VMs zu programmieren.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Aktivieren von TCP- und UDP-Protokollen für NAT für ausgehenden Datenverkehr mit einer öffentlichen Load Balancer Standard-Instanz

- Beim Verwenden einer öffentlichen Load Balancer Standard-Instanz entspricht die bereitgestellte automatische NAT-Programmierung für ausgehenden Datenverkehr dem Transportprotokoll der Lastenausgleichsregel.  

   1. Deaktivieren Sie SNAT für ausgehenden Datenverkehr in der Lastenausgleichsregel.
   2. Konfigurieren Sie eine Ausgangsregel in derselben Load Balancer-Instanz.
   3. Verwenden Sie erneut den Back-End-Pool, den Ihre VMs bereits verwenden.
   4. Geben Sie „Protokoll“: „Alle“ als Teil der Ausgangsregel an.

- Wenn nur NAT-Eingangsregeln verwendet werden, wird keine NAT für ausgehenden Datenverkehr bereitgestellt.

   1. Stellen Sie die VMs in einem Back-End-Pool bereit.
   2. Definieren Sie mindestens eine Front-End-IP-Konfiguration mit mindestens einer öffentlichen IP-Adresse oder einem Präfix für öffentliche IP-Adressen.
   3. Konfigurieren Sie eine Ausgangsregel in derselben Load Balancer-Instanz.
   4. Geben Sie „Protokoll“: „Alle“ als Teil der Ausgangsregel an.

## <a name="limitations"></a>Einschränkungen

- Die maximale Anzahl von verwendbaren kurzlebigen Ports pro Front-End-IP-Adresse beträgt 51.200.
- Das konfigurierbare Leerlauftimeout für ausgehenden Datenverkehr beträgt ist 4 bis 120 Minuten (240 bis 7200 Sekunden).
- Load Balancer unterstützt kein ICMP für die NAT ausgehenden Datenverkehrs.
- Sie können Ausgangsregeln im Portal weder konfigurieren noch ansehen.  Verwenden Sie stattdessen Vorlagen, die REST-API, die Azure CLI 2.0 oder PowerShell.
- Ausgangsregeln können nur auf die primäre NIC und die primäre IP-Konfiguration angewendet werden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung von [Load Balancer für ausgehende Verbindungen](load-balancer-outbound-connections.md)
- Erfahren Sie mehr über [Load Balancer Standard](load-balancer-standard-overview.md).
- Weitere Informationen zur [bidirektionalen TCP-Zurücksetzung bei Leerlauftimeout](load-balancer-tcp-reset.md)
- [Konfigurieren von Ausgangsregeln mit der Azure CLI 2.0](configure-load-balancer-outbound-cli.md)
