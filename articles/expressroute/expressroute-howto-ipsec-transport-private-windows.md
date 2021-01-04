---
title: 'Privates Azure ExpressRoute-Peering: Konfigurieren des IPsec-Transportmodus – Windows-Hosts'
description: Vorgehensweise zum Aktivieren des IPsec-Transportmodus zwischen Windows Azure-VMs und lokalen Windows-Hosts über privates ExpressRoute-Peering mit Gruppenrichtlinienobjekten und Organisationseinheiten.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/17/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 2dcb8489d94b9afc3ae4df829b37dd9785383d85
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92208242"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Konfigurieren des IPsec-Transportmodus für privates ExpressRoute-Peering

In diesem Artikel ist beschrieben, wie Sie IPsec-Tunnel im Transportmodus über privates ExpressRoute-Peering zwischen Azure-VMs unter Windows und lokalen Windows-Hosts erstellen. In den Schritten in diesem Artikel wird diese Konfiguration mit Gruppenrichtlinienobjekten erstellt. Grundsätzlich ist es möglich, diese Konfiguration zu erstellen, ohne Organisationseinheiten (OEs) und Gruppenrichtlinienobjekte (GROs) zu verwenden. Die Kombination von Organisationseinheiten und Gruppenrichtlinienobjekten vereinfacht aber die Verwaltung Ihrer Sicherheitsrichtlinien und ermöglicht Ihnen schnelles Hochskalieren. Für die Schritte in diesem Artikel wird davon ausgegangen, dass Sie bereits eine Active Directory-Konfiguration haben und mit der Verwendung von Organisationseinheiten und Gruppenrichtlinienobjekten vertraut sind.

## <a name="about-this-configuration"></a>Informationen zu dieser Konfiguration.

Für die Konfiguration in den folgenden Schritten ein einzelnes virtuelles Azure-Netzwerk (Azure-VNet) mit privatem ExpressRoute-Peering verwendet. Diese Konfiguration kann jedoch mehrere Azure-VNets und lokale Netzwerke umfassen. In diesem Artikel ist erläutert, wie eine IPsec-Verschlüsselungsrichtlinie definiert und wie diese auf eine Gruppe von virtuellen Azure-Computern und auf lokale Hosts angewendet wird, die zur selben Organisationseinheit gehören. Sie konfigurieren die Verschlüsselung zwischen den virtuellen Azure-Computern (vm1 und vm2) und dem lokalen „host1“ nur für HTTP-Datenverkehr mit dem Zielport 8080. Je nach Ihren Anforderungen können verschiedene Arten von IPsec-Richtlinien erstellt werden.

### <a name="working-with-ous"></a>Arbeiten mit Organisationseinheiten 

Die Sicherheitsrichtlinie, die mit einer Organisationseinheit verknüpft ist, wird über ein Gruppenrichtlinienobjekt an die Computern gesendet. Einige der Vorteile, die ein Verwenden von Organisationseinheiten gegenüber dem Anwenden von Richtlinien auf einen einzelnen Host bietet, sind:

* Durch Verknüpfen einer Richtlinie mit einer Organisationseinheit wird sichergestellt, dass Computer, die zur selben Organisationseinheit gehören, dieselben Richtlinien erhalten.
* Wird die Sicherheitsrichtlinie, die mit der Organisationseinheit verknüpft ist, geändert, werden die Änderungen auf alle Hosts in der Organisationseinheit angewendet.

### <a name="diagrams"></a>Diagramme

Das folgende Diagramm zeigt die Vernetzung und den zugewiesenen IP-Adressraum. Auf den Azure-VMs und auf dem lokalen Host wird Windows 2016 ausgeführt. Die Azure-VMs und der lokale „host1“ gehören zur selben Domäne. Auf den Azure-VMs und auf den lokalen Hosts können Namen ordnungsgemäß mit DNS aufgelöst werden.

[![1]][1]

Dieses Diagramm zeigt die IPsec-Tunnel in der Übertragung im privaten ExpressRoute-Peering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Arbeiten mit einer IPsec-Richtlinie

In Windows ist Verschlüsselung mit einer IPsec-Richtlinie verknüpft. In einer IPsec-Richtlinie wird bestimmt, welcher IP-Datenverkehr geschützt wird, und wird der Sicherheitsmechanismus bestimmt, der auf die IP-Pakete angewendet wird.
**IPSec-Richtlinien** bestehen aus den folgenden Elementen: **Filterlisten**, **Filteraktionen** und **Sicherheitsregeln**.

Für das Konfigurieren einer IPsec-Richtlinie muss die Bedeutung der folgenden IPsec-Richtlinienbegriffe klar sein:

* **IPSec-Richtlinien:** Eine Sammlung von Regeln. Es kann immer nur jeweils genau eine Richtlinie aktiv („zugewiesen“) sein. Jede Richtlinie kann mehrere Regeln haben, die alle gleichzeitig aktiv sein können. Einem Computer kann immer nur jeweils genau eine aktive IPsec-Richtlinie zugewiesen sein. In einer IPsec-Richtlinie können aber mehrere Aktionen definiert sein, die in verschiedenen Situationen ausgeführt werden können. Jede Gruppe von IPsec-Regeln ist mit einer Filterliste verknüpft, die den Typ des Netzwerkdatenverkehrs betrifft, für den die jeweilige Regel gilt.

* **Filterliste:** Eine Filterliste ist eine Zusammenstellung eines oder mehrerer Filter. Eine Liste kann mehrere Filter enthalten. In einem Filter wird definiert, ob Kommunikation je nach IP-Adressbereichen, Protokollen oder sogar bestimmten Protokollports zugelassen, geschützt oder blockiert wird. In jedem Filter wird ein bestimmter Satz von Bedingungen abgeglichen, z. B. Pakete, die aus einem bestimmten Subnetz an einen bestimmten Computer hinter einem bestimmten Zielport gesendet wurden. Wenn die Netzwerkbedingungen mindestens einem dieser Filter entsprechen, wird die Filterliste aktiviert. Jeder Filter ist in einer bestimmten Filterliste definiert. Ein Filter kann nicht zu mehreren Filterlisten gehören. Eine Filterliste kann aber in mehrere IPsec-Richtlinien integriert werden. 

* **Filteraktion:** In einer Sicherheitsmethode sind Sicherheitsalgorithmen, Protokolle und Schlüssel definiert, die ein Computer bei einer Internetschlüsselaustausch-Aushandlung bereitstellt. Eine Filteraktion ist ein Liste von Sicherheitsmethoden, die in der Reihenfolge ihrer Priorität geordnet sind.  Wenn ein Computer eine IPsec-Sitzung aushandelt, akzeptiert oder sendet er Vorschläge auf Basis der Sicherheitseinstellung, die in der entsprechenden Filteraktionenliste gespeichert ist.

* **Sicherheitsregel:** In einer Regel wird bestimmt, wie und wann eine IPsec-Richtlinie Kommunikation schützt. In einer Regel werden eine **Filterliste** und **Filteraktionen** verwendet, um eine IPsec-Regel zu erstellen, gemäß der die jeweilige IPsec-Verbindung erstellt wird. Jede Richtlinie kann mehrere Regeln haben, die alle gleichzeitig aktiv sein können. Jede Regel enthält eine IP-Filterliste und eine Sammlung von Sicherheitsaktionen, die ausgeführt werden, wenn es eine Übereinstimmung mit dieser Filterliste gibt:
  * IP-Filteraktionen
  * Authentifizierungsmethoden
  * IP-Tunneleinstellungen
  * Verbindungstypen

[![5]][5]

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Sie benötigen eine funktionierende Active Directory-Konfiguration, die Sie dazu verwenden können, Gruppenrichtlinieneinstellungen zu implementieren. Weitere Informationen zu Gruppenrichtlinienobjekten finden Sie unter [Gruppenrichtlinienobjekte](/previous-versions/windows/desktop/Policy/group-policy-objects).

* Sie benötigen eine aktive ExpressRoute-Verbindung.
  * Weitere Informationen zum Erstellen einer ExpressRoute-Verbindung (Leitung) finden Sie unter [Erstellen und Ändern einer ExpressRoute-Verbindung mit PowerShell](expressroute-howto-circuit-arm.md). 
  * Vergewissern Sie sich, dass die Verbindung durch Ihren Konnektivitätsanbieter aktiviert ist. 
  * Vergewissern Sie sich, dass ein privates Azure-Peering für die Verbindung konfiguriert ist. Informationen zum Routing finden Sie unter [Konfigurieren des Routings](expressroute-howto-routing-arm.md) . 
  * Vergewissern Sie sich, dass ein virtuelles Netzwerk und ein virtuelles Netzwerkgateway erstellt und vollständig bereitgestellt sind. Befolgen Sie die Anweisungen zum [Erstellen eines virtuellen Netzwerkgateways für ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Für ein virtuelles Netzwerkgateway für ExpressRoute wird der Gatewaytyp „ExpressRoute“ und nicht „VPN“ verwendet.

* Das virtuelle ExpressRoute-Netzwerkgateway muss mit der ExpressRoute-Leitung verbunden werden. Weitere Informationen finden Sie unter [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md).

* Vergewissern Sie sich, dass die virtuellen Azure Windows-Computer im virtuellen Netzwerk bereitgestellt werden.

* Vergewissern Sie sich, dass Konnektivität zwischen den lokalen Hosts und den virtuellen Azure-Computern gegeben ist.

* Vergewissern Sie sich, dass die virtuellen Azure Windows-Computer und die lokalen Hosts DNS verwenden können, um Namen ordnungsgemäß aufzulösen.

### <a name="workflow"></a>Workflow

1. Erstellen Sie ein Gruppenrichtlinienobjekt, und verknüpfen Sie es mit der Organisationseinheit.
2. Definieren Sie eine IPsec-**Filteraktion**.
3. Definieren Sie eine IPsec-**Filterliste**.
4. Erstellen Sie eine IPsec-Richtlinie mit **Sicherheitsregeln**.
5. Weisen Sie der Organisationseinheit das IPsec-Gruppenrichtlinienobjekt zu.

### <a name="example-values"></a>Beispielwerte

* **Domänenname:** ipsectest.com

* **Organisationseinheit:** IPSecOU

* **Lokaler Windows-Computer:** host1

* **Azure Windows-VMs:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. Erstellen eines Gruppenrichtlinienobjekts

1. Um ein neues Gruppenrichtlinienobjekt zu erstellen, das mit einer Organisationseinheit verknüpft ist, öffnen Sie das Gruppenrichtlinienverwaltungs-Snap-In, und suchen Sie nach der Organisationseinheit, mit der das Gruppenrichtlinienobjekt verknüpft werden soll. In diesem Beispiel hat die Organisationseinheit den Namen **IPSecOU**. 

   [![9]][9]
2. Wählen Sie im Gruppenrichtlinienverwaltungs-Snap-In die Organisationseinheit aus, und klicken Sie mit der rechten Maustaste. Klicken Sie in der Dropdownliste auf **Gruppenrichtlinienobjekt hier erstellen und verknüpfen...** .

   [![10]][10]
3. Geben Sie dem Gruppenrichtlinienobjekt einen intuitiven Namen, damit Sie es später leicht finden können. Klicken Sie auf **OK**, um das Gruppenrichtlinienobjekt zu erstellen und zu verknüpfen.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Aktivieren Sie die Gruppenrichtlinienobjektverknüpfung

Damit das Gruppenrichtlinienobjekt auf die Organisationseinheit angewendet wird, muss das Gruppenrichtlinienobjekt nicht nur mit der Organisationseinheit verknüpft werden, sondern die Verknüpfung muss auch aktiviert werden.

1. Suchen Sie nach dem Gruppenrichtlinienobjekt, das Sie erstellt haben, klicken Sie mit der rechten Maustaste, und wählen Sie **Bearbeiten** in der Dropdownliste aus.
2. Damit das Gruppenrichtlinienobjekt auf die Organisationseinheit angewendet wird, wählen Sie **Verknüpfung aktiviert** aus.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. Definieren der IP-Filteraktion

1. Klicken Sie in der Dropdownliste mit der rechten Maustaste auf **IP-Sicherheitsrichtlinien in Active Directory**, und klicken Sie dann auf **IP-Filterlisten und Filteraktionen verwalten...** .

   [![15]][15]
2. Klicken Sie auf der Registerkarte **Filteraktionen verwalten** auf **Hinzufügen**.

   [![16]][16]

3. Klicken Sie auf der Seite des **Filteraktions-Assistenten für IP-Sicherheit** auf **Weiter**.

   [![17]][17]
4. Geben Sie der Filteraktion einen aussagekräftigen Namen, damit Sie sie später finden können. In diesem Beispiel wird der Filteraktion der Name **myEncryption** gegeben. Sie können auch eine Beschreibung hinzufügen. Klicken Sie auf **Weiter**.

   [![18]][18]
5. Über **Sicherheit aushandeln** können Sie das Verhalten für den Fall festlegen, dass IPsec nicht mit einem anderen Computer eingerichtet werden kann. Aktivieren Sie **Sicherheit aushandeln**, und klicken Sie dann auf **Weiter**.

   [![19]][19]
6. Aktivieren Sie auf der Seite **Kommunikation mit Computern, auf denen IPsec nicht unterstützt wird** die Option **Keine ungeschützte Kommunikation zulassen**, und klicken Sie dann auf **Weiter**.

   [![20]][20]
7. Aktivieren Sie auf der Seite **Sicherheit des IP-Datenverkehrs** die Option **Benutzerdefiniert**, und klicken Sie dann auf **Einstellungen...** .

   [![21]][21]
8. Wählen Sie auf der Seite **Einstellungen für Sicherheitsmethoden anpassen** die Option **Datenintegrität und -verschlüsselung (ESP): SHA1, 3DES** aus. Klicken Sie dann auf **OK**.

   [![22]][22]
9. Auf der Seite **Filteraktionen verwalten** können Sie sehen, dass der **myEncryption**-Filter erfolgreich hinzugefügt wurde. Klicken Sie auf **Schließen**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. Definieren einer IP-Filterliste

Erstellen Sie eine Filterliste, in der verschlüsselter HTTP-Datenverkehr mit dem Zielport 8080 angegeben ist.

1. Um zu bestimmen, welche Arten von Datenverkehr verschlüsselt werden müssen, verwenden Sie eine **IP-Filterliste**. Klicken Sie auf der Registerkarte **IP-Filterlisten verwalten** auf **Hinzufügen**, um eine neue IP-Filterliste hinzuzufügen.

   [![24]][24]
2. Geben Sie in das Feld **Name:** einen Namen für die Filterliste ein. Beispiel: **azure-onpremises-HTTP8080**. Klicken Sie anschließend auf **Hinzufügen**.

   [![25]][25]
3. Aktivieren Sie auf der Seite **IP-Filterbeschreibung und Eigenschaft "Gespiegelt"** die Option **Gespiegelt**. Die Einstellung für „Gespiegelt“ bewirkt ein Abgleichen von Paketen in beide Richtungen, wodurch bidirektionale Kommunikation ermöglicht wird. Klicken Sie dann auf **Weiter**.

   [![26]][26]
4. Wählen Sie auf der Seite **Quelle des IP-Datenverkehrs** in der Dropdownliste **Quelladresse** die Option **Eine bestimmte IP-Adresse oder ein bestimmtes Subnetz** aus. 

   [![27]][27]
5. Geben Sie die Quelladresse für den IP-Datenverkehr in **IP-Adresse oder Subnetz** an,und klicken Sie dann auf **Weiter**.

   [![28]][28]
6. Festlegen der **Zieladresse:** IP-Adresse oder Subnetz. Klicken Sie auf **Weiter**.

   [![29]][29]
7. Wählen Sie auf der Seite **IP-Protokolltyp** die Option **TCP** aus. Klicken Sie auf **Weiter**.

   [![30]][30]
8. Aktivieren Sie auf der Seite **Port des IP-Protokolls** die Optionen **Von jedem Port** und **An diesen Port**. Geben Sie **8080** in das Textfeld ein. Diese Einstellungen geben an, dass nur der HTTP-Datenverkehr am Zielport 8080 verschlüsselt wird. Klicken Sie auf **Weiter**.

   [![31]][31]
9. Zeigen Sie die IP-Filterliste an.  Die Konfiguration der IP-Filterliste **azure-onpremises-HTTP8080** bewirkt, dass Verschlüsselung für den gesamten Datenverkehr ausgelöst wird, der die folgenden Kriterien erfüllt:

   * Eine Quelladresse in Bereich 10.0.1.0/24 (Azure-Subnetz2)
   * Eine Zieladresse im Bereich 10.2.27.0/25 (lokales Subnetz)
   * TCP-Protokoll
   * Zielport 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. Bearbeiten der IP-Filterliste

Um dieselbe Art von Datenverkehr in umgekehrter Richtung (vom lokalen Host zur Azure-VM) zu verschlüsseln, benötigen Sie einen zweiten IP-Filter. Die Vorgehensweise zum Einrichten des neuen Filters ist mit der identisch, mit der Sie den ersten IP-Filter eigerichtet haben. Die einzigen Unterschiede sind das Quellsubnetz und das Zielsubnetz.

1. Um einen neuen IP-Filter zur IP-Filterliste hinzuzufügen, wählen Sie **Bearbeiten** aus.

   [![33]][33]
2. Klicken Sie auf der Seite **IP-Filterliste** auf **Hinzufügen**.

   [![34]][34]
3. Erstellen Sie einen zweiten IP-Filter mit den Einstellungen aus dem folgenden Beispiel:

   [![35]][35]
4. Nachdem Sie den zweiten IP-Filter erstellt haben, sieht die IP-Filterliste wie folgt aus:

   [![36]][36]

Ist Verschlüsselung zwischen einem lokalen Standort und einem Azure-Subnetz erforderlich, um eine Anwendung zu schützen, können Sie, statt die vorhandene IP-Filterliste zu ändern, eine neue IP-Filterliste hinzufügen. Ein Verknüpfen von zwei IP-Filterlisten mit derselben IPsec-Richtlinie bietet mehr Flexibilität, da eine bestimmte IP-Filterliste jederzeit geändert oder entfernt werden kann, ohne dass sich dies auf die anderen IP-Filterlisten auswirkt.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. Erstellen einer IPsec-Sicherheitsrichtlinie 

Erstellen Sie eine IPsec-Richtlinie mit Sicherheitsregeln.

1. Wählen Sie den mit der Organisationseinheit verknüpften Knoten **IP-Sicherheitsrichtlinien in Active Directory** aus. Mit der rechten Maustaste, und wählen **IP-Sicherheitsrichtlinie erstellen** aus.

   [![37]][37]
2. Geben Sie der Sicherheitsrichtlinie einen Namen. Beispiel: **policy-azure-onpremises**. Klicken Sie auf **Weiter**.

   [![38]][38]
3. Klicken Sie auf **Weiter**, ohne das Kontrollkästchen zu aktivieren.

   [![39]][39]
4. Aktivieren Sie das Kontrollkästchen **Eigenschaften bearbeiten**, und klicken Sie dann auf **Fertig stellen**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. Bearbeiten der IPsec-Sicherheitsrichtlinie

Fügen Sie der IPSec-Richtlinie die **IP-Filterliste** und die **Filteraktion** hinzu, die Sie zuvor konfiguriert haben.

1. Klicken Sie auf „HTTP-Richtlinie Eigenschaften“ auf der Registerkarte **Regeln** auf **Hinzufügen**.

   [![41]][41]
2. Klicken Sie auf der Seite **Willkommen** auf Weiter.

   [![42]][42]
3. Für eine Regel kann der IPsec-Modus festgelegt werden: Tunnelmodus oder Transportmodus.

   * Im Tunnelmodus wird das ursprüngliche Paket durch eine Reihe von IP-Headern gekapselt. Im Tunnelmodus werden die internen Routinginformationen geschützt, indem der IP-Header des ursprünglichen Pakets verschlüsselt wird. Der Tunnelmodus ist häufig zwischen Gateways in Site-to-Site-VPN-Szenarios implementiert. Der Tunnelmodus wird in den meisten Fällen für die End-to-End-Verschlüsselung zwischen Hosts verwendet.

   * Im Transportmodus werden nur die Nutzlast und der ESP-Nachspann verschlüsselt. Der IP-Header des ursprünglichen Pakets wird nicht verschlüsselt. Im Transportmodus werden die IP-Quelle und das IP-Ziel der Pakete nicht geändert.

   Aktivieren Sie **Diese Regel gibt keinen Tunnel an**, und klicken Sie dann auf **Weiter**.

   [![43]][43]
4. In **Netzwerktyp** wird festgelegt, welche Netzwerkverbindung mit der Sicherheitsrichtlinie verknüpft wird. Aktivieren Sie **Alle Netzwerkverbindungen**, und klicken Sie dann auf **Weiter**.

   [![44]][44]
5. Wählen Sie die IP-Filterliste, die Sie zuvor erstellt haben (**azure-onpremises-HTTP8080**), und klicken Sie dann auf **Weiter**.

   [![45]][45]
6. Wählen Sie die vorhandene Filteraktion **myEncryption** aus, die Sie zuvor erstellt haben.

   [![46]][46]
7. Windows unterstützt vier verschiedene Arten der Authentifizierung: Kerberos, Zertifikate, NTLMv2 und vorinstallierte Schlüssel. Da hier mit Hosts gearbeitet wird, die in eine Domäne eingebunden sind, aktivieren Sie **Active Directory-Standard (Kerberos V5-Protokoll)** , und klicken Sie dann auf **Weiter**.

   [![47]][47]
8. Die neue Richtlinie erstellt die Sicherheitsregel **azure-onpremises-HTTP8080**. Klicken Sie auf **OK**.

   [![48]][48]

Die IPsec-Richtlinie erfordert, dass für alle HTTP-Verbindungen am Zielport 8080 der IPsec-Transportmodus verwendet wird. Da HTTP ein Klartextprotokoll ist, wird durch Aktivieren der Sicherheitsrichtlinie sichergestellt, dass Daten verschlüsselt werden, wenn sie über das private ExpressRoute-Peering übertragen werden. Das Konfigurieren von IP-Sicherheitsrichtlinien für Active Directory ist komplizierter als das Konfigurieren der Windows-Firewall mit erweiterter Sicherheit, aber es ermöglicht eine umfangreichere Anpassung der IPSec-Verbindung.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Zuweisen des IPsec-Gruppenrichtlinienobjekts zur Organisationseinheit

1. Zeigen Sie die Richtlinie an. Die Sicherheitsgruppenrichtlinie ist definiert, aber noch nicht zugewiesen.

   [![49]][49]
2. Um die Sicherheitsgruppenrichtlinie zur Organisationseinheit **IPSecOU** zuzuweisen, klicken Sie mit der rechten Maustaste auf die Sicherheitsrichtlinie, und wählen **Zuweisen** aus.
   Die Sicherheitsgruppenrichtlinie wird jedem Computer zugewiesen, der zu der Organisationseinheit gehört.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Überprüfen der Verschlüsselung von Datenverkehr

Um das Verschlüsselungsgruppenrichtlinienobjekt zu testen, das auf die Organisationseinheit angewendet wird, installieren Sie IIS auf allen Azure-VMs und in „host1“. Jede IIS-Instanz wird so angepasst, dass sie auf HTTP-Anforderungen an Port 8080 reagiert.
Um die Verschlüsselung zu überprüfen, können Sie einen Netzwerk-Sniffer (z. B. Wireshark) auf allen Computer in der Organisationseinheit installieren.
Ein Powershell-Skript fungiert als HTTP-Client, um HTTP-Anforderungen an Port 8080 zu generieren:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
Die folgende Netzwerkerfassung zeigt die Ergebnisse für den lokalen Host „host1“ mit Anzeigefilter ESP, sodass nur der verschlüsselte Datenverkehr berücksichtigt wird:

[![51]][51]

Wenn Sie das Powershell-Skript lokal (HTTP-Client) ausführen, zeigt die Netzwerkerfassung in der Azure-VM eine ähnliche Ablaufverfolgung an.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Netzwerkdiagramm für IPsec-Transportmodus über ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "Datenverkehr, für den IPsec verwendet werden soll"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows-IPsec-Richtlinie"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Organisationseinheit in der Gruppenrichtlinie"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "Erstellen eines Gruppenrichtlinienobjekts, das mit der Organisationseinheit verknüpft ist"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "Zuweisen eines Namens zu dem Gruppenrichtlinienobjekt, das mit der Organisationseinheit verknüpft ist"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Bearbeiten des Gruppenrichtlinienobjekts"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Verwalten von IP-Filterlisten und Filteraktionen"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Hinzufügen einer Filteraktion"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Aktions-Assistent"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Filteraktionsname"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Filteraktion"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Angeben des Verhaltens, wenn eine unsichere Verbindung hergestellt wird"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "Sicherheitsmechanismus"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Angepasste Sicherheitsmethode"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "Filteraktionsliste"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Hinzufügen einer neuen IP-Filterliste"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Hinzufügen von HTTP-Datenverkehr zum IP-Filter"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "Abgleichen von Paketen in beide Richtungen"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "Auswahl das Quellsubnetzes"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Quellnetzwerk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Zielnetzwerk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protokoll"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "Quell- und Zielport"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Filterliste"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-Filterliste mit HTTP-Datenverkehr"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Hinzufügen eines zweiten IP-Filters"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-Filterliste, zweiter Eintrag"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-Filterliste, zweiter Eintrag"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Erstellen der IP-Sicherheitsrichtlinie"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "Name der IPsec-Richtlinie"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Assistent für IPsec-Richtlinie"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "Bearbeiten der IPsec-Richtlinie"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Hinzufügen einer neuen Sicherheitsregel zur IPsec-Richtlinie"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Erstellen einer neuen Sicherheitsregel"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Transportmodus"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Netzwerktyp"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "Auswahl der vorhandenen IP-Filterliste"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "Auswahl der vorhandenen Filteraktion"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Auswahl der Authentifizierungsmethode"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "Ende des Erstellens der Sicherheitsrichtlinie"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPsec-Richtlinie, die mit dem Gruppenrichtlinienobjekt verknüpft ist, aber nicht zugewiesen ist"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec-Richtlinie, die dem Gruppenrichtlinienobjekt zugewiesen ist"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Erfassung von IPsec-verschlüsseltem Datenverkehr"