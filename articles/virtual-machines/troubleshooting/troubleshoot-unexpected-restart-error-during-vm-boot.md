---
title: 'Betriebssystemstart: Computer wurde unerwartet neu gestartet, oder es ist ein unerwarteter Fehler aufgetreten'
description: Dieser Artikel enthält Schritte zum Beheben von Problemen, wenn für den virtuellen Computer bei der Installation von Windows ein unerwarteter Neustart oder Fehler auftritt.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: cfeb040893ae2be5842959ed8458bd713bebe6ee
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512136"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Betriebssystemstart: Computer wurde unerwartet neu gestartet, oder es ist ein unerwarteter Fehler aufgetreten

Dieser Artikel enthält Schritte zum Beheben von Problemen, wenn für den virtuellen Computer (virtuelle Maschine, VM) bei der Installation von Windows ein unerwarteter Neustart oder Fehler auftritt.

## <a name="symptom"></a>Symptom

Wenn Sie die [Startdiagnose](./boot-diagnostics.md) verwenden, um den Screenshot des virtuellen Computers anzuzeigen, sehen Sie, dass im Screenshot eine fehlgeschlagene Windows-Installation mit dem folgenden Fehler angezeigt wird:

**Der Computer wurde unerwartet neu gestartet, oder ein unerwarteter Fehler ist aufgetreten. Die Windows-Installation kann nicht fortgesetzt werden. Klicken Sie auf "OK", um den Computer neu zu starten, und führen Sie die Installation dann erneut aus.**

![Fehler beim Ausführen der Windows-Installation: Der Computer wurde unerwartet neu gestartet, oder ein unerwarteter Fehler ist aufgetreten. Die Windows-Installation kann nicht fortgesetzt werden. Klicken Sie auf "OK", um den Computer neu zu starten, und führen Sie die Installation dann erneut aus.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Fehler, wenn das Setupprogramm bei der Windows-Installation Dienste startet: Der Computer wurde unerwartet neu gestartet, oder ein unerwarteter Fehler ist aufgetreten. Die Windows-Installation kann nicht fortgesetzt werden. Klicken Sie auf "OK", um den Computer neu zu starten, und führen Sie die Installation dann erneut aus.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Ursache

Der Computer versucht, einen Erststart eines [generalisierten Image](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) auszuführen, stößt aber auf Probleme, weil eine benutzerdefinierte Antwortdatei (Unattend.xml) verarbeitet wird. **Benutzerdefinierte Antwortdateien werden in Azure nicht unterstützt.** 

Die Antwortdatei ist eine spezielle XML-Datei, die Einstellungsdefinitionen und Werte für die Konfigurationseinstellungen enthält, die Sie während der Installation eines Windows Server-Betriebssystems automatisieren möchten. Zu den Konfigurationsoptionen gehören Anweisungen, wie Datenträger partitioniert werden sollen und wo das zu installierende Windows-Image, die anzuwendenden Produktschlüssel und weitere Befehle zu finden sind, die Sie ausführen möchten.

Benutzerdefinierte Antwortdateien werden wie bereits erwähnt in Azure nicht unterstützt. Dieser Fall tritt somit ein, wenn ein Image für die Verwendung in Azure vorbereitet wurde, Sie aber eine benutzerdefinierte Datei vom Typ „Unattend.xml“ angegeben und dazu **SYSPREP** mit einem Flag wie im folgenden Befehl verwendet haben:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

Verwenden Sie in Azure die Option **Out-of-Box-Experience (OOBE) für System aktivieren** auf der **grafischen Benutzeroberfläche des Tools für die Systemvorbereitung**, oder verwenden Sie `sysprep /oobe` (anstelle der Datei „Unattend.xml“).

Dieses Problem tritt in der Regel auf, wenn Sie „sysprep“ mit einem lokalen virtuellen Computer verwenden, um einen generalisierten virtuellen Computer in Azure hochzuladen. In dieser Situation möchten Sie möglicherweise auch wissen, wie ein generalisierter virtueller Computer ordnungsgemäß hochgeladen wird.

## <a name="solution"></a>Lösung

### <a name="do-not-use-unattendxml"></a>Verwenden Sie nicht „Unattend.xml“.

Um dieses Problem zu beheben, befolgen Sie [die Azure-Anleitung zum Vorbereiten/Erfassen eines Image](../windows/upload-generalized-managed.md), und bereiten Sie ein neues generalisiertes Image vor. Verwenden Sie beim Ausführen von „sysprep“ **nicht das Flag `/unattend:<your file’s name>`** . Verwenden Sie stattdessen nur die folgenden Flags:

`sysprep /oobe /generalize /shutdown`

- Out-of-Box-Experience (OOBE) ist die unterstützte Einstellung für Azure-VMs.

Sie können dieselbe Aufgabe wie im obigen Befehl auch ausführen, indem Sie in der grafischen Benutzeroberfläche von **System Preparation Tool** die nachstehend gezeigten Optionen auswählen:

- Enter System Out-of-Box-Experience
- Generalize
- Shutdown
 
![Fenster von „System Preparation Tool“ mit ausgewählten Optionen „OOBE“, „Generalize“ und „Shutdown“](./media/unexpected-restart-error-during-vm-boot/3.png)
