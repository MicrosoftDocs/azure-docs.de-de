---
title: 'Tutorial: Bereitstellen und Konfigurieren von VMware HCX'
description: Erfahren Sie, wie Sie eine VMware HCX-Lösung für Ihre private Azure VMware Solution-Cloud bereitstellen und konfigurieren.
ms.topic: tutorial
ms.date: 11/25/2020
ms.openlocfilehash: a792f8dbc646f61377cf0a88e1a6e386340f23e8
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357890"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Bereitstellen und Konfigurieren von VMware HCX

In diesem Artikel erfahren Sie, wie Sie den lokalen VMware HCX-Connector für Ihre private Azure VMware Solution-Cloud bereitstellen und konfigurieren. Mit VMware HCX können Sie Ihre VMware-Workloads zu Azure VMware Solution und zu anderen verbundenen Standorten über verschiedene Migrationstypen migrieren. Da Azure VMware Solution den HCX-Cloud-Manager bereitstellt und konfiguriert, müssen Sie den HCX-Connector in Ihr lokales VMware-Rechenzentrum herunterladen und ihn aktivieren und konfigurieren.

VMware HCX Advanced Connector wird in Azure VMware Solution vorab bereitgestellt. Er unterstützt bis zu drei Standortverbindungen (lokal-zu-Cloud oder Cloud-zu-Cloud). Wenn Sie mehr als drei Standortverbindungen benötigen, senden Sie eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support), um das Add-On [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/) zu aktivieren. Das Add-On befindet sich derzeit in der Vorschauphase. 

>[!TIP]
>Obwohl das Tool VMware Configuration Maximum angibt, dass die maximale Anzahl von Standortpaaren zwischen dem lokalen Connector und Cloud Manager gleich 25 ist, schränkt die Lizenzierung dies auf 3 für Advanced und 10 für Enterprise Edition ein.

>[!NOTE]
>VMware HCX Enterprise ist mit Azure VMware Solution als Vorschaudienst verfügbar. Der Dienst ist kostenlos und unterliegt den Nutzungsbedingungen für einen Vorschaudienst. Wenn der VMware HCX Enterprise-Dienst allgemein verfügbar ist, erhalten Sie eine Benachrichtigung, dass in 30 Tagen die Abrechnung umgestellt wird. Sie haben auch die Möglichkeit, den Dienst zu deaktivieren bzw. abzuwählen. Es gibt keinen einfachen Downgradepfad von VMware HCX Enterprise zu VMware HCX Advanced. Wenn Sie ein Downgrade durchführen möchten, ist eine erneute Bereitstellung erforderlich, was mit Downtime einhergeht.

Machen Sie sich zuerst mit den [Vorbereitungen](#before-you-begin), der [erforderlichen Softwareversionen](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) und den [Voraussetzungen](#prerequisites) vertraut. 

Danach werden alle erforderlichen Schritte beschrieben:

> [!div class="checklist"]
> * Herunterladen der OVA-Datei für den VMware HCX-Connector.
> * Bereitstellen der lokalen OVA-Datei für VMware HCX (VMware HCX-Connector).
> * Aktivieren des VMware HCX-Connectors.
> * Koppeln Ihres lokalen VMware HCX-Connectors mit Ihrem Azure VMware Solution HCX-Cloud-Manager.
> * Konfigurieren der Verbindungen (Netzwerkprofil, Computeprofil und Service Mesh).
> * Abschließen des Setups, indem Sie den Gerätestatus überprüfen und prüfen, ob die Migration möglich ist.

Führen Sie nach Abschluss dieser Schritte die empfohlenen nächsten Schritte am Ende dieses Artikels aus.  

## <a name="before-you-begin"></a>Voraussetzungen

Im Zuge der Vorbereitung Ihrer Bereitstellung sollten Sie sich mit der folgenden VMware-Dokumentation vertraut machen:

* [Benutzerhandbuch für VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Migrieren virtueller Computer mit VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Überlegungen zur Bereitstellung von VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [VMware-Blogreihe: Cloudmigration](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Für VMware HCX erforderliche Netzwerkports](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie VMware HCX Enterprise verwenden möchten, vergewissern Sie sich, dass Sie die Aktivierung über die Azure VMware Solution-Supportkanäle angefordert haben.


### <a name="on-premises-vsphere-environment"></a>Lokale vSphere-Umgebung

Stellen Sie sicher, dass Ihre lokale vSphere-Umgebung (Quellumgebung) die [Mindestanforderungen](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) erfüllt. 

### <a name="network-and-ports"></a>Netzwerk und Ports

* Zwischen dem lokalen Standort und Azure VMware Solution SDDC ist [Azure ExpressRoute Global Reach](tutorial-expressroute-global-reach-private-cloud.md) mit ExpressRoute-Leitungen konfiguriert.

* [Alle erforderlichen Ports](https://ports.vmware.com/home/VMware-HCX) sind für die Kommunikation zwischen lokalen Komponenten und Azure VMware Solution SDDC geöffnet.

### <a name="ip-addresses"></a>IP-Adressen

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Herunterladen der OVA-Datei für den VMware HCX-Connector

Bevor Sie das virtuelle Gerät (Appliance) in Ihrer lokalen vCenter-Instanz bereitstellen, müssen Sie die OVA-Datei für den VMware HCX-Connector herunterladen.  

1. Wählen Sie im Azure-Portal die private Azure VMware Solution-Cloud aus. 

1. Wählen Sie unter **Manage** > **Connectivity** („Verwalten“ > „Konnektivität“) die Registerkarte **HCX** aus, um die IP-Adresse des Azure VMware Solution HCX-Managers zu ermitteln. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Screenshot: IP-Adresse von VMware HCX" lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Wählen Sie unter **Manage** > **Identity** („Verwalten“ > „Identität“) die Option **vCenter admin password** (vCenter-Administratorkennwort) aus, um das Kennwort zu ermitteln.

   > [!TIP]
   > Das vCenter-Kennwort wurde festgelegt, als Sie die private Cloud eingerichtet haben. Dabei handelt es sich um das gleiche Kennwort, das Sie für die Anmeldung beim Azure VMware Solution HCX-Manager verwenden.

   :::image type="content" source="media/tutorial-vmware-hcx/hcx-admin-password.png" alt-text="Ermitteln des HCX-Kennworts" lightbox="media/tutorial-vmware-hcx/hcx-admin-password.png":::

1. Öffnen Sie ein Browserfenster, und melden Sie sich unter `https://x.x.x.9` (Port 443) mit den Benutzeranmeldeinformationen **cloudadmin\@vsphere.local** beim Azure VMware Solution HCX-Manager an.

1. Wählen Sie unter **Administration** > **System Updates** („Verwaltung“ > „Systemupdates“) die Option **Request Download Link** (Downloadlink anfordern) aus.

1. Wählen Sie die gewünschte Option aus, um die OVA-Datei des VMware HCX-Connectors herunterzuladen.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Lokales Bereitstellen der OVA-Datei für den VMware HCX-Connector

1. Wählen Sie in Ihrer lokalen vCenter-Instanz eine [OVF-Vorlage](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) aus, um den VMware HCX-Connector in Ihrer lokalen vCenter-Instanz bereitzustellen. 

   > [!TIP]
   > Wählen Sie die OVA-Datei aus, die Sie im vorherigen Abschnitt heruntergeladen haben.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot: Navigieren zu einer OVF-Vorlage." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Wählen Sie einen Namen und einen Speicherort sowie eine Ressource oder einen Cluster aus, in der bzw. in dem der VMware HCX-Connector bereitgestellt werden soll. Überprüfen Sie anschließend die Details und die erforderlichen Ressourcen, und wählen Sie **Next** (Weiter) aus.  

1. Lesen Sie die Lizenzbedingungen. Wenn Sie diesen zustimmen, wählen Sie den erforderlichen Speicher und das erforderliche Netzwerk aus, und wählen Sie dann **Next** (Weiter) aus.

1. Wählen Sie Speicher und anschließend **Next** (Weiter) aus.

1. Wählen Sie das VMware HCX-Verwaltungsnetzwerksegment aus, das Sie zuvor im [Abschnitt mit den Voraussetzungen für IP-Adressen](#ip-addresses) definiert haben.  Klicken Sie anschließend auf **Weiter**.

1. Geben Sie unter **Customize template** (Vorlage anpassen) alle erforderlichen Informationen ein, und wählen Sie anschließend **Next** (Weiter) aus. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot: Felder zum Anpassen einer Vorlage." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Überprüfen Sie die Konfiguration, und wählen Sie anschließend **Finish** (Fertig stellen) aus, um die OVA-Datei für den VMware HCX-Connector bereitzustellen.
   
   > [!IMPORTANT]
   > Das virtuelle Gerät muss manuell eingeschaltet werden.  Warten Sie nach dem Einschalten zehn bis 15 Minuten, bevor Sie mit dem nächsten Schritt fortfahren.

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Bereitstellung der HCX-Appliance](https://www.youtube.com/embed/UKmSTYrL6AY). 


## <a name="activate-vmware-hcx"></a>Aktivieren von VMware HCX

Nachdem Sie den VMware HCX-Connector lokal bereitgestellt und die Appliance gestartet haben, sind Sie bereit für die Aktivierung. Zunächst müssen Sie einen Lizenzschlüssel aus dem Azure VMware Solution-Portal abrufen.

1. Navigieren Sie im Azure VMware Solution-Portal zu **Manage** > **Connectivity** (Verwalten > Konnektivität), wählen Sie die Registerkarte **HCX** aus, und wählen Sie dann **Add** (Hinzufügen) aus.

1. Verwenden Sie die **Administrator** anmeldeinformationen, um sich beim lokalen VMware-HCX-Manager unter `https://HCXManagerIP:9443` anzumelden. 

   > [!TIP]
   > Das Benutzerkennwort für den **Administrator** wurde im Rahmen der Bereitstellung der OVA-Datei für den VMware-HCX-Manager definiert.

   > [!IMPORTANT]
   > Geben Sie die Portnummer `9443` mit der IP-Adresse des VMware HCX Managers an.

1. Geben Sie unter **Licensing** (Lizenzierung) Ihren Schlüssel für **HCX Advanced Key** (Erweiterter HCX-Schlüssel) ein, und wählen Sie **Activate** (Aktivieren) aus.  
   
    > [!NOTE]
    > Für den VMware HCX Manager muss offener Internetzugriff möglich oder ein Proxy konfiguriert sein.

1. Geben Sie unter **Datacenter Location** (Standort des Rechenzentrums) den nächstgelegenen Standort für die lokale Installation des VMware-HCX-Managers an. Klicken Sie anschließend auf **Weiter**.

1. Ändern Sie unter **System Name** (Systemname) den Namen, oder übernehmen Sie die Standardeinstellung, und wählen Sie anschließend **Continue** (Weiter) aus.
   
1. Wählen Sie **Yes, Continue** (Ja, fortsetzen) aus, um den Vorgang fortzusetzen.

1. Geben Sie in **Connect your vCenter** (Mit vCenter verbinden) den FQDN oder die IP-Adresse Ihres vCenter-Servers und die entsprechenden Anmeldeinformationen an, und wählen Sie dann **Continue** aus.
   
   > [!TIP]
   > Der vCenter-Server ist der Ort, an dem Sie den VMware HCX-Connector in Ihrem Rechenzentrum bereitgestellt haben.

1. Geben Sie in **Configure SSO/PSC** (SSO/PSC konfigurieren) den FQDN oder die IP-Adresse Ihres Platform Services Controllers an, und wählen Sie dann **Continue** (Weiter) aus.
   
   > [!NOTE]
   > In der Regel ist dieser Wert mit Ihrem vCenter-FQDN oder Ihrer IP-Adresse identisch.

1. Vergewissern Sie sich, dass die eingegebenen Informationen korrekt sind, und wählen Sie anschließend **Restart** (Neu starten) aus.
    
   > [!NOTE]
   > Nach dem Neustart tritt eine Verzögerung auf, bevor Sie zum Wechsel zum nächsten Schritt aufgefordert werden.

Nachdem der Dienst neu gestartet wurde, wird vCenter auf dem angezeigten Bildschirm mit einem grünen Punkt dargestellt. Sowohl vCenter als auch SSO müssen die geeigneten Konfigurationsparameter aufweisen, die mit denen im vorherigen Bildschirm identisch sein sollten.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot: Dashboard mit grünem vCenter-Status." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Aktivieren von HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720).

   > [!IMPORTANT]
   > Unter Umständen muss der Patch aus dem [KB-Artikel 81558](https://kb.vmware.com/s/article/81558) von VMware installiert werden. Dies gilt sowohl bei Verwendung von VMware HCX Advanced als auch bei Verwendung von VMware HCX Enterprise. 

## <a name="configure-the-vmware-hcx-connector"></a>Konfigurieren des VMware HCX-Connectors

Nun können Sie eine Standortkopplung (Site Pairing) hinzufügen, ein Netzwerk- und Computeprofil erstellen und Dienste wie Migration, Netzwerkerweiterung (Network Extension) oder Notfallwiederherstellung (Disaster Recovery) aktivieren. 

### <a name="add-a-site-pairing"></a>Hinzufügen einer Standortkopplung

Sie können den VMware HCX-Cloud-Manager in Azure VMware Solution mit dem VMware HCX-Connector in Ihrem Rechenzentrum verbinden oder koppeln. 

1. Melden Sie sich bei Ihrem lokalen vCenter an, und wählen Sie unter **Home** die Option **HCX** aus.

1. Wählen Sie unter **Infrastructure** die Option **Site Pairing** (Standortkopplung) aus, und wählen Sie dann die Option **Connect To Remote Site** (Verbindung mit Remotestandort herstellen) aus (in der Mitte des Bildschirms). 

1. Geben Sie die zuvor notierte URL oder IP-Adresse (`https://x.x.x.9`) des Azure VMware Solution HCX-Cloud-Managers, den Azure VMware Solution-Benutzernamen (cloudadmin@vsphere.local) und das Kennwort ein. Wählen Sie dann **Verbinden** aus.

   > [!NOTE]
   > So richten Sie ein Standortpaar ein:
   > * Ihr VMware HCX-Connector muss in der Lage sein, das Routing an Ihre HCX-Cloud-Manager-IP-Adresse über den Port 443 durchzuführen.
   >
   > * Verwenden Sie das gleiche Kennwort, mit dem Sie sich bei vCenter angemeldet haben. Sie haben dieses Kennwort auf dem ersten Bereitstellungsbildschirm angegeben.

   Ein Bildschirm wird angezeigt, auf dem der VMware HCX-Cloud-Manager in Azure VMware Solution mit dem lokalen VMware HCX-Connector verbunden (gekoppelt) ist.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot: Kopplung des HCX-Managers in Azure VMware Solution und des VMware HCX-Connectors":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: HCX-Standortkopplung](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

### <a name="create-network-profiles"></a>Erstellen von Netzwerkprofilen

Vom VMware HCX-Connector wird eine Teilmenge virtueller Geräte (automatisiert) bereitgestellt, die mehrere IP-Segmente erfordern. Verwenden Sie bei der Erstellung Ihrer Netzwerkprofile die IP-Segmente, die Sie im Rahmen der [Vorbereitungs- und Planungsphase für die Bereitstellung der VMware HCX-Netzwerksegmente](production-ready-deployment-steps.md#vmware-hcx-network-segments) ermittelt haben.

Sie erstellen vier Netzwerkprofile:

   - Verwaltung
   - vMotion
   - Replikation
   - Uplink

1. Wählen Sie unter **Infrastruktur** die Option **Interconnect** > **Multi-Site Service Mesh (Dienstnetz mit mehreren Standorten)**  > **Network Profiles (Netzwerkprofile)**  > **Create Network Profile (Netzwerkprofil erstellen)** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot: Auswahlmöglichkeiten zum Starten der Erstellung eines Netzwerkprofils." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Wählen Sie für jedes Netzwerkprofil das Netzwerk und die Portgruppe aus, geben Sie einen Namen an, und erstellen Sie den IP-Pool des Segments. Klicken Sie anschließend auf **Erstellen**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot: Details für ein neues Netzwerkprofil.":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: HCX-Netzwerkprofil](https://www.youtube.com/embed/O0rU4jtXUxc).


### <a name="create-a-compute-profile"></a>Erstellen eines Computeprofils

1. Wählen Sie unter **Infrastructure** (Infrastruktur) Folgendes aus: **Interconnect** > **Compute Profiles** > **Create Compute Profile** („Verbinden“ > „Compute-Profile“ > „Compute-Profil erstellen“).

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Starten der Erstellung eines Computeprofils." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Geben Sie einen Namen für das Profil ein, und wählen Sie **Continue** (Weiter) aus.  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot: Eintrag eines Computeprofilnamens und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Wählen Sie die Dienste aus, die aktiviert werden sollen, z. B. für Migration, Netzwerkerweiterung oder Notfallwiederherstellung, und wählen Sie dann **Continue** (Weiter) aus.
  
   > [!NOTE]
   > Im Allgemeinen ändert sich hier nichts.

1. Wählen Sie unter **Select Service Resources** (Dienstressourcen auswählen) mindestens eine Dienstressource (Cluster) aus, um die ausgewählten VMware HCX-Dienste zu aktivieren.  

1. Wenn Sie die Cluster in Ihrem lokalen Rechenzentrum sehen, wählen Sie **Continue** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot: Ausgewählten Dienstressourcen und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. Wählen Sie unter **Select Datastore** (Datenspeicher auswählen) die Datenspeicher-Speicherressource für die Bereitstellung der VMware HCX Interconnect-Appliances aus. Klicken Sie anschließend auf **Weiter**.

   Wenn mehrere Ressourcen ausgewählt sind, verwendet VMware HCX die erste ausgewählte Ressource, bis deren Kapazität erschöpft ist.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot: Ausgewählte Datenspeicherressource und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. Wählen Sie unter **Select Management Network Profile** (Verwaltungsnetzwerkprofil auswählen) das Verwaltungsnetzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot: Auswahl eines Verwaltungsnetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. Wählen Sie unter **Select Uplink Network Profile** (Uplinknetzwerkprofil auswählen) das Uplinknetzwerkprofil aus, das Sie in der vorherigen Prozedur erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot: Auswahl eines Uplinknetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. Wählen Sie unter **Select vMotion Network Profile** (vMotion-Netzwerkprofil auswählen) das vMotion-Netzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot: Auswahl eines vMotion-Netzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. Wählen Sie unter **Select vSphere Replication Network Profile** (vSphere-Replikationsnetzwerkprofil auswählen) das vMotion-Replikationsnetzwerkprofil aus, das Sie in den vorherigen Schritten erstellt haben. Klicken Sie anschließend auf **Weiter**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot: Auswahl eines Replikationnetzwerkprofils und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. Wählen Sie unter **Select Distributed Switches for Network Extensions** (Verteilte Switches für Network Extensions auswählen) die Switches aus, die die virtuellen Computer, die zu Azure VMware Solution migriert werden sollen, in einem erweiterten Schicht-2-Netzwerk enthalten. Klicken Sie anschließend auf **Weiter**.

   > [!NOTE]
   > Falls Sie keine virtuellen Computer in erweiterten Schicht-2-Netzwerken migrieren, können Sie diesen Schritt überspringen.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot: Auswahl eines verteilten virtuellen Switches und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Überprüfen Sie die Verbindungsregeln, und wählen Sie **Continue** (Weiter) aus.  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot: Verbindungsregeln und die Schaltfläche „Continue“ (Weiter)." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Wählen Sie **Finish** (Fertig stellen) aus, um das Compute-Profil zu erstellen.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot: Computeprofilinformationen." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Computeprofil](https://www.youtube.com/embed/e02hsChI3b8).

### <a name="create-a-service-mesh"></a>Erstellen eines Service Mesh

Im nächsten Schritt konfigurieren Sie ein Service Mesh zwischen dem lokalen Standort und Azure VMware Solution SDDC.



> [!NOTE]
> So richten Sie erfolgreich ein Service Mesh mit Azure VMware Solution ein:
>
> * Die Ports UDP 500/4500 sind zwischen den Uplink-Netzwerkprofiladressen des lokalen VMware HCC-Connectors und den Azure VMware Solution HCX-Cloudnetzwerk-Profiladressen geöffnet.
>
> * Überprüfen Sie unbedingt die [Ports, die für VMware HCX erforderlich sind](https://ports.vmware.com/home/VMware-HCX).

1. Wählen Sie unter **Infrastructure** (Infrastruktur) die Option **Interconnect** (Verbinden) > **Service Mesh** (Dienstnetz) > **Create Service Mesh** (Dienstnetz erstellen) aus.    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot: Auswahlmöglichkeiten zum Starten der Erstellung eines Service Mesh." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Überprüfen Sie die vorab aufgefüllten Websites, und wählen Sie dann **Continue** aus. 

   > [!NOTE]
   > Wenn dies Ihre erste Dienstnetzkonfiguration ist, müssen Sie diesen Bildschirm nicht ändern.  

1. Wählen Sie das Quellcomputeprofil und das Remotecomputeprofil aus den Dropdownlisten aus, und wählen Sie dann **Continue** (Weiter) aus.  

   Mit der Auswahl werden die Ressourcen definiert, in denen VMs VMware HCX-Dienste nutzen können.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot: Auswählen des Quellcomuteprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot: Auswählen des Remotecomputeprofils." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Überprüfen Sie die Dienste, die aktiviert werden, und wählen Sie **Continue** (Weiter) aus.  

1. Wählen Sie unter **Advanced Configuration - Override Uplink Network profiles** (Erweiterte Konfiguration – Uplinknetzwerkprofile überschreiben) die Option **Continue** (Weiter) aus.  

   Uplinknetzwerkprofile stellen Verbindungen mit dem Netzwerk her, über das die Interconnect-Appliances des Remotestandorts erreicht werden können.  
  
1. Überprüfen Sie die Einstellungen in **Advanced Configuration – Network Extension Appliance Scale Out** (Erweiterte Konfiguration – Aufskalierung für Network Extension-Appliance konfigurieren), und wählen Sie **Continue** aus. 

   Sie können bis zu acht VLANs pro Appliance einrichten, können jedoch eine weitere Appliance bereitstellen, um weitere acht VLANs hinzuzufügen. Außerdem benötigen Sie einen IP-Adressraum für die zusätzlichen Appliances. Pro Appliance wird jeweils eine IP-Adresse verwendet.  Weitere Informationen finden Sie unter [Grenzwerte für die VMware HCX-Konfiguration](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Screenshot, der zeigt, wo die VLAN-Anzahl erhöht wird" lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. Überprüfen Sie die Einstellungen in **Advanced Configuration – Traffic Engineering** (Erweiterte Konfiguration – Verwaltung des Datenverkehrs), nehmen Sie alle erforderlichen Änderungen vor, und wählen Sie dann **Continue** (Weiter) aus.

1. Überprüfen Sie die Topologievorschau, und wählen Sie **Continue** (Weiter) aus.

1. Geben Sie einen benutzerfreundlichen Namen für dieses Dienstnetz ein, und wählen Sie **Finish** (Fertig stellen) aus, um den Vorgang abzuschließen.  

1. Wählen Sie **View Tasks** (Aufgaben anzeigen) aus, um die Bereitstellung zu überwachen. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot: Schaltfläche zum Anzeigen von Aufgaben.":::

   Wenn die Service Mesh-Bereitstellung erfolgreich abgeschlossen wurde, werden die Dienste grün angezeigt.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot: Grüne Indikatoren für Dienste." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Überprüfen Sie die Integrität des Dienstnetzes, indem Sie den Appliancestatus überprüfen. 

1. Wählen Sie **Interconnect** > **Appliances** aus.

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Überprüfen des Gerätestatus." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Service Mesh](https://www.youtube.com/embed/COY3oIws108).

### <a name="optional-create-a-network-extension"></a>(Optional) Erstellen einer Netzwerkerweiterung

Wenn Sie Netzwerke aus Ihrer lokalen Umgebung um Azure VMware Solution erweitern möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie unter **Services** (Dienste) Folgendes aus: **Network Extension** > **Create a Network Extension** („Netzwerkerweiterung“ > „Netzwerkerweiterung erstellen“).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot: Auswahlmöglichkeiten für das Starten der Erstellung einer Netzwerkerweiterung." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Wählen Sie jedes der Netzwerke aus, die Sie um Azure VMware Solution erweitern möchten, und wählen Sie dann **Next** (Weiter) aus.

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot: Auswahl eines Netzwerks.":::

1. Geben Sie die lokale Gateway-IP-Adresse für jedes der Netzwerke ein, die Sie erweitern möchten, ein, und wählen Sie dann **Submit** (Senden) aus. 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot: Eintrag einer Gateway-IP-Adresse.":::

   Es dauert einige Minuten, bis die Netzwerkerweiterung abgeschlossen ist. Sobald dies der Fall ist, erfolgt die Statusänderung in **Extension complete** (Erweiterung abgeschlossen).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot: Status „Erweiterung abgeschlossen“." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Eine umfassende Übersicht über diese Vorgehensweise vermittelt das Video [Azure VMware Solution: Netzwerkerweiterung](https://www.youtube.com/embed/gYR0nftKui0).


## <a name="next-steps"></a>Nächste Schritte

Wenn der Tunnelstatus für die Interconnect-Appliance **UP** lautet und grün angezeigt wird, können Sie virtuelle Azure VMware Solution-Computer mit VMware HCX migrieren und schützen. Azure VMware Solution unterstützt Workloadmigrationen (mit oder ohne Netzwerkerweiterung). Sie können weiterhin Workloads in ihrer vSphere-Umgebung migrieren sowie Netzwerke lokal erstellen und VMs in diesen Netzwerken bereitstellen.  

Weitere Informationen zur Verwendung von HCx finden Sie in der technischen VMware-Dokumentation:

* [VMware HCX-Dokumentation](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrieren virtueller Computer mit VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Für HCX erforderliche Ports](https://ports.vmware.com/home/VMware-HCX)
