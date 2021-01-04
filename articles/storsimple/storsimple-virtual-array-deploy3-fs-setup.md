---
title: Einrichten von StorSimple Virtual Array als Dateiserver | Microsoft-Dokumentation
description: In diesem dritten Tutorial zur StorSimple Virtual Array-Bereitstellung lernen Sie, wie Sie ein virtuelles Gerät als Dateiserver einrichten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 244fdbf7cb723fe85e0987d176a13242f0bff064
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005927"
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>Bereitstellen von StorSimple Virtual Array – Einrichten als Dateiserver über das Azure-Portal
![Abbildung der Schritte, die zur Bereitstellung eines virtuellen Arrays erforderlich sind. Der erste Teil des dritten Schritts heißt „Einrichten als Dateiserver“ und ist hervorgehoben.](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>Einführung

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

In diesem Artikel wird beschrieben, wie Sie die anfängliche Einrichtung durchführen, Ihren StorSimple-Dateiserver registrieren, die Geräteinstallation durchführen und SMB-Freigaben erstellen und die Verbindung dafür herstellen. Dies ist der letzte Artikel einer Reihe von Tutorials, in denen die vollständige Bereitstellung Ihres virtuellen Arrays als Dateiserver oder iSCSI-Server erläutert wird.

Der Einrichtungs- und Konfigurationsvorgang kann ca. zehn Minuten dauern. Die Informationen in diesem Artikel beziehen sich nur auf das Bereitstellen des StorSimple Virtual Arrays. Informationen zum Bereitstellen von Geräten der StorSimple 8000-Serie finden Sie hier: [Bereitstellen Ihres Gerät der StorSimple 8000-Serie mit Update 2](./storsimple-8000-deployment-walkthrough-u2.md).

## <a name="setup-prerequisites"></a>Voraussetzungen für das Setup
Überprüfen Sie Folgendes, bevor Sie Ihr StorSimple Virtual Array konfigurieren und einrichten:

* Sie haben ein virtuelles Array bereitgestellt und die Verbindung dafür hergestellt, wie unter [Bereitstellen eines StorSimple Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) bzw. [Bereitstellen eines StorSimple Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) beschrieben.
* Sie verfügen über den Dienstregistrierungsschlüssel aus dem StorSimple-Geräte-Manager-Dienst, den Sie zum Verwalten von StorSimple Virtual Arrays erstellt haben. Weitere Informationen finden Sie unter [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) für StorSimple Virtual Array.
* Falls dies das zweite oder ein nachfolgendes virtuelles Array ist, das Sie für einen vorhandenen StorSimple-Geräte-Manager-Dienst registrieren, sollten Sie den Dienstdaten-Verschlüsselungsschlüssel bereithalten. Dieser Schlüssel wurde generiert, als das erste Gerät erfolgreich für den Dienst registriert wurde. Wenn Sie diesen Schlüssel verloren haben, helfen Ihnen die Informationen unter [Abrufen des Dienstdaten-Verschlüsselungsschlüssels](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) zum StorSimple Virtual Array weiter.

## <a name="step-by-step-setup"></a>Schritt-für-Schritt-Einrichtung
Verwenden Sie die folgende Schritt-für-Schritt-Anleitung, um Ihr StorSimple Virtual Array einzurichten und zu konfigurieren:

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>Schritt 1: Durchführen der Einrichtung für die lokale Webbenutzeroberfläche und Registrieren Ihres Geräts
#### <a name="to-complete-the-setup-and-register-the-device"></a>So führen Sie das Setup durch und registrieren das Gerät
1. Öffnen Sie ein Browserfenster, und stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche her. Typ:
   
   `https://<ip-address of network interface>`
   
   Verwenden Sie die Verbindungs-URL aus dem vorherigen Schritt. Es wird eine Fehlermeldung mit dem Hinweis angezeigt, dass ein Problem mit dem Sicherheitszertifikat der Website aufgetreten ist. Klicken Sie auf **Mit dieser Webseite fortfahren**.
   
   ![Screenshot: Browserfenster, in dem ein Problem mit dem Sicherheitszertifikat gemeldet wird. Zwei Links werden angezeigt, einer für die Website und einer für die Startseite des Benutzers.](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. Melden Sie sich bei der Webbenutzeroberfläche Ihres virtuellen Arrays als **StorSimpleAdmin** an. Geben Sie das Kennwort des Geräteadministrators ein, das Sie in „Schritt 3: Starten des virtuellen Geräts“ unter [Bereitstellen von StorSimple Virtual Array in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) bzw. [Bereitstellen von StorSimple Virtual Array in VMware](storsimple-virtual-array-deploy2-provision-vmware.md) geändert haben.
   
   ![Screenshot der StorSimple-Anmeldeseite. Der Benutzername „StorSimpleAdmin“ und ein Feld, in dem das Kennwort durch Punkte ersetzt wurde, werden angezeigt.](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. Sie gelangen auf die **Startseite**. Auf dieser Seite werden die verschiedenen Einstellungen beschrieben, die zum Konfigurieren und Registrieren des virtuellen Arrays beim StorSimple-Geräte-Manager-Dienst erforderlich sind. Die **Netzwerkeinstellungen**, **Webproxyeinstellungen** und **Uhrzeiteinstellungen** sind optional. Die einzigen erforderlichen Einstellungen sind **Geräteeinstellungen** und **Cloudeinstellungen**.
   
   ![Screenshot der Startseite. Im Text ist angegeben, dass das Gerät nicht konfiguriert ist. Links zu verschiedenen Arten von Einstellungen sind sichtbar.](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. Auf der Seite **Netzwerkeinstellungen** unter **Netzwerkschnittstellen** wird DATA 0 automatisch für Sie konfiguriert. Jede Netzwerkschnittstelle ist standardmäßig so eingestellt, dass die IP-Adresse automatisch abgerufen wird (DHCP). Daher werden IP-Adresse, Subnetz und Gateway automatisch zugewiesen (sowohl für IPv4 als auch für IPv6).
   
   ![Screenshot: Seite mit den Netzwerkeinstellungen, auf der die IP-Adressen angezeigt werden, die für verschiedene Versionen des Internetprotokolls konfiguriert sind](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   Wenn Sie während der Bereitstellung des Geräts mehr als eine Netzwerkschnittstelle hinzugefügt haben, können Sie diese hier konfigurieren. Beachten Sie, dass Sie die Netzwerkschnittstelle als nur IPv4 oder als IPv4 und IPv6 konfigurieren können. Konfigurationen für nur IPv6 werden nicht unterstützt.
5. DNS-Server sind erforderlich, da sie verwendet werden, wenn Ihr Gerät versucht, mit Ihren Cloudspeicher-Dienstanbietern zu kommunizieren. Sie werden auch genutzt, um Ihr Gerät nach dem Namen aufzulösen, wenn es als Dateiserver konfiguriert ist. Auf der Seite **Netzwerkeinstellungen** unter **DNS-Server**:
   
   1. Es wird automatisch ein primärer und ein sekundärer DNS-Server konfiguriert. Wenn Sie sich für die Konfiguration statischer IP-Adressen entscheiden, können Sie DNS-Server angeben. Zur Sicherstellung der Hochverfügbarkeit wird empfohlen, einen primären und einen sekundären DNS-Server zu konfigurieren.
   2. Klicken Sie auf **Übernehmen**, um die Netzwerkeinstellungen anzuwenden und zu überprüfen.
6. Auf der Seite **Geräteeinstellungen** :
   
   1. Weisen Sie Ihrem Gerät einen eindeutigen **Namen** zu. Dieser Name kann 1 bis 15 Zeichen lang sein und Buchstaben, Zahlen und Bindestriche enthalten.
   2. Klicken Sie auf das **Dateiserver**-Symbol :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image6.png"::: für den **Typ** des Geräts, das Sie erstellen. Mit einem Dateiserver können Sie freigegebene Ordner erstellen.
   3. Da es sich bei Ihrem Gerät um einen Dateiserver handelt, müssen Sie dafür den Beitritt zu einer Domäne durchführen. Geben Sie einen **Domänennamen** ein.
   4. Klicken Sie auf **Anwenden**.
7. Ein Dialogfeld wird angezeigt. Geben Sie die Anmeldeinformationen für die Domäne im angegebenen Format ein. Klicken Sie auf das Häkchensymbol. Die Anmeldeinformationen für die Domäne werden überprüft. Es wird eine Fehlermeldung angezeigt, wenn die Anmeldeinformationen falsch sind.
   
   ![Screenshot: Dialogfeld, in dem der Benutzername und das Kennwort ausgefüllt sind](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. Klicken Sie auf **Anwenden**. Die Geräteeinstellungen werden angewendet und überprüft.
   
   ![Screenshot: Seite „Geräteeinstellungen“. Der Gerätename und der Domänenname sind ausgefüllt.](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > Stellen Sie sicher, dass sich Ihr virtuelles Array in einer eigenen Organisationseinheit für Active Directory befindet und keine Gruppenrichtlinienobjekte darauf angewendet werden oder geerbt sind. Gruppenrichtlinien können Anwendung wie beispielsweise Virenschutzsoftware auf dem StorSimple Virtual Array installieren. Die Installation zusätzlicher Software wird nicht unterstützt und kann zur Beschädigung von Daten führen. 
   > 
   > 
9. Optional: Konfigurieren Sie Ihren Webproxyserver. Die Webproxykonfiguration ist optional. Achten Sie jedoch bei Verwendung eines Webproxys darauf, dass dieser nur hier konfiguriert werden kann.
   
   ![Screenshot: Seite mit den Webproxyeinstellungen. Die Option „Webproxy aktivieren“ ist deaktiviert, und die Authentifizierung ist auf „Keine“ festgelegt. Es sind keine weiteren Werte festgelegt.](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   Auf der Seite **Webproxy** :
   
   1. Geben Sie die **Webproxy-URL** in diesem Format an: *http://&lt;Host-IP-Adresse oder FQDN&gt;:Portnummer*. Beachten Sie, dass HTTPS-URLs nicht unterstützt werden.
   2. Geben Sie unter **Authentifizierung** die Option **Einfach** oder **Keine** an.
   3. Wenn Sie die Authentifizierung verwenden, müssen Sie auch einen **Benutzernamen** und ein **Kennwort** angeben.
   4. Klicken Sie auf **Anwenden**. Die konfigurierten Webproxyeinstellungen werden überprüft und angewendet.
10. Optional: Konfigurieren Sie die Zeiteinstellungen für Ihr Gerät, z. B. die Zeitzone und die primären und sekundären NTP-Server. NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät bei den Clouddienstanbietern authentifiziert werden kann.
    
    ![Screenshot: Seite „Zeiteinstellungen“. Die Zeitzone und der primäre NTP-Server sind ausgefüllt. Der sekundäre NTP-Server ist leer.](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    Auf der Seite **Uhrzeiteinstellungen** :
    
    1. Legen Sie über die Dropdownliste die **Zeitzone** basierend auf dem geografischen Standort fest, an dem das Gerät bereitgestellt wird. Die Standardzeitzone für Ihr Gerät ist „PST“. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.
    2. Geben Sie einen **primären NTP-Server** für das Gerät an, oder übernehmen Sie den Standardwert „time.windows.com“. Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt.
    3. Geben Sie optional einen **sekundären NTP-Server** für Ihr Gerät an.
    4. Klicken Sie auf **Anwenden**. Die konfigurierten Uhrzeiteinstellungen werden überprüft und angewendet.
11. Konfigurieren Sie die Cloudeinstellungen für Ihr Gerät. In diesem Schritt führen Sie die lokale Gerätekonfiguration durch und registrieren das Gerät anschließend beim StorSimple-Geräte-Manager-Dienst.
    
    1. Geben Sie den **Dienstregistrierungsschlüssel** ein, den Sie in [Schritt 2: Abrufen des Dienstregistrierungsschlüssels](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key) für StorSimple Virtual Array erhalten haben.
    2. Falls dies das erste Gerät ist, das Sie bei diesem Dienst registrieren, müssen Sie den **Dienstdaten-Verschlüsselungsschlüssel** angeben. Kopieren Sie diesen Schlüssel, und bewahren Sie ihn an einem sicheren Ort auf. Dieser Schlüssel ist zusammen mit dem Dienstregistrierungsschlüssel zum Registrieren weiterer Geräte beim StorSimple-Geräte-Manager-Dienst erforderlich. 
       
       Falls dies nicht das erste Gerät ist, das Sie bei diesem Dienst registrieren, müssen Sie den Dienstdaten-Verschlüsselungsschlüssel angeben. Sehen Sie sich die weiteren Informationen zum Abrufen des [Dienstdaten-Verschlüsselungsschlüssels](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) für Ihre lokale Webbenutzeroberfläche an.
    3. Klicken Sie auf **Registrieren**. Das Gerät wird neu gestartet. Sie müssen ggf. zwei bis drei Minuten warten, bis das Gerät erfolgreich registriert wurde. Nachdem das Gerät neu gestartet wurde, wird die Anmeldeseite angezeigt.
       
       ![Screenshot: Seite „Cloudeinstellungen“. Die Felder für Registrierungsschlüssel und Verschlüsselungsschlüssel sind ausgefüllt, aber die Werte wurden unkenntlich gemacht.](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. Kehren Sie zum Azure-Portal zurück. Wechseln Sie zu **All resources** (Alle Ressourcen), und suchen Sie nach Ihrem StorSimple-Geräte-Manager-Dienst.
    
    ![Screenshot: Seite „Alle Ressourcen“ im Azure-Portal. Ein Geräte-Manager-Dienst ist hervorgehoben.](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. Wählen Sie in der gefilterte Liste den StorSimple-Geräte-Manager-Dienst aus, und navigieren Sie anschließend zu **Management > Geräte**. Vergewissern Sie sich auf dem Blatt **Geräte**, dass das Gerät erfolgreich mit dem Dienst verbunden wurde und den Status **Zur Einrichtung bereit** aufweist.
    
    ![Bereitstellen](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>Schritt 2: Konfigurieren des Geräts als Dateiserver
Führen Sie die folgenden Schritte im [Azure-Portal](https://portal.azure.com/) aus, um die erforderliche Geräteinstallation durchzuführen.

#### <a name="to-configure-the-device-as-file-server"></a>So konfigurieren Sie das Gerät als Dateiserver
1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst und anschließend zu **Management > Geräte**. Wählen Sie auf dem Blatt **Geräte** das Gerät aus, das Sie gerade erstellt haben. Für das Gerät wird **Zur Einrichtung bereit** angezeigt.
   
   ![Konfigurieren eines Dateiservers](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. Klicken Sie auf das Gerät. Ein Banner zeigt, dass das Gerät für die Einrichtung bereit ist.
   
    ![Konfigurieren eines Dateiservers 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. Klicken Sie in der Befehlsleiste auf **Konfigurieren**. Daraufhin öffnet sich das Blatt **Konfigurieren**. Gehen Sie auf dem Blatt **Konfigurieren** wie folgt vor:
   
   1. Der Dateiservername wird automatisch aufgefüllt.
    
   2. Stellen Sie sicher, dass die Cloudspeicherverschlüsselung auf **Aktiviert** festgelegt ist. Dadurch werden alle in die Cloud gesendeten Daten verschlüsselt. 
    
   3. Für die Verschlüsselung wird ein 256-Bit-AES-Schlüssel zusammen mit dem benutzerdefinierten Schlüssel verwendet. Geben Sie einen Schlüssel mit 32 Zeichen an, und geben Sie den Schlüssel anschließend zur Bestätigung erneut ein. Hinterlegen Sie den Schlüssel in einer App zur Schlüsselverwaltung für zukünftige Verwendungen.
    
   4. Klicken Sie auf **Erforderliche Einstellungen konfigurieren**, um anzugeben, welche Anmeldeinformationen des Speicherkontos mit Ihrem Gerät verwendet werden sollen. Klicken Sie auf **Neue hinzufügen**, wenn noch keine Anmeldeinformationen des Speicherkontos konfiguriert wurden. **Stellen Sie sicher, dass das von Ihnen verwendete Speicherkonto Blockblobs unterstützt. Seitenblobs werden nicht unterstützt.** Weitere Informationen zu [Blockblobs und Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).
   
      ![Konfigurieren eines Dateiservers 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. Gehen Sie auf dem Blatt **Anmeldeinformationen des Speicherkontos hinzufügen** wie folgt vor: 

    1. Wählen Sie das aktuelle Abonnement aus, wenn für das Speicherkonto das gleiche Abonnement wie für den Dienst gilt. Geben Sie „Other“ (Anderes) ein, wenn das Speicherkonto nicht zum Dienstabonnement gehört. 
    
    2. Wählen Sie aus der Dropdownliste ein vorhandenes Speicherkonto aus. 
    
    3. Der Speicherort wird automatisch auf der Basis des angegebenen Speicherkontos aufgefüllt. 
    
    4. Aktivieren Sie TLS, um einen sicheren Netzwerkkommunikationskanal zwischen dem Gerät und der Cloud zu gewährleisten.
    
    5. Klicken Sie auf **Hinzufügen**, um diese Anmeldeinformationen des Speicherkontos hinzufügen. 
   
        ![Konfigurieren eines Dateiservers 4](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. Nachdem die Anmeldeinformationen des Speicherkontos erfolgreich erstellt wurden, wird das Blatt **Konfigurieren** aktualisiert und zeigt die Anmeldeinformationen des angegebenen Speicherkontos an. Klicken Sie auf **Konfigurieren**.
   
   ![Konfigurieren eines Dateiservers 5](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   Es wird ein Dateiserver erstellt. Nachdem der Dateiserver erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt.
   
   ![Konfigurieren eines Dateiservers 5b](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   Der Gerätestatus ändert sich außerdem auf **Online**.
   
   ![Konfigurieren eines Dateiservers 5c](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   Sie können nun fortfahren, um eine Freigabe hinzufügen.

## <a name="step-3-add-a-share"></a>Schritt 3: Hinzufügen einer Freigabe
Führen Sie die folgenden Schritte im [Azure-Portal](https://portal.azure.com/) aus, um eine Freigabe zu erstellen.

#### <a name="to-create-a-share"></a>So erstellen Sie eine Freigabe
1. Wählen Sie das Dateiservergerät aus, das Sie im vorherigen Schritt konfiguriert haben, und klicken Sie auf **...** (oder klicken Sie mit der rechten Maustaste). Wählen Sie im Kontextmenü **Freigabe hinzufügen** aus. Alternativ können Sie in der Gerätebefehlsleiste auf **+ Freigabe hinzufügen** klicken.
   
   ![Hinzufügen einer Freigabe](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. Geben Sie die folgenden Freigabeeinstellungen an:

   1. Geben Sie einen eindeutigen Namen für die Freigabe an. Der Name muss eine Zeichenfolge mit einer Länge von 3 bis 127 Zeichen sein.
    
   2. Geben Sie eine optionale **Beschreibung** für die Freigabe an. Die Beschreibung ist für die Identifizierung der Freigabenbesitzer hilfreich.
    
   3. Geben Sie einen **Typ** für die Freigabe an. Der Typ kann **Mehrstufig** oder **Lokal** lauten. „Mehrstufig“ ist die Standardeinstellung. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie **Lokal** für die Freigabe aus. Wählen Sie für alle anderen Daten **Mehrstufig** für die Freigabe aus.
      Eine lokale Freigabe wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf der Freigabe lokal auf dem Gerät verbleiben und nicht in die Cloud übergehen. Eine mehrstufige Freigabe dagegen wird mit schlanker Speicherzuweisung bereitgestellt. Wenn Sie eine mehrstufige Freigabe erstellen, werden 10 % des Speicherplatzes auf der lokalen Ebene und 90 % des Speicherplatzes in der Cloud bereitgestellt. Wenn Sie beispielsweise ein Volume mit 1 TB bereitstellen, befinden sich 100 GB lokal, und 900 GB werden in der Cloud zum Anordnen der Daten genutzt. Dies bedeutet wiederum Folgendes: Wenn der gesamte lokale Speicherplatz auf dem Gerät aufgebraucht ist, können Sie keine mehrstufige Freigabe bereitstellen.
   
   4. Weisen Sie die Berechtigungen im Feld **Set default full permissions to**  (Vollzugriff als Standardeinstellung festlegen) dem Benutzer oder der Gruppe zu, der bzw. die auf die Freigabe zugreift. Geben Sie den Namen des Benutzers oder der Benutzergruppe im Format *john\@contoso.com* ein. Es wird empfohlen, eine Benutzergruppe (anstelle eines einzelnen Benutzers) zu verwenden, um für Administratorberechtigungen den Zugriff auf diese Freigaben zuzulassen. Nachdem Sie hier die Berechtigungen zugewiesen haben, können Sie den Datei-Explorer verwenden, um diese Berechtigungen zu ändern.
   
   5. Klicken Sie auf **Hinzufügen**, um die Freigabe zu erstellen. 
    
       ![Hinzufügen einer Freigabe 1](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
       Sie werden benachrichtigt, wenn die Freigabe erstellt wird.
   
       ![Hinzufügen einer Freigabe 2](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
      Nachdem die Freigabe mit den angegebenen Einstellungen erstellt wurde, wird das Blatt **Freigaben** aktualisiert und zeigt die neue Freigabe. Standardmäßig ist die Überwachung und die Sicherung für die Freigabe aktiviert.
   
      ![Hinzufügen einer Freigabe 3](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>Schritt 4: Herstellen einer Verbindung mit der Freigabe
Als Nächstes müssen Sie eine Verbindung mit einer oder mehreren Freigaben herstellen, die Sie im vorherigen Schritt erstellt haben. Führen Sie diese Schritte auf dem Windows Server-Host aus, der mit Ihrem StorSimple Virtual Array verbunden ist.

#### <a name="to-connect-to-the-share"></a>So stellen Sie eine Verbindung mit der Freigabe her
1. Drücken Sie :::image type="icon" source="./media/storsimple-virtual-array-deploy3-fs-setup/image22.png"::: + R. Geben Sie im Fenster „Ausführen“ als Pfad *&#92;&#92;&lt;Dateiservername&gt;* an, und ersetzen Sie *Dateiservername* durch den Gerätenamen, den Sie Ihrem Dateiserver zugewiesen haben. Klicken Sie auf **OK**.
   
   ![Screenshot: Dialogfeld „Ausführen“. Im Feld „Öffnen“ ist ein Pfad zum Dateiserver angegeben.](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. Der Datei-Explorer wird geöffnet. Die von Ihnen erstellten Freigaben sollten jetzt als Ordner angezeigt werden. Wählen Sie eine Freigabe (einen Ordner) aus, und doppelklicken Sie darauf, um den Inhalt anzuzeigen.
   
   ![Screenshot des Datei-Explorers. Mehrere Ordner werden angezeigt, die die neu erstellten Freigaben darstellen.](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. Sie können den Freigaben jetzt Dateien hinzufügen und eine Sicherung durchführen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie die lokale Webbenutzeroberfläche verwenden, um [Ihr StorSimple Virtual Array zu verwalten](storsimple-ova-web-ui-admin.md).