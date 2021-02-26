---
title: Abrechnungsmodell für Azure Active Directory B2C
description: Lernen Sie das Azure AD B2C-Abrechnungsmodell kennen, das auf monatlich aktiven Benutzern (MAU) basiert, und erfahren Sie, wie Sie einen Azure AD B2C-Mandanten mit einem Azure-Abonnement verknüpfen und den entsprechenden Premium-Tarif auswählen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225207"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Abrechnungsmodell für Azure Active Directory B2C

Die Preisgestaltung für Azure Active Directory B2C (Azure AD B2C) basiert auf den monatlich aktiven Benutzern (MAU), d. h. die Anzahl eindeutiger Benutzer mit Authentifizierungsaktivität innerhalb eines Kalendermonats. Dieses Abrechnungsmodell gilt sowohl für Azure AD B2C-Mandanten als auch für die [Zusammenarbeit mit Azure AD-Gastbenutzern (B2B)](../active-directory/external-identities/external-identities-pricing.md). Das MAU-Abrechnungsmodell hilft Ihnen, Ihre Kosten zu senken, da es einen kostenlosen Tarif sowie eine flexible, vorhersehbare Preisgestaltung bietet. In diesem Artikel finden Sie Informationen zur MAU-Abrechnung, zum Verknüpfen Ihrer Azure AD B2C-Mandanten mit einem Abonnement und zum Ändern Ihres Tarifs.

> [!IMPORTANT]
> Dieser Artikel enthält keine Preisinformationen. Die aktuellen Informationen zur Nutzungsabrechnung und zu den Preisen finden Sie unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Ausführliche Informationen zu den Orten, an denen der Azure AD B2C-Dienst verfügbar ist und Benutzerdaten gespeichert werden, finden Sie unter [Regionale Verfügbarkeit und Datenresidenz in Azure AD B2C](data-residency.md).

## <a name="what-do-i-need-to-do"></a>Wie muss ich vorgehen?

Um die MAU-Abrechnung nutzen zu können, muss Ihr Azure AD B2C-Mandant mit einem Azure-Abonnement verknüpft sein. Möglicherweise müssen Sie auch Ihren Azure AD B2C-Mandanten auf einen anderen Tarif umstellen, wenn Sie Azure AD B2C Premium P2-Features (z. B. risikobasierter bedingter Zugriff) verwenden möchten.

|Für Ihren Mandanten gilt Folgendes:  |Sie müssen folgende Schritte durchführen:  |
|---------|---------|
| Azure AD B2C-Mandant, der bereits nach MAU abgerechnet wird     | Sie unternehmen nichts. Wenn sich Benutzer bei Ihrem Azure AD B2C-Mandanten authentifizieren, wird die Abrechnung automatisch anhand des MAU-basierten Abrechnungsmodells vorgenommen.        |
| Azure AD B2C-Mandant, der noch nicht mit einem Abonnement verknüpft ist     |  [Verknüpfen Sie Ihren Azure AD B2C-Mandanten mit einem Abonnement](#link-an-azure-ad-b2c-tenant-to-a-subscription), um die MAU-Abrechnung zu aktivieren.     |
| Azure AD B2C-Mandant, der vor dem 1. November 2019 mit einem Abonnement verknüpft wurde    | [Wechseln Sie zur MAU-Abrechnung (empfohlen)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants), oder behalten Sie das Abrechnungsmodell pro Authentifizierung bei.     |
| Azure AD B2C-Mandant, für den Sie Premium-Features (z. B. risikobasierter bedingter Zugriff) verwenden möchten    | [Wechseln Sie zu einem Azure AD-Tarif](#change-your-azure-ad-pricing-tier), der die gewünschten Features unterstützt.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Informationen zum MAU-Abrechnungsmodell (auf Basis der monatlich aktiven Benutzer)

Das MAU-Abrechnungsmodell für Azure AD B2C-Mandanten ist am **1. November 2019** in Kraft getreten. Alle Azure AD B2C-Mandanten, die Sie an oder nach diesem Datum erstellt und mit einem Abonnement verknüpft haben, werden auf MAU-Basis abgerechnet. Wenn Sie einen Azure AD B2C-Mandanten haben, der noch nicht mit einem Abonnement verknüpft wurde, müssen Sie dies jetzt tun. Wenn Sie einen vorhandenen Azure AD B2C-Mandanten haben, der vor dem 1. November 2019 mit einem Abonnement verknüpft wurde, empfiehlt es sich, ein Upgrade auf das MAU-Abrechnungsmodell (auf Basis der monatlich aktiven Benutzer) auszuführen. Alternativ können Sie auch das Abrechnungsmodell pro Authentifizierung beibehalten.
  
Ihren Azure AD B2C-Mandanten müssen Sie außerdem mit dem entsprechenden Azure-Tarif verknüpfen, der die gewünschten Features unterstützt. Für Premium-Features ist der Azure AD B2C [Premium P1- oder P2-Tarif](https://azure.microsoft.com/pricing/details/active-directory-b2c/) erforderlich. Möglicherweise müssen Sie Ihren Tarif aktualisieren, wenn Sie neue Features verwenden. Beispielsweise müssen Sie für den bedingten Zugriff den Azure AD B2C Premium P2-Tarif für Ihren Mandanten auswählen.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Verknüpfen eines Azure AD B2C-Mandanten mit einem Abonnement

Nutzungsgebühren für Azure Active Directory B2C (Azure AD B2C) werden einem Azure-Abonnement in Rechnung gestellt. Sie müssen einen Azure AD B2C-Mandanten explizit mit einem Azure-Abonnement verknüpfen, indem Sie eine Azure AD B2C-*Ressource* im Azure-Zielabonnement erstellen. In einem einzelnen Azure-Abonnement können neben anderen Azure-Ressourcen wie virtuellen Computern, Speicherkonten und Logik-Apps auch mehrere Azure AD B2C-Ressourcen erstellt werden. Sie können alle Ressourcen in einem Abonnement anzeigen, indem Sie den Azure Active Directory (Azure AD)-Mandanten aufrufen, dem das Abonnement zugeordnet ist.

Ein Abonnement, das mit einem Azure AD B2C-Mandanten verknüpft ist, kann für die Abrechnung der Azure AD B2C-Nutzung oder anderer Azure-Ressourcen, einschließlich zusätzlicher Azure AD B2C-Ressourcen, verwendet werden. Das Abonnement kann nicht verwendet werden, um weitere lizenzbasierte Azure-Dienste oder Office 365-Lizenzen innerhalb des Azure AD B2C-Mandanten hinzuzufügen.

### <a name="prerequisites"></a>Voraussetzungen

* [Azure-Abonnement](https://azure.microsoft.com/free/)
* [Azure AD B2C-Mandant](tutorial-create-tenant.md), der mit einem Abonnement verknüpft werden soll
  * Sie müssen Mandantenadministrator sein.
  * Der Mandant darf nicht bereits mit einem Abonnement verknüpft sein.

### <a name="create-the-link"></a>Erstellen des Links

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis mit dem zu verwendenden Azure-Abonnement (*nicht* das Verzeichnis mit dem Azure AD B2C-Mandanten) aus.
3. Wählen Sie **Ressource erstellen** aus, geben Sie `Active Directory B2C` im Feld **Marketplace durchsuchen** ein, und wählen Sie dann **Azure Active Directory B2C** aus.
4. Klicken Sie auf **Erstellen**.
5. Wählen Sie **Vorhandenen Azure AD B2C Mandanten mit meinem Azure-Abonnement verknüpfen** aus.
6. Wählen Sie in der Dropdownliste einen **Azure AD B2C-Mandanten** aus. Es werden nur die Mandanten angezeigt, bei denen Sie als globaler Administrator fungieren und die noch nicht mit einem Abonnement verknüpft sind. Das Feld **Name der Azure AD B2C-Ressource** wird mit dem Domänennamen des ausgewählten Azure AD B2C-Mandanten ausgefüllt.
7. Wählen Sie ein aktives Azure-**Abonnement** aus, für das Sie Administratorrechte haben.
8. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie dann den **Ressourcengruppenstandort** an. Die hier vorgenommenen Einstellungen für die Ressourcengruppe haben keine Auswirkungen auf den Standort, die Leistung oder den Abrechnungsstatus Ihres Azure AD B2C-Mandanten.
9. Klicken Sie auf **Erstellen**.

    ![Die Seite zum Erstellen der Azure AD-B2C-Ressource im Azure-Portal](./media/billing/portal-01-create-b2c-resource-page.png)

Nachdem Sie diese Schritte für einen Azure AD B2C-Mandanten ausgeführt haben, wird die Abrechnung für Ihr Azure-Abonnement gemäß den Azure Direct- oder den Enterprise Agreement-Details abgewickelt (sofern zutreffend).

## <a name="change-your-azure-ad-pricing-tier"></a>Ändern Ihres Azure AD-Tarifs

Ein Mandant muss auf Basis der Features, die Sie mit Ihrem Azure AD B2C-Mandanten nutzen möchten, mit dem entsprechenden Azure-Tarif verknüpft werden. Für Premium-Features ist Azure AD B2C Premium P1 oder P2 erforderlich, wie unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/) beschrieben. In manchen Fällen müssen Sie ein Upgrade für Ihren Tarif vornehmen, wenn Sie neue Features verwenden. Wenn Sie z. B. Identity Protection, den risikobasierten bedingten Zugriff und alle zukünftigen Premium P2-Funktionen mit Azure AD B2C nutzen möchten, müssen Sie den Azure AD B2C Premium P2-Tarif für Ihren Mandanten auswählen.

Führen Sie zum Ändern Ihres Tarifs die folgenden Schritte aus.

1. Melden Sie sich beim Azure-Portal an.

2. Wählen Sie im obersten Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis mit dem Azure-Abonnement aus, mit dem Ihr Azure AD B2C-Mandant verknüpft ist. (Wählen Sie *nicht* den Azure AD B2C-Mandanten selbst aus.)

3. Geben Sie im Suchfeld oben im Portal den Namen Ihres Azure AD B2C-Mandanten ein. Wählen Sie dann in den Suchergebnissen unter **Ressourcen** den Mandanten aus.

4. Wählen Sie auf der Seite **Ressourcenübersicht** unter **Tarif** die Option **Ändern** aus.

   ![Ändern des Tarifs](media/billing/change-pricing-tier.png)
 
5. Wählen Sie den Tarif aus, der die Funktionen enthält, die Sie aktivieren möchten.

   ![Auswählen des Tarifs](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Wechseln zur MAU-Abrechnung (Azure AD B2C-Mandant vor November 2019)

Wenn Sie Ihren Azure AD B2C-Mandanten vor dem **1. November 2019** mit einem Abonnement verknüpft haben, wird das vorherige Abrechnungsmodell pro Authentifizierung verwendet. Wir empfehlen, ein Upgrade auf das MAU-Abrechnungsmodell (basierend auf den monatlich aktiven Benutzern) vorzunehmen. Abrechnungsoptionen werden in Ihrer Azure AD B2C-Ressource konfiguriert.

Die Umstellung auf das MAU-Abrechnungsmodell ist **nicht umkehrbar**. Nachdem Sie eine Azure AD B2C-Ressource auf das MAU-Abrechnungsmodell umgestellt haben, können Sie diese Ressource nicht mehr auf das Abrechnungsmodell pro Authentifizierung zurücksetzen.

Im Folgenden wird erläutert, wie Sie für eine vorhandene Azure AD B2C-Ressource die Umstellung auf die MAU-Abrechnung vornehmen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) als Abonnementbesitzer mit Administratorzugriff auf die Azure AD B2C-Ressource an.

2. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Azure AD B2C-Verzeichnis aus, für das Sie ein Upgrade auf die MAU-Abrechnung durchführen möchten.<br/>

    ![Verzeichnis- und Abonnementfilter im Azure-Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.

4. Wählen Sie auf der Seite **Übersicht** des Azure AD B2C-Mandanten den Link unter **Ressourcenname** aus. Sie werden an die Azure AD B2C-Ressource in Ihrem Azure AD-Mandanten weitergeleitet.<br/>

    ![Hervorgehobener Link zur Azure AD B2C-Ressource im Azure-Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Wählen Sie auf der Seite **Übersicht** für die Azure AD B2C-Ressource unter **Abrechenbare Einheiten** den Link **Pro Authentifizierung (Änderung in MAU)** aus.<br/>

    ![Hervorgehobener Link für die Änderung in MAU im Azure-Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Wählen Sie **Bestätigen** aus, um das Upgrade auf die MAU-Abrechnung abzuschließen.<br/>

    ![Bestätigungsdialogfeld für die MAU-basierte Abrechnung im Azure-Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Was erwartet Sie, wenn Sie die Abrechnung „Pro Authentifizierung“ auf die MAU-Abrechnung umstellen?

Die MAU-basierte Messung wird aktiviert, sobald Sie als Abonnement-/Ressourcenbesitzer die Änderung bestätigen. Ihre monatliche Rechnung enthält die Authentifizierungseinheiten, die bis zur Änderung abgerechnet wurden, und die neuen Einheiten der MAU-Abrechnung ab dem Änderungszeitpunkt.

Benutzer werden im Übergangsmonat nicht doppelt gezählt. Eindeutige aktive Benutzer, die sich vor der Änderung authentifizieren, werden in einem Kalendermonat nach dem Tarif „Pro Authentifizierung“ abgerechnet. Diese Benutzer werden im restlichen Abrechnungszyklus des Abonnements nicht in die MAU-Berechnung einbezogen. Beispiel:

* Der B2C-Mandanten von Contoso hat 1.000 Benutzer. 250 Benutzer sind in jedem Monat aktiv. Der Abonnementadministrator ändert am 10. Tag des Monats die Abrechnung „Pro Authentifizierung“ in die MAU-Abrechnung (basierend auf den monatlich aktiven Benutzern).
* Die Abrechnung vom 1. bis 10. des Monats erfolgt nach dem Modell „Pro Authentifizierung“.
  * Wenn sich in diesem Zeitraum (1. bis 10.) 100 Benutzer anmelden, werden diese Benutzer als *für den Monat bezahlt* gekennzeichnet.
* Die Abrechnung ab dem 10. (effektiver Zeitpunkt der Umstellung) erfolgt nach dem MAU-Tarif.
  * Wenn sich in diesem Zeitraum (10. bis 30.) weitere 150 Benutzer anmelden, werden nur die weiteren 150 Benutzer in Rechnung gestellt.
  * Die fortgesetzte Aktivität der ersten 100 Benutzer hat keine Auswirkung auf die Abrechnung für den Rest des Kalendermonats.

Während des Abrechnungszeitraums der Umstellung werden dem Abonnementbesitzer in der Rechnung für sein Azure-Abonnement wahrscheinlich Einträge für beide Methoden (Pro Authentifizierung und MAU) angezeigt:

* Ein Eintrag für die Nutzung nach dem Modell „Pro Authentifizierung“ bis zum Datum/Uhrzeit der Änderung.
* Ein Eintrag für die Nutzung nach der Änderung für die MAU-Abrechnung (basierend auf monatlich aktiven Benutzern).

Die aktuellen Informationen zur Nutzungsabrechnung und zu den Preisen für Azure AD B2C finden Sie unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Verwalten Ihrer Azure AD B2C-Mandantenressourcen

Nach der Erstellung einer Azure AD B2C-Ressource in einem Azure-Abonnement sollte zusammen mit Ihren anderen Azure-Ressourcen eine neue Ressource vom Typ „B2C-Mandant“ angezeigt werden.

Diese Ressource ermöglicht Folgendes:

* Navigieren zum Abonnement zum Überprüfen der Abrechnungsinformationen
* Abrufen der Mandanten-ID des Azure AD B2C Mandanten im GUID-Format
* Navigieren zu Ihrem Azure AD B2C-Mandanten
* Senden Sie eine Supportanfrage.
* Verschieben Ihrer Azure AD B2C-Mandantenressource in ein anderes Azure-Abonnement oder eine andere Ressourcengruppe

### <a name="regional-restrictions"></a>Regionale Einschränkungen

Wenn Sie bei der Erstellung von Azure-Ressourcen in Ihrem Abonnement regionale Einschränkungen festgelegt haben, können diese Einschränkungen das Erstellen der Azure AD B2C-Ressource verhindern.

Um dieses Problem zu beheben, sollten Sie Ihre regionalen Einschränkungen lockern.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure Cloud Solution Provider (CSP)-Abonnements

Azure Cloud Solution Provider (CSP)-Abonnements werden in Azure AD B2C unterstützt. Die Funktionalität ist über APIs oder das Azure-Portal für Azure AD B2C und für alle Azure-Ressourcen verfügbar. CSP-Abonnementadministratoren können auf die gleiche Weise wie bei allen anderen Azure-Ressourcen Beziehungen mit Azure AD B2C verknüpfen, verschieben und löschen.

Die Verwaltung von Azure AD B2C mit der rollenbasierten Zugriffssteuerung wird durch die Zuordnung eines Azure AD B2C-Mandanten zu einem CSP-Abonnement nicht beeinflusst. Die rollenbasierte Zugriffssteuerung wird mithilfe von mandantenbasierten (nicht abonnementbasierten) Rollen realisiert.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Ändern des Abonnements zur Abrechnung des Azure AD B2C-Mandanten

### <a name="move-using-azure-resource-manager"></a>Verschieben mit Azure Resource Manager

Azure AD B2C-Mandanten können mit Azure Resource Manager in ein anderes Abonnement verschoben werden, wenn Quell- und Zielabonnement innerhalb desselben Azure Active Directory-Mandanten liegen.

Informationen zum Verschieben von Azure-Ressourcen wie Ihr Azure AD B2C-Mandant in ein anderes Abonnement finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Bevor Sie die Verschiebung beginnen, lesen Sie unbedingt den gesamten Artikel, um die Einschränkungen bei einer solchen Verschiebung und die entsprechenden Anforderungen in vollem Umfang zu verstehen. Zusätzlich zu den Anweisungen zum Verschieben von Ressourcen enthält er wichtige Informationen wie eine vor der Verschiebung anzuwendende Checkliste und wie der Verschiebevorgang überprüft wird.

### <a name="move-by-unlinking-and-relinking"></a>Verschieben durch Aufheben der Verknüpfung und Neuverknüpfen

Wenn Quell- und Zielabonnement mit unterschiedlichen Azure Active Directory-Mandanten verknüpft sind, können Sie die Verschiebung nicht in der oben beschriebenen Weise mit Azure Resource Manager ausführen. Sie können jedoch trotzdem dasselbe Ergebnis erzielen, indem Sie die Verknüpfung des Azure AD B2C-Mandanten mit dem Quellabonnement aufheben und eine Neuverknüpfung mit dem Zielabonnement vornehmen. Diese Methode ist sicher, da nur der *Abrechnungslink* gelöscht wird und nicht der Azure AD B2C-Mandant selbst. Es sind keine Benutzer, Apps, Benutzerflows usw. betroffen.

1. Im Azure AD B2C-Verzeichnis selbst [laden Sie einen Gastbenutzer ein](user-overview.md#guest-user), der aus dem Azure AD-Zielmandanten (der Mandant, mit dem das Azure-Zielabonnement verknüpft ist) stamm. Stellen Sie dabei sicher, dass diesem Benutzer in Azure AD B2C die **globale Administratorrolle** zugewiesen ist.
1. Navigieren Sie zu der *Azure-Ressource*, die Azure AD B2C in Ihrem Azure-Quellabonnement darstellt, wie im Abschnitt [Verwalten Ihrer Azure AD B2C-Mandantenressourcen](#manage-your-azure-ad-b2c-tenant-resources) weiter oben erläutert. Wechseln Sie nicht zum eigentlichen Azure AD B2C-Mandanten.
1. Wählen Sie auf der Seite **Übersicht** die Schaltfläche **Löschen** aus. Dadurch werden *keine* Benutzer oder Anwendungen des entsprechenden Azure AD B2C-Mandanten gelöscht. Lediglich der Abrechnungslink wird aus dem Quellabonnement entfernt.
1. Melden Sie sich im Azure-Portal mit dem Benutzerkonto an, das im 1. Schritt Azure AD B2C als Administratorkonto hinzugefügt wurde. Navigieren Sie dann zum Azure-Zielabonnement, das mit dem Azure Active Directory-Zielmandanten verknüpft ist. 
1. Stellen Sie den Abrechnungslink im Zielabonnement wieder her, indem Sie den oben unter [Erstellen des Links](#create-the-link) beschriebenen Vorgang ausführen.
1. Ihre Azure AD B2C-Ressource wurde nun in das Azure-Zielabonnement verschoben (verknüpft mit dem Azure Active Directory-Zielmandanten) und wird ab jetzt über dieses Abonnement abgerechnet.

## <a name="next-steps"></a>Nächste Schritte

Die aktuellsten Preisinformationen finden Sie unter [Azure Active Directory B2C – Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).