---
title: Installieren von Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Anweisungen zur Installation von Azure IoT Edge auf Windows-Geräten
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 049c24beb6bb1573458779bf0796357fa634898f
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008569"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Installieren und Bereitstellen von Azure IoT Edge für Linux auf einem Windows-Gerät (Vorschau)

Die Azure IoT Edge-Runtime verwandelt ein Gerät in ein IoT Edge-Gerät. Die Runtime kann auf verschiedensten Geräten bereitgestellt werden: von der einfachen PC-Klasse bis zu Industrieservern. Wenn ein Gerät mit der IoT Edge-Runtime konfiguriert wurde, können Sie darauf Geschäftslogik aus der Cloud bereitstellen. Weitere Informationen finden Sie unter [Grundlegendes zur Azure IoT Edge-Runtime und ihrer Architektur](iot-edge-runtime.md).

Azure IoT Edge für Linux unter Windows ermöglicht Ihnen die Verwendung von Azure IoT Edge auf Windows-Geräten mithilfe von Linux-VMs. Die Linux-Version von Azure IoT Edge und alle darin bereitgestellten Linux-Module werden auf der VM ausgeführt. Von dort aus können Windows-Anwendungen und Windows-Code sowie die IoT Edge-Runtime und Module frei miteinander interagieren.

In diesem Artikel werden die Schritte zum Einrichten von IoT Edge auf einem Windows-Gerät beschrieben. Mit diesen Schritten wird eine Linux-VM mit der IoT Edge-Runtime für die Ausführung auf Ihrem Windows-Gerät bereitgestellt. Anschließend wird das Gerät mit seiner IoT Hub-Geräteidentität bereitgestellt.

>[!NOTE]
>IoT Edge für Linux unter Windows befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Obwohl IoT Edge für Linux unter Windows die empfohlene Umgebung für die Verwendung von Azure IoT Edge unter Windows ist, sind Windows-Container weiterhin verfügbar. Wenn Sie Windows-Container bevorzugen, finden Sie unter [Installieren und Verwalten von Azure IoT Edge für Windows](how-to-install-iot-edge-windows-on-windows.md) eine Schrittanleitung.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem gültigen Abonnement. Wenn Sie kein [Azure-Abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

* Eine [IoT Hub-Instanz](../iot-hub/iot-hub-create-through-portal.md) in Azure mit dem Tarif „Free“ oder „Standard“.

* Ein Windows-Gerät mit den folgenden Mindestsystemanforderungen:

  * Windows 10 ab Version 1809, Build 17763 oder höher
  * Professional, Enterprise oder Server Edition
  * Minimaler RAM: 4 GB (8 GB empfohlen)
  * Minimale Speichergröße: 10 GB

* Zugriff auf den Insider-Build von Windows Admin Center mit installierter Azure IoT Edge-Erweiterung für Windows Admin Center:  <!-- The link below needs the language localization to work; otherwise broken -->
   1. Besuchen Sie dazu die [Windows-Insider-Vorschau](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver).

   1. Wählen Sie in der Dropdownliste mit den Vorschauversionen **Windows Admin Center Preview – Build 2012** und dann **Bestätigen** aus.

      ![Auswählen von „Windows Admin Center Preview – Build 2012“ in der Dropdownliste der verfügbaren Vorschauversionen](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. Wählen Sie in der Dropdownliste **Sprache auswählen** die Option **Englisch** und dann **Bestätigen** aus.

   1. Wählen Sie **Jetzt herunterladen** aus, um die Datei *WindowsAdminCenterPreview2012.msi* herunterzuladen.

   1. Führen Sie die Datei *WindowsAdminCenterPreview2012.msi* aus, und befolgen Sie die Anweisungen im Installations-Assistenten, um Windows Admin Center zu installieren. Öffnen Sie Windows Admin Center nach Abschluss der Installation.

   1. Bei der ersten Verwendung von Windows Admin Center werden Sie aufgefordert, ein Zertifikat auszuwählen. Wählen Sie als Zertifikat **Windows Admin Center-Client** aus.

   1. Nun installieren Sie die Azure IoT Edge-Erweiterung. Wählen Sie das Zahnradsymbol in der rechten oberen Ecke des Windows Admin Center-Dashboards aus.

      ![Auswählen des Zahnradsymbols in der rechten oberen Ecke des Dashboards zum Öffnen der Einstellungen](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. Wählen Sie im Menü **Einstellungen** unter **Gateway** die Option **Erweiterungen** aus.

   1. Wählen Sie die Registerkarte **Feeds** und dann **Hinzufügen** aus.

   1. Geben Sie https://aka.ms/wac-insiders-feed in das Textfeld ein, und wählen Sie **Hinzufügen** aus.

   1. Nachdem der Feed hinzugefügt wurde, navigieren Sie zur Registerkarte **Verfügbare Erweiterungen**. Es kann einen Moment dauern, bis die Liste der Erweiterungen aktualisiert wird.

   1. Suchen Sie auf der Registerkarte **Verfügbare Erweiterungen** in der Liste der Erweiterungen nach **Azure IoT Edge**. Markieren Sie diese Option, und wählen Sie über der Liste der Erweiterungen die Aufforderung **Installieren** aus.

   1. Nach Abschluss der Installation sollte Azure IoT Edge in der Liste der installierten Erweiterungen auf der Registerkarte **Installierte Erweiterungen** angezeigt werden.

## <a name="choose-your-provisioning-method"></a>Auswählen der Bereitstellungsmethode

Azure IoT Edge für Linux unter Windows unterstützt die folgenden Bereitstellungsmethoden:

* Manuelle Bereitstellung mit der Verbindungszeichenfolge Ihres IoT Edge-Geräts: Führen Sie für diese Methode die unter [Registrieren eines neuen Azure IoT Edge-Geräts](how-to-register-device.md) aufgeführten Schritte aus, um Ihr Gerät zu registrieren und eine Verbindungszeichenfolge abzurufen.
  * Wählen Sie als Authentifizierungsoption symmetrische Schlüssel aus, da selbstsignierte X.509-Zertifikate derzeit nicht von IoT Edge für Linux unter Windows unterstützt werden.
* Automatische Bereitstellung mit Device Provisioning Service (DPS) und symmetrischen Schlüsseln: Erfahren Sie mehr über das [Erstellen und Bereitstellen eines IoT Edge-Geräts mit DPS und symmetrischen Schlüsseln](how-to-auto-provision-symmetric-keys.md).
* Automatische Bereitstellung mithilfe von DPS und X.509-Zertifikaten: Erfahren Sie mehr über das [Erstellen und Bereitstellen eines IoT Edge-Geräts mit DPS und X.509-Zertifikaten](how-to-auto-provision-x509-certs.md).

Die manuelle Bereitstellung ist einfacher, um zunächst mit einigen wenigen Geräten zu beginnen. DPS (Device Provisioning Service) ist hilfreich für die Bereitstellung vieler Geräte.

Wenn Sie beabsichtigen, eine der DPS-Methoden für die Bereitstellung Ihrer Geräte zu verwenden, befolgen Sie die Schritte im entsprechenden Artikel, der oben verlinkt ist, um eine Instanz von DPS zu erstellen, Ihre DPS-Instanz mit Ihrem IoT-Hub zu verknüpfen und eine DPS-Registrierung zu erstellen. Sie können eine *individuelle Registrierung* für ein einzelnes Gerät oder eine *Gruppenregistrierung* für eine Gruppe von Geräten erstellen. Weitere Informationen zu den Registrierungstypen finden Sie unter den [Konzepten von Azure IoT Hub Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment).

## <a name="create-a-new-deployment"></a>Erstellen einer neuen Bereitstellung

Erstellen Sie Ihre Bereitstellung von Azure IoT Edge für Linux unter Windows auf dem Zielgerät.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Auf der Startseite von Windows Admin Center wird in der Liste der Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.

Sie können mit Windows Admin Center Azure IoT Edge für Linux unter Windows auf Ihrem lokalen Gerät und auf Remotegeräten installieren und verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows.

Wenn Sie die Bereitstellung nicht auf dem lokalen Gerät, sondern auf einem Remotezielgerät ausführen möchten, und das gewünschte Zielgerät nicht in der Liste angezeigt wird, befolgen Sie die Anweisungen zum [Hinzufügen Ihres Zielgeräts](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Anfängliches Windows Admin Center-Dashboard mit aufgeführtem Zielgerät](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Wählen Sie **Hinzufügen**.

1. Suchen Sie im Bereich **Ressourcen hinzufügen oder erstellen** die Kachel **Azure IoT Edge**. Wählen Sie **Neu erstellen** aus, um eine neue Instanz von Azure IoT Edge für Linux unter Windows auf einem Gerät zu installieren.

   Wenn Sie bereits über eine Instanz von IoT Edge für Linux unter Windows verfügen, die auf einem Gerät ausgeführt wird, können Sie **Hinzufügen** auswählen, um eine Verbindung mit diesem IoT Edge-Gerät herzustellen und es mit Windows Admin Center zu verwalten.

   ![Auswählen von „Neu erstellen“ auf der Kachel „Azure IoT Edge“ in Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Der Bereich **Create an Azure IoT Edge for Linux on Windows deployment** (Azure IoT Edge für Linux unter Windows-Bereitstellung erstellen) wird geöffnet. Überprüfen Sie auf der Registerkarte **1. Erste Schritte**, ob das Zielgerät die Mindestanforderungen erfüllt, und wählen Sie dann **Weiter** aus.

1. Lesen Sie die Lizenzbedingungen, aktivieren Sie **Ich stimme zu**, und wählen Sie **Weiter** aus.

1. Sie können **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten.

1. Klicken Sie auf **Weiter: Bereitstellen**.

   ![Auswählen der Schaltfläche „Weiter: Bereitstellen“ nach dem Umschalten von „Optionale Diagnosedaten“](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Klicken Sie auf der Registerkarte **2. Bereitstellen** unter **Zielgerät auswählen** auf das aufgeführte Gerät, um zu überprüfen, ob es die Mindestanforderungen erfüllt. Wenn als Status „Unterstützt“ angezeigt wird, wählen Sie **Weiter** aus.

   ![Auswählen des Geräts zur Überprüfung der Unterstützung](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Übernehmen Sie auf der Registerkarte **2.2 Einstellungen** die Standardeinstellungen.

1. Auf der Registerkarte **2.3 Bereitstellung** können Sie den Fortschritt der Bereitstellung überwachen. Der gesamte Prozess umfasst das Herunterladen und Installieren des Pakets von Azure IoT Edge für Linux unter Windows, das Konfigurieren des Hostgeräts und das Einrichten der Linux-VM. Es kann einige Minuten dauern, bis dieser Vorgang abgeschlossen ist. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung werden für jeden Schritt ein grünes Häkchen und die Bezeichnung „Erledigt“ angezeigt.](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen. Klicken Sie auf **Weiter: Verbinden**, um zur Registerkarte **3. Verbinden** zu wechseln, auf der die Bereitstellung des Azure IoT Edge-Geräts durchgeführt wird.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie IoT Edge für Linux unter Windows auf dem Zielgerät, sofern dies noch nicht erfolgt ist.

> [!NOTE]
> Im folgenden PowerShell-Prozess wird beschrieben, wie Sie eine lokale Hostbereitstellung von Azure IoT Edge für Linux unter Windows erstellen. Zum Erstellen einer Bereitstellung auf einem Remotezielgerät mithilfe von PowerShell können Sie mit [Remotebefehlen in PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) eine Verbindung mit einem Remotegerät herstellen und diese Befehle remote auf dem Gerät ausführen.

1. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten die folgenden Befehle nacheinander aus, um IoT Edge für Linux unter Windows herunterzuladen.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Eine benutzerdefinierte Installation von IoT Edge für Linux unter Windows sowie VHDX-Verzeichnissen geben Sie an, indem Sie dem Installationsbefehl oben die Parameter „INSTALLDIR = "<vollqualifizierter_Pfad>"“ und „VHDXDIR = "<vollqualifizierter_Pfad>"“ hinzufügen.

1. Damit die Bereitstellung erfolgreich ausgeführt werden kann, müssen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` festlegen, sofern dies nicht bereits erfolgt ist. Sie können die aktuelle Ausführungsrichtlinie in einer PowerShell-Eingabeaufforderung mit erhöhten Rechten wie folgt überprüfen:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Wenn die Ausführungsrichtlinie von `local machine` nicht `AllSigned` ist, können Sie die Ausführungsrichtlinie wie folgt festlegen:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Erstellen Sie die Bereitstellung von IoT Edge für Linux unter Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > Sie können diesen Befehl ohne Parameter ausführen oder die Bereitstellung optional mit Parametern anpassen. Sie finden die Parameter und ihre Bedeutung im PowerShell-Modul „AzureEFLOW.psm1“ (unter „C:\Programme\WindowsPowerShell\Modules\AzureEFLOW“).

1. Geben Sie „Y“ ein, um den Lizenzbedingungen zuzustimmen.

1. Durch die Eingabe von „O“ oder „R“ können Sie **Optionale Diagnosedaten** bei Bedarf ein- oder ausschalten. Eine erfolgreiche Bereitstellung ist unten dargestellt.

   ![Bei einer erfolgreichen Bereitstellung wird am Ende der Meldungen „Deployment successful“ (Bereitstellung erfolgreich) angezeigt.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Nach Abschluss dieses Vorgangs können Sie Ihr Gerät bereitstellen.

---

Sie können für die Bereitstellung Ihres Geräts über die folgenden Links zum Abschnitt mit der ausgewählten Bereitstellungsmethode navigieren:

* [Option 1: Manuelle Bereitstellung mit der Verbindungszeichenfolge Ihres IoT Edge-Geräts](#option-1-provisioning-manually-using-the-connection-string)
* [Option 2: Automatische Bereitstellung mit Device Provisioning Service (DPS) und symmetrischen Schlüsseln](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Option 3: Automatische Bereitstellung mithilfe von DPS und X.509-Zertifikaten](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Bereitstellen Ihres Geräts

Wählen Sie eine Methode für die Bereitstellung Ihres Geräts aus, und befolgen Sie die Anweisungen im zugehörigen Abschnitt. Sie können für das Bereitstellen Ihrer Geräte Windows Admin Center oder eine PowerShell-Sitzung mit erhöhten Rechten verwenden.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Option 1: Manuelles Bereitstellen mit der Verbindungszeichenfolge

In diesem Abschnitt wird das manuelle Bereitstellen Ihres Geräts mithilfe der Verbindungszeichenfolge Ihres Azure IoT Edge-Geräts beschrieben.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **Verbindungszeichenfolge (manuell)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zur Registerkarte **IoT Edge** Ihres IoT-Hubs.

1. Klicken Sie auf die Geräte-ID Ihres Geräts. Kopieren Sie den Wert im Feld **Primäre Verbindungszeichenfolge**.

1. Fügen Sie ihn in das Feld für die Geräteverbindungszeichenfolge in Windows Admin Center ein. Wählen Sie dann **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Einfügen der Verbindungszeichenfolge](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zur Registerkarte **IoT Edge** Ihres IoT-Hubs.

1. Klicken Sie auf die Geräte-ID Ihres Geräts. Kopieren Sie den Wert im Feld **Primäre Verbindungszeichenfolge**.

1. Ersetzen Sie den Platzhaltertext im folgenden Befehl, und führen Sie diesen in einer PowerShell-Sitzung mit erhöhten Rechten auf dem Zielgerät aus.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Option 2: Bereitstellung über DPS mithilfe von symmetrischen Schlüsseln

In diesem Abschnitt wird die automatische Bereitstellung von Geräten mithilfe von DPS und symmetrischen Schlüsseln beschrieben.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **Symmetrischer Schlüssel (DPS)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Kopieren Sie auf der Registerkarte **Übersicht** den Wert **ID-Bereich**. Fügen Sie ihn in Windows Admin Center in das Feld mit der Bereichs-ID ein.

1. Wählen Sie auf der Registerkarte **Registrierungen verwalten** im Azure-Portal die erstellte Registrierung aus. Kopieren Sie den Wert **Primärschlüssel** in den Registrierungsdetails. Fügen Sie ihn in Windows Admin Center in das Feld für den symmetrischen Schlüssel ein.

1. Geben Sie die Registrierungs-ID Ihres Geräts in das Feld „Registrierungs-ID“ in Windows Admin Center ein.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit symmetrischen Schlüsseln](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieren Sie den folgenden Befehl, und fügen Sie ihn in einen Text-Editor ein. Ersetzen Sie den Platzhaltertext wie angegeben durch Ihre Daten.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Kopieren Sie auf der Registerkarte **Übersicht** den Wert **ID-Bereich**. Fügen Sie ihn anstelle des entsprechenden Platzhaltertexts in den Befehl ein.

1. Wählen Sie auf der Registerkarte **Registrierungen verwalten** im Azure-Portal die erstellte Registrierung aus. Kopieren Sie den Wert **Primärschlüssel** in den Registrierungsdetails. Fügen Sie ihn anstelle des entsprechenden Platzhaltertexts in den Befehl ein.

1. Geben Sie die Registrierungs-ID des Geräts an, um den entsprechenden Platzhaltertext im Befehl zu ersetzen.

1. Führen Sie den Befehl in einer PowerShell-Sitzung mit erhöhten Rechten auf dem Zielgerät aus.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Option 3: Bereitstellung über DPS mithilfe von X.509-Zertifikaten

In diesem Abschnitt wird die automatische Bereitstellung von Geräten mithilfe von DPS und X.509-Zertifikaten beschrieben.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wählen Sie im Bereich **Azure IoT Edge-Gerätebereitstellung** in der Dropdownliste mit den Bereitstellungsmethoden **X.509-Zertifikat (DPS)** aus.

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Kopieren Sie auf der Registerkarte **Übersicht** den Wert **ID-Bereich**. Fügen Sie ihn in Windows Admin Center in das Feld mit der Bereichs-ID ein.

1. Geben Sie die Registrierungs-ID Ihres Geräts in das Feld „Registrierungs-ID“ in Windows Admin Center ein.

1. Laden Sie die Dateien mit Ihrem Zertifikat und dem privaten Schlüssel hoch.

1. Wählen Sie **Provisioning with the selected method** (Mit der ausgewählten Methode bereitstellen) aus.

   ![Auswählen von „Provisioning with the selected method“ (Mit der ausgewählten Methode bereitstellen) nach dem Ausfüllen der erforderlichen Felder für die Bereitstellung mit X.509-Zertifikaten](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Nachdem die Bereitstellung abgeschlossen ist, wählen Sie **Fertig stellen** aus. Daraufhin gelangen Sie wieder zum Hauptdashboard. Nun sollte ein neues Gerät mit dem Typ `IoT Edge Devices` aufgeführt werden. Sie können das IoT Edge-Gerät auswählen, um eine Verbindung damit herzustellen. Auf der zugehörigen Seite **Übersicht** können Sie die **Liste der IoT Edge-Module** und den **IoT Edge-Status** Ihres Geräts anzeigen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Kopieren Sie den folgenden Befehl, und fügen Sie ihn in einen Text-Editor ein. Ersetzen Sie den Platzhaltertext wie angegeben durch Ihre Daten.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zu Ihrer DPS-Instanz.

1. Kopieren Sie auf der Registerkarte **Übersicht** den Wert **ID-Bereich**. Fügen Sie ihn anstelle des entsprechenden Platzhaltertexts in den Befehl ein.

1. Geben Sie die Registrierungs-ID des Geräts an, um den entsprechenden Platzhaltertext im Befehl zu ersetzen.

1. Ersetzen Sie den entsprechenden Platzhaltertext durch den absoluten Pfad zur Quelle Ihrer Zertifikatsdatei.

1. Ersetzen Sie den entsprechenden Platzhaltertext durch den absoluten Pfad zur Quelle Ihrer Datei mit dem privaten Schlüssel.

1. Führen Sie den Befehl in einer PowerShell-Sitzung mit erhöhten Rechten auf dem Zielgerät aus.

---

## <a name="verify-successful-configuration"></a>Überprüfen der erfolgreichen Konfiguration

Vergewissern Sie sich, dass IoT Edge für Linux unter Windows erfolgreich auf Ihrem IoT Edge-Gerät installiert und konfiguriert wurde.

1. Wählen Sie Ihr IoT Edge-Gerät in der Liste der verbundenen Geräte in Windows Admin Center aus, um eine Verbindung damit herzustellen.
1. Auf der Übersichtsseite des Geräts werden Informationen zum Gerät angezeigt:

    1. Im Abschnitt mit der **Liste der IoT Edge-Module** werden die auf dem Gerät ausgeführten Module angezeigt. Wenn der IoT Edge-Dienst zum ersten Mal gestartet wird, sollte nur das Modul **edgeAgent** ausgeführt werden. Das Modul edgeAgent wird standardmäßig ausgeführt und unterstützt Sie beim Installieren und Starten von zusätzlichen Modulen, die Sie auf Ihrem Gerät bereitstellen.
    1. Im Abschnitt **IoT Edge-Status** wird der Dienststatus angezeigt. Dieser sollte **Aktiv (wird ausgeführt)** lauten.

1. Wenn Sie Probleme mit dem IoT Edge-Dienst beheben müssen, verwenden Sie das Tool **Befehlsshell** auf der Geräteseite, um per SSH (Secure Shell) eine Verbindung mit der VM herzustellen und auf dieser Linux-Befehle auszuführen.

    1. Sollte eine Problembehandlung für den Dienst erforderlich sein, rufen Sie die Dienstprotokolle ab.

       ```bash
       journalctl -u iotedge
       ```

    2. Verwenden Sie das Tool `check`, um die Konfiguration und den Verbindungsstatus des Geräts zu überprüfen.

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>Nächste Schritte

Sie können nun mit dem Artikel zum [Bereitstellen von IoT Edge-Modulen](how-to-deploy-modules-portal.md) fortfahren, um zu erfahren, wie Sie Module auf Ihrem Gerät bereitstellen.
