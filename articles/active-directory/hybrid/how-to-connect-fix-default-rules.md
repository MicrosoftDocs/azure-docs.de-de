---
title: Korrigieren von geänderten Standardregeln – Azure AD Connect | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie geänderte Standardregeln korrigieren, die mit Azure AD Connect bereitgestellt werden.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2f0956b44d6df64fb73e5eee7844574237d8755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067630"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Korrigieren von geänderten Standardregeln in Azure AD Connect

Azure Active Directory (Azure AD) Connect verwendet Standardregeln für die Synchronisierung.  Leider können diese Regeln nicht universell für alle Organisationen gelten. Sie müssen ggf. entsprechend den jeweiligen Anforderungen geändert werden. In diesem Artikel werden zwei Beispiele für die häufigsten vorgenommenen Anpassungen erläutert und die richtige Vorgehensweise dafür beschrieben.

>[!NOTE] 
> Vorhandene Standardregeln können nicht geändert werden, um die jeweils benötigte Anpassung zu erreichen. Andernfalls werden diese Regeln in künftigen Versionen nicht auf die neueste Version aktualisiert. Sie erhalten dann keine erforderlichen Fehlerbehebungen und neuen Features. In diesem Dokument wird erklärt, wie Sie das gleiche Ergebnis erzielen können, ohne die bestehenden Standardregeln zu ändern. 

## <a name="how-to-identify-modified-default-rules"></a>Wie sind geänderte Standardregeln zu erkennen?
Seit Version 1.3.7.0 von Azure AD Connect ist es nun einfach, geänderte Standardregel zu identifizieren. Gehen Sie zu **Apps auf dem Desktop**, und wählen Sie **Synchronisierungsregel-Editor** aus.

![Azure AD Connect mit hervorgehobenem Synchronisierungsregel-Editor](media/how-to-connect-fix-default-rules/default1.png)

Im Editor werden alle geänderten Standardregeln mit einem Warnsymbol vor dem Namen angezeigt.

![Symbol "Warnung"](media/how-to-connect-fix-default-rules/default2.png)

 Eine deaktivierte Regel mit dem gleichen Namen wird ebenfalls aufgeführt (dies ist unbearbeitete Standardregel).

![Synchronisierungsregel-Editor mit unbearbeiteter Standardregel und geänderter Standardregel](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>Häufige Anpassungen
Im Folgenden sind häufige Anpassungen der Standardregeln aufgeführt:

- Attributfluss ändern
- Bereichsfilter ändern
- Verknüpfungsbedingung ändern

Vor dem Ändern von Regeln:

- Deaktivieren Sie den Synchronisierungsplaner. Der Synchronisierungsplaner wird standardmäßig alle 30 Minuten ausgeführt. Stellen Sie sicher, dass er nicht gestartet wird, während Sie Änderungen vornehmen und Probleme in Ihren neuen Regeln beheben. Um den Planer vorübergehend zu deaktivieren, starten Sie PowerShell, und führen Sie den Befehl `Set-ADSyncScheduler -SyncCycleEnabled $false` aus.
 ![PowerShell-Befehle zum Deaktivieren des Synchronisierungsplaners](media/how-to-connect-fix-default-rules/default3.png)

- Die Änderung eines Bereichsfilters kann zum Löschen von Objekten im Zielverzeichnis führen. Seien Sie vorsichtig, bevor Sie Änderungen an den Objektbereichen vornehmen. Es wird empfohlen, zunächst einen Stagingserver zu ändern, bevor Sie Änderungen am aktiven Server vornehmen.
- Führen Sie eine Vorschau für ein einzelnes Objekt aus, wie im Abschnitt [Überprüfen der Synchronisierungsregel](#validate-sync-rule) beschrieben, nachdem Sie eine neue Regel hinzugefügt haben.
- Führen Sie eine vollständige Synchronisierung durch, nachdem Sie eine neue Regel hinzugefügt oder eine benutzerdefinierte Synchronisierungsregel geändert haben. Diese Synchronisierung wendet neue Regeln auf alle Objekte an.

## <a name="change-attribute-flow"></a>Attributfluss ändern
Es gibt drei verschiedene Szenarien für die Änderung des Attributflusses:
- Hinzufügen eines neuen Attributs
- Überschreiben des Werts eines vorhandenen Attributs
- Festlegen, dass ein vorhandenes Attribut nicht synchronisiert wird

Diese Vorgänge können ausgeführt werden, ohne Standardregeln zu ändern.

### <a name="add-a-new-attribute"></a>Hinzufügen eines neuen Attributs
Wenn Sie feststellen, dass ein Attribut nicht aus dem Quellverzeichnis in das Zielverzeichnis fließt, verwenden Sie [Azure AD Connect-Synchronisierung: Verzeichniserweiterungen](how-to-connect-sync-feature-directory-extensions.md), um dieses Problem zu beheben.

Wenn die Erweiterungen nicht funktionieren, versuchen Sie, zwei neue Synchronisierungsregeln hinzuzufügen (dies wird in den folgenden Abschnitten beschrieben).


#### <a name="add-an-inbound-sync-rule"></a>Hinzufügen einer Synchronisierungsregel für eingehenden Datenverkehr
Eine Synchronisierungsregel für eingehenden Datenverkehr bedeutet, dass die Quelle für das Attribut ein Connectorbereich und das Ziel der Metaverse ist. Wenn beispielsweise ein neuer Attributfluss vom lokalen Active Directory nach Azure Active Directory eingerichtet werden soll, erstellen Sie eine neue Synchronisierungsregel für eingehenden Datenverkehr. Starten Sie den **Synchronisierungsregel-Editor**, wählen Sie **Eingehend** als Richtung aus, und wählen Sie **Neue Regel hinzufügen** aus. 

 !Synchronization Rules Editor](media/how-to-connect-fix-default-rules/default3a.png)

Benennen Sie die Regel nach Ihren eigenen Namenskonventionen. Hier verwenden wir **Custom In from AD - User**. Das heißt, dass es sich um eine benutzerdefinierte Regel für eingehenden Datenverkehr aus dem Active Directory-Connectorbereich zum Metaverse handelt.   

 ![Erstellen einer Synchronisierungsregel für eingehende Daten](media/how-to-connect-fix-default-rules/default3b.png)

Geben Sie eine eigene Beschreibung für die Regel an, um die künftige Pflege der Regel zu erleichtern. Die Beschreibung kann beispielsweise das Ziel der Regel und ihren Zweck enthalten.

Wählen Sie in den Feldern **Verbundenes System**, **Objekttyp des verbundenen Systems** und **Metaverse-Objekttyp** die entsprechenden Optionen aus.

Geben Sie den Rangfolgenwert zwischen 0 und 99 an (je niedriger die Zahl, desto höher die Rangfolge). Übernehmen Sie für die Felder **Tag**, **Kennwortsynchronisierung aktivieren** und **Deaktiviert** die Standardwerte.

Lassen Sie **Bereichsfilter** leer. Das heißt, dass die Regel auf alle Objekte angewendet wird, die zwischen dem verbundenen Active Directory-System und dem Metaverse eingebunden sind.

Lassen Sie **Verknüpfungsregeln** leer. Das heißt, dass diese Regel die Verknüpfungsbedingung verwendet, die in der unbearbeiteten Standardregel definiert ist. Dies ist ein weiterer Grund dafür, dass die unbearbeitete Standardregel nicht deaktiviert oder gelöscht werden sollte. Ist keine Verknüpfungsbedingung vorhanden, erfolgt kein Attributfluss. 

Fügen Sie die entsprechenden Transformationen für das Attribut hinzu. Sie können eine Konstante zuweisen, um einen den Fluss eines konstanten Werts zum Zielattribut einzurichten. Sie können eine direkte Zuordnung zwischen dem Quell- oder Zielattribut verwenden. Sie können aber auch einen Ausdruck für das Attribut verwenden. Hier finden Sie verschiedene [expression-Funktionen](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference), die Sie verwenden können.

#### <a name="add-an-outbound-sync-rule"></a>Hinzufügen einer Synchronisierungsregel für ausgehenden Datenverkehr
Um das Attribut mit dem Zielverzeichnis zu verknüpfen, müssen Sie eine Ausgangsregel erstellen. Das heißt, dass der Metaverse die Quelle und das verbundene System das Ziel ist. Um eine Ausgangsregel zu erstellen, starten Sie den **Synchronisierungsregel-Editor**, ändern Sie die **Richtung** in **Ausgehend**, und wählen Sie **Neue Regel hinzufügen** aus. 

![Synchronisierungsregel-Editor](media/how-to-connect-fix-default-rules/default3c.png)

Wie bei der Regel für eingehenden Datenverkehr können Sie zum Benennen der Regel Ihre eigene Namenskonvention verwenden. Wählen Sie das **verbundene System** als Azure AD-Mandanten und das verbundene Systemobjekt aus, auf das Sie den Attributwert festlegen möchten. Legen Sie die Rangfolge auf einen Wert zwischen 0 und 99 fest. 

![Erstellen einer Synchronisierungsregel für ausgehende Daten](media/how-to-connect-fix-default-rules/default3d.png)

Lassen Sie die Felder **Bereichsfilter** und **Verknüpfungsregeln** leer. Geben Sie die Transformation als Konstante, Direkt oder Ausdruck ein. 

Nun wissen Sie, wie Sie ein neues Attribut für ein Benutzerobjekt von Active Directory nach Azure Active Directory fließen lassen. Sie können diese Schritte verwenden, um jedem beliebigen Attribut jedes beliebigen Objekts eine Quelle und ein Ziel zuzuordnen. Weitere Informationen finden Sie unter [Erstellen benutzerdefinierter Synchronisierungsregeln](how-to-connect-create-custom-sync-rule.md) und [Vorbereiten der Bereitstellung von Benutzern](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization).

### <a name="override-the-value-of-an-existing-attribute"></a>Überschreiben des Werts eines vorhandenen Attributs
Eventuell möchten Sie den Wert eines Attributs überschreiben, das bereits zugeordnet wurde. Wenn ein Attribut in Azure AD beispielsweise immer auf einen NULL-Wert festgelegt sein soll, erstellen Sie einfach nur eine eingehende Regel. Lassen Sie den konstanten Wert `AuthoritativeNull` zum Zielattribut fließen. 

>[!NOTE] 
> Verwenden Sie in diesem Fall `AuthoritativeNull` anstelle von `Null`. Grund hierfür ist, dass ein Wert ungleich NULL einen NULL-Wert auch dann ersetzt, wenn er eine geringere Rangfolge (einen höheren Nummernwert in der Regel) aufweist. `AuthoritativeNull` hingegen wird von anderen Regeln nicht durch einen Wert ungleich NULL ersetzt. 

### <a name="dont-sync-existing-attribute"></a>Vorhandenes Attribut nicht synchronisieren
Wenn Sie ein Attribut von der Synchronisierung ausschließen möchten, verwenden Sie die Attributfilterfunktion in Azure AD Connect. Starten Sie **Azure AD Connect** über das Desktopsymbol, und wählen Sie dann **Synchronisierungsoptionen anpassen** aus.

![Optionen für weitere Aufgaben in Azure AD Connect](media/how-to-connect-fix-default-rules/default4.png)

 Stellen Sie sicher, dass **Azure AD-App und Attributfilterung** ausgewählt ist, und wählen Sie **Weiter** aus.

![Optionale Funktionen für Azure AD Connect](media/how-to-connect-fix-default-rules/default5.png)

Löschen Sie die Attribute, die Sie von der Synchronisierung ausschließen möchten.

![Azure AD Connect-Attribute](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>Bereichsfilter ändern
AADSync verarbeitet die meisten Objekte. Sie können den Bereich der Objekte eingrenzen und die Anzahl der zu exportierenden Objekte reduzieren, ohne dass Sie die unbearbeiteten Standardregeln für die Synchronisierung ändern. 

Grenzen Sie mit einer der folgenden Methoden den Bereich der zu synchronisierenden Objekte ein:

- cloudFiltered-Attribut
- Filtern von Organisationseinheiten

Wenn Sie den Bereich der synchronisierten Benutzer verkleinern, wird auch die Kennworthashsynchronisierung für die herausgefilterten Benutzer beendet. Wenn die Objekte nach dem Eingrenzen des Bereichs bereits synchronisiert werden, werden die herausgefilterten Objekte aus dem Zielverzeichnis gelöscht. Daher sollten Sie beim Anpassen des Bereichs mit Umsicht vorgehen.

>[!IMPORTANT] 
> Das Vergrößern des von Azure AD Connect konfigurierten Bereichs von Objekten wird nicht empfohlen. Dies würde es dem Microsoft-Supportteam erschweren, die Anpassungen zu verstehen. Wenn Sie den Objektbereich vergrößern müssen, können Sie die vorhandene Regel bearbeiten, klonen und die ursprüngliche Regel deaktivieren. 

### <a name="cloudfiltered-attribute"></a>cloudFiltered-Attribut
Dieses Attribut kann in Active Directory nicht festgelegt werden. Legen Sie den Wert dieses Attributs fest, indem Sie eine neue Regel für eingehenden Datenverkehr hinzufügen. Anschließend können Sie **Transformation** und **Ausdruck** verwenden, um dieses Attribut im Metaverse festzulegen. Im folgenden Beispiel sollen alle Benutzer, deren Abteilungsname mit **HRD** (ohne Berücksichtigung der Groß- und Kleinschreibung) beginnt, nicht synchronisiert werden:

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

Zunächst wird die Abteilung von der Quelle (Active Directory) in Kleinbuchstaben umgewandelt. Anschließend wurden mit der `Left`-Funktion die ersten drei Zeichen erfasst und mit `hrd` verglichen. Bei Übereinstimmung wird der Wert auf `True` festgelegt, andernfalls `NULL`. Wenn der Wert auf NULL festgelegt wird, kann eine andere Regel mit geringerer Rangfolge (höherem Zahlenwert) den Wert mit einer anderen Bedingung überschreiben. Führen Sie eine Vorschau für ein Objekt aus, um die Synchronisierungsregel zu überprüfen, wie im Abschnitt [Überprüfen der Synchronisierungsregel](#validate-sync-rule) beschrieben.

![Optionen zum Erstellen einer Synchronisierungsregel für eingehende Daten](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>Filtern von Organisationseinheiten
Sie können eine oder mehrere Organisationseinheiten (OUs) erstellen und die Objekte verschieben, die nicht mit diesen Organisationseinheiten synchronisiert werden sollen. Konfigurieren Sie anschließend die Filterung von Organisationseinheiten in Azure AD Connect. Starten Sie **Azure AD Connect** über das Desktopsymbol, und wählen Sie die folgenden Optionen aus. Sie können die Filterung der Organisationseinheiten auch zum Zeitpunkt der Installation von Azure AD Connect konfigurieren. 

![Zusätzliche Aufgaben für Azure AD Connect](media/how-to-connect-fix-default-rules/default8.png)

Folgen Sie den Anweisungen des Assistenten, und deaktivieren Sie dann die Organisationseinheiten, die nicht synchronisiert werden sollen.

![Optionen für das Filtern von Azure AD Connect-Domänen -Organisationseinheiten](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>Verknüpfungsbedingung ändern
Verwenden Sie die standardmäßigen, von Azure AD Connect konfigurierten Verknüpfungsbedingungen. Wenn Sie die standardmäßigen Verknüpfungsbedingungen ändern, wird es für das Microsoft-Supportteam schwieriger, die Anpassungen zu verstehen und das Produkt zu unterstützen.

## <a name="validate-sync-rule"></a>Überprüfen der Synchronisierungsregel
Sie können die neu hinzugefügte Synchronisierungsregel mit der Previewfunktion überprüfen, ohne den gesamten Synchronisierungszyklus auszuführen. Wählen Sie in Azure AD Connect die Option **Synchronisierungsdienst** aus.

![Azure AD Connect mit hervorgehobener Option für Synchronisierungsdienst](media/how-to-connect-fix-default-rules/default10.png)

Wählen Sie **Metaversesuche** aus. Wählen Sie als Bereichsobjekt **Person** und anschließend **Klausel hinzufügen** aus, und geben Sie Ihre Suchkriterien an. Klicken Sie dann auf **Suchen**, und doppelklicken Sie auf das Objekt in den Suchergebnissen. Führen Sie Import und Synchronisierung für die Gesamtstruktur aus, bevor Sie diesen Schritt ausführen, um sicherzustellen, dass die Daten in Azure AD Connect für dieses Objekt auf dem neuesten Stand sind.

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

Wählen Sie in **Metaverse-Objekteigenschaften** die Option **Connectors** aus, wählen Sie das Objekt im entsprechenden Connector (Gesamtstruktur) aus, und wählen Sie **Eigenschaften** aus.

![Metaverse Object Properties](media/how-to-connect-fix-default-rules/default12.png)

Wählen Sie **Vorschau** aus.

![Eigenschaften der Objekte des Connectorbereichs](media/how-to-connect-fix-default-rules/default13a.png)

Wählen Sie im Vorschaufenster **Vorschau generieren** und **Importattributfluss** im linken Bereich aus.

![Vorschau](media/how-to-connect-fix-default-rules/default14.png)
 
Hier stellen Sie fest, dass die neu hinzugefügte Regel für das Objekt ausgeführt wird und dass das Attribut `cloudFiltered` auf „True“ festgelegt wurde.

![Vorschau](media/how-to-connect-fix-default-rules/default15a.png)
 
Exportieren Sie beide Regeln separat als Textdateien, um die geänderte Regel mit der Standardregel zu vergleichen. Die Regeln werden als PowerShell-Skriptdatei exportiert. Sie können sie mit einem beliebigen Dateivergleichstool (z.B. WinDiff) vergleichen, um die Änderungen sichtbar zu machen. 
 
Beachten Sie, dass in der geänderten Regel das Attribut `msExchMailboxGuid` in den Typ **Ausdruck** geändert wurde und nicht den Typ **Direkt** aufweist. Außerdem wurde der Wert in **NULL** und die Option **ExecuteOnce** geändert. Unterschiede „Identified“ und „Precedence“ können ignoriert werden. 

![Ausgabe des Tools WinDiff](media/how-to-connect-fix-default-rules/default17.png)
 
Um die Regeln zu korrigieren und sie wieder auf die Standardeinstellungen zurückzusetzen, löschen Sie die geänderte Regel, und aktivieren Sie die Standardregel. Achten Sie dabei darauf, dass die gewünschte Anpassung nicht verloren geht. Wenn Sie bereit sind, führen Sie die **vollständige Synchronisierung** aus.

## <a name="next-steps"></a>Nächste Schritte
- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Express-Einstellungen](how-to-connect-install-express.md)
- [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md)



