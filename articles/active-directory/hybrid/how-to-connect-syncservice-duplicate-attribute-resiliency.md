---
title: Identitätssynchronisierung und Resilienz bei doppelten Attributen | Microsoft Docs
description: Neues Verhalten zur Behandlung von Objekten mit UPN- oder ProxyAddress-Konflikten bei der Verzeichnissynchronisierung mit Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413427"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Identitätssynchronisierung und Resilienz bei doppelten Attributen
Die Resilienz bei doppelten Attributen ist ein Feature von Azure Active Directory, das der Beseitigung von Konflikten zwischen **UserPrincipalName** und SMTP- **ProxyAddress** dient, die beim Ausführen eines Synchronisierungstools von Microsoft auftreten können.

Die beiden Attribute müssen generell für alle Objekte des Typs **User** , **Group** oder **Contact** eines bestimmten Azure Active Directory-Mandanten eindeutig sein.

> [!NOTE]
> Nur Benutzer können über UPNs verfügen.
> 
> 

Das durch dieses neue Feature ermöglichte Verhalten befindet sich im Cloudteil der Synchronisierungspipeline und ist damit clientunabhängig und für jedes Synchronisierungsprodukt von Microsoft relevant – einschließlich Azure AD Connect, DirSync und MIM + Connector. Diese Produkte werden hier unter dem allgemeinen Begriff „Synchronisierungsclient“ zusammengefasst.

## <a name="current-behavior"></a>Aktuelles Verhalten
Beim Versuch, ein neues Objekt mit einem UPN- oder ProxyAddress-Wert bereitzustellen, der die Eindeutigkeitsanforderung nicht erfüllt, wird die Erstellung des Objekts durch Azure Active Directory blockiert. Analog dazu gilt: Ein Objekt kann nicht mit einem nicht eindeutigen UPN- oder ProxyAddress-Wert aktualisiert werden. Der Bereitstellungs- oder Aktualisierungsversuch wird vom Synchronisierungsclient bei jedem Exportzyklus wiederholt und kann erst nach Beseitigung des Konflikts erfolgreich abgeschlossen werden. Bei jedem Versuch wird eine E-Mail mit einem Fehlerbericht generiert, und vom Synchronisierungsclient wird ein Fehler protokolliert.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Verhalten mit Resilienz bei doppelten Attributen
Das Bereitstellen oder Aktualisieren eines Objekts mit doppeltem Attribut ist nicht einfach ohne Erfolg. Stattdessen wird das doppelte Attribut, das gegen die Eindeutigkeitsanforderung verstößt, von Azure Active Directory isoliert. Ist dieses Attribut für die Bereitstellung erforderlich (wie etwa im Falle von UserPrincipalName), weist der Dienst einen Platzhalterwert zu. Diese temporären Werte weisen folgendes Format auf:  
_**\<OriginalPrefix>+\<4DigitNumber>\@\<InitialTenantDomain>.onmicrosoft.com**_.

Der Prozess für die Resilienz von Attributen verarbeitet nur UPN- und SMTP- **ProxyAddress** -Werte.

Ist das Attribut nicht erforderlich (etwa im Falle von **ProxyAddress** ), wird das Konfliktattribut einfach von Azure Active Directory unter Quarantäne gestellt, und die Objekterstellung oder -aktualisierung wird fortgesetzt.

Im Falle einer Attributisolierung werden Informationen zum Konflikt in der gleichen Fehlerbericht-E-Mail gesendet, die auch im Rahmen des alten Verhaltens verwendet wurde. Diese Informationen werden aber nur einmal (zum Zeitpunkt der Isolierung) in den Fehlerbericht aufgenommen und in zukünftigen E-Mails nicht immer wieder erneut protokolliert. Da der Export für das Objekt erfolgreich war, protokolliert der Synchronisierungsclient keinen Fehler, und es wird in den folgenden Synchronisierungszyklen nicht erneut versucht, die Erstellung/Aktualisierung durchzuführen.

Zur Unterstützung dieses Verhaltens wurde den Objektklassen für Benutzer, Gruppen und Kontakte ein neues Attribut hinzugefügt:  
**DirSyncProvisioningErrors**

Hierbei handelt es sich um ein mehrwertiges Attribut zum Speichern der in Konflikt stehenden Attribute, die gegen die Eindeutigkeitsanforderung verstoßen würden, wenn sie normal hinzugefügt würden. In Azure Active Directory wurde eine Timer-Hintergrundaufgabe hinzugefügt, um stündlich nach behobenen Konflikten mit doppelten Attributen zu suchen und automatisch die Isolation der betreffenden Attribute aufzuheben.

### <a name="enabling-duplicate-attribute-resiliency"></a>Aktivieren der Resilienz bei doppelten Attributen
Resilienz bei doppelten Attributen wird in allen Azure Active Directory-Mandanten das neue Standardverhalten sein. Es wird standardmäßig für alle Mandanten eingeschaltet sein, die die Synchronisierung zum ersten Mal am 22. August 2016 oder später aktiviert haben. Bei Mandanten, die die Synchronisierung vor diesem Datum aktiviert haben, wird das Feature mithilfe von Batches aktiviert. Dieses Rollout wird im September 2016 beginnen. Die Benachrichtigung über das genaue Aktivierungsdatum des Features wird an die technische Kontaktadresse der Mandaten gesendet.

> [!NOTE]
> Nach der Aktivierung der Resilienz bei doppelten Attributen kann diese nicht mehr deaktiviert werden.

Durch Herunterladen der neuesten Version des PowerShell-Moduls von Azure Active Directory und Ausführen der folgenden Befehle kann geprüft werden, ob das Feature für Ihren Mandanten aktiviert ist:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> Die Funktion für Resilienz bei doppelten Attributen kann vor der Aktivierung für den Mandanten nicht mehr proaktiv mithilfe des Cmdlets „Set-MsolDirSyncFeature“ aktiviert werden. Damit Sie die Funktion testen können, müssen Sie einen neuen Azure Active Directory-Mandanten erstellen.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Ermitteln von Objekten mit DirSyncProvisioningErrors
Objekte mit Fehlern aufgrund von Konflikten mit doppelten Eigenschaften können derzeit auf zwei Arten ermittelt werden: über Azure Active Directory PowerShell und über das [Microsoft 365 Admin Center](https://admin.microsoft.com). Es ist geplant, dieses Feature im Laufe der Zeit um weitere Portale zu erweitern.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
Für die PowerShell-Cmdlets in diesem Thema gilt Folgendes:

* Bei allen der folgenden Cmdlets muss die Groß-/Kleinschreibung beachtet werden.
* **–ErrorCategory PropertyConflict** muss immer eingeschlossen werden. Derzeit sind keine anderen Arten von **ErrorCategory** vorhanden, dies kann sich jedoch in Zukunft ändern.

Führen Sie zunächst **Connect-MsolService** aus, und geben Sie Anmeldeinformationen für einen Mandantenadministrator ein.

Verwenden Sie anschließend die folgenden Cmdlets und Operatoren, um Fehler auf verschiedene Arten anzuzeigen:

1. [Alle anzeigen](#see-all)
2. [Nach Eigenschaftstyp](#by-property-type)
3. [Nach Konfliktwert](#by-conflicting-value)
4. [Mithilfe einer Zeichenfolgensuche](#using-a-string-search)
5. Sortiert
6. [Eingeschränkte Menge oder alle](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>Alle anzeigen
Führen Sie nach dem Herstellen der Verbindung den folgenden Befehl aus, um eine allgemeine Liste mit Attributbereitstellungsfehlern für den Mandanten anzuzeigen:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Das Ergebnis sieht beispielsweise wie folgt aus:  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>Nach Eigenschaftstyp
Wenn Sie Fehler nach Eigenschaftstyp anzeigen möchten, fügen Sie das Flag **-PropertyName** mit dem Argument **UserPrincipalName** oder **ProxyAddresses** hinzu:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

oder

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Nach Konfliktwert
Wenn Sie Fehler für eine bestimmte Eigenschaft anzeigen möchten, fügen Sie das Flag **-PropertyValue** hinzu (in diesem Fall muss auch **-PropertyName** verwendet werden):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>Mithilfe einer Zeichenfolgensuche
Verwenden Sie für eine allgemeine Zeichenfolgensuche das Flag **-SearchString** . Dieses Flag kann unabhängig von den weiter oben genannten Flags verwendet werden – mit Ausnahme von **-ErrorCategory PropertyConflict** , das immer erforderlich ist:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Eingeschränkte Menge oder alle
1. Mit **MaxResults \<Int>** kann die Abfrage auf eine bestimmte Anzahl von Werten beschränkt werden.
2. **All** sicherstellen, dass alle Ergebnisse abgerufen werden.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 Admin Center
Sie können Fehler bei der Verzeichnissynchronisierung im Microsoft 365 Admin Center anzeigen. Der Bericht im Microsoft 365 Admin Center enthält nur Objekte vom Typ **User** , für die diese Fehler vorliegen. Er enthält keine Informationen zu Konflikten zwischen Objekten des Typs **Gruppen** und **Kontakte**.

![Screenshot: Fehler bei der Verzeichnissynchronisierung im Microsoft 365 Admin Center](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "Aktive Benutzer")

Eine Anleitung zum Anzeigen von Fehlern bei der Verzeichnissynchronisierung im Microsoft 365 Admin Center finden Sie unter [Ermitteln von Fehlern der Verzeichnissynchronisierung in Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).

### <a name="identity-synchronization-error-report"></a>Fehlerbericht für die Identitätssynchronisierung
Wenn dieses neue Verhalten bei einem Objekt mit einem Konflikt aufgrund eines doppelten Attributs angewendet wird, enthält die standardmäßige Fehlerberichts-E-Mail für die Identitätssynchronisierung, die an den Kontakt für technische Benachrichtigungen des Mandanten gesendet wird, eine entsprechende Benachrichtigung. Bei diesem Verhalten gibt es jedoch eine wichtige Änderung. In der Vergangenheit wurden Informationen zu einem Konflikt aufgrund eines doppelten Attributs in jeden nachfolgenden Fehlerbericht einbezogen, bis der Konflikt behoben wurde. Bei Verwendung des neuen Verhaltens erscheint die Fehlerbenachrichtigung für einen bestimmten Konflikt lediglich einmal (zu dem Zeitpunkt, zu dem das in Konflikt stehende Attribut isoliert wird).

Hier sehen Sie ein Beispiel für eine E-Mail-Benachrichtigung bei einem ProxyAddress-Konflikt:  
    ![Screenshot: Beispiel für eine E-Mail-Benachrichtigung bei einem ProxyAddress-Konflikt](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktive Benutzer")  

## <a name="resolving-conflicts"></a>Beheben von Konflikten
Zur Behandlung von Fehlern aufgrund von doppelten Attributen werden die gleichen Strategien und Vorgehensweisen verwendet wie zuvor. Der einzige Unterschied besteht darin, dass für den Mandanten automatisch dienstseitig die Timer-Aufgabe ausgeführt wird, um dem entsprechenden Objekt nach Behebung des Konflikts das betreffende Attribut hinzuzufügen.

Der folgende Artikel beschäftigt sich mit verschiedenen Strategien zur Problembehandlung und -behebung: [Duplizierte oder ungültige Attribute verhindern die Verzeichnissynchronisierung in Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Bekannte Probleme
Keines dieser bekannten Probleme führt zu Datenverlusten oder Dienstbeeinträchtigungen. Einige sind kosmetischer Natur, andere führen dazu, dass das in Konflikt stehende Attribut nicht isoliert und stattdessen ein Standardfehler wie *vor der Resilienz* bei doppelten Attributen ausgelöst wird, und wieder ein anderer bewirkt, dass bei bestimmten Fehlern zusätzliche manuelle Korrekturen erforderlich sind.

**Grundverhalten:**

1. Bei Objekten mit einer bestimmten Attributkonfiguration treten immer wieder Exportfehler auf, obwohl doppelte Attribute eigentlich isoliert werden sollten.  
   Beispiel:
   
    a. Der neue Benutzer wird in Active Directory mit dem UPN **Joe\@contoso.com** und dem ProxyAddress-Wert **smtp:Joe\@contoso.com** erstellt.
   
    b. Die Eigenschaften dieses Objekts stehen mit einer vorhandenen Gruppe mit dem ProxyAddress-Wert **SMTP:Joe\@contoso.com** in Konflikt.
   
    c. Beim Exportieren werden die in Konflikt stehenden Attribute nicht isoliert. Stattdessen wird ein Fehler vom Typ **ProxyAddress-Konflikt** ausgegeben. Der Vorgang wird wie vor der Aktivierung des Resilienzfeatures in jedem nachfolgenden Synchronisierungszyklus wiederholt.
2. Wenn zwei Gruppen lokal mit der gleichen SMTP-Adresse erstellt werden, kann eine davon beim ersten Versuch nicht erfolgreich bereitgestellt werden, und es tritt ein Standardfehler für doppelte Werte vom Typ **ProxyAddress** auf. Der doppelte Wert wird beim nächsten Synchronisierungszyklus aber richtig isoliert.

**Bericht des Office-Portals:**

1. Die ausführliche Fehlermeldung für zwei Objekte in einem UPN-Konfliktsatz ist identisch. Sie gibt an, dass bei beiden der UPN-Wert geändert/isoliert wurde, obwohl sich eigentlich nur die Daten eines der Objekte geändert haben.
2. Die ausführliche Fehlermeldung für einen UPN-Konflikt enthält den falschen displayName-Wert für einen Benutzer, dessen UPN geändert/isoliert wurde. Beispiel:
   
    a. Zunächst wird **Benutzer A** mit **UPN = User\@contoso.com** synchronisiert.
   
    b. Anschließend wird versucht, **Benutzer B** mit **UPN = User\@contoso.com** zu synchronisieren.
   
    c. Der UPN von **Benutzer B** wird in **User1234\@contoso.onmicrosoft.com** geändert, und **User\@contoso.com** wird **DirSyncProvisioningErrors**  hinzugefügt.
   
    d. Die Fehlermeldung für **Benutzer B** sollte angeben, dass **Benutzer A** bereits den UPN-Wert **User\@contoso.com** besitzt, aber den displayName-Wert von **Benutzer B** anzeigt.

**Fehlerbericht für die Identitätssynchronisierung** :

Der Link zur *Anleitung zum Beheben dieses Problems* ist nicht korrekt:  
    ![Aktive Benutzer](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "Aktive Benutzer")  

Er sollte auf [https://aka.ms/duplicateattributeresiliency]() verweisen.

## <a name="see-also"></a>Weitere Informationen
* [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
* [Ermitteln von Fehlern der Verzeichnissynchronisierung in Microsoft 365](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)