---
title: Mehrere Front-Ends – Azure Load Balancer
description: In diesem Lernpfad erhalten Sie zunächst eine Übersicht über mehrere Front-Ends in Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5c2072d13cab9839a276c0437747d7075918e78a
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696879"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Mehrere Front-Ends für Azure Load Balancer

Mit Azure Load Balancer können Sie für Dienste an mehreren Ports, mehreren IP-Adressen oder beidem einen Lastenausgleich vornehmen. Sie können öffentliche und interne Load Balancer-Definitionen verwenden, um einen Lastenausgleich für Datenflüsse innerhalb einer VM-Gruppe durchzuführen.

Dieser Artikel beschreibt die Grundlagen dieser Funktion, wichtige Konzepte und Einschränkungen. Wenn Sie Dienste mit einer IP-Adresse verfügbar machen möchten, finden Sie vereinfachte Anweisungen für öffentliche Load Balancer-Konfigurationen [hier](./quickstart-load-balancer-standard-public-portal.md) und für interne Load Balancer-Konfigurationen [hier](./quickstart-load-balancer-standard-internal-portal.md). Mehrere Front-Ends können einer Konfiguration mit einem einzelnen Front-End inkrementell hinzugefügt werden. Mit den Konzepten in diesem Artikel können Sie eine vereinfachte Konfiguration jederzeit erweitern.

Wenn Sie einen Azure Load Balancer definieren, sind eine Front-End- und eine Back-End-Pool-Konfiguration über Regeln verbunden. Mit dem Integritätstest, auf den von der Regel verwiesen wird, wird bestimmt, wie neue Datenflüsse an einen Knoten im Back-End-Pool gesendet werden. Das Front-End (bzw. VIP) wird durch eine aus 3 Tupeln bestehende IP-Adresse (öffentlich oder intern), ein Transportprotokoll (UDP oder TCP) und eine Portnummer von der Lastenausgleichsregel definiert. Der Back-End-Pool ist eine Sammlung von VM-IP-Konfigurationen (Teil der NIC-Ressource), die auf den Load Balancer-Back-End-Pool verweisen.

Die folgende Tabelle enthält einige Beispielkonfigurationen des Front-Ends:

| Front-End | IP-Adresse | Protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

Die Tabelle enthält vier verschiedene Front-Ends. Die Front-Ends 1, 2 und 3 sind ein einzelnes Front-End mit mehreren Regeln. Die gleiche IP-Adresse wird verwendet, aber der Port oder das Protokoll ist für jedes Front-End anders. Die Front-Ends 1 und 4 sind ein Beispiel für mehrere Front-Ends, wobei das gleiche Front-End-Protokoll und der gleiche Port für mehrere Front-Ends verwendet werden.

Azure Load Balancer bietet Flexibilität beim Definieren der Lastenausgleichsregeln. Eine Regel deklariert, wie eine Adresse und ein Port auf dem Front-End der Zieladresse und dem Port auf dem Back-End zugeordnet werden. Ob Back-End-Ports in mehreren Regeln wiederverwendet werden, hängt vom Typ der Regel ab. Für jeden Regeltyp gelten bestimmte Anforderungen, die die Hostkonfiguration und den Testentwurf beeinflussen können. Es gibt zwei Regeltypen:

1. Die Standardregel ohne Wiederverwendung des Back-End-Ports
2. Die Floating IP-Regel, bei der Back-End-Ports wiederverwendet werden

Mit Azure Load Balancer können Sie beide Regeltypen in einer Load Balancer-Konfiguration mischen. Der Load Balancer kann sie gleichzeitig für eine bestimmte VM oder eine beliebige Kombination verwenden, sofern Sie die Einschränkungen der Regel beachten. Welchen Regeltyp Sie auswählen, hängt von den Anforderungen der Anwendung und der Komplexität bei der Unterstützung der Konfiguration ab. Sie sollten bewerten, welche Regeltypen für Ihr Szenario am besten geeignet sind.

Wir untersuchen diese Szenarien näher und beginnen mit dem Standardverhalten.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regeltyp 1: Keine Wiederverwendung von Back-End-Ports

![Abbildung mehrerer Front-Ends mit Front-Ends in Grün und Lila](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In diesem Szenario werden die Front-Ends wie folgt konfiguriert:

| Front-End | IP-Adresse | Protokoll | port |
| --- | --- | --- | --- |
| ![Front-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Front-End (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Die DIP ist das Ziel des eingehenden Datenflusses. Im Back-End-Pool macht jede VM den gewünschten Dienst an einem eindeutigen Port einer DIP verfügbar. Dieser Dienst ist dem Front-End über eine Regeldefinition zugeordnet.

Wir definieren zwei Regeln:

| Regel | Front-End-Zuordnung | Im Back-End-Pool |
| --- | --- | --- |
| 1 |![Front-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Back-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![Back-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Back-End (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![Back-End (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Die vollständige Zuordnung in Azure Load Balancer sieht jetzt wie folgt aus:

| Regel | Front-End-IP-Adresse | Protokoll | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![Regel (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP-IP-Adresse |80 |
| ![Regel (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP-IP-Adresse |81 |

Jede Regel muss einen Datenfluss mit einer eindeutigen Kombination aus IP-Zieladresse und Zielport erzeugen. Durch die Änderung des Zielports für den Datenfluss können mehrere Regeln Datenflüsse an die gleiche DIP an unterschiedlichen Ports übermitteln.

Integritätstests werden immer an die DIP einer VM weitergeleitet. Sie müssen sicherstellen, dass der Test den Zustand der VM widerspiegelt.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regeltyp 2: Wiederverwendung von Back-End-Ports mit Floating IP

Azure Load Balancer bietet die Flexibilität, den Front-End-Port unabhängig vom verwendeten Regeltyp für mehrere Front-Ends wiederzuverwenden. Darüber hinaus ist es in einigen Anwendungsszenarien eine Priorität bzw. eine Anforderung, den gleichen Port für mehrere Anwendungsinstanzen auf einer einzelnen VM im Back-End-Pool zu verwenden. Gängige Beispiele für die Portwiederverwendung sind das Clustering für Hochverfügbarkeit, virtuelle Netzwerkgeräte und die Bereitstellung mehrerer TLS-Endpunkte ohne erneute Verschlüsselung.

Wenn Sie den Back-End-Port in mehreren Regeln wiederverwenden möchten, müssen Sie in der Regeldefinition Floating IP aktivieren.

„Floating IP“ ist die bei Azure verwendete Benennung für einen Bestandteil von Direct Server Return (DSR). DSR besteht aus zwei Teilen: einer Datenflusstopologie und einem Zuordnungsschema für IP-Adressen. Auf Plattformebene wird Azure Load Balancer immer in einer DSR-Datenflusstopologie betrieben, unabhängig davon, ob Floating IP aktiviert ist. Dies bedeutet, dass der ausgehende Teil eines Datenflusses immer ordnungsgemäß so umgeschrieben wird, dass er direkt wieder an den Ursprung übermittelt wird.

Um die Nutzung zu vereinfachen, stellt Azure mit dem Standardregeltyp ein herkömmliches Zuordnungsschema für IP-Adressen für den Lastenausgleich bereit. Durch das Aktivieren von Floating IP ändert sich das Zuordnungsschema für IP-Adressen, sodass größere Flexibilität möglich ist, wie im Folgenden beschrieben.

Das folgende Diagramm veranschaulicht diese Konfiguration:

![Abbildung mehrerer Front-Ends mit grünen und lilafarbenen Front-Ends mit DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

In diesem Szenario hat jede VM im Back-End-Pool drei Netzwerkschnittstellen:

* DIP: eine mit der VM verknüpfte virtuelle NIC (IP-Konfiguration der NIC-Ressource von Azure)
* Front-End 1: eine Loopbackschnittstelle im Gastbetriebssystem, die mit der IP-Adresse von Front-End 1 konfiguriert ist
* Front-End 2: eine Loopbackschnittstelle im Gastbetriebssystem, die mit der IP-Adresse von Front-End 2 konfiguriert ist

Führen Sie für jeden virtuellen Computer im Back-End-Pool die folgenden Befehle an einer Windows-Eingabeaufforderung aus.

Geben Sie den folgenden Befehl ein, um die Liste der Schnittstellennamen auf Ihrem virtuellen Computer abzurufen:

```console
netsh interface show interface 
```

Geben Sie für den Netzwerkadapter des virtuellen Computers (von Azure verwaltet) den folgenden Befehl ein:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(Ersetzen Sie „interfacename“ durch den Namen dieser Schnittstelle.)

Wiederholen Sie für jede hinzugefügte Loopbackschnittstelle die folgenden Befehle:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(Ersetzen Sie „interfacename“ durch den Namen dieser Loopbackschnittstelle.)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(Ersetzen Sie „interfacename“ durch den Namen dieser Loopbackschnittstelle.)

> [!IMPORTANT]
> Die Konfiguration der Loopbackschnittstellen erfolgt innerhalb des Gastbetriebssystems. Diese Konfiguration wird nicht von Azure vorgenommen oder verwaltet. Ohne diese Konfiguration funktionieren die Regeln nicht. Integritätstestdefinitionen verwenden anstelle der Loopbackschnittstelle, die das DSR-Front-End darstellt, die DIP der VM. Deshalb muss Ihr Dienst Testantworten an einem DIP-Port bereitstellen, die den Status des Diensts wiedergeben, der an der das DSR-Front-End darstellenden Loopbackschnittstelle angeboten wird.


Gehen wir von der gleichen Front-End-Konfiguration wie im vorherigen Szenario aus:

| Front-End | IP-Adresse | Protokoll | port |
| --- | --- | --- | --- |
| ![Front-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![Front-End (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Wir definieren zwei Regeln:

| Regel | Front-End | Zuordnung zum Back-End-Pool |
| --- | --- | --- |
| 1 |![Regel (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![Back-End (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (in VM1 und VM2) |
| 2 |![Regel (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![Back-End (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (in VM1 und VM2) |

Die folgende Tabelle veranschaulicht die vollständige Zuordnung im Load Balancer:

| Regel | Front-End-IP-Adresse | Protokoll | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![Regel (grün)](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Identisch mit dem Front-End (65.52.0.1) |Identisch mit dem Front-End (80) |
| ![Regel (lila)](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Identisch mit dem Front-End (65.52.0.2) |Identisch mit dem Front-End (80) |

Das Ziel des eingehenden Datenflusses ist die Front-End-IP-Adresse der Loopbackschnittstelle der VM. Jede Regel muss einen Datenfluss mit einer eindeutigen Kombination aus IP-Zieladresse und Zielport erzeugen. Durch die Änderung der IP-Zieladresse für den Datenfluss ist die Portwiederverwendung auf der gleichen VM möglich. Ihr Dienst wird für den Load Balancer verfügbar gemacht, indem er an die Front-End-IP-Adresse und den Port der jeweiligen Loopbackschnittstelle gebunden wird.

Beachten Sie, dass in diesem Beispiel der Zielport nicht geändert wird. Obwohl dies ein Floating IP-Szenario ist, unterstützt Azure Load Balancer auch das Definieren einer Regel, um den Back-End-Zielport zu ändern, damit er sich vom Front-End-Zielport unterscheidet.

Der Floating IP-Regeltyp bildet die Grundlage für mehrere Load Balancer-Konfigurationsmuster. Ein Beispiel, das derzeit verfügbar ist, ist die [Konfiguration von SQL AlwaysOn mit mehreren Listenern](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) . Mit der Zeit werden weitere dieser Szenarien dokumentiert.

## <a name="limitations"></a>Einschränkungen

* Konfigurationen mit mehreren Front-Ends werden nur für IaaS-VMs unterstützt.
* Bei der Floating IP-Regel muss die Anwendung die primäre IP-Konfiguration für ausgehende SNAT-Datenflüsse verwenden. Wenn Ihre Anwendung an die Front-End-IP-Adresse gebunden ist, die an der Loopbackschnittstelle im Gastbetriebssystem konfiguriert ist, ist die Azure-SNAT für ausgehenden Datenverkehr nicht verfügbar, um den ausgehenden Datenfluss umzuschreiben, und beim Datenfluss tritt ein Fehler auf.  Lesen Sie die Informationen unter [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).
* Floating IP wird für sekundäre IP-Konfigurationen in Szenarien mit internem Lastenausgleich derzeit nicht unterstützt.
* Öffentliche IP-Adressen haben Auswirkungen auf die Abrechnung. Weitere Informationen finden Sie unter [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Es gelten Grenzwerte für Abonnements. Weitere Informationen finden Sie unter [Einschränkungen für Dienste](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md), um die Auswirkung mehrerer Front-Ends auf das Verhalten ausgehender Verbindungen zu verstehen.