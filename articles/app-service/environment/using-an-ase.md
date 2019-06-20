---
title: Verwenden der App Service-Umgebung – Azure
description: Erstellen, Veröffentlichen und Skalieren in einer Azure App Service-Umgebung
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e9c34d3cfd5ce9bb3a8f9a9072f2843331065100
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496519"
---
# <a name="use-an-app-service-environment"></a>Verwenden einer App Service-Umgebung #

Die Azure App Service-Umgebung ist eine Bereitstellung des Azure App Service in einem Subnetz im virtuellen Azure-Netzwerk eines Kunden. Sie besteht aus:

- **Front-Ends**: In den Front-Ends endet HTTP/HTTPS in einer App Service-Umgebung (ASE).
- **Worker**: Bei Workern handelt es sich um die Ressourcen, die Ihre Apps hosten.
- **Datenbank**: Die Datenbank enthält Informationen, durch die die Umgebung festgelegt ist.
- **Speicher**: Der Speicher wird zum Hosten der vom Kunden veröffentlichten Apps verwendet.

> [!NOTE]
> Es gibt zwei Versionen der App Service-Umgebung: ASEv1 und ASEv2. In ASEv1 müssen Sie die Ressourcen verwalten, bevor Sie sie verwenden können. Informationen zum Konfigurieren und Verwalten von ASEv1 finden Sie unter [Konfigurieren einen App Service-Umgebung v1][ConfigureASEv1]. Im weiteren Verlauf dieses Artikels liegt der Schwerpunkt auf ASEv2.
>
>

Sie können eine ASE (ASEv1 und ASEv2) mit einer externen oder internen virtuellen IP (VIP) für den App-Zugriff bereitstellen. Die Bereitstellung mit einer externen VIP wird im Allgemeinen als externe ASE bezeichnet. Die interne Version wird als ILB-ASE bezeichnet, da ein interner Load Balancer (ILB) verwendet wird. Weitere Informationen zur ILB-ASE finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Erstellen einer App in einer ASE ##

Um eine App in einer ASE zu erstellen, verwenden Sie den gleichen Prozess wie bei der normalen Erstellung, jedoch mit einigen kleinen Unterschieden. Beim Erstellen eines neuen App Service-Plans gilt:

- Anstatt einen geografischen Standort auszuwählen, an dem Sie Ihre App bereitstellen, wählen Sie eine ASE als Ihren Standort aus.
- Alle in einer ASE erstellen App Service-Pläne müssen im Tarif „Isoliert“ enthalten sein.

Wenn Sie über keine ASE verfügen, können Sie eine erstellen. Folgen Sie hierzu den Anweisungen in [Erstellen einer App Service-Umgebung][MakeExternalASE].

So erstellen Sie eine App in einer ASE

1. Wählen Sie **Ressource erstellen** > **Web + Mobil** > **Web-Apps** aus.

2. Geben Sie einen Namen für die App ein. Wenn Sie bereits einen App Service-Plan in einer ASE ausgewählt haben, entspricht der Domänenname der App dem Domänenname der ASE.

    ![Namensauswahl für Apps][1]

1. Wählen Sie ein Abonnement aus.

1. Geben Sie einen Namen für eine neue Ressourcengruppe ein, oder wählen Sie **Vorhandene verwenden** aus, und wählen Sie in der Dropdownliste eine Ressourcengruppe aus.

1. Wählen Sie Ihr Betriebssystem aus. 

1. Wählen Sie einen in Ihrer ASE vorhandenen App Service-Plan aus, oder erstellen Sie mit den folgenden Schritten einen neuen:

    a. Wählen Sie **Neu erstellen**.

    b. Geben Sie einen Namen für Ihren App Service-Plan ein.

    c. Wählen Sie Ihre ASE in der Dropdownliste **Speicherort** aus. 
    
    d. Wählen Sie einen **Isolierten** Tarif. Wählen Sie **Auswählen**.

    e. Klicken Sie auf **OK**.
    
    ![Isolierte Tarife][2]

    > [!NOTE]
    > Linux-Apps und Windows-Apps können sich nicht im selben App Service-Plan befinden, jedoch in derselben App Service-Umgebung. 
    >

2. Klicken Sie auf **Erstellen**.

## <a name="how-scale-works"></a>Skalieren ##

Jede App Service-App wird in einem App Service-Plan ausgeführt. Im Containermodell beinhalten Umgebungen App Service-Pläne, und App Service-Pläne beinhalten Apps. Wenn Sie eine App skalieren, skalieren Sie den App Service-Plan und damit alle Apps in diesem Plan.

Wenn Sie in einer ASEv2 einen App Service-Plan skalieren, wird die erforderliche Infrastruktur automatisch hinzugefügt. Bei der Skalierung von Vorgängen kommt es zu einer Zeitverzögerung, während die Infrastruktur hinzugefügt wird. In ASEv1 muss die erforderliche Infrastruktur vor dem Erstellen oder horizontalen Hochskalieren Ihres App Service-Plans hinzugefügt werden. 

Im mehrinstanzenfähigen App Service erfolgt die Skalierung normalerweise unmittelbar, da sofort ein Ressourcenpool für die Unterstützung verfügbar ist. In einer ASE gibt es keinen solchen Puffer. Die Ressourcen werden nach Bedarf zugeordnet.

In einer ASE können Sie auf 100 Instanzen zentral hochskalieren. Diese 100 Instanzen können alle zu demselben App Service-Plan gehören oder über mehrere App Service-Pläne verteilt sein.

## <a name="ip-addresses"></a>IP-Adressen ##

In App Service gibt es die Möglichkeit, eine dedizierte IP-Adresse an eine App zu vergeben. Diese Funktion ist nach dem Konfigurieren einer IP-basierten SSL verfügbar, wie unter [Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure App Service][ConfigureSSL] beschrieben. In einer ASE gibt es jedoch eine wichtige Ausnahme. Sie können keine zusätzlichen IP-Adressen hinzufügen, die für eine IP-basierte SSL in einer ILB-ASE verwendet werden sollen.

In ASEv1 müssen die IP-Adressen vor der Verwendung als Ressourcen zugewiesen werden. In ASEv2 können Sie die IP-Adressen einfach genau wie im mehrinstanzenfähigen App Service aus der App heraus verwenden. In einer ASEv2 mit bis zu 30 IP-Adressen ist immer eine freie Adresse vorhanden. Bei jeder Verwendung einer IP-Adresse wird eine weitere hinzugefügt, sodass immer eine sofort verfügbare Adresse zur Verfügung steht. Bei der Vergabe einer weiteren IP-Adresse ist eine zeitliche Verzögerung erforderlich. Daher können IP-Adressen nicht in schneller Abfolge hinzugefügt werden.

## <a name="front-end-scaling"></a>Front-End-Skalierung ##

Beim horizontalen Hochskalieren Ihrer App Service-Pläne in einer ASEv2 werden die Worker zur Unterstützung der App Service-Pläne automatisch hinzugefügt. Jede ASE wird mit zwei Front-Ends erstellt. Darüber hinaus werden die Front-Ends automatisch mit einer Rate von einem Front-End für je 15 Instanzen in Ihren App Service-Plänen horizontal hochskaliert. Bei 15 Instanzen bedeutet dies beispielsweise, dass Sie drei Front-Ends haben. Wenn Sie auf 30 Instanzen skalieren, haben Sie vier Front-Ends usw.

Diese Anzahl von Front-Ends sollte für die meisten Szenarien mehr als ausreichend sein. Sie können allerdings auch schneller horizontal hochskalieren. Sie können das Verhältnis bis hin zu einem Front-End für je fünf Instanzen ändern. Für das Ändern des Verhältnisses fällt eine Gebühr an. Weitere Informationen finden Sie unter [Azure App Service – Preise][Pricing].

Front-End-Ressourcen sind die HTTP/HTTPS-Endpunkte der ASE. Mit der standardmäßigen Front-End-Konfiguration beträgt der Speicherverbrauch pro Front-End konstant etwa 60 %. Kundenworkloads können nicht in einem Front-End ausgeführt werden. Der wichtigste Skalierungsfaktor für ein Front-End ist die CPU, die hauptsächlich vom HTTPS-Datenverkehr abhängig ist.

## <a name="app-access"></a>App-Zugriff ##

In einer externen ASE unterscheidet sich die für das Erstellen von Apps verwendete Domäne vom mehrinstanzenfähigen App Service. Sie enthält den Namen der ASE. Weitere Informationen zum Erstellen einer externen ASE finden Sie unter [Erstellen einer App Service-Umgebung][MakeExternalASE]. In einer externen ASE folgt der Domänenname dem folgenden Muster: *.&lt;Name der ASE&gt;.p.azurewebsites.net*. Wenn Ihre ASE z.B. den Namen _external-ase_ trägt und Sie in dieser ASE eine App mit dem Namen _contoso_ hosten, erreichen Sie sie über die folgenden URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Die URL contoso.scm.external-ase.p.azurewebsites.net wird für den Zugriff auf die Kudu-Konsole und zum Veröffentlichen Ihrer App mit Web Deploy verwendet. Informationen zur Kudu-Konsole finden Sie in [Kudu-Konsole für Azure App Service][Kudu]. Die Kudu-Konsole bietet eine Webbenutzeroberfläche für das Debuggen, Hochladen und Bearbeiten von Dateien und vieles mehr.

In einer ILB-ASE legen Sie die Domäne und die Bereitstellungszeit fest. Weitere Informationen zum Erstellen einer ILB-ASE finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE]. Wenn Sie den Domänennamen _Ilb-ase.info_ angeben, verwenden die Apps in dieser ASE während der App-Erstellung diese Domäne. Für die App mit dem Namen _contoso_ lauten die URLs wie folgt:

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>Veröffentlichung ##

Wie bei dem mehrinstanzenfähigen App Service können Sie auch folgendermaßen in einer ASE veröffentlichen:

- Webbereitstellung
- FTP
- Continuous Integration
- Drag & Drop in der Kudu-Konsole
- Eine IDE wie Visual Studio, Eclipse oder IntelliJ IDEA

Mit einer externen ASE weisen alle diese Veröffentlichungsoptionen das gleiche Verhalten auf. Weitere Informationen finden Sie unter [Bereitstellen in Azure App Service][AppDeploy]. 

Der wesentliche Unterschied bei der Veröffentlichung betrifft die ILB-ASE. In einer ILB-ASE sind die Veröffentlichungsendpunkte nur über die ILB verfügbar. Die ILB liegt in einer privaten IP-Adresse im ASE-Subnetz im virtuellen Netzwerk. Wenn Sie keinen Netzwerkzugriff auf die ILB haben, können Sie in der betreffenden ASE keine Apps veröffentlichen. Wie in [Erstellen und Verwenden einer ILB-ASE][MakeILBASE] erwähnt, müssen Sie für die Apps im System ein DNS konfigurieren. Dazu gehört der SCM-Endpunkt. Wenn sie nicht ordnungsgemäß definiert sind, ist keine Veröffentlichung möglich. Darüber hinaus müssen Ihre IDEs über Netzwerkzugriff auf den ILB verfügen, um direkt an ihn veröffentlichen zu können.

Internetbasierte CI-Systeme wie GitHub oder Azure DevOps können nicht standardmäßig mit einer ILB-ASE verwendet werden, da der Veröffentlichungsendpunkt nicht über das Internet erreichbar ist. Für Azure DevOps können Sie dieses Problem durch Installation eines selbst gehosteten Release-Agent in Ihrem internen Netzwerk umgehen, wo Zugriff auf den internen Lastenausgleich möglich ist. Alternativ können Sie auch ein CI-System wie Dropbox verwenden, das ein Pullmodell verwendet.

Die Veröffentlichungsendpunkte für die Apps in einer ILB-ASE verwenden die Domäne, mit der die ILB-ASE erstellt wurde. Dies wird im Veröffentlichungsprofil und auf dem Portalblatt der App angezeigt (in **Übersicht** > **Zusammenfassung** sowie in den **Eigenschaften**). 

## <a name="pricing"></a>Preise ##

Die Preis-SKU **Isoliert** wurde ausschließlich für die Verwendung mit ASEv2 erstellt. Alle App Service-Pläne, die in einer ASEv2 gehostet werden, befinden sich in der Preis-SKU „Isoliert“. Die Raten für isolierte App Service-Pläne können für verschiedene Regionen unterschiedlich sein. 

Zusätzlich zum Preis Ihrer App Service-Pläne fällt eine Pauschalgebühr für die eigentliche ASE an. Die Pauschalgebühr ist von der Größe Ihrer ASE unabhängig. Sie fällt für die ASE-Infrastruktur mit der Standardskalierungsrate mit einem zusätzlichen Front-End für jeweils 15 Instanzen des App Service-Plans an.  

Wenn die Standardskalierung von einem Front-End für jeweils 15 Instanzen des App Service-Plans nicht schnell genug ist, können Sie das Verhältnis, nach dem Front-Ends hinzugefügt werden, oder die Größe der Front-Ends anpassen.  Wenn Sie das Verhältnis oder die Größe anpassen, bezahlen Sie für die Front-End-Kerne, die standardmäßig nicht hinzugefügt werden würden.  

Wenn Sie z.B. das Skalierungsverhältnis auf den Wert 10 anpassen, wird für je zehn Instanzen in Ihren App Service-Plänen ein Front-End hinzugefügt. Die Pauschalgebühr deckt ein Skalierungsverhältnis von einem Front-End pro 15 Instanzen ab. Mit einem Skalierungsverhältnis von 10 bezahlen Sie für das dritte Front-End, das für die 10 Instanzen des App Service-Plans hinzugefügt wird, eine Gebühr. Sie müssen nicht dafür bezahlen, wenn Sie 15 Instanzen erreichen, da es in diesem Fall automatisch hinzugefügt wurde.

Wenn Sie die Größe der Front-Ends auf 2 Kerne ändern, aber das Verhältnis nicht anpassen, bezahlen Sie für die zusätzlichen Kerne.  Eine ASE wird mit zwei Front-Ends erstellt – Sie würden also selbst unterhalb des Schwellenwerts für die automatische Skalierung zwei zusätzliche Kerne bezahlen, wenn Sie die Größe auf 2-Kern-Front-Ends erhöhen.

Weitere Informationen finden Sie unter [Azure App Service – Preise][Pricing].

## <a name="delete-an-ase"></a>Löschen einer ASE ##

So löschen Sie eine ASE 

1. Verwenden Sie **Löschen** im oberen Bereich des Blatts **App Service-Umgebung**. 

1. Geben Sie den Namen Ihrer ASE ein, um zu bestätigen, dass Sie sie löschen möchten. Beim Löschen einer ASE wird auch ihr gesamter Inhalt gelöscht. 

    ![Löschen einer ASE][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
