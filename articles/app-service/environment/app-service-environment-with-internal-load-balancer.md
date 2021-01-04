---
title: Erstellen einer ILB-ASE v1
description: Erstellen und verwenden Sie eine ASE mit einem internen Lastenausgleich. Dieses Dokument wird nur für Kunden bereitgestellt, die die ASE-Legacyumgebung v1 verwenden.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021519"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Verwenden einen internen Lastenausgleichs mit einer App Service-Umgebung

> [!NOTE] 
> In diesem Artikel wird die App Service-Umgebung v1 behandelt. Für die App Service-Umgebung steht eine neuere Version zur Verfügung. Diese ist benutzerfreundlicher und basiert auf einer leistungsfähigeren Infrastruktur. Weitere Informationen zu dieser neuen Version finden Sie unter [Einführung in die App Service-Umgebung](intro.md).
>

Das Feature App Service-Umgebung (App Service Environment, ASE) ist eine Premium-Dienstoption von Azure App Service mit erweiterter Konfigurationsmöglichkeit, die in den mehrinstanzenfähigen Stamps nicht verfügbar ist. Mit dem ASE-Feature wird im Wesentlichen Azure App Service in Ihrem virtuellen Azure-Netzwerk (VNet) bereitgestellt. Um ein besseres Verständnis der Möglichkeiten von App Service-Umgebungen zu erhalten, lesen Sie die Dokumentation [Was ist eine App Service-Umgebung?][WhatisASE]. Wenn Sie die Vorteile des Betriebs in einem VNET nicht kennen, ist es ratsam, die [FAQs zu virtuellen Netzwerken][virtualnetwork] zu lesen. 

## <a name="overview"></a>Übersicht
Eine ASE kann mit einem Endpunkt, auf den über das Internet zugegriffen werden kann, oder einer IP-Adresse in Ihrem VNet bereitgestellt werden. Um die IP-Adresse auf eine VNet-Adresse festzulegen, müssen Sie Ihre ASE mit einem internen Lastenausgleich (Internal Load Balancer, ILB) bereitstellen. Wenn Ihre ASE mit einem ILB konfiguriert ist, stellen Sie Folgendes bereit:

* Ihre eigene Domäne oder Unterdomäne. Zur Vereinfachung wird in diesem Dokument eine Unterdomäne vorausgesetzt, aber Sie können jeden Fall konfigurieren. 
* das für HTTPS verwendete Zertifikat
* DNS-Verwaltung für die Unterdomäne. 

Im Gegenzug können Sie z.B. Folgendes tun:

* Sicheres Hosten von Intranetanwendungen, z.B. Branchenanwendungen, auf die Sie über ein Site-to-Site- oder ExpressRoute-VPN zugreifen
* Apps in der Cloud hosten, die nicht im öffentlichen DNS-Server aufgeführt sind
* Erstellen von Back-End-Apps, die vom Internet isoliert sind, in die Ihre Front-End-Apps sicher integriert werden können

#### <a name="disabled-functionality"></a>Deaktivierte Funktionen
Es gibt einige Dinge, die Sie mit einer ILB-ASE nicht machen können. Hierzu zählen:

* Verwenden von IPSSL
* Zuweisen von IP-Adressen an bestimmte Apps
* Kaufen und Verwenden eines Zertifikats mit einer App über das Portal. Sie können natürlich weiterhin Zertifikate direkt bei einer Zertifizierungsstelle abrufen und sie mit Ihren Apps verwenden, allerdings nicht über das Azure-Portal.

## <a name="creating-an-ilb-ase"></a>Erstellen einer ILB-ASE
Das Erstellen einer ILB-ASE unterscheidet sich nicht wesentlich vom Erstellen einer normalen ASE. Eine eingehendere Erläuterung zum Erstellen einer ASE finden Sie unter [Erstellen einer App Service-Umgebung][HowtoCreateASE]. Der Prozess zum Erstellen einer ILB-ASE ist beim Erstellen eines VNet während der ASE-Erstellung und bei der Auswahl eines vorhandenen VNet identisch. So erstellen Sie eine ILB-ASE: 

1. Wählen Sie im Azure-Portal **Ressource erstellen > Web + Mobil > App Service-Umgebung** aus.
2. Wählen Sie Ihr Abonnement aus.
3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.
4. Wählen Sie ein VNET aus, oder erstellen Sie eines.
5. Erstellen Sie ein Subnetz, wenn Sie ein VNET auswählen.
6. Wählen Sie **Virtuelles Netzwerk/Speicherort > VNET-Konfiguration**, und legen Sie den VIP-Typ auf „Intern“ fest.
7. Geben Sie einen Namen für die Unterdomäne an (diese Unterdomäne wird für Apps verwendet, die in dieser ASE erstellt werden).
8. Wählen Sie **OK** und dann **Erstellen** aus.

![Dieser Screenshot zeigt die zum Erstellen einer ILB-ASE verwendeten Bildschirme.][1]

Im Bereich „Virtuelles Netzwerk“ gibt es eine Option für die VNET-Konfiguration, mit der Sie zwischen einer externen VIP oder einer internen VIP auswählen können. Die externe Adresse ist die Standardeinstellung. Wenn Sie „Extern“ festlegen müssen, verwendet Ihre ASE eine über das Internet zugängliche VIP. Bei Auswahl von „Intern“ wird Ihre ASE mit einem ILB unter einer IP-Adresse in Ihrem VNET konfiguriert. 

Nach Auswahl der internen Adresse können Sie Ihrer ASE keine weiteren IP-Adressen hinzufügen, Sie müssen stattdessen die Unterdomäne der ASE bereitstellen. In einer ASE mit einer externen VIP-Adresse wird der Name der ASE in der Unterdomäne für Apps verwendet, die in dieser ASE erstellt werden. Wenn Ihre ASE als **_contosotest_* _ und Ihre App in dieser ASE als _*_mytest_*_ benannt wurde, weist die Unterdomäne das Format _*_contosotest.p.azurewebsites.net_*_ auf, und die URL für die App lautet _*_mytest.contosotest.p.azurewebsites.net_*_ . Wenn Sie „Intern“ für den VIP-Typ festgelegt haben, wird Ihr ASE-Name in der Unterdomäne für die ASE nicht verwendet. Sie geben die Unterdomäne explizit an. Wenn Ihre Unterdomäne _*_contoso.corp.net_*_ heißt und Sie in dieser ASE eine App mit dem Namen _*_timereporting_*_ erstellt haben, lautet die URL für diese App _*_timereporting.contoso.corp.net_*_.

## <a name="apps-in-an-ilb-ase"></a>Apps in einer ILB-ASE
Das Erstellen einer App in einer ILB-ASE entspricht dem normalen Erstellen einer App in einer ASE. 

1. Klicken Sie im Azure-Portal auf _ *Ressource erstellen -> Web + Mobil -> Web** oder auf **Mobil** oder **API-App**.
2. Geben Sie den Namen der App ein.
3. Wählen Sie Ihr Abonnement aus.
4. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.
5. Wählen Sie einen App Service-Plan (ASP) aus, oder erstellen Sie einen Plan. Wenn Sie einen neuen ASP erstellen, wählen Sie Ihre ASE als Speicherort, und wählen Sie den Workerpool, in dem Ihre ASP erstellt werden soll. Beim Erstellen des ASP wählen Sie Ihre ASE als Speicherort und den Workerpool aus. Wenn Sie den Namen der App angeben, sehen Sie, dass die Unterdomäne unter Ihrem App-Namen durch die Unterdomäne für Ihre ASE ersetzt wird. 
6. Klicken Sie auf **Erstellen**. Aktivieren Sie das Kontrollkästchen **An das Dashboard anheften** aus, wenn die App im Dashboard angezeigt werden soll. 

![Dieser Screenshot zeigt, wie eine App in einer ILB ASE im Azure-Portal erstellt wird.][2]

Unter dem App-Namen wird der Name der Unterdomäne aktualisiert, um die Unterdomäne Ihrer ASE anzuzeigen. 

## <a name="post-ilb-ase-creation-validation"></a>Überprüfung nach der ILB-ASE-Erstellung
Eine ILB-ASE unterscheidet sich etwas von einer Nicht-ILB-ASE. Wie bereits erwähnt, müssen Sie Ihren eigenen DNS verwalten und auch Ihr eigenes Zertifikat für HTTPS-Verbindungen bereitstellen. 

Nach dem Erstellen Ihrer ASE werden Sie bemerken, dass die Unterdomäne angezeigt wird, die Sie angegeben haben, und dass das Menü **Einstellung** ein neues Element **ILB-Zertifikat** enthält. Die ASE wird mit einem selbstsignierten Zertifikat erstellt, um das Testen von HTTPS zu vereinfachen. Im Portal werden Sie darauf hingewiesen, dass Sie Ihr eigenes Zertifikat für HTTPS bereitstellen müssen, aber der Zweck dabei ist, dass Sie ein zu Ihrer Unterdomäne passendes Zertifikat verwenden. 

![Dieser Screenshot zeigt die Subdomäne, die Sie angegeben haben, als Sie die ASE erstellt haben.][3]

Wenn Sie einfach experimentieren und nicht wissen, wie Sie ein Zertifikat erstellen, können Sie mit der IIS-MMC-Konsolenanwendung ein selbstsigniertes Zertifikat erstellen. Nach der Erstellung können Sie es als PFX-Datei exportieren und dann in die ILB-Zertifikatbenutzeroberfläche hochladen. Wenn Sie auf eine Website zugreifen, die mit einem selbstsignierten Zertifikat geschützt wird, werden Sie in einer Browserwarnung darauf hingewiesen, dass die Website, auf die Sie zugreifen, nicht sicher ist, weil das Zertifikat nicht validiert werden kann. Wenn Sie diese Warnung vermeiden möchten, benötigen Sie ein ordnungsgemäß signiertes Zertifikat, das Ihrer Unterdomäne entspricht und eine Zertifikatkette aufweist, die von Ihrem Browser erkannt wird.

![Dieser Screenshot zeigt, wie die IIS-MMC-Konsolenanwendung verwendet wird, um ein selbstsigniertes Zertifikat zu erstellen.][6]

Gehen Sie wie folgt vor, wenn Sie den Flow mit Ihren eigenen Zertifikaten ausprobieren und sowohl HTTP als auch HTTPS für den Zugriff auf die ASE testen möchten:

1. Wechseln Sie nach der ASE-Erstellung zur ASE-Benutzeroberfläche: **ASE > Einstellungen > ILB-Zertifikate**.
2. Legen Sie das ILB-Zertifikat durch Auswahl von Zertifikat-PFX-Datei und Angabe des Kennworts fest. Dieser Schritt nimmt etwas Zeit in Anspruch, und es wird die Meldung angezeigt, dass eine Skalierung ausgeführt wird.
3. Rufen Sie die ILB-Adresse für Ihre ASE ab (**ASE > Eigenschaften > Virtuelle IP-Adresse**).
4. Erstellen Sie nach der Erstellung eine Web-App in der ASE. 
5. Erstellen Sie einen virtuellen Computer, wenn in diesem VNET keiner vorhanden ist (nicht im gleichen Subnetz wie die ASE, da dies zum Absturz führt).
6. Legen Sie den DNS für die Unterdomäne fest. Sie können einen Platzhalter mit Ihrer Unterdomäne in Ihrem DNS verwenden, oder wenn Sie einige einfache Tests durchführen möchten, bearbeiten Sie die Hostdatei auf Ihrem virtuellen Computer, um die VIP-Adresse für den Web-App-Namen festzulegen. Wenn Sie in einer ASE mit dem Unterdomänennamen „.ilbase.com“ die Web-App „mytestapp“ erstellen, sodass sie mit „mytestapp.ilbase.com“ adressiert wird, müssen Sie dies in der Hostdatei festlegen. (Unter Windows befindet sich die Hostdatei unter C:\Windows\System32\drivers\etc\.\)
7. Wechseln Sie in einem Browser auf diesem virtuellen Computer zu `https://mytestapp.ilbase.com` (bzw. dem Namen Ihrer Web-App in Ihrer Unterdomäne).
8. Verwenden Sie einen Browser auf diesem virtuellen Computer, und wechseln Sie zu `https://mytestapp.ilbase.com`. Bei Verwendung eines selbstsignierten Zertifikats müssen Sie eine Beeinträchtigung der Sicherheit in Kauf nehmen. 

Die IP-Adresse der ILB wird in den Eigenschaften als virtuelle IP-Adresse aufgeführt.

![Dieser Screenshot zeigt, dass die IP-Adresse der ILB in den Eigenschaften als virtuelle IP-Adresse aufgeführt wird.][4]

## <a name="using-an-ilb-ase"></a>Verwenden einer ILB-ASE
#### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
Eine ILB-ASE ermöglicht eine Netzwerkisolation für Ihre Apps. Die Apps sind weder zugänglich noch im Internet bekannt. Dieser Ansatz eignet sich hervorragend zum Hosten von Intranetsites, wie etwa Branchenanwendungen. Wenn Sie den Zugriff noch weiter einschränken müssen, können Sie Netzwerksicherheitsgruppen (NSGs) zum Steuern des Zugriffs auf Netzwerkebene verwenden. 

Wenn Sie NSGs verwenden möchten, um den Zugriff weiter einzuschränken, müssen Sie sicherstellen, dass Sie nicht die Kommunikation unterbrechen, die die ASE für den Betrieb benötigt. Obwohl der HTTP/HTTPS-Zugriff nur über den von der ASE verwendeten ILB erfolgt, ist die ASE weiterhin von Ressourcen außerhalb des VNET abhängig. Um festzustellen, welcher Netzwerkzugriff erforderlich ist, lesen Sie das Dokument [Steuern von eingehendem Datenverkehr in eine App Service-Umgebung][ControlInbound] und den Artikel [Details zur Netzwerkkonfiguration für App Service-Umgebungen mit ExpressRoute][ExpressRoute]. 

Um Ihre Netzwerksicherheitsgruppen zu konfigurieren, müssen Sie die IP-Adresse kennen, die Azure zum Verwalten Ihrer ASE verwendet. Diese IP-Adresse ist auch die ausgehende IP-Adresse Ihrer ASE für Internetanfrage. Die ausgehende IP-Adresse für Ihr ASE bleibt während der Lebensdauer Ihrer ASE statisch. Wenn Sie die ASE löschen und neu erstellen, erhalten Sie eine neue IP-Adresse. Wechseln Sie zum Ermitteln der IP-Adresse zu **Einstellungen > Eigenschaften**, und suchen Sie nach **Ausgehende IP-Adresse**. 

![Dieser Screenshot zeigt, wo Sie die IP-Adresse für ausgehenden Datenverkehr für Ihre ASE finden.][5]

#### <a name="general-ilb-ase-management"></a>Allgemeine ILB-ASE-Verwaltung
Das Verwalten einer ILB-ASE ist weitgehend identisch mit dem normalen Verwalten einer ASE. Sie müssen Ihre Workerpools hochskalieren, um weitere ASP-Instanzen zu hosten, und Ihre Front-End-Server hochskalieren, um zusätzlichen HTTP/HTTPS-Datenverkehr zu verarbeiten. Allgemeine Informationen zur Verwaltung der Konfiguration einer ASE finden Sie unter [Konfigurieren einer App Service-Umgebung][ASEConfig]. 

Die zusätzlichen Verwaltungselemente sind Zertifikat- und DNS-Verwaltung. Sie müssen das für HTTPS verwendete Zertifikat nach der ILB-ASE-Erstellung abrufen und hochladen und vor Ablauf ersetzen. Da Azure die Basisdomäne besitzt, können Zertifikate für ASEs mit einer externen VIP-Adresse bereitgestellt werden. Da die für eine ILB-ASE verwendete Unterdomäne eine beliebige Unterdomäne sein kann, müssen Sie Ihr eigenes Zertifikat für HTTPS bereitstellen. 

#### <a name="dns-configuration"></a>DNS-Konfiguration
Bei Verwendung einer externen VIP-Adresse wird das DNS mithilfe von Azure verwaltet. Jede in Ihrer ASE erstellte App wird automatisch dem Azure-DNS – einem öffentlichen DNS – hinzugefügt. In einer ILB-ASE müssen Sie einen eigenen DNS verwalten. Für eine vorgegebene Unterdomäne, wie z.B. contoso.corp.net, müssen Sie DNS-A-Einträge mit folgenden Verweisen auf Ihre ILB-Adresse erstellen:

- \*
- *.scm
- ftp
- Veröffentlichen

## <a name="getting-started"></a>Erste Schritte
Informationen zu den ersten Schritten mit App Service-Umgebungen finden Sie unter [Einführung in App Service-Umgebungen][WhatisASE].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md