---
title: Erstellen eines Hostpools für die Vorschauversion von Windows Virtual Desktop mit Azure Marketplace – Azure
description: Hier erfahren Sie, wie Sie mit Azure Marketplace einen Hostpool für die Vorschauversion von Windows Virtual Desktop erstellen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: e19523834c0ddb517fa9d15853411c1b58024b43
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834006"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>Tutorial: Erstellen eines Hostpools mit Azure Marketplace

Hostpools sind eine Sammlung identischer virtueller Computer innerhalb von Mandantenumgebungen mit Windows Virtual Desktop (Vorschauversion). Jeder Hostpool kann eine App-Gruppe enthalten, mit der Benutzer genau wie auf einem physischen Desktop interagieren können.

In diesem Artikel erfahren Sie, wie Sie innerhalb eines Windows Virtual Desktop-Mandanten einen Hostpool unter Verwendung eines Microsoft Azure Marketplace-Angebots erstellen. Dazu gehört das Erstellen eines Hostpools in Windows Virtual Desktop, das Erstellen einer Ressourcengruppe mit virtuellen Computern in einem Azure-Abonnement, das Verknüpfen der virtuellen Computer mit der Active Directory-Domäne sowie das Registrieren der virtuellen Computer bei Windows Virtual Desktop.

Zur Vorbereitung müssen Sie ggf. zunächst das [Windows Virtual Desktop-PowerShell-Modul herunterladen und importieren](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview), um es in Ihrer PowerShell-Sitzung verwenden zu können.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter <https://portal.azure.com> beim Azure-Portal an.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Ausführen des Azure Marketplace-Angebots zum Bereitstellen eines neuen Hostpools

So führen Sie das Azure Marketplace-Angebot aus, um einen neuen Hostpool bereitzustellen:

1. Wählen Sie **+** oder **+ Ressource erstellen** aus.
2. Geben Sie **Windows Virtual Desktop** in das Marketplace-Suchfenster ein.
3. Wählen Sie **Windows Virtual Desktop - Provision a host pool** (Windows Virtual Desktop – Hostpool bereitstellen) und anschließend **Erstellen** aus.

Geben Sie die Informationen für die entsprechenden Blätter gemäß dem Leitfaden ein.

### <a name="basics"></a>Grundlagen

Gehen Sie auf dem Blatt „Grundlagen“ wie folgt vor:

1. Geben Sie für den Hostpool einen Namen ein, der innerhalb des Windows Virtual Desktop-Mandanten eindeutig ist.
2. Wählen Sie die passende Option für einen persönlichen Desktop aus. Wenn Sie **Ja** auswählen, wird jeder Benutzer, der eine Verbindung mit diesem Hostpool herstellt, dauerhaft einem virtuellen Computer zugewiesen.
3. Geben Sie eine kommagetrennte Liste mit Benutzern ein, die sich nach Abschluss des Azure Marketplace-Angebots bei Windows Virtual Desktop-Clients anmelden und auf einen Desktop zugreifen können sollen. Geben Sie also beispielsweise „user1@contoso.com,user2@contoso.com“ ein, um user1@contoso.com und user2@contoso.com Zugriff zu gewähren.
4. Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die neue Ressourcengruppe an.
5. Wählen Sie unter **Standort** den Standort des virtuellen Netzwerks aus, das mit dem Active Directory-Server verbunden ist.
6. Klicken Sie auf **OK**.

### <a name="configure-virtual-machines"></a>Konfigurieren virtueller Computer

Gehen Sie auf dem Blatt zum Konfigurieren virtueller Computer wie folgt vor:

1. Übernehmen Sie die Standardeinstellungen, oder passen Sie Anzahl und Größe der virtuellen Computer an.
2. Geben Sie ein Namenspräfix für die virtuellen Computer ein. Wenn Sie also beispielsweise „prefix“ eingeben, heißen die virtuellen Computer „prefix-0“, „prefix-1“ usw.
3. Klicken Sie auf **OK**.

### <a name="virtual-machine-settings"></a>Einstellungen des virtuellen Computers

Gehen Sie auf dem Blatt mit den VM-Einstellungen wie folgt vor:

>[!NOTE]
> Wenn Sie Ihre virtuellen Computer einer Azure AD Domain Services-Umgebung beitreten lassen, stellen Sie sicher, dass Ihre Domänenbeitrittsbenutzer ebenfalls Mitglied der [Gruppe „AAD DC-Administratoren“](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group) ist.

1. Wählen Sie die **Imagequelle** aus, und geben Sie entsprechende Informationen zum Speicherort und zur Art der Speicherung ein. Falls Sie keine verwalteten Datenträger verwenden möchten, wählen Sie das Speicherkonto mit der VHD-Datei aus.
2. Geben Sie den Benutzerprinzipalnamen und das Kennwort für das Domänenkonto ein, über das die virtuellen Computer mit der Active Directory-Domäne verknüpft werden. Auf den virtuellen Computern wird ein lokales Konto mit dem gleichen Benutzernamen und Kennwort erstellt. Diese lokalen Konten können später zurückgesetzt werden.
3. Wählen Sie das virtuelle Netzwerk aus, das mit dem Active Directory-Server verbunden ist, und wählen Sie anschließend ein Subnetz als Host für die virtuellen Computer aus.
4. Klicken Sie auf **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informationen zum Mandanten mit Windows Virtual Desktop (Vorschauversion)

Gehen Sie auf dem Blatt mit den Informationen zum Windows Virtual Desktop-Mandanten wie folgt vor:

1. Geben Sie den**Namen der Windows Virtual Desktop-Mandantengruppe** für die Mandantengruppe ein, die Ihren Mandanten enthält. Lassen Sie den Standardnamen, es sei denn, Ihnen wurde ein spezieller Mandantengruppenname bereitgestellt.
2. Geben Sie den**Namen des Windows Virtual Desktop-Mandanten** für den Mandanten ein, in dem Sie diesen Hostpool erstellen möchten.
3. Geben Sie die Art der Anmeldeinformationen an, die Sie für die Authentifizierung als RDS-Besitzer des Windows Virtual Desktop-Mandanten verwenden möchten. Wenn Sie das Tutorial [Erstellen von Dienstprinzipalen und Rollenzuweisungen mit PowerShell](./create-service-principal-role-powershell.md) abgeschlossen haben, wählen Sie **Dienstprinzipal** aus. Sie müssen nun die **Azure AD-Mandanten-ID** der Azure Active Directory-Instanz eingeben, die den Dienstprinzipal enthält.
4. Geben Sie die Anmeldeinformationen für das Administratorkonto des Mandanten ein. Es werden nur Dienstprinzipale mit Kennwort unterstützt.
5. Klicken Sie auf **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Abschließen der Einrichtung und Erstellen des virtuellen Computers

Gehen Sie auf den letzten beiden Blättern wie folgt vor:

1. Überprüfen Sie auf dem Blatt **Zusammenfassung** die Setupinformationen. Sollten Änderungen erforderlich sein, kehren Sie zum entsprechenden Blatt zurück, und nehmen Sie die gewünschten Änderungen vor, bevor Sie den Vorgang fortsetzen. Sind die Angaben korrekt, wählen Sie **OK** aus.
2. Überprüfen Sie auf dem Blatt **Kaufen** die zusätzlichen Informationen zu Ihrem Azure Marketplace-Kauf.
3. Wählen Sie **Erstellen** aus, um Ihren Hostpool bereitzustellen.

Dieser Vorgang kann abhängig von der zu erstellenden VM-Anzahl 30 Minuten oder länger dauern.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Optional: Zuweisen zusätzlicher Benutzer zur Desktopanwendungsgruppe

Nach Abschluss des Azure Marketplace-Angebots können Sie der Desktopanwendungsgruppe weitere Benutzer zuweisen, bevor Sie damit beginnen, die vollständigen Sitzungsdesktops auf Ihren virtuellen Computern zu testen. Falls Sie im Rahmen des Azure Marketplace-Angebots bereits Standardbenutzer hinzugefügt haben und keine weiteren Benutzer hinzufügen möchten, können Sie diesen Abschnitt überspringen.

Wenn Sie einer Desktopanwendungsgruppe Benutzer zuweisen möchten, müssen Sie zunächst ein PowerShell-Fenster öffnen. Danach müssen die beiden folgenden Cmdlets ausgeführt werden.

Führen Sie das folgende Cmdlet aus, um sich bei der Windows Virtual Desktop-Umgebung anzumelden:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Nun können Sie der Desktopanwendungsgruppe mithilfe des folgenden Cmdlets Benutzer hinzufügen:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Der UPN des Benutzers muss der Identität des Benutzers in Azure Active Directory entsprechen (Beispiel: user1@contoso.com). Wenn Sie mehrere Benutzer hinzufügen möchten, müssen Sie dieses Cmdlet für jeden Benutzer separat ausführen.

Nach Abschluss dieser Schritte können sich Benutzer, die der Desktopanwendungsgruppe hinzugefügt wurden, mit unterstützten Remotedesktopclients bei Windows Virtual Desktop anmelden und eine Ressource für einen Sitzungsdesktop anzeigen.

Derzeit werden folgende Clients unterstützt:

- [Remotedesktopclient für Windows 7 und Windows 10](connect-windows-7-and-10.md)
- [Windows Virtual Desktop-Webclient](connect-web.md)

>[!IMPORTANT]
>Zum Schutz Ihrer Windows Virtual Desktop-Umgebung in Azure empfiehlt es sich, den eingehenden Port 3389 auf Ihren virtuellen Computern nicht zu öffnen. Für Windows Virtual Desktop muss der eingehende Port 3389 nicht geöffnet sein, damit Benutzer auf die virtuellen Computer des Hostpools zugreifen können. Wenn Sie den Port 3389 zur Problembehandlung öffnen müssen, verwenden Sie am besten den [Just-In-Time-Zugriff auf virtuelle Computer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Hostpool erstellt und Benutzer für den Zugriff auf dessen Desktop zugewiesen haben, können Sie den Hostpool als Nächstes mit RemoteApps füllen. Weitere Informationen zum Verwalten von Apps in Windows Virtual Desktop finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Manage app groups for Windows Virtual Desktop Preview](./manage-app-groups.md) (Verwalten von App-Gruppen für Windows Virtual Desktop (Vorschauversion))
