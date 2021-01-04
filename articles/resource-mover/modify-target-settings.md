---
title: Ändern der Zieleinstellungen beim Verschieben von Azure-VMs zwischen Regionen mithilfe von Azure Resource Mover
description: In diesem Artikel erfahren Sie, wie Sie die Zieleinstellungen ändern, wenn Sie Azure-VMs mithilfe von Azure Resource Mover zwischen Regionen verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 27e7c899f0d22789c10541fc98a0d2c63a7843ec
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533054"
---
# <a name="modify-target-settings"></a>Ändern von Zieleinstellungen

In diesem Artikel wird beschrieben, wie Sie die Zieleinstellungen ändern, wenn Sie Ressourcen mithilfe von [Azure Resource Mover](overview.md) zwischen Azure-Regionen verschieben.


## <a name="modify-vm-settings"></a>Ändern der VM-Einstellungen

Wenn Sie virtuelle Azure-Computer (Virtual Machines, VMs) und die dazugehörigen Ressourcen verschieben, können Sie die Zieleinstellungen ändern. 

- Es wird empfohlen, die Zieleinstellungen erst zu ändern, nachdem die Verschiebungssammlung überprüft wurde.
- Es wird außerdem empfohlen, die Einstellungen zu ändern, bevor Sie die Ressourcen vorbereiten. Der Grund ist, dass einige Zieleigenschaften nach Abschluss der Vorbereitung ggf. nicht mehr zur Bearbeitung zur Verfügung stehen.

Allerdings:
- Wenn Sie die Quellressource verschieben, können Sie die Zieleinstellungen in der Regel solange ändern, wie die Verschiebung noch nicht begonnen hat.
- Wenn Sie eine vorhandene Ressource in der Quellregion zuweisen, können Sie die Zieleinstellungen ändern, bis die Verschiebung beendet ist.

### <a name="settings-you-can-modify"></a>Änderbare Einstellungen

In der folgenden Tabelle sind die Konfigurationseinstellungen zusammengefasst, die Sie bearbeiten können.

**Ressource** | **Optionen** 
--- | --- | --- 
**VM-Name** | Optionen:<br/><br/> – Erstellen einer neuen VM mit demselben Namen in der Zielregion<br/><br/> – Erstellen einer neuen VM mit einem anderen Namen in der Zielregion<br/><br/> – Auswählen einer vorhandenen VM in der Zielregion<br/><br/> Wenn Sie eine neue VM erstellen, werden der neuen Ziel-VM bis auf die von Ihnen geänderten Einstellungen dieselben Einstellungen wie der Quell-VM zugewiesen.
**VM-Verfügbarkeitszone** | Die Verfügbarkeitszone, in der die Ziel-VM platziert wird. Diese kann mit **Nicht zutreffend** gekennzeichnet werden, wenn Sie die Quelleinstellungen nicht ändern oder die VM in keiner Verfügbarkeitszone platzieren möchten.
**VM-SKU** | Der [VM-Typ](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (verfügbar in der Zielregion), der für die Ziel-VM verwendet wird.<br/><br/> Die ausgewählte Ziel-VM sollte nicht kleiner sein als die Quell-VM.
**Netzwerkressourcen** | Optionen für virtuelle Netzwerke (Virtual Networks, VNETs)/Netzwerksicherheitsgruppen/Netzwerkschnittstellen:<br/><br/> – Erstellen einer neuen Ressource mit demselben Namen in der Zielregion<br/><br/> – Erstellen einer neuen Ressource mit einem anderen Namen in der Zielregion<br/><br/> – Verwenden einer vorhandene Netzwerkressource in der Zielregion<br/><br/> Wenn Sie eine neue Zielressource erstellen, werden dieser bis auf die von Ihnen geänderten Einstellungen dieselben Einstellungen wie der Quellressource zugewiesen.
**Name der öffentlichen IP-Adresse** | Geben Sie den Namen an.
**SKU der öffentlichen IP-Adresse** | Geben Sie die [SKU](../virtual-network/public-ip-addresses.md#sku) an.
**Öffentliche IP-Adresszone** | Geben Sie die [Zone](../virtual-network/public-ip-addresses.md#standard) für die öffentliche Standard-IP-Adressen an.<br/><br/> Wenn die Zone redundant sein soll, geben Sie **Zonenredundant** ein.
**Name des Lastenausgleichs** | Geben Sie den Namen an.
**SKU des Lastenausgleichs** | „Basic“ oder „Standard“. Es wird empfohlen, „Standard“ zu verwenden.
**Zone des Lastenausgleichs** | Geben Sie eine Zone für den Lastenausgleich an. <br/><br/> Wenn die Zone redundant sein soll, geben Sie **Zonenredundant** ein.
**Ressourcenabhängigkeiten** | Optionen für die einzelnen Abhängigkeiten:<br/><br/>– Die Ressource verwendet quellenabhängige Ressourcen, die in die Zielregion verschoben werden.<br/><br/> – Die Ressource verwendet andere abhängige Ressourcen, die sich in der Zielregion befinden. In diesem Fall können Sie aus beliebigen ähnlichen Ressourcen in der Zielregion auswählen.

### <a name="edit-vm-target-settings"></a>Ändern der VM-Zieleinstellungen

Wenn Sie nicht möchten, dass Ressourcen aus der Quellregion vom Ziel abhängen, haben Sie folgende Optionen:

- Erstellen einer neuen Ressource in der Zielregion. Die neue Ressource verfügt über dieselben Einstellungen wie die Quellressource, es sei denn, Sie geben andere Einstellungen an.
- Verwenden einer vorhandenen Ressource in der Zielregion

Das genaue Verhalten hängt vom Ressourcentyp ab. [Weitere Informationen](modify-target-settings.md) zur Bearbeitung von Zieleinstellungen

Sie können die Zieleinstellungen für eine Ressource mithilfe des Eintrags **Target configuration** (Zielkonfiguration) in der Sammlung für die Ressourcenverschiebung ändern. 

So ändern Sie einen Filter 

1. Klicken Sie auf der Seite **Across regions** (Regionsübergreifend) in der Spalte **Target configuration** (Zielkonfiguration) auf den Link für den Ressourceneintrag.
2. Unter **Konfigurationseinstellungen** können Sie eine neue VM in der Zielregion erstellen.
3. Weisen Sie der Ziel-VM eine neue Verfügbarkeitszone, Verfügbarkeitsgruppe oder SKU zu. **Verfügbarkeitszone** und **SKU**.

Änderungen werden an der Ressource vorgenommen, die Sie bearbeiten. Alle abhängigen Ressourcen müssen separat aktualisiert werden.


## <a name="modify-sql-settings"></a>Ändern der SQL-Einstellungen

Wenn Sie Azure SQL-Datenbank-Ressourcen verschieben, können Sie die Zieleinstellungen für die Verschiebung ändern. 

- Für SQL-Datenbank:
    - wird empfohlen, die Zielkonfigurationseinstellungen vor der Vorbereitung der Verschiebung zu ändern
    - können Sie die Einstellungen für die Zieldatenbank sowie Zonenredundanz der Datenbank ändern
- Bei Pools für elastische Datenbanken:
    -  können Sie die Zielkonfiguration jederzeit ändern, bevor die Verschiebung eingeleitet wird
    - können Sie den Zielpool für elastische Datenbanken sowie die Zonenredundanz des Pool ändern 

### <a name="sql-settings-you-can-modify"></a>Änderbare SQL-Einstellungen

**Einstellung** | **SQL-Datenbank** | **Pool für elastische Datenbanken**
--- | --- | ---
**Name** | Erstellen einer neuen Datenbank mit demselben Namen in der Zielregion<br/><br/> Erstellen einer neuen Datenbank mit einem anderen Namen in der Zielregion<br/><br/> Verwenden einer vorhandenen Datenbank in der Zielregion | Erstellen eines neuen Pools für elastische Datenbanken mit demselben Namen in der Zielregion<br/><br/> Erstellen eines neuen Pools für elastische Datenbanken mit einem anderen Namen in der Zielregion<br/><br/> Verwenden eines vorhandenen Pools für elastische Datenbanken in der Zielregion
**Zonenredundanz** | Wenn Sie Ressourcen von einer Region, die Zonenredundanz unterstützt, in eine Region verschieben möchten, die keine Zonenredundanz unterstützt, geben Sie **Deaktivieren** in der Zoneneinstellung ein.<br/><br/> Wenn Sie Ressourcen von einer Region, die keine Zonenredundanz unterstützt, in eine Region verschieben möchten, die Zonenredundanz unterstützt, geben Sie **Aktivieren** in der Zoneneinstellung ein. | Wenn Sie Ressourcen von einer Region, die Zonenredundanz unterstützt, in eine Region verschieben möchten, die keine Zonenredundanz unterstützt, geben Sie **Deaktivieren** in der Zoneneinstellung ein.<br/><br/> Wenn Sie Ressourcen von einer Region, die keine Zonenredundanz unterstützt, in eine Region verschieben möchten, die Zonenredundanz unterstützt, geben Sie **Aktivieren** in der Zoneneinstellung ein.

### <a name="edit-sql-target-settings"></a>Ändern der SQL-Zieleinstellungen

Die Zieleinstellungen für eine Azure SQL-Datenbank-Ressource werden wie folgt geändert: 

1. Klicken Sie unter **Across regions** (Regionsübergreifend) für die Ressource, die Sie ändern möchten, auf den Eintrag **Target configuration** (Zielkonfiguration).
2. Geben Sie unter **Konfigurationseinstellungen** die in der obigen Tabelle zusammengefassten Zieleinstellungen an.

## <a name="next-steps"></a>Nächste Schritte

[Verschieben Sie eine Azure-VM](tutorial-move-region-virtual-machines.md) in eine andere Region.