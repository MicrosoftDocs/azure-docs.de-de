---
title: Azure Load Balancer Standard und Verfügbarkeitszonen
titlesuffix: Azure Load Balancer
description: Load Balancer Standard und Verfügbarkeitszonen
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: kumud
ms.openlocfilehash: 6f33be6e418366f57d243f578035b5c87079c99e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60734455"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard und Verfügbarkeitszonen

Azure Load Balancer Standard-SKU unterstützt [Verfügbarkeitszonen](../availability-zones/az-overview.md)-Szenarios. Load Balancer Standard bietet verschiedene neue Konzepte, mit denen Sie die Verfügbarkeit in Ihrem End-to-End-Szenario optimieren können, indem Sie Ressourcen an Zonen ausrichten und auf mehrere Zonen verteilen.  In [Verfügbarkeitszonen](../availability-zones/az-overview.md) erfahren Sie, was Verfügbarkeitszonen sind, welche Regionen derzeit Verfügbarkeitszonen unterstützen, und Sie erhalten Informationen über Konzepte und Produkte, die im Zusammenhang stehen. Verfügbarkeitszonen und Load Balancer Standard bilden eine umfassenden und flexiblen Satz an Features, mit dem sich viele verschiedene Szenarien darstellen lassen.  Lesen Sie dieses Dokument, um diese [Konzepte](#concepts) und die [Entwurfsanleitung](#design) für das grundlegende Szenario zu verstehen.

>[!IMPORTANT]
>Informationen zu verwandten Themen finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md), einschließlich aller regionsspezifischen Informationen.

## <a name="concepts"></a> Auf den Load Balancer angewendete Verfügbarkeitszonenkonzepte

Es gibt keine direkte Beziehung zwischen Load Balancer-Ressourcen und der tatsächlichen Infrastruktur; durch Erstellen einer Load Balancer-Instanz wird keine Instanz erstellt. Load Balancer-Ressourcen sind Objekte, in denen Sie ausdrücken können, wie Azure seine vorgefertigte mehrinstanzenfähige Infrastruktur programmieren sollte, um das Szenario zu erzielen, das Sie erstellen möchten.  Dies ist im Kontext von Verfügbarkeitszonen von Bedeutung, da eine einzelne Load Balancer-Ressource die Programmierung der Infrastruktur in mehrere Verfügbarkeitszonen steuern kann, während ein zonenredundanter Dienst aus Kundensicht als eine einzige Ressource erscheint.

Die Funktionen einer Load Balancer-Ressource werden als Front-End-, Regel-, Integritätstest- und Back-End-Pool-Definition ausgedrückt.

Im Kontext von Verfügbarkeitszonen werden das Verhalten und die Eigenschaften einer Load Balancer-Ressource als „zonenredundant“ oder „zonal“ beschrieben.  „Zonenredundant“ und „zonal“ beschreiben die Zonalität einer Eigenschaft.  Im Kontext des Load Balancers bezieht sich „zonenredundant“ immer auf *mehrere Zonen*, und „zonal“ bedeutet, dass der Dienst in einer *einzelnen Zone* isoliert wird.

Der öffentliche und der interne Load Balancer unterstützen zonenredundante und zonengebundene Szenarien, und beide können Datenverkehr nach Bedarf zonenübergreifend weiterleiten (*zonenübergreifender Lastenausgleich*).

Eine Load Balancer-Ressource selbst ist regional und nie zonal.  Ein VNet und ein Subnetz sind auch immer regional und nie zonal.

### <a name="frontend"></a>Front-End

Ein Load Balancer-Front-End ist eine Front-End-IP-Konfiguration, die entweder auf eine öffentliche IP-Adressressource oder eine private IP-Adresse innerhalb des Subnetzes einer virtuellen Netzwerkressource verweist.  Es bildet den Lastenausgleichsendpunkt, in dem Ihr Dienst verfügbar gemacht wird.

Eine Load Balancer-Ressource darf gleichzeitig zonale und zonenredundante Front-Ends enthalten. 

Wenn eine öffentliche IP-Ressource einer Zone zugesichert wurde, ist die Zonalität (oder deren Fehlen) nicht veränderbar.  Wenn Sie die Zonalität eines öffentlichen IP-Front-Ends ändern oder auslassen möchten, müssen Sie die öffentliche IP-Adresse in der entsprechenden Zone neu erstellen.  

Sie können die Zonalität eines Front-Ends eines internen Load Balancers ändern, indem Sie das Front-End entfernen und erneut erstellen und dabei die Zonalität ändern oder auslassen.

Wenn Sie mehrere Front-Ends verwenden, informieren Sie sich in dem Artikel [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md) über wichtige Aspekte.

#### <a name="zone-redundant-by-default"></a>Standardmäßige Zonenredundanz

>[!IMPORTANT]
>Informationen zu verwandten Themen finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md), einschließlich aller regionsspezifischen Informationen.

In einer Region mit Verfügbarkeitszonen ist ein Front-End mit Load Balancer Standard standardmäßig zonenredundant.  Eine einzelne Front-End-IP-Adresse kann Zonenfehler überstehen und verwendet werden, um alle Back-End-Pool-Elemente unabhängig von der Zone zu erreichen. Dies bedeutet keinen störungsfreien Datenpfad, aber alle Wiederholungen oder Wiederherstellungen sind erfolgreich. DNS-Redundanzschemas sind nicht erforderlich. Die einzelne IP-Adresse des Front-Ends wird simultan durch mehrere unabhängige Infrastrukturbereitstellungen in mehreren Verfügbarkeitszonen bedient.  „Zonenredundant“ bedeutet, dass alle eingehenden oder ausgehenden Datenströme von mehreren Verfügbarkeitszonen in einer Region gleichzeitig über eine einzelne IP-Adresse geleitet werden.

Eine oder mehrere Verfügbarkeitszonen können ausfallen, wobei der Datenpfad solange überdauert, wie eine Zone in der Region fehlerfrei bleibt. Die zonenredundante Konfiguration ist die Standardeinstellung und erfordert keine weiteren Aktionen.  

Verwenden Sie das folgende Skript zum Erstellen einer zonenredundanten öffentlichen IP-Adresse für Ihren internen Load Balancer Standard. Wenn Sie vorhandene Resource Manager-Vorlagen in Ihrer Konfiguration verwenden, fügen Sie diesen Vorlagen den Abschnitt **sku** hinzu.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Verwenden Sie das folgende Skript zum Erstellen einer zonenredundanten Front-End-IP-Adresse für Ihren internen Load Balancer Standard. Wenn Sie vorhandene Resource Manager-Vorlagen in Ihrer Konfiguration verwenden, fügen Sie diesen Vorlagen den Abschnitt **sku** hinzu.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

#### <a name="optional-zone-isolation"></a>Optionale Zonenisolation

Sie können festlegen, dass ein Front-End – ein so genanntes *zonales Front-End* – einer einzelnen Zone zugesichert wird.  Dies bedeutet, dass alle eingehenden oder ausgehenden Datenströme von einer einzelnen Zone in einer Region bedient werden.  Ihr Front-End ist von der Integrität der Zone abhängig.  Der Datenpfad ist nicht betroffen von Fehlern in Zonen, in denen er nicht zugesichert wurde. Mit zonalen Front-Ends können Sie eine IP-Adresse pro Verfügbarkeitszone verfügbar machen.  Außerdem können Sie zonengebundene Front-Ends direkt verwenden oder, wenn das Front-End aus öffentlichen IP-Adressen besteht, sie in ein DNS-Lastenausgleichsprodukt wie [Traffic Manager](../traffic-manager/traffic-manager-overview.md) integrieren. Dabei können Sie einen einzigen DNS-Namen verwenden, den ein Client in mehrere zonengebundene IP-Adressen auflöst.  Sie können dies auch verwenden, um pro Zone Lastenausgleichs-Endpunkte zur individuellen Überwachung jeder Zone verfügbar zu machen.  Wenn Sie diese Konzepte (zonenredundant und zonal für dasselbe Back-End) mischen möchten, lesen Sie den Artikel [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md).

Für ein öffentliches Load Balancer-Front-End fügen Sie der öffentlichen IP-Adresse, auf die die Front-End-IP-Konfiguration verweist, einen *zones*-Parameter hinzu.  

Für ein internes Load Balancer-Front-End fügen Sie der internen Load Balancer-Front-End-IP-Konfiguration einen *zones*-Parameter hinzu. Das zonale Front-End bewirkt, dass der Load Balancer eine IP-Adresse in einem Subnetz einer bestimmten Zone zusichert.

Verwenden Sie das folgende Skript, um eine zonale standardmäßige öffentliche IP-Adresse in der Verfügbarkeitszone 1 zu erstellen. Wenn Sie vorhandene Resource Manager-Vorlagen in Ihrer Konfiguration verwenden, fügen Sie diesen Vorlagen den Abschnitt **sku** hinzu.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Verwenden Sie das folgende Skript, um ein internes Front-End mit Load Balancer Standard in der Verfügbarkeitszone 1 zu erstellen.

Wenn Sie vorhandene Resource Manager-Vorlagen in Ihrer Konfiguration verwenden, fügen Sie diesen Vorlagen den Abschnitt **sku** hinzu. Definieren Sie außerdem die Eigenschaft **zones** in der Front-End-IP-Konfiguration für die untergeordnete Ressource.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

### <a name="cross-zone-load-balancing"></a>Zonenübergreifender Lastenausgleich

Zonenübergreifender Lastenausgleich ist die Fähigkeit von Load Balancer, einen Back-End-Endpunkt in einer beliebigen Zone zu erreichen, und ist unabhängig vom Front-End und seiner Zonalität.

Wenn Sie Ihre Bereitstellung innerhalb einer einzelnen Zone ausrichten und garantieren möchten, richten Sie zonale Front-End- und zonale Back-End-Ressourcen an derselben Zone aus. Es ist keine weitere Aktion erforderlich.

### <a name="backend"></a>Back-End

Load Balancer arbeitet mit Virtual Machines zusammen.  Ein virtueller Computer in einem einzelnen VNet kann Teil des Back-End-Pools sein, unabhängig davon, ob er einer Zone zugesichert wurde oder welcher Zone er zugesichert wurde.

Wenn Sie Ihr Front-End und Back-End an einer einzelnen Zone ausrichten und dort zusichern möchten, platzieren Sie VMs nur innerhalb derselben Zone im entsprechenden Back-End-Pool.

Wenn Sie VMs über mehrere Zonen hinweg adressieren möchten, platzieren Sie einfach virtuelle Computer aus mehreren Zonen im selben Back-End-Pool.  Bei Verwendung von VM-Skalierungsgruppen können Sie eine oder mehrere VM-Skalierungsgruppen im selben Back-End-Pool platzieren.  Außerdem kann jede dieser VM-Skalierungsgruppen sich in einer einzelnen oder mehreren Zonen befinden.

### <a name="outbound-connections"></a>Ausgehende Verbindungen

[Ausgehende Verbindungen](load-balancer-outbound-connections.md) werden von allen Zonen bedient und sind in einer Region mit Verfügbarkeitszonen automatisch zonenredundant, wenn ein virtueller Computer mit einem öffentlichen Load Balancer und einem zonenredundanten Front-End verknüpft ist.  SNAT-Portzuordnungen einer ausgehenden Verbindung überstehen Zonenfehler.  

Wenn der virtuelle Computer wiederum mit einem öffentlichen Load Balancer und einem zonalen Front-End verknüpft ist, werden ausgehende Verbindungen garantiert von einer einzelnen Zone bedient.  Ausgehende Verbindungen sind von der Integrität der jeweiligen Zone abhängig.

SNAT-Portvorabzuordnung und -Algorithmus sind mit oder ohne Zonen identisch.

### <a name="health-probes"></a>Integritätstests

Ihre vorhandenen Integritätstestdefinitionen bleiben so, wie sie ohne Verfügbarkeitszonen sind.  Wir haben aber das Integritätsmodell auf Infrastrukturebene erweitert. 

Bei Verwendung zonenredundanter Front-Ends erweitert Load Balancer sein internes Integritätsmodell, um unabhängig die Erreichbarkeit eines virtuellen Computers aus jeder Verfügbarkeitszone zu testen und Pfade, bei denen Fehler aufgetreten sind, zonenübergreifend ohne Eingriff des Kunden zu schließen.  Wenn ein bestimmter Pfad von der Load Balancer-Infrastruktur einer Zone aus zu einem virtuellen Computer in einer anderen Zone nicht verfügbar ist, kann Load Balancer diesen Fehler erkennen und vermeiden. Andere Zonen, die diesen virtuellen Computer erreichen können, bedienen den virtuellen Computer weiterhin von ihren jeweiligen Front-Ends aus.  Daher ist es möglich, dass die Zonen während Fehlerereignissen geringfügig unterschiedliche Datenstromverteilungen aufweisen und dabei die allgemeine Integrität Ihres End-to-End-Diensts schützen.

## <a name="design"></a> Überlegungen zum Entwurf

Load Balancer ist im Kontext von Verfügbarkeitszonen absichtlich flexibel. Sie können die Ausrichtung an Zonen oder Zonenredundanz auswählen.  Erhöhte Verfügbarkeit kann erhöhte Komplexität mit sich bringen, und Ihr Entwurf muss Verfügbarkeit bei optimaler Leistung bieten.  Lassen Sie uns ein paar wichtige Überlegungen zum Entwurf anstellen.

### <a name="automatic-zone-redundancy"></a>Automatische Zonenredundanz

Load Balancer macht es Ihnen leicht, eine einzelne IP-Adresse als zonenredundantes Front-End einzusetzen. Eine zonenredundante IP-Adresse kann sicher eine zonale Ressource in einer Zone bedienen und einen oder mehrere Zonenfehler überstehen kann, solange eine Zone in der Region fehlerfrei ist. Andererseits ist ein zonales Front-End eine Reduzierung des Diensts auf eine einzelne Zone und von der jeweiligen Zone abhängig.

Zonenredundanz impliziert weder einen störungsfreien Datenpfad noch eine störungsfreie Steuerebene; es ist ausdrücklich eine Datenebene. Zonenredundante Datenströme können alle Zonen verwenden, und die Datenströme eines Kunden verwenden alle fehlerfreien Zonen in einer Region. Bei Ausfall einer Zone sind Datenströme, die zu diesem Zeitpunkt fehlerfreie Zonen verwenden, nicht betroffen.  Datenströme, die zum Zeitpunkt des Zonenausfalls eine Zone verwenden, können beeinträchtigt werden, aber Anwendungen können wiederhergestellt werden, und diese Datenströme können in den verbleibenden fehlerfreien Zonen innerhalb der Region nach erneuter Übertragung oder Wiederherstellung fortgeführt werden, wenn Azure den Zonenausfall in den Griff bekommen hat.

### <a name="xzonedesign"></a> Zonenübergreifende Grenzen

Sie müssen sich unbedingt darüber im Klaren sein, dass Sie jedes Mal, wenn ein End-to-End-Dienst zonenübergreifend wirkt, nicht nur von einer, sondern potenziell von mehreren Zonen abhängig sind.  Daraus resultiert, dass Ihr End-to-End-Dienst möglicherweise keine Verfügbarkeit über nicht zonale Bereitstellungen erlangt hat.

Vermeiden Sie die Einführung unbeabsichtigter zonenübergreifender Abhängigkeiten, die Verfügbarkeitsverbesserungen bei der Verwendung von Verfügbarkeitszonen zunichtemachen.  Wenn Ihre Anwendung aus mehreren Komponenten besteht und Sie gegenüber Zonenausfällen resistent sein möchten, müssen Sie darauf achten, das Überdauern entsprechend wichtiger Komponenten bei Zonenausfällen sicherzustellen.  Eine einzelne wichtige Komponente für Ihre Anwendung kann z.B. die gesamte Anwendung beeinträchtigen, wenn sie nur in einer anderen Zone als der überlebenden vorhanden ist.  Berücksichtigen Sie darüber hinaus auch die Zonenwiederherstellung und wie Ihre Anwendung konvergieren wird. Wir betrachten nun einige wichtige Punkte und verwenden sie als Inspiration für Fragen, die aufkommen, während Sie über Ihr konkretes Szenario nachdenken.

- Wenn Ihre Anwendung aus zwei Komponenten wie einer IP-Adresse und einem virtuellen Computer mit verwaltetem Datenträger besteht und sie in Zone 1 und Zone 2 zugesichert sind, wird Ihr End-to-End-Dienst den Ausfall von Zone 1 nicht überstehen.  Planen Sie nicht zonenübergreifend, solange Sie nicht vollständig verstehen, dass Sie einen potenziell gefährlichen Fehlermodus erstellen.

- Wenn Ihre Anwendung aus zwei Komponenten wie einer IP-Adresse und einem virtuellen Computer mit verwaltetem Datenträger besteht und sie als zonenredundant bzw. in Zone 1 zugesichert sind, wird Ihr End-to-End-Dienst den Ausfall von Zone 2, Zone 3 oder beiden überstehen, solange Zone 1 nicht ausfällt.  Allerdings verlieren Sie in gewissem Umfang die Integrität Ihres Diensts aus dem Auge, wenn Sie sich nur auf die Erreichbarkeit des Front-Ends konzentrieren.  Erwägen Sie die Entwicklung eines umfassenderen Integritäts- und Kapazitätsmodells.  Sie können zonenredundante und zonengebundene Konzepte zusammen verwenden, um Einblicke und Verwaltungsfähigkeit zu verbessern.

- Wenn Ihre Anwendung über zwei Komponenten wie ein zonenredundantes Load Balancer-Front-End und eine zonenübergreifende VM-Skalierungsgruppe in drei Zonen verfügt, sind Ihre Ressourcen in nicht von Ausfällen betroffenen Zonen verfügbar, aber die Kapazität Ihres End-to-End-Diensts ist während des Zonenausfalls möglicherweise beeinträchtigt. Aus Sicht der Infrastruktur kann Ihre Bereitstellung einen oder mehrere Zonenausfälle überstehen, und daraus ergeben sich folgende Fragen:
  - Verstehen Sie, wie Ihre Anwendung mit solchen Ausfällen und eingeschränkter Kapazität umgeht?
  - Müssen Sie Sicherheitsmaßnahmen in Ihren Dienst integrieren, um ggf. ein Failover zu einem Regionspaar zu erzwingen?
  - Wie überwachen, erkennen und mildern Sie ein solches Szenario? Sie können möglicherweise mit der Load Balancer Standard-Diagnose die Überwachung Ihrer End-to-End-Dienstleistung erweitern. Berücksichtigen Sie, was verfügbar ist, und welche Ergänzung für ein vollständiges Bild erforderlich ist.

- In Zonen können Ausfälle leichter verständlich sein.  Allerdings unterscheidet sich ein Zonenausfall nicht von anderen Ausfällen, wenn es um Konzepte wie Timeouts, Neuversuche und Backoffalgorithmen geht. Obwohl Azure Load Balancer zonenredundante Pfade bietet und auf Paketebene in Echtzeit eine schnelle Wiederherstellung versucht, können Neuübertragungen oder Wiederherstellungen zu Beginn eines Fehlers auftreten, und Sie müssen wissen, wie Ihre Anwendung mit Fehlern umgeht. Ihr Lastenausgleichsschema wird überdauern, aber Sie müssen für Folgendes planen:
  - Versteht Ihr End-to-End-Dienst den Ausfall einer Zone, und wie nehmen Sie die Wiederherstellung vor, wenn der Status verloren geht?
  - Kann Ihre Anwendung sicher konvergieren, wenn eine Zone zurückkehrt?

### <a name="zonalityguidance"></a> „Zonenredundant“ im Vergleich zu „zonal“

>[!IMPORTANT]
>Informationen zu verwandten Themen finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md), einschließlich aller regionsspezifischen Informationen.

Zonenredundanz kann eine zonenagnostische und zugleich resistente Option mit einer einzelnen IP-Adresse für den Dienst bieten.  Sie kann wiederum die Komplexität reduzieren.  Zonenredundanz bietet auch zonenübergreifende Mobilität und kann sicher auf Ressourcen in jeder Zone verwendet werden.  Darüber hinaus ist sie zukunftssicher in Regionen ohne Verfügbarkeitszonen, was die Änderungen begrenzen kann, die erforderlich sind, sobald eine Region Verfügbarkeitszonen erhält.  Die Konfigurationssyntax für zonenredundante IP-Adressen oder Front-Ends ist in allen Regionen einsetzbar, einschließlich derjenigen ohne Verfügbarkeitszonen.

„Zonal“ kann eine explizite Zusicherung für eine Zone bieten, die von der Integrität der Zone abhängig ist. Das Zuordnen einer zonalen IP-Adresse oder eines zonalen Load Balancer-Front-Ends kann ein wünschenswertes oder geeignetes Attribut sein, insbesondere wenn Ihre angefügte Ressource ein zonaler virtueller Computer in der gleichen Zone ist.  Vielleicht erfordert die Anwendung auch explizite Kenntnisse darüber, in welcher Zone sich eine Ressource befindet, und Sie möchten explizit über die Verfügbarkeit in separaten Zonen nachdenken.  Sie können wählen, mehrere zonale Front-Ends für einen End-to-End-Dienst über Zonen verteilt verfügbar zu machen (d.h. pro Zone zonale Front-Ends für mehrere zonale VM-Skalierungsgruppen).  Wenn Ihre zonalen Front-Ends öffentliche IP-Adressen sind, können Sie diese mehreren zonale Front-Ends zum Verfügbarmachen des Diensts mit [Traffic Manager](../traffic-manager/traffic-manager-overview.md) verwenden.  Sie können mit mehreren zonalen Front-Ends auch pro Zone mit Überwachungslösungen von Drittanbietern Einblicke in Integrität und Leistung erzielen und den gesamten Dienst mit einem zonenredundanten Front-End verfügbar machen. Sie sollten zonale Ressourcen nur mit zonalen Front-Ends bedienen, die an derselben Zone ausgerichtet sind, und potenziell schädliche zonenübergreifende Szenarios für zonale Ressourcen vermeiden.  Zonale Ressourcen sind nur in Regionen vorhanden, in denen Verfügbarkeitszonen vorhanden sind.

Ohne Kenntnis der Dienstarchitektur gibt es keine Faustregel, welche Option die bessere Wahl ist.

## <a name="limitations"></a>Einschränkungen

- Während die Datenebene vollständig zonenredundant ist (solange keine Zonenzusicherung gemacht wurde), sind Vorgänge auf Steuerungsebene nicht vollständig zonenredundant.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Verfügbarkeitszonen](../availability-zones/az-overview.md)
- Weitere Informationen zum [Load Balancer Standard](load-balancer-standard-overview.md).
- Erfahren Sie mehr über den [Lastenausgleich für VMs innerhalb einer Zone mit einem Standard-Lastenausgleich und einem zonenredundanten Front-End](load-balancer-standard-public-zonal-cli.md).
- Erfahren Sie mehr über den [zonenübergreifenden Lastenausgleich für VMs mit einem Standard-Lastenausgleich und einem zonenredundanten Front-End](load-balancer-standard-public-zone-redundant-cli.md).
