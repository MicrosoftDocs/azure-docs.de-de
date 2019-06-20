---
title: Erstellen eines Volumes für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Erstellen eines Volumes für Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: b-juche
ms.openlocfilehash: 53b2742cf92f3a3df346ba3557c718b8d7a11a4e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719443"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Erstellen eines Volumes für Azure NetApp Files

Jeder Kapazitätspool kann maximal 500 Volumes enthalten. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet. Azure NetApp Files unterstützt NFS- und SMBv3-Volumes. 

## <a name="before-you-begin"></a>Voraussetzungen 
Sie müssen bereits einen Kapazitätspool eingerichtet haben.   
[Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)   
Ein Subnetz muss an Azure NetApp Files delegiert werden.  
[Delegieren eines Subnetzes für Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Erstellen eines NFS-Volumes

1.  Klicken Sie auf dem Blatt „Kapazitätspools“ auf das Blatt **Volumes**. 

    ![Navigieren zu Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Klicken Sie auf **+ Volume hinzufügen**, um ein Volume zu erstellen.  
    Das Fenster „Volume erstellen“ wird angezeigt.

3.  Klicken Sie im Fenster „Volume erstellen“ auf **Erstellen**, und geben Sie Informationen für die folgenden Felder an:   
    * **Volumename**      
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.   

        Ein Volumename muss innerhalb der einzelnen Kapazitätspools eindeutig sein. Er muss mindestens drei Zeichen lang sein. Sie dürfen beliebige alphanumerische Zeichen verwenden.

    * **Kapazitätspool**  
        Geben Sie den Kapazitätspool an, in dem das Volume erstellt werden soll.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, die dem Volume zugewiesen wird.  

        Das Feld **Verfügbares Kontingent** zeigt den ungenutzten Speicherplatz im ausgewählten Kapazitätspool an, den Sie beim Erstellen eines neuen Volumes verwenden können. Die Größe des neuen Volumes darf das verfügbare Kontingent nicht überschreiten.  

    * **Virtuelles Netzwerk**  
        Geben Sie das virtuelle Azure-Netzwerk (VNet) an, von dem aus Sie auf das Volume zugreifen möchten.  

        Das von Ihnen angegebene VNET muss über ein an Azure NetApp Files delegiertes Subnetz verfügen. Auf den Azure NetApp Files-Dienst kann nur vom gleichen VNET aus oder per VNET-Peering von einem VNET aus zugegriffen werden, das sich in der gleichen Region befindet wie das Volume. Sie können auch über ExpressRoute von Ihrem lokalen Netzwerk aus auf das Volume zugreifen.   

    * **Subnetz**  
        Geben Sie das Subnetz an, das Sie für das Volume verwenden möchten.  
        Das von Ihnen angegebene Subnetz muss an Azure NetApp Files delegiert werden. 
        
        Wenn Sie kein Subnetz delegiert haben, klicken Sie auf der Seite „Volume erstellen“ auf **Neu erstellen**. Geben Sie dann auf der Seite „Subnetz erstellen“ die Subnetzinformationen an, und wählen Sie **Microsoft.NetApp/volumes** aus, um das Subnetz für Azure NetApp Files zu delegieren. In jedem VNET kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
 
        ![Erstellen eines Volumes](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Erstellen eines Subnetzes](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicken Sie auf **Protokoll**, und wählen Sie dann **NFS** als Protokolltyp für das Volume aus.   
    * Geben Sie den **Dateipfad** zum Erstellen des Exportpfads für das neue Volume an. Der Exportpfad dient zum Einbinden und Zugreifen auf das Volume.

        Der Dateipfadname darf nur Buchstaben, Zahlen und Bindestriche („-“) enthalten. Er muss 16 bis 40 Zeichen umfassen. 

        Der Dateipfad muss innerhalb der einzelnen Abonnements und Regionen eindeutig sein. 

    * Optional können Sie [die Exportrichtlinie für das NFS-Volume konfigurieren](azure-netapp-files-configure-export-policy.md).

    ![Angeben des NFS-Protokolls](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Klicken Sie auf **Bewerten + erstellen**, um die Volumedetails zu überprüfen.  Klicken Sie dann auf **Erstellen**, um das NFS-Volume zu erstellen.

    Das von Ihnen erstellte Volume wird auf der Seite „Volumes“ angezeigt. 
 
    Ein Volume erbt Abonnement-, Ressourcengruppen- und Standortattribute aus dem Kapazitätspool. Den Volumebereitstellungsstatus können Sie auf der Benachrichtigungsregisterkarte überwachen.

## <a name="create-an-smb-volume"></a>Erstellen eines SMB-Volumes

Azure NetApp Files unterstützt SMBv3-Volumes. Bevor Sie ein SMB-Volume hinzufügen, müssen Sie zunächst Active Directory-Verbindungen erstellen. 

### <a name="create-an-active-directory-connection"></a>Erstellen einer Active Directory-Verbindung

1. Stellen Sie sicher, dass die folgenden Anforderungen erfüllt sind: 

    * Das von Ihnen verwendete Administratorkonto muss in der Lage sein, Computerkonten im Pfad der Organisationseinheit (OU) zu erstellen, den Sie angeben werden.
    * Auf dem entsprechenden Windows Active Directory-Server (AD) müssen die richtigen Ports geöffnet sein.  
        Die erforderlichen Ports lauten wie folgt: 

        |     Dienst           |     Port     |     Protocol     |
        |-----------------------|--------------|------------------|
        |    AD-Webdienste    |    9389      |    TCP           |
        |    DNS                |    53        |    TCP           |
        |    DNS                |    53        |    UDP           |
        |    ICMPv4             |    –       |    Echo Reply    |
        |    Kerberos           |    464       |    TCP           |
        |    Kerberos           |    464       |    UDP           |
        |    Kerberos           |    88        |    TCP           |
        |    Kerberos           |    88        |    UDP           |
        |    LDAP               |    389       |    TCP           |
        |    LDAP               |    389       |    UDP           |
        |    LDAP               |    3268      |    TCP           |
        |    NetBIOS-Name       |    138       |    UDP           |
        |    SAM/LSA            |    445       |    TCP           |
        |    SAM/LSA            |    445       |    UDP           |
        |    Sicheres LDAP (LDAPS)        |    636       |    TCP           |
        |    Sicheres LDAP (LDAPS)        |    3269      |    TCP           |
        |    w32time            |    123       |    UDP           |


1. Klicken Sie über Ihr NetApp-Konto auf **Active Directory-Verbindungen**, und klicken Sie dann auf **Verbinden**.  

    ![Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geben Sie im Fenster „Active Directory beitreten“ die folgenden Informationen an:

    * **Primäres DNS**   
        Dies ist die IP-Adresse des Domänencontrollers für die bevorzugten Active Directory Domain Services, die mit Azure NetApp Files verwendet werden sollen. 
    * **Sekundäres DNS**  
        Dies ist die IP-Adresse des Domänencontrollers für die sekundären Active Directory Domain Services, die mit Azure NetApp Files verwendet werden sollen. 
    * **Domäne**  
        Dies ist der Domänenname Ihrer Active Directory Domain Services, denen Sie beitreten möchten.
    * **Präfix des SMB-Servers (Computerkonto)**  
        Dies ist das Namenspräfix für das Computerkonto in Active Directory, das Azure NetApp Files für die Erstellung von neuen Konten verwendet.

        Wenn beispielsweise der Benennungsstandard in Ihrer Organisation für Dateiserver „NAS-01“, „NAS-02“, „NAS-045“ usw. ist, geben Sie „NAS“ als Präfix ein. 

        Der Dienst erstellt dann bei Bedarf zusätzliche Computerkonten in Active Directory.

    * **Pfad der Organisationseinheit**  
        Dies ist der LDAP-Pfad für die Organisationseinheit, in der Computerkonten für SMB-Server erstellt werden. Das bedeutet, Organisationseinheit = zweite Ebene, Organisationseinheit = erste Ebene. 
    * Anmeldeinformationen, einschließlich **Benutzername** und **Kennwort**

    ![Beitreten zu Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Klicken Sie auf **Verknüpfen**.  

    Die von Ihnen erstellte Active Directory-Verbindung wird angezeigt.

    ![Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Hinzufügen eines SMB-Volumes

1. Klicken Sie auf dem Blatt „Kapazitätspools“ auf das Blatt **Volumes**. 

    ![Navigieren zu Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicken Sie auf **+ Volume hinzufügen**, um ein Volume zu erstellen.  
    Das Fenster „Volume erstellen“ wird angezeigt.

3. Klicken Sie im Fenster „Volume erstellen“ auf **Erstellen**, und geben Sie Informationen für die folgenden Felder an:   
    * **Volumename**      
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.   

        Ein Volumename muss innerhalb der einzelnen Kapazitätspools eindeutig sein. Er muss mindestens drei Zeichen lang sein. Sie dürfen beliebige alphanumerische Zeichen verwenden.

    * **Kapazitätspool**  
        Geben Sie den Kapazitätspool an, in dem das Volume erstellt werden soll.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, die dem Volume zugewiesen wird.  

        Das Feld **Verfügbares Kontingent** zeigt den ungenutzten Speicherplatz im ausgewählten Kapazitätspool an, den Sie beim Erstellen eines neuen Volumes verwenden können. Die Größe des neuen Volumes darf das verfügbare Kontingent nicht überschreiten.  

    * **Virtuelles Netzwerk**  
        Geben Sie das virtuelle Azure-Netzwerk (VNet) an, von dem aus Sie auf das Volume zugreifen möchten.  

        Das von Ihnen angegebene VNET muss über ein an Azure NetApp Files delegiertes Subnetz verfügen. Auf den Azure NetApp Files-Dienst kann nur vom gleichen VNET aus oder per VNET-Peering von einem VNET aus zugegriffen werden, das sich in der gleichen Region befindet wie das Volume. Sie können auch über ExpressRoute von Ihrem lokalen Netzwerk aus auf das Volume zugreifen.   

    * **Subnetz**  
        Geben Sie das Subnetz an, das Sie für das Volume verwenden möchten.  
        Das von Ihnen angegebene Subnetz muss an Azure NetApp Files delegiert werden. 
        
        Wenn Sie kein Subnetz delegiert haben, klicken Sie auf der Seite „Volume erstellen“ auf **Neu erstellen**. Geben Sie dann auf der Seite „Subnetz erstellen“ die Subnetzinformationen an, und wählen Sie **Microsoft.NetApp/volumes** aus, um das Subnetz für Azure NetApp Files zu delegieren. In jedem VNET kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
 
        ![Erstellen eines Volumes](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Erstellen eines Subnetzes](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Klicken Sie auf **Protokoll**, und geben Sie die folgenden Informationen an:  
    * Wählen Sie **SMB** als Protokolltyp für das Volume aus. 
    * Wählen Sie in der Dropdownliste Ihre **Active Directory**-Verbindung aus.
    * Geben Sie in **Freigabename** den Namen des freigegebenen Volumes ein.

    ![Angeben des SMB-Protokolls](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klicken Sie auf **Bewerten + erstellen**, um die Volumedetails zu überprüfen.  Klicken Sie dann auf **Erstellen**, um das SMB-Volume zu erstellen.

    Das von Ihnen erstellte Volume wird auf der Seite „Volumes“ angezeigt. 
 
    Ein Volume erbt Abonnement-, Ressourcengruppen- und Standortattribute aus dem Kapazitätspool. Den Volumebereitstellungsstatus können Sie auf der Benachrichtigungsregisterkarte überwachen.

## <a name="next-steps"></a>Nächste Schritte  

* [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurieren der Exportrichtlinie für ein NFS-Volume](azure-netapp-files-configure-export-policy.md)
* [Erfahren Sie mehr über die Integration virtueller Netzwerke für Azure-Dienste](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
