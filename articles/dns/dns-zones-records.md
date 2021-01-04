---
title: 'Übersicht über DNS-Zonen und -Einträge: Azure DNS'
description: Übersicht über die Unterstützung für das Hosten von DNS-Zonen und -Einträgen in Microsoft Azure DNS.
author: rohinkoul
ms.assetid: be4580d7-aa1b-4b6b-89a3-0991c0cda897
ms.service: dns
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: rohink
ms.openlocfilehash: 41eb15a38c97532951723f12d1ac74c90c838eb1
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968195"
---
# <a name="overview-of-dns-zones-and-records"></a>Übersicht über DNS-Zonen und -Einträge

Auf dieser Seite werden die Konzepte Domänen, DNS-Zonen und DNS-Einträge sowie Ressourceneintragssätze erläutert, und es wird erklärt, wie diese in Azure DNS unterstützt werden.

## <a name="domain-names"></a>Domänennamen

Das Domain Name System ist eine Hierarchie von Domänen. Die Hierarchie beginnt mit der Stammdomäne, deren Name einfach „ **.** “ lautet.  Darunter befinden sich Domänen der obersten Ebene, z.B. „com“, „net“, „org“, „uk“ oder „jp“.  Unterhalb davon befinden sich die Domänen der zweiten Ebene, z.B. „org.uk“ oder „co.jp“. Diese Domänen in der DNS-Hierarchie sind global verteilt und werden von DNS-Namenservern in der ganzen Welt gehostet.

Eine Domänennamen-Registrierungsstelle ist eine Organisation, von der Sie einen Domänennamen wie `contoso.com` erwerben können.  Das Erwerben eines Domänennamens gibt Ihnen das Recht, die DNS-Hierarchie unter diesem Namen zu steuern. Sie können z. B. der Website Ihrer Firma den Namen `www.contoso.com` geben. Die Registrierungsstelle kann die Domäne in Ihrem Auftrag auf ihren eigenen Namenservern hosten oder es Ihnen gestatten, alternative Namenserver anzugeben.

Azure DNS bietet eine weltweit verteilte Infrastruktur von Namenservern mit hoher Verfügbarkeit, die Sie benutzen können, um Ihre Domäne zu hosten. Durch das Hosten Ihrer Domänen in Azure DNS stehen Ihnen für die Verwaltung Ihrer DNS-Einträge die gleichen Anmeldeinformationen, APIs und Tools wie für Ihre anderen Azure-Dienste zur Verfügung. Auch Abrechnung und Support erfolgen wie bei diesen Diensten.

Azure DNS unterstützt derzeit nicht den Kauf von Domänennamen. Wenn Sie einen Domänennamen kaufen möchten, müssen Sie sich an eine von einem Drittanbieter betriebene Registrierungsstelle für Domänennamen wenden. Die Registrierungsstelle erhebt in der Regel eine geringe jährliche Gebühr. Die Domänen können dann in Azure DNS für die Verwaltung von DNS-Einträgen gehostet werden. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md) .

## <a name="dns-zones"></a>DNS-Zonen

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="dns-records"></a>DNS-Datensätze

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

### <a name="time-to-live"></a>Gültigkeitsdauer

Die Gültigkeitsdauer (Time to Live, TTL) gibt an, wie lange jeder Eintrag von den Clients zwischengespeichert wird, bevor er erneut abgefragt wird. Im obigen Beispiel ist der TTL-Wert 3600 Sekunden oder 1 Stunde.

In Azure DNS wird der TTL-Wert für den Ressourceneintragssatz und nicht für jeden Eintrag angegeben. Dadurch wird der gleiche Wert für alle Einträge in diesem Ressourceneintragssatz verwendet.  Sie können einen beliebigen TTL-Wert zwischen 1 und 2.147.483.647 Sekunden angeben.

### <a name="wildcard-records"></a>Platzhalterdatensätze

Azure DNS unterstützt [Platzhalterdatensätze](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Platzhalterdatensätze werden für alle Abfragen mit einem übereinstimmenden Namen zurückgegeben (es sei denn, es gibt eine genauere Übereinstimmung aus einem Ressourceneintragssatz ohne Platzhalter). Azure DNS unterstützt Ressourceneintragssätze mit Platzhaltern für alle Eintragstypen mit Ausnahme von NS und SOA.

Verwenden Sie zum Erstellen eines Ressourceneintragssatzes mit Platzhaltern den Ressourceneintragssatz-Namen „\*“. Alternativ können Sie einen Namen mit „\*“ als Bezeichnung an der linken Stelle verwenden, z.B. „\*.foo“.

### <a name="caa-records"></a>CAA-Einträge

Mit CAA-Einträgen können Domänenbesitzer angeben, welche Zertifizierungsstellen zum Ausstellen von Zertifikaten für ihre Domäne autorisiert sind. Dadurch können Zertifizierungsstellen ein falsches Ausstellen von Zertifikaten in einigen Fällen verhindern. CAA-Einträge weisen drei Eigenschaften auf:
* **Flags**: Dies ist eine ganze Zahl zwischen 0 und 255, mit der das kritische Flag dargestellt wird, das gemäß [RFC](https://tools.ietf.org/html/rfc6844#section-3) eine besondere Bedeutung hat.
* **Tag**: Eine ASCII-Zeichenfolge, bei der es sich um eine der folgenden Optionen handeln kann:
    * **issue**: Verwenden Sie diese Option, wenn Sie Zertifizierungsstellen angeben möchten, denen das Ausstellen von Zertifikaten (alle Typen) gestattet ist.
    * **issuewild**: Verwenden Sie diese Option, wenn Sie Zertifizierungsstellen angeben möchten, denen das Ausstellen von Zertifikaten (nur Platzhalterzertifikate) gestattet ist.
    * **iodef**: Geben Sie eine E-Mail-Adresse oder einen Hostnamen an, an die bzw. den Zertifizierungsstellen eine Benachrichtigung senden können, falls die Ausstellung eines nicht autorisierten Zertifikats angefordert wird.
* **Value**: Der Wert für das jeweils ausgewählte Tag.

### <a name="cname-records"></a>CNAME-Einträge

CNAME-Ressourceneintragssätze können nicht gleichzeitig neben anderen Ressourceneintragssätzen mit dem gleichen Namen vorhanden sein. Sie können einen CNAME-Ressourceneintragssatz z.B. nicht mit dem relativen Namen „www“ und gleichzeitig einen A-Eintrag mit dem relativen Namen „www“ erstellen.

Da der Zonen-Apex (name = „\@“) immer die NS- und SOA-Ressourceneintragssätze enthält, die beim Erstellen der Zone erstellt wurden, können Sie keinen CNAME-Ressourceneintragssatz am Zonen-Apex erstellen.

Diese Einschränkungen ergeben sich aus den DNS-Standards und sind keine Einschränkungen von Azure DNS.

### <a name="ns-records"></a>NS-Einträge

Der NS-Datensatz am Zonen-Apex (Name „\@“) wird automatisch bei jeder DNS-Zone erstellt und automatisch gelöscht, wenn die Zone gelöscht wird (er kann nicht separat gelöscht werden).

Dieser Datensatz enthält die Namen der Azure-DNS-Namensserver, die der Zone zugewiesen sind. Sie können diesem NS-Eintragssatz weitere Namenserver hinzufügen, um das gemeinsame Hosten von Domänen mit mehr als einem DNS-Anbieter zu unterstützen. Sie können auch die Gültigkeitsdauer und die Metadaten für diesen Datensatz ändern. Es ist aber nicht möglich, die vorab mit Daten aufgefüllten Azure-DNS-Namensserver zu entfernen oder zu ändern. 

Dies gilt nur für den NS-Eintragssatz des Zonen-Apex. Andere NS-Eintragssätze in Ihrer Zone (zur Delegierung von untergeordneten Zonen) können ohne Einschränkungen erstellt, geändert und gelöscht werden.

### <a name="soa-records"></a>SOA-Einträge

Ein Satz von SOA-Einträgen wird automatisch am Apex jeder Zone erstellt (name = „\@“), und wird automatisch gelöscht, wenn die Zone gelöscht wird.  SOA-Einträge können nicht separat erstellt oder gelöscht werden.

Sie können alle Eigenschaften des SOA-Eintrags ändern, bis auf die Eigenschaft „host“, die vorkonfiguriert ist, um auf den primären Namen des Namenserver zu verweisen, der von Azure DNS bereitgestellt wird.

Die Seriennummer der Zone im SOA-Eintrag wird nicht automatisch aktualisiert, wenn Änderungen an den Datensätzen in der Zone vorgenommen werden. Sie kann bei Bedarf manuell aktualisiert werden, indem Sie den SOA-Eintrag bearbeiten.

### <a name="spf-records"></a>SPF-Einträge

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="srv-records"></a>SRV-Einträge

[SRV-Einträge](https://en.wikipedia.org/wiki/SRV_record) werden von verschiedenen Diensten verwendet, um Serverstandorte anzugeben. Wenn Sie einen SRV-Eintrag in Azure DNS angeben:

* Müssen *service* und *protocol* als Teil des Namens des Eintragssatzes angegeben werden, und mit Unterstrichen vorangestellt werden.  z.B. '\_sip.\_tcp.name'.  Bei einem Eintrag am Zonen-Apex ist es nicht nötig, „\@“ im Namen des Eintrags anzugeben. Verwenden Sie einfach den Dienst und das Protokoll, z.B. „\_sip.\_tcp“.
* Werden *priority*, *weight*, *port* und *target* als Parameter jedes Eintrags im Eintragssatz angegeben.

### <a name="txt-records"></a>TXT-Einträge

TXT-Einträge werden verwendet, um Domänennamen willkürlichen Textzeichenfolgen zuzuordnen. Sie werden in mehreren Anwendungen eingesetzt, vor allem in Bezug auf die E-Mail-Konfiguration, z.B. [Sender Policy Framework (SPF)](https://en.wikipedia.org/wiki/Sender_Policy_Framework) und [DomainKeys Identified Mail (DKIM)](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail).

Gemäß den DNS-Standards kann ein einzelner TXT-Eintrag mehrere Zeichenfolgen enthalten, die jeweils eine Länge von bis zu 254 Zeichen haben können. Wenn mehrere Zeichenfolgen verwendet werden, werden sie von Clients verkettet und wie eine einzelne Zeichenfolge behandelt.

Beim Aufrufen der Azure DNS-REST-API müssen Sie jede TXT-Zeichenfolge separat angeben.  Beim Nutzen des Azure-Portals, von PowerShell oder der CLI sollten Sie nur eine Zeichenfolge pro Eintrag angeben, die bei Bedarf automatisch in Segmente mit einer Länge von 254 Zeichen unterteilt wird.

Die Zeichenfolgen eines DNS-Eintrags dürfen nicht mit den TXT-Einträgen in einem TXT-Ressourceneintragssatz verwechselt werden.  Ein TXT-Ressourceneintragssatz kann mehrere Einträge enthalten, die wiederum *jeweils* mehrere Zeichenfolgen enthalten können.  Azure DNS unterstützt für Zeichenfolgen eine Gesamtlänge von bis zu 1024 Zeichen pro TXT-Ressourceneintragssatz (Kombination aller Einträge).

## <a name="tags-and-metadata"></a>Tags und Metadaten

### <a name="tags"></a>`Tags`

Tags sind eine Liste von Name-Wert-Paaren, die von Azure Resource Manager zum Bezeichnen von Ressourcen verwendet werden.  Mithilfe von Tags ermöglicht Azure Resource Manager gefilterte Ansichten Ihrer Azure-Rechnung. Zusätzlich können Sie eine Richtlinie erstellen, die die erforderlichen Tags festlegt. Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).

Azure DNS unterstützt die Verwendung von Tags von Azure Resource Manager in DNS-Zonenressourcen.  Tags für DNS-Ressourceneintragssätze werden nicht unterstützt, aber als Alternative werden „Metadaten“ für DNS-Ressourceneintragssätze wie unten beschrieben unterstützt.

### <a name="metadata"></a>Metadaten

Azure DNS unterstützt das Hinzufügen von Ressourceneintragssätzen mithilfe von „Metadaten“ als Alternative zu Tags in Ressourceneintragssätzen.  Ähnlich wie Tags ermöglichen Metadaten das Zuordnen von Name-Wert-Paare zu jedem Ressourceneintragssatz.  Dies kann hilfreich sein, um z. B. den Zweck jedes Ressourceneintragssatzes zu dokumentieren.  Anders als Tags können Metadaten nicht verwendet werden, um Ihre Azure-Rechnung zu filtern, und sie können nicht in einer Richtlinie von Azure Resource Manager angegeben werden.

## <a name="etags"></a>Etags

Angenommen, zwei Personen oder zwei Prozesse versuchen, einen DNS-Eintrag zur gleichen Zeit zu ändern. Welcher hat Vorrang? Und weiß die Person, die Vorrang erhalten hat, dass sie die Änderungen einer anderen Person überschrieben hat?

Azure DNS verwendet ETags, um gleichzeitige Änderungen an derselben Ressource sicher zu handhaben. ETags unterscheiden sich von [Azure Resource Manager-„Tags“](#tags). Jeder DNS-Ressourcen (Zone oder Datensatzgruppe) ist ein ETag zugeordnet. Sobald eine Ressource abgerufen wird, wird auch sein ETag abgerufen. Beim Aktualisieren einer Ressource können Sie auswählen, dass die ETags wieder zurückgegeben werden sollen, damit Azure DNS überprüfen kann, ob das ETag auf dem Server übereinstimmt. Da jedes Update einer Ressource dazu führt, dass das ETag erneut generiert wird, weist eine Nichtübereinstimmung des ETags darauf hin, dass eine gleichzeitige Änderung vorgenommen wurde. ETags können auch beim Erstellen einer neuen Ressource verwendet werden, um sicherzustellen, dass die Ressource nicht bereits vorhanden ist.

Standardmäßig verwendet Azure DNS PowerShell ETags, um gleichzeitige Änderungen an Zonen und Datensatzgruppen zu blockieren. Der optionale Switch *-Overwrite* kann verwendet werden, um ETag-Überprüfungen zu unterdrücken. In diesem Fall werden gleichzeitig vorgenommene Änderungen überschrieben.

Auf der Ebene der REST-API von Azure DNS werden ETags mithilfe von HTTP-Headern angegeben.  Ihr Verhalten ist in der folgenden Tabelle angegeben:

| Header | Verhalten |
| --- | --- |
| Keine |PUT ist immer erfolgreich (keine Etag-Prüfung) |
| If-match \<etag> |PUT ist nur erfolgreich, wenn die Ressource vorhanden ist und das Etag übereinstimmt. |
| If-match * |PUT ist nur erfolgreich, wenn eine Ressource vorhanden ist. |
| If-none-match * |PUT ist nur erfolgreich, wenn die Ressource nicht vorhanden ist. |


## <a name="limits"></a>Einschränkungen

Bei der Verwendung von Azure DNS gelten folgende Standardgrenzwerte:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

* Lernen Sie, wie Sie [eine DNS-Zone erstellen](./dns-getstarted-portal.md) und [DNS-Einträge erstellen](./dns-getstarted-portal.md), um mit der Verwendung von Azure DNS zu beginnen.
* Um eine vorhandene DNS-Zone zu migrieren, sollten Sie sich zuerst darüber informieren, wie Sie [eine DNS-Zonendatei importieren und exportieren](dns-import-export.md).