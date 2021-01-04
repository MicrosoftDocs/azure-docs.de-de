---
title: Überprüfen von Traffic Manager-Einstellungen
description: In diesem Artikel erfahren Sie, wie Sie Ihre Traffic Manager-Einstellungen überprüfen und die Methode für das Routing von Datenverkehr testen können.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022325"
---
# <a name="verify-traffic-manager-settings"></a>Überprüfen von Traffic Manager-Einstellungen

Um Ihre Traffic Manager-Einstellungen zu testen, benötigen Sie mehrere Clients, die sich an verschiedenen Standorten befinden und auf denen Sie Ihre Tests ausführen können. Fahren Sie dann die Endpunkte in Ihrem Traffic Manager-Profil einzeln herunter.

* Setzen Sie die DNS-TTL so weit herunter, dass sich die Änderungen schnell verteilen (z.B. 30 Sekunden).
* Notieren Sie sich die IP-Adresse der Azure-Cloud-Services und -Websites in dem von Ihnen getesteten Profil.
* Verwenden Sie Tools, mit denen Sie einen DNS-Namen in eine IP-Adresse auflösen und anzeigen können.

Prüfen Sie, ob die DNS-Namen zu IP-Adressen aufgelöst werden, die Endpunkten in Ihrem Profil entsprechen. Die Auflösung der Namen sollte in Übereinstimmung mit der in Ihrem Traffic Manager-Profil festgelegten Routingmethode für Datenverkehr erfolgen. Für die Auflösung von DNS-Namen können Sie Tools wie **nslookup** oder **dig** verwenden.

Die folgenden Beispiele sollen Sie beim Test Ihres Traffic Manager-Profils unterstützen.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Überprüfen des Traffic Manager-Profils in Windows mit nslookup und ipconfig

1. Öffnen Sie eine Eingabeaufforderung oder eine Windows PowerShell-Eingabeaufforderung als Administrator.
2. Geben Sie `ipconfig /flushdns` ein, um den DNS-Auflösungscache zu leeren.
3. Geben Sie `nslookup <your Traffic Manager domain name>`ein. Der folgende Befehl überprüft beispielsweise einen Domänennamen mit dem Präfix *myapp.contoso*

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    Ein typisches Ergebnis umfasst die folgenden Informationen:

    + Der DNS-Name und die IP-Adresse des DNS-Servers, auf den zugegriffen wird, um diesen Traffic Manager-Domänennamen aufzulösen.
    + Den Traffic Manager-Domänennamen, die Sie in der Befehlszeile nach "Nslookup" und die IP-Adresse in der Traffic Manager-Domäne aufgelöst wird eingegeben. Die zweite IP-Adresse ist für die Überprüfung wichtig. Es sollte eine öffentliche virtuelle IP-Adresse (VIP) Adresse für eines der Cloud-Dienste oder Websites in Traffic Manager-Profil, das Sie testen übereinstimmen.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>So testen Sie die Routingmethode für Failoverdatenverkehr

1. Alle Endpunkte müssen ausgeführt werden.
2. Bei Verwendung eines einzelnen Clients fordern Sie die DNS-Auflösung für den Namen Ihrer Unternehmensdomäne mithilfe von nslookup oder eines ähnlichen Hilfsprogramms an.
3. Stellen Sie sicher, dass die aufgelöste IP-Adresse dem primären Endpunkt entspricht.
4. Fahren Sie den primären Endpunkt herunter bzw. entfernen Sie die Überwachungsdatei, sodass Traffic Manager davon ausgeht, dass die Anwendung nicht in Betrieb ist.
5. Warten Sie den Zeitraum der im Traffic Manager-Profile angegebenen DNS-Gültigkeitsdauer zuzüglich weiterer zwei Minuten ab. Wenn Ihre DNS-Gültigkeitsdauer z.B. 300 Sekunden (5 Minuten) beträgt, müssen Sie 7 Minuten warten.
6. Leeren Sie den DNS-Clientcache, und fordern Sie dann mit nslookup die DNS-Auflösung an. In Windows können Sie den DNS-Cache mit dem Befehl „ipconfig /flushdns“ leeren.
7. Stellen Sie sicher, dass die aufgelöste IP-Adresse Ihrem sekundären Endpunkt entspricht.
8. Wiederholen Sie dies so oft, bis nach und nach alle Endpunkte heruntergefahren sind. Stellen Sie sicher, dass vom DNS die IP-Adresse des nächsten Endpunkts in der Liste zurückgegeben wird. Wenn alle Endpunkte nicht betriebsbereit sind, sollte erneut die IP-Adresse des primären Endpunkts abgerufen werden.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>So testen Sie die gewichtete Routingmethode

1. Alle Endpunkte müssen ausgeführt werden.
2. Bei Verwendung eines einzelnen Clients fordern Sie die DNS-Auflösung für den Namen Ihrer Unternehmensdomäne mithilfe von nslookup oder eines ähnlichen Hilfsprogramms an.
3. Stellen Sie sicher, dass die aufgelöste IP-Adresse einem Ihrer Endpunkte entspricht.
4. Leeren Sie den DNS-Clientcache, und wiederholen Sie dann für jeden Endpunkt Schritt 2 und 3. Für jeden Ihrer Endpunkte sollte eine andere IP-Adresse zurückgegeben werden.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>So testen Sie die Routingmethode für Leistungsdatenverkehr

Um die Routingmethode für Datenverkehr "Leistung" effizient zu testen, müssen Sie weltweit über Clients verfügen. Für den Test Ihrer Dienste können Sie Clients in verschiedenen Azure-Regionen erstellen. Wenn Sie über ein globales Netzwerk verfügen, können Sie sich remote mit den Clients in anderen Teilen der Welt verbinden und Ihre Test von dort ausführen.

Sie können auch kostenlose webbasierte DNS-Lookup- und Analysedienste nutzen. Mit einigen dieser Tools können Sie die DNS-Namensauflösung von verschiedenen auf der ganzen Welt verteilten Standorten aus überprüfen. Führen Sie beispielsweise eine Suche nach „DNS-Lookup“ aus, um Beispiele zu erhalten. Sie können einen Drittanbieterdienst wie Gomez oder Keynote verwenden, um sicherzustellen, dass der Datenverkehr von den Profilen wie erwartet verteilt wird.

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zu Traffic Manager-Routingmethoden für Datenverkehr](traffic-manager-routing-methods.md)
* [Leistungsüberlegungen zu Traffic Manager](traffic-manager-performance-considerations.md)
* [Problembehandlung beim Status „Heruntergestuft“ in Azure Traffic Manager](traffic-manager-troubleshooting-degraded.md)
