---
title: 'Azure AD Connect: Wenn Sie bereits über Azure AD verfügen | Microsoft-Dokumentation'
description: Dieses Thema beschreibt die Verwendung von Connect, wenn ein Azure AD-Mandant vorhanden ist.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420528"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: Wenn Sie bereits über einen Mandanten verfügen
Bei den meisten Themen zur Verwendung von Azure AD Connect wird davon ausgegangen, dass Sie mit einem neuen Azure AD-Mandanten starten und dass es keine weiteren Benutzer oder Objekte gibt. Wenn Sie jedoch bereits über einen Azure AD-Mandanten verfügen, der mit Benutzern und anderen Objekten aufgefüllt ist, und jetzt Connect verwenden möchten, eignet sich dieses Thema perfekt für Sie.

## <a name="the-basics"></a>Grundlagen
Ein Objekt in Azure AD wird entweder in der Cloud (Azure AD) oder lokal verwaltet. Bei nur einem Objekt ist es nicht möglich, einige Attribute lokal und andere Attribute in Azure AD zu verwalten. Jedes Objekt verfügt über ein Flag, das angibt, wo das Objekt verwaltet wird.

Sie können einige Benutzer lokal und andere in der Cloud verwalten. Ein häufiges Szenario für diese Konfiguration ist eine Organisation, die sowohl über Buchhaltungs- als auch Vertriebsmitarbeiter verfügt. Die Buchhaltungsmitarbeiter haben ein lokales AD-Konto, aber die Vertriebsmitarbeiter nicht – sie haben ein Konto in Azure AD. Daher müssen Sie einige Benutzer lokal und einige in Azure AD verwalten.

Wenn Sie damit begonnen haben, Benutzer in Azure AD zu verwalten, die sich auch im lokalen Azure Directory befinden, und später Connect verwenden möchten, sind einige weitere Aspekte zu berücksichtigen.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronisieren mit vorhandenen Benutzern in Azure AD
Wenn Sie Azure AD Connect installieren und mit der Synchronisierung beginnen, überprüft der Azure AD-Synchronisierungsdienst jedes neue Objekt (in Azure AD) und versucht, ein entsprechendes vorhandenes Objekt zu finden. Für diesen Prozess werden drei Attribute verwendet: **userPrincipalName** , **proxyAddresses** und **sourceAnchor**/**immutableID**. Eine Übereinstimmung bei **userPrincipalName** und **proxyAddresses** wird als **Soft Match** bezeichnet. Eine Übereinstimmung bei **sourceAnchor** wird als **Hard Match** bezeichnet. Für das Attribut **proxyAddresses** wird zur Auswertung nur der Wert mit **SMTP:** verwendet – dies ist die primäre E-Mail-Adresse.

Die Übereinstimmung wird nur für neue, aus Connect eingehende Objekte ausgewertet. Wenn Sie ein vorhandenes Objekt so ändern, dass es einem dieser Attribute entspricht, wird stattdessen ein Fehler angezeigt.

Wenn Azure AD ein Objekt findet, dessen Attributwerte die gleichen sind wie für ein aus Connect eingehendes Objekt, und das Objekt bereits in Azure AD vorhanden ist, wird das Objekt in Azure AD von Connect übernommen. Das zuvor in der Cloud verwaltete Objekt wird als „lokal verwaltet“ gekennzeichnet. Alle Attribute in Azure AD mit einem Wert im lokalen Active Directory werden mit dem lokalen Wert überschrieben.

> [!WARNING]
> Da alle Attribute in Azure AD durch ihre lokalen Werte überschrieben werden, stellen Sie sicher, dass Ihre lokalen Daten richtig und vollständig sind. Ein Beispiel: Wenn Sie E-Mail-Adressen nur in Microsoft 365 verwaltet und im lokalen AD DS nicht aktualisiert haben, gehen alle Werte aus Azure AD/Microsoft 365 verloren, die nicht im AD DS vorhanden sind.

> [!IMPORTANT]
> Wenn Sie die Kennwortsynchronisierung verwenden – die bei Expresseinstellungen immer zum Einsatz kommt –, wird das Kennwort in Azure AD durch das Kennwort im lokalen Active Directory überschrieben. Wenn Ihre Benutzer daran gewöhnt sind, verschiedene Kennwörter zu verwenden, müssen Sie sie darüber informieren, dass sie ihr lokales Kennwort verwenden müssen, nachdem Sie Connect installiert haben.

Die Warnungen und Informationen im vorherigen Abschnitt müssen in Ihrer Planung berücksichtigt werden. Wenn Sie Änderungen in Azure AD vorgenommen haben, die nicht im lokalen AD DS übernommen wurden, müssen Sie planen, wie der AD DS mit den aktualisierten Werten aufgefüllt werden soll, bevor Sie Ihre Objekte mithilfe von Azure AD Connect synchronisieren.

Wenn Sie Ihre Objekte mit einem Soft Match abgeglichen haben, wird dem Objekt in Azure AD ein **sourceAnchor** hinzugefügt, sodass später ein Hard Match verwendet werden kann.

>[!IMPORTANT]
> Microsoft rät dringend davon ab, lokale Konten mit bereits vorhandenen Verwaltungskonten in Azure Active Directory zu synchronisieren.

### <a name="hard-match-vs-soft-match"></a>Hard Match im Vergleich zu Soft Match
Bei einer neuen Connect-Installation gibt es keinen praktischen Unterschied zwischen einem Soft Match und einem Hard Match. Der Unterschied kommt bei einer Notfallwiederherstellung zum Tragen. Wenn der Server mit Azure AD Connect nicht mehr vorhanden ist, können Sie eine neue Instanz installieren, ohne dass Daten verloren gehen. Während der Erstinstallation wird ein Objekt mit einem sourceAnchor-Parameter an Connect gesendet. Die Übereinstimmung kann dann vom Client (Azure AD Connect) ausgewertet werden – dies ist erheblich schneller als das gleiche Vorgehen in Azure AD. Ein Hard Match wird sowohl von Connect als auch von Azure AD ausgewertet. Ein Soft Match wird nur von Azure AD ausgewertet.

### <a name="other-objects-than-users"></a>Andere Objekte als Benutzer
Für e-mail-aktivierte Gruppen und Kontakte können Sie ein Soft-Match basierend auf proxyAddresses durchführen. Ein Hard-Match ist nicht anwendbar, da Sie „sourceAnchor/immutableID“ (mithilfe von PowerShell) nur für Benutzer aktualisieren können. Für nicht e-mail-aktivierte Gruppen gibt es derzeit weder Soft-Match- noch Hard-Match-Unterstützung.

### <a name="admin-role-considerations"></a>Überlegungen zu Administratorrollen
Um zu verhindern, dass nicht vertrauenswürdige lokale Benutzer einem Cloudbenutzer mit einer Administratorrolle zugeordnet werden, ordnet Azure AD Connect Objekten mit Administratorrolle keine lokalen Benutzerobjekte zu. Dies ist die Standardeinstellung. Um dieses Verhalten zu umgehen, können Sie die folgenden Schritte ausführen:

1.  Entfernen Sie die Verzeichnisrollen von dem reinen Cloudbenutzerobjekt.
2.  Wenn bei einem Benutzersynchronisierungsversuch ein Fehler aufgetreten ist, löschen Sie das unter Quarantäne gestellte Objekt in der Cloud endgültig.
3.  Führen Sie eine Synchronisierung aus.
4.  Fügen Sie optional die Verzeichnisrollen dem Benutzerobjekt in der Cloud wieder hinzu, nachdem die Zuordnung stattgefunden hat.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Erstellen eines neuen lokalen Active Directory aus Daten in Azure AD
Einige Kunden beginnen mit einer reinen Cloudlösung mit Azure AD und verfügen nicht über ein lokales Active Directory. Später möchten diese Kunden ihre lokalen Ressourcen nutzen und ein lokales Active Directory basierend auf den Azure AD-Daten aufbauen. Für dieses Szenario kann Azure AD Connect nicht eingesetzt werden. Connect erstellt keine lokalen Benutzer und bietet keine Möglichkeit, lokale Kennwörter auf die gleichen Kennwörter festzulegen, die in Azure AD verwendet werden.

Wenn der einzige Grund, aus dem Sie ein lokales Active Directory planen, die Unterstützung von branchenspezifischen Apps ist, sollten Sie stattdessen die Verwendung von [Azure AD Domain Services](../../active-directory-domain-services/index.yml) erwägen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
