---
title: Firewalltabellen – VMware-Lösung von CloudSimple – Azure
description: Erfahren Sie mehr über Firewalltabellen und Firewallregeln einer privaten CloudSimple-Cloud.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64576858"
---
# <a name="firewall-tables-overview"></a>Übersicht über Firewalltabellen

In einer Firewalltabelle sind die Regeln aufgeführt, gemäß denen Netzwerkdatenverkehr an und von Ressourcen einer privaten Cloud gefiltert werden. Sie können die Regeln auf ein VLAN oder ein Subnetz anwenden. Über die Regeln wird dann der Netzwerkdatenverkehr zwischen einem Quellnetzwerk oder einer Quell-IP-Adresse und einem Zielnetzwerk oder einer Ziel-IP-Adresse gesteuert.

## <a name="firewall-rules"></a>Firewallregeln

In der folgenden Tabelle sind die Parameter beschrieben, die es in einer Firewallregel gibt.

| Eigenschaft | Details |
| ---------| --------|
| **Name** | Ein Name, der die Firewallregel und deren Zweck eindeutig kennzeichnet. |
| **Priority** | Eine Zahl zwischen 100 und 4096, wobei 100 die höchste Priorität bedeutet. Regeln werden in der Reihenfolge ihrer Priorität verarbeitet. Ergibt sich für Datenverkehr eine Regelübereinstimmung, wird die Regelverarbeitung beendet. Daher werden alle Regeln, die niedrigere Prioritäten, aber dieselben Attribute wie Regeln mit höheren Prioritäten haben, nicht verarbeitet.  Achten Sie darauf, sich blockierende Regeln zu vermeiden. |
| **Zustandsnachverfolgung** | Nachverfolgung kann zustandslos (private Cloud, Internet oder VPN) oder zustandsbehaftet (öffentliche IP-Adresse) sein.  |
| **Protokoll** | Zu den Optionen gehören „Any“ (Beliebig), „TCP“ und „UDP“. Wenn Sie ICMP benötigen, verwenden Sie „Any“. |
| **Richtung** | Gibt an, ob die Regel für ein- oder ausgehenden Datenverkehr gilt. |
| **Aktion** | Zulassen oder ablehnen für den Typ von Datenverkehr, der in der Regel definiert ist. |
| **Quelle** | Eine IP-Adresse, ein CIDR-Block (Classless Inter-Domain Routing, z. B. 10.0.0.0/24) oder „Alle“.  Durch das Angeben eines Bereichs, eines Diensttags oder einer Anwendungssicherheitsgruppe haben Sie die Möglichkeit, weniger Sicherheitsregeln zu erstellen. |
| **Quellport** | Port, von dem der Netzwerkdatenverkehr stammt.  Sie können einen einzelnen Port oder einen Portbereich, etwa 443 oder 8000-8080, angeben. Das Angeben von Bereichen ermöglicht Ihnen die Erstellung von weniger Sicherheitsregeln. |
| **Ziel** | Eine IP-Adresse, ein CIDR-Block (Classless Inter-Domain Routing, z. B. 10.0.0.0/24) oder „Any“.  Durch das Angeben eines Bereichs, eines Diensttags oder einer Anwendungssicherheitsgruppe haben Sie die Möglichkeit, weniger Sicherheitsregeln zu erstellen.  |
| **Zielport** | Der Port, an den der Netzwerkdatenverkehr gesendet wird.  Sie können einen einzelnen Port oder einen Portbereich, etwa 443 oder 8000-8080, angeben. Das Angeben von Bereichen ermöglicht Ihnen die Erstellung von weniger Sicherheitsregeln.|

### <a name="stateless"></a>Zustandslos

Mit einer zustandslosen Regel werden nur einzelne Pakete analysiert, und diese werden entsprechend der Regel gefiltert.  
Möglicherweise sind weitere Regeln für Datenverkehr in umgekehrter Richtung erforderlich.  Verwenden Sie zustandslose Regeln für Datenverkehr zwischen den folgenden Punkten:

* Subnetze von privaten Clouds
* Ein lokales Subnetz und ein Subnetz in einer privaten Cloud
* Internetdatenverkehr aus den privaten Clouds

### <a name="stateful"></a>Zustandsbehaftet

 Mit einer zustandsbehafteten Regel werden die Verbindungen überwacht, die ihr zugeordnet sind. Für vorhandene Verbindungen wird ein Flussdatensatz erstellt. Die Kommunikation wird basierend auf dem Verbindungszustand der Flussdatensätze zugelassen oder verweigert.  Verwenden Sie Regeln dieses Typs für öffentliche IP-Adressen, um Datenverkehr aus dem Internet zu filtern.

### <a name="default-rules"></a>Standardregeln

Die folgenden Standardregeln werden in jeder Firewalltabelle erstellt.

|Priorität|NAME|Zustandsnachverfolgung|Direction|Datenverkehrstyp|Protocol|`Source`|Quellport|Ziel|Zielport|Aktion|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|Zustandsbehaftet|Ausgehend|Öffentliche IP-Adresse oder Internetdatenverkehr|Alle|Beliebig|Beliebig|Beliebig|Beliebig|ZULASSEN|
|65001|deny-all-from-internet|Zustandsbehaftet|Eingehend|Öffentliche IP-Adresse oder Internetdatenverkehr|Alle|Beliebig|Beliebig|Beliebig|Beliebig|Verweigern|
|65002|allow-all-to-intranet|Zustandslos|Ausgehend|Datenverkehr in privater Cloud oder VPN-Datenverkehr|Alle|Beliebig|Beliebig|Beliebig|Beliebig|ZULASSEN|
|65003|allow-all-from-intranet|Zustandslos|Eingehend|Datenverkehr in privater Cloud oder VPN-Datenverkehr|Alle|Beliebig|Beliebig|Beliebig|Beliebig|ZULASSEN|

## <a name="next-steps"></a>Nächste Schritte

* [Einrichten von Firewalltabellen und -regeln](https://docs.azure.cloudsimple.com/firewall/)