---
title: Veröffentlichen einer Web-App auf einem virtuellen Azure-Computer aus Visual Studio
description: Veröffentlichen einer ASP.NET-Webanwendung auf einem virtuellen Azure-Computer aus Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 7cf2474c9ca005d85cea28f2b8fa1e23836b191f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487557"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Veröffentlichen einer ASP.NET-Web-App auf einer Azure-VM aus Visual Studio

Dieses Dokument beschreibt das Veröffentlichen einer ASP.NET-Webanwendung auf einem virtuellen Azure-Computer (VM) mithilfe des Veröffentlichungsfeatures **Microsoft Azure Virtual Machines** in Visual Studio 2019.  

## <a name="prerequisites"></a>Voraussetzungen
Damit Sie mit Visual Studio ein ASP.NET-Projekt auf einer Azure-VM veröffentlichen können, muss der virtuelle Computer ordnungsgemäß eingerichtet werden.

- Der Computer muss für das Ausführen einer ASP.NET-Webanwendung konfiguriert sein, und WebDeploy muss darauf installiert sein. Weitere Informationen finden Sie unter [Erstellen einer ASP.NET-VM mit WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- Für den virtuellen Computer muss ein DNS-Name konfiguriert sein. Weitere Informationen finden Sie unter [Erstellen eines vollqualifizierten Domänennamens im Azure-Portal für eine Windows-VM](../create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Veröffentlichen der ASP.NET-Web-App auf der Azure-VM mit Visual Studio
Der folgende Abschnitt beschreibt das Veröffentlichen einer vorhandenen ASP.NET-Webanwendung auf einem virtuellen Azure-Computer.

1. Öffnen Sie Ihre Web-App-Projektmappe in Visual Studio 2019.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
3. Scrollen Sie mit dem Pfeil auf der rechten Seite durch die Veröffentlichungsoptionen bis **Microsoft Azure Virtual Machines**.  

   ![Seite „Veröffentlichen“ – Pfeil rechts]

4. Wählen Sie das Symbol **Microsoft Azure Virtual Machines** und dann **Veröffentlichen** aus.

   ![Seite „Veröffentlichen“ – Symbol „Microsoft Azure Virtual Machine“]

5. Wählen Sie das gewünschte Konto aus (dessen Azure-Abonnement mit dem virtuellen Computer verknüpft ist).  
   - Wenn Sie in Visual Studio angemeldet sind, wird die Kontoliste mit all Ihren authentifizierten Konten gefüllt.  
   - Wenn Sie nicht angemeldet sind oder wenn das benötigte Konto nicht aufgeführt ist, wählen Sie „Konto hinzufügen“ aus, und befolgen Sie die Anweisungen für die Anmeldung.  
   ![Azure-Kontoauswahl]  

6. Wählen Sie den entsprechenden virtuellen Computer in der Liste der vorhandenen virtuellen Computer aus.

   > [!Note]
   > Das Füllen dieser Liste kann einige Zeit in Anspruch nehmen.

   ![Azure-VM-Auswahl]

7. Klicken Sie auf „OK“, um mit der Veröffentlichung zu beginnen.

8. Wenn Sie zum Eingeben von Anmeldeinformationen aufgefordert werden, geben Sie den Benutzernamen und das Kennwort eines Benutzerkontos auf der Ziel-VM an, das mit Veröffentlichungsrechten konfiguriert ist. Diese Anmeldeinformationen sind normalerweise der Administratorbenutzername und das Kennwort, die beim Erstellen des virtuellen Computers verwendet wurden.  

   ![WebDeploy-Anmeldung]

9. Akzeptieren Sie das Sicherheitszertifikat.

   ![Zertifikatfehler]

10. Überprüfen Sie im Ausgabefenster den Fortschritt des Veröffentlichungsvorgangs.

    ![Ausgabefenster]

11. Wenn die Veröffentlichung erfolgreich war, wird ein Browser gestartet, in dem Sie die URL der neu veröffentlichten Website öffnen können.

**Erfolg!**

Sie haben Ihre Web-App damit erfolgreich auf einem virtuellen Azure-Computer veröffentlicht.

## <a name="publish-page-options"></a>Optionen auf der Seite „Veröffentlichen“

Nach Abschluss des Veröffentlichungs-Assistenten wird im Dokument die Seite „Veröffentlichen“ geöffnet, auf der das neue Veröffentlichungsprofil bereits ausgewählt ist.

### <a name="re-publish"></a>Erneutes Veröffentlichen

Wählen Sie zum Veröffentlichen von Updates für Ihre Webanwendung die Schaltfläche **Veröffentlichen** auf der Seite „Veröffentlichen“ aus.  
- Geben Sie den Benutzernamen und das Kennwort ein, wenn Sie dazu aufgefordert werden.  
- Die Veröffentlichung beginnt sofort.

![Seite „Veröffentlichen“ – Schaltfläche „Veröffentlichen“]

### <a name="modify-publish-profile-settings"></a>Ändern der Einstellungen für das Veröffentlichungsprofil

Wählen Sie zum Anzeigen und Ändern der Einstellungen für das Veröffentlichungsprofil **Einstellungen** aus.  

![Seite „Veröffentlichen“ – Schaltfläche „Einstellungen“]

Die Einstellungen sollten in etwa wie folgt aussehen:  

![Veröffentlichungseinstellungen – Seite „Verbindung“]

#### <a name="save-user-name-and-password"></a>Speichern von Benutzername und Kennwort
- Vermeiden der Bereitstellung von Authentifizierungsinformationen bei jedem Mal, wenn Sie veröffentlichen. Füllen Sie zu diesem Zweck die Felder **Benutzernamen** und **Kennwort** aus, und aktivieren Sie das Feld **Kennwort speichern**.
- Mit der Schaltfläche **Verbindung überprüfen** können Sie sich vergewissern, dass Sie die richtigen Informationen eingegeben haben.

#### <a name="deploy-to-clean-web-server"></a>Bereitstellen auf einem bereinigten Webserver

- Wenn Sie nach jedem Upload sicherstellen möchten, dass der Webserver über eine bereinigte Kopie der Webanwendung verfügt (und dass keine anderen Dateien aus früheren Bereitstellungen zurück geblieben sind), aktivieren Sie auf der Registerkarte **Einstellungen** das Kontrollkästchen **Zusätzliche Dateien am Ziel entfernen**.

- Warnung: Durch das Veröffentlichen mit dieser Einstellung werden alle Dateien auf dem Webserver (Verzeichnis „wwwroot“) gelöscht. Sie sollten unbedingt den Zustand des Computers kennen, bevor Sie eine Veröffentlichung mit dieser Option durchführen. 

![Veröffentlichungseinstellungen – Seite „Einstellungen“]

## <a name="next-steps"></a>Nächste Schritte

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Einrichten von CI/CD für automatisierte Bereitstellungen auf Azure-VMs

Informationen zum Einrichten einer Pipeline für die fortlaufende Übermittlung mit Azure Pipelines finden Sie unter [Deploy to a Windows Virtual Machine](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups) (Bereitstellen auf einem virtuellen Windows-Computer).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Seite „Veröffentlichen“ – Pfeil rechts]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Seite „Veröffentlichen“ – Symbol „Microsoft Azure Virtual Machine“]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure-Kontoauswahl]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure-VM-Auswahl]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[WebDeploy-Anmeldung]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Zertifikatfehler]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Ausgabefenster]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Seite „Veröffentlichen“ – Schaltfläche „Veröffentlichen“]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Seite „Veröffentlichen“ – Schaltfläche „Einstellungen“]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Veröffentlichungseinstellungen – Seite „Verbindung“]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Veröffentlichungseinstellungen – Seite „Einstellungen“]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png