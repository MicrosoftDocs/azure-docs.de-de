---
title: Verwalten von DNS für Azure AD Domain Services | Microsoft-Dokumentation
description: Verwalten von DNS für Azure AD Domain Services
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
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 3c844b6ede63b2e036f05b5b7f6cb3803eb4b47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245295"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Verwalten von DNS in einer durch Azure AD-Domänendienste verwalteten Domäne
Azure Active Directory-Domänendienste umfassen einen DNS-Server (Domain Name Service), der die DNS-Auflösung für die verwaltete Domäne durchführt. Gelegentlich kann es erforderlich sein, DNS für die verwaltete Domäne zu konfigurieren. Unter Umständen müssen Sie DNS-Einträge für nicht in die Domäne eingebundene Computer erstellen, virtuelle IP-Adressen für Load Balancer konfigurieren oder externe DNS-Weiterleitungen einrichten. Aus diesem Grund werden Benutzern, die zur Administratorengruppe für Azure AD-Domänencontroller gehören, DNS-Administratorrechte in der verwalteten Domäne gewährt.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Zum Ausführen der in diesem Artikel aufgeführten Anweisungen ist Folgendes erforderlich:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Einen **in die Domäne eingebundenen virtuellen Computer** , über den Sie die mit den Azure AD-Domänendiensten verwaltete Domäne verwalten. Wenn Sie nicht über einen virtuellen Computer dieser Art verfügen, führen Sie alle Schritte wie in Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md) beschriebenen aus.
5. Sie benötigen die Anmeldeinformationen eines **Benutzerkontos, das in Ihrem Verzeichnis der Administratorengruppe für AAD-Domänencontroller angehört** , um DNS für die verwaltete Domäne verwalten zu können.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Aufgabe 1: Erstellen eines der Domäne beigetretenen virtuellen Computers zur Remoteverwaltung von DNS für die verwaltete Domäne
Durch die Azure AD Domain Services verwaltete Domänen können mithilfe bekannter Active Directory-Verwaltungstools verwaltet werden, beispielsweise über das Active Directory-Verwaltungscenter (Active Directory Administrative Center, ADAC) oder AD PowerShell. Auf die gleiche Weise kann DNS für die verwaltete Domäne remote mithilfe der DNS-Server-Verwaltungstools verwaltet werden.

Administratoren in Ihrem Azure AD-Verzeichnis verfügen über keine Berechtigungen, um über Remotedesktop eine Verbindung mit Domänencontrollern in der verwalteten Domäne herzustellen. Mitglieder der Administratorengruppe für AAD-Domänencontroller können DNS für verwaltete Domänen remote über die DNS-Servertools eines Windows Server- oder Clientcomputers verwalten, der der verwalteten Domäne beigetreten ist. DNS-Servertools sind Teil des optionalen Features Remoteserver-Verwaltungstools (Remote Server Administration Tools, RSAT).

Die erste Aufgabe besteht darin, einen virtuellen Windows Server-Computer zu erstellen, der der verwalteten Domäne beigetreten ist. Anweisungen hierzu finden Sie im Artikel [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Aufgabe 2: Installieren von DNS-Servertools auf dem virtuellen Computer
Führen Sie die folgenden Schritte aus, um die DNS-Verwaltungstools auf dem virtuellen Computer zu installieren, der der Domäne beigetreten ist. Weitere Informationen zum [Installieren und Verwenden der Remote Server-Verwaltungstools](https://technet.microsoft.com/library/hh831501.aspx) finden Sie in TechNet.

1. Navigieren Sie zum Azure-Portal. Klicken Sie im linken Bereich auf **Alle Ressourcen**. Klicken Sie auf den virtuellen Computer, den Sie in Aufgabe 1 erstellt haben.
2. Klicken Sie auf der Registerkarte „Übersicht“ auf die Schaltfläche **Verbinden**. Eine Remotedesktopprotokoll-Datei (RDP-Datei) wird erstellt und heruntergeladen.

    ![Verbindung mit virtuellem Windows-Computer herstellen](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Öffnen Sie die heruntergeladene RDP-Datei, um eine Verbindung mit Ihrem virtuellen Computer herzustellen. Klicken Sie in der angezeigten Aufforderung auf **Verbinden**. Verwenden Sie die Anmeldeinformationen eines Benutzers, der zur Administratorengruppe für Azure AD-Domänencontroller gehört. Beispiel: bob@domainservicespreview.onmicrosoft.com. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Klicken Sie auf „Ja“ oder „Fortfahren", um eine Verbindung herstellen.

4. Öffnen Sie auf dem Startbildschirm den **Server-Manager**. Klicken Sie im mittleren Bereich des Server-Manager-Fensters auf **Rollen und Features hinzufügen** .

    ![Server-Manager auf dem virtuellen Computer starten](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Klicken Sie auf der Seite **Vorbereitung** des **Assistenten zum Hinzufügen von Rollen und Funktionen** auf **Weiter**.

    ![Seite „Vorbereitung“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Behalten Sie auf der Seite **Installationstyp** die Aktivierung der Option **Rollenbasierte oder featurebasierte Installation** bei, und klicken Sie auf **Weiter**.

    ![Seite „Installationstyp“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Wählen Sie auf der Seite **Serverauswahl** den aktuellen virtuellen Computer im Serverpool aus, und klicken Sie auf **Weiter**.

    ![Seite „Serverauswahl“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Klicken Sie auf der Seite **Serverrollen** auf **Weiter**.
9. Erweitern Sie auf der Seite **Features** durch Klicken den Knoten **Remote Server-Verwaltungstools**, und erweitern Sie dann durch erneutes Klicken den Knoten **Rollenverwaltungstools**. Wählen Sie das Feature **DNS-Servertools** in der Liste der Rollenverwaltungstools aus.

    ![Seite „Features“](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Klicken Sie auf der Seite **Bestätigung** auf **Installieren**, um die DNS-Servertools auf dem virtuellen Computer zu installieren. Wenn die Installation des Features erfolgreich abgeschlossen wurde, klicken Sie auf **Schließen**, um den Assistenten zum **Hinzufügen von Rollen und Funktionen** zu beenden.

    ![Bestätigungsseite](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Aufgabe 3: Starten der DNS-Verwaltungskonsole zum Verwalten von DNS
Nun können Sie mit Windows Server-DNS-Tools den DNS auf der verwalteten Domäne verwalten.

> [!NOTE]
> Sie müssen der Administratorengruppe für AAD-Domänencontroller angehören, um DNS in der verwalteten Domäne verwalten zu können.
>
>

1. Klicken Sie auf dem Startbildschirm auf **Verwaltung**. Daraufhin sollte die auf dem virtuellen Computer installierte **DNS** -Konsole angezeigt werden.

    ![Verwaltungstools – DNS-Konsole](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Klicken Sie auf **DNS** , um die DNS-Verwaltungskonsole zu starten.
3. Klicken Sie im Dialogfeld **Verbindung mit DNS-Server herstellen** auf **Folgender Computer**, und geben Sie den DNS-Domänennamen der verwalteten Domäne ein (beispielsweise „contoso100.com“).

    ![DNS-Konsole – Verbindung mit Domäne herstellen](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. Die DNS-Konsole stellt eine Verbindung mit der verwalteten Domäne her.

    ![DNS-Konsole – Domäne verwalten](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Sie können die DNS-Konsole jetzt verwenden, um DNS-Einträge für Computer in dem virtuellen Netzwerk hinzuzufügen, in dem Sie die Azure AD-Domänendienste aktiviert haben.

> [!WARNING]
> Gehen Sie vorsichtig bei der Handhabung des DNS für die verwaltete Domäne mithilfe der DNS-Verwaltungstools vor. Stellen Sie sicher, dass Sie **die integrierten DNS-Einträge, die von den Domänendiensten in der Domäne verwendet werden, nicht löschen oder ändern**. Zu den integrierten DNS-Einträgen zählen Domänen-DNS-Einträge, Namenservereinträge sowie weitere für den DC-Standort verwendete Einträge. Wenn Sie diese Einträge ändern, werden die Domänendienste im virtuellen Netzwerk unterbrochen.
>
>

Weitere Informationen zur DNS-Verwaltung finden Sie im [Technet-Artikel zu DNS-Tools](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Verwandte Inhalte
* [Azure AD-Domänendienste – Leitfaden zu den ersten Schritten](create-instance.md)
* [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne der Azure AD-Domänendienste](active-directory-ds-admin-guide-join-windows-vm.md)
* [Verwalten einer Azure AD Domain Services-Domäne](manage-domain.md)
* [DNS-Verwaltungstools](https://technet.microsoft.com/library/cc753579.aspx)
