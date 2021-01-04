---
title: 'Einrichten von Laufwerk „D:“ eines virtuellen Computers als Datenträger '
description: 'Es wird beschrieben, wie Sie Laufwerkbuchstaben für eine Windows-VM ändern, um das Laufwerk D: als Datenlaufwerk zu verwenden.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cae29954211e62601debb35d76f938fb6a92779e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019802"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Verwenden des Laufwerks „D:“ als Datenlaufwerk auf einem virtuellen Windows-Computer
Wenn Ihre Anwendung Laufwerk D zum Speichern der Daten verwenden muss, können Sie diese Anweisungen befolgen, um für den temporären Datenträger einen anderen Laufwerkbuchstaben zu verwenden. Verwenden Sie niemals den temporären Datenträger zum Speichern von Daten, die Sie behalten müssen.

Wenn Sie die Größe eines virtuellen Computers ändern oder diesen **beenden (Zuordnung aufheben)** , kann dies das Platzieren des virtuellen Computers auf einem neuen Hypervisor auslösen. Ein geplantes oder ungeplantes Wartungsereignis kann ebenfalls diese Neuanordnung auslösen. In diesem Szenario wird dem temporären Datenträger der erste verfügbare Laufwerkbuchstabe zugewiesen. Wenn Sie eine Anwendung nutzen, für die speziell das Laufwerk D: erforderlich ist, müssen Sie diese Schritte ausführen. Sie verschieben die Datei „pagefile.sys“ vorübergehend, fügen einen neuen Datenträger an und weisen ihm den Buchstaben D zu und verschieben die Datei „pagefile.sys“ dann wieder zurück auf das temporäre Laufwerk. Nachdem der Vorgang abgeschlossen ist, wird D: von Azure nicht zurückgenommen, wenn die VM auf einen anderen Hypervisor verschoben wird.

Weitere Informationen zur Verwendung des temporären Datenträgers in Azure finden Sie unter [Understanding the temporary drive on Microsoft Azure Virtual Machines](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>Anfügen des Datenträgers
Zunächst müssen Sie den Datenträger an den virtuellen Computer anfügen. Informationen zur Verwendung des Portals finden Sie unter [Anfügen eines verwalteten Datenträgers an eine Windows-VM im Azure-Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Temporäres Verschieben von „pagefile.sys“ zu Laufwerk C
1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. 
2. Klicken Sie mit der rechten Maustaste auf **Start**, und wählen Sie **System**.
3. Wählen Sie im linken Menü **Erweiterte Systemeinstellungen**.
4. Wählen Sie im Abschnitt **Leistung** die Option **Einstellungen**.
5. Wählen Sie die Registerkarte **Erweitert** .
6. Wählen Sie im Abschnitt **Virtueller Arbeitsspeicher** die Option **Ändern**.
7. Wählen Sie das Laufwerk **C**, und klicken Sie dann auf **Größe wird vom System verwaltet** und **Festlegen**.
8. Wählen Sie das Laufwerk **D**, und klicken Sie dann auf **Keine Auslagerungsdatei** und **Festlegen**.
9. Klicken Sie auf „Übernehmen“. Sie erhalten eine Warnung, dass der Computer neu gestartet werden muss, damit die Änderungen wirksam muss werden.
10. Starten Sie den virtuellen Computer neu.

## <a name="change-the-drive-letters"></a>Ändern der Laufwerkbuchstaben
1. Sobald die VM neu gestartet wird, melden Sie sich wieder bei der VM an.
2. Klicken Sie auf **Start**, geben Sie **diskmgmt.msc** ein, und drücken Sie die EINGABETASTE. Die Datenträgerverwaltung wird gestartet.
3. Klicken Sie mit der rechten Maustaste auf **D**, das temporäre Speicherlaufwerk, und wählen Sie **Laufwerkbuchstaben und -pfade** ändern.
4. Wählen Sie als Laufwerkbuchstaben ein neues Laufwerk wie etwa **T** aus, und klicken Sie dann auf **OK**. 
5. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen Sie **Laufwerkbuchstaben und -pfade ändern**.
6. Wählen Sie **D** als Laufwerkbuchstaben, und klicken Sie dann auf **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Verschieben von „pagefile.sys“ zurück auf das temporäre Speicherlaufwerk
1. Klicken Sie mit der rechten Maustaste auf **Start**, und wählen Sie **System**.
2. Wählen Sie im linken Menü **Erweiterte Systemeinstellungen**.
3. Wählen Sie im Abschnitt **Leistung** die Option **Einstellungen**.
4. Wählen Sie die Registerkarte **Erweitert** .
5. Wählen Sie im Abschnitt **Virtueller Arbeitsspeicher** die Option **Ändern**.
6. Wählen Sie das Betriebssystemlaufwerk **C**, und klicken Sie dann auf **Keine Auslagerungsdatei** und **Festlegen**.
7. Wählen Sie das temporäre Speicherlaufwerk **T**, und klicken Sie dann auf **Größe wird vom System verwaltet** und **Festlegen**.
8. Klicken Sie auf **Anwenden**. Sie erhalten eine Warnung, dass der Computer neu gestartet werden muss, damit die Änderungen wirksam muss werden.
9. Starten Sie den virtuellen Computer neu.

## <a name="next-steps"></a>Nächste Schritte
* Sie können den für den virtuellen Computer verfügbaren Speicher erhöhen, indem Sie [einen zusätzlichen Datenträger anfügen](attach-managed-disk-portal.md).
