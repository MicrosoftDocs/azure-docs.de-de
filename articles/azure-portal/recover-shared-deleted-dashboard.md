---
title: Wiederherstellen eines gelöschten Dashboards im Azure-Portal
description: Wenn Sie ein veröffentlichtes Dashboard im Azure-Portal löschen, können Sie das Dashboard wiederherstellen.
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 095964691a3cb22f8a805af2e8fe37af4c47cb28
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745620"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Wiederherstellen eines gelöschten Dashboards im Azure-Portal

Wenn Sie in der öffentlichen Azure-Cloud ein _veröffentlichtes_ Dashboard im Azure-Portal löschen, können Sie das Dashboard innerhalb von 14 Tagen nach dem Löschvorgang wiederherstellen. Wenn Sie eine Azure Government-Cloud nutzen oder das Dashboard nicht veröffentlicht ist, können Sie es nicht wiederherstellen und müssen es neu erstellen. Weitere Informationen zum Veröffentlichen eines Dashboards finden Sie unter [Veröffentlichen des Dashboards](azure-portal-dashboard-share-access.md#publish-dashboard). Führen Sie diese Schritte aus, um ein veröffentlichtes Dashboard wiederherzustellen:

1. Wählen Sie im Menü des Azure-Portals **Ressourcengruppen** und dann die Ressourcengruppe aus, in der Sie das Dashboard veröffentlicht haben (standardmäßig heißt es **Dashboards**).

1. Erweitern Sie unter **Aktivitätsprotokoll** den Vorgang **Dashboard löschen**. Wählen Sie die Registerkarte **Änderungsverlauf** und dann **\<deleted resource\>** aus.

    ![Screenshot der Registerkarte „Änderungsverlauf“](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. Wählen Sie den Inhalt des linken Bereichs aus, kopieren Sie ihn, und speichern Sie ihn in einer Textdatei mit der Dateierweiterung _JSON_. Das Portal verwendet die JSON-Datei, um das Dashboard neu zu erstellen.

    ![Screenshot der Registerkarte „Änderungsverlauf“ (Unterschiede)](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. Wählen Sie im Menü des Azure-Portals **Dashboards** und dann **Hochladen** aus.

    ![Screenshot des Dashboarduploads](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. Wählen Sie die JSON-Datei aus, die Sie gespeichert haben. Das Portal erstellt das Dashboard mit dem gleichen Namen und den gleichen Elementen wie das gelöschte Dashboard neu.

1. Wählen Sie **Freigeben** aus, um das Dashboard zu veröffentlichen und die entsprechende Zugriffssteuerung wiederherzustellen.

    ![Screenshot der Dashboardfreigabe](media/recover-shared-deleted-dashboard/dashboard-share.png)
