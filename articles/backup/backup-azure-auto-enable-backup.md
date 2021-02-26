---
title: Automatisches Aktivieren der Sicherung bei der VM-Erstellung mithilfe von Azure Policy
description: In diesem Artikel wird beschrieben, wie Sie Azure Policy für die automatische Aktivierung der Sicherung für alle VMs verwenden, die in einem bestimmten Bereich erstellt wurden.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896866"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatisches Aktivieren der Sicherung bei der VM-Erstellung mithilfe von Azure Policy

Eine der Hauptaufgaben eines Sicherungs- oder Kompatibilitätsadministrators in einer Organisation besteht darin sicherzustellen, dass alle geschäftskritischen Computer mit der entsprechenden Aufbewahrung gesichert werden.

Heute bietet Azure Backup eine Vielzahl von integrierten Richtlinien ( unter Verwendung von [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)), mit denen Sie automatisch sicherstellen können, dass Ihre virtuellen Azure-Computer für die Sicherung konfiguriert sind. Je nachdem, wie Ihre Sicherungsteams und Ressourcen organisiert sind, können Sie eine der folgenden Richtlinien verwenden:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Richtlinie 1: Konfigurieren der Sicherung auf virtuellen Computern ohne ein bestimmtes Tag in einem vorhandenen Recovery Services-Tresor am selben Speicherort

Wenn Ihr Unternehmen über ein zentrales Sicherungsteam verfügt, das anwendungsübergreifende Sicherungen verwaltet, können Sie diese Richtlinie verwenden, um die Sicherung in einen vorhandenen zentralen Recovery Services-Tresor zu konfigurieren, der sich im selben Abonnement und am gleichen Ort befindet wie die zu verwaltenden VMs. Sie können wählen, VMs, die ein bestimmtes Tag enthalten, vom Bereich dieser Richtlinie **auszuschließen**.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Richtlinie 2: [Vorschau] Konfigurieren der Sicherung auf virtuellen Computern mit einem bestimmten Tag in einem vorhandenen Recovery Services-Tresor am selben Speicherort
Diese Richtlinie funktioniert genauso wie Richtlinie 1 oben, mit dem einzigen Unterschied, dass Sie diese Richtlinie verwenden können, um VMs, die ein bestimmtes Tag enthalten, in den Bereich dieser Richtlinie **einzuschließen**. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Richtlinie 3: [Vorschau] Konfigurieren der Sicherung auf virtuellen Computern ohne ein bestimmtes Tag in einem neuen Recovery Services-Tresor mit einer Standardrichtlinie
Wenn Sie Anwendungen in dedizierten Ressourcengruppen organisieren und diese von demselben Tresor sichern lassen möchten, können Sie diese Aktion mit dieser Richtlinie automatisch verwalten. Sie können wählen, VMs, die ein bestimmtes Tag enthalten, vom Bereich dieser Richtlinie **auszuschließen**.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Richtlinie 4: [Vorschau] Konfigurieren der Sicherung auf virtuellen Computern mit einem bestimmten Tag in einem neuen Recovery Services-Tresor mit einer Standardrichtlinie
Diese Richtlinie funktioniert genauso wie Richtlinie 3 oben, mit dem einzigen Unterschied, dass Sie diese Richtlinie verwenden können, um VMs, die ein bestimmtes Tag enthalten, in den Bereich dieser Richtlinie **einzuschließen**. 

Zusätzlich zu den oben genannten Möglichkeiten bietet Azure Backup auch eine [reine Überwachungsrichtlinie](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) – **Azure Backup sollte für virtuelle Computer aktiviert sein**. Diese Richtlinie identifiziert, für welche virtuellen Computer keine Sicherung aktiviert ist, konfiguriert aber nicht automatisch Sicherungen für diese VMs. Dies ist nützlich, wenn Sie nur die allgemeine Konformität der VMs bewerten, aber nicht sofort Maßnahmen ergreifen möchten.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Die integrierte Richtlinie wird derzeit nur für Azure-VMs unterstützt. Benutzer müssen darauf achten, dass die bei der Zuweisung angegebene Aufbewahrungsrichtlinie eine VM-Aufbewahrungsrichtlinie ist. In [diesem](./backup-azure-policy-supported-skus.md) Dokument finden Sie alle von dieser Richtlinie unterstützten VM-SKUs.

* Die Richtlinien 1 und 2 können immer nur einem einzelnen Standort und Abonnement zugewiesen werden. Zum Aktivieren der Sicherung für standort- und abonnementübergreifende virtuelle Computer müssen mehrere Instanzen der Richtlinienzuweisung erstellt werden, eine für jede Kombination aus Standort und Abonnement.

* Für die Richtlinien 1 und 2 wird der Bereich „Verwaltungsgruppe“ derzeit nicht unterstützt.

* Für die Richtlinien 1 und 2 können sich der angegebene Tresor und die für die Sicherung konfigurierten virtuellen Computer in verschiedenen Ressourcengruppen befinden.

* Die Richtlinien 1, 2, 3 und 4 sind in nationalen Clouds zurzeit nicht verfügbar.

* Die Richtlinien 3 und 4 können jeweils einem Abonnement (oder einer Ressourcengruppe innerhalb eines Abonnements) zugewiesen werden.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Verwenden der integrierten Richtlinien

In den folgenden Schritten wird der End-to-End-Prozess der Zuweisung von Richtlinie 1, **Konfigurieren der Sicherung auf virtuellen Computern ohne ein bestimmtes Tag in einem vorhandenen Recovery Services-Tresor am selben Speicherort**, zu einem bestimmten Bereich beschrieben. Ähnliche Anweisungen gelten auch für die anderen Richtlinien. Nach der Zuweisung werden alle neuen virtuellen Computer, die im Bereich erstellt wurden, automatisch für die Sicherung konfiguriert.

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zum Dashboard **Richtlinie**.
2. Wählen Sie im Menü auf der linken Seite **Definitionen** aus, um eine Liste aller integrierten Richtlinien für Azure-Ressourcen abzurufen.
3. Filtern Sie die Liste nach **Kategorie=Sicherung**, und wählen Sie die Richtlinie mit dem Namen „Sicherung von VMs eines Standorts in einem vorhandenen zentralen Tresor am selben Standort konfigurieren“ aus.
![Dashboard „Richtlinie“](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Wählen Sie den Namen der Richtlinie aus. Sie werden zur detaillierten Definition für diese Richtlinie umgeleitet.
![Bereich „Richtliniendefinition“](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Wählen Sie oben im Bereich die Schaltfläche **Zuweisen** aus. Dadurch werden Sie zum Bereich **Richtlinie zuweisen** umgeleitet.
6. Wählen Sie unter **Grundlagen** die drei Punkte neben dem Feld **Bereich** aus. Dadurch wird ein Bereich mit dem richtigen Kontext geöffnet, auf dem Sie das Abonnement auswählen können, auf das die Richtlinie angewendet werden soll. Optional können Sie auch eine Ressourcengruppe auswählen, damit die Richtlinie nur für VMs in einer bestimmten Ressourcengruppe angewendet wird.
![Grundlegendes zur Richtlinienzuweisung](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Wählen Sie auf der Registerkarte **Parameter** einen Speicherort aus der Dropdownliste aus, und wählen Sie dann den Tresor und die Sicherungsrichtlinie aus, denen die VMs im Bereich zugeordnet werden müssen. Sie können auch einen Namen für das Tag und ein Array von Tagwerten angeben. Ein virtueller Computer, der einen der angegebenen Werte für das angegebene Tag enthält, wird vom Bereich der Richtlinienzuweisung ausgeschlossen.
![Parameter der Richtlinienzuweisung](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Stellen Sie sicher, dass **Effekt** auf „deployIfNotExists“ festgelegt ist.
9. Navigieren Sie zu **Überprüfen und erstellen**, und wählen Sie **Erstellen** aus.

> [!NOTE]
>
> Azure Policy kann mithilfe von [Wiederherstellung](../governance/policy/how-to/remediate-resources.md) auch auf vorhandenen virtuellen Computern verwendet werden.

> [!NOTE]
>
> Sie sollten diese Richtlinie nicht mehr als 200 virtuellen Computern gleichzeitig zuzuweisen. Wenn die Richtlinie mehr als 200 virtuellen Computern zugewiesen wird, kann dies dazu führen, dass die Sicherung einige Stunden später ausgelöst wird, als durch den Zeitplan festgelegt.

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zu Azure Policy](../governance/policy/overview.md)
