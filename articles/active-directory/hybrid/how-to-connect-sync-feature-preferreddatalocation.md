---
title: 'Azure AD Connect: Konfigurieren des bevorzugten Datenspeicherorts für Microsoft 365-Ressourcen'
description: Erfahren Sie, wie Sie Ihre Microsoft 365-Benutzerressourcen mit der Azure Active Directory Connect-Synchronisierung in der Nähe der Benutzer platzieren.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972758"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Azure Active Directory Connect-Synchronisierung: Konfigurieren des bevorzugten Datenspeicherorts für Microsoft 365-Ressourcen
In diesem Thema wird ausführlich erläutert, wie Sie das Attribut für den bevorzugten Datenspeicherort in der Azure Active Directory Connect-Synchronisierung (Azure AD) konfigurieren. Wenn jemand Multi-Geo-Funktionen in Microsoft 365 verwendet, legen Sie mit diesem Attribut den geografischen Standort der Microsoft 365-Daten des Benutzers fest. (Die Benennungen *Region* und *geografischer Raum* werden synonym verwendet.)

## <a name="enable-synchronization-of-preferred-data-location"></a>Aktivieren der Synchronisierung des bevorzugten Datenspeicherorts
Standardmäßig befinden sich die Microsoft 365-Ressourcen für Ihre Benutzer im selben geografischen Raum wie Ihr Azure AD-Mandant. Wenn sich Ihr Mandant z.B. in Nordamerika befindet, befinden sich die Exchange-Postfächer der Benutzer ebenfalls in Nordamerika. Für ein multinationales Unternehmen ist dies möglicherweise nicht optimal.

Durch Festlegen des **preferredDataLocation**-Attributs können Sie den geografischen Raum des Benutzers definieren. Sie können die Microsoft 365-Ressourcen eines Benutzers, z. B. das Postfach und OneDrive, im selben geografischen Raum wie den Benutzer platzieren und haben immer noch einen Mandanten für Ihre gesamte Organisation.

> [!IMPORTANT]
> Die Multi-Geo-Funktion steht zurzeit für Kunden mit einem aktiven Konzernvertrag (Enterprise Agreement) und mindestens 250 Abonnements für Microsoft 365-Dienste zur Verfügung. Ausführliche Informationen erhalten Sie von Ihrem Microsoft-Vertreter.
>
>

Eine Liste mit allen geografischen Regionen für Microsoft 365 finden Sie auf der Seite [An welchem Ort befinden sich Ihre Daten?](/microsoft-365/enterprise/o365-data-locations).

Folgende geografische Regionen stehen in Microsoft 365 für die Multi-Geo-Funktion zur Verfügung:

| Geografische Region | preferredDataLocation-Wert |
| --- | --- |
| Asien-Pazifik | APC |
| Australien | AUS |
| Canada | CAN |
| Europäische Union | EUR |
| Frankreich | FRA |
| Indien | IND |
| Japan | JPN |
| Korea | KOR |
| Südafrika | ZAF |
| Schweiz | CHE |
| Vereinigte Arabische Emirate | ARE |
| United Kingdom | GBR |
| USA | NAM |

* Wenn ein geografischer Raum nicht in dieser Tabelle aufgeführt wird (z.B. Südamerika), kann er nicht für Multi-Geo Capabilities verwendet werden.

* Nicht alle Microsoft 365-Workloads unterstützen die Festlegung der geografischen Region eines Benutzers.

### <a name="azure-ad-connect-support-for-synchronization"></a>Azure AD Connect-Unterstützung für die Synchronisierung

Azure AD Connect unterstützt in Version 1.1.524.0 und höher die Synchronisierung des Attributs **preferredDataLocation** für **Benutzerobjekte**. Dies gilt insbesondere in folgenden Fällen:

* Das Schema des Objekttyps **Benutzer** im Azure AD-Connector wird zum Einbeziehen des **preferredDataLocation**-Attributs erweitert. Das Attribut ist vom Typ einwertige Zeichenfolge.
* Das Schema des Objekttyps **Person** in der Metaverse wird zum Einbeziehen des **preferredDataLocation**-Attributs erweitert. Das Attribut ist vom Typ einwertige Zeichenfolge.

Standardmäßig ist das **preferredDataLocation**-Attribut für die Synchronisierung nicht aktiviert. Dieses Feature ist für größere Organisationen vorgesehen. Das Active Directory-Schema in Windows Server 2019 verfügt über das Attribut **msDS-preferredDataLocation**, das Sie für diesen Zweck verwenden sollten. Wenn Sie das Active Directory-Schema nicht aktualisiert haben und dies auch nicht möglich ist, müssen Sie ein Attribut identifizieren, das die geografische Region von Microsoft 365 für Ihre Benutzer enthält. Dies ist für jede Organisation unterschiedlich.

> [!IMPORTANT]
> Azure AD erlaubt die direkte Konfiguration des **preferredDataLocation**-Attributs in **Cloudbenutzerobjekten** mithilfe von Azure AD PowerShell. Für die Konfiguration dieses Attributs in **synchronisierten Benutzerobjekten** müssen Sie Azure AD Connect verwenden.

Vor Aktivieren der Synchronisierung:

* Wenn Sie das Active Directory-Schema nicht auf die Version 2019 aktualisiert haben, legen Sie fest, welches lokale Active Directory-Attribut als Quellattribut verwendet werden soll. Dieses muss vom Typ **einwertige Zeichenfolge** sein.
* Wenn Sie das Attribut **preferredDataLocation** mithilfe von Azure AD PowerShell zuvor in vorhandenen **synchronisierten Benutzerobjekten** in Azure AD konfiguriert haben, müssen Sie ein Backport für die Attributwerte auf die entsprechenden **Benutzerobjekte** im lokalen Active Directory durchführen.

    > [!IMPORTANT]
    > Wenn Sie kein Backport für diese Werte durchführen, entfernt Azure AD Connect bei aktivierter Synchronisierung für das Attribut **preferredDataLocation** die vorhandenen Attributwerte in Azure AD.

* Konfigurieren Sie jetzt das Quellattribut für mindestens ein paar lokale Active Directory-Benutzerobjekte. Sie können dies später für die Überprüfung verwenden.

Die folgenden Abschnitte enthalten die Schritte zum Aktivieren der Synchronisierung des **preferredDataLocation**-Attributs.

> [!NOTE]
> Die Schritte werden im Kontext einer Azure AD-Bereitstellung mit einer Topologie mit einer einzelnen Gesamtstruktur und ohne benutzerdefinierte Synchronisierungsregeln beschrieben. Wenn Sie über eine Topologie mit mehreren Gesamtstrukturen verfügen, benutzerdefinierte Synchronisierungsregeln konfiguriert haben oder einen Stagingserver verwenden, sollten Sie die Schritte entsprechend anpassen.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Schritt 1: Deaktivieren des Synchronisierungsplaners und Sicherstellen, dass derzeit keine Synchronisierung ausgeführt wird
Um zu vermeiden, dass unbeabsichtigte Änderungen nach Azure AD exportiert werden, stellen Sie sicher, dass keine Synchronisierung ausgeführt wird, während Sie Synchronisierungsregeln aktualisieren. So deaktivieren Sie den integrierten Synchronisierungsplaner:

1. Starten Sie eine PowerShell-Sitzung auf dem Azure AD Connect-Server.
2. Deaktivieren Sie die geplante Synchronisierung durch Ausführung des Cmdlets `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Starten Sie **Synchronization Service Manager**, indem Sie zu **START** > **Synchronization Service** navigieren.
4. Wählen Sie die Registerkarte **Vorgänge** aus, und vergewissern Sie sich, dass kein Vorgang mit dem Status *In Arbeit* angezeigt wird.

![Screenshot des Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Schritt 2: Aktualisieren des Schemas für Active Directory
Wenn Sie das Active Directory-Schema auf die Version 2019 aktualisiert haben und Connect vor der Schemaerweiterung installiert wurde, enthält der Connect-Schemacache nicht das aktualisierte Schema. Sie müssen das Schema anschließend im Assistenten aktualisieren, damit es auf der Benutzeroberfläche angezeigt wird.

1. Starten Sie den Azure AD Connect-Assistenten auf dem Desktop.
2. Wählen Sie die Option **Aktualisieren des Verzeichnisschemas** aus, und klicken Sie auf **Weiter**.
3. Geben Sie Ihre Azure AD-Anmeldeinformationen ein, und klicken Sie auf **Weiter**.
4. Auf der Seite **Verzeichnisschema aktualisieren** müssen alle Gesamtstrukturen ausgewählt sein, bevor Sie auf **Weiter** klicken.
5. Wenn Sie fertig sind, schließen Sie den Assistenten.

![Screenshot der Aktualisierung des Verzeichnisschemas im Connect-Assistenten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Schritt 3: Hinzufügen des Quellattributs zum lokalen Active Directory-Connectorschema
**Dieser Schritt ist nur für Connect Version 1.3.21 oder früher erforderlich. Wenn Sie 1.4.18 oder höher nutzen, fahren Sie mit Schritt 5 fort.**  
Nicht alle Azure AD-Attribute werden in den lokalen Active Directory Connector-Bereich importiert. Wenn Sie ein Attribut ausgewählt haben, das in der Standardeinstellung nicht synchronisiert wird, müssen Sie es importieren. So fügen Sie das Quellattribut zur Liste der importierten Attribute hinzu:

1. Wählen Sie in Synchronization Service Manager die Registerkarte **Connectors** aus.
2. Klicken Sie mit der rechten Maustaste auf den lokalen Active Directory Connector, und wählen Sie **Eigenschaften** aus.
3. Navigieren Sie im Popupdialogfeld zur Registerkarte **Attribute auswählen**.
4. Stellen Sie sicher, dass das von Ihnen ausgewählte Quellattribut in der Attributliste aktiviert ist. Wenn das Attribut nicht angezeigt wird, klicken Sie auf das Kontrollkästchen **Alle anzeigen**.
5. Wählen Sie zum Speichern **OK** aus.

![Screenshot: Synchronization Service Manager und Dialogfeld „Eigenschaften“, in dem die Liste „Attribute“ hervorgehoben ist](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Schritt 4: Hinzufügen von **preferredDataLocation** zum Azure AD-Connectorschema
**Dieser Schritt ist nur für Connect Version 1.3.21 oder früher erforderlich. Wenn Sie 1.4.18 oder höher nutzen, fahren Sie mit Schritt 5 fort.**  
Standardmäßig wird das **preferredDataLocation**-Attribut nicht in den Azure AD Connector-Bereich importiert. So fügen Sie es der Liste der importierten Attribute hinzu:

1. Wählen Sie in Synchronization Service Manager die Registerkarte **Connectors** aus.
2. Klicken Sie mit der rechten Maustaste auf den Azure AD-Connector, und wählen Sie **Eigenschaften** aus.
3. Navigieren Sie im Popupdialogfeld zur Registerkarte **Attribute auswählen**.
4. Wählen Sie das **preferredDataLocation**-Attribut in der Liste aus.
5. Wählen Sie zum Speichern **OK** aus.

![Screenshot von Synchronization Service Manager und Dialogfeld „Eigenschaften“](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Schritt 5: Erstellen einer Synchronisierungsregel für eingehende Daten
Die Synchronisierungsregel für eingehende Daten ermöglicht die Übertragung des Attributwerts aus dem Quellattribut im lokalen Active Directory in die Metaverse.

1. Starten Sie den **Synchronisierungsregel-Editor**, indem Sie zu **START** > **Synchronisierungsregel-Editor** navigieren.
2. Legen Sie den Suchfilter **Richtung** auf **Eingehend** fest.
3. Um eine neue Regel für eingehende Daten zu erstellen, wählen Sie **Neue Regel hinzufügen**.
4. Geben Sie auf der Registerkarte **Beschreibung** die folgende Konfiguration an:

    | attribute | Wert | Details |
    | --- | --- | --- |
    | Name | *Geben Sie einen Namen ein.* | Beispiel: „In from AD – User preferredDataLocation“ |
    | BESCHREIBUNG | *Geben Sie eine benutzerdefinierte Beschreibung an.* |  |
    | Verbundenes System | *Lokalen Active Directory Connector auswählen* |  |
    | Objekttyp des verbundenen Systems | **Benutzer** |  |
    | Metaverse-Objekttyp | **Person** |  |
    | Verknüpfungstyp | **Join** |  |
    | Rangfolge | *Wählen Sie eine Zahl zwischen 1 und 99 aus.* | Die Zahlen zwischen 1 und 99 sind für benutzerdefinierte Synchronisierungsregeln reserviert. Wählen Sie keinen Wert aus, der von einer anderen Synchronisierungsregel verwendet wird. |

5. Lassen Sie die **Bereichsfilter** leer, um alle Objekte einzubeziehen. Möglicherweise müssen Sie den Bereichsfilter entsprechend Ihrer Azure AD Connect-Bereitstellung anpassen.
6. Navigieren Sie zur Registerkarte **Transformation**, und implementieren Sie folgende Transformationsregel:

    | Attributflusstyp | Zielattribut | `Source` | Einmal anwenden | Mergetyp |
    | --- | --- | --- | --- | --- |
    |Direkt | preferredDataLocation | Wählen Sie das Quellattribut aus. | Deaktiviert | Aktualisieren |

7. Wählen Sie **Hinzufügen**, um die Regel für eingehende Daten zu erstellen.

![Screenshot des Erstellens einer Synchronisierungsregel für eingehende Daten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Schritt 6: Erstellen einer Synchronisierungsregel für ausgehende Daten
Die Synchronisierungsregel für ausgehende Daten ermöglicht die Übertragung des Attributwerts aus der Metaverse auf das Attribut **preferredDataLocation** in Azure AD:

1. Navigieren Sie zum **Synchronisierungsregel-Editor**.
2. Legen Sie den Suchfilter **Richtung** auf **Ausgehend** fest.
3. Wählen Sie **Neue Regel hinzufügen** aus.
4. Geben Sie auf der Registerkarte **Beschreibung** die folgende Konfiguration an:

    | attribute | Wert | Details |
    | ----- | ------ | --- |
    | Name | *Geben Sie einen Namen ein.* | Beispiel: „Out to Azure AD – User preferredDataLocation“ |
    | BESCHREIBUNG | *Geben Sie eine Beschreibung ein.* ||
    | Verbundenes System | *Wählen Sie den Azure AD-Connector aus.* ||
    | Objekttyp des verbundenen Systems | **Benutzer** ||
    | Metaverse-Objekttyp | **Person** ||
    | Verknüpfungstyp | **Join** ||
    | Rangfolge | *Wählen Sie eine Zahl zwischen 1 und 99 aus.* | Die Zahlen zwischen 1 und 99 sind für benutzerdefinierte Synchronisierungsregeln reserviert. Wählen Sie keinen Wert aus, der von einer anderen Synchronisierungsregel verwendet wird. |

5. Navigieren Sie zur Registerkarte **Bereichsfilter**, und fügen Sie eine einzelne Bereichsfiltergruppe mit zwei Klauseln hinzu:

    | attribute | Operator | Wert |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Benutzer |
    | cloudMastered | NOTEQUAL | True |

    Der Bereichsfilter legt fest, auf welche Azure AD-Objekte diese Synchronisierungsregel für ausgehende Daten angewendet wird. In diesem Beispiel verwenden wir den Bereichsfilter aus der Standardsynchronisierungsregel „Ausgehend nach Azure AD – Benutzeridentität“. Er verhindert, dass die Synchronisierungsregel auf **Benutzer** objekte angewendet wird, die nicht über ein lokales Active Directory synchronisiert werden. Möglicherweise müssen Sie den Bereichsfilter entsprechend Ihrer Azure AD Connect-Bereitstellung anpassen.

6. Navigieren Sie zur Registerkarte **Transformation**, und implementieren Sie folgende Transformationsregel:

    | Attributflusstyp | Zielattribut | `Source` | Einmal anwenden | Mergetyp |
    | --- | --- | --- | --- | --- |
    | Direkt | preferredDataLocation | preferredDataLocation | Deaktiviert | Aktualisieren |

7. Klicken Sie auf **Hinzufügen**, um die Regel für ausgehende Daten zu erstellen.

![Screenshot des Erstellens einer Synchronisierungsregel für ausgehende Daten](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Schritt 7: Ausführen des vollständigen Synchronisierungszyklus
Im Allgemeinen ist der vollständige Synchronisierungszyklus erforderlich. Der Grund ist, dass Sie sowohl dem Active Directory- als auch dem Azure AD Connector-Schema neue Attribute hinzugefügt und benutzerdefinierte Synchronisierungsregeln eingeführt haben. Überprüfen Sie die Änderungen vor dem Export nach Azure AD. Anhand der folgenden Schritte können Sie die Änderungen überprüfen, während Sie die Schritte des vollständigen Synchronisierungszyklus manuell ausführen.

1. Führen Sie **Vollständiger Import** auf dem lokalen Active Directory Connector aus:

   1. Wechseln Sie in Synchronization Service Manager zur Registerkarte **Vorgänge**.
   2. Klicken Sie mit der rechten Maustaste auf den **lokalen Active Directory Connector**, und wählen Sie **Ausführen** aus.
   3. Wählen Sie im Dialogfeld **Vollständiger Import** und dann **OK** aus.
   4. Warten Sie, bis der Vorgang abgeschlossen wurde.

      > [!NOTE]
      > Sie können den vollständigen Import im lokalen Active Directory Connector überspringen, wenn das Quellattribut bereits in der Liste der importierten Attribute enthalten ist. D.h., Sie hätten in Schritt 2 oben in diesem Artikel keine Änderungen vornehmen müssen.

2. Führen Sie **Vollständiger Import** auf dem Azure AD Connector aus:

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Dialogfeld **Vollständiger Import** und dann **OK** aus.
   3. Warten Sie, bis der Vorgang abgeschlossen wurde.

3. Überprüfen Sie die Änderungen an den Synchronisierungsregeln zu einem vorhandenen **Benutzerobjekt**.

   Das Quellattribut aus dem lokalen Active Directory und **preferredDataLocation** aus Azure AD wurden in den jeweiligen Connectorbereich importiert. Bevor Sie mit dem Schritt der vollständigen Synchronisierung fortfahren, sollten Sie eine Vorschau zu einem vorhandenen **Benutzerobjekt** im lokalen Active Directory Connector-Bereich ausführen. In dem von Ihnen ausgewählten Objekt muss das Quellattribut aufgefüllt sein. Eine erfolgreiche Vorschau, bei der **preferredDataLocation** in der Metaverse aufgefüllt ist, ist ein guter Indikator dafür, dass Sie die Synchronisierungsregeln korrekt konfiguriert haben. Informationen zum Durchführen einer Vorschau finden Sie unter [Überprüfen der Änderungen](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Führen Sie **Vollständige Synchronisierung** auf dem lokalen Active Directory Connector aus:

   1. Klicken Sie mit der rechten Maustaste auf den **lokalen Active Directory Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Dialogfeld **Vollständige Synchronisierung** aus und dann **OK**.
   3. Warten Sie, bis der Vorgang abgeschlossen wurde.

5. Stellen Sie sicher, dass **Ausstehende Exporte** in Azure AD festgelegt ist, indem Sie wie folgt vorgehen:

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD Connector**, und wählen Sie **Connectorbereich durchsuchen** aus.
   2. Gehen Sie im Dialogfeld **Connectorbereich durchsuchen** wie folgt vor:

        a. Legen Sie den **Bereich** auf **Ausstehender Export** fest.<br>
        b. Wählen Sie alle drei Kontrollkästchen aus: **„Hinzufügen“, „Ändern“ und „Löschen“** .<br>
        c. Um die Liste von Objekten mit Änderungen, die exportiert werden sollen, anzuzeigen, wählen Sie **Durchsuchen** aus. Um die Änderungen für ein bestimmtes Objekt zu untersuchen, doppelklicken Sie auf das Objekt.<br>
        d. Stellen Sie sicher, dass die Änderungen Ihren Erwartungen entsprechen.

6. Ausführen von **Exportieren** im **Azure AD Connector**

   1. Klicken Sie mit der rechten Maustaste auf den **Azure AD Connector**, und wählen Sie **Ausführen** aus.
   2. Wählen Sie im Dialogfeld **Connector ausführen** die Option **Exportieren** und dann **OK** aus.
   3. Warten Sie, bis der Vorgang abgeschlossen wurde.

> [!NOTE]
> Ihnen fällt vielleicht auf, dass der Schritt der vollständigen Synchronisierung auf dem Azure AD Connector bzw. des Exportierens auf dem Active Directory Connector nicht enthalten sind. Diese Schritte sind nicht erforderlich, da die Attributwerte lediglich von dem lokalen Active Directory nach Azure AD übertragen werden.

## <a name="step-8-re-enable-sync-scheduler"></a>Schritt 8: Erneutes Aktivieren des Synchronisierungsplaners
So aktivieren Sie erneut den integrierten Synchronisierungsplaner:

1. Starten Sie eine PowerShell-Sitzung.
2. Aktivieren Sie die geplante Synchronisierung durch Ausführung dieses Cmdlets neu: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Schritt 9: Überprüfen des Ergebnisses
Sie können nun die Konfiguration überprüfen und für Ihre Benutzer aktivieren.

1. Fügen Sie dem ausgewählte Attribut für einen Benutzer den geografischen Raum hinzu. Die Liste der verfügbaren geografischen Räume finden Sie in dieser Tabelle.  
![Screenshot eines einem Benutzer hinzugefügten AD-Attributs](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Warten Sie, bis das Attribut mit Azure AD synchronisiert wurde.
3. Überprüfen Sie mithilfe von Exchange Online-PowerShell, ob die Postfachregion ordnungsgemäß festgelegt wurde.  
![Screenshot von Exchange Online-PowerShell](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Sofern Ihr Mandant für die Verwendung dieses Features aktiviert wurde, wird das Postfach in den richtigen geografischen Raum verschoben. Dies können Sie überprüfen, indem Sie sich den Namen des Servers ansehen, auf dem sich das Postfach befindet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Multi-Geo-Funktionen in Microsoft 365:

* [Multi-Geo-Sitzungen mit Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo-Funktionen in OneDrive und SharePoint Online in Office 365](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Multi-Geo-Funktionen in OneDrive und SharePoint Online in Office 365](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Weitere Informationen zum Konfigurationsmodell im Synchronisierungsmodul:

* Weitere Informationen zum Konfigurationsmodell finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).
* Weitere Informationen zur Ausdruckssprache finden Sie unter [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Übersichtsthemen:

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)