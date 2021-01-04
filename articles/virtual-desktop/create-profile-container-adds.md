---
title: Erstellen eines FSLogix-Profilcontainers mit Azure Files und Active Directory Domain Services – Azure
description: In diesem Artikel wird beschrieben, wie ein FSLogix-Profil-Container mit Azure Files und Azure Active Directory Domain Services erstellt wird.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70a56b7efc34ba2fd3c06521c6e4cac6ea28778f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302466"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>Erstellen eines Profilcontainers mit Azure Files und Azure AD DS

In diesem Artikel wird gezeigt, wie ein FSLogix-Profilcontainer mit Azure Files und Azure Active Directory Domain Services (AD DS) erstellt wird.

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie bereits eine Azure AD DS-Instanz eingerichtet haben. Falls nicht, befolgen Sie zunächst die Anweisungen unter [Erstellen einer einfachen verwalteten Domäne](../active-directory-domain-services/tutorial-create-instance.md), und kehren Sie dann hierher zurück.

## <a name="add-azure-ad-ds-admins"></a>Hinzufügen von Azure AD DS-Administratoren

Wenn Sie weitere Administratoren hinzufügen möchten, erstellen Sie einen neuen Benutzer und erteilen ihm Berechtigungen.

So fügen Sie einen Administrator hinzu

1. Wählen Sie auf der Randleiste **Azure Active Directory**, **Alle Benutzer** und dann **Neuer Benutzer** aus.

2.  Geben Sie die Benutzerdetails in die Felder ein.

3. Wählen Sie im Bereich „Azure Active Directory“ auf der linken Seite des Bildschirms **Gruppen** aus.

4. Wählen Sie die Gruppe **AAD DC-Administratoren** aus.

5. Wählen Sie im linken Bereich **Mitglieder** und dann im Hauptbereich **Mitglieder hinzufügen** aus. Dadurch wird eine Liste aller in Azure AD verfügbaren Benutzer angezeigt. Wählen Sie den Namen des Benutzerprofils aus, das Sie gerade erstellt haben.

## <a name="set-up-an-azure-storage-account"></a>Einrichten eines Azure Storage-Kontos

Aktivieren Sie nun die Azure AD DS-Authentifizierung über Server Message Block (SMB).

So aktivieren Sie die Authentifizierung

1. Falls noch nicht geschehen, müssen Sie ein universelles Azure Storage-Konto (v2) durch Befolgen der Anweisungen unter [Erstellen eines Azure Storage-Kontos](../storage/common/storage-account-create.md) einrichten und bereitstellen.

2. Wenn das Einrichten Ihres Kontos abgeschlossen ist, wählen Sie **Zu Ressource wechseln** aus.

3. Wählen Sie links auf dem Bildschirm **Konfiguration** aus. Aktivieren Sie anschließend im Hauptbereich **Azure Active Directory-Authentifizierung für Azure Files**. Klicken Sie auf **Speichern**, wenn Sie fertig sind.

4. Wählen Sie links auf dem Bildschirm **Übersicht** und dann im Hauptbereich **Dateien** aus.

5. Wählen Sie **Dateifreigabe** aus, und geben Sie **Name** und **Kontingent** in die Felder rechts auf dem Bildschirm ein.

## <a name="assign-access-permissions-to-an-identity"></a>Zuweisen von Zugriffsberechtigungen zu einer Identität

Andere Benutzer benötigen Zugriffsberechtigungen für die Dateifreigabe. Zu diesem Zweck müssen Sie jedem Benutzer eine Rolle mit den entsprechenden Zugriffsberechtigungen zuweisen.

So weisen Sie Benutzern Zugriffsberechtigungen zu

1. Öffnen Sie im Azure-Portal die Dateifreigabe, die Sie unter [Einrichten eines Azure Storage-Kontos](#set-up-an-azure-storage-account) erstellt haben.

2. Wählen Sie **Access Control (IAM)** aus.

3. Wählen Sie **Rollenzuweisung hinzufügen** aus.

4. Wählen Sie auf der Registerkarte **Rollenzuweisung hinzufügen** in der Liste „Rolle“ die entsprechende integrierte Rolle aus. Sie müssen für das Konto zumindest **Mitwirkender für Speicherdateidaten-SMB-Freigabe** auswählen, um die entsprechenden Berechtigungen zu erhalten.

5. Wählen Sie für **Zugriff zuweisen zu** die Option **Azure Active Directory-Benutzer, -Gruppe oder -Dienstprinzipal** aus.

6. Wählen Sie für die Azure Active Directory-Zielidentität einen Namen oder eine E-Mail-Adresse aus.

7. Wählen Sie **Speichern** aus.

## <a name="get-the-storage-account-access-key"></a>Abrufen des Zugriffsschlüssels für das Speicherkonto

Als Nächstes müssen Sie den Zugriffsschlüssel für Ihr Speicherkonto abrufen.

So rufen Sie den Zugriffsschlüssel für das Speicherkonto ab

1. Wählen Sie im Azure-Portal auf der Randleiste **Speicherkonten** aus.

2. Wählen Sie in der Liste der Speicherkonten das Konto aus, für das Sie Azure AD DS aktiviert und die benutzerdefinierten Rollen in den obigen Schritten erstellt haben.

3. Wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus, und kopieren Sie den Schlüssel aus **Schlüssel1**.

4. Wechseln Sie zur Registerkarte **Virtuelle Computer**, und suchen Sie alle VMs, die Teil Ihres Hostpools werden sollen.

5. Wählen Sie unter **Virtuelle Computer (adVM)** den Namen des virtuellen Computers (VM) und danach **Verbinden** aus.

    Dadurch wird eine RDP-Datei heruntergeladen, mit der Sie sich bei der VM mit ihren eigenen Anmeldeinformationen anmelden können.

    > [!div class="mx-imgBorder"]
    > ![Ein Screenshot der Registerkarte „RDP“ im Fenster „Verbindung mit virtuellem Computer herstellen“.](media/rdp-tab.png)

6. Nachdem Sie sich bei der VM angemeldet haben, führen Sie eine Eingabeaufforderung als Administrator aus.

7. Führen Sie den folgenden Befehl aus:

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - Ersetzen Sie `<desired-drive-letter>` durch einen Laufwerkbuchstaben Ihrer Wahl (z. B. `y:`).
    - Ersetzen Sie alle Vorkommen von `<storage-account-name>` durch den Namen des Speicherkontos, das Sie zuvor angegeben haben.
    - Ersetzen Sie `<share-name>` durch den Namen der Freigabe, die Sie zuvor erstellt haben.
    - Ersetzen Sie `<storage-account-key>` durch den Speicherkontoschlüssel aus Azure.

    Beispiel:

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile)
     ```

8. Führen Sie die folgenden Befehle aus, damit Ihre Windows Virtual Desktop-Benutzer ihre eigenen Profilcontainer erstellen können, während sie den Zugriff auf ihren Profilcontainer durch andere Benutzer blockieren.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - Ersetzen Sie `<mounted-drive-letter>` durch den Buchstaben des Laufwerks, den Sie zum Zuordnen des Laufwerks verwendet haben.
    - Ersetzen Sie `<user-email>` durch den UPN des Benutzers oder der Active Directory-Gruppe, die die Benutzer mit erforderlichem Zugriff auf die Freigabe enthält.

    Beispiel:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>Erstellen eines Profilcontainers

Nun, da Ihre Profile bereit sind, erstellen wir einen FSLogix-Profilcontainer.

So konfigurieren Sie einen FSLogix-Profilcontainer

1. Melden Sie sich bei der Sitzungshost-VM an, die Sie am Anfang dieses Artikels konfiguriert haben. Anschließend können Sie den [FSLogix-Agent herunterladen und installieren](/fslogix/install-ht/).

2. Entzippen Sie die FSLogix-Agent-Datei, die Sie heruntergeladen haben, wechseln Sie zu **x64** > **Releases**, und öffnen Sie dann **FSLogixAppsSetup.exe**.

3. Aktivieren Sie im gestarteten Installationsprogramm **Ich stimme den Lizenzbedingungen zu**. Geben Sie ggf. einen neuen Schlüssel an.

4. Wählen Sie **Installieren** aus.

5. Öffnen Sie **Laufwerk C**, und wechseln Sie dann zu **Programmdateien** > **FSLogix** > **Apps**, um sicherzustellen, dass der FSLogix-Agent ordnungsgemäß installiert wurde.

     >[!NOTE]
     > Wenn sich mehrere VMs im Hostpool befinden, müssen Sie die Schritte 1 bis 5 für jede VM wiederholen.

6. Führen Sie den **Registrierungs-Editor** (RegEdit) als Administrator aus.

7. Navigieren Sie zu **Computer** > **HKEY_LOCAL_MACHINE** > **Software** > **FSLogix**. Klicken Sie mit der rechten Maustaste auf **FSLogix**. Wählen Sie **Neu** und dann **Schlüssel** aus.

8. Erstellen Sie einen neuen Schlüssel mit dem Namen **Profiles**.

9.  Klicken Sie mit der rechten Maustaste auf **Profiles**. Wählen Sie **Neu** und dann **DWORD-Wert (32-Bit)** aus. Nennen Sie den Wert **Aktiviert**, und legen Sie den Wert für **Daten** auf **1** fest.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Schlüssels „Profiles“. Die REG_DWORD-Datei ist hervorgehoben und ihr Wert „Daten“ auf 1 festgelegt.](media/dword-value.png)

10. Klicken Sie mit der rechten Maustaste auf **Profiles**. Wählen Sie **Neu** und dann **Wert der mehrteiligen Zeichenfolge** aus. Nennen Sie den Wert **VHDLocations**, und legen Sie den URI für die Azure Files-Freigabe `\\fsprofile.file.core.windows.net\share` als Wert für „Daten“ fest.

    > [!div class="mx-imgBorder"]
    > ![Ein Screenshot des Schlüssels „Profiles“ mit der Datei „VHDLocations“. Ihr Wert „Daten“ enthält den URI für die Azure Files-Freigabe.](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>Zuweisen von Benutzern zu einem Sitzungshost

Nun müssen Sie Ihrem Sitzungshost Benutzer zuweisen.

So weisen Sie Benutzer zu

1. Führen Sie Windows PowerShell als Administrator und dann das folgende Cmdlet aus, um sich mit PowerShell bei Windows Virtual Desktop anzumelden:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   Geben Sie bei Aufforderung die Anmeldeinformationen des Benutzers ein, dem die Rolle TenantCreator bzw. Besitzer oder Mitwirkender für RDS im Windows Virtual Desktop-Mandanten gewährt wurde.

2. Führen Sie die folgenden Cmdlets aus, um einen Benutzer einer Remotedesktopgruppe zuzuweisen:

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    Stellen Sie wie bei den vorherigen Cmdlets sicher, dass Sie `<your-wvd-tenant>`, `<wvd-pool>` und `<user-principal>` durch die entsprechenden Werte ersetzen.

    Beispiel:

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>Sicherstellen, dass Ihr Profil funktioniert

Jetzt müssen Sie nur noch sicherstellen, dass das von Ihnen erstellte Profil vorhanden ist und wie gewünscht funktioniert.

So überprüfen Sie Ihr Profil

1. Öffnen Sie einen Browser, und wechseln Sie zum [Windows Virtual Desktop-Webclient](https://rdweb.wvd.microsoft.com/arm/webclient).

2. Melden Sie sich mit dem Benutzerkonto an, das der Remotedesktopgruppe zugewiesen ist.

3. Nachdem die Benutzersitzung eingerichtet wurde, öffnen Sie das Azure-Portal, und melden Sie sich mit einem Administratorkonto an.

4. Wählen Sie auf der Randleiste **Speicherkonten** aus.

5. Wählen Sie das Speicherkonto aus, das Sie als Dateifreigabe für Ihren Sitzungshostpool konfiguriert und mit Azure AD DS aktiviert haben.

6. Wählen Sie das Symbol **Dateien** aus, und erweitern Sie dann die Freigabe.

    Wenn alles ordnungsgemäß eingerichtet ist, sollten Sie ein **Verzeichnis** mit einem Namen sehen, der wie folgt formatiert ist: `<user SID>-<username>`.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nach alternativen Möglichkeiten zum Erstellen von FSLogix-Profilcontainern suchen, lesen Sie die folgenden Artikel:

- [Erstellen eines Profilcontainers für einen Hostpool unter Verwendung einer Dateifreigabe](create-host-pools-user-profile.md)
- [Erstellen eines FSLogix-Profilcontainers für einen Hostpool mit Azure NetApp Files](create-fslogix-profile-container.md)

Ausführlichere Informationen zu Konzepten in Bezug auf FSlogix-Container für Azure Files finden Sie unter [FSLogix-Profilcontainer und Azure Files](fslogix-containers-azure-files.md).
