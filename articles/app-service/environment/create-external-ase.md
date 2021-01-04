---
title: Erstellen einer externen ASE
description: Erfahren Sie, wie Sie eine App Service-Umgebung mit einer App darin oder eine eigenständige (leere) ASE erstellen.
author: ccompy
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c953c31792b8d01199d409cbd91124138a6ebb15
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927446"
---
# <a name="create-an-external-app-service-environment"></a>Erstellen einer externen App Service-Umgebung

Die Azure App Service-Umgebung ist eine Bereitstellung von Azure App Service in einem Subnetz in einem virtuellen Azure-Netzwerk (VNET).

> [!NOTE]
> Jede App Service-Umgebung verfügt über eine virtuelle IP-Adresse (VIP), die für den Kontakt mit der App Service-Umgebung verwendet werden kann.

Eine App Service-Umgebung (App Service Environment, ASE) kann auf zwei Arten bereitgestellt werden:

- Mit einer VIP unter einer externen IP-Adresse, die häufig als „externe ASE“ bezeichnet wird
- Mit der VIP unter einer internen IP-Adresse, die häufig als ILB-ASE bezeichnet wird, da der interne Endpunkt ein interner Load Balancer (ILB) ist.

In diesem Artikel wird gezeigt, wie Sie eine externe ASE erstellen. Eine Übersicht über die ASE finden Sie unter [Einführung in die App Service-Umgebung][Intro]. Informationen zum Erstellen einer ILB-ASE finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Bevor Sie Ihre ASE erstellen

Nach dem Erstellen einer ASE können Sie folgende Elemente nicht mehr ändern:

- Position
- Subscription
- Resource group
- Verwendetes VNET
- Verwendetes Subnetz
- Subnetzgröße

> [!NOTE]
> Stellen Sie beim Auswählen eines virtuellen Netzwerks und Angeben eines Subnetzes sicher, dass die jeweilige Größe in Bezug auf zukünftige Wachstums- und Skalierungsanforderungen ausreichend hoch gewählt ist. Es wird eine Größe von `/24` mit 256 Adressen empfohlen.
>

## <a name="three-ways-to-create-an-ase"></a>Drei Möglichkeiten zum Erstellen einer ASE

Es gibt drei Möglichkeiten, eine ASE zu erstellen:

- **Beim Erstellen eines App Service-Plans**. Bei dieser Methode werden ASE und App Service-Plan in einem Schritt erstellt.
- **Als eigenständige Aktion**. Bei dieser Methode wird eine eigenständige ASE erstellt, also eine ASE, die nichts enthält. Diese Methode ist ein erweiterter Prozess zum Erstellen einer ASE. Sie wird verwendet, um eine ASE mit einem ILB zu erstellen.
- **Aus einer Azure Resource Manager-Vorlage**. Diese Methode eignet sich für fortgeschrittene Benutzer. Weitere Informationen finden Sie unter [Erstellen einer ASE aus einer Vorlage][MakeASEfromTemplate].

Eine externe ASE verfügt über eine öffentliche VIP – das bedeutet, dass sämtlicher HTTP-/HTTPS-Datenverkehr zu den Apps in der ASE auf eine über das Internet zugängliche IP-Adresse trifft. Eine ASE mit einem ILB verfügt über eine IP-Adresse aus dem von der ASE verwendeten Subnetz. Die Apps, die in einer ILB-ASE gehostet werden, sind nicht für den direkten Zugriff aus dem Internet verfügbar.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Gemeinsames Erstellen einer ASE und eines App Service-Plans

Der App Service-Plan ist ein Container mit Apps. Wenn Sie in App Service eine App erstellen, müssen Sie einen App Service-Plan auswählen oder erstellen. App Service-Umgebungen enthalten App Service-Pläne, und App Service-Pläne enthalten Apps.

Um eine ASE zu erstellen, während Sie einen App Service-Plan erstellen, gehen Sie folgendermaßen vor:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Ressource erstellen** > **Web + Mobil** > **Web-App**.

    ![Screenshot: Azure-Portal, in dem „Web + Mobil“ im Azure Marketplace ausgewählt und auf der rechten Seite der Bildschirm zum Erstellen einer neuen Web-App geöffnet ist][1]

2. Wählen Sie Ihr Abonnement aus. App und ASE werden im gleichen Abonnement erstellt.

3. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie. Mithilfe von Ressourcengruppen können Sie verwandte Azure-Ressourcen als Einheit verwalten. Ressourcengruppen sind auch nützlich, wenn Sie Regeln für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Ihre Apps einrichten. Weitere Informationen finden Sie unter [Übersicht über Azure Resource Manager][ARMOverview].

4. Wählen Sie Ihr Betriebssystem aus (Windows, Linux oder Docker). 

5. Klicken Sie auf den App Service-Plan, und wählen Sie anschließend die Option **Neu erstellen**. Linux-Web-Apps und Windows-Web-Apps können sich nicht im selben App Service-Plan befinden, jedoch in derselben App Service-Umgebung. 

    ![Screenshot: Azure-Portal, in dem die Bereiche „Web-App“, „App Service-Plan“ und „Neuer App Service-Plan“ geöffnet sind][2]

6. Wählen Sie in der Dropdownliste **Speicherort** die Region aus, in der Sie die ASE erstellen möchten. Wenn Sie eine vorhandene ASE auswählen, wird keine neue ASE erstellt. Der App Service-Plan wird in der von Ihnen ausgewählten ASE erstellt. 

7. Wählen Sie **Tarif** , und wählen Sie eine der SKUs in der Preisstufe **Isolated** aus. Wenn Sie eine SKU vom Typ **Isolated** und einen Speicherort auswählen, der keine ASE ist, wird an diesem Speicherort eine neue ASE erstellt. Um den Erstellungsprozess für eine ASE zu starten, klicken Sie auf **Auswählen**. Eine **Isolated** -SKU ist nur zusammen mit einer ASE verfügbar. Es ist außerdem nicht möglich, einen anderen SKU-Tarif als **Isolated** in einer ASE zu verwenden. 

    ![Auswahl des Tarifs][3]

8. Geben Sie den Namen für Ihre ASE ein. Dieser Name wird im aufrufbaren Namen für Ihre Apps verwendet. Wenn der Name der ASE _appsvcenvdemo_ ist, lautet der Domänenname *.appsvcenvdemo.p.azurewebsites.net*. Wenn Sie eine App namens *mytestapp* erstellen, kann sie unter der Adresse „mytestapp.appsvcenvdemo.p.azurewebsites.net“ aufgerufen werden. Sie dürfen keine Leerzeichen im Namen verwenden. Bei Verwendung von Großbuchstaben wird der entsprechende Domänenname dennoch vollständig in Kleinbuchstaben geschrieben.

    ![Name für den neuen App Service-Plan][4]

9. Geben Sie die Informationen für Ihr virtuelles Azure-Netzwerk an. Wählen Sie entweder **Neu erstellen** oder **Vorhandenes auswählen**. Sie können nur dann ein vorhandenes VNET auswählen, wenn Sie in der ausgewählten Region über ein VNET verfügen. Wenn Sie **Neu erstellen** ausgewählt haben, geben Sie einen Namen für das VNET ein. Es wird ein neues Resource Manager-VNET mit diesem Namen erstellt. Das VNET verwendet den Adressraum `192.168.250.0/23` in der ausgewählten Region. Bei Auswahl von **Vorhandene auswählen** gehen Sie wie folgt vor:

    a. Wählen Sie den Adressblock des virtuellen Netzwerks aus, falls Sie mehr als ein VNET verwenden.

    b. Geben Sie einen neuen Subnetznamen an.

    c. Wählen Sie die Größe des Subnetzes aus. *Die Größe sollte auf einen ausreichend großen Wert festgelegt werden, um das zukünftige Wachstum Ihrer ASE abzudecken.* Die empfohlene Größe ist `/24` mit 256 Adressen zur Verarbeitung einer ASE maximaler Größe. `/28` ist beispielsweise nicht zu empfehlen, da nur 16 Adressen verfügbar sind. In der Infrastruktur werden mindestens sieben und im Azure-Netzwerk weitere fünf Adressen verwendet. In einem `/28`-Subnetz können Sie höchstens vier Instanzen des App Service-Plans für eine externe ASE und nur drei Instanzen des App Service-Plans für eine ILB-ASE skalieren.

    d. Wählen Sie den IP-Bereich des Subnetzes aus.

10. Wählen Sie **Erstellen** , um die ASE zu erstellen. Dieser Prozess erstellt auch den App Service-Plan und die App. Die ASE, der App Service-Plan und die App befinden sich im gleichen Abonnement und in der gleichen Ressourcengruppe. Wenn für Ihre ASE eine separate Ressourcengruppe erforderlich ist oder Sie eine ILB-ASE benötigen, führen Sie die Schritte zum Erstellen einer eigenständigen ASE aus.

## <a name="create-an-ase-and-a-linux-web-app-using-a-custom-docker-image-together"></a>Gemeinsames Erstellen einer ASE und einer Linux-Web-App mit einem benutzerdefinierten Docker-Image

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **Ressource erstellen** > **Web + Mobil** > **Web-App für Container** aus. 

    ![Screenshot: Azure-Portal, in dem „Web + Mobil“ im Azure Marketplace ausgewählt und auf der rechten Seite der Bereich „Web-App für Container“ geöffnet ist][7]

1. Wählen Sie Ihr Abonnement aus. App und ASE werden im gleichen Abonnement erstellt.

1. Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie sie. Mithilfe von Ressourcengruppen können Sie verwandte Azure-Ressourcen als Einheit verwalten. Ressourcengruppen sind auch nützlich, wenn Sie Regeln für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Ihre Apps einrichten. Weitere Informationen finden Sie unter [Übersicht über Azure Resource Manager][ARMOverview].

1. Klicken Sie auf den App Service-Plan, und wählen Sie anschließend die Option **Neu erstellen**. Linux-Web-Apps und Windows-Web-Apps können sich nicht im selben App Service-Plan befinden, jedoch in derselben App Service-Umgebung. 

    ![Screenshot: Azure-Portal, in dem die Bereiche „Web-App für Container“, „App Service-Plan“ und „Neuer App Service-Plan“ geöffnet sind][8]

1. Wählen Sie in der Dropdownliste **Speicherort** die Region aus, in der Sie die ASE erstellen möchten. Wenn Sie eine vorhandene ASE auswählen, wird keine neue ASE erstellt. Der App Service-Plan wird in der von Ihnen ausgewählten ASE erstellt. 

1. Wählen Sie **Tarif** , und wählen Sie eine der SKUs in der Preisstufe **Isolated** aus. Wenn Sie eine SKU vom Typ **Isolated** und einen Speicherort auswählen, der keine ASE ist, wird an diesem Speicherort eine neue ASE erstellt. Um den Erstellungsprozess für eine ASE zu starten, klicken Sie auf **Auswählen**. Eine **Isolated** -SKU ist nur zusammen mit einer ASE verfügbar. Es ist außerdem nicht möglich, einen anderen SKU-Tarif als **Isolated** in einer ASE zu verwenden. 

    ![Auswahl des Tarifs][3]

1. Geben Sie den Namen für Ihre ASE ein. Dieser Name wird im aufrufbaren Namen für Ihre Apps verwendet. Wenn der Name der ASE _appsvcenvdemo_ ist, lautet der Domänenname *.appsvcenvdemo.p.azurewebsites.net*. Wenn Sie eine App namens *mytestapp* erstellen, kann sie unter der Adresse „mytestapp.appsvcenvdemo.p.azurewebsites.net“ aufgerufen werden. Sie dürfen keine Leerzeichen im Namen verwenden. Bei Verwendung von Großbuchstaben wird der entsprechende Domänenname dennoch vollständig in Kleinbuchstaben geschrieben.

    ![Name für den neuen App Service-Plan][4]

1. Geben Sie die Informationen für Ihr virtuelles Azure-Netzwerk an. Wählen Sie entweder **Neu erstellen** oder **Vorhandenes auswählen**. Sie können nur dann ein vorhandenes VNET auswählen, wenn Sie in der ausgewählten Region über ein VNET verfügen. Wenn Sie **Neu erstellen** ausgewählt haben, geben Sie einen Namen für das VNET ein. Es wird ein neues Resource Manager-VNET mit diesem Namen erstellt. Das VNET verwendet den Adressraum `192.168.250.0/23` in der ausgewählten Region. Bei Auswahl von **Vorhandene auswählen** gehen Sie wie folgt vor:

    a. Wählen Sie den Adressblock des virtuellen Netzwerks aus, falls Sie mehr als ein VNET verwenden.

    b. Geben Sie einen neuen Subnetznamen an.

    c. Wählen Sie die Größe des Subnetzes aus. *Die Größe sollte auf einen ausreichend großen Wert festgelegt werden, um das zukünftige Wachstum Ihrer ASE abzudecken.* Die empfohlene Größe ist `/24` mit 128 Adressen zur Verarbeitung einer ASE maximaler Größe. `/28` ist beispielsweise nicht zu empfehlen, da nur 16 Adressen verfügbar sind. In der Infrastruktur werden mindestens sieben und im Azure-Netzwerk weitere fünf Adressen verwendet. In einem `/28`-Subnetz können Sie höchstens vier Instanzen des App Service-Plans für eine externe ASE und nur drei Instanzen des App Service-Plans für eine ILB-ASE skalieren.

    d. Wählen Sie den IP-Bereich des Subnetzes aus.

1.  Wählen Sie „Container konfigurieren“ aus.
    * Geben Sie den Namen Ihres benutzerdefinierten Images ein. (Sie können die Azure-Containerregistrierung, einen Docker-Hub und Ihre eigene private Registrierung verwenden.) Wenn Sie keine eigenen benutzerdefinierten Container verwenden möchten, können Sie einfach Ihren Code bereitstellen und ein integriertes Image mit App Service unter Linux gemäß den oben genannten Anweisungen verwenden. 

    ![Container konfigurieren][9]

1. Wählen Sie **Erstellen** , um die ASE zu erstellen. Dieser Prozess erstellt auch den App Service-Plan und die App. Die ASE, der App Service-Plan und die App befinden sich im gleichen Abonnement und in der gleichen Ressourcengruppe. Wenn für Ihre ASE eine separate Ressourcengruppe erforderlich ist oder Sie eine ILB-ASE benötigen, führen Sie die Schritte zum Erstellen einer eigenständigen ASE aus.


## <a name="create-an-ase-by-itself"></a>Erstellen einer eigenständigen ASE

Wenn Sie eine eigenständige ASE erstellen, enthält sie keinerlei Elemente. Für eine leere ASE fällt trotzdem eine monatliche Gebühr für die Infrastruktur an. Führen Sie diese Schritte aus, um eine ASE mit einem ILB oder eine ASE in einer eigenen Ressourcengruppe zu erstellen. Nach dem Erstellen der ASE können Sie mit der ganz normalen Vorgehensweise Apps darin erstellen. Wählen Sie Ihre neue ASE als Standort aus.

1. Suchen Sie im Azure Marketplace nach **App Service-Umgebung** , oder wählen Sie **Neu** > **Web + Mobil** > **App Service-Umgebung** aus. 

1. Geben Sie den Namen Ihrer ASE ein. Dieser Name wird für die in der ASE erstellten Apps verwendet. Wenn der Name *mynewdemoase* lautet, ist der Name der Unterdomäne *.mynewdemoase.p.azurewebsites.net*. Wenn Sie eine App namens *mytestapp* erstellen, kann sie unter der Adresse „mytestapp.mynewdemoase.p.azurewebsites.net“ aufgerufen werden. Sie dürfen keine Leerzeichen im Namen verwenden. Bei Verwendung von Großbuchstaben wird der entsprechende Domänenname dennoch vollständig in Kleinbuchstaben geschrieben. Bei Verwendung eines ILB wird Ihr ASE-Name nicht in Ihrer Unterdomäne verwendet, sondern stattdessen explizit während der ASE-Erstellung angegeben.

    ![Benennung der ASE][5]

1. Wählen Sie Ihr Abonnement aus. Dieses Abonnement ist auch dasjenige, das alle Apps in der ASE verwenden. Sie können Ihre ASE nicht in einem VNET platzieren, das sich in einem anderen Abonnement befindet.

1. Wählen eine neue Ressourcengruppe aus, oder geben Sie eine an. Die Ressourcengruppe, die für Ihre ASE verwendet wird, muss identisch mit derjenigen sein, die für das VNET verwendet wird. Wenn Sie ein vorhandenes VNET auswählen, wird die Auswahl der Ressourcengruppe für Ihre ASE entsprechend der Auswahl für das VNET aktualisiert. *Sie können eine ASE mit einer Ressourcengruppe erstellen, die sich von der VNET-Ressourcengruppe unterscheidet, wenn Sie eine Resource Manager-Vorlage verwenden.* Informationen zum Erstellen einer ASE mit einer Vorlage finden Sie unter [Erstellen einer ASE aus einer Vorlage][MakeASEfromTemplate].

    ![Auswahl der Ressourcengruppe][6]

1. Wählen Sie das VNET und den Standort aus. Sie können ein neues VNET erstellen oder ein vorhandenes VNET auswählen: 

    * Wenn Sie ein neues VNET erstellen, können Sie einen Namen und Speicherort angeben. 
    
    * Das neue VNET hat den Adressbereich 192.168.250.0/23 und ein Subnetz namens „default“. Das Subnetz ist als 192.168.250.0/24 definiert. Sie können nur ein Resource Manager-VNET auswählen. Die Auswahl des **VIP-Typs** bestimmt, ob auf Ihre ASE ein direkter Zugriff aus dem Internet möglich ist (extern) oder ob ein interner Load Balancer (ILB) verwendet wird. Mehr hierzu erfahren Sie unter [Erstellen und Verwenden eines internen Lastenausgleichs mit einer App Service-Umgebung][MakeILBASE]. 

      * Wenn Sie **Extern** als **VIP-Typ** auswählen, können Sie angeben, mit wie vielen externen IP-Adressen für IP-basierte SSL-Zwecke das System erstellt wird. 
    
      * Wenn Sie **Intern** als **VIP-Typ** auswählen, müssen Sie die Domäne angeben, die Ihre ASE verwendet. Sie können eine ASE in einem VNET bereitstellen, das öffentliche oder private Adressbereiche verwendet. Um ein VNET mit einem öffentlichen Adressbereich zu verwenden, müssen Sie das VNET vorab erstellen. 
    
    * Wenn Sie ein vorhandenes VNET auswählen, wird bei der Erstellung der ASE ein neues Subnetz erstellt. *Ein vorab erstelltes Subnetz kann nicht im Portal verwendet werden. Sie können eine ASE mit einem vorhandenen Subnetz erstellen, wenn Sie eine Resource Manager-Vorlage verwenden.* Informationen zum Erstellen einer ASE mit einer Vorlage finden Sie unter [Erstellen einer ASE aus einer Vorlage][MakeASEfromTemplate].

## <a name="app-service-environment-v1"></a>App Service-Umgebung v1

Sie können weiterhin Instanzen der ersten Version der App Service-Umgebung (ASEv1) erstellen. Um diesen Prozess zu starten, suchen Sie im Marketplace nach **App Service-Umgebung v1**. Eine solche ASE erstellen Sie auf die gleiche Weise wie eine eigenständige ASE. Nach Abschluss der Einrichtung verfügt Ihre ASEv1 über zwei Front-Ends und zwei Worker. Bei ASEv1 müssen Sie die Front-Ends und Worker verwalten. Diese werden beim Erstellen Ihrer App Service-Pläne nicht automatisch hinzugefügt. Die Front-Ends agieren als HTTP-/HTTPS-Endpunkte und senden Datenverkehr an die Worker. Die Worker sind die Rollen, die Ihre Apps hosten. Sie können die Anzahl von Front-Ends und Workern nach dem Erstellen der ASE anpassen. 

Weitere Informationen zu ASEv1 finden Sie unter [Einführung in die App Service-Umgebung v1][ASEv1Intro]. Weitere Informationen zur Skalierung, Verwaltung und Überwachung von ASEv1 finden Sie unter [Konfigurieren einer App Service-Umgebung][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png
[7]: ./media/how_to_create_an_external_app_service_environment/createexternalase-createwafc.png
[8]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreatewafc.png
[9]: ./media/how_to_create_an_external_app_service_environment/createexternalase-configurecontainer.png



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
