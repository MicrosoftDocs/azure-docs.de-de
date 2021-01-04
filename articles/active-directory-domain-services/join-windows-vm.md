---
title: Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Azure AD Domain Services-Domäne | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Windows Server-Computer in eine verwaltete Azure Active Directory Domain Services-Domäne einbinden.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 869c827485d9b7a6baf68d2619af98d4c2ee82b9
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619571"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Azure Active Directory Domain Services-Domäne

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung, die mit Windows Server Active Directory vollständig kompatibel sind. Mit einer verwalteten Azure AD DS-Domäne können Sie Features für den Domänenbeitritt und Verwaltungsfunktionen für virtuelle Computer (VMs) in Azure bereitstellen. In diesem Tutorial erfahren Sie, wie Sie einen virtuellen Windows Server-Computer erstellen und dann in eine verwaltete Domäne einbinden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Windows Server-VM
> * Herstellen einer Verbindung zwischen dem virtuellen Windows Server-Computer und einem virtuellen Azure-Netzwerk
> * Einbinden der VM in die verwaltete Domäne

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.
* Ein Benutzerkonto, das Teil der verwalteten Domäne ist.
    * Stellen Sie sicher, dass die Azure AD Connect-Kennworthashsynchronisierung oder die Self-Service-Kennwortzurücksetzung ausgeführt wurde, damit Sie sich mit dem Konto bei der verwalteten Domäne anmelden können.
* Einen in Ihrem virtuellen Azure AD DS-Netzwerk bereitgestellten Azure Bastion-Host.
    * Eine Anleitung zum Erstellen eines Azure Bastion-Hosts finden Sie [hier][azure-bastion].

Wenn Sie bereits über einen virtuellen Computer verfügen, den Sie in die Domäne einbinden möchten, fahren Sie mit dem Abschnitt [Einbinden der VM in die verwaltete Domäne](#join-the-vm-to-the-managed-domain) fort.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Tutorial erstellen Sie im Azure-Portal einen virtuellen Windows Server-Computer, der in Ihre verwaltete Domäne eingebunden werden soll. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-windows-server-virtual-machine"></a>Erstellen einer Windows-VM

Um aufzuzeigen, wie ein Computer in eine verwaltete Domäne eingebunden wird, erstellen wir zunächst einen virtuellen Windows Server-Computer. Dieser virtuelle Computer ist mit einem virtuellen Azure-Netzwerk verbunden, das Konnektivität mit der verwalteten Domäne ermöglicht. Der Vorgang zum Einbinden in eine verwaltete Domäne entspricht dem Einbinden in eine normale lokale Active Directory Domain Services-Domäne.

Wenn Sie bereits über einen virtuellen Computer verfügen, den Sie in die Domäne einbinden möchten, fahren Sie mit dem Abschnitt [Einbinden der VM in die verwaltete Domäne](#join-the-vm-to-the-managed-domain) fort.

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.

1. Wählen Sie unter **Erste Schritte** die Option **Windows Server 2016 Datacenter** aus.

    ![Erstellen einer Windows Server-2016 Datacenter-VM im Azure-Portal](./media/join-windows-vm/select-vm-image.png)

1. Konfigurieren Sie im Bereich **Grundlagen** die wichtigsten Einstellungen für den virtuellen Computer. Übernehmen Sie die Standardeinstellungen für *Verfügbarkeitsoptionen*, *Image* und *Größe*.

    | Parameter            | Vorgeschlagener Wert   |
    |----------------------|-------------------|
    | Resource group       | Wählen Sie eine Ressourcengruppe wie z. B. *myResourceGroup* aus, oder erstellen Sie eine solche. |
    | Name des virtuellen Computers | Geben Sie einen Namen für die VM ein, z. B. *myVM*. |
    | Region               | Wählen Sie die Region aus, in der Ihre VM erstellt werden soll, z. B. *USA, Osten*. |
    | Username             | Geben Sie einen Benutzernamen für das lokale Administratorkonto ein, das auf der VM erstellt werden soll, z. B. *azureuser*. |
    | Kennwort             | Geben Sie ein sicheres Kennwort für das auf der VM zu erstellende lokale Administratorkonto ein, und bestätigen Sie es. Geben Sie nicht die Anmeldeinformationen eines Domänenbenutzerkontos ein. |

1. Auf virtuelle Computer, die in Azure erstellt werden, kann standardmäßig nicht per RDP über das Internet zugegriffen werden. Bei aktiviertem Remotedesktopprotokoll sind Angriffe durch automatisierte Anmeldeversuche wahrscheinlich, wodurch Konten mit gängigen Namen wie *Admin* oder *Administrator* aufgrund mehrerer fehlerhafter Anmeldeversuche deaktiviert werden können.

    RDP sollte nur aktiviert werden, wenn es benötigt wird, und es sollte auf eine Reihe autorisierter IP-Adressbereiche beschränkt werden. Diese Konfiguration trägt zur Sicherheit der VM bei und reduziert potenzielle Angriffsflächen. Alternativ können Sie einen Azure Bastion-Host erstellen und verwenden, der den Zugriff auf das Azure-Portal nur über TLS zulässt. Im nächsten Schritt dieses Tutorials wird ein Azure Bastion-Host verwendet, um eine sichere Verbindung mit dem virtuellen Computer herzustellen.

    Wählen Sie unter **Öffentliche Eingangsports** die Option *Keine* aus.

1. Klicken Sie dann auf **Weiter: Datenträger**.
1. Wählen Sie im Dropdownmenü **Typ des Betriebssystemdatenträgers** die Option *SSD Standard* aus, und klicken Sie dann auf **Weiter: Netzwerk** aus.
1. Ihre VM muss eine Verbindung mit einem Subnetz eines virtuellen Azure-Netzwerks herstellen, das mit dem Subnetz kommunizieren kann, in dem Ihre verwaltete Domäne bereitgestellt ist. Es wird empfohlen, eine verwaltete Domäne in einem eigenen dedizierten Subnetz bereitzustellen. Stellen Sie Ihre VM nicht in demselben Subnetz wie Ihre verwaltete Domäne bereit.

    Es gibt zwei Hauptmethoden, die VM bereitzustellen und eine Verbindung mit einem geeigneten virtuellen Subnetz herzustellen:
    
    * Erstellen Sie ein Subnetz in dem virtuellen Netzwerk, in dem Ihre verwaltete Domäne bereitgestellt ist, oder wählen Sie ein entsprechendes Subnetz aus.
    * Wählen Sie ein Subnetz in einem virtuellen Azure-Netzwerk aus, das per [Peering in virtuellen Azure-Netzwerken][vnet-peering] mit diesem verbunden ist.
    
    Wenn Sie ein virtuelles Subnetz auswählen, das nicht mit dem Subnetz für Ihre verwaltete Domäne verbunden ist, können Sie die VM nicht in die verwaltete Domäne einbinden. In diesem Tutorial erstellen wir ein neues Subnetz im virtuellen Azure-Netzwerk.

    Wählen Sie im Bereich **Netzwerk** das virtuelle Netzwerk aus, in dem Ihre verwaltete Domäne bereitgestellt wurde, z. B. *aaads-vnet*.
1. In diesem Beispiel wird das vorhandene Subnetz *aaads-subnet* angezeigt, mit dem die verwaltete Domäne verbunden ist. Verbinden Sie Ihre VM nicht mit diesem Subnetz. Um ein Subnetz für die VM zu erstellen, wählen Sie **Subnetzkonfiguration verwalten** aus.

    ![Auswählen von „Subnetzkonfiguration verwalten“ im Azure-Portal](./media/join-windows-vm/manage-subnet.png)

1. Wählen Sie im Menü auf der linken Seite des Fensters des virtuellen Netzwerks die Option **Adressraum** aus. Das virtuelle Netzwerk wird mit einem einzelnen Adressraum (*10.0.2.0/24*) erstellt. Dieser wird vom Standardsubnetz verwendet. Möglicherweise sind auch andere Subnetze verfügbar, z. B. für *Workloads* oder Azure Bastion.

    Fügen Sie dem virtuellen Netzwerk einen zusätzlichen IP-Adressbereich hinzu. Die Größe dieses Adressbereichs und der tatsächlich zu verwendende IP-Adressbereich hängen von anderen, bereits bereitgestellten Netzwerkressourcen ab. Der IP-Adressbereich darf sich nicht mit vorhandenen Adressbereichen in Ihrer Azure-Umgebung oder in Ihrer lokalen Umgebung überschneiden. Wählen Sie die Größe des IP-Adressbereichs so, dass sie für die Anzahl virtueller Computer ausreicht, die voraussichtlich im Subnetz bereitgestellt werden.

    Im folgenden Beispiel wird ein zusätzlicher IP-Adressbereich (*10.0.5.0/24*) hinzugefügt. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

    ![Hinzufügen eines zusätzlichen IP-Adressbereichs für das virtuelle Netzwerk im Azure-Portal](./media/join-windows-vm/add-vnet-address-range.png)

1. Wählen Sie als Nächstes im Menü auf der linken Seite des Fensters für das virtuelle Netzwerk die Option **Subnetze** und anschließend **+ Subnetz** aus, um ein Subnetz hinzuzufügen.

1. Wählen Sie **+ Subnetz** aus, und geben Sie einen Namen für das Subnetz ein, z. B. *management*. Geben Sie einen **Adressbereich (CIDR-Block)** an, z. B. *10.0.5.0/24*. Stellen Sie sicher, dass sich dieser Adressbereich nicht mit einem vorhandenen Azure- oder lokalen Adressbereich überschneidet. Behalten Sie bei den anderen Optionen die Standardwerte bei, und klicken Sie auf **OK**.

    ![Erstellen einer Subnetzkonfiguration im Azure-Portal](./media/join-windows-vm/create-subnet.png)

1. Das Erstellen des Subnetzes dauert einige Sekunden. Klicken Sie nach dem Erstellen auf das *X*, um das Subnetzfenster zu schließen.
1. Wählen Sie für die VM-Erstellung im Bereich **Netzwerk** im Dropdownmenü das erstellte Subnetz aus (hier also *management*). Vergewissern Sie sich, dass Sie das richtige Subnetz auswählen und die VM nicht in demselben Subnetz bereitstellen wie Ihre verwaltete Domäne.
1. Wählen Sie im Dropdownmenü unter **Öffentliche IP-Adresse** die Option *Keine* aus. Da in diesem Tutorial Azure Bastion verwendet wird, um eine Verbindung mit der Verwaltung herzustellen, muss dem virtuellen Computer keine öffentliche IP-Adresse zugewiesen werden.
1. Behalten Sie bei den anderen Optionen die Standardwerte bei, und klicken Sie auf **Verwaltung**.
1. Legen Sie **Startdiagnose** auf *Aus* fest. Behalten Sie bei den anderen Optionen die Standardwerte bei, und klicken Sie auf **Überprüfen + erstellen**.
1. Überprüfen Sie die VM-Einstellungen, und klicken Sie dann auf **Erstellen**.

Das Erstellen der VM dauert einige Minuten. Im Azure-Portal wird der Status der Bereitstellung angezeigt. Wenn die VM bereit ist, wählen Sie **Zu Ressource wechseln** aus.

![Wechseln zur VM-Ressource im Azure-Portal nach der erfolgreichen Erstellung](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Herstellen einer Verbindung mit der Windows Server-VM

Verwenden Sie einen Azure Bastion-Host, um eine sichere Verbindung mit Ihren virtuellen Computern herzustellen. Bei Verwendung von Azure Bastion wird in Ihrem virtuellen Netzwerk ein verwalteter Host bereitgestellt, der webbasierte RDP- oder SSH-Verbindungen mit virtuellen Computern ermöglicht. Für die virtuellen Computer sind keine öffentlichen IP-Adressen erforderlich, und Sie müssen keine Netzwerksicherheitsgruppen-Regeln für externen Remotedatenverkehr öffnen. Die Verbindung mit virtuellen Computern wird im Webbrowser über das Azure-Portal hergestellt. Eine Anleitung zum Erstellen eines Azure Bastion-Hosts finden Sie [hier][azure-bastion].

Gehen Sie wie folgt vor, um für die Verbindungsherstellung mit Ihrem virtuellen Computer einen Bastionhost zu verwenden:

1. Wählen Sie im Bereich **Übersicht** für Ihren virtuellen Computer zuerst **Verbinden** und dann **Bastion** aus.

    ![Herstellen einer Verbindung mit einem virtuellen Windows-Computer unter Verwendung von Bastion im Azure-Portal](./media/join-windows-vm/connect-to-vm.png)

1. Geben Sie die Anmeldeinformationen für Ihren virtuellen Computer aus dem vorherigen Abschnitt ein, und wählen Sie anschließend **Verbinden** aus.

   ![Herstellen einer Verbindung über den Bastionhost im Azure-Portal](./media/join-windows-vm/connect-to-bastion.png)

Lassen Sie in Ihrem Webbrowser bei Bedarf das Öffnen von Popups zu, damit die Bastion-Verbindung angezeigt wird. Es dauert einige Sekunden, bis die Verbindung mit Ihrem virtuellen Computer hergestellt wurde.

## <a name="join-the-vm-to-the-managed-domain"></a>Einbinden der VM in die verwaltete Domäne

Nach dem Erstellen des virtuellen Computers und dem Herstellen einer webbasierten RDP-Verbindung mit Azure Bastion binden wir den virtuellen Windows Server-Computer in die verwaltete Domäne ein. Dieser Prozess ist der gleiche wie beim Herstellen einer Verbindung eines Computers mit einer regulären lokalen Active Directory Domain Services-Domäne.

1. Wenn **Server-Manager** bei der Anmeldung beim virtuellen Computer nicht standardmäßig geöffnet wird, wählen Sie das **Startmenü** und dann **Server-Manager** aus.
1. Wählen Sie im linken Bereich des **Server-Manager**-Fensters die Option **Lokaler Server**. Wählen Sie im rechten Bereich unter **Eigenschaften** die Option **Arbeitsgruppe** aus.

    ![Öffnen des Server-Managers auf der VM und Bearbeiten der Eigenschaften für die Arbeitsgruppe](./media/join-windows-vm/server-manager.png)

1. Wählen Sie im Fenster **Systemeigenschaften** die Option **Ändern** aus, um die verwaltete Domäne einzubinden.

    ![Ändern der Arbeitsgruppen- oder Domäneneigenschaften](./media/join-windows-vm/change-domain.png)

1. Geben Sie im Feld **Domäne** den Namen Ihrer verwalteten Domäne (z. B. *aaddscontoso.com*) an, und wählen Sie dann **OK** aus.

    ![Angeben der verwalteten Domäne für die Einbindung](./media/join-windows-vm/join-domain.png)

1. Geben Sie für die Einbindung in die Domäne Domänenanmeldeinformationen ein. Geben Sie Anmeldeinformationen für einen Benutzer an, der der verwalteten Domäne angehört. Das Konto muss Teil der verwalteten Domäne oder des Azure AD-Mandanten sein. Konten aus externen Verzeichnissen, die Ihrem Azure AD-Mandanten zugeordnet sind, können während der Einbindung in die Domäne nicht richtig authentifiziert werden.

    Anmeldeinformationen können auf eine der folgenden Arten angegeben werden:

    * **UPN-Format** (empfohlen): Geben Sie das Suffix für den Benutzerprinzipalnamen (User Principal Name, UPN) für das Benutzerkonto an, wie in Azure AD konfiguriert. Das UPN-Suffix des Benutzers *contosoadmin* würde beispielsweise `contosoadmin@aaddscontoso.onmicrosoft.com` lauten. Es gibt einige Anwendungsfälle, in denen das UPN-Format zuverlässig anstelle des *SAMAccountName*-Formats zum Anmelden bei der Domäne verwendet werden kann:
        * Wenn das UPN-Präfix eines Benutzers lang ist (z.B. *Janwirklichlangerbenutzername*), wird der *SAMAccountName* möglicherweise automatisch generiert.
        * Falls mehrere Benutzer in Ihrem Azure AD-Mandanten das gleiche UPN-Präfix besitzen (z.B. *jan*), wird ihr *SAMAccountName*-Format möglicherweise automatisch generiert.
    * **SAMAccountName-Format**: Geben Sie den Kontonamen im Format *SAMAccountName* an. Der *SAMAccountName* des Benutzers *contosoadmin* würde beispielsweise `AADDSCONTOSO\contosoadmin` lauten.

1. Das Einbinden in die verwaltete Domäne dauert einige Sekunden. Nach Abschluss werden Sie mit folgender Meldung in der Domäne begrüßt:

    ![Willkommen in der Domäne](./media/join-windows-vm/join-domain-successful.png)

    Klicken Sie auf **OK** , um fortzufahren.

1. Um den Vorgang zum Einbinden in die verwaltete Domäne abzuschließen, starten Sie die VM neu.

> [!TIP]
> Sie können eine VM mit PowerShell mit dem Cmdlet [Add-Computer][add-computer] in eine Domäne einbinden. Im folgenden Beispiel wird die Domäne *AADDSCONTOSO* eingebunden und der virtuelle Computer neu gestartet. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen für einen Benutzer ein, der Teil der verwalteten Domäne ist:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> Wenn Sie eine VM in die Domäne einbinden möchten, ohne eine Verbindung damit herzustellen und die Verbindung manuell zu konfigurieren, können Sie das Azure PowerShell-Cmdlet [Set-AzVmAdDomainExtension][set-azvmaddomainextension] nutzen.

Nachdem der virtuelle Windows Server-Computer neu gestartet wurde, werden alle in der verwalteten Domäne angewendeten Richtlinien an den virtuellen Computer gepusht. Sie können sich jetzt auch mit den geeigneten Domänenanmeldeinformationen bei der Windows Server-VM anmelden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Im nächsten Tutorial verwenden Sie diese Windows Server-VM, um die Verwaltungstools zu installieren, mit denen Sie die verwaltete Domäne verwalten. Wenn Sie die Tutorialreihe nicht fortsetzen möchten, führen Sie die folgenden Bereinigungsschritte zum [Löschen des virtuellen Computers](#delete-the-vm) aus. Andernfalls [fahren Sie mit dem nächsten Tutorial fort](#next-steps).

### <a name="unjoin-the-vm-from-the-managed-domain"></a>Entfernen des virtuellen Computers aus der verwalteten Domäne

Führen Sie zum Entfernen der VM aus der verwalteten Domäne erneut die Schritte zum [Einbinden der VM in eine Domäne](#join-the-vm-to-the-managed-domain) aus. Statt die VM in die verwaltete Domäne einzubinden, wählen Sie die Einbindung in eine Arbeitsgruppe (z. B. in die Standardgruppe *WORKGROUP*) aus. Nach dem Neustart der VM wird das Computerobjekt aus der verwalteten Domäne entfernt.

Wenn Sie die [VM löschen](#delete-the-vm), ohne sie aus der Domäne zu entfernen, bleibt ein verwaistes Computerobjekt in Azure AD DS zurück.

### <a name="delete-the-vm"></a>Löschen der virtuellen Computer

Wenn Sie diese Windows Server-VM nicht weiterverwenden möchten, führen Sie folgende Schritte aus, um sie zu löschen:

1. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.
1. Wählen Sie Ihre Ressourcengruppe aus, z. B. *myResourceGroup*.
1. Wählen Sie Ihre VM aus, z. B. *myVM*, und klicken Sie dann auf **Löschen**. Klicken Sie auf **Ja**, um das Löschen der Ressource zu bestätigen. Das Löschen der VM dauert einige Minuten.
1. Nachdem die VM gelöscht wurde, wählen Sie den Betriebssystemdatenträger, die Netzwerkschnittstellenkarte sowie weitere Ressourcen mit dem Präfix *myVM-* aus, und löschen Sie sie.

## <a name="troubleshoot-domain-join-issues"></a>Behandeln von Problemen mit dem Einbinden in eine Domäne

Die erfolgreiche Einbindung der Windows Server-VM in die verwaltete Domäne sollte auf die gleiche Weise erfolgen wie das Einbinden eines normalen lokalen Computers in eine Active Directory Domain Services-Domäne. Wenn die Windows Server-VM nicht in die verwaltete Domäne eingebunden werden kann, weist dies darauf hin, dass ein Problem mit der Konnektivität oder den Anmeldeinformationen besteht. Sehen Sie sich die folgenden Abschnitte zur Problembehandlung an, um die VM erfolgreich in die verwaltete Domäne einzubinden.

### <a name="connectivity-issues"></a>Konnektivitätsprobleme

Wenn Sie beim Einbinden in die Domäne nicht zur Eingabe von Anmeldeinformationen aufgefordert werden, besteht ein Konnektivitätsproblem. Die VM kann die verwaltete Domäne im virtuellen Netzwerk nicht erreichen.

Führen Sie die folgenden Problembehandlungsschritte aus, und versuchen Sie erneut, die Windows Server-VM in die verwaltete Domäne einzubinden.

* Überprüfen Sie, ob die VM mit dem virtuellen Netzwerk verbunden ist, in dem Azure AD DS aktiviert ist, oder eine Peeringnetzwerkverbindung nutzt.
* Versuchen Sie, den DNS-Domänennamen der verwalteten Domäne zu pingen, z. B. mit `ping aaddscontoso.com`.
    * Wenn diese Pinganforderung nicht erfolgreich ist, versuchen Sie, die IP-Adressen für die verwaltete Domäne zu pingen, z. B. mit `ping 10.0.0.4`. Die IP-Adresse Ihrer Umgebung wird auf der Seite *Eigenschaften* angezeigt, wenn Sie die verwaltete Domäne aus Ihrer Liste der Azure-Ressourcen auswählen.
    * Wenn Sie die IP-Adresse pingen können, aber nicht die Domäne, ist das DNS möglicherweise falsch konfiguriert. Überprüfen Sie, ob die IP-Adressen der verwalteten Domäne als DNS-Server für das virtuelle Netzwerk konfiguriert sind.
* Leeren Sie mit dem Befehl `ipconfig /flushdns` den DNS-Resolvercache auf dem virtuellen Computer.

### <a name="credentials-related-issues"></a>Probleme mit Anmeldeinformationen

Wenn Sie beim Einbinden in die Domäne dazu aufgefordert werden, Anmeldeinformationen einzugeben, nach dieser Eingabe aber ein Fehler auftritt, kann die VM eine Verbindung mit der verwalteten Domäne herstellen. Mit den angegebenen Anmeldeinformationen kann die VM nicht in die verwaltete Domäne eingebunden werden.

Führen Sie die folgenden Problembehandlungsschritte aus, und versuchen Sie erneut, die Windows Server-VM in die verwaltete Domäne einzubinden.

* Stellen Sie sicher, dass das angegebene Benutzerkonto zur verwalteten Domäne gehört.
* Vergewissern Sie sich, dass das Konto Teil der verwalteten Domäne oder des Azure AD-Mandanten ist. Konten aus externen Verzeichnissen, die Ihrem Azure AD-Mandanten zugeordnet sind, können während der Einbindung in die Domäne nicht richtig authentifiziert werden.
* Geben Sie die Anmeldeinformationen im UPN-Format an, z. B. als `contosoadmin@aaddscontoso.onmicrosoft.com`. Wenn mehrere Benutzer in Ihrem Mandanten das gleiche UPN-Präfix verwenden oder das UPN-Präfix sehr lang ist, wird der *SAMAccountName* für Ihr Konto möglicherweise automatisch generiert. In diesen Fällen ist das *SAMAccountName*-Format für Ihr Konto möglicherweise anders als Sie erwarten bzw. unterscheidet sich von dem, was Sie in Ihrer lokalen Domäne verwenden.
* Überprüfen Sie, ob Sie die [Kennwortsynchronisierung für Ihre verwaltete Domäne aktiviert haben][password-sync]. Ohne diesen Konfigurationsschritt sind die erforderlichen Kennworthashes in der verwalteten Domäne nicht vorhanden, und Ihr Anmeldeversuch kann nicht ordnungsgemäß authentifiziert werden.
* Warten Sie, bis die Kennwortsynchronisierung abgeschlossen ist. Wird das Kennwort eines Benutzerkontos geändert, aktualisiert eine automatische Hintergrundsynchronisierung von Azure AD das Kennwort in Azure AD DS. Es dauert einige Zeit, bis das Kennwort für das Einbinden in die Domäne verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Windows Server-VM
> * Herstellen einer Verbindung zwischen der Windows Server-VM und einem virtuellen Azure-Netzwerk
> * Einbinden der VM in die verwaltete Domäne

Konfigurieren Sie im Active Directory-Verwaltungscenter (Active Directory Administrative Center, ADAC) eine Verwaltungs-VM, um Ihre verwaltete Domäne zu verwalten.

> [!div class="nextstepaction"]
> [Installieren von Verwaltungstools auf einer Verwaltungs-VM](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: ./tutorial-create-instance.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension