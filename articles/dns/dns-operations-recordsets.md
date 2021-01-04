---
title: Verwalten von DNS-Einträgen in Azure DNS mithilfe von Azure PowerShell | Microsoft Docs
description: Verwalten von DNS-Datensatzgruppen und Einträgen in Azure DNS, wenn Sie Ihre Domäne in Azure DNS hosten. Alle PowerShell-Befehle für Vorgänge für Datensatzgruppen und Einträge.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011590"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Verwalten von DNS-Einträgen und -Ressourceneintragssätzen in Azure DNS über Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure-Portal](dns-operations-recordsets-portal.md)
> * [Klassische Azure-Befehlszeilenschnittstelle](./dns-operations-recordsets-cli.md)
> * [Azure-Befehlszeilenschnittstelle](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell DNS-Einträge für Ihre DNS-Zone verwalten. DNS-Einträge können auch mithilfe der plattformübergreifenden [Azure-Befehlszeilenschnittstelle](dns-operations-recordsets-cli.md) oder über das [Azure-Portal](dns-operations-recordsets-portal.md) verwaltet werden.

Bei den Beispielen in diesem Artikel wird vorausgesetzt, dass Sie bereits [Azure PowerShell installiert haben, angemeldet sind und eine DNS-Zone erstellt haben](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Einführung

Bevor Sie DNS-Einträge in Azure DNS erstellen, müssen Sie zunächst verstehen, wie DNS-Einträge von Azure DNS in DNS-Ressourceneintragssätzen organisiert werden.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Weitere Informationen zu DNS-Einträgen in Azure DNS finden Sie unter [DNS-Zonen und -Einträge](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Erstellen eines neuen DNS-Eintrags

Wenn Ihr neuer Eintrag den gleichen Namen und Typ besitzt wie ein bereits vorhandener Eintrag, müssen Sie ihn [dem vorhandenen Ressourceneintragssatz](#add-a-record-to-an-existing-record-set) hinzufügen. Besitzt Ihr neuer Eintrag einen anderen Namen und Typ als alle bereits vorhandenen Einträge, müssen Sie einen neuen Ressourceneintragssatz erstellen. 

### <a name="create-a-records-in-a-new-record-set"></a>Erstellen von A-Einträgen in einer neuen Datensatzgruppe

Sie erstellen Ressourceneintragssätze mit dem Cmdlet `New-AzDnsRecordSet`. Beim Erstellen eines Ressourceneintragssatzes müssen Sie den Namen des Ressourceneintragssatzes, die Zone, die Gültigkeitsdauer (TTL), den Eintragstyp und die zu erstellenden Einträge angeben.

Die Parameter zum Hinzufügen von Einträgen zu einer Datensatzgruppe variieren je nach Typ der Datensatzgruppe. Wenn Sie beispielsweise einen Ressourceneintragssatz vom Typ „A“ verwenden, müssen Sie die IP-Adresse mithilfe des Parameters `-IPv4Address` angeben. Für andere Eintragstypen werden andere Parameter verwendet. Ausführlichere Informationen finden Sie unter „Zusätzliche Beispiele für Datensatztypen“.

Im folgenden Beispiel wird ein Ressourceneintragssatz mit dem relativen Namen „www“ in der DNS-Zone „contoso.com“ erstellt. Der vollqualifizierte Name des Ressourceneintragssatzes ist „www.contoso.com“. Der Eintragstyp ist „A“, und die Gültigkeitsdauer beträgt 3.600 Sekunden. Der Ressourceneintragssatz enthält einen einzelnen Eintrag (mit der IP-Adresse 1.2.3.4).

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Zum Erstellen eines Ressourceneintragssatzes auf oberster Ebene der Zone (in diesem Fall „contoso.com“) verwenden Sie den Namen des Ressourceneintragssatzes „\@“ (ohne Anführungszeichen):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Wenn Sie einen Eintragssatz mit mehreren Einträgen erstellen möchten, erstellen Sie zunächst ein lokales Array, fügen Sie die Einträge hinzu, und übergeben Sie das Array anschließend wie folgt an `New-AzDnsRecordSet`:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

Mithilfe von [Metadaten für den Eintragssatz](dns-zones-records.md#tags-and-metadata) können den einzelnen Eintragssätzen anwendungsspezifische Daten als Schlüssel-Wert-Paare zugeordnet werden. Das folgende Beispiel zeigt, wie Sie einen Ressourceneintragssatz mit zwei Metadateneinträgen („dept=finance“ und „environment=production“) erstellen.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS unterstützt auch leere Ressourceneintragssätze. Diese können als Platzhalter verwendet werden, um vor der Erstellung von DNS-Einträgen einen DNS-Namen zu reservieren. Leere Ressourceneintragssätze sind auf der Steuerungsebene von Azure DNS sichtbar, erscheinen aber auf den Azure DNS-Namenservern. Im folgenden Beispiel wird ein leerer Ressourceneintragssatz erstellt:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Erstellen anderer Eintragstypen

Nach der ausführlichen Erstellungsanleitung für A-Einträge erfahren Sie in den folgenden Beispielen, wie Sie andere von Azure DNS unterstützte Eintragstypen erstellen.

In den einzelnen Fällen wird jeweils gezeigt, wie Sie einen Eintragssatz mit einem einzelnen Eintrag erstellen. Die vorherigen Beispiele für A-Einträge können für die Erstellung von Ressourceneintragssätzen mit mehreren Einträgen (mit Metadaten) oder für die Erstellung leerer Ressourceneintragssätze angepasst werden.

Die Erstellung eines SOA-Ressourceneintragssatzes wird hier nicht gezeigt, da SOAs zusammen mit der jeweiligen DNS-Zone erstellt und gelöscht werden und nicht separat erstellt oder gelöscht werden können. Der SOA-Eintrag kann jedoch geändert werden. Dies wird in einem [späteren Beispiel](#to-modify-an-soa-record) gezeigt.

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Erstellen einer AAAA-Datensatzgruppe mit einem einzelnen Datensatz

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Erstellen einer CAA-Datensatzgruppe mit einem einzelnen Datensatz

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Erstellen einer CNAME-Datensatzgruppe mit einem einzelnen Datensatz

> [!NOTE]
> Aufgrund der DNS-Standards sind auf der obersten Ebene einer Zone (`-Name '@'`) keine CNAME-Einträge und keine Ressourceneintragssätze mit mehreren Einträgen zulässig.
> 
> Weitere Informationen finden Sie unter [CNAME-Einträge](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Erstellen einer MX-Datensatzgruppe mit einem einzelnen Datensatz

In diesem Beispiel verwenden wir den Namen des Eintragssatzes „\@“ zum Erstellen des MX-Eintrags auf oberster Ebene der Zone (in diesem Fall „contoso.com“).


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Erstellen einer NS-Datensatzgruppe mit einem einzelnen Datensatz

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Erstellen eines PTR-Eintragssatzes mit einem einzelnen Eintrag

In diesem Fall ist „my-arpa-zone.com“ die ARPA-Zone für Reverse-Lookup, die Ihren IP-Adressbereich darstellt. Jeder PTR-Eintragssatz in dieser Zone entspricht einer IP-Adresse innerhalb dieses IP-Adressbereichs. Der Eintragsname „10“ ist das letzte Oktett der IP-Adresse innerhalb dieses IP-Adressbereichs, der durch diesen Eintrag dargestellt wird.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Erstellen einer SRV-Datensatzgruppe mit einem einzelnen Datensatz

Geben Sie beim Erstellen eines [SRV-Ressourceneintragssatzes](dns-zones-records.md#srv-records) den *\_Dienst* und das *\_Protokoll* im Namen des Ressourceneintragssatzes an. Wenn Sie einen SRV-Ressourceneintragssatz auf der obersten Ebene der Zone erstellen, muss „\@“ nicht in den Namen des Ressourceneintragssatzes eingeschlossen werden.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Erstellen eines TXT-Ressourceneintragssatzes mit einem einzelnen Datensatz

Das folgende Beispiel zeigt die Erstellung eines TXT-Eintrags. Weitere Informationen zur maximal unterstützten Zeichenfolgenlänge in TXT-Einträgen finden Sie unter [TXT-Einträge](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Abrufen einer Datensatzgruppe

Verwenden Sie `Get-AzDnsRecordSet`zum Abrufen eines vorhandenen Ressourceneintragssatzes. Dieses Cmdlet gibt ein lokales Objekt zurück, das den Ressourceneintragssatz in Azure DNS darstellt.

Wie bei `New-AzDnsRecordSet` muss der Name des Ressourceneintragssatzes ein *relativer* Name (also ein Name ohne Zonenname) sein. Außerdem müssen der Eintragstyp und die Zone angegeben werden, die den Ressourceneintragssatz enthält.

Das folgende Beispiel zeigt, wie Sie einen Ressourceneintragssatz abrufen. In diesem Beispiel wird die Zone mithilfe der Parameter `-ZoneName` und `-ResourceGroupName` angegeben.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativ können Sie die Zone auch unter Verwendung eines Zonenobjekts angeben, das mithilfe des Parameters `-Zone` übergeben wird.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Auflisten von Datensatzgruppen

Mit `Get-AzDnsZone` können Sie auch eine Liste mit Ressourceneintragssätzen in einer Zone anzeigen. Lassen Sie dazu den Parameter `-Name` und/oder den Parameter `-RecordType` weg.

Das folgende Beispiel gibt alle Ressourceneintragssätze in der Zone zurück:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Das folgende Beispiel zeigt, wie Sie alle Ressourceneintragssätze eines bestimmten Typs abrufen können, indem Sie den Eintragstyp angeben und den Namen des Ressourceneintragssatzes weglassen:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Wenn Sie alle Ressourceneintragssätze mit einem bestimmten Namen (unabhängig vom Eintragstyp) abrufen möchten, müssen Sie zunächst alle Ressourceneintragssätze abrufen und die Ergebnisse anschließend filtern:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

In den Beispielen oben kann die Zone entweder mit den Parametern `-ZoneName` und `-ResourceGroupName` (wie gezeigt) oder mithilfe eines Zonenobjekts angegeben werden:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Hinzufügen eines Eintrags zu einem vorhandenen Ressourceneintragssatz

Führen Sie die folgenden drei Schritte aus, um einem vorhandenen Ressourceneintragssatz einen Eintrag hinzuzufügen:

1. Abrufen des vorhandenen Ressourceneintragssatzes

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Hinzufügen des neuen Eintrags zum lokalen Ressourceneintragssatz. Hierbei handelt es sich um einen Offlinevorgang.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Committen der Änderung an den Azure DNS-Dienst. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Mit `Set-AzDnsRecordSet` werden der vorhandene Ressourceneintragssatz in Azure DNS und alle darin enthaltenen Einträge durch den angegebenen Ressourceneintragssatz *ersetzt*. Mithilfe von [ETag-Überprüfungen](dns-zones-records.md#etags) wird die Überschreibung gleichzeitiger Änderungen verhindert. Diese Überprüfungen können mithilfe des optionalen Switchs `-Overwrite` unterdrückt werden.

Diese Vorgangssequenz kann auch *weitergeleitet* werden. Das bedeutet, Sie übergeben das Ressourceneintragssatz-Objekt nicht als Parameter, sondern verwenden stattdessen die Pipe:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Die obigen Beispiele zeigen, wie einem vorhandenen Ressourceneintragssatz vom Typ „A“ ein A-Eintrag hinzugefügt wird. Eine ähnliche Vorgangssequenz wird verwendet, um Ressourceneintragssätzen eines anderen Typs Einträge hinzuzufügen. Hierbei wird dann der `-Ipv4Address`-Parameter von `Add-AzDnsRecordConfig` durch andere (eintragstypspezifische) Parameter ersetzt. Die Parameter für den jeweiligen Eintragstyp entsprechen den Parametern für das `New-AzDnsRecordConfig`-Cmdlet (siehe „Zusätzliche Beispiele für Datensatztypen“ weiter oben).

Ressourceneintragssätze vom Typ „CNAME“ oder „SOA“ können maximal einen Eintrag enthalten. Diese Einschränkung ergibt sich aus den DNS-Standards. Sie ist nicht durch Azure DNS bedingt.

## <a name="remove-a-record-from-an-existing-record-set"></a>Entfernen eines Eintrags aus einer vorhandenen Datensatzgruppe

Der Prozess zum Entfernen eines Eintrags aus einem Ressourceneintragssatz ähnelt dem Prozess zum Hinzufügen eines Eintrags zu einem Ressourceneintragssatz:

1. Abrufen des vorhandenen Ressourceneintragssatzes

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Entfernen des Eintrags aus dem lokalen Ressourceneintragssatz-Objekt. Hierbei handelt es sich um einen Offlinevorgang. Der entfernte Eintrag muss für alle Parameter exakt mit einem vorhandenen Eintrag übereinstimmen.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Committen der Änderung an den Azure DNS-Dienst. Verwenden Sie den optionalen Switch `-Overwrite`, um [ETag-Überprüfungen](dns-zones-records.md#etags) für gleichzeitige Änderungen zu unterdrücken.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Wenn Sie mithilfe der obigen Vorgehensweise den letzten Eintrag aus einem Ressourceneintragssatz entfernen, wird der Ressourceneintragssatz dadurch nicht gelöscht, sondern bleibt als leerer Ressourceneintragssatz bestehen. Informationen zum vollständigen Entfernen eines Ressourceneintragssatzes finden Sie unter [Löschen eines Ressourceneintragssatzes](#delete-a-record-set).

Ähnlich wie beim Hinzufügen von Einträgen zu einem Ressourceneintragssatz kann auch die Vorgangssequenz zum Entfernen eines Ressourceneintragssatzes weitergeleitet werden:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Zur Unterstützung verschiedener Eintragstypen müssen jeweils die passenden typspezifischen Parameter an `Remove-AzDnsRecordSet` übergeben werden. Die Parameter für den jeweiligen Eintragstyp entsprechen den Parametern für das `New-AzDnsRecordConfig`-Cmdlet (siehe „Zusätzliche Beispiele für Datensatztypen“ weiter oben).


## <a name="modify-an-existing-record-set"></a>Ändern eines vorhandenen Ressourceneintragssatzes

Die Schritte zum Ändern eines vorhandenen Ressourceneintragssatzes ähneln den Schritten zum Hinzufügen oder Entfernen von Einträgen:

1. Verwenden Sie `Get-AzDnsRecordSet`zum Abrufen des vorhandenen Ressourceneintragssatzes.
2. Ändern Sie das lokale Ressourceneintragssatz-Objekt mittels folgender Schritte:
    * Hinzufügen oder Entfernen von Einträgen
    * Ändern der Parameter vorhandener Einträge
    * Ändern der Metadaten und der Gültigkeitsdauer (Time-to-Live, TTL) des Ressourceneintragssatzes
3. Übertragen Sie die Änderungen mit dem Cmdlet `Set-AzDnsRecordSet` . Dadurch wird der vorhandene Ressourceneintragssatz in Azure DNS durch den angegebenen Ressourceneintragssatz *ersetzt*.

Bei Verwendung von `Set-AzDnsRecordSet` wird mithilfe von [ETag-Überprüfungen](dns-zones-records.md#etags) die Überschreibung gleichzeitiger Änderungen verhindert. Diese Überprüfungen können mithilfe des optionalen Switchs `-Overwrite` unterdrückt werden.

### <a name="to-update-a-record-in-an-existing-record-set"></a>So aktualisieren Sie einen Eintrag in einem vorhandenen Ressourceneintragssatz

In diesem Beispiel wird die IP-Adresse eines vorhandenen A-Eintrags geändert:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>So ändern Sie einen SOA-Eintrag

Sie können dem automatisch erstellten SOA-Ressourceneintragssatz auf der obersten Ebene der Zone (`-Name "@"`, einschließlich Anführungszeichen) weder Einträge hinzufügen noch Einträge daraus entfernen. Allerdings können Sie jeden Parameter im SOA-Eintrag (mit Ausnahme von „Host“) und die Gültigkeitsdauer des Ressourceneintragssatzes ändern.

Im folgenden Beispiel wird veranschaulicht, wie Sie die Eigenschaft *Email* des SOA-Eintrags ändern:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>So ändern Sie NS-Einträge an der Zonenspitze

Der für die Zonenspitze festgelegte NS-Eintrag wird für jede DNS-Zone automatisch erstellt. Er enthält die Namen der Azure DNS-Namenserver, die der Zone zugewiesen sind.

Sie können diesem NS-Eintragssatz weitere Namenserver hinzufügen, um das gemeinsame Hosten von Domänen mit mehr als einem DNS-Anbieter zu unterstützen. Sie können auch die Gültigkeitsdauer und die Metadaten für diesen Datensatz ändern. Es ist aber nicht möglich, die vorab mit Daten aufgefüllten Azure-DNS-Namensserver zu entfernen oder zu ändern.

Beachten Sie, dass dies nur für den NS-Eintragssatz der Zonenspitze gilt. Andere NS-Eintragssätze in Ihrer Zone (zur Delegierung von untergeordneten Zonen) können ohne Einschränkungen geändert werden.

Im folgenden Beispiel wird gezeigt, wie Sie dem NS-Eintragssatz der Zonenspitze einen zusätzlichen Namenserver hinzufügen:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>So ändern Sie die Metadaten des Ressourceneintragssatzes

Mithilfe von [Metadaten für den Eintragssatz](dns-zones-records.md#tags-and-metadata) können den einzelnen Eintragssätzen anwendungsspezifische Daten als Schlüssel-Wert-Paare zugeordnet werden.

Das folgende Beispiel zeigt, wie Sie die Metadaten eines vorhandenen Ressourceneintragssatzes ändern:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Löschen eines Eintragssatzes

Ressourceneintragssätze können mit dem Cmdlet `Remove-AzDnsRecordSet` gelöscht werden. Beim Löschen eines Ressourceneintragssatzes werden auch alle darin enthaltenen Einträge gelöscht.

> [!NOTE]
> Die SOA- und NS-Ressourceneintragssätze auf der obersten Ebene der Zone (`-Name '@'`) können nicht gelöscht werden.  Diese wurden von Azure DNS automatisch erstellt, als die Zone erstellt wurde. Sie werden automatisch gelöscht, wenn die Zone gelöscht wird.

Das folgende Beispiel zeigt die Vorgehensweise zum Löschen eines Ressourceneintragssatzes. In diesem Beispiel werden Name und Typ des Ressourceneintragssatzes sowie der Zonenname und die Ressourcengruppe jeweils explizit angegeben.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativ kann der Ressourceneintragssatz mit Name und Typ und die Zone mithilfe eines Objekts angegeben werden:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Als dritte Möglichkeit kann der Ressourceneintragssatz unter Verwendung eines Ressourceneintragssatz-Objekts angegeben werden:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Wenn Sie den zu löschenden Ressourceneintragssatz mithilfe eines Ressourceneintragssatz-Objekts angeben, kann durch [ETag-Überprüfungen](dns-zones-records.md#etags) sichergestellt werden, dass gleichzeitige Änderungen nicht gelöscht werden. Diese Überprüfungen können mithilfe des optionalen Switchs `-Overwrite` unterdrückt werden.

Das Datensatzgruppenobjekt kann auch weitergeleitet werden, anstatt als Parameter übergeben zu werden:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Bestätigungsaufforderungen

Die Cmdlets `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` und `Remove-AzDnsRecordSet` unterstützen jeweils Bestätigungsaufforderungen.

Jedes Cmdlet fordert eine Bestätigung an, wenn die PowerShell-Einstellungsvariable `$ConfirmPreference` einen Wert von `Medium` oder weniger hat. Da `$ConfirmPreference` standardmäßig auf den Wert `High` festgelegt ist, werden diese Aufforderungen bei Verwendung der PowerShell-Standardeinstellungen nicht angezeigt.

Die aktuelle Einstellung für `$ConfirmPreference` kann mithilfe des `-Confirm`-Parameters überschrieben werden. Bei Angabe von `-Confirm` oder `-Confirm:$True` fordert das Cmdlet vor der Ausführung eine Bestätigung an. Bei Verwendung von `-Confirm:$False` fordert das Cmdlet keine Bestätigung an. 

Weitere Informationen zu `-Confirm` und `$ConfirmPreference` finden Sie unter [About Preference Variables](/powershell/module/microsoft.powershell.core/about/about_preference_variables) (Informationen zu Einstellungsvariablen).

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Zonen und Einträge in Azure DNS](dns-zones-records.md).
<br>
Lernen Sie, wie Sie bei Verwendung von Azure DNS [Ihre Zonen und Einträge schützen](dns-protect-zones-recordsets.md).
<br>
Machen Sie sich mit der [Azure DNS PowerShell-Referenzdokumentation](/powershell/module/az.dns) vertraut.