---
title: Ändern des Geräteadministratorkennworts für das StorSimple Virtual Array | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie das Azure-Portal oder die StorSimple Virtual Array-Webbenutzeroberfläche verwenden können, um das Geräteadministratorkennwort zu ändern.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580363"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>Ändern des StorSimple Virtual Array-Geräteadministratorkennworts mit dem StorSimple-Geräte-Manager

## <a name="overview"></a>Übersicht

Wenn Sie über die Windows PowerShell-Benutzeroberfläche auf das StorSimple Virtual Array zugreifen, müssen Sie ein Geräteadministratorkennwort eingeben. Wenn das StorSimple-Gerät zum ersten Mal bereitgestellt und gestartet wird, lautet das Standardkennwort *Password1*. Um Ihre Daten zu schützen, läuft das Standardkennwort bei der ersten Anmeldung ab, und Sie müssen dieses Kennwort ändern.

Sie können jederzeit entweder die lokale Webbenutzeroberfläche oder das Azure-Portal nutzen, um das Geräteadministratorkennwort nach der Bereitstellung des Geräts in Ihrer Produktionsumgebung zu ändern. Die einzelnen Schritte werden in diesem Artikel beschrieben.

 ![Blatt „Geräte“](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Ändern des Kennworts über das Azure-Portal

Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort über das Azure-Portal zu ändern.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>So ändern Sie das Geräteadministratorkennwort über das Azure-Portal

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, und doppelklicken Sie auf den Namen. Klicken Sie anschließend im Abschnitt **Verwaltung** auf **Geräte**. Daraufhin wird das Blatt **Geräte** geöffnet, das all Ihre Geräte des StorSimple Virtual Array auflistet.

2. Doppelklicken Sie auf dem Blatt **Geräte** auf das Gerät, bei dem eine Änderung des Kennworts notwendig ist.

3. Klicken Sie auf dem Blatt **Einstellungen** für Ihr Gerät auf **Sicherheit**.

4. Gehen Sie auf dem Blatt **Sicherheitseinstellungen** wie folgt vor:
   
   1. Scrollen Sie bis zum Abschnitt **Geräteadministratorkennwort** nach unten. Geben Sie ein Administratorkennwort ein, das zwischen 8 und 15 Zeichen lang ist.
   2. Bestätigen Sie das Kennwort.
   3. Klicken Sie oben auf dem Blatt auf **Speichern** .

Das Geräteadministratorkennwort ist jetzt aktualisiert. Sie können dieses geänderte Kennwort verwenden, um lokal auf das Gerät zuzugreifen.

![Blatt „Sicherheitseinstellungen“](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Verwenden der lokalen Webbenutzeroberfläche zum Ändern des Kennworts

Führen Sie die folgenden Schritte aus, um das Geräteadministratorkennwort über die lokale Webbenutzeroberfläche zu ändern.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>So ändern Sie das Geräteadministratorkennwort über die lokale Webbenutzeroberfläche

1. Klicken Sie auf der lokalen Webbenutzeroberfläche für Ihr Gerät auf **Wartung** > **Kennwortänderung**.
   
    ![password1 ändern](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Geben Sie das **aktuelle Kennwort**ein.
3. Geben Sie ein **neues Kennwort**ein. Das Kennwort muss mindestens 8 Zeichen umfassen. Das Kennwort muss drei der vier folgenden Elemente enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen.
   
    Hinweis: Ihr Kennwort muss sich von den letzten 24 Kennwörtern unterscheiden.
4. Geben Sie das Kennwort erneut ein, um es zu bestätigen.
   
    ![password2 ändern](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Klicken Sie unten auf der Seite auf **Übernehmen**. Das neue Kennwort wurde übernommen. War die Änderung des Kennworts nicht erfolgreich, wird der folgende Fehler angezeigt:
   
    ![Kennwortfehler](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Wenn das Kennwort erfolgreich aktualisiert wurde, werden Sie benachrichtigt. Sie können dieses geänderte Kennwort anschließend verwenden, um lokal auf das Gerät zuzugreifen.


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [Ihr StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).

