---
title: 'Tutorial: Erstellen einer benutzerdefinierten verwalteten Azure Active Directory Domain Services-Domäne | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie im Azure-Portal eine benutzerdefinierte verwaltete Azure Active Directory Domain Services-Domäne erstellen und konfigurieren und erweiterte Konfigurationsoptionen angeben.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4d03edac98329d7c47b8bfafbf4d30cfca18863c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618228"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-managed-domain-with-advanced-configuration-options"></a>Tutorial: Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne mit erweiterten Konfigurationsoptionen

Azure Active Directory Domain Services (Azure AD DS) stellt verwaltete Domänendienste bereit, z. B. Domänenbeitritt, Gruppenrichtlinie, LDAP und Kerberos-/NTLM-Authentifizierung, die mit Windows Server Active Directory vollständig kompatibel sind. Sie können diese Domänendienste nutzen, ohne selbst Domänencontroller bereitstellen, verwalten und patchen zu müssen. Azure AD DS lässt sich in Ihren vorhandenen Azure AD-Mandanten integrieren. Dank dieser Integration können Benutzer sich mit ihren Unternehmensanmeldeinformationen anmelden, und Sie können vorhandene Gruppen und Benutzerkonten verwenden, um den Zugriff auf Ressourcen zu sichern.

Sie können [eine verwaltete Domäne mit Standardkonfigurationsoptionen für Netzwerk und Synchronisierung erstellen][tutorial-create-instance] oder diese Einstellungen manuell festlegen. In diesem Tutorial erfahren Sie, wie Sie die erweiterten Konfigurationsoptionen definieren, um eine verwaltete Azure AD DS-Domäne im Azure-Portal zu erstellen und zu konfigurieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Einstellungen für DNS und ein virtuelles Netzwerk für eine verwaltete Domäne
> * Erstellen einer verwalteten Domäne
> * Hinzufügen von Administratorbenutzern zur Domänenverwaltung
> * Aktivieren der Kennworthashsynchronisierung

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um Azure AD DS zu aktivieren.
* Sie benötigen Berechtigungen als *Mitwirkender* in Ihrem Azure-Abonnement, um die erforderlichen Azure AD DS-Ressourcen zu erstellen.

Es ist bei Azure AD DS zwar nicht erforderlich, für den Azure AD-Mandanten die [Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) zu konfigurieren][configure-sspr], es wird jedoch empfohlen. Benutzer können Ihr Kennwort ohne SSPR ändern. SSPR ist jedoch hilfreich, wenn Benutzer ihr Kennwort vergessen haben und es zurücksetzen müssen.

> [!IMPORTANT]
> Nach der Erstellung einer verwalteten Domäne können Sie diese nicht in eine andere Ressourcengruppe, ein anderes virtuelles Netzwerk, ein anderes Abonnement usw. verschieben. Wählen Sie beim Bereitstellen der verwalteten Domäne das am besten geeignete Abonnement, die am besten geeignete Ressourcengruppe und Region und das am besten geeignete virtuelle Netzwerk aus.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Tutorial erstellen und konfigurieren Sie die verwaltete Domäne im Azure-Portal. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-managed-domain-and-configure-basic-settings"></a>Erstellen einer verwalteten Domäne und Konfigurieren grundlegender Einstellungen

Um den Assistenten zum **Aktivieren von Azure AD Domain Services** zu starten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus.
1. Geben Sie *Domain Services* in die Suchleiste ein, und wählen Sie *Azure AD Domain Services* aus den Suchvorschlägen aus.
1. Klicken Sie auf der Seite „Azure AD Domain Services“ auf **Erstellen**. Der Assistent zum **Aktivieren von Azure AD Domain Services** wird gestartet.
1. Wählen Sie das **Azure-Abonnement**, in dem Sie die verwaltete Domäne erstellen möchten.
1. Wählen Sie die **Ressourcengruppe**, zu der die verwaltete Domäne gehören soll. Klicken Sie auf **Neu erstellen**, oder wählen Sie eine vorhandene Ressourcengruppe aus.

Beim Erstellen einer verwalteten Domäne geben Sie einen DNS-Namen an. Bei der Auswahl dieses DNS-Namens sind folgende Aspekte zu berücksichtigen:

* **Integrierter Domänenname:** Standardmäßig wird der integrierte Domänenname des Verzeichnisses verwendet (das Suffix *.onmicrosoft.com*). Wenn Sie Secure LDAP für den Zugriff auf die verwaltete Domäne über das Internet aktivieren möchten, können Sie kein digitales Zertifikat erstellen, um die Verbindung mit dieser Standarddomäne zu sichern. Die Domäne *.onmicrosoft.com* ist im Besitz von Microsoft, daher stellt keine Zertifizierungsstelle ein Zertifikat aus.
* **Benutzerdefinierte Domänennamen:** Die gängigste Vorgehensweise besteht darin, einen benutzerdefinierten Domänennamen anzugeben – in der Regel den Namen einer Domäne, die Sie bereits besitzen und die routingfähig ist. Wenn Sie eine routingfähige benutzerdefinierte Domäne verwenden, kann der Datenverkehr ordnungsgemäß und bedarfsgerecht weitergeleitet werden, um Ihre Anwendungen zu unterstützen.
* **Nicht routingfähige Domänensuffixe:** Im Allgemeinen wird empfohlen, nicht routingfähige Domänennamesuffixe wie z. B. *contoso.local* zu vermeiden. Das Suffix *.local* ist nicht routingfähig und kann zu Problemen mit der DNS-Auflösung führen.

> [!TIP]
> Lassen Sie beim Erstellen eines benutzerdefinierten Domänennamens Vorsicht in Bezug auf DNS-Namespaces walten. Es wird empfohlen, einen Domänennamen zu verwenden, der von vorhandenen Azure- oder lokalen DNS-Namespaces getrennt ist.
>
> Lautet Ihr vorhandener DNS-Namespace beispielsweise *contoso.com*, erstellen Sie eine verwaltete Domäne mit dem benutzerdefinierten Domänennamen *aaddscontoso.com*. Wenn Sie Secure LDAP verwenden, müssen Sie diesen benutzerdefinierten Domänennamen registrieren und sein Besitzer sein, um die erforderlichen Zertifikate generieren zu können.
>
> Möglicherweise müssen Sie einige zusätzliche DNS-Einträge für andere Dienste in Ihrer Umgebung oder bedingte DNS-Weiterleitungen zwischen vorhandenen DNS-Namespaces in Ihrer Umgebung erstellen. Beispiel: Wenn Sie einen Webserver ausführen, der unter Verwendung des DNS-Stammnamens eine Website hostet, können Namenskonflikte auftreten, aufgrund derer zusätzliche DNS-Einträge erforderlich sind.
>
> In diesen Tutorials und Anleitungen wird die benutzerdefinierte Domäne *aaddscontoso.com* als kurzes Beispiel verwendet. Geben Sie in allen Befehlen Ihren eigenen Domänennamen an.

Es gelten außerdem die folgenden Einschränkungen für DNS-Namen:

* **Einschränkungen für Domänenpräfixe:** Sie können keine verwaltete Domäne mit einem Präfix erstellen, das länger ist als 15 Zeichen. Das Präfix des angegebenen Domänennamens (beispielsweise *aaddscontoso* im Domänennamen *aaddscontoso.com*) darf maximal 15 Zeichen lang sein.
* **Netzwerknamenskonflikte:** Der DNS-Domänenname für Ihre verwaltete Domäne darf im virtuellen Netzwerk noch nicht vorhanden sein. Achten Sie speziell auf die folgenden Szenarien, die zu einem Namenskonflikt führen würden:
    * Im virtuellen Azure-Netzwerk ist bereits eine Active Directory-Domäne mit dem gleichen DNS-Domänennamen vorhanden.
    * Das virtuelle Netzwerk, in dem Sie die verwaltete Domäne aktivieren möchten, verfügt über eine VPN-Verbindung mit Ihrem lokalen Netzwerk. In diesem Szenario stellen Sie sicher, dass Sie keine Domäne mit demselben DNS-Domänennamen in Ihrem lokalen Netzwerk haben.
    * Im virtuellen Azure-Netzwerk ist bereits ein Azure-Clouddienst mit diesem Namen vorhanden.

Füllen Sie im Azure-Portal die Felder im Fenster *Grundlagen* aus, um eine verwaltete Domäne zu erstellen:

1. Geben Sie einen **DNS-Domänennamen** für Ihre verwaltete Domäne ein, und berücksichtigen Sie dabei die oben genannten Punkte.
1. Wählen Sie den **Azure-Speicherort**, in dem die verwaltete Domäne erstellt werden soll. Wenn Sie eine Region mit Unterstützung von Verfügbarkeitszonen auswählen, werden die Azure AD DS-Ressourcen auf mehrere Zonen verteilt, um zusätzliche Redundanz zu erzielen.

    > [!TIP]
    > Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden.
    >
    > Für die Verteilung auf Zonen für Azure AD DS fällt für Sie kein Konfigurationsaufwand an. Die Verteilung der Ressourcen auf Zonen wird von der Azure-Plattform automatisch durchgeführt. Weitere Informationen, z. B. zur regionalen Verfügbarkeit, finden Sie unter [Was sind Verfügbarkeitszonen in Azure?][availability-zones].

1. Die **SKU** bestimmt die Leistung, die Sicherungshäufigkeit und die maximale Anzahl von Gesamtstrukturvertrauensstellungen, die Sie erstellen können. Sie können die SKU nach der Erstellung der verwalteten Domäne ändern, wenn es Ihr Unternehmen erfordert oder sich die Anforderungen ändern. Weitere Informationen finden Sie unter [Verwaltungskonzepte für Benutzerkonten, Kennwörter und die Verwaltung in Azure Active Directory Domain Services][concepts-sku].

    Wählen Sie für dieses Tutorial die SKU *Standard* aus.
1. Eine *Gesamtstruktur* ist ein logisches Konstrukt, das von Active Directory Domain Services zum Gruppieren von Domänen verwendet wird. Eine verwaltete Domäne wird standardmäßig als *Benutzergesamtstruktur* erstellt. Mit dieser Art von Gesamtstruktur werden alle Objekte aus Azure AD synchronisiert – einschließlich aller in einer lokalen AD DS-Umgebung erstellten Benutzerkonten.

    Eine *Ressourcengesamtstruktur* synchronisiert nur Benutzer und Gruppen, die direkt in Azure AD erstellt werden. Kennworthashes für lokale Benutzer werden nie in einer verwalteten Domäne synchronisiert, wenn Sie eine Ressourcengesamtstruktur erstellen. Weitere Informationen zu *Ressourcengesamtstrukturen* finden Sie in der [Übersicht zu Azure AD DS-Ressourcengesamtstrukturen][resource-forests]. Dort werden u. a. die Gründe für ihre Verwendung sowie die Vorgehensweise zum Erstellen von Gesamtstrukturvertrauensstellungen mit lokalen AD DS-Domänen erläutert.

    Erstellen Sie für dieses Tutorial eine *Benutzergesamtstruktur*.

    ![Konfigurieren der grundlegenden Einstellungen für eine verwaltete Azure AD Domain Services-Domäne](./media/tutorial-create-instance-advanced/basics-window.png)

1. Wählen Sie zum manuellen Konfigurieren zusätzlicher Optionen **Weiter: Netzwerk** aus. Wählen Sie andernfalls **Überprüfen + erstellen** aus, um die Standardkonfigurationsoptionen zu übernehmen, und fahren Sie anschließend mit dem Abschnitt [Bereitstellen der verwalteten Domäne](#deploy-the-managed-domain) fort. Bei Verwendung dieser Erstellungsoption werden folgende Standardwerte konfiguriert:

    * Ein virtuelles Netzwerk mit dem Namen *aadds-vnet* und dem IP-Adressbereich *10.0.1.0/24* wird erstellt.
    * Ein Subnetz mit dem Namen *aadds-subnet* und dem IP-Adressbereich *10.0.1.0/24* wird erstellt.
    * *Alle* Benutzer aus Azure AD werden mit der verwalteten Domäne synchronisiert.

## <a name="create-and-configure-the-virtual-network"></a>Erstellen und Konfigurieren des virtuellen Netzwerks

Um Konnektivität bereitzustellen, werden ein virtuelles Azure-Netzwerk und ein dediziertes Subnetz benötigt. Azure AD DS wird in diesem virtuellen Subnetz aktiviert. In diesem Tutorial erstellen Sie ein virtuelles Netzwerk, obwohl Sie auch ein vorhandenes virtuelles Netzwerk verwenden könnten. Bei beiden Verfahrensweisen müssen Sie ein dediziertes Subnetz erstellen, das durch Azure AD DS genutzt werden kann.

Im Folgenden finden Sie einige Überlegungen, die Sie bei diesem dedizierten Subnetz im virtuellen Netzwerk berücksichtigen müssen:

* Das Subnetz muss mindestens 3–5 verfügbare IP-Adressen in seinem Adressbereich aufweisen, um die Azure AD DS-Ressourcen zu unterstützen.
* Wählen Sie zum Bereitstellen von Azure AD DS nicht das Subnetz *Gateway* aus. Die Bereitstellung von Azure AD DS in einem *Gatewaysubnetz* wird nicht unterstützt.
* Stellen Sie in diesem Subnetz keine weiteren virtuellen Computer bereit. Anwendungen und VMs nutzen häufig Netzwerksicherheitsgruppen, um die Konnektivität zu sichern. Indem diese Workloads in einem separaten Subnetz ausgeführt werden, können Sie diese Netzwerksicherheitsgruppen anwenden, ohne die Konnektivität mit Ihrer verwalteten Domäne zu unterbrechen.
* Nach dem Aktivieren von Azure AD DS können Sie Ihre verwaltete Domäne nicht mehr in ein anderes virtuelles Netzwerk verschieben.

Weitere Informationen zum Planen und Konfigurieren des virtuellen Netzwerks finden Sie unter [Netzwerkaspekte für die Azure Active Directory Domain Services][network-considerations].

Füllen Sie die Felder im Fenster *Netzwerk* wie folgt aus:

1. Wählen Sie auf der Seite **Netzwerk** im Dropdownmenü ein virtuelles Netzwerk für die Bereitstellung von Azure AD DS aus, oder klicken Sie auf **Neues Element erstellen**.
    1. Falls Sie sich für die Erstellung eines virtuellen Netzwerks entscheiden, geben Sie einen Namen für das virtuelle Netzwerk (beispielsweise *myVnet*) ein und anschließend einen Adressbereich (beispielsweise *10.0.1.0/24*) an.
    1. Erstellen Sie ein dediziertes Subnetz mit einem eindeutigen Namen, z. B. *DomainServices*. Geben Sie einen Adressbereich an (beispielsweise *10.0.1.0/24*).

    [ ![Erstellen eines virtuellen Netzwerks und Subnetzes für die Verwendung mit Azure AD Domain Services](./media/tutorial-create-instance-advanced/create-vnet.png)](./media/tutorial-create-instance-advanced/create-vnet-expanded.png#lightbox)

    Stellen Sie sicher, dass Sie einen Adressbereich auswählen, der innerhalb Ihres privaten IP-Adressbereichs liegt. IP-Adressen, die Sie nicht besitzen und die sich im öffentlichen Adressraum befinden, führen zu Fehlern in Azure AD DS.

1. Wählen Sie ein Subnetz des virtuellen Netzwerks aus (beispielsweise *DomainServices*).
1. Wählen Sie **Weiter: Verwaltung** aus, wenn Sie so weit sind.

## <a name="configure-an-administrative-group"></a>Konfigurieren einer Administratorgruppe

Zur Verwaltung der Azure AD DS-Domäne wird eine spezielle Administratorgruppe namens *AAD DC-Administratoren* verwendet. Mitgliedern dieser Gruppe werden Administratorrechte auf VMs erteilt, die in die verwaltete Domäne eingebunden sind. Auf in die Domäne eingebundenen VMs wird diese Gruppe zur lokalen Administratorengruppe hinzugefügt. Mitglieder dieser Gruppe können auch Remotedesktop verwenden, um Remoteverbindungen mit den in die Domäne eingebundenen VMs herzustellen.

> [!IMPORTANT]
> Sie besitzen keine Berechtigungen als *Domänenadministrator* oder *Unternehmensadministrator* für eine verwaltete Domäne, in der Azure AD DS verwendet wird. Diese Berechtigungen sind vom Dienst reserviert und stehen Benutzern innerhalb des Mandanten nicht zur Verfügung.
>
> Stattdessen können Sie über die Gruppe *AAD DC-Administratoren* einige Vorgänge ausführen, für die entsprechende Berechtigungen erforderlich sind. Zu diesen Vorgängen gehören die Zugehörigkeit zur Administratorengruppe auf in die Domäne eingebundenen virtuellen Computern und das Konfigurieren von Gruppenrichtlinien.

Der Assistent erstellt die Gruppe *AAD DC-Administratoren* automatisch in Ihrem Azure AD-Verzeichnis. Wenn eine Gruppe mit diesem Namen in Ihrem Azure AD-Verzeichnis besteht, wählt der Assistent diese Gruppe aus. Sie können der Gruppe *AAD DC-Administratoren* während des Bereitstellungsprozesses optional weitere Benutzer hinzufügen. Diese Schritte können später ausgeführt werden.

1. Um dieser Gruppe der *AAD DC-Administratoren* weitere Benutzer hinzuzufügen, klicken Sie auf **Gruppenmitgliedschaft verwalten**.

    ![Konfigurieren der Gruppenmitgliedschaft der Gruppe „AAD DC-Administratoren“](./media/tutorial-create-instance-advanced/admin-group.png)

1. Klicken Sie auf die Schaltfläche **Mitglieder hinzufügen**, suchen Sie in Ihrem Azure AD-Verzeichnis nach Benutzern, und fügen Sie diese hinzu. Sie können beispielsweise nach Ihrem eigenen Konto suchen und dieses der Gruppe *AAD DC-Administratoren* hinzufügen.
1. Bei Bedarf können Sie die Empfänger von Benachrichtigungen ändern oder weitere Empfänger hinzufügen, wenn in der verwalteten Domäne Warnungen vorliegen, auf die reagiert werden muss.
1. Wählen Sie **Weiter: Synchronisierung** aus, wenn Sie so weit sind.

## <a name="configure-synchronization"></a>Konfigurieren der Synchronisierung

Mit Azure AD DS können Sie *alle* in Azure AD verfügbaren Benutzer und Gruppen synchronisieren oder eine *bereichsbezogene* Synchronisierung spezifischer Gruppen durchführen. Sie können den Synchronisierungsbereich jetzt oder nach dem Bereitstellen der verwalteten Domäne ändern. Weitere Informationen finden Sie unter [Bereichsbezogene Synchronisierung für Azure AD Domain Services][scoped-sync].

1. Synchronisieren Sie für dieses Tutorial **alle** Benutzer und Gruppen. Dies ist die Standardoption.

    ![Ausführen einer vollständigen Synchronisierung von Benutzern und Gruppen über Azure AD](./media/tutorial-create-instance-advanced/sync-all.png)

1. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="deploy-the-managed-domain"></a>Bereitstellen der verwalteten Domäne

Überprüfen Sie die Konfigurationseinstellungen für Ihre verwaltete Domäne auf der Seite **Zusammenfassung** des Assistenten. Sie können zu jedem Schritt des Assistenten zurückgehen, um Änderungen vorzunehmen. Wenn Sie eine verwaltete Domäne mit diesen Konfigurationsoptionen auf konsistente Weise für einen anderen Azure AD-Mandanten erneut bereitstellen möchten, können Sie auch **eine Vorlage für die Automatisierung herunterladen**.

1. Wählen Sie zum Erstellen der verwalteten Domäne **Erstellen** aus. Ein Hinweis wird angezeigt, dass bestimmte Konfigurationsoptionen, etwa der DNS-Name oder das virtuelle Netzwerk, nach der Erstellung der verwalteten Azure AD DS-Domäne nicht geändert werden können. Wählen Sie **OK** aus, um fortzufahren.
1. Der Prozess der Bereitstellung Ihrer verwalteten Domänen kann bis zu einer Stunde dauern. Es wird eine Benachrichtigung angezeigt, die Sie über den Status Ihrer Azure AD DS-Bereitstellung informiert. Wählen Sie die Benachrichtigung aus, um sich den detaillierten Fortschritt Ihrer Bereitstellung anzusehen.

    ![Benachrichtigung im Azure-Portal über den Fortschritt der Bereitstellung](./media/tutorial-create-instance-advanced/deployment-in-progress.png)

1. Wählen Sie Ihre Ressourcengruppe (z. B. *myResourceGroup*) aus, und wählen Sie dann aus der Liste der Azure-Ressourcen Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus. Die Registerkarte **Übersicht** zeigt an, dass die verwaltete Domäne sich im Status *Wird bereitgestellt* befindet. Sie können die verwaltete Domäne erst dann konfigurieren, wenn sie vollständig bereitgestellt ist.

    ![Status „Wird bereitgestellt“ der Domäne](./media/tutorial-create-instance-advanced/provisioning-in-progress.png)

1. Wenn die verwaltete Domäne vollständig bereitgestellt ist, zeigt die Registerkarte **Overview** (Übersicht) den Domänenstatus als *Running* (Wird ausgeführt) an.

    ![Status der Domäne nach erfolgreicher Bereitstellung](./media/tutorial-create-instance-advanced/successfully-provisioned.png)

> [!IMPORTANT]
> Die verwaltete Domäne wird Ihrem Azure AD-Mandanten zugeordnet. Während des Bereitstellungsprozesses erstellt Azure AD DS auf Ihrem Azure AD-Mandanten zwei Unternehmensanwendungen mit den Namen *Domain Controller Services* und *AzureActiveDirectoryDomainControllerServices*. Diese Unternehmensanwendungen werden zur Unterstützung Ihrer verwalteten Domäne benötigt. Löschen Sie diese Anwendungen nicht.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk

Nachdem Azure AD DS erfolgreich bereitgestellt wurde, konfigurieren Sie nun das virtuelle Netzwerk so, dass andere verbundene VMs und Anwendungen die verwaltete Domäne verwenden können. Um diese Konnektivität zu ermöglichen, aktualisieren Sie die DNS-Servereinstellungen für Ihr virtuelles Netzwerk so, dass diese auf die beiden IP-Adressen verweisen, unter denen die verwaltete Domäne bereitgestellt ist.

1. Auf der Registerkarte **Übersicht** für Ihre verwaltete Domäne werden einige **erforderliche Konfigurationsschritte** angezeigt. Der erste Konfigurationsschritt besteht darin, die DNS-Servereinstellungen für Ihr virtuelles Netzwerk zu aktualisieren. Sobald die DNS-Einstellungen ordnungsgemäß konfiguriert sind, wird dieser Schritt nicht mehr angezeigt.

    Die aufgelisteten Adressen sind die Domänencontroller, die im virtuellen Netzwerk genutzt werden können. In diesem Beispiel lauten die Adressen *10.0.1.4* und *10.0.1.5*. Sie finden diese IP-Adressen später auf der Registerkarte **Eigenschaften**.

    ![Konfigurieren von DNS-Einstellungen für Ihr virtuelles Netzwerk mit den IP-Adressen der Azure AD Domain Services](./media/tutorial-create-instance-advanced/configure-dns.png)

1. Klicken Sie auf die Schaltfläche **Konfigurieren**, um die DNS-Servereinstellungen für das virtuelle Netzwerk zu aktualisieren. Die DNS-Einstellungen werden automatisch für Ihr virtuelles Netzwerk konfiguriert.

> [!TIP]
> Wenn Sie in den vorherigen Schritten ein virtuelles Netzwerk ausgewählt haben, erhalten alle VMs, die mit dem Netzwerk verbunden sind, die neuen DNS-Einstellungen erst nach einem Neustart. Sie können VMs über das Azure-Portal, mit Azure PowerShell oder mithilfe der Azure CLI neu starten.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Aktivieren von Benutzerkonten für Azure AD DS

Um Benutzer in der verwalteten Domäne authentifizieren zu können, benötigt Azure AD DS Kennworthashes in einem Format, das für die Authentifizierung über NT LAN Manager (NTLM) und Kerberos geeignet ist. Azure AD generiert oder speichert erst dann Kennworthashes in dem für die NTLM- oder Kerberos-Authentifizierung erforderlichen Format, wenn Sie Azure AD DS für Ihren Mandanten aktivieren. Aus Sicherheitsgründen speichert Azure AD Kennwörter nicht als Klartext. Daher kann Azure AD diese NTLM- oder Kerberos-Kennworthashes nicht automatisch auf der Grundlage bereits vorhandener Anmeldeinformationen von Benutzern generieren.

> [!NOTE]
> Nach entsprechender Konfiguration werden die verwendbaren Kennworthashes in der verwalteten Domäne gespeichert. Wenn Sie die verwaltete Domäne löschen, werden alle zu diesem Zeitpunkt gespeicherten Kennworthashes ebenfalls gelöscht.
>
> Synchronisierte Anmeldeinformationen in Azure AD können nicht wiederverwendet werden, wenn Sie später eine verwaltete Domäne erstellen. Sie müssen die Kennworthashsynchronisierung erneut konfigurieren, um die Kennworthashes wieder zu speichern. VMs oder Benutzer, die zuvor in eine Domäne eingebunden wurden, können sich nicht sofort authentifizieren, weil Azure AD die Kennworthashes in der neuen verwalteten Domäne generieren und speichern muss.
>
> Weitere Informationen finden Sie unter [Prozess der Kennworthashsynchronisierung für Azure AD DS und Azure AD Connect][password-hash-sync-process].

Zum Generieren und Speichern dieser Kennworthashes müssen für in Azure AD erstellte reine Cloudbenutzerkonten andere Schritte ausgeführt werden als für Benutzerkonten, die mit Azure AD Connect aus Ihrem lokalen Verzeichnis synchronisiert werden.

Ein reines Cloudbenutzerkonto ist ein Konto, das in Ihrem Azure AD-Verzeichnis über das Azure-Portal oder mithilfe von Azure AD PowerShell-Cmdlets erstellt wurde. Diese Benutzerkonten werden nicht von einem lokalen Verzeichnis aus synchronisiert.

In diesem Tutorial verwenden wir ein einfaches, rein cloudbasiertes Benutzerkonto. Weitere Informationen zu den zusätzlichen Schritten, die für die Verwendung von Azure AD Connect erforderlich sind, finden Sie unter [Synchronisieren von Kennworthashes für Benutzerkonten, die von Ihrem lokalen Active Directory aus in Ihrer verwalteten Domäne synchronisiert werden][on-prem-sync].

> [!TIP]
> Wenn Ihr Azure AD-Mandant über eine Kombination aus reinen Cloudbenutzern und Benutzern aus Ihrem lokalen Active Directory verfügt, müssen beide Prozeduren durchgeführt werden.

Bei reinen Cloudbenutzerkonten müssen Benutzer ihre Kennwörter ändern, bevor sie Azure AD DS verwenden können. Diese Kennwortänderung führt dazu, dass die Kennworthashes für die Kerberos- und NTLM-Authentifizierung in Azure AD generiert und gespeichert werden. Das Konto wird erst von Azure AD mit Azure AD DS synchronisiert, wenn das Kennwort geändert wurde. Markieren Sie die Kennwörter für alle Cloudbenutzer im Mandanten, die Azure AD DS verwenden müssen, als „abgelaufen“, wodurch bei der nächsten Anmeldung eine Kennwortänderung erzwungen wird. Alternativ dazu können Sie Cloudbenutzer anweisen, ihre Kennwörter manuell zu ändern. In diesem Tutorial ändern wir ein Benutzerkennwort manuell.

Bevor ein Benutzer sein Kennwort zurücksetzen kann, muss der Azure AD-Mandant für die [Self-Service-Kennwortzurücksetzung konfiguriert][configure-sspr] werden.

Wenn Sie reiner Cloudbenutzer sind, müssen Sie zum Ändern des Kennworts folgende Schritte ausführen:

1. Navigieren Sie zum Azure AD-Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Klicken Sie in der oberen rechten Ecke auf Ihren Namen, und wählen Sie dann aus dem Dropdownmenü die Option **Profil** aus.

    ![Auswählen des Profils](./media/tutorial-create-instance-advanced/select-profile.png)

1. Klicken Sie auf der Seite **Profil** auf **Kennwort ändern**.
1. Geben Sie auf der Seite **Kennwort ändern** Ihr vorhandenes (altes) Kennwort ein. Geben Sie dann ein neues Kennwort ein und bestätigen dies.
1. Klicken Sie auf **Submit** (Senden).

Nach der Kennwortänderung dauert es einige Minuten, bis das neue Kennwort in Azure AD DS verwendet werden kann und Sie sich bei Computern anmelden können, die in die verwaltete Domäne eingebunden sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Konfigurieren der Einstellungen für DNS und ein virtuelles Netzwerk für eine verwaltete Domäne
> * Erstellen einer verwalteten Domäne
> * Hinzufügen von Administratorbenutzern zur Domänenverwaltung
> * Aktivieren von Benutzerkonten für Azure AD DS und Generieren von Kennworthashes

Um diese verwaltete Domäne in Aktion zu erleben, erstellen Sie eine VM, und binden Sie diese in die Domäne ein.

> [!div class="nextstepaction"]
> [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/tutorial-enable-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[resource-forests]: concepts-resource-forest.md
[availability-zones]: ../availability-zones/az-overview.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus

<!-- EXTERNAL LINKS -->
