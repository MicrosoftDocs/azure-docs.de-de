---
title: Aktualisieren von Data Science Virtual Machine auf Ubuntu 18.04
titleSuffix: Azure Data Science Virtual Machine
description: Erfahren Sie, wie Sie ein Upgrade von CentOS und Ubuntu 16.04 auf die neueste Version Ubuntu 18.04 Data Science Virtual Machine durchführen.
keywords: Deep Learning, KI, Data Science-Tools, virtueller Data Science-Computer, Data Science-Teamprozess
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320977"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Aktualisieren von Data Science Virtual Machine auf Ubuntu 18.04

Wenn Sie eine Data Science Virtual Machine-Instanz mit einer älteren Version wie Ubuntu 16.04 oder CentOS ausführen, sollten Sie Ihre DSVM zu Ubuntu 18.04 migrieren. Durch die Migration wird sichergestellt, dass Sie die neuesten Betriebssystempatches, Treiber, vorinstallierten Software und Bibliotheksversionen erhalten. In diesem Dokument erfahren Sie, wie Sie von älteren Versionen von Ubuntu oder CentOS migrieren. 

## <a name="prerequisites"></a>Voraussetzungen

- Vertrautheit mit SSH und der Linux-Befehlszeile

## <a name="overview"></a>Übersicht

Die Migration kann auf zwei Arten erreicht werden:

- Direkte Migration, auch als Migration mit „gleichem Server“ bezeichnet. Bei dieser Migration wird die vorhandene VM aktualisiert, ohne dass ein neuer virtueller Computer erstellt wird. Direkte Migration ist die einfachere Möglichkeit, von Ubuntu 16.04 zu Ubuntu 18.04 zu migrieren.
- Parallele Migration, auch als „serverübergreifende“ Migration bezeichnet. Bei dieser Migration werden Daten vom vorhandenen virtuellen Computer in eine neu erstellte VM übertragen. Parallele Migration ermöglicht die Migration von CentOS zu Ubuntu 18.04. Vielleicht bevorzugen Sie eine parallele Migration für ein Upgrade zwischen Ubuntu-Versionen, wenn Sie das Gefühl haben, dass Ihre alte Installation unübersichtlich geworden ist.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Erstellen einer Momentaufnahme Ihrer VM für den Fall, dass ein Rollback ausgeführt werden muss

Verwenden Sie im Azure-Portal die Suchleiste, um nach der Funktionalität für **Momentaufnahmen** zu suchen. 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Screenshot des Azure-Portals und der Suchleiste mit hervorgehobener Option „Momentaufnahmen“":::

1. Wählen Sie **Hinzufügen** aus. Dadurch gelangen Sie zur Seite **Momentaufnahme erstellen**. Wählen Sie das Abonnement und die Ressourcengruppe des virtuellen Computers aus. Wählen Sie als **Region** die gleiche Region aus, in der der Zielspeicher vorhanden ist. Wählen Sie den DSVM-Speicherdatenträger und zusätzliche Sicherungsoptionen aus. **HDD Standard** ist ein geeigneter Speichertyp für dieses Sicherungsszenario.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Screenshot: Optionen „Momentaufnahme erstellen“":::

2. Nachdem alle Details ausgefüllt und die Überprüfungen bestanden wurden, wählen Sie **Überprüfen und erstellen** aus, um die Momentaufnahme zu validieren und zu erstellen. Nachdem die Momentaufnahme erfolgreich abgeschlossen wurde, wird eine Meldung mit dem Hinweis angezeigt, dass die Bereitstellung abgeschlossen ist.

## <a name="in-place-migration"></a>Direkte Migration

Wenn Sie eine ältere Version von Ubuntu migrieren, können Sie eine direkte Migration durchführen. Bei dieser Migration wird kein neuer virtueller Computer erstellt, und es sind weniger Schritte als bei einer parallelen Migration erforderlich.  Wenn Sie eine parallele Migration durchführen möchten, da Sie mehr Kontrolle wünschen oder weil Sie eine Migration von einer anderen Distribution durchführen möchten (z. B. CentOS), fahren Sie mit dem Abschnitt [Parallele Migration](#side-by-side-migration) fort. 

1. Starten Sie Ihre DSVM aus dem Azure-Portal, und melden Sie sich mit SSH an. Wählen Sie hierzu **Verbinden** und **SSH** aus, und befolgen Sie die Verbindungsanweisungen. 

1. Nachdem Sie eine Verbindung mit einer Terminalsitzung auf Ihrer DSVM hergestellt haben, führen Sie den folgenden Upgradebefehl aus:

    ```bash
    sudo do-release-upgrade
    ```

Der Upgradevorgang dauert eine Weile. Nachdem er abgeschlossen wurde, fordert das Programm Sie auf, den Neustart des virtuellen Computers zu bestätigen. Antworten Sie mit **Ja**. Sie werden von der SSH-Sitzung getrennt, während das System neu gestartet wird.

### <a name="if-necessary-regenerate-ssh-keys"></a>Neugenerieren von SSH-Schlüsseln bei Bedarf

> [!IMPORTANT] 
> Nach dem Upgrade und Neustart müssen Sie möglicherweise Ihre SSH-Schlüssel neu generieren.

Nachdem die VM aktualisiert und neu gestartet wurde, versuchen Sie erneut, über SSH darauf zuzugreifen. Die IP-Adresse wurde während des Neustarts möglicherweise geändert. Bestätigen Sie diese daher, bevor Sie versuchen, eine Verbindung herzustellen.

Wenn Sie die Fehlermeldung **REMOTE HOST IDENTIFICATION HAS CHANGED** (Identifizierung des Remotehosts hat sich geändert) erhalten, müssen Sie Ihre SSH-Anmeldeinformationen neu generieren.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="PowerShell-Screenshot: Warnung „Identifizierung des Remotehosts hat sich geändert“":::

Führen Sie zu diesem Zweck auf dem lokalen Computer den folgenden Befehl aus:

```bash
ssh-keygen -R "your server hostname or ip"
```

Es sollte nun möglich sein, eine Verbindung mit SSH herzustellen. Wenn weiterhin Probleme auftreten, folgen Sie auf der Seite **Verbinden** dem Link **Beheben von Problemen mit der SSH-Verbindung**.

## <a name="side-by-side-migration"></a>Parallele Migration

Wenn Sie von CentOS migrieren oder eine saubere Betriebssysteminstallation wünschen, können Sie eine parallele Migration ausführen. Bei dieser Art der Migration sind mehr Schritte erforderlich, aber Sie können genau steuern, welche Dateien übertragen werden.

Migrationen von anderen Systemen auf der Grundlage desselben Satzes von Upstreamquellpaketen sollten relativ unkompliziert sein, z. B. [FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Sie können die Betriebssystemkomponenten des Dateisystems aktualisieren und Benutzerverzeichnisse (z. B. `/home`) beibehalten. Wenn Sie die alten Benutzerbasisverzeichnisse beibehalten, sollten Sie einige Probleme mit den GNOME-/KDE-Menüs und anderen Desktopelementen erwarten. Möglicherweise ist es am einfachsten, neue Benutzerkonten zu erstellen und die alten Verzeichnisse an einer anderen Stelle im Dateisystem als Referenz einzubinden, zu kopieren oder Benutzerdateien nach der Migration zu verknüpfen.

### <a name="migration-at-a-glance"></a>Migration auf einen Blick

1.  Erstellen eine Momentaufnahme Ihrer vorhandenen VM wie zuvor beschrieben

1.  Erstellen eines Datenträgers aus dieser Momentaufnahme

1.  Erstellen einer neuen Data Science Virtual Machine-Instanz für Ubuntu

1.  Erneutes Erstellen von Benutzerkonten auf dem neuen virtuellen Computer

1.  Einbinden des Datenträgers des virtuellen Computers, von dem die Momentaufnahme erstellt wurde, als Datenträger in die neue Data Science Virtual Machine-Instanz

1.  Manuelles Kopieren der gewünschten Daten

### <a name="create-a-disk-from-your-vm-snapshot"></a>Erstellen eines Datenträgers aus der VM-Momentaufnahme

Wenn Sie noch keine VM-Momentaufnahme wie oben beschrieben erstellt haben, erstellen Sie diese Momentaufnahme jetzt. 

1. Suchen Sie im Azure-Portal nach **Datenträgern** , und wählen Sie **Hinzufügen** aus. Dadurch wird die Seite **Datenträger** geöffnet.

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Screenshot: Azure-Portal mit der Seite „Nach Datenträgern suchen“ und der Schaltfläche „Hinzufügen“":::

2. Legen Sie das **Abonnement** , die **Ressourcengruppe** und die **Region** auf die Werte Ihrer VM-Momentaufnahme fest. Wählen Sie einen **Namen** für den zu erstellenden Datenträger aus.

3. Wählen Sie **Quelltyp** als **Momentaufnahme** aus, und wählen Sie die VM-Momentaufnahme als **Quellmomentaufnahme** aus. Überprüfen und erstellen Sie den Datenträger. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Screenshot: Dialogfeld zum Erstellen von Datenträgern mit Optionen":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Erstellen einer neuen Data Science Virtual Machine-Instanz für Ubuntu

Erstellen Sie eine neue Data Science Virtual Machine-Instanz für Ubuntu mithilfe des [Azure-Portals](https://portal.azure.com) oder einer [ARM-Vorlage](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Neuerstellen von Benutzerkonten in der neuen Data Science Virtual Machine-Instanz

Da Sie nur Daten vom alten Computer kopieren, müssen Sie die Benutzerkonten und Softwareumgebungen, die Sie auf dem neuen Computer verwenden möchten, neu erstellen.

Linux ist flexibel genug, um es Ihnen zu ermöglichen, Verzeichnisse und Pfade Ihrer neuen Installation so anzupassen, dass sie Ihrem alten Computer entsprechen. Im Allgemeinen ist es jedoch einfacher, das bevorzugte Layout des modernen Ubuntu zu verwenden und die Benutzerumgebung und Skripts entsprechend zu ändern.

Weitere Informationen finden Sie unter [Quickstart: Einrichten von Data Science Virtual Machine für Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Einbinden des Datenträgers des virtuellen Computers, von dem die Momentaufnahme erstellt wurde, als Datenträger in die neue Data Science Virtual Machine-Instanz

1. Stellen Sie im Azure-Portal sicher, dass die Data Science Virtual Machine-Instanz ausgeführt wird.

2. Navigieren Sie im Azure-Portal zur Seite Ihrer Data Science Virtual Machine-Instanz. Wählen Sie auf der linken Seite das Blatt **Datenträger** aus. Wählen Sie **Vorhandenen Datenträger anfügen** aus.

3. Wählen Sie in der Dropdownliste **Datenträgername** den Datenträger aus, den Sie aus der Momentaufnahme Ihrer alten VM erstellt haben.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Screenshot: DSVM-Optionsseite mit Optionen für Datenträgeranfügung":::

4. Wählen Sie **Speichern** aus, um Ihren virtuellen Computer zu aktualisieren.

> [!Important]
> Ihre VM sollte ausgeführt werden, wenn Sie den Datenträger anfügen. Wenn die VM nicht ausgeführt wird, können die Datenträger in falscher Reihenfolge hinzugefügt werden, was zu einem verwirrenden und potenziell nicht startfähigen System führen kann. Wenn Sie den Datenträger ohne ausgeführten virtuellen Computer hinzufügen, wählen Sie das **X** neben dem Datenträger aus, starten Sie die VM, und fügen Sie ihn dann erneut an.

### <a name="manually-copy-the-wanted-data"></a>Manuelles Kopieren der gewünschten Daten 

1. Melden Sie sich mit SSH beim virtuellen Computer an, der ausgeführt wird.

2. Vergewissern Sie sich, dass Sie den Datenträger aus der Momentaufnahme Ihrer alten VM angefügt haben, indem Sie den folgenden Befehl ausführen:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Das Ergebnis sollte etwa wie die folgende Abbildung aussehen. In der Abbildung ist Datenträger `sda1` im Stamm eingebunden, und `sdb2` ist der `/mnt`-Ablagedatenträger. Der aus der Momentaufnahme Ihres alten virtuellen Computers erstellte Datenträger wird als `sdc1` identifiziert, ist aber noch nicht verfügbar. Dies wird durch das Fehlen eines Einbindungsorts deutlich. Ihre Ergebnisse haben möglicherweise andere Bezeichner, Sie sollten jedoch ein ähnliches Muster sehen.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Screenshot:lsblk-Ausgabe, die das nicht eingebundene Datenlaufwerk zeigt":::
    
3. Um auf das Datenlaufwerk zuzugreifen, erstellen Sie einen Speicherort für das Laufwerk, und binden Sie es ein. Ersetzen Sie `/dev/sdc1` durch den entsprechenden Wert, der von `lsblk` zurückgegeben wird:

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Nun enthält `/datadrive` die Verzeichnisse und Dateien Ihrer alten Data Science Virtual Machine-Instanz. Verschieben oder kopieren Sie die gewünschten Verzeichnisse oder Dateien vom Datenlaufwerk auf die neue VM.

Weitere Informationen finden Sie unter [Verwenden des Portals zum Anfügen eines Datenträgers an eine Linux-VM](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Herstellen einer Verbindung und Bestätigen des Versionsupgrades

Vergewissern Sie sich unabhängig davon, ob Sie eine direkte oder parallele Migration durchgeführt haben, dass das Upgrade erfolgreich war. Führen Sie den folgenden Befehl in einer Terminalsitzung aus: 

```bash
cat /etc/os-release
```

Außerdem sollte angezeigt werden, dass Sie Ubuntu 18.04 ausführen.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Screenshot: Ubuntu-Terminal mit Daten zur Betriebssystemversion":::

Die Versionsänderung wird auch im Azure-Portal angezeigt.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Screenshot: Portal mit DSVM-Eigenschaften einschließlich Betriebssystemversion":::

## <a name="next-steps"></a>Nächste Schritte

- [Data Science mit Ubuntu Data Science Machine-Instanz in Azure](./linux-dsvm-walkthrough.md)
- [Welche Tools sind in Azure Data Science Virtual Machine enthalten?](./tools-included.md)