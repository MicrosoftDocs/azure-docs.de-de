---
title: Zuordnung virtueller Netzwerke zwischen zwei Regionen in Azure Site Recovery
description: Erfahren Sie mehr über die Zuordnung virtueller Netzwerke zwischen zwei Azure-Regionen für die Notfallwiederherstellung von virtuellen Azure-Computern mit Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: ff1f80641dc3db1f6b69fc0223c60022f8cf8435
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95811625"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Einrichten der Netzwerkzuordnung und IP-Adressierung für VNETs

In diesem Artikel wird beschrieben, wie Sie zwei Instanzen von virtuellen Azure-Netzwerken (VNETs) in verschiedenen Azure-Regionen einander zuordnen und IP-Adressierung zwischen Netzwerken einrichten. Die Netzwerkzuordnung stellt ein Standardverhalten für Zielnetzwerkauswahl basierend auf dem Quellnetzwerk zum Zeitpunkt der Aktivierung der Replikation bereit.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Netzwerke zuordnen, müssen [Azure-VNETs](../virtual-network/virtual-networks-overview.md) in der Azure-Quell- und -Zielregion vorhanden sein. 

## <a name="set-up-network-mapping-manually-optional"></a>Manuelles Einrichten der Netzwerkzuordnung (optional)

Ordnen Sie Netzwerke wie folgt zu:

1. Klicken Sie unter **Site Recovery-Infrastruktur** auf **+Netzwerkzuordnung**.

    ![ Erstellen einer Netzwerkzuordnung](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Wählen Sie unter **Netzwerkzuordnung hinzufügen** die Quell- und Zielstandorte aus. In unserem Beispiel wird die Quell-VM in der Region „Asien, Osten“ ausgeführt und in der Region „Asien, Südosten“ repliziert.

    ![Auswählen von Quelle und Ziel](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Erstellen Sie nun eine Netzwerkzuordnung in umgekehrter Richtung. In unserem Beispiel ist die Quelle nun „Asien, Südosten“ und das Ziel „Asien, Osten“.

    ![Bereich „Netzwerkzuordnung hinzufügen“ – Auswählen der Quell- und Zielstandorte für das Zielnetzwerk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Zuordnen von Netzwerken beim Aktivieren der Replikation

Wenn Sie die Netzwerkzuordnung nicht vorbereitet haben, bevor Sie die Notfallwiederherstellung für Azure-VMs konfigurieren, können Sie ein Zielnetzwerk angeben, wenn Sie [die Replikation einrichten und aktivieren](azure-to-azure-how-to-enable-replication.md). Dabei geschieht Folgendes:

- Basierend auf dem von Ihnen ausgewählten Ziel erstellt Site Recovery automatisch Netzwerkzuordnungen von der Quellregion zur Zielregion und von der Zielregion zur Quellregion.
- Site Recovery erstellt standardmäßig ein Netzwerk in der Zielregion, das identisch mit dem Quellnetzwerk ist. Site Recovery fügt den Suffix **-asr** an den Namen des Quellnetzwerks an. Sie können das Zielnetzwerk anpassen.
- Wenn die Netzwerkzuordnung für ein Quellnetzwerk bereits vorgenommen wurde, ist das zugeordnete Zielnetzwerk immer das Standardnetzwerk zum Zeitpunkt der Aktivierung von Replikationen weiterer virtueller Computer. Sie können das virtuelle Zielnetzwerk ändern, indem Sie in der Dropdownliste andere verfügbare Optionen auswählen. 
- Zum Ändern des virtuellen Standard-Zielnetzwerks müssen Sie die vorhandene Netzwerkzuordnung ändern.
- Wenn Sie eine Netzwerkzuordnung von Region A nach Region B ändern möchten, löschen Sie zunächst die Netzwerkzuordnung von Region B nach Region A. Ändern Sie nach dem Löschen der umgekehrten Zuordnung die Netzwerkzuordnung von Region A nach Region B, und erstellen Sie dann die gewünschte umgekehrte Zuordnung.

>[!NOTE]
>* Das Ändern der Netzwerkzuordnung ändert nur die Standardwerte für neue VM-Replikationen. Der Vorgang wirkt sich nicht auf die Auswahl des virtuellen Zielnetzwerks für vorhandene Replikationen aus. 
>* Wenn Sie das Zielnetzwerk für eine vorhandene Replikation ändern möchten, verwenden Sie die Einstellungen unter „Compute und Netzwerk“ für das replizierte Element.

## <a name="specify-a-subnet"></a>Angeben eines Subnetzes

Das Subnetz der Ziel-VM wird basierend auf dem Namen des Subnetzes der Quell-VM ausgewählt.

- Wenn im Zielnetzwerk ein Subnetz mit demselben Namen wie bei der Quell-VM verfügbar ist, wird dieses Subnetz für die Ziel-VM ausgewählt.
- Wenn im Zielnetzwerk kein Subnetz mit demselben Namen vorhanden ist, wird das erste Subnetz in der alphabetischen Reihenfolge als Zielsubnetz ausgewählt.
- Sie können das Zielsubnetz in den **Compute und Netzwerk**-Einstellungen für die VM ändern.

    ![Fenster „Eigenschaften für Compute und Netzwerk“](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Einrichten der IP-Adressierung für Ziel-VMs

Die IP-Adresse für die einzelnen Netzwerkkarten auf einem virtuellen Zielcomputer wird wie folgt konfiguriert:

- **DHCP**: Falls die Netzwerkkarte der Quell-VM DHCP verwendet, wird die Netzwerkkarte der Ziel-VM ebenfalls als DHCP festgelegt.
- **Statische IP-Adresse**: Wenn die Netzwerkkarte der Quell-VM die statische IP-Adressierung verwendet, verwendet die Netzwerkkarte der Ziel-VM auch eine statische IP-Adresse.


## <a name="ip-address-assignment-during-failover"></a>IP-Adresszuweisung beim Failover

**Quell- und Zielsubnetze** | **Details**
--- | ---
Gleicher Adressraum | Die IP-Adresse der Netzwerkkarte der Quell-VM wird als IP-Adresse der Netzwerkkarte der Ziel-VM festgelegt.<br/><br/> Wenn die Adresse nicht verfügbar ist, wird die nächste verfügbare IP-Adresse als Ziel festgelegt.
Unterschiedlicher Adressraum | Die nächste verfügbare IP-Adresse im Zielsubnetz wird als Netzwerkkartenadresse der Ziel-VM festgelegt.



## <a name="ip-address-assignment-during-test-failover"></a>IP-Adresszuweisung beim Testfailover

**Zielnetzwerk** | **Details**
--- | ---
Das Zielnetzwerk ist das Failover-VNET. | – Die Ziel-IP-Adresse ist statisch mit derselben IP-Adresse. <br/><br/>  – Wenn dieselbe IP-Adresse bereits zugewiesen wurde, ist die IP-Adresse die nächste verfügbare IP-Adresse am Ende des Subnetzbereichs. Beispiel: Wenn die IP-Quelladresse 10.0.0.19 lautet und das Failovernetzwerk den Bereich 10.0.0.0/24 verwendet, lautet die nächste IP-Adresse, die der Ziel-VM zugewiesen wird, 10.0.0.254.
Das Zielnetzwerk ist nicht das Failover-VNET. | – Die Ziel-IP-Adresse ist statisch mit derselben IP-Adresse.<br/><br/>  – Wenn dieselbe IP-Adresse bereits zugewiesen wurde, ist die IP-Adresse die nächste verfügbare IP-Adresse am Ende des Subnetzbereichs.<br/><br/> Beispiel: Wenn die statische Quell-IP-Adresse 10.0.0.19 lautet und ein Failover in einem Netzwerk ausgeführt wird, das nicht das Failovernetzwerk mit dem Bereich 10.0.0.0/24 darstellt, lautet die statische IP-Zieladresse 10.0.0.19, falls verfügbar, und andernfalls 10.0.0.254.

- Das Failover-VNET ist das Zielnetzwerk, das Sie beim Einrichten der Notfallwiederherstellung auswählen.
- Es wird empfohlen, immer ein Netzwerk für das Testfailover zu verwenden, das nicht für die Produktion bestimmt ist.
- Sie können die Ziel-IP-Adresse in den Einstellungen unter **Compute und Netzwerk** der VM ändern.


## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Notfallwiederherstellung bei Azure-VMs finden Sie unter [Grundlegendes zu Netzwerken bei Replikationen mit Azure als Quelle und Ziel](./azure-to-azure-about-networking.md).
- Erfahren Sie mehr über [die Beibehaltung von IP-Adressen nach einem Failover](site-recovery-retain-ip-azure-vm-failover.md).
