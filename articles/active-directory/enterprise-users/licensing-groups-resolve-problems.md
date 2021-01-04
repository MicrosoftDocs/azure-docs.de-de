---
title: Beheben von Problemen bei Gruppenlizenzzuweisungen – Azure Active Directory | Microsoft-Dokumentation
description: Vorgehensweise zum Identifizieren und Beheben von Lizenzzuweisungsproblemen bei Verwendung der gruppenbasierten Lizenzierung mit Azure Active Directory
services: active-directory
keywords: Azure AD-Lizenzierung
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc3dce8bb51299eb78bd2958eebf7a69594318a4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546470"
---
# <a name="identify-and-resolve-license-assignment-problems-for-a-group-in-azure-active-directory"></a>Identifizieren und Beheben von Lizenzzuweisungsproblemen für eine Gruppe in Azure Active Directory

Mit der gruppenbasierten Lizenzierung in Azure Active Directory (Azure AD) wird für Benutzer das Konzept eines Fehlerzustands für die Lizenzierung eingeführt. In diesem Artikel werden die Gründe dafür beschrieben, warum Benutzer sich in diesem Zustand befinden können.

Wenn Sie Lizenzen direkt einzelnen Benutzern zuweisen, ohne die gruppenbasierte Lizenzierung zu verwenden, tritt für den Zuweisungsvorgang ggf. ein Fehler auf. Wenn Sie beispielsweise das PowerShell-Cmdlet `Set-MsolUserLicense` auf einem Benutzersystem ausführen, kann für das Cmdlet aufgrund der Geschäftslogik aus verschiedensten Gründen ein Fehler auftreten. Beispielsweise kann die Anzahl von Lizenzen nicht ausreichen, oder es kann ein Konflikt zwischen zwei Dienstplänen bestehen, die nicht gleichzeitig zugewiesen werden können. Das Problem wird Ihnen sofort gemeldet.

Bei Verwendung der gruppenbasierten Lizenzierung können die gleichen Fehler auftreten. Diese treten allerdings im Hintergrund auf, während der Azure AD-Dienst Lizenzen zuweist. Daher können Ihnen die Fehler nicht sofort gemeldet werden. Stattdessen werden sie im Benutzerobjekt aufgezeichnet und anschließend über das Verwaltungsportal gemeldet. Das ursprüngliche Ziel (die Lizenzierung Benutzers) bleibt stets erhalten und wird im Falle eines Fehlers zur künftigen Untersuchung und Behebung aufgezeichnet.

## <a name="find-license-assignment-errors"></a>Suchen von Fehlern bei der Lizenzzuweisung

### <a name="to-find-users-in-an-error-state-in-a-group"></a>So suchen Sie Benutzer mit einem Fehlerzustand in einer Gruppe

1. Öffnen Sie die Übersichtsseite der Gruppe, und wählen Sie **Lizenzen** aus. Falls Benutzer mit einem Fehlerzustand vorhanden sind, wird eine Benachrichtigung angezeigt.

   ![Gruppen- und Fehlerbenachrichtigungsnachricht](./media/licensing-groups-resolve-problems/group-error-notification.png)

1. Wählen Sie die Benachrichtigung aus, um eine Liste mit allen betroffenen Benutzern zu öffnen. Sie können jeden Benutzer einzeln auswählen, um weitere Details anzuzeigen.

   ![Liste der Benutzer im Gruppenlizenzierungs-Fehlerzustand](./media/licensing-groups-resolve-problems/list-of-users-with-errors.png)

1. Um nach allen Gruppen zu suchen, die mindestens einen Fehler enthalten, klicken Sie auf dem Blatt **Azure Active Directory** erst auf **Lizenzen** und dann auf **Übersicht**. Sollte für Gruppen eine Aktion erforderlich sein, wird ein Informationsfeld angezeigt.

   ![Übersicht und Informationen zu Gruppen im Fehlerzustand](./media/licensing-groups-resolve-problems/group-errors-widget.png)

1. Klicken Sie auf das Feld, um eine Liste aller Gruppen mit Fehlern anzuzeigen. Klicken Sie bei Bedarf auf die einzelnen Gruppen, um weitere Details anzuzeigen.

   ![Übersicht und Liste der Gruppen mit Fehlern](./media/licensing-groups-resolve-problems/list-of-groups-with-errors.png)

In den folgenden Abschnitten werden die einzelnen potenziellen Probleme sowie die dazugehörigen Lösungsmöglichkeiten beschrieben.

## <a name="not-enough-licenses"></a>Nicht genügend Lizenzen

**Problem:** Es sind nicht genügend Lizenzen für eines der in der Gruppe angegebenen Produkte verfügbar. Sie müssen entweder weitere Lizenzen für das Produkt erwerben oder nicht verwendete Lizenzen anderer Benutzer oder Gruppen freigeben.

Um anzuzeigen, wie viele Lizenzen verfügbar sind, wechseln Sie zu **Azure Active Directory** > **Lizenzen** > **Alle Produkte**.

Klicken Sie auf ein Produkt, um zu ermitteln, welche Benutzer und Gruppen Lizenzen verwenden. Unter **Lizenzierte Benutzer** wird eine Liste mit allen Benutzern angezeigt, denen Lizenzen zugewiesen wurden (direkt oder über Gruppen). Unter **Lizenzierte Gruppen** sind alle Gruppen aufgeführt, denen Produkte zugewiesen sind.

**PowerShell:** PowerShell-Cmdlets melden diesen Fehler als _CountViolation_.

## <a name="conflicting-service-plans"></a>In Konflikt stehende Diensteeinstellungen

**Problem:** Eines der in der Gruppe angegebenen Produkte enthält einen Dienstplan, der in Konflikt mit einem anderen Dienstplan steht, der dem Benutzer bereits über ein anderes Produkt zugewiesen ist. Einige Dienstpläne sind so konfiguriert, dass sie dem gleichen Benutzer nicht als anderer verwandter Dienstplan zugewiesen werden können.

Betrachten Sie das folgende Beispiel. Einem Benutzer ist eine Lizenz für Office 365 Enterprise *E1* direkt zugewiesen worden, wobei alle Pläne aktiviert wurden. Der Benutzer wurde einer Gruppe hinzugefügt, der das Produkt Office 365 Enterprise *E3* zugewiesen ist. Das E3-Produkt enthält Dienstpläne, die sich nicht mit den in E1 enthaltenen Plänen überschneiden dürfen. Daher tritt bei der Gruppenlizenzzuweisung der Fehler „Widersprüchliche Servicepläne“ auf. Bei diesem Beispiel stehen die folgenden Dienstpläne in Konflikt:

- SharePoint Online (Plan 2) steht in Konflikt mit SharePoint Online (Plan 1).
- Exchange Online (Plan 2) steht in Konflikt mit Exchange Online (Plan 1).

Zur Behebung dieses Konflikts müssen zwei der Pläne deaktiviert werden. Sie können die dem Benutzer direkt zugewiesene E1-Lizenz deaktivieren. Eine andere Möglichkeit besteht darin, die gesamte Gruppenlizenzzuweisung zu ändern und die Pläne für die E3-Lizenz zu deaktivieren. Alternativ können Sie dem Benutzer auch die E1-Lizenz entziehen, falls sie im Kontext der E3-Lizenz redundant ist.

Die Entscheidung, wie der Produktlizenzkonflikt zu lösen ist, liegt stets beim Administrator. Azure AD löst Lizenzkonflikte nicht automatisch.

**PowerShell:** PowerShell-Cmdlets melden diesen Fehler als _MutuallyExclusiveViolation_.

## <a name="other-products-depend-on-this-license"></a>Andere Produkte sind von dieser Lizenz abhängig

**Problem:** Eines der in der Gruppe angegebenen Produkte enthält einen Dienstplan, der für einen anderen Dienstplan (in einem anderen Produkt) aktiviert werden muss, um zu funktionieren. Dieser Fehler tritt auf, wenn Azure AD versucht, den zugrunde liegenden Dienstplan zu entfernen. Dieser Fall kann eintreten, wenn Sie den Benutzer aus der Gruppe entfernen.

Stellen Sie zur Behebung dieses Problems sicher, dass der erforderliche Plan Benutzern weiterhin über eine andere Methode zugewiesen ist oder dass die abhängigen Dienste für diese Benutzer deaktiviert sind. Anschließend können Sie die Gruppenlizenz für diese Benutzer richtig entfernen.

**PowerShell:** PowerShell-Cmdlets melden diesen Fehler als _DependencyViolation_.

## <a name="usage-location-isnt-allowed"></a>Verwendungsstandort ist nicht zulässig

**Problem:** Einige Microsoft-Dienste sind aufgrund der vor Ort geltenden Gesetze und Bestimmungen nicht an allen Standorten verfügbar. Um eine Lizenz einem Benutzer zuweisen können, müssen Sie zunächst die Eigenschaft **Usage location** (Verwendungsstandort) für den Benutzer angeben. Den Standort können Sie im Azure-Portal unter **Benutzer** > **Profil** > **Bearbeiten** angeben.

Wenn Azure AD versucht, einem Benutzer, dessen Verwendungsstandort nicht unterstützt wird, eine Gruppenlizenz zuzuweisen, tritt ein Fehler auf, der für den Benutzer aufgezeichnet wird.

Sie können dieses Problem beheben, indem Sie Benutzer von nicht unterstützten Standorten aus der lizenzierten Gruppe entfernen. Alternativ können Sie wie folgt vorgehen: Wenn die aktuellen Werte des Verwendungsstandorts nicht den tatsächlichen Benutzerstandort darstellen, können Sie sie ändern, damit die Lizenzen beim nächsten Mal richtig zugewiesen werden (wenn der neue Standort unterstützt wird).

**PowerShell:** PowerShell-Cmdlets melden diesen Fehler als _ProhibitedInUsageLocationViolation_.

> [!NOTE]
> Wenn von Azure AD Gruppenlizenzen zugewiesen werden, erben alle Benutzer ohne Verwendungsstandort den Standort des Verzeichnisses. Es wird empfohlen, dass Administratoren die richtigen Werte für den Verwendungsstandort von Benutzern festlegen, bevor sie die gruppenbasierte Lizenzierung verwenden, um die vor Ort geltenden Gesetze und Bestimmungen zu erfüllen.

## <a name="duplicate-proxy-addresses"></a>Doppelte Proxyadressen

Bei Verwendung von Exchange Online sind unter Umständen einige Benutzer in Ihrer Organisation fälschlicherweise mit dem gleichen Proxyadresswert konfiguriert. Wenn bei der gruppenbasierten Lizenzierung versucht wird, einem dieser Benutzer eine Lizenz zuzuweisen, tritt ein Fehler auf, und ein Hinweis der Art „Proxyadresse wird bereits verwendet“ wird angezeigt.

> [!TIP]
> Um zu ermitteln, ob eine doppelte Proxyadresse vorhanden ist, führen Sie das folgende PowerShell-Cmdlet für Exchange Online aus:
> ```
> Get-Recipient -ResultSize unlimited | where {$_.EmailAddresses -match "user@contoso.onmicrosoft.com"} | fL Name, RecipientType,emailaddresses
> ```
> Weitere Informationen zu diesem Problem finden Sie unter ["Proxy address is already being used" error message in Exchange Online](https://support.microsoft.com/help/3042584/-proxy-address-address-is-already-being-used-error-message-in-exchange-online) (Fehlermeldung „Proxyadresse wird bereits verwendet“ in Exchange Online). Der Artikel enthält auch Informationen zum [Herstellen einer Verbindung mit Exchange Online mithilfe einer PowerShell-Remotesitzung](/powershell/exchange/connect-to-exchange-online-powershell?view=exchange-ps).

Nachdem Sie die Probleme mit den Proxyadressen für die betroffenen Benutzer behoben haben, erzwingen Sie die Lizenzverarbeitung für diese Gruppe, um sicherzustellen, dass die Lizenzen nun zugewiesen werden können.

## <a name="azure-ad-mail-and-proxyaddresses-attribute-change"></a>Ändern von Azure AD Mail- und ProxyAddresses-Attribut

**Problem:** Beim Aktualisieren der Lizenzzuweisung für einen Benutzer oder eine Gruppe wird möglicherweise angezeigt, dass Azure AD Mail- und ProxyAddresses-Attribut geändert werden.

Beim Aktualisieren der Lizenzzuweisung für einen Benutzer wird die Berechnung der Proxyadresse ausgelöst, die Attribute der Benutzer ändern kann. Wie Sie die genaue Ursache der Änderung ermitteln und das Problem lösen, erfahren Sie in dem Artikel [Wie das Attribut ProxyAddresses in Azure AD aufgefüllt wird](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

## <a name="licenseassignmentattributeconcurrencyexception-in-audit-logs"></a>LicenseAssignmentAttributeConcurrencyException in Überwachungsprotokollen

**Problem:** Für den Benutzer enthalten die Überwachungsprotokolle eine LicenseAssignmentAttributeConcurrencyException für die Lizenzzuweisung.
Wenn die gruppenbasierte Lizenzierung versucht, gleichzeitige Lizenzzuweisungen derselben Lizenz zu einem Benutzer zu verarbeiten, wird diese Ausnahme für den Benutzer aufgezeichnet. Dies ist normalerweise der Fall, wenn ein Benutzer Mitglied mehrerer Gruppen ist, denen dieselbe Lizenz zugewiesen ist. Azure AD versucht erneut, die Benutzerlizenz zu verarbeiten und das Problem zu beheben. Es ist keine Aktion durch den Kunden erforderlich, um dieses Problem zu beheben.

## <a name="more-than-one-product-license-assigned-to-a-group"></a>Mehrere Produktlizenzen für eine Gruppe

Sie können einer Gruppe mehr als eine Produktlizenz zuweisen. Beispielsweise können Sie Office 365 Enterprise E3 und Enterprise Mobility + Security einer Gruppe zuweisen, um alle enthaltenen Dienste den Benutzern mühelos zur Verfügung zu stellen.

Azure AD versucht, den einzelnen Benutzern alle in der Gruppe angegebenen Lizenzen zuzuweisen. Falls Azure AD aufgrund von Problemen mit der Geschäftslogik eines der Produkte nicht zuweisen kann, werden die anderen Lizenzen in der Gruppe auch nicht zugewiesen. Dieser Fall kann beispielsweise eintreten, wenn nicht genügen Lizenzen zur Verfügung stehen oder wenn Konflikte mit anderen für den Benutzer aktivierten Diensten bestehen.

Sie können die Benutzer anzeigen, für die die Zuweisung nicht erfolgreich war, und ermitteln, welche Produkte von diesem Problem betroffen sind.

## <a name="when-a-licensed-group-is-deleted"></a>Wenn eine lizenzierte Gruppe gelöscht wird

Sie müssen alle einer Gruppe zugewiesenen Lizenzen entfernen, bevor Sie die Gruppe löschen können. Das Entfernen der Lizenzen aller Benutzer in der Gruppe kann jedoch einige Zeit in Anspruch nehmen. Beim Entfernen von Lizenzzuweisungen einer Gruppe können Fehler auftreten, wenn einem Benutzer eine abhängige Lizenz zugewiesen ist oder ein Konflikt mit einer Proxyadresse besteht, der das Entfernen der Lizenz verhindert. Wenn ein Benutzer über eine Lizenz verfügt, die von einer Lizenz abhängig ist, die aufgrund einer Gruppenlöschung entfernt wird, erfolgt für den Benutzer eine Umwandlung der Lizenzzuweisung von geerbt in direkt.

Beispiel: Einer Gruppe sind Office 365 E3-/E5-Lizenzen zugewiesen, und es ist ein Skype for Business-Dienstplan aktiviert. Darüber hinaus wurden einigen Mitgliedern der Gruppe Lizenzen für Audiokonferenz direkt zugewiesen. Wenn die Gruppe gelöscht wird, versucht die gruppenbasierte Lizenzierung, Office 365 E3/E5 für alle Benutzer zu entfernen. Weil Audiokonferenz von Skype for Business abhängig ist, wandelt die gruppenbasierte Lizenzierung die Office 365 E3-/E5-Lizenzen für alle Benutzer, denen Audiokonferenz zugewiesen ist, in direkte Lizenzzuweisungen um.

## <a name="manage-licenses-for-products-with-prerequisites"></a>Verwalten von Lizenzen für Produkte mit bestimmten Voraussetzungen

Bei einigen Ihrer Microsoft Online-Produkte handelt es sich unter Umständen um *Add-Ons*. Bei Add-Ons muss für einen Benutzer oder eine Gruppe ein Dienstplan mit Voraussetzungen aktiviert werden, bevor eine Lizenz zugewiesen werden kann. Bei der gruppenbasierten Lizenzierung müssen der Dienstplan mit den Voraussetzungen und der Add-On-Dienstplan in der gleichen Gruppe enthalten sein. Hierdurch wird sichergestellt, dass alle Benutzer, die der Gruppe hinzugefügt werden, das voll funktionsfähige Produkt erhalten können. Betrachten Sie das folgende Beispiel:

Microsoft Workplace Analytics ist ein Add-On-Produkt. Es enthält einen einzelnen Dienstplan mit demselben Namen. Dieser Dienstplan kann einem Benutzer (oder einer Gruppe) nur zugewiesen werden, wenn auch eine der folgenden Voraussetzungen zugewiesen ist:

- Exchange Online (Plan 1)
- Exchange Online (Plan 2)

Wenn Sie versuchen, dieses Produkt separat einer Gruppe zuzuweisen, gibt das Portal eine Benachrichtigung zurück. Nach dem Auswählen der Details des Elements wird die folgende Fehlermeldung angezeigt:

  „Fehler bei Lizenzvorgang. Stellen Sie sicher, dass die Gruppe die notwendigen Dienste besitzt, bevor Sie einen abhängigen Dienst hinzufügen oder entfernen. **Für den Dienst ‚Microsoft Workplace Analytics‘ muss auch ‚Exchange Online (Plan 2)‘ aktiviert werden.** “

Um diese Add-On-Lizenz einer Gruppe zuzuweisen, müssen wir sicherstellen, dass die Gruppe auch den Dienstplan mit der Voraussetzung enthält. Wir können beispielsweise eine vorhandene Gruppe aktualisieren, die bereits das vollständige Produkt „Office 365 E3“ enthält, und das Add-On-Produkt anschließend hinzufügen.

Es ist auch möglich, eine eigenständige Gruppe zu erstellen, die nur die mindestens erforderlichen Produkte für die Funktionsfähigkeit des Add-Ons enthält. Sie kann verwendet werden, um nur die ausgewählten Benutzer für das Add-On-Produkt zu lizenzieren. Basierend auf dem vorherigen Beispiel würden Sie derselben Gruppe die folgenden Produkte zuweisen:

- Office 365 Enterprise E3, wobei nur der Dienstplan „Exchange Online (Plan 2)“ aktiviert ist
- Microsoft Workplace Analytics

Ab jetzt verwenden alle Benutzer, die dieser Gruppe hinzugefügt werden, eine Lizenz des E3-Produkts und eine Lizenz des Workplace Analytics-Produkts. Diese Benutzer können gleichzeitig Mitglieder einer anderen Gruppe sein, über die sie das vollständige E3-Produkt erhalten, und sie nutzen hierbei weiterhin nur eine Lizenz für das Produkt.

> [!TIP]
> Für jeden Dienstplan mit Voraussetzungen können mehrere Gruppen erstellt werden. Wenn Sie für Ihre Benutzer sowohl „Office 365 Enterprise E1“ als auch „Office 365 Enterprise E3“ verwenden, können Sie zwei Gruppen erstellen, um Microsoft Workplace Analytics zu lizenzieren: eine mit E1 als Voraussetzung und die andere mit E3. So können Sie das Add-On auf E1- und E3-Benutzer verteilen, ohne zusätzliche Lizenzen zu verbrauchen.

## <a name="force-group-license-processing-to-resolve-errors"></a>Erzwingen der Verarbeitung von Gruppenlizenzen zum Beheben von Fehlern

Je nachdem, welche Maßnahmen Sie ergriffen haben, um die Fehler zu beheben, müssen Sie die Verarbeitung einer Gruppe ggf. manuell auslösen, um den Benutzerzustand zu aktualisieren.

Wenn Sie beispielsweise einige Lizenzen freigegeben haben, indem Sie direkte Zuweisungen von Lizenzen für Benutzer entfernt haben, müssen Sie die Verarbeitung von Gruppen auslösen, für die zuvor ein Fehler aufgetreten ist. Nur so erreichen Sie die vollständige Versorgung von Benutzern mit Lizenzen. Navigieren Sie zum erneuten Verarbeiten einer Gruppe zum Namen der Gruppe, öffnen Sie **Lizenzen**, und klicken Sie auf der Symbolleiste auf **Erneut verarbeiten**.

## <a name="force-user-license-processing-to-resolve-errors"></a>Erzwingen der Verarbeitung von Benutzerlizenzen zum Beheben von Fehlern

Je nachdem, welche Maßnahmen Sie ergriffen haben, um die Fehler zu beheben, müssen Sie die Verarbeitung eines Benutzers ggf. manuell auslösen, um den Benutzerzustand zu aktualisieren.

Sie müssen beispielsweise die Verarbeitung des Benutzers auslösen, nachdem Sie das Problem mit der doppelten Proxyadresse für einen betroffenen Benutzer behoben haben. Navigieren Sie zum erneuten Verarbeiten eines Benutzers zum entsprechenden Benutzerbereich, öffnen Sie **Lizenzen**, und klicken Sie auf der Symbolleiste auf **Erneut verarbeiten**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Szenarien für die Lizenzverwaltung über Gruppen finden Sie unter:

* [Was ist die gruppenbasierte Lizenzierung in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Zuweisen von Lizenzen zu einer Gruppe in Azure Active Directory](./licensing-groups-assign.md)
* [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung in Azure Active Directory](licensing-groups-migrate-users.md)
* [Sicheres Migrieren von Benutzern zwischen Produktlizenzen mithilfe von gruppenbasierter Lizenzierung in Azure Active Directory](licensing-groups-change-licenses.md)
* [Gruppenbasierte Azure Active Directory-Lizenzierung – zusätzliche Szenarien](./licensing-group-advanced.md)
* [PowerShell-Beispiele für die gruppenbasierte Lizenzierung in Azure AD](licensing-ps-examples.md)