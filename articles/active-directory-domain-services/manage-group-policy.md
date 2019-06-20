---
title: 'Azure Active Directory Domain Services: Verwalten von Gruppenrichtlinien für eine verwaltete Domäne | Microsoft-Dokumentation'
description: Verwalten von Gruppenrichtlinien in durch Azure Active Directory Domain Services verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: db5fd06bc4d9a923279095ab187d867a6624480a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245291"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Verwalten von Gruppenrichtlinien in einer durch Azure Active Directory Domain Services verwalteten Domäne
Azure Active Directory Domain Services umfassen integrierte Gruppenrichtlinienobjekte (Group Policy Objects, GPOs) für die Container „AADDC Users“ und „AADDC Computers“. Sie können diese integrierten GPOs anpassen, um eine Gruppenrichtlinie in der verwalteten Domäne zu konfigurieren. Darüber hinaus können Mitglieder der Administratorengruppe für Azure AD-Domänencontroller benutzerdefinierte Organisationseinheiten (OEs) in der verwalteten Domäne erstellen. Sie können auch benutzerdefinierte GPOs erstellen und mit diesen benutzerdefinierten OEs verknüpfen. Benutzern, die zur Administratorengruppe für Azure AD-Domänencontroller gehören, werden Administratorrechte für Gruppenrichtlinien in der verwalteten Domäne gewährt.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Einen **in die Domäne eingebundenen virtuellen Computer** , über den Sie die mit den Azure AD-Domänendiensten verwaltete Domäne verwalten. Wenn Sie nicht über einen virtuellen Computer dieser Art verfügen, führen Sie alle Schritte wie in Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md) beschriebenen aus.
5. Sie benötigen die Anmeldeinformationen eines **Benutzerkontos, das in Ihrem Verzeichnis der Administratorengruppe für Azure AD-Domänencontroller angehört**, um Gruppenrichtlinien für die verwaltete Domäne verwalten zu können.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>Aufgabe 1: Bereitstellen eines in die Domäne eingebundenen virtuellen Computers zur Remoteverwaltung von Gruppenrichtlinien für die verwaltete Domäne
Durch die Azure AD Domain Services verwaltete Domänen können mithilfe bekannter Active Directory-Verwaltungstools verwaltet werden, beispielsweise über das Active Directory-Verwaltungscenter (Active Directory Administrative Center, ADAC) oder AD PowerShell. Auf die gleiche Weise können Gruppenrichtlinien für die verwaltete Domäne remote mithilfe der Tools für die Verwaltung von Gruppenrichtlinien verwaltet werden.

Administratoren in Ihrem Azure AD-Verzeichnis verfügen über keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Mitglieder der Administratorengruppe für Azure AD-Domänencontroller können Gruppenrichtlinien für verwaltete Domänen remote verwalten. Sie können Gruppenrichtlinientools auf einem Windows Server-/Clientcomputer verwenden, der in die verwaltete Domäne eingebunden ist. Gruppenrichtlinientools können im Rahmen des optionalen Features für die Gruppenrichtlinienverwaltung auf Windows Server- und Windows-Clientcomputern installiert werden, die in die verwaltete Domäne eingebunden sind.

Die erste Aufgabe besteht darin, einen virtuellen Windows Server-Computer einzurichten, der der verwalteten Domäne beigetreten ist. Anweisungen hierzu finden Sie im Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>Aufgabe 2: Installieren von Gruppenrichtlinientools auf dem virtuellen Computer
Führen Sie die folgenden Schritte aus, um die Tools für die Gruppenrichtlinienverwaltung auf dem virtuellen Computer zu installieren, der in die Domäne eingebunden ist.

1. Navigieren Sie zum Azure-Portal. Klicken Sie im linken Bereich auf **Alle Ressourcen**. Klicken Sie auf den virtuellen Computer, den Sie in Aufgabe 1 erstellt haben.
2. Klicken Sie auf der Registerkarte „Übersicht“ auf die Schaltfläche **Verbinden**. Eine Remotedesktopprotokoll-Datei (RDP-Datei) wird erstellt und heruntergeladen.

    ![Verbindung mit virtuellem Windows-Computer herstellen](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Klicken Sie in der angezeigten Aufforderung auf **Verbinden**. Verwenden Sie bei der Anmeldeaufforderung die Anmeldeinformationen eines Benutzers, der zur Administratorengruppe für Azure AD-Domänencontroller gehört. In unserem Fall verwenden wir „bob@domainservicespreview.onmicrosoft.com“. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Klicken Sie auf „Ja“ bzw. „Weiter“, um mit dem Herstellen der Verbindung fortzufahren.
4. Öffnen Sie auf dem Startbildschirm den **Server-Manager**. Klicken Sie im mittleren Bereich des Server-Manager-Fensters auf **Rollen und Features hinzufügen** .

    ![Server-Manager auf dem virtuellen Computer starten](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Klicken Sie auf der Seite **Vorbereitung** des **Assistenten zum Hinzufügen von Rollen und Funktionen** auf **Weiter**.

    ![Seite „Vorbereitung“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Behalten Sie auf der Seite **Installationstyp** die Aktivierung der Option **Rollenbasierte oder featurebasierte Installation** bei, und klicken Sie auf **Weiter**.

    ![Seite „Installationstyp“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Wählen Sie auf der Seite **Serverauswahl** den aktuellen virtuellen Computer im Serverpool aus, und klicken Sie auf **Weiter**.

    ![Seite „Serverauswahl“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Klicken Sie auf der Seite **Serverrollen** auf **Weiter**. Überspringen Sie diese Seite, da in diesem Beispiel keine Rollen auf dem Server installiert werden.
9. Wählen Sie auf der Seite **Features** das Feature **Gruppenrichtlinienverwaltung** aus.

    ![Seite „Features“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Klicken Sie auf der Seite **Bestätigung** auf **Installieren**, um die Gruppenrichtlinienverwaltung auf dem virtuellen Computer zu installieren. Wenn die Installation des Features erfolgreich abgeschlossen wurde, klicken Sie auf **Schließen**, um den Assistenten zum **Hinzufügen von Rollen und Funktionen** zu beenden.

    ![Bestätigungsseite](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>Aufgabe 3: Starten der Gruppenrichtlinien-Verwaltungskonsole zum Verwalten von Gruppenrichtlinien
Sie können die Gruppenrichtlinien-Verwaltungskonsole auf dem in die Domäne eingebundenen virtuellen Computer verwenden, um Gruppenrichtlinien in der verwalteten Domäne zu verwalten.

> [!NOTE]
> Sie müssen der Administratorengruppe für Azure AD-Domänencontroller angehören, um Gruppenrichtlinien in der verwalteten Domäne verwalten zu können.
>
>

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Daraufhin sollte die auf dem virtuellen Computer installierte Konsole für die **Gruppenrichtlinienverwaltung** angezeigt werden.

    ![Starten der Gruppenrichtlinienverwaltung](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Klicken Sie auf **Gruppenrichtlinienverwaltung**, um die Gruppenrichtlinien-Verwaltungskonsole zu starten.

    ![Gruppenrichtlinien-Verwaltungskonsole](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>Aufgabe 4: Anpassen integrierter Gruppenrichtlinienobjekte
Es gibt zwei integrierte Gruppenrichtlinienobjekte: je eins für die Container „AADDC Computers“ und „AADDC Users“ in Ihrer Domäne. Sie können diese integrierten Gruppenrichtlinienobjekte (Group Policy Objects, GPOs) anpassen, um eine Gruppenrichtlinie in der verwalteten Domäne zu konfigurieren.

1. Klicken Sie in der Konsole für die **Gruppenrichtlinienverwaltung** , um die Knoten **Gesamtstruktur: contoso100.com** und **Domänen** zu erweitern und die Gruppenrichtlinien für Ihre verwaltete Domäne anzuzeigen.

    ![Integrierte Gruppenrichtlinienobjekte](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Sie können diese integrierten GPOs anpassen, um Gruppenrichtlinien in Ihrer verwalteten Domäne zu konfigurieren. Klicken Sie mit der rechten Maustaste auf das Gruppenrichtlinienobjekt, und klicken Sie auf **Bearbeiten...** , um das integrierte Gruppenrichtlinienobjekt anzupassen. Der Editor für die Gruppenrichtlinienkonfiguration ermöglicht das Anpassen des Gruppenrichtlinienobjekts.

    ![Bearbeiten des integrierten Gruppenrichtlinienobjekts](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Sie können jetzt den **Gruppenrichtlinienverwaltungs-Editor** verwenden, um das integrierte Gruppenrichtlinienobjekt zu bearbeiten. Der folgende Screenshot zeigt die Anpassung des integrierten Gruppenrichtlinienobjekts „AADDC Computers“.

    ![Anpassen von Gruppenrichtlinienobjekten](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>Aufgabe 5: Erstellen eines benutzerdefinierten Gruppenrichtlinienobjekts
Sie können eigene benutzerdefinierte Gruppenrichtlinienobjekte erstellen oder importieren. Darüber hinaus können Sie benutzerdefinierte GPOs mit einer in der verwalteten Domäne erstellten Organisationseinheit verknüpfen. Weitere Informationen zum Erstellen von benutzerdefinierten Organisationseinheiten finden Sie unter [Erstellen einer Organisationseinheit in einer verwalteten Domäne](create-ou.md).

> [!NOTE]
> Sie müssen der Administratorengruppe für Azure AD-Domänencontroller angehören, um Gruppenrichtlinien in der verwalteten Domäne verwalten zu können.
>
>

1. Klicken Sie in der Konsole für die **Gruppenrichtlinienverwaltung**, um die benutzerdefinierte Organisationseinheit auszuwählen. Klicken Sie mit der rechten Maustaste auf die Organisationseinheit, und klicken Sie dann auf **Gruppenrichtlinienobjekt hier erstellen und verknüpfen...** .

    ![Erstellen eines benutzerdefinierten GPOs](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Geben Sie einen Namen für das neue Gruppenrichtlinienobjekt an, und klicken Sie auf **OK**.

    ![Angeben eines Namens für das GPO](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Ein neues GPO wird erstellt und mit Ihrer benutzerdefinierten Organisationseinheit verknüpft. Klicken Sie mit der rechten Maustaste auf das GPO, und klicken Sie anschließend im Menü auf **Bearbeiten...** .

    ![Neu erstelltes GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Sie können das neu erstellte GPO mit dem **Gruppenrichtlinienverwaltungs-Editor** anpassen.

    ![Anpassen des neuen Gruppenrichtlinienobjekts](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


Weitere Informationen zur Verwendung der [Gruppenrichtlinien-Verwaltungskonsole](https://technet.microsoft.com/library/cc753298.aspx) finden Sie in TechNet.

## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne der Azure AD-Domänendienste](active-directory-ds-admin-guide-join-windows-vm.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [Gruppenrichtlinien-Verwaltungskonsole.](https://technet.microsoft.com/library/cc753298.aspx)
