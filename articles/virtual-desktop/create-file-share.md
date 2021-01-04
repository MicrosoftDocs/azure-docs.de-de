---
title: Erstellen einer Azure Files-Dateifreigabe mit einem Domänencontroller (Azure)
description: Richten Sie einen FSLogix-Profilcontainer auf einer Azure-Dateifreigabe in einem vorhandenen Windows Virtual Desktop-Hostpool mit Ihrer Active Directory-Domäne ein.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8f8086aced26fc46fb1430df074082e8c3365baa
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746811"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Erstellen eines Profilcontainers mit Azure Files und AD DS

In diesem Artikel erfahren Sie, wie Sie eine Azure-Dateifreigabe erstellen, die von einem Domänencontroller auf einem vorhandenen Windows Virtual Desktop-Hostpool authentifiziert wird. Sie können diese Dateifreigabe zum Speichern von Speicherprofilen verwenden.

Bei diesem Prozess wird der lokale Verzeichnisdienst Active Directory Domain Services (AD DS) verwendet. Informationen zum Erstellen eines FSLogix-Profilcontainers mit Azure AD DS finden Sie unter [Erstellen eines FSLogix-Profilcontainers mit Azure Files](create-profile-container-adds.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Ihr Domänencontroller mit Azure synchronisiert und aus dem virtuellen Azure-Netzwerk (VNET), mit dem Ihre Sitzungshosts verbunden sind, aufgelöst werden kann.

## <a name="set-up-a-storage-account"></a>Einrichten eines Speicherkontos

Zunächst müssen Sie ein Azure Files-Speicherkonto einrichten.

So richten Sie ein Speicherkonto ein:

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie in der Suchleiste nach **Speicherkonto**.

3. Wählen Sie **+ Hinzufügen** aus.

4. Geben Sie auf der Seite **Speicherkonto erstellen** folgende Informationen ein:

    - Erstellen Sie eine neue Ressourcengruppe.
    - Geben Sie einen eindeutigen Namen für Ihr Speicherkonto ein.
    - Für **Speicherort** wird empfohlen, den gleichen Speicherort wie der Windows Virtual Desktop-Hostpool auszuwählen.
    - Wählen Sie für **Leistung** die Option **Standard** aus. (Abhängig von Ihren IOPS-Anforderungen. Weitere Informationen finden Sie unter [Speicheroptionen für FSLogix-Profilcontainer in Windows Virtual Desktop](store-fslogix-profile.md).)
    - Wählen Sie für **Kontotyp** die Option **StorageV2** oder **FileStorage-** (nur verfügbar, wenn die Leistungsstufe gleich „Premium“ ist) aus.
    - Wählen Sie für **Replikation** die Option **Lokal redundanter Speicher (LRS)** aus.

5. Wenn Sie fertig sind, klicken Sie auf **Überprüfen + erstellen** und dann auf **Erstellen**.

Genauere Anweisungen zur Konfiguration finden Sie unter [Regionale Verfügbarkeit](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability).

## <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe

Als Nächstes müssen Sie eine Azure-Dateifreigabe erstellen.

Gehen Sie wie folgt vor, um eine Dateifreigabe zu erstellen:

1. Wählen Sie **Zu Ressource wechseln** aus.

2. Klicken Sie in der Übersicht auf **Dateifreigaben**.

3. Klicken Sie auf **+Dateifreigaben** , erstellen Sie eine neue Dateifreigabe mit dem Namen **Profile** , und geben Sie dann entweder ein geeignetes Kontingent ein, oder lassen Sie das Feld leer, um kein Kontingent anzugeben.

4. Klicken Sie auf **Erstellen**.

## <a name="enable-active-directory-authentication"></a>Aktivieren von Active Directory-Authentifizierung

Als Nächstes müssen Sie die Active Directory-Authentifizierung (AD) aktivieren. Zum Aktivieren dieser Richtlinie müssen Sie die Anweisungen in diesem Abschnitt auf einem Computer befolgen, der bereits in die Domäne eingebunden ist. Befolgen Sie diese Anweisungen zum Aktivieren der Authentifizierung auf dem virtuellen Computer, auf dem der Domänencontroller ausgeführt wird:

1. Remotedesktopprotokoll im virtuellen Computer, der in die Domäne eingebunden ist

2. Befolgen Sie die Anweisungen unter [Teil 1: Aktivieren der AD DS-Authentifizierung für Ihre Dateifreigaben in Azure](../storage/files/storage-files-identity-ad-ds-enable.md), um das Modul „AzFilesHybrid“ zu installieren und die Authentifizierung zu aktivieren.

3.  Öffnen Sie im Azure-Portal Ihr Speicherkonto, klicken Sie auf **Konfiguration** , und bestätigen Sie dann, dass die Option **Active Directory (AD)** auf **Aktiviert** festgelegt ist.

     > [!div class="mx-imgBorder"]
     > ![Screenshot der Konfigurationsseite mit aktiviertem Azure Active Directory (AD)](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Zuweisen von Berechtigungen für Windows Virtual Desktop-Benutzer

Allen Benutzern, die FSLogix-Profile im Speicherkonto speichern müssen, muss die Rolle „Mitwirkender für Speicherdateidaten-SMB-Freigabe“ zugewiesen werden.

Benutzer, die sich bei den Windows Virtual Desktop-Sitzungshosts anmelden, benötigen Zugriffsberechtigungen für den Zugriff auf Ihre Dateifreigabe. Das Gewähren von Zugriff auf eine Azure-Dateifreigabe umfasst das Konfigurieren von Berechtigungen sowohl auf Freigabeebene als auch auf der NTFS-Ebene, ähnlich wie bei einer herkömmlichen Windows-Freigabe.

Zum Konfigurieren von Berechtigungen auf Freigabeebene weisen Sie jedem Benutzer eine Rolle mit den entsprechenden Zugriffsberechtigungen zu. Berechtigungen können entweder einzelnen Benutzern oder einer Azure AD-Gruppe zugewiesen werden. Weitere Informationen finden Sie unter [Zuweisen von Zugriffsberechtigungen zu einer Identität](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>Die Konten oder Gruppen, denen Sie Berechtigungen zuweisen, sollten in der Domäne erstellt und mit Azure AD synchronisiert worden sein. In Azure AD erstellte Konten funktionieren nicht.

Zuweisen von Berechtigungen über die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC):

1. Öffnen Sie das Azure-Portal.

2. Öffnen Sie das Speicherkonto, das Sie unter [Einrichten eines Speicherkontos](#set-up-a-storage-account) erstellt haben.

3. Wählen Sie **Dateifreigaben** aus, und wählen Sie dann den Namen der Dateifreigabe aus, die Sie verwenden möchten.

4. Wählen Sie **Access Control (IAM)** aus.

5. Wählen Sie **Rollenzuweisung hinzufügen** aus.

6. Wählen Sie auf der Registerkarte **Rollenzuweisung hinzufügen** für das Administratorkonto **Mitwirkender mit erhöhten Rechten für Speicherdateidaten-SMB-Freigabe** aus.

     Befolgen Sie dieselben Anweisungen, um Benutzern Berechtigungen für Ihre FSLogix-Profile zuzuweisen. Wenn Sie jedoch zu Schritt 5 gelangen, wählen Sie stattdessen **Mitwirkender für Speicherdateidaten-SMB-Freigabe** aus.

7. Wählen Sie **Speichern** aus.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Zuweisen von Benutzerberechtigungen für die Azure-Dateifreigabe

Nachdem Sie Ihren Benutzern Azure RBAC-Berechtigungen zugewiesen haben, müssen Sie als Nächstes die NTFS-Berechtigungen konfigurieren.

Sie benötigen zwei Information aus dem Azure-Portal, um loszulegen:

- UNC-Pfad
- Der Speicherkontoschlüssel.

### <a name="get-the-unc-path"></a>Abrufen des UNC-Pfads

So rufen Sie den UNC-Pfad ab:

1. Öffnen Sie das Azure-Portal.

2. Öffnen Sie das Speicherkonto, das Sie unter [Einrichten eines Speicherkontos](#set-up-a-storage-account) erstellt haben.

3. Klicken Sie auf **Einstellungen** und dann auf **Eigenschaften**.

4. Kopieren Sie den URI **Primärer Dateidienstendpunkt** in einen beliebigen Text-Editor.

5. Nachdem Sie den URI kopiert haben, führen Sie die folgenden Schritte aus, um ihn in den UNC zu ändern:

    - Ersetzen Sie `https://` durch `\\`.
    - Ersetzen Sie den Schrägstrich `/` durch einen umgekehrten Schrägstrich `\`.
    - Fügen Sie den Namen der Dateifreigabe, die Sie unter [Erstellen einer Azure-Dateifreigabe](#create-an-azure-file-share) erstellt haben, zum Ende des UNC-Pfads hinzu.

        Beispiel: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>Abrufen des Speicherkontoschlüssels

So rufen Sie den Speicherkontoschlüssel ab:

1. Öffnen Sie das Azure-Portal.

2. Öffnen Sie das Speicherkonto, das Sie unter [Einrichten eines Speicherkontos](#set-up-a-storage-account) erstellt haben.

3. Klicken Sie auf der Registerkarte **Speicherkonto** auf **Zugriffsschlüssel**.

4. Kopieren Sie **key1** oder **key2** in eine Datei auf dem lokalen Computer.

### <a name="configure-ntfs-permissions"></a>Konfigurieren von NTFS-Berechtigungen

So konfigurieren Sie NTFS-Berechtigungen:

1. Öffnen Sie eine Eingabeaufforderung auf einem in die Domäne eingebundenen virtuellen Computer.

2. Führen Sie den folgenden Befehl aus, um die Azure-Dateifreigabe bereitzustellen und ihr einen Laufwerkbuchstaben zuzuweisen:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. Führen Sie den folgenden Befehl aus, um die Zugriffsberechtigungen für die Azure-Dateifreigabe zu überprüfen:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    Ersetzen Sie `<mounted-drive-letter>` durch den Buchstaben des Laufwerks, das Sie zugeordnet haben.

    Sowohl *NT Authority\Authenticated Users* (NT-Autorität\Authentifizierte Benutzer) als auch *BUILTIN\Users* (Vordefiniert\Benutzer) verfügen standardmäßig über bestimmte Berechtigungen. Mit diesen Standardberechtigungen können diese Benutzer die Profilcontainer anderer Benutzer lesen. Mit den unter [Konfigurieren von Speicherberechtigungen für die Verwendung mit Profilcontainern und Office-Containern](/fslogix/fslogix-storage-config-ht) beschriebenen Berechtigungen können Benutzer jedoch nicht gegenseitig ihre Profilcontainer lesen.

4. Führen Sie die folgenden Befehle aus, damit Ihre Windows Virtual Desktop-Benutzer eigene Profilcontainer erstellen können, während sie den Zugriff auf ihren Profilcontainer durch andere Benutzer blockieren.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Ersetzen Sie <mounted-drive-letter> durch den Buchstaben des Laufwerks, den Sie zum Zuordnen des Laufwerks verwendet haben.
     - Ersetzen Sie <user-email> durch den UPN des Benutzers oder der Active Directory-Gruppe, die die Benutzer mit erforderlichem Zugriff auf die Freigabe enthält.

     Beispiel:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>Konfigurieren von FSLogix auf Sitzungshost-VMs

In diesem Abschnitt erfahren Sie, wie Sie eine VM mit FSLogix konfigurieren. Sie müssen diese Anweisungen jedes Mal befolgen, wenn Sie einen Sitzungshost konfigurieren. Bevor Sie mit der Konfiguration beginnen, befolgen Sie die Anweisungen unter [Herunterladen und Installieren von FSLogix](/fslogix/install-ht). Es stehen mehrere Optionen zur Verfügung, die sicherstellen, dass die Registrierungsschlüssel auf allen Sitzungshosts festgelegt sind. Sie können diese Optionen in einem Image festlegen oder eine Gruppenrichtlinie konfigurieren.

So konfigurieren Sie FSLogix auf Ihrer Sitzungshost-VM:

1. RDP zur Sitzungshost-VM des Windows Virtual Desktop-Hostpools

2. [Laden Sie FSLogix herunter, und installieren Sie es](/fslogix/install-ht).

5. Befolgen Sie die Anweisungen unter [Konfigurieren der Einstellungen für die Profilcontainerregistrierung:](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)

    - Navigieren Sie zu **Computer** > **HKEY_LOCAL_MACHINE** > **SOFTWARE** > **FSLogix**.

    - Erstellen Sie einen **Profiles** -Schlüssel.

    - Erstellen Sie **Enabled, DWORD** mit dem Wert 1.

    - Erstellen Sie **VHDLocations, MULTI_SZ**.

    - Legen Sie den Wert **VHDLocations** auf den UNC-Pfad fest, den Sie unter [Abrufen des UNC-Pfads](#get-the-unc-path) erzeugt haben.

6. Starten Sie den virtuellen Computer neu.

## <a name="testing"></a>Testen

Nachdem Sie FSLogix installiert und konfiguriert haben, können Sie die Bereitstellung testen, indem Sie sich mit einem Benutzerkonto anmelden, dem eine App-Gruppe oder ein Desktop im Hostpool zugewiesen wurde. Stellen Sie sicher, dass das Benutzerkonto, mit dem Sie sich anmelden, über Berechtigungen für die Dateifreigabe verfügt.

Wenn sich der Benutzer zuvor bereits angemeldet hat, verfügt er über ein vorhandenes lokales Profil, das während dieser Sitzung verwendet wird. Wenn Sie das Erstellen eines lokalen Profils vermeiden möchten, erstellen Sie entweder ein neues Benutzerkonto für Tests, oder verwenden Sie die Konfigurationsmethoden, die im folgenden Artikel beschrieben werden: [Tutorial: Konfigurieren des Profilcontainers zum Umleiten von Benutzerprofilen](/fslogix/configure-profile-container-tutorial/).

So überprüfen Sie die Berechtigungen für Ihre Sitzung:

1. Starten Sie eine Sitzung in Windows Virtual Desktop.

2. Öffnen Sie das Azure-Portal.

3. Öffnen Sie das Speicherkonto, das Sie unter [Einrichten eines Speicherkontos](#set-up-a-storage-account) erstellt haben.

4. Klicken Sie auf der Seite zum Erstellen einer Azure-Dateifreigabe auf **Freigabe erstellen**.

5. Stellen Sie sicher, dass in Ihren Dateien jetzt ein Ordner mit dem Benutzerprofil vorhanden ist.

Befolgen Sie für weitere Tests die Anweisungen unter [Sicherstellen, dass Ihr Profil funktioniert](create-profile-container-adds.md#make-sure-your-profile-works).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Behandeln von Problemen mit FSLogix finden Sie in [diesem Leitfaden zur Problembehandlung](/fslogix/fslogix-trouble-shooting-ht).
