---
title: Verwalten von Zugriffssteuerungsdatensätzen in StorSimple | Microsoft Docs
description: Beschreibt, wie Sie mit Zugriffssteuerungsdatensätzen (Access Control Record, ACR) angeben, welche Hosts Verbindungen mit einem Volume auf dem StorSimple-Gerät herstellen können.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693237"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Verwalten von Zugriffssteuerungsdatensätzen mithilfe des StorSimple Manager-Diensts

## <a name="overview"></a>Übersicht
Mit Zugriffssteuerungsdatensätzen (Access Control Record, ACR) können Sie angeben, welche Hosts Verbindungen mit einem Volume auf dem StorSimple-Gerät herstellen können. ACRs sind auf ein bestimmtes Volume festgelegt. Sie enthalten die qualifizierten iSCSI-Namen (IQN) der Hosts. Wenn ein Host versucht, eine Verbindung mit einem Volume herzustellen, überprüft das Gerät den ACR zu diesem Volume anhand des IQN-Namens. Bei einer Übereinstimmung wird eine Verbindung hergestellt. Auf dem Blatt „Access Control-Datensätze“ im Abschnitt **Konfiguration** Ihres StorSimple-Geräte-Manager-Diensts werden alle Access Control-Datensätze mit den jeweiligen IQNs der Hosts angezeigt.

In diesem Tutorial werden die folgenden häufig durchgeführten ACR-bezogenen Aufgaben erläutert:

* Hinzufügen von Zugriffssteuerungsdatensätzen
* Bearbeiten von Zugriffssteuerungsdatensätzen
* Löschen von Access Control-Datensätzen

> [!IMPORTANT]
> * Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte.
> * Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.

## <a name="get-the-iqn"></a>Abrufen des IQN

Führen Sie die folgenden Schritte aus, um den IQN eines Windows-Hosts abzurufen, auf dem Windows Server 2012 ausgeführt wird.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Hinzufügen von Zugriffssteuerungsdatensätzen
Im Abschnitt **Konfiguration** auf dem Blatt des StorSimple-Geräte-Managers können Sie ACRs hinzufügen. In der Regel ordnen Sie einem Volume einen ACR zu.

Führen Sie die folgenden Schritte aus, um einen ACR hinzuzufügen:

#### <a name="to-add-an-acr"></a>So fügen Sie einen ACR hinzu

1. Öffnen Sie Ihren StorSimple-Geräte-Manager-Dienst, und doppelklicken Sie auf den Dienstnamen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf **Access Control-Datensätze**.
2. Klicken Sie auf dem Blatt **Access Control-Datensätze** auf **+ ACR hinzufügen**.

    ![Klicken Sie auf „ACR hinzufügen“](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Gehen Sie auf dem Blatt **ACR hinzufügen** wie folgt vor:

    1. Geben Sie einen Namen für den ACR ein.
    
    2. Geben Sie unter **iSCSI-Initiatorname (IQN)** den qualifizierten IQN-Namen Ihres Windows Server-Hosts an.

    3. Klicken Sie auf **Hinzufügen**, um den ACR hinzuzufügen.

        ![Klicken Sie auf „ACR hinzufügen“](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Der neu hinzugefügte ACR wird in der Tabelle mit den ACRs angezeigt.

    ![Klicken Sie auf „ACR hinzufügen“](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Bearbeiten von Zugriffssteuerungsdatensätzen
Im Abschnitt **Konfiguration** auf dem Blatt des StorSimple-Geräte-Managers können Sie ACRs bearbeiten.

> [!NOTE]
> Sie sollten nur die ACRs ändern, die derzeit nicht verwendet werden. Zum Bearbeiten eines ACR, der einem das momentan verwendeten Volume zugeordnet ist, müssen Sie zunächst das Volume offline schalten.

Führen Sie die folgenden Schritte aus, um einen ACR zu bearbeiten:

#### <a name="to-edit-an-access-control-record"></a>So bearbeiten Sie einen Zugriffssteuerungsdatensatz
1.  Öffnen Sie Ihren StorSimple-Geräte-Manager-Dienst, und doppelklicken Sie auf den Dienstnamen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf **Access Control-Datensätze**.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Wählen Sie in der Tabelle mit den Zugriffssteuerungsdatensätzen den zu ändernden ACR aus.

    ![Bearbeiten von Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Geben Sie auf dem Blatt **Access Control-Datensatz bearbeiten** einen anderen IQN an, der einem anderen Host entspricht.

    ![Bearbeiten von Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. 

    ![Bearbeiten von Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Sie werden benachrichtigt, sobald der ACR aktualisiert ist. Die Tabelle wird ebenfalls den Änderungen entsprechend aktualisiert.

   
## <a name="delete-an-access-control-record"></a>Löschen von Access Control-Datensätzen
Im Abschnitt **Konfiguration** auf dem Blatt des StorSimple-Geräte-Managers können Sie ACRs löschen.

> [!NOTE]
> Sie können nur die ACRs löschen, die derzeit nicht verwendet werden. Zum Löschen eines ACR, der einem momentan verwendeten Volume zugeordnet ist, müssen Sie zunächst das Volume offline schalten.

Führen Sie die folgenden Schritte aus, um einen Zugriffssteuerungsdatensatz zu löschen.

#### <a name="to-delete-an-access-control-record"></a>So löschen Sie einen Zugriffssteuerungsdatensatz
1.  Öffnen Sie Ihren StorSimple-Geräte-Manager-Dienst, und doppelklicken Sie auf den Dienstnamen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf **Access Control-Datensätze**.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Wählen Sie in der Tabelle mit den Access Control-Datensätzen den zu löschenden ACR aus.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Löschen** aus.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, überprüfen Sie die Informationen, und klicken Sie dann auf **Löschen**.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Sie werden benachrichtigt, sobald der Löschvorgang abgeschlossen ist. Die tabellarische Auflistung wird nach dem Löschen entsprechend aktualisiert.

    ![Wechseln zu den Access Control-Datensätzen](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwalten von StorSimple-Volumes](storsimple-8000-manage-volumes-u2.md).
* Erfahren Sie mehr über das [Verwenden Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-8000-manager-service-administration.md).

