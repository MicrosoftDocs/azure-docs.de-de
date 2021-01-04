---
title: Azure Enterprise-Übertragungen
description: Hier werden Azure EA-Übertragungen beschrieben.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: f5555662c6cb5f44be3959ec3d22d1cf1953674d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030092"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise-Übertragungen

Dieser Artikel bietet eine Übersicht über Enterprise-Übertragungen.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Übertragen eines Unternehmenskontos in eine neue Registrierung

Bei einer Kontoübertragung wird ein Kontobesitzer von einer Registrierung zu einer anderen verschoben. Alle zugehörigen Abonnements des Kontobesitzers werden zur Zielregistrierung verschoben. Verwenden Sie eine Kontoübertragung, wenn Sie über mehrere aktive Registrierungen verfügen und nur ausgewählte Kontobesitzer verschieben möchten.

Dieser Abschnitt dient nur zu Informationszwecken, da die Aktion von einem Unternehmensadministrator nicht durchgeführt werden kann. Es ist eine Supportanfrage erforderlich, um ein Unternehmenskonto in eine neue Registrierung zu übertragen.

Beachten Sie die folgenden Punkte, wenn Sie ein Unternehmenskonto in eine neue Registrierung übertragen:

- Nur die in der Anforderung angegebenen Konten werden übertragen. Wenn alle Konten ausgewählt sind, werden alle Konten übertragen.
- Bei der Quellregistrierung wird der Status „Aktiv“ oder „Erweitert“ beibehalten. Sie können die Registrierung weiterverwenden, bis sie abläuft.

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie eine Kontoübertragung anfordern, geben Sie die folgenden Informationen an:

- Die Nummer der Zielregistrierung, der Kontoname und die E-Mail-Adresse des Besitzers des zu übertragenden Kontos
- Für die Quellregistrierung: Registrierungsnummer und zu übertragendes Konto

Andere Punkte, die vor einer Kontoübertragung zu beachten sind:

- Die Genehmigung eines EA-Administrators ist für die Ziel- und Quellregistrierung erforderlich.
- Wenn eine Kontoübertragung Ihre Anforderungen nicht erfüllt, ziehen Sie eine Registrierungsübertragung in Betracht.
- Bei der Kontoübertragung werden alle Dienste und Abonnements des spezifischen Kontos übertragen.
- Nach Abschluss der Übertragung wird das übertragene Konto unter der Quellregistrierung als inaktiv und unter der Zielregistrierung als aktiv angezeigt.
- Im Konto wird das Enddatum angezeigt, das das effektive Übertragungsdatum für die Quellregistrierung und das Startdatum für die Zielregistrierung darstellt.
- Jegliche Nutzung, die für das Konto vor dem tatsächlichen Übertragungsdatum erfolgt ist, verbleibt unter der Quellregistrierung.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Übertragen einer Unternehmensregistrierung in eine neue Registrierung

Eine Registrierungsübertragung wird in folgenden Fällen in Betracht gezogen:

- Die Laufzeit der Vorauszahlung für eine aktuelle Registrierung ist abgelaufen.
- Eine Registrierung befindet sich im Status abgelaufen/verlängert und eine neue Vereinbarung wird ausgehandelt.
- Sie verfügen über mehrere Registrierungen und möchten alle Konten und Abrechnungen in einer einzigen Registrierung kombinieren.

Dieser Abschnitt dient nur zu Informationszwecken, da die Aktion von einem Unternehmensadministrator nicht durchgeführt werden kann. Es ist eine Supportanfrage erforderlich, um eine Unternehmensregistrierung in eine neue Registrierung zu übertragen.

Wenn Sie die Übertragung einer gesamten Unternehmensregistrierung in eine Registrierung anfordern, werden die folgenden Aktionen ausgeführt:

- Alle Azure-Dienste, Abonnements, Konten, Abteilungen sowie die gesamte Registrierungsstruktur einschließlich aller EA-Abteilungsadministratoren werden in die neue Zielregistrierung übertragen.
- Der Registrierungsstatus wird auf _Übertragen_ festgelegt. Die übertragene Registrierung ist nur für Berichte zum Nutzungsverlauf verfügbar.
- Einer übertragenen Registrierung können Sie keine Rollen oder Abonnements hinzufügen. Der Status „Übertragen“ verhindert eine zusätzliche Nutzung in Bezug auf die Registrierung.
- Verbleibendes Guthaben in Bezug auf die Azure-Vorauszahlung in der Vereinbarung geht dabei verloren. Dies umfasst auch zukünftige Bestimmungen.
-    Wenn die Registrierung, von der aus Sie die Übertragung durchführen, über RI-Käufe verfügt, verbleibt die Gebühr für RI-Käufe in der Quellregistrierung. Sämtliche RI-Vorteile werden aber in die neue Registrierung übertragen und stehen dort zur Verfügung.
-    Die einmalige Gebühr für Marketplace-Käufe sowie feste Monatsgebühren, die ggf. bereits für die alte Registrierung angefallen sind, werden nicht in die neue Registrierung übertragen. Verbrauchsbasierte Marketplace-Gebühren werden übertragen.

### <a name="effective-transfer-date"></a>Effektives Übertragungsdatum

Das effektive Datum der Übertragung kann ein Datum ab dem Startdatum der Zielregistrierung sein.

Die Nutzung der Quellregistrierung wird gegen eine Azure-Vorauszahlung oder als Überschreitung abgerechnet. Die Nutzung, die nach dem effektiven Übertragungsdatum erfolgt, wird in die neue Registrierung übertragen und entsprechend abgerechnet.

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie eine Registrierungsübertragung anfordern, geben Sie die folgenden Informationen an:

- Für die Quellregistrierung: die Registrierungsnummer.
- Für die Zielregistrierung: die Registrierungsnummer, an die die Übertragung erfolgen soll.
- Das effektive Datum der Registrierungsübertragung kann ein Datum ab dem Startdatum der Zielregistrierung sein. Das ausgewählte Datum wirkt sich nicht auf die Nutzung aus, für die bereits eine Überschreitungsrechnung ausgestellt wurde.

Andere Punkte, die vor einer Registrierungsübertragung zu beachten sind:

- Sowohl die Genehmigung des EA-Administrators der Ziel- als auch des EA-Administrators der Quellregistrierung ist erforderlich.
- Wenn eine Registrierungsübertragung Ihre Anforderungen nicht erfüllt, ziehen Sie eine Kontoübertragung in Betracht.
- Der Quellregistrierungsstatus wird auf „übertragen“ aktualisiert und steht nur für die Nutzungsberichterstellung zur Verfügung.

### <a name="azure-prepayment"></a>Azure-Vorauszahlung

Die Azure-Vorauszahlung ist zwischen Registrierungen übertragbar. Salden in Bezug auf Azure-Vorauszahlungen sind vertraglich an die Registrierung gebunden, für die sie bestellt wurden. Die Azure-Vorauszahlung wird nicht als Teil des Konto- oder Registrierungstransferprozesses übertragen.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Für Konto- und Registrierungsübertragungen sind keine Dienste betroffen.

Während der Konto- oder Registrierungsübertragung entstehen keine Ausfallzeiten. Sie kann am Tag Ihrer Anforderung abgeschlossen werden, wenn alle erforderlichen Informationen bereitgestellt werden.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Übertragen eines Enterprise-Abonnements auf ein Abonnement mit nutzungsbasierter Bezahlung

Wenn Sie ein Enterprise-Abonnement auf ein einzelnes Abonnement mit nutzungsbasierter Bezahlung übertragen möchten, müssen Sie im Azure Enterprise Portal eine neue Supportanfrage erstellen. Klicken Sie zum Erstellen einer Supportanfrage im Bereich **Hilfe und Support** auf **+ Neue Supportanfrage**.

## <a name="change-azure-subscription-or-account-ownership"></a>Ändern des Besitzes von Azure-Abonnements oder -Konten

Das Azure EA-Portal kann Abonnements von einem Kontobesitzer auf einen anderen übertragen. Weitere Informationen finden Sie unter [Ändern des Besitzes von Azure-Abonnements oder -Konten](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Auswirkungen der Abonnementübertragung

Wenn ein Azure-Abonnement an ein Konto desselben Azure Active Directory-Mandanten übertragen wird, behalten alle Benutzer, Gruppen und Dienstprinzipale, die über die [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) Zugriff zum Verwalten von Ressourcen hatten, ihre Zugriffsberechtigung.

So zeigen Sie Benutzer mit RBAC-Zugriff auf das Abonnement an:

1. Öffnen Sie im Azure-Portal **Abonnements**.
2. Wählen Sie das anzuzeigende Abonnement und dann **Zugriffssteuerung (IAM)** aus.
3. Wählen Sie **Rollenzuweisungen** aus. Auf der Seite „Rollenzuweisungen“ werden alle Benutzer mit RBAC-Zugriff auf das Abonnement aufgelistet.

Wenn das Abonnement an ein Konto in einem anderen Azure AD-Mandanten übertragen wird, _verlieren_ alle Benutzer, Gruppen und Dienstprinzipale ihren Zugriff, die über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md) Zugriff zum Verwalten von Ressourcen hatten. Auch wenn kein RBAC-Zugriff vorhanden ist, ist der Zugriff auf das Abonnement unter Umständen über Sicherheitsmechanismen verfügbar, z. B.:

- Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](../../cloud-services/cloud-services-certs-create.md).
- Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Azure-Speicherkonto – Übersicht](../../storage/common/storage-account-overview.md).
- Anmeldeinformationen für den Remotezugriff für Dienste wie Azure Virtual Machines.

Der neue Besitzer sollte sich überlegen, ob er die dem Dienst zugeordneten Geheimnisse aktualisiert, wenn er den Zugriff auf die Azure-Ressourcen einschränken muss. Die meisten Ressourcen können durch Ausführen der folgenden Schritte aktualisiert werden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Menü „Hub“ die Option **Alle Ressourcen** aus.
3. Wählen Sie die Ressource.
4. Wählen Sie auf der Ressourcenseite die Option **Einstellungen** aus, um vorhandene Geheimnisse anzuzeigen und zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- Hilfe zu Problemen im Azure EA-Portal finden Sie unter [Beheben von Zugriffsproblemen beim Azure EA-Portal](ea-portal-troubleshoot.md).