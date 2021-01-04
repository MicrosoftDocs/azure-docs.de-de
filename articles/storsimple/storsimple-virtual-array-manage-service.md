---
title: Bereitstellen des StorSimple-Geräte-Manager-Diensts | Microsoft-Dokumentation
description: Erläutert das Erstellen und Löschen des StorSimple-Geräte-Manager-Diensts im Azure-Portal und beschreibt die Verwaltung des Dienstregistrierungsschlüssels.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 28499494-8c4d-4a7f-9d44-94b2b8a93c93
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 92041be4d3d5ef4d980c17fa0a5870b740c1784a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995006"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-virtual-array"></a>Bereitstellen des StorSimple-Geräte-Manager-Diensts für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er verfügt über Verbindungen mit mehreren StorSimple-Geräten. Nachdem Sie den Dienst erstellt haben, können Sie ihn zum Verwalten der Geräte über das in einem Browser ausgeführte Microsoft Azure-Portal verwenden. So können Sie alle Geräte, die mit den StorSimple-Geräte-Manager-Dienst verbunden sind, von einem zentralen Standort aus überwachen und damit den Verwaltungsaufwand minimieren.

Zu den allgemeinen Aufgaben im Zusammenhang mit einem StorSimple-Geräte-Manager-Dienst zählen:

* Erstellen von Diensten
* Löschen von Diensten
* Abrufen des Dienstregistrierungsschlüssels
* Neugenerieren des Dienstregistrierungsschlüssels

In diesem Tutorial wird beschrieben, wie diese Aufgaben durchgeführt werden. Die in diesem Artikel enthaltenen Informationen gelten nur für StorSimple Virtual Arrays. Weitere Informationen zur StorSimple 8000-Serie finden Sie unter [Bereitstellen des StorSimple Manager-Diensts](./storsimple-8000-manage-service.md).

## <a name="create-a-service"></a>Erstellen von Diensten

Für das Erstellen eines Diensts ist Folgendes erforderlich:

* Ein Abonnement mit einem Enterprise Agreement
* Ein aktives Microsoft Azure-Speicherkonto
* Die Abrechnungsinformationen für die Zugriffsverwaltung

Sie können beim Erstellen des Diensts auch ein Speicherkonto generieren.

Mit einem Dienst können mehrere Geräte verwaltet werden. Ein Gerät kann jedoch nicht mehrere Dienste umfassen. Große Unternehmen können mit mehreren Dienstinstanzen mit verschiedenen Abonnements, Organisationen oder sogar Bereitstellungsstandorten arbeiten.

> [!NOTE]
> Sie benötigen separate Instanzen des StorSimple-Geräte-Manager-Diensts, um StorSimple-Geräte der 8000er-Serie und StorSimple Virtual Arrays zu verwalten.


Führen Sie die folgenden Schritte aus, um einen Dienst zu erstellen.

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

## <a name="delete-a-service"></a>Löschen von Diensten

Bevor Sie einen Dienst löschen, stellen Sie sicher, dass er von keinen verbundenen Geräten verwendet wird. Wenn der Dienst verwendet wird, deaktivieren Sie die verbundenen Geräte. Der deaktivierte Vorgang trennt die Verbindung zwischen dem Gerät und dem Dienst, behält aber die Gerätedaten in der Cloud bei.

> [!IMPORTANT]
> Das Löschen eines Diensts kann nicht rückgängig gemacht werden. Jedes Gerät, das den Dienst verwendet hat, muss auf die Werkseinstellungen zurückgesetzt werden, bevor es mit einem anderen Dienst verwendet werden kann. In diesem Szenario gehen sowohl die lokalen Daten auf dem Gerät als auch die Konfiguration verloren.
 

Führen Sie die folgenden Schritte aus, um einen Dienst zu löschen.

#### <a name="to-delete-a-service"></a>So löschen Sie einen Dienst

1. Wechseln Sie zu **All resources** (Alle Ressourcen). Suchen Sie nach Ihrem StorSimple-Geräte-Manager-Dienst. Wählen Sie den Dienst aus, den Sie löschen möchten.
   
    ![Zu löschenden Dienst auswählen](./media/storsimple-virtual-array-manage-service/deleteservice2.png)
2. Wechseln Sie zum Dienst-Dashboard, um sicherzustellen, dass keine Geräte mit dem Dienst verbunden sind. Wenn keine Geräte bei diesem Dienst angemeldet sind, erhalten Sie einen entsprechenden Meldungsbanner. Klicken Sie auf **Löschen**.
   
    ![Suchdienst löschen](./media/storsimple-virtual-array-manage-service/deleteservice3.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie in der Bestätigungsbenachrichtigung auf **Ja**. 
   
    ![Löschen des Diensts bestätigen](./media/storsimple-virtual-array-manage-service/deleteservice4.png)
4. Es dauert einige Minuten, bis der Dienst gelöscht wird. Nachdem der Dienst erfolgreich gelöscht wurde, werden Sie benachrichtigt.
   
    ![Erfolgreiches Löschen des Diensts](./media/storsimple-virtual-array-manage-service/deleteservice6.png)

Die Liste der Dienste wird aktualisiert.

 ![Aktualisierte Liste der Dienste](./media/storsimple-virtual-array-manage-service/deleteservice7.png)

## <a name="get-the-service-registration-key"></a>Abrufen des Dienstregistrierungsschlüssels
Nachdem Sie einen Dienst erstellt haben, müssen Sie Ihr StorSimple-Gerät bei dem Dienst registrieren. Um Ihr erstes StorSimple-Gerät zu registrieren, benötigen Sie den Dienstregistrierungsschlüssel. Um zusätzliche Geräte bei einem vorhandenen StorSimple-Dienst zu registrieren, benötigen Sie den Registrierungsschlüssel und den Verschlüsselungsschlüssel für Dienstdaten (die während der Registrierung auf dem ersten Gerät generiert werden). Weitere Informationen zum Verschlüsselungsschlüssel für Dienstdaten finden Sie unter [StorSimple-Sicherheit](./storsimple-8000-security.md). Sie erhalten den Registrierungsschlüssel, indem Sie auf das Blatt **Schlüssel** für Ihren Dienst zugreifen.

Führen Sie die folgenden Schritte durch, um den Dienstregistrierungsschlüssel abzurufen.

#### <a name="to-get-the-service-registration-key"></a>So rufen Sie den Dienstregistrierungsschlüssel ab
1. Wechseln Sie auf dem Blatt **StorSimple-Geräte-Manager** zu **Management &gt;** **Schlüssel**.
   
   ![Blatt „Schlüssel“](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Auf dem Blatt **Schlüssel** wird ein Dienstregistrierungsschlüssel angezeigt. Kopieren Sie den Registrierungsschlüssel mithilfe des Symbols für Kopieren. 

Bewahren Sie den Dienstregistrierungsschlüssel an einem sicheren Ort auf. Sie benötigen diesen Schlüssel sowie den Verschlüsselungsschlüssel für Dienstdaten, um zusätzliche Geräte bei diesem Dienst zu registrieren. Nach dem Abrufen des Dienstregistrierungsschlüssels müssen Sie das Gerät mithilfe von Windows PowerShell für StorSimple konfigurieren.

## <a name="regenerate-the-service-registration-key"></a>Neugenerieren des Dienstregistrierungsschlüssels
Sie müssen den Dienstregistrierungsschlüssel neu generieren, wenn Sie die Schlüsselrotation durchführen müssen oder wenn sich die Liste der Dienstadministratoren geändert hat. Wenn Sie den Schlüssel neu generieren, wird der neue Schlüssel nur für die Registrierung nachfolgender Geräte verwendet. Bereits registrierte Geräte sind von diesem Vorgang nicht betroffen.

Führen Sie die folgenden Schritte durch, um den Dienstregistrierungsschlüssel neu zu generieren.

#### <a name="to-regenerate-the-service-registration-key"></a>So generieren Sie den Dienstregistrierungsschlüssel neu
1. Wechseln Sie auf dem Blatt **StorSimple-Geräte-Manager** zu **Management &gt;** **Schlüssel**.
   
   ![Blatt „Schlüssel“ (erneut)](./media/storsimple-virtual-array-manage-service/getregkey2.png)
2. Klicken Sie auf dem Blatt **Schlüssel** auf **Neu generieren**.
   
   ![Klicken Sie auf „Neu generieren“.](./media/storsimple-virtual-array-manage-service/getregkey5.png)
3. Überprüfen Sie auf dem Blatt **Dienstregistrierungsschlüssel erneut generieren**, welche Aktion erforderlich ist, wenn die Schlüssel neu generiert werden. Alle weiteren Geräte, die bei diesem Dienst angemeldet werden, verwenden den neuen Registrierungsschlüssel. Klicken Sie zum Bestätigen auf **Regenerieren**. Sie werden benachrichtigt, sobald die Registrierung abgeschlossen ist.
   
   ![Erneutes Generierens des Schlüssels bestätigen](./media/storsimple-virtual-array-manage-service/getregkey3.png)
4. Ein neuer Dienstregistrierungsschlüssel wird angezeigt.
   
    ![Erneutes Generieren eines zusätzlichen Schlüssels bestätigen](./media/storsimple-virtual-array-manage-service/getregkey4.png)
   
   Kopieren Sie diesen Schlüssel, und speichern Sie ihn für die Registrierung neuer Geräte bei diesem Dienst.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr zu den [ersten Schritten](storsimple-virtual-array-deploy1-portal-prep.md) mit einem StorSimple Virtual Array.
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät verwalten](storsimple-ova-web-ui-admin.md).