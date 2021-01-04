---
title: Erstellen einer Azure AD Domain Services-Ressourcengesamtstruktur mithilfe von Azure PowerShell | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell eine Azure Active Directory Domain Services-Ressourcengesamtstruktur und eine ausgehende Gesamtstruktur zu einer lokalen Active Directory Domain Services-Umgebung erstellen und konfigurieren.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/27/2020
ms.author: justinha
ms.openlocfilehash: ebfc2476b7955b926f86094de03973155386eb8f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619966"
---
# <a name="create-an-azure-active-directory-domain-services-resource-forest-and-outbound-forest-trust-to-an-on-premises-domain-using-azure-powershell"></a>Erstellen einer Azure Active Directory Domain Services-Ressourcengesamtstruktur und einer ausgehenden Gesamtstruktur-Vertrauensstellung zu einer lokalen Domäne mithilfe von Azure PowerShell

In Umgebungen, in denen Sie keine Kennworthashes synchronisieren können, oder wenn Sie Benutzer haben, die sich ausschließlich mit Smartcards anmelden, sodass sie ihre Kennwörter nicht wissen, können Sie eine Ressourcengesamtstruktur in Azure Active Directory Domain Services (Azure AD DS) verwenden. Eine Ressourcengesamtstruktur verwendet eine unidirektionale ausgehende Vertrauensstellung von Azure AD DS zu mindestens einer lokalen AD DS-Umgebung. Diese Vertrauensstellung ermöglicht es Benutzern, Anwendungen und Computern, sich aus der verwalteten Azure AD DS-Domäne bei einer lokalen Domäne zu authentifizieren. In einer Ressourcengesamtstruktur werden lokale Kennworthashes nie synchronisiert.

![Darstellung einer Gesamtstruktur-Vertrauensstellung von Azure AD DS zu lokalem AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Erstellen einer Azure AD DS-Ressourcengesamtstruktur mithilfe von Azure PowerShell
> * Erstellen einer unidirektionalen ausgehenden Gesamtstruktur-Vertrauensstellung in der verwalteten Domäne mithilfe von Azure PowerShell
> * Konfigurieren von DNS in einer lokalen AD DS-Umgebung zur Unterstützung der Konnektivität der verwalteten Domäne
> * Erstellen einer unidirektionalen eingehenden Gesamtstruktur-Vertrauensstellung in einer lokalen AD DS-Umgebung
> * Testen und Überprüfen der Vertrauensstellung hinsichtlich Authentifizierung und Ressourcenzugriff

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

> [!IMPORTANT]
> Ressourcengesamtstrukturen für verwaltete Domänen unterstützen derzeit weder Microsoft Azure HDInsight noch Azure Files. Die standardmäßigen Benutzergesamtstrukturen für verwaltete Domänen unterstützen diese beiden zusätzlichen Dienste.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.

* Installieren und konfigurieren Sie Azure PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure PowerShell-Moduls und Verbinden mit Ihrem Azure-Abonnement](/powershell/azure/install-az-ps) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzAccount][Connect-AzAccount] bei Ihrem Azure-Abonnement anmelden.
* Installieren und konfigurieren Sie Azure AD PowerShell.
    * Führen Sie bei Bedarf die Anweisungen zum [Installieren des Azure AD PowerShell-Moduls und Verbinden mit Azure AD](/powershell/azure/active-directory/install-adv2) aus.
    * Stellen Sie sicher, dass Sie sich mit dem Cmdlet [Connect-AzureAD][Connect-AzureAD] bei Ihrem Azure AD-Mandanten anmelden.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um Azure AD DS zu aktivieren.
* Sie benötigen Berechtigungen als *Mitwirkender* in Ihrem Azure-Abonnement, um die erforderlichen Azure AD DS-Ressourcen zu erstellen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Artikel erstellen und konfigurieren Sie die ausgehende Gesamtstruktur-Vertrauensstellung von einer verwalteten Domäne über das Azure-Portal. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="deployment-process"></a>Bereitstellungsprozess

Hierbei handelt es sich um einen mehrstufigen Prozess zum Erstellen einer Ressourcengesamtstruktur der verwalteten Domäne und der Vertrauensstellung zu einer lokalen AD DS-Instanz. Mithilfe der folgenden allgemeinen Schritte wird die vertrauenswürdige Hybridumgebung erstellt:

1. Erstellen eines Dienstprinzipals für die verwaltete Domäne
1. Erstellen einer Ressourcengesamtstruktur der verwalteten Domäne
1. Erstellen von Hybridnetzwerk-Konnektivität mithilfe von Site-to-Site-VPN oder Express Route
1. Erstellen der verwalteten Domänenseite der Vertrauensstellung
1. Erstellen der lokalen AD DS-Seite der Vertrauensstellung

Bevor Sie beginnen, vergewissern Sie sich, dass Ihnen die [Überlegungen zum Netzwerkbetrieb, die Benennung der Gesamtstruktur und die DNS-Anforderungen](tutorial-create-forest-trust.md#networking-considerations) bekannt sind. Sie können den Gesamtstrukturnamen der verwalteten Domäne nach der Bereitstellung nicht ändern.

## <a name="create-the-azure-ad-service-principal"></a>Erstellen des Azure AD-Dienstprinzipals

Azure AD DS erfordert, dass ein Dienstprinzipal die Daten von Azure AD synchronisiert. Dieser Prinzipal muss in Ihrem Azure AD-Mandanten erstellt werden, bevor Sie die Ressourcengesamtstruktur der verwalteten Domäne erstellen.

Erstellen Sie einen Azure AD-Dienstprinzipal für Azure AD DS für die Kommunikation und Authentifizierung. Es wird eine bestimmte Anwendungs-ID namens *Domain Controller Services* mit der ID *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* verwendet. Ändern Sie diese Anwendungs-ID nicht.

Erstellen Sie mit dem Cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] einen Azure AD-Dienstprinzipal:

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

## <a name="create-a-managed-domain-resource-forest"></a>Erstellen einer Ressourcengesamtstruktur der verwalteten Domäne

Zum Erstellen einer Ressourcengesamtstruktur der verwalteten Domäne verwenden Sie das Skript `New-AzureAaddsForest`. Dieses Skript ist Teil einer umfassenden Gruppe von Befehlen, die das Erstellen und Verwalten von Ressourcengesamtstrukturen für verwaltete Domänen unterstützen, einschließlich der Erstellung der unidirektionalen gebundenen Gesamtstruktur in einem der folgenden Abschnitte. Diese Skripts stehen über den [PowerShell-Katalog](https://www.powershellgallery.com/) zur Verfügung und sind vom Azure AD-Entwicklerteam digital signiert.

1. Erstellen Sie zuerst mit dem Cmdlet [New-AzResourceGroup][New-AzResourceGroup] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *westus* erstellt. Verwenden Sie Ihren eigenen Namen und die gewünschte Region:

    ```azurepowershell
    New-AzResourceGroup `
      -Name "myResourceGroup" `
      -Location "WestUS"
    ```

1. Installieren Sie das Skript `New-AaddsResourceForestTrust` aus dem [PowerShell-Katalog][powershell-gallery] mit dem Cmdlet [Install-Script][Install-Script]:

    ```powershell
    Install-Script -Name New-AaddsResourceForestTrust
    ```

1. Überprüfen Sie die folgenden Parameter, die für das Skript `New-AzureAaddsForest` erforderlich sind. Stellen Sie sicher, dass Sie auch über die erforderlichen **Azure PowerShell**- und **Azure AD PowerShell**-Module verfügen. Vergewissern Sie sich, dass Sie die Anforderungen für das virtuelle Netzwerk zur Bereitstellung von Anwendungs- und lokaler Konnektivität geplant haben.

    | Name                         | Skriptparameter          | BESCHREIBUNG |
    |:-----------------------------|---------------------------|:------------|
    | Subscription                 | *-azureSubscriptionId*    | Die Abonnement-ID, die zur Azure AD DS-Abrechnung verwendet wird. Sie können die Liste der Abonnements mit dem Cmdlet [Get-AzureRMSubscription][Get-AzureRMSubscription] abrufen. |
    | Ressourcengruppe               | *-aaddsResourceGroupName* | Der Name der Ressourcengruppe für die verwaltete Domäne und zugehörige Ressourcen. |
    | Standort                     | *-aaddsLocation*          | Die Azure-Region zum Hosten der verwalteten Domäne. Informationen zu verfügbaren Regionen finden Sie unter [Unterstützte Regionen für Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all). |
    | Azure AD DS-Administrator    | *-aaddsAdminUser*         | Der Benutzerprinzipalname des ersten Administrators der verwalteten Domäne. Bei diesem Konto muss es sich um ein vorhandenes Cloudbenutzerkonto in Ihrem Azure Active Directory handeln. Der Benutzer und der Benutzer, der das Skript ausführt, werden der Gruppe *AAD DC-Administratoren* hinzugefügt. |
    | Azure AD DS-Domänenname      | *-aaddsDomainName*        | Der FQDN der verwalteten Domäne gemäß der vorherigen Anleitungen zum Auswählen eines Gesamtstrukturnamens. |

    Das Skript `New-AzureAaddsForest` kann das virtuelle Azure-Netzwerk und das Azure AD DS-Subnetz erstellen, falls diese Ressourcen noch nicht vorhanden sind. Das Skript kann optional die Workloadsubnetze erstellen, wenn dies angegeben ist:

    | Name                              | Skriptparameter                  | BESCHREIBUNG |
    |:----------------------------------|:----------------------------------|:------------|
    | Name des virtuellen Netzwerks              | *-aaddsVnetName*                  | Der Name des virtuellen Netzwerks für die verwaltete Domäne.|
    | Adressraum                     | *-aaddsVnetCIDRAddressSpace*      | Der Adressbereich des virtuellen Netzwerks in CIDR-Notation (wenn das virtuelle Netzwerk erstellt wird).|
    | Azure AD DS-Subnetzname           | *-aaddsSubnetName*                | Der Name des Subnetzes des virtuellen Netzwerks *aaddsVnetName*, das die verwaltete Domäne hostet. Stellen Sie in diesem Subnetz nicht Ihre eigenen VMs und Workloads bereit. |
    | Azure AD DS-Adressbereich         | *-aaddsSubnetCIDRAddressRange*    | Der Subnetzadressbereich in CIDR-Notation für die AAD DS-Instanz, z. B. *192.168.1.0/24*. Der Adressbereich muss im Adressbereich des virtuellen Netzwerks enthalten sein und sich von anderen Subnetzen unterscheiden. |
    | Name des Workloadsubnetzes (optional)   | *-workloadSubnetName*             | Der optionale Name eines Subnetzes im virtuellen Netzwerk *aaddsVnetName*, das für Ihre eigenen Anwendungsworkloads erstellt werden soll. VMs und Anwendungen können stattdessen auch mit einem virtuellen Azure-Netzwerk mit Peering verbunden werden. |
    | Adressbereich der Workload (optional) | *-workloadSubnetCIDRAddressRange* | Der optionale Subnetzadressbereich in CIDR-Notation für eine Anwendungsworkload, z. B. *192.168.2.0/24*. Der Adressbereich muss im Adressbereich des virtuellen Netzwerks enthalten sein und sich von anderen Subnetzen unterscheiden.|

1. Erstellen Sie nun eine Ressourcengesamtstruktur der verwalteten Domäne mit dem Skript `New-AzureAaaddsForest`. Im folgenden Beispiel werden eine Gesamtstruktur mit dem Namen *addscontoso.com* und ein Workloadsubnetz erstellt. Geben Sie Ihre eigenen Parameternamen und IP-Adressbereiche oder vorhandene virtuelle Netzwerke an.

    ```azurepowershell
    New-AzureAaddsForest `
        -azureSubscriptionId <subscriptionId> `
        -aaddsResourceGroupName "myResourceGroup" `
        -aaddsLocation "WestUS" `
        -aaddsAdminUser "contosoadmin@contoso.com" `
        -aaddsDomainName "aaddscontoso.com" `
        -aaddsVnetName "myVnet" `
        -aaddsVnetCIDRAddressSpace "192.168.0.0/16" `
        -aaddsSubnetName "AzureADDS" `
        -aaddsSubnetCIDRAddressRange "192.168.1.0/24" `
        -workloadSubnetName "myWorkloads" `
        -workloadSubnetCIDRAddressRange "192.168.2.0/24"
    ```

    Das Erstellen der Ressourcengesamtstruktur der verwalteten Domäne und der unterstützenden Ressourcen nimmt einige Zeit in Anspruch. Warten Sie, bis das Skript abgeschlossen ist. Fahren Sie mit dem nächsten Abschnitt fort, um die Konnektivität des lokalen Netzwerks zu konfigurieren, während die Azure AD-Ressourcengesamtstruktur im Hintergrund bereitgestellt wird.

## <a name="configure-and-validate-network-settings"></a>Konfigurieren und Überprüfen von Netzwerkeinstellungen

Während die verwaltete Domäne weiterhin bereitgestellt wird, konfigurieren und überprüfen Sie die Hybridnetzwerk-Konnektivität mit dem lokalen Rechenzentrum. Außerdem benötigen Sie eine Verwaltungs-VM, die bei der verwalteten Domäne für die regelmäßige Wartung verwendet werden soll. Ein Teil der Hybridkonnektivität ist möglicherweise bereits in Ihrer Umgebung vorhanden, oder Sie müssen vielleicht mit anderen in Ihrem Team zusammenarbeiten, um die Verbindungen zu konfigurieren.

Bevor Sie beginnen, vergewissern Sie sich, dass Ihnen die [Überlegungen zum Netzwerkbetrieb und Empfehlungen](tutorial-create-forest-trust.md#networking-considerations) bekannt sind.

1. Erstellen Sie die Hybridkonnektivität mit dem lokalen Netzwerk in Azure mithilfe einer Azure-VPN- oder Azure ExpressRoute-Verbindung. Die Konfiguration des Hybridnetzwerks geht über den Rahmen dieser Dokumentation hinaus und ist möglicherweise bereits in Ihrer Umgebung vorhanden. Ausführliche Informationen zu bestimmten Szenarien finden Sie in den folgenden Artikeln:

    * [Azure-Site-to-Site-VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
    * [Übersicht über Azure ExpressRoute](../expressroute/expressroute-introduction.md)

    > [!IMPORTANT]
    > Wenn Sie die Verbindung direkt mit dem virtuellen Netzwerk Ihrer verwalteten Domäne erstellen, verwenden Sie ein separates Gatewaysubnetz. Erstellen Sie das Gateway nicht im Subnetz der verwalteten Domäne.

1. Zum Verwalten einer verwalteten Domäne erstellen Sie eine Verwaltungs-VM, verknüpfen diese mit der verwalteten Domäne und installieren die erforderlichen AD DS-Verwaltungstools.

    Während die Ressourcengesamtstruktur der verwalteten Domäne bereitgestellt wird, [erstellen Sie eine Windows Server-VM](./join-windows-vm.md) und [installieren die wichtigsten AD DS-Verwaltungstools](./tutorial-create-management-vm.md), um die erforderlichen Verwaltungstools zu installieren. Binden Sie die Verwaltungs-VM erst in einem der folgenden Schritte in die verwaltete Domäne ein, nachdem die Domäne erfolgreich bereitgestellt wurde.

1. Überprüfen Sie die Netzwerkkonnektivität zwischen Ihrem lokalen Netzwerk und dem virtuellen Azure-Netzwerk.

    * Vergewissern Sie sich, dass Ihr lokaler Domänencontroller beispielsweise über `ping` oder Remotedesktop eine Verbindung mit der verwalteten VM herstellen kann.
    * Stellen Sie sicher, dass Ihre Verwaltungs-VM eine Verbindung mit den lokalen Domänencontrollern herstellen kann. Verwenden Sie auch dazu ein Hilfsprogramm wie `ping`.

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus. Wählen Sie Ihre verwaltete Domäne aus, z. B. *aaddscontoso.com*, und warten Sie, bis der Status **Wird ausgeführt** gemeldet wird.

    Während der Ausführung [aktualisieren Sie DNS-Einstellungen für das virtuelle Azure-Netzwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network), und dann [aktivieren Sie Benutzerkonten für Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds), um die Konfigurationen für Ihre Ressourcengesamtstruktur der verwalteten Domäne abzuschließen.

1. Notieren Sie sich die DNS-Adressen, die auf der Übersichtsseite angezeigt werden. Sie benötigen diese Adressen, wenn Sie die lokale Active Directory-Seite der Vertrauensstellung in einem der folgenden Abschnitte konfigurieren.
1. Starten Sie die Verwaltungs-VM neu, damit diese die neuen DNS-Einstellungen empfängt, und dann [binden Sie die VM in die verwaltete Domäne ein](join-windows-vm.md#join-the-vm-to-the-managed-domain).
1. Nachdem die Verwaltungs-VM in die verwaltete Domäne eingebunden wurde, stellen Sie erneut eine Verbindung über Remotedesktop her.

    Verwenden Sie an einer Eingabeaufforderung `nslookup` und den Namen der Ressourcengesamtstruktur der verwalteten Domäne, um die Namensauflösung für die Ressourcengesamtstruktur zu überprüfen.

    ```console
    nslookup aaddscontoso.com
    ```

    Der Befehl sollte zwei IP-Adressen für die Ressourcengesamtstruktur zurückgeben.

## <a name="create-the-forest-trust"></a>Erstellen der Gesamtstruktur-Vertrauensstellung

Die Gesamtstruktur-Vertrauensstellung besteht aus zwei Teilen: der unidirektionalen ausgehenden Gesamtstruktur-Vertrauensstellung in der Ressourcengesamtstruktur der verwalteten Domäne und der unidirektionalen eingehenden Gesamtstruktur-Vertrauensstellung in der lokalen AD DS-Gesamtstruktur. Sie erstellen beide Seiten dieser Vertrauensstellung manuell. Nach dem Erstellen beider Seiten können sich Benutzer und Ressourcen erfolgreich mithilfe der Gesamtstruktur-Vertrauensstellung authentifizieren. Eine Ressourcengesamtstruktur der verwalteten Domäne unterstützt bis zu fünf unidirektionale ausgehende Gesamtstruktur-Vertrauensstellungen zu lokalen Gesamtstrukturen.

### <a name="create-the-managed-domain-side-of-the-trust-relationship"></a>Erstellen der verwalteten Domänenseite der Vertrauensstellung

Verwenden Sie das Skript `Add-AaddsResourceForestTrust`, um die verwaltete Domänenseite der Vertrauensstellung zu erstellen. Installieren Sie zuerst das Skript `Add-AaddsResourceForestTrust` aus dem [PowerShell-Katalog][powershell-gallery] mit dem Cmdlet [Install-Script][Install-Script]:

```powershell
Install-Script -Name Add-AaddsResourceForestTrust
```

Geben Sie nun die folgenden Informationen für das Skript an:

| Name                               | Skriptparameter     | BESCHREIBUNG |
|:-----------------------------------|:---------------------|:------------|
| Azure AD DS-Domänenname            | *-ManagedDomainFqdn* | Der FQDN der verwalteten Domäne, z. B. *aaddscontoso.com*. |
| Lokaler AD DS-Domänenname      | *-TrustFqdn*         | Der FQDN der vertrauenswürdigen Gesamtstruktur, z. B. *onprem.contoso.com*. |
| Anzeigename der Vertrauensstellung                | *-TrustFriendlyName* | Der Anzeigename der Vertrauensstellung. |
| Lokale IP-Adressen des AD DS-DNS | *-TrustDnsIPs*       | Eine durch Trennzeichen getrennte Liste von IPv4-Adressen des DNS-Servers für die aufgeführte vertrauenswürdige Domäne. |
| Kennwort der Vertrauensstellung                     | *-TrustPassword*     | Ein komplexes Kennwort für die Vertrauensstellung. Dieses Kennwort wird auch eingegeben, wenn die unidirektionale eingehende Vertrauensstellung in der lokalen AD DS-Instanz erstellt wird. |
| Anmeldeinformationen                        | *-Credentials*       | Die Anmeldeinformationen zum Authentifizieren bei Azure. Der Benutzer muss in der Gruppe *AAD DC-Administratoren* enthalten sein. Wenn nicht angegeben, fordert das Skript zur Authentifizierung auf. |

Im folgenden Beispiel wird eine Vertrauensstellung mit dem Namen *myAzureADDSTrust* zu *onprem.contoso.com* erstellt. Verwenden Sie Ihre eigenen Parameternamen und Kennwörter.

```azurepowershell
Add-AaddsResourceForestTrust `
    -ManagedDomainFqdn "aaddscontoso.com" `
    -TrustFqdn "onprem.contoso.com" `
    -TrustFriendlyName "myAzureADDSTrust" `
    -TrustDnsIPs "10.0.1.10,10.0.1.11" `
    -TrustPassword <complexPassword>
```

> [!IMPORTANT]
> Merken Sie sich das Kennwort der Vertrauensstellung. Sie müssen das gleiche Kennwort verwenden, wenn Sie die lokale Seite der Vertrauensstellung erstellen.

## <a name="configure-dns-in-the-on-premises-domain"></a>Konfigurieren von DNS in der lokalen Domäne

Um die verwaltete Domäne ordnungsgemäß aus der lokalen Umgebung aufzulösen, müssen Sie möglicherweise Weiterleitungen zu den vorhandenen DNS-Servern hinzufügen. Wenn Sie die lokale Umgebung nicht für die Kommunikation mit der verwalteten Domäne konfiguriert haben, führen Sie auf einer Verwaltungsarbeitsstation die folgenden Schritte für die lokale AD DS-Domäne aus:

1. Wählen Sie **Start | Verwaltung | DNS** aus.
1. Klicken Sie mit der rechten Maustaste auf den DNS-Server, z. B. *meinAD01*, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie **Weiterleitungen** und dann **Bearbeiten** aus, um weitere Weiterleitungen hinzuzufügen.
1. Fügen Sie die IP-Adressen der verwalteten Domäne hinzu, z. B. *10.0.1.4* und *10.0.1.5*.
1. Überprüfen Sie an einer lokalen Eingabeaufforderung die Namensauflösung mithilfe von **nslookup** für den Domänennamen der Ressourcengesamtstruktur der verwalteten Domäne. `Nslookup aaddscontoso.com` sollte beispielsweise die zwei IP-Adressen für die Ressourcengesamtstruktur der verwalteten Domäne zurückgeben.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Erstellen der eingehenden Gesamtstruktur-Vertrauensstellung in der lokalen Domäne

Für die lokale AD DS-Domäne ist eine eingehende Gesamtstruktur-Vertrauensstellung für die verwaltete Domäne erforderlich. Diese Vertrauensstellung muss manuell in der lokalen AD DS-Domäne erstellt werden. Sie kann nicht im Azure-Portal erstellt werden.

Um die eingehende Vertrauensstellung in der lokalen AD DS-Domäne zu konfigurieren, führen Sie für die lokale AD DS-Domäne die folgenden Schritte auf einer Verwaltungsarbeitsstation aus:

1. Wählen Sie **Start | Verwaltung | Active Directory-Domänen und -Vertrauensstellungen** aus.
1. Wählen Sie mit der rechten Maustaste eine Domäne aus, z. B. *onprem.contoso.com*, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie die Registerkarte **Vertrauensstellungen** aus, und wählen Sie dann **Neue Vertrauensstellung** aus.
1. Geben Sie den Namen der verwalteten Domäne ein, z. B. *aaddscontoso.com*, und wählen Sie anschließend **Weiter** aus.
1. Wählen Sie die Option zum Erstellen einer **Gesamtstruktur-Vertrauenswürdigkeit** und dann die Option zum Erstellen einer **Unidirektional: eingehend**-Vertrauensstellung aus.
1. Wählen Sie die Option aus, mit der die Vertrauensstellung **Nur für diese Domäne** erstellt wird. Im nächsten Schritt erstellen Sie die Vertrauensstellung im Azure-Portal für die verwaltete Domäne.
1. Wählen Sie die Option zum Verwenden von **Gesamtstrukturweite Authentifizierung** aus, und geben Sie dann ein Vertrauensstellungskennwort ein. Dasselbe Kennwort wird auch im Azure-Portal im nächsten Abschnitt eingegeben.
1. Durchlaufen Sie die nächsten Fenster mit Standardoptionen, und aktivieren Sie dann die Option **Nein, ausgehende Vertrauensstellung nicht bestätigen**. Die Vertrauensstellung kann nicht überprüft werden, da das delegierte Administratorkonto zur Ressourcengesamtstruktur der verwalteten Domäne nicht über die erforderlichen Berechtigungen verfügt. Dieses Verhalten ist beabsichtigt.
1. Wählen Sie **Fertig stellen**.

## <a name="validate-resource-authentication"></a>Überprüfen der Ressourcenauthentifizierung

Mit den folgenden gängigen Szenarien können Sie überprüfen, ob die Gesamtstruktur-Vertrauensstellung Benutzer und Zugriff auf Ressourcen ordnungsgemäß authentifiziert:

* [Lokale Benutzerauthentifizierung aus der Azure AD DS-Ressourcengesamtstruktur](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Zugreifen auf Ressourcen in der Azure AD DS-Ressourcengesamtstruktur über einen lokalen Benutzer](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Aktivieren von Datei- und Druckerfreigabe](#enable-file-and-printer-sharing)
    * [Erstellen einer Sicherheitsgruppe und Hinzufügen von Mitgliedern](#create-a-security-group-and-add-members)
    * [Erstellen einer Dateifreigabe für gesamtstrukturübergreifenden Zugriff](#create-a-file-share-for-cross-forest-access)
    * [Überprüfen der gesamtstrukturübergreifenden Authentifizierung für eine Ressource](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Lokale Benutzerauthentifizierung aus der Azure AD DS-Ressourcengesamtstruktur

Der virtuelle Windows Server-Computer muss mit der Ressourcendomäne der verwalteten Domäne verknüpft sein. Verwenden Sie diesen virtuellen Computer, um zu testen, ob der lokale Benutzer sich bei einem virtuellen Computer authentifizieren kann.

1. Stellen Sie über Remotedesktop und Ihre Administratoranmeldeinformationen für die verwaltete Domäne eine Verbindung mit der Windows Server-VM her, die mit der Ressourcengesamtstruktur der verwalteten Domäne verknüpft ist. Wird ein „Authentifizierung auf Netzwerkebene“-Fehler gemeldet, überprüfen Sie, ob das von Ihnen verwendete Benutzerkonto tatsächlich ein Domänenbenutzerkonto ist.

    > [!TIP]
    > Um sicher eine Verbindung mit Ihren virtuellen Computern herzustellen, die mit Azure AD Domain Services verknüpft sind, können Sie den [Azure Bastion-Hostdienst](../bastion/bastion-overview.md) in unterstützten Azure-Regionen verwenden.

1. Öffnen Sie eine Eingabeaufforderung, und verwenden Sie den `whoami`-Befehl, um den kennzeichnenden Namen (distinguished name) des derzeit authentifizierten Benutzers anzuzeigen:

    ```console
    whoami /fqdn
    ```

1. Verwenden Sie den `runas`-Befehl, um sich als Benutzer aus der lokalen Domäne zu authentifizieren. Ersetzen Sie `userUpn@trusteddomain.com` im folgenden Befehl durch den Benutzerprinzipalnamen (User Principal Name, UPN) eines Benutzers aus der vertrauenswürdigen lokalen Domäne. Der Befehl fordert Sie auf, das Kennwort des Benutzers einzugeben:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Wenn die Authentifizierung erfolgreich ist, wird eine neue Eingabeaufforderung geöffnet. Der Titel der neuen Eingabeaufforderung enthält `running as userUpn@trusteddomain.com`.
1. Verwenden Sie `whoami /fqdn` in der neuen Eingabeaufforderung, um den kennzeichnenden Namen des authentifizierten Benutzers aus dem lokalen Active Directory anzuzeigen.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Zugreifen auf Ressourcen in der Azure AD DS-Ressourcengesamtstruktur über einen lokalen Benutzer

Mit der Windows Server-VM, die mit der Ressourcengesamtstruktur der verwalteten Domäne verknüpft ist, können Sie das Szenario testen, in dem Benutzer auf Ressourcen, die in der Ressourcengesamtstruktur gehostet werden, zugreifen können, wenn sie sich von Computern in der lokalen Domäne als Benutzer der lokalen Domäne authentifizieren. In den folgenden Beispielen wird gezeigt, wie verschiedene gängige Szenarien erstellt und getestet werden.

#### <a name="enable-file-and-printer-sharing"></a>Aktivieren von Datei- und Druckerfreigabe

1. Stellen Sie über Remotedesktop und Ihre Administratoranmeldeinformationen für die verwaltete Domäne eine Verbindung mit der Windows Server-VM her, die mit der Ressourcengesamtstruktur der verwalteten Domäne verknüpft ist. Wird ein „Authentifizierung auf Netzwerkebene“-Fehler gemeldet, überprüfen Sie, ob das von Ihnen verwendete Benutzerkonto tatsächlich ein Domänenbenutzerkonto ist.

    > [!TIP]
    > Um sicher eine Verbindung mit Ihren virtuellen Computern herzustellen, die mit Azure AD Domain Services verknüpft sind, können Sie den [Azure Bastion-Hostdienst](../bastion/bastion-overview.md) in unterstützten Azure-Regionen verwenden.

1. Öffnen Sie **Windows-Einstellungen**, suchen Sie nach **Netzwerk- und Freigabecenter**, und wählen Sie diese Option aus.
1. Wählen Sie die Option **Erweiterte Freigabeeinstellungen ändern** aus.
1. Wählen Sie unter **Domänenprofil** die Option **Datei- und Druckerfreigabe aktivieren** aus, und wählen Sie dann **Änderungen speichern** aus.
1. Schließen Sie **Netzwerk- und Freigabecenter**.

#### <a name="create-a-security-group-and-add-members"></a>Erstellen einer Sicherheitsgruppe und Hinzufügen von Mitgliedern

1. Öffnen Sie **Active Directory-Benutzer und -Computer**.
1. Wählen Sie mit der rechten Maustaste den Domänennamen aus, wählen Sie **Neu** aus, und wählen Sie dann **Organisationseinheit** aus.
1. Geben Sie in das Feld „Name“ den Namen *LokaleObjekte* ein, und wählen Sie dann **OK** aus.
1. Klicken Sie im Navigationsbereich mit der rechten Maustaste auf **LokaleObjekte**. Wählen Sie **Neu** und dann **Gruppe** aus.
1. Geben Sie *Dateiserverzugriff* in das Feld **Gruppenname** ein. Wählen Sie für **Gruppenbereich** die Option **Lokal (in Domäne)** aus, und wählen Sie dann **OK** aus.
1. Doppelklicken Sie im Inhaltsbereich auf **Dateiserverzugriff**. Wählen Sie **Mitglieder** aus, wählen Sie **Hinzufügen** aus, und wählen Sie dann **Standorte** aus.
1. Wählen Sie Ihr lokales Active Directory in der **Standort**-Ansicht aus, und wählen Sie dann **OK** aus.
1. Geben Sie *Domänenbenutzer* in das Feld **Geben Sie die zu verwendenden Objektnamen ein** ein. Wählen Sie **Namen überprüfen** aus, geben Sie die Anmeldeinformationen für das lokale Active Directory an, und wählen Sie dann **OK** aus.

    > [!NOTE]
    > Sie müssen Anmeldeinformationen angeben, weil die Vertrauensstellung nur unidirektional ist. Das heißt, dass Benutzer aus der verwalteten Domäne weder auf Ressourcen in der vertrauenswürdigen (lokalen) Domäne zugreifen noch dort nach Benutzern oder Gruppen suchen können.

1. Die Gruppe **Domänenbenutzer** aus Ihrem lokalen Active Directory muss Mitglied der Gruppe **Dateiserverzugriff** sein. Wählen Sie **OK** aus, um die Gruppe zu speichern und das Fenster zu schließen.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Erstellen einer Dateifreigabe für gesamtstrukturübergreifenden Zugriff

1. Erstellen Sie auf der Windows Server-VM, die mit der Ressourcengesamtstruktur der verwalteten Domäne verknüpft ist, einen Ordner, und geben Sie diesem einen Namen wie *GesamtstrukturFreigabe* (CrossForestShare).
1. Wählen Sie den Ordner mit der rechten Maustaste aus, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie die Registerkarte **Sicherheit** aus, und wählen Sie dann **Bearbeiten** aus.
1. Wählen Sie im Dialogfeld *Berechtigungen für GesamtstrukturFreigabe* die Option **Hinzufügen** aus.
1. Geben Sie *Dateiserverzugriff* in das Feld **Geben Sie die zu verwendenden Objektnamen ein** ein, und wählen Sie dann **OK** aus.
1. Wählen Sie *Dateiserverzugriff* in der Liste **Gruppen- oder Benutzernamen** aus. Wählen Sie in der Liste **Berechtigungen für GesamtstrukturFreigabe** die Option *Zulassen* für die Berechtigungen **Ändern** und **Schreiben** aus, und wählen Sie dann **OK** aus.
1. Wählen Sie die Registerkarte **Freigabe** aus, und wählen Sie dann **Erweiterte Freigabe...** aus.
1. Wählen Sie **Diesen Ordner freigeben** aus, und geben Sie dann einen Namen für die Dateifreigabe in **Freigabename** ein, z. B. *GesamtstrukturFreigabe*.
1. Wählen Sie **Berechtigungen** aus. Wählen Sie in der Liste **Berechtigungen für Jeder** die Option **Zulassen** für die Berechtigung **Ändern** aus.
1. Wählen Sie zweimal **OK** und dann **Schließen** aus.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Überprüfen der gesamtstrukturübergreifenden Authentifizierung für eine Ressource

1. Melden Sie sich mit einem Benutzerkonto aus Ihrem lokalen Active Directory bei einem Windows-Computer an, der mit Ihrem lokalen Active Directory verknüpft ist.
1. Stellen Sie über **Windows-Explorer-** eine Verbindung mit der von Ihnen erstellten Freigabe her. Verwenden Sie dazu den vollqualifizierten Hostnamen und den Namen der Freigabe, z. B. `\\fs1.aaddscontoso.com\CrossforestShare`.
1. Um die Schreibberechtigung zu überprüfen, wählen Sie den Ordner mit der rechten Maustaste aus, wählen Sie **Neu** aus, und wählen Sie dann **Textdokument** aus. Verwenden Sie den Standardnamen **Neues Textdokument**.

    Wenn die Schreibberechtigungen ordnungsgemäß festgelegt sind, wird ein neues Textdokument erstellt. In den folgenden Schritten öffnen, bearbeiten und löschen Sie die Datei nach Bedarf.
1. Um die Leseberechtigung zu überprüfen, öffnen Sie **Neues Textdokument**.
1. Um die Änderungsberechtigung zu überprüfen, fügen Sie Text zur Datei hinzu, und schließen Sie **Editor**. Wenn Sie aufgefordert werden, Änderungen zu speichern, wählen Sie **Speichern** aus.
1. Um die Löschberechtigung zu überprüfen, wählen Sie mit der rechten Maustaste den Namen **Neues Textdokument** aus, und wählen Sie **Löschen** aus. Wählen Sie **Ja** aus, um das Löschen der Datei Löschung zu bestätigen.

## <a name="update-or-remove-outbound-forest-trust"></a>Aktualisieren oder Entfernen der ausgehenden Gesamtstruktur-Vertrauensstellung

Wenn Sie eine vorhandene unidirektionale ausgehende Gesamtstruktur aus der verwalteten Domäne aktualisieren müssen, können Sie die Skripts `Get-AaddsResourceForestTrusts` und `Set-AaddsResourceForestTrust` verwenden. Diese Skripts sind hilfreich in Szenarien, in denen Sie den Anzeigenamen der Gesamtstruktur-Vertrauensstellung oder das Kennwort der Vertrauensstellung aktualisieren möchten. Um eine unidirektionale ausgehende Vertrauensstellung von der verwalteten Domäne zu entfernen, können Sie das Skript `Remove-AaddsResourceForestTrust` verwenden. Sie müssen die unidirektionale eingehende Gesamtstruktur-Vertrauensstellung in der zugeordneten lokalen AD DS-Gesamtstruktur manuell entfernen.

### <a name="update-a-forest-trust"></a>Aktualisieren einer Gesamtstruktur-Vertrauensstellung

Im normalen Betrieb handeln die Gesamtstruktur der verwalteten Domäne und die lokale Gesamtstruktur einen regelmäßigen Prozess zur Kennwortaktualisierung untereinander aus. Dieser ist Teil des normalen Sicherheitsprozesses für die AD DS-Vertrauensstellung. Sie müssen das Kennwort der Vertrauensstellung nur dann manuell rotieren, wenn in der Vertrauensstellung ein Problem aufgetreten ist und Sie ein bekanntes Kennwort manuell zurücksetzen möchten. Weitere Informationen finden Sie unter [Kennwortänderungen für vertrauenswürdige Domänenobjekte](concepts-forest-trust.md#tdo-password-changes).

In den folgenden Beispielschritten wird gezeigt, wie Sie eine vorhandene Vertrauensstellung aktualisieren, wenn Sie das Kennwort der ausgehenden Vertrauensstellung manuell zurücksetzen müssen:

1. Installieren Sie die Skripts `Get-AaddsResourceForestTrusts` und `Set-AaddsResourceForestTrust` aus dem [PowerShell-Katalog][powershell-gallery] mit dem Cmdlet [Install-Script][Install-Script]:

    ```powershell
    Install-Script -Name Get-AaddsResourceForestTrusts,Set-AaddsResourceForestTrust
    ```

1. Bevor Sie eine vorhandene Vertrauensstellung aktualisieren können, müssen Sie zunächst die Vertrauensstellungsressource mithilfe des Skripts `Get-AaddsResourceForestTrusts` abrufen. Im folgenden Beispiel ist die vorhandene Vertrauensstellung einem Objekt mit dem Namen *existingTrust* zugewiesen. Geben Sie den Namen der eigenen Gesamtstruktur der verwalteten Domäne und den Namen der lokalen Gesamtstruktur für die Aktualisierung an:

    ```powershell
    $existingTrust = Get-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

1. Zum Aktualisieren des vorhandenen Kennworts der Vertrauensstellung verwenden Sie das Skript `Set-AaddsResourceForestTrust`. Geben Sie das vorhandene Vertrauensstellungsobjekt aus dem vorherigen Schritt und dann ein neues Kennwort für die Vertrauensstellung an. Es wird keine Kennwortkomplexität von PowerShell erzwungen. Stellen Sie deshalb sicher, dass Sie ein sicheres Kennwort für Ihre Umgebung generieren und verwenden.

    ```powershell
    Set-AaddsResourceForestTrust `
        -Trust $existingTrust `
        -TrustPassword <newComplexPassword>
    ```

### <a name="delete-a-forest-trust"></a>Löschen einer Gesamtstruktur-Vertrauensstellung

Wenn die unidirektionale ausgehende Gesamtstruktur-Vertrauensstellung von der verwalteten Domäne zu einer lokalen AD DS-Gesamtstruktur nicht mehr benötigt wird, können Sie diese entfernen. Stellen Sie sicher, dass sich keine Anwendungen oder Dienste bei der lokalen AD DS-Gesamtstruktur authentifizieren müssen, bevor Sie die Vertrauensstellung entfernen. Sie müssen auch die unidirektionale eingehende Vertrauensstellung in der lokalen AD DS-Gesamtstruktur manuell entfernen.

1. Installieren Sie das Skript `Remove-AaddsResourceForestTrust` aus dem [PowerShell-Katalog][powershell-gallery] mit dem Cmdlet [Install-Script][Install-Script]:

    ```powershell
    Install-Script -Name Remove-AaddsResourceForestTrust
    ```

1. Entfernen Sie nun die Gesamtstruktur-Vertrauensstellung mithilfe des Skripts `Remove-AaddsResourceForestTrust`. Im folgenden Beispiel wird die Vertrauensstellung mit dem Namen *myAzureADDSTrust* zwischen der Gesamtstruktur der verwalteten Domäne mit dem Namen *aaddscontoso.com* und der lokalen Gesamtstruktur *onprem.contoso.com* entfernt. Geben Sie den Namen der eigenen Gesamtstruktur der verwalteten Domäne und den Namen der lokalen Gesamtstruktur für das Entfernen an:

    ```powershell
    Remove-AaddsResourceForestTrust `
        -ManagedDomainFqdn "aaddscontoso.com" `
        -TrustFqdn "onprem.contoso.com" `
        -TrustFriendlyName "myAzureADDSTrust"
    ```

Zum Entfernen der unidirektionalen eingehenden Vertrauensstellung von der lokalen AD DS-Gesamtstruktur stellen Sie eine Verbindung mit einem Verwaltungscomputer mit Zugriff auf die lokale AD DS-Gesamtstruktur her, und führen Sie die folgenden Schritte aus:

1. Wählen Sie **Start | Verwaltung | Active Directory-Domänen und -Vertrauensstellungen** aus.
1. Wählen Sie mit der rechten Maustaste eine Domäne aus, z. B. *onprem.contoso.com*, und wählen Sie **Eigenschaften** aus.
1. Wählen Sie die Registerkarte **Vertrauensstellungen** aus, und wählen Sie dann die vorhandene eingehende Vertrauensstellung von Ihrer Gesamtstruktur der verwalteten Domäne aus.
1. Wählen Sie **Entfernen** aus, und bestätigen Sie dann, dass Sie die eingehende Vertrauensstellung entfernen möchten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Ressourcengesamtstruktur der verwalteten Domäne mithilfe von Azure PowerShell
> * Erstellen einer unidirektionalen ausgehenden Gesamtstruktur-Vertrauensstellung in der verwalteten Domäne mithilfe von Azure PowerShell
> * Konfigurieren von DNS in einer lokalen AD DS-Umgebung zur Unterstützung der Konnektivität der verwalteten Domäne
> * Erstellen einer unidirektionalen eingehenden Gesamtstruktur-Vertrauensstellung in einer lokalen AD DS-Umgebung
> * Testen und Überprüfen der Vertrauensstellung hinsichtlich Authentifizierung und Ressourcenzugriff

Weitere konzeptbezogene Informationen zu Gesamtstrukturtypen in Azure AD DS finden Sie unter [Was sind Ressourcengesamtstrukturen?][concepts-forest] sowie unter [Wie funktionieren Gesamtstruktur-Vertrauensstellungen in Azure AD DS?][concepts-trust].

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[network-peering]: ../virtual-network/virtual-network-peering-overview.md
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[Get-AzureRMSubscription]: /powershell/module/AzureRM.Profile/Get-AzureRmSubscription
[Install-Script]: /powershell/module/powershellget/install-script

<!-- EXTERNAL LINKS -->
[powershell-gallery]: https://www.powershellgallery.com/