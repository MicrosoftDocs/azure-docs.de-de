---
title: Erstellen einer ILB-ASE mit ARM
description: Hier erfahren Sie, wie Sie mit Azure Resource Manager-Vorlagen eine App Service-Umgebung mit einem internen Lastenausgleich (ILB-ASE) erstellen. Isolieren Sie Ihre Apps vollständig vom Internet.
author: ccompy
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: ccompy
ms.custom: mvc, seodec18
ms.openlocfilehash: 27c9198558a730d0af49077d6f5baa6db4789416
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009550"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>Erstellen und Verwenden einer App Service-Umgebung für internen Lastenausgleich 

Die Azure App Service-Umgebung ist eine Bereitstellung von Azure App Service in einem Subnetz in einem virtuellen Azure-Netzwerk (VNET). Eine App Service-Umgebung (App Service Environment, ASE) kann auf zwei Arten bereitgestellt werden: 

- Mit einer VIP unter einer externen IP-Adresse, die häufig als „externe ASE“ bezeichnet wird
- Mit einer VIP unter einer internen IP-Adresse, die häufig als „ILB-ASE“ bezeichnet wird, da der interne Endpunkt ein interner Lastenausgleich (ILB) ist. 

In diesem Artikel wird veranschaulicht, wie Sie eine ILB-ASE erstellen. Eine Übersicht über die ASE finden Sie unter [Einführung in App Service-Umgebungen][Intro]. Informationen zum Erstellen einer externen ASE finden Sie unter [Erstellen einer externen ASE][MakeExternalASE].

## <a name="overview"></a>Übersicht 

Sie können eine ASE mit einem Endpunkt, auf den über das Internet zugegriffen werden kann, oder einer IP-Adresse in Ihrem VNET bereitstellen. Um die IP-Adresse auf eine VNET-Adresse festzulegen, muss die ASE mit einem ILB bereitgestellt werden. Beim Bereitstellen der ASE mit einem ILB müssen Sie den Namen Ihrer ASE angeben. Der Name Ihrer ASE wird im Domänensuffix für die Apps in Ihrer ASE verwendet.  Das Domänensuffix für Ihre ILB-ASE lautet „&lt;ASE-Name&gt;.appserviceenvironment.net“. Apps, die in einer ILB-ASE erstellt werden, werden nicht im öffentlichen DNS abgelegt. 

In früheren Versionen der ILB-ASE mussten Sie ein Domänensuffix und ein Standardzertifikat für HTTPS-Verbindungen bereitstellen. Das Domänensuffix wird bei der Erstellung der ILB-ASE nicht mehr erfasst, ebenso wenig wie das Standardzertifikat. Wenn Sie jetzt eine ILB-ASE erstellen, wird das Standardzertifikat von Microsoft bereitgestellt und vom Browser als vertrauenswürdig eingestuft. Sie können weiterhin benutzerdefinierte Domänennamen für Apps in Ihrer ASE sowie Zertifikate für diese benutzerdefinierten Domänennamen festlegen. 

Mit einer ILB-ASE können Sie z.B. folgende Aktionen durchführen:

-   Intranetanwendungen sicher in der Cloud hosten, auf die Sie über ein Site-to-Site- oder ExpressRoute-VPN zugreifen
-   Apps mit einem WAF-Gerät schützen
-   Apps in der Cloud hosten, die nicht in öffentlichen DNS-Servern aufgeführt sind
-   Vom Internet isolierte Back-End-Apps erstellen, in die Ihre Front-End-Apps sicher integriert werden können

### <a name="disabled-functionality"></a>Deaktivierte Funktionen ###

Es gibt einige Dinge, die Sie mit einer ILB-ASE nicht machen können:

-   IP-basiertes SSL verwenden
-   Bestimmten Apps IP-Adressen zuweisen
-   Ein Zertifikat mit einer App über das Azure-Portal kaufen und verwenden. Sie können Zertifikate direkt von einer Zertifizierungsstelle beziehen und mit Ihren Apps verwenden. Über das Azure-Portal können diese nicht bezogen werden.

## <a name="create-an-ilb-ase"></a>Erstellen einer ILB-ASE ##

So erstellen Sie eine ILB-ASE:

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Web** > **App Service-Umgebung**.

2. Wählen Sie Ihr Abonnement aus.

3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.

4. Geben Sie den Namen Ihrer App Service-Umgebung ein.

5. Wählen Sie als Typ für die virtuelle IP „Intern“ aus.

    ![ASE-Erstellung](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

> [!NOTE]
> Der Name der App Service-Umgebung darf nicht mehr als 37 Zeichen umfassen.

6. Wählen Sie „Netzwerk“.

7. Erstellen oder wählen Sie ein virtuelles Netzwerk aus. Wenn Sie hier ein neues VNET erstellen, wird es mit einem Adressbereich von 192.168.250.0/23 definiert. Zum Erstellen eines VNET mit einem anderen Adressbereich oder in einer anderen Ressourcengruppe als der ASE verwenden Sie das Portal zur Azure Virtual Network-Erstellung. 

8. Wählen Sie ein leeres Subnetz aus, oder erstellen Sie eins. Wenn Sie ein Subnetz auswählen möchten, muss dieses leer und nicht delegiert sein. Nach Erstellung der ASE kann die Subnetzgröße nicht mehr geändert werden. Empfohlen wird eine Größe von `/24` mit 256 Adressen, die eine ASE in maximaler Größe und alle Skalierungsanforderungen verarbeiten kann. 

    ![ASE-Netzwerk][1]

7. Klicken Sie auf **Überprüfen und erstellen** und dann auf **Erstellen**.


## <a name="create-an-app-in-an-ilb-ase"></a>Erstellen einer App in einer ILB-ASE ##

Sie erstellen eine App in einer ILB-ASE auf dieselbe Weise, wie Sie eine App normalerweise in einer ASE erstellen würden.

1. Wählen Sie im Azure-Portal **Ressource erstellen** > **Web** > **Web-App** aus.

1. Geben Sie den Namen der App ein.

1. Wählen Sie das Abonnement aus.

1. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie.

1. Wählen Sie Veröffentlichungseinstellungen, Laufzeitstapel und Betriebssystem aus.

1. Wählen Sie eine vorhandene ILB-ASE als Standort aus.  Sie können während der Erstellung der App auch eine neue ASE erstellen, indem Sie einen isolierten App Service-Plan auswählen. Wenn Sie eine neue ASE erstellen möchten, wählen Sie die Region aus, in der die ASE erstellt werden soll.

1. Wählen Sie einen App Service-Plan aus, oder erstellen Sie einen. 

1. Wenn Sie bereit sind, klicken Sie auf **Überprüfen und erstellen** und dann auf **Erstellen**.

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Webaufträge, Funktionen und die ILB-ASE 

Funktionen und Webaufträge werden auf einer ILB-ASE unterstützt. Damit das Portal jedoch mit ihnen arbeiten kann, benötigen Sie Netzwerkzugriff auf die SCM-Site.  Das bedeutet, dass Ihr Browser sich auf einem Host befinden muss, der sich im virtuellen Netzwerk befindet oder mit diesem verbunden ist. Wenn Ihre ILB-ASE über einen Domänennamen verfügt, der nicht auf *appserviceenvironment.net* endet, müssen Sie das von Ihrer scm-Website verwendete HTTPS-Zertifikat für Ihren Browser als vertrauenswürdig kennzeichnen.

## <a name="dns-configuration"></a>DNS-Konfiguration 

Wenn Sie eine externe ASE verwenden, werden in ihrer ASE erstellte Apps bei Azure DNS registriert. Damit Ihre Apps öffentlich verfügbar sind, sind keine zusätzlichen Schritte in einer externen ASE erforderlich. In einer ILB-ASE müssen Sie Ihr eigenes DNS verwalten. Sie können dies entweder in Ihrem eigenen DNS-Server oder mit privaten Azure DNS-Zonen erzielen.

So konfigurieren Sie DNS in Ihrem eigenen Server mit Ihrer ILB-ASE

1. Erstellen Sie eine Zone für „&lt;ASE-Name&gt;.appserviceenvironment.net“.
2. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die ILB-IP-Adresse verweist.
3. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die ILB-IP-Adresse verweist.
4. Erstellen Sie eine Zone namens „scm“ in „&lt;ASE-Name&gt;.appserviceenvironment.net“.
5. Erstellen Sie einen A-Eintrag in der Zone „scm“, der * auf die ILB-IP-Adresse verweist.

So konfigurieren Sie DNS in privaten Azure DNS-Zonen

1. Erstellen Sie eine private Azure DNS-Zone namens „&lt;ASE-Name&gt;.appserviceenvironment.net“.
2. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die ILB-IP-Adresse verweist.
3. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die ILB-IP-Adresse verweist.
4. Erstellen Sie einen A-Eintrag in dieser Zone, der *.scm auf die ILB-IP-Adresse verweist.

Die DNS-Einstellungen für Ihr ASE-Standarddomänensuffix schränken Ihre Apps nicht dahingehend ein, dass sie nur über diese Namen zugänglich sind. Sie können einen benutzerdefinierten Domänennamen ohne jegliche Validierung für Ihre Apps in einer ILB-ASE festlegen. Wenn Sie dann eine Zone namens contoso.net erstellen möchten, können Sie dies tun und die Zone auf die ILB-IP-Adresse verweisen lassen. Der benutzerdefinierte Domänenname funktioniert für App-Anforderungen, aber nicht für die SCM-Site. Die SCM-Site ist nur unter „&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net“ verfügbar.

Die Zone namens .&lt;asename&gt;.appserviceenvironment.net ist global eindeutig. Vor Mai 2019 konnten Kunden das Domänensuffix der ILB-ASE angeben. Wenn Sie .contoso.com als Domänensuffix verwenden wollten, konnten Sie dies tun, wobei dies dann die SCM-Site einschloss. Dieses Modell bot Herausforderungen, einschließlich: Verwalten des SSL-Standardzertifikats, fehlendes einmaliges Anmelden (Single Sign-On) bei der SCM-Site und die Anforderung, ein Platzhalterzertifikat zu verwenden. Der Upgradeprozess für das ILB-ASE-Standardzertifikat war außerdem führte außerdem zu Unterbrechungen und Neustarts von Anwendungen. Um diese Probleme zu beheben, wurde das ILB-ASE-Verhalten dahingehend geändert, dass ein Domänensuffix, das auf dem Namen der ASE basiert, zusammen mit einem Suffix, das Microsoft gehört, verwendet wird. Die Änderung des ILB-ASE-Verhaltens wirkt sich nur auf ILB-ASEs aus, die nach Mai 2019 erstellt wurden. Davor vorhandene ILB-ASEs müssen weiterhin das Standardzertifikat der ASE und seine DNS-Konfiguration verwalten.

## <a name="publish-with-an-ilb-ase"></a>Veröffentlichen mit einer ILB-ASE

Für jede erstellte App sind zwei Endpunkte vorhanden. In einer ILB-ASE befinden sich sowohl *&lt;App-Name&gt;.&lt;ILB-ASE-Domäne&gt;* als auch *&lt;App-Name&gt;.scm.&lt;ILB-ASE-Domäne&gt;* . 

Über den Namen der SCM-Website gelangen Sie zur Kudu-Konsole, die im Azure-Portal als **Erweitertes Portal** bezeichnet wird. An der Kudu-Konsole können Sie u.a. die Umgebungsvariablen anzeigen, den Datenträger untersuchen und eine Konsole verwenden. Weitere Informationen hierzu finden Sie unter [Kudu-Konsole für Azure App Service][Kudu]. 

Internetbasierte CI-Systeme, wie GitHub und Azure DevOps, funktionieren weiterhin mit einer ILB-ASE, wenn der Build-Agent internetfähig ist und sich im selben Netzwerk wie die ILB-ASE befindet. Im Falle von Azure DevOps, wenn der Build-Agent auf dem gleichen VNET wie ILB-ASE erstellt wird (ein anderes Subnetz ist in Ordnung), kann er Code aus Azure DevOps-Git pullen und in der ILB-ASE bereitstellen. Wenn Sie keinen eigenen Build-Agent erstellen möchten, müssen Sie ein CI-System verwenden, das ein Pullmodell wie Dropbox verwendet.

Die Veröffentlichungsendpunkte für die Apps in einer ILB-ASE verwenden die Domäne, mit der die ILB-ASE erstellt wurde. Diese Domäne wird im Veröffentlichungsprofil und auf dem Portalblatt der App angezeigt (unter **Übersicht** > **Zusammenfassung** sowie unter **Eigenschaften**). Wenn Sie eine ILB-ASE mit dem Domänensuffix *&lt;ASE-Name&gt;.appserviceenvironment.net* und eine App namens *mytest* besitzen, verwenden Sie *mytest.&lt;ASE-Name&gt;.appserviceenvironment.net* für FTP und *mytest.scm.contoso.net* für die Webbereitstellung.

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>Konfigurieren einer ILB-ASE mit einem WAF-Gerät ##

Sie können ein WAF-Gerät (Web Application Firewall) mit Ihrer ILB-ASE kombinieren, um nur die gewünschten Apps im Internet verfügbar zu machen und den Zugriff auf die übrigen Apps nur über das VNET zu ermöglichen. So können Sie unter anderem sichere mehrstufige Anwendungen erstellen.

Weitere Informationen zum Konfigurieren Ihrer ILB-ASE mit einem WAF-Gerät finden Sie unter [Konfigurieren einer Web Application Firewall (WAF) für eine App Service-Umgebung][ASEWAF]. In diesem Artikel wird die Verwendung eines virtuellen Barracuda-Geräts mit Ihrer ASE erläutert. Eine weitere Möglichkeit besteht darin, Azure Application Gateway zu verwenden. Application Gateway sichert mithilfe der OWASP-Kernregeln alle dahinter platzierten Anwendungen. Weitere Informationen zu Application Gateway finden Sie unter [Einführung zur Web Application Firewall (WAF) von Azure][AppGW].

## <a name="ilb-ases-made-before-may-2019"></a>Vor Mai 2019 erstellte ILB-ASEs

Bei ILB-ASEs, die vor Mai 2019 erstellt wurden, mussten Sie das Domänensuffix während der ASE-Erstellung festlegen. Sie mussten auch ein Standardzertifikat hochladen, das auf diesem Domänensuffix basierte. Darüber hinaus war mit einer älteren ILB-ASE kein einmaliges Anmelden bei der Kudu-Konsole mit Apps in dieser ILB-ASE möglich. Beim Konfigurieren von DNS für eine ältere ILB-ASE müssen Sie den A-Platzhaltereintrag in einer Zone festlegen, die Ihrem Domänensuffix entspricht. 

## <a name="get-started"></a>Erste Schritte ##

* Informationen zum Einstieg in ASEs finden Sie unter [Einführung in App Service-Umgebungen][Intro]. 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../overview.md#app-service-on-linux
