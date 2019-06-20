---
title: Migrieren Ihrer klassischen Warnungen in Azure Monitor mithilfe des freiwilligen Migrationstools
description: Erfahren Sie, wie Sie mithilfe des freiwilligen Migrationstools Ihre klassischen Warnungsregeln migrieren können.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5149c20431155b92aa6dab3b976fe68e6d9cb8c2
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153949"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Verwenden des freiwilligen Migrationstools zum Migrieren Ihrer klassischen Warnungsregeln

Wie [bereits angekündigt](monitoring-classic-retirement.md) werden klassische Warnungen in Azure Monitor im September 2019 (ursprünglich Juli 2019) eingestellt. Im Azure-Portal steht ein Migrationstool für Kunden bereit, die klassische Warnungsregeln verwenden und die Migration selbst auslösen möchten. In diesem Artikel wird erläutert, wie Sie mithilfe des Migrationstools Ihre klassischen Warnungsregeln vor Beginn der automatischen Migration im September 2019 freiwillig migrieren.

> [!NOTE]
> Aufgrund einer Verzögerung beim Rollout des Migrationstools wurde der Deaktivierungstermin vom ursprünglich angekündigten Datum, dem 30. Juni 2019, auf den [31. August 2019 verschoben](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/).

## <a name="benefits-of-new-alerts"></a>Vorteile der neuen Warnungen

Klassische Warnungen werden durch neue einheitliche Warnungen in Azure Monitor ersetzt. Die neue Warnungsplattform bietet die folgenden Vorteile:

- Sie können Warnungen für eine Vielzahl mehrdimensionaler Metriken für [viele weitere Azure-Dienste](alerts-metric-near-real-time.md#metrics-and-dimensions-supported) ausgeben.
- Die neuen Metrikwarnungen unterstützen [Warnungsregeln für mehrere Ressourcen](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor), sodass der erforderliche Aufwand zur Verwaltung vieler Regeln erheblich reduziert wird.
- Einheitlicher Benachrichtigungsmechanismus, mit dem Folgendes unterstützt wird:
  - [Aktionsgruppen](action-groups.md), ein modularer Benachrichtigungsmechanismus, der mit allen neuen Warnungstypen (Metrik, Protokoll und Aktivitätsprotokoll) verwendet werden kann.
  - Neue Benachrichtigungsmechanismen wie SMS, Sprache und ITSM-Connector.
- In der [einheitlichen Oberfläche für Warnungen](alerts-overview.md) werden alle Warnungen für unterschiedliche Signale (Metrik, Protokoll und Aktivitätsprotokoll) an einem zentralen Ort zusammengeführt.

## <a name="before-you-migrate"></a>Vor der Migration

Im Migrationsprozess werden klassische Warnungsregeln in neue entsprechende Warnungsregeln umgewandelt und Aktionsgruppen erstellt. Bei der Vorbereitung sind folgende Punkte zu beachten:

- Das Format der Benachrichtigungsnutzlast sowie die APIs zum Erstellen und Verwalten neuer Warnungsregeln unterscheiden sich von denen der klassischen Warnungsregeln, da mehr Funktionen unterstützt werden. [Erfahren Sie, wie Sie die Migration vorbereiten](alerts-prepare-migration.md).

- Einige klassische Warnungsregeln können mit dem Tool nicht migriert werden. [Erfahren Sie, welche Regeln nicht migriert werden können und wie Sie mit diesen Regeln verfahren](alerts-understand-migration.md#which-classic-alert-rules-cannot-be-migrated).

    > [!NOTE]
    > Der Migrationsprozess hat keine Auswirkung auf die Auswertung Ihrer klassischen Warnungsregeln. Diese werden weiterhin ausgeführt und senden Warnungen, bis sie migriert werden und die neuen Warnungsregeln wirksam werden.

## <a name="how-to-use-the-migration-tool"></a>Verwenden des Migrationstools

Gehen Sie folgendermaßen vor, um die Migration Ihrer klassischen Warnungsregeln im Azure-Portal auszulösen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Monitor** aus.

1. Wählen Sie **Warnungen** und dann **Warnungsregeln verwalten** oder **Klassische Warnungen anzeigen** aus.

1. Wählen Sie **Zu neuen Regeln migrieren** aus, um zur Zielseite der Migration zu wechseln. Auf dieser Seite werden alle Ihre Abonnements und der jeweilige Migrationsstatus angezeigt:

    ![Landing Page der Migration](media/alerts-migration/migration-landing.png "Regeln migrieren")

    Alle Abonnements, die mit dem Tool migriert werden können, sind als **Bereit zur Migration** gekennzeichnet.

    > [!NOTE]
    > Der Rollout des Migrationstools erfolgt in mehreren Phasen für alle Abonnements, die klassische Warnungsregeln verwenden. In den frühen Phasen des Rollouts werden einige Abonnements möglicherweise als nicht bereit zur Migration angezeigt.

1. Wählen Sie ein oder mehrere Abonnements und dann **Migrationsvorschau anzeigen** aus.

    Auf der dann geöffneten Seite werden die Details der klassischen Warnungsregeln angezeigt, die jeweils für ein Abonnement migriert werden. Sie können auch **Migrationsdetails für dieses Abonnement herunterladen** auswählen, um die Details im CSV-Format herunterzuladen.

    ![Migrationsvorschau](media/alerts-migration/migration-preview.png "Migrationsvorschau anzeigen")

1. Geben Sie eine oder mehrere E-Mail-Adressen an, an die Benachrichtigungen zum Migrationsstatus gesendet werden sollen. Sie erhalten eine E-Mail, wenn die Migration abgeschlossen oder eine Aktion von Ihnen erforderlich ist.

1. Wählen Sie **Migration starten** aus. Lesen Sie die Informationen im Bestätigungsdialogfeld, und bestätigen Sie, dass der Migrationsprozess gestartet werden kann.

    > [!IMPORTANT]
    > Nachdem Sie die Migration für ein Abonnement initiiert haben, können Sie für das Abonnement keine klassischen Warnungsregeln mehr bearbeiten oder erstellen. Durch diese Einschränkung wird sichergestellt, dass bei der Migration zu den neuen Regeln keine Änderungen an den klassischen Warnungsregeln verloren gehen. Obwohl Sie Ihre klassischen Warnungsregeln nicht ändern können, werden sie weiterhin ausgeführt und stellen Warnungen bereit, bis sie migriert wurden. Nachdem die Migration für Ihr Abonnement abgeschlossen ist, können Sie keine klassischen Warnungsregeln mehr verwenden.

    ![Migrationsbestätigung](media/alerts-migration/migration-confirm.png "Migrationsstart bestätigen")

1. Wenn die Migration abgeschlossen oder eine Aktion von Ihnen erforderlich ist, erhalten Sie eine E-Mail an die zuvor angegebenen E-Mail-Adressen. Sie können den Status auch regelmäßig auf der Zielseite der Migration im Portal überprüfen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Warum ist mein Abonnement als nicht bereit zur Migration aufgeführt?

Der Rollout des Migrationstools an Kunden erfolgt in mehreren Phasen. In den frühen Phasen sind die meisten oder alle Ihre Abonnements möglicherweise als **Nicht bereit zur Migration** gekennzeichnet. 

Wenn ein Abonnement zur Migration bereit ist, wird der Abonnementbesitzer per E-Mail über die Verfügbarkeit des Tools benachrichtigt. Achten Sie auf diese E-Mail-Nachricht.

### <a name="who-can-trigger-the-migration"></a>Wer kann die Migration auslösen?

Benutzer, denen die Rolle „Überwachungsmitwirkender“ auf Abonnementebene zugewiesen ist, können die Migration auslösen. [Weitere Informationen zur rollenbasierten Zugriffssteuerung für den Migrationsprozess](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Wie lange dauert die Migration?

Bei den meisten Abonnements ist die Migration nach weniger als einer Stunde abgeschlossen. Sie können den Migrationsfortschritt auf der Zielseite der Migration verfolgen. Sie können sicher sein, dass Ihre Warnungen während der Migration entweder im klassischen Warnungssystem oder im neuen System weiterhin ausgeführt werden.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>Was kann ich tun, wenn während der Migration ein Problem auftritt?

Im [Leitfaden zur Problembehandlung](alerts-understand-migration.md#common-problems-and-remedies) finden Sie Informationen zu Problemen, die während der Migration auftreten können. Wenn zum Abschließen der Migration eine Aktion von Ihnen erforderlich ist, werden Sie über die E-Mail-Adressen, die Sie beim Einrichten des Tools angegeben haben, darüber benachrichtigt.

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Migration](alerts-prepare-migration.md)
- [Funktionsweise des Migrationstools](alerts-understand-migration.md)
