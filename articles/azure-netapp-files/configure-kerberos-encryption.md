---
title: Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel werden die Konfiguration der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files und die daraus resultierenden Auswirkung auf die Leistung beschrieben.
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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: 1ffbcc24a2ee386be1a8ce50b55375d5bd458df1
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410095"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files

Azure NetApp Files unterstützt die Verschlüsselung des NFS-Clients in Kerberos-Modi (krb5, krb5i und krb5p) mit AES-256-Verschlüsselung. In diesem Artikel werden die erforderlichen Konfigurationen für die Verwendung eines NFSv4.1-Volumes mit Kerberos-Verschlüsselung beschrieben.

## <a name="requirements"></a>Requirements (Anforderungen)

Für die Verschlüsselung des NFSv4.1-Clients gelten die folgenden Anforderungen: 

* Verbindung mit Active Directory Domain Services (AD DS) zur Vereinfachung der Kerberos-Ticketausstellung 
* Erstellung von DNS-A/PTR-Einträgen für die IP-Adressen des Clients sowie des NFS-Servers in Azure NetApp Files
* Linux-Client  
    Dieser Artikel enthält Anleitungen für RHEL- und Ubuntu-Clients.  Für andere Clients gelten ähnliche Konfigurationsschritte. 
* NTP-Serverzugriff  
    Sie können einen der häufig verwendeten Active Directory-Domänencontroller (AD DC) verwenden.

## <a name="create-an-nfs-kerberos-volume"></a>Erstellen eines NFS-Kerberos-Volumes

1.  Führen Sie die unter [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md) angegebenen Schritte aus, um das NFSv4.1-Volume zu erstellen.   

    Legen Sie auf der Seite „Volume erstellen“ die NFS-Version auf **NFSv4.1** und Kerberos auf **Aktiviert** fest.

    > [!IMPORTANT] 
    > Nachdem das Volume erstellt wurde, können Sie die Auswahl der Kerberos-Aktivierung nicht mehr ändern.

    ![Erstellen des NFSv4.1-Kerberos-Volumes](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Wählen Sie **Exportrichtlinie** aus, um die gewünschte Zugriffsebene und Sicherheitsoption (Kerberos 5, Kerberos 5i oder Kerberos 5p) für das Volume festzulegen.   

    Informationen zu den Leistungsauswirkungen von Kerberos finden Sie unter [Leistungsauswirkungen von Kerberos auf NFSv4.1](#kerberos_performance).  

    Sie können außerdem die Kerberos-Sicherheitsmethoden für das Volume ändern, indem Sie im Azure NetApp Files-Navigationsbereich auf „Exportrichtlinie“ klicken.

3.  Klicken Sie zum Erstellen des NFSv4.1-Volumes auf **Überprüfen + erstellen**.

## <a name="configure-the-azure-portal"></a>Konfigurieren des Azure-Portals 

1.  Befolgen Sie die Anweisungen unter [Erstellen einer Active Directory-Verbindung](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection).  

    Kerberos erfordert, dass Sie mindestens ein Computerkonto in Active Directory erstellen. Die angegebenen Kontoinformationen werden für die Erstellung der Konten für SMB- *sowie* NFSv4.1-Kerberos-Volumes verwendet. Dieses Computerkonto wird bei der Volumeerstellung automatisch erstellt.

2.  Geben Sie unter **Kerberos Realm** (Kerberos-Bereich) den **AD-Servernamen** und die **KDC-IP-Adresse** ein.

    AD-Server und KDC-IP können derselbe Server sein. Diese Informationen werden verwendet, um das in Azure NetApp Files verwendete SPN-Computerkonto zu erstellen. Nachdem das Computerkonto erstellt wurde, werden in Azure NetApp Files DNS-Servereinträge verwendet, um bei Bedarf weitere KDC-Server zu suchen. 

    ![Kerberos-Bereich](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Klicken Sie auf **Beitreten** , um die Konfiguration zu speichern.

## <a name="configure-active-directory-connection"></a>Konfigurieren der Active Directory-Verbindung 

Bei der Konfiguration von NFSv4.1-Kerberos werden zwei Computerkonten in Active Directory erstellt:
* Ein Computerkonto für SMB-Freigaben
* Ein Computerkonto für NFSv4.1: Dieses Konto können Sie am Präfix `NFS-` erkennen. 

Legen Sie nach dem Erstellen des ersten NFSv4.1-Kerberos-Volumes den Verschlüsselungstyp für das Computerkonto durch Ausführen des folgenden PowerShell-Befehls fest:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Konfigurieren des NFS-Clients 

Befolgen Sie die Anweisungen unter [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md), um den NFS-Client zu konfigurieren.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Einbinden des NFS-Kerberos-Volumes

1. Wählen Sie auf der Seite **Volumes** das NFS-Volume aus, das Sie einbinden möchten.

2. Wählen Sie **Einbindungsanweisungen** in dem Volume aus, um die Anweisungen anzuzeigen.

    Beispiel: 

    ![Einstellungsanweisungen für Kerberos-Volumes](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Erstellen Sie das Verzeichnis (Bereitstellungspunkt) für das neue Volume.  

4. Legen Sie den Standardverschlüsselungstyp für das Computerkonto auf AES 256 fest:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Sie müssen diesen Befehl für jedes Computerkonto nur einmal ausführen.
    * Sie können diesen Befehl über einen Domänencontroller oder über einen PC ausführen, auf dem [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) installiert ist. 
    * Die Variable `$NFSCOMPUTERACCOUNT` entspricht dem beim Bereitstellen des Kerberos-Volumes in Active Directory erstellten Computerkonto. Dabei handelt es sich um das Konto mit dem Präfix `NFS-`. 
    * Die Variable `$ANFSERVICEACCOUNT` entspricht einem Active Directory-Benutzerkonto ohne Berechtigungen mit delegierter Steuerung der Organisationseinheit, in der das Computerkonto erstellt wurde. 

5. Binden Sie das Volume auf dem Host ein: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * Die Variable `$ANFEXPORT` entspricht dem Pfad `host:/export` aus den Einbindungsanweisungen.
    * Die Variable `$ANFMOUNTPOINT` entspricht dem vom Benutzer erstellten Ordner auf dem Linux-Host.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>Leistungsauswirkungen von Kerberos auf NFSv4.1 

In diesem Abschnitt erhalten Sie Informationen zu den Leistungsauswirkungen von Kerberos auf NFSv4.1.

### <a name="available-security-options"></a>Verfügbare Sicherheitsoptionen 

Derzeit sind folgende Sicherheitsoptionen für NFSv4.1-Volumes verfügbar: 

* **sec=sys** verwendet lokale UNIX-UIDs und -GIDs mithilfe von AUTH_SYS zum Authentifizieren von NFS-Vorgängen.
* **sec=krb5** verwendet Kerberos V5 anstelle von lokalen UNIX-UIDs und -GIDs zum Authentifizieren von Benutzern.
* **sec=krb5i** verwendet Kerberos V5 für die Benutzerauthentifizierung und führt die Integritätsüberprüfung von NFS-Vorgängen mithilfe von sicheren Prüfsummen durch, um Datenmanipulationen zu verhindern.
* **sec=krb5p** verwendet Kerberos V5 für die Benutzerauthentifizierung und die Integritätsüberprüfung. Bei dieser Option wird der NFS-Datenverkehr verschlüsselt, um das Ausspähen des Datenverkehrs zu verhindern. Diese Option ist die sicherste Einstellung, sie stellt aber auch die größten Anforderungen an die Leistung.

### <a name="performance-vectors-tested"></a>Getestete Leistungsvektoren

In diesem Abschnitt werden die Leistungsauswirkungen der verschiedenen `sec=*`-Optionen auf einen einzelnen Client beschrieben.

* Die Leistungsauswirkungen wurden auf zwei Ebenen getestet: geringe Parallelität (niedrige Auslastung) und hohe Parallelität (Obergrenze für E/A und Durchsatz).  
* Drei Typen von Workloads wurden getestet:  
    * Kleine Vorgänge mit zufälligem Lese-/Schreibzugriff (mit FIO)
    * Umfangreiche Vorgänge mit sequenziellem Lese-/Schreibzugriff (mit FIO)
    * Hohe Arbeitsauslastung durch Metadaten, z. B. durch Anwendungen wie Git generiert

### <a name="expected-performance-impact"></a>Erwartete Leistungsauswirkungen 

Es gibt zwei Schwerpunktbereiche: geringe Auslastung und Obergrenze. In den folgenden Listen werden die Leistungsauswirkungen der einzelnen Sicherheitseinstellungen und Szenarien beschrieben. Alle Vergleiche beziehen sich auf den Sicherheitsparameter `sec=sys`. Der Test wurde für ein einzelnes Volume unter Verwendung eines einzelnen Clients durchgeführt. 

Leistungsauswirkungen von krb5:

* Geringe Parallelität (L/S):
    * Sequenzielle Latenz um 0,3 ms erhöht.
    * Zufällige E/A-Latenz um 0,2 ms erhöht.
    * E/A-Latenz bei Metadaten um 0,2 ms erhöht.
* Hohe Parallelität (L/S): 
    * Der maximale sequenzielle Durchsatz wurde durch krb5 nicht beeinträchtigt.
    * Die maximale zufällige E/A verringerte sich bei reinen Leseworkloads um 30 %, wobei die Gesamtauswirkungen bei reinen Leseworkloads bei 0 lagen. 
    * Die maximale Workload für Metadaten wurde um 30 % verringert.

Leistungsauswirkungen von krb5i: 

* Geringe Parallelität (L/S):
    * Sequenzielle Latenz um 0,5 ms erhöht.
    * Zufällige E/A-Latenz um 0,2 ms erhöht.
    * E/A-Latenz bei Metadaten um 0,2 ms erhöht.
* Hohe Parallelität (L/S): 
    * Der maximale sequenzielle Durchsatz wurde unabhängig von der Workloadmischung um insgesamt 70 % verringert.
    * Die maximale zufällige E/A verringerte sich bei reinen Leseworkloads um 50 %, wobei die Gesamtauswirkungen bei reinen Leseworkloads bei 25 % lagen. 
    * Die maximale Workload für Metadaten wurde um 30 % verringert.

Leistungsauswirkungen von krb5p:

* Geringe Parallelität (L/S):
    * Sequenzielle Latenz um 0,8 ms erhöht.
    * Zufällige E/A-Latenz um 0,2 ms erhöht.
    * E/A-Latenz bei Metadaten um 0,2 ms erhöht.
* Hohe Parallelität (L/S): 
    * Der maximale sequenzielle Durchsatz wurde unabhängig von der Workloadmischung um insgesamt 85 % verringert. 
    * Die maximale zufällige E/A verringerte sich bei reinen Leseworkloads um 65 %, wobei die Gesamtauswirkungen bei reinen Leseworkloads bei 43 % lagen. 
    * Die maximale Workload für Metadaten wurde um 30 % verringert.

## <a name="next-steps"></a>Nächste Schritte  

* [Häufig gestellte Fragen zu Azure NetApp Files](azure-netapp-files-faqs.md)
* [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erstellen einer Active Directory-Verbindung](azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection)
* [Konfigurieren eines NFS-Clients für Azure NetApp Files](configure-nfs-clients.md) 
