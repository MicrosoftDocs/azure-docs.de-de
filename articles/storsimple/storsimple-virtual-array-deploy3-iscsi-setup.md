---
title: Setup von Microsoft Azure StorSimple Virtual Array als iSCSI-Server | Microsoft Docs
description: Es wird beschrieben, wie Sie die anfängliche Einrichtung durchführen, den StorSimple-iSCSI-Server registrieren und die Geräteinstallation durchführen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 6289f335234c9b7efec02a18e12b40a1140c1b3f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956550"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>Bereitstellen von StorSimple Virtual Array – Einrichten als iSCSI-Server über das Azure-Portal

![Prozessablauf für iSCSI-Setup](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>Übersicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Dieses Bereitstellungstutorial bezieht sich auf das Microsoft Azure StorSimple Virtual Array. In diesem Tutorial wird beschrieben, wie Sie das anfängliche Setup durchführen, Ihren StorSimple-iSCSI-Server registrieren, die Geräteinstallation abschließen und anschließend Volumes auf dem als iSCSI-Server konfigurierten StorSimple Virtual Array erstellen, bereitstellen, initialisieren und formatieren. 

Die hier beschriebenen Verfahren dauern zwischen 30 Minuten und einer Stunde. Die in diesem Artikel veröffentlichten Informationen gelten nur für StorSimple Virtual Arrays.

## <a name="setup-prerequisites"></a>Voraussetzungen für das Setup

Überprüfen Sie Folgendes, bevor Sie Ihr StorSimple Virtual Array konfigurieren und einrichten:

* Sie haben ein virtuelles Array bereitgestellt und die Verbindung dafür hergestellt, wie unter [Bereitstellen von StorSimple Virtual Array – Bereitstellen eines virtuellen Arrays in Hyper-V](./storsimple-virtual-array-deploy2-provision-hyperv.md) oder [Bereitstellen von StorSimple Virtual Array – Bereitstellen eines virtuellen Arrays in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) beschrieben.
* Sie verfügen über den Dienstregistrierungsschlüssel aus dem StorSimple-Geräte-Manager-Dienst, den Sie zum Verwalten von StorSimple Virtual Arrays erstellt haben. Weitere Informationen finden Sie unter **Schritt 2: Abrufen des Dienstregistrierungsschlüssels** in [Bereitstellen des StorSimple Virtual Array – Vorbereiten des Portals](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key).
* Falls dies das zweite oder ein nachfolgendes virtuelles Array ist, das Sie für einen vorhandenen StorSimple-Geräte-Manager-Dienst registrieren, sollten Sie den Dienstdaten-Verschlüsselungsschlüssel bereithalten. Dieser Schlüssel wurde generiert, als das erste Gerät erfolgreich für den Dienst registriert wurde. Wenn Sie diesen Schlüssel verloren haben, helfen Ihnen die Informationen unter **Abrufen des Verschlüsselungsschlüssels für Dienstdaten** in [Verwaltung des StorSimple Virtual Array (Vorschau) mithilfe der Web-UI](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)weiter.

## <a name="step-by-step-setup"></a>Schritt-für-Schritt-Einrichtung

Verwenden Sie die folgende Schritt-für-Schritt-Anleitung, um Ihr StorSimple Virtual Array einzurichten und zu konfigurieren:

* [Schritt 1: Durchführen der Einrichtung für die lokale Webbenutzeroberfläche und Registrieren Ihres Geräts](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* Schritt 2: Durchführen der erforderlichen Geräteinstallation
* [Schritt 3: Hinzufügen eines Volumes](#step-3-add-a-volume)
* [Schritt 4: Einlegen, Initialisieren und Formatieren eines Volumes](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Schritt 1: Durchführen der Einrichtung für die lokale Webbenutzeroberfläche und Registrieren Ihres Geräts

#### <a name="to-complete-the-setup-and-register-the-device"></a>So führen Sie das Setup durch und registrieren das Gerät

1. Öffnen Sie ein Browserfenster. So stellen Sie eine Verbindung mit dem Typ der Webbenutzeroberfläche her:
   
    `https://<ip-address of network interface>`
   
    Verwenden Sie die Verbindungs-URL aus dem vorherigen Schritt. Es wird eine Fehlermeldung mit dem Hinweis angezeigt, dass ein Problem mit dem Sicherheitszertifikat der Website aufgetreten ist. Klicken Sie auf **Mit dieser Webseite fortfahren**.
   
    ![Sicherheitszertifikatfehler](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. Melden Sie sich bei der Webbenutzeroberfläche des virtuellen Geräts als **StorSimpleAdmin** an. Geben Sie das Geräteadministratorkennwort ein, das Sie in Schritt 3: Starten des virtuellen Geräts“ im Abschnitt [Bereitstellen von StorSimple Virtual Array – Bereitstellen eines virtuellen Geräts in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) bzw. [Bereitstellen von StorSimple Virtual Array – Bereitstellen eines virtuellen Geräts in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) geändert haben.
   
    ![Anmeldeseite](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. Sie gelangen auf die **Startseite** . Auf dieser Seite werden die verschiedenen Einstellungen beschrieben, die zum Konfigurieren und Registrieren des virtuellen Geräts beim StorSimple-Geräte-Manager-Dienst erforderlich sind. Beachten Sie, dass die **Netzwerkeinstellungen**, **Webproxyeinstellungen** und **Uhrzeiteinstellungen** optional sind. Die einzigen erforderlichen Einstellungen sind **Geräteeinstellungen** und **Cloudeinstellungen**.
   
    ![Startseite](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. Auf der Seite **Netzwerkeinstellungen** unter **Netzwerkschnittstellen** wird DATA 0 automatisch für Sie konfiguriert. Jede Netzwerkschnittstelle ist standardmäßig so eingestellt, dass die IP-Adresse automatisch abgerufen wird (DHCP). Daher werden IP-Adresse, Subnetz und Gateway automatisch zugewiesen (sowohl für IPv4 als auch für IPv6).
   
    Da Sie planen, Ihr Gerät als iSCSI-Server bereitzustellen (zur Bereitstellung eines Blockspeichers), empfehlen wir Folgendes: Deaktivieren Sie die Option **IP-Adresse automatisch beziehen** , und konfigurieren Sie statische IP-Adressen.
   
    ![Seite „Netzwerkeinstellungen“](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    Wenn Sie während der Bereitstellung des Geräts mehr als eine Netzwerkschnittstelle hinzugefügt haben, können Sie diese hier konfigurieren. Beachten Sie, dass Sie die Netzwerkschnittstelle als nur IPv4 oder als IPv4 und IPv6 konfigurieren können. Konfigurationen für nur IPv6 werden nicht unterstützt.
5. DNS-Server sind erforderlich, da sie verwendet werden, wenn Ihr Gerät versucht, mit Ihren Cloudspeicher-Dienstanbietern zu kommunizieren. Sie werden auch genutzt, um Ihr Gerät nach dem Namen aufzulösen, wenn es als Dateiserver konfiguriert ist. Auf der Seite **Netzwerkeinstellungen** unter **DNS-Server**:
   
   1. Es werden automatisch ein primärer und ein sekundärer DNS-Server konfiguriert. Wenn Sie sich für die Konfiguration statischer IP-Adressen entscheiden, können Sie DNS-Server angeben. Zur Sicherstellung der Hochverfügbarkeit wird empfohlen, einen primären und einen sekundären DNS-Server zu konfigurieren.
   2. Klicken Sie auf **Anwenden**. Die Netzwerkeinstellungen werden angewendet und überprüft.
6. Auf der Seite **Geräteeinstellungen** :
   
   1. Weisen Sie Ihrem Gerät einen eindeutigen **Namen** zu. Dieser Name kann 1 bis 15 Zeichen lang sein und Buchstaben, Zahlen und Bindestriche enthalten.
   2. Klicken Sie auf das **iSCSI-Server**-Symbol ![iSCSI server icon](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png) für den **Typ** des Geräts, das Sie erstellen. Ein iSCSI-Server ermöglicht Ihnen die Bereitstellung von Blockspeicher.
   3. Geben Sie an, ob für das Gerät der Beitritt zu einer Domäne durchgeführt werden soll. Wenn es sich bei Ihrem Gerät um einen iSCSI-Server handelt, ist der Domänenbeitritt optional. Wenn Sie sich gegen den Beitritt des iSCSI-Servers zu einer Domäne entscheiden, klicken Sie auf **Übernehmen**, warten Sie das Anwenden der Einstellungen ab, und wechseln Sie dann zum nächsten Schritt.
      
       Gehen Sie wie folgt vor, wenn Sie für das Gerät den Domänenbeitritt durchführen möchten: Geben Sie einen **Domänennamen** ein, und klicken Sie dann auf **Übernehmen**.
      
      > [!NOTE]
      > Stellen Sie beim Beitritt Ihres iSCSI-Servers zu einer Domäne sicher, dass sich Ihr virtuelles Array in seiner eigenen Organisationseinheit für Microsoft Azure Active Directory befindet und keine Gruppenrichtlinienobjekte darauf angewendet sind.
      > 
      > 
   4. Ein Dialogfeld wird angezeigt. Geben Sie die Anmeldeinformationen für die Domäne im angegebenen Format ein. Klicken Sie auf das Häkchen ![Häkchensymbol](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png)erforderlich. Die Anmeldeinformationen für die Domäne werden überprüft. Es wird eine Fehlermeldung angezeigt, wenn die Anmeldeinformationen falsch sind.
      
       ![Anmeldeinformationen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. Klicken Sie auf **Anwenden**. Die Geräteeinstellungen werden angewendet und überprüft.
7. Optional: Konfigurieren Sie Ihren Webproxyserver. Die Webproxykonfiguration ist optional. Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann.
   
    ![Webproxy konfigurieren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    Auf der Seite **Webproxy** :
   
   1. Geben Sie die **Webproxy-URL** in diesem Format an: *http:\//Host-IP-Adresse* oder *FQDN:Portnummer*. Beachten Sie, dass HTTPS-URLs nicht unterstützt werden.
   2. Geben Sie unter **Authentifizierung** die Option **Einfach** oder **Keine** an.
   3. Wenn Sie die Authentifizierung verwenden, müssen Sie auch einen **Benutzernamen** und ein **Kennwort** angeben.
   4. Klicken Sie auf **Anwenden**. Die konfigurierten Webproxyeinstellungen werden überprüft und angewendet.
8. Optional: Konfigurieren Sie die Zeiteinstellungen für Ihr Gerät, z. B. die Zeitzone und die primären und sekundären NTP-Server. NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät bei den Clouddienstanbietern authentifiziert werden kann.
   
    ![Uhrzeiteinstellungen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    Auf der Seite **Uhrzeiteinstellungen** :
   
   1. Legen Sie über die Dropdownliste die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Die Standardzeitzone für Ihr Gerät ist „PST“. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
   2. Geben Sie einen **primären NTP-Server** für das Gerät an, oder übernehmen Sie den Standardwert „time.windows.com“. Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt.
   3. Geben Sie optional einen **sekundären NTP-Server** für Ihr Gerät an.
   4. Klicken Sie auf **Anwenden**. Die konfigurierten Uhrzeiteinstellungen werden überprüft und angewendet.
9. Konfigurieren Sie die Cloudeinstellungen für Ihr Gerät. In diesem Schritt führen Sie die lokale Gerätekonfiguration durch und registrieren das Gerät anschließend beim StorSimple-Geräte-Manager-Dienst.
   
   1. Geben Sie den **Dienstregistrierungsschlüssel** ein, den Sie in **Schritt 2: Abrufen des Dienstregistrierungsschlüssels** in [Bereitstellen des StorSimple Virtual Array – Vorbereiten des Portals](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) erhalten haben.
   2. Falls dies nicht das erste Gerät ist, das Sie bei diesem Dienst registrieren, müssen Sie den **Dienstdaten-Verschlüsselungsschlüssel** angeben. Dieser Schlüssel ist zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple-Geräte-Manager-Dienst erforderlich. Sehen Sie sich die weiteren Informationen zum [Abrufen des Verschlüsselungsschlüssels für Dienstdaten](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) für Ihre lokale Webbenutzeroberfläche an.
   3. Klicken Sie auf **Registrieren**. Das Gerät wird neu gestartet. Sie müssen ggf. zwei bis drei Minuten warten, bis das Gerät erfolgreich registriert wurde. Nachdem das Gerät neu gestartet wurde, wird die Anmeldeseite angezeigt.
      
      ![Gerät registrieren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. Kehren Sie zum Azure-Portal zurück.
11. Navigieren Sie zum Blatt **Geräte** Ihres Diensts. Wenn Sie über zahlreiche Ressourcen verfügen, klicken Sie auf **All resources** (Alle Ressourcen), klicken Sie auf den Namen Ihres Diensts (suchen Sie ihn bei Bedarf) und anschließend auf **Geräte**.
12. Vergewissern Sie sich auf dem Blatt **Geräte**, dass das Gerät erfolgreich mit dem Dienst verbunden wurde, indem Sie seinen Status überprüfen. Der Gerätestatus sollte **Zur Einrichtung bereit** lauten.
    
    ![Gerät bereitstellen](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>Schritt 2: Konfigurieren des Geräts als ISCSI-Server

Führen Sie die folgenden Schritte im Azure-Portal aus, um die erforderliche Geräteinstallation durchzuführen.

#### <a name="to-configure-the-device-as-iscsi-server"></a>So konfigurieren Sie das Gerät als ISCSI-Server

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst und anschließend zu **Management > Geräte**. Wählen Sie auf dem Blatt **Geräte** das Gerät aus, das Sie gerade erstellt haben. Für das Gerät wird **Zur Einrichtung bereit** angezeigt.
   
    ![Konfigurieren des Geräts als ISCSI-Server](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. Klicken Sie auf das Gerät. Ein Banner zeigt, dass das Gerät für die Einrichtung bereit ist.
   
    ![Konfigurieren des Geräts als iSCSI-Server 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. Klicken Sie in der Gerätebefehlsleiste auf **Konfigurieren**. Daraufhin öffnet sich das Blatt **Konfigurieren**. Gehen Sie auf dem Blatt **Konfigurieren** wie folgt vor:
   
   * Der iSCSI-Servername wird automatisch gefüllt.
   * Stellen Sie sicher, dass die Cloudspeicherverschlüsselung auf **Aktiviert** festgelegt ist. Dadurch wird gewährleistet, dass die vom Gerät an die Cloud gesendeten Daten verschlüsselt werden.
   * Geben Sie einen Verschlüsselungsschlüssel mit 32 Zeichen an, und hinterlegen Sie ihn in einer App zur Schlüsselverwaltung für zukünftige Verwendungen.
   * Wählen Sie ein Speicherkonto, das Sie mit Ihrem Gerät verwenden möchten. In diesem Abonnement können Sie ein vorhandenes Speicherkonto auswählen, oder Sie klicken auf **Hinzufügen**, um ein Konto eines anderen Abonnements zu wählen.
     
     ![Konfigurieren des Geräts als iSCSI-Server 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. Klicken Sie auf **Konfigurieren**, um das Setup des iSCSI-Servers abzuschließen.
   
    ![Konfigurieren des Geräts als iSCSI-Server 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. Sie werden benachrichtigt, dass die Erstellung des iSCSI-Servers ausgeführt wird. Nachdem der iSCSI-Server erfolgreich erstellt wurde, wird das Blatt **Geräte** aktualisiert, und der entsprechende Gerätestatus lautet **Online**.
   
    ![Konfigurieren des Geräts als iSCSI-Server 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>Schritt 3: Hinzufügen eines Volumes

1. Wählen Sie auf dem Blatt **Geräte** das Gerät aus, das Sie gerade als iSCSI-Server konfiguriert haben. Klicken Sie auf **...** (Sie können auch mit der rechten Maustaste in dieser Zeile klicken), und wählen Sie im Kontextmenü **Volume hinzufügen** aus. Sie können auch in der Befehlsleiste auf **+ Volume hinzufügen** klicken. Daraufhin öffnet sich das Blatt **Volume hinzufügen**.
   
    ![Hinzufügen eines Volumes](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. Gehen Sie auf dem Blatt **Volume hinzufügen** wie folgt vor:
   
   * Geben Sie im Feld **Volumename** einen eindeutigen Namen für Ihr Volume ein. Der Name muss eine Zeichenfolge mit einer Länge von 3 bis 127 Zeichen sein.
   * Geben Sie in der Dropdownliste **Typ** an, ob das von Ihnen erstellte Volume **Mehrstufig** oder **Lokal** sein soll. Für Workloads, die lokale Garantien, niedrige Wartezeiten und höhere Leistung benötigen, wählen Sie **Lokales** **Volume** aus. Wählen Sie für alle anderen Daten **Mehrstufiges** **Volume** aus.
   * Geben Sie im Feld **Kapazität** die Größe des Volumes an. Ein mehrstufiges Volume muss zwischen 500 GB und 5 TB groß sein und ein lokales Volume zwischen 50 GB und 500 GB.
     
     Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume auf dem Gerät verbleiben und nicht in die Cloud übergehen.
     
     Ein mehrstufiges Volume dagegen wird mit schlanker Speicherzuweisung bereitgestellt. Wenn Sie ein mehrstufiges Volume erstellen, werden ca. 10 % des Speicherplatzes auf der lokalen Ebene und 90 % des Speicherplatzes in der Cloud bereitgestellt. Wenn Sie beispielsweise ein Volume mit 1 TB bereitstellen, befinden sich 100 GB lokal, und 900 GB werden in der Cloud zum Anordnen der Daten genutzt. Dies bedeutet wiederum Folgendes: Wenn der gesamte lokale Speicherplatz auf dem Gerät aufgebraucht ist, können Sie keine mehrstufige Freigabe bereitstellen (da die 10 % nicht verfügbar sind).
     
     ![Hinzufügen eines Volumes 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * Klicken Sie auf **Verbundene Hosts**, wählen Sie einen Access Control-Datensatz (ACR) aus, der dem iSCSI-Initiator entspricht, den Sie mit diesem Volume verbinden möchten, und klicken Sie anschließend auf **Auswählen**. <br><br> 
3. Klicken Sie zum Hinzufügen eines neuen verbundenen Hosts auf **Neu hinzufügen**, geben Sie einen Namen für den Host und seinen IQN-Namen (qualifizierter iSCSI-Name) ein, und klicken Sie anschließend auf **Hinzufügen**. Wenn Sie den IQN nicht haben, gehen Sie zu [Anhang A: Abrufen des IQN eines Windows Server-Hosts](#appendix-a-get-the-iqn-of-a-windows-server-host).
   
      ![Hinzufügen eines Volumes 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. Klicken Sie nach Abschluss der Konfiguration Ihres Volume auf **OK**. Ein Volume wird mit den angegebenen Einstellungen erstellt, und Sie erhalten eine Benachrichtigung. Standardmäßig werden für das Volume die Überwachung und die Sicherung aktiviert.
   
     ![Hinzufügen eines Volumes 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. Wechseln Sie zum Blatt **Volumes**, um sicherzustellen, dass das Volume erfolgreich erstellt wurde. Das Volume sollte hier aufgeführt werden.
   
   ![Hinzufügen eines Volumes 5](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>Schritt 4: Einlegen, Initialisieren und Formatieren eines Volumes

Führen Sie die folgenden Schritte aus, um Ihre StorSimple-Volumes auf einem Windows Server-Host bereitzustellen, zu initialisieren und zu formatieren.

#### <a name="to-mount-initialize-and-format-a-volume"></a>So stellen Sie ein Volume bereit und initialisieren und formatieren dieses

1. Öffnen Sie die App **iSCSI-Initiator** auf dem entsprechenden Server.
2. Klicken Sie im Fenster **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Erkennung** auf **Portal ermitteln**.
   
    ![Portal ermitteln](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. Geben Sie im Dialogfeld **Zielportal ermitteln** die IP-Adresse Ihrer iSCSI-aktivierten Netzwerkschnittstelle an, und klicken Sie dann auf **OK**.
   
    ![IP-Adresse](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. Suchen Sie auf der Registerkarte **Ziele** des Fensters **Eigenschaften des iSCSI-Initiators** nach **Ermittelte Ziele**. (Jedes Volume ist ein ermitteltes Ziel.) Als Gerätestatus sollte **Inaktiv** angezeigt werden.
   
    ![Ermittelte Ziele](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. Wählen Sie ein Zielgerät aus, und klicken Sie dann auf **Verbinden**. Nachdem das Gerät verbunden ist, sollte sich der Status in **Verbunden** ändern. (Weitere Informationen zum Verwenden des Microsoft iSCSI-Initiators finden Sie unter [Installieren und Konfigurieren des Microsoft iSCSI-Initiators][1].)
   
    ![Zielgerät auswählen](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. Drücken Sie auf Ihrem Windows-Host die Windows-Logo-Taste+X, und klicken Sie dann auf **Ausführen**.
7. Geben Sie im Dialogfeld **Ausführen** die Angabe **Diskmgmt.msc** ein. Klicken Sie auf **OK**. Das Dialogfeld **Datenträgerverwaltung** wird angezeigt. Im rechten Bereich werden die Volumes auf Ihrem Host angezeigt.
8. Im Fenster **Datenträgerverwaltung** werden die bereitgestellten Volumes wie in der folgenden Abbildung dargestellt angezeigt. Klicken Sie mit der rechten Maustaste auf das ermittelte Volume (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Online**.
   
    ![Datenträgerverwaltung](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. Klicken Sie mit der rechten Maustaste, und wählen Sie **Datenträgerinitialisierung** aus.
   
    ![Datenträger 1 initialisieren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. Wählen Sie im Dialogfeld die zu initialisierenden Datenträger aus, und klicken Sie dann auf **OK**.
    
    ![Datenträger 2 initialisieren](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. Der Assistent zum Erstellen neuer einfacher Volumes wird gestartet. Wählen Sie eine Datenträgergröße aus, und klicken Sie dann auf **Weiter**.
    
    ![Assistent für neue Volumes 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. Weisen Sie dem Volume einen Laufwerkbuchstaben zu, und klicken Sie dann auf **Weiter**.
    
    ![Assistent für neue Volumes 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. Geben Sie die Parameter zum Formatieren des Volumes ein. **Unter Windows Server wird nur NTFS unterstützt.** Legen Sie die Größe der Zuordnungseinheit auf 64 KB fest. Geben Sie eine Bezeichnung für das Volume an. Als bewährte Methode wird empfohlen, den gleichen Volume-Namen zu verwenden wie auf Ihrem StorSimple Virtual Array. Klicken Sie auf **Weiter**.
    
    ![Assistent für neue Volumes 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. Überprüfen Sie die Werte für Ihr Volume, und klicken Sie dann auf **Fertig stellen**.
    
    ![Assistent für neue Volumes 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    Die Volumes werden auf der Seite **Online** on the **Datenträgerverwaltung** .
    
    ![Volumes online](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die lokale Webbenutzeroberfläche verwenden, um [Ihr StorSimple Virtual Array zu verwalten](storsimple-ova-web-ui-admin.md).

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>Anhang A: Abrufen des IQN eines Windows Server-Hosts

Führen Sie die folgenden Schritte aus, um den IQN (iSCSI Qualified Name) eines Windows-Hosts abzurufen, auf dem Windows Server 2012 ausgeführt wird.

#### <a name="to-get-the-iqn-of-a-windows-host"></a>So rufen Sie den IQN eines Windows Server-Hosts ab

1. Starten Sie den Microsoft iSCSI-Initiator auf dem Windows-Host.
2. Wählen Sie im Fenster **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Konfiguration** die Zeichenfolge im Feld **Initiatorname** aus, und kopieren Sie sie.
   
    ![Eigenschaften von iSCSI-Initiator](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. Speichern Sie diese Zeichenfolge.

<!--Reference link-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee338480(v=ws.10)