---
title: Zuweisen des Zugriffs auf Daten in Azure Cost Management
description: Dieser Artikel führt Sie durch das Zuweisen von Berechtigungen für Daten in Azure Cost Management für verschiedene Zugriffsbereiche.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 660b5751e3b3cbc632331e99d797af3392a8aea4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "91371965"
---
# <a name="assign-access-to-cost-management-data"></a>Zuweisen des Zugriffs auf Daten in Cost Management

Für Benutzer mit Azure Enterprise Agreements wird die Zugriffsebene auf Daten in Azure Cost Management durch eine Kombination aus im Azure-Portal und im Enterprise-Portal (EA-Portal) gewährten Berechtigungen definiert. Für Benutzer mit anderen Typen von Azure-Konten ist das Festlegen der Zugriffsebene auf Cost Management-Daten einfacher, da dort die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) genutzt werden kann. Dieser Artikel führt Sie durch das Zuweisen des Zugriffs auf Daten in Cost Management. Nachdem die Kombination von Berechtigungen zugewiesen wurde, kann der Benutzer anhand des Zugriffsbereichs und des im Azure-Portal ausgewählten Bereichs auf Daten in Cost Management zugreifen.

Der von einem Benutzer ausgewählte Bereich wird im gesamten Cost Management verwendet, um Daten zu konsolidieren und den Zugriff auf Kosteninformationen zu steuern. Bei Verwendung von Bereichen findet keine Mehrfachauswahl durch Benutzer statt. Stattdessen wählen sie einen größeren Bereich aus, auf den untergeordnete Bereiche erweitert werden, und filtern diese dann nach den gewünschten Elementen für die Anzeige. Diese Datenkonsolidierung sollten Sie verstehen, da einige Personen nicht auf einen übergeordneten Bereich zugreifen sollten, in den die untergeordneten Bereiche zusammengefasst werden.

Sehen Sie sich das Video zum [Steuern des Zugriffs mit Cost Management](https://www.youtube.com/watch?v=_uQzQ9puPyM) an, um mehr über die Zuweisung des Zugriffs zum Anzeigen von Kosten und Gebühren mit der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) zu erfahren. Weitere Videos finden Sie im [YouTube-Kanal zu Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Bereiche in Cost Management

Cost Management unterstützt eine Vielzahl von Azure-Kontotypen. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](understand-cost-mgt-data.md). Der Kontotyp bestimmt die verfügbaren Bereiche.

### <a name="azure-ea-subscription-scopes"></a>Azure EA-Abonnementbereiche

Zum Anzeigen von Kostendaten für Azure EA-Abonnements muss ein Benutzer mindestens über Lesezugriff auf einen oder mehrere der folgenden Bereiche verfügen.

| **Umfang** | **Definiert unter** | **Erforderlicher Zugriff zum Anzeigen von Daten** | **Erforderliche EA-Einstellung** | **Konsolidierung von Daten** |
| --- | --- | --- | --- | --- |
| Abrechnungskonto<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Unternehmensadministrator | Keine | Alle Abonnements aus dem Enterprise Agreement |
| Department | [https://ea.azure.com](https://ea.azure.com/) | Abteilungsadministrator | **Gebühren anzeigen** für Abteilungsadministratoren aktiviert | Alle Abonnements, die zu einem Registrierungskonto gehören, das mit der Abteilung verknüpft ist |
| Registrierungskonto<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Kontobesitzer | **AO view charges** (Gebühren anzeigen für Kontobesitzer) aktiviert | Alle Abonnements aus dem Registrierungskonto |
| Verwaltungsgruppe | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-Leser (oder Leser) | **AO view charges** (Gebühren anzeigen für Kontobesitzer) aktiviert | Alle Abonnements unter der Verwaltungsgruppe |
| Subscription | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-Leser (oder Leser) | **AO view charges** (Gebühren anzeigen für Kontobesitzer) aktiviert | Alle Ressourcen/Ressourcengruppen im Abonnement |
| Resource group | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-Leser (oder Leser) | **AO view charges** (Gebühren anzeigen für Kontobesitzer) aktiviert | Alle Ressourcen in der Ressourcengruppe |

<sup>1</sup> Das Abrechnungskonto wird auch als Enterprise Agreement oder Registrierung bezeichnet.

<sup>2</sup> Das Registrierungskonto wird auch als Kontobesitzer bezeichnet.


## <a name="other-azure-account-scopes"></a>Andere Azure-Kontobereiche

Zum Anzeigen von Kostendaten für andere Azure-Abonnements muss ein Benutzer mindestens über Lesezugriff auf einen oder mehrere der folgenden Bereiche verfügen:

- Azure-Konto
- Verwaltungsgruppe
- Resource group

Nachdem Partner Kunden in eine Microsoft-Kundenvereinbarung aufgenommen haben, stehen verschiedene Bereiche zur Verfügung. CSP-Kunden können Features von Cost Management verwenden, wenn sie von ihrem CSP-Partner aktiviert werden. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Cost Management für Partner](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Aktivieren des Zugriffs auf Kosten im Azure-Portal

Für den Abteilungsbereich muss die Option **Abteilungsadministratoren können Gebühren anzeigen.** (Gebühren anzeigen für Abteilungsadministratoren) auf **Ein** festgelegt werden. Konfigurieren Sie die Option entweder im Azure-Portal oder im EA-Portal. Für alle anderen Bereiche muss die Option **Kontobesitzer können Gebühren anzeigen.** (Gebühren anzeigen für Kontobesitzer) auf **Ein** festgelegt werden.

So aktivieren Sie eine Option im Azure-Portal:

1. Melden Sie sich beim Azure-Portal unter https://portal.azure.com mit einem Enterprise-Administratorkonto an.
1. Wählen Sie das Menüelement **Cost Management + Abrechnung** aus.
1. Wählen Sie **Abrechnungsbereiche** aus, um eine Liste der verfügbaren Abrechnungsbereiche und Abrechnungskonten anzuzeigen.
1. Wählen Sie in der Liste der verfügbaren Abrechnungskonten Ihr **Abrechnungskonto** aus.
1. Wählen Sie unter **Einstellungen** das Menüelement **Richtlinien** aus, und konfigurieren Sie dann die Einstellung.  
    ![Richtlinien für den Abrechnungsbereich mit Optionen zum Anzeigen von Gebühren](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Nachdem die Optionen zum Anzeigen von Gebühren aktiviert wurden, müssen für die meisten Bereiche auch Berechtigungen für die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) im Azure-Portal konfiguriert werden.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Aktivieren des Zugriffs auf Kosten im EA-Portal

Für den Abteilungsbereich muss die Option **Abteilungsadministratoren können Gebühren anzeigen** im EA-Portal **Aktiviert** sein. Konfigurieren Sie die Option entweder im Azure-Portal oder im EA-Portal. Für alle anderen Bereiche muss die Option **AO view charges** (Gebühren anzeigen für Kontobesitzer) im EA-Portal **aktiviert** sein.

So aktivieren Sie eine Option im EA-Portal:

1. Melden Sie sich beim EA-Portal unter [https://ea.azure.com](https://ea.azure.com) mit einem Enterprise-Administratorkonto an.
2. Wählen Sie im linken Bereich **Verwalten** aus.
3. Aktivieren Sie für die Kostenverwaltungsbereiche, auf die Sie Zugriff gewähren möchten, nach Bedarf die Optionen **DA view charges** (Gebühren anzeigen für Abteilungsadministrator) oder **AO view charges** (Gebühren anzeigen für Kontobesitzer).  
    ![Registerkarte „Registrierung“ mit Optionen zum Anzeigen der Gebühren für Abteilungsadministratoren und Kontobesitzer](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Nachdem die Optionen zum Anzeigen von Gebühren aktiviert wurden, müssen für die meisten Bereiche auch Berechtigungen für die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) im Azure-Portal konfiguriert werden.

## <a name="enterprise-administrator-role"></a>Unternehmensadministratorrolle

Standardmäßig kann ein Unternehmensadministrator auf das Abrechnungskonto (Enterprise Agreement/Registrierung) und alle anderen untergeordneten Bereiche zugreifen. Der Unternehmensadministrator weist anderen Benutzern den Zugriff auf Bereiche zu. Als bewährte Methode für Geschäftskontinuität sollten immer zwei Benutzer den Zugriff eines Unternehmensadministrators haben. Die folgenden Abschnitte sind detaillierte Beispiele für die Zuweisung des Zugriffs auf Bereiche für andere Benutzer durch den Unternehmensadministrator.

## <a name="assign-billing-account-scope-access"></a>Zuweisen des Zugriffs auf den Abrechnungskontenbereich

Für den Zugriff auf den Abrechnungskontenbereich muss im EA-Portal die Berechtigung eines Unternehmensadministrators vorliegen. Der Unternehmensadministrator kann Kosten in der gesamten EA-Registrierung oder in mehreren Registrierungen anzeigen. Es ist keine Aktion im Azure-Portal für den Abrechnungskontenbereich erforderlich.

1. Melden Sie sich beim EA-Portal unter [https://ea.azure.com](https://ea.azure.com) mit einem Enterprise-Administratorkonto an.
2. Wählen Sie im linken Bereich **Verwalten** aus.
3. Wählen Sie auf der Registerkarte **Registrierung** die Registrierung aus, die Sie verwalten möchten.  
    ![Auswählen Ihrer Registrierung im EA-Portal](./media/assign-access-acm-data/ea-portal.png)
4. Wählen Sie **+ Administrator hinzufügen** aus.
5. Wählen Sie im Feld „Administrator hinzufügen“ den Authentifizierungstyp aus, und geben Sie die E-Mail-Adresse des Benutzers ein.
6. Wenn der Benutzer schreibgeschützten Zugriff auf Kosten- und Nutzungsdaten erhalten soll, wählen Sie unter **Schreibgeschützt** die Option **Ja** aus.  Klicken Sie andernfalls auf **Nein**.
7. Wählen Sie **Hinzufügen** aus, um das Konto zu erstellen.  
    ![Beispielinformationen im Feld „Administrator hinzufügen“](./media/assign-access-acm-data/add-admin.png)

Es kann bis zu 30 Minuten dauern, bevor der neue Benutzer in Cost Management auf Daten zugreifen kann.

### <a name="assign-department-scope-access"></a>Zuweisen des Zugriffs auf den Abteilungsbereich

Für den Zugriff auf den Abteilungsbereich muss im EA-Portal der Zugriff eines Abteilungsadministrators („DA view charges“) gewährt werden. Der Abteilungsadministrator kann Kosten- und Nutzungsdaten für eine oder mehrere Abteilungen anzeigen. Die Daten für die Abteilung schließen alle Abonnements ein, die zu einem Registrierungskonto gehören und mit der Abteilung verknüpft sind. Im Azure-Portal ist keine Aktion erforderlich.

1. Melden Sie sich beim EA-Portal unter [https://ea.azure.com](https://ea.azure.com) mit einem Enterprise-Administratorkonto an.
2. Wählen Sie im linken Bereich **Verwalten** aus.
3. Wählen Sie auf der Registerkarte **Registrierung** die Registrierung aus, die Sie verwalten möchten.
4. Wählen Sie die Registerkarte **Abteilung** und anschließend **Administrator hinzufügen** aus.
5. Wählen Sie im Feld „Add Department Administrator“ (Abteilungsadministrator hinzufügen) den Authentifizierungstyp aus, und geben Sie die E-Mail-Adresse des Benutzers ein.
6. Wenn der Benutzer schreibgeschützten Zugriff auf Kosten- und Nutzungsdaten erhalten soll, wählen Sie unter **Schreibgeschützt** die Option **Ja** aus.  Klicken Sie andernfalls auf **Nein**.
7. Wählen Sie die Abteilungen aus, denen Sie Berechtigungen für die Abteilungsverwaltung gewähren möchten.
8. Wählen Sie **Hinzufügen** aus, um das Konto zu erstellen.  
    ![Eingeben der erforderlichen Informationen in das Feld „Abteilungsadministrator hinzufügen“](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Zuweisen des Zugriffs auf den Registrierungskontobereich

Für den Zugriff auf den Registrierungskontobereich muss im EA-Portal der Zugriff eines Kontobesitzers („AO view charges“) bestehen. Der Kontobesitzer kann Kosten- und Nutzungsdaten anzeigen, die mit den Abonnements verknüpft sind, die von einem Registrierungskonto erstellt wurden. Im Azure-Portal ist keine Aktion erforderlich.

1. Melden Sie sich beim EA-Portal unter [https://ea.azure.com](https://ea.azure.com) mit einem Enterprise-Administratorkonto an.
2. Wählen Sie im linken Bereich **Verwalten** aus.
3. Wählen Sie auf der Registerkarte **Registrierung** die Registrierung aus, die Sie verwalten möchten.
4. Wählen Sie die Registerkarte **Konto** und dann **Konto hinzufügen** aus.
5. Wählen Sie im Feld „Konto hinzufügen“ die **Abteilung** aus, der das Konto zugeordnet werden soll, oder lassen sie es nicht zugewiesen.
6. Wählen Sie den Authentifizierungstyp aus, und geben Sie den Kontonamen ein.
7. Geben Sie die E-Mail-Adresse des Benutzers und dann optional die Kostenstelle ein.
8. Wählen Sie **Hinzufügen** aus, um das Konto zu erstellen.  
    ![Eingeben der erforderlichen Informationen in das Feld „Konto hinzufügen“ für ein Registrierungskonto](./media/assign-access-acm-data/add-account.png)

Nachdem die obigen Schritte ausgeführt wurden, wird das Benutzerkonto zu einem Registrierungskonto im Enterprise Portal. Fortan können damit Abonnements erstellt werden. Der Benutzer kann auf Kosten- und Nutzungsdaten für von ihm erstellte Abonnements zugreifen.

## <a name="assign-management-group-scope-access"></a>Zuweisen des Zugriffs auf den Verwaltungsgruppenbereich

Für den Zugriff zur Anzeige des Verwaltungsgruppenbereichs ist mindestens die Berechtigung „Cost Management-Leser“ (oder „Leser“) erforderlich. Sie können die Berechtigungen für eine Verwaltungsgruppe im Azure-Portal konfigurieren. Sie benötigen mindestens die Berechtigung „Benutzerzugriffsadministrator“ (oder „Besitzer“) für die Verwaltungsgruppe, um anderen Benutzern Zugriff zu gewähren. Für Azure EA-Konten muss zudem im EA-Portal die Einstellung **Kontobesitzer können Gebühren anzeigen** aktiviert sein.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie auf der Seitenleiste **Alle Dienste** aus, suchen Sie _Verwaltungsgruppen_, und wählen Sie dann **Verwaltungsgruppen** aus.
3. Wählen Sie die Verwaltungsgruppe in der Hierarchie aus.
4. Wählen Sie neben dem Namen Ihrer Verwaltungsgruppe **Details** aus.
5. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
6. Wählen Sie **Hinzufügen**.
7. Wählen Sie unter **Rolle** die Option **Cost Management-Leser** aus.
8. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Anwendung** aus.
9. Um den Zugriff zuzuweisen, suchen Sie den Benutzer, und wählen Sie ihn aus.
10. Wählen Sie **Speichern** aus.  
    ![Beispielinformationen im Feld „Berechtigungen hinzufügen“ für eine Verwaltungsgruppe](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Zuweisen des Zugriffs auf den Abonnementbereich

Der Zugriff auf ein Abonnement erfordert mindestens die Berechtigung „Kostenverwaltung: Leser“ (oder „Leser“). Sie können die Berechtigungen für ein Abonnement im Azure-Portal konfigurieren. Sie benötigen mindestens die Berechtigung „Benutzerzugriffsadministrator“ (oder „Besitzer“) für das Abonnement, um anderen Benutzern Zugriff zu gewähren. Für Azure EA-Konten muss zudem im EA-Portal die Einstellung **Kontobesitzer können Gebühren anzeigen** aktiviert sein.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie auf der Seitenleiste **Alle Dienste** aus, suchen Sie _Abonnements_, und wählen Sie dann **Abonnements** aus.
3. Wählen Sie Ihr Abonnement aus.
4. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
5. Wählen Sie **Hinzufügen**.
6. Wählen Sie unter **Rolle** die Option **Cost Management-Leser** aus.
7. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Anwendung** aus.
8. Um den Zugriff zuzuweisen, suchen Sie den Benutzer, und wählen Sie ihn aus.
9. Wählen Sie **Speichern** aus.

## <a name="assign-resource-group-scope-access"></a>Zuweisen des Zugriffs auf den Ressourcengruppenbereich

Für den Zugriff auf den Ressourcengruppenbereich ist mindestens die Berechtigung „Kostenverwaltung: Leser“ (oder „Leser“) erforderlich. Sie können die Berechtigungen für eine Ressourcengruppe im Azure-Portal konfigurieren. Sie benötigen mindestens die Berechtigung „Benutzerzugriffsadministrator“ (oder „Besitzer“) für die Ressourcengruppe, um anderen Benutzern Zugriff zu gewähren. Für Azure EA-Konten muss zudem im EA-Portal die Einstellung **Kontobesitzer können Gebühren anzeigen** aktiviert sein.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Wählen Sie auf der Seitenleiste **Alle Dienste** aus, suchen Sie _Ressourcengruppen_, und wählen Sie dann **Ressourcengruppen** aus.
3. Wählen Sie Ihre Ressourcengruppe aus.
4. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
5. Wählen Sie **Hinzufügen**.
6. Wählen Sie unter **Rolle** die Option **Cost Management-Leser** aus.
7. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Anwendung** aus.
8. Um den Zugriff zuzuweisen, suchen Sie den Benutzer, und wählen Sie ihn aus.
9. Wählen Sie **Speichern** aus.

## <a name="cross-tenant-authentication-issues"></a>Mandantenübergreifende Authentifizierungsprobleme

Zurzeit bietet Azure Cost Management eingeschränkte Unterstützung für die mandantenübergreifende Authentifizierung. Unter bestimmten Umständen wird bei der Kostenanalyse der Fehler **Zugriff verweigert** zurückgegeben, wenn Sie versuchen, sich mandantenübergreifend zu authentifizieren. Dieses Problem kann auftreten, wenn Sie die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für das Abonnement eines anderen Mandanten konfigurieren und dann versuchen, Kostendaten anzuzeigen.

*So können Sie das Problem umgehen*: Nachdem Sie die mandantenübergreifende rollenbasierte Azure RBAC konfiguriert haben, warten Sie eine Stunde. Versuchen Sie dann, in beiden Mandanten Kosten in der Kostenanalyse anzuzeigen oder Benutzern Zugriff auf Cost Management zu gewähren.  


## <a name="next-steps"></a>Nächste Schritte

- Falls Sie den ersten Schnellstart für Cost Management noch nicht abgeschlossen haben, lesen Sie ihn unter [Erste Schritte in der Analyse von Kosten](quick-acm-cost-analysis.md).
