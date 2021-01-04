---
title: Vorbereiten von Azure Site Recovery-Ressourcen für die Notfallwiederherstellung von virtuellen Azure VMware Solution-Computern
description: Hier erfahren Sie, wie Sie Azure-Ressourcen für die Notfallwiederherstellung von Azure VMware Solution-VMs mit Azure Site Recovery vorbereiten.
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 2bd305e3760a8c3d743037e7d90b71f5e9579eda
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395477"
---
# <a name="prepare-azure-site-recovery-resources-for-disaster-recovery-of-azure-vmware-solution-vms"></a>Vorbereiten von Azure Site Recovery-Ressourcen für die Notfallwiederherstellung von virtuellen Azure VMware Solution-Computern

In diesem Artikel erfahren Sie, wie Sie Azure-Ressourcen und -Komponenten so vorbereiten, dass Sie die Notfallwiederherstellung von Azure VMware Solution-VMs (virtuelle Computer) mithilfe des [Azure Site Recovery](site-recovery-overview.md)-Diensts einrichten können. [Azure VMware Solution](../azure-vmware/introduction.md) stellt private Clouds in Azure bereit. Diese privaten Clouds enthalten vSphere-Cluster, die auf dedizierter Bare-Metal-Azure-Infrastruktur basieren.

Dieser Artikel ist das erste Tutorial einer Reihe, in der Sie das Einrichten der Notfallwiederherstellung für Azure VMware Solution-VMs erlernen. 


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen, ob Ihr Azure-Konto über Replikationsberechtigungen verfügt
> * Erstellen Sie einen Recovery Services-Tresor. Ein Tresor enthält Metadaten und Konfigurationsinformationen für virtuelle Computer und andere Replikationskomponenten.
> * Einrichten eines virtuellen Azure-Netzwerks (VNet). Wenn virtuelle Azure-Computer nach einem Failover erstellt werden, werden sie mit diesem Netzwerk verbunden.

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

> [!NOTE]
> Einige Konzepte der Verwendung von Azure Site Recovery für Azure VMware Solution überlappen mit der Notfallwiederherstellung von lokalen VMware-VMs. Daher wird entsprechend auf die Dokumentation verwiesen.

## <a name="before-you-start"></a>Vorbereitung

- Sie müssen eine private Azure VMware Solution-Cloud in Azure [bereitstellen](../azure-vmware/tutorial-create-private-cloud.md).
- Überprüfen Sie die Architektur für die [VMware](vmware-azure-architecture.md)-Notfallwiederherstellung.
- Lesen Sie die allgemeinen Fragen zu [VMware](vmware-azure-common-questions.md).

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.


## <a name="verify-account-permissions"></a>Überprüfen von Kontoberechtigungen

Wenn Sie gerade ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements und besitzen die erforderlichen Berechtigungen. Falls Sie nicht der Abonnementadministrator sind, können Sie sich an den Administrator wenden, damit dieser die erforderlichen Berechtigungen zuweist. Zum Aktivieren der Replikation für einen neuen virtuellen Computer benötigen Sie die folgenden Berechtigungen:

- Erstellen einer VM in der ausgewählten Ressourcengruppe
- Erstellen einer VM im ausgewählten virtuellen Netzwerk
- Schreiben in ein Azure-Speicherkonto
- Schreiben auf einen verwalteten Azure-Datenträger

Zum Ausführen dieser Aufgaben muss Ihrem Konto die integrierte Rolle „Mitwirkender von virtuellen Computern“ zugewiesen werden. Zum Verwalten von Site Recovery-Vorgängen in einem Tresor muss Ihrem Konto außerdem die integrierte Rolle „Site Recovery-Mitwirkender“ zugewiesen werden.


## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

1. Klicken Sie im Azure-Portal im Menü auf **Ressource erstellen**, und suchen Sie in Marketplace nach **Wiederherstellung**.
2. Klicken Sie in den Suchergebnissen auf **Backup & Site Recovery**, und klicken Sie auf der Seite „Backup & Site Recovery“ auf **Erstellen**. 
3. Wählen Sie auf der Seite **Recovery Services-Tresor erstellen** das **Abonnement** aus. Hier wird **Contoso Subscription** verwendet.
4. Wählen Sie in **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Für dieses Tutorial verwenden wir **contosoRG**.
5. Geben Sie in **Tresorname** einen Anzeigenamen zum Bestimmen des Tresors ein. Für diese Tutorials verwenden wir **ContosoVMVault**.
6. Wählen Sie in **Region** die Region aus, in der sich der Tresor befinden soll. verwenden wir **Europa, Westen**.
7. Klicken Sie auf **Überprüfen + erstellen**.

   ![Screenshot: Seite „Recovery Services-Tresor erstellen“](./media/tutorial-prepare-azure/new-vault-settings.png)

   Der neue Tresor wird nun unter **Dashboard** > **Alle Ressourcen** und auf der Hauptseite **Recovery Services-Tresore** aufgeführt.

## <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

 Azure VMware Solution-VMs werden auf verwalteten Azure-Datenträgern repliziert. Bei einem Failover werden auf der Grundlage dieser verwalteten Datenträger virtuelle Azure-Computer erstellt und mit dem in diesem Verfahren angegebenen Azure-Netzwerk verknüpft.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**.
2. Behalten Sie **Resource Manager** als Auswahl für das Bereitstellungsmodell bei.
3. Geben Sie unter **Name** einen Netzwerknamen ein. Der Name muss innerhalb der Azure-Ressourcengruppe eindeutig sein. Wir verwenden **ContosoASRnet** in diesem Tutorial.
4. Geben Sie unter **Adressraum** den Adressbereich des virtuellen Netzwerks in CIDR-Notation ein. Wir verwenden **10.1.0.0/24**.
5. Wählen Sie unter **Abonnement** das Abonnement aus, in dem das Netzwerk erstellt werden soll.
6. Geben Sie die **Ressourcengruppe** an, in der das Netzwerk erstellt wird. Wir verwenden die vorhandene Ressourcengruppe **contosoRG**.
7. Wählen Sie unter **Standort** die gleiche Region aus, in der auch der Recovery Services-Tresor erstellt wurde. In diesem Tutorial wird **Europa, Westen** verwendet. Das Netzwerk muss sich in der gleichen Region wie der Tresor befinden.
8. Geben Sie unter **Adressbereich** den Netzwerkadressbereich ein. Hier wird **10.1.0.0/24** und kein Subnetz verwendet.
9. Wir behalten die Standardoptionen des DDoS-Basisschutzes ohne Dienstendpunkt oder Firewall im Netzwerk bei.
9. Klicken Sie auf **Erstellen**.

   ![Screenshot: Optionen zum Erstellen eines virtuellen Netzwerks](media/tutorial-prepare-azure/create-network.png)

Die Erstellung des virtuellen Netzwerks dauert ein paar Sekunden. Nach der Erstellung wird es auf dem Dashboard im Azure-Portal angezeigt.




## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Infrastruktur vorbereiten](avs-tutorial-prepare-avs.md)
- [Informationen zu](../virtual-network/virtual-networks-overview.md) Azure-Netzwerken.
- [Informationen zu verwalteten Datenträgern](../virtual-machines/managed-disks-overview.md)
