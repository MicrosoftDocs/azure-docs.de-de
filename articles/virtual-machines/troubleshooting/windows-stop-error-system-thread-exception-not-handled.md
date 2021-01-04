---
title: 'Windows: kritischer Fehler 0x0000007E – Systemthreadausnahme nicht behandelt'
description: In diesem Artikel finden Sie Schritte zum Beheben von Problemen, bei denen im Gastbetriebssystem ein Problem auftritt und daher ein Neustart Ihrer Azure-VM angefordert wird. Die Meldung enthält die Information, dass eine „Systemthreadausnahme nicht behandelt“ wurde.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681889"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows: kritischer Fehler 0x0000007E – Systemthreadausnahme nicht behandelt

In diesem Artikel finden Sie Schritte zum Beheben von Problemen, bei denen im Gastbetriebssystem (Gast-BS) ein Problem auftritt und daher ein Neustart Ihrer Azure-VM versucht wird. Die angezeigte Fehlermeldung enthält die Information, dass eine Systemthreadausnahme nicht behandelt wurde.

## <a name="symptoms"></a>Symptome

Wenn Sie die [Startdiagnose](./boot-diagnostics.md) verwenden, um einen Screenshots der VM-Ausgabe anzuzeigen, sehen Sie, dass Windows neu gestartet werden muss, entweder mit dem Stillstandcode „SYSTEMTHREADAUSNAHME NICHT BEHANDELT“ oder dem Fehlercode „0x0000007E“.

![Screenshot, der zeigt, dass Windows während des Starts mit der Meldung „Auf dem PC ist ein Problem aufgetreten. Er muss neu gestartet werden. Wir führen den Neustart für Sie durch.“ und dem Stillstandcode „SYSTEMTHREADAUSNAHME NICHT BEHANDELT“ hängen bleibt](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Ursache

Die Ursache kann erst bestimmt werden, wenn eine Speicherabbilddatei analysiert wird. Fahren Sie mit dem Erfassen der Speicherabbilddatei fort.

## <a name="solution"></a>Lösung

Um dieses Problem zu behandeln, müssen Sie zunächst die Speicherabbilddatei für den Absturz erstellen und die Datei dann an den Microsoft-Support senden. Zum Erstellen der Speicherabbilddatei für den Absturz befolgen Sie die Anweisungen in den nächsten beiden Abschnitten.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Anfügen des Betriebssystemdatenträgers an eine neue Reparatur-VM

1. Führen Sie die [Schritte 1 bis 3 der VM-Reparaturbefehle](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) aus, um eine Reparatur-VM vorzubereiten.
1. Stellen Sie über eine Remotedesktopverbindung eine Verbindung mit der Reparatur-VM her.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Suchen nach der Speicherabbilddatei und Senden eines Supporttickets

1. Navigieren Sie auf der Reparatur-VM zum Windows-Ordner auf dem angefügten Betriebssystemdatenträger. Wenn dem angefügten Betriebssystemdatenträger beispielsweise der Laufwerkbuchstabe *F* zugeordnet ist, rufen Sie `F:\Windows` auf.
1. Suchen Sie nach der Datei *memory.dmp*, und [senden Sie dann ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) mit der Speicherabbilddatei als Anhang.
1. Wenn Sie Probleme beim Auffinden der Datei *memory.dmp* haben, befolgen Sie die Anweisungen im Leitfaden zum [Generieren einer Absturzabbilddatei mithilfe von NMI-Aufrufen (nicht maskierbarer Interrupt)](/windows/client-management/generate-kernel-or-complete-crash-dump).

Weitere Informationen zu NMI-Aufrufen finden Sie im Benutzerhandbuch zu [NMI-Aufrufen in der seriellen Azure-Konsole](./serial-console-windows.md#use-the-serial-console-for-nmi-calls).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Problembehandlung bei Startfehlern von Azure-VMs](./boot-error-troubleshoot.md)
