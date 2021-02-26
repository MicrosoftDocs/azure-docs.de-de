---
title: Testen des Azure-VM-Netzwerkdurchsatzes
titlesuffix: Azure Virtual Network
description: Verwenden Sie NTTTCP, um das Netzwerk gezielt zu testen und den Einsatz anderer Ressourcen, die die Leistung beeinträchtigen könnten, zu minimieren.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: 27cc55978582468c759603571bd8b0e69f29db5d
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226572"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Testen der Bandbreite / des Durchsatzes (NTTTCP)

Wenn Sie die Leistung des Netzwerkdurchsatzes in Azure testen, ist es empfehlenswert, ein Tool zu verwenden, das das Netzwerk zum Testen als Ziel hat und die Verwendung anderer Ressourcen minimiert, die die Leistung beeinflussen könnten. NTTTCP wird empfohlen.

Kopieren Sie das Tool in zwei virtuelle Azure-Computer der selben Größe. Ein virtueller Computer fungiert als SENDER und der andere als EMPFÄNGER.

#### <a name="deploying-vms-for-testing"></a>Bereitstellen von virtuellen Computern zu Testzwecken
Im Rahmen dieser Test sollten sich die beiden VMs entweder in der gleichen [Näherungsplatzierungsgruppe](../virtual-machines/co-location.md) oder in der gleichen Verfügbarkeitsgruppe befinden, sodass wir deren externe IPs verwenden und die Load Balancer aus dem Test ausschließen können. Es ist möglich, einen Test mit der VIP-Adresse durchzuführen, aber diese Art von Tests geht über den Rahmen dieses Dokuments hinaus.

Notieren Sie die IP-Adresse des EMPFÄNGERS. Nennen wir diese IP-Adresse „a.b.c.r“

Notieren Sie die Anzahl der Kerne auf dem virtuellen Computer. Nennen wir diese „\#num\_cores“

Führen Sie den NTTTCP-Test 300 Sekunden lang (oder 5 Minuten lang) auf der Sender-VM und der Empfänger-VM aus.

Tipp: Wenn Sie diesen Test zum ersten Mal einrichten, möchten Sie womöglich eine kürzere Testperiode ausprobieren, um früher Feedback einzuholen. Nachdem das Tool wie erwartet funktioniert, erweitern Sie den Testzeitraum auf 300 Sekunden, um genaueste Ergebnisse zu erhalten.

> [!NOTE]
> Der Absender **und** Empfänger muss **denselben** Parameter für die Testdauer (-t) angeben.

So testen Sie einen einzelnen TCP-Datenstrom für 10 Sekunden:

Empfängerparameter: ntttcp -r -t 10 -P 1

Senderparameter: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Das vorherige Beispiel sollte nur verwendet werden, um Ihre Konfiguration zu bestätigen. Gültige Testbeispiele werden später in diesem Dokument behandelt.

## <a name="testing-vms-running-windows"></a>Testen von VMs unter WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Rufen Sie NTTTCP auf den virtuellen Computern auf.

Herunterladen der aktuellen Version: https://github.com/microsoft/ntttcp/releases/download/v5.35/NTttcp.exe

Oder zeigen Sie die GitHub-Seite oberster Ebene an: <https://github.com/microsoft/ntttcp>\

Erwägen Sie, NTTTCP in separaten Ordnern zu speichern, z.B. C:\\Extras

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Lassen Sie NTTTCP über die Windows-Firewall zu
Erstellen Sie auf dem EMPFÄNGER eine Zulassungsregel für die Windows-Firewall, damit der NTTTCP-Datenstrom zugelassen wird. Am einfachsten ist es, das gesamte NTTTCP-Programm nach Namen anstatt nach bestimmten eingehenden TCP-Ports zuzulassen.

Lassen Sie NTTTCP wie folgt auf der Windows-Firewall zu:

netsh advfirewall firewall add rule program=\<PATH\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Angenommen, Sie haben ntttcp.exe in den Ordner „C:\\Extras“ kopiert, dann wäre dies der Befehl: 

netsh advfirewall firewall add rule program=c:\\tools\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Ausführen von NTTTCP-Tests

Starten Sie NTTTCP beim EMPFÄNGER (**Ausführen über CMD** und nicht über PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Wenn der virtuelle Computer vier Kerne und die IP-Adresse 10.0.0.4 aufweist, würde es folgendermaßen aussehen:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Starten Sie NTTTCP auf den SENDER (**Ausführen über CMD** und nicht über PowerShell):

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

Warten Sie auf die Ergebnisse.


## <a name="testing-vms-running-linux"></a>Testen von VMs unter LINUX:

Verwenden Sie nttcp-for-linux. Diese Option ist bei <https://github.com/Microsoft/ntttcp-for-linux> verfügbar.

Führen Sie diese Befehle zum Vorbereiten von ntttcp-for-linux auf den Linux-VMs (jeweils SENDER und EMPFÄNGER) auf Ihren virtuellen Computern aus:

CentOS – Git installieren:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu – Git installieren:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Auf beiden erstellen und installieren:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Wie im Windows-Beispiel gehen wir davon aus, dass die IP des Linux-EMPFÄNGERS 10.0.0.4 ist

Starten Sie ntttcp-for-linux auf dem EMPFÄNGER:

``` bash
ntttcp -r -t 300
```

Führen Sie nun Folgendes auf dem SENDER aus:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Die Testlänge beträgt standardmäßig 60 Sekunden, wenn kein Zeitparameter angegeben ist

## <a name="testing-between-vms-running-windows-and-linux"></a>Vergleich der Tests zwischen virtuellen Computern unter Windows und Linux:

In diesen Szenarien sollten wir den Modus ohne Synchronisierung aktivieren, damit der Test ausgeführt werden kann. Dies erfolgt unter Linux mit dem **-N-Flag** und unter Windows mit dem **-ns-Flag**.

#### <a name="from-linux-to-windows"></a>Von Linux nach Windows:

Empfänger \<Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Sender \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Von Windows nach Linux:

Empfänger \<Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Sender \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Testen von Clouddienstinstanzen:
Sie müssen der Datei „ServiceDefinition.csdef“ den folgenden Abschnitt hinzufügen:
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Nächste Schritte
* [Optimieren des Netzwerkdurchsatzes für virtuelle Azure-Computer](virtual-network-optimize-network-bandwidth.md) hilft Ihnen womöglich bei Ihrem Szenario, abhängig von den Ergebnissen.
* Lesen Sie mehr über die [Zuweisung von Bandbreite zu virtuellen Computern](virtual-machine-network-throughput.md).
* Weitere Informationen finden Sie unter [Azure Virtual Network – häufig gestellte Fragen (FAQs)](virtual-networks-faq.md).