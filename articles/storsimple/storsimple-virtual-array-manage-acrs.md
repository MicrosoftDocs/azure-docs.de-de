---
title: Verwalten von Zugriffssteuerungsdatensätzen für das StorSimple Virtual Array | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie mit Access Control-Datensätzen (Access Control Records, ACRs) bestimmen, welche Hosts Verbindungen mit einem Volume auf dem StorSimple Virtual Array herstellen können.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724418"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Managers zum Verwalten von Access Control-Datensätzen für StorSimple Virtual Array

## <a name="overview"></a>Übersicht

Mit Access Control-Datensätzen (Access Control Records, ACRs) können Sie angeben, welche Hosts eine Verbindung mit einem Volume im StorSimple Virtual Array (auch als lokales virtuelles StorSimple-Gerät bezeichnet) herstellen können. ACRs sind auf ein bestimmtes Volume festgelegt. Sie enthalten die qualifizierten iSCSI-Namen (IQN) der Hosts. Wenn ein Host versucht, eine Verbindung mit einem Volume herzustellen, überprüft das Gerät den ACR zu diesem Volume anhand des IQN-Namens. Bei einer Übereinstimmung wird eine Verbindung hergestellt. Auf dem Blatt **Access Control-Datensätze** im Abschnitt **Konfiguration** Ihres Geräte-Manager-Diensts werden alle Access Control-Datensätze zu den jeweiligen IQNs der Hosts angezeigt.

![Verwalten von Zugriffssteuerungsdatensätzen](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

In diesem Tutorial werden die folgenden häufig durchgeführten ACR-bezogenen Aufgaben erläutert:

* Abrufen des IQN
* Hinzufügen von Zugriffssteuerungsdatensätzen
* Bearbeiten von Zugriffssteuerungsdatensätzen
* Löschen von Access Control-Datensätzen

> [!IMPORTANT]
> 
> * Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte.
> * Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.


## <a name="get-the-iqn"></a>Abrufen des IQN

Führen Sie die folgenden Schritte aus, um den IQN eines Windows-Hosts abzurufen, auf dem Windows Server 2012 ausgeführt wird.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Hinzufügen eines ACR

Verwenden Sie das Blatt **Access Control-Datensätze** im Abschnitt **Konfiguration** Ihres StorSimple-Geräte-Manager-Diensts, um ACRs hinzuzufügen. In der Regel ordnen Sie einem Volume einen ACR zu.

Informationen zum Zuordnen eines ACR zu einem Volume finden Sie unter [Hinzufügen eines Volume](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte.


Führen Sie die folgenden Schritte aus, um einen ACR hinzuzufügen:

#### <a name="to-add-an-acr"></a>So fügen Sie einen ACR hinzu

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, und doppelklicken Sie auf den Namen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf die Option **Access Control-Datensätze**.
2. Klicken Sie auf dem Blatt **Access Control-Datensätze** auf **Hinzufügen**.
3. Gehen Sie auf dem Blatt **Access Control-Datensätze** wie folgt vor:
   
    1. Geben Sie einen **Namen** für den ACR ein.
    
    2. Geben Sie unter **iSCSI-Initiatorname**den IQN des Windows-Hosts an. Gehen Sie zum Abrufen des IQN des Windows Server-Hosts folgendermaßen vor:
   
    3. Starten Sie den Microsoft iSCSI-Initiator auf dem Windows-Host. Wählen Sie im Fenster „iSCSI Initiator Properties“ (Eigenschaften des iSCSI-Initiators) auf der Registerkarte **Konfiguration** die Zeichenfolge im Feld **Initiator Name** (Initiatorname) aus, und kopieren Sie sie.
    Fügen Sie diese Zeichenfolge in das **IQN**-Feld auf dem Blatt **ACR hinzufügen** ein.
   
    6. Klicken Sie auf **Hinzufügen**, um den ACR hinzuzufügen.  
   
        ![Hinzufügen von Access Control-Datensätzen](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Die tabellarische Auflistung wird mit dem hinzugefügten Eintrag aktualisiert.

## <a name="edit-an-acr"></a>Bearbeiten eines ACR

Verwenden Sie das Blatt **Access Control-Datensätze** im Abschnitt **Konfiguration** Ihres StorSimple-Geräte-Manager-Diensts im Azure-Portal, um ACRs zu bearbeiten.

> [!NOTE]
> Sie sollten keinen ACR ändern, der aktuell verwendet wird. Zum Bearbeiten eines ACR, der einem momentan verwendeten Volume zugeordnet ist, sollten Sie zunächst das Volume offline schalten.


Führen Sie die folgenden Schritte aus, um einen ACR zu bearbeiten:

#### <a name="to-edit-an-acr"></a>So bearbeiten Sie einen ACR

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, und doppelklicken Sie auf den Namen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf die Option **Access Control-Datensätze**.
2. Doppelklicken Sie in der tabellarischen Auflistung der Access Control-Datensätze auf dem Blatt **Access Control-Datensätze** auf den ACR, den Sie ändern möchten.
3. Gehen Sie auf dem Blatt **Edit access control records** (Access Control-Datensätze bearbeiten) wie folgt vor:
   
    1. Geben Sie den IQN für den Access Control-Datensatz an.
   
    2. Klicken Sie oben auf dem Blatt auf **Speichern**, um den geänderten ACR zu speichern. Die folgende Bestätigungsmeldung wird angezeigt:
   
        ![Bearbeiten von Access Control-Datensätzen](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Löschen von Access Control-Datensätzen

Verwenden Sie im Azure-Portal die Seite **Konfiguration**, um ACRs zu löschen.

> [!NOTE]
> 
> * Sie sollten keinen ACR löschen, der aktuell verwendet wird. Zum Löschen eines ACR, der einem momentan verwendeten Volume zugeordnet ist, sollten Sie zunächst das Volume offline schalten.
> * Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.


Führen Sie die folgenden Schritte aus, um einen Zugriffssteuerungsdatensatz zu löschen.

#### <a name="to-delete-an-access-control-record"></a>So löschen Sie einen Zugriffssteuerungsdatensatz

1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, und doppelklicken Sie auf den Namen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf die Option **Access Control-Datensätze**.

2. Doppelklicken Sie in der tabellarischen Auflistung der Access Control-Datensätze auf dem Blatt **Access Control-Datensätze** auf den ACR, den Sie löschen möchten.

3. Klicken Sie auf dem Blatt „Edit access control records“ (Access Control-Datensätze bearbeiten) auf **Löschen**.
   
    ![Löschen von ACRs](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Löschen**, um den Löschvorgang fortzusetzen. Die tabellarische Auflistung wird nach dem Löschen entsprechend aktualisiert.
   
   ![Warnmeldung](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie sich die weiteren Informationen zum [Hinzufügen von Volumes und Konfigurieren von ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume) durch.

