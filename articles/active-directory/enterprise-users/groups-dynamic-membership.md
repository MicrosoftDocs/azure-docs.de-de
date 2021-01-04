---
title: Regeln für dynamisch ausgegebene Gruppenmitgliedschaften – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Mitgliedschaftsregeln erstellt werden, um Gruppen automatisch aufzufüllen. Sie finden hier außerdem eine Regelreferenz.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c60d54a905f460eb5c26c2f183cd22b175a5b3c4
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860812"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie komplexe, attributbasierte Regeln für die Aktivierung von dynamischen Mitgliedschaften für Gruppen erstellen. Mit einer dynamischen Gruppenmitgliedschaft wird der Verwaltungsaufwand für das Hinzufügen und Entfernen von Benutzern reduziert. In diesem Artikel werden die Eigenschaften und die Syntax zum Erstellen der Regeln für eine dynamische Mitgliedschaft für Benutzer oder Geräte erläutert. Sie können eine Regel für die dynamische Mitgliedschaft für Sicherheits- oder Microsoft 365-Gruppen einrichten.

Wenn sich Attribute eines Benutzers oder Geräts ändern, bewertet das System alle dynamischen Gruppenregel in einem Verzeichnis, um zu ermitteln, ob die Änderung irgendwelche Vorgänge zum Hinzufügen oder Löschen von Gruppen auslöst. Falls ein Benutzer oder Gerät wird als Mitglied zu einer Gruppe hinzugefügt, wenn eine Regel dieser Gruppe erfüllt wird. Wenn sie diese Regel nicht mehr erfüllen, werden sie entfernt. Sie können ein Mitglied einer dynamischen Gruppe nicht manuell hinzufügen oder entfernen.

- Sie können zwar eine dynamische Gruppe für Geräte oder Benutzer erstellen, jedoch keine Regel festlegen, die sowohl Benutzer als auch Geräte enthält.
- Sie können keine Gerätegruppe basierend auf den Attributen der Gerätebesitzer erstellen. Regeln für die Gerätemitgliedschaft können nur Geräteattribute referenzieren.

> [!NOTE]
> Dieses Feature erfordert für jeden eindeutigen Benutzer, der Mitglied mindestens einer dynamischen Gruppe ist, eine Azure AD Premium P1-Lizenz. Sie müssen den Benutzern keine Lizenzen zuweisen, damit sie Mitglieder dynamischer Gruppen werden können, aber Sie müssen die Mindestanzahl von Lizenzen in der Azure AD-Organisation haben, um alle diese Benutzer abzudecken. Beispiel: Wenn Sie über insgesamt 1.000 eindeutige Benutzer in allen dynamischen Gruppen Ihrer Organisation verfügen, benötigen Sie mindestens 1.000 Lizenzen für Azure AD Premium P1 oder höher, um die Lizenzanforderung zu erfüllen.
> Für Geräte, die Mitglied einer dynamischen Gerätegruppe sind, ist keine Lizenz erforderlich.

## <a name="rule-builder-in-the-azure-portal"></a>Regel-Generator im Azure-Portal

Azure AD stellt einen Regel-Generator bereit, mit dem Sie wichtige Regeln schneller erstellen und aktualisieren können. Der Regel-Generator unterstützt die Erstellung von bis zu fünf Ausdrücken. Die Erstellung einer Regel mit einigen einfachen Ausdrücken wird durch den Regel-Generator vereinfacht, aber er kann nicht verwendet werden, um jede Regel zu reproduzieren. Falls der Regel-Generator die zu erstellende Regel nicht unterstützt, können Sie das Textfeld verwenden.

Im Anschluss folgen einige Beispiele für erweiterte Regeln oder für Syntax, für die die Erstellung über das Textfeld empfohlen wird:

- Regel mit mehr als fünf Ausdrücken
- Mitarbeiterregel
- Festlegen der [Rangfolge der Operatoren](#operator-precedence)
- [Regeln mit komplexen Ausdrücken](#rules-with-complex-expressions), z. B. `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Der Regel-Generator kann ggf. einige Regeln, die über das Textfeld erstellt wurden, nicht anzeigen. Unter Umständen wird eine Meldung angezeigt, falls die Regel vom Regel-Generator nicht angezeigt werden kann. Der Regel-Generator nimmt keinerlei Änderungen an der unterstützten Syntax, Überprüfung oder Verarbeitung von Regeln für dynamische Gruppen vor.

Schrittweise Anweisungen dazu finden Sie unter [Erstellen oder Aktualisieren einer dynamischen Gruppe](groups-create-rule.md).

![Hinzufügen einer Mitgliedschaftsregel für eine dynamische Gruppe](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Regelsyntax für einen einzelnen Ausdruck

Ein einzelner Ausdruck ist die einfachste Form einer Mitgliedschaftsregel, und sie besteht nur aus den drei oben genannten Teilen. Eine Regel mit einem einzelnen Ausdruck sieht wie folgt aus: `Property Operator Value`, wobei die Syntax für die Eigenschaft der Name von „object.property“ ist.

Im Folgenden finden ein Beispiel für eine richtig aufgebaute Mitgliedschaftsregel mit einem einzelnen Ausdruck:

```
user.department -eq "Sales"
```

Bei einem einzelnen Ausdruck sind Klammern optional. Die Gesamtlänge des Texts der Mitgliedschaftsregel darf 2048 Zeichen nicht überschreiten.

## <a name="constructing-the-body-of-a-membership-rule"></a>Erstellen des Texts einer Mitgliedschaftsregel

Eine Mitgliedschaftsregel, die eine Gruppe automatisch mit Benutzern oder Geräten auffüllt, ist ein binärer Ausdruck, der als Ergebnis „true“ oder „false“ ergibt. Die drei Teile einer einfachen Regel sind:

- Eigenschaft
- Operator
- Wert

Die Reihenfolge der Teile in einem Ausdruck ist wichtig, um Syntaxfehler zu vermeiden.

## <a name="supported-properties"></a>Unterstützte Eigenschaften

Es gibt drei Arten von Eigenschaften, die verwendet werden können, um eine Mitgliedschaftsregel zu erstellen.

- Boolean
- String
- Zeichenfolgensammlung

Im Folgenden sind die Benutzereigenschaften aufgelistet, die Sie verwenden können, um einen einzelnen Ausdruck zu erstellen.

### <a name="properties-of-type-boolean"></a>Eigenschaften vom Typ "boolesch"

| Eigenschaften | Zulässige Werte | Verwendung |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Eigenschaften vom Typ "string"

| Eigenschaften | Zulässige Werte | Verwendung |
| --- | --- | --- |
| city |Jeder string-Wert oder *null* |(user.city -eq "value") |
| country |Jeder string-Wert oder *null* |(user.country -eq "value") |
| companyName | Jeder string-Wert oder *null* | (user.companyName -eq "value") |
| department |Jeder string-Wert oder *null* |(user.department -eq "value") |
| displayName |Ein beliebiger Zeichenfolgenwert |(user.displayName -eq "value") |
| employeeId |Ein beliebiger Zeichenfolgenwert |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Jeder string-Wert oder *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |Jeder string-Wert oder *null* |(user.givenName -eq "value") |
| jobTitle |Jeder string-Wert oder *null* |(user.jobTitle -eq "value") |
| mail |Jeder string-Wert oder *null* (SMTP-Adresse des Benutzers) |(user.mail -eq "value") |
| mailNickName |Jeder string-Wert (E-Mail-Alias des Benutzers) |(user.mailNickName -eq "value") |
| mobile |Jeder string-Wert oder *null* |(user.mobile -eq "value") |
| objectId |GUID des Benutzerobjekts. |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Lokale Sicherheits-ID (SID) für Benutzer, deren Daten von einem lokalen Standort in die Cloud synchronisiert wurden. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Jeder string-Wert oder *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |Jeder string-Wert oder *null* |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Jeder string-Wert oder *null* |(user.sipProxyAddress -eq "value") |
| state |Jeder string-Wert oder *null* |(user.state -eq "value") |
| streetAddress |Jeder string-Wert oder *null* |(user.streetAddress -eq "value") |
| surname |Jeder string-Wert oder *null* |(user.surname -eq "value") |
| telephoneNumber |Jeder string-Wert oder *null* |(user.telephoneNumber -eq "value") |
| usageLocation |Aus zwei Buchstaben bestehender Länder-/Regionscode |(user.usageLocation -eq "US") |
| userPrincipalName |Ein beliebiger Zeichenfolgenwert |(user.userPrincipalName -eq "alias@domain") |
| userType |member-Gast *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>Eigenschaften vom Typ "string collection"

| Eigenschaften | Zulässige Werte | Verwendung |
| --- | --- | --- |
| otherMails |Ein beliebiger Zeichenfolgenwert |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Die für Geräteregeln verwendeten Eigenschaften finden Sie unter [Regeln für Geräte](#rules-for-devices).

## <a name="supported-expression-operators"></a>Unterstützte Ausdrucksoperatoren

Die folgende Tabelle enthält alle unterstützten Operatoren und deren Syntax für einen einzelnen Ausdruck. Alle Operatoren können mit oder ohne vorangestellten Bindestrich (-) verwendet werden.

| Operator | Syntax |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| Starts With |-startsWith |
| Not Contains |-notContains |
| Enthält |-contains |
| Not Match |-notMatch |
| Match |-match |
| Geben Sie in | -in |
| Not In | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Mithilfe der Operatoren „-in“ und „-notIn“

Wenn Sie den Wert eines Benutzerattributs mit einer Reihe unterschiedlicher Werte vergleichen möchten, können Sie die Operatoren „-in“ oder „-notIn“ verwenden. Verwenden Sie die Klammersymbole „[“ und „]“ für den Anfang und das Ende der Liste von Werten.

 Im folgenden Beispiel wird der Ausdruck als „true“ ausgewertet, wenn der Wert von „user.department“ einem der Werte in der Liste entspricht:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Mithilfe des Operators „-match“ 
Der Operator **-match** wird für Übereinstimmungen mit beliebigen regulären Ausdrücken verwendet. Beispiele:

```
user.displayName -match "Da.*"   
```
„Da“, „Dav“ und „David“ werden mit TRUE ausgewertet, „aDa“ hingegen mit FALSE.

```
user.displayName -match ".*vid"
```
„David“ wird mit TRUE ausgewertet, „Da“ hingegen mit FALSE.

## <a name="supported-values"></a>Unterstützte Werte

Die in einem Ausdruck verwendeten Werte können aus mehreren Typen bestehen, Beispiele:

* Zeichenfolgen
* Boolesch: true, false
* Zahlen
* Arrays: Zahlenarray, Zeichenfolgenarray

Wenn Sie einen Wert in einem Ausdruck angeben, ist es wichtig, die richtige Syntax zu verwenden, um Fehler zu vermeiden. Einige Tipps für die Syntax:

* Doppelte Anführungszeichen sind optional, es sei denn, der Wert ist eine Zeichenfolge.
* Bei Vorgängen mit Zeichenfolgen und regulären Ausdrücken wird die Groß- und Kleinschreibung nicht beachtet.
* Wenn ein Zeichenfolgenwert doppelte Anführungszeichen enthält, müssen beide Anführungszeichen mit dem Escapezeichen \` versehen werden, Beispiel: user.department -eq \`"Sales\`" ist die richtige Syntax, wenn der Wert "Sales" ist.
* Sie können auch NULL-Überprüfungen durchführen, indem Sie „null“ als Wert verwenden, Beispiel: `user.department -eq null`.

### <a name="use-of-null-values"></a>Verwenden von NULL-Werten

Zum Angeben eines NULL-Werts in einer Regel können Sie den Wert *null* verwenden. 

* Verwenden Sie „-eq“ oder „-ne“ zum Vergleichen des *null*-Werts in einem Ausdruck.
* Verwenden Sie nur Anführungszeichen um das Wort *null*, wenn es als literaler Zeichenfolgenwert interpretiert werden soll.
* Der -not-Operator kann nicht als Vergleichsoperator für NULL verwendet werden. Wenn Sie ihn verwenden, erhalten Sie eine Fehlermeldung, egal ob Sie „null“ oder „$null“ verwenden.

Die richtige Referenzierung des NULL-Werts erfolgt auf diese Weise:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regeln mit mehreren Ausdrücken

Eine Gruppenmitgliedschaftsregel kann aus mehreren Ausdrücken bestehen, die durch die logischen Operatoren „-and“, „-or“ und „-not“ verbunden sind. Logische Operatoren können auch in Kombination verwendet werden. 

Im Folgenden sehen Sie Beispiele für ordnungsgemäß konstruierte Mitgliedschaftsregeln mit mehreren Ausdrücken:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Rangfolge der Operatoren

Alle Operatoren sind unten in der Rangfolge von oben nach unten aufgeführt. Operatoren in der gleichen Zeile haben den gleichen Rang:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Im Folgenden finden Sie ein Beispiel für die Rangfolge der Operatoren, bei dem zwei Ausdrücke für den Benutzer ausgewertet werden:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Klammern sind nur erforderlich, wenn die Rangfolge nicht Ihren Anforderungen entspricht. Wenn die Abteilung zuerst ausgewertet werden soll, zeigt folgendes Beispiel, wie Klammern verwendet werden können, um die Reihenfolge zu bestimmen:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regeln mit komplexen Ausdrücken

Eine Mitgliedschaftsregel kann aus komplexen Ausdrücken bestehen, in denen die Eigenschaften, Operatoren und Werte komplexere Formen annehmen. Ausdrücke werden als komplex angesehen, wenn eine der folgenden Bedingungen zutrifft:

* Die Eigenschaft besteht aus einer Sammlung von Werten, insbesondere mehrwertigen Eigenschaften.
* In den Ausdrücken werden die Operatoren „-any“ und „-all“ verwendet.
* Der Wert des Ausdrucks kann selbst ein Ausdruck oder mehrere Ausdrücke sein.

## <a name="multi-value-properties"></a>Mehrwertige Eigenschaften

Mehrwertige Eigenschaften sind Sammlungen von Objekten desselben Typs. Sie können verwendet werden, um mithilfe der logischen Operatoren „-any“ und „-all“ Mitgliedschaftsregel zu erstellen.

| Eigenschaften | Werte | Verwendung |
| --- | --- | --- |
| assignedPlans | Jedes Objekt in der Sammlung macht folgende Zeichenfolgeneigenschaften verfügbar: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains „contoso“)) |

### <a name="using-the--any-and--all-operators"></a>Verwenden der Operatoren „-any“ und „-all“

Sie können die Operatoren „-any“ und „-all“ verwenden, um eine Bedingung auf ein Element oder alle Elemente in der Sammlung anzuwenden.

* „-any“ (erfüllt, wenn mindestens ein Element in der Auflistung der Bedingung entspricht)
* „-all“ (erfüllt, wenn alle Elemente in der Auflistung der Bedingung entsprechen)

#### <a name="example-1"></a>Beispiel 1

assignedPlans ist eine mehrwertige-Eigenschaft, die alle Service-Pläne auflistet, die dem Benutzer zugewiesen sind. Der folgende Ausdruck wählt Benutzer aus, die über einen Serviceplan von Exchange Online verfügen (Plan 2, als GUID-Wert), der sich im Status „Enabled“ befindet:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Mit einer Regel wie dieser können alle Benutzer gruppiert werden, für die eine Microsoft 365-Funktion (oder ein anderer Microsoft-Onlinedienst) aktiviert ist. Sie könnten dann einen Satz von Richtlinien auf die Gruppe anwenden.

#### <a name="example-2"></a>Beispiel 2

Der folgende Ausdruck wählt alle Benutzer aus, die über einen Serviceplan verfügen, der mit dem Intune-Dienst (identifizierbar anhand des Dienstnamens „SCO“) verknüpft ist:

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Verwenden den Syntax „Unterstrich“ (\_)

Die Syntax „Unterstrich“ (\_) entspricht dem Vorkommen eines bestimmten Werts in einer mehrwertigen Eigenschaft der Zeichenfolgensammlung, um Benutzer oder Geräte einer dynamischen Gruppe hinzuzufügen. Die Syntax wird mit den Operatoren „-any“ oder „-all“ verwendet.

Im Folgenden sehen Sie ein Beispiel für die Verwendung des Unterstrichs (\_) in einer Regel zum Hinzufügen von Mitgliedern basierend auf „user.proxyAddress“ (identische Funktionsweise wie „user.otherMails“). Diese Regel fügt der Gruppe jeden Benutzer mit einer Proxyadresse hinzu, die „contoso“ enthält.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Andere Eigenschaften und allgemeine Regeln

### <a name="create-a-direct-reports-rule"></a>Erstellen einer Mitarbeiterregel

Sie können eine Gruppe erstellen, die alle einem Manager direkt unterstellten Mitarbeiter enthält. Wenn sich die direkt unterstellten Mitarbeiter eines Managers in der Zukunft ändern, wird die Mitgliedschaft in der Gruppe automatisch angepasst.

Die Mitarbeiterregel wird mithilfe der folgenden Syntax erstellt:

```
Direct Reports for "{objectID_of_manager}"
```

Dies ist ein Beispiel einer gültigen Regel, bei der „62e19b97-8b3d-4d4a-a106-4ce66896a863“ die Objekt-ID des Managers ist:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

Die folgenden Tipps können helfen, die Regel ordnungsgemäß zu verwenden.

- Die **Manager-ID** ist die Objekt-ID des Managers. Sie finden sie im **Profil** des Managers.
- Damit diese Regel funktioniert, stellen Sie sicher, dass die Eigenschaft **Manager** für die Benutzer in Ihrer Organisation korrekt festgelegt ist. Sie können den aktuellen Wert im **Profil** des Benutzers überprüfen.
- Diese Regel unterstützt nur die dem Manager direkt unterstellten Mitarbeiter. Sie können also keine Gruppe mit dem Manager direkt unterstellten Mitarbeitern *und* den ihnen unterstellten Mitarbeitern erstellen.
- Diese Regel kann nicht mit anderen Mitgliedschaftsregeln kombiniert werden.

### <a name="create-an-all-users-rule"></a>Erstellen einer Regel für alle Benutzer

Sie können mithilfe einer Mitgliedschaftsregel eine Gruppe mit allen Benutzern innerhalb einer Organisation erstellen. Wenn Benutzer in Zukunft zur Organisation hinzugefügt oder daraus entfernt werden, wird die Mitgliedschaft in der Gruppe automatisch angepasst.

Die Regel für alle Benutzer wird mit einem einzelnen Ausdruck mit dem -ne-Operator und dem null-Wert erstellt. Mit dieser Regel werden B2B-Gastbenutzer und Mitgliedsbenutzer der Gruppe hinzugefügt.

```
user.objectId -ne null
```
Wenn Ihre Gruppe Gastbenutzer ausschließen und nur Mitglieder Ihrer Organisation enthalten soll, können Sie die folgende Syntax verwenden:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Erstellen einer Regel für alle Geräte

Sie können mithilfe einer Mitgliedschaftsregel eine Gruppe mit allen Geräten innerhalb einer Organisation erstellen. Wenn Geräte in Zukunft zur Organisation hinzugefügt oder daraus entfernt werden, wird die Mitgliedschaft in der Gruppe automatisch angepasst.

Die Regel für alle Geräte wird mit einem einzelnen Ausdruck mit dem -ne-Operator und dem null-Wert erstellt:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Erweiterungseigenschaften und benutzerdefinierte Erweiterungseigenschaften

Erweiterungsattribute und benutzerdefinierte Erweiterungsattribute werden als Zeichenfolgeneigenschaften in den Regeln für eine dynamische Mitgliedschaft unterstützt. [Erweiterungsattribute](/graph/api/resources/onpremisesextensionattributes) werden von einer lokalen Windows Server AD-Instanz synchronisiert und erhalten das Format „ExtensionAttributeX“, wobei X den Zahlen 1 bis 15 entspricht. Dies ist ein Beispiel für eine Regel, die ein Erweiterungsattribut als Eigenschaft verwendet:

```
(user.extensionAttribute15 -eq "Marketing")
```

[Benutzerdefinierte Erweiterungseigenschaften](../hybrid/how-to-connect-sync-feature-directory-extensions.md) werden von einer lokalen Windows Server AD-Instanz oder einer verbundenen SaaS-Anwendung synchronisiert und weisen das Format `user.extension_[GUID]_[Attribute]` auf. Dabei gilt Folgendes:

* [GUID] ist der eindeutige Bezeichner in Azure AD für die Anwendung, die die Eigenschaft in Azure AD erstellt hat.
* [Attribute] ist der Name der Eigenschaft bei ihrer Erstellung.

Beispiel für eine Regel, die eine benutzerdefinierte Erweiterungseigenschaft verwendet:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

Den Namen der benutzerdefinierten Eigenschaft finden Sie im Verzeichnis. Fragen Sie dazu die Eigenschaft eines Benutzers mithilfe des Graph-Explorers ab, und suchen Sie nach dem Eigenschaftennamen. Sie können jetzt im Regel-Generator für dynamische Benutzergruppen auf den Link **Get custom extension properties** (Benutzerdefinierte Erweiterungseigenschaften abrufen) klicken, um Ihre eindeutige App-ID einzugeben und die vollständige Liste mit benutzerdefinierten Erweiterungseigenschaften abzurufen, die Sie beim Erstellen einer dynamischen Mitgliedschaftsregel verwenden. Diese Liste kann auch aktualisiert werden, um neue benutzerdefinierte Erweiterungseigenschaften für diese App zu erhalten.

## <a name="rules-for-devices"></a>Regeln für Geräte

Sie können auch eine Regel erstellen, die Geräteobjekte für die Mitgliedschaft in einer Gruppe auswählt. Benutzer und Geräte können nicht gleichzeitig Gruppenmitglieder sein. 

> [!NOTE]
> Das Attribut **organizationalUnit** wird nicht mehr aufgelistet und sollte nicht verwendet werden. Diese Zeichenfolge wird von Intune in bestimmten Fällen festgelegt, aber nicht durch Azure AD erkannt, sodass Gruppen keine Geräte basierend auf diesem Attribut hinzugefügt werden.

> [!NOTE]
> „systemlabels“ ist ein schreibgeschütztes Attribut, das mit Intune nicht festgelegt werden kann.
>
> Bei Windows 10 lautet das richtige Format des deviceOSVersion-Attributs wie folgt: (device.deviceOSVersion -eq "10.0.17763"). Die Formatierung kann mit dem PowerShell-Cmdlet „Get-MsolDevice“ überprüft werden.

Die folgenden Geräteattribute können verwendet werden.

 Geräteattribut  | Werte | Beispiel
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | Jeder string-Wert. |(device.displayName -eq "Rob iPhone")
 deviceOSType | Jeder string-Wert. | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | Jeder string-Wert. | (device.deviceOSVersion -eq "9.1")
 deviceCategory | ein gültiger Gerätekategoriename | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Jeder string-Wert. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Jeder string-Wert. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personal, Unternehmen, Unbekannt | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Profilname für Apple-Geräteregistrierung, Android Enterprise-Profilname für die Registrierung firmeneigener dedizierter Geräte oder Name des Windows Autopilot-Profils | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (bei mobilen Geräten)<br>PC (bei Computern, die vom Intune-PC-Agent verwaltet werden) | (device.managementType -eq "MDM")
 deviceId | eine gültige Azure AD-Geräte-ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | eine gültige Azure AD-Objekt-ID |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | Jeder von Autopilot verwendete Zeichenfolgenwert, z. B. alle Autopilotgeräte, OrderID oder PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | Eine beliebige Zeichenfolge, die mit der Intune-Geräteeigenschaft zum Kennzeichnen von Geräten der modernen Arbeitswelt übereinstimmt | (device.systemLabels -contains "M365Managed")

> [!Note]  
> Für das Geräteattribut „deviceOwnership“ müssen Sie beim Erstellen dynamischer Gruppen für Geräte den Wert auf „Company“ festlegen. In Intune wird der Gerätebesitz dagegen mit „Corporate“ angegeben. Weitere Informationen finden Sie unter [OwnerTypes](/intune/reports-ref-devices#ownertypes). 

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Gruppen in Azure Active Directory.

- [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Erstellen einer neuen Gruppe und Hinzufügen von Mitgliedern](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-create-rule.md)
