---
title: Konfigurieren der Replikation für ADE-fähige (Azure Disk Encryption) virtuelle Computer in Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die Replikation ADE-fähiger (Azure Disk Encryption) virtueller Azure-Computer zwischen Azure-Regionen mithilfe von Site Recovery konfigurieren.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790996"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replizieren von ADE-fähigen (Azure Disk Encryption) virtuellen Computern in einer anderen Azure-Region

In diesem Artikel erfahren Sie, wie Sie ADE-fähige (Azure Disk Encryption) virtueller Computer zwischen Azure-Regionen replizieren.

>[!NOTE]
>Azure Site Recovery unterstützt derzeit nur virtuelle Azure-Computer, auf denen das Windows-Betriebssystem ausgeführt wird und die [für die Verschlüsselung mit Azure Active Directory (Azure AD) aktiviert sind](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Erforderliche Benutzerberechtigungen
Site Recovery erfordert, dass der Benutzer Berechtigungen besitzt, um den Schlüsseltresor in der Zielregion zu erstellen und Schlüssel in die Region zu kopieren.

Zum Aktivieren der Replikation von Azure Disk Encryption-fähigen VMs aus dem Azure-Portal benötigt der Benutzer folgende Berechtigungen:

- Schlüsseltresorberechtigungen
    - Auflisten
    - Erstellen
    - Get

-   Berechtigungen für Schlüsseltresorgeheimnisse
    - Auflisten
    - Erstellen
    - Get

- Berechtigungen für Schlüsseltresorschlüssel (nur erforderlich, wenn die virtuellen Computer den Schlüssel für die Schlüsselverschlüsselung verwenden, um Datenträger-Verschlüsselungsschlüssel zu verschlüsseln)
    - Auflisten
    - Get
    - Erstellen
    - Verschlüsseln
    - Entschlüsseln

Wechseln Sie zur Schlüsseltresorressource im Portal, um Berechtigungen zu verwalten. Fügen Sie die erforderlichen Berechtigungen für den Benutzer hinzu. Im folgenden Beispiel wird veranschaulicht, wie Berechtigungen für den Schlüsseltresor *ContosoWeb2Keyvault* aktiviert werden, der sich in der Quellregion befindet.

1. Wählen Sie **Home** > **Schlüsseltresore** > **ContosoWeb2KeyVault > Zugriffsrichtlinien** aus.

   ![Fenster für Schlüsseltresorberechtigungen](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Sie stellen fest, dass keine Benutzerberechtigungen vorhanden sind. Wählen Sie **Neue hinzufügen** aus. Geben Sie die Informationen zum Benutzer und zu den Berechtigungen ein.

   ![Schlüsseltresorberechtigungen](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Wenn der Benutzer, der die Notfallwiederherstellung (DR) aktiviert, nicht über die erforderlichen Berechtigungen zum Kopieren der Schlüssel verfügt, kann ein Sicherheitsadministrator mit entsprechenden Berechtigungen mithilfe des folgenden Skripts die Verschlüsselungsgeheimnisse und Schlüssel in die Zielregion kopieren.

Weitere Informationen zum Beheben von Problemen mit Berechtigungen finden Sie unter [Probleme mit Berechtigungen für Schlüsseltresore](#trusted-root-certificates-error-code-151066) später in diesem Artikel.

>[!NOTE]
>Zum Aktivieren der Replikation von Disk Encryption-VMs aus dem Portal benötigen Sie mindestens die Berechtigungen „Liste“ für die Schlüsseltresore, Schlüssel und Geheimnisse.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopieren von Datenträgerverschlüsselungsschlüsseln in die DR-Region mithilfe des PowerShell-Skripts

1. [Öffnen Sie den unformatierten Skriptcode „CopyKeys“](https://aka.ms/ade-asr-copy-keys-code).
2. Kopieren Sie das Skript in eine Datei, und benennen Sie diese **Copy-keys.ps1**.
3. Öffnen Sie die Windows PowerShell-Anwendung, und wechseln Sie zum Ordner, in dem Sie die Datei gespeichert haben.
4. Führen Sie „Copy-keys.ps1“ aus.
5. Geben Sie Azure-Anmeldeinformationen an, um sich anzumelden.
6. Wählen Sie das **Azure-Abonnement** Ihrer virtuellen Computer aus.
7. Warten Sie, bis die Ressourcengruppen geladen wurden, und wählen Sie dann die **Ressourcengruppe** Ihrer virtuellen Computer aus.
8. Wählen Sie die VMs in der angezeigten Liste aus. In der Liste werden nur VMs aufgeführt, die für die Datenträgerverschlüsselung aktiviert sind.
9. Wählen Sie den **Zielspeicherort** aus.

    - **Schlüsseltresore für Datenträgerverschlüsselung**
    - **Schlüsseltresore für Schlüsselverschlüsselung**

   Site Recovery erstellt standardmäßig einen neuen Schlüsseltresor in der Zielregion. Der Name des Tresors weist das Suffix „asr“ auf und basiert auf den Datenträgerverschlüsselungsschlüsseln der Quell-VM. Falls bereits ein von Site Recovery erstellter Schlüsseltresor vorhanden ist, wird er wiederverwendet. Wählen Sie bei Bedarf einen anderen Schlüsseltresor in der Liste aus.

## <a name="enable-replication"></a>Aktivieren der Replikation

In Beispiel ist die primäre Azure-Region „Asien, Osten“, und die sekundäre Region ist „Asien, Südosten“.

1. Wählen Sie im Tresor **+Replizieren** aus.
2. Die unten aufgeführten Felder werden angezeigt.
    - **Quelle**: Der Ursprung der virtuellen Computer, in diesem Fall **Azure**.
    - **Quellstandort**: Die Azure-Region, in der Sie Ihre virtuellen Computer schützen möchten. In diesem Beispiel lautet der Quellstandort „Asien, Osten“.
    - **Bereitstellungsmodell**: Das Azure-Bereitstellungsmodell der Quellcomputer.
    - **Quellabonnement**: Das Abonnement, dem Ihre virtuellen Quellcomputer angehören. Dies kann ein beliebiges Abonnement im gleichen Azure Active Directory-Mandanten sein, in dem sich auch Ihr Recovery Services-Tresor befindet.
    - **Ressourcengruppe**: Die Ressourcengruppe, der Ihre virtuellen Quellcomputer angehören. Alle virtuellen Computer in der ausgewählten Ressourcengruppe werden im nächsten Schritt für den Schutz aufgeführt.

3. Wählen Sie unter **Virtuelle Computer** > **Virtuelle Computer auswählen** die einzelnen VMs aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Wählen Sie anschließend **OK** aus.

4. Unter **Einstellungen** können Sie die folgenden Einstellungen für den Zielstandort konfigurieren.

    - **Zielspeicherort**: Der Standort, an den die Daten Ihrer virtuellen Quellcomputer repliziert werden. Site Recovery stellt eine Liste geeigneter Zielregionen bereit, die auf dem Standort des ausgewählten Computers basiert. Es empfiehlt sich, denselben Standort wie für den Recovery Services-Tresor zu verwenden.
    - **Zielabonnement**: Das Zielabonnement für die Notfallwiederherstellung. Zielabonnement und Quellabonnement sind standardmäßig identisch.
    - **Zielressourcengruppe**: Die Ressourcengruppe, der all Ihre replizierten virtuellen Computer angehören. Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe. Der Name erhält das Suffix „asr“. Falls bereits eine von Azure Site Recovery erstellte Ressourcengruppe vorhanden ist, wird diese wiederverwendet. Sie können die Gruppe auch anpassen, wie im folgenden Abschnitt gezeigt. Der Speicherort der Zielressourcengruppe kann eine beliebige Azure-Region sein, mit Ausnahme der Region, in der die virtuellen Quellcomputer gehostet werden.
    - **Virtuelles Zielnetzwerk**: Site Recovery erstellt standardmäßig in der Zielregion ein neues virtuelles Netzwerk. Der Name erhält das Suffix „asr“. Es wird Ihrem Quellnetzwerk zugeordnet und für alle zukünftigen Schutzaktivitäten verwendet. Informationen zur Netzwerkzuordnung finden Sie [hier](site-recovery-network-mapping-azure-to-azure.md).
    - **Zielspeicherkonten (wenn die Quell-VM keine verwalteten Datenträger verwendet)** : Standardmäßig erstellt Site Recovery ein neues Zielspeicherkonto und übernimmt dabei die Speicherkonfiguration Ihrer Quell-VM. Sollte bereits ein Speicherkonto vorhanden sein, wird dieses wiederverwendet.
    - **Verwaltete Replikatdatenträger (wenn die Quell-VM verwaltete Datenträger verwendet)** : Site Recovery erstellt neue verwaltete Replikatdatenträger in der Zielregion, um die verwalteten Datenträger der Quell-VM zu spiegeln. Dabei wird der gleiche Speichertyp (Standard oder Premium) verwendet wie für die verwalteten Datenträger der Quell-VM.
    - **Cachespeicherkonten**: Site Recovery benötigt als zusätzliches Speicherkonto in der Quellregion ein sogenanntes *Cachespeicherkonto*. Alle Änderungen an den virtuellen Quellcomputern werden nachverfolgt und an das Cachespeicherkonto gesendet. Anschließend werden sie an den Zielspeicherort repliziert.
    - **Verfügbarkeitsgruppe**: Site Recovery erstellt standardmäßig in der Zielregion eine neue Verfügbarkeitsgruppe. Der Name weist das Suffix „asr“ auf. Falls bereits eine von Site Recovery erstellte Verfügbarkeitsgruppe vorhanden ist, wird diese wiederverwendet.
    - **Schlüsseltresore für Datenträgerverschlüsselung**: Site Recovery erstellt standardmäßig einen neuen Schlüsseltresor in der Zielregion. Sein Name weist das Suffix „asr“ auf und basiert auf den Datenträgerverschlüsselungsschlüsseln der Quell-VM. Falls bereits ein von Azure Site Recovery erstellter Schlüsseltresor vorhanden ist, wird er wiederverwendet.
    - **Schlüsseltresore für Schlüsselverschlüsselung**: Site Recovery erstellt standardmäßig einen neuen Schlüsseltresor in der Zielregion. Der Name weist das Suffix „asr“ auf und basiert auf den Schlüsselverschlüsselungsschlüsseln der Quell-VM. Falls bereits ein von Azure Site Recovery erstellter Schlüsseltresor vorhanden ist, wird er wiederverwendet.
    - **Replikationsrichtlinie**: Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf des Wiederherstellungspunkts und die Häufigkeit von anwendungskonsistenten Momentaufnahmen. Standardmäßig erstellt Site Recovery eine neue Replikationsrichtlinie mit der Standardeinstellung *24 Stunden* für den Aufbewahrungszeitraum des Wiederherstellungspunkts und *60 Minuten* für die App-konsistente Momentaufnahmenhäufigkeit.

## <a name="customize-target-resources"></a>Anpassen der Zielressourcen

Führen Sie die folgenden Schritte aus, um die Site Recovery-Standardzieleinstellungen zu ändern.

1. Wählen Sie neben „Zielabonnement“ die Option **Anpassen** aus, um das Standardzielabonnement zu ändern. Wählen Sie das Abonnement aus der Liste der Abonnements aus, die im Azure AD-Mandanten verfügbar sind.

2. Klicken Sie neben „Ressourcengruppe“, „Netzwerk“, „Speicher“ und „Verfügbarkeitsgruppen“ auf **Anpassen**, um die folgenden Standardeinstellungen zu ändern:
    - Wählen Sie für **Zielressourcengruppe** aus der Liste der Ressourcengruppen, die innerhalb des Abonnements am Zielspeicherort vorhanden sind, die gewünschte Ressourcengruppe aus.
    - Wählen Sie unter **Virtuelles Zielnetzwerk** in der Liste der virtuellen Netzwerke am Zielspeicherort das gewünschte Netzwerk aus.
    - Für **Verfügbarkeitsgruppe** können Sie dem virtuellen Computer Verfügbarkeitsgruppeneinstellungen hinzufügen, sofern dieser einer Verfügbarkeitsgruppe in der Quellregion angehört.
    - Wählen Sie für **Zielspeicherkonten** das Konto aus, das Sie verwenden möchten.

2. Klicken Sie neben „Verschlüsselungseinstellungen“ auf **Anpassen**, um die folgenden Standardeinstellungen zu ändern:
   - Wählen Sie für **Schlüsseltresor für Zieldatenträgerverschlüsselung** den Schlüsseltresor für die Zieldatenträgerverschlüsselung in der Liste der Schlüsseltresore am Zielspeicherort des Abonnements aus.
   - Wählen Sie unter **Schlüsseltresor für Zielschlüsselverschlüsselung** den Schlüsseltresor für die Zielschlüsselverschlüsselung in der Liste der Schlüsseltresore am Zielspeicherort des Abonnements aus.

3. Wählen Sie **Zielressource erstellen** > **Replikation aktivieren** aus.
4. Nachdem Sie die Replikation für die virtuellen Computer aktiviert haben, können Sie unter **Replizierte Elemente** den VM-Integritätsstatus überprüfen.

>[!NOTE]
>Die Aktualisierung des Status kann während der Erstreplikation einige Zeit dauern, und zu Beginn wird unter Umständen kein offensichtlicher Fortschritt angezeigt. Klicken Sie zum Abrufen des aktuellen Status auf **Aktualisieren**.

## <a name="update-target-vm-encryption-settings"></a>Aktualisieren der Verschlüsselungseinstellungen für die Ziel-VM
In den folgenden Szenarien müssen Sie die Verschlüsselungseinstellungen für den virtuellen Zielcomputer aktualisieren:
  - Sie haben die Site Recovery-Replikation für die VM aktiviert. Später haben Sie die Datenträgerverschlüsselung auf der Quell-VM aktiviert.
  - Sie haben die Site Recovery-Replikation für die VM aktiviert. Später haben Sie den Schlüssel für die Datenträgerverschlüsselung bzw. den Schlüssel für die Schlüsselverschlüsselung auf der Quell-VM geändert.

Sie können mit einem [Skript](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) die Verschlüsselungsschlüssel in die Zielregion kopieren und dann die Zielverschlüsselungseinstellungen unter **Recovery Services-Tresor** > *repliziertes Element* > **Eigenschaften** > **Compute und Netzwerk** aktualisieren.

![Dialogfeld zum Aktualisieren von ADE-Einstellungen](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Beheben von Problemen mit der Berechtigung für den Schlüsseltresor während der Azure-zu-Azure-VM-Replikation

**Ursache 1:** Möglicherweise haben Sie in der Zielregion einen bereits erstellten Schlüsseltresor ohne die erforderlichen Berechtigungen ausgewählt, anstatt einen von Site Recovery erstellen zu lassen. Vergewissern Sie sich, dass der Schlüsseltresor über die beschriebenen erforderlichen Berechtigungen verfügt.

*Beispiel*: Sie versuchen, eine VM zu replizieren, die über den Schlüsseltresor *ContososourceKeyvault* in einer Quellregion verfügt.
Sie besitzen alle Berechtigungen für den Schlüsseltresor in der Quellregion. Während des Schutzes wählen Sie jedoch den bereits erstellten Schlüsseltresor „ContosotargetKeyvault“, der über keine Berechtigungen verfügt. Ein Fehler tritt auf.

**Wie behebe ich das Problem?** Wählen Sie **Home** > **Schlüsseltresore** > **ContososourceKeyvault** > **Zugriffsrichtlinien** aus, und fügen Sie die entsprechenden Berechtigungen hinzu.

**Ursache 2:** Möglicherweise haben Sie in der Zielregion einen bereits erstellten Schlüsseltresor ohne Berechtigungen zum Entschlüsseln und Verschlüsseln ausgewählt, anstatt einen von Site Recovery erstellen zu lassen. Stellen Sie sicher, dass Sie über Berechtigungen zum Entschlüsseln und Verschlüsseln verfügen, falls Sie den Schlüssel auch in der Quellregion verschlüsseln.</br>

*Beispiel*: Sie versuchen, eine VM zu replizieren, die über den Schlüsseltresor *ContososourceKeyvault* in der Quellregion verfügt. Sie besitzen alle erforderlichen Berechtigungen für den Schlüsseltresor in der Quellregion. Während des Schutzes wählen Sie jedoch den bereits erstellten Schlüsseltresor „ContosotargetKeyvault“, der über keine Berechtigungen zum Entschlüsseln und Verschlüsseln verfügt. Ein Fehler tritt auf.</br>

**Wie behebe ich das Problem?** Wählen Sie **Home** > **Schlüsseltresore** > **ContososourceKeyvault** > **Zugriffsrichtlinien** aus. Fügen Sie unter **Schlüsselberechtigungen** > **Kryptografische Vorgänge** Berechtigungen hinzu.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](site-recovery-test-failover-to-azure.md) über die Ausführung von Testfailovervorgängen.
