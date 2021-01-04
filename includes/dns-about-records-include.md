---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 8ca054b3a3d5147b7d98a021ce1e26d02d5581b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011562"
---
### <a name="record-names"></a>Eintragsnamen

Einträge in Azure DNS werden mit relativen Namen angegeben. Ein *vollqualifizierter* Domänenname (fully qualified domain name, FQDN) beinhaltet den Zonennamen, ein *relativer* Name hingegen nicht. Der relative Eintragsname `www` in der Zone `contoso.com` gibt beispielsweise den vollqualifizierten Eintragsnamen `www.contoso.com` an.

Ein Eintrag an der *Zonenspitze* ist ein DNS-Eintrag im Stamm (oder der *Spitze*) einer DNS-Zone. Beispiel: Ein Eintrag an der Zonenspitze der DNS-Zone `contoso.com` besitzt auch den vollqualifizierten Namen `contoso.com` (dies wird mitunter als *reine* Domäne bezeichnet).  Gemäß der Konvention wird der relative Name “\@“ verwendet, um Einträge an der Zonenspitze darzustellen.

### <a name="record-types"></a>Eintragstypen

Jeder DNS-Eintrag hat einen Namen und einen Typ. Datensätze werden anhand der darin enthaltenen Daten nach verschiedenen Typen unterteilt. Der häufigste Typ ist ein „A“-Eintrag, der einer IPv4-Adresse einen Namen zuordnet. Ein weiterer gängiger Typ ist ein „MX“-Eintrag, der einem E-Mail-Server einen Namen zuordnet.

Azure DNS unterstützt alle allgemeinen DNS-Eintragstypen: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV und TXT. Beachten Sie, dass [SPF-Datensätze mithilfe von TXT-Einträgen dargestellt werden](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Ressourceneintragssätze

In einigen Fällen müssen Sie mehrere DNS-Einträge mit einem bestimmten Namen und Typ erstellen. Angenommen, die Website „www.contoso.com“ wird auf zwei verschiedenen IP-Adressen gehostet. Für diese Website sind zwei verschiedene A-Einträge erforderlich (einer für jede IP-Adresse). Hier ist ein Beispiel für einen Ressourceneintragssatz:

```dns
www.contoso.com.        3600    IN    A    134.170.185.46
www.contoso.com.        3600    IN    A    134.170.188.221
```

Azure DNS verwaltet alle DNS-Einträge mithilfe von *Ressourceneintragssätzen*. Bei *Ressourceneintragssätzen* handelt es sich um die Auflistung von DNS-Einträgen in einer Zone, die den gleichen Namen und den gleichen Typ aufweisen. Die meisten Ressourceneintragssätze enthalten einen einzelnen Eintrag, aber Beispiele wie das obige, in dem eine Datensatzgruppe mehr als einen Eintrag enthält, sind nicht ungewöhnlich.

Angenommen, dass Sie bereits einen A-Eintrag „www“ in der Zone „contoso.com“ erstellt haben, der auf die IP Adresse „134.170.185.46“ zeigt (der erste Eintrag darüber).  Um den zweiten Eintrag zu erstellen, müssen Sie diesen Eintrag zum bestehenden Ressourceneintragssatz hinzufügen, anstatt einen weiteren Ressourceneintragssatz zu erstellen.

Die Eintragstypen SOA und CNAME sind Ausnahmen. Die DNS-Standards lassen mehrere Datensätze mit demselben Namen für diese Typen nicht zu, daher können diese Ressourceneintragssätze nur einen Eintrag enthalten.