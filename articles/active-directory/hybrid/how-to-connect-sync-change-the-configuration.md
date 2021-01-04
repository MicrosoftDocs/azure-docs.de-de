---
title: 'Azure AD Connect-Synchronisierung: Ändern der Standardkonfiguration'
description: Erläutert Schritt für Schritt, wie Sie eine Änderung an der Konfiguration in der Azure AD Connect-Synchronisierung vornehmen.
services: active-directory
author: billmath
manager: daveba
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2044653673da10de59d5ff125da44ac1f89e22f9
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861849"
---
# <a name="azure-ad-connect-sync-make-a-change-to-the-default-configuration"></a>Azure AD Connect-Synchronisierung: Ändern der Standardkonfiguration
In diesem Artikel wird Schritt für Schritt erläutert, wie Sie Änderungen an der Standardkonfiguration in der Azure AD Connect-Synchronisierung (Azure Active Directory) vornehmen. Es enthält Schrittanleitungen für einige allgemeinen Szenarien. Mit diesem Wissen sollten Sie in der Lage sein, basierend auf Ihren eigenen Geschäftsregeln einfache Änderungen an Ihrer eigenen Konfiguration vorzunehmen.

> [!WARNING]
> Wenn Sie Änderungen an den vorkonfigurierten Standardsynchronisierungsregeln vornehmen, werden diese bei der nächsten Aktualisierung von Azure AD Connect überschrieben. Dies kann zu unerwarteten und wahrscheinlich unerwünschten Synchronisierungsergebnissen führen.
>
> Die vorkonfigurierten Standardsynchronisierungsregeln verfügen über einen Fingerabdruck. Wenn Sie diese Regeln ändern, stimmt der Fingerabdruck nicht mehr überein. In Zukunft treten unter Umständen Probleme auf, wenn Sie eine neue Version von Azure AD Connect anwenden möchten. Führen Sie Änderungen nur wie in diesem Artikel beschrieben durch.

## <a name="synchronization-rules-editor"></a>Synchronisierungsregel-Editor
Der Synchronisierungsregel-Editor dient zum Anzeigen und Ändern der Standardkonfiguration. Sie finden ihn im Menü **Start** in der Gruppe **Azure AD Connect**.  
![Startmenü mit dem Synchronisierungsregel-Editor](./media/how-to-connect-sync-change-the-configuration/startmenu2.png)

Wenn Sie den Editor öffnen, sehen Sie die vordefinierten Standardregeln.

![Synchronisierungsregel-Editor](./media/how-to-connect-sync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>Navigation im Editor
Mithilfe der Dropdownmenüs am oberen Rand des Editors können Sie schnell nach einer bestimmten Regel suchen. Wenn Sie z.B. die Regeln mit dem Attribut „proxyAddresses“ anzeigen möchten, ändern Sie die Einstellungen in den Dropdownmenüs wie folgt:  
![SRE-Filterung](./media/how-to-connect-sync-change-the-configuration/filtering.png)  
Drücken Sie zum Zurücksetzen des Filters und zum Laden einer neuen Konfiguration die Taste F5 auf der Tastatur.

Im oberen rechten Bereich finden Sie die Schaltfläche **Neue Regel hinzufügen**. Mit dieser Schaltfläche können Sie eigene benutzerdefinierte Regeln erstellen.

Unten im Fenster befinden sich Schaltflächen, mit denen Sie Aktionen für eine ausgewählte Synchronisierungsregel ausführen können. Die Schaltflächen **Bearbeiten** und **Löschen** sind selbsterklärend. **Exportieren** erzeugt ein PowerShell-Skript zur erneuten Erstellung der Synchronisierungsregel. Mit diesem Verfahren können Sie eine Synchronisierungsregel von einem Server zu einem anderen verschieben.

## <a name="create-your-first-custom-rule"></a>Erstellen Ihrer ersten benutzerdefinierten Regel
Änderungen an den Attributflüssen zählen zu den häufigsten Änderungen. Die Daten in Ihrem Quellverzeichnis weichen möglicherweise von denen in Azure AD ab. Stellen Sie im Beispiel in diesem Abschnitt sicher, dass der Vorname eines Benutzers immer die *richtige Groß-/Kleinschreibung* aufweist.

### <a name="disable-the-scheduler"></a>Deaktivieren des Schedulers
Der [Scheduler](how-to-connect-sync-feature-scheduler.md) wird standardmäßig alle 30 Minuten ausgeführt. Stellen Sie sicher, dass er nicht gestartet wird, während Sie Änderungen vornehmen und Probleme in Ihren neuen Regeln beheben. Um den Scheduler vorübergehend zu deaktivieren, starten Sie PowerShell, und führen Sie den Befehl `Set-ADSyncScheduler -SyncCycleEnabled $false` aus.

![Deaktivieren des Schedulers](./media/how-to-connect-sync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>Erstellen der Regel
1. Klicken Sie auf **Neue Regel hinzufügen**.
2. Geben Sie auf der Seite **Beschreibung** Folgendes ein:  
   ![Filterung für die eingehende Regel](./media/how-to-connect-sync-change-the-configuration/description2.png)  
   * **Name**: Geben Sie einen beschreibenden Namen für die Regel ein.
   * **Beschreibung**: Fügen Sie Informationen hinzu, damit andere Benutzer wissen, wozu die Regel dient.
   * **Verbundenes System:** Das System, in dem sich das Objekt befindet. Wählen Sie in diesem Fall **Active Directory Connector** aus.
   * **Verbundenes System/Metaverse-Objekttyp:** Wählen Sie **Benutzer** bzw. **Person** aus.
   * **Verknüpfungstyp:** Ändern Sie diesen Wert in **Join**.
   * **Rangfolge:** Geben Sie einen Wert an, der im System eindeutig ist. Ein niedrigerer numerischer Wert steht für eine höhere Rangfolge.
   * **Tag:** Lassen Sie dieses Feld leer. Dieses Feld sollte nur bei vordefinierten Regeln von Microsoft einen Wert enthalten.
3. Geben Sie auf der Seite **Bereichsfilter** den Wert **givenName ISNOTNULL** ein.  
   ![Bereichsfilter für die eingehende Regel](./media/how-to-connect-sync-change-the-configuration/scopingfilter.png)  
   Dieser Abschnitt wird verwendet, um zu definieren, auf welche Objekte die Regel angewendet werden soll. Wenn Sie den Abschnitt leer lassen, gilt die Regel für alle Benutzerobjekte. Damit würden jedoch auch Konferenzräume, Dienstkonten und andere nicht personenbezogene Benutzerobjekte einbezogen.
4. Lassen Sie das Feld auf der Seite **Verknüpfungsregeln** leer.
5. Ändern Sie auf der Seite **Transformationen** den **FlowType** zu **Ausdruck**. Wählen Sie als **Zielattribut** die Option **givenName** aus. Als **Quelle** geben Sie **PCase([givenName])** ein.
   ![Transformationen für die eingehende Regel](./media/how-to-connect-sync-change-the-configuration/transformations.png)  
   In der Synchronisierungsengine wird sowohl beim Funktions- als auch beim Attributnamen die Groß-/Kleinschreibung beachtet. Im Fall einer fehlerhaften Eingabe wird beim Hinzufügen der Regel eine Warnung angezeigt. Sie können speichern und fortfahren, müssen die Regel jedoch erneut öffnen und korrigieren.
6. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.

Die neue benutzerdefinierte Regel sollte zusammen mit den anderen Synchronisierungsregeln im System angezeigt werden.

### <a name="verify-the-change"></a>Überprüfen der Änderung
Nachdem Sie diese Änderung vorgenommen haben, möchten Sie sicherstellen, dass die Regel wie erwartet funktioniert und nicht zu Fehlern führt. Hierzu stehen Ihnen abhängig von der Anzahl von Objekten zwei Methoden zur Auswahl:

- Führen Sie vollständige Synchronisierung aller Objekte aus.
- Führen Sie eine Vorschau und eine vollständige Synchronisierung eines einzelnen Objekts aus.

Öffnen Sie über das **Startmenü** den **Synchronisierungsdienst**. Alle Schritte in diesem Abschnitt werden in diesem Tool ausgeführt.

**Vollständige Synchronisierung aller Objekte**  

   1. Klicken Sie im Menü „Aktionen“ auf **Connectors** aus. Suchen Sie nach dem Connector, den Sie im vorherigen Abschnitt geändert haben (in diesem Fall die Active Directory Domain Services), und wählen Sie ihn aus. 
   2. Wählen Sie unter **Aktionen** die Option **Ausführen** aus.
   3. Wählen Sie **Vollständige Synchronisierung** und danach **OK** aus.
   ![Vollständige Synchronisierung](./media/how-to-connect-sync-change-the-configuration/fullsync.png)  
   Die Objekte sind jetzt in der Metaverse aktualisiert. Überprüfen Sie Ihre Änderungen, indem Sie sich das Objekt in der Metaverse ansehen.

**Vorschau und vollständige Synchronisierung eines einzelnen Objekts**  

   1. Klicken Sie im Menü „Aktionen“ auf **Connectors** aus. Suchen Sie nach dem Connector, den Sie im vorherigen Abschnitt geändert haben (in diesem Fall die Active Directory Domain Services), und wählen Sie ihn aus.
   2. Wählen Sie **Search Connector Space**(Connectorbereich durchsuchen) aus. 
   3. Verwenden Sie den **Bereich**, um nach einem Objekt zu suchen, das Sie zum Testen der Änderung verwenden möchten. Wählen Sie das Objekt aus, und klicken Sie auf **Vorschau**. 
   4. Wählen Sie auf dem neuen Bildschirm **Commitvorschau** aus.  
   ![Commit preview](./media/how-to-connect-sync-change-the-configuration/commitpreview.png)  
   Die Änderung wird jetzt an die Metaverse übergeben.

**Anzeigen des Objekts in der Metaverse**  

1. Wählen Sie einige Beispielobjekte aus, um sicherzustellen, dass der Wert wie erwartet ist und die Regel angewendet wird. 
2. Wählen Sie oben im Fenster **Metaverse Search** (Metaversesuche) aus. Fügen Sie beliebige Filter hinzu, die Sie zur Suche nach den entsprechenden Objekten benötigen. 
3. Öffnen Sie im Suchergebnis ein Objekt. Sehen Sie sich die Attributwerte an, und überprüfen Sie in der Spalte **Synchronisierungsregeln**, ob die Regel wie erwartet angewendet wurde.  
![Metaverse search](./media/how-to-connect-sync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>Deaktivieren des Schedulers
Wenn alles wie erwartet funktioniert, können Sie den Scheduler wieder aktivieren. Führen Sie in PowerShell `Set-ADSyncScheduler -SyncCycleEnabled $true`aus.

## <a name="other-common-attribute-flow-changes"></a>Andere häufige Änderungen am Attributfluss
Im vorherigen Abschnitt wurde beschrieben, wie Sie Änderungen an einem Attributfluss vornehmen. In diesem Abschnitt werden einige weitere Beispiele vorgestellt. Die Schritte zum Erstellen der Synchronisierungsregel werden hier in gekürzter Form wiedergegeben. Die vollständigen Schritte finden Sie bei Bedarf im vorherigen Abschnitt.

### <a name="use-an-attribute-other-than-the-default"></a>Verwenden eines vom Standardwert abweichenden Attributs
In diesem Fabrikam-Szenario wird in einer Gesamtstruktur das lokale Alphabet für Vorname, Nachname und Anzeigename verwendet. Die lateinische Zeichenfolgendarstellung dieser Attribute ist in den Erweiterungsattributen zu finden. Zum Erstellen einer globalen Adressliste in Azure AD und Microsoft 365 möchte die Organisation stattdessen diese Attribute verwenden.

Mit einer Standardkonfiguration sieht ein Objekt aus der lokalen Gesamtstruktur wie folgt aus:  
![Attributfluss 1](./media/how-to-connect-sync-change-the-configuration/attributeflowjp1.png)

Führen Sie folgende Schritte aus, um eine Regel mit anderen Attributflüssen zu erstellen:

1. Starten Sie den **Synchronisierungsregel-Editor** über das Menü **Start**.
2. Lassen Sie auf der linken Seite **Eingehend** ausgewählt, und klicken Sie auf die Schaltfläche **Neue Regel hinzufügen**.
3. Benennen Sie die Regel und fügen Sie eine Beschreibung hinzu. Wählen Sie die lokale Active Directory-Instanz und die entsprechenden Objekttypen aus. Wählen Sie als **Verknüpfungstyp** die Option **Join** aus. Wählen Sie als **Rangfolge** eine Zahl, die nicht von einer anderen Regel verwendet wird. Die vordefinierten Regeln beginnen mit 100. In diesem Beispiel kann also der Wert 50 verwendet werden.
  ![Attributfluss 2](./media/how-to-connect-sync-change-the-configuration/attributeflowjp2.png)
4. Lassen Sie **Bereichsfilter** leer. (Das bedeutet, dass die Regel für alle Benutzerobjekte in der Gesamtstruktur gelten soll.)
5. Lassen Sie **Verknüpfungsregeln** leer. (Das bedeutet, dass alle Verknüpfungen von der Standardregel behandelt werden sollen.)
6. Erstellen Sie unter **Transformationen** die folgenden Attributflüsse:  
  ![Attributfluss 3](./media/how-to-connect-sync-change-the-configuration/attributeflowjp3.png)
7. Klicken Sie auf **Hinzufügen** , um die Regel zu speichern.
8. Wechseln Sie zu **Synchronization Service Manager**. Wählen Sie unter **Connectors** den Connector aus, für den Sie die Regel hinzugefügt haben. Wählen Sie **Ausführen** und danach **Vollständige Synchronisierung** aus. Bei einer vollständigen Synchronisierung werden alle Objekte anhand der aktuellen Regeln neu berechnet.

Dies ist das Ergebnis für dasselbe Objekt mit dieser benutzerdefinierten Regel:  
![Attributfluss 4](./media/how-to-connect-sync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>Länge der Attribute
Zeichenfolgenattribute sind standardmäßig indizierbar, die maximale Länge beträgt 448 Zeichen. Wenn Sie mit Zeichenfolgenattributen arbeiten, die möglicherweise mehr Zeichen enthalten, müssen Sie Folgendes in den Attributfluss einschließen:  
`attributeName` <- `Left([attributeName],448)`.

### <a name="changing-the-userprincipalsuffix"></a>Ändern von "userPrincipalSuffix"
Das userPrincipalName-Attribut in Active Directory ist den Benutzern nicht immer bekannt und eignet sich möglicherweise nicht als Anmelde-ID. Mit dem Installations-Assistenten für die Azure AD Connect-Synchronisierung können Sie ein anderes Attribut auswählen, z.B. *mail*. In einigen Fällen muss das Attribut jedoch berechnet werden.

Das Unternehmen Contoso verfügt beispielsweise über zwei Azure AD-Verzeichnisse, eins für die Produktion und eins für Testzwecke. Benutzer im Testmandanten sollen ein anderes Suffix in der Anmelde-ID verwenden:  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`.

Verketten Sie in diesem Ausdruck alle Zeichen links neben dem ersten @-sign (Word) mit einer festgelegten Zeichenfolge.

### <a name="convert-a-multi-value-attribute-to-single-value"></a>Konvertieren eines mehrwertigen Attributs in einen einzelnen Wert
Einige Attribute in Active Directory weisen im Schema mehrere Werte auf, obwohl sie in „Active Directory-Benutzer und -Computer“ wie einwertige Attribute aussehen. Das Attribut „description“ ist ein Beispiel hierfür:  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`.

Falls das Attribut einen Wert besitzt, wird in diesem Ausdruck das erste Element (*Item*) im Attribut genommen, die voran- und nachgestellten Leerzeichen (*Trim*) werden entfernt, und die ersten 448 Zeichen (*Left*) der Zeichenfolge werden beibehalten.

### <a name="do-not-flow-an-attribute"></a>Verhindern des „Fließens“ eines Attributs
Hintergrundinformationen zum Szenario in diesem Abschnitt finden Sie unter [Steuern des Attributflussprozesses](concept-azure-ad-connect-sync-declarative-provisioning.md#control-the-attribute-flow-process).

Es gibt zwei Möglichkeiten, wie Sie dafür sorgen, dass ein Attribut nicht „fließt“ (also nicht übertragen wird). Die erste Option besteht darin, den Installations-Assistenten zu verwenden, um [ausgewählte Attribute zu entfernen](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering). Diese Option funktioniert, wenn Sie das Attribut vorher noch nie synchronisiert haben. Falls Sie bereits mit der Synchronisierung dieses Attributs begonnen haben und es danach mit diesem Funktion entfernen, beendet die Synchronisierungsengine die Verwaltung des Attributs, und die vorhandenen Werte verbleiben in Azure AD.

Wenn Sie den Wert eines Attributs entfernen und sicherstellen möchten, dass es in Zukunft nicht mehr zum Fluss gehört, müssen Sie eine benutzerdefinierte Regel erstellen.

In diesem Fabrikam-Szenario haben wir festgestellt, dass einige der Attribute, die wir mit der Cloud synchronisieren, nicht vorhanden sein sollten. Wir möchten sicherstellen, dass diese Attribute aus Azure AD entfernt werden.  
![Ungültige Erweiterungsattribute](./media/how-to-connect-sync-change-the-configuration/badextensionattribute.png)

1. Erstellen Sie eine neue Regel für die eingehende Synchronisierung, und füllen Sie die Beschreibung auf.
  ![Beschreibungen](./media/how-to-connect-sync-change-the-configuration/syncruledescription.png)
2. Erstellen Sie Attributflüsse mit **Ausdruck** als **FlowType** und **AuthoritativeNull** als **Source**. Das Literal **AuthoritativeNull** gibt an, dass der Wert in der Metaverse auch dann leer sein sollte, wenn eine Synchronisierungsregel mit geringerer Position in der Rangfolge versucht, den Wert aufzufüllen.
  ![Transformation für Erweiterungsattribute](./media/how-to-connect-sync-change-the-configuration/syncruletransformations.png)
3. Speichern Sie die Synchronisierungsregel. Starten Sie den **Synchronisierungsdienst**, suchen Sie nach dem Connector, und wählen Sie **Ausführen** und **Vollständige Synchronisierung** aus. Dadurch werden alle Attributflüsse neu berechnet.
4. Stellen Sie sicher, dass die beabsichtigten Änderungen exportiert werden, indem Sie den Connectorbereich durchsuchen.
  ![Gestaffeltes Löschen](./media/how-to-connect-sync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>Erstellen von Regeln mit PowerShell
Der Synchronisierungsregel-Editor funktioniert gut, wenn Sie nur wenige Änderungen vornehmen möchten. Wenn viele Änderungen erforderlich sind, ist PowerShell wahrscheinlich besser geeignet. Einige erweiterte Funktionen sind nur über PowerShell verfügbar.

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>Abrufen des PowerShell-Skripts für eine Standardregel
Um das PowerShell-Skript anzuzeigen, mit dem eine Standardregel erstellt wurde, wählen Sie die Regel im Synchronisierungsregel-Editor aus, und klicken Sie auf **Exportieren**. Damit erhalten Sie das PowerShell-Skript, mit dem die Regel erstellt wurde.

### <a name="advanced-precedence"></a>Erweiterte Rangfolge
Die standardmäßigen Synchronisierungsregeln beginnen mit dem Rangfolgewert 100. Wenn Sie über viele Gesamtstrukturen verfügen und viele benutzerdefinierte Änderungen vornehmen müssen, sind 99 Synchronisierungsregeln möglicherweise nicht ausreichend.

Sie können die Synchronisierungsengine anweisen, dass vor den Standardregeln weitere Regeln eingefügt werden sollen. Führen Sie folgende Schritte aus, um dieses Verhalten festzulegen:

1. Markieren Sie im Synchronisierungsregel-Editor die erste Standardregel (**Eingehend von AD – Benutzerverknüpfung**), und wählen Sie **Exportieren** aus. Kopieren Sie den SR-ID-Wert.  
![PowerShell vor der Änderung](./media/how-to-connect-sync-change-the-configuration/powershell1.png)  
2. Erstellen Sie die neue Synchronisierungsregel. Hierzu können Sie den Synchronisierungsregel-Editor verwenden. Exportieren Sie die Regel in ein PowerShell-Skript.
3. Fügen Sie den Bezeichnerwert aus der Standardregel in die Eigenschaft **PrecedenceBefore** ein. Legen Sie die **Rangfolge** auf **0** fest. Stellen Sie sicher, dass das Bezeichnerattribut eindeutig ist und Sie keine GUID aus einer anderen Regel wiederverwenden. Stellen Sie außerdem sicher, dass die Eigenschaft **ImmutableTag** nicht festgelegt ist. Diese Eigenschaft sollte nur für eine Standardregel festgelegt sein.
4. Speichern Sie das PowerShell-Skript, und führen Sie es aus. Das Ergebnis dieses Vorgehens: Ihrer benutzerdefinierten Regel wird der Rangfolgewert 100 zugewiesen, und alle anderen Standardregeln erhalten schrittweise ansteigende Werte.  
![PowerShell nach der Änderung](./media/how-to-connect-sync-change-the-configuration/powershell2.png)  

Bei Bedarf können Sie eine Vielzahl von benutzerdefinierten Synchronisierungsregeln einrichten, die den gleichen **PrecedenceBefore**-Wert verwenden.

## <a name="enable-synchronization-of-usertype"></a>Synchronisierung des Benutzertyps aktivieren
Azure AD Connect unterstützt ab Version 1.1.524.0 die Synchronisierung des **UserType**-Attributs für **Benutzer**-Objekte. Genauer gesagt wurden folgende Änderungen eingeführt:

- Das Schema des Objekttyps **Benutzer** im Azure AD-Connector wurde um das UserType-Attribut erweitert, das vom Typ „Zeichenfolge“ und einwertig ist.
- Das Schema des Objekttyps **Person** in der Metaverse wurde um das UserType-Attribut erweitert, das vom Typ „Zeichenfolge“ und einwertig ist.

Standardmäßig ist das UserType-Attribut nicht für die Synchronisierung aktiviert, da kein entsprechendes UserType-Attribut im lokalen Active Directory vorhanden ist. Sie müssen die Synchronisierung manuell aktivieren. Beachten Sie das folgende von Azure AD erzwungene Verhalten:

- Azure AD akzeptiert nur zwei Werte für das UserType-Attribut: **Member** und **Guest**.
- Wenn das UserType-Attribut nicht für die Synchronisierung in Azure AD Connect aktiviert ist, ist das UserType-Attribut von Azure AD-Benutzern, die über die Verzeichnissynchronisierung erstellt wurden, auf **Member** festgelegt.
- Vor Version 1.5.30.0 hat Azure AD nicht das Ändern des UserType-Attributs vorhandener Azure AD-Benutzer durch Azure AD Connect erlaubt. In älteren Versionen konnte es nur während der Erstellung des Azure AD-Benutzers festgelegt und [über PowerShell geändert](/powershell/module/azuread/set-azureaduser) werden.

Vor dem Aktivieren der Synchronisierung des UserType-Attributs müssen Sie entscheiden, wie das Attribut vom lokalen Active Directory abgeleitet wird. Folgende Vorgehensweisen sind die gängigsten:

- Legen Sie ein nicht verwendetes lokales AD-Attribut (z.B. „extensionAttribute1“) als Quellattribut fest. Das festgelegte lokale AD-Attribut sollte vom Typ **Zeichenfolge** und einwertig sein und den Wert **Member** oder **Guest** enthalten. 

    Wenn Sie sich für diesen Ansatz entscheiden, müssen Sie vor dem Aktivieren der Synchronisierung des UserType-Attributs sicherstellen, dass das ausgewählte Attribut mit dem richtigen Wert für alle vorhandenen Benutzerobjekte im lokalen Active Directory, die mit Azure AD synchronisiert werden, aufgefüllt wird.

- Alternativ können Sie den Wert für das UserType-Attribut auch aus anderen Eigenschaften ableiten. Angenommen, Sie möchten alle Benutzer als **Guest** synchronisieren, wenn ihr lokales AD-Attribut „UserPrincipalName“ mit dem Domänenteil <em>@partners.fabrikam123.org</em> endet. 

    Wie bereits erwähnt, erlauben ältere Versionen von Azure AD Connect das Ändern des UserType-Attributs vorhandener Azure AD-Benutzer durch Azure AD Connect nicht. Aus diesem Grund müssen Sie sicherstellen, dass die verwendete Logik mit der bereits vorhandenen Konfiguration des UserType-Attributs für alle vorhandenen Azure AD-Benutzer in Ihrem Mandanten konsistent ist.

Die Schritte zum Aktivieren der Synchronisierung des UserType-Attributs können wie folgt zusammengefasst werden:

1.  Deaktivieren Sie den Synchronisierungsplaner, und stellen Sie sicher, dass derzeit keine Synchronisierung ausgeführt wird.
2.  Fügen Sie das Quellattribut zum lokalen AD-Connectorschema hinzu.
3.  Fügen Sie das UserType-Attribut zum Azure AD-Connectorschema hinzu.
4.  Erstellen Sie eine Synchronisierungsregel für eingehende Daten zur Übertragung des Attributwerts aus dem lokalen Active Directory.
5.  Erstellen Sie eine Synchronisierungsregel für ausgehende Daten zur Übertragung des Attributwerts an Azure AD.
6.  Führen Sie einen vollständigen Synchronisierungszyklus aus.
7.  Aktivieren Sie den Synchronisierungsplaner.

>[!NOTE]
> Im restlichen Teil dieses Abschnitts werden diese Schritte erläutert. Sie werden im Kontext einer Azure AD-Bereitstellung mit einer Topologie mit einer einzelnen Gesamtstruktur und ohne benutzerdefinierte Synchronisierungsregeln beschrieben. Wenn Sie über eine Topologie mit mehreren Gesamtstrukturen verfügen, benutzerdefinierte Synchronisierungsregeln konfiguriert haben oder einen Stagingserver verwenden, müssen Sie die Schritte entsprechend anpassen.

### <a name="step-1-disable-the-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Schritt 1: Deaktivieren des Synchronisierungsplaners und Sicherstellen, dass derzeit keine Synchronisierung ausgeführt wird
Um zu vermeiden, dass unbeabsichtigte Änderungen nach Azure AD exportiert werden, stellen Sie sicher, dass keine Synchronisierung ausgeführt wird, während Sie Synchronisierungsregeln aktualisieren. So deaktivieren Sie den integrierten Synchronisierungsplaner:

 1. Starten Sie eine PowerShell-Sitzung auf dem Azure AD Connect-Server.
 2. Deaktivieren Sie die geplante Synchronisierung durch Ausführung des Cmdlets `Set-ADSyncScheduler -SyncCycleEnabled $false`.
 3. Öffnen Sie den Synchronization Service Manager, indem Sie zu **Start** > **Synchronization Service** wechseln.
 4. Wechseln Sie zur Registerkarte **Vorgänge**, und vergewissern Sie sich, dass kein Vorgang mit dem Status *Wird ausgeführt* angezeigt wird.

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>Schritt 2: Hinzufügen des Quellattributs zum lokalen AD-Connectorschema
Nicht alle Azure AD-Attribute werden in den lokalen AD-Connectorbereich importiert. So fügen Sie das Quellattribut zur Liste der importierten Attribute hinzu:

 1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
 2. Klicken Sie mit der rechten Maustaste auf den lokalen AD-Connector, und wählen Sie **Eigenschaften** aus.
 3. Navigieren Sie im Popupdialogfeld zur Registerkarte **Attribute auswählen**.
 4. Stellen Sie sicher, dass das Quellattribut in der Attributliste aktiviert ist.
 5. Klicken Sie zum Speichern auf **OK**.
![Hinzufügen des Quellattributs zum lokalen AD-Connectorschema](./media/how-to-connect-sync-change-the-configuration/usertype1.png)

### <a name="step-3-add-the-usertype-attribute-to-the-azure-ad-connector-schema"></a>Schritt 3: Hinzufügen des UserType-Attributs zum Azure AD-Connectorschema
Standardmäßig wird das UserType-Attribut nicht in den Azure AD Connect-Bereich importiert. So fügen Sie das UserType-Attribut der Liste der importierten Attribute hinzu

 1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
 2. Klicken Sie mit der rechten Maustaste auf den **Azure AD-Connector**, und wählen Sie **Eigenschaften** aus.
 3. Navigieren Sie im Popupdialogfeld zur Registerkarte **Attribute auswählen**.
 4. Stellen Sie sicher, dass das UserType-Attribut in der Attributliste aktiviert ist.
 5. Klicken Sie zum Speichern auf **OK**.

![Hinzufügen des Quellattributs zum Azure AD-Connectorschema](./media/how-to-connect-sync-change-the-configuration/usertype2.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>Schritt 4: Erstellen einer Synchronisierungsregel für eingehende Daten zur Übertragung des Attributwerts aus dem lokalen Active Directory
Die Synchronisierungsregel für eingehende Daten ermöglicht die Übertragung des Attributwerts aus dem Quellattribut im lokalen Active Directory in die Metaverse:

1. Öffnen Sie den Synchronisierungsregel-Editor, indem Sie zu **Start** > **Synchronisierungsregel-Editor** wechseln.
2. Legen Sie den Suchfilter **Richtung** auf **Eingehend** fest.
3. Klicken Sie auf die Schaltfläche **Neue Regel hinzufügen**, um eine neue Regel für eingehende Daten zu erstellen.
4. Geben Sie auf der Registerkarte **Beschreibung** die folgende Konfiguration an:

    | attribute | Wert | Details |
    | --- | --- | --- |
    | Name | *Geben Sie einen Namen ein.* | Beispiel: *Eingehend von AD – UserType „Benutzer“* |
    | BESCHREIBUNG | *Geben Sie eine Beschreibung ein.* |  |
    | Verbundenes System | *Wählen Sie den lokalen AD-Connector aus.* |  |
    | Objekttyp des verbundenen Systems | **Benutzer** |  |
    | Metaverse-Objekttyp | **Person** |  |
    | Verknüpfungstyp | **Join** |  |
    | Rangfolge | *Wählen Sie eine Zahl zwischen 1 und 99 aus.* | Die Zahlen zwischen 1 und 99 sind für benutzerdefinierte Synchronisierungsregeln reserviert. Wählen Sie keinen Wert aus, der von einer anderen Synchronisierungsregel verwendet wird. |

5. Navigieren Sie zur Registerkarte **Bereichsfilter**, und fügen Sie eine **einzelne Bereichsfiltergruppe** mit folgender Klausel hinzu:

    | attribute | Operator | Wert |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | Benutzer\_ |

    Der Bereichsfilter legt fest, auf welche lokalen AD-Objekte diese Synchronisierungsregel für eingehende Daten angewendet wird. In diesem Beispiel verwenden wir denselben Bereichsfilter wie die Standardsynchronisierungsregel *Eingehend von AD – Benutzer „Common“* , die verhindert, dass die Synchronisierungsregel auf Benutzerobjekte angewendet wird, die über die Funktion zum Rückschreiben von Azure AD-Benutzern erstellt wurden. Möglicherweise müssen Sie den Bereichsfilter entsprechend Ihrer Azure AD Connect-Bereitstellung anpassen.

6. Navigieren Sie zur Registerkarte **Transformation**, und implementieren Sie die gewünschte Transformationsregel. Wenn Sie beispielsweise ein nicht verwendetes lokales AD-Attribut (z.B. „extensionAttribute1“) als Quellattribut für „UserType“ festgelegt haben, können Sie einen direkten Attributfluss implementieren:

    | Attributflusstyp | Zielattribut | `Source` | Einmal anwenden | Mergetyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | extensionAttribute1 | Deaktiviert | Aktualisieren |

    Ein weiteres Beispiel: Sie möchten den Wert für das UserType-Attribut aus anderen Eigenschaften ableiten. Angenommen, Sie möchten alle Benutzer als „Guest“ synchronisieren, wenn ihr lokales AD-Attribut „UserPrincipalName“ mit dem Domänenteil <em>@partners.fabrikam123.org</em> endet. Sie können einen Ausdruck wie den folgenden implementieren:

    | Attributflusstyp | Zielattribut | `Source` | Einmal anwenden | Mergetyp |
    | --- | --- | --- | --- | --- |
    | Ausdruck | UserType | IIF(IsPresent([userPrincipalName]),IIF(CBool(InStr(LCase([userPrincipalName]),"@partners.fabrikam123.org")=0),"Member","Guest"),Error("UserPrincipalName is not present to determine UserType")) | Deaktiviert | Aktualisieren |

7. Klicken Sie auf **Hinzufügen**, um die Regel für eingehende Daten zu erstellen.

![Erstellen einer Synchronisierungsregel für eingehende Daten](./media/how-to-connect-sync-change-the-configuration/usertype3.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>Schritt 5: Erstellen einer Synchronisierungsregel für ausgehende Daten zur Übertragung des Attributwerts an Azure AD
Die Synchronisierungsregel für ausgehende Daten ermöglicht die Übertragung des Attributwerts aus der Metaverse auf das UserType-Attribut in Azure AD:

1. Navigieren Sie zum Synchronisierungsregel-Editor.
2. Legen Sie den Suchfilter **Richtung** auf **Ausgehend** fest.
3. Klicken Sie auf die Schaltfläche **Neue Regel hinzufügen**.
4. Geben Sie auf der Registerkarte **Beschreibung** die folgende Konfiguration an:

    | attribute | Wert | Details |
    | ----- | ------ | --- |
    | Name | *Geben Sie einen Namen ein.* | Beispiel: *Ausgehend nach AAD – UserType „Benutzer“* |
    | BESCHREIBUNG | *Geben Sie eine Beschreibung ein.* ||
    | Verbundenes System | *Wählen Sie den AAD-Connector aus.* ||
    | Objekttyp des verbundenen Systems | **Benutzer** ||
    | Metaverse-Objekttyp | **Person** ||
    | Verknüpfungstyp | **Join** ||
    | Rangfolge | *Wählen Sie eine Zahl zwischen 1 und 99 aus.* | Die Zahlen zwischen 1 und 99 sind für benutzerdefinierte Synchronisierungsregeln reserviert. Wählen Sie keinen Wert aus, der von einer anderen Synchronisierungsregel verwendet wird. |

5. Navigieren Sie zur Registerkarte **Bereichsfilter**, und fügen Sie eine **einzelne Bereichsfiltergruppe** mit zwei Klauseln hinzu:

    | attribute | Operator | Wert |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Benutzer |
    | cloudMastered | NOTEQUAL | True |

    Der Bereichsfilter legt fest, auf welche Azure AD-Objekte diese Synchronisierungsregel für ausgehende Daten angewendet wird. In diesem Beispiel verwenden wir den Bereichsfilter aus der Standardsynchronisierungsregel *Ausgehend nach – Benutzeridentität*. Er verhindert, dass die Synchronisierungsregel auf Benutzerobjekte angewendet wird, die nicht über das lokale Active Directory synchronisiert werden. Möglicherweise müssen Sie den Bereichsfilter entsprechend Ihrer Azure AD Connect-Bereitstellung anpassen.

6. Navigieren Sie zur Registerkarte **Transformation**, und implementieren Sie folgende Transformationsregel:

    | Attributflusstyp | Zielattribut | `Source` | Einmal anwenden | Mergetyp |
    | --- | --- | --- | --- | --- |
    | Direkt | UserType | UserType | Deaktiviert | Aktualisieren |

7. Klicken Sie auf **Hinzufügen**, um die Regel für ausgehende Daten zu erstellen.

![Erstellen einer Synchronisierungsregel für ausgehende Daten](./media/how-to-connect-sync-change-the-configuration/usertype4.png)

### <a name="step-6-run-a-full-synchronization-cycle"></a>Schritt 6: Ausführen eines vollständigen Synchronisierungszyklus
Im Allgemeinen ist ein vollständiger Synchronisierungszyklus erforderlich, weil wir sowohl dem Active Directory- als auch dem Azure AD-Connectorschema neue Attribute hinzugefügt und benutzerdefinierte Synchronisierungsregeln eingeführt haben. Es empfiehlt sich, die Änderungen vor dem Export nach Azure AD zu überprüfen. 

Anhand der folgenden Schritte können Sie die Änderungen überprüfen, während Sie die Schritte des vollständigen Synchronisierungszyklus manuell ausführen.

1. Führen Sie im **lokalen AD-Connector** einen **vollständigen Import** aus:

   1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Connectors**.
   2. Klicken Sie mit der rechten Maustaste auf den **lokalen AD-Connector**, und wählen Sie **Ausführen** aus.
   3. Wählen Sie im Popupdialogfeld **Vollständiger Import** aus, und klicken Sie auf **OK**.
   4. Warten Sie, bis der Vorgang abgeschlossen wurde.

      > [!NOTE]
      > Sie können den vollständigen Import im lokalen AD-Connector überspringen, wenn das Quellattribut bereits in der Liste der importierten Attribute enthalten ist. Anders gesagt: Sie mussten in [Schritt 2: Hinzufügen des Quellattributs zum lokalen AD-Connectorschema](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema) keine Änderungen vornehmen.

2. Führen Sie im **Azure AD-Connector** einen **vollständigen Import** aus:

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD-Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Popupdialogfeld **Vollständiger Import** aus, und klicken Sie auf **OK**.
   3. Warten Sie, bis der Vorgang abgeschlossen wurde.

3. Überprüfen Sie die Änderungen an den Synchronisierungsregeln zu einem vorhandenen Benutzerobjekt:

    Das Quellattribut aus dem lokalen Active Directory und das UserType-Attribut aus Azure AD wurden in ihre jeweiligen Connectorbereiche importiert. Bevor Sie mit einer vollständigen Synchronisierung fortfahren, führen Sie eine **Vorschau** für ein vorhandenes Benutzerobjekt im lokalen AD-Connectorbereich aus. In dem von Ihnen ausgewählten Objekt muss das Quellattribut aufgefüllt sein.
    
    Eine erfolgreiche **Vorschau**, bei der das UserType-Attribut in der Metaverse aufgefüllt ist, ist ein guter Indikator dafür, dass die Synchronisierungsregeln richtig konfiguriert wurden. Informationen zum Ausführen einer **Vorschau** finden Sie im Abschnitt [Überprüfen der Änderungen](#verify-the-change).

4. Führen Sie im **lokalen AD-Connector** eine **vollständige Synchronisierung** aus:

   1. Klicken Sie mit der rechten Maustaste auf den **lokalen AD-Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Popupdialogfeld **Vollständige Synchronisierung** aus, und klicken Sie auf **OK**.
   3. Warten Sie, bis der Vorgang abgeschlossen wurde.

5. Stellen Sie sicher, dass **Ausstehende Exporte** in Azure AD festgelegt ist, indem Sie wie folgt vorgehen:

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD-Connector**, und wählen Sie **Search Connector Space** (Connectorbereich durchsuchen) aus.
   2. Gehen Sie im Popupdialogfeld **Connectorbereich durchsuchen** wie folgt vor:

      - Legen Sie den **Bereich** auf **Ausstehender Export** fest.
      - Aktivieren Sie alle drei Kontrollkästchen: **Hinzufügen**, **Ändern** und **Löschen**.
      - Klicken Sie auf die Schaltfläche **Durchsuchen**, um die Liste von Objekten mit Änderungen, die exportiert werden sollen, abzurufen. Um die Änderungen für ein bestimmtes Objekt zu untersuchen, doppelklicken Sie auf das Objekt.
      - Stellen Sie sicher, dass die Änderungen Ihren Erwartungen entsprechen.

6. Führen Sie **Export** im **Azure AD-Connector** aus:

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD-Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Popupdialogfeld **Connector ausführen** die Option **Exportieren** aus, und klicken Sie auf **OK**.
   3. Warten Sie, bis der Export nach Azure AD abgeschlossen ist.

> [!NOTE]
> Diese Schritte enthalten nicht die Schritte zur vollständigen Synchronisierung und zum Exportieren im Azure AD-Connector. Diese Schritte sind nicht erforderlich, da die Attributwerte lediglich von dem lokalen Active Directory nach Azure AD übertragen werden.

### <a name="step-7-re-enable-the-sync-scheduler"></a>Schritt 7: Erneutes Aktivieren des Synchronisierungsplaners
So aktivieren Sie erneut den integrierten Synchronisierungsplaner:

1. Starten Sie eine PowerShell-Sitzung.
2. Aktivieren Sie die geplante Synchronisierung erneut, indem Sie das Cmdlet `Set-ADSyncScheduler -SyncCycleEnabled $true` ausführen.


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Konfigurationsmodell finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).
* Weitere Informationen zur Ausdruckssprache finden Sie unter [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
