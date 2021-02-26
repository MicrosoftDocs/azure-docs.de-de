---
title: Erstellen eines SMB-Volumes für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie ein SMB3-Volume in Azure NetApp Files erstellen. Sie erhalten Informationen zu den Anforderungen für Active Directory-Verbindungen und Active Directory Domain Services.
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
ms.topic: how-to
ms.date: 12/01/2020
ms.author: b-juche
ms.openlocfilehash: 7c5f18d33efa1838fccab3935054ea19227bc06c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102851"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Erstellen eines SMB-Volumes für Azure NetApp Files

Azure NetApp Files unterstützt das Erstellen von Volumes mithilfe von NFS (NFSv3 und NFSv4.1), SMB3 oder einem dualen Protokoll (NFSv3 und SMB). Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet. In diesem Artikel wird veranschaulicht, wie Sie ein SMB3-Volume erstellen.

## <a name="before-you-begin"></a>Voraussetzungen 
Sie müssen bereits einen Kapazitätspool eingerichtet haben.   
[Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)   
Ein Subnetz muss an Azure NetApp Files delegiert werden.  
[Delegieren eines Subnetzes für Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Anforderungen an Active Directory-Verbindungen

 Bevor Sie ein SMB-Volume erstellen, müssen Sie zunächst Active Directory-Verbindungen erstellen. Die Anforderungen für Active Directory-Verbindungen lauten wie folgt: 

* Es muss mit dem verwendeten Administratorkonto möglich sein, Computerkonten im angegebenen Pfad der Organisationseinheit (OU) zu erstellen.  

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
    |    w32time            |    123       |    UDP           |

* Die Standorttopologie für die Ziel-Active Directory Domain Services muss den Richtlinien entsprechen. Dies gilt insbesondere für das Azure-VNET, in dem Azure NetApp Files bereitgestellt wird.  

    Der Adressraum für das virtuelle Netzwerk, in dem Azure NetApp Files bereitgestellt wird, muss einem neuen oder vorhandenen Active Directory-Standort hinzugefügt werden (in dem sich ein Domänencontroller befindet, der über Azure NetApp Files erreichbar ist). 

* Die angegebenen DNS-Server müssen über das [delegierte Subnetz](./azure-netapp-files-delegate-subnet.md) von Azure NetApp Files erreichbar sein.  

    Informationen zu unterstützten Netzwerktopologien finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).

    Die Netzwerksicherheitsgruppen (NSGs) und Firewalls müssen ordnungsgemäß konfigurierte Regeln aufweisen, um Active Directory- und DNS-Datenverkehrsanforderungen zuzulassen. 

* Das delegierte Azure NetApp Files-Subnetz muss in der Lage sein, alle Active Directory Domain Services-Domänencontroller (ADDS) in der Domäne zu erreichen, einschließlich aller lokalen und Remotedomänencontroller. Andernfalls kann es zu Dienstunterbrechungen kommen.  

    Wenn Sie über Domänencontroller verfügen, die über das delegierte Azure NetApp Files-Subnetz nicht erreichbar sind, können Sie während der Erstellung der Active Directory-Verbindung einen Active Directory-Standort angeben.  Azure NetApp Files muss nur mit Domänencontrollern an dem Standort kommunizieren, an dem sich der Adressraum des delegierten Azure NetApp Files-Subnetzes befindet.

    Weitere Informationen zu AD-Standorten und -Diensten finden Sie unter [Entwerfen der Standorttopologie](/windows-server/identity/ad-ds/plan/designing-the-site-topology). 
    
* Sie können AES-Verschlüsselung für die AD-Authentifizierung aktivieren, indem Sie im Fenster [Active Directory beitreten](#create-an-active-directory-connection) das Kontrollkästchen **AES-Verschlüsselung** aktivieren. Azure NetApp Files unterstützt die Verschlüsselungstypen DES, Kerberos AES 128 und Kerberos AES 256 (von der niedrigsten hin zur höchsten Sicherheitsstufe). Wenn Sie AES-Verschlüsselung aktivieren, muss für die für den Beitritt zu Active Directory verwendeten Anmeldeinformationen die höchste entsprechende Kontooption aktiviert sein, die den für Ihr Active Directory aktivierten Funktionen entspricht.    

    Wenn Ihr Active Directory beispielsweise nur über die AES-128-Funktion verfügt, müssen Sie die AES-128-Kontooption für die Anmeldeinformationen des Benutzers aktivieren. Wenn Ihr Active Directory die AES-256-Funktion aufweist, müssen Sie die AES-256-Kontooption aktivieren (die auch AES-128 unterstützt). Wenn Ihr Active Directory keine Kerberos-Verschlüsselungsfunktion besitzt, verwendet Azure NetApp Files standardmäßig DES.  

    Sie können die Kontooptionen in den Eigenschaften von „Active Directory-Benutzer und -Computer“ in der Microsoft Management Console (MMC) aktivieren:   

    ![„Active Directory-Benutzer und -Computer“ (MMC)](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files unterstützt [LDAP-Signatur](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), was eine sichere Übertragung des LDAP-Datenverkehrs zwischen dem Azure NetApp Files-Dienst und den [Active Directory-Zieldomänencontrollern](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) ermöglicht. Wenn Sie den Leitfaden von Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) für LDAP-Signatur befolgen, sollten Sie das LDAP-Signaturfeature in Azure NetApp Files aktivieren, indem Sie das Kontrollkästchen **LDAP-Signatur** im Fenster [Active Directory beitreten](#create-an-active-directory-connection) aktivieren. 

    Die Konfiguration der [LDAP-Kanalbindung](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) hat allein keine Auswirkungen auf den Azure NetApp Files-Dienst. Wenn Sie jedoch sowohl die LDAP-Kanalbindung als auch sicheres LDAP verwenden (z. B. LDAPS oder `start_tls`), tritt bei der Erstellung des SMB-Volumes ein Fehler auf.

Weitere Informationen zu zusätzlichen AD-Informationen finden Sie in den [häufig gestellten Fragen zu SMB](./azure-netapp-files-faqs.md#smb-faqs) für Azure NetApp Files. 

## <a name="decide-which-domain-services-to-use"></a>Festlegen der zu verwendenden Domänendienste 

Azure NetApp Files unterstützt sowohl [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) als auch Azure Active Directory Domain Services (AADDS) für AD-Verbindungen.  Vor der Erstellung einer AD-Verbindung müssen Sie festlegen, ob ADDS oder AADDS verwendet werden soll.  

Weitere Informationen finden Sie unter [Vergleichen von selbstverwalteten Active Directory Domain Services, Azure Active Directory und verwalteten Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Sie können den Bereich Ihrer bevorzugten [Active Directory-Standorte und -Dienste](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) für Azure NetApp Files verwenden. Mit dieser Option werden Lese- und Schreibvorgänge für Active Directory Domain Services-Domänencontroller (ADDS) ermöglicht, auf die über [Azure NetApp Files](azure-netapp-files-network-topologies.md) zugegriffen werden kann. Außerdem wird verhindert, dass der Dienst mit Domänencontrollern kommuniziert, die sich nicht am angegebenen Standort für Active Directory-Standorte und -Dienste befinden. 

Um bei Verwendung von ADDS den Standortnamen zu ermitteln, können Sie sich an die administrative Gruppe Ihrer Organisation wenden, die für Active Directory Domain Services zuständig ist. Im folgenden Beispiel wird das Plug-In „Active Directory-Standorte und -Dienste“ gezeigt, in dem der Standortname angezeigt wird: 

![Active Directory-Standorte und -Dienste](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Wenn Sie eine AD-Verbindung für Azure NetApp Files konfigurieren, geben Sie den Standortnamen im Bereich für das Feld **Active Directory-Standortname** an.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Informationen zur Konfiguration und Anleitungen für Azure Active Directory Domain Services (AADDS) finden Sie in der [Dokumentation zu Azure AD Domain Services](../active-directory-domain-services/index.yml).

Weitere Überlegungen zu Azure NetApp Files im Hinblick auf AADDS: 

* Stellen Sie sicher, dass sich das VNET oder Subnetz, in dem AADDS bereitgestellt wird, in der gleichen Azure-Region wie die Azure NetApp Files-Bereitstellung befindet.
* Wenn Sie ein anderes VNET in der Region verwenden, in der Azure NetApp Files bereitgestellt wird, sollten Sie ein Peering zwischen den beiden VNETs erstellen.
* Azure NetApp Files unterstützt die Typen `user` und `resource forest`.
* Für den Synchronisierungstyp können Sie `All` oder `Scoped` auswählen.   
    Wenn Sie `Scoped` auswählen, stellen Sie sicher, dass die richtige Azure AD-Gruppe für den Zugriff auf SMB-Freigaben ausgewählt ist.  Wenn Sie unsicher sind, können Sie den Synchronisierungstyp `All` verwenden.
* Es muss die Enterprise- oder Premium-SKU verwendet werden. Die Standard-SKU wird nicht unterstützt.

Beachten Sie beim Erstellen einer Active Directory-Verbindung die folgenden Besonderheiten für AADDS:

* Im Menü „AADDS“ finden Sie Informationen zu **Primäres DNS**, **Sekundäres DNS** und **AD-DNS-Domänenname**.  
Für DNS-Server werden zwei IP-Adressen für die Konfiguration der Active Directory-Verbindung verwendet. 
* Der **Pfad der Organisationseinheit** lautet `OU=AADDC Computers`.  
Diese Einstellung wird in **Active Directory-Verbindungen** unter **NetApp-Konto** konfiguriert:

  ![Pfad der Organisationseinheit](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Als Anmeldeinformationen kann für **Benutzername** ein beliebiger Benutzer angegeben werden, der Mitglied der Azure AD-Gruppe **Azure AD DC-Administratoren** ist.


## <a name="create-an-active-directory-connection"></a>Erstellen einer Active Directory-Verbindung

1. Klicken Sie über Ihr NetApp-Konto auf **Active Directory-Verbindungen**, und klicken Sie dann auf **Verbinden**.  

    ![Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Geben Sie im Fenster „Active Directory beitreten“ basierend auf den zu verwendenden Domänendiensten die folgenden Informationen an:  

    Spezifische Informationen zu den verwendeten Domänendiensten finden Sie unter [Festlegen der zu verwendenden Domänendienste](#decide-which-domain-services-to-use). 

    * **Primäres DNS**  
        Das DNS, das für den Active Directory-Domänenbeitritt und SMB-Authentifizierungsvorgänge erforderlich ist 
    * **Sekundäres DNS**   
        Der sekundäre DNS-Server für das Sicherstellen redundanter Namensdienste 
    * **AD-DNS-Domänenname**  
        Dies ist der Domänenname Ihrer Active Directory Domain Services, denen Sie beitreten möchten.
    * **AD-Standortname**  
        Dies ist der Name des Standorts, auf den die Domänencontrollerermittlung beschränkt wird. Sollte dem Standortnamen in Active Directory-Standorte und -Dienste entsprechen.
    * **Präfix des SMB-Servers (Computerkonto)**  
        Dies ist das Namenspräfix für das Computerkonto in Active Directory, das Azure NetApp Files für die Erstellung von neuen Konten verwendet.

        Wenn beispielsweise in Ihrer Organisation der Benennungsstandard für Dateiserver „NAS-01“, „NAS-02“ … „NAS-045“ verwendet wird, geben Sie „NAS“ als Präfix ein. 

        Der Dienst erstellt dann bei Bedarf zusätzliche Computerkonten in Active Directory.

        > [!IMPORTANT] 
        > Das Umbenennen des SMB-Serverpräfixes nach dem Erstellen der Active Directory-Verbindung sorgt für Unterbrechungen. Sie müssen vorhandene SMB-Freigaben nach dem Umbenennen des SMB-Serverpräfixes erneut einbinden.

    * **Pfad der Organisationseinheit**  
        Dies ist der LDAP-Pfad für die Organisationseinheit, in der Computerkonten für SMB-Server erstellt werden. Das bedeutet, Organisationseinheit = zweite Ebene, Organisationseinheit = erste Ebene. 

        Bei Verwendung von Azure NetApp Files mit Azure Active Directory Domain Services lautet der Pfad der Organisationseinheit `OU=AADDC Computers`, wenn Sie Active Directory für Ihr NetApp-Konto konfigurieren.

    ![Beitreten zu Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-Verschlüsselung**   
        Aktivieren Sie dieses Kontrollkästchen, um AES-Verschlüsselung für ein SMB-Volume zu aktivieren. Informationen zu Anforderungen finden Sie unter [Anforderungen für Active Directory-Verbindungen](#requirements-for-active-directory-connections). 

        ![Active Directory: AES-Verschlüsselung](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Die Funktion **AES-Verschlüsselung** befindet sich zurzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

    * **LDAP-Signatur**   
        Aktivieren Sie dieses Kontrollkästchen, um LDAP-Signatur zu aktivieren. Diese Funktionalität ermöglicht sichere LDAP-Suchvorgänge zwischen dem Azure NetApp Files-Dienst und den benutzerseitig angegebenen [Active Directory Domain Services-Domänencontrollern](/windows/win32/ad/active-directory-domain-services). Weitere Informationen finden Sie unter [ADV190023 | Microsoft-Leitfaden zum Aktivieren der LDAP-Kanalbindung und der LDAP-Signatur](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory: LDAP-Signatur](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Die Funktion **LDAP-Signatur** befindet sich derzeit in der Vorschauphase. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

     * **Sicherungsrichtlinienbenutzer**  
        Sie können weitere Konten einschließen, die erhöhte Rechte für das für Azure NetApp Files erstellte Computerkonto erfordern. Die angegebenen Konten dürfen die NTFS-Berechtigungen auf Datei- oder Ordnerebene ändern. Beispielsweise können Sie ein nicht privilegiertes Dienstkonto angeben, das zum Migrieren von Daten zu einer SMB-Dateifreigabe in Azure NetApp Files verwendet wird.  

        ![Active Directory: Sicherungsrichtlinienbenutzer](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Das Feature **Sicherungsrichtlinienbenutzer** steht derzeit als Vorschau zur Verfügung. Wenn Sie dieses Feature zum ersten Mal verwenden, registrieren Sie es vor der Verwendung: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Überprüfen Sie den Status der Funktionsregistrierung: 

        > [!NOTE]
        > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

    * Anmeldeinformationen, einschließlich **Benutzername** und **Kennwort**

        ![Active Directory-Anmeldeinformationen](../media/azure-netapp-files/active-directory-credentials.png)

3. Klicken Sie auf **Verknüpfen**.  

    Die von Ihnen erstellte Active Directory-Verbindung wird angezeigt.

    ![Erstellte Active Directory-Verbindungen](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Hinzufügen eines SMB-Volumes

1. Klicken Sie auf dem Blatt „Kapazitätspools“ auf das Blatt **Volumes**. 

    ![Navigieren zu Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klicken Sie auf **+ Volume hinzufügen**, um ein Volume zu erstellen.  
    Das Fenster „Volume erstellen“ wird angezeigt.

3. Klicken Sie im Fenster „Volume erstellen“ auf **Erstellen**, und geben Sie auf der Registerkarte „Grundlagen“ Informationen in den folgenden Feldern an:   
    * **Volumename**      
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.   

        Ein Volumename muss innerhalb der einzelnen Kapazitätspools eindeutig sein. Er muss mindestens drei Zeichen lang sein. Sie dürfen beliebige alphanumerische Zeichen verwenden.   

        Sie können `default` oder `bin` nicht als Volumename verwenden.

    * **Kapazitätspool**  
        Geben Sie den Kapazitätspool an, in dem das Volume erstellt werden soll.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, die dem Volume zugewiesen wird.  

        Das Feld **Verfügbares Kontingent** zeigt den ungenutzten Speicherplatz im ausgewählten Kapazitätspool an, den Sie beim Erstellen eines neuen Volumes verwenden können. Die Größe des neuen Volumes darf das verfügbare Kontingent nicht überschreiten.  

    * **Durchsatz (MiB/s)**    
        Wenn das Volume in einem manuellen QoS-Kapazitätspool erstellt wird, geben Sie den für das Volume gewünschten Durchsatz an.   

        Wenn das Volume in einem automatischen QoS-Kapazitätspool erstellt wird, lautet der in diesem Feld angezeigte Wert (Kontingent x Serviceleveldurchsatz).   

    * **Virtuelles Netzwerk**  
        Geben Sie das virtuelle Azure-Netzwerk (VNET) an, von dem aus Sie auf das Volume zugreifen möchten.  

        Das von Ihnen angegebene VNET muss über ein an Azure NetApp Files delegiertes Subnetz verfügen. Auf den Azure NetApp Files-Dienst kann nur vom gleichen VNET aus oder per VNET-Peering von einem VNET aus zugegriffen werden, das sich in der gleichen Region befindet wie das Volume. Sie können auch über ExpressRoute von Ihrem lokalen Netzwerk aus auf das Volume zugreifen.   

    * **Subnetz**  
        Geben Sie das Subnetz an, das Sie für das Volume verwenden möchten.  
        Das von Ihnen angegebene Subnetz muss an Azure NetApp Files delegiert werden. 
        
        Wenn Sie kein Subnetz delegiert haben, klicken Sie auf der Seite „Volume erstellen“ auf **Neu erstellen**. Geben Sie dann auf der Seite „Subnetz erstellen“ die Subnetzinformationen an, und wählen Sie **Microsoft.NetApp/volumes** aus, um das Subnetz für Azure NetApp Files zu delegieren. In jedem VNET kann nur ein Subnetz an Azure NetApp Files delegiert werden.   
 
        ![Erstellen eines Volumes](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Erstellen eines Subnetzes](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Wenn Sie eine vorhandene Momentaufnahmerichtlinie auf das Volume anwenden möchten, klicken Sie auf **Abschnitt „Erweitert“ anzeigen**, um den Bereich zu erweitern, geben Sie an, ob Sie den Momentaufnahmepfad ausblenden möchten, und wählen Sie im Pulldownmenü eine Momentaufnahmerichtlinie aus. 

        Informationen zum Erstellen einer Momentaufnahmenrichtlinie finden Sie unter [Verwalten von Momentaufnahmenrichtlinien](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Abschnitt „Erweitert“ anzeigen](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klicken Sie auf **Protokoll**, und geben Sie die folgenden Informationen an:  
    * Wählen Sie **SMB** als Protokolltyp für das Volume aus. 
    * Wählen Sie in der Dropdownliste Ihre **Active Directory**-Verbindung aus.
    * Geben Sie in **Freigabename** den Namen des freigegebenen Volumes ein.

    ![Angeben des SMB-Protokolls](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klicken Sie auf **Bewerten + erstellen**, um die Volumedetails zu überprüfen.  Klicken Sie dann auf **Erstellen**, um das SMB-Volume zu erstellen.

    Das von Ihnen erstellte Volume wird auf der Seite „Volumes“ angezeigt. 
 
    Ein Volume erbt Abonnement-, Ressourcengruppen- und Standortattribute aus dem Kapazitätspool. Den Volumebereitstellungsstatus können Sie auf der Benachrichtigungsregisterkarte überwachen.

## <a name="control-access-to-an-smb-volume"></a>Steuern des Zugriffs auf ein SMB-Volume  

Der Zugriff auf ein SMB-Volume wird durch Berechtigungen verwaltet.  

### <a name="share-permissions"></a>Freigabeberechtigungen  

Ein neues Volume verfügt standardmäßig über die Freigabeberechtigungen **Jeder/Vollzugriff**. Mitglieder der Gruppe „Domänen-Admins“ können die Freigabeberechtigungen mithilfe der Computerverwaltung unter dem Computerkonto ändern, das für das Azure NetApp Files-Volume verwendet wird.

![SMB-Einbindungspfad](../media/azure-netapp-files/smb-mount-path.png) 
![Festlegen von Freigabeberechtigungen](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-Datei- und -Ordnerberechtigungen  

Sie können Berechtigungen für eine Datei oder einen Ordner festlegen, indem Sie die Registerkarte **Sicherheit** in den Eigenschaften des Objekts im Windows-SMB-Client verwenden.
 
![Festlegen von Datei- und Ordnerberechtigungen](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Nächste Schritte  

* [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Häufig gestellte Fragen zu SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Problembehandlung für SMB-Volumes und Volumes mit dualem Protokoll](troubleshoot-dual-protocol-volumes.md)
* [Erfahren Sie mehr über die Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md)
* [Installieren einer neuen Active Directory-Gesamtstruktur mit der Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
