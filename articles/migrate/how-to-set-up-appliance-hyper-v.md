---
title: Einrichten einer Azure Migrate-Appliance für Hyper-V
description: Erfahren Sie, wie Sie eine Azure Migrate-Appliance einrichten, um virtuelle Hyper-V-Computer zu bewerten und zu migrieren.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 5c5fdd1423d806bcc4d2f124310112a3e407e416
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751120"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Einrichten einer Appliance für Hyper-V-VMs

In diesem Artikel finden Sie Informationen zum Einrichten der Azure Migrate-Appliance für die Bewertung von Hyper-V-VMs mit dem Tool [Azure Migrate-Serverbewertung](migrate-services-overview.md#azure-migrate-server-assessment-tool).

Die [Azure Migrate-Appliance](migrate-appliance.md) ist eine einfache Appliance, die von der Azure Migrate-Serverbewertung/-Migration verwendet wird, um lokale Hyper-V-VMs zu entdecken und VM-Metadaten/-Leistungsdaten an Azure zu senden.

Sie können die Appliance mithilfe verschiedener Methoden bereitstellen:

- Einrichten der Appliance auf einem virtuellen Hyper-V-Computers mithilfe einer heruntergeladenen VHD. Diese Methode wird in diesem Artikel beschrieben.
- Einrichten der Appliance auf einem virtuellen Hyper-V-Computer oder physischen Computer mithilfe eines PowerShell-Installationsskripts. [Diese Methode](deploy-appliance-script.md) sollte verwendet werden, wenn Sie keinen virtuellen Computer mithilfe einer VHD-Vorlage einrichten können oder in Azure Government arbeiten.

Überprüfen Sie nach der Erstellung der Appliance, ob diese eine Verbindung mit der Azure Migrate-Serverbewertung herstellen kann. Führen Sie dann die erstmalige Konfiguration durch, und registrieren Sie sie für das Azure Migrate-Projekt.

## <a name="appliance-deployment-vhd"></a>Bereitstellung der Appliance (VHD)

So richten Sie die Appliance mithilfe einer VHD-Vorlage ein

- Geben Sie einen Appliancenamen ein, und generieren Sie einen Azure Migrate-Projektschlüssel im Portal.
- Herunterladen einer komprimierten Hyper-V-VHD über das Azure-Portal
- Erstellen der Appliance und Überprüfen der Verbindungsherstellung mit der Azure Migrate-Serverbewertung
- Führen Sie eine Erstkonfiguration für die Appliance aus, und registrieren Sie die Appliance beim Azure Migrate-Projekt unter Verwendung des Azure Migrate-Projektschlüssels.

### <a name="generate-the-azure-migrate-project-key"></a>Generieren des Azure Migrate-Projektschlüssels

1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit Hyper-V**.
3. Geben Sie in **1: Generieren eines Azure Migrate-Projektschlüssels** einen Namen für die Azure Migrate-Appliance an, die Sie für die Ermittlung von Hyper-V-VMs einrichten möchten. Der Name muss alphanumerisch sein und darf höchstens 14 Zeichen enthalten.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Computer ermitteln“ nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Azure Migrate-Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

### <a name="download-the-vhd"></a>Herunterladen der VHD

Wählen Sie in **2: Azure Migrate-Appliance herunterladen** die VHD-Datei aus, und klicken Sie auf **Herunterladen**. 

   ![Auswahloptionen für „Computer ermitteln“](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Auswahloptionen für „Schlüssel generieren“](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die VHD zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>Erstellen der Appliance-VM

Importieren Sie die heruntergeladene Datei, und erstellen Sie die VM.

1. Extrahieren Sie die gezippte VHD-Datei in einem Ordner auf dem Hyper-V-Host, der die Appliance-VM hostet. Drei Ordner werden extrahiert.
2. Öffnen Sie den Hyper-V-Manager. Klicken Sie unter **Aktionen** auf **Virtuellen Computer importieren**.

    ![Bereitstellen der VHD](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klicken Sie im Assistenten zum Importieren virtueller Computer unter **Vorbereitung** auf **Weiter**.
3. Geben Sie unter **Ordner suchen** den Ordner an, der die extrahierte VHD enthält. Klicken Sie dann auf **Weiter**.
1. Klicken Sie unter **Virtuellen Computer auswählen** auf **Weiter**.
2. Klicken Sie unter **Importtyp auswählen** auf **Virtuellen Computer kopieren (neue eindeutige ID erstellen)** . Klicken Sie dann auf **Weiter**.
3. Behalten Sie unter **Ziel auswählen** die Standardeinstellung bei. Klicken Sie auf **Weiter**.
4. Behalten Sie unter **Speicherordner** die Standardeinstellung bei. Klicken Sie auf **Weiter**.
5. Geben Sie unter **Netzwerk auswählen** den virtuellen Switch an, der von der VM verwendet wird. Der Switch benötigt Internetkonnektivität, um Daten an Azure senden zu können.
6. Überprüfen Sie die Einstellungen unter **Zusammenfassung**. Klicken Sie auf **Fertig stellen**.
7. Starten Sie die VM im Hyper-V-Manager unter **Virtuelle Computer**.


### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance-VM eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

> [!NOTE]
> Wenn Sie die Appliance mithilfe eines [PowerShell-Skripts](deploy-appliance-script.md) und nicht mit der heruntergeladenen VHD einrichten, sind die ersten beiden Schritte in diesem Verfahren nicht relevant.

1. Klicken Sie im Hyper-V-Manager unter **Virtuelle Computer** mit der rechten Maustaste auf die VM, und klicken Sie anschließend auf **Verbinden**.
2. Geben Sie die Sprache, die Zeitzone und das Kennwort für die Appliance an.
3. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der VM herstellen kann, und öffnen Sie die URL der Appliance-Web-App: **https://*Appliancename oder IP-Adresse*: 44368**.

   Alternativ können Sie auch auf dem Appliancedesktop auf die App-Verknüpfung klicken, um die App zu öffnen.
1. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
    - **Konnektivität**: Die App überprüft, ob die VM über Internetzugriff verfügt. Falls die VM einen Proxy verwendet, gehen Sie wie folgt vor:
      - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
      - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
      - Es werden nur HTTP-Proxys unterstützt.
      - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
    - **Uhrzeitsynchronisierung**: Die Uhrzeit wird überprüft. Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die VM-Ermittlung ordnungsgemäß funktioniert.
    - **Updates installieren**: Azure Migrate-Serverbewertung prüft, ob die neuesten Updates für die Appliance installiert sind. Nachdem die Prüfung abgeschlossen ist, können Sie auf **Appliancedienste anzeigen** klicken, um den Status und die Versionen der auf der Appliance ausgeführten Komponenten anzuzeigen.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Azure Migrate-Projektschlüssel** ein. Wenn Sie den Schlüssel nicht haben, wechseln Sie zu **Serverbewertung > Ermitteln > Vorhandene Appliances verwalten**, wählen Sie den Appliancenamen aus, den Sie bei der Generierung des Schlüssels angegeben haben, und kopieren Sie den entsprechenden Schlüssel.
1. Klicken Sie auf **Anmelden**. Auf einer neuen Browserregisterkarte wird eine Azure-Anmeldeaufforderung geöffnet. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Melden Sie sich auf dem neuen Tab mit Ihrem Azure-Benutzernamen und -Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Kehren Sie nach erfolgreicher Anmeldung zur Web-App zurück. 
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.



### <a name="delegate-credentials-for-smb-vhds"></a>Delegieren von Anmeldeinformationen für SMB-VHDs

Wenn Sie VHDs in SMBs ausführen, müssen Sie die Delegierung von Anmeldeinformationen von der Appliance an die Hyper-V-Hosts aktivieren. Gehen Sie dazu in der Appliance wie folgt vor:

1. Führen Sie auf der Appliance-VM den folgenden Befehl aus. „HyperVHost1“ und „HyperVHost2“ sind Beispielhostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. Gehen Sie alternativ im Editor für lokale Gruppenrichtlinien auf der Appliance wie folgt vor:
    - Klicken Sie unter **Richtlinie für "Lokaler Computer"**  > **Computerkonfiguration** auf **Administrative Vorlagen** > **System** > **Delegierung von Anmeldeinformationen**.
    - Doppelklicken Sie auf **Delegierung von aktuellen Anmeldeinformationen zulassen**, und wählen Sie **Aktiviert** aus.
    - Klicken Sie unter **Optionen** auf **Anzeigen**, und fügen Sie jeden Hyper-V-Host, den Sie ermitteln möchten, mit dem Präfix **wsman/** der Liste hinzu.
    - Doppelklicken Sie unter **Delegierung von Anmeldeinformationen** auf **Delegierung von aktuellen Anmeldeinformationen mit reiner NTLM-Serverauthentifizierung zulassen**. Fügen Sie auch hier wieder jeden Hyper-V-Host, den Sie ermitteln möchten, mit dem Präfix **wsman/** der Liste hinzu.

## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie von der Appliance aus eine Verbindung mit Hyper-V-Hosts oder -Clustern her, und starten Sie VM-Ermittlung.

1. Wählen Sie in **Schritt 1: Geben Sie die Hyper-V-Hostanmeldeinformationen** ein, klicken Sie auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, und fügen Sie **Benutzername** und **Kennwort** für einen Hyper-V-Host/-Cluster hinzu, der die Appliance verwendet, um VMs zu ermitteln. Klicken Sie auf **Speichern**.
1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Es werden mehrere Anmeldeinformationen für die Ermittlung von Hyper-V-VMs unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu Hyper-V-Host/-Cluster** auf **Ermittlungsquelle hinzufügen**, um **IP-Adresse/FQDN** für den Hyper-V-Host/-Cluster und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Host/Cluster anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Details zu dem Hyper-V-Host/-Cluster über **CSV importieren** bereitzustellen.

    ![Auswahloptionen zum Hinzufügen der Ermittlungsquelle](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Wenn Sie **Einzelnes Element hinzufügen** auswählen, müssen Sie den Anzeigenamen für die Anmeldeinformationen und **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie **Mehrere Elemente hinzufügen** auswählen _(standardmäßig ausgewählt)_ , können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben. **Überprüfen** Sie die hinzugefügten Datensätze, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** auswählen, können Sie eine CSV-Vorlagendatei herunterladen und die Datei mit **IP-Adresse/FQDN** des Hyper-V-Hosts/-Clusters und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Hyper-V-Hosts/-Clustern zu überprüfen, und zeigt den **Überprüfungszustand** für jeden Host/Cluster in der Tabelle an.
    - Wenn die Hosts/Cluster erfolgreich überprüft werden, können Sie weitere Details anzeigen, indem Sie auf die zugehörigen IP-Adressen/FQDNs klicken.
    - Sollte bei der Überprüfung eines Hosts ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Wenn Sie Hosts oder Cluster entfernen möchten, klicken Sie auf **Löschen**.
    - Es ist nicht möglich, einen bestimmten Host aus einem Cluster zu entfernen. Sie können nur den gesamten Cluster entfernen.
    - Ein Cluster kann auch dann hinzugefügt werden, wenn Probleme mit bestimmten, in dem Cluster enthaltenen Hosts vorliegen.
1. Sie können die Verbindung mit Hosts/Clustern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Klicken Sie auf **Ermittlung starten**, um die VM-Ermittlung von den erfolgreich überprüften Hosts/Clustern aus zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Host/Cluster in der Tabelle überprüfen.

Daraufhin wird die Ermittlung gestartet. Es dauert ca. 2 Minuten pro Host, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.

## <a name="verify-vms-in-the-portal"></a>Überprüfen virtueller Computer im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die VMs im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf das Symbol mit der Anzahl für **Ermittelte Server**.


## <a name="next-steps"></a>Nächste Schritte

Testen Sie die [Hyper-V-Bewertung](tutorial-assess-hyper-v.md) mit der Azure Migrate-Serverbewertung.