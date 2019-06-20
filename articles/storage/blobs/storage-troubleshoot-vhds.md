---
title: Problembehandlung bei an virtuelle Azure-Computer angeschlossenen Datenträgern | Microsoft-Dokumentation
description: Enthält Links zu Problembehandlungsressourcen für virtuelle Festplatten (VHDs) virtueller Azure-Computer.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: c00fdf3ad02edc1faf0d3257d3836a1c2f44d682
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65150772"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Problembehandlung bei an virtuelle Azure-Computer angeschlossenen Datenträgern 

Virtuelle Azure-Computer (VMs) nutzen virtuelle Festplatten (VHDs) als Betriebssystemdatenträger und angefügte Datenträger. VHDs werden als Seitenblobs in einem oder mehreren Azure Storage-Konten gespeichert. Dieser Artikel verweist auf Inhalte zur Problembehandlung bei allgemeinen Problemen, die bei VHDs auftreten können. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Problembehandlung bei Speicherlöschfehlern für eine VM

In bestimmten Fällen kann ein Fehler beim Löschen einer Speicherressource auftreten, wenn ein virtueller Computer in einer Resource Manager-Bereitstellung angefügte VHDs enthält. Hilfe zum Beheben dieses Problems finden Sie in den folgenden Artikeln: 

  * Auf Linux-VMs: [Speicherbezogene Fehler beim Löschen in der Resource Manager-Bereitstellung](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Auf Windows-VMs: [Speicherbezogene Fehler beim Löschen in der Resource Manager-Bereitstellung](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Problembehandlung bei unerwarteten Neustarts von virtuellen Computern mit angefügten VHDs

Wenn unerwartete Neustarts eines virtuellen Computers mit einer großen Anzahl angefügter VHDs auftreten, lesen Sie folgende Artikel:

  * Auf Linux-VMs: [Unerwartete Neustarts von virtuellen Computern mit angefügten VHDs](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Auf Windows-VMs: [Unerwartete Neustarts von virtuellen Computern mit angefügten VHDs](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
