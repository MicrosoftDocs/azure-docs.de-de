---
title: Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie im Azure-Portal einen verwalteten Datenträger an einen virtuellen Windows-Computer anfügen.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e3641960131d23bf5a8e5b2310a09e7a4dbd70b9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64680396"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal einen neuen verwalteten Datenträger an einen virtuellen Windows-Computer (Virtual Machine, VM) anfügen. Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](sizes.md).


## <a name="add-a-data-disk"></a>Hinzufügen eines Datenträgers

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite die Option **Virtuelle Computer** aus.
2. Wählen Sie in der Liste einen virtuellen Computer aus.
3. Wählen Sie auf der Seite **Virtueller Computer** die Option **Datenträger** aus.
4. Wählen Sie auf der Seite **Datenträger** die Option **Datenträger hinzufügen** aus.
5. Wählen Sie in der Dropdownliste für den neuen Datenträger **Datenträger erstellen** aus.
6. Geben Sie auf der Seite **Verwalteten Datenträger erstellen** einen Namen für den Datenträger ein, und passen Sie die anderen Einstellungen nach Bedarf an. Wählen Sie **Erstellen**, wenn Sie fertig sind.
7. Wählen Sie auf der Seite **Datenträger** die Option **Speichern** aus, um die neue Datenträgerkonfiguration für den virtuellen Computer zu speichern.
8. Nachdem der Datenträger von Azure erstellt und an den virtuellen Computer angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger** angezeigt.


## <a name="initialize-a-new-data-disk"></a>Initialisieren eines neuen Datenträgers

1. Stellen Sie eine Verbindung zur VM her.
1. Wählen Sie auf dem ausgeführten virtuellen Computer das **Windows-Startmenü** aus, und geben Sie im Suchfeld den Namen **diskmgmt.msc** ein. Die **Datenträgerverwaltungskonsole** wird geöffnet.
2. Die Datenträgerverwaltung erkennt, dass ein neuer nicht initialisierter Datenträger vorhanden ist, und deshalb wird das Fenster **Datenträger initialisieren** angezeigt.
3. Vergewissern Sie sich, dass der neue Datenträger ausgewählt ist, und wählen Sie dann **OK** aus, um ihn zu initialisieren.
4. Der neue Datenträger wird als **Nicht zugeordnet** angezeigt. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen **Neues einfaches Volume** aus. Das Fenster **Assistent zum Erstellen neuer einfacher Volumes** wird geöffnet.
5. Führen Sie die Schritte des Assistenten aus, und übernehmen Sie dabei alle Standardeinstellungen. Wählen Sie abschließend **Fertig stellen** aus.
6. Schließen Sie die **Datenträgerverwaltung**.
7. Ein Popupfenster wird angezeigt, in dem Sie darüber informiert werden, dass Sie den neuen Datenträger formatieren müssen, bevor Sie ihn verwenden können. Wählen Sie **Datenträger formatieren** aus.
8. Überprüfen Sie im Fenster **Neuen Datenträger formatieren** die Einstellungen, und wählen Sie dann **Starten** aus.
9. Es wird eine Warnung angezeigt, die Sie darüber informiert, dass bei der Formatierung des Datenträgers alle Daten gelöscht werden. Klicken Sie auf **OK**.
10. Wählen Sie nach Abschluss der Formatierung die Option **OK** aus.

## <a name="next-steps"></a>Nächste Schritte

- Sie können auch [einen Datenträger mithilfe von PowerShell anfügen](attach-disk-ps.md).
- Wenn Ihre Anwendung Laufwerk *D:* für die Datenspeicherung verwenden muss, können Sie [den Laufwerkbuchstaben des temporären Windows-Datenträgers ändern](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
