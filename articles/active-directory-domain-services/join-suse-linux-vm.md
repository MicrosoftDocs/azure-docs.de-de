---
title: Einbinden einer SLE-VM in Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen virtuellen SUSE Linux Enterprise-Computer in eine durch Azure AD Domain Services verwaltete Domäne einbinden.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619605"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Einbinden eines virtuellen SUSE Linux Enterprise-Computers in eine durch Azure Active Directory Domain Services verwaltete Domäne

Um Benutzern die Möglichkeit einzuräumen, sich mit einem einzigen Satz von Anmeldeinformationen bei virtuellen Computern (VMs) in Azure anzumelden, können Sie VMs in eine durch Azure Active Directory Domain Services (Azure AD DS) verwaltete Domäne einbinden. Wenn Sie eine VM in eine durch Azure AD DS verwaltete Domäne einbinden, können Benutzerkonten und Anmeldeinformationen aus der Domäne zum Anmelden und Verwalten von Servern verwendet werden. Gruppenmitgliedschaften aus der verwalteten Domäne werden ebenfalls angewandt, damit Sie den Zugriff auf Dateien oder Dienste auf der VM steuern können.

In diesem Artikel wird gezeigt, wie Sie eine SUSE Linux Enterprise-VM (SLE) in eine verwaltete Domäne einbinden.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Falls Sie keine solche Domäne haben, gehen Sie wie im ersten Tutorial beschrieben vor, um eine [verwaltete Azure Active Directory Domain Services-Domäne zu erstellen und zu konfigurieren][create-azure-ad-ds-instance].
* Ein Benutzerkonto, das Teil der verwalteten Domäne ist.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Erstellen einer SLE-VM und Herstellen einer Verbindung

Wenn Sie über eine vorhandene SLE-VM in Azure verfügen, stellen Sie über SSH eine Verbindung mit dieser VM her. Dann fahren Sie mit dem nächsten Schritt fort und beginnen mit der [Konfiguration der VM](#configure-the-hosts-file).

Wenn Sie eine SLE-VM erstellen müssen oder eine Test-VM zur Verwendung mit diesem Artikel erstellen möchten, stehen Ihnen folgende Methoden zur Verfügung:

* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Achten Sie beim Erstellen der VM auf die Einstellungen des virtuellen Netzwerks, um sicherzustellen, dass die VM mit der verwalteten Domäne kommunizieren kann:

* Stellen Sie die VM in demselben oder einem mittels Peering verbundenen virtuellen Netzwerk bereit, in dem Sie Azure AD Domain Services aktiviert haben.
* Stellen Sie die VM in einem anderen Subnetz als Ihre verwaltete Azure AD Domain Services-Domäne bereit.

Nachdem die VM bereitgestellt wurde, führen Sie die Schritte zum Herstellen einer Verbindung mit der VM über SSH aus.

## <a name="configure-the-hosts-file"></a>Konfigurieren der Datei „hosts“

Um sicherzustellen, dass der VM-Hostname ordnungsgemäß für die verwaltete Domäne konfiguriert ist, bearbeiten Sie die Datei */etc/hosts*, und legen Sie den Hostnamen fest:

```console
sudo vi /etc/hosts
```

Aktualisieren Sie in der Datei *hosts* die Adresse *localhost*. Siehe folgendes Beispiel:

* *aaddscontoso.com* ist der DNS-Domänenname Ihrer verwalteten Domäne.
* *linux-q2gr* ist der Hostname Ihrer SLE-VM, die Sie in die verwaltete Domäne einbinden.

Aktualisieren Sie diese Namen mit Ihren eigenen Werten:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Anschließend speichern und beenden Sie die Datei *hosts* mit dem Befehl `:wq` im Editor.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Einbinden der VM in die verwaltete Domäne mithilfe von SSSD

Um die VM mithilfe von **SSSD** und dem YaST-Modul *Benutzeranmeldungs-Verwaltung* in die verwaltete Domäne einzubinden, führen Sie die folgenden Schritte aus:

1. Installieren Sie das YaST-Modul *Benutzeranmeldungs-Verwaltung*:

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Öffnen Sie YaST.

1. Um die automatische DNS-Ermittlung später erfolgreich verwenden zu können, konfigurieren Sie die IP-Adressen der verwalteten Domäne (den *Active Directory-Server*) als Namenserver für Ihren Client.

    Wählen Sie in YaST **System > Netzwerkeinstellungen** aus.

1. Wählen Sie die Registerkarte *Hostname/DNS* aus, und geben Sie im Feld *Namenserver 1* die IP-Adresse(n) der verwalteten Domäne ein. Diese IP-Adressen werden im Fenster *Eigenschaften* im Azure-Portal für Ihre verwaltete Domäne angezeigt. Beispiele: *10.0.2.4* und *10.0.2.5*.

    Fügen Sie die IP-Adressen Ihrer verwalteten Domäne hinzu, und klicken Sie auf **OK**.

1. Wählen Sie im YaST-Hauptfenster *Netzwerkdienste* > *Verwaltung von Benutzeranmeldungen* aus.

    Das Modul wird mit einer Übersicht über die verschiedenen Netzwerkeigenschaften Ihres Computers und die derzeit verwendete Authentifizierungsmethode geöffnet, wie im folgenden Screenshot gezeigt:

    ![Beispielscreenshot des Fensters zur Verwaltung von Benutzeranmeldungen in YaST](./media/join-suse-linux-vm/overview-window.png)

    Um mit der Bearbeitung zu beginnen, klicken Sie auf **Einstellungen ändern**.

Führen Sie die folgenden Schritte aus, um die VM in die verwaltete Domäne einzubinden:

1. Wählen Sie im Dialogfeld die Option **Domäne hinzufügen** aus.

1. Geben Sie den richtigen *Domänennamen* an, z. B. *aaddscontoso.com*, und geben Sie dann die Dienste an, die für Identitätsdaten und Authentifizierung verwendet werden sollen. Wählen Sie für beides *Microsoft Active Directory* aus.

    Stellen Sie sicher, dass die Option *Domäne aktivieren* ausgewählt ist.

1. Wählen Sie nach Abschluss des Vorgangs **OK** aus.

1. Akzeptieren Sie die im folgenden Dialogfeld die Standardeinstellungen, und klicken Sie dann auf **OK**.

1. Die VM installiert je nach Bedarf weitere Softwareanwendungen und überprüft dann, ob die verwaltete Domäne verfügbar ist.

    Wenn alles richtig ist, wird das folgende Beispieldialogfeld angezeigt, in dem darauf hingewiesen wird, dass die VM die verwaltete Domäne ermittelt hat, Sie aber *noch nicht registriert* sind.

    ![Beispielscreenshot des Active Directory-Registrierungsfensters in YaST](./media/join-suse-linux-vm/enroll-window.png)

1. Geben Sie im Dialogfeld den *Benutzernamen* und das *Passwort* eines Benutzers an, der zur verwalteten Domäne gehört. Bei Bedarf [fügen Sie ein Benutzerkonto zu einer Gruppe in Azure AD hinzu](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Um sicherzustellen, dass die aktuelle Domäne für Samba aktiviert ist, aktivieren Sie *Samba-Konfiguration gemäß diesem AD überschreiben*.

1. Klicken Sie auf **OK**, um sich zu registrieren.

1. Es wird eine Meldung angezeigt, in der bestätigt wird, dass Sie erfolgreich registriert wurden. Klicken Sie zum Beenden auf **OK**.

Nachdem die VM in der verwalteten Domäne registriert ist, konfigurieren Sie den Clientcomputer mithilfe von *Domänenbenutzeranmeldung verwalten*, wie im folgenden Beispielscreenshot gezeigt:

![Beispielscreenshot des Fensters „Domänenbenutzeranmeldung verwalten“ in YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Um Anmeldungen mithilfe von Daten zu zuzulassen, die von der verwalteten Domäne bereitgestellt wurden, aktivieren Sie das Kontrollkästchen für *Domänenbenutzeranmeldung zulassen*.

1. Optional können Sie unter *Domänendatenquelle aktivieren* weitere Datenquellen auswählen, die für Ihre Umgebung benötigt werden. Sie können beispielsweise angeben, welche Benutzer **sudo** verwenden dürfen oder welche Netzwerklaufwerke verfügbar sind.

1. Um Benutzern in der verwalteten Domäne die Einrichtung von Startverzeichnissen auf der VM zu gestatten, aktivieren Sie das Kontrollkästchen für *Home-Verzeichnis erstellen*.

1. Wählen Sie in der Seitenleiste **Dienstoptionen > Name umschalten** und dann *Erweiterte Optionen* aus. Wählen Sie in diesem Fenster entweder *fallback_homedir* oder *override_homedir* aus, und klicken Sie dann auf **Hinzufügen**.

1. Geben Sie einen Wert für den Speicherort des Homeverzeichnisses an. Damit Startverzeichnisse dem Format */home/BENUTZERNAME* folgen, verwenden Sie */home/%u*. Weitere Informationen zu möglichen Variablen finden Sie auf der sssd.conf-Manpage (`man 5 sssd.conf`) im Abschnitt *override_homedir*.

1. Klicken Sie auf **OK**.

1. Klicken Sie zum Speichern der Änderungen auf **OK**. Stellen Sie anschließend sicher, dass die angezeigten Werte richtig sind. Zum Verlassen des Dialogfelds klicken Sie auf **Abbrechen**.

1. Wenn Sie SSSD und Winbind gleichzeitig ausführen möchten (um beispielsweise den Domänenbeitritt über SSSD abzuwickeln, aber einen Samba-Dateiserver auszuführen), legen Sie die Samba-Option *Kerberos-Methode* in „smb.conf“ auf *Geheimnisse und Schlüsseltabelle* fest. Die SSSD-Option *ad_update_samba_machine_account_password* muss in „sssd.conf“ auf *true* festgelegt werden. Diese Optionen sorgen dafür, dass die Schlüsseltabelle des Systems immer synchron ist.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Einbinden der VM in die verwaltete Domäne mithilfe von Winbind

Um die VM mithilfe von **Winbind** und dem YaST-Modul *Windows-Domänenmitgliedschaft* in die verwaltete Domäne einzubinden, führen Sie die folgenden Schritte aus:

1. Wählen Sie in YaST die Optionen **Netzwerkdienste > Windows-Domänenmitgliedschaft** aus.

1. Geben Sie auf dem Bildschirm *Windows-Domänenmitgliedschaft* unter *Domäne oder Arbeitsgruppe* die gewünschte Domäne an. Geben Sie den Namen der verwalteten Domäne ein, z. B. *aaddscontoso.com*.

    ![Beispielscreenshot des Fensters „Windows-Domänenmitgliedschaft“ in YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Um die SMB-Quelle für die Linux-Authentifizierung zu verwenden, aktivieren Sie das Kontrollkästchen *SMB-Informationen für Linux-Authentifizierung verwenden*.

1. Wenn Sie auf der VM automatisch ein lokales Homeverzeichnis für Benutzer der verwalteten Domäne erstellen möchten, aktivieren Sie das Kontrollkästchen *Home-Verzeichnis bei Anmeldung erstellen*.

1. Aktivieren Sie das Kontrollkästchen *Offline-Authentifizierung*, um Ihren Domänenbenutzern die Anmeldung zu erlauben, auch wenn die verwaltete Domäne vorübergehend nicht verfügbar ist.

1. Wenn Sie die UID- und GID-Bereiche für Samba-Benutzer und -Gruppen ändern möchten, wählen Sie *Einstellungen für Experten* aus.

1. Konfigurieren Sie die Network Time Protocol (NTP)-Uhrzeitsynchronisierung für Ihre verwaltete Domäne, indem Sie *NTP-Konfiguration* auswählen. Geben Sie die IP-Adressen der verwalteten Domäne ein. Diese IP-Adressen werden im Fenster *Eigenschaften* im Azure-Portal für Ihre verwaltete Domäne angezeigt. Beispiele: *10.0.2.4* und *10.0.2.5*.

1. Klicken Sie auf **OK**, und bestätigen Sie den Domänenbeitritt, wenn Sie dazu aufgefordert werden.

1. Geben Sie das Kennwort für einen Administrator in der verwalteten Domäne ein, und klicken Sie auf **OK**.

    ![Beispielscreenshot des Dialogfelds für die Authentifizierung beim Einbinden einer SLE-VM in die verwaltete Domäne](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Nachdem Sie der verwalteten Domäne beigetreten sind, können Sie sich von Ihrer Arbeitsstation aus über den Anzeige-Manager Ihres Desktops oder über die Konsole bei der Domäne anmelden.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Einbinden der VM in die verwaltete Domäne mithilfe von Winbind über die YaST-Befehlszeilenschnittstelle

So treten Sie der verwalteten Domäne mithilfe von **winbind** und über die *YaST-Befehlszeilenschnittstelle* bei:

* Treten Sie der Domäne bei:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Einbinden der VM in die verwaltete Domäne mithilfe von Winbind über das Terminal

So treten Sie der verwalteten Domäne mithilfe von **winbind** und des Befehls *`samba net` bei*:

1. Installieren Sie den Kerberos-Client und „samba-winbind“:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Bearbeiten Sie die Konfigurationsdateien:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind.conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Überprüfen Sie, ob Datum und Uhrzeit in Azure AD und Linux synchronisiert sind. Dazu können Sie den Azure AD-Server dem NTP-Dienst hinzufügen:
   
   1. Fügen Sie die folgende Zeile zu „/etc/etp.conf“ hinzu:
     
      ```console
      server aaddscontoso.com
      ```

   1. Starten Sie den NTP-Dienst neu:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Treten Sie der Domäne bei:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Aktivieren Sie „winbind“ in den Linux-Modulen für austauschbare Authentifizierung (Pluggable Authentication Modules, PAM):

   ```console
   pam-config --add --winbind
   ```

6. Aktivieren Sie die automatische Erstellung von Basisverzeichnissen, damit sich Benutzer anmelden können:

   ```console
   pam-config -a --mkhomedir
   ```

7. Starten Sie, und aktivieren Sie den „winbind“-Dienst:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>Zulassen der Kennwortauthentifizierung für SSH

Standardmäßig können sich Benutzer nur mithilfe der auf einen öffentlichen Schlüssel basierenden SSH-Authentifizierung bei einer VM anmelden. Die kennwortbasierte Authentifizierung schlägt fehl. Wenn Sie die VM in eine verwalteten Domäne einbinden, müssen diese Domänenkonten die kennwortbasierte Authentifizierung verwenden. Aktualisieren Sie die SSH-Konfiguration wie folgt, um die kennwortbasierte Authentifizierung zuzulassen.

1. Öffnen Sie die Datei *sshd_conf* mit einem Editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Aktualisieren Sie die Zeile für *PasswordAuthentication* in *yes*:

    ```console
    PasswordAuthentication yes
    ```

    Anschließend speichern und beenden Sie die Datei *sshd_conf* mit dem Befehl `:wq` im Editor.

1. Starten Sie den SSH-Dienst neu, um die Änderungen zu übernehmen und Benutzern das Anmelden mit einem Kennwort zu ermöglichen:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Erteilen von sudo-Berechtigungen für die Gruppe „AAD DC-Administratoren“

Um Mitgliedern der Gruppe *AAD DC-Administratoren* Administratorrechte für die SLE-VM zu erteilen, fügen Sie */etc/sudoers* einen Eintrag hinzu. Nach dem Hinzufügen können Mitglieder der Gruppe *AAD DC-Administratoren* den Befehl `sudo` auf der SLE-VM verwenden.

1. Öffnen Sie die Datei *sudoers* zur Bearbeitung:

    ```console
    sudo visudo
    ```

1. Fügen Sie den folgenden Eintrag am Ende der Datei */etc/sudoers* hinzu. Die Gruppe *AAD DC-Administratoren* enthält Leerzeichen im Namen. Fügen Sie deshalb den umgekehrten Schrägstrich als Escapezeichen in den Gruppennamen ein. Fügen Sie den Namen Ihrer Domäne (z. B. *aaddscontoso.com*) hinzu:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Anschließend speichern und beenden Sie den Editor mit dem Befehl `:wq` im Editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Anmelden bei der VM mit einem Domänenkonto

Um zu überprüfen, ob die VM erfolgreich in die verwaltete Domäne eingebunden wurde, starten Sie eine neue SSH-Verbindung mithilfe eines Domänenbenutzerkontos. Vergewissern Sie sich, dass ein Basisverzeichnis erstellt wurde und die Gruppenmitgliedschaft aus der Domäne angewendet wird.

1. Erstellen Sie eine neue SSH-Verbindung über die Konsole. Verwenden Sie ein Domänenkonto, das der verwalteten Domäne angehört, verwenden Sie den Befehl `ssh -l` (z. B. `contosoadmin@aaddscontoso.com`), und geben Sie dann die Adresse Ihrer VM ein (z. B. *linux-q2gr.aaddscontoso.com*). Bei Verwendung von Azure Cloud Shell verwenden Sie die öffentliche IP-Adresse der VM anstelle des internen DNS-Namens.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Wenn Sie erfolgreich eine Verbindung mit der VM hergestellt haben, vergewissern Sie sich, dass das Basisverzeichnis ordnungsgemäß initialisiert wurde:

    ```console
    pwd
    ```

    Sie sollten sich mit Ihrem eigenen Verzeichnis, das dem Benutzerkonto entspricht, im Verzeichnis */home* befinden.

3. Überprüfen Sie nun, ob die Gruppenmitgliedschaften ordnungsgemäß aufgelöst werden:

    ```console
    id
    ```

    Ihre Gruppenmitgliedschaften aus der verwalteten Domäne sollten angezeigt werden.

4. Wenn Sie sich als Mitglied der Gruppe *AAD DC-Administratoren* bei der VM angemeldet haben, überprüfen Sie, ob Sie den Befehl `sudo` ordnungsgemäß verwenden können:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Nächste Schritte

Wenn beim Verbinden der VM mit der verwalteten Domäne oder bei der Anmeldung mit einem Domänenkonto Probleme auftreten, lesen Sie [Behandeln von Problemen mit dem Einbinden in eine Domäne](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
