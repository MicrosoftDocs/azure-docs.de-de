---
title: 'Tutorial: Konfigurieren eines virtuellen Netzwerks für die Azure AD Domain Services | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie über das Azure-Portal ein Azure-VNET-Subnetz oder das Netzwerkpeering für eine verwaltete Azure Active Directory Domain Services-Domäne erstellen und konfigurieren.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618415"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Konfigurieren Sie virtuelle Netzwerke für eine verwaltete Azure Active Directory Domain Services-Domäne.

Um Konnektivität für Benutzer und Anwendungen bereitzustellen, wird eine verwaltete Azure AD DS-Domäne (Azure Active Directory Domain Services) in einem Azure-VNET-Subnetz bereitgestellt. Dieses VNET-Subnetz darf nur für die von der Azure-Plattform bereitgestellten Ressourcen der verwalteten Domäne verwendet werden.

Selbst erstellte virtuelle Computer und Anwendungen dürfen nicht im gleichen VNET-Subnetz bereitgestellt werden. Stattdessen müssen Ihre Anwendungen in einem separaten VNET-Subnetz oder in einem separaten virtuellen Netzwerk erstellt und bereitgestellt werden, das mittels Peering mit dem virtuellen Azure AD DS-Netzwerk verbunden ist.

In diesem Tutorial erfahren Sie, wie Sie ein dediziertes VNET-Subnetz erstellen und konfigurieren oder ein anderes Netzwerk mittels Peering mit dem virtuellen Netzwerk der verwalteten Azure AD DS-Domäne verbinden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Nachvollziehen der VNET-Konnektivitätsoptionen für in die Domäne eingebundene Ressourcen im Zusammenhang mit Azure AD DS
> * Erstellen eines IP-Adressbereichs und eines zusätzlichen Subnetzes im virtuellen Azure AD DS-Netzwerk
> * Konfigurieren des Peerings virtueller Netzwerke mit einem von Azure AD DS getrennten Netzwerk

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Sie benötigen in Ihrem Azure AD-Mandanten Berechtigungen vom Typ *Globaler Administrator*, um Azure AD DS zu konfigurieren.
* Sie benötigen Berechtigungen als *Mitwirkender* in Ihrem Azure-Abonnement, um die erforderlichen Azure AD DS-Ressourcen zu erstellen.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Falls Sie keine solche Domäne haben, gehen Sie wie im ersten Tutorial beschrieben vor, um eine [verwaltete Azure Active Directory Domain Services-Domäne zu erstellen und zu konfigurieren][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Tutorial erstellen und konfigurieren Sie die verwaltete Domäne im Azure-Portal. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="application-workload-connectivity-options"></a>Konnektivitätsoptionen für die Anwendungsworkload

Im vorherigen Tutorial wurde eine verwaltete Domäne mit einigen Standardkonfigurationsoptionen für das virtuelle Netzwerk erstellt. Durch diese Standardoptionen wurden ein virtuelles Azure-Netzwerk und ein VNET-Subnetz erstellt. Die Azure AD DS-Domänencontroller, die die verwalteten Domänendienste bereitstellen, sind mit diesem VNET-Subnetz verbunden.

Wenn Sie virtuelle Computer erstellen und ausführen, die die verwaltete Domäne benötigen, ist Netzwerkkonnektivität erforderlich. Diese Netzwerkkonnektivität kann auf eine der folgenden Arten bereitgestellt werden:

* Erstellen Sie im virtuellen Netzwerk der verwalteten Domäne ein zusätzliches VNET-Subnetz. In diesem zusätzlichen Subnetz können Sie dann Ihre virtuellen Computer erstellen und verbinden.
    * Da die virtuellen Computer dem gleichen virtuellen Netzwerk angehören, können sie automatisch eine Namensauflösung durchführen und mit den Azure AD DS-Domänencontrollern kommunizieren.
* Konfigurieren Sie das Peering virtueller Azure-Netzwerke zwischen dem virtuellen Netzwerk der verwalteten Domäne und mindestens einem separaten virtuellen Netzwerk. In diesen separaten virtuellen Netzwerken können Sie Ihre virtuellen Computer erstellen und verbinden.
    * Wenn Sie das Peering virtueller Netzwerke konfigurieren, müssen Sie auch DNS-Einstellungen konfigurieren, um die Namensauflösung für die Azure AD DS-Domänencontroller zu verwenden.

In der Regel wird nur eine dieser Netzwerkkonnektivitätsoptionen verwendet. Die Wahl der Option hängt häufig davon ab, wie Sie Ihre Azure-Ressourcen verwalten/trennen möchten.

* Wenn Sie Azure AD DS und verbundene virtuelle Computer als einzelne Ressourcengruppe verwalten möchten, können Sie ein zusätzliches VNET-Subnetz für virtuelle Computer zu erstellen.
* Wenn Sie Azure AD DS und verbundene virtuelle Computer getrennt verwalten möchten, können Sie das Peering virtueller Netzwerke verwenden.
    * Das Peering virtueller Netzwerke kann auch verwendet werden, um Konnektivität für vorhandene virtuelle Computer in Ihrer Azure-Umgebung bereitzustellen, die mit einem vorhandenen virtuellen Netzwerk verbunden sind.

In diesem Tutorial muss lediglich eine dieser Verbindungsoptionen für das virtuelle Netzwerk konfiguriert werden.

Weitere Informationen zum Planen und Konfigurieren des virtuellen Netzwerks finden Sie unter [Netzwerkaspekte für die Azure Active Directory Domain Services][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Erstellen eines VNET-Subnetzes

Standardmäßig enthält das virtuelle Azure-Netzwerk, das mit der verwalteten Domäne erstellt wurde, ein einzelnes VNET-Subnetz. Dieses VNET-Subnetz darf von der Azure-Plattform nur verwendet werden, um Ressourcen der verwalteten Domäne bereitzustellen. Erstellen Sie ein zusätzliches Subnetz, um Ihre eigenen virtuellen Computer in diesem virtuellen Azure-Netzwerk zu erstellen und zu verwenden.

Gehen Sie wie folgt vor, um ein VNET-Subnetz für virtuelle Computer und Anwendungsworkloads zu erstellen:

1. Wählen Sie im Azure-Portal die Ressourcengruppe Ihrer verwalteten Domäne aus (beispielsweise *myResourceGroup*). Wählen Sie in der Ressourcenliste das virtuelle Standardnetzwerk aus (beispielsweise *aadds-vnet*).
1. Wählen Sie im Menü auf der linken Seite des Fensters des virtuellen Netzwerks die Option **Adressraum** aus. Das virtuelle Netzwerk wird mit einem einzelnen Adressraum (*10.0.2.0/24*) erstellt. Dieser wird vom Standardsubnetz verwendet.

    Fügen Sie dem virtuellen Netzwerk einen zusätzlichen IP-Adressbereich hinzu. Die Größe dieses Adressbereichs und der tatsächlich zu verwendende IP-Adressbereich hängen von anderen, bereits bereitgestellten Netzwerkressourcen ab. Der IP-Adressbereich darf sich nicht mit vorhandenen Adressbereichen in Ihrer Azure-Umgebung oder in Ihrer lokalen Umgebung überschneiden. Wählen Sie die Größe des IP-Adressbereichs so, dass sie für die Anzahl virtueller Computer ausreicht, die voraussichtlich im Subnetz bereitgestellt werden.

    Im folgenden Beispiel wird ein zusätzlicher IP-Adressbereich (*10.0.3.0/24*) hinzugefügt. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

    ![Hinzufügen eines zusätzlichen IP-Adressbereichs für das virtuelle Netzwerk im Azure-Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Wählen Sie als Nächstes im Menü auf der linken Seite des Fensters für das virtuelle Netzwerk die Option **Subnetze** und anschließend **+ Subnetz** aus, um ein Subnetz hinzuzufügen.
1. Geben Sie einen Namen für das Subnetz ein (beispielsweise *workloads*). Aktualisieren Sie bei Bedarf den **Adressbereich**, wenn Sie eine Teilmenge des IP-Adressbereichs verwenden möchten, der in den vorherigen Schritten für das virtuelle Netzwerk konfiguriert wurde. Behalten Sie für Optionen wie Netzwerksicherheitsgruppe, Routingtabelle und Dienstendpunkte vorerst die Standardeinstellungen bei.

    Im folgenden Beispiel wird ein Subnetz namens *workloads* mit dem IP-Adressbereich *10.0.3.0/24* erstellt:

    ![Hinzufügen eines zusätzlichen VNET-Subnetzes im Azure-Portal](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Wählen Sie nach Abschluss des Vorgangs **OK** aus. Die Erstellung des VNET-Subnetzes dauert etwas.

Wenn Sie einen virtuellen Computer erstellen, der die verwaltete Domäne verwenden muss, muss dieses VNET-Subnetz ausgewählt werden. Erstellen Sie keine virtuellen Computer im Standardsubnetz (*aadds-subnet*). Wenn Sie ein anderes virtuelles Netzwerk auswählen, besteht keine Netzwerkkonnektivität, und es gibt keine DNS-Auflösung, um die verwaltete Domäne zu erreichen – es sei denn, Sie konfigurieren das Peering virtueller Netzwerke.

## <a name="configure-virtual-network-peering"></a>Konfigurieren des VNET-Peerings

Möglicherweise verfügen Sie über ein bereits vorhandenes virtuelles Azure-Netzwerk für virtuelle Computer oder möchten Ihr virtuelles Netzwerk der verwalteten Domäne separat nutzen. Um die verwaltete Domäne verwenden zu können, müssen virtuelle Computer in anderen virtuellen Netzwerken mit den Azure AD DS-Domänencontrollern kommunizieren können. Diese Konnektivität kann mithilfe des Peerings virtueller Azure-Netzwerke bereitgestellt werden.

Mithilfe des Peerings virtueller Azure-Netzwerke werden zwei virtuelle Netzwerke ohne Verwendung eines VPN-Geräts (virtuelles privates Netzwerk) miteinander verbunden. Per Netzwerkpeering können Sie schnell eine Verbindung zwischen virtuellen Netzwerken herstellen und Datenverkehrsflüsse in Ihrer gesamten Azure-Umgebung definieren.

Weitere Informationen zum Peering finden Sie in der [Übersicht über das Peering virtueller Azure-Netzwerke][peering-overview].

Gehen Sie wie folgt vor, wenn Sie ein virtuelles Netzwerk mittels Peering mit dem virtuellen Netzwerk der verwalteten Domäne verbinden möchten:

1. Wählen Sie das virtuelle Standardnetzwerk aus, das für Ihre verwaltete Domäne namens *aadds-vnet* erstellt wurde.
1. Wählen Sie im Menü auf der linken Seite des Fensters des virtuellen Netzwerks die Option **Peerings** aus.
1. Wählen Sie **+ Hinzufügen** aus, um ein Peering zu erstellen. Im folgenden Beispiel wird das virtuelle Standardnetzwerk *aadds-vnet* mittels Peering mit einem virtuellen Netzwerk namens *myVnet* verbunden. Konfigurieren Sie die folgenden Einstellungen mit Ihren eigenen Werten:

    * **Name für das Peering von „aadds-vnet“ zu „Virtuelles Remotenetzwerk“** : Ein aussagekräftiger Bezeichner für die beiden Netzwerke (beispielsweise *aadds-vnet-to-myvnet*).
    * **Bereitstellungsmodell für das virtuelle Netzwerk**: *Ressourcen-Manager*
    * **Abonnement**: Das Abonnement des virtuellen Netzwerks, das Sie mittels Peering verbinden möchten (beispielsweise *Azure*).
    * **Virtuelles Netzwerk:** Das virtuelle Netzwerk, das Sie mittels Peering verbinden möchten (beispielsweise *myVnet*).
    * **Name für das Peering von „myVnet“ zu „aadds-vnet“** : Ein aussagekräftiger Bezeichner für die beiden Netzwerke (beispielsweise *myvnet-to-aadds-vnet*).

    ![Konfigurieren des Peerings virtueller Netzwerke im Azure-Portal](./media/tutorial-configure-networking/create-peering.png)

    Behalten Sie die Standardeinstellungen für den VNET-Zugriff sowie für weitergeleiteten Datenverkehr bei – es sei denn, Sie haben spezielle Anforderungen für Ihre Umgebung. Wählen Sie **OK** aus.

1. Es dauert etwas, bis das Peering sowohl für das virtuelle Azure AD DS-Netzwerk als auch für das ausgewählte virtuelle Netzwerk erstellt wurde. Nach Abschluss des Vorgangs wird unter **Peeringstatus** der Status *Verbunden* angezeigt, wie im folgenden Beispiel zu sehen:

    ![Erfolgreich mittels Peering verbundene Netzwerke im Azure-Portal](./media/tutorial-configure-networking/connected-peering.png)

Damit virtuelle Computer im virtuellen Netzwerk mit Peering die verwaltete Domäne verwenden können, müssen die DNS-Server für eine korrekte Namensauflösung konfiguriert werden.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Konfigurieren von DNS-Servern im mittels Peering verbundenen virtuellen Netzwerk

Damit virtuelle Computer und Anwendungen im virtuellen Netzwerk mit Peering erfolgreich mit der verwalteten Domäne kommunizieren können, müssen die DNS-Einstellungen aktualisiert werden. Die IP-Adressen der Azure AD DS-Domänencontroller müssen als DNS-Server für das mittels Peering verbundene virtuelle Netzwerk konfiguriert werden. Domänencontroller können auf zwei Arten als DNS-Server für das mittels Peering verbundene virtuelle Netzwerk konfiguriert werden:

* Konfigurieren Sie die DNS-Server des virtuellen Azure-Netzwerks für die Verwendung der Azure AD DS-Domänencontroller.
* Konfigurieren Sie den vorhandenen DNS-Server, der im virtuellen Netzwerk mit Peering verwendet wird, für die Verwendung der bedingten DNS-Weiterleitung, um Abfragen an die verwaltete Domäne weiterzuleiten. Diese Schritte sind abhängig vom verwendeten vorhandenen DNS-Server.

In diesem Tutorial werden die DNS-Server des virtuellen Azure-Netzwerks für die Weiterleitung aller Abfragen an die Azure AD DS-Domänencontroller konfiguriert.

1. Wählen Sie im Azure-Portal die Ressourcengruppe des mittels Peering verbundenen virtuellen Netzwerks aus (beispielsweise *myResourceGroup*). Wählen Sie in der Ressourcenliste das mittels Peering verbundene virtuelle Netzwerk aus (beispielsweise *myVnet*).
1. Wählen Sie im Menü auf der linken Seite des Fensters des virtuellen Netzwerks die Option **DNS-Server** aus.
1. Von einem virtuellen Netzwerk werden standardmäßig die integrierten, von Azure bereitgestellten DNS-Server verwendet. Wählen Sie die DNS-Serveroption **Benutzerdefiniert** aus. Geben Sie die IP-Adressen für die Azure AD DS-Domänencontroller ein (üblicherweise *10.0.2.4* und *10.0.2.5*). Bestätigen Sie diese IP-Adressen im Portal im Fenster **Übersicht** Ihrer verwalteten Domäne.

    ![Konfigurieren der DNS-Server des virtuellen Netzwerks für die Verwendung der Azure AD DS-Domänencontroller](./media/tutorial-configure-networking/custom-dns.png)

1. Wählen Sie **Speichern** aus, wenn Sie so weit sind. Es dauert etwas, bis die DNS-Server für das virtuelle Netzwerk aktualisiert wurden.
1. Starten Sie die virtuellen Computer, die mit dem mittels Peering verbundenen virtuellen Netzwerk verbunden sind, um die aktualisierten DNS-Einstellungen auf die virtuellen Computer anzuwenden.

Wenn Sie einen virtuellen Computer erstellen, der die verwaltete Domäne verwenden muss, muss dieses virtuelle Netzwerk mit Peering ausgewählt werden. Wenn Sie ein anderes virtuelles Netzwerk auswählen, besteht keine Netzwerkkonnektivität, und es gibt keine DNS-Auflösung, um die verwaltete Domäne zu erreichen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Nachvollziehen der VNET-Konnektivitätsoptionen für in die Domäne eingebundene Ressourcen im Zusammenhang mit Azure AD DS
> * Erstellen eines IP-Adressbereichs und eines zusätzlichen Subnetzes im virtuellen Azure AD DS-Netzwerk
> * Konfigurieren des Peerings virtueller Netzwerke mit einem von Azure AD DS getrennten Netzwerk

Um diese verwaltete Domäne in Aktion zu erleben, erstellen Sie eine VM, und binden Sie diese in die Domäne ein.

> [!div class="nextstepaction"]
> [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
