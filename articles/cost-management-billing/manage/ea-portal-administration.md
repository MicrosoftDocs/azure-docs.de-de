---
title: Azure EA-Portalverwaltung
description: In diesem Artikel werden die häufigsten Aufgaben erläutert, die Administratoren im Azure EA-Portal durchführen.
author: bandersmsft
ms.author: banders
ms.date: 11/13/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 3ce13a103b605ea7b6066e8b2868fa974f4e64e5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050565"
---
# <a name="azure-ea-portal-administration"></a>Azure EA-Portalverwaltung

In diesem Artikel werden die häufigsten Aufgaben erläutert, die Administratoren im Azure EA-Portal durchführen (https://ea.azure.com). Das Azure EA-Portal ist ein Onlineverwaltungsportal, das Kunden beim Verwalten der Kosten ihrer Azure EA-Dienste unterstützt. Einführende Informationen zum Azure EA-Portal finden Sie im Artikel [Erste Schritte mit dem Azure EA-Portal](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Aktivieren Ihrer Registrierung

Um den Dienst zu aktivieren, öffnet der erste Unternehmensadministrator das [Azure Enterprise Portal](https://ea.azure.com) und meldet sich mit der E-Mail-Adresse aus der Einladungs-E-Mail an.

Wenn Sie als Unternehmensadministrator eingerichtet sind, benötigen Sie keine Aktivierungs-E-Mail. Stattdessen können Sie zum [Azure Enterprise Portal](https://ea.azure.com) wechseln und sich mit der E-Mail-Adresse Ihres Geschäfts-, Schul-, Uni- oder Microsoft-Kontos und Ihrem Kennwort anmelden.

Wenn Sie über mehrere Registrierungen verfügen, wählen Sie eine aus, die Sie aktivieren möchten. Standardmäßig werden nur aktive Registrierungen angezeigt. Um den Registrierungsverlauf anzuzeigen, deaktivieren Sie die Option **Aktiv** in der oberen rechten Ecke des Azure Enterprise Portals.

Unter **Registrierung** wird der Status **Aktiv** angezeigt.

![Beispiel für eine aktive Registrierung](./media/ea-portal-administration/ea-enrollment-status.png)

Nur vorhandene Azure-Unternehmensadministratoren können andere Unternehmensadministratoren erstellen.

### <a name="create-another-enterprise-administrator"></a>Erstellen eines weiteren Unternehmensadministrators

So fügen Sie einen weiteren Unternehmensadministrator hinzu

1. Melden Sie sich beim [Azure Enterprise Portal](https://ea.azure.com) an.
1. Wechseln Sie zu **Verwalten** > **Registrierungsdetail**.
1. Wählen Sie rechts oben **+ Administrator hinzufügen** aus.

Stellen Sie sicher, dass Sie über die E-Mail-Adresse und die bevorzugte Authentifizierungsmethode des Benutzers verfügen, wie etwa ein Geschäfts-, Schul-, Uni- oder Microsoft-Konto.

Wenn Sie kein Unternehmensadministrator sind, bitten Sie einen Unternehmensadministrator, Sie einer Registrierung hinzuzufügen. Nachdem Sie einer Registrierung hinzugefügt wurden, erhalten Sie eine Aktivierungs-E-Mail.

Wenn Ihr Unternehmensadministrator Ihnen nicht helfen kann, erstellen Sie eine [Supportanfrage für das Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Geben Sie die folgenden Informationen ein:

- Registrierungsnummer
- Hinzuzufügende E-Mail-Adresse und Authentifizierungstyp (Geschäfts-, Schul-, Uni- oder Microsoft-Konto)
- E-Mail-Genehmigung von einem vorhandenen Unternehmensadministrator
  - Wenn der vorhandene Unternehmensadministrator nicht verfügbar ist, bitten Sie Ihren Partner oder Softwareberater, die Kontaktinformationen über das VLSC-Tool (Volume Licensing Service Center) zu ändern.

## <a name="create-an-azure-enterprise-department"></a>Erstellen einer Azure Enterprise-Abteilung

Unternehmensadministratoren und Abteilungsadministratoren verwenden Abteilungen zum Organisieren und Erstellen von Berichten zu Azure-Diensten für Unternehmen und zur Nutzung nach Abteilung und Kostenstelle. Der Unternehmensadministrator kann folgende Aktionen ausführen:

- Hinzufügen oder Entfernen von Abteilungen
- Zuordnen eines Kontos zu einer Abteilung
- Erstellen von Abteilungsadministratoren
- Zulassen, dass Abteilungsadministratoren Preise und Kosten anzeigen

Ein Abteilungsadministrator kann seinen Abteilungen neue Konten hinzufügen. Er kann auch Konten aus den Abteilungen entfernen, nicht jedoch aus der Registrierung.

So fügen Sie eine Abteilung hinzu:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Wählen Sie im linken Bereich **Verwalten** aus.
1. Wählen Sie die Registerkarte **Abteilung** aus, und klicken Sie dann auf **+ Abteilung hinzufügen**.
1. Geben Sie die Informationen ein.
   Der Abteilungsname ist das einzige Pflichtfeld. Er muss mindestens drei Zeichen lang sein.
1. Wählen Sie zum Abschluss **Hinzufügen** aus.

## <a name="add-a-department-administrator"></a>Hinzufügen eines Abteilungsadministrators

Nachdem eine Abteilung erstellt wurde, kann der Unternehmensadministrator Abteilungsadministratoren hinzufügen und diese jeweils einer Abteilung zuordnen. Abteilungsadministratoren können für ihre Abteilungen die folgenden Aktionen ausführen:

- Erstellen anderer Abteilungsadministratoren
- Anzeigen und Bearbeiten von Abteilungseigenschaften wie dem Namen oder der Kostenstelle
- Hinzufügen von Konten
- Entfernen von Konten
- Herunterladen von Nutzungsdetails
- Anzeigen der monatlichen Nutzung und Gebühren <sup>1</sup>

> <sup>1</sup> Diese Berechtigungen müssen von einem Unternehmensadministrator erteilt werden. Wenn Sie die Berechtigung zum Anzeigen der monatlichen Nutzung und Gebühren für die Abteilung erhalten haben, diese Informationen aber nicht angezeigt werden, wenden Sie sich an Ihren Partner.

### <a name="to-add-a-department-administrator"></a>So fügen Sie einen Abteilungsadministrator hinzu

Als Unternehmensadministrator:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Wählen Sie im linken Bereich **Verwalten** aus.
1. Wählen Sie die Registerkarte **Abteilung** und anschließend die Abteilung aus.
1. Klicken Sie auf **+ Administrator hinzufügen**, und fügen Sie die erforderlichen Informationen hinzu.
1. Legen Sie für Lesezugriff die Option **Schreibgeschützt** auf **Ja** fest, und klicken Sie anschließend auf **Hinzufügen**.

![Beispiel für das Dialogfeld „Abteilungsadministrator hinzufügen“](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>So legen Sie Lesezugriff fest

Sie können Abteilungsadministratoren Lesezugriff gewähren.

- Wenn Sie einen neuen Abteilungsadministrator erstellen, legen Sie die Option „Schreibgeschützt“ auf **Ja** fest.

- So bearbeiten Sie einen vorhandenen Abteilungsadministrator:
   1. Wählen Sie eine Abteilung aus, und klicken Sie dann auf das Stiftsymbol neben dem **Abteilungsadministrator**, den Sie bearbeiten möchten.
   1. Legen Sie die Option „Schreibgeschützt“ auf **Ja** fest, und klicken Sie dann auf **Speichern**.

Unternehmensadministratoren erhalten automatisch Abteilungsadministratorberechtigungen.

## <a name="add-an-account"></a>Konto hinzufügen

Die Struktur von Konten und Abonnements wirkt sich darauf aus, wie diese verwaltet und auf Rechnungen und Berichten angezeigt werden. Zu den Beispielen für typische Organisationsstrukturen zählt die Strukturierung nach Geschäftsbereichen, funktionsbezogenen Teams und geografischen Standorten.

So fügen Sie ein Konto hinzu:

1. Wählen Sie im Azure Enterprise Portal im Navigationsbereich auf der linken Seite die Option **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**. Klicken Sie auf der Seite **Konto** auf **+ Konto hinzufügen**.
1. Wählen Sie eine Abteilung aus, oder lassen Sie sie nicht zugewiesen, und wählen Sie dann den gewünschten Authentifizierungstyp aus.
1. Geben Sie einen Anzeigenamen ein, damit Sie das Konto in Berichten identifizieren können.
1. Geben Sie die **E-Mail-Adresse des Kontobesitzers** ein, die dem neuen Konto zugeordnet werden soll.
1. Bestätigen Sie die E-Mail-Adresse, und klicken Sie auf **Hinzufügen**.

![Beispiel für die Liste der Konten und die Option „Konto hinzufügen“](./media/ea-portal-administration/create-ea-add-an-account.png)

Um ein weiteres Konto hinzuzufügen, klicken Sie auf **Weiteres Konto hinzufügen**, oder klicken Sie in der rechten unteren Ecke der linken Symbolleiste auf **Hinzufügen**.

So bestätigen Sie den Kontobesitz:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Sehen Sie sich den Status an.

   Der Status sollte sich von **Ausstehend** in **Start-/Enddatum** ändern. Das Start-/Enddatum ist das Datum, an dem der Benutzer sich zuerst angemeldet hat, und das Enddatum der Vereinbarung.
1. Wenn die **Warnmeldung** angezeigt wird, muss der Kontobesitzer auf **Weiter** klicken, um das Konto bei der erstmaligen Anmeldung beim Azure Enterprise Portal zu aktivieren.

## <a name="change-azure-subscription-or-account-ownership"></a>Ändern des Besitzes von Azure-Abonnements oder -Konten

Unternehmensadministratoren können das Azure Enterprise Portal verwenden, um den Kontobesitz bestimmter oder aller Abonnements in einer Registrierung zu übertragen.

Wenn Sie eine Besitzübertragung für ein Abonnement oder Konto durchführen, aktualisiert Microsoft den Kontobesitzer.

Machen Sie sich vor der Besitzübertragung mit den folgenden Azure RBAC-Richtlinien (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung in Azure) vertraut:

- Wenn Sie Besitzübertragungen für Abonnements oder Konten zwischen zwei Organisations-IDs im gleichen Mandanten durchführen, werden Azure RBAC-Richtlinien und vorhandene Dienstadministrator- und Co-Admin-Rollen beibehalten.
- Bei mandantenübergreifenden Besitzübertragungen für Abonnements oder Konten gehen Ihre Azure RBAC-Richtlinien und Rollenzuweisungen verloren.
- Richtlinien und Administratorrollen werden nicht über verschiedene Verzeichnisse hinweg übertragen. Dienstadministratoren werden auf den Besitzer des Zielkontos aktualisiert.
- Wenn Sie den Verlust der Azure RBAC-Richtlinien und Rollenzuweisungen bei der Abonnementübertragung zwischen Mandanten vermeiden möchten, achten Sie darauf, dass das Kontrollkästchen **Move the subscriptions to the recipient’s Azure AD tenant** (Abonnements in den Azure AD-Mandanten des Empfängers verschieben) **deaktiviert** ist. Dadurch bleiben die Dienste, Azure-Rollen und Richtlinien im aktuellen Azure AD-Mandanten erhalten, und es wird lediglich der Abrechnungsbesitz für das Konto übertragen.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abbildung: Deaktiviertes Kontrollkästchen für die Verschiebung von Abonnements an einen Azure AD-Mandanten" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Führen Sie vor dem Ändern des Kontobesitzers die folgenden Aktionen durch:

1. Zeigen Sie im Azure Enterprise Portal die Registerkarte **Konto** an, und identifizieren Sie das Quellkonto. Das Quellkonto muss aktiv sein.
1. Identifizieren Sie das Zielkonto, und stellen Sie sicher, dass es aktiv ist.

So übertragen Sie den Kontobesitz für alle Abonnements:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Wählen Sie im Navigationsbereich links die Option **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**, und zeigen Sie auf ein Konto.
1. Klicken Sie auf der rechten Seite auf das Symbol zum Ändern des Kontobesitzers. Das Symbol stellt eine Person dar.  
    ![Bild des Symbols zum Ändern des Kontobesitzers](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Wählen Sie das Zielkonto für die Übertragung und anschließend **Weiter** aus.
1. Wenn Sie den Kontobesitz an einen anderen Azure AD-Mandanten übertragen möchten, aktivieren Sie das Kontrollkästchen **Move the subscriptions to the recipient's Azure AD tenant** (Abonnements in den Azure AD-Mandanten des Empfängers verschieben).  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abbildung: Aktiviertes Kontrollkästchen für die Verschiebung von Abonnements an einen Azure AD-Mandanten" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bestätigen Sie die Übertragung, und klicken Sie auf **Senden**.

So übertragen Sie den Kontobesitz für ein einzelnes Abonnement:

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Wählen Sie im Navigationsbereich links die Option **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**, und zeigen Sie auf ein Konto.
1. Klicken Sie rechts auf das Symbol zum Übertragen von Abonnements. Das Symbol stellt eine Seite dar.  
    ![Bild des Symbols zum Übertragen von Abonnements](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Wählen Sie das Zielkonto für die Abonnementübertragung und anschließend **Weiter** aus.
1. Wenn Sie den Abonnementbesitz an einen anderen Azure AD-Mandanten übertragen möchten, aktivieren Sie das Kontrollkästchen **Move the subscriptions to the recipient's Azure AD tenant** (Abonnements in den Azure AD-Mandanten des Empfängers verschieben).  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Abbildung: Aktiviertes Kontrollkästchen für die Verschiebung von Abonnements an einen Azure AD-Mandanten" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bestätigen Sie die Übertragung, und klicken Sie auf **Senden**.


## <a name="associate-an-account-to-a-department"></a>Zuordnen eines Kontos zu einer Abteilung

Unternehmensadministratoren können Abteilungen unter der Registrierung vorhandene Konten zuordnen.

### <a name="to-associate-an-account-to-a-department"></a>So ordnen Sie ein Konto einer Abteilung zu

1. Melden Sie sich beim Azure EA-Portal als Unternehmensadministrator an.
1. Wählen Sie im linken Navigationsbereich **Verwalten** aus.
1. Wählen Sie **Abteilung** aus.
1. Zeigen Sie auf die Zeile mit dem Konto, und wählen Sie rechts das Stiftsymbol aus.
1. Wählen Sie im Dropdownmenü die Abteilung aus.
1. Wählen Sie **Speichern** aus.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Zuordnen eines vorhandenen Kontos zu Ihrem Abonnement mit nutzungsbasierter Bezahlung

Falls Sie im Azure-Portal bereits über ein Microsoft Azure-Konto verfügen, geben Sie das zugeordnete Geschäfts-, Schul-, Uni- oder Microsoft-Konto ein, um es Ihrer Enterprise Agreement-Registrierung zuzuordnen.

### <a name="associate-an-existing-account"></a>Zuordnen eines vorhandenen Kontos

1. Wählen Sie im Azure Enterprise Portal **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**.
1. Klicken Sie auf **+ Konto hinzufügen**.
1. Geben Sie das Geschäfts-, Schul-, Uni- oder Microsoft-Konto ein, das dem vorhandenen Azure-Konto zugeordnet ist.
1. Bestätigen Sie das dem vorhandenen Azure-Konto zugeordnete Konto.
1. Geben Sie einen Namen ein, den Sie zum Identifizieren dieses Kontos in Berichten verwenden möchten.
1. Wählen Sie **Hinzufügen**.
1. Um ein weiteres Konto hinzuzufügen, können Sie erneut die Option **+ Konto hinzufügen** auswählen oder zur Homepage zurückkehren, indem Sie die Schaltfläche **Verwaltung** auswählen.
1. Wenn Sie die Seite **Konto** öffnen, wird das neu hinzugefügte Konto mit dem Status **Ausstehend** angezeigt.

### <a name="confirm-account-ownership"></a>Bestätigen des Kontobesitzes

1. Melden Sie sich bei dem E-Mail-Konto an, das dem angegebenen Geschäfts-, Schul-, Uni- oder Microsoft-Konto zugeordnet ist.
1. Öffnen Sie die E-Mail-Benachrichtigung mit dem Titel _Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing_ (Einladung zur Aktivierung Ihres Kontos im Microsoft Azure-Dienst über die Microsoft-Volumenlizenzierung).
1. Klicken Sie in der Einladung auf den Link **Beim Microsoft Azure Enterprise Portal anmelden**.
1. Wählen Sie **Anmelden**.
1. Geben Sie Ihr Geschäfts-, Schul-, Uni- oder Microsoft-Konto sowie das zugehörige Kennwort ein, um sich anzumelden und den Kontobesitz zu bestätigen.

### <a name="azure-marketplace"></a>Azure Marketplace

Die meisten Abonnements können von einer Umgebung mit nutzungsbasierter Bezahlung in Azure Enterprise Agreement konvertiert werden. Bei Azure Marketplace-Diensten ist dies jedoch nicht der Fall. Um eine einzige Ansicht aller Abonnements und Gebühren zu erhalten, empfiehlt es sich, die Azure Marketplace-Dienste dem Azure Enterprise Portal hinzuzufügen.

1. Melden Sie sich beim Azure Enterprise Portal an.
1. Klicken Sie im linken Navigationsbereich auf **Verwalten**.
1. Klicken Sie auf die Registerkarte **Registrierung**.
1. Sehen Sie sich den Abschnitt **Registrierungsdetail** an.
1. Klicken Sie zur Aktivierung rechts neben dem Feld „Azure Marketplace“ auf das Stiftsymbol. Wählen Sie **Speichern** aus.

Der Kontobesitzer kann nun Azure Marketplace-Dienste erwerben, die zuvor vom Abonnement mit nutzungsbasierter Bezahlung verwaltet wurden.

Nachdem die neuen Azure Marketplace-Abonnements unter Ihrer Azure EA-Registrierung aktiviert wurden, kündigen Sie die Azure Marketplace-Abonnements, die in der Umgebung mit nutzungsbasierter Bezahlung erstellt wurden. Dieser Schritt ist wichtig, um einen Fehlerzustand Ihrer Azure Marketplace-Abonnements zu vermeiden, wenn Ihr Zahlungsmittel für die nutzungsbasierte Bezahlung abläuft.

### <a name="msdn"></a>MSDN

MSDN-Abonnements werden automatisch in MSDN Dev/Test konvertiert, und das Azure EA-Angebot verliert sämtliches ggf. vorhandenes Guthaben.

### <a name="azure-in-open"></a>Azure in Open

Wenn Sie ein Azure in Open-Abonnement einem Enterprise Agreement zuordnen, verfällt das nicht in Anspruch genommene Azure in Open-Guthaben. Daher wird empfohlen, das gesamte Guthaben für ein Azure in Open-Abonnement aufzubrauchen, bevor Sie das Konto Ihrem Enterprise Agreement hinzufügen.  

### <a name="accounts-with-support-subscriptions"></a>Konten mit Supportabonnements

Wenn Ihr Enterprise Agreement kein Supportabonnement umfasst und Sie dem Azure Enterprise Portal ein vorhandenes Konto mit einem Supportabonnement hinzufügen, wird Ihr MOSA-Supportabonnement nicht automatisch übertragen. Sie müssen während der Toleranzperiode (bis zum Ende des Folgemonats) ein Supportabonnement in Azure EA neu erwerben.

## <a name="department-spending-quotas"></a>Ausgabenkontingente für Abteilungen

EA-Kunden können für jede Abteilung unter einer Registrierung Ausgabenkontingente festlegen oder ändern. Die Höhe des Ausgabenkontingents wird für die aktuelle Laufzeit der Vorauszahlung festgelegt. Am Ende der aktuellen Laufzeit der Vorauszahlung wird das vorhandene Ausgabenkontingent automatisch auf die nächste Laufzeit der Vorauszahlung verlängert, sofern die Werte nicht aktualisiert werden.

Der Abteilungsadministrator kann das Ausgabenkontingent zwar anzeigen, aber nur der Unternehmensadministrator kann den Kontingentbetrag aktualisieren. Unternehmensadministrator und Abteilungsadministrator erhalten Benachrichtigungen, sobald 50 Prozent, 75 Prozent, 90 Prozent und 100 Prozent des Kontingents erreicht werden.

### <a name="enterprise-administrator-to-set-the-quota"></a>Als Unternehmensadministrator können Sie das Kontingent wie folgt festlegen:

 1. Öffnen Sie das Azure EA-Portal.
 1. Wählen Sie im linken Navigationsbereich **Verwalten** aus.
 1. Wählen Sie die Registerkarte **Abteilung** aus.
 1. Wählen Sie die Abteilung aus.
 1. Wählen Sie im Abschnitt mit den Abteilungsdetails das Stiftsymbol oder das Symbol **+ Abteilung hinzufügen** aus, um zusammen mit einer neuen Abteilung ein Ausgabenkontingent hinzuzufügen.
 1. Geben Sie in den Abteilungsdetails ein Ausgabenkontingent in der Währung der Registrierung in das entsprechende Feld ein. (Der angegebene Betrag muss größer Null sein.)
    - Bei dieser Gelegenheit können Sie auch den Abteilungsnamen und die Kostenstelle bearbeiten.
 1. Wählen Sie **Speichern** aus.

Das Ausgabenkontingent für die Abteilung wird nun auf der Registerkarte „Abteilung“ in der Abteilungsliste angezeigt. Am Ende der aktuellen Vorauszahlung behält das Azure EA-Portal die Ausgabenkontingente für die nächste Laufzeit der Vorauszahlung bei.

Die Höhe des Abteilungskontingents ist unabhängig von der aktuellen Azure-Vorauszahlung, und die Höhe des Kontingents sowie die Warnungen gelten nur für die Erstanbieternutzung. Das Ausgabenkontingent für die Abteilung dient nur zur Information und erzwingt keine Ausgabenlimits.

### <a name="department-administrator-to-view-the-quota"></a>Als Abteilungsadministrator können Sie das Kontingent wie folgt anzeigen:

1. Öffnen Sie das Azure EA-Portal.
1. Wählen Sie im linken Navigationsbereich **Verwalten** aus.
1. Wählen Sie die Registerkarte **Abteilung** aus, und sehen Sie sich die Abteilungsliste mit den Ausgabenkontingenten an.

Wenn Sie ein indirekter Kunde sind, müssen die Kostenfeatures von Ihrem Channelpartner aktiviert werden.

## <a name="enterprise-user-roles"></a>Unternehmensbenutzerrollen

Das Azure EA-Portal unterstützt Sie bei der Verwaltung Ihrer Azure EA-Kosten und der Nutzung. Im Azure EA-Portal gibt es drei zentrale Rollen:

- EA-Administrator
- Abteilungsadministrator
- Kontobesitzer

Jede Rolle verfügt über eine andere Zugriffs- und Autorisierungsebene.

Weitere Informationen zu Benutzerrollen finden Sie unter [Unternehmensbenutzerrollen](./understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Hinzufügen eines Azure EA-Kontos

Das Azure EA-Konto ist eine Organisationseinheit im Azure EA Portal. Sie wird zur Abonnementverwaltung und auch für die Berichterstellung verwendet. Für den Zugriff auf Azure-Dienste und ihre Verwendung müssen Sie ein Konto erstellen oder erstellen lassen.

Weitere Informationen zu Azure-Konten finden Sie unter [Hinzufügen eines Kontos](#add-an-account).

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test-Angebot

Als Azure-Unternehmensadministrator können Sie Kontobesitzern in Ihrer Organisation die Erstellung von Abonnements auf Grundlage des EA Dev/Test-Angebots ermöglichen. Wählen Sie hierfür im Azure EA Portal das Feld „Dev/Test“ für den Kontobesitzer aus.

Informieren Sie anschließend den Kontobesitzer, dass er nun die EA Dev/Test-Abonnements einrichten kann, die er für seine Teams mit den Dev/Test-Abonnenten benötigt.

Dank dieses Angebots können aktive Visual Studio-Abonnenten Entwicklungs- und Testworkloads in Azure zu speziellen Dev/Test-Tarifen ausführen. Es besteht Zugriff auf den vollständigen Katalog mit Dev/Test-Images, einschließlich Windows 8.1 und Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>So richten Sie das Enterprise Dev/Test-Angebot ein:

1. Melden Sie sich als Unternehmensadministrator an.
1. Wählen Sie im linken Navigationsbereich **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**.
1. Wählen Sie die Zeile für das Konto aus, unter dem Sie den Dev/Test-Zugriff aktivieren möchten.
1. Wählen Sie das Stiftsymbol rechts neben der Zeile aus.
1. Aktivieren Sie das Kontrollkästchen „Dev/Test“.
1. Wählen Sie **Speichern** aus.

Wenn ein Benutzer über das Azure EA-Portal als Kontobesitzer hinzugefügt wird, werden alle dem Kontobesitzer zugeordneten Azure-Abonnements, die entweder auf dem Dev/Test-Angebot mit nutzungsbasierter Zahlung oder auf den Angeboten mit monatlichem Guthaben für Visual Studio-Abonnenten basieren, in das EA Dev/Test-Angebot konvertiert. Abonnements, die auf anderen Angebotstypen (beispielsweise nutzungsbasierte Zahlung) basieren und dem Kontobesitzer zugeordnet sind, werden in Microsoft Azure Enterprise-Angebote konvertiert.

Das Dev/Test-Angebot gilt derzeit nicht für Azure Government-Kunden.

## <a name="create-a-subscription"></a>Erstellen eines Abonnements

Kontobesitzer können Abonnements anzeigen und verwalten. Sie können Abonnements verwenden, um Teams in Ihrer Organisation Zugriff auf Entwicklungsumgebungen und Projekte zu ermöglichen. Beispiele dafür sind Test, Produktion, Entwicklung und Staging.

Wenn Sie verschiedene Abonnements für jede Anwendungsumgebung erstellen, trägt dies zur Sicherheit der Umgebungen bei.

- Es ist auch möglich, dass Sie jedem Abonnement ein anderes Dienstadministratorkonto zuweisen.
- Sie können Abonnements beliebig viele Dienste zuordnen.
- Der Kontobesitzer erstellt Abonnements und weist jedem Abonnement in seinem Konto ein Dienstadministratorkonto zu.

### <a name="add-a-subscription"></a>Hinzufügen eines Abonnements

Verwenden Sie die folgenden Informationen, um ein Abonnement hinzuzufügen.

Wenn Sie Ihrem Konto zum ersten Mal ein Abonnement hinzufügen, werden Sie aufgefordert, den Microsoft Online-Abonnementvertrag (Microsoft Online Subscription Agreement, MOSA) und eine Gebührenübersicht zu akzeptieren. Auch wenn sie bei Enterprise Agreement-Kunden keine Anwendung finden, sind MOSA und Gebührenübersicht zum Erstellen des Abonnements erforderlich. Ihre Microsoft Azure Enterprise Agreement-Registrierungszusatzvereinbarung hat Vorrang vor diesen Punkten, und Ihre Vertragsbeziehung ändert sich dadurch nicht. Wenn Sie dazu aufgefordert werden, aktivieren Sie das Kontrollkästchen und bestätigen damit, dass Sie die Bedingungen akzeptieren.

_Microsoft Azure Enterprise_ ist der Standardname, der beim Erstellen eines Abonnements verwendet wird. Sie können den Namen ändern, um dieses Abonnement von den anderen Abonnements in Ihrer Registrierung zu unterscheiden und sicherzustellen, dass es in Berichten auf Unternehmensebene identifizierbar ist.

So fügen Sie ein Abonnement hinzu:

1. Melden Sie sich im Azure Enterprise Portal beim Konto an.
1. Klicken Sie auf die Registerkarte **Administrator** und anschließend oben auf der Seite auf **Abonnement**.
1. Vergewissern Sie sich, dass Sie als Kontobesitzer des Kontos angemeldet sind.
1. Wählen Sie **+ Abonnement hinzufügen** und dann **Kaufen** aus.

   Wenn Sie ein Abonnement zum ersten Mal zu einem Konto hinzufügen, müssen Sie Ihre Kontaktinformationen angeben. Wenn Sie weitere Abonnements hinzufügen, werden Ihre Kontaktinformationen für Sie hinzugefügt.

1. Wählen Sie **Abonnements** und dann das von Ihnen erstellte Abonnement aus.
1. Wählen Sie **Abonnementdetails bearbeiten** aus.
1. Bearbeiten Sie den **Abonnementnamen** und den **Dienstadministrator**, und aktivieren Sie das Kontrollkästchen.

   Der Abonnementname wird in Berichten angezeigt. Dies ist der Name des Projekts, das dem Abonnement im Entwicklungsportal zugeordnet ist.

Es kann bis zu 24 Stunden dauern, bis neue Abonnements in der Liste der Abonnements angezeigt werden. Nachdem Sie ein Abonnement erstellt haben, können Sie folgende Aktionen ausführen:

- [Bearbeiten von Abonnementdetails](https://portal.azure.com)
- [Verwalten von Abonnementdiensten](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Löschen eines Abonnements

Löschen Sie ein Abonnement, für das Sie als Kontobesitzer fungieren, wie folgt:

1. Melden Sie sich mit den Anmeldeinformationen Ihres Kontos beim Azure-Portal an.
1. Wählen Sie im Menü „Hub“ die Option **Abonnements** aus.
1. Wählen Sie links oben auf der Seite auf der Registerkarte „Abonnements“ das Abonnement aus, das Sie kündigen möchten, und wählen Sie **Cancel Sub** (Abo kündigen) aus, um die Registerkarte für die Kündigung zu öffnen.
1. Geben Sie den Abonnementnamen ein, wählen Sie einen Kündigungsgrund aus, und wählen Sie die Schaltfläche **Cancel Sub** (Abo kündigen) aus.

Abonnements können nur von Kontoadministratoren gekündigt werden.

Weitere Informationen finden Sie unter [Was geschieht, nachdem ich mein Abonnement gekündigt habe?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-account"></a>Löschen eines Kontos

Eine Kontoentfernung kann nur für aktive Konten ohne aktive Abonnements durchgeführt werden.

1. Wählen Sie im Enterprise Portal im linken Navigationsbereich die Option **Verwalten** aus.
1. Klicken Sie auf die Registerkarte **Konto**.
1. Wählen Sie in der Kontotabelle das Konto aus, das Sie löschen möchten.
1. Wählen Sie rechts von der Zeile „Konto“ das Symbol „X“ aus.
1. Wenn unter dem Konto keine aktiven Abonnements mehr vorhanden sind, können Sie unter der Kontozeile die Schaltfläche **Ja** auswählen, um die Kontoentfernung zu bestätigen.

## <a name="update-notification-settings"></a>Aktualisierung der Benachrichtigungseinstellungen

Unternehmensadministratoren werden automatisch für den Erhalt von Nutzungsbenachrichtigungen für ihre Registrierung registriert. Jeder Unternehmensadministrator kann das Intervall der einzelnen Benachrichtigungen ändern oder diese vollständig deaktivieren.

Benachrichtigungskontakte werden im Azure EA-Portal im Abschnitt **Benachrichtigungskontakt** angezeigt. Durch die Verwaltung Ihrer Benachrichtigungskontakte stellen Sie sicher, dass die richtigen Personen in Ihrer Organisation Azure EA-Benachrichtigungen erhalten.

So zeigen Sie die aktuellen Benachrichtigungseinstellungen an:

1. Navigieren Sie im Azure EA-Portal zu **Verwalten** > **Benachrichtigungskontakt**.
2. E-Mail-Adresse: Die E-Mail-Adresse, die dem Microsoft-Konto des Unternehmensadministrators oder dem Geschäfts-, Schul- oder Unikonto zugeordnet ist, das die Benachrichtigungen erhält
3. Benachrichtigungshäufigkeit zu nicht in Rechnung gestellten Salden: Die Häufigkeit, mit der Benachrichtigungen an die einzelnen Unternehmensadministratoren gesendet werden

So fügen Sie einen Kontakt hinzu:

1. Wählen Sie **+ Kontakt hinzufügen** aus.
2. Geben Sie die E-Mail-Adresse ein, und bestätigen Sie sie.
3. Wählen Sie **Speichern** aus.

Der neue Benachrichtigungskontakt wird im Abschnitt **Benachrichtigungskontakt** angezeigt. Wählen Sie zum Ändern der Benachrichtigungshäufigkeit den Benachrichtigungskontakt und das Stiftsymbol rechts neben der ausgewählten Zeile aus. Legen Sie die Häufigkeit auf **Täglich**, **Wöchentlich**, **Monatlich** oder **Keine** fest.

Sie können das _bevorstehende Enddatum des Abdeckungszeitraums_ unterdrücken und deaktivieren und die _Bereitstellung von Lebenszyklusbenachrichtigungen zu bevorstehenden Daten aufheben_. Durch das Deaktivieren von Lebenszyklusbenachrichtigungen werden Benachrichtigungen über den Abdeckungszeitraum und das Enddatum der Vereinbarung unterdrückt.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-Angebot

Das Azure Sponsorship-Angebot ist ein eingeschränktes gesponsertes Microsoft Azure-Konto. Es ist nur per E-Mail-Einladung für bestimmte Kunden verfügbar, die von Microsoft ausgewählt wurden. Wenn Sie zur Nutzung des Microsoft Azure Sponsorship-Angebots berechtigt sind, erhalten Sie eine E-Mail-Einladung für Ihre Konto-ID.

Sollten Sie weitere Informationen benötigen, erstellen Sie eine [Supportanfrage für die Sponsorship-Aktivierung](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Umstellung auf die Authentifizierung per Geschäfts-, Schul- oder Unikonto

Azure-Unternehmensbenutzer können von der Authentifizierung per Microsoft-Konto (MSA oder Live-ID) zur Authentifizierung per Geschäfts-, Schul- oder Unikonto (unter Verwendung von Azure Active Directory) wechseln.

Vorbereitung:

1. Fügen Sie dem Azure EA-Portal das Geschäfts-, Schul- oder Unikonto in den benötigten Rollen hinzu.
1. Sollten Fehler auftreten, ist das Konto in Active Directory möglicherweise nicht gültig.  Azure verwendet den Benutzerprinzipalnamen (User Principal Name, UPN), und dieser ist nicht immer mit der E-Mail-Adresse identisch.
1. Authentifizieren Sie sich beim Azure EA-Portal mit dem Geschäfts-, Schul- oder Unikonto.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>So stellen Sie Abonnements von Microsoft-Konten auf Geschäfts-, Schul- oder Unikonten um:

1. Melden Sie sich mit dem Microsoft-Konto, zu dem die Abonnements gehören, beim Verwaltungsportal an.
1. Verwenden Sie die Übertragung des Kontobesitzes, um zu dem neuen Konto zu wechseln.
1. Für das Microsoft-Konto sollten nun keine aktiven Abonnements mehr vorhanden sein, sodass es gelöscht werden kann.
1. Aus Abrechnungsgründen werden gelöschte Konten im Portal weiterhin angezeigt, sie haben allerdings den Status „Inaktiv“.  Wenn Sie sie aus der Ansicht herausfiltern möchten, können Sie durch Aktivieren eines Kontrollkästchens festlegen, dass nur aktive Konten angezeigt werden sollen.

## <a name="account-subscription-ownership-faq"></a>Häufige gestellte Fragen zum Kontoabonnementbesitz

In diesem Dokument werden häufig gestellte Fragen im Zusammenhang mit dem Kontoabonnementbesitz beantwortet.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Kann ich der Azure EA-Registrierung mein vorhandenes Azure-Konto zuordnen?

Ja. Alle Azure-Abonnements, für die Sie als Kontobesitzer fungieren, werden in Ihr Enterprise Agreement konvertiert. Dazu gehören Abonnements, die ein monatliches Guthaben verwenden, z. B. Visual Studio, AzurePass, MPN, BizSpark usw. Beim Konvertieren solcher Abonnements verlieren Sie das monatliche Guthaben.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Wie viele Azure-Kontobesitzer sind pro Abonnement zulässig?

Pro Abonnement ist immer nur ein einzelner Kontobesitzer zulässig.  Weitere Rollen können mithilfe des rollenbasierten Zugriffs oder der Zugriffssteuerung (IAM) im [Azure-Portal](https://portal.azure.com) auf der Registerkarte „Abonnement“ (links oben auf der Seite) hinzugefügt werden.

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Kann der Abonnementbesitz auf ein anderes Konto übertragen werden?

Ja, Sie können den Abonnementbesitz auf ein anderes Konto übertragen. Wenn das Konto A beispielsweise über drei Abonnements verfügt, kann der Unternehmensadministrator ein Abonnement auf das Konto B, ein Abonnement auf das Konto C und ein Abonnement auf das Konto D oder aber alle Abonnements auf das Konto E übertragen.

So übertragen Sie Abonnements

1. Wählen Sie im Azure Enterprise Portal **Verwalten** > **Konto** aus.
1. Zeigen Sie ganz rechts auf **Konto**, um die Optionen **Besitz übertragen** (Personensymbol) und **Abonnement übertragen** (Listensymbol) anzuzeigen. Diese Optionen sind nur für aktive Konten sichtbar.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kann ein Azure-Kontobesitzer unter mehreren Abteilungen aufgeführt werden?

Nein. Ein Kontobesitzer kann immer nur einer einzelnen Abteilung zugeordnet sein. Durch diese Richtlinie wird eine korrekte Überwachung und Zuordnung von Kosten und Ausgaben im Zusammenhang mit der entsprechenden Abteilung unter der EA-Registrierung im Azure EA Portal sichergestellt.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kann ein Azure-Kontobesitzer als Sicherheitsgruppe aufgeführt werden?

Nein. Bei einem Abonnementbesitzer muss es sich um eine eindeutige Authentifizierung vom Typ Microsoft-Konto (MSA) oder Azure Active Directory (Azure AD) handeln. Zur Regelung der Nachfolge in Ihrer Organisation können Sie ggf. generische Konten erstellen und den Abonnementzugriff mithilfe von Azure AD verwalten.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kann ein einzelner Benutzer mehrere Abonnements besitzen?

Ein Azure-Kontobesitzer kann eine unbegrenzte Anzahl von Abonnements erstellen und verwalten.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Wie kann ich auf alle Abonnements meiner Organisation zugreifen bzw. diese anzeigen?

Hierzu muss aktuell eine Richtlinie verwendet werden. Ihr Konto muss also für jedes erstellte Abonnement mithilfe des rollenbasierten Zugriffs einer Abonnementrolle hinzugefügt werden.

### <a name="where-do-i-go-to-create-a-subscription"></a>Wo kann ich ein Abonnement erstellen?

Wenn Sie ein Abonnement für ein EA-Angebot (Enterprise Azure) erstellen möchten, muss Ihr Konto zunächst im Azure EA Portal durch den Administrator Ihrer EA-Registrierung der Rolle „Kontobesitzer“ hinzugefügt werden. Anschließend müssen Sie sich beim Azure EA Portal anmelden, um Ihre Berechtigung zum Erstellen von Abonnements für den EA-Angebotstyp zu erhalten. Das erste EA-Abonnement sollte über den Link „+ Abonnement hinzufügen“ auf der Registerkarte „Abonnement“ des EA Portals erstellt werden.  Sobald Ihr Konto allerdings über die nötige Berechtigung verfügt, ist es unter Umständen einfacher, Abonnements unter „portal.azure.com“ auf der Registerkarte „Abonnement“ (links oben auf der Seite) zu erstellen, da Sie Ihr Abonnement dort in einem einzelnen Schritt erstellen und umbenennen können.

### <a name="who-can-create-a-subscription"></a>Wer kann ein Abonnement erstellen?

Abonnements für den Enterprise Azure-Angebotstyp können nur von Benutzern erstellt werden, die im [EA-Portal](https://ea.azure.com) über die Rolle „Kontobesitzer“ verfügen.

## <a name="azure-ea-term-glossary"></a>Glossar für Azure EA-Begriffe

- **Konto:** Eine Organisationseinheit im Azure Enterprise Portal. Sie wird zur Abonnementverwaltung und Berichterstellung verwendet.
- **Kontobesitzer:** Die Person, die Abonnements und Dienstadministratoren in Azure verwaltet. Kontobesitzer können Nutzungsdaten für dieses Konto und die zugehörigen Abonnements anzeigen.
- **Zusatzabonnement:** Ein Jahres- oder Anschlussabonnement unter der Registrierungszusatzvereinbarung.
- **Vorauszahlung:** Vorauszahlung in Form eines jährlichen Geldbetrags für Azure-Dienste zu einem rabattierten Vorauszahlungssatz für die Nutzung im Rahmen dieser Vorauszahlung.
- **Abteilungsadministrator:** Die Person, die Abteilungen verwalten, neue Konten und Kontobesitzer erstellen, Nutzungsdetails für die von ihr verwalteten Abteilungen einsehen sowie bei entsprechender Berechtigung Kosten anzeigen kann.
- **Registrierungsnummer:** Ein eindeutiger, von Microsoft bereitgestellter Bezeichner zur Identifizierung der spezifischen Registrierung, die einem Enterprise Agreement zugeordnet ist.
- **Unternehmensadministrator:** Die Person, die Abteilungen, Abteilungsbesitzer, Konten und Kontobesitzer in Azure verwaltet. Diese Personen können sowohl Unternehmensadministratoren verwalten als auch Nutzungsdaten, in Rechnung gestellte Mengen sowie nicht in Rechnung gestellte Gebühren für alle Konten und Abonnements anzeigen, die der Unternehmensregistrierung zugeordnet sind.
- **Enterprise Agreement:** Ein Microsoft-Lizenzvertrag für Kunden mit zentralisiertem Einkauf, die ihre gesamte Organisation auf der Grundlage von Microsoft-Technologie standardisieren und eine auf Microsoft-Software basierende IT-Infrastruktur nutzen möchten.
- **Enterprise Agreement-Registrierung:** Eine Registrierung im Enterprise Agreement-Programm für die Volumenbereitstellung von Microsoft-Produkten zu vergünstigten Preisen.
- **Microsoft-Konto**: Ein webbasierter Dienst, der beteiligten Websites die Authentifizierung eines Benutzers mit einem einzelnen Satz von Anmeldeinformationen ermöglicht.
- **Microsoft Azure Enterprise-Registrierungszusatzvereinbarung (Registrierungszusatzvereinbarung)** : Eine von einem Unternehmen unterzeichnete Zusatzvereinbarung, durch die das Unternehmen im Rahmen seiner Registrierung Zugang zu Azure erhält.
- **Azure Enterprise Portal**: Das Portal, das von unseren Unternehmenskunden für die Verwaltung ihrer Azure-Konten und der zugehörigen Abonnements verwendet wird.
- **Verbrauchte Ressourcenmenge:** Die Menge eines einzelnen Azure-Diensts, die innerhalb eines Monats genutzt wurde.
- **Dienstadministrator:** Die Person, die auf Abonnements und Entwicklungsprojekte im Azure Enterprise Portal zugreifen und diese verwalten kann.
- **Abonnement**: Stellt ein Azure Enterprise Portal-Abonnement dar und ist ein Container mit Azure-Diensten, die von demselben Dienstadministrator verwaltet werden.
- **Geschäfts-, Schul- oder Unikonto:** Konto für Organisationen, die Azure Active Directory mit Cloudverbund eingerichtet haben und bei denen sich alle Konten in einem einzigen Mandanten befinden

### <a name="enrollment-statuses"></a>Registrierungsstatus

- **Neu:** Dieser Status wird einer Registrierung zugewiesen, die innerhalb von 24 Stunden erstellt wurde. Er wird innerhalb von 24 Stunden auf den Status „Ausstehend“ aktualisiert.
- **Pending**: Der Registrierungsadministrator muss sich beim Azure Enterprise Portal anmelden. Nach der Anmeldung wechselt die Registrierung den Status „Aktiv“.
- **Aktiv:** Die Registrierung ist aktiv, und Konten und Abonnements können im Azure Enterprise Portal erstellt werden. Die Registrierung bleibt bis zum Enddatum des Enterprise Agreements aktiv.
- **Indefinite Extended Term** (Unbestimmte Laufzeitverlängerung): Eine unbestimmte Laufzeitverlängerung tritt ein, wenn das Enddatum des Enterprise Agreements überschritten wurde. Mit diesem Status können Azure EA-Kunden, die sich für die Laufzeitverlängerung entschieden haben, Azure-Dienste am Ende ihres Enterprise Agreements unbefristet weiterverwenden.

   Bevor die Azure EA-Registrierung das Enddatum des Enterprise Agreements erreicht, muss der Registrierungsadministrator sich für eine der folgenden Optionen entscheiden:

  - Verlängern der Registrierung durch Hinzufügen einer zusätzlichen Azure-Vorauszahlung
  - Übertragen auf eine neue Registrierung
  - Migrieren zum Microsoft Online Subscription-Programm (MOSP)
  - Bestätigen der Deaktivierung aller Dienste, die der Registrierung zugeordnet sind
- **Abgelaufen:** Der Azure EA-Kunde wurde aus der Laufzeitverlängerung ausgeschlossen, und die Azure EA-Registrierung hat das Enddatum des Enterprise Agreements erreicht. Die Registrierung läuft ab, und alle zugeordneten Dienste werden deaktiviert.
- **Übertragen:** Status für Registrierungen, bei denen alle zugeordneten Konten und Dienste in eine neue Registrierung übertragen wurden.
  >[!NOTE]
  > Registrierungen werden nicht automatisch übertragen, wenn während der Verlängerung eine neue Registrierungsnummer generiert wird. Sie müssen die vorherige Registrierungsnummer in Ihren Verlängerungsdokumenten angeben, um eine automatische Übertragung zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie mit [Reservierungen virtueller Computer](ea-portal-vm-reservations.md) Geld sparen können.
- Hilfe zu Problemen im Azure EA-Portal finden Sie unter [Beheben von Zugriffsproblemen beim Azure EA-Portal](ea-portal-troubleshoot.md).