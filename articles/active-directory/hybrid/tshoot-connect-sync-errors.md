---
title: 'Azure AD Connect: Beheben von Fehlern während der Synchronisierung | Microsoft Docs'
description: Erläutert die Behebung von Fehlern während der Synchronisierung mit Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a668fa9bf0ef4fd3b5451ff4c815b676fe237e51
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410622"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Beheben von Fehlern während der Synchronisierung
Fehler können auftreten, wenn Identitätsdaten aus Windows Server Active Directory (AD DS) mit Azure Active Directory (Azure AD) synchronisiert werden. Dieser Artikel bietet einen Überblick über verschiedene Fehlertypen, die während der Synchronisierung auftreten können, einige der möglichen Szenarios, die solche Fehler verursachen, und Möglichkeiten, diese Fehler zu beheben. In diesem Artikel werden die häufigsten Fehlertypen behandelt, daher sind eventuell nicht alle möglichen Fehler enthalten.

 In diesem Artikel wird vorausgesetzt, dass der Leser mit den zugrunde liegenden [Designkonzepten von Azure AD und Azure AD Connect](plan-connect-design-concepts.md) vertraut ist.

Mit der neuesten Version von Azure AD Connect, \(August 2016 oder höher\), ist nun ein Bericht der Synchronisierungsfehler im [Azure-Portal](https://aka.ms/aadconnecthealth) als Teil von Azure AD Connect Health für die Synchronisierung verfügbar.

Ab 1. September 2016 wird das Feature [Azure Active Directory Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md) standardmäßig für alle *neuen* Azure Active Directory-Mandanten aktiviert. Dieses Feature wird in den kommenden Monaten automatisch für bereits bestehende Mandanten aktiviert.

Azure AD Connect führt drei Arten von Vorgängen aus den Verzeichnissen heraus aus, die synchron gehalten werden: Importieren, Synchronisieren und Exportieren. In allen Vorgängen können Fehler auftreten. In diesem Artikel werden hauptsächlich Fehler während des Exportierens nach Azure AD behandelt.

## <a name="errors-during-export-to-azure-ad"></a>Fehler während des Exportierens nach Azure AD
Im folgenden Abschnitt werden verschiedene Typen von Synchronisierungsfehlern beschrieben, die während des Exportvorgangs nach Azure AD mit dem Azure AD-Connector auftreten können. Dieser Connector kann anhand des Formats des Namens erkannt werden: „contoso. *onmicrosoft.com* “.
Fehler, die während des Exportierens nach Azure AD auftreten, deuten an, dass der Vorgang \(hinzufügen, aktualisieren, löschen, usw.\) fehlgeschlagen ist, der durch das \(Synchronisierungsmodul\) von Azure AD Connect unter Azure Active Directory gestartet wurde.

![Übersicht Exportieren von Fehlern](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Fehler durch Datenkonflikt
### <a name="invalidsoftmatch"></a>InvalidSoftMatch (Ungültiges Soft Match (mögliche Übereinstimmung))
#### <a name="description"></a>BESCHREIBUNG
* Wenn das \(Synchronisierungsmodul\) von Azure AD Connect die Anweisung an Azure Active Directory gibt, Objekte hinzuzufügen oder zu aktualisieren, untersucht Azure AD mithilfe des Attributs **sourceAnchor** das eingehende Objekt nach Übereinstimmungen mit dem Attribut **immutableId** von Objekten in Azure AD. Diese Übereinstimmung nennt man **Hard Match (genaue Übereinstimmung)** .
* Wenn Azure AD **kein** Objekt findet, bei dem das Attribut **immutableID** mit dem Attribut **sourceAnchor** eines eingehenden Objekts übereinstimmt, bevor ein neues Objekt bereitgestellt wird, werden stattdessen die Attribute „ProxyAddresses“ und „UserPrincipalName“ verwendet, um eine Übereinstimmung zu finden. Diese Übereinstimmung nennt man **Soft Match (mögliche Übereinstimmung)** . Ein Soft Match soll Objekte, die sich bereits in Azure AD befinden (die Azure AD als Quelle haben), mit neuen Objekten abgleichen, die während der Synchronisierung hinzugefügt/aktualisiert werden, und lokal die gleiche Entität (Benutzer, Gruppen) darstellen.
* Der Fehler **InvalidSoftMatch** tritt auf, wenn das Hard Match keine übereinstimmenden Objekte findet, **UND** das Soft Match ein übereinstimmendes Objekt findet, dieses Objekt aber einen anderen Wert für *immutableId* hat als *SourceAnchor* des eingehenden Objekts. Das bedeutet, dass das übereinstimmende Objekt mit einem anderen Objekt aus dem lokalen Active Directory synchronisiert wurde.

Anders ausgedrückt bedeutet das, dass das Objekt, mit dem eine mögliche Übereinstimmung bestehen soll, keinen Wert für das Attribut *immutableId* haben sollte, damit das Soft Match funktioniert. Wenn ein Objekt, bei dem für *immutableId* ein Wert festgelegt ist, nicht den Kriterien eines Hard Matchs erfüllt, aber den Kriterien eines Soft Matchs entspricht, tritt beim Vorgang der Synchronisierungsfehler „InvalidSoftMatch“ auf.

Ein Azure Active Directory Schema lässt es nicht zu, dass zwei oder mehr Objekte denselben Wert für eines der folgenden Attribute haben. \(Dies ist keine vollständige Liste.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectID

> [!NOTE]
> Das Feature [Azure Active Directory Resilienz bei doppelten Attributen](how-to-connect-syncservice-duplicate-attribute-resiliency.md) wird auch als Standardverhalten von Azure Active Directory eingeführt.  Dadurch wird die Anzahl der Synchronisierungsfehler, die in Azure AD Connect (sowie in anderen Clients für die Synchronisierung) auftreten, verringert, nachdem Azure AD weniger fehleranfällig beim Umgang mit duplizierten Attributen „ProxyAddresses“ und „UserPrincipalName“ ist, die in lokalen AD-Umgebungen vorkommen. Dieses Feature behebt allerdings nicht die Duplizierungsfehler. Daher müssen die Daten immer noch bereinigt werden. Allerdings wird die Bereitstellung neuer Objekte ermöglicht, die sonst aufgrund der Duplizierungswerte in Azure AD nicht bereitgestellt werden konnten. Dadurch wird auch die Anzahl der Fehler bei der Synchronisierung verringert, die an den Client für Synchronisierung zurückgegeben werden.
> Wenn dieses Feature für Ihren Mandanten aktiviert ist, werden die Synchronisierungsfehler „InvalidSoftMatch“ während der Bereitstellung neuer Objekte nicht auftreten.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Beispielszenarios für „InvalidSoftMatch“
1. In der lokalen Active Directory-Umgebung verfügen mindestens zwei Objekte über denselben Wert für das Attribut „ProxyAddresses“. Nur eines wird in Azure AD bereitgestellt.
2. In der lokalen Active Directory-Umgebung verfügen mindestens zwei Objekte über denselben Wert für das Attribut „userPrincipalName“. Nur eines wird in Azure AD bereitgestellt.
3. Ein Objekt wurde in das lokale Active Directory hinzugefügt. Dieses Objekt hatte den gleichen Wert des Attributs „ProxyAddresses“ wie ein bestehendes Objekt in Azure Active Directory. Das Objekt, das lokal hinzugefügt wurde, wird nicht in Azure Active Directory bereitgestellt.
4. Ein Objekt wurde in das lokale Active Directory hinzugefügt. Dieses Objekt hatte den gleichen Wert des Attributs „userPrincipalName“ wie ein Konto in Azure Active Directory. Das Objekt wird nicht in Azure Active Directory bereitgestellt.
5. Ein synchronisiertes Konto wurde aus Forest A (Gesamtstruktur A) in Forest B (Gesamtstruktur B) verschoben. Azure AD Connect (das Synchronisierungsmodul) hat das Attribut „ObjectGUID“ verwendet, um „SourceAnchor“ zu berechnen. Nach dem Verschieben der Gesamtstruktur, hat „SourceAnchor“ einen anderen Wert. Das neue Objekt (aus Forest B) kann nicht mit dem bestehenden Objekt in Azure AD synchronisiert werden.
6. Ein synchronisiertes Objekt wurde versehentlich aus dem lokalen Active Directory gelöscht, und ein neues Objekt für die gleiche Entität (z.B. Benutzer) wurde in Active Directory erstellt, ohne das Konto in Azure Active Directory zu löschen. Das neue Konto kann nicht mit dem bestehenden Azure AD-Objekt synchronisiert werden.
7. Azure AD Connect wurde deinstalliert und neu installiert. Bei der erneuten Installation wurde ein anderes Attribut als „SourceAnchor“ ausgewählt. Alle Objekte, die vorher synchronisiert wurden, beendeten die Synchronisierung aufgrund des Fehlers „InvalidSoftMatch“.

#### <a name="example-case"></a>Beispielfall:
1. **Bob Smith** ist ein synchronisierter Benutzer in Azure Active Directory aus dem lokalem Active Directory *contoso.com*.
2. Das Attribut **UserPrincipalName** von Bob Smith lautet **bobs\@contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** ist das Attribut **SourceAnchor** , das von Azure AD Connect mithilfe von Bob Smiths **objectGUID** aus dem lokalen Active Directory berechnet wurde, das **immutableId** für Bob Smith im Azure Active Directory darstellt.
4. Außerdem hat Bob die folgenden Werte des Attributs **proxyAddresses** :
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
5. Ein neuer Benutzer, **Bob Taylor** , wird zum lokalen Active Directory hinzugefügt.
6. Das Attribut **UserPrincipalName** von Bob Taylor lautet **bobt\@contoso.com**.
7. **"abcdefghijkl0123456789==""** ist das Attribut **sourceAnchor** , das von Azure AD Connect mithilfe von Bob Taylors Attribut **objectGUID** aus dem lokalen Active Directory berechnet wurde. Bob Taylors Objekt wurde noch NICHT mit Azure Active Directory synchronisiert.
8. Außerdem hat Bob Taylor die folgenden Werte des Attributs „proxyAddresses“:
   * smtp: bobt@contoso.com
   * smtp: bob.taylor@contoso.com
   * **smtp: bob\@contoso.com**
9. Während der Synchronisierung erkennt Azure AD Connect das Hinzufügen von Bob Taylor in das lokale Active Directory, und weist Azure AD an, dieselbe Änderung durchzuführen.
10. Azure AD führt zuerst ein Hard Match aus. Das bedeutet, dass nach einem Objekt gesucht wird, dessen „immutableId“ "abcdefghijkl0123456789==" entspricht. Hard Match schlägt fehl, da in Azure AD kein anderes Objekt mit diesem Wert in „immutableId“ besteht.
11. Azure AD versucht dann, ein Soft Match mit Bob Taylor durchzuführen. Das bedeutet, dass nach einem Objekt gesucht wird, dessen Attribut „proxyAddresses“ den drei Werten entspricht, darunter „smtp: bob@contoso.com“.
12. Azure AD findet heraus, dass Bob Smiths Objekt den Kriterien des Soft Matchs entspricht. Dieses Objekt hat aber in „immutableId“ den Wert "abcdefghijklmnopqrstuv==". Das bedeutet, dass dieses Objekt aus einem anderen Objekt aus dem lokalen Active Directory synchronisiert wurde. Daher kann Azure AD kein Soft Match dieser Objekte durchführen, weswegen der Synchronisierungsfehler **InvalidSoftMatch** auftritt.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>So behebt man den Fehler InvalidSoftMatch
Die häufigste Ursache für den Fehler „InvalidSoftMatch“ ist, dass zwei Objekte mit einem anderen Source Anchor \(immutableId\) den gleichen Wert für die Attribute „ProxyAddresses“ und/oder „UserPrincipalName“ haben, die während des Soft Match-Vorgangs in Azure AD verwendet werden. Um den Fehler InvalidSoftMatch zu beheben, müssen Sie Folgendes tun:

1. Identifizieren Sie den duplizierten Wert für „proxyAddresses“, „userPrincipalName“ oder ein anderes Attribut, das den Fehler verursacht hat. Finden Sie auch heraus, welche zwei \(oder mehr\) Objekte am Konflikt beteiligt sind. Der Bericht, der von [Azure AD Connect Health für die Synchronisierung](./how-to-connect-health-sync.md) erstellt wurde, kann Ihnen dabei helfen, diese beiden Objekte zu identifizieren.
2. Bestimmen Sie, welches Objekt weiterhin den duplizierten Wert haben soll, und welches nicht.
3. Entfernen Sie den duplizierten Wert aus dem Objekt, das diesen Wert NICHT haben soll. Sie müssen die Änderung in dem Verzeichnis durchführen, aus dem das Objekt eingebunden wurde. In einigen Fällen müssen Sie eines der in Konflikt stehenden Objekte löschen.
4. Wenn Sie die Änderungen im lokalen AD durchgeführt haben, synchronisieren Sie die Änderung mit Azure AD Connect.

Synchronisierungsfehlerberichte in Azure AD Connect Health für die Synchronisierung werden alle 30 Minuten aktualisiert und enthalten die Fehler aus dem letzten Synchronisierungsversuch.

> [!NOTE]
> ImmutableId sollte per Definition während der Lebensdauer des Objekts nicht geändert werden. Wenn Azure AD Connect nicht unter Berücksichtigung einiger der Szenarios der obigen Liste konfiguriert wurde, könnten Sie in eine Situation kommen, in der Azure AD Connect einen anderen Wert für „SourceAnchor“ des AD-Objekts berechnet, das die gleiche Entität (gleiche Benutzer/Gruppe/Kontakt usw.) darstellt, die ein bestehendes Azure AD-Objekt hat, das Sie weiterhin verwenden möchten.
>
>

#### <a name="related-articles"></a>Verwandte Artikel
* [Doppelte oder ungültige Attribute verhindern die Verzeichnissynchronisierung in Microsoft 365.](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch (Konflikt mit Objekttyp)
#### <a name="description"></a>BESCHREIBUNG
Wenn Azure AD versucht, ein Soft Match mit zwei Objekten durchzuführen, kann es vorkommen, dass zwei Objekte unterschiedlicher „Typen“ (z.B. Benutzer, Gruppe, Kontakt usw) den gleichen Wert für die Attribute haben, die für die Ausführung des Soft Matchs benötigt werden. Da die Duplizierung dieser Attribute in Azure AD nicht zulässig ist, kann bei diesem Vorgang ein Synchronisierungsfehler „ObjectTypeMismatch“ auftreten.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Beispielszenarios für ObjectTypeMismatch-Fehler
* In Microsoft 365 wird eine per E-Mail aktivierte Sicherheitsgruppe erstellt. Der Administrator fügt einen neuen Benutzer oder Kontakt im lokalen AD hinzu (das noch nicht mit Azure AD synchronisiert wurde), der den gleichen Wert für das Attribut „ProxyAddresses“ hat wie das Attribut der Microsoft 365-Gruppe.

#### <a name="example-case"></a>Beispielfall
1. Der Administrator erstellt in Microsoft 365 eine neue per E-Mail aktivierte Sicherheitsgruppe für die Steuerabteilung, und stellt als E-Mail Adresse tax@contoso.com bereit. Diese Gruppe wird dem Wert des Attributs ProxyAddresses von **smtp: tax\@contoso.com** zugewiesen.
2. Ein neuer Benutzer tritt Contoso.com bei. Für diesen Benutzer wird ein lokales Konto mit proxyAddress als **smtp: tax\@contoso.com** erstellt.
3. Wenn Azure AD Connect das neue Benutzerkonto synchronisiert, erscheint der Fehler „ObjectTypeMismatch“.

#### <a name="how-to-fix-objecttypemismatch-error"></a>So behebt man den Fehler ObjectTypeMismatch
Der häufigste Grund für den Fehler „ObjectTypeMismatch“ ist, dass zwei Objekte eines anderen Typs (Benutzer, Gruppe, Kontakt usw.) den gleichen Wert für das Attribut „ProxyAddresses“ haben. Um den Fehler „ObjectTypeMismatch“ zu beheben, müssen Sie folgendes tun:

1. Identifizieren Sie den duplizierten Wert für das Attribut „proxyAddresses“ (oder ein anderes Attribut), der den Fehler verursacht hat. Finden Sie auch heraus, welche zwei \(oder mehr\) Objekte am Konflikt beteiligt sind. Der Bericht, der von [Azure AD Connect Health für die Synchronisierung](./how-to-connect-health-sync.md) erstellt wurde, kann Ihnen dabei helfen, diese beiden Objekte zu identifizieren.
2. Bestimmen Sie, welches Objekt weiterhin den duplizierten Wert haben soll, und welches nicht.
3. Entfernen Sie den duplizierten Wert aus dem Objekt, das diesen Wert NICHT haben soll. Beachten Sie, dass Sie diese Änderung in dem Verzeichnis durchführen sollten, aus dem das Objekt erstellt wurde. In einigen Fällen müssen Sie eines der in Konflikt stehenden Objekte löschen.
4. Wenn Sie die Änderungen im lokalen AD durchgeführt haben, synchronisieren Sie die Änderung mit Azure AD Connect. Der Synchronisierungsfehlerbericht in Azure AD Connect Health für die Synchronisierung alle 30 Minuten aktualisiert wird, und die Fehler aus dem letzten Synchronisierungsversuch enthält.

## <a name="duplicate-attributes"></a>Duplizierte Attribute
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique (Wert für Attribut muss eindeutig sein)
#### <a name="description"></a>BESCHREIBUNG
Ein Azure Active Directory Schema lässt es nicht zu, dass zwei oder mehr Objekte denselben Wert für eines der folgenden Attribute haben. Das bedeutet, dass jedes Objekt in Azure AD einen eindeutigen Wert für diese Attribute bei einer jeweiligen Instanz haben muss.

* ProxyAddresses
* UserPrincipalName

Wenn Azure AD Connect versucht, ein neues Objekt hinzuzufügen oder ein vorhandenes Objekt zu aktualisieren, dessen Wert für obige Attribute bereits einem anderen Objekt in Azure Active Directory zugewiesen ist, tritt bei diesem Vorgang der Synchronisierungsfehler „AttributeValueMustBeUnique“ auf.

#### <a name="possible-scenarios"></a>Mögliche Szenarios:
1. Ein duplizierter Wert wird einem bereits synchronisierten Objekt zugewiesen, das mit einem anderen synchronisierten Objekt in Konflikt steht.

#### <a name="example-case"></a>Beispielfall:
1. **Bob Smith** ist ein synchronisierter Benutzer in Azure Active Directory aus dem lokalem Active Directory contoso.com.
2. Der Wert für das Attribut **UserPrincipalName** von Bob Smith wird lokal als **bobs\@contoso.com** festgelegt.
3. Außerdem hat Bob die folgenden Werte des Attributs **proxyAddresses** :
   * smtp: bobs@contoso.com
   * smtp: bob.smith@contoso.com
   * **smtp: bob\@contoso.com**
4. Ein neuer Benutzer, **Bob Taylor** , wird zum lokalen Active Directory hinzugefügt.
5. Das Attribut **UserPrincipalName** von Bob Taylor lautet **bobt\@contoso.com**.
6. Außerdem hat **Bob Taylor** die folgenden Werte des Attributs **ProxyAddresses** i. smtp: bobt@contoso.com ii. smtp: bob.taylor@contoso.com
7. Bob Taylors Objekt wird erfolgreich mit Azure AD synchronisiert.
8. Der Administrator hat entschieden, Bob Taylors Attribut **ProxyAddresses** mit dem folgenden Wert zu aktualisieren: I. **smtp: bob\@contoso.com**
9. Azure AD versucht, Bob Taylors Objekt in Azure AD mit dem obigen Wert zu aktualisieren. Der Vorgang schlägt allerdings fehl, nachdem der Wert für „ProxyAddresses“ bereits Bob Smith zugewiesen ist, wodurch der Fehler „AttributeValueMustBeUnique“ verursacht wird.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Beheben des Fehlers AttributeValueMustBeUnique
Die häufigste Ursache für den Fehler „AttributeValueMustBeUnique“ ist, dass zwei Objekte mit einem anderen Source Anchor \(immutableId\) den gleichen Wert für die Attribute „ProxyAddresses“ und/oder „UserPrincipalName“ haben. Um den Fehler „AttributeValueMustBeUnique“ zu beheben, müssen Sie folgendes tun:

1. Identifizieren Sie den duplizierten Wert für das Attribut „userPrincipalName“, das Atribut „proxyAddresses“ oder ein anderes Attribut, das den Fehler verursacht hat. Finden Sie auch heraus, welche zwei \(oder mehr\) Objekte am Konflikt beteiligt sind. Der Bericht, der von [Azure AD Connect Health für die Synchronisierung](./how-to-connect-health-sync.md) erstellt wurde, kann Ihnen dabei helfen, diese beiden Objekte zu identifizieren.
2. Bestimmen Sie, welches Objekt weiterhin den duplizierten Wert haben soll, und welches nicht.
3. Entfernen Sie den duplizierten Wert aus dem Objekt, das diesen Wert NICHT haben soll. Beachten Sie, dass Sie diese Änderung in dem Verzeichnis durchführen sollten, aus dem das Objekt erstellt wurde. In einigen Fällen müssen Sie eines der in Konflikt stehenden Objekte löschen.
4. Wenn Sie die Änderungen im lokalen AD durchgeführt haben, synchronisieren Sie die Änderung mit Azure AD Connect, um den Fehler zu beheben.

#### <a name="related-articles"></a>Verwandte Artikel
-[Doppelte oder ungültige Attribute verhindern die Verzeichnissynchronisierung in Microsoft 365.](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Fehler bei der Datenüberprüfung
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed (Überprüfung von Identitätsdaten schlägt fehl)
#### <a name="description"></a>BESCHREIBUNG
Azure Active Directory erzwingt verschiedene Einschränkungen für die Daten selbst, bevor diese Daten in das Verzeichnis geschrieben werden können. Diese Einschränkungen gewährleisten eine optimale Erfahrung für Endbenutzer, wenn sie die Anwendungen verwenden, die von diesen Daten abhängig sind.

#### <a name="scenarios"></a>Szenarien
a. Der Wert des Attributs „UserPrincipalName“ enthält ungültige/nicht unterstützte Zeichen.
b. Das Attribut „UserPrincipalName“ folgt nicht dem erforderlichen Format.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>So behebt man den Fehler „IdentityDataValidationFailed“
a. Stellen Sie sicher, dass das Attribut „UserPrincipalName“ nur unterstütze Zeichen enthält und dem erforderlichen Format entspricht.

#### <a name="related-articles"></a>Verwandte Artikel
* [Vorbereiten von Benutzern auf die Bereitstellung in Microsoft 365 über die Verzeichnissynchronisierung](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>BESCHREIBUNG
In diesem Fall tritt der Synchronisierungsfehler **FederatedDomainChangeError** auf, wenn das Suffix des Attributs „UserPrincipalName“ eines Benutzers von einer Verbunddomäne in eine andere Verbunddomäne geändert wird.

#### <a name="scenarios"></a>Szenarien
Bei einem synchronisierten Benutzer wurde das Suffix des „UserPrincipalName“ von einer Verbunddomäne zu einer anderen lokalen Verbunddomäne geändert. Beispiel: *UserPrincipalName = bob\@contoso.com* wurde in *UserPrincipalName = bob\@fabrikam.com* geändert.

#### <a name="example"></a>Beispiel
1. Bob Smith, ein Konto für Contoso.com, wird mit „UserPrincipalName“ bob@contoso.com als neuer Benutzer in Active Directory hinzugefügt.
2. Bob wechselt zu einem anderen Bereich von Contoso.com mit dem Namen Fabrikam.com. Sein Attribut „UserPrincipalName“ wird in bob@fabrikam.com geändert.
3. Die Domänen „contoso.com“ und „fabrikam.com“ sind Verbunddomänen mit Azure Active Directory.
4. Bobs „userPrincipalName“ wird nicht aktualisiert und verursacht den Synchronisierungsfehler „FederatedDomainChangeError“.

#### <a name="how-to-fix"></a>So behebt man den Fehler
Wenn das UserPrincipalName-Suffix eines Benutzers von bob@ **contoso.com** in bob\@**fabrikam.com** geändert wurde und es sich sowohl bei **contoso.com** als auch bei **fabrikam.com** um **Verbunddomänen** handelt, führen Sie die folgenden Schritte aus, um den Synchronisierungsfehler zu beheben.

1. Ändern Sie den UserPrincipalName des Benutzers in Azure AD von bob@contoso.com in bob@contoso.onmicrosoft.com. Sie können die folgenden PowerShell-Befehl mit dem Azure AD PowerShell-Modul verwenden: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Lassen Sie im nächsten Synchronisierungszyklus die Synchronisierung zu. Diesmal ist die Synchronisierung erfolgreich, und aktualisiert wie erwartet Bobs „UserPrincipalName“ in bob@fabrikam.com.

#### <a name="related-articles"></a>Verwandte Artikel
* [Änderungen werden nicht mehr vom Azure Active Directory-Synchronisierungstool synchronisiert, wenn der UPN eines Benutzerkontos für die Verbindung mit einer anderen Verbunddomäne geändert wird.](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject (Großes Objekt)
### <a name="description"></a>BESCHREIBUNG
Wenn ein Attribut die maximal zulässigen Grenzwerte für Größe, Länge oder Anzahl überschreitet, die durch das Azure Active Directory-Schema festgelegt sind, tritt beim Synchronisierungsvorgang der Synchronisierungsfehler **LargeObject** oder **ExceededAllowedLength** auf. In der Regel tritt dieser Fehler für die folgenden Attribute auf:

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Mögliche Szenarien
1. Bobs Attribut „userCertificate“ speichert zu viele Zertifikate, die Bob zugewiesen sind. Dazu können auch ältere, abgelaufene Zertifikate gehören. Die harte Grenze liegt bei 15 Zertifikaten. Weitere Informationen zum Beheben von LargeObject-Fehlern mit einem userCertificate-Attribut finden Sie im Artikel [Beheben von LargeObject-Fehlern, die auf das userCertificate-Attribut zurückzuführen sind](tshoot-connect-largeobjecterror-usercertificate.md).
2. Bobs Attribut „userSMIMECertificate“ speichert zu viele Zertifikate, die Bob zugewiesen sind. Dazu können auch ältere, abgelaufene Zertifikate gehören. Die harte Grenze liegt bei 15 Zertifikaten.
3. Bobs „thumbnailPhoto“, das in Active Directory festgelegt ist, ist zu groß, um in Azure AD synchronisiert zu werden.
4. Während des automatischen Auffüllens des ProxyAddresses-Attributs in Active Directory wurden einem Objekt zu viele Proxyadressen zugewiesen.

### <a name="how-to-fix"></a>So behebt man den Fehler
1. Stellen Sie sicher, dass das Attribut, das den Fehler verursacht, innerhalb der zulässigen Einschränkungen ist.

## <a name="existing-admin-role-conflict"></a>Konflikt mit vorhandener Administratorrolle

### <a name="description"></a>BESCHREIBUNG
Ein **Konflikt mit vorhandener Administratorrolle** tritt bei einem Benutzerobjekt während der Synchronisierung auf, wenn dieses Benutzerobjekt:

- administrative Berechtigungen aufweist und
- denselben „UserPrincipalName“ wie ein vorhandenes Azure AD-Objekt besitzt.

Azure AD Connect darf keinen Soft Match eines Benutzerobjekts aus dem lokalen AD mit einem Benutzerobjekt in Azure AD vornehmen, dem eine Administratorrolle zugewiesen ist.  Weitere Informationen finden Sie unter [Auffüllung des UserPrincipalName-Attributs in Azure AD](plan-connect-userprincipalname.md).

![Vorhandener Administrator](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>So behebt man den Fehler
Führen Sie folgende Schritte aus, um dieses Problem zu beheben:

1. Entfernen Sie das Azure AD-Konto (Besitzer) aus allen Administratorrollen. 
2. Führen Sie für das Objekt „Unter Quarantäne“ in der Cloud das **endgültige Löschen** durch. 
3. Beim nächsten Synchronisierungszyklus wird der lokale Benutzer dem Cloudkonto per Soft Match-Vorgang hinzugefügt (da es sich beim Cloudbenutzer nicht mehr um einen globalen Administrator handelt). 
4. Stellen Sie die Rollenmitgliedschaften für den Besitzer wieder her. 

>[!NOTE]
>Sie können die Administratorrolle dem vorhandenen Benutzerobjekt erneut zuweisen, nachdem der Soft Match zwischen dem lokalen Benutzerobjekt und dem Azure AD-Benutzerobjekt abgeschlossen ist.

## <a name="related-links"></a>Verwandte Links
* [In Active Directory-Verwaltungscenter nach Active Directory-Objekten suchen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560661(v=ws.10))
* [Verwalten von Azure AD mit Windows PowerShell](/previous-versions/azure/jj151815(v=azure.100))