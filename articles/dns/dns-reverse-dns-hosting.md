---
title: Hosten von Reverse-DNS-Lookupzonen in Azure DNS
description: Erfahren Sie, wie Sie mit Azure DNS die Reverse-DNS-Lookupzonen für Ihre IP-Adressbereiche hosten.
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 6a0aebc727233cdd838f3e1bf8eeb5cd247b9836
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014022"
---
# <a name="host-reverse-dns-lookup-zones-in-azure-dns"></a>Hosten von Reverse-DNS-Lookupzonen in Azure DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Artikel wird erläutert, wie Sie Reverse-DNS-Lookupzonen für Ihre zugewiesenen IP-Adressbereiche in Azure DNS hosten. Die durch Reverse-Lookupzonen dargestellten IP-Adressbereiche müssen Ihrer Organisation zugewiesen werden – in der Regel durch Ihren Internetdienstanbieter.

Informationen zum Konfigurieren von Reverse-DNS für eine Azure-eigene IP-Adresse, die Ihrem Azure-Dienst zugewiesen ist, finden Sie unter [Konfigurieren von Reverse-DNS für in Azure gehostete Dienste](dns-reverse-dns-for-azure-services.md).

Bevor Sie diesen Artikel lesen, sollten Sie sich mit der [Übersicht über Reverse-DNS und die Unterstützung in Azure](dns-reverse-dns-overview.md) vertraut machen.

In diesem Artikel werden die Schritte zum Erstellen Ihrer ersten Reverse-Lookup-DNS-Zone und des dazugehörigen Eintrags per Azure-Portal, Azure PowerShell, klassischer Azure CLI oder Azure CLI erläutert.

## <a name="create-a-reverse-lookup-dns-zone"></a>Erstellen einer Reverse-DNS-Lookupzone

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie im Menü **Hub** auf **Neu** > **Netzwerk** und anschließend auf **DNS-Zone**.

   ![Klicken auf „DNS-Zone“](./media/dns-reverse-dns-hosting/figure1.png)

1. Benennen Sie Ihre DNS-Zone im Bereich **DNS-Zone erstellen**. Der Name der Zone wird für IPv4- und IPv6-Präfixe unterschiedlich definiert. Gehen Sie zum Benennen der Zone gemäß den Anweisungen für [IPv4](#ipv4) oder [IPv6](#ipv6) vor. Klicken Sie abschließend auf **Erstellen**, um die Zone zu erstellen.

### <a name="ipv4"></a>IPv4

Der Name einer IPv4-Reverse-Lookupzone basiert auf dem IP-Adressbereich, den sie darstellt. Er sollte das folgende Format aufweisen: `<IPv4 network prefix in reverse order>.in-addr.arpa`. Beispiele finden Sie in der [Übersicht über Reverse-DNS und die Unterstützung in Azure](dns-reverse-dns-overview.md#ipv4).

> [!NOTE]
> Beim Erstellen von klassenlosen Reverse-DNS-Lookupzonen in Azure DNS muss im Zonennamen anstelle eines Schrägstrichs (`/`) ein Bindestrich (`-`) verwendet werden.
>
> Beispiel: Für den IP-Adressbereich 192.0.2.128/26 müssen Sie `128-26.2.0.192.in-addr.arpa` als Zonennamen verwenden, nicht `128/26.2.0.192.in-addr.arpa`.
>
> Der DNS-Standard unterstützt zwar beide Methoden, Azure DNS unterstützt jedoch keine DNS-Zonennamen mit Schrägstrich (`/`).

Das folgende Beispiel zeigt, wie Sie über das Azure-Portal eine Reverse-DNS-Zone der Klasse C mit dem Namen `2.0.192.in-addr.arpa` in Azure DNS erstellen:

 ![Screenshot, der zeigt, wie Sie über das Azure-Portal eine Reverse-DNS-Zone der Klasse C mit dem Namen „2.0.192.in-addr.arpa“ in Azure DNS erstellen.](./media/dns-reverse-dns-hosting/figure2.png)

**Ressourcengruppenstandort** definiert den Standort für die Ressourcengruppe. Die Angabe hat keine Auswirkungen auf die DNS-Zone. Der Standort der DNS-Zone ist immer „global“ und wird nicht angezeigt.

Die folgenden Beispiele zeigen, wie diese Aufgabe mit Azure PowerShell und der Azure-Befehlszeilenschnittstelle durchgeführt wird:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns zone create MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns zone create -g MyResourceGroup -n 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Der Name einer IPv6-Reverse-Lookupzone sollte folgendes Format aufweisen: `<IPv6 network prefix in reverse order>.ip6.arpa`.  Beispiele finden Sie in der [Übersicht über Reverse-DNS und die Unterstützung in Azure](dns-reverse-dns-overview.md#ipv6).


Das folgende Beispiel zeigt, wie Sie eine IPv6-Reverse-DNS-Lookupzone mit dem Namen `0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa` in Azure DNS über das Azure-Portal erstellen:

 ![Bereich „DNS-Zone erstellen“ mit ausgefüllten Feldern](./media/dns-reverse-dns-hosting/figure3.png)

**Ressourcengruppenstandort** definiert den Standort für die Ressourcengruppe. Die Angabe hat keine Auswirkungen auf die DNS-Zone. Der Standort der DNS-Zone ist immer „global“ und wird nicht angezeigt.

Die folgenden Beispiele zeigen, wie diese Aufgabe mit Azure PowerShell und der Azure-Befehlszeilenschnittstelle durchgeführt wird:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsZone -Name 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns zone create MyResourceGroup 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns zone create -g MyResourceGroup -n 0.0.0.0.d.c.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="delegate-a-reverse-dns-lookup-zone"></a>Delegieren einer Reverse-DNS-Lookupzone

Nachdem Sie Ihre Reverse-DNS-Lookupzone erstellt haben, müssen Sie sicherstellen, dass die Zone aus der übergeordneten Zone delegiert wird. Die DNS-Delegierung ermöglicht es dem DNS-Namensauflösungsprozess, nach den Namenservern zu suchen, die Ihre Reverse-DNS-Lookupzone hosten. Diese Namenserver können dann auf DNS-Reverse-Abfragen für die IP-Adressen in Ihrem Adressbereich reagieren.

Für Forward-Lookupzonen wird der Vorgang der Delegierung einer DNS-Zone unter [Delegieren einer Domäne an Azure DNS](dns-delegate-domain-azure-dns.md) beschrieben. Die Delegierung für Reverse-Lookupzonen funktioniert auf dieselbe Weise. Der einzige Unterschied besteht darin, dass Sie die Namenserver nicht mit der Registrierungsstelle Ihres Domänennamens konfigurieren müssen, sondern mit dem Internetdienstanbieter, der den IP-Adressbereich bereitgestellt hat.

## <a name="create-a-dns-ptr-record"></a>Erstellen eines DNS-PTR-Eintrags

### <a name="ipv4"></a>IPv4

Im folgenden Beispiel wird die Erstellung eines PTR-Eintrags in einer Reverse-DNS-Zone in Azure DNS ausführlich erläutert. Informationen zu anderen Eintragstypen und zum Ändern vorhandener Einträge finden Sie unter [Verwalten von DNS-Einträgen und - Ressourceneintragssätzen im Azure-Portal](dns-operations-recordsets-portal.md).

1. Klicken Sie oben im Bereich **DNS-Zone** auf **+ Datensatzgruppe**, um den Bereich **Datensatzgruppe hinzufügen** zu öffnen.

   ![Screenshot des Bereichs der DNS-Zone mit einem Pfeil, der auf die Schaltfläche „+ Datensatzgruppe“ zeigt.](./media/dns-reverse-dns-hosting/figure4.png)

1. Der Name des Datensatzes für einen PTR-Eintrag muss der restliche Teil der IPv4-Adresse in umgekehrter Reihenfolge sein. 

   In diesem Beispiel werden die ersten drei Oktette bereits als Teil des Namens der Zone (.2.0.192) eingetragen. Aus diesem Grund wird im Feld **Name** nur das letzte Oktett angegeben. Eine Datensatzgruppe für eine Ressource mit der IP-Adresse 192.0.2.15 können Sie beispielsweise **15** nennen.  
1. Wählen Sie unter **Typ** die Option **PTR** aus.  
1. Geben Sie für **DOMÄNENNAME** den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der Ressource an, die die IP-Adresse verwendet.
1. Klicken Sie im unteren Bereich auf **OK**, um den DNS-Eintrag zu erstellen.

   ![Bereich „Datensatzgruppe hinzufügen“ mit ausgefüllten Feldern](./media/dns-reverse-dns-hosting/figure5.png)

Die folgenden Beispiele zeigen, wie diese Aufgabe mit PowerShell oder mit der Azure-Befehlszeilenschnittstelle durchgeführt wird:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name 15 -RecordType PTR -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc1.contoso.com")
```
#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns record-set add-record MyResourceGroup 2.0.192.in-addr.arpa 15 PTR --ptrdname dc1.contoso.com  
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 2.0.192.in-addr.arpa -n 15 --ptrdname dc1.contoso.com
```

### <a name="ipv6"></a>IPv6

Im folgenden Beispiel werden die einzelnen Schritte zur Erstellung eines neuen PTR-Eintrags erläutert. Informationen zu anderen Eintragstypen und zum Ändern vorhandener Einträge finden Sie unter [Verwalten von DNS-Einträgen und - Ressourceneintragssätzen im Azure-Portal](dns-operations-recordsets-portal.md).

1. Klicken Sie oben im Bereich **DNS-Zone** auf **+ Datensatzgruppe**, um den Bereich **Datensatzgruppe hinzufügen** zu öffnen.

   ![Schaltfläche zum Erstellen einer Datensatzgruppe](./media/dns-reverse-dns-hosting/figure6.png)

2. Der Name des Datensatzes für einen PTR-Eintrag muss der restliche Teil der IPv6-Adresse in umgekehrter Reihenfolge sein. Er darf keine Nullkomprimierung enthalten. 

   In diesem Beispiel wurden die ersten 64 Bits der IPv6-Adresse bereits als Teil des Zonennamens aufgefüllt (0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa). Aus diesem Grund werden im Feld **Name** nur die letzten 64 Bits angegeben. Die letzten 64 Bits der IP-Adresse werden in umgekehrter Reihenfolge eingegeben, und die einzelnen hexadezimalen Zahlen werden jeweils durch einen Punkt getrennt. Eine Datensatzgruppe für eine Ressource, deren IP-Adresse 2001:0db8:abdc:0000:f524:10bc:1af9:405e lautet, kann beispielsweise wie mit **e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f** benannt werden.  
3. Wählen Sie unter **Typ** die Option **PTR** aus.  
4. Geben Sie für **DOMÄNENNAME** den FQDN der Ressource an, die die IP-Adresse verwendet.
5. Klicken Sie im unteren Bereich auf **OK**, um den DNS-Eintrag zu erstellen.

![Screenshot des Bereichs „Datensatzgruppe hinzufügen“ mit einem Pfeil, der auf den Wert im Typfeld zeigt.](./media/dns-reverse-dns-hosting/figure7.png)

Die folgenden Beispiele zeigen, wie diese Aufgabe mit PowerShell oder mit der Azure-Befehlszeilenschnittstelle durchgeführt wird:

#### <a name="powershell"></a>PowerShell

```powershell
New-AzDnsRecordSet -Name "e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f" -RecordType PTR -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "dc2.contoso.com")
```

#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns record-set add-record MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f PTR --ptrdname dc2.contoso.com 
```
 
#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns record-set ptr add-record -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -n e.5.0.4.9.f.a.1.c.b.0.1.4.2.5.f --ptrdname dc2.contoso.com
```

## <a name="view-records"></a>Anzeigen von Datensätzen

Navigieren Sie zum Anzeigen der erstellten Einträge im Azure-Portal zu Ihrer DNS-Zone. Die Einträge für die DNS-Zone werden im unteren Teil des Bereichs **DNS-Zone** angezeigt. Dort sollten Sie die standardmäßigen NS- und SOA-Einträge sowie alle neuen Einträge sehen, die Sie erstellt haben. Die NS- und SOA-Einträge werden in jeder Zone erstellt. 

### <a name="ipv4"></a>IPv4

Im Bereich **DNS-Zone** werden die IPv4-PTR-Einträge angezeigt:

![Bereich „DNS-Zone“ mit IPv4-Einträgen](./media/dns-reverse-dns-hosting/figure8.png)

Die folgenden Beispiele zeigen, wie die PTR-Einträge mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle angezeigt werden:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 2.0.192.in-addr.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns record-set list MyResourceGroup 2.0.192.in-addr.arpa
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns record-set list -g MyResourceGroup -z 2.0.192.in-addr.arpa
```

### <a name="ipv6"></a>IPv6

Im Bereich **DNS-Zone** werden die IPv6-PTR-Einträge angezeigt:

![Bereich „DNS-Zone“ mit IPv6-Einträgen](./media/dns-reverse-dns-hosting/figure9.png)

Die folgenden Beispiele zeigen, wie die Einträge mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle angezeigt werden:

#### <a name="powershell"></a>PowerShell

```powershell
Get-AzDnsRecordSet -ZoneName 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa -ResourceGroupName MyResourceGroup
```

#### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle

```azurecli
azure network dns record-set list MyResourceGroup 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network dns record-set list -g MyResourceGroup -z 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa
```

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="can-i-host-reverse-dns-lookup-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Kann ich Reverse-DNS-Lookupzonen für meine vom ISP zugewiesenen IP-Adressblöcke in Azure DNS hosten?

Ja. Das Hosten der Reverse-Lookupzonen (ARPA) für eigene IP-Adressbereiche in Azure DNS wird vollständig unterstützt.

Erstellen Sie die Reverse-Lookupzone in Azure DNS, wie in diesem Artikel erläutert, und [delegieren Sie die Zone](dns-domain-delegation.md) dann in Zusammenarbeit mit Ihrem Internetdienstanbieter. Sie können dann die PTR-Einträge für jedes Reverse-Lookup auf dieselbe Weise wie andere Eintragstypen verwalten.

### <a name="how-much-does-hosting-my-reverse-dns-lookup-zone-cost"></a>Wie viel kostet das Hosten meiner Reverse-DNS-Lookupzone?

Das Hosten der Reverse-DNS-Lookupzone für Ihren vom ISP zugewiesenen IP-Adressblock in Azure DNS wird mit den [Standardsätzen für Azure DNS](https://azure.microsoft.com/pricing/details/dns/) berechnet.

### <a name="can-i-host-reverse-dns-lookup-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Kann ich Reverse-DNS-Lookupzonen für sowohl IPv4- als auch IPv6-Adressen in Azure DNS hosten?

Ja. In diesem Artikel wird erläutert, wie Reverse-DNS-Lookupzonen für sowohl IPv4 als auch IPv6 in Azure DNS erstellt werden.

### <a name="can-i-import-an-existing-reverse-dns-lookup-zone"></a>Kann ich eine vorhandene Reverse-DNS-Lookupzone importieren?

Ja. Vorhandene DNS-Zonen können über die Azure-Befehlszeilenschnittstelle in Azure DNS importiert werden. Diese Methode kann sowohl für Forward-Lookupzonen als auch für Reverse-Lookupzonen verwendet werden.

Weitere Informationen finden Sie unter [Importieren und Exportieren einer DNS-Zonendatei mit Azure CLI 1.0](dns-import-export.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Reverse-DNS finden Sie unter [Reverse DNS](https://en.wikipedia.org/wiki/Reverse_DNS_lookup) in der Wikipedia.
<br>
Erfahren Sie, wie Sie [Reverse-DNS-Einträge für Ihre Azure-Dienste verwalten](dns-reverse-dns-for-azure-services.md).
