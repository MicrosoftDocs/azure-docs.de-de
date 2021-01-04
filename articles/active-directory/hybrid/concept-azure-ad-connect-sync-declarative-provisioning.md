---
title: 'Azure AD Connect: Grundlegendes zur deklarativen Bereitstellung | Microsoft-Dokumentation'
description: Erklärt das Konfigurationsmodell für die deklarative Bereitstellung in Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59dc94e37dfa1ef8b0b079bf5d78d0504e0cb8c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313619"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect-Synchronisierung: Grundlegendes zur deklarativen Bereitstellung
In diesem Thema wird das Konfigurationsmodell in Azure AD Connect beschrieben. Dieses Modell, die „deklarative Bereitstellung“, ermöglicht es Ihnen, Konfigurationsänderungen einfach vorzunehmen. In diesem Thema sind auch viele Punkte für fortgeschrittene Benutzer beschrieben, die für die meisten Benutzerszenarien nicht erforderlich sind.

## <a name="overview"></a>Übersicht
Deklarative Bereitstellung bedeutet, Objekte aus einem mit der Quelle verbundenen Verzeichnis zu verarbeiten und zu bestimmen, wie das Objekt und die Attribute von einer Quelle in ein Ziel transformiert werden. Ein Objekt wird in einer Synchronisierungspipeline verarbeitet. Die Pipeline ist für ein- und ausgehende Regeln identisch. Eine eingehende Regel wird von einem Connectorbereich an die Metaverse übergeben, und eine ausgehende Regel wird von der Metaverse an einen Connectorbereich übergeben.

![Diagramm mit einem Beispiel für eine Synchronisierungspipeline.](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

Die Pipeline enthält mehrere unterschiedliche Module. Jede ist für ein Konzept bei der Objektsynchronisierung verantwortlich.

![Diagramm der Module in der Pipeline.](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Quelle: das Quellobjekt
* [Bereich](#scope): Sucht nach allen Synchronisierungsregeln innerhalb des Bereichs
* [Verbindung](#join): Bestimmt die Beziehung zwischen Connectorbereich und Metaverse
* Transformation: Berechnet die Transformation der Attribute und den Datenfluss
* [Rangfolge](#precedence): Löst in Konflikt stehende Attributbeiträge
* Ziel: das Zielobjekt

## <a name="scope"></a>`Scope`
Das Bereichsmodul wertet ein Objekt aus und bestimmt die Regeln, die sich innerhalb des Bereichs befinden und verarbeitet werden sollen. Abhängig von den Attributwerten des Objekts werden verschiedene Synchronisierungsregeln für den Bereich ausgewertet. Beispielsweise verfügt ein deaktivierter Benutzer ohne Exchange-Postfach über andere Regeln als ein aktivierter Benutzer mit einem Postfach.  
![Diagramm des Bereichsmoduls für ein Objekt.](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

Der Bereich wird als Gruppen und Klauseln definiert. Die Klauseln liegen innerhalb einer Gruppe. Ein logisches AND wird zwischen allen Klauseln in einer Gruppe verwendet. Zum Beispiel (department =IT AND country = Denmark). Ein logisches OR wird zwischen Gruppen verwendet.

![`Scope`](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Der Bereich in der folgenden Abbildung wird gelesen als (department = IT AND country = Denmark) OR (country=Sweden). Wenn entweder Gruppe 1 oder Gruppe 2 wahr ist, befindet sich die Regel im Geltungsbereich.

Das Bereichsmodul unterstützt die folgenden Vorgänge.

| Vorgang | BESCHREIBUNG |
| --- | --- |
| EQUAL, NOTEQUAL |Ein Vergleich von Zeichenfolgen, der auswertet, ob der Wert und der Attributwert gleich sind. Informationen zu mehrwertigen Attributen finden Sie unter ISIN und ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Ein Vergleich von Zeichenfolgen, der auswertet, ob der Wert kleiner als der Attributwert ist. |
| CONTAINS, NOTCONTAINS |Ein Vergleich von Zeichenfolgen, der auswertet, ob sich der Wert innerhalb des Attributwerts befindet. |
| STARTSWITH, NOTSTARTSWITH |Ein Vergleich von Zeichenfolgen, der auswertet, ob sich der Wert am Anfang des Attributwerts befindet. |
| ENDSWITH, NOTENDSWITH |Ein Vergleich von Zeichenfolgen, der auswertet, ob sich der Wert am Ende des Attributwerts befindet. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Ein Vergleich von Zeichenfolgen, der auswertet, ob der Wert größer als der Attributwert ist. |
| ISNULL, ISNOTNULL |Wertet aus, ob das Attribut im Objekt nicht vorhanden ist. Wenn das Attribut nicht vorhanden und daher Null ist, liegt die Regel innerhalb des Bereichs. |
| ISIN, ISNOTIN |Wertet aus, ob der Wert im definierten Attribut vorhanden ist. Dieser Vorgang ist die mehrwertige Entsprechung von EQUAL und NOTEQUAL. Das Attribut sollte ein mehrwertiges Attribut sein. Wenn der Wert in einem der Attributwerte gefunden wird, befindet sich die Regel innerhalb des Bereichs. |
| ISBITSET, ISNOTBITSET |Wertet aus, ob ein bestimmtes Bit festgelegt ist. Kann beispielsweise die Bits in „userAccountControl“ auswerten, um zu sehen, ob ein Benutzer aktiviert oder deaktiviert ist. |
| ISMEMBEROF, ISNOTMEMBEROF |Der Wert sollte einen DN in Bezug auf eine Gruppe im Connectorbereich enthalten. Wenn das Objekt Mitglied der festgelegten Gruppe ist, liegt die Regel innerhalb des Bereichs. |

## <a name="join"></a>Join
Das Verbindungsmodul in der Synchronisierungspipeline ist dafür zuständig, die Beziehung zwischen dem Objekt in der Quelle und dem Objekt im Ziel zu finden. Bei einer eingehenden Regel ist diese Beziehung ein Objekt in einem Connectorbereich, das eine Beziehung zu einem Objekt in der Metaverse herstellt.  
![Verbindung zwischen Connectorbereich und Metaverse](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
Das Ziel besteht darin, festzustellen, ob sich in der Metaverse bereits ein Objekt befindet, das von einem anderen Connector erstellt wurde und mit dem eine Zuordnung hergestellt werden soll. Z.B. in einer Kontoressourcengesamtstruktur muss der Benutzer aus der Kontogesamtstruktur mit dem Benutzer aus der Ressourcengesamtstruktur verbunden werden.

Verbindungen werden meistens für eingehende Regeln verwendet, um die Objekte des Connectorbereichs mit dem gleichen Metaverse-Objekt zu verbinden.

Verbindungen werden als eine oder mehrere Gruppen definiert. Innerhalb einer Gruppe haben Sie Klauseln. Ein logisches AND wird zwischen allen Klauseln in einer Gruppe verwendet. Ein logisches OR wird zwischen Gruppen verwendet. Die Gruppen werden in der Reihenfolge von oben nach unten verarbeitet. Wenn eine Gruppe genau eine Übereinstimmung mit einem Objekt im Ziel gefunden hat, werden keine anderen Verbindungsregeln ausgewertet. Wenn keine oder mehrere Objekte gefunden werden, wird die Verarbeitung bis zur nächsten Regelgruppe fortgesetzt. Aus diesem Grund müssen die Regeln so erstellt werden, dass die expliziten Regel zuerst und die ungenauen Regeln zuletzt kommen.  
![Verbindungsdefinition](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Die Verbindungen in diesem Bild werden von oben nach unten verarbeitet. Zuerst prüft die Synchronisierungspipeline, ob eine Übereinstimmung für eine EmployeeID vorliegt. Andernfalls prüft die zweite Regel, ob die Objekte mithilfe des Kontonamens verbunden werden können. Liegt hier ebenfalls keine Übereinstimmung vor, prüft die dritte und letzte Regel eine etwas ungenauere Übereinstimmung unter Verwendung des Benutzernamens.

Wenn alle Verbindungsregeln ausgewertet wurden und keine Übereinstimmung vorliegt, wird der **Verknüpfungstyp** auf der Seite **Beschreibung** verwendet. Wenn für diese Einstellung **Bereitstellen** festgelegt ist, wird im Ziel ein neues Objekt erstellt.  
![Screenshot des geöffneten Dropdownmenüs „Verknüpfungstyp“.](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Ein Objekt darf nur über eine einzelne Synchronisierungsregel mit Verbindungsregeln im Bereich verfügen. Wenn mehrere Synchroniserungsregeln vorhanden sind und „Join“ (Verbindung) definiert ist, tritt ein Fehler auf. Die Rangfolge wird nicht zum Lösen von Verbindungskonflikten verwenden. Ein Objekt benötigt eine Verbindungsregel im Bereich, damit der Datenfluss der Attribute in die gleiche ein-/ausgehende Richtung erfolgt. Wenn der Datenfluss der Attribute sowohl ein- als auch ausgehend zum gleichen Objekt erfolgen soll, benötigen Sie sowohl eine ein- als auch eine ausgehende Synchronisierungsregel mit der Verbindung.

Die ausgehende Verbindung weist ein besonderes Verhalten auf, wenn sie versucht, ein Objekt für einen Zielconnectorbereich bereitzustellen. Das DN-Attribut wird verwendet, um zuerst eine Reverseverbindung zu versuchen. Wenn sich im Zielconnectorbereich mit dem gleichen DN bereits ein Objekt befindet, werden die Objekte verbunden.

Das Verbindungsmodul wird nur einmal ausgewertet, wenn eine neue Synchronisierungsregel hinzugefügt wird. Wenn ein Objekt verbunden wurde, wird es auch nicht getrennt, wenn die Verbindungskriterien nicht mehr erfüllt sind. Wenn Sie ein Objekt trennen möchten, muss die Synchronisierungsregel, die die Objekte verbunden hat, den Gültigkeitsbereich verlassen.

### <a name="metaverse-delete"></a>Löschen der Metaverse
Ein Metaverse-Objekt besteht, solange sich eine Synchronisierungsregel im Bereich befindet, für die der **Verknüpfungstyp** auf **Bereitstellen** oder **StickyJoin** (Fixierte Verbindung) festgelegt ist. Ein „StickyJoin“ (Fixierte Verbindung) wird verwendet, wenn ein Connector kein neues Objekt in der Metaverse bereitstellen darf, aber wenn er verbunden wurde, muss er vor dem Löschen des Metaverse-Objekts in der Quelle gelöscht werden.

Wenn ein Metaverse-Objekt gelöscht wird, werden alle Objekte, die einer ausgehenden, für **Bereitstellen** markierten Synchronisierungsregel zugeordnet sind, zum Löschen markiert.

## <a name="transformations"></a>Transformationen
Transformationen werden verwendet, um festzulegen, wie der Datenfluss von Attributen von der Quelle zum Ziel erfolgen soll. Für die Datenflüsse gibt es die folgenden **FlowTypes**(Durchflusstypen): Direkt, Konstant oder Expression (Ausdruck). Bei einem direkten Durchfluss wird der Attributwert im vorliegenden Zustand ohne weitere Änderungen übertragen. Ein konstanter Wert legt den angegebenen Wert fest. Ein Ausdruck verwendet die deklarative Bereitstellungsausdruckssprache, um auszudrücken, wie die Transformation aussehen soll. Weitere Informationen zur Ausdruckssprache finden Sie im Thema [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

![Bereitstellen oder verbinden](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Das Kontrollkästchen **Apply once** (Einmal anwenden) definiert, dass das Attribut nur festgelegt wird wenn das Objekt erstmals erstellt wird. Diese Konfiguration kann beispielsweise verwendet werden, um ein anfängliches Kennwort für ein neues Benutzerobjekt festzulegen.

### <a name="merging-attribute-values"></a>Zusammenführen von Attributwerten
In den Attributflüssen ist eine Einstellung verfügbar, mit der Sie ermitteln können, ob mehrwertige Attribute aus mehreren verschiedenen Connectors zusammengeführt werden sollten. Beim Standardwert **Update** wird die Synchronisierungsregel mit der höchsten Rangfolge angewendet.

![Screenshot des Abschnitts „Transformationen hinzufügen“ mit dem geöffneten Dropdownmenü „Zusammenführungstypen“.](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Weitere Einstellungen sind **Merge** und **MergeCaseInsensitive**. Mit diesen Optionen können Sie Werte aus unterschiedlichen Quellen zusammenführen. Sie können beispielsweise das Attribut „member“ oder „proxyAddresses“ aus mehreren unterschiedlichen Gesamtstrukturen zusammenführen. Wenn Sie diese Option verwenden, müssen alle Synchronisierungsregeln im Bereich für ein Objekt denselben Zusammenführungstyp aufweisen. Es ist nicht möglich, den Zusammenführungstyp **Update** für einen Connector und **Merge** für einen anderen Connector festzulegen. Wenn Sie es versuchen, wird eine Fehlermeldung ausgegeben.

Der Unterschied zwischen **Merge** und **MergeCaseInsensitive** ist die Verarbeitung doppelter Attributwerte. Das Synchronisierungsmodul stellt sicher, dass keine doppelten Werte in das Zielattribut eingefügt werden. Bei **MergeCaseInsensitive** werden auch keine doppelten Werte eingefügt, bei denen sich nur die Groß-/Kleinschreibung unterscheidet. Beispielsweise sollten SMTP:bob@contoso.com und smtp:bob@contoso.com nicht gleichzeitig im Zielattribut vorhanden sein. **Merge** überprüft nur die genauen Werte. Daher ist es möglich, dass mehrere Werte vorhanden sind, bei denen sich nur die Groß-/Kleinschreibung unterscheidet.

Die Option **Replace** entspricht **Update**, wird aber nicht verwendet.

### <a name="control-the-attribute-flow-process"></a>Steuern des Attributflussprozesses
Wenn mehrere eingehende Synchronisierungsregeln konfiguriert sind, die zu demselben Metaverseattribut beitragen, bestimmt die Rangfolge, welche Regel angewendet wird. Die Synchronisierungsregel mit der höchsten Rangfolge (dem niedrigsten numerischen Wert) trägt den Wert bei. Gleiches gilt für ausgehende Regeln. Die Synchronisierungsregel mit der höchsten Rangfolge trägt den Wert zum verbundenen Verzeichnis bei.

In einigen Fällen trägt die Synchronisierungsregel keinen Wert bei, sondern bestimmt, wie andere Regeln sich verhalten sollen. In diesem Fall werden einige spezielle Literale verwendet.

Bei eingehenden Synchronisierungsregeln kann das Literal **NULL** verwendet werden, um anzugeben, dass der Attributfluss keinen Wert beiträgt. Eine andere Regel mit niedrigerer Rangfolge kann einen Wert beitragen. Wenn keine Regel einen Wert beiträgt, wird das Metaverseattribut entfernt. Wenn bei einer ausgehenden Regel **NULL** der endgültige Wert nach der Verarbeitung aller Synchronisierungsregeln ist, wird der Wert im verbundenen Verzeichnis entfernt.

Das Literal **AuthoritativeNull** ähnelt **NULL**, jedoch mit dem Unterschied, dass keine Regeln mit niedrigerer Rangfolge einen Wert beitragen können.

Ein Attributfluss kann auch **IgnoreThisFlow** verwenden. Dieses Literal ähnelt NULL, da es angibt, dass kein beizutragender Wert vorhanden ist. Der Unterschied besteht darin, dass ein bereits vorhandener Wert im Ziel nicht entfernt wird. Es ist, als hätte es den Attributfluss nie gegeben.

Beispiel:

In *Out to AD - User Exchange hybrid* (Ausgehend nach AD – Benutzer Exchange Hybrid) finden Sie folgenden Fluss:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Dieser Ausdruck ist wie folgt zu lesen: Wenn sich das Postfach des Benutzers in Azure AD befindet, fließt das Attribut von Azure AD nach AD. Andernfalls fließt nichts zurück nach Active Directory. In diesem Fall wird der vorhandene Wert in AD beibehalten.

### <a name="importedvalue"></a>ImportedValue
Die Funktion „ImportedValue“ unterscheidet sich von allen anderen Funktionen, da der Attributname in Anführungszeichen statt in eckige Klammern eingeschlossen werden muss:  
`ImportedValue("proxyAddresses")`.

Üblicherweise verwendet ein Attribut während der Synchronisierung den erwarteten Wert, selbst wenn er noch nicht exportiert wurde oder während des Exports ein Fehler empfangen wurde („top of the tower“). Bei einer eingehenden Synchronisierung wird vorausgesetzt, dass ein Attribut, das ein verbundenes Verzeichnis noch nicht erreicht hat, dieses schließlich erreicht. In einigen Fällen ist es wichtig, nur Werte zu synchronisieren, die vom verbundenen Verzeichnis bestätigt wurden („hologram and delta import tower“).

Ein Beispiel für diese Funktion finden Sie in der vordefinierten Synchronisierungsregel *In from AD – User Common from Exchange*(Eingehend von AD – Benutzer allgemein aus Exchange). In Hybrid Exchange sollte der von Exchange Online hinzugefügte Wert nur synchronisiert werden, wenn der erfolgreiche Export des Werts bestätigt wurde:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Rangfolge
Wenn mehrere Synchronisierungsregeln versuchen, den gleichen Attributwert für das Ziel beizutragen, wird mit dem Rangfolgenwert bestimmt, welcher Wert Vorrang hat. Die Regel mit der höchsten Rangfolge (dem niedrigsten numerischen Wert) trägt in einem Konflikt das Attribut bei.

![Zusammenführungstypen](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Diese Sortierung kann verwendet werden, um genauere Attributflüsse für eine kleine Teilmenge an Objekten zu definieren. Die vordefinierten Regeln stellen beispielsweise sicher, dass Attribute eines aktivierten Kontos (**User AccountEnabled**) Vorrang vor anderen Konten haben.

Die Rangfolge kann zwischen Connectors definiert werden. Dadurch tragen Connectors mit besseren Daten zuerst die Werte bei.

### <a name="multiple-objects-from-the-same-connector-space"></a>Mehrere Objekte vom gleichen Connectorbereich
Wenn Sie über mehrere Objekte im gleichen Connectorbereich mit demselben Metaverse-Objekt verbunden haben, muss die Rangfolge angepasst werden. Wenn mehrere Objekte im Gültigkeitsbereich der gleichen Synchronisierungsregel liegen, kann das Synchronisierungsmodul die Rangfolge nicht bestimmen. Es ist nicht eindeutig, welches Quellobjekt den Wert zur Metaverse beitragen soll. Diese Konfiguration wird als mehrdeutig gemeldet, auch wenn die Attribute in der Quelle den gleichen Wert aufweisen.  
![Diagramm, in dem mehrere Objekte mit demselben Metaverse-Objekt verbunden sind, mit einer transparenten roten X-Überlagerung. ](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

In diesem Szenario müssen Sie den Bereich der Synchronisierungsregeln ändern, sodass sich verschiedene Synchronisierungsregeln im Bereich der Quellobjekte befinden. Damit können Sie eine andere Rangfolge definieren.  
![Mehrere Objekte mit demselben Metaverse-Objekt verbunden](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur Ausdruckssprache finden Sie unter [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Unter [Grundlegendes zur Standardkonfiguration](concept-azure-ad-connect-sync-default-configuration.md)wird die standardmäßige Verwendung der deklarativen Bereitstellung veranschaulicht.
* Unter [Ändern der Standardkonfiguration](how-to-connect-sync-change-the-configuration.md)wird beschrieben, wie Sie mit der deklarativen Bereitstellung eine praktische Änderung vornehmen.
* Unter [Grundlegendes zu Benutzern und Kontakten](concept-azure-ad-connect-sync-user-and-contacts.md)erfahren Sie, wie Benutzer und Kontakte zusammenarbeiten.

**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)

**Referenzthemen**

* [Azure AD Connect-Synchronisierung: Funktionsreferenz](reference-connect-sync-functions-reference.md)
