---
title: 'Azure Active Directory Domain Services: Einbinden einer Ubuntu-VM in eine verwaltete Domäne | Microsoft-Dokumentation'
description: Einbinden eines virtuellen Ubuntu Linux-Computers in die Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 9b1ad1b70258f623219ca171797aac4cdd33b310
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245303"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Einbinden eines virtuellen Ubuntu-Computers in Azure in eine verwaltete Domäne
Dieser Artikel zeigt, wie ein virtueller Ubuntu Linux-Computer einer durch Azure AD Domain Services verwalteten Domäne beitritt.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:  
1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Stellen Sie sicher, dass Sie die IP-Adressen der verwalteten Domäne nicht als DNS-Server für das virtuelle Netzwerk konfiguriert haben. Weitere Informationen finden Sie unter [Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk](active-directory-ds-getting-started-dns.md).
5. Führen Sie die Schritte aus, die zum [Synchronisieren der Kennwörter für Ihre verwaltete Domäne der Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md) erforderlich sind.


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Bereitstellen eines virtuellen Ubuntu Linux-Computers
Stellen Sie mithilfe einer der folgenden Methoden einen virtuellen Ubuntu Linux-Computer in Azure bereit:
* [Azure-Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Stellen Sie den virtuellen Computer in **demselben virtuellen Netzwerk bereit, in dem Sie Azure AD Domain Services aktiviert haben**.
> * Wählen Sie ein **anderes Subnetz** als das aus, in dem Sie Azure AD Domain Services aktiviert haben.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Herstellen einer Remoteverbindung zum virtuellen Ubuntu Linux-Computer
Der virtuelle Ubuntu-Computer wurde in Azure bereitgestellt. Die nächste Aufgabe besteht darin, über das lokale Administratorkonto, das während der Bereitstellung des virtuellen Computers erstellt wurde, eine Remoteverbindung zum virtuellen Computer herzustellen.

Befolgen Sie die Anweisungen im Artikel [Anmelden bei einem virtuellen Computer mit Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Konfigurieren der Hostdatei auf dem virtuellen Linux-Computer
Bearbeiten Sie in Ihrem SSH-Terminal die Datei „/etc/hosts“, und aktualisieren Sie die IP-Adresse und den Hostnamen Ihres Computers.

```
sudo vi /etc/hosts
```

Geben Sie in der Hostdatei die folgenden Wert ein:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
„contoso100.com“ steht für den DNS-Domänennamen Ihrer verwalteten Domäne. „contoso-ubuntu“ steht für den Hostnamen des virtuellen Ubuntu-Computers, den Sie in die verwaltete Domäne einbinden.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Installieren der erforderlichen Pakete auf dem virtuellen Linux-Computer
Als Nächstes installieren Sie Pakete, die für den Domänenbeitritt auf dem virtuellen Computer erforderlich sind. Führen Sie die folgenden Schritte aus:

1.  Geben Sie in Ihrem SSH-Terminal folgenden Befehl ein, um die Paketlisten aus den Repositorys herunterzuladen. Dieser Befehl aktualisiert die Paketlisten, um Informationen zu den neuesten Paketversionen und deren Abhängigkeiten zu erhalten.

    ```
    sudo apt-get update
    ```

2. Geben Sie folgenden Befehl ein, um die erforderlichen Pakete zu installieren.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Bei der Kerberos-Installation wird Ihnen ein rosafarbener Bildschirm angezeigt. Bei der Installation des Pakets „krb5-user“ werden Sie aufgefordert, den Bereichsnamen einzugeben (in Großbuchstaben). Die Installation schreibt die Abschnitte [realm] und [domain_realm] in die Datei „/etc/krb5.conf“.

    > [!TIP]
    > Wenn der Name Ihrer verwalteten Domäne „contoso100.com“ ist, geben Sie als Bereich „CONTOSO100.COM“ ein. Denken Sie daran, dass der Bereichsname in Großbuchstaben angegeben werden muss.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Konfigurieren der NTP-Einstellungen (NTP = Network Time Protocol) auf dem virtuellen Linux-Computer
Das Datum und die Uhrzeit Ihres virtuellen Ubuntu-Computers müssen mit der verwalteten Domäne synchronisiert werden. Fügen Sie den NTP-Hostnamen Ihrer verwalteten Domäne in die Datei „/etc/ntp.conf“ hinzu.

```
sudo vi /etc/ntp.conf
```

Geben Sie folgenden Wert in die Datei „ntp.conf“ ein, und speichern Sie die Datei:

```
server contoso100.com
```
„contoso100.com“ steht für den DNS-Domänennamen Ihrer verwalteten Domäne.

Synchronisieren Sie jetzt das Datum und die Uhrzeit des virtuellen Ubuntu-Computers mit dem NTP-Server, und starten Sie anschließend den NTP-Dienst:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Einbinden der virtuellen Linux-Computers in die verwaltete Domäne
Nachdem die erforderlichen Pakete auf dem virtuellen Linux-Computer installiert sind, besteht die nächste Aufgabe darin, den virtuellen Computer in die verwaltete Domäne einzubinden.

1. Ermitteln Sie die durch Azure AD-Domänendienste verwaltete Domäne. Geben Sie in Ihrem SSH-Terminal folgenden Befehl ein:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Problembehandlung:** Wenn Ihre verwaltete Domäne über *realm discover* (Bereich ermitteln) nicht gefunden werden kann:
   >   * Stellen Sie durch Pingen sicher, dass die VM die Domäne erreichen kann.
   >   * Überprüfen Sie, ob die VM tatsächlich in demselben virtuellen Netzwerk bereitgestellt wurde, in dem die verwaltete Domäne verfügbar ist.
   >   * Überprüfen Sie, ob Sie die DNS-Servereinstellungen aktualisiert haben, sodass das virtuelle Netzwerk auf die Domänencontroller der verwalteten Domäne verweist.

2. Initialisieren Sie Kerberos. Geben Sie in Ihrem SSH-Terminal folgenden Befehl ein:

    > [!TIP]
    > * Stellen Sie sicher, dass Sie einen Benutzer angeben, der zur Administratorengruppe für Azure AD-Domänencontroller gehört.
    > * Geben Sie den Domänennamen in Großbuchstaben an. Andernfalls führt kinit zu einem Fehler.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Binden Sie den Computer in die Domäne ein. Geben Sie in Ihrem SSH-Terminal folgenden Befehl ein:

    > [!TIP]
    > Verwenden Sie das gleiche Benutzerkonto, das Sie im vorherigen Schritt („kinit“) angegeben haben.
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Wenn der Computer erfolgreich in die verwaltete Domäne eingebunden wurde, sollte eine Meldung angezeigt werden, dass der Computer erfolgreich im Bereich registriert wurde.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Aktualisieren der SSSD-Konfiguration und Neustart des Diensts
1. Geben Sie in Ihrem SSH-Terminal folgenden Befehl ein. Öffnen Sie die Datei „sssd.conf“ und, nehmen Sie folgende Änderung vor
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Kommentieren Sie die Zeile **use_fully_qualified_names = True** aus, und speichern Sie die Datei.
    ```
    # use_fully_qualified_names = True
    ```

3. Starten Sie den SSSD-Dienst neu.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Konfigurieren der automatischen Erstellung des Basisverzeichnisses
Geben Sie folgende Befehle in Ihren PuTTY-Terminal ein, um die automatische Erstellung des Basisverzeichnisses nach der Anmeldung von Benutzern zu aktivieren:
```
sudo vi /etc/pam.d/common-session
```

Fügen Sie in dieser Datei unter der Zeile „session optional pam_sss.so“ folgende Zeile hinzu, und speichern Sie die Datei:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Überprüfen des Domänenbeitritts
Überprüfen Sie, ob der Computer der verwalteten Domäne erfolgreich beigetreten ist. Stellen Sie über eine andere SSH-Verbindung eine Verbindung zur beigetretenen Domäne des virtuellen Ubuntu-Computers her. Verwenden Sie ein Domänenbenutzerkonto, und überprüfen Sie anschließend, ob das Benutzerkonto ordnungsgemäß aufgelöst wurde.

1. Geben Sie in Ihrem SSH-Terminal den folgenden Befehl ein, um über SSH eine Verbindung zu dem der Domäne beigetretenen virtuellen Ubuntu-Computer herzustellen. Verwenden Sie ein Domänenkonto, das zu der verwalteten Domäne gehört (in diesem Fall z.B. 'bob@CONTOSO100.COM').
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. Geben Sie in Ihrem SSH-Terminal den folgenden Befehl ein, um zu ermitteln, ob das Basisverzeichnis ordnungsgemäß initialisiert wurde.
    ```
    pwd
    ```

3. Geben Sie in Ihrem SSH-Terminal den folgenden Befehl ein, um zu ermitteln, ob die Gruppenmitgliedschaften ordnungsgemäß aufgelöst wurden.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Erteilen von sudo-Berechtigungen für die Gruppe „AAD DC-Administratoren“
Sie können Mitgliedern der Gruppe „AAD DC-Administratoren“ Administratorrechte für den virtuellen Ubuntu-Computer erteilen. Die sudo-Datei befindet sich unter /etc/sudoers. Die Mitglieder von AD-Gruppen, die in der Datei „sudoers“ hinzugefügt werden, können sudo ausführen.

1. Stellen Sie in Ihrem SSH-Terminal sicher, dass sie mit Administratorrechten angemeldet sind. Sie können während der Erstellung des virtuellen Computers das von Ihnen angegebene lokale Administratorkonto verwenden. Führen Sie den folgenden Befehl aus:
    ```
    sudo vi /etc/sudoers
    ```

2. Fügen Sie folgenden Eintrag zur Datei „/etc/sudoers“ hinzu, und speichern Sie die Datei:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Sie können sich jetzt als Mitglied der Gruppe „AAD DC-Administratoren“ anmelden und müssten über Administratorrechte für den virtuellen Computer verfügen.


## <a name="troubleshooting-domain-join"></a>Problembehandlung beim Domänenbeitritt
Informationen finden Sie im Artikel [Problembehandlung beim Domänenbeitritt](join-windows-vm.md#troubleshoot-joining-a-domain) .


## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne der Azure AD-Domänendienste](active-directory-ds-admin-guide-join-windows-vm.md)
* [Anmelden bei einem virtuellen Computer unter Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
