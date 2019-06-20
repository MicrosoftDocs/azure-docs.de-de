---
title: include file
description: include file
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178138"
---
Zum Löschen eines Volumecontainers müssen Sie
 - die Volumes im Volumecontainer löschen. Wenn dem Volumecontainer Volumes zugeordnet sind, schalten Sie diese zuerst offline. Führen Sie die unter [Offlineschalten von Volumes](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)genannten Schritte aus. Wenn die Volumes offline sind, können Sie sie löschen. 
 - die zugeordneten Sicherungsrichtlinien und Cloudmomentaufnahmen löschen. Überprüfen Sie, ob dem Volumecontainer Sicherungsrichtlinien und Cloudmomentaufnahmen zugeordnet sind. Wenn dies der Fall ist, [löschen Sie die Sicherungsrichtlinien](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Damit werden auch die Cloudmomentaufnahmen gelöscht. 
 
Wenn dem Volumecontainer keine Volumes, Sicherungsrichtlinien und Cloudmomentaufnahmen zugeordnet sind, können Sie ihn löschen. Gehen Sie folgendermaßen vor, um einen Volumecontainer zu löschen.

#### <a name="to-delete-a-volume-container"></a>So löschen Sie einen Volumecontainer
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**. Wählen Sie das Gerät aus, klicken Sie darauf, und navigieren Sie zu **Einstellungen > Verwalten > Volumecontainer**.

    ![Blatt „Volumecontainer“](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Wählen Sie in der tabellarischen Volumecontainerliste den Volumecontainer aus, den Sie löschen möchten, klicken Sie mit der rechten Maustaste auf **...** , und wählen Sie dann **Löschen**.

    ![Volumecontainer löschen](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Wenn einem Volumecontainer keine Volumes, Sicherungsrichtlinien und Cloudmomentaufnahmen zugeordnet sind, kann er gelöscht werden. Wenn Sie zur Bestätigung aufgefordert werden, überprüfen Sie das Kontrollkästchen mit den Informationen zu den Auswirkungen der Volumecontainer-Löschung und aktivieren es. Klicken Sie dann auf **Löschen**, um den Volumecontainer zu löschen.

    ![Löschvorgang bestätigen](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

Die Liste der Volumecontainer wird dem gelöschten Volumecontainer entsprechend aktualisiert.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


