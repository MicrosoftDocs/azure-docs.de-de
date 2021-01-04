---
title: Verwalten von StorSimple Virtual Array-Speicherkonto-Anmeldeinformationen | Microsoft-Dokumentation
description: Beschreibt, wie Sie die Seite „Konfigurieren“ des StorSimple-Geräte-Managers zum Hinzufügen, Bearbeiten und Löschen von Speicherkonten verwenden, und wie Sie die Sicherheitsschlüssel für die Anmeldeinformationen eines Speicherkontos rotieren, das StorSimple Virtual Array zugeordnet ist.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 234bf8bb-d5fe-40be-9d25-721d7482bc3b
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 70b6231829173059ffee675105bacbe1bd1532f2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023890"
---
# <a name="use-storsimple-device-manager-to-manage-storage-account-credentials-for-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Managers zum Verwalten der Anmeldeinformationen eines Speicherkontos für StorSimple Virtual Array

## <a name="overview"></a>Übersicht
Der Abschnitt **Konfiguration** des StorSimple-Geräte-Manager-Dienstblatts Ihres StorSimple Virtual Array enthält die globalen Dienstparameter, die im StorSimple-Manager-Dienst erstellt werden können. Diese Parameter können für alle mit dem Dienst verbundenen Geräte angewendet werden und umfassen Folgendes:

* Anmeldeinformationen des Speicherkontos
* Zugriffssteuerungsdatensätze
  
  ![Dashboards des Geräte-Manager-Diensts](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccts-dashboard.png)  

In diesem Tutorial wird erläutert, wie Sie die Anmeldeinformationen eines Speicherkontos für Ihr StorSimple Virtual Array hinzufügen, bearbeiten und löschen können. Die Informationen in diesem Tutorial gelten nur für das StorSimple Virtual Array. Weitere Informationen zum Verwalten der Speicherkonten der 8000-Serie finden Sie unter [Verwalten von Speicherkonten mithilfe des StorSimple Manager-Diensts](./storsimple-8000-manage-storage-accounts.md).

Anmeldeinformationen des Speicherkontos sind Daten, die das Gerät für den Zugriff auf das Speicherkonto mit dem Clouddienstanbieter nutzt. Für Microsoft Azure-Speicherkonten sind dies die Anmeldeinformationen, wie z. B. Kontoname und primärer Zugriffsschlüssel.

Auf dem Blatt **Anmeldeinformationen des Speicherkontos** werden alle Anmeldeinformationen des Speicherkontos, die für das Abrechnungsabonnement erstellt werden, in einem Tabellenformat mit den folgenden Informationen angezeigt:

* **Name** – der eindeutige Name für das Konto, der bei dessen Erstellung zugewiesen wurde.
* **SSL enabled** – zeigt an, ob TLS aktiviert ist und die Kommunikation zwischen Gerät und Cloud über einen sicheren Kanal verläuft.
  
  ![Konfigurationsabschnitt](./media/storsimple-virtual-array-manage-storage-accounts/ova-storageaccountcredentials-blade.png)

Zu den häufigsten Aufgaben im Zusammenhang mit Anmeldeinformationen eines Speicherkontos, die auf dem Blatt **Anmeldeinformationen des Speicherkontos** ausgeführt werden können, zählen die Folgenden:

* Hinzufügen von Anmeldeinformationen eines Speicherkontos
* Bearbeiten von Anmeldeinformationen eines Speicherkontos
* Löschen von Anmeldeinformationen eines Speicherkontos

## <a name="types-of-storage-account-credentials"></a>Typen von Anmeldeinformationen eines Speicherkontos
Es gibt drei Typen von Anmeldeinformationen eines Speicherkontos, die mit Ihrem StorSimple-Gerät verwendet werden können.

* **Auto-generated storage accounts**(Automatisch generierte Anmeldeinformationen des Speicherkontos) – Diese Art von Anmeldeinformationen wird bei der ersten Erstellung des Dienstes automatisch generiert. Weitere Informationen zum Erstellen dieser Anmeldeinformationen eines Speicherkontos finden Sie unter [So erstellen Sie einen neuen Dienst](storsimple-virtual-array-manage-service.md#create-a-service).
* **Storage accounts in the service subscription** (Dem Dienstabonnnement zugeordnete Anmeldeinformationen des Speicherkontos) – Diese Anmeldeinformationen für das Azure-Speicherkonto sind demselben Abonnement zugeordnet wie der Dienst. Weitere Informationen zum Erstellen dieser Anmeldeinformationen eines Speicherkontos finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md).
* **Storage account credentials outside of the service subscription** (Nicht im Dienstabonnnement enthaltene Anmeldeinformationen des Speicherkontos) – Diese Anmeldeinformationen des Azure-Speicherkontos sind nicht Ihrem Dienst zugeordnet und waren wahrscheinlich schon vorhanden, bevor der Dienst erstellt wurde.

## <a name="add-a-storage-account-credential"></a>Hinzufügen von Anmeldeinformationen eines Speicherkontos
Sie können Anmeldeinformationen eines Speicherkontos zu Ihrer Konfiguration für den StorSimple-Geräte-Manager-Dienst hinzufügen, indem Sie einen eindeutigen Anzeigenamen und Anmeldeinformationen für den Zugriff angeben, die mit dem Speicherkonto verknüpft sind. Sie haben außerdem die Möglichkeit, den TLS-Modus (Transport Layer Security) – zuvor bekannt als SSL-Modus (Secure Sockets Layer) – zu aktivieren, um einen sicheren Kanal für die Netzwerkkommunikation zwischen dem Gerät und der Cloud zu erstellen.

Sie können mehrere Konten für einen Clouddienstanbieter erstellen. Während die Anmeldeinformationen des Speicherkontos gespeichert werden, versucht der Dienst mit Ihrem Clouddienstanbieter zu kommunizieren. Die Anmelde- und Zugriffsinformationen, die Sie bereitgestellt haben, werden zu diesem Zeitpunkt authentifiziert. Die Anmeldeinformationen eines Speicherkontos werden nur dann erstellt, wenn die Authentifizierung erfolgreich war. Wenn die Authentifizierung fehlschlägt, wird eine entsprechende Fehlermeldung angezeigt.

Verwenden Sie die folgenden Verfahren, um Anmeldeinformationen für das Azure-Speicherkonto hinzufügen:

* So fügen Sie Anmeldeinformationen für ein Speicherkonto hinzu, das zum gleichen Azure-Abonnement wie der Geräte-Manager-Dienst gehört
* So fügen Sie Anmeldeinformationen für ein Azure-Speicherkonto hinzu, das nicht im Abonnement des Geräte-Manager-Diensts enthalten ist

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>So fügen Sie Anmeldeinformationen für ein Speicherkonto hinzu, das zum gleichen Azure-Abonnement wie der Geräte-Manager-Dienst gehört

1. Navigieren Sie zu Ihrem Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus.
3. Klicken Sie auf **Hinzufügen**.
4. Gehen Sie auf dem Blatt **Hinzufügen eines Speicherkontos** wie folgt vor:
   
    1. Wählen Sie für **Abonnement** die Option **Aktuell** aus.
    2. Geben Sie den Namen Ihres Azure-Speicherkontos an.
    3. Wählen Sie **Aktivieren** aus, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem StorSimple-Gerät und der Cloud zu erstellen. Wählen Sie **Deaktivieren** nur dann aus, wenn Sie in einer Private Cloud arbeiten.
    4. Klicken Sie auf **Hinzufügen**. Sie werden benachrichtigt, nachdem das Speicherkonto erfolgreich erstellt wurde.<br></br>
   
        ![Hinzufügen der Anmeldeinformationen für ein vorhandenes Speicherkonto](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

#### <a name="to-add-an-azure-storage-account-credential-that-is-outside-of-the-device-manager-service-subscription"></a>So fügen Sie Anmeldeinformationen für ein Azure-Speicherkonto hinzu, das nicht im Abonnement des Geräte-Manager-Diensts enthalten ist

1. Navigieren Sie zu Ihrem Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus. Dadurch werden alle Anmeldeinformationen für ein vorhandenes Speicherkonto aufgeführt, das mit dem StorSimple-Geräte-Manager-Dienst verknüpft ist.
3. Klicken Sie auf **Hinzufügen**.
4. Gehen Sie auf dem Blatt **Hinzufügen eines Speicherkontos** wie folgt vor:
   
    1. Wählen Sie als **Abonnement** die Option **Other** (Andere) aus.
   
    2. Geben Sie den Namen der Anmeldeinformationen für Ihr Azure-Speicherkonto an.
   
    3. Geben Sie im Textfeld **Speicherkonto-Zugriffsschlüssel** den primären Zugriffsschlüssel für die Anmeldeinformationen Ihres Azure-Speicherkontos an. Sie finden diesen Schlüssel, indem Sie zum Azure Storage-Dienst navigieren, die Anmeldeinformationen Ihres Speicherkontos auswählen und auf **Manage account keys** (Kontoschlüssel verwalten) klicken. Nun können Sie den primären Zugriffsschlüssel kopieren.
   
    4. Aktivieren Sie TLS, indem Sie auf die Schaltfläche **Aktivieren** klicken, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem StorSimple-Geräte-Manager-Dienst und der Cloud zu erstellen. Klicken Sie nur dann auf die Schaltfläche **Deaktivieren**, wenn Sie in einer Private Cloud arbeiten.
   
    5. Klicken Sie auf **Hinzufügen**. Sie werden benachrichtigt, nachdem die Anmeldeinformationen des Speicherkontos erfolgreich erstellt wurden.

5. Die neu erstellten Anmeldeinformationen des Speicherkontos werden auf dem Blatt „Konfigurieren“ des StorSimple-Geräte-Manager-Diensts unter **Anmeldeinformationen des Speicherkontos** angezeigt.
   
    ![Hinzufügen von Anmeldeinformationen für ein Speicherkonto, das nicht im Abonnement des Geräte-Manager-Diensts enthalten ist](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-outside-storageacct.png)

## <a name="edit-a-storage-account-credential"></a>Bearbeiten von Anmeldeinformationen eines Speicherkontos
Sie können Anmeldeinformationen für ein Speicherkonto bearbeiten, das von Ihrem Gerät verwendet wird. Wenn Sie Anmeldeinformationen für ein derzeit verwendetes Speicherkonto bearbeiten, lassen sich nur die Felder mit dem Zugriffsschlüssel und dem TLS-Modus ändern. Sie können einen neuen Speicherzugriffsschlüssel angeben oder die Auswahl für **SSL-Modus aktivieren** ändern und die aktualisierten Einstellungen speichern.

#### <a name="to-edit-a-storage-account-credential"></a>So bearbeiten Sie Anmeldeinformationen für ein Speicherkonto
1. Navigieren Sie zu Ihrem Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus. Dadurch werden alle Anmeldeinformationen für ein vorhandenes Speicherkonto aufgeführt, das mit dem StorSimple-Geräte-Manager-Dienst verknüpft ist.
3. Wählen Sie in der tabellarischen Auflistung der Anmeldeinformationen des Speicherkontos das Konto aus, das Sie ändern möchten, und doppelklicken Sie darauf.
4. Gehen Sie auf dem Blatt **Eigenschaften** der Anmeldeinformationen des Speicherkontos wie folgt vor:
   
   1. Ändern Sie bei Bedarf die Auswahl für den Modus **SSL aktivieren**.
   2. Falls gewünscht, können Sie auch die Speicherkonto-Zugriffsschlüssel neu generieren. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../storage/common/storage-account-keys-manage.md). Geben Sie den neuen Speicherkonto-Zugriffsschlüssel an. Für ein Azure-Speicherkonto ist dies der primäre Zugriffsschlüssel
   3. Klicken Sie am oberen Rand des Blatts **Einstellungen** auf **Speichern**, um die Einstellungen zu speichern. Die Einstellungen werden auf dem Blatt **Anmeldeinformationen des Speicherkontos** aktualisiert.
      
      ![Bearbeiten von Anmeldeinformationen eines Speicherkontos](./media/storsimple-virtual-array-manage-storage-accounts/ova-edit-storageacct.png)

## <a name="delete-a-storage-account-credential"></a>Löschen von Anmeldeinformationen eines Speicherkontos
> [!IMPORTANT]
> Sie können die Anmeldeinformationen eines Speicherkontos nur dann löschen, wenn es nicht verwendet wird. Wenn ein Speicherkonto verwendet wird, werden Sie benachrichtigt.
> 
> 

#### <a name="to-delete-a-storage-account-credential"></a>So löschen Sie die Anmeldeinformationen eines Speicherkontos
1. Navigieren Sie zu Ihrem Geräte-Manager-Dienst, und wählen Sie ihn aus. Doppelklicken Sie anschließend darauf. Daraufhin wird das Blatt **Übersicht** geöffnet.
2. Wählen Sie die **Anmeldeinformationen des Speicherkontos** innerhalb des Abschnitts **Konfiguration** aus. Dadurch werden alle Anmeldeinformationen für ein vorhandenes Speicherkonto aufgeführt, das mit dem StorSimple-Geräte-Manager-Dienst verknüpft ist.
3. Wählen Sie in der tabellarischen Auflistung der Anmeldeinformationen des Speicherkontos das Konto aus, das Sie löschen möchten, und doppelklicken Sie darauf.
4. Gehen Sie auf dem Blatt **Eigenschaften** der Anmeldeinformationen des Speicherkontos wie folgt vor:
   
   1. Klicken Sie auf **Löschen**, um die Anmeldeinformationen zu löschen.
   2. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja** , um den Löschvorgang fortzusetzen. Die tabellarische Auflistung wird den Änderungen entsprechend aktualisiert.
      
      ![Löschen von Anmeldeinformationen eines Speicherkontos](./media/storsimple-virtual-array-manage-storage-accounts/ova-del-storageacct.png)

## <a name="synchronizing-storage-account-credential-keys"></a>Synchronisieren der Schlüssel für die Anmeldeinformationen des Speicherkontos
Aus Sicherheitsgründen wird in Rechenzentren oft Schlüsselrotation erfordert. Ein Microsoft Azure-Administrator kann den primären oder sekundären Schlüssel durch direkten Zugriff auf die Anmeldeinformationen des Speicherkontos (über den Microsoft Azure Storage-Dienst) erneut generieren oder ändern. Dem StorSimple-Geräte-Manager-Dienst wird diese Änderung nicht automatisch angezeigt.

Um den StorSimple-Geräte-Manager-Dienst über die Änderung zu informieren, müssen Sie auf den StorSimple-Geräte-Manager-Dienst sowie auf die Anmeldeinformationen des Speicherkontos zugreifen und anschließend (je nach geändertem Schlüssel) den primären oder sekundären Schlüssel synchronisieren. Der Dienst ruft dann den neuesten Schlüssel ab, verschlüsselt ihn und sendet ihn an das Gerät.

#### <a name="to-synchronize-keys-for-storage-account-credentials-in-the-same-subscription-as-the-service-azure-only"></a>So synchronisieren Sie die Schlüssel für die Anmeldeinformationen des Speicherkontos im selben Abonnement wie der Dienst (nur Azure)
1. Wählen Sie auf der Startseite für Dienste Ihren Dienst aus, und doppelklicken Sie auf den Namen. Klicken Sie anschließend im Abschnitt **Konfiguration** auf die Option **Anmeldeinformationen des Speicherkontos**.
2. Wählen Sie in der Liste der Anmeldeinformationen des Speicherkontos auf dem Blatt **Anmeldeinformationen des Speicherkontos** die Anmeldeinformationen des Speicherkontos aus, dessen Schlüssel Sie synchronisieren möchten.
3. Gehen Sie auf dem Blatt **Eigenschaften** für die ausgewählten Anmeldeinformationen des Speicherkontos wie folgt vor:
   
    1. Klicken Sie auf **More** (Weitere), und klicken Sie anschließend auf **Zugriffsschlüssel synchronisieren**.
   
    2. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Schlüssel synchronisieren**, um die Synchronisierung abzuschließen.
    
4. Sie müssen den im Microsoft Azure Storage-Dienst zuvor geänderten Schlüssel im StorSimple-Geräte-Manager-Dienst aktualisieren. Wenn auf dem Blatt **Speicherkontoschlüssel synchronisieren** der primäre Zugriffsschlüssel geändert (neu generiert) wurde, klicken Sie auf „Primär“ und anschließend auf **Schlüssel synchronisieren**. Wenn der sekundäre Schlüssel geändert wurde, klicken Sie auf **Sekundär** und anschließend auf **Schlüssel synchronisieren**.
   
    ![Zugriffsschüssel synchronisieren](./media/storsimple-virtual-array-manage-storage-accounts/ova-sync-access-key.png)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [Ihr StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).