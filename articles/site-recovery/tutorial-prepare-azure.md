---
title: Vorbereiten von Azure für die Notfallwiederherstellung von lokalen Computern mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure für die Notfallwiederherstellung von lokalen Computern mit Azure Site Recovery vorbereiten.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cb2ca7229524cf8d84041140129c7b9ca6876ea3
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417807"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Vorbereiten von Azure-Ressourcen für die Notfallwiederherstellung von lokalen Computern

In diesem Artikel wird beschrieben, wie Sie Azure-Ressourcen und -Komponenten so vorbereiten, dass Sie die Notfallwiederherstellung lokaler virtueller VMware- und Hyper-V-Computer oder physischer Windows-/Linux-Server in Azure mit dem Dienst [Azure Site Recovery](site-recovery-overview.md) einrichten können.

In diesem Artikel ist das erste Tutorial in einer Reihe, welche die Einrichtung der Notfallwiederherstellung für lokale virtuelle Computer veranschaulicht. 


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen, ob Ihr Azure-Konto über Replikationsberechtigungen verfügt
> * Erstellen Sie einen Recovery Services-Tresor. Ein Tresor enthält Metadaten und Konfigurationsinformationen für virtuelle Computer und andere Replikationskomponenten.
> * Einrichten eines virtuellen Azure-Netzwerks (VNet). Wenn virtuelle Azure-Computer nach einem Failover erstellt werden, werden sie mit diesem Netzwerk verbunden.

> [!NOTE]
> Tutorials zeigen den einfachsten Bereitstellungspfad für ein Szenario. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in dem Artikel, der im Anleitungsabschnitt des Site Recovery-Inhaltsverzeichnisses angegeben ist.

## <a name="before-you-start"></a>Vorbereitung

- Überprüfen Sie die Architektur für die Notfallwiederherstellung von [VMware](vmware-azure-architecture.md), [Hyper-V](hyper-v-azure-architecture.md) und [physischen Servern](physical-azure-architecture.md).
- Lesen Sie die allgemeinen Fragen zu [VMware](vmware-azure-common-questions.md) und [Hyper-V](hyper-v-azure-common-questions.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.


## <a name="verify-account-permissions"></a>Überprüfen von Kontoberechtigungen

Wenn Sie gerade ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements und besitzen die erforderlichen Berechtigungen. Falls Sie nicht der Abonnementadministrator sind, können Sie sich an den Administrator wenden, damit dieser die erforderlichen Berechtigungen zuweist. Zum Aktivieren der Replikation für einen neuen virtuellen Computer benötigen Sie die folgenden Berechtigungen:

- Erstellen einer VM in der ausgewählten Ressourcengruppe
- Erstellen einer VM im ausgewählten virtuellen Netzwerk
- Schreiben in ein Azure-Speicherkonto
- Schreiben auf einen verwalteten Azure-Datenträger

Zum Ausführen dieser Aufgaben muss Ihrem Konto die integrierte Rolle „Mitwirkender von virtuellen Computern“ zugewiesen werden. Zum Verwalten von Site Recovery-Vorgängen in einem Tresor muss Ihrem Konto außerdem die integrierte Rolle „Site Recovery-Mitwirkender“ zugewiesen werden.


## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

1. Klicken Sie im Azure-Portal auf **+ Ressource erstellen**, und suchen Sie im Marketplace nach **Wiederherstellung**.
2. Klicken Sie auf **Backup & Site Recovery (OMS)** , und klicken Sie auf der Seite „Backup & Site Recovery“ auf **Erstellen**. 
1. Geben Sie unter **Recovery Services-Tresor** > **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird. Für diese Tutorials verwenden wir **ContosoVMVault**.
2. Wählen Sie in **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Für dieses Tutorial verwenden wir **contosoRG**.
3. Wählen Sie in **Standort** die Region aus, in der der Tresor gespeichert werden soll. verwenden wir **Europa, Westen**.
4. Wählen Sie **An Dashboard anheften** > **Erstellen**, um über das Dashboard schnell auf den Tresor zuzugreifen.

   ![Erstellen eines neuen Tresors](./media/tutorial-prepare-azure/new-vault-settings.png)

   Der neue Tresor wird unter **Dashboard** > **Alle Ressourcen** und auf der Hauptseite **Recovery Services-Tresore** angezeigt.

## <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

Lokale Computer werden auf verwalteten Azure-Datenträgern repliziert. Bei einem Failover werden auf der Grundlage dieser verwalteten Datenträger virtuelle Azure-Computer erstellt und mit dem in diesem Verfahren angegebenen Azure-Netzwerk verknüpft.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**.
2. Behalten Sie **Resource Manager** als Auswahl für das Bereitstellungsmodell bei.
3. Geben Sie unter **Name** einen Netzwerknamen ein. Der Name muss innerhalb der Azure-Ressourcengruppe eindeutig sein. Wir verwenden **ContosoASRnet** in diesem Tutorial.
4. Geben Sie die Ressourcengruppe an, in der das Netzwerk erstellt wird. Wir verwenden die vorhandene Ressourcengruppe **contosoRG**.
5. Geben Sie unter **Adressbereich** den Netzwerkadressbereich ein. Hier wird **10.1.0.0/24** und kein Subnetz verwendet.
6. Wählen Sie unter **Abonnement** das Abonnement aus, in dem das Netzwerk erstellt werden soll.
7. Wählen Sie unter **Standort** die gleiche Region aus, in der auch der Recovery Services-Tresor erstellt wurde. In diesem Tutorial wird **Europa, Westen** verwendet. Das Netzwerk muss sich in der gleichen Region wie der Tresor befinden.
8. Wir behalten die Standardoptionen des DDoS-Basisschutzes ohne Dienstendpunkt im Netzwerk bei.
9. Klicken Sie auf **Create**.

   ![Erstellen eines virtuellen Netzwerks](media/tutorial-prepare-azure/create-network.png)

Die Erstellung des virtuellen Netzwerks dauert ein paar Sekunden. Nach der Erstellung wird es auf dem Dashboard im Azure-Portal angezeigt.




## <a name="next-steps"></a>Nächste Schritte

- VMware-Notfallwiederherstellung: [Vorbereiten der lokalen VMware-Infrastruktur](tutorial-prepare-on-premises-vmware.md)
- Hyper-V-Notfallwiederherstellung: [Vorbereiten lokaler Hyper-V-Server](hyper-v-prepare-on-premises-tutorial.md)
- Notfallwiederherstellung physischer Server: [Einrichten des Konfigurationsservers und der Quellumgebung](physical-azure-disaster-recovery.md)
- [Informationen zu](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure-Netzwerken.
- [Informationen zu verwalteten Datenträgern](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)
