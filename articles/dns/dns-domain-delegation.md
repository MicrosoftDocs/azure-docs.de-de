---
title: Übersicht über die Azure DNS-Delegierung
description: Grundlegendes zum Ändern der Domänendelegierung und zum Verwenden von DNS-Namenserver zum Hosten von Domänen.
services: dns
author: vhorne
ms.service: dns
ms.date: 2/19/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 70c1c1ab0dd5d1998054cf0c68325022803dff06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563143"
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Delegieren von DNS-Zonen mit Azure DNS

Azure DNS ermöglicht es Ihnen, eine DNS-Zone zu hosten und die DNS-Einträge für eine Domäne in Azure zu verwalten. Damit DNS-Abfragen für eine Domäne Azure DNS erreichen, muss die Domäne aus der übergeordneten Domäne an Azure DNS delegiert werden. Denken Sie daran, dass Azure DNS keine Domänenregistrierungsstelle ist. In diesem Artikel wird die Funktionsweise der Domänendelegierung und die Domänendelegierung an Azure DNS beschrieben.

## <a name="how-dns-delegation-works"></a>Funktionsweise der DNS-Delegierung

### <a name="domains-and-zones"></a>Domänen und Zonen

Das Domain Name System ist eine Hierarchie von Domänen. Die Hierarchie beginnt mit der Stammdomäne, deren Name einfach „ **.** “ lautet.  Darunter befinden sich Domänen der obersten Ebene, z.B. „com“, „net“, „org“, „uk“ oder „jp“.  Unter diesen Domänen der obersten Ebene befinden sich die Domänen der zweiten Ebene (beispielsweise „org.uk“ oder „co.jp“).  Und so weiter. Die Domänen in der DNS-Hierarchie werden mithilfe von separaten DNS-Zonen gehostet. Diese Zonen sind global verteilt und werden von DNS-Namenservern in der ganzen Welt gehostet.

**DNS-Zone:** Eine Domäne ist ein eindeutiger Name im Domain Name System (beispielsweise „contoso.com“). Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet. Beispiel: Die Domäne „contoso.com“ kann eine Reihe von DNS-Einträgen wie „mail.contoso.com“ (für einen E-Mail-Server) und „www.contoso.com“ (für eine Website) enthalten.

**Domänenregistrierungsstelle:** Eine Domänenregistrierungsstelle ist ein Unternehmen, das Internetdomänennamen bereitstellen kann. Das Unternehmen überprüft, ob die Internetdomäne, die Sie verwenden möchten, noch verfügbar ist, und ermöglicht es Ihnen, sie zu erwerben. Sobald der Domänenname registriert wurde, sind Sie der rechtmäßige Besitzer des Domänennamens. Wenn Sie bereits über eine Internetdomäne verfügen, verwenden Sie für die Delegierung an Azure DNS die aktuelle Domänenregistrierungsstelle.

Eine Liste von anerkannten Domänenregistrierungsstellen finden Sie unter [ICANN-Accredited Registrars](https://www.icann.org/registrar-reports/accredited-list.html) (Von der ICANN anerkannte Registrierungsstellen).

### <a name="resolution-and-delegation"></a>Auflösung und Delegierung

Es gibt zwei Arten von DNS-Server:

* Ein *autoritativer* DNS-Server hostet DNS-Zonen. Er antwortet nur auf DNS-Abfragen nach Einträgen für diese Zonen.
* Ein *rekursiver* DNS-Server hostet keine DNS-Zonen. Er reagiert auf alle DNS-Abfragen durch Aufrufen der autoritativen DNS-Server, um die benötigten Daten zu erfassen.

Azure DNS stellt einen autoritativen DNS-Dienst bereit.  Ein rekursiver DNS-Dienst wird nicht bereitgestellt. Cloud Services und virtuelle Computer in Azure werden automatisch für die Verwendung eines rekursiven DNS-Diensts konfiguriert, der separat als Teil der Infrastruktur von Azure bereitgestellt wird. Informationen zum Ändern dieser DNS-Einstellungen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

DNS-Clients in PCs und mobilen Geräten rufen in der Regel einen rekursiven DNS-Server auf, um DNS-Abfragen auszuführen, die die Clientanwendungen benötigen.

Wenn ein rekursiver DNS-Server eine Abfrage für einen DNS-Eintrag erhält, z.B. „www.contoso.com“, muss er zuerst nach dem Namenserver suchen, der die Zone für die Domäne „contoso.com“ hostet. Um den Namensserver zu finden, beginnt er mit den Stammnamenservern und sucht von dort aus die Namenserver, die die Zone „com“ hosten. Anschließend fragt er die com-Namenserver ab, um den Namenserver zu suchen, der die Zone „contoso.com“ hostet.  Anschließend kann er diese Namenserver nach „www.contoso.com“ abfragen.

Dieses Verfahren wird als Auflösen des DNS-Namens bezeichnet. Streng genommen umfasst die DNS-Auflösung zusätzliche Schritte wie das Verfolgen von CNAMEs, aber für die Funktionsweise der DNS-Delegierung ist dies nicht relevant.

Wie verweist eine übergeordnete Zone auf die Namenserver für eine untergeordnete Zone? Hierfür wird eine besondere Art von DNS-Eintrag verwendet: ein so genannter NS-Eintrag. (NS steht hier für Namenserver.) Die Stammzone enthält beispielsweise NS-Einträge für „com“ und zeigt die Namenserver für die Zone „com“ an. Die Zone „com“ enthält im Gegenzug NS-Einträge für „contoso.com“ und zeigt die Namenserver für die Zone „contoso.com“ an. Das Einrichten der NS-Einträge für eine untergeordnete Zone in einer übergeordneten Zone wird Delegieren der Domäne genannt.

Die folgende Abbildung zeigt eine DNS-Beispielabfrage. „contoso.net“ und „partners.contoso.net“ sind Azure DNS-Zonen.

![Dns-nameserver](./media/dns-domain-delegation/image1.png)

1. Der Client fordert `www.partners.contoso.net` von seinem lokalen DNS-Server an.
2. Da der lokale DNS-Server nicht über den Eintrag verfügt, sendet er eine Anforderung an seinen Stammnamenserver.
3. Der Stammnamenserver verfügt zwar nicht über den Eintrag, kennt aber die Adresse des Namenservers für `.net` und gibt sie an den DNS-Server weiter.
4. Der lokale DNS-Server sendet die Anforderung an den `.net`-Namenserver.
5. Der `.net`-Namenserver verfügt zwar nicht über den Eintrag, kennt jedoch die Adresse des `contoso.net`-Namenservers. In diesem Fall antwortet er mit der Adresse des Namenservers für die in Azure DNS gehostete DNS-Zone.
6. Der lokale DNS-Server sendet die Anforderung an den Namenserver für die in Azure DNS gehostete `contoso.net`-Zone.
7. Die Zone `contoso.net` verfügt nicht über den Eintrag, kennt aber den Namenserver für `partners.contoso.net` und antwortet mit der Adresse. In diesem Fall handelt es sich um eine in Azure DNS gehostete DNS-Zone.
8. Der lokale DNS-Server sendet die Anforderung an den Namenserver für die `partners.contoso.net`-Zone.
9. Die `partners.contoso.net`-Zone hat den A-Eintrag und antwortet mit der IP-Adresse.
10. Der lokale DNS-Server gibt die IP-Adresse an den Client weiter.
11. Der Client stellt eine Verbindung mit der Website `www.partners.contoso.net` her.

Jede Delegierung umfasst eigentlich zwei Kopien der NS-Einträge: eine in der übergeordneten Zone, die auf die untergeordnete Zone verweist, und eine in der untergeordneten Zone selbst. Die Zone „contoso.net“ enthält die NS-Einträge für „contoso.net“ (neben den NS-Einträgen in „net“). Diese Einträge werden als autoritative NS-Einträge bezeichnet und befinden sich an der Spitze der untergeordneten Zone.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Ihre Domäne an Azure DNS delegieren](dns-delegate-domain-azure-dns.md).

