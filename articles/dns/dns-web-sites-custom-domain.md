---
title: 'Tutorial: Erstellen von benutzerdefinierten Azure DNS-Einträgen für eine Web-App'
description: In diesem Tutorial erstellen Sie mithilfe von Azure DNS benutzerdefinierte Domänen-DNS-Einträge für eine Web-App.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: 369c7dab174f0269797b10635882a6821ade8311
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952912"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Tutorial: Erstellen von DNS-Einträgen in einer benutzerdefinierten Domäne für eine Web-App 

Sie können Azure DNS konfigurieren, um eine benutzerdefinierte Domäne für Ihre Web-Apps zu hosten. Sie können beispielsweise eine Azure-Web-App erstellen und Ihre Benutzer entweder über den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) „www\.contoso.com“ oder „contoso.com“ darauf zugreifen lassen.

> [!NOTE]
> In diesem Tutorial wird „contoso.com“ als Beispiel verwendet. Ersetzen Sie Ihren eigenen Domänennamen durch „contoso.com“.

Zu diesem Zweck müssen Sie drei Einträge erstellen:

* Einen Stammeintrag des Typs „A“, der auf „contoso.com“ zeigt
* Einen TXT-Stammeintrag für die Überprüfung
* Einen Eintrag des Typs „CNAME“ für den „www“-Namen, der auf den A-Eintrag zeigt

Beachten Sie, dass Sie beim Erstellen eines A-Eintrags für eine Web-App in Azure den A-Eintrag manuell aktualisieren müssen, wenn sich die zugrunde liegende IP-Adresse für die Web-App ändert.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines A- und eines TXT-Eintrags für Ihre benutzerdefinierte Domäne
> * Erstellen eines CNAME-Eintrags für Ihre benutzerdefinierten Domäne
> * Testen der neuen Einträge
> * Hinzufügen benutzerdefinierter Hostnamen zu Ihrer Web-App
> * Testen der benutzerdefinierten Hostnamen

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Sie müssen über einen Domänennamen zum Testen verfügen, den Sie in Azure DNS hosten können. Sie müssen uneingeschränkte Kontrolle über diese Domäne haben. Das bedeutet, Sie müssen unter anderem die Namenservereinträge für die Domäne festlegen können.
* [Erstellen Sie eine App Service-App](../app-service/quickstart-html.md), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben.

* Erstellen Sie in Azure DNS eine DNS-Zone, und delegieren Sie die Zone in Ihrer Registrierungsstelle an Azure DNS.

   1. Befolgen Sie zum Erstellen einer DNS-Zone die Anweisungen unter [Erstellen einer DNS-Zone](./dns-getstarted-powershell.md).
   2. Befolgen Sie zum Delegieren Ihrer Zone an Azure DNS die Schritte unter [Delegieren von DNS-Zonen mit Azure DNS](dns-delegate-domain-azure-dns.md).

Nach dem Erstellen einer Zone und ihrer Delegierung an Azure DNS können Sie Einträge für Ihre benutzerdefinierte Domäne erstellen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-a-record-and-txt-record"></a>Erstellen eines A-Eintrags und eines TXT-Eintrags

Ein A-Eintrag wird verwendet, um der IP-Adresse einen Namen zuzuordnen. Im folgenden Beispiel wird „\@“ als A-Datensatz unter Verwendung der IPv4-Adresse Ihrer Web-App zugewiesen. „\@“ steht in der Regel für die Stammdomäne.

### <a name="get-the-ipv4-address"></a>Abrufen der IPv4-Adresse

Wählen Sie im linken Navigationsbereich der App Services-Seite im Azure-Portal die Option **Benutzerdefinierte Domänen**. 

![Menü „Benutzerdefinierte Domänen“](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Kopieren Sie auf der Seite **Benutzerdefinierte Domänen** die IPv4-Adresse der App:

![Portalnavigation zur Azure-App](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Erstellen des A-Eintrags

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Erstellen des TXT-Eintrags

App Services nutzt diesen Eintrag nur zur Konfigurationszeit, um zu bestätigen, dass sich die benutzerdefinierte Domäne in Ihrem Besitz befindet. Sie können diesen TXT-Eintrag löschen, nachdem Ihre benutzerdefinierte Domäne überprüft und in App Service konfiguriert wurde.

> [!NOTE]
> Wenn Sie den Domänennamen überprüfen, aber nicht Produktionsdatenverkehr an die Web-App weiterleiten möchten, müssen Sie nur den TXT-Eintrag für den Überprüfungsschritt angeben.  Die Überprüfung erfordert keinen A- oder CNAME-Eintrag zusätzlich zum TXT-Eintrag.

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Erstellen des CNAME-Eintrags

Wenn Ihre Domäne bereits von Azure DNS verwaltet wird (siehe [DNS-Domänendelegierung](dns-domain-delegation.md)), können Sie das folgende Beispiel verwenden, um einen CNAME-Eintrag für „contoso.azurewebsites.net“ zu erstellen:

Öffnen Sie Azure PowerShell, und erstellen Sie einen neuen CNAME-Eintrag. In diesem Beispiel wird ein Ressourceneintragssatz des Typs CNAME mit einer Gültigkeitsdauer von 600 Sekunden in der DNS-Zone „contoso.com“ mit dem Alias für die Web-App „contoso.azurewebsites.net“ erstellt.

### <a name="create-the-record"></a>Erstellen des Eintrags

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

Das folgende Beispiel ist die Antwort:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Testen der neuen Einträge

Sie können überprüfen, ob die Einträge korrekt erstellt wurden, indem Sie „www.contoso.com“ and „contoso.com“ mit nslookup abfragen, wie nachfolgend gezeigt:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Hinzufügen von benutzerdefinierten Hostnamen

Nun können Sie die benutzerdefinierten Hostnamen zur Web-App hinzufügen:

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testen der benutzerdefinierten Hostnamen

Öffnen Sie einen Webbrowser, und gehen Sie zu `http://www.<your domainname>` und `http://<you domain name>`.

> [!NOTE]
> Geben Sie unbedingt das Präfix `http://` mit an, andernfalls versucht der Browser ggf., die URL zu antizipieren.

Für beide URLs sollte die gleiche Seite angezeigt werden. Beispiel:

![Contoso-App-Dienst](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcen nicht mehr benötigen, können Sie die Ressourcengruppe **myresourcegroup** löschen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie private Azure DNS-Zonen erstellen.

> [!div class="nextstepaction"]
> [Erste Schritte mit privaten Azure DNS-Zonen mithilfe von PowerShell](private-dns-getstarted-powershell.md)