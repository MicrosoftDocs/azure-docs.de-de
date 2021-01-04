---
title: Direktes Upgrade von Red Hat Enterprise Linux-Images in Azure
description: Hier erfahren Sie, wie Sie ein direktes Upgrades von Red Hat Enterprise 7.x-Images auf die neueste 8.x-Version durchführen.
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 9b35a3cbe23af91f7f0b8aceecfb8ba3c9720461
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484412"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Direkte Upgrades für Red Hat Enterprise Linux

Dieser Artikel bietet eine Anleitung dazu, wie Sie ein direktes Upgrade von Red Hat Enterprise Linux 7 (RHEL) auf Red Hat Enterprise Linux 8 durchführen. Bei dieser Anleitung wird das `leapp`-Tool in Azure verwendet. Während des direkten Upgrades wird das bestehende RHEL 7-Betriebssystem durch die RHEL 8-Version ersetzt.

>[!Note] 
> Angebote von SQL Server auf Red Hat Enterprise Linux unterstützen kein direktes Upgrade in Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Erwartungen während des Upgrades
Während des Upgrades startet das System einige Male neu. Beim letzten Neustart wird das Upgrade der VM auf die neueste Nebenversion von RHEL 8 durchgeführt. 

Es kann zwischen 20 Minuten und 2 Stunden dauern, bis der Upgradeprozess abgeschlossen ist. Die Gesamtzeit hängt von verschiedenen Faktoren ab, beispielsweise der VM-Größe und der Anzahl der auf dem System installierten Pakete.

## <a name="preparations"></a>Vorbereitungen
Red Hat und Azure empfehlen ein direktes Upgrade, um in einem System zur nächsten Hauptversion zu wechseln. 

Bevor Sie das Upgrade starten, beachten Sie die folgenden Überlegungen. 

>[!Important] 
> Erstellen Sie eine Momentaufnahme des Images, bevor Sie das Upgrade starten.

* Stellen Sie sicher, dass Sie die neueste RHEL 7-Version verwenden. Derzeit ist RHEL 7.9 die neueste Version. Wenn Sie eine gesperrte Version verwenden und kein Upgrade auf RHEL 7.9 durchführen können, [befolgen Sie diese Schritte, um zu einem Nicht-EUS-Repository (Extended Update Support) zu wechseln](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Führen Sie den folgenden Befehl aus, um das Upgrade zu überprüfen und festzustellen, ob es erfolgreich ausgeführt werden kann. Der Befehl sollte diese Datei generieren: */var/log/leapp/leapp-report.txt*. Dieser Datei erläutert, wie der Prozess funktioniert, welche Vorgänge ausgeführt werden können und ob das Upgrade möglich ist.

    >[!NOTE]
    > Verwenden Sie das Root-Konto, um die Befehle in diesem Artikel auszuführen. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Stellen Sie sicher, dass die serielle Konsole funktionsfähig ist. Sie benötigen diese Konsole zur Überwachung während des Upgradeprozesses.

* Aktivieren Sie den SSH-Root-Zugriff in */etc/ssh/sshd_config*:
    1. Öffnen Sie die Datei */etc/ssh/sshd_config*.
    1. Suchen Sie nach `#PermitRootLogin yes`.
    1. Entfernen Sie das Nummernzeichen (`#`), um die Auskommentierung der Zeichenfolge aufzuheben.

## <a name="upgrade-steps"></a>Upgradeschritte

Befolgen Sie diese Schritte sehr genau. Es wird empfohlen, das Upgrade auf einem Testcomputer zu testen, bevor Sie es auf Instanzen der Produktionsumgebung durchführen.

1. Führen Sie ein `yum`-Update durch, um die neuesten Clientpakete abzurufen.
    ```bash
    yum update -y
    ```

1. Installieren Sie `leapp-client-package`.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Rufen Sie im [Red Hat-Portal](https://access.redhat.com/articles/3664871) die Datei *leapp-data.tar.gz* ab, die *repomap.csv* und *pes-events.json* enthält. Extrahieren Sie die Datei *leapp-data.tar.gz*.
    1. Laden Sie die Datei *leapp-data.tar.gz* herunter.
    1. Extrahieren Sie den Inhalt, und entfernen Sie die Datei. Verwenden Sie den folgenden Befehl:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Fügen Sie eine `answers`-Datei für `leapp` hinzu.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Starten Sie das Upgrade.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Nach erfolgreicher Ausführung des `leapp upgrade`-Befehls starten Sie das System manuell neu, um den Vorgang abzuschließen. Das System ist nicht verfügbar, weil es mehrmals neu gestartet wird. Überwachen Sie den Prozess über die serielle Konsole.

1.  Überprüfen Sie, ob das Upgrade erfolgreich abgeschlossen wurde.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Wenn das Upgrade abgeschlossen ist, entfernen Sie den SSH-Root-Zugriff:
    1. Öffnen Sie die Datei */etc/ssh/sshd_config*.
    1. Suchen Sie nach `#PermitRootLogin yes`.
    1. Fügen Sie ein Nummernzeichen (`#`) hinzu, um die Zeichenfolge auszukommentieren.

1. Starten Sie den SSHD-Dienst neu, damit die Änderungen übernommen werden.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Häufige Probleme

Folgende Fehler sind häufig festzustellen, wenn der `leapp preupgrade`-Prozess oder der `leapp upgrade`-Prozess nicht funktioniert:

* **Fehler:** Keine Übereinstimmungen für die folgenden Muster für deaktivierte Plug-Ins gefunden.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Lösung**: Deaktivieren Sie das Plug-In „subscription-manager“. Hierzu bearbeiten Sie die Datei */etc/yum/pluginconf.d/subscription-manager.conf* und ändern `enabled` in `enabled=0`.

    Dieser Fehler tritt auf, wenn das aktivierte `yum`-Plug-In „subscription-manager“ für `PAYG`-VMs nicht verwendet wird.

* **Fehler:** Mögliche Probleme mit der Remoteanmeldung über „root“.

    Dieser Fehler wird möglicherweise bei einem Fehler in `leapp preupgrade` angezeigt:

    ```structured-text
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    
    Upgrade has been inhibited due to the following problems:
        1. Inhibitor: Possible problems with remote login using root account
    Consult the pre-upgrade report for details and possible remediation.
    
    ============================================================
                         UPGRADE INHIBITED
    ============================================================
    ```
    **Lösung**: Aktivieren Sie den Root-Zugriff in */etc/sshd_config*.

    Dieser Fehler tritt auf, wenn der SSH-Root-Zugriff in */etc/sshd_config* nicht aktiviert ist. Weitere Informationen finden Sie im Abschnitt [Vorbereitungen](#preparations) in diesem Artikel. 


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Red Hat-Images in Azure](./redhat-images.md).
* Erfahren Sie mehr über die [Red Hat-Updateinfrastruktur](./redhat-rhui.md).
* Erfahren Sie mehr über das [RHEL-BYOS-Angebot](./byos.md).
* Informationen zu den Prozessen beim direkten Red Hat-Upgrade finden Sie in der Red Hat-Dokumentation unter [Upgrading from RHEL 7 TO RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index).
* Informationen zu Red Hat-Supportrichtlinien für alle RHEL-Versionen finden Sie in der Red Hat-Dokumentation unter [Red Hat Enterprise Linux Life Cycle](https://access.redhat.com/support/policy/updates/errata).