---
title: Verhindern des versehentlichen Löschens von Azure-Dateifreigaben
description: Erfahren Sie, wie Sie das vorläufige Löschen für Azure-Dateifreigaben aktivieren, um die Wiederherstellung von Daten zu ermöglichen und versehentliche Löschvorgänge zu verhindern.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 0fecc9fc954a1ac648e8f60badf69ad1d2e8f1cc
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126939"
---
# <a name="prevent-accidental-deletion-of-azure-file-shares"></a>Verhindern eines versehentlichen Löschens von Azure-Dateifreigaben

Azure Storage bietet jetzt für Dateifreigaben vorläufiges Löschen. Dadurch können Sie Ihre Dateifreigabe leichter wiederherstellen, wenn diese irrtümlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos gelöscht wurden.

## <a name="how-soft-delete-works"></a>Funktionsweise des vorläufigen Löschens

Wenn vorläufiges Löschen für Azure-Dateifreigaben aktiviert ist und eine Dateifreigabe gelöscht wird, wechselt sie in einen vorläufig gelöschten Zustand, anstatt dauerhaft gelöscht zu werden. Sie können den Zeitraum konfigurieren, in dem vorläufig gelöschte Daten wiederhergestellt werden können, ehe sie dauerhaft gelöscht werden, und die Löschung der Freigabe während dieses Aufbewahrungszeitraums jederzeit rückgängig machen. Nachdem das Löschen rückgängig gemacht wurde, werden die Freigabe und ihr gesamter Inhalt, einschließlich der Momentaufnahmen, in dem Zustand wieder hergestellt, in dem sie sich vor dem Löschen befunden hatten. Vorläufiges Löschen funktioniert nur auf Ebene der Dateifreigabe – einzelne Dateien, die gelöscht werden, werden weiterhin endgültig gelöscht.

Vorläufiges Löschen kann entweder für neue oder vorhandene Dateifreigaben aktiviert werden. Da vorläufiges Löschen auch abwärtskompatibel ist, müssen Sie keine Änderungen an Ihren Anwendungen vornehmen, um in den Genuss der Schutzvorteile dieses Features zu kommen. 

Um eine Dateifreigabe mit aktiviertem vorläufigen Löschen vor dem Ablaufdatum dauerhaft zu löschen, müssen Sie das Löschen der Freigabe rückgängig machen, vorläufiges Löschen deaktivieren und dann die Freigabe erneut löschen. Anschließend müssen Sie vorläufiges Löschen reaktivieren, da alle anderen Dateifreigaben in diesem Speicherkonto anfällig für ein versehentliches Löschen sind, solange vorläufiges Löschen deaktiviert ist.

Bei vorläufig gelöschten Premium-Dateifreigaben wird das Dateifreigabekontingent (die bereitgestellte Größe einer Dateifreigabe) bei der Berechnung des gesamten Speicherkontokontingents bis zum Ablaufdatum der vorläufig gelöschten Freigabe genutzt, an dem die Freigabe vollständig gelöscht wird.

## <a name="configuration-settings"></a>Konfigurationseinstellungen

### <a name="enabling-or-disabling-soft-delete"></a>Aktivieren oder Deaktivieren des vorläufigen Löschens

Weil vorläufiges Löschen für Dateifreigaben auf Speicherkontoebene aktiviert wird, gelten die Einstellungen für vorläufiges Löschen für alle Dateifreigaben eines Speicherkontos. Sie können vorläufiges Löschen jederzeit aktivieren oder deaktivieren. Wenn Sie ein neues Speicherkonto erstellen, ist vorläufiges Löschen für Dateifreigaben standardmäßig deaktiviert. Sie können diese Funktion während der Bereitstellung oder zu jedem späteren Zeitpunkt aktivieren. Vorläufiges Löschen bleibt auch für vorhandene Speicherkonten standardmäßig deaktiviert. Wenn Sie [Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md) für eine Azure-Dateifreigabe konfiguriert haben, wird das vorläufige Löschen für Azure-Dateifreigaben automatisch für das Speicherkonto dieser Freigabe aktiviert.

Wenn Sie vorläufiges Löschen für Dateifreigaben aktivieren, einige Dateifreigaben löschen und dann vorläufiges Löschen deaktivieren, können Sie, wenn die Freigaben in diesem Zeitraum gespeichert wurden, weiterhin auf diese Dateifreigaben zugreifen und sie wiederherstellen. Wenn Sie vorläufiges Löschen aktivieren, müssen Sie auch den Aufbewahrungszeitraum festlegen.

### <a name="retention-period"></a>Beibehaltungsdauer

Der Aufbewahrungszeitraum ist die Zeitspanne, für die vorläufig gelöschte Datenfreigaben gespeichert werden und zur Wiederherstellung verfügbar sind. Für Dateifreigaben, die explizit gelöscht werden, beginnt der Aufbewahrungszeitraum, wenn die Daten gelöscht werden. Derzeit können Sie einen Aufbewahrungszeitraum zwischen 1 und 365 Tagen angeben. Sie können die Beibehaltungsdauer beim vorläufigen Löschen jederzeit ändern. Eine Änderung des Aufbewahrungszeitraums gilt nur für Freigaben, die nach erfolgter Änderung des Aufbewahrungszeitraums gelöscht werden. Freigaben, die vor der Änderung des Aufbewahrungszeitraums gelöscht wurden, laufen entsprechend dem Aufbewahrungszeitraum ab, der beim Löschen dieser Daten festgelegt war.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Sowohl Standard- als auch Premium-Dateifreigaben werden nach genutzter Kapazität in Rechnung gestellt, wenn sie vorläufig gelöscht werden, und nicht nach bereitgestellter Kapazität. Darüber hinaus werden Premium-Dateifreigaben mit dem Tarif für Momentaufnahmen in Rechnung gestellt, solange vorläufiges Löschen für sie aktiviert ist. Standarddateifreigaben werden bei aktiviertem vorläufigen Löschen mit dem Standardtarif in Rechnung gestellt. Für Daten, die nach Ablauf des festgelegten Aufbewahrungszeitraums dauerhaft gelöscht werden, fallen keine Kosten an.

Weitere Informationen zu Preisen für Azure File Storage im Allgemeinen finden Sie auf der Seite [Preise für Azure File Storage](https://azure.microsoft.com/pricing/details/storage/files/).

Wenn Sie vorläufiges Löschen erstmals aktivieren, wird ein kurzer Aufbewahrungszeitraum empfohlen, um besser zu verstehen, wie sich das Feature auf Ihre Rechnung auswirkt.

## <a name="next-steps"></a>Nächste Schritte

Um zu erfahren, wie Sie vorläufiges Löschen aktivieren und nutzen können, fahren Sie mit [Aktivieren des vorläufigen Löschens](storage-files-enable-soft-delete.md) fort.
