---
title: Erstellen und Verwalten von Gruppenrichtlinien in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die integrierten Gruppenrichtlinienobjekte (GPOs) bearbeiten und Ihre eigenen benutzerdefinierten Richtlinien in einer von Azure Active Directory Domain Services verwalteten Domäne erstellen.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: f1f2499c49c4adf16b632bc75c246a28330ad27b
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619384"
---
# <a name="administer-group-policy-in-an-azure-active-directory-domain-services-managed-domain"></a>Verwalten von Gruppenrichtlinien in einer von Azure Active Directory Domain Services verwalteten Domäne

Einstellungen für Benutzer- und Computerobjekte in Azure Active Directory Domain Services (Azure AD DS) werden häufig über Gruppenrichtlinienobjekte (GPOs) verwaltet. Azure AD DS umfasst integrierte GPOs für die Container *AADDC-Benutzer* und *AADDC-Computer*. Sie können diese integrierten GPOs anpassen, um die Gruppenrichtlinie nach Bedarf für Ihre Umgebung zu konfigurieren. Mitglieder der Gruppe *Azure AD DC-Administratoren* haben Gruppenrichtlinien-Administratorrechte in der Azure AD DS-Domäne und können auch benutzerdefinierte Gruppenrichtlinienobjekte und Organisationseinheiten erstellen. Weitere Informationen zu Gruppenrichtlinien und deren Funktionsweise finden Sie unter [Übersicht über Gruppenrichtlinien][group-policy-overview].

In einer Hybridumgebung werden die in einer lokalen AD DS-Umgebung konfigurierten Gruppenrichtlinien nicht mit Azure AD DS synchronisiert. Um Konfigurationseinstellungen für Benutzer oder Computer in Azure AD DS zu definieren, bearbeiten Sie eines der Standard-Gruppenrichtlinienobjekte, oder erstellen Sie ein benutzerdefiniertes Gruppenrichtlinienobjekt.

In diesem Artikel erfahren Sie, wie Sie die Gruppenrichtlinien-Verwaltungstools installieren, dann die integrierten GPOs bearbeiten und benutzerdefinierte GPOs erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance] aus.
* Eine Windows Server-Verwaltungs-VM, die in die verwaltete Azure AD DS-Domäne eingebunden ist.
    * Führen Sie bei Bedarf die [Schritte im Tutorial zum Erstellen einer Windows Server-VM und Einbinden der VM in eine verwaltete Domäne][create-join-windows-vm] aus.
* Ein Benutzerkonto, das Mitglied der *Administratorengruppe für Azure AD-Domänencontroller* (AAD-DC-Administratoren) in Ihrem Azure AD-Mandanten ist.

> [!NOTE]
> Sie können administrative Vorlagen für Gruppenrichtlinien verwenden, indem Sie die neuen Vorlagen in die Verwaltungsarbeitsstation kopieren. Kopieren Sie die *ADMX*-Dateien in das Verzeichnis `%SYSTEMROOT%\PolicyDefinitions` und die gebietsschemaspezifischen *ADML*-Dateien in das Verzeichnis `%SYSTEMROOT%\PolicyDefinitions\[Language-CountryRegion]`, wobei `Language-CountryRegion` der Sprache und der Region der *ADML*-Dateien entspricht.
>
> Kopieren Sie z. B. die Version „en-US (Englisch, USA)“ der *ADML*-Dateien in den Ordner `\en-us`.
>
> Alternativ können Sie Ihre administrative Vorlage für Gruppenrichtlinien auf den Domänencontrollern, die Teil der verwalteten Domäne sind, zentral speichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten des zentralen Speichers für administrative Vorlagendateien für Gruppenrichtlinien in Windows](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra).

## <a name="install-group-policy-management-tools"></a>Installieren der Gruppenrichtlinien-Verwaltungstools

Um Gruppenrichtlinienobjekte (Group Policy Object, GPOs) zu erstellen und zu konfigurieren, müssen Sie die Gruppenrichtlinien-Verwaltungstools installieren. Diese Tools können als Feature in Windows Server installiert werden. Weitere Informationen zum Installieren der Verwaltungstools auf einem Windows-Client finden Sie unter [Installieren der Remoteserver-Verwaltungstools (RSAT)][install-rsat].

1. Melden Sie sich bei Ihrer Verwaltungs-VM an. Weitere Informationen zu den Schritten zum Herstellen einer Verbindung mithilfe des Azure-Portals finden Sie unter [Herstellen einer Verbindung mit einer Windows Server-VM][connect-windows-server-vm].
1. Der **Server-Manager** sollte standardmäßig geöffnet werden, wenn Sie sich bei der VM anmelden. Wenn dies nicht der Fall ist, wählen Sie im **Startmenü** die Option **Server-Manager** aus.
1. Wählen Sie im Bereich *Dashboard* des Fensters **Server-Manager** die Option **Rollen und Features hinzufügen** aus.
1. Klicken Sie auf der Seite **Vorbereitung** des *Assistenten zum Hinzufügen von Rollen und Features* auf **Weiter**.
1. Lassen Sie für *Installationstyp* die Option **Rollenbasierte oder featurebasierte Installation** aktiviert, und wählen Sie **Weiter** aus.
1. Wählen Sie auf der Seite **Serverauswahl** den aktuellen virtuellen Computer aus dem Serverpool aus (z. B. *myvm.aaddscontoso.com*), und wählen Sie dann **Weiter** aus.
1. Klicken Sie auf der Seite **Serverrollen** auf **Weiter**.
1. Wählen Sie auf der Seite **Features** das Feature **Gruppenrichtlinienverwaltung** aus.

    ![Installieren der „Gruppenrichtlinienverwaltung“ über die Seite „Features“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. Wählen Sie auf der Seite **Bestätigung** die Option **Installieren** aus. Die Installation der Tools für die Gruppenrichtlinienverwaltung kann ein bis zwei Minuten dauern.
1. Wenn die Installation des Features abgeschlossen ist, wählen Sie **Schließen** aus, um den **Assistenten zum Hinzufügen von Rollen und Features** zu beenden.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>Öffnen der Gruppenrichtlinien-Verwaltungskonsole und Bearbeiten eines Objekts

Für Benutzer und Computer in einer verwalteten Domäne sind Standard-Gruppenrichtlinienobjekte (GPOs) vorhanden. Wenn das Feature zur Gruppenrichtlinienverwaltung aus dem vorherigen Abschnitt installiert ist, können wir ein vorhandenes Gruppenrichtlinienobjekt anzeigen und bearbeiten. Im nächsten Abschnitt erstellen Sie ein benutzerdefiniertes Gruppenrichtlinienobjekt.

> [!NOTE]
> Zum Verwalten einer Gruppenrichtlinie in einer verwalteten Domäne müssen Sie bei einem Benutzerkonto angemeldet sein, das Mitglied der Gruppe *AAD DC-Administrators* ist.

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Es wird eine Liste der verfügbaren Verwaltungstools angezeigt, einschließlich der **Gruppenrichtlinienverwaltung**, die im vorherigen Abschnitt installiert wurde.
1. Wählen Sie **Gruppenrichtlinienverwaltung** aus, um die Gruppenrichtlinien-Verwaltungskonsole (GPMC) zu öffnen.

    ![Die Gruppenrichtlinien-Verwaltungskonsole wird geöffnet, um Gruppenrichtlinienobjekte zu bearbeiten.](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Es gibt zwei integrierte Gruppenrichtlinienobjekte (GPOs) in einer verwalteten Domäne: eines für den Container *AADDC-Computer* und eines für den Container *AADDC-Benutzer*. Sie können diese GPOs anpassen, um Gruppenrichtlinien nach Bedarf innerhalb Ihrer verwalteten Domäne zu konfigurieren.

1. Erweitern Sie in der **Gruppenrichtlinien-Verwaltungskonsole** den Knoten **Gesamtstruktur: aaddscontoso.com**. Erweitern Sie anschließend die **Domänen**-Knoten.

    Es gibt zwei integrierte Container für *AADDC-Computer* und *AADDC-Benutzer*. Jedem dieser Container ist ein Standard-GPO zugeordnet.

    ![Auf die standardmäßigen Container „AADDC-Computer“ und „AADDC-Benutzer“ angewandte integrierte GPOs](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. Diese integrierten GPOs können angepasst werden, um bestimmte Gruppenrichtlinien für Ihre verwaltete Domäne zu konfigurieren. Klicken Sie mit der rechten Maustaste auf eines der Gruppenrichtlinienobjekte (beispielsweise *AADDC-Computer-GPO*), und wählen Sie dann **Bearbeiten...** aus.

    ![Wählen Sie die Option zum Bearbeiten eines der integrierten Gruppenrichtlinienobjekte aus.](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. Das Tool „Gruppenrichtlinienverwaltungs-Editor“ wird geöffnet, mit dem Sie das Gruppenrichtlinienobjekt anpassen können, z. B. *Kontorichtlinien*:

    ![Screenshot des Gruppenrichtlinienverwaltungs-Editors.](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    Wenn Sie fertig sind, wählen Sie **Datei > Speichern**, um die Richtlinie zu speichern. Computer aktualisieren die Gruppenrichtlinie standardmäßig alle 90 Minuten und übernehmen die von Ihnen vorgenommenen Änderungen.

## <a name="create-a-custom-group-policy-object"></a>Erstellen eines benutzerdefinierten Gruppenrichtlinienobjekts

Um ähnliche Richtlinieneinstellungen zu gruppieren, erstellen Sie häufig zusätzliche GPOs, anstatt alle erforderlichen Einstellungen in dem einzelnen, standardmäßigen GPO anzuwenden. Mit Azure AD DS können Sie Ihre eigenen benutzerdefinierten Gruppenrichtlinienobjekte erstellen oder importieren und diese mit einer benutzerdefinierten Organisationseinheit verknüpfen. Wenn Sie zuerst eine benutzerdefinierte Organisationseinheit erstellen müssen, finden Sie weitere Informationen unter [Erstellen einer benutzerdefinierten Organisationseinheit in einer verwalteten Domäne](create-ou.md).

1. Wählen Sie in der Konsole für die **Gruppenrichtlinienverwaltung** die benutzerdefinierte Organisationseinheit aus, z. B. *MyCustomOU*. Klicken Sie mit der rechten Maustaste auf die Organisationseinheit, und wählen Sie dann **Gruppenrichtlinienobjekt hier erstellen und verknüpfen...** aus.

    ![Erstellen eines benutzerdefinierten GPOs in der Gruppenrichtlinien-Verwaltungskonsole](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. Geben Sie einen Namen für das neue GPO an, z. B. *Mein benutzerdefiniertes GPO*, und wählen Sie dann **OK** aus. Sie können dieses benutzerdefinierte GPO optional auf einem vorhandenen GPO und einer Reihe von Richtlinienoptionen aufbauen.

    ![Geben Sie einen Namen für das neue benutzerdefinierte Gruppenrichtlinienobjekt an.](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. Das benutzerdefinierte GPO wird erstellt und mit Ihrer benutzerdefinierten Organisationseinheit verknüpft. Klicken Sie mit der rechten Maustaste auf das benutzerdefinierte GPO, und wählen Sie **Bearbeiten...** aus, um jetzt die Richtlinieneinstellungen zu konfigurieren:

    ![Wählen Sie die Option zum Bearbeiten des benutzerdefinierten Gruppenrichtlinienobjekts aus.](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. Der **Gruppenrichtlinienverwaltungs-Editor** wird geöffnet, mit dem Sie das Gruppenrichtlinienobjekt anpassen können:

    ![Anpassen des Gruppenrichtlinienobjekts zum bedarfsgesteuerten Konfigurieren der Einstellungen](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    Wenn Sie fertig sind, wählen Sie **Datei > Speichern**, um die Richtlinie zu speichern. Computer aktualisieren die Gruppenrichtlinie standardmäßig alle 90 Minuten und übernehmen die von Ihnen vorgenommenen Änderungen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den verfügbaren Gruppenrichtlinieneinstellungen, die Sie über die Gruppenrichtlinien-Verwaltungskonsole konfigurieren können, finden Sie unter [Arbeiten mit Gruppenrichtlinien-Einstellungselementen][group-policy-console].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
