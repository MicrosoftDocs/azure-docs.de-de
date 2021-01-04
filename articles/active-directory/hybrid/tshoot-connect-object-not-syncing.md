---
title: Beheben von Problemen mit einem Objekt, das nicht mit Azure Active Directory synchronisiert wird | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme mit einem Objekt beheben, das nicht mit Azure Active Directory synchronisiert wird.
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
ms.topic: troubleshooting
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a0c8a42edad08308095469039c048f8dd8552af
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413461"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Beheben von Problemen mit einem Objekt, das nicht mit Azure Active Directory synchronisiert wird

Wenn ein Objekt nicht wie erwartet mit Microsoft Azure Active Directory (Azure AD) synchronisiert wird, kann dies mehrere Gründe haben. Wenn Sie eine E-Mail mit einer Fehlerbenachrichtigung von Azure AD erhalten haben oder wenn der Fehler in Azure AD Connect Health angezeigt wird, lesen Sie stattdessen [Beheben von Fehlern während der Synchronisierung](tshoot-connect-sync-errors.md). Wenn Sie ein Problem behandeln möchten, bei dem das Objekt nicht in Azure AD enthalten ist, dann ist dieses Thema für Sie das Richtige. Es wird beschrieben, wie Fehler in der Azure AD Connect-Synchronisierung für lokale Komponenten gefunden werden.

>[!IMPORTANT]
>Verwenden Sie für die Azure AD Connect-Bereitstellung ab Version 1.1.749.0 die [Problembehandlungsaufgabe](tshoot-connect-objectsync.md) im Assistenten, um Probleme bei der Objektsynchronisierung zu behandeln. 

## <a name="synchronization-process"></a>Synchronisierungsvorgang

Vor dem Untersuchen von Synchronisierungsproblemen ist es wichtig, den Synchronisierungsprozess in Azure AD Connect zu verstehen:

  ![Prozessdiagramm der Azure AD Connect-Synchronisierung](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connectorbereich, eine Tabelle in einer Datenbank
* **MV:** Metaverse, eine Tabelle in einer Datenbank

### <a name="synchronization-steps"></a>**Synchronisierungsschritte**
Der Synchronisierungsprozess umfasst die folgenden Schritte:

1. **Aus AD importieren:** Active Directory-Objekte werden in den Active Directory-Connectorbereich eingefügt.

2. **Importieren aus Azure AD:** Azure AD-Objekte werden in den Azure AD-Connectorbereich eingefügt.

3. **Synchronisierung:** Es werden eingehende Synchronisierungsregeln und ausgehende Synchronisierungsregeln in der Reihenfolge der Priorität von niedrig zu hoch ausgeführt. Informationen zu den Synchronisierungsregeln finden Sie im Synchronisierungsregel-Editor in den Desktopanwendungen. Die Synchronisierungsregeln für eingehenden Datenverkehr importieren Daten aus dem Connectorbereich in die Metaverse. Die Synchronisierungsregeln für ausgehenden Datenverkehr verschieben Daten aus der Metaverse in den Connectorbereich.

4. **In AD exportieren:** Nach der Synchronisierung werden Objekte aus dem Active Directory-Connectorbereich nach Active Directory exportiert.

5. **Exportieren nach Azure AD:** Nach der Synchronisierung werden die Objekte aus dem Azure AD-Connectorbereich nach Azure AD exportiert.

## <a name="troubleshooting"></a>Problembehandlung

Um Fehler zu finden, müssen Sie in folgender Reihenfolge an unterschiedlichen Stellen suchen:

1. In den [Vorgangsprotokollen](#operations), um Fehler zu finden, die während des Importierens und Synchronisierens vom Synchronisierungsmodul identifiziert werden.
2. Im [Connectorbereich](#connector-space-object-properties), um fehlende Objekte und Synchronisierungsfehler zu finden.
3. In der [Metaverse](#metaverse-object-properties), um datenbezogene Probleme zu finden.

Starten Sie [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) vor dem Beginn der folgenden Schritte.

## <a name="operations"></a>Operationen (Operations)
Sie sollten mit Ihrer Problembehandlung auf der Registerkarte **Vorgänge** in Synchronization Service Manager beginnen. Diese Registerkarte zeigt die Ergebnisse der letzten Vorgänge. 

![Screenshot von Synchronization Service Manager mit ausgewählter Registerkarte „Vorgänge“](./media/tshoot-connect-object-not-syncing/operations.png)  

Die obere Hälfte der Registerkarte **Vorgänge** zeigt alle Ausführungen in chronologischer Reihenfolge. Standardmäßig enthält das Vorgangsprotokoll Informationen der letzten sieben Tage. Diese Einstellung kann mit dem [Scheduler](how-to-connect-sync-feature-scheduler.md) geändert werden. Suchen Sie nach Ausführungen ohne den Status **Erfolg**. Die Sortierung kann durch Klicken auf die Kopfzeilen geändert werden.

Die Spalte **Status** enthält die wichtigsten Informationen und das schwerwiegendste Problem einer Ausführung. Im Folgenden finden Sie eine kurze Zusammenfassung der häufigsten Status in der Reihenfolge ihrer Untersuchungspriorität (bei „*“ gibt es mehrere mögliche Fehlerzeichenfolgen).

| Status | Comment |
| --- | --- |
| stopped-* |Die Ausführung konnte nicht fertig gestellt werden. Dies kann beispielsweise passieren, wenn das Remotesystem ausgefallen ist und nicht kontaktiert werden kann. |
| stopped-error-limit |Es gibt mehr als 5.000 Fehler. Die Ausführung wurde aufgrund der großen Anzahl von Fehlern automatisch beendet. |
| completed-\*-errors |Die Ausführung wurde abgeschlossen, es sind jedoch Fehler (weniger als 5.000) aufgetreten, die untersucht werden sollten. |
| completed-\*-warnings |Die Ausführung wurde abgeschlossen, einige Daten weisen jedoch einen unerwarteten Zustand auf. Wenn Fehler auftreten, ist diese Meldung in der Regel nur ein Symptom. Bis Sie die Fehler behoben haben, sollten Sie keine Warnungen untersuchen. |
| success |Keine Probleme |

Wenn Sie eine Zeile auswählen, wird der untere Bereich der Registerkarte **Vorgänge** aktualisiert, und die Details dieser Ausführung werden angezeigt. Ganz links in diesem Bereich finden Sie möglicherweise eine Liste mit der **Schrittnummer**. Diese Liste wird nur angezeigt, wenn Ihre Gesamtstruktur mehrere Domänen enthält und jede Domäne als einzelner Schritt dargestellt wird. Den Domänennamen finden Sie unter der Überschrift **Partition**. Unter der Überschrift **Synchronization Statistics** (Synchronisierungsstatistik) finden Sie weitere Informationen zur Anzahl verarbeiteter Änderungen. Wählen Sie die Links aus, um eine Liste mit den geänderten Objekten anzuzeigen. Sind Objekte mit Fehlern vorhanden, werden diese Fehler unter der Überschrift **Synchronisierungsfehler** angezeigt.

### <a name="errors-on-the-operations-tab"></a>Fehler auf der Registerkarte „Vorgänge“
Wenn Fehler auftreten, werden in Synchronization Service Manager sowohl das fehlerhafte Objekt als auch der Fehler selbst als Links dargestellt, über die weitere Informationen abgerufen werden können.

![Screenshot von Fehlern in Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Wählen Sie zunächst die Fehlerzeichenfolge aus. (In der Abbildung oben lautet die Fehlerzeichenfolge **sync-rule-error-function-triggered**.) Eine Übersicht über das Objekt wird angezeigt. Wählen Sie zum Anzeigen des tatsächlichen Fehlers **Stapelüberwachung** aus. Dadurch werden für den Fehler Informationen der Debugebene angezeigt.

Klicken Sie im Feld **Call Stack Information** (Aufruflisteninformationen) mit der rechten Maustaste auf **Alle auswählen** , und wählen Sie anschließend **Kopieren** aus. Sie können dann den Stapel kopieren und den Fehler in Ihrem bevorzugten Editor, z. B. Windows-Editor, anzeigen.

Wenn der Fehler aus **SyncRulesEngine** stammt, beginnen die Aufruflisteninformationen mit einer Liste aller Attribute für das Objekt. Scrollen Sie nach unten, bis Sie die Überschrift **InnerException = >** sehen.  

  ![Screenshot von Synchronization Service Manager mit Fehlerinformationen unter der Überschrift „InnerException =>“](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
Die Zeile nach der Überschrift gibt Aufschluss über den Fehler. In der obigen Abbildung stammt der Fehler von einer benutzerdefinierten Synchronisierungsregel, die Fabrikam erstellt hat.

Wenn der Fehler selbst nicht genügend Informationen liefert, sollten Sie sich die Daten ansehen. Wählen Sie den Link mit der Objekt-ID aus, und fahren Sie mit der Problembehandlung der [importierten Objekte des Connectorbereichs](#cs-import) fort.

## <a name="connector-space-object-properties"></a>Eigenschaften der Objekte des Connectorbereichs
Wenn auf der Registerkarte [**Vorgänge**](#operations) keine Fehler angezeigt werden, verfolgen Sie das Objekt aus dem Connectorbereich von Active Directory zur Metaverse in Azure AD. In diesem Pfad sollten Sie feststellen können, wo das Problem liegt.

### <a name="searching-for-an-object-in-the-cs"></a>Suchen eines Objekts im Connectorbereich

Wählen Sie in Synchronization Service Manager die Option **Connectors** , anschließend den Active Directory-Connector und dann **Connectorbereich durchsuchen** aus.

Wählen Sie im Feld **Bereich** entweder **RDN** (wenn Sie nach dem CN-Attribut suchen möchten) oder **DN oder Anker** (wenn Sie auf dem Attribut **distinguishedName** suchen möchten) aus. Geben Sie einen Wert ein, und wählen Sie **Suchen** aus. 
 
![Screenshot einer Suche im Connectorbereich](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Wenn Sie das gesuchte Objekt nicht finden, wurde es möglicherweise mit der [domänenbasierten Filterung](how-to-connect-sync-configure-filtering.md#domain-based-filtering) oder der [Filterung basierend auf der Organisationseinheit](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) gefiltert. Informationen dazu, wie Sie überprüfen, ob die Filterung wie erwartet konfiguriert wurde, finden Sie unter [Azure AD Connect-Synchronisierung: Konfigurieren der Filterung](how-to-connect-sync-configure-filtering.md).

Sie können eine weitere nützliche Suche ausführen, indem Sie den Azure AD-Connector auswählen. Wählen Sie im Feld **Bereich** die Option **Import steht aus** und dann das Kontrollkästchen **Hinzufügen** aus. Bei dieser Suche werden Ihnen alle synchronisierten Objekte in Azure AD angezeigt, die keinem lokalen Objekt zugeordnet werden können.  

![Screenshot einer Suche nach verwaisten Elementen im Connectorbereich](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
Diese Objekte wurden von einem anderen Synchronisierungsmodul oder einem Synchronisierungsmodul mit einer anderen Filterkonfiguration erstellt. Diese verwaisten Objekte werden nicht mehr verwaltet. Sie sollten diese Liste überprüfen, und diese Objekte mit den [Azure ADPowerShell](/previous-versions/azure/jj151815(v=azure.100))-Cmdlets entfernen.

### <a name="cs-import"></a>Importieren aus dem Connectorbereich
Beim Öffnen eines Connectorbereichsobjekts befinden sich oben mehrere Registerkarten. Auf der Registerkarte **Importieren** werden die Daten angezeigt, die nach einem Import bereitgestellt werden.  

![Screenshot des Fensters „Connector Space Object Properties“ (Eigenschaften der Objekte des Connectorbereichs) mit ausgewählter Registerkarte „Importieren“](./media/tshoot-connect-object-not-syncing/csobject.png)    

In der Spalte **Alter Wert** wird dargestellt, was derzeit im System gespeichert ist, und in der Spalte **Neuer Wert** wird angezeigt, was aus dem Quellsystem empfangen, aber noch nicht angewandt wurde. Wenn ein Fehler für das Objekt vorhanden ist, werden die Änderungen nicht verarbeitet.

Die Registerkarte **Synchronisierungsfehler** wird nur dann im Fenster **Connector Space Object Properties** (Eigenschaften der Objekte des Connectorbereichs) angezeigt, wenn ein Problem mit dem Objekt vorliegt. Weitere Informationen finden Sie unter [Beheben von Synchronisierungsfehlern auf der Registerkarte **Vorgänge**](#errors-on-the-operations-tab).

![Screenshot der Registerkarte „Synchronisierungsfehler“ im Fenster „Connector Space Object Properties“ (Eigenschaften der Objekte des Connectorbereichs)](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS-Herkunft
Auf der Registerkarte **Herkunft** im Fenster **Connector Space Object Properties** (Eigenschaften der Objekte des Connectorbereichs) wird gezeigt, in welchem Verhältnis das Objekt aus dem Connectorbereich mit dem Metaverseobjekt steht. Sie sehen, wann der Connector zuletzt eine Änderung aus dem verbundenen System importiert hat und welche Regeln zum Auffüllen der Daten in der Metaverse angewandt wurden.  

![Screenshot der Registerkarte „Herkunft“ im Fenster „Connector Space Object Properties“ (Eigenschaften der Objekte des Connectorbereichs)](./media/tshoot-connect-object-not-syncing/cslineage.png)  

In der obigen Abbildung zeigt die Spalte **Aktion** eine Synchronisierungsregel für eingehende Daten mit der Aktion **Bereitstellen**. Damit wird angegeben, dass das Metaverse-Objekt erhalten bleibt, solange dieses Objekt des Connectorbereichs vorhanden ist. Wenn die Liste mit den Synchronisierungsregeln hingegen eine Synchronisierungsregel für ausgehende Daten mit der Aktion **Bereitstellen** enthält, wird beim Löschen des Metaverseobjekts auch dieses Objekt gelöscht.  

![Screenshot eines Herkunftsfensters auf der Registerkarte „Herkunft“ im Fenster „Connector Space Object Properties“ (Eigenschaften der Objekte des Connectorbereichs)](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

Sie können in der obigen Abbildung in der Spalte **PasswordSync** (Kennwortsynchronisierung) auch sehen, dass der eingehende Connectorbereich Kennwortänderungen beitragen kann, da eine Synchronisierungsregel den Wert **True** aufweist. Dieses Kennwort wird anschließend über die Ausgangsregel an Azure AD gesendet.

Auf der Registerkarte **Herkunft** gelangen Sie zum Metaverse, indem Sie [**Metaverse Object Properties**](#mv-attributes) (Eigenschaften der Metaverseobjekte) auswählen.

### <a name="preview"></a>Vorschau
In der linken unteren Ecke des Fensters **Connector Space Object Properties** (Eigenschaften der Objekte des Connectorbereichs) befindet sich die Schaltfläche **Vorschau**. Wählen Sie diese Schaltfläche aus, um die Seite **Vorschau** zu öffnen, auf der Sie ein einzelnes Objekt synchronisieren können. Diese Seite ist nützlich, wenn Sie Probleme mit einigen benutzerdefinierten Synchronisierungsregeln behandeln und die Auswirkungen einer Änderung auf ein einzelnes Objekt sehen möchten. Sie können eine **vollständige Synchronisierung** oder eine **Deltasynchronisierung** auswählen. Sie können auch **Generate Preview** (Vorschau generieren) auswählen, um die Änderung nur im Arbeitsspeicher beizubehalten. Wählen Sie stattdessen **Commit Preview** (Vorschau ausführen) aus, um die Metaverse zu aktualisieren und alle Änderungen in den Zielconnectorbereichen bereitzustellen.  

![Screenshot der Seite „Vorschau“ mit ausgewählter Option „Vorschau starten“](./media/tshoot-connect-object-not-syncing/preview.png)  

Sie können das Objekt und die für einen bestimmten Attributfluss angewandte Regel in der Vorschau prüfen.  

![Screenshot der Seite „Vorschau“ mit dem Importflow der Attribute](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Log
Wählen Sie neben der Schaltfläche **Vorschau** die Schaltfläche **Protokoll** aus, um die Seite **Protokoll** zu öffnen. Dort können Sie den Kennwortsynchronisierungsstatus und den Verlauf anzeigen. Weitere Informationen finden Sie unter [Problembehandlung für die Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Objekteigenschaften der Metaverse
Es ist in der Regel besser, mit der Suche im Active Directory-Connectorbereich zu starten. Aber Sie können die Suche auch aus der Metaverse starten.

### <a name="searching-for-an-object-in-the-mv"></a>Suchen eines Objekts in der Metaverse
Wählen Sie in Synchronization Service Manager **Metaverse Search** (Metaversesuche) aus, wie in der folgenden Abbildung gezeigt. Erstellen Sie eine Abfrage, bei der Sie wissen, dass sie den Benutzer findet. Suchen Sie nach allgemeinen Attributen, z. B. **accountName** ( **sAMAccountName** ) und **userPrincipalName**. Weitere Informationen finden Sie unter [Synchronization Service Manager – Metaverse Search](how-to-connect-sync-service-manager-ui-mvsearch.md).

![Screenshot von Synchronization Service Manager mit ausgewählter Registerkarte „Metaverse Search“ (Metaversesuche)](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Klicken Sie im Fenster **Suchergebnisse** auf das Objekt.

Wenn Sie das Objekt nicht gefunden haben, hat es die Metaverse noch nicht erreicht. Suchen Sie das Objekt weiter im Active Directory-[Connectorbereich](#connector-space-object-properties). Wenn Sie das Objekt im Active Directory-Connectorbereich finden, könnte ein Synchronisierungsfehler vorliegen, der das Objekt daran hindert, zur Metaverse zu gelangen, oder es könnte ein Synchronisierungsregelfilter angewandt werden.

### <a name="object-not-found-in-the-mv"></a>Objekt in MV nicht gefunden
Wenn das Objekt im Active Directory-Connectorbereich, aber nicht im Metaverse vorhanden ist, wird ein Bereichsfilter angewandt. Um den Bereichsfilter anzuzeigen, wählen Sie im Menü der Desktopanwendung den **Synchronisierungsregel-Editor** aus. Filtern Sie die für das Objekt geltenden Regeln anhand der Filter unten.

  ![Screenshot des Synchronisierungsregel-Editors mit einer Suche nach Synchronisierungsregeln für eingehende Daten](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

Zeigen Sie Regel in der Liste von oben nach unten an, und überprüfen Sie die **Bereichsfilter**. Wenn der Wert **isCriticalSystemObject** im folgenden Bereichsfilter NULL, FALSE oder leer ist, gehört er zum Bereich.

  ![Screenshot eines Bereichsfilters in einer Suche nach Synchronisierungsregeln für eingehende Daten](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

Wechseln Sie zur Attributliste [CS Import](#cs-import) (Aus dem Connectorbereich importieren), und überprüfen Sie, welcher Filter das Verschieben des Objekts in die Metaverse verhindert. Die Attributliste **Connector Space** (Connectorbereich) zeigt nur Attribute an, die nicht NULL oder leer sind. Wenn beispielsweise **isCriticalSystemObject** nicht in der Liste angezeigt wird, ist der Wert dieses Attributs NULL oder leer.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Objekt im Azure AD-CS nicht gefunden
Wenn das Objekt nicht im Connectorbereich von Azure AD, aber in der Metaverse vorhanden ist, sehen Sie sich den Bereichsfilter der Ausgangsregeln des entsprechenden Connectorbereichs an. Überprüfen Sie, ob das Objekt herausgefiltert wurde, da die [MV-Attribute](#mv-attributes) nicht die Kriterien erfüllen.

Um den ausgehenden Bereichsfilter anzuzeigen, wählen Sie die für das Objekt anwendbaren Regeln aus, indem Sie den untenstehenden Filter anpassen. Zeigen Sie jede einzelne Regel an, und überprüfen Sie den entsprechenden Wert für das [MV-Attribut](#mv-attributes).

  ![Screenshot einer Suche nach Synchronisierungsregeln für ausgehende Daten im Synchronisierungsregel-Editor](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-Attribute
Auf der Registerkarte **Attribute** werden die Werte und der Connectors, von denen sie stammen, angezeigt.  

![Screenshot des Fensters „Metaverse Object Properties“ (Eigenschaften der Metaverseobjekte) mit ausgewählter Registerkarte „Attribute“](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Wenn ein Objekt nicht synchronisiert wird, stellen Sie die folgenden Fragen zum Attributstatus in der Metaverse:
- Ist das Attribut **cloudFiltered** vorhanden und auf **True** festgelegt? Wenn dies zutrifft, wurde es anhand der Schritte zur [attributbasierten Filterung](how-to-connect-sync-configure-filtering.md#attribute-based-filtering) gefiltert.
- Ist das Attribut **sourceAnchor** vorhanden? Wenn dies nicht der Fall ist, haben Sie eine Topologie mit Kontoressourcengesamtstruktur? Wenn ein Objekt als ein verknüpftes Postfach identifiziert wird (das Attribut **msExchRecipientTypeDetails** hat den Wert **2** ), dann wird **sourceAnchor** von der Gesamtstruktur mit einem aktivierten Active Directory-Konto bereitgestellt. Stellen Sie sicher, dass das Hauptkonto ordnungsgemäß importiert und synchronisiert wurde. Das Hauptkonto muss bei den [Connectors](#mv-connectors) für das Objekt aufgelistet sein.

### <a name="mv-connectors"></a>Metaverseconnectors
Auf der Registerkarte **Connectors** werden alle Connectorbereiche angezeigt, die über eine Darstellung des Objekts verfügen. 
 
![Screenshot des Fensters „Metaverse Object Properties“ (Eigenschaften der Metaverseobjekte) mit ausgewählter Registerkarte „Connectors“](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

Sie müssen einen Connector haben für:

- Jede Active Directory-Gesamtstruktur, in der der Benutzer dargestellt wird. Diese Darstellung kann die Objekte **foreignSecurityPrincipals** und **Contact** umfassen.
- Ein Connector in Azure AD.

Wenn Ihnen der Azure AD-Connector fehlt, dann lesen Sie den Abschnitt zu [Metaverseattributen](#mv-attributes), um die Kriterien für die Bereitstellung in Azure AD zu überprüfen.

Sie können auch von der Registerkarte **Connectors** zum [Objekt im Connectorbereich](#connector-space-object-properties) navigieren. Wählen Sie eine Zeile aus, und klicken Sie auf **Eigenschaften**.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie hier mehr über die [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md).
- Erfahren Sie mehr über [Hybrididentitäten](whatis-hybrid-identity.md).