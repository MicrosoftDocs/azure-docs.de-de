---
title: 'Azure Active Directory Domain Services: Erste Schritte | Microsoft-Dokumentation'
description: Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: a4a515f3d669794d5bcdd8fa506a8d3b846db987
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234939"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals

## <a name="task-3-configure-administrative-group"></a>Aufgabe 3: Konfigurieren der administrativen Gruppe

In dieser Konfigurationsaufgabe erstellen Sie eine administrative Gruppe in Ihrem Azure AD-Verzeichnis. Diese spezielle administrative Gruppe heißt *AAD DC Administrators*. Mitgliedern dieser Gruppe werden Administratorrechte auf Computern erteilt, die in die verwaltete Domäne eingebunden sind. Auf in die Domäne eingebundenen Computern wird diese Gruppe der Gruppe „Administratoren“ hinzugefügt. Mitglieder dieser Gruppe können außerdem mithilfe von Remotedesktop Remoteverbindungen mit den in die Domäne eingebundenen Computern herstellen.

> [!NOTE]
> Sie verfügen nicht über Domänenadministrator- oder Unternehmensadministratorberechtigungen in der verwalteten Domäne, die Sie mithilfe von Azure Active Directory Domain Services erstellt haben. In verwalteten Domänen sind diese Berechtigungen für den Dienst reserviert und stehen Benutzern innerhalb des Mandanten nicht zur Verfügung. Sie können jedoch die in dieser Konfigurationsaufgabe verwendete spezielle administrative Gruppe verwenden, um einige privilegierte Vorgänge auszuführen. Zu diesen Vorgängen gehört das Einbinden von Computern in die Domäne, die Mitgliedschaft in der Administratorengruppe auf der Domäne beigetretenen Computern und das Konfigurieren von Gruppenrichtlinien.
>

Der Assistent erstellt automatisch die administrative Gruppe in Ihrem Azure AD-Verzeichnis. Diese Gruppe heißt „AAD DC Administrators“. Wenn eine Gruppe mit diesem Namen in Ihrem Azure AD-Verzeichnis besteht, wählt der Assistent diese Gruppe aus. Sie können die Gruppenmitgliedschaften konfigurieren, indem Sie das Blatt **Administratorgruppen** des Assistenten verwenden.

1. Um die Gruppenmitgliedschaften zu konfigurieren, klicken Sie auf **AAD DC Administrators**.

    ![Konfigurieren der Gruppenmitgliedschaft](./media/getting-started/domain-services-blade-admingroup.png)

2. Klicken Sie auf die Schaltfläche **Add members** (Mitglieder hinzufügen), um Benutzer aus Ihrem Azure AD-Verzeichnis zur Administratorgruppe hinzuzufügen.

3. Wenn Sie fertig sind, klicken Sie auf **OK**, um zur Seite **Summary** (Zusammenfassung) des Assistenten zu gelangen.

## <a name="configure-synchronization"></a>Konfigurieren der Synchronisierung

Azure AD Domain Services ermöglicht entweder die vollständige Synchronisierung aller in Azure AD verfügbaren Benutzer und Gruppen, oder die bereichsbezogene Synchronisierung, um nur bestimmte Gruppen zu synchronisieren. Wenn Sie die vollständige Synchronisierung wählen, können Sie zu einem späteren Zeitpunkt **nicht** die bereichsbezogene Synchronisierung auswählen. Weitere Informationen zur bereichsbezogenen Synchronisierung finden Sie im Artikel [Bereichsbezogene Synchronisierung für Azure AD Domain Services](scoped-synchronization.md).

### <a name="full-synchronization"></a>Vollständige Synchronisierung

1. Für die vollständige Synchronisierung klicken Sie einfach auf „OK“ am unteren Bildschirmrand, da „Vollständig“ bereits ausgewählt ist.
    ![Vollständige Synchronisierung](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Bereichsbezogene Synchronisierung

1. Schalten Sie die Schaltfläche für die Synchronisierung auf „Bereichsbezogen“ um, und es wird eine Seite zum Auswählen der Gruppen angezeigt. Darauf können Sie ersehen, welche Gruppen bereits ausgewählt sind, um mit Ihrer verwalteten Domäne synchronisiert zu werden.
    ![Bereichsbezogene Synchronisierung](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Wählen Sie in der oberen Navigationsleiste **Gruppen auswählen** aus. Von hier aus wird eine Gruppenauswahl auf der Seite eingeblendet. Verwenden Sie diese Option, um alle weiteren Gruppen auszuwählen, die mit Azure AD Domain Services synchronisiert werden sollen. Wenn Sie fertig sind, klicken Sie auf **Auswählen**, um die Gruppenauswahl zu schließen und diese Gruppen zur ausgewählten Liste hinzuzufügen.
    ![Gruppenauswahl für bereichsbezogene Synchronisierung](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Klicken Sie auf **OK**. um zur Zusammenfassung zu gelangen.

## <a name="deploy-your-managed-domain"></a>Bereitstellen Ihrer verwalteten Domäne

1. Überprüfen Sie die Konfigurationseinstellungen für die verwaltete Domäne auf der Seite **Summary** (Zusammenfassung) des Assistenten. Sie können zu jedem Schritt des Assistenten zurückgehen, um eventuell notwendige Änderungen vorzunehmen. Wenn Sie fertig sind, klicken Sie auf **OK**, um die neue verwaltete Domäne zu erstellen.

    ![Zusammenfassung](./media/getting-started/domain-services-blade-summary.png)

2. Sie sehen eine Benachrichtigung, die den Status Ihrer Bereitstellung über Azure AD Domain Services anzeigt. Klicken Sie auf die Benachrichtigung, um den detaillierten Status Ihrer Bereitstellung anzuzeigen.

    ![Benachrichtigung: Die Bereitstellung wird ausgeführt](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Überprüfen des Bereitstellungsstatus Ihrer verwalteten Domäne

Der Prozess der Bereitstellung Ihrer verwalteten Domänen kann bis zu einer Stunde dauern.

1. Während Ihre Bereitstellung ausgeführt wird, können Sie im Suchfeld **Search resources** (Ressourcen suchen) nach „Domänendienste“ suchen. Wählen Sie aus dem Suchergebnis **Azure AD Domain Services** aus. Das Blatt **Azure AD Domain Services** listet die verwaltete Domäne auf, die gerade bereitgestellt wird.

    ![Suchen der bereitgestellten verwalteten Domäne](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicken Sie auf den Namen der verwalteten Domäne (z.B. „contoso100.com“), um weitere Details zu der verwalteten Domäne zu erfahren.

    ![Domänendienste – Bereitstellungsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Die Registerkarte **Übersicht** zeigt an, dass die verwaltete Domäne gerade bereitgestellt wird. Die verwaltete Domäne kann nicht konfiguriert werden, bis sie vollständig bereitgestellt ist. Es kann bis zu einer Stunde dauern, bis Ihre verwaltete Domäne vollständig bereitgestellt ist.

    ![Domänendienste: Registerkarte „Übersicht“ während des Bereitstellungszustands](./media/getting-started/domain-services-provisioning-state-details.png)

4. Wenn die verwaltete Domäne vollständig bereitgestellt ist, zeigt die Registerkarte **Overview** (Übersicht) den Domänenstatus als **Running** (Wird ausgeführt) an.

    ![Domänendienste – Registerkarte „Übersicht“ nach der vollständigen Bereitstellung](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Während des Bereitstellungsprozesses erstellt Azure AD Domain Services Unternehmensanwendungen mit den Namen „Domain Controller Services“ und „AzureActiveDirectoryDomainControllerServices“ in Ihrem Verzeichnis. Diese Unternehmensanwendungen werden zur Unterstützung Ihrer verwalteten Domäne benötigt. Diese dürfen unter keinen Umständen gelöscht werden.
    >

5. Auf der Registerkarte **Properties** (Eigenschaften) sehen Sie zwei IP-Adressen, auf denen Domänencontroller für Ihr virtuelles Netzwerk verfügbar sind.

    ![Domänendienste: Registerkarte „Übersicht“ nach der vollständigen Bereitstellung](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Sie brauchen Hilfe?

Es dauert möglicherweise ein oder zwei Stunden, bis beide Domänencontroller für die verwaltete Domäne bereitgestellt werden. Wenn bei Ihrer Bereitstellung Fehler auftreten, oder sie im Status „Ausstehend“ steckenbleibt, [bitten Sie das Produktteam um Hilfe](contact-us.md).

## <a name="next-step"></a>Nächster Schritt

[Aufgabe 4: Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk](active-directory-ds-getting-started-dns.md)
