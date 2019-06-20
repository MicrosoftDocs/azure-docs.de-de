---
title: 'Azure Active Directory Domain Services: Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne | Microsoft-Dokumentation'
description: Einbinden eines virtuellen Windows Server-Computers in Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 1564fff8a749cc2d4db9e8e820981ba5dc1da570
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245307"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne
In diesem Artikel wird veranschaulicht, wie Sie mit dem Azure-Portal einen neuen virtuellen Windows Server-Computer bereitstellen. Anschließend erfahren Sie, wie Sie den virtuellen Computer in eine verwaltete Domäne von Azure Active Directory Domain Services (Azure AD DS) einbinden.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Schritt 1: Erstellen einer Windows-VM
Führen Sie folgende Schritte durch, um einen virtuellen Windows-Computer zu erstellen, der in das virtuelle Netzwerk eingebunden wird, in dem Sie Azure AD DS aktiviert haben:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie oben im linken Bereich die Option **Neu**.
3. Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter-Link](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Konfigurieren Sie im Bereich **Grundlagen** des Assistenten die grundlegenden Einstellungen für den virtuellen Computer.

    ![Bereich „Grundlagen“](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > Der hier eingegebene Benutzername und das Kennwort sind für ein lokales Administratorkonto bestimmt, das zum Anmelden am virtuellen Computer verwendet wird. Wählen Sie ein sicheres Kennwort zum Schutz des virtuellen Computers gegen Brute-Force-Kennwortangriffe. Geben Sie hier nicht die Anmeldeinformationen eines Domänenbenutzerkontos ein.
    >

5. Wählen Sie eine **Größe** für den virtuellen Computer. Wählen Sie die Option **Alle anzeigen**, oder ändern Sie den Filter **Unterstützter Datenträgertyp** entsprechend, um weitere Größen anzuzeigen.

    ![Bereich „Größe auswählen“](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Wählen Sie im Bereich **Einstellungen** das virtuelle Netzwerk aus, in dem Ihre per Azure AD DS verwaltete Domäne bereitgestellt wurde. Wählen Sie ein Subnetz aus, das sich von dem Subnetz unterscheidet, in dem Ihre verwaltete Domäne bereitgestellt wurde. Behalten Sie bei den anderen Einstellungen die Standardwerte bei, und wählen Sie **OK**.

    ![Einstellungen des virtuellen Netzwerks für den virtuellen Computer](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Wählen Sie das richtige virtuelle Netzwerk und Subnetz aus.**
    >
    > Wählen Sie entweder das virtuelle Netzwerk aus, in dem Ihre verwaltete Domäne bereitgestellt wird, oder ein virtuelles Netzwerk, das per VNet-Peering damit verbunden ist. Wenn Sie ein unverbundenes virtuelles Netzwerk auswählen, können Sie die VM nicht mit der verwalteten Domäne verknüpfen.
    >
    > Es wird empfohlen, Ihre verwaltete Domäne in einem dedizierten Subnetz bereitzustellen. Wählen Sie daher nicht das Subnetz aus, in dem Sie die verwaltete Domäne aktiviert haben.

7. Behalten Sie bei den anderen Einstellungen die Standardwerte bei, und wählen Sie **OK**.
8. Überprüfen Sie auf der Seite **Purchase** (Kauf) die Einstellungen, und wählen Sie **OK**, um den virtuellen Computer bereitzustellen.
9. Die Bereitstellung des virtuellen Computers (VM) wird im Dashboard des Azure-Portals angeheftet.

    ![Vorgehensweise](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Nach Abschluss der Bereitstellung können Sie auf der Seite **Übersicht** Informationen zur VM anzeigen.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Schritt 2: Herstellen einer Verbindung mit der Windows-VM über das lokale Administratorkonto
Stellen Sie als Nächstes eine Verbindung mit dem neu erstellten virtuellen Windows Server-Computer her, damit dieser der Domäne beitreten kann. Verwenden Sie die Anmeldeinformationen des lokalen Administrators, die Sie beim Erstellen des virtuellen Computers angegeben haben.

Um eine Verbindung mit dem virtuellen Computer herzustellen, führen Sie die folgenden Schritte durch:

1. Wählen Sie im Bereich **Übersicht** die Option **Verbinden**.  
    Eine Remotedesktopprotokoll-Datei (RDP-Datei) wird erstellt und heruntergeladen.

    ![Verbindung mit virtuellem Windows-Computer herstellen](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus.
3. Geben Sie Ihre **Anmeldeinformationen für den lokalen Administrator** ein, die Sie beim Erstellen des virtuellen Computers angegeben haben (z.B. *localhost\mahesh*).
4. Wenn während des Anmeldevorgangs eine Zertifikatwarnung angezeigt wird, klicken Sie auf **Ja** oder **Weiter**, um eine Verbindung herzustellen.

Sie sollten Sie mit Ihren Anmeldeinformationen für den lokalen Administrator am neu erstellten virtuellen Windows-Computer angemeldet werden. Der nächste Schritt besteht darin, den virtuellen Computer in die Domäne einzubinden.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Schritt 3: Einbinden der Windows-VM in die mit Azure AD DS verwaltete Domäne
Um den virtuellen Windows Server-Computer in die mit Azure AD DS verwalteten Domäne einzubinden, führen Sie folgende Schritte durch:

1. Stellen Sie eine Verbindung mit dem virtuellen Windows Server-Computer her, wie in Schritt 2 gezeigt. Öffnen Sie auf dem **Startbildschirm** den **Server-Manager**.
2. Wählen Sie im linken Bereich des **Server-Manager**-Fensters die Option **Lokaler Server**.

    ![Server-Manager-Fenster auf dem virtuellen Computer](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Wählen Sie unter **Eigenschaften** die Option **Arbeitsgruppe**.
4. Wählen Sie im Fenster **Systemeigenschaften** die Option **Ändern**, um die Domäne einzubinden.

    ![Fenster „Systemeigenschaften“](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Geben Sie im Feld **Domäne** den Namen Ihrer per Azure AD DS verwalteten Domäne an, und wählen Sie anschließend **OK**.

    ![Domäne für den Beitritt angeben](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. Sie werden aufgefordert, Ihre Anmeldeinformationen für den Domänenbeitritt einzugeben. Geben Sie die Anmeldeinformationen eines *Benutzers an, der zur Administratorengruppe für Azure AD-Domänencontroller gehört*. Nur Mitglieder dieser Gruppen verfügen über die Berechtigungen, Computer in die verwaltete Domäne einzubinden.

    ![Fenster von „Windows-Sicherheit“ zum Angeben von Anmeldeinformationen](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Sie können Anmeldeinformationen auf eine der folgenden Arten eingeben:

   * **UPN-Format** (empfohlen): Geben Sie das Benutzerprinzipalnamen-Suffix für das Benutzerkonto an, wie in Azure AD konfiguriert. Das UPN-Suffix des Benutzers *bob* in diesem Beispiel lautet *bob\@domainservicespreview.onmicrosoft.com*.

   * **SAMAccountName-Format**: Sie können den Kontonamen im Format „SAMAccountName“ angeben. Der Benutzer *bob* würde in diesem Beispiel *CONTOSO100\bob* eingeben.

     > [!TIP]
     > **Wir empfehlen die Verwendung des UPN-Formats, um Anmeldeinformationen anzugeben.**
     >
     > Wenn das UPN-Präfix eines Benutzers übermäßig lang ist (z.B. *Janwirklichlangerbenutzername*), wird unter Umständen der Name „SAMAccountName“ automatisch generiert. Falls mehrere Benutzer in Ihrem Azure AD-Mandanten das gleiche UPN-Präfix besitzen (z.B. *bob*), wird ihr SAMAccountName-Format vom Dienst unter Umständen automatisch generiert. In diesen Fällen kann das UPN-Format zur Anmeldung an der Domäne verwendet werden.
     >

8. Nachdem Sie eine Domäne erfolgreich eingebunden haben, werden Sie mit der folgenden Meldung in der Domäne begrüßt:

    ![Willkommen in der Domäne](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Starten Sie den virtuellen Computer neu, um die Einbindung in die Domäne abzuschließen.

## <a name="troubleshoot-joining-a-domain"></a>Problembehandlung für die Einbindung in eine Domäne
### <a name="connectivity-issues"></a>Konnektivitätsprobleme
Wenn der virtuelle Computer die Domäne nicht findet, versuchen Sie es mit den folgenden Schritten zur Problembehandlung:

* Stellen Sie sicher, dass der virtuelle Computer mit dem gleichen virtuellen Netzwerk verbunden ist, in dem Azure AD DS aktiviert ist. Andernfalls kann der virtuelle Computer keine Verbindung mit der Domäne herstellen oder nicht in diese eingebunden werden.

* Überprüfen Sie, ob der virtuelle Computer mit einem virtuellen Netzwerk verbunden ist, das wiederum mit dem virtuellen Netzwerk verbunden ist, in dem Azure AD DS aktiviert ist.

* Pingen Sie die Domäne unter Verwendung des DNS-Domänennamens der verwalteten Domäne (z.B. *ping contoso100.com*). Sollte das nicht möglich sein, pingen Sie die IP-Adressen für die Domäne, die auf der Seite angezeigt werden, auf der Sie Azure AD DS aktiviert haben (Beispiel: *ping 10.0.0.4*). Wenn Sie die IP-Adresse pingen können, aber nicht die Domäne, ist das DNS möglicherweise falsch konfiguriert. Überprüfen Sie, ob die IP-Adressen der Domäne für das virtuelle Netzwerk als DNS-Server konfiguriert sind.

* Leeren Sie den DNS-Resolvercache im virtuellen Computer (*ipconfig /flushdns*).

Wenn ein Fenster angezeigt wird, in dem Anmeldeinformationen für den Domänenbeitritt angefordert werden, bestehen keine Verbindungsprobleme.

### <a name="credentials-related-issues"></a>Probleme mit Anmeldeinformationen
Probieren Sie es mit den folgenden Schritten zur Problembehandlung, wenn Probleme mit Anmeldeinformationen auftreten und ein Domänenbeitritt nicht möglich ist:

* Verwenden Sie das UPN-Format für die Angabe von Anmeldeinformationen. Der „SAMAccountName“ für Ihr Konto wird möglicherweise automatisch generiert, wenn mehrere Benutzer in Ihrem Mandanten das gleiche UPN-Präfix verwenden oder wenn Ihr UPN-Präfix zu lang ist. In diesen Fällen ist das Format „SAMAccountName“ für Ihr Konto möglicherweise anders als Sie erwarten bzw. unterscheidet sich von dem, was Sie in Ihrer lokalen Domäne verwenden.

* Versuchen Sie, die Anmeldeinformationen eines Benutzerkontos zu verwenden, das zur Gruppe *AAD DC-Administratoren* gehört.

* Überprüfen Sie, ob Sie die [Kennwortsynchronisierung für Ihre verwaltete Domäne aktiviert haben](active-directory-ds-getting-started-password-sync.md).

* Vergewissern Sie sich, dass Sie den UPN des Benutzers bei der Anmeldung genau so verwenden, wie er in Azure AD konfiguriert ist (z.B. *bob\@domainservicespreview.onmicrosoft.com*).

* Warten Sie so lange, bis die Kennwortsynchronisierung abgeschlossen ist, wie im Leitfaden für die ersten Schritte angegeben wird.

## <a name="related-content"></a>Verwandte Inhalte
* [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](create-instance.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
