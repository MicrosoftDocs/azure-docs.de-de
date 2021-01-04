---
title: 'Verwalten von Paketerfassungen: Azure-Portal'
titleSuffix: Azure Network Watcher
description: Erfahren Sie, wie das Network Watcher-Feature zur Paketerfassung über das Azure-Portal verwaltet wird.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 099ead37b8d6b46b767ef59ff24a7e7ff9dc9e3c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966444"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Verwalten von Paketerfassungen mit Azure Network Watcher über das Portal

Mithilfe der Paketerfassung von Network Watcher können Sie Sitzungen erfassen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Für die Erfassungssitzung werden Filter bereitgestellt, um sicherzustellen, dass nur der gewünschte Datenverkehr erfasst wird. Mithilfe der Paketerfassung können Sie Netzwerkanomalien sowohl reaktiv als auch proaktiv diagnostizieren. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und vieles mehr. Durch die Möglichkeit zur Remoteauslösung von Paketerfassungen wird die manuelle Ausführung einer Paketerfassung auf einem gewünschten virtuellen Computer erleichtert. So lässt sich wertvolle Zeit sparen.

In diesem Artikel wird beschrieben, wie Sie eine Paketerfassung starten, beenden, herunterladen und löschen. 

## <a name="before-you-begin"></a>Voraussetzungen

Für Paketerfassung sind folgende ausgehende TCP-Verbindungen erforderlich:
- mit dem ausgewählten Speicherkonto über Port 443
- mit 169.254.169.254 über Port 80
- mit 168.63.129.16 über Port 8037

> [!NOTE]
> Die in den beiden letztgenannten Fällen erwähnten Ports sind allen Network Watcher-Funktionen, die die Network Watcher-Erweiterung beinhalten, gemeinsam und können sich gelegentlich ändern.


Wenn eine Netzwerksicherheitsgruppe der Netzwerkschnittstelle oder einem Subnetz, in dem sich die Netzwerkschnittstelle befindet, zugeordnet ist, stellen Sie sicher, dass Regeln definiert sind, die die oben genannten Ports zulassen. Auf ähnliche Weise kann das Hinzufügen von benutzerdefinierten Datenverkehrsrouten zu Ihrem Netzwerk Verbindungen mit den oben erwähnten IP-Adressen und Ports verhindern. Stellen Sie sicher, dass diese erreichbar sind. 

## <a name="start-a-packet-capture"></a>Starten einer Paketerfassung

1. Browsen Sie zum [Azure-Portal](https://portal.azure.com), und wählen Sie **Alle Dienste** und dann **Network Watcher** im Abschnitt **Netzwerk** aus.
2. Wählen Sie unter **Netzwerkdiagnosetools** die Option **Paketerfassung** aus. Alle vorhandenen Paketerfassungen werden unabhängig von ihrem Status aufgelistet.
3. Wählen Sie **Hinzufügen** aus, um eine Paketerfassung zu erstellen. Sie können Werte für die folgenden Eigenschaften auswählen:
   - **Abonnement**: Das Abonnement, dem der virtuelle Computer zugeordnet ist, für den Sie die Paketerfassung erstellen möchten.
   - **Ressourcengruppe**: Die Ressourcengruppe des virtuellen Computers.
   - **Virtueller Zielcomputer**: Der virtuelle Computer, für den die Paketerfassung erstellt werden soll.
   - **Paketerfassungsname**: Ein Name für die Paketerfassung.
   - **Speicherkonto oder Datei**: Wählen Sie **Speicherkonto**, **Datei** oder beides aus. Wenn Sie **Datei** auswählen, wird die Erfassung in einen Pfad innerhalb des virtuellen Computers geschrieben.
   - **Lokaler Dateipfad**: Der lokale Pfad auf dem virtuellen Computer zum Speicherort für die Paketerfassung (nur gültig, wenn *Datei* ausgewählt ist). Der Pfad muss ein gültiger Pfad sein. Bei einem virtuellen Linux-Computer muss der Pfad mit */var/captures* beginnen.
   - **Speicherkonten**: Wählen Sie ein vorhandenes Speicherkonto aus, wenn Sie zuvor *Speicherkonto* ausgewählt haben. Diese Option ist nur verfügbar, wenn Sie **Speicher** ausgewählt haben.
   
     > [!NOTE]
     > Für Storage Premium-Konten wird das Speichern von Paketerfassungen derzeit nicht unterstützt.

   - **Maximale Anzahl von Bytes pro Paket**: Die Anzahl der Bytes aus jedem Paket, die erfasst werden. Wenn keine Angabe erfolgt, werden alle Bytes erfasst.
   - **Maximale Anzahl von Bytes pro Sitzung**: Die Gesamtzahl der erfassten Bytes. Wenn dieser Wert erreicht wird, wird die Paketerfassung beendet.
   - **Zeitlimit (Sekunden)** : Das Zeitlimit, nach dem die Paketerfassung beendet wird. Der Standardwert ist 18.000 Sekunden.
   - Filterung (optional). Wählen Sie **+ Filter hinzufügen** aus.
     - **Protokoll:** Das zu filternde Protokoll für die Paketerfassung. Die verfügbaren Werte sind „TCP“, „UDP“ und „Alle“.
     - **Lokale IP-Adresse**: Filtert die Paketerfassung für Pakete, deren lokale IP-Adresse mit diesem Wert übereinstimmt.
     - **Lokaler Port**: Filtert die Paketerfassung nach Paketen, deren lokaler Port mit diesem Wert übereinstimmt.
     - **Remote-IP-Adresse**: Filtert die Paketerfassung nach Paketen, deren Remote-IP-Adresse mit diesem Wert übereinstimmt.
     - **Remoteport**: Filtert die Paketerfassung nach Paketen, deren Remoteport mit diesem Wert übereinstimmt.
    
     > [!NOTE]
     > Bei den Werten für Port und IP-Adresse kann es sich um einen Einzelwert, einen Bereich von Werten oder einen Bereich handeln, z.B. 80–1024 für den Port. Sie können beliebig viele Filter definieren.

4. Klicken Sie auf **OK**.

Nachdem das für die Paketerfassung festgelegte Zeitlimit abgelaufen ist, wird die Paketerfassung beendet und kann überprüft werden. Sie können eine Paketerfassungssitzung auch manuell beenden.

> [!NOTE]
> Im Portal werden folgende Vorgänge automatisch durchgeführt:
>  * Erstellen einer Network Watcher-Instanz in der Region, in der sich der ausgewählte virtuelle Computer befindet, sofern in der Region noch keine Network Watcher-Instanz vorhanden ist.
>  * Hinzufügen der Erweiterung *AzureNetworkWatcherExtension* für virtuelle [Linux](../virtual-machines/extensions/network-watcher-linux.md)- oder [Windows](../virtual-machines/extensions/network-watcher-windows.md)-Computer, sofern sie noch nicht installiert ist.

## <a name="delete-a-packet-capture"></a>Löschen einer Paketerfassung

1. Wählen Sie in der Paketerfassungsansicht rechts neben der Paketerfassung die Option **...** aus, oder klicken Sie mit der rechten Maustaste auf eine vorhandene Paketerfassung, und wählen Sie **Löschen** aus.
2. Sie werden aufgefordert, den Löschvorgang für die Paketerfassung zu bestätigen. Wählen Sie **Ja** aus.

> [!NOTE]
> Durch das Löschen einer Paketerfassung wird die Erfassungsdatei im Speicherkonto oder auf dem virtuellen Computer nicht gelöscht.

## <a name="stop-a-packet-capture"></a>Beenden einer Paketerfassung

Wählen Sie in der Paketerfassungsansicht rechts neben der Paketerfassung die Option **...** aus, oder klicken Sie mit der rechten Maustaste auf eine vorhandene Paketerfassung, und wählen Sie **Beenden** aus.

## <a name="download-a-packet-capture"></a>Herunterladen einer Paketerfassung

Nachdem die Paketerfassungssitzung abgeschlossen wurde, wird die Erfassungsdatei in den Blobspeicher oder in eine lokale Datei auf dem virtuellen Computer hochgeladen. Der Speicherort der Paketerfassung wird beim Erstellen der Paketerfassung definiert. Ein nützliches Tool für den Zugriff auf die in einem Speicherkonto gespeicherten Erfassungsdateien ist der Microsoft Azure Storage-Explorer, der [hier](https://storageexplorer.com/) heruntergeladen werden kann.

Wenn ein Speicherkonto angegeben wird, werden Paketerfassungsdateien in einem Speicherkonto am folgenden Speicherort gespeichert:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Wenn Sie bei der Erstellung der Erfassung die Option **Datei** ausgewählt haben, können Sie die Datei über den auf dem virtuellen Computer konfigurierten Pfad anzeigen oder herunterladen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Automatisierung von Paketerfassungen mit VM-Warnungen finden Sie unter [Erstellen einer durch Warnungen ausgelösten Paketerfassung](network-watcher-alert-triggered-packet-capture.md).
- Informationen zum Bestimmen, ob bestimmter eingehender oder ausgehender Datenverkehr für einen virtuellen Computer zulässig ist, finden Sie unter [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers](diagnose-vm-network-traffic-filtering-problem.md).