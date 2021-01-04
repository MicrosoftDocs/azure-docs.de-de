---
title: 'Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Standardkonfiguration der Azure AD Connect-Synchronisierung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996626"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration
In diesem Artikel werden die standardmäßigen Konfigurationsregeln erläutert. Er dokumentiert die Regeln und deren Auswirkungen auf die Konfiguration. Außerdem wird die Standardkonfiguration der Azure AD Connect-Synchronisierung beschrieben. Der Leser soll verstehen, wie das als deklarative Bereitstellung bezeichnete Konfigurationsmodell in einem realistischen Beispiel funktioniert. Dieser Artikel setzt voraus, dass die Azure AD Connect-Synchronisierung bereits mit dem Installations-Assistenten installiert und konfiguriert wurde.

Informationen zu den Details des Konfigurationsmodells finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Standardregeln für die lokale Bereitstellung zu Azure AD
Die Standardkonfiguration kann folgende Ausdrücke enthalten.

### <a name="user-out-of-box-rules"></a>Standardregeln für Benutzer
Diese Regeln gelten auch für den Objekttyp „iNetOrgPerson“.

Ein Benutzerobjekt muss Folgendes erfüllen, um synchronisiert zu werden:

* Es muss über ein sourceAnchor-Element verfügen.
* Nach dem Erstellen des Objekts in Azure AD kann das sourceAnchor-Element nicht mehr geändert werden. Sollte der Wert lokal geändert werden, wird das Objekt nicht mehr synchronisiert, bis das sourceAnchor-Element wieder auf den vorherigen Wert zurückgesetzt wurde.
* Das accountEnabled-Attribut (userAccountControl) muss aufgefüllt sein. Bei einer lokalen Active Directory-Instanz ist dieses Attribut immer vorhanden und aufgefüllt.

Die folgenden Benutzerobjekte werden **nicht** mit Azure AD synchronisiert:

* `IsPresent([isCriticalSystemObject])`. Sorgt dafür, dass viele vorkonfigurierte Objekte in Active Directory (wie etwa das integrierte Administratorkonto) nicht synchronisiert werden.
* `IsPresent([sAMAccountName]) = False`. Sorgt dafür, dass Benutzerobjekte ohne sAMAccountName-Attribut nicht synchronisiert werden. Dieser Fall gilt in der Praxis nur für Domänen, für die ein Upgrade von NT4 durchgeführt wurde.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Verhindert die Synchronisierung des Dienstkontos, das von der Azure AD Connect-Synchronisierung und von älteren Versionen verwendet wird.
* Verhindert die Synchronisierung von Exchange-Konten, die nicht für Exchange Online geeignet sind.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Verhindert die Synchronisierung von Objekten, die nicht für Exchange Online geeignet sind.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Diese Bitmaske (&H21C07000) filtert folgende Objekte heraus:
  * E-Mail-aktivierter öffentlicher Ordner (ab Version 1.1.524.0 als Vorschauversion)
  * Postfach der Systemaufsicht
  * Postfachdatenbankpostfach (Systempostfach)
  * Universelle Sicherheitsgruppe (gilt nicht für Benutzer; nur aus Legacygründen vorhanden)
  * Nicht universelle Sicherheitsgruppe (gilt nicht für Benutzer; nur aus Legacygründen vorhanden)
  * Postfachplan
  * Discoverypostfach
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Verhindert die Synchronisierung von Replikationsopferobjekten.

Geltende Attributregeln:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. Bei verknüpften Postfächern wird das sourceAnchor-Attribut nicht bereitgestellt. Falls ein verknüpftes Postfach gefunden wird, wird davon ausgegangen, dass das eigentliche Konto später hinzugefügt wird.
* Exchange-bezogene Attribute werden nur synchronisiert, wenn das Attribut **mailNickName** über einen Wert verfügt.
* Sollten mehrere Gesamtstrukturen vorhanden sein, werden die Attribute in der folgenden Reihenfolge verwendet:
  1. Anmeldungsbezogene Attribute (z.B. „userPrincipalName“) werden aus der Gesamtstruktur mit einem aktivierten Konto bereitgestellt.
  2. Attribute aus einer Exchange-GAL (Global Address List, globale Adressliste) werden aus der Gesamtstruktur mit einem Exchange-Postfach bereitgestellt.
  3. Wird kein Postfach gefunden, können diese Attribute aus einer beliebigen Gesamtstruktur stammen.
  4. Exchange-bezogene Attribute (in der GAL nicht sichtbare technische Attribute) werden aus der Gesamtstruktur bereitgestellt, für die Folgendes gilt: `mailNickname ISNOTNULL`.
  5. Sollte eine dieser Regeln von mehreren Gesamtstrukturen erfüllt werden, ist die Erstellungsreihenfolge (Datum/Uhrzeit) der Connectors (Gesamtstrukturen) ausschlaggebend. Die erste verbundene Gesamtstruktur wird auch als erste synchronisiert. 

### <a name="contact-out-of-box-rules"></a>Standardregeln für Kontakte
Ein Kontaktobjekt muss Folgendes erfüllen, um synchronisiert zu werden:

* Der Kontakt muss E-Mail-aktiviert sein. Die Überprüfung umfasst folgende Regeln:
  * `IsPresent([proxyAddresses]) = True)`. Das proxyAddresses-Attribut muss aufgefüllt sein.
  * Eine primäre E-Mail-Adresse befindet sich entweder im proxyAddresses-Attribut oder im mail-Attribut. Ob es sich bei dem Inhalt um eine E-Mail-Adresse handelt, wird anhand des Vorhandenseins eines \@-Zeichens ermittelt. Eine der beiden Regeln muss „True“ ergeben:
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Ist ein Eintrag mit „SMTP:“ vorhanden? Wenn ja, enthält die Zeichenfolge ein \@-Zeichen?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. Ist das E-Mail-Attribut aufgefüllt, und falls ja, enthält die Zeichenfolge ein \@-Zeichen?

Die folgenden Kontaktobjekte werden **nicht** mit Azure AD synchronisiert:

* `IsPresent([isCriticalSystemObject])`. Sorgt dafür, dass als kritisch markierte Kontaktobjekte nicht synchronisiert werden. Bei einer Standardkonfiguration sind für gewöhnlich keine solchen Objekte vorhanden.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Diese Objekte wären nicht für Exchange Online geeignet.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Verhindert die Synchronisierung von Replikationsopferobjekten.

### <a name="group-out-of-box-rules"></a>Standardregeln für Gruppen
Ein Gruppenobjekt muss Folgendes erfüllen, um synchronisiert zu werden:

* Es muss weniger als 50.000 Mitglieder enthalten. Hierbei handelt es sich um die Mitgliederanzahl der lokalen Gruppe.
  * Sind vor dem Start der ersten Synchronisierung mehr Mitglieder vorhanden, wird die Gruppe nicht synchronisiert.
  * Sollte die Mitgliederanzahl später 50.000 erreichen, wird die Synchronisierung so lange ausgesetzt, bis die Anzahl der Mitglieder wieder unter 50.000 liegt.
  * Hinweis: Die Mitgliederanzahl von 50.000 wird auch von Azure AD erzwungen. Gruppen mit einer höheren Mitgliederanzahl können auch dann nicht synchronisiert werden, wenn Sie diese Regel ändern oder entfernen.
* Falls es sich bei der Gruppe um eine **Verteilergruppe** handelt, muss sie zudem E-Mail-aktiviert sein. Informationen zur Erzwingung dieser Regel finden Sie unter [Standardregeln für Kontakte](#contact-out-of-box-rules) .

Die folgenden Gruppenobjekte werden **nicht** mit Azure AD synchronisiert:

* `IsPresent([isCriticalSystemObject])`. Sorgt dafür, dass viele vorkonfigurierte Objekte in Active Directory (wie etwa die integrierte Administratorgruppe) nicht synchronisiert werden.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Von DirSync verwendete Legacygruppe.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Rollengruppe.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Verhindert die Synchronisierung von Replikationsopferobjekten.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Standardregeln für „ForeignSecurityPrincipal“
FSPs werden im Metaverse zu einem any-Objekt (\*) verknüpft. In der Realität wird diese Verknüpfung aber nur für Benutzer und Sicherheitsgruppen durchgeführt. Mit dieser Konfiguration wird sichergestellt, dass gesamtstrukturübergreifende Mitgliedschaften richtig aufgelöst und korrekt in Azure AD dargestellt werden.

### <a name="computer-out-of-box-rules"></a>Standardregeln für Computer
Ein Computerobjekt muss Folgendes erfüllen, um synchronisiert zu werden:

* `userCertificate ISNOTNULL`. Dieses Attribut wird nur auf Windows 10-Computern aufgefüllt. Alle Computerobjekte mit einem Wert in diesem Attribut werden synchronisiert.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Grundlegendes zum Standardregelszenario
In diesem Beispiel wird eine Bereitstellung mit einer Kontogesamtstruktur (A), einer Ressourcengesamtstruktur (R) und einem Azure AD-Verzeichnis verwendet.

![Bild mit Szenariobeschreibung](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Bei dieser Konfiguration wird vorausgesetzt, dass die Kontogesamtstruktur ein aktiviertes Konto und die Ressourcengesamtstruktur ein deaktiviertes Konto mit einem verknüpften Postfach enthält.

Unser Ziel bei der Standardkonfiguration ist:

* Auf die Anmeldung bezogene Attribute werden von der Gesamtstruktur aus mit dem aktivierten Konto synchronisiert.
* Attribute, die in der GAL (Global Address List, globale Adressliste) gefunden werden können, werden von der Gesamtstruktur aus mit dem Postfach synchronisiert. Wenn kein Postfach gefunden werden kann, wird jede beliebige andere Gesamtstruktur verwendet.
* Wenn ein verknüpftes Postfach gefunden wird, muss das verknüpfte aktivierte Konto für das Objekt gefunden werden, das in Azure AD exportiert wird.

### <a name="synchronization-rule-editor"></a>Synchronisierungsregel-Editor
Die Konfiguration kann mit dem Tool Synchronisierungsregel-Editor (Synchronization Rules Editor, SRE) angezeigt und geändert werden – eine entsprechende Verknüpfung befindet sich im Startmenü.

![Symbol für Synchronisierungsregel-Editor](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

Der SRE ist ein Resource Kit-Tool und wird mit der Azure AD Connect-Synchronisierung installiert. Um es starten zu können, müssen Sie Mitglied der Gruppe „ADSyncAdmins“ sein. Beim Start sollte Folgendes angezeigt werden:

![Synchronisierungsregeln für eingehenden Datenverkehr](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

In diesem Bereich sehen Sie alle für die Konfiguration erstellten Synchronisierungsregeln. Jede Zeile in der Tabelle stellt eine Synchronisierungsregel dar. Auf der linken Seite sind unter „Regeltypen“ die beiden verschiedenen Arten aufgeführt: Eingehend und ausgehend. Eingehend und ausgehend bedeutet aus Sicht vom Metaverse. In dieser Übersicht geht es vor allem um die Regeln für eingehenden Datenverkehr. Die tatsächliche Liste mit den Synchronisierungsregeln richtet sich nach dem in AD erkannten Schema. Die Kontogesamtstruktur (fabrikamonline.com) in der obigen Abbildung weist keine Dienste wie Exchange und Lync auf, und es wurden keine Synchronisierungsregeln für diese Dienste erstellt. In der Ressourcengesamtstruktur (res.fabrikamonline.com) sind aber Synchronisierungsregeln für diese Dienste vorhanden. Der Inhalt der Regeln hängt von der erkannten Version ab. In einer Bereitstellung mit Exchange 2013 werden z.B. mehr Attributflüsse als in Exchange 2010/2007 konfiguriert.

### <a name="synchronization-rule"></a>Synchronisierungsregel
Eine Regel ist ein Konfigurationsobjekt mit einem Satz von Attributen, die übertragen werden, wenn eine Bedingung erfüllt ist. Damit wird auch beschrieben, welche Beziehung ein Objekt in einem Connectorbereich zu einem Objekt im Metaverse hat. Dies wird als **Verknüpfung** oder **Übereinstimmung** bezeichnet. Die Synchronisierungsregeln besitzen einen Rangfolgenwert, der über ihre Beziehungen untereinander Aufschluss gibt. Eine Synchronisierungsregel mit einem niedrigeren numerischen Wert steht in der Rangfolge an einer höheren Position, und im Falle eines Attributflusskonflikts hat die höhere Rangfolgenposition Vorrang bei der Lösung des Konflikts.

Als Beispiel betrachten wir die Synchronisierungsregel **Ein von AD – Benutzer AccountEnabled**. Markieren Sie diese Zeile im SRE, und wählen Sie **Bearbeiten**.

Da es sich hierbei um eine vordefinierte Regel handelt, erhalten Sie eine Warnung, wenn Sie die Regel öffnen. [Vordefinierte Regeln sollten nicht geändert werden.](how-to-connect-sync-best-practices-changing-default-configuration.md)Daher werden Sie nach Ihren Absichten gefragt. In diesem Fall möchten Sie die Regel nur anzeigen. Wählen Sie also **Nein**.

![Synchronisierungsregeln – Warnung](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Eine Synchronisierungsregel verfügt über vier Konfigurationsabschnitte: Beschreibung, Bereichsfilter, Verknüpfungsregeln und Transformationen.

#### <a name="description"></a>BESCHREIBUNG
Der erste Bereich bietet grundlegende Informationen wie Name und Beschreibung.

![Registerkarte „Beschreibung“ im Synchronisierungsregel-Editor](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Es sind auch Informationen dazu enthalten, auf welches verbundene System sich diese Regel bezieht, auf welchen Objekttyp sie im verbundenen System angewendet wird und welcher Metaverse-Objekttyp vorliegt. Der Metaverse-Objekttyp ist immer eine Person, unabhängig davon, ob der Quellobjekttyp ein Benutzer, iNetOrgPerson oder ein Kontakt ist. Der Metaverse-Objekttyp sollte nie geändert werden, daher wird er als generischer Typ erstellt. Für den Verknüpfungstyp kann "Join", "StickyJoin" oder "Provision" festgelegt werden. Diese Einstellung arbeitet mit dem Bereich der Verknüpfungsregeln zusammen. Dies wird später behandelt.

Sie können auch sehen, dass diese Synchronisierungsregel zur Kennwortsynchronisierung verwendet wird. Wenn ein Benutzer im Bereich dieser Synchronisierungsregel liegt, wird das lokale Kennwort mit der Cloud synchronisiert (sofern Sie das Kennwortsynchronisierungsfeature aktiviert haben).

#### <a name="scoping-filter"></a>Bereichsfilter
Der Abschnitt "Bereichsfilter" wird zum Konfigurieren verwendet, wann eine Synchronisierungsregel angewendet werden soll. Da der Name der betrachteten Synchronisierungsregel darauf hinweist, dass sie nur für aktivierte Benutzer angewendet werden soll, wird der Gültigkeitsbereich so konfiguriert, dass für das AD-Attribut **userAccountControl** das Bit 2 nicht gesetzt sein darf. Wenn vom Synchronisierungsmodul ein Benutzer in AD gefunden wird, wird diese Synchronisierungsregel angewendet, sofern **userAccountControl** auf den Dezimalwert 512 (aktivierter normaler Benutzer) festgelegt ist. Sie wird nicht angewendet, wenn für den Benutzer der Wert 514 für **userAccountControl** (deaktivierter normaler Benutzer) festgelegt ist.

![Screenshot: Abschnitt „Bereichsfilter“ des Fensters „Eingehende Synchronisierungsregel bearbeiten“](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

Der Bereichsfilter verfügt über Gruppen und Klauseln, die geschachtelt werden können. Alle Klauseln innerhalb einer Gruppe müssen erfüllt werden, damit eine Synchronisierungsregel angewendet wird. Wenn mehrere Gruppen definiert sind, muss mindestens eine Gruppe die Anforderungen erfüllen, damit die Regel angewendet wird. Dies bedeutet, dass zwischen Gruppen ein logisches ODER und innerhalb einer Gruppe ein logisches UND ausgewertet wird. Ein Beispiel für diese Konfiguration befindet sich in der Synchronisierungsregel **Out to AAD – Group Join** (Aus an AAD – Gruppenverknüpfung) für ausgehenden Datenverkehr. Es gibt verschiedene Synchronisierungsfiltergruppen, z.B. eine für Sicherheitsgruppen (`securityEnabled EQUAL True`) und eine für Verteilergruppen (`securityEnabled EQUAL False`).

![Registerkarte „Bereichsdefinition“ im Synchronisierungsregel-Editor](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Diese Regel wird zur Definition verwendet, welche Gruppen für Azure AD bereitgestellt werden sollen. Verteilergruppen müssen E-Mail-aktiviert sein, damit sie mit Azure AD synchronisiert werden können, aber für Sicherheitsgruppen ist keine E-Mail erforderlich.

#### <a name="join-rules"></a>Verknüpfungsregeln
Der dritte Bereich wird für die Konfiguration verwendet, wie sich Objekte im Connectorbereich auf Objekte im Metaverse beziehen. Die zuvor betrachtete Regel verfügt über keine Konfiguration für Verknüpfungsregeln, sodass wir stattdessen die Regel **Ein von AD – Benutzerverknüpfung** betrachten.

![Registerkarte „Verknüpfungsregeln“ im Synchronisierungsregel-Editor](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Der Inhalt der Verknüpfungsregeln hängt von der entsprechenden Option ab, die im Installations-Assistenten ausgewählt ist. Für eine Regel für eingehenden Datenverkehr beginnt die Auswertung mit einem Objekt im Quellconnectorbereich, und jede Gruppe in den Verknüpfungsregeln wird nacheinander ausgewertet. Wenn die Auswertung eines Quellobjekts mit einer der Verknüpfungsregeln ergibt, dass es genau mit einem Objekt im Metaverse übereinstimmt, werden die Objekte verknüpft. Wenn alle Regeln ausgewertet wurden und es keine Übereinstimmung gibt, wird der Verknüpfungstyp auf der Beschreibungsseite verwendet. Wenn diese Konfiguration auf **Bereitstellung** festgelegt ist, wird im Ziel ein neues Objekt erstellt, das Metaverse, wenn mindestens ein Attribut in den Joinkriterien vorhanden ist (einen Wert hat). Die Bereitstellung eines neuen Objekts im Metaverse wird auch als das **Projizieren** eines Objekts für den Metaverse bezeichnet.

Die Verknüpfungsregeln werden nur einmal ausgewertet. Wenn ein Connectorbereichsobjekt und ein Metaverse-Objekt verknüpft werden, bleiben sie verknüpft, solange der Gültigkeitsbereich der Synchronisierungsregel weiterhin gegeben ist.

Bei der Auswertung von Synchronisierungsregeln muss sich nur eine Synchronisierungsregel mit Verknüpfungsregeln im Gültigkeitsbereich befinden. Wenn mehrere Synchronisierungsregeln mit Verknüpfungsregeln für ein Objekt gefunden werden, wird ein Fehler ausgelöst. Aus diesem Grund besteht die bewährte Methode darin, nur eine Synchronisierungsregel mit definierter Verknüpfung zu besitzen, wenn sich mehrere Synchronisierungsregeln im Gültigkeitsbereich eines Objekts befinden. In der Standardkonfiguration für Azure AD Connect Sync können diese Regeln durch eine Suche nach den Namen ermittelt werden, die das Wort **Join** am Ende des Namens enthalten. Eine Synchronisierungsregel ohne definierte Verknüpfungsregeln wendet die Attributflüsse an, wenn eine andere Synchronisierungsregel die Objekte verknüpft oder ein neues Objekt im Ziel bereitgestellt hat.

Wenn Sie die obige Abbildung betrachten, sehen Sie, dass die Regel versucht, **objectSID** mit **msExchMasterAccountSid** (Exchange) und **msRTCSIP-OriginatorSid** (Lync) zu verknüpfen, was in einer Kontoressourcengesamtstruktur-Topologie zu erwarten ist. Die gleiche Regel finden wir bei allen Gesamtstrukturen. Die Annahme ist, dass jede Gesamtstruktur entweder eine Konto- oder eine Ressourcengesamtstruktur sein könnte. Diese Konfiguration funktioniert auch, wenn Sie über Konten verfügen, die in einer einzelnen Gesamtstruktur vorhanden sind und nicht verknüpft werden müssen.

#### <a name="transformations"></a>Transformationen
Der Transformationsbereich definiert alle Attributflüsse, die sich auf das Zielobjekt beziehen, wenn die Objekte verknüpft werden und der Bereichsfilter zutrifft. Wenn Sie zur Synchronisierungsregel **Ein von AD – Benutzer AccountEnabled** zurückkehren, sind die folgenden Transformationen vorhanden:

![Registerkarte „Transformationen“ im Synchronisierungsregel-Editor](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Zur Orientierung in Bezug auf diese Konfiguration: In einer Kontoressourcen-Gesamtstrukturbereitstellung wird erwartet, dass ein aktiviertes Konto in der Kontogesamtstruktur und ein deaktiviertes Konto in der Ressourcengesamtstruktur mit Exchange- und Lync-Einstellungen vorgefunden werden. Die betrachtete Synchronisierungsregel enthält die für die Anmeldung erforderlichen Attribute, und diese sollen von der Gesamtstruktur übermittelt werden, in der ein aktiviertes Konto enthalten ist. Alle diese Attributflüsse sind in einer Synchronisierungsregel zusammengestellt.

Eine Transformation kann unterschiedliche Typen aufweisen: „Konstant“, „Direkt“ und „Ausdruck“.

* Bei einem konstanten Fluss wird immer ein hartcodierter Wert übermittelt. Im obigen Fall wird im Metaverseattribut mit dem Namen **accountEnabled** immer der Wert **True** festgelegt.
* Bei einem direkten Fluss wird der Wert des Attributs der Quelle immer unverändert an das Zielattribut übermittelt.
* Der dritte Ablauftyp ist "Expression", der fortgeschrittenere Konfigurationen gestattet.

Die Ausdruckssprache ist VBA (Visual Basic for Applications), sodass Benutzer, die über Microsoft Office- oder VBScript-Kenntnisse verfügen, mit diesem Format vertraut sein sollten. Attribute werden in eckige Klammern eingeschlossen: "[Attributname]". Bei Attributnamen und Funktionsnamen wird die Groß-/Kleinschreibung berücksichtigt, aber der Synchronisierungsregel-Editor wertet die Ausdrücke aus und zeigt eine Warnung an, wenn der Ausdruck nicht zulässig ist. Alle Ausdrücke werden in einer einzelnen Zeile mit verschachtelten Funktionen ausgedrückt. Zur Veranschaulichung der Leistung der Konfigurationssprache folgt hier der Ablauf für "pwdLastSet", jedoch mit zusätzlich eingefügten Kommentaren:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Weitere Informationen zur Ausdruckssprache für Attributflüsse finden Sie unter [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) .

### <a name="precedence"></a>Rangfolge
Sie haben nun einige individuelle Synchronisierungsregeln betrachtet, aber die Regeln wirken in der Konfiguration zusammen. In einigen Fällen tragen mehrere Synchronisierungsregeln einen Attributwert zum gleichen Zielattribut bei. In diesem Fall wird die Attributrangfolge verwendet, um zu bestimmen, welches Attribut „gewinnt“. Als Beispiel werfen wir einen Blick auf das sourceAnchor-Attribut. Dieses Attribut ist eine wichtige Voraussetzung zur Anmeldung bei Azure AD. Zwei verschiedene Synchronisierungsregeln enthalten einen Attributfluss für dieses Attribut: **In from AD – User AccountEnabled** (Ein von AD – Benutzer AccountEnabled) und **In from AD – User Common** (Ein von AD – Benutzer allgemein). Aufgrund des Synchronisierungsregelvorrangs trägt die Gesamtstruktur das sourceAnchor-Attribut mit einem aktivierten Konto erst dann bei, wenn mehrere Objekte mit dem Metaverse-Objekt verknüpft sind. Wenn keine aktivierten Konten vorhanden sind, verwendet das Synchronisierungsmodul die allgemeine Synchronisierungsregel **Ein von AD – Benutzer allgemein**. Mit dieser Konfiguration wird sichergestellt, dass auch für deaktivierte Konten ein sourceAnchor-Element vorhanden ist.

![Synchronisierungsregeln für eingehenden Datenverkehr](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Die Rangfolge für Synchronisierungsregeln wird vom Installations-Assistenten in Gruppen festgelegt. Alle Regeln einer Gruppe haben den gleichen Namen, aber sie sind mit unterschiedlichen verbundenen Verzeichnissen verbunden. Der Installations-Assistent räumt der Regel **Ein von AD – Benutzerverknüpfung** die höchste Rangfolgenposition ein und führt dafür eine Iteration über alle verbundenen AD-Verzeichnisse aus. Er fährt dann in einer vordefinierten Reihenfolge mit der nächsten Gruppe von Regeln fort. Innerhalb einer Gruppe werden die Regeln in der Reihenfolge hinzugefügt, in der die Connectors dem Assistenten hinzugefügt wurden. Wenn ein weiterer Connector durch den Assistenten hinzugefügt wird, werden die Synchronisierungsregeln neu geordnet, und die Regeln für den neuen Connector werden zuletzt in jede Gruppe eingefügt.

### <a name="putting-it-all-together"></a>Zusammenfügen des Gesamtbilds
Jetzt wissen wir genug über Synchronisierungsregeln, um die Funktionsweise der Konfiguration mit verschiedenen Synchronisierungsregeln zu verstehen. Wenn Sie einen Benutzer und die Attribute betrachten, die zum Metaverse beigetragen werden, werden die Regeln in der folgenden Reihenfolge angewendet:

| Name | Comment |
|:--- |:--- |
| Ein von AD – Benutzerverknüpfung |Regel für die Verknüpfung von Connectorbereichobjekten mit Metaverse. |
| Ein von AD – Benutzer AccountEnabled |Erforderliche Attribute für eine Anmeldung bei Azure AD und Microsoft 365. Diese Attribute sollen aus dem aktivierten Konto kommen. |
| Ein von AD – Benutzer allgemein aus Exchange |In der globalen Adressliste gefundene Attribute. Es wird vorausgesetzt, dass die Qualität der Daten in der Gesamtstruktur am besten ist, in der wir das Postfach des Benutzers gefunden haben. |
| Ein von AD – Benutzer allgemein |In der globalen Adressliste gefundene Attribute. Für den Fall, dass ein Postfach nicht gefunden wurde, kann jedes andere verknüpfte Objekt den Attributwert beitragen. |
| Ein von AD – Benutzer Exchange |Ist nur vorhanden, wenn Exchange erkannt wurde. Fluss aller Exchange-Attribute der Infrastruktur wird ausgeführt. |
| Ein von AD – Benutzer Lync |Ist nur vorhanden, wenn Lync erkannt wurde. Fluss aller Lync-Attribute der Infrastruktur wird ausgeführt. |

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Konfigurationsmodell finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).
* Weitere Informationen zur Ausdruckssprache finden Sie unter [Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Weitere Informationen zur Funktionsweise der Standardkonfiguration finden Sie unter [Grundlegendes zu Benutzern und Kontakten](concept-azure-ad-connect-sync-user-and-contacts.md)
* Unter [Ändern der Standardkonfiguration](how-to-connect-sync-change-the-configuration.md)wird beschrieben, wie Sie mit der deklarativen Bereitstellung eine praktische Änderung vornehmen.

**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)

