---
title: Einrichten der VMware-Notfallwiederherstellung in Azure in einer mehrinstanzenfähigen Umgebung mithilfe von Site Recovery und dem Cloud Solution Provider-Programm (CSP) | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie die VMware-Notfallwiederherstellung in einer mehrinstanzenfähigen Umgebung mithilfe von Azure Site Recovery einrichten.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60460980"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Einrichten der VMware-Notfallwiederherstellung in einer mehrinstanzenfähigen Umgebung mit dem Cloud Solution Provider-Programm (CSP)

Das [CSP-Programm](https://partner.microsoft.com/en-US/cloud-solution-provider) fördert ein besseres Zusammenspiel für Microsoft Cloud Services (einschließlich Office 365, Enterprise Mobility Suite und Microsoft Azure). Mit CSP können sich Partner um sämtliche Aspekte der Kundenbeziehung kümmern und als Hauptansprechpartner fungieren. Partner können Azure-Abonnements für Kunden bereitstellen und diese Abonnements mit ihren eigenen angepassten Angeboten mit einem Mehrwert kombinieren.

Mit [Azure Site Recovery](site-recovery-overview.md) können Partner die Notfallwiederherstellung für Kunden direkt im CSP-Programm verwalten. Sie können im CSP-Programm auch Site Recovery-Umgebungen einrichten, damit Kunden ihre eigenen Anforderungen an die Notfallwiederherstellung selbstständig verwalten können. In beiden Szenarien fungieren Partner als Verbindungsglied zwischen Site Recovery und ihren Kunden. Partner sind für die Kundenbeziehung zuständig und stellen Kunden die Nutzung von Site Recovery in Rechnung.

In diesem Artikel erfahren Sie, wie Sie als Partner über CSP Mandantenabonnements für ein VMware-Replikationsszenario mit mehreren Mandanten erstellen und verwalten können.

## <a name="prerequisites"></a>Voraussetzungen

Zum Einrichten der VMware-Replikation müssen Sie die folgenden Aktionen ausführen:

- [Vorbereiten](tutorial-prepare-azure.md) von Azure-Ressourcen, einschließlich eines Azure-Abonnements, eines virtuellen Azure-Netzwerks und eines Speicherkontos.
- [Vorbereiten](vmware-azure-tutorial-prepare-on-premises.md) von lokalen VMware-Servern und -VMs.
- Erstellen Sie für jeden Mandanten einen separaten Verwaltungsserver, der mit den VMs des Mandanten und Ihren vCenter-Servern kommunizieren kann. Nur Sie als Partner sollten über die Zugriffsrechte auf diesen Verwaltungsserver verfügen. Erfahren Sie mehr über [Umgebungen mit mehreren Mandanten](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Erstellen eines Mandantenkontos

1. Melden Sie im [Microsoft Partner Center](https://partnercenter.microsoft.com/) bei Ihrem CSP-Konto an.
2. Klicken Sie im Menü **Dashboard** auf **Kunden**.
3. Klicken Sie auf der Seite, die geöffnet wird, auf **Kunden hinzufügen**.
4. Füllen Sie auf der Seite **Neuer Kunde** die Kontoinformationen für den Mandanten aus.

    ![Die Seite „Kontoinformationen“](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klicken Sie auf **Weiter: Abonnements**.
6. Aktivieren Sie auf der Seite zur Auswahl des Abonnements das Kontrollkästchen**Microsoft Azure**. Sie können weitere Abonnements sofort oder zu einem späteren Zeitpunkt hinzufügen.
7. Bestätigen Sie auf der Seite **Überprüfung** die Mandantendetails, und klicken Sie dann auf **Senden**.
8. Nachdem Sie das Mandantenkonto erstellt haben, wird eine Bestätigungsseite mit den Details des Standardkontos und dem Kennwort dieses Abonnements angezeigt. Speichern Sie die Informationen, und ändern Sie das Kennwort später auf der Anmeldungsseite des Azure-Portals den Anforderungen entsprechend.

Sie können diese Informationen dem Mandanten unverändert mitteilen oder bei Bedarf ein getrenntes Konto erstellen und freigeben.

## <a name="access-the-tenant-account"></a>Zugreifen auf das Mandantenkonto

Sie können über das Microsoft Partner Center-Dashboard auf das Abonnement des Mandanten zugreifen.

1. Klicken Sie auf der Seite **Kunden** auf den Namen des Mandantenkontos.
2. Auf der Seite **Abonnements** des Mandantenkontos können Sie die vorhandenen Abonnements des Kontos überwachen und bei Bedarf weitere Abonnements hinzufügen.
3. Wählen Sie zum Verwalten der Vorgänge zur Notfallwiederherstellung des Mandanten **Alle Ressourcen (Azure-Portal)** aus. Damit wird Ihnen Zugriff auf die Azure-Abonnements des Mandanten gewährt.

    ![Der Link „Alle Ressourcen“](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Sie können den Zugriff überprüfen, indem Sie rechts oben im Azure-Portal auf den Link „Azure Active Directory“ klicken.

    ![Der Link „Azure Active Directory“](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

Sie können nun alle Site Recovery-Vorgänge für den Mandanten über das Azure-Portal ausführen und verwalten. Um für die verwaltete Notfallwiederherstellung über das CSP-Programm auf das Mandantenabonnement zugreifen zu können, befolgen Sie den zuvor beschriebenen Prozess.

## <a name="assign-tenant-access-to-the-subscription"></a>Zuweisen des Mandantenzugriffs auf das Abonnement

1. Stellen Sie sicher, dass die Infrastruktur für die Notfallwiederherstellung eingerichtet wurde. Unabhängig davon, ob der Typ der Notfallwiederherstellung „Verwaltet“ oder „Self-Service“ ist, greifen Partner über das CSP-Portal auf Abonnements des Mandanten zu. Richten Sie den Tresor ein, und registrieren Sie die Infrastruktur für die Abonnements des Mandanten.
2. Geben Sie dem Mandanten das [erstellte Konto](#create-a-tenant-account) an.
3. Sie können auch über das CSP-Portal wie folgt dem Mandantenabonnement neue Benutzer hinzufügen:

    a) Wechseln Sie zur CSP-Abonnementseite des Mandanten, und wählen Sie die Option **Benutzer und Lizenzen** aus.

      ![Der CSP-Seite mit dem Abonnement des Mandanten](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) Erstellen Sie nun einen neuen Benutzer, indem Sie die relevanten Details eingeben und Berechtigungen auswählen. Sie können aber auch eine Liste der Benutzer als CSV-Datei hochladen.
    
    c) Wechseln Sie, nachdem Sie einen neuen Benutzer erstellt haben, zurück zum Azure-Portal. Wählen Sie auf der Seite **Abonnement** das gewünschte Abonnement aus.

    d) Wählen Sie **Zugriffssteuerung (IAM)** aus, und klicken Sie dann auf **Rollenzuweisungen**.

    e) Klicken Sie auf **Rollenzuweisung hinzufügen**, um einen Benutzer mit dem entsprechenden Zugriffsumfang hinzuzufügen. Die über das CSP-Portal erstellten Benutzer werden auf der Registerkarte „Rollenzuweisungen“ angezeigt.

      ![Hinzufügen eines Benutzers](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Für die meisten Verwaltungsvorgänge ist die Rolle *Mitwirkender* ausreichend. Benutzer mit dieser Zugriffsebene können fast alle Aufgaben für ein Abonnement ausführen, jedoch keine Zugriffsebenen ändern (wofür die Zugriffsebene *Besitzer* erforderlich ist).
- Site Recovery verfügt auch über drei [vordefinierte Benutzerrollen](site-recovery-role-based-linked-access-control.md), die bei Bedarf zur weiteren Einschränkung von Zugriffsebenen verwendet werden können.

## <a name="multi-tenant-environments"></a>Umgebungen mit mehreren Mandanten

Es gibt drei wesentliche Modelle mit mehreren Mandanten:

* **Anbieter von gemeinsam genutzten Hostingdiensten**: Der Partner besitzt die physische Infrastruktur und arbeitet mit gemeinsam genutzten Ressourcen (vCenter, Rechenzentren, physischem Speicher usw.), um die VMs mehrerer Mandanten in derselben Infrastruktur zu hosten. Der Partner kann die Verwaltung der Notfallwiederherstellung als verwalteten Dienst bereitstellen, oder der Mandant kann die Notfallwiederherstellung als Self-Service-Lösung betreiben.

* **Anbieter von dedizierten Hostingdiensten**: Der Partner besitzt die physische Infrastruktur, verwendet aber dedizierte Ressourcen (mehrere vCenter-Server, physische Datenspeicher usw.), um die virtuellen Computer der einzelnen Mandanten jeweils in einer separaten Infrastruktur zu hosten. Der Partner kann die Verwaltung der Notfallwiederherstellung als verwalteten Dienst bereitstellen, oder der Mandant kann diese als Self-Service-Lösung betreiben.

* **Anbieter verwalteter Dienste**: Der Kunde besitzt die physische Infrastruktur, in der die VMs gehostet werden, während der Partner für die Aktivierung und Verwaltung der Notfallwiederherstellung zuständig ist.

Durch das Einrichten von Mandantenabonnements wie in diesem Artikel beschrieben können Sie schnell in jedem der entsprechenden Modelle mit mehreren Mandanten Kunden aktivieren. Weitere Informationen zu den verschiedenen Modellen mit mehreren Mandanten und zum Aktivieren lokaler Zugriffssteuerungen finden Sie [hier](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das Verwalten von Azure Site Recovery-Bereitstellungen mithilfe der [rollenbasierten Zugriffssteuerung](site-recovery-role-based-linked-access-control.md).
- Erfahren Sie mehr über VMware in der [Replikationsarchitektur](vmware-azure-architecture.md) von Azure.
- [Bearbeiten Sie das Tutorial](vmware-azure-tutorial.md) für das Replizieren von virtuellen VMware-Computern in Azure.
Erfahren Sie mehr über [Umgebungen mit mehreren Mandanten](vmware-azure-multi-tenant-overview.md) für die Replikation von VMware-VMs in Azure.
