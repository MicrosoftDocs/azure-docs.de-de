---
title: Aktivieren der Replikation für verschlüsselte Azure-VMs in Azure Site Recovery
description: In diesem Artikel erfahren Sie, wie Sie die Replikation für VMs mit Datenträgern, die für kundenseitig verwaltete Schlüssel (CMK) aktiviert sind, zwischen Azure-Regionen mithilfe von Site Recovery konfigurieren.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146534"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replizieren von Computern mit Datenträgern, die für kundenseitig verwaltete Schlüssel (CMK) aktiviert sind

In diesem Artikel erfahren Sie, wie Sie Azure-VMs mit Datenträgern, die für kundenseitig verwaltete Schlüssel aktiviert sind, zwischen Azure-Regionen replizieren.

## <a name="prerequisite"></a>Voraussetzung
Sie müssen die Datenträgerverschlüsselungssätze in der Zielregion für das Zielabonnement erstellen, bevor Sie die Replikation für Ihre virtuellen Computer aktivieren, die über CMK-aktivierte verwaltete Datenträger verfügen.

## <a name="enable-replication"></a>Aktivieren der Replikation

In Beispiel ist die primäre Azure-Region „Asien, Osten“, und die sekundäre Region ist „Asien, Südosten“.

1. Wählen Sie im Tresor **+Replizieren** aus.
2. Die unten aufgeführten Felder werden angezeigt.
    - **Quelle** : Der Ursprung der virtuellen Computer, in diesem Fall **Azure**.
    - **Quellstandort** : Die Azure-Region, in der Sie Ihre virtuellen Computer schützen möchten. In diesem Beispiel lautet der Quellstandort „Asien, Osten“.
    - **Bereitstellungsmodell** : Das Azure-Bereitstellungsmodell der Quellcomputer.
    - **Quellabonnement** : Das Abonnement, dem Ihre virtuellen Quellcomputer angehören. Dies kann ein beliebiges Abonnement im gleichen Azure Active Directory-Mandanten sein, in dem sich auch Ihr Recovery Services-Tresor befindet.
    - **Ressourcengruppe** : Die Ressourcengruppe, der Ihre virtuellen Quellcomputer angehören. Alle virtuellen Computer in der ausgewählten Ressourcengruppe werden im nächsten Schritt für den Schutz aufgeführt.

3. Wählen Sie unter **Virtuelle Computer** > **Virtuelle Computer auswählen** die einzelnen VMs aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Wählen Sie anschließend **OK** aus.

4. Unter **Einstellungen** können Sie die folgenden Einstellungen für den Zielstandort konfigurieren.

    - **Zielspeicherort** : Der Standort, an den die Daten Ihrer virtuellen Quellcomputer repliziert werden. Site Recovery stellt eine Liste geeigneter Zielregionen bereit, die auf dem Standort des ausgewählten Computers basiert. Es empfiehlt sich, denselben Standort wie für den Recovery Services-Tresor zu verwenden.
    - **Zielabonnement** : Das Zielabonnement für die Notfallwiederherstellung. Zielabonnement und Quellabonnement sind standardmäßig identisch.
    - **Zielressourcengruppe** : Die Ressourcengruppe, der all Ihre replizierten virtuellen Computer angehören. Site Recovery erstellt standardmäßig in der Zielregion eine neue Ressourcengruppe. Der Name erhält das Suffix `asr`. Falls bereits eine von Azure Site Recovery erstellte Ressourcengruppe vorhanden ist, wird diese wiederverwendet. Sie können die Gruppe auch anpassen, wie im folgenden Abschnitt gezeigt. Der Speicherort der Zielressourcengruppe kann eine beliebige Azure-Region sein, mit Ausnahme der Region, in der die virtuellen Quellcomputer gehostet werden.
    - **Virtuelles Zielnetzwerk** : Site Recovery erstellt standardmäßig in der Zielregion ein neues virtuelles Netzwerk. Der Name erhält das Suffix `asr`. Es wird Ihrem Quellnetzwerk zugeordnet und für alle zukünftigen Schutzaktivitäten verwendet. Informationen zur Netzwerkzuordnung finden Sie [hier](./azure-to-azure-network-mapping.md).
    - **Zielspeicherkonten (wenn die Quell-VM keine verwalteten Datenträger verwendet)** : Standardmäßig erstellt Site Recovery ein neues Zielspeicherkonto und übernimmt dabei die Speicherkonfiguration Ihrer Quell-VM. Sollte bereits ein Speicherkonto vorhanden sein, wird dieses wiederverwendet.
    - **Verwaltete Replikatdatenträger (wenn die Quell-VM verwaltete Datenträger verwendet)** : Site Recovery erstellt neue verwaltete Replikatdatenträger in der Zielregion, um die verwalteten Datenträger der Quell-VM zu spiegeln. Dabei wird der gleiche Speichertyp (Standard oder Premium) verwendet wie für die verwalteten Datenträger der Quell-VM.
    - **Cachespeicherkonten** : Site Recovery benötigt als zusätzliches Speicherkonto in der Quellregion ein sogenanntes *Cachespeicherkonto*. Alle Änderungen an den virtuellen Quellcomputern werden nachverfolgt und an das Cachespeicherkonto gesendet. Anschließend werden sie an den Zielspeicherort repliziert.
    - **Verfügbarkeitsgruppe** : Site Recovery erstellt standardmäßig in der Zielregion eine neue Verfügbarkeitsgruppe. Der Name weist das Suffix `asr` auf. Falls bereits eine von Site Recovery erstellte Verfügbarkeitsgruppe vorhanden ist, wird diese wiederverwendet.
    - **Datenträgerverschlüsselungssätze (DES)** : Site Recovery erfordert, dass die Datenträgerverschlüsselungssätze für Replikat- und Zieldatenträger verwendet werden. Vor dem Aktivieren der Replikation müssen DES im Zielabonnement und in der Zielregion vorab erstellen. Standardmäßig ist DES nicht ausgewählt. Sie müssen auf „Anpassen“ klicken, um einen DES pro Quelldatenträger auszuwählen.
    - **Replikationsrichtlinie** : Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf des Wiederherstellungspunkts und die Häufigkeit von anwendungskonsistenten Momentaufnahmen. Standardmäßig erstellt Site Recovery eine neue Replikationsrichtlinie mit der Standardeinstellung *24 Stunden* für den Aufbewahrungszeitraum des Wiederherstellungspunkts und *60 Minuten* für die App-konsistente Momentaufnahmenhäufigkeit.

    ![Aktivieren der Replikation für einen Computer mit CMK-aktivierten Datenträgern](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Anpassen der Zielressourcen

Führen Sie die folgenden Schritte aus, um die Site Recovery-Standardzieleinstellungen zu ändern.

1. Wählen Sie neben „Zielabonnement“ die Option **Anpassen** aus, um das Standardzielabonnement zu ändern. Wählen Sie das Abonnement aus der Liste der Abonnements aus, die im Azure AD-Mandanten verfügbar sind.

2. Klicken Sie neben „Ressourcengruppe“, „Netzwerk“, „Speicher“ und „Verfügbarkeitsgruppen“ auf **Anpassen** , um die folgenden Standardeinstellungen zu ändern:
    - Wählen Sie für **Zielressourcengruppe** aus der Liste der Ressourcengruppen, die innerhalb des Abonnements am Zielspeicherort vorhanden sind, die gewünschte Ressourcengruppe aus.
    - Wählen Sie unter **Virtuelles Zielnetzwerk** in der Liste der virtuellen Netzwerke am Zielspeicherort das gewünschte Netzwerk aus.
    - Für **Verfügbarkeitsgruppe** können Sie dem virtuellen Computer Verfügbarkeitsgruppeneinstellungen hinzufügen, sofern dieser einer Verfügbarkeitsgruppe in der Quellregion angehört.
    - Wählen Sie für **Zielspeicherkonten** das Konto aus, das Sie verwenden möchten.

3. Wählen Sie **Anpassen** neben „Speicherverschlüsselungseinstellungen“ aus, um den Ziel-DES für jeden CMK-aktivierten (Customer Managed Key, kundenseitig verwalteter Schlüssel) verwalteten Quelldatenträger auszuwählen. Zum Zeitpunkt der Auswahl können Sie auch sehen, welchem Zielschlüsseltresor der DES zugeordnet ist.

4. Wählen Sie **Zielressource erstellen** > **Replikation aktivieren** aus.
5. Nachdem Sie die Replikation für die virtuellen Computer aktiviert haben, können Sie unter **Replizierte Elemente** den VM-Integritätsstatus überprüfen.

![Der Screenshot zeigt, wo der Integritätsstatus der VMs zu überprüfen ist.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Die Aktualisierung des Status kann während der Erstreplikation einige Zeit dauern, und zu Beginn wird unter Umständen kein offensichtlicher Fortschritt angezeigt. Klicken Sie zum Abrufen des aktuellen Status auf **Aktualisieren**.

## <a name="faqs"></a>Häufig gestellte Fragen

* Ich habe CMK für ein vorhandenes repliziertes Element aktiviert. Wie kann ich sicherstellen, dass CMK auch auf die Zielregion angewendet wird?

    Sie können den Namen des verwalteten Replikatdatenträgers ermitteln (erstellt von Azure Site Recovery in der Zielregion) und den DES an den Replikatdatenträger anfügen. Die DES-Details können jedoch nicht auf dem Blatt „Datenträger“ angezeigt werden, nachdem Sie ihn angefügt haben. Wahlweise können Sie auch die Replikation des virtuellen Computers deaktivieren und dann erneut aktivieren. So wird sichergestellt, dass die Details des DES und des Schlüsseltresors auf dem Blatt „Datenträger“ für das replizierte Element angezeigt werden.

* Ich habe dem replizierten Element einen neuen CMK-aktivierten Datenträger hinzugefügt. Wie kann ich diesen Datenträger mit Azure Site Recovery replizieren?

    Das Hinzufügen eines neuen CMK-aktivierten Datenträgers zu einem vorhandenen replizierten Element wird nicht unterstützt. Deaktivieren Sie die Replikation, und aktivieren Sie die Replikation für den virtuellen Computer dann erneut.

* Ich habe sowohl plattform- als auch kundenseitig verwaltete Schlüssel aktiviert. Wie kann ich meine Datenträger schützen?

    Die Aktivierung einer doppelten Verschlüsselung sowohl mit plattformseitig als auch mit kundenseitig verwalteten Schlüsseln wird von Site Recovery unterstützt. Befolgen Sie die Anweisungen in diesem Artikel, um Ihren Computer zu schützen. Sie müssen im Voraus einen für die doppelte Verschlüsselung aktivierten DES in der Zielregion erstellen. Diesen DES stellen Sie in dem Moment für Site Recovery bereit, in dem Sie die Replikation für eine solche VM aktivieren.

