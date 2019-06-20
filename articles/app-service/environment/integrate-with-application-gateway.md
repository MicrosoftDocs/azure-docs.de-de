---
title: Integrieren der ILB-App Service-Umgebung in ein Application Gateway – Azure
description: Exemplarische Vorgehensweise zum Integrieren einer App in Ihrer ILB-App Service-Umgebung in ein Application Gateway
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ea46b5e57e4e508a3311de8633ae61d346b574eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764848"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrieren Ihrer ILB-App Service-Umgebung in ein Azure Application Gateway #

Die [Azure App Service-Umgebung](./intro.md) ist eine Bereitstellung von Azure App Service in einem Subnetz im virtuellen Azure-Netzwerk eines Kunden. Sie kann für den App-Zugriff über einen öffentlichen oder privaten Endpunkt bereitgestellt werden. Die Bereitstellung der App Service-Umgebung mit einem privaten Endpunkt (d.h. mit einem internen Lastenausgleich) wird als ILB-App Service-Umgebung bezeichnet.  

Web Application Firewalls tragen zum Schutz Ihrer Webanwendungen bei, indem sie eingehenden Webdatenverkehr untersuchen und die Einschleusung von SQL-Befehlen, websiteübergreifendes Scripting, das Hochladen von Schadsoftware sowie DDoS-Angriffe und andere Angriffe unterbinden. WAF überprüft auch zur Verhinderung von Datenverlust (Data Loss Prevention, DLP) die Antworten von den Back-End-Webservern. Sie erhalten ein WAF-Gerät im Azure-Marketplace, oder verwenden Sie das [Azure Application Gateway][appgw].

Das Azure Application Gateway ist eine virtuelle Anwendung, die Lastenausgleich in Schicht 7, SSL-Abladung und WAF-Schutz (Web Application Firewall) bietet. Es kann eine öffentliche IP-Adresse überwachen und Datenverkehr an Ihren Anwendungsendpunkt routen. In den folgenden Informationen wird die Vorgehensweise beim Integrieren eines WAF-konfigurierten Application Gateways mit einer App in einer ILB-App Service-Umgebung beschrieben.  

Die Integration des Application Gateways mit der ILB-App Service-Umgebung erfolgt auf App-Ebene. Wenn Sie das Application Gateway mit der ILB-App Service-Umgebung konfigurieren, erfolgt dies für bestimmte Apps in Ihrer ILB-App Service-Umgebung. Dieses Verfahren ermöglicht es, sichere mehrinstanzenfähige Anwendungen in einer einzelnen ILB App Service-Umgebung zu hosten.  

![Application Gateway, das auf eine App in einer ILB-App Service-Umgebung verweist][1]

In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

* Erstellen eines Azure Application Gateways.
* Konfigurieren des Application Gateways zum Verweisen auf eine App in Ihrer ILB-App Service-Umgebung.
* Konfigurieren Ihrer App, sodass der benutzerdefinierte Domänenname anerkannt wird
* Bearbeiten des öffentlichen DNS-Hostnamens, der auf Ihr Application Gateway verweist

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Ihr Application Gateway in die ILB-App Service-Umgebung zu integrieren:

* Eine ILB-App Service-Umgebung
* Eine App, die in der ILB-App Service-Umgebung ausgeführt wird
* Einen über das Internet routingfähigen Domänennamen, der mit Ihrer App in der ILB-App Service-Umgebung verwendet wird
* Die ILB-Adresse, die Ihre ILB-App Service-Umgebung verwendet. Diese Informationen sind im Portal für die App Service-Umgebung unter **Einstellungen** > **IP-Adressen** zu finden:

    ![Beispielliste von IP-Adressen, die von der ILB-App Service-Umgebung verwendet werden][9]
    
* Einen öffentlichen DNS-Namen, über den später auf Ihr Application Gateway verwiesen wird. 

Ausführliche Informationen zum Erstellen einer ILB-App Service-Umgebung finden Sie unter [Erstellen und Verwenden einer ILB-App Service-Umgebung][ilbase].

In diesem Artikel wird davon ausgegangen, dass ein Application Gateway in demselben virtuellen Azure-Netzwerk vorhanden sein soll, in dem die App Service-Umgebung bereitgestellt wird. Wählen Sie vor dem Erstellen des Application Gateways ein Subnetz aus, das Sie als Host für das Application Gateway verwenden möchten, oder erstellen Sie es. 

Sie sollten ein anderes Subnetz als GatewaySubnet verwenden. Wenn Sie das Application Gateway in GatewaySubnet ablegen, können Sie später kein Gateway für das virtuelle Netzwerk erstellen. 

Außerdem können Sie das Gateway nicht dem Subnetz hinzufügen, das Ihre ILB-App Service-Umgebung verwendet. In diesem Subnetz kann sich ausschließlich die App Service-Umgebung befinden.

## <a name="configuration-steps"></a>Konfigurationsschritte ##

1. Wechseln Sie im Azure-Portal zu **Neu** > **Netzwerk** > **Application Gateway**.

2. Gehen Sie im Bereich **Grundeinstellungen** folgendermaßen vor:

   a. Geben Sie unter **Name** den Namen des Subnetzes für das Application Gateway ein.

   b. Wählen Sie unter **Ebene** die Option **WAF** aus.

   c. Wählen Sie für **Abonnement** dasselbe Abonnement aus, das für das virtuelle Netzwerk der App Service-Umgebung verwendet wird.

   d. Erstellen Sie unter **Ressourcengruppe** die Ressourcengruppe, oder wählen Sie eine aus.

   e. Wählen Sie unter **Speicherort** den Speicherort des virtuellen Netzwerks der App Service-Umgebung aus.

   ![Grundlagen zur Erstellung eines neuen Application Gateways][2]

3. Gehen Sie im Bereich **Einstellungen** folgendermaßen vor:

   a. Wählen Sie unter **Virtuelles Netzwerk** das virtuelle Netzwerk der App Service-Umgebung aus.

   b. Wählen Sie unter **Subnetz** das Subnetz aus, in dem das Application Gateway bereitgestellt werden soll. Verwenden Sie nicht GatewaySubnet, da es die Erstellung von VPN-Gateways verhindert.

   c. Wählen Sie unter **IP-Adresstyp** die Option **Öffentlich** aus.

   d. Wählen Sie unter **Öffentliche IP-Adresse** eine öffentliche IP-Adresse aus. Wenn keine öffentliche IP-Adresse vorhanden ist, erstellen Sie jetzt eine.

   e. Wählen Sie unter **Protokoll** eine der Optionen **HTTP** oder **HTTPS** aus. Bei der Konfiguration für HTTPS müssen Sie ein PFX-Zertifikat bereitstellen.

   f. Unter **Web Application Firewall** können Sie die Firewall aktivieren und nach Bedarf entweder **Erkennung** oder **Prävention** festlegen.

   ![Einstellungen zur Erstellung eines neuen Application Gateways][3]
    
4. Überprüfen Sie die Einstellungen im Bereich **Zusammenfassung**, und wählen Sie **OK** aus. Es kann etwas mehr als 30 Minuten dauern, bis die Einrichtung Ihres Application Gateways abgeschlossen ist.  

5. Nachdem das Application Gateway eingerichtet wurde, wechseln Sie zum Application Gateway-Portal. Wählen Sie **Back-End-Pool** aus. Fügen Sie die ILB-Adresse für Ihre ILB-App Service-Umgebung hinzu.

   ![Konfigurieren des Back-End-Pools][4]

6. Wählen Sie nach Abschluss der Konfiguration Ihres Back-End-Pools die Option **Integritätstests** aus. Erstellen Sie einen Integritätstest für den Domänennamen, den Sie für Ihre App verwenden möchten. 

   ![Konfigurieren von Integritätstests][5]
    
7. Wählen Sie nach Abschluss der Konfiguration Ihrer Integritätstests die Option **HTTP-Einstellungen** aus. Bearbeiten Sie die vorhandenen Einstellungen, und wählen Sie die Option **Benutzerdefinierten Test verwenden** und dann den von Ihnen konfigurierten Test aus.

   ![Konfigurieren von HTTP-Einstellungen][6]
    
8. Wechseln Sie zum Abschnitt **Übersicht** des Application Gateways, und kopieren Sie die öffentliche IP-Adresse, die von Ihrem Application Gateway verwendet wird. Legen Sie diese IP-Adresse als A-Datensatz für Ihren App-Domänennamen fest, oder verwenden Sie den DNS-Namen für diese Adresse im CNAME-Datensatz. Es ist einfacher, die öffentliche IP-Adresse auszuwählen und sie von der Benutzeroberfläche der öffentlichen IP-Adresse zu kopieren als über den Link im Abschnitt **Übersicht** des Application Gateways. 

   ![Application Gateway-Portal][7]

9. Legen Sie den benutzerdefinierten Domänennamen für Ihre App in Ihrer ILB-App Service-Umgebung fest. Wechseln Sie im Portal zu Ihrer App, und wählen Sie unter **Einstellungen** die Option **Benutzerdefinierte Domänen** aus.

   ![Festlegen des benutzerdefinierten Domänennamens für die App][8]

Informationen zum Festlegen der benutzerdefinierten Domänennamen für Ihre Web-Apps finden Sie im Artikel [Festlegen benutzerdefinierter Domänennamen für Ihre Web-App][custom-domain]. Für eine App in einer ILB-App Service-Umgebung erfolgt jedoch keine Überprüfung des Domänennamens. Da Sie Besitzer des DNS sind, mit dem die App-Endpunkte verwaltet werden, können Sie dort ablegen, was Sie möchten. Der benutzerdefinierte Domänenname, den Sie in diesem Fall hinzufügen, muss sich nicht in Ihrem DNS befinden, er muss jedoch weiterhin mit Ihrer App konfiguriert werden. 

Nachdem die Einrichtung abgeschlossen ist und Sie eine kurze Zeit gewartet haben, bis die DNS-Änderungen verbreitet wurden, können Sie über den erstellten benutzerdefinierten Domänennamen auf Ihre App zugreifen. 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
