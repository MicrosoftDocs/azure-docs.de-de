---
title: Vorbereiten von Azure-Ressourcen für die Notfallwiederherstellung von lokalen Computern
description: Hier erfahren Sie, wie Sie Azure für die Notfallwiederherstellung lokaler virtueller Hyper-V-Computer mit Azure Site Recovery vorbereiten.
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 07a5ee6ccdaecc78c9a8e61ae9e64a5264e3a875
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418355"
---
# <a name="prepare-azure-resources-for-disaster-recovery-of-on-premises-machines"></a>Vorbereiten von Azure-Ressourcen für die Notfallwiederherstellung von lokalen Computern

 [Azure Site Recovery](site-recovery-overview.md) unterstützt Business Continuity & Disaster Recovery (BCDR), indem die Verfügbarkeit von Geschäftsanwendungen bei geplanten und ungeplanten Ausfällen gewährleistet wird. Site Recovery verwaltet und koordiniert die Notfallwiederherstellung von lokalen Computern sowie virtuellen Azure-Computern (VMs), einschließlich Replikation, Failover und Wiederherstellung.

Dieses Tutorial ist das erste Tutorial in einer Reihe, die die Einrichtung der Notfallwiederherstellung für lokale virtuelle Hyper-V-Computer veranschaulicht.

> [!NOTE]
> Wir entwickeln Tutorials, die zur Veranschaulichung des einfachsten Bereitstellungspfads für ein Szenario dienen. Diese Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Weitere Informationen zu den einzelnen Szenarien finden Sie im Abschnitt mit den Anleitungen.

In diesem Tutorial wird gezeigt, wie Sie Azure-Komponenten vorbereiten, wenn Sie lokale virtuelle Computer (Hyper-V) in Azure replizieren möchten. Sie lernen Folgendes:

> [!div class="checklist"]
> * Überprüfen, ob Ihr Azure-Konto über Replikationsberechtigungen verfügt
> * Erstellen Sie ein Azure-Speicherkonto, in dem Images der replizierten Computer gespeichert werden.
> * Erstellen Sie einen Recovery Services-Tresor, in dem Metadaten und Konfigurationsinformationen für virtuelle Computer und andere Replikationskomponenten gespeichert werden.
> * Richten Sie ein Azure-Netzwerk ein. Wenn virtuelle Azure-Computer nach einem Failover erstellt werden, werden sie mit diesem Netzwerk verbunden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="sign-in"></a>Anmelden

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="verify-account-permissions"></a>Überprüfen von Kontoberechtigungen

Wenn Sie gerade ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator dieses Abonnements. Falls Sie nicht der Administrator sind, können Sie sich an den Administrator wenden, damit dieser die erforderlichen Berechtigungen zuweist. Zum Aktivieren der Replikation für einen neuen virtuellen Computer benötigen Sie die folgenden Berechtigungen:

- Erstellen einer VM in der ausgewählten Ressourcengruppe
- Erstellen einer VM im ausgewählten virtuellen Netzwerk
- Schreiben in das ausgewählte Speicherkonto

Zum Ausführen dieser Aufgaben muss Ihrem Konto die integrierte Rolle „Mitwirkender von virtuellen Computern“ zugewiesen werden. Zum Verwalten von Site Recovery-Vorgängen in einem Tresor muss Ihrem Konto die integrierte Rolle „Site Recovery-Mitwirkender“ zugewiesen werden.

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Images der replizierten Computer sind in Azure Storage gespeichert. Azure-VMs werden aus dem Speicher erstellt, wenn Sie ein Failover von einem lokalen Standort nach Azure ausführen. Das Speicherkonto muss sich in der gleichen Region wie der Recovery Services-Tresor befinden.

1. Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) die Optionen **Ressource erstellen** > **Speicher** > **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** aus.
2. Geben Sie unter **Speicherkonto erstellen** einen Namen für das Konto ein.  Der gewählte Name muss innerhalb von Azure eindeutig und 3 bis 24 Zeichen lang sein und darf nur aus Kleinbuchstaben und Zahlen bestehen. Verwenden Sie für dieses Tutorial **contosovmsacct1910171607**.
3. Wählen Sie unter **Bereitstellungsmodell** die Option **Resource Manager**.
4. Wählen Sie unter **Kontoart** die Option **Speicher (universell v1)** aus. Wählen Sie keinen Blobspeicher aus.
5. Wählen Sie unter **Replikation** für die Speicherredundanz die Standardoption **Georedundanter Speicher mit Lesezugriff**. Übernehmen Sie für die Einstellung „Sichere Übertragung erforderlich“ die Einstellung „Deaktiviert“.
6. Wählen Sie unter **Leistung** die Option **Standard**. Wählen Sie als Nächstes unter **Zugriffsebene** die Standardoption **Heiße Ebene** aus.
7. Wählen Sie unter **Abonnement** das Abonnement aus, in dem Sie das neue Speicherkonto erstellen möchten.
8. Geben Sie unter **Ressourcengruppe** eine neue Ressourcengruppe ein. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Verwenden Sie für dieses Tutorial **ContosoRG**.
9. Wählen Sie unter **Standort** den geografischen Standort für das Speicherkonto aus. Verwenden Sie für dieses Tutorial **Europa, Westen**.
10. Wählen Sie **Erstellen**, um das Speicherkonto zu erstellen.

   ![Speicherkonto erstellen](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

1. Wählen Sie im Azure-Portal **+Ressource erstellen** aus, und suchen Sie im Azure Marketplace nach „Recovery Services“.
2. Wählen Sie **Backup & Site Recovery (OMS)** aus. Wählen Sie als Nächstes auf der Seite **Backup und Site Recovery** die Option **Erstellen** aus.
1. Geben Sie unter **Recovery Services-Tresor > Name** einen Anzeigenamen ein, mit dem der Tresor identifiziert wird. Verwenden Sie für dieses Tutorial den Namen **ContosoVMVault**.
2. Wählen Sie in **Ressourcengruppe** eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. Verwenden Sie für dieses Tutorial **contosoRG**.
3. Wählen Sie unter **Standort** die Region aus, in der sich der Tresor befinden soll. Verwenden Sie für dieses Tutorial **Europa, Westen**.
4. Wählen Sie **An Dashboard anheften** > **Erstellen**, um über das Dashboard schnell auf den Tresor zuzugreifen.

![Erstellen eines neuen Tresors](./media/tutorial-prepare-azure/new-vault-settings.png)

Der neue Tresor wird unter **Dashboard** > **Alle Ressourcen** und auf der Hauptseite **Recovery Services-Tresore** angezeigt.

## <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

Wenn die Azure-VMs nach einem Failover aus dem Speicher erstellt werden, werden sie mit diesem Netzwerk verknüpft.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**. Übernehmen Sie für das Bereitstellungsmodell die Auswahl „Resource Manager“.
2. Geben Sie unter **Name** einen Netzwerknamen ein. Der Name muss innerhalb der Azure-Ressourcengruppe eindeutig sein. Verwenden Sie für dieses Tutorial **ContosoASRnet**.
3. Geben Sie die Ressourcengruppe an, in der das Netzwerk erstellt werden soll. Verwenden Sie in diesem Tutorial die vorhandene Ressourcengruppe **contosoRG**.
4. Geben Sie unter **Adressbereich** den Netzwerkadressbereich **10.0.0.0/24** ein. Für dieses Netzwerk ist kein Subnetz vorhanden.
5. Wählen Sie unter **Abonnement** das Abonnement aus, in dem das Netzwerk erstellt werden soll.
6. Wählen Sie unter **Standort** die Option **Europa, Westen** aus. Das Netzwerk muss sich in derselben Region wie der Recovery Services-Tresor befinden.
7. Behalten Sie die Standardoptionen des DDoS-Basisschutzes ohne Dienstendpunkt im Netzwerk bei.
8. Klicken Sie auf **Erstellen**.

![Erstellen eines virtuellen Netzwerks](media/tutorial-prepare-azure/create-network.png)

Die Erstellung des virtuellen Netzwerks dauert ein paar Sekunden. Nach der Erstellung wird es auf dem Dashboard im Azure-Portal angezeigt.

## <a name="useful-links"></a>Nützliche Links

Sie erhalten Informationen zu folgenden Themen:
- [Azure-Netzwerke](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
- [Verwaltete Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorbereiten lokaler Hyper-V-Server für die Notfallwiederherstellung in Azure](hyper-v-prepare-on-premises-tutorial.md)
