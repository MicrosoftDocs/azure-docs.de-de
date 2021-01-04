---
title: 'Azure AD Connect-Synchronisierung: Konfigurieren der Filterung | Microsoft-Dokumentation'
description: Erläutert das Konfigurieren der Filterung bei der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 880facf6-1192-40e9-8181-544c0759d506
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595cf2c1dbc105634d33b426c67e5123b9751e6e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996541"
---
# <a name="azure-ad-connect-sync-configure-filtering"></a>Azure AD Connect-Synchronisierung: Konfigurieren der Filterung
Per Filterung können Sie für Ihr lokales Verzeichnis steuern, welche Objekte in Azure Active Directory (Azure AD) angezeigt werden. Die Standardkonfiguration deckt alle Objekte in allen Domänen der konfigurierten Gesamtstrukturen ab. Dies ist die für den Normalfall empfohlene Konfiguration. Benutzer, die Microsoft 365-Workloads wie etwa Exchange Online und Skype for Business verwenden, profitieren von einer vollständigen globalen Adressliste, mit der sie E-Mails an andere Personen senden und diese anrufen können. In der Standardkonfiguration erhalten diese Benutzer die gleiche Funktionalität wie bei einer lokalen Implementierung von Exchange oder Lync.

In einigen Fällen ist es jedoch erforderlich, Änderungen an der Standardkonfiguration vorzunehmen. Im Folgenden finden Sie einige Beispiele:

* Sie planen, die [Azure AD-Multiverzeichnistopologie](plan-connect-topologies.md#each-object-only-once-in-an-azure-ad-tenant) zu verwenden. Sie müssen einen Filter anwenden, um zu steuern, welche Objekte mit einem bestimmten Azure AD-Verzeichnis synchronisiert werden sollen.
* Sie führen einen Pilotversuch für Azure oder Microsoft 365 durch und möchten nur eine Teilmenge der Benutzer in Azure AD verwenden. In dem kleinen Pilotprojekt müssen Sie nicht unbedingt über eine vollständige globale Adressliste verfügen, um die Funktionsweise zu demonstrieren.
* Sie haben viele Dienstkonten und andere nicht persönliche Konten, die nicht in Azure AD enthalten sein sollen.
* Aus Compliancegründen löschen Sie lokal keine Benutzerkonten. Sie deaktivieren sie nur. In Azure AD sollen aber nur aktive Konten vorhanden sein.

In diesem Artikel wird beschrieben, wie Sie die verschiedenen Filtermethoden konfigurieren.

> [!IMPORTANT]
> Microsoft unterstützt die Änderung oder den Einsatz der Azure AD Connect-Synchronisierung außerhalb dieser formal dokumentierten Aktionen nicht. Eine dieser Aktionen kann zu einem inkonsistenten oder nicht unterstützten Status der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft auch keinen technischen Support für solche Bereitstellungen leisten.

## <a name="basics-and-important-notes"></a>Grundlagen und wichtige Hinweise
In der Azure AD Connect-Synchronisierung können Sie die Filterung jederzeit aktivieren. Wenn Sie mit einer Standardkonfiguration der Verzeichnissynchronisierung beginnen und dann die Filterung konfigurieren, werden die Objekte, die herausgefiltert werden, nicht mehr mit Azure AD synchronisiert. Infolge dieser Änderung werden alle Objekte in Azure AD, die zuvor synchronisiert, aber dann gefiltert wurden, in Azure AD gelöscht.

Stellen Sie vor dem Vornehmen von Änderungen an der Filterung sicher, dass Sie die [geplante Aufgabe deaktivieren](#disable-the-scheduled-task). So werden nicht versehentlich Änderungen exportiert, deren Richtigkeit Sie nicht überprüft haben.

Da bei der Filterung viele Objekte gleichzeitig entfernt werden können, sollten Sie darauf achten, dass Ihre neuen Filter korrekt sind, bevor Sie mit dem Exportieren von Änderungen nach Azure AD beginnen. Es wird dringend empfohlen, nach dem Durchführen der Konfigurationsschritte die [Überprüfungsschritte](#apply-and-verify-changes) auszuführen, bevor Sie exportieren und Änderungen an Azure AD vornehmen.

Um das versehentliche Löschen von vielen Objekten zu verhindern, ist das Feature zum [Verhindern versehentlicher Löschungen](how-to-connect-sync-feature-prevent-accidental-deletes.md) standardmäßig aktiviert. Wenn Sie aufgrund einer Filterung viele Objekte löschen (standardmäßig 500), müssen Sie die Schritte in diesem Artikel ausführen, damit die Löschvorgänge auch für Azure AD gelten.

Wenn Sie einen älteren Build als November 2015 nutzen ([1.0.9125](reference-connect-version-history.md)), eine Änderung an der Filterkonfiguration vornehmen und die Kennworthashsynchronisierung verwenden, müssen Sie eine vollständige Synchronisierung aller Kennwörter auslösen, nachdem Sie die Konfiguration abgeschlossen haben. Informationen zu Schritten zum Auslösen einer vollständigen Kennwortsynchronisierung finden Sie unter [Auslösen einer vollständigen Synchronisierung aller Kennwörter](tshoot-connect-password-hash-synchronization.md#trigger-a-full-sync-of-all-passwords). Falls Sie Version 1.0.9125 oder höher verwenden, wird mit der normalen Aktion **Vollständige Synchronisierung** auch berechnet, ob Kennwörter synchronisiert werden sollen. Dieser zusätzliche Schritt ist nicht mehr erforderlich.

Wenn **Benutzerobjekte** in Azure AD aufgrund eines Filterungsfehlers versehentlich gelöscht wurden, können Sie die Benutzerobjekte in Azure AD neu erstellen, indem Sie Ihre Filterkonfigurationen entfernen. Anschließend können Sie Ihre Verzeichnisse erneut synchronisieren. Mit dieser Aktion werden die Benutzer aus dem Papierkorb in Azure AD wiederhergestellt. Das Löschen anderer Objekttypen kann jedoch nicht rückgängig gemacht werden. Wenn Sie beispielsweise eine Sicherheitsgruppe versehentlich löschen, die als Zugriffssteuerungsliste (ACL) für eine Ressource verwendet wurde, können die Gruppe und die zugehörigen ACLs nicht wiederhergestellt werden.

Von Azure AD Connect werden nur Objekte gelöscht, die einmal als zum Bereich gehörend betrachtet wurden. Wenn in Azure AD Objekte enthalten sind, die von einem anderen Synchronisierungsmodul erstellt wurden und nicht Teil des Bereichs sind, werden sie durch das Hinzufügen der Filterung nicht entfernt. Wenn Sie beispielsweise mit einem DirSync-Server beginnen, mit dem eine vollständige Kopie Ihres gesamten Verzeichnisses in Azure AD erstellt wurde, und einen neuen Azure AD Connect-Synchronisierungsserver parallel mit der von Beginn an aktivierten Filterung installieren, werden die von DirSync erstellten zusätzlichen Objekte von Azure AD Connect nicht entfernt.

Die Filterkonfigurationen werden beibehalten, wenn Sie eine neuere Version von Azure AD Connect installieren oder ein Upgrade darauf durchführen. Es ist immer empfehlenswert, vor dem Ausführen des ersten Synchronisierungszyklus zu verifizieren, dass die Konfiguration nach einem Upgrade auf eine neuere Version nicht versehentlich geändert wurde.

Wenn Sie über mehrere Gesamtstrukturen verfügen, müssen die in diesem Thema beschriebenen Filterkonfigurationen auf jede Gesamtstruktur angewendet werden (vorausgesetzt, für alle soll die gleiche Konfiguration gelten).

### <a name="disable-the-scheduled-task"></a>Deaktivieren von geplanten Aufgaben
Führen Sie die folgenden Schritte aus, um den integrierten Scheduler zu deaktivieren, der jeweils im Abstand von 30 Minuten einen Synchronisierungszyklus auslöst:

1. Wechseln Sie zu einer PowerShell-Eingabeaufforderung.
2. Führen Sie `Set-ADSyncScheduler -SyncCycleEnabled $False` aus, um den Scheduler zu deaktivieren.
3. Nehmen Sie die in diesem Thema beschriebenen Änderungen vor.
4. Führen Sie `Set-ADSyncScheduler -SyncCycleEnabled $True` aus, um den Scheduler wieder zu aktivieren.

**Bei Verwendung eines älteren Azure AD Connect-Builds als 1.1.105.0**  
Führen Sie die folgenden Schritte aus, um die geplante Aufgabe zu deaktivieren, mit der jeweils im Abstand von drei Stunden ein Synchronisierungszyklus ausgelöst wird:

1. Starten Sie im **Startmenü** die **Aufgabenplanung**.
2. Suchen Sie direkt unterhalb der **Aufgabenplanungsbibliothek** nach der Aufgabe namens **Azure AD-Synchronisierungsplaner**, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Deaktivieren** aus.  
   ![Aufgabenplanung](./media/how-to-connect-sync-configure-filtering/taskscheduler.png)  
3. Sie können jetzt Konfigurationsänderungen vornehmen und das Synchronisierungsmodul manuell über die Konsole **Synchronization Service Manager** ausführen.

Nachdem Sie alle Änderungen an der Filterung vorgenommen haben, sollten Sie nicht vergessen, die Aufgabe wieder zu **aktivieren**.

## <a name="filtering-options"></a>Filteroptionen
Sie können die folgenden Filterkonfigurationstypen auf das Tool für die Verzeichnissynchronisierung anwenden:

* [**Gruppenbasiert**](#group-based-filtering): Die auf einer einzelnen Gruppe basierende Filterung kann nur bei der Erstinstallation mit dem Installations-Assistenten konfiguriert werden.
* [**Domänenbasiert**](#domain-based-filtering): Bei dieser Option können Sie auswählen, welche Domänen mit Azure AD synchronisiert werden. Außerdem können Sie Domänen zur Konfiguration des Synchronisierungsmoduls hinzufügen und daraus entfernen, wenn Sie Änderungen an Ihrer lokalen Infrastruktur vornehmen, nachdem Sie die Azure AD Connect-Synchronisierung installiert haben.
* [**Organisationseinheitenbasiert**](#organizational-unitbased-filtering): Bei dieser Option können Sie auswählen, welche Organisationseinheiten mit Azure AD synchronisiert werden. Diese Option ist für alle Objekttypen in den ausgewählten Organisationseinheiten vorhanden.
* [**Attributbasiert**](#attribute-based-filtering): Bei dieser Option können Sie Objekte basierend auf den Attributwerten der Objekte filtern. Sie können auch unterschiedliche Filter für unterschiedliche Objekttypen verwenden.

Sie können gleichzeitig mehrere Filteroptionen verwenden. Beispielsweise können Sie die organisationseinheitenbasierte Filterung verwenden, um ausschließlich Objekte in einer Organisationseinheit einzuschließen. Gleichzeitig können Sie die attributbasierte Filterung verwenden, um die Objekte noch weiter zu filtern. Wenn Sie mehrere Filtermethoden verwenden, wird für die Filter zwischen den Filtern ein logisches „Und“ genutzt.

## <a name="domain-based-filtering"></a>Domänenbasierte Filterung
Dieser Abschnitt enthält die Schritte, die Sie zum Konfigurieren des Domänenfilters ausführen müssen. Wenn Sie nach der Installation von Azure AD Connect in der Gesamtstruktur Domänen hinzugefügt oder entfernt haben, müssen Sie auch die Konfiguration der Filterung aktualisieren.

Die bevorzugte Methode zum Ändern der domänenbasierten Filterung besteht darin, den Installations-Assistenten auszuführen und die [Filterung von Domänen und Organisationseinheiten](how-to-connect-install-custom.md#domain-and-ou-filtering) zu ändern. Der Installations-Assistent automatisiert alle Aufgaben, die in diesem Thema dokumentiert sind.

Sie sollten die folgenden Schritte nur ausführen, wenn Sie den Installations-Assistenten aus irgendeinem Grund nicht ausführen können.

Die Konfiguration der domänenbasierten Filterung umfasst folgende Schritte:

1. Wählen Sie die Domänen aus, die in die Synchronisierung einbezogen werden sollen.
2. Passen Sie für jede hinzugefügte und entfernte Domäne die Ausführungsprofile an.
3. [Wenden Sie die Änderungen an, und überprüfen Sie sie.](#apply-and-verify-changes)

### <a name="select-the-domains-to-be-synchronized"></a>Auswählen der zu synchronisierenden Domänen
Es gibt zwei Möglichkeiten für das Auswählen der zu synchronisierenden Domänen:
    - Verwenden des Synchronisierungsdiensts
    - Verwenden des Azure AD Connect-Assistenten.


#### <a name="select-the-domains-to-be-synchronized-using-the-synchronization-service"></a>Wählen Sie die zu synchronisierenden Domänen über den Synchronisierungsdienst aus.
Um den Filter für die Domäne festzulegen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
2. Starten Sie den **Synchronisierungsdienst** über das **Startmenü**.
3. Wählen Sie **Connectors** aus, und wählen Sie in der Liste **Connectors** den Connector mit dem Typ **Active Directory Domain Services** aus. Wählen Sie unter **Aktionen** die Option **Eigenschaften** aus.  
   ![Connectoreigenschaften](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicken Sie auf **Verzeichnispartitionen konfigurieren**.
5. Aktivieren bzw. deaktivieren Sie je nach Bedarf die Domänen in der Liste **Select directory partitions** (Verzeichnispartitionen auswählen). Achten Sie darauf, dass nur die Partitionen ausgewählt sind, die Sie synchronisieren möchten.  
   ![Screenshot: Verzeichnispartitionen im Fenster „Eigenschaften“](./media/how-to-connect-sync-configure-filtering/connectorpartitions.png)  
   Wenn Sie Ihre lokale Active Directory-Infrastruktur geändert und der Gesamtstruktur Domänen hinzugefügt oder daraus entfernt haben, können Sie auf die Schaltfläche **Aktualisieren** klicken, um eine aktualisierte Liste zu erhalten. Beim Aktualisieren werden Sie zur Eingabe von Anmeldeinformationen aufgefordert. Geben Sie Anmeldeinformationen an, die Ihnen den Lesezugriff auf Windows Server Active Directory ermöglichen. Es muss sich nicht um den Benutzer handeln, der im Dialogfeld bereits angegeben ist.  
   ![Aktualisierung erforderlich](./media/how-to-connect-sync-configure-filtering/refreshneeded.png)  
6. Schließen Sie nach Abschluss des Vorgangs das Dialogfeld **Eigenschaften**, indem Sie auf **OK** klicken. Wenn Sie Domänen aus der Gesamtstruktur entfernt haben, werden Sie in einer Popupmeldung darauf hingewiesen, dass eine Domäne entfernt wurde und diese Konfiguration bereinigt wird.
7. Fahren Sie mit dem Anpassen der Ausführungsprofile fort.

#### <a name="select-the-domains-to-be-synchronized-using-the-azure-ad-connect-wizard"></a>Wählen Sie die zu synchronisierenden Domänen mit dem Azure AD Connect-Assistenten aus.
Um den Filter für die Domäne festzulegen, führen Sie die folgenden Schritte aus:

1.  Starten Sie den Azure AD Connect-Assistenten.
2.  Klicken Sie auf **Konfigurieren**.
3.  Wählen Sie **Synchronisierungsoptionen anpassen**, und klicken Sie auf **Weiter**.
4.  Geben Sie Ihre Azure AD-Anmeldeinformationen ein.
5.  Klicken Sie auf dem Bildschirm **Verbundene Verzeichnisse**  auf **Weiter**.
6.  Klicken Sie auf der Seite **Filtern von Domänen und Organisationseinheiten** auf **Aktualisieren**.  Neue Domänen werden nun angezeigt, und gelöschte Domänen werden ausgeblendet.
   ![Partitionen](./media/how-to-connect-sync-configure-filtering/update2.png)  

### <a name="update-the-run-profiles"></a>Aktualisieren von Ausführungsprofilen
Wenn Sie den Domänenfilter aktualisiert haben, müssen Sie auch die Ausführungsprofile aktualisieren.

1. Stellen Sie in der Liste **Connectors** sicher, dass der Connector ausgewählt ist, den Sie im vorherigen Schritt geändert haben. Wählen Sie unter **Aktionen** die Option **Configure Run Profiles** (Ausführungsprofile konfigurieren).  
   ![Connector-Ausführungsprofile 1](./media/how-to-connect-sync-configure-filtering/connectorrunprofiles1.png)  
2. Suchen und identifizieren Sie die folgenden Profile:
    * Vollständiger Import
    * Vollständige Synchronisierung
    * Deltaimport
    * Deltasynchronisierung
    * Exportieren
3. Passen Sie für jedes Profil die **hinzugefügten** und **entfernten** Domänen an.
    1. Führen Sie für jedes der fünf Profile für alle **hinzugefügten** Domänen jeweils die folgenden Schritte aus:
        1. Wählen Sie das Ausführungsprofil aus, und klicken Sie auf **Neuer Schritt**.
        2. Wählen Sie auf der Seite **Configure Step** (Schritt konfigurieren) im Dropdownmenü **Type** (Typ) den Schritttyp mit dem gleichen Namen wie das Profil aus, das Sie konfigurieren. Klicken Sie dann auf **Weiter**.  
        ![Connector-Ausführungsprofile 2](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep1.png)  
        3. Wählen Sie auf der Seite **Connector Configuration** (Connectorkonfiguration) im Dropdownmenü **Partition** den Namen der Domäne aus, die Sie dem Domänenfilter hinzugefügt haben.  
        ![Connector-Ausführungsprofile 3](./media/how-to-connect-sync-configure-filtering/runprofilesnewstep2.png)  
        4. Um das Dialogfeld **Configure Run Profile** zu schließen, klicken Sie auf **Finish** (Fertig stellen).
    2. Führen Sie für jedes der fünf Profile für alle **entfernten** Domänen jeweils die folgenden Schritte aus:
        1. Wählen Sie das Ausführungsprofil aus.
        2. Wenn der **Wert** des Attributs **Partition** eine GUID ist, wählen Sie den Ausführungsschritt aus und klicken auf **Delete Step** (Schritt löschen).  
        ![Connector-Ausführungsprofile 4](./media/how-to-connect-sync-configure-filtering/runprofilesdeletestep.png)  
    3. Überprüfen Sie Ihre Änderungen. Jede Domäne, die Sie synchronisieren möchten, wird als Schritt in jedem Ausführungsprofil aufgelistet.
4. Um das Dialogfeld **Configure Run Profiles** zu schließen, klicken Sie auf **OK**.
5.  Um die Konfiguration abzuschließen, müssen Sie einen **vollständigen Import** und eine **Deltasynchronisierung** durchführen. Fahren Sie mit dem Abschnitt [Anwenden und Überprüfen von Änderungen](#apply-and-verify-changes) fort.

## <a name="organizational-unitbased-filtering"></a>Filterung basierend auf Organisationseinheiten
Die bevorzugte Methode zum Ändern der organisationseinheitenbasierten Filterung besteht darin, den Installations-Assistenten auszuführen und die [Filterung von Domänen und Organisationseinheiten](how-to-connect-install-custom.md#domain-and-ou-filtering) zu ändern. Der Installations-Assistent automatisiert alle Aufgaben, die in diesem Thema dokumentiert sind.

Sie sollten die folgenden Schritte nur ausführen, wenn Sie den Installations-Assistenten aus irgendeinem Grund nicht ausführen können.

Führen Sie zum Konfigurieren der auf Organisationseinheiten basierenden Filterung die folgenden Schritte aus:

1. Melden Sie sich bei dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
2. Starten Sie den **Synchronisierungsdienst** über das **Startmenü**.
3. Wählen Sie **Connectors** aus, und wählen Sie in der Liste **Connectors** den Connector mit dem Typ **Active Directory Domain Services** aus. Wählen Sie unter **Aktionen** die Option **Eigenschaften** aus.  
   ![Connectoreigenschaften](./media/how-to-connect-sync-configure-filtering/connectorproperties.png)  
4. Klicken Sie auf **Configure Directory Partitions** (Verzeichnispartitionen konfigurieren), wählen Sie die Domäne aus, die Sie konfigurieren möchten, und klicken Sie dann auf **Containers** (Container).
5. Geben Sie bei Aufforderung Anmeldeinformationen an, die Ihnen den Lesezugriff auf Ihr lokales Active Directory-Verzeichnis ermöglichen. Es muss sich nicht um den Benutzer handeln, der im Dialogfeld bereits angegeben ist.
6. Deaktivieren Sie im Dialogfeld **Select Containers** (Container auswählen) die Organisationseinheiten, die Sie nicht mit dem Cloudverzeichnis synchronisieren möchten, und klicken Sie auf **OK**.  
   ![OEs im Dialogfeld „Container auswählen“](./media/how-to-connect-sync-configure-filtering/ou.png)  
   * Der Container **Computer** sollte ausgewählt sein, damit die Synchronisierung Ihrer Windows 10-Computer mit Azure AD erfolgreich ist. Falls sich die einer Domäne angehörenden Computer in anderen Organisationseinheiten befinden, sollten Sie sicherstellen, dass sie ausgewählt sind.
   * Der Container **ForeignSecurityPrincipals** sollte ausgewählt sein, wenn Sie über mehrere Gesamtstrukturen mit Vertrauensstellungen verfügen. Dieser Container ermöglicht die Auflösung der gesamtstrukturübergreifenden Sicherheitsgruppenmitgliedschaft.
   * Die Organisationseinheit **RegisteredDevices** sollte ausgewählt sein, wenn Sie das Feature für das Geräterückschreiben aktiviert haben. Falls Sie ein anderes Feature für das Geräterückschreiben verwenden, z. B. das Gruppenrückschreiben, sollten Sie sicherstellen, dass diese Speicherorte ausgewählt sind.
   * Wählen Sie eine beliebige andere Organisationseinheit aus, in der Benutzer, iNetOrgPersons, Gruppen, Kontakte und Computer enthalten sind. In der Abbildung befinden sich all diese Organisationseinheiten in der Organisationseinheit „ManagedObjects“.
   * Wenn Sie die gruppenbasierte Filterung verwenden, muss die Organisationseinheit, zu der die Gruppe gehört, enthalten sein.
   * Hinweis: Sie können konfigurieren, ob neue Organisationseinheiten, die nach Abschluss der Filterkonfiguration hinzugefügt wurden, synchronisiert werden sollen. Weitere Details finden Sie im nächsten Abschnitt.
7. Schließen Sie nach Abschluss des Vorgangs das Dialogfeld **Eigenschaften**, indem Sie auf **OK** klicken.
8. Um die Konfiguration abzuschließen, müssen Sie einen **vollständigen Import** und eine **Deltasynchronisierung** durchführen. Fahren Sie mit dem Abschnitt [Anwenden und Überprüfen von Änderungen](#apply-and-verify-changes) fort.

### <a name="synchronize-new-ous"></a>Synchronisieren neuer Organisationseinheiten
Für neue Organisationseinheiten, die nach dem Konfigurieren der Filterung erstellt werden, erfolgt standardmäßig eine Synchronisierung. Dieser Zustand wird durch ein aktiviertes Kontrollkästchen angegeben. Sie können auch die Auswahl einiger untergeordneter Organisationseinheiten wieder aufheben. Klicken Sie dazu auf das Feld, bis es weiß mit einem blauen Häkchen (Standardzustand) dargestellt wird. Deaktivieren Sie danach alle untergeordneten Organisationseinheiten, die Sie nicht synchronisieren möchten.

Wenn alle untergeordneten Organisationseinheiten synchronisiert sind, ist das Feld weiß und enthält ein blaues Häkchen.  
![Organisationseinheit mit allen Feldern aktiviert](./media/how-to-connect-sync-configure-filtering/ousyncnewall.png)

Wenn die Auswahl einiger untergeordneter Organisationseinheiten aufgehoben wurde, wird das Feld grau mit einem weißen Häkchen dargestellt.  
![Organisationseinheit mit einigen deaktivierten untergeordneten Organisationseinheiten](./media/how-to-connect-sync-configure-filtering/ousyncnew.png)

Bei dieser Konfiguration wird eine neue Organisationseinheit, die unter ManagedObjects erstellt wurde, synchronisiert.

Der Installations-Assistent für Azure AD Connect erstellt immer diese Konfiguration.

### <a name="dont-synchronize-new-ous"></a>Aufheben der Synchronisierung neuer Organisationseinheiten
Sie können konfigurieren, dass das Synchronisierungsmodul neue Organisationseinheiten nach Abschluss der Filterkonfiguration nicht synchronisiert. Dieser Zustand wird auf der Benutzeroberfläche mit einem einfarbig grauen Feld ohne Häkchen dargestellt. Klicken Sie für diese Einstellung auf das Feld, bis es weiß ohne ein Häkchen dargestellt wird. Wählen Sie dann die untergeordneten Organisationseinheiten aus, die Sie synchronisieren möchten.

![Organisationseinheit mit deaktiviertem Stamm](./media/how-to-connect-sync-configure-filtering/oudonotsyncnew.png)

Bei dieser Konfiguration wird eine neue Organisationseinheit, die unter ManagedObjects erstellt wurde, nicht synchronisiert.

## <a name="attribute-based-filtering"></a>Attributbasierte Filterung
Stellen Sie sicher, dass Sie den Build vom November 2015 ([1.0.9125](reference-connect-version-history.md)) oder höher verwenden, damit diese Schritte funktionieren.

> [!IMPORTANT]
>Microsoft empfiehlt, die von **Azure AD Connect** erstellten Standardregeln nicht zu ändern. Falls Sie die Regel ändern möchten, können Sie sie klonen und dann die ursprüngliche Regel deaktivieren. Nehmen Sie die gewünschten Änderungen an der geklonten Regel vor. Beachten Sie, dass Sie dadurch (das Deaktivieren der ursprünglichen Regel) keine der Fehlerbehebungen oder Features erhalten, die durch diese Regel aktiviert werden.

Die attributbasierte Filterung ist die flexibelste Möglichkeit zum Filtern von Objekten. Sie können die hohe Leistungsfähigkeit der [deklarativen Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning.md) nutzen, um nahezu jeden Aspekt der Synchronisierung von Objekten mit Azure AD zu steuern.

Die Filterung kann sowohl in [eingehender](#inbound-filtering) Richtung von Active Directory zur Metaverse als auch in [ausgehender](#outbound-filtering) Richtung von der Metaverse zu Azure AD angewendet werden. Es wird empfohlen, die eingehende Filterung anzuwenden, da dies am einfachsten zu verwalten ist. Die ausgehende Filterung sollte nur verwendet werden, wenn Objekte aus mehreren Gesamtstrukturen verknüpft werden müssen, bevor die Auswertung stattfinden kann.

### <a name="inbound-filtering"></a>Eingehende Filterung
Bei der eingehenden Filterung wird die Standardkonfiguration genutzt, bei der für an Azure AD gesendete Objekte das Metaverse-Attribut „cloudFiltered“ nicht auf einen Wert festgelegt sein darf, damit die Synchronisierung erfolgen kann. Wenn der Wert dieses Attributs auf **TRUE** festgelegt ist, wird das Objekt nicht synchronisiert. Es sollte nicht standardmäßig auf **FALSE** festgelegt sein. Um sicherzustellen, dass über andere Regeln ein Wert beigetragen werden kann, sollte dieses Attribut nur über die Werte **TRUE** oder **NULL** (nicht vorhanden) verfügen.

Bei der eingehenden Filterung nutzen Sie die Leistungsfähigkeit des **Bereichs**, um zu ermitteln, welche Objekte synchronisiert werden sollen. Hierbei nehmen Sie die Anpassungen vor, um die Anforderungen Ihres Unternehmens zu erfüllen. Das Bereichsmodul verfügt über die Elemente **group** (Gruppe) und **clause** (Klausel), um zu bestimmen, wann eine Synchronisierungsregel zum Bereich gehören soll. Eine Gruppe enthält eine oder mehrere Klauseln. Ein logisches „Und“ wird zwischen mehreren Klauseln und ein logisches „Oder“ zwischen mehreren Gruppen verwendet.

Beispiel:  
![Screenshot: Beispiel für das Hinzufügen von Bereichsfiltern](./media/how-to-connect-sync-configure-filtering/scope.png)  
Lesen Sie dieses Beispiel wie folgt: **(department = IT) OR (department = Sales AND c = US)** .

In den folgenden Beispielen und Schritten verwenden Sie das Benutzerobjekt als Beispiel, aber Sie können es für alle Objekttypen nutzen.

In den folgenden Beispielen beginnen die Rangfolgenwerte bei 50. Dies kann eine beliebige nicht verwendete Zahl sein, die jedoch kleiner als 100 sein sollte.

#### <a name="negative-filtering-do-not-sync-these"></a>Negative Filterung („keine Synchronisierung“)
Im folgenden Beispiel werden alle Benutzer herausgefiltert (nicht synchronisiert), bei denen **extensionAttribute15** den Wert **NoSync** hat.

1. Melden Sie sich bei dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
2. Starten Sie den **Synchronisierungsregel-Editor** über das **Startmenü**.
3. Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
4. Geben Sie der Regel einen aussagekräftigen Namen, z.B. *In from AD – User DoNotSyncFilter*. Wählen Sie die richtige Gesamtstruktur und anschließend **Benutzer** für **CS object type** (CS-Objekttyp) und **Person** für **MV object type** (MV-Objekttyp) aus. Wählen Sie als **Verknüpfungstyp** die Option **Join** aus. Geben Sie unter **Rangfolge** einen Wert ein, der zurzeit noch von keiner anderen Synchronisierungsregel verwendet wird (z.B. 50), und klicken Sie auf **Weiter**.  
   ![Eingehend 1 Beschreibung](./media/how-to-connect-sync-configure-filtering/inbound1.png)  
5. Klicken Sie in **Scoping filter** (Bereichsfilter) auf **Gruppe hinzufügen** und dann auf **Klausel hinzufügen**. Wählen Sie in **Attribut** die Option **ExtensionAttribute15** aus. Stellen Sie sicher, dass der **Operator** auf **EQUAL** festgelegt ist, und geben Sie dann den Wert **NoSync** in das Feld **Wert** ein. Klicken Sie auf **Weiter**.  
   ![Eingehend 2 Bereich](./media/how-to-connect-sync-configure-filtering/inbound2.png)  
6. Lassen Sie **Join rules** (Joinregeln) leer, und klicken Sie dann auf **Next**.
7. Klicken Sie auf **Transformation hinzufügen**, und wählen Sie **Konstante** als **FlowType** und **cloudFiltered** als **Zielattribut** aus. Geben Sie **True** im Textfeld **Quelle** ein. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.  
   ![Eingehend 3 Transformation](./media/how-to-connect-sync-configure-filtering/inbound3.png)
8. Um die Konfiguration abzuschließen, müssen Sie eine **vollständige Synchronisierung** durchführen. Fahren Sie mit dem Abschnitt [Anwenden und Überprüfen von Änderungen](#apply-and-verify-changes) fort.

#### <a name="positive-filtering-only-sync-these"></a>Positive Filterung („ausschließliche Synchronisierung“)
Das Ausdrücken der positiven Filterung kann mit mehr Aufwand verbunden sein. Sie müssen hierbei nämlich auch Objekte berücksichtigen, bei denen die Synchronisierung nicht offensichtlich ist, z.B. Konferenzräume. Sie überschreiben auch den Standardfilter in der vordefinierten Regel **Ein von AD – Benutzerverknüpfung**. Achten Sie beim Erstellen eines benutzerdefinierten Filters darauf, keine wichtigen Systemobjekte, Replikationskonfliktobjekte, speziellen Postfächer und Dienstkonten für Azure AD Connect einzuschließen.

Die positive Filterung erfordert zwei Synchronisierungsregeln. Sie benötigen eine Regel (oder mehrere) mit dem richtigen Bereich der zu synchronisierenden Objekte. Darüber hinaus benötigen Sie eine zweite Catchall-Synchronisierungsregel, die alle Objekte herausfiltert, die noch nicht als ein zu synchronisierendes Objekt identifiziert wurden.

Im folgenden Beispiel werden nur Benutzerobjekte synchronisiert, bei denen das department-Attribut den Wert **Sales** hat.

1. Melden Sie sich bei dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
2. Starten Sie den **Synchronisierungsregel-Editor** über das **Startmenü**.
3. Stellen Sie sicher, dass **Eingehend** ausgewählt ist, und klicken Sie auf **Neue Regel hinzufügen**.
4. Geben Sie der Regel einen aussagekräftigen Namen, z.B. *In from AD – User Sales sync*. Wählen Sie die richtige Gesamtstruktur und anschließend **Benutzer** für **CS object type** (CS-Objekttyp) und **Person** für **MV object type** (MV-Objekttyp) aus. Wählen Sie als **Verknüpfungstyp** die Option **Join** aus. Geben Sie unter **Rangfolge** einen Wert ein, der zurzeit noch von keiner anderen Synchronisierungsregel verwendet wird (z.B. 51), und klicken Sie auf **Weiter**.  
   ![Eingehend 4 Beschreibung](./media/how-to-connect-sync-configure-filtering/inbound4.png)  
5. Klicken Sie in **Scoping filter** (Bereichsfilter) auf **Gruppe hinzufügen** und dann auf **Klausel hinzufügen**. Wählen Sie in **Attribut** den Wert **department** aus. Stellen Sie sicher, dass der Operator auf **EQUAL** festgelegt ist, und geben Sie dann den Wert **Sales** in das Feld **Wert** ein. Klicken Sie auf **Weiter**.  
   ![Eingehend 5 Bereich](./media/how-to-connect-sync-configure-filtering/inbound5.png)  
6. Lassen Sie **Join rules** (Joinregeln) leer, und klicken Sie dann auf **Next**.
7. Klicken Sie auf **Transformation hinzufügen**, und wählen Sie **Konstante** als **FlowType** und **cloudFiltered** als **Zielattribut** aus. Geben Sie **False** im Feld **Quelle** ein. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.  
   ![Eingehend 6 Transformation](./media/how-to-connect-sync-configure-filtering/inbound6.png)  
   Dies ist ein Sonderfall, in dem cloudFiltered explizit auf **FALSE** festgelegt wird.
8. Wir müssen jetzt die Synchronisierungsregel „catch-all“ erstellen, die alles abdeckt. Geben Sie der Regel einen aussagekräftigen Namen, z.B. *In from AD – User Catch-all filter*. Wählen Sie die richtige Gesamtstruktur und anschließend **Benutzer** für **CS object type** (CS-Objekttyp) und **Person** für **MV object type** (MV-Objekttyp) aus. Wählen Sie als **Verknüpfungstyp** die Option **Join** aus. Geben Sie unter **Rangfolge** einen Wert ein, der zurzeit noch von keiner anderen Synchronisierungsregel verwendet wird (z.B. 99). Sie haben einen Rangfolgewert ausgewählt, der höher (niedrigere Rangfolge) als der für die vorherige Synchronisierungsregel ist. Sie haben aber auch Platz gelassen, sodass Sie später noch weitere Filterregeln für die Synchronisierung hinzufügen können, wenn Sie zusätzliche Abteilungen synchronisieren möchten. Klicken Sie auf **Weiter**.  
   ![Eingehend 7 Beschreibung](./media/how-to-connect-sync-configure-filtering/inbound7.png)  
9. Lassen Sie **Scoping filter** leer, und klicken Sie auf **Next**. Ein leerer Filter gibt an, dass die Regel nicht auf alle Objekte angewendet wird.
10. Lassen Sie **Join rules** (Joinregeln) leer, und klicken Sie dann auf **Next**.
11. Klicken Sie auf **Transformation hinzufügen**, und wählen Sie **Konstante** als **FlowType** und **cloudFiltered** als **Zielattribut** aus. Geben Sie **True** im Feld **Quelle** ein. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.  
    ![Eingehend 3 Transformation](./media/how-to-connect-sync-configure-filtering/inbound3.png)  
12. Um die Konfiguration abzuschließen, müssen Sie eine **vollständige Synchronisierung** durchführen. Fahren Sie mit dem Abschnitt [Anwenden und Überprüfen von Änderungen](#apply-and-verify-changes) fort.

Bei Bedarf können Sie weitere Regeln des ersten Typs erstellen, bei denen Sie mehr Objekte in die Synchronisierung einbeziehen.

### <a name="outbound-filtering"></a>Ausgehende Filterung
In einigen Fällen ist es erforderlich, die Filterung erst auszuführen, nachdem die Objekte der Metaverse hinzugefügt wurden. Es kann beispielsweise erforderlich sein, das E-Mail-Attribut aus der Ressourcengesamtstruktur und das Attribut „userPrincipalName“ aus der Kontogesamtstruktur zu untersuchen, um zu ermitteln, ob ein Objekt synchronisiert werden soll. In diesen Fällen wird die Filterung für die ausgehende Regel erstellt.

In diesem Beispiel wird die Filterung so geändert, dass nur Benutzer synchronisiert werden, deren Attribute „mail“ und „userPrincipalName“ auf @contoso.com enden:

1. Melden Sie sich bei dem Server, auf dem die Azure AD Connect-Synchronisierung ausgeführt wird, mit einem Konto an, das Mitglied der Sicherheitsgruppe **ADSyncAdmins** ist.
2. Starten Sie den **Synchronisierungsregel-Editor** über das **Startmenü**.
3. Klicken Sie unter **Rules Type** (Regeltyp) auf **Outbound** (Ausgehend).
4. Suchen Sie abhängig von der verwendeten Connect-Version entweder die Regel **Out to Azure AD – User Join** (Ausgehend zu Azure AD – Beitritt Benutzer) oder die Regel **Out to Azure AD – User Join SOAInAD** (Ausgehend zu Azure AD – Beitritt Benutzer SOAInAD), und klicken Sie auf **Bearbeiten**.
5. Wählen Sie im Popupfenster die Antwort **Ja** , um eine Kopie der Regel zu erstellen.
6. Ändern Sie auf der Seite **Beschreibung** die **Rangfolge** in einen nicht verwendeten Wert, z.B. 50.
7. Klicken Sie im linken Navigationsbereich auf **Scoping filter** (Bereichsfilter) und dann auf **Klausel hinzufügen**. Wählen Sie in **Attribut** den Wert **mail** aus. Wählen Sie in **Operator** die Option **ENDSWITH** aus. Geben Sie in **Wert** die Zeichenfolge **\@contoso.com** ein, und klicken Sie dann auf **Klausel hinzufügen**. Wählen Sie in **Attribut** die Option **userPrincipalName** aus. Wählen Sie in **Operator** die Option **ENDSWITH** aus. Geben Sie in **Wert** die Zeichenfolge **\@contoso.com** ein.
8. Klicken Sie auf **Speichern**.
9. Um die Konfiguration abzuschließen, müssen Sie eine **vollständige Synchronisierung** durchführen. Fahren Sie mit dem Abschnitt [Anwenden und Überprüfen von Änderungen](#apply-and-verify-changes) fort.

## <a name="apply-and-verify-changes"></a>Anwenden und Überprüfen von Änderungen
Nachdem Sie Änderungen an der Konfiguration vorgenommen haben, müssen diese Änderungen auf die Objekte angewendet werden, die bereits im System vorhanden sind. Es kann auch sein, dass derzeit nicht im Synchronisierungsmodul enthaltene Objekte verarbeitet werden sollen und das Synchronisierungsmodul das Quellsystem erneut auslesen muss, um den Inhalt zu verifizieren.

Wenn Sie die Konfiguration per Filterung nach **Domäne** oder **Organisationseinheit** geändert haben, müssen Sie einen **vollständigen Import** gefolgt von einer **Deltasynchronisierung** durchführen.

Falls Sie die Konfiguration per Filterung nach dem **Attribut** geändert haben, müssen Sie die **vollständige Synchronisierung** durchführen.

Führen Sie die folgenden Schritte aus:

1. Starten Sie den **Synchronisierungsdienst** über das **Startmenü**.
2. Wählen Sie **Connectors** aus. Wählen Sie in der Liste **Connectors** den Connector aus, für den Sie vorher eine Konfigurationsänderung vorgenommen haben. Wählen Sie unter **Aktionen** die Option **Ausführen** aus.  
   ![Connectorausführung](./media/how-to-connect-sync-configure-filtering/connectorrun.png)  
3. Wählen Sie unter **Ausführungsprofile** den im vorherigen Abschnitt erwähnten Vorgang aus. Wenn Sie zwei Aktionen ausführen müssen, führen Sie die zweite nach Abschluss der ersten Aktivität aus. (Die Spalte **Status** zeigt für den ausgewählten Connector **Im Leerlauf** an.)

Nach der Synchronisierung werden alle Änderungen für den Export bereitgestellt. Bevor Sie die Änderungen in Azure AD tatsächlich vornehmen, sollten Sie sicherstellen, dass alle Änderungen richtig sind.

1. Starten Sie eine Eingabeaufforderung, und wechseln Sie zu `%ProgramFiles%\Microsoft Azure AD Sync\bin`.
2. Führen Sie `csexport "Name of Connector" %temp%\export.xml /f:x` aus.  
   Den Namen des Connectors finden Sie im Synchronisierungsdienst. Für Azure AD sieht der Name in etwa wie folgt aus: contoso.com – Azure AD.
3. Führen Sie `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` aus.
4. Sie verfügen jetzt im Ordner „%temp%“ über eine Datei namens „export.csv“, die in Microsoft Excel untersucht werden kann. Diese Datei enthält alle Änderungen, die exportiert werden sollen.
5. Nehmen Sie erforderliche Änderungen an den Daten oder der Konfiguration vor, und führen Sie die oben genannten Schritte (Importieren, Synchronisieren und Überprüfen) erneut aus, bis Sie die Änderungen erhalten, die Sie exportieren möchten.

Wenn Sie zufrieden sind, können Sie die Änderungen nach Azure AD exportieren.

1. Wählen Sie **Connectors** aus. Wählen Sie in der Liste **Connectors** den Azure AD-Connector aus. Wählen Sie unter **Aktionen** die Option **Ausführen** aus.
2. Wählen Sie unter **Ausführungsprofile** die Option **Exportieren** aus.
3. Falls im Rahmen Ihrer Konfigurationsänderungen viele Objekte geändert werden, wird für den Export ein Fehler angezeigt, wenn die Zahl die konfigurierte Schwelle (standardmäßig 500) übersteigt. In diesem Fall müssen Sie das Feature [Verhindern von versehentlichen Löschungen](how-to-connect-sync-feature-prevent-accidental-deletes.md) vorübergehend deaktivieren.

Jetzt ist es an der Zeit, den Scheduler wieder zu aktivieren.

1. Starten Sie im **Startmenü** die **Aufgabenplanung**.
2. Suchen Sie direkt unterhalb der **Aufgabenplanungsbibliothek** nach der Aufgabe mit dem Namen **Azure AD-Synchronisierungsplaner**, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Aktivieren** aus.

## <a name="group-based-filtering"></a>Gruppenbasierte Filterung
Die gruppenbasierte Filterung kann bei der erstmaligen Installation von Azure AD Connect als [benutzerdefinierte Installation](how-to-connect-install-custom.md#sync-filtering-based-on-groups) konfiguriert werden. Die Option ist für Pilotbereitstellungen gedacht, bei denen nur ein kleiner Satz von Objekten synchronisiert werden soll. Wenn Sie die gruppenbasierte Filterung deaktiviert haben, können Sie sie nicht mehr aktivieren. Die Verwendung der gruppenbasierten Filterung in einer benutzerdefinierten Konfiguration wird *nicht unterstützt*. Die Konfiguration dieses Features wird nur mit dem Installations-Assistenten unterstützt. Wenn Sie Ihr Pilotprojekt abgeschlossen haben, sollten Sie eine der anderen Filteroptionen in diesem Thema verwenden. Wenn Sie die organisationseinheitenbasierte Filterung zusammen mit der gruppenbasierten Filterung verwenden, müssen die Organisationseinheiten mit den Gruppen- und Mitgliedsobjekten eingeschlossen werden.

Beim Synchronisieren mehrerer AD-Gesamtstrukturen können Sie die gruppenbasierte Filterung konfigurieren, indem Sie für jeden AD-Connector eine andere Gruppe angeben. Wenn Sie einen Benutzer in einer AD-Gesamtstruktur synchronisieren möchten und für diesen Benutzer mindestens ein entsprechendes Objekt in anderen AD-Gesamtstrukturen vorhanden ist, müssen Sie sicherstellen, dass das Benutzerobjekt und alle entsprechenden Objekte sich innerhalb eines Bereichs für die gruppenbasierte Filterung befinden. Beispiele:

* Sie verfügen über einen Benutzer in einer Gesamtstruktur, für den ein entsprechendes FSP-Objekt (Foreign Security Principal, fremder Sicherheitsprinzipal) in einer anderen Gesamtstruktur vorhanden ist. Beide Objekte müssen sich innerhalb eines Bereichs für die gruppenbasierte Filterung befinden. Andernfalls wird der Benutzer nicht mit Azure AD synchronisiert.

* Sie verfügen über einen Benutzer in einer Gesamtstruktur, für den ein entsprechendes Ressourcenkonto (z.B. ein verknüpftes Postfach) in einer anderen Gesamtstruktur vorhanden ist. Darüber hinaus haben Sie Azure AD Connect so konfiguriert, dass der Benutzer mit dem Ressourcenkonto verknüpft ist. Beide Objekte müssen sich innerhalb eines Bereichs für die gruppenbasierte Filterung befinden. Andernfalls wird der Benutzer nicht mit Azure AD synchronisiert.

* Sie verfügen über einen Benutzer in einer Gesamtstruktur, für den ein entsprechender E-Mail-Kontakt in einer anderen Gesamtstruktur vorhanden ist. Darüber hinaus haben Sie Azure AD Connect so konfiguriert, dass der Benutzer mit dem E-Mail-Kontakt verknüpft ist. Beide Objekte müssen sich innerhalb eines Bereichs für die gruppenbasierte Filterung befinden. Andernfalls wird der Benutzer nicht mit Azure AD synchronisiert.


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md).
- Weitere Informationen zum [Integrieren lokaler Identitäten in Azure AD](whatis-hybrid-identity.md).
