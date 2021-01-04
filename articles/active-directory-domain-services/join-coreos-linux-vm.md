---
title: Einbinden eines virtuellen CoreOS-Computers in Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen virtuellen CoreOS-Computer in eine durch Azure AD Domain Services verwaltete Domäne einbinden.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619588"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Einbinden eines virtuellen CoreOS-Computers in eine verwaltete Azure Active Directory Domain Services-Domäne

Um Benutzern die Möglichkeit einzuräumen, sich mit einem einzigen Satz von Anmeldeinformationen bei virtuellen Computern (Virtual Machines, VMs) in Azure anzumelden, können Sie VMs in eine von Azure Active Directory Domain Services (Azure AD DS) verwaltete Domäne einbinden. Wenn Sie eine VM in eine durch Azure AD DS verwaltete Domäne einbinden, können Benutzerkonten und Anmeldeinformationen aus der Domäne zum Anmelden und Verwalten von Servern verwendet werden. Gruppenmitgliedschaften aus der verwalteten Domäne werden ebenfalls angewendet, damit Sie den Zugriff auf Dateien oder Dienste auf der VM steuern können.

In diesem Artikel wird beschrieben, wie Sie einen virtuellen CoreOS-Computer in eine verwaltete Domäne einbinden.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Falls Sie keine solche Domäne haben, gehen Sie wie im ersten Tutorial beschrieben vor, um eine [verwaltete Azure Active Directory Domain Services-Domäne zu erstellen und zu konfigurieren][create-azure-ad-ds-instance].
* Ein Benutzerkonto, das Teil der verwalteten Domäne ist.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Erstellen einer CoreOS Linux-VM und Herstellen einer Verbindung

Wenn Sie über eine vorhandene CoreOS Linux-VM in Azure verfügen, stellen Sie über SSH eine Verbindung mit dieser VM her. Dann fahren Sie mit dem nächsten Schritt fort und beginnen mit der [Konfiguration der VM](#configure-the-hosts-file).

Wenn Sie eine CoreOS Linux-VM erstellen müssen oder eine Test-VM zur Verwendung mit diesem Artikel erstellen möchten, können Sie eine der folgenden Methoden verwenden:

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
* *coreos* ist der Hostname Ihrer CoreOS-VM, die Sie in die verwaltete Domäne einbinden.

Aktualisieren Sie diese Namen mit Ihren eigenen Werten:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Anschließend speichern und beenden Sie die Datei *hosts* mit dem Befehl `:wq` im Editor.

## <a name="configure-the-sssd-service"></a>Konfigurieren des SSSD-Diensts

Aktualisieren Sie die SSSD-Konfigurationsdatei */etc/sssd/sssd.conf*.

```console
sudo vi /etc/sssd/sssd.conf
```

Geben Sie für die folgenden Parameter den Namen Ihrer eigenen verwalteten Domäne an:

* *domains* in Großbuchstaben
* *[domain/AADDSCONTOSO]* mit AADDSCONTOSO in Großbuchstaben
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* in Großbuchstaben

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Einbinden der VM in die verwaltete Domäne

Nachdem Sie die SSSD-Konfigurationsdatei aktualisiert haben, binden Sie nun den virtuellen Computer in die verwaltete Domäne ein.

1. Verwenden Sie zunächst den Befehl `adcli info`, um zu überprüfen, ob Sie Informationen über die verwaltete Domäne anzeigen können. Im folgenden Beispiel werden Informationen für die Domäne *AADDSCONTOSO.COM* abgerufen. Geben Sie den Namen Ihrer eigenen verwalteten Domäne in GROSSBUCHSTABEN an:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Wenn mit dem Befehl `adcli info` Ihre verwaltete Domäne nicht gefunden werden kann, führen Sie die folgenden Schritte zur Problembehandlung aus:

    * Vergewissern Sie sich, dass die VM die Domäne erreichen kann. Versuchen Sie `ping aaddscontoso.com`, um zu überprüfen, ob eine positive Antwort zurückgegeben wird.
    * Überprüfen Sie, ob die VM in demselben oder einem mittels Peering verbundenen virtuellen Netzwerk bereitgestellt wurde, in dem die verwaltete Domäne verfügbar ist.
    * Vergewissern Sie sich, dass die DNS-Servereinstellungen für das virtuelle Netzwerk so aktualisiert wurden, dass auf die Domänencontroller der verwalteten Domäne verwiesen wird.

1. Binden Sie jetzt die VM mit dem Befehl `adcli join` in die verwaltete Domäne ein. Geben Sie einen Benutzer an, der Mitglied der verwalteten Domäne ist. Bei Bedarf [fügen Sie ein Benutzerkonto zu einer Gruppe in Azure AD hinzu](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Auch hier muss der Name der verwalteten Domäne in GROßBUCHSTABEN eingegeben werden. Im folgenden Beispiel wird das Konto mit dem Namen `contosoadmin@aaddscontoso.com` zum Initialisieren von Kerberos verwendet. Geben Sie Ihr eigenes Benutzerkonto ein, das Teil der verwalteten Domäne ist.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    Mit dem Befehl `adcli join` werden keine Informationen darüber zurückgegeben, ob die VM erfolgreich in die verwaltete Domäne eingebunden wurde.

1. Starten Sie den SSSD-Dienst, um die Konfiguration für das Einbinden in die Domäne anzuwenden:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Anmelden bei der VM mit einem Domänenkonto

Um zu überprüfen, ob die VM erfolgreich in die verwaltete Domäne eingebunden wurde, starten Sie eine neue SSH-Verbindung mithilfe eines Domänenbenutzerkontos. Vergewissern Sie sich, dass ein Basisverzeichnis erstellt wurde und die Gruppenmitgliedschaft aus der Domäne angewendet wird.

1. Erstellen Sie eine neue SSH-Verbindung über die Konsole. Verwenden Sie mithilfe des Befehls `ssh -l` ein Domänenkonto, das der verwalteten Domäne angehört (z. B. `contosoadmin@aaddscontoso.com`), und geben Sie dann die Adresse Ihres virtuellen Computers (z. B. *coreos.aaddscontoso.com*) ein. Bei Verwendung von Azure Cloud Shell verwenden Sie die öffentliche IP-Adresse der VM anstelle des internen DNS-Namens.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Überprüfen Sie nun, ob die Gruppenmitgliedschaften ordnungsgemäß aufgelöst werden:

    ```console
    id
    ```

    Es sollten Ihre Gruppenmitgliedschaften aus der verwalteten Domäne angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Wenn beim Verbinden der VM mit der verwalteten Domäne oder bei der Anmeldung mit einem Domänenkonto Probleme auftreten, lesen Sie [Behandeln von Problemen mit dem Einbinden in eine Domäne](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
