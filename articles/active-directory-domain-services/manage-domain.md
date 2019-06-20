---
title: 'Azure Active Directory Domain Services: Verwalten einer verwalteten Domäne | Microsoft-Dokumentation'
description: Verwalten von durch die Azure Active Directory-Domänendienste verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 31221b17059bb7754e24ce1139e8cb2ec22cf7a7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245293"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Verwalten einer durch die Azure Active Directory-Domänendienste verwalteten Domäne
Dieser Artikel erläutert die Verwaltung einer durch die Azure Active Directory-Domänendienste verwalteten Domäne.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Zum Ausführen der in diesem Artikel aufgeführten Anweisungen ist Folgendes erforderlich:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Einen **in die Domäne eingebundenen virtuellen Computer** , über den Sie die mit den Azure AD-Domänendiensten verwaltete Domäne verwalten. Wenn Sie nicht über einen virtuellen Computer dieser Art verfügen, führen Sie alle im Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md) beschriebenen Aufgaben aus.
5. Sie benötigen die Anmeldeinformationen eines **Benutzerkontos, das in Ihrem Verzeichnis der Administratorengruppe für AAD-Domänencontroller angehört** , um die verwaltete Domäne verwalten zu können.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Verwaltungsaufgaben, die Sie in einer verwalteten Domäne durchführen können
Mitgliedern der Administratorengruppe für Azure AD-Domänencontroller werden Berechtigungen für die verwaltete Domäne gewährt, mit denen sie Aufgaben wie die folgenden ausführen können:

* Hinzufügen von Computern zur verwalteten Domäne
* Konfigurieren des integrierten Gruppenrichtlinienobjekts für Azure AD-DC-Computer und Azure AD-DC-Benutzer in der verwalteten Domäne
* Verwalten von DNS in der verwalteten Domäne
* Erstellen und Verwalten benutzerdefinierter Organisationseinheiten (OEs) in der verwalteten Domäne
* Administrativer Zugriff auf Computer, die der verwalteten Domäne beigetreten sind

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>In einer verwalteten Domäne nicht vorhandene Administratorberechtigungen
Die Domäne wird von Microsoft verwaltet – einschließlich Aktivitäten wie Patching, Überwachung und Durchführung von Sicherungen. Die Domäne ist gesperrt, und Sie besitzen keine Berechtigungen für bestimmte administrative Aufgaben in der Domäne. Im Folgenden sind einige Beispiele für Aufgaben aufgeführt, die Sie nicht ausführen können.

* Sie besitzen keine Domänenadministrator- oder Unternehmensadministratorberechtigungen für die verwaltete Domäne.
* Sie können das Schema der verwalteten Domäne nicht erweitern.
* Sie können über Remotedesktop keine Verbindung mit Domänencontrollern für die verwaltete Domäne herstellen.
* Sie können der verwalteten Domäne keine Domänencontroller hinzufügen.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Aufgabe 1: Erstellen eines in die Domäne eingebundenen virtuellen Windows Server-Computers zur Remoteverwaltung der verwalteten Domäne
Durch die Azure AD-Domänendienste verwaltete Domänen können mithilfe bekannter Active Directory-Verwaltungstools verwaltet werden, beispielsweise über das Active Directory-Verwaltungscenter (Active Directory Administrative Center, ADAC) oder AD PowerShell. Mandantenadministratoren besitzen keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Mitglieder der Administratorengruppe für Azure AD-Domänencontroller können verwaltete Domänen remote über die AD-Verwaltungstools eines Windows Server- oder Windows-Clientcomputers verwalten, der der verwalteten Domäne beigetreten ist. AD-Verwaltungstools können im Rahmen der optionalen Remoteserver-Verwaltungstools (Remote Server Administration Tools, RSAT) auf Windows Server- und Windows-Clientcomputern installiert werden, die der verwalteten Domäne beigetreten sind.

Der erste Schritt besteht darin, einen virtuellen Windows Server-Computer einzurichten, der der verwalteten Domäne beigetreten ist. Anweisungen hierzu finden Sie im Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Remoteverwaltung der verwalteten Domäne über einen Clientcomputer (beispielsweise unter Windows 10)
In diesem Artikel wird ein virtueller Windows Server-Computer verwendet, um die mit den AAD-Domänendiensten verwaltete Domäne zu verwalten. Sie können hierzu aber auch einen virtuellen Windows-Clientcomputer (beispielsweise unter Windows 10) verwenden.

Sie können die [Remote Server Administration Tools (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) (Remoteserver-Verwaltungstools) mithilfe der folgenden Anweisungen in TechNet auf einem virtuellen Windows-Clientcomputer installieren.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Aufgabe 2: Installieren der Active Directory-Verwaltungstools auf dem virtuellen Computer
Führen Sie die folgenden Schritte aus, um die Active Directory-Verwaltungstools auf dem virtuellen Computer zu installieren, der der Domäne beigetreten ist. Weitere Informationen zum Installieren und Verwenden der Remoteserver-Verwaltungstools finden Sie in TechNet im Artikel [Bereitstellen der Remoteserver-Verwaltungstools](https://technet.microsoft.com/library/hh831501.aspx).

1. Navigieren Sie zum Azure-Portal. Klicken Sie im linken Bereich auf **Alle Ressourcen**. Klicken Sie auf den virtuellen Computer, den Sie in Aufgabe 1 erstellt haben.
2. Klicken Sie auf der Registerkarte „Übersicht“ auf die Schaltfläche **Verbinden**. Eine Remotedesktopprotokoll-Datei (RDP-Datei) wird erstellt und heruntergeladen.

    ![Verbindung mit virtuellem Windows-Computer herstellen](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Klicken Sie in der angezeigten Aufforderung auf **Verbinden**. Verwenden Sie die Anmeldeinformationen eines Benutzers, der zur Administratorengruppe für Azure AD-Domänencontroller gehört. Beispiel: „bob@domainservicespreview.onmicrosoft.com“. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Klicken Sie auf „Ja“ bzw. „Weiter“, um mit dem Herstellen der Verbindung fortzufahren.
4. Öffnen Sie auf dem Startbildschirm den **Server-Manager**. Klicken Sie im mittleren Bereich des Server-Manager-Fensters auf **Rollen und Features hinzufügen** .

    ![Server-Manager auf dem virtuellen Computer starten](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Klicken Sie auf der Seite **Vorbereitung** des **Assistenten zum Hinzufügen von Rollen und Funktionen** auf **Weiter**.

    ![Seite „Vorbereitung“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Behalten Sie auf der Seite **Installationstyp** die Aktivierung der Option **Rollenbasierte oder featurebasierte Installation** bei, und klicken Sie auf **Weiter**.

    ![Seite „Installationstyp“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Wählen Sie auf der Seite **Serverauswahl** den aktuellen virtuellen Computer im Serverpool aus, und klicken Sie auf **Weiter**.

    ![Seite „Serverauswahl“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Klicken Sie auf der Seite **Serverrollen** auf **Weiter**.
9. Erweitern Sie auf der Seite **Features** durch Klicken den Knoten **Remote Server-Verwaltungstools**, und erweitern Sie dann durch erneutes Klicken den Knoten **Rollenverwaltungstools**. Wählen Sie in der Liste mit den Rollenverwaltungstools das Feature **AD DS- und AD LDS-Tools** aus.

    ![Seite „Features“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Klicken Sie auf der Seite **Bestätigung** auf **Installieren**, um die AD- und AD LDS-Tools auf dem virtuellen Computer zu installieren. Wenn die Installation des Features erfolgreich abgeschlossen wurde, klicken Sie auf **Schließen**, um den Assistenten zum **Hinzufügen von Rollen und Funktionen** zu beenden.

    ![Bestätigungsseite](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Aufgabe 3: Verbindungsherstellung und Erkunden der verwalteten Domäne
Jetzt können Sie die verwaltete Domäne mit Windows Server AD-Verwaltungstools untersuchen und verwalten.

> [!NOTE]
> Sie müssen der Administratorengruppe für AAD-Domänencontroller angehören, um die verwaltete Domäne verwalten zu können.
>
>

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Daraufhin werden die auf dem virtuellen Computer installierten AD-Verwaltungstools angezeigt.

    ![Auf dem Server installierte Verwaltungstools](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Klicken Sie auf **Active Directory-Verwaltungscenter**.

    ![Active Directory-Verwaltungscenter](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Klicken Sie im linken Bereich auf den Domänennamen (beispielsweise „contoso100.com“), um die Domäne zu erkunden. Beachten Sie die beiden Container „AADDC Computers“ und „AADDC Users“.

    ![ADAC – Domäne anzeigen](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Klicken Sie auf den Container **AADDC Users** , um alle Benutzer und Gruppen anzuzeigen, die zur verwalteten Domäne gehören. In diesem Container sollten Benutzerkonten und Gruppen Ihres Azure AD-Mandanten angezeigt werden. Beachten Sie, dass der Container in diesem Beispiel ein Benutzerkonto für den Benutzer „Bob“ und eine Gruppe namens „AAD DC Administrators“ enthält.

    ![ADAC – Domänenbenutzer](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Klicken Sie auf den Container **AADDC Computers** , um die Computer anzuzeigen, die dieser verwalteten Domäne beigetreten sind. Es sollte ein Eintrag für den aktuellen virtuellen Computer angezeigt werden, der der Domäne beigetreten ist. Computerkonten für alle Computer, die der von den Azure AD-Domänendiensten verwalteten Domäne beigetreten sind, werden im Container „AADDC Computers“ gespeichert.

    ![ADAC – der Domäne beigetretene Computer](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne der Azure AD-Domänendienste](active-directory-ds-admin-guide-join-windows-vm.md)
* [Bereitstellen der Remoteserver-Verwaltungstools](https://technet.microsoft.com/library/hh831501.aspx)
