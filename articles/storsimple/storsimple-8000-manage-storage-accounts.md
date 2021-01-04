---
title: Verwalten von Anmeldeinformationen für Speicherkonten, Gerät der StorSimple 8000-Serie
description: Beschreibt, wie Sie die Seite „Konfigurieren“ des StorSimple-Geräte-Manager-Diensts zum Hinzufügen, Bearbeiten oder Löschen von Speicherkonten verwenden oder wie Sie die Sicherheitsschlüssel für ein Speicherkonto rotieren.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 0df0c12d5942179e2492bfdc932fa25d126a2426
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016868"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-storage-account-credentials"></a>Verwalten von Anmeldeinformationen für Speicherkonten mithilfe des StorSimple-Geräte-Manager-Diensts

## <a name="overview"></a>Übersicht

Der Abschnitt **Konfiguration** des Blatts „StorSimple-Geräte-Manager-Dienst“ enthält die globalen Dienstparameter, die im StorSimple-Geräte-Manager-Dienst erstellt werden können. Diese Parameter können für alle mit dem Dienst verbundenen Geräte angewendet werden und umfassen Folgendes:

* Anmeldeinformationen des Speicherkontos
* Bandbreitenvorlagen 
* Zugriffssteuerungsdatensätze 

In diesem Tutorial wird erläutert, wie Sie Anmeldeinformationen für ein Speicherkonto hinzufügen, bearbeiten oder löschen und die Sicherheitsschlüssel für ein Speicherkonto rotieren.

 ![Liste der Anmeldeinformationen für Speicherkonten (1)](./media/storsimple-8000-manage-storage-accounts/createnewstorageacct6.png)  

Speicherkonten enthalten die Anmeldeinformationen, die das StorSimple-Gerät für den Zugriff auf das Speicherkonto bei Ihrem Clouddienstanbieter nutzt. Für Microsoft Azure-Speicherkonten sind dies die Anmeldeinformationen, wie z. B. Kontoname und primärer Zugriffsschlüssel. 

Auf dem Blatt **Anmeldeinformationen des Speicherkontos** werden alle Speicherkonten, die für das Abrechnungsabonnement erstellt werden, in einem Tabellenformat mit den folgenden Informationen angezeigt:

* **Name** – der eindeutige Name für das Konto, der bei dessen Erstellung zugewiesen wurde.
* **SSL enabled** – zeigt an, ob TLS aktiviert ist und die Kommunikation zwischen Gerät und Cloud über einen sicheren Kanal verläuft.
* **Used by** – die Anzahl der Datenträger, die das Speicherkonto verwenden.

Im Folgenden sind die häufigsten ausgeführten Aufgaben im Zusammenhang mit Speicherkonten aufgeführt:

* Hinzufügen von Speicherkonten 
* Bearbeiten eines Speicherkontos 
* Löschen von Speicherkonten 
* Schlüsselrotation für Speicherkonten 

## <a name="types-of-storage-accounts"></a>Speicherkontentypen

Es gibt drei Typen von Speicherkonten, die mit dem StorSimple-Gerät verwendet werden können.

* **Auto-generated storage accounts** – diese Art von Speicherkonto wird bei der ersten Erstellung des Dienstes automatisch generiert. Weitere Informationen zum Erstellen dieses Speicherkontos finden Sie unter [Schritt 1: Erstellen eines neuen Diensts](storsimple-8000-deployment-walkthrough-u2.md#step-1-create-a-new-service) in [Bereitstellen Ihres lokalen StorSimple-Geräts](storsimple-8000-deployment-walkthrough-u2.md). 
* **Storage accounts in the service subscription** – Azure-Speicherkonten, die demselben Abonnement zugeordnet sind wie der Dienst . Weitere Informationen zur Erstellung dieser Speicherkonten finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md). 
* **Storage accounts outside of the service subscription** – Azure-Speicherkonten, die nicht mit dem Dienst verknüpft sind und wahrscheinlich schon vorhanden waren, bevor der Dienst erstellt wurde.

## <a name="add-a-storage-account"></a>Hinzufügen von Speicherkonten

Sie können ein Speicherkonto hinzufügen, indem Sie einen eindeutigen Anzeigenamen und Anmeldeinformationen für den Zugriff angeben, die mit dem Speicherkonto (mit dem angegebenen Clouddienstanbieter) verknüpft sind. Sie haben außerdem die Möglichkeit, den TLS-Modus (Transport Layer Security) – zuvor bekannt als SSL-Modus (Secure Sockets Layer) – zu aktivieren, um einen sicheren Kanal für die Netzwerkkommunikation zwischen dem Gerät und der Cloud zu erstellen.

Sie können mehrere Konten für einen Clouddienstanbieter erstellen. Bedenken Sie jedoch, dass der Clouddienstanbieter nach der Erstellung eines Speicherkontos nicht mehr geändert werden kann.

Während das Speicherkonto gespeichert wird, versucht der Dienst mit dem Clouddienstanbieter zu kommunizieren. Die Anmelde- und Zugriffsinformationen, die Sie bereitgestellt haben, werden zu diesem Zeitpunkt authentifiziert. Ein Speicherkonto wird nur erstellt, wenn die Authentifizierung erfolgreich war. Wenn die Authentifizierung fehlschlägt, wird eine entsprechende Fehlermeldung angezeigt.

Verwenden Sie die folgenden Verfahren, um Anmeldeinformationen für das Azure-Speicherkonto hinzufügen:

* So fügen Sie Anmeldeinformationen für ein Speicherkonto hinzu, das zum gleichen Azure-Abonnement wie der Geräte-Manager-Dienst gehört
* So fügen Sie Anmeldeinformationen für ein Azure-Speicherkonto hinzu, das nicht im Abonnement des Geräte-Manager-Diensts enthalten ist

[!INCLUDE [add-a-storage-account-update2](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

#### <a name="to-add-an-azure-storage-account-credential-outside-of-the-storsimple-device-manager-service-subscription"></a>So fügen Sie Anmeldeinformationen für ein Azure-Speicherkonto hinzu, das nicht im Abonnement des StorSimple-Geräte-Manager-Diensts enthalten ist

1. Navigieren Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus. Dadurch werden alle Anmeldeinformationen für ein vorhandenes Speicherkonto aufgeführt, das mit dem StorSimple-Geräte-Manager-Dienst verknüpft ist.
3. Klicken Sie auf **Hinzufügen**.
4. Führen Sie auf dem Blatt **Anmeldeinformationen des Speicherkontos hinzufügen** folgende Schritte aus:
   
    1. Wählen Sie als **Abonnement** die Option **Other** (Andere) aus.
   
    2. Geben Sie den Namen der Anmeldeinformationen für Ihr Azure-Speicherkonto an.
   
    3. Geben Sie im Textfeld **Speicherkonto-Zugriffsschlüssel** den primären Zugriffsschlüssel für die Anmeldeinformationen Ihres Azure-Speicherkontos an. Sie finden diesen Schlüssel, indem Sie zum Azure Storage-Dienst navigieren, die Anmeldeinformationen Ihres Speicherkontos auswählen und auf **Manage account keys** (Kontoschlüssel verwalten) klicken. Nun können Sie den primären Zugriffsschlüssel kopieren.
   
    4. Aktivieren Sie TLS, indem Sie auf die Schaltfläche **Aktivieren** klicken, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem StorSimple-Geräte-Manager-Dienst und der Cloud zu erstellen. Klicken Sie nur dann auf die Schaltfläche **Deaktivieren**, wenn Sie in einer Private Cloud arbeiten.
   
    5. Klicken Sie auf **Hinzufügen**. Sie werden benachrichtigt, nachdem die Anmeldeinformationen des Speicherkontos erfolgreich erstellt wurden.

5. Die neu erstellten Anmeldeinformationen des Speicherkontos werden auf dem Blatt „Konfigurieren“ des StorSimple-Geräte-Manager-Diensts unter **Anmeldeinformationen des Speicherkontos** angezeigt.
   


## <a name="edit-a-storage-account"></a>Bearbeiten eines Speicherkontos

Sie können ein Speicherkonto, das von einem Volumecontainer verwendet wird, bearbeiten. Wenn Sie ein Speicherkonto, das derzeit verwendet wird, bearbeiten, kann nur das Feld mit dem Zugriffsschlüssel für das Speicherkonto geändert werden. Sie können den neuen Speicherzugriffsschlüssel angeben und die aktualisierten Einstellungen speichern.

#### <a name="to-edit-a-storage-account"></a>So bearbeiten Sie ein Speicherkonto

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie im Abschnitt **Konfiguration** auf **Anmeldeinformationen des Speicherkontos**.

    ![Anmeldeinformationen des Speicherkontos (2)](./media/storsimple-8000-manage-storage-accounts/editstorageacct1.png)

2. Klicken Sie auf dem Blatt **Anmeldeinformationen für Speicherkonten** in der Liste auf das Speicherkonto, das Sie bearbeiten möchten. 

3. Sie können die Auswahl für **SSL aktivieren** ändern. Sie können auch auf **Mehr...** klicken und dann **Zugriffsschlüssel synchronisieren** auswählen, um die Zugriffsschlüssel für Ihr Speicherkonto zu rotieren. Weitere Informationen zur Schlüsselrotation finden Sie unter [Schlüsselrotation für Speicherkonten](#key-rotation-of-storage-accounts). Nachdem Sie die Einstellungen geändert haben, klicken Sie auf **Speichern**. 

    ![Speichern der bearbeiteten Anmeldeinformationen des Speicherkontos](./media/storsimple-8000-manage-storage-accounts/editstorageacct3.png)

4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. 

    ![Bestätigen von Änderungen](./media/storsimple-8000-manage-storage-accounts/editstorageacct4.png)

Die Einstellungen werden aktualisiert und für Ihr Speicherkonto gespeichert. 

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

> [!IMPORTANT]
> Sie können ein Speicherkonto nur dann löschen, wenn es nicht von einem Volumecontainer verwendet wird. Wenn ein Speicherkonto von einem Volumecontainer verwendet wird, löschen Sie zunächst den Volumecontainer, und löschen Sie dann das zugehörige Speicherkonto.

#### <a name="to-delete-a-storage-account"></a>So löschen Sie ein Speicherkonto

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie im Abschnitt **Konfiguration** auf **Anmeldeinformationen des Speicherkontos**.

2. Zeigen Sie in der tabellarischen Liste der Speicherkonten auf das Konto, das Sie löschen möchten. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Löschen** aus.

    ![Löschen von Anmeldeinformationen des Speicherkontos](./media/storsimple-8000-manage-storage-accounts/deletestorageacct1.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja** , um den Löschvorgang fortzusetzen. Die tabellarische Auflistung wird den Änderungen entsprechend aktualisiert.

    ![Bestätigen des Löschens](./media/storsimple-8000-manage-storage-accounts/deletestorageacct2.png)

## <a name="key-rotation-of-storage-accounts"></a>Schlüsselrotation für Speicherkonten

Aus Sicherheitsgründen wird in Rechenzentren oft Schlüsselrotation erfordert. Jedes Microsoft Azure-Abonnement kann ein oder mehrere zugeordnete Speicherkonten haben. Der Zugriff auf diese Konten wird durch die Abonnement- und Zugriffsschlüssel für jedes Speicherkonto gesteuert. 

Wenn Sie ein Speicherkonto erstellen, generiert Microsoft Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch das Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Microsoft Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst. Der derzeit verwendete Schlüssel wird *primärer Schlüssel* genannt, und der Sicherungsschlüssel wird *sekundärer Schlüssel* genannt. Einer dieser beiden Schlüssel muss angegeben werden, wenn Ihr Microsoft Azure StorSimple-Gerät auf den Cloudspeicher-Dienstanbieter zugreift.

## <a name="what-is-key-rotation"></a>Was ist Schlüsselrotation?

In der Regel verwenden Anwendungen nur einen der Schlüssel, um auf Daten zuzugreifen. Sie können die Anwendungen so konfigurieren, dass sie nach einem bestimmten Zeitraum zum zweiten Schlüssel wechseln. Nachdem die Anwendungen zum sekundären Schlüssel gewechselt sind, können Sie den ersten Schlüssel zurückziehen und einen neuen Schlüssel generieren. Mit den beiden Schlüsseln haben Ihre Anwendungen die Möglichkeit, ohne Ausfallzeiten auf die Daten zuzugreifen.

Die Speicherkontenschlüssel werden immer in verschlüsselter Form im Dienst gespeichert. Allerdings können diese über den StorSimple-Geräte-Manager-Dienst zurückgesetzt werden. Der Dienst kann den primären und sekundären Schlüssel für alle Speicherkonten im selben Abonnement abrufen, einschließlich der im Speicherdienst erstellten Konten sowie der Standardspeicherkonten, die bei der ersten Erstellung des StorSimple-Geräte-Manager-Diensts generiert wurden. Der StorSimple-Geräte-Manager-Dienst ruft diese Schlüssel immer aus dem klassischen Azure-Portal ab und speichert sie dann in verschlüsselter Form.

## <a name="rotation-workflow"></a>Rotationsworkflow

Ein Microsoft Azure-Administrator kann den primären oder sekundären Schlüssel durch direkten Zugriff auf das Speicherkonto (über den Microsoft Azure-Speicherdienst) erneut generieren oder ändern. Dem StorSimple-Geräte-Manager-Dienst wird diese Änderung nicht automatisch angezeigt.

Um den StorSimple-Geräte-Manager-Dienst über die Änderung zu informieren, müssen Sie auf den StorSimple-Geräte-Manager-Dienst sowie auf das Speicherkonto zugreifen und anschließend (je nach geändertem Schlüssel) den primären oder sekundären Schlüssel synchronisieren. Der Dienst ruft dann den neuesten Schlüssel ab, verschlüsselt ihn und sendet ihn an das Gerät.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service"></a>So synchronisieren Sie Schlüssel für Speicherkonten im selben Abonnement wie der Dienst 
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst. Klicken Sie im Abschnitt **Konfiguration** auf **Anmeldeinformationen des Speicherkontos**.
2. Klicken Sie in der Tabelle mit den Speicherkonten auf dasjenige, das Sie ändern möchten. 

    ![Synchronisieren von Schlüsseln](./media/storsimple-8000-manage-storage-accounts/syncaccesskey1.png)

3. Klicken Sie auf **Mehr...** , und wählen Sie dann **Zugriffsschlüssel synchronisieren** aus.   

    ![Synchronisieren von Schlüsseln (2)](./media/storsimple-8000-manage-storage-accounts/syncaccesskey2.png)

4. Sie müssen den im Microsoft Azure Storage-Dienst zuvor geänderten Schlüssel im StorSimple-Geräte-Manager-Dienst aktualisieren. Wenn der primäre Zugriffsschlüssel geändert (erneut generiert) wurde, wählen Sie den **primären** Schlüssel aus. Wenn der sekundäre Schlüssel geändert wurde, wählen Sie den **sekundären** Schlüssel aus. Klicken Sie auf **Schlüssel synchronisieren**.
      
      ![Synchronisieren von Schlüsseln (3)](./media/storsimple-8000-manage-storage-accounts/syncaccesskey3.png)

Sie werden benachrichtigt, sobald der Schlüssel erfolgreich synchronisiert wurde.

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>So synchronisieren Sie Schlüssel für Speicherkonten, die nicht im Dienstabonnement enthalten sind
1. Klicken Sie auf der Seite **Dienste** auf die Registerkarte **Konfigurieren**.
2. Klicken Sie auf **Speicherkonten hinzufügen/bearbeiten**.
3. Führen Sie im Dialogfeld folgende Schritte aus:
   
   1. Wählen Sie das Speicherkonto mit dem Zugriffsschlüssel, den Sie aktualisieren möchten, aus.
   2. Sie müssen den Speicherzugriffsschlüssel im StorSimple-Geräte-Manager-Dienst aktualisieren. In diesem Fall wird der Speicherzugriffsschlüssel angezeigt. Geben Sie den neuen Schlüssel im Feld **Speicherkonto-Zugriffsschlüssel** ein. 
   3. Speichern Sie die Änderungen. Der Speicherkonten-Zugriffsschlüssel sollte jetzt aktualisiert sein.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur [StorSimple-Sicherheit](storsimple-8000-security.md)
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).