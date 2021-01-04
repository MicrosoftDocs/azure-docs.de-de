---
title: Erstellen einer App Service-Umgebung
description: Erfahren Sie, wie Sie eine App Service-Umgebung erstellen.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 12c9fd43933f9f54e013752c5fe482ce31650536
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238393"
---
# <a name="create-an-app-service-environment"></a>Erstellen einer App Service-Umgebung

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung v3 (Vorschau) behandelt.
> 

Die [App Service-Umgebung (ASE)][Intro] ist eine Bereitstellung von App Service für einen Mandanten, die in Ihre Azure Virtual Network-Instanz (VNET) eingeschleust wird.  ASEv3 unterstützt nur die Bereitstellung von Apps über eine private Adresse in Ihrem VNET. Wenn während der Vorschau eine ASEv3-Instanz erstellt wird, werden dem Abonnement drei Ressourcen hinzugefügt.

- App Service-Umgebung
- Private Azure DNS-Zone
- Privater Endpunkt

Für eine Bereitstellung einer ASE müssen zwei Subnetze verwendet werden.  Ein Subnetz enthält den privaten Endpunkt.  Dieses Subnetz kann für andere Zwecke wie z. B. virtuelle Computer verwendet werden.  Das andere Subnetz wird für ausgehende Aufrufe aus der ASE verwendet.  Dieses Subnetz kann für nichts anderes als die ASE verwendet werden. 

## <a name="before-you-create-your-ase"></a>Bevor Sie Ihre ASE erstellen

Nachdem die ASE erstellt wurde, können Sie Folgendes nicht mehr ändern:

- Standort
- Subscription
- Ressourcengruppe
- Verwendetes Azure Virtual Network (VNET)
- Verwendete Subnetze
- Subnetzgröße
- Name Ihrer ASE

Das ausgehende Subnetz muss groß genug sein, um die maximale Größe zu speichern, die Sie für Ihre ASE skalieren. Wählen Sie ein ausreichend großes Subnetz zur Unterstützung ihrer maximalen Skalierungsanforderungen aus, da es nach der Erstellung nicht mehr geändert werden kann. Die empfohlene Größe ist /24 mit 256 Adressen.

Nachdem die ASE erstellt wurde, können Sie ihr Apps hinzufügen. Wenn Ihre ASEv3 keine App Service-Pläne enthält, wird so abgerechnet, als hätten Sie eine Instanz eines I1v2-App Service-Plans in Ihrer ASE.  

Die ASEv3 wird nur in ausgewählten Regionen angeboten. Weitere Regionen werden während des Verlaufs der Vorschau zur allgemeinen Verfügbarkeit hinzugefügt. 

## <a name="creating-an-ase-in-the-portal"></a>Erstellen einer ASE im Portal

1. Um eine ASEv3 zu erstellen, suchen Sie im Marketplace nach **App Service-Umgebung (Vorschau)** .  
2. Grundlegendes:  Wählen Sie das Abonnement aus, wählen Sie die Ressourcengruppe aus, bzw. erstellen Sie sie, und geben Sie den Namen Ihrer ASE ein.  Der ASE-Name wird auch für das Domänensuffix Ihrer ASE verwendet.  Wenn Ihr ASE-Name *contoso* ist, lautet das Domänensuffix *contoso.appserviceenvironment.net*.  Dieser Name wird automatisch in Ihrer privaten Azure DNS-Zone festgelegt, die von dem VNET verwendet wird, in dem die ASE bereitgestellt wird. 

    ![App Service-Umgebung: Registerkarte „Grundlagen“ zur Erstellung](./media/creation/creation-basics.png)

3. Hosting: Wählen Sie „Betriebssystemeinstellung“ und „Hostgruppenbereitstellung“ aus. „Betriebssystemeinstellung“ gibt das Betriebssystem an, das Sie anfänglich für Ihre Apps in dieser ASE verwenden werden. Sie können nach der ASE-Erstellung Apps des anderen Betriebssystems hinzufügen. „Hostgruppenbereitstellung“ wird verwendet, um dedizierte Hardware auszuwählen. Mit ASEv3 können Sie die Option „Aktiviert“ auswählen und dann zu dedizierter Hardware gelangen. Ihnen wird der gesamte dedizierte Host mit der ASE-Erstellung und dann ein reduzierter Preis für Ihre App Service-Plan-Instanzen in Rechnung gestellt. 

    ![App Service-Umgebung: Hostingauswahl](./media/creation/creation-hosting.png)

4. Netzwerk:  Wählen oder erstellen Sie Ihr VNET, wählen oder erstellen Sie Ihr eingehendes Subnetz, wählen oder erstellen Sie Ihr ausgehendes Subnetz. Jedes für den Ausgang verwendete Subnetz muss leer sein und an Microsoft.Web/hostingEnvironments delegiert werden. Wenn Sie das Subnetz über das Portal erstellen, wird das Subnetz automatisch für Sie delegiert.

    ![App Service-Umgebung: Netzwerkauswahl](./media/creation/creation-networking.png)

5. Überprüfen und erstellen: Überprüfen Sie, ob die Konfiguration korrekt ist, und wählen Sie „Erstellen“ aus. Die Erstellung Ihrer ASE dauert ungefähr eine Stunde. 

    ![App Service-Umgebung: Überprüfen und erstellen](./media/creation/creation-review.png)

Nachdem die Erstellung Ihrer ASE abgeschlossen ist, können Sie sie als Speicherort auswählen, wenn Sie Ihre Apps erstellen. Weitere Informationen zum Erstellen von Apps in der neuen ASE finden Sie unter [Verwenden einer App Service-Umgebung][UsingASE].

## <a name="os-preference"></a>Betriebssystemeinstellung
In einer ASE können Sie über Windows-Apps, Linux-Apps oder beides verfügen. In ASEv2 wird für das bei der Erstellung ausgewählte anfänglich bevorzugte Betriebssystem die Hochverfügbarkeitsinfrastruktur hinzugefügt. Wenn Sie Apps des anderen Betriebssystems hinzufügen möchten, erstellen Sie die Apps einfach wie gewohnt, und wählen Sie das gewünschte Betriebssystem aus. In ASEv3 hat dies keine Auswirkung auf das Back-End-Verhalten.  

## <a name="dedicated-hosts"></a>Dedizierte Hosts
Die ASE wird normalerweise auf VMs bereitgestellt, die auf einem mehrinstanzfähigen Hypervisor bereitgestellt werden. Wenn Sie auf dedizierten Systemen (Hardware eingeschlossen) bereitstellen müssen, können Sie Ihre ASE auf dedizierten Hosts bereitstellen. In der anfänglichen ASEv3-Vorschau sind dedizierte Hosts in einem Paar enthalten. Jeder dedizierte Host befindet sich in einer separaten Verfügbarkeitszone, wenn die Region dies zulässt. Dedizierte hostbasierte ASE-Bereitstellungen werden anders berechnet als normal. Es fällt eine Gebühr für den dedizierten Host und dann für jede App Service-Plan-Instanz eine weitere Gebühr an.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
