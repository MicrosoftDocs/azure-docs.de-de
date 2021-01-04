---
title: FSLogix-Profilcontainer mit NetApp in Windows Virtual Desktop – Azure
description: Es wird beschrieben, wie Sie einen FSLogix-Profilcontainer mit Azure NetApp Files in Windows Virtual Desktop erstellen.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016827"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Erstellen eines Profilcontainers mit Azure NetApp Files und AD DS

Wir empfehlen Ihnen die Verwendung des FSLogix-Profilcontainers als Benutzerprofillösung für den [Dienst „Windows Virtual Desktop“](overview.md). Bei FSLogix-Profilcontainern wird ein vollständiges Benutzerprofil in einem einzelnen Container gespeichert. Sie sind so konzipiert, dass für Profile in nicht persistenten Remotecomputingumgebungen, z. B. Windows Virtual Desktop, ein Roaming durchgeführt wird. Wenn Sie sich anmelden, wird der Container dynamisch an die Computingumgebung angefügt, indem eine lokal unterstützte virtuelle Festplatte (VHD) und eine virtuelle Hyper-V-Festplatte (VHDX) verwendet wird. Aufgrund dieser modernen Filtertreibertechnologie ist das Benutzerprofil sofort verfügbar und wird im System genauso wie ein lokales Benutzerprofil angezeigt. Weitere Informationen zu FSLogix-Profilcontainern finden Sie unter [FSLogix-Profilcontainer und Azure Files](fslogix-containers-azure-files.md).

Sie können FSLogix-Profilcontainer erstellen, indem Sie [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) nutzen. Hierbei handelt es sich um einen einfach zu verwendenden nativen Azure-Plattformdienst, mit dem Kunden schnell und zuverlässig SMB-Volumes auf Unternehmensniveau für ihre Windows Virtual Desktop-Umgebungen bereitstellen können. Weitere Informationen zu Azure NetApp Files finden Sie unter [Was ist Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md).

In dieser Anleitung wird veranschaulicht, wie Sie ein Azure NetApp Files-Konto einrichten und in Windows Virtual Desktop FSLogix-Profilcontainer erstellen.

In diesem Artikel wird vorausgesetzt, dass Sie bereits [Hostpools](create-host-pools-azure-marketplace.md) eingerichtet und in Ihrer Windows Virtual Desktop-Umgebung in einem oder mehreren Mandanten gruppiert haben. Weitere Informationen zum Einrichten von Mandanten finden Sie unter [Erstellen eines Mandanten in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) und in [unserem Tech Community-Blogbeitrag](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Die Anleitungen in diesem Leitfaden gelten speziell für Windows Virtual Desktop-Benutzer. Eine allgemeinere Anleitung zur Einrichtung von Azure NetApp Files und Erstellung von FSLogix-Profilcontainern außerhalb von Windows Virtual Desktop finden Sie unter [Set up Azure NetApp Files and create an NFS volume quickstart](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md) (Einrichten von Azure NetApp Files und Erstellen eines NFS-Volumes: Schnellstartanleitung).

>[!NOTE]
>In diesem Artikel werden die bewährten Methoden zum Schützen des Zugriffs auf die Azure NetApp Files-Freigabe nicht behandelt.

>[!NOTE]
>Wenn Sie nach Vergleichsmaterial zu den verschiedenen Speicheroptionen des FSLogix-Profilcontainers in Azure suchen, lesen Sie [Speicheroptionen für FSLogix-Profilcontainer](store-fslogix-profile.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen FSLogix-Profilcontainer für einen Hostpool erstellen können, müssen Sie Folgendes durchführen:

- Einrichten und Konfigurieren von Windows Virtual Desktop
- Bereitstellen eines Windows Virtual Desktop-Hostpools
- [Registrieren für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Einrichten Ihres Azure NetApp Files-Kontos

Zunächst müssen Sie ein Azure NetApp Files-Konto einrichten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Stellen Sie sicher, dass Ihr Konto über Berechtigungen vom Typ „Mitwirkender“ oder „Administrator“ verfügt.

2. Wählen Sie rechts von der Suchleiste das **Azure Cloud Shell-Symbol** aus, um Azure Cloud Shell zu öffnen.

3. Wählen Sie nach dem Öffnen von Azure Cloud Shell die Option **PowerShell** aus.

4. Gehen Sie wie folgt vor, falls Sie Azure Cloud Shell zum ersten Mal verwenden: Erstellen Sie ein Speicherkonto unter demselben Abonnement, unter dem sich auch Azure NetApp Files und Windows Virtual Desktop befinden.

   > [!div class="mx-imgBorder"]
   > ![Das Speicherkontofenster mit der Schaltfläche zum Erstellen von Speicher ist am unteren Rand rot hervorgehoben.](media/create-storage-button.png)

5. Führen Sie nach dem Laden von Azure Cloud Shell die folgenden beiden Cmdlets aus.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Wählen Sie auf der linken Seite des Fensters die Option **Alle Dienste** aus. Geben Sie im Suchfeld, das oben im Menü angezeigt wird, **Azure NetApp Files** ein.

   > [!div class="mx-imgBorder"]
   > ![Ein Screenshot mit einem Benutzer, der in das Suchfeld von „Alle Dienste“ den Suchbegriff „Azure NetApp Files“ eingibt. In den Suchergebnissen wird die Azure NetApp Files-Ressource angezeigt.](media/azure-netapp-files-search-box.png)


7. Wählen Sie in den Suchergebnissen den Eintrag **Azure NetApp Files** und dann die Option **Erstellen** aus.

8. Wählen Sie die Schaltfläche **Hinzufügen** aus.
9. Geben Sie die folgenden Werte ein, nachdem die Registerkarte **Neues NetApp-Konto** geöffnet wurde:

    - Geben Sie unter **Name** den Namen Ihres NetApp-Kontos ein.
    - Wählen Sie unter **Abonnement** im Dropdownmenü das Abonnement für das Speicherkonto aus, das Sie in Schritt 4 eingerichtet haben.
    - Wählen Sie unter **Ressourcengruppe** entweder im Dropdownmenü eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue, indem Sie die Option **Neu erstellen** auswählen.
    - Wählen Sie unter **Standort** im Dropdownmenü die Region für Ihr NetApp-Konto aus. Diese Region muss dieselbe Region wie für Ihre Sitzungshost-VMs sein.

   >[!NOTE]
   >Azure NetApp Files unterstützt derzeit nicht die regionsübergreifende Einbindung eines Volumes.

10. Wählen Sie nach Abschluss des Vorgangs die Option **Erstellen** aus, um Ihr NetApp-Konto zu erstellen.

## <a name="create-a-capacity-pool"></a>Einrichten eines Kapazitätspools

Erstellen Sie als Nächstes einen neuen Kapazitätspool:

1. Navigieren Sie zum Azure NetApp Files-Menü, und wählen Sie Ihr neues Konto aus.
2. Wählen Sie in Ihrem Kontomenü unter dem Storage-Dienst die Option **Kapazitätspools** aus.
3. Wählen Sie **Pool hinzufügen** aus.
4. Geben Sie die folgenden Werte ein, nachdem die Registerkarte **Neuer Kapazitätspool** geöffnet wurde:

    - Geben Sie unter **Name** einen Namen für den neuen Kapazitätspool ein.
    - Wählen Sie unter **Servicelevel** im Dropdownmenü Ihren gewünschten Wert aus. Für die meisten Umgebungen lautet unsere Empfehlung **Premium**.
       >[!NOTE]
       >Bei der Einstellung „Premium“ wird der kleinstmögliche Durchsatz bereitgestellt, der für einen Premium-Servicelevel verfügbar ist (256 MBit/s). Unter Umständen müssen Sie diesen Durchsatz für eine Produktionsumgebung anpassen. Der endgültige Durchsatz basiert auf der Beziehung, die unter [Durchsatzlimits](../azure-netapp-files/azure-netapp-files-service-levels.md) beschrieben ist.
    - Geben Sie unter **Größe (TiB)** die Kapazitätspoolgröße ein, die für Ihre Anforderungen am besten geeignet ist. Die Mindestgröße beträgt 4 TiB.

5. Klicken Sie auf **OK**, wenn Sie fertig sind.

## <a name="join-an-active-directory-connection"></a>Durchführen des Beitritts zu einer Active Directory-Verbindung

Als Nächstes müssen Sie den Beitritt zu einer Active Directory-Verbindung durchführen.

1. Wählen Sie im Menü links auf der Seite die Option **Active Directory-Verbindungen** und dann die Schaltfläche **Beitreten** aus, um die Seite **Active Directory beitreten** zu öffnen.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Menü zum Beitreten zu Active Directory-Verbindungen](media/active-directory-connections-menu.png)

2. Geben Sie auf der Seite **Active Directory beitreten** die folgenden Werte ein, um den Beitritt zu einer Verbindung durchzuführen:

    - Geben Sie unter **Primäres DNS** die IP-Adresse des DNS-Servers in Ihrer Umgebung ein, der den Domänennamen auflösen kann.
    - Geben Sie unter **Domäne** Ihren vollqualifizierten Domänennamen (FQDN) ein.
    - Geben Sie unter **Präfix des SMB-Servers (Computerkonto)** die Zeichenfolge ein, die Sie an den Computerkontonamen anfügen möchten.
    - Geben Sie unter **Benutzername** den Namen des Kontos mit Berechtigungen zum Durchführen eines Domänenbeitritts ein.
    - Geben Sie unter **Kennwort** das Kennwort des Kontos ein.

## <a name="create-a-new-volume"></a>Erstellen eines neuen Volumes

Als Nächstes müssen Sie ein neues Volume erstellen.

1. Wählen Sie **Volumes** und dann **Volume hinzufügen** aus.

2. Geben Sie die folgenden Werte ein, nachdem die Registerkarte **Volume erstellen** geöffnet wurde:

    - Geben Sie unter **Volumename** einen Namen für das neue Volume ein.
    - Wählen Sie unter **Kapazitätspool** im Dropdownmenü den gerade erstellten Kapazitätspool aus.
    - Geben Sie unter **Kontingent (GiB)** die Volumegröße ein, die für Ihre Umgebung geeignet ist.
    - Wählen Sie unter **Virtuelles Netzwerk** im Dropdownmenü ein vorhandenes virtuelles Netzwerk aus, für das Konnektivität mit dem Domänencontroller besteht.
    - Wählen Sie unter **Subnetz** die Option **Neu erstellen** aus. Beachten Sie, dass dieses Subnetz an Azure NetApp Files delegiert wird.

3.  Klicken Sie auf **Weiter: Protokoll \>\>** , um die Registerkarte „Protokoll“ zu öffnen, und konfigurieren Sie Ihre Parameter für den Zugriff auf das Volume.

## <a name="configure-volume-access-parameters"></a>Konfigurieren von Parametern für den Volumezugriff

Konfigurieren Sie die Parameter für den Volumezugriff, nachdem Sie das Volume erstellt haben.

1.  Wählen Sie **SMB** als Protokolltyp aus.
2.  Wählen Sie unter „Konfiguration“ im Dropdownmenü **Active Directory** dasselbe Verzeichnis aus, mit dem Sie unter [Durchführen des Beitritts zu einer Active Directory-Verbindung](create-fslogix-profile-container.md#join-an-active-directory-connection) ursprünglich eine Verbindung hergestellt haben. Beachten Sie, dass eine Beschränkung besteht, gemäß der pro Abonnement nur eine Active Directory-Instanz zulässig ist.
3.  Geben Sie im Textfeld **Freigabename** den Namen der Freigabe ein, die vom Sitzungshostpool und dessen Benutzern verwendet wird.

4.  Wählen Sie am unteren Rand der Seite die Option **Bewerten + erstellen** aus. Die Überprüfungsseite wird geöffnet. Wählen Sie **Erstellen** aus, nachdem die Überprüfung Ihres Volumes erfolgreich abgeschlossen wurde.

5.  An diesem Punkt wird mit der Bereitstellung des neuen Volumes begonnen. Nach Abschluss der Bereitstellung können Sie die Azure NetApp Files-Freigabe nutzen.

6.  Wählen Sie zum Anzeigen des Einbindungspfads die Option **Zu Ressource wechseln** aus, und suchen Sie auf der Registerkarte „Übersicht“ danach.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Übersichtsfenster mit rotem Pfeil, der auf den Einbindungspfad zeigt](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurieren von FSLogix auf virtuellen Sitzungshostcomputern (VMs)

Dieser Abschnitt basiert auf [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md).

1. [Laden Sie die ZIP-Datei für den FSLogix-Agent herunter](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409), während Sie weiterhin über die Sitzungshost-VM den Remotezugriff nutzen.

2. Extrahieren Sie die heruntergeladene Datei.

3. Navigieren Sie in der Datei zu **x64** > **Releases**, und führen Sie **FSLogixAppsSetup.exe** aus. Das Installationsmenü wird geöffnet.

4.  Geben Sie Ihren Product Key in das entsprechende Feld ein, falls Sie über einen Product Key verfügen.

5. Aktivieren Sie das Kontrollkästchen **Ich stimme den Lizenzbedingungen zu**.

6. Wählen Sie **Installieren** aus.

7. Navigieren Sie zu **C:\\Programme\\FSLogix\\Apps**, um sich zu vergewissern, dass der Agent installiert wurde.

8. Führen Sie im Startmenü **RegEdit** als Administrator aus.

9. Navigieren Sie zu **Computer\\HKEY_LOCAL_MACHINE\\Software\\FSLogix**.

10. Erstellen Sie einen Schlüssel mit dem Namen **Profile**.

11.  Erstellen Sie einen Wert mit dem Namen **Enabled**, für den der Typ **REG_DWORD** auf den Datenwert **1** festgelegt ist.

12. Erstellen Sie einen Wert mit dem Namen **VHDLocations** mit dem Typ **Multi-String**, und legen Sie den Datenwert auf den URI für die Azure NetApp Files-Freigabe fest.

13. Erstellen Sie vor dem Anmelden einen Wert namens **DeleteLocalProfileWhenVHDShouldApply** mit dem DWORD-Wert 1, um Probleme mit vorhandenen lokalen Profilen zu vermeiden.

     >[!WARNING]
     >Seien Sie beim Erstellen des Werts für DeleteLocalProfileWhenVHDShouldApply vorsichtig. Wenn das FSLogix Profiles-System feststellt, dass ein Benutzer über ein FSLogix-Profil verfügen muss, jedoch bereits ein lokales Profil vorhanden ist, wird das lokale Profil vom Profile-Container dauerhaft gelöscht. Der Benutzer wird dann mit dem neuen FSLogix-Profil angemeldet.

## <a name="assign-users-to-session-host"></a>Zuweisen von Benutzern zum Sitzungshost

1. Öffnen Sie **PowerShell ISE** als Administrator, und melden Sie sich an Windows Virtual Desktop an.

2. Führen Sie die folgenden Cmdlets aus:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen für den Benutzer mit den Rollen „Ersteller“ (für Mandanten) oder „Besitzer/Mitwirkender“ (für RDS) auf dem Windows Virtual Desktop-Mandanten ein.

4. Führen Sie die folgenden Cmdlets aus, um einen Benutzer einer Remotedesktopgruppe zuzuweisen:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Sicherstellen, dass Benutzer auf die Azure NetApp Files-Freigabe zugreifen können

1. Öffnen Sie Ihren Internetbrowser, und navigieren Sie zu <https://rdweb.wvd.microsoft.com/arm/webclient>.

2. Melden Sie sich mit den Anmeldeinformationen eines Benutzers an, der der Remotedesktopgruppe zugewiesen ist.

3. Melden Sie sich nach dem Einrichten der Benutzersitzung mit einem Administratorkonto beim Azure-Portal an.

4. Öffnen Sie **Azure NetApp Files**, und wählen Sie Ihr Azure NetApp Files-Konto und dann die Option **Volumes** aus. Wählen Sie das entsprechende Volume aus, nachdem das Menü „Volumes“ geöffnet wurde.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Im Azure-Portal eingerichtetes NetApp-Konto mit ausgewählter Schaltfläche „Volumes“](media/netapp-account.png)

5. Navigieren Sie zur Registerkarte **Übersicht**, und vergewissern Sie sich, dass der FSLogix-Profilcontainer Speicherplatz belegt.

6. Stellen Sie eine direkte Verbindung mit einem beliebigen VM-Bestandteil des Hostpools per Remotedesktop her, und öffnen Sie den **Datei-Explorer**. Navigieren Sie zu **Einbindungspfad**  (im folgenden Beispiel lautet der Einbindungspfad \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   In diesem Ordner sollte eine Profil-VHD (oder VHDX) wie im folgenden Beispiel enthalten sein.

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Inhalt des Ordners im Einbindungspfad. Er enthält eine VHD-Datei mit dem Namen „Profile_ssbb“.](media/mount-path-folder.png)

## <a name="next-steps"></a>Nächste Schritte

Sie können FSLogix-Profilcontainer verwenden, um eine Benutzerprofilfreigabe einzurichten. Informationen zur Erstellung von Benutzerprofilfreigaben mit Ihren neuen Containern finden Sie unter [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md).

Sie können auch eine Azure Files-Dateifreigabe erstellen, in dem Sie Ihr FSLogix-Profil speichern können. Weitere Informationen finden Sie unter [Erstellen einer Azure Files-Dateifreigabe mit einem Domänencontroller](create-file-share.md).