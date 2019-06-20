---
title: Erstellen einer Windows-VM auf Grundlage einer speziellen VHD im Azure-Portal | Microsoft-Dokumentation
description: Erstellen einer neuen Windows-VM aus einer VHD im Azure-Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: b24435501ed1312e91ebec9b9c434971dbc94b55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61402303"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Erstellen eines virtuellen Computers anhand einer VHD mithilfe des Azure-Portals

Es gibt mehrere Möglichkeiten zum Erstellen eines virtuellen Computers (Virtual Machine, VM) in Azure: 

- Wenn Sie bereits über eine verwendbare virtuelle Festplatte (Virtual Hard Disk, VHD) verfügen oder die VHD von einem vorhandenen virtuellen Computer für die Verwendung kopieren möchten, können Sie einen neuen VM erstellen, indem Sie die VHD als Betriebssystemdatenträger an den neuen VM *anfügen*. 

- Sie können einen neuen virtuellen Computer anhand der VHD eines virtuellen Computers erstellen, der gelöscht wurde. Wenn Sie beispielsweise über einen virtuellen Azure-Computer verfügen, der nicht ordnungsgemäß funktioniert, können Sie den VM löschen und dessen VHD verwenden, um einen neuen VM zu erstellen. Sie können entweder die gleiche VHD wiederverwenden oder eine Kopie der VHD erstellen, indem Sie eine Momentaufnahme und dann anhand dieser einen neuen verwalteten Datenträger erstellen. Beim Erstellen einer Momentaufnahme müssen zwar einige weitere Schritte ausgeführt werden, aber dafür bleibt die ursprüngliche VHD erhalten, und Sie können auf ein Fallback zurückgreifen.

- Verwenden Sie auf einem klassischen virtuellen Computer die VHD, um einen neuen virtuellen Computer zu erstellen, der das Resource Manager-Bereitstellungsmodell und verwaltete Datenträger nutzt. **Beenden** Sie zum Erzielen optimaler Ergebnisse den klassischen virtuellen Computer im Azure-Portal, bevor Sie die Momentaufnahme erstellen.
 
- Sie können einen virtuellen Azure-Computer anhand einer lokalen VHD erstellen, indem Sie die lokale VHD hochladen und an einen neuen virtuellen Computer anfügen. Verwenden Sie PowerShell oder ein anderes Tool, um die VHD in ein Speicherkonto hochzuladen, und erstellen Sie dann einen verwalteten Datenträger anhand der VHD. Weitere Informationen finden Sie unter [Hochladen einer speziellen VHD](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Verwenden Sie keinen speziellen Datenträger, wenn Sie mehrere VMs erstellen möchten. Für größere Bereitstellungen sollten Sie stattdessen [ein Image erstellen](capture-image-resource.md) und dann [dieses Image zum Erstellen mehrerer VMs verwenden](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Kopieren eines Datenträgers

Erstellen Sie eine Momentaufnahme, und erstellen Sie dann einen Datenträger aus der Momentaufnahme. Bei dieser Strategie können Sie die ursprüngliche VHD als Fallback behalten:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü die Option **Alle Dienste** aus.
2. Geben Sie im Suchfeld **Alle Dienste** den Begriff **Datenträger** ein, und wählen Sie dann **Datenträger** aus, um die Liste der verfügbaren Datenträger anzuzeigen.
3. Wählen Sie den Datenträger aus, den Sie verwenden möchten. Die Seite **Datenträger** für diesen Datenträger wird angezeigt.
4. Wählen Sie im oberen Menü die Option **Momentaufnahme erstellen** aus. 
5. Geben Sie einen **Namen** für die Momentaufnahme ein.
6. Wählen Sie eine **Ressourcengruppe** für die Momentaufnahme aus. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen.
7. Wählen Sie für **Kontotyp** entweder **Standard (HDD)** oder **Premium (SSD)** aus.
8. Wählen Sie abschließend **Erstellen** aus, um die Momentaufnahme zu erstellen.
9. Nachdem die Momentaufnahme erstellt wurde, wählen Sie im linken Menü die Option **Ressource erstellen** aus.
10. Geben Sie im Suchfeld den Begriff **verwalteter Datenträger** ein, und wählen Sie dann in der Liste den Eintrag **Verwaltete Datenträger** aus.
11. Wählen Sie auf der Seite **Verwaltete Datenträger** die Option **Erstellen** aus.
12. Geben Sie einen **Namen** für den Datenträger ein.
13. Wählen Sie eine **Ressourcengruppe** für den Datenträger aus. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Diese Auswahl wird auch als Ressourcengruppe verwendet, in der Sie den virtuellen Computer anhand des Datenträgers erstellen.
14. Wählen Sie für **Kontotyp** entweder **Standard (HDD)** oder **Premium (SSD)** aus.
15. Achten Sie unter **Quelltyp** darauf, dass **Momentaufnahme** ausgewählt ist.
16. Wählen Sie im Dropdownfeld **Quellmomentaufnahme** die zu verwendende Momentaufnahme aus.
17. Nehmen Sie alle weiteren Anpassungen nach Bedarf vor, und wählen Sie dann **Erstellen** aus, um den Datenträger zu erstellen.

## <a name="create-a-vm-from-a-disk"></a>Erstellen einer VM aus einem Datenträger

Wenn Sie über die verwaltete Datenträger-VHD verfügen, die Sie verwenden möchten, können Sie den virtuellen Computer im Portal erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Menü die Option **Alle Dienste** aus.
2. Geben Sie im Suchfeld **Alle Dienste** den Begriff **Datenträger** ein, und wählen Sie dann **Datenträger** aus, um die Liste der verfügbaren Datenträger anzuzeigen.
3. Wählen Sie den Datenträger aus, den Sie verwenden möchten. Die Seite **Datenträger** für diesen Datenträger wird geöffnet.
4. Vergewissern Sie sich auf der Seite **Übersicht**, dass **DATENTRÄGERZUSTAND** als **Nicht angefügt** aufgelistet wird. Wenn dies nicht der Fall ist, müssen Sie möglicherweise entweder den Datenträger von der VM trennen oder die VM löschen, um den Datenträger freizugeben.
4. Wählen Sie im Menü oben auf der Seite die Option **VM erstellen** aus.
5. Geben Sie auf der Seite **Grundlagen** für den neuen VM einen **Namen des virtuellen Computers** ein, und wählen Sie entweder eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue.
6. Wählen Sie für **Größe** die Option **Größe ändern** aus, um auf die Seite **Größe** zuzugreifen.
7. Wählen Sie eine Zeile der VM-Größe aus, und wählen Sie dann **Auswählen** aus.
8. Auf der Seite **Netzwerk** können Sie entweder alle neuen Ressourcen vom Portal erstellen lassen oder ein vorhandenes **Virtuelles Netzwerk** und eine **Netzwerksicherheitsgruppe** auswählen. Das Portal erstellt immer eine neue Netzwerkschnittstelle und eine öffentliche IP-Adresse für den neuen virtuellen Computer. 
9. Nehmen Sie auf der Seite **Verwaltung** die gewünschten Änderungen an den Überwachungsoptionen vor.
10. Fügen Sie auf der Seite **Gastkonfiguration** nach Bedarf Erweiterungen hinzu.
11. Wählen Sie abschließend **Überprüfen + erstellen** aus. 
12. Wählen Sie bei erfolgreicher Überprüfung der VM-Konfiguration die Option **Erstellen** aus, um die Bereitstellung zu starten.

## <a name="next-steps"></a>Nächste Schritte

Sie können ebenfalls PowerShell verwenden, um [eine VHD in Azure hochzuladen und eine spezielle VM zu erstellen](create-vm-specialized.md).


