---
title: Ändern Ihres StorSimple-Kennworts | Microsoft Docs
description: Beschreibt, wie Sie den StorSimple-Geräte-Manager-Dienst verwenden, um Ihr StorSimple Snapshot Manager- und Geräteadministratorkennwort zu ändern.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 038084ba9ae43e14bc2eb42bf258912be27d062c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023753"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Ändern von StorSimple-Kennwörtern

## <a name="overview"></a>Übersicht
Die Option **Geräteeinstellungen** im Azure-Portal enthält alle Geräteparameter, die Sie für ein StorSimple-Gerät neu konfigurieren können, das über einen StorSimple-Geräte-Manager-Dienst verwaltet wird. In diesem Tutorial wird erläutert, wie Sie die Option **Sicherheit** unter **Geräteeinstellungen** verwenden können, um Ihr Geräteadministrator- oder StorSimple Snapshot Manager-Kennwort zu ändern.

## <a name="change-the-device-administrator-password"></a>Ändern des Geräteadministratorkennworts
Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das StorSimple-Gerät zugreifen, müssen Sie ein Geräteadministratorkennwort eingeben. Wenn das erste StorSimple-Gerät mit einem Dienst registriert ist, ist das Standardkennwort für diese Schnittstelle gleich *Kennwort1*. Für die Sicherheit Ihrer Daten müssen Sie dieses Kennwort am Ende des Registrierungsprozesses ändern. Sie können den Registrierungsprozess nicht beenden, ohne dieses Kennwort geändert zu haben. Weitere Informationen finden Sie unter [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Das Kennwort, das zuerst über die Windows PowerShell-Schnittstelle während der Registrierung festgelegt wurde, kann später im Azure-Portal geändert werden. Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort zu ändern.

#### <a name="to-change-the-device-administrator-password"></a>So ändern Sie das Geräteadministratorkennwort
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**.

2. Klicken Sie in der Tabelle mit den Geräten auf das Gerät, dessen Kennwort Sie ändern möchten.

    ![Screenshot, der den StorSimple-Geräte-Manager-Dienst zeigt. Unter „Verwaltung“ ist „Geräte“ ausgewählt. In der Geräteliste ist ein Gerät ausgewählt.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Wechseln Sie auf dem Blatt **Einstellungen** zu **Geräteeinstellungen > Sicherheit**.

    ![Screenshot, der das Blatt „Einstellungen“ des Geräte-Manager-Diensts zeigt. Unter „Geräteeinstellungen“ ist „Sicherheit“ ausgewählt.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **Kennwort**, um das Geräteadministratorkennwort zu ändern.

    ![Screenshot, der das Blatt „Sicherheitseinstellungen“ zeigt. Die Schaltfläche „Kennwort“ ist hervorgehoben.](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Geben Sie auf dem Blatt **Kennwort** ein Administratorkennwort ein, das 8 bis 15 Zeichen lang ist. Beim Kennwort muss es sich um eine Kombination aus mindestens drei der Elemente Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen handeln.

6. Bestätigen Sie das Kennwort.

    ![Screenshot, der das Blatt „Kennwort“ zeigt. Unter „Geräteadministratorkennwort“ sind die Felder „Neues Kennwort“ und „Kennwort bestätigen“ ausgefüllt.](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

    ![Screenshot, der das Blatt „Kennwort“ zeigt. Die Schaltfläche „Speichern“ ist hervorgehoben.](./media/storsimple-8000-change-passwords/changepwd6.png)

Das Geräteadministratorkennwort wurde jetzt aktualisiert. Sie können dieses geänderte Kennwort verwenden, um auf die Windows PowerShell-Schnittstelle zugreifen.

## <a name="set-the-storsimple-snapshot-manager-password"></a>Festlegen des StorSimple Snapshot Manager-Kennworts
Der StorSimple-Momentaufnahme-Manager befindet sich auf dem Windows-Host und ermöglicht Administratoren die Verwaltung der Sicherungen Ihres StorSimple-Geräts in Form von lokalen und Cloudmomentaufnahmen.

Beim Konfigurieren eines Geräts im StorSimple Snapshot Manager werden Sie aufgefordert, zur Authentifizierung des Speichergeräts die IP-Adresse und das Kennwort des Geräts anzugeben.

Sie können das Kennwort für StorSimple Snapshot Manager im Azure-Portal festlegen oder ändern. Führen Sie die folgenden Schritte aus, um das Kennwort für StorSimple Snapshot Manager festzulegen oder zu ändern.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>So legen sie das StorSimple Snapshot Manager-Kennwort fest
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte**.

2. Klicken Sie in der Tabelle mit den Geräten auf das Gerät, dessen StorSimple Snapshot Manager-Kennwort Sie ändern möchten.

     ![Screenshot, der den StorSimple-Geräte-Manager-Dienst zeigt. Unter „Verwaltung“ ist „Geräte“ ausgewählt. In der Geräteliste ist ein Gerät ausgewählt.](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Wechseln Sie auf dem Blatt **Einstellungen** zu **Geräteeinstellungen > Sicherheit**.

     ![Screenshot, der das Blatt „Einstellungen“ des Geräte-Manager-Diensts zeigt. Unter „Geräteeinstellungen“ ist „Sicherheit“ ausgewählt.](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Klicken Sie auf dem Blatt **Sicherheitseinstellungen** auf **Kennwort**, um das Kennwort für StorSimple Snapshot Manager festzulegen oder zu ändern.

     ![Screenshot, der das Blatt „Sicherheitseinstellungen“ zeigt. Die Schaltfläche „Kennwort“ ist hervorgehoben.](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Geben Sie auf dem Blatt **Kennwort** ein Kennwort mit einer Länge von 14 oder 15 Zeichen ein. Stellen Sie sicher, dass das Kennwort eine Kombination aus mindestens drei der Elemente Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen enthält.

6. Bestätigen Sie das Kennwort.

     ![Screenshot, der das Blatt „Kennwort“ zeigt. Unter „Snapshot Manager-Kennwort“ sind die Felder „Neues Kennwort“ und „Kennwort bestätigen“ ausgefüllt.](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

     ![Screenshot, der das Blatt „Kennwort“ zeigt. Die Schaltfläche „Speichern“ ist hervorgehoben.](./media/storsimple-8000-change-passwords/changepwd6.png)

Das Kennwort für StorSimple Snapshot Manager sollte jetzt aktualisiert sein.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur [StorSimple-Sicherheit](storsimple-8000-security.md)
* Weitere Informationen zum [Ändern Ihrer Gerätekonfiguration](storsimple-8000-modify-device-config.md).
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

