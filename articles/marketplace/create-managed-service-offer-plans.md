---
title: Erstellen von Plänen für Ihr Angebot für den verwalteten Dienst im Azure Marketplace
description: Erfahren Sie, wie Sie Pläne für Ihr Angebot für den verwalteten Dienst mit Microsoft Partner Center in Azure Marketplace konfigurieren.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383227"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Erstellen von Plänen für Ihr Angebot für den verwalteten Dienst

Angebote für den verwalteten Dienst, die über den kommerziellen Marketplace von Microsoft verkauft werden, müssen mindestens einen Plan aufweisen. Sie können eine Vielzahl von Plänen mit unterschiedlichen Optionen im gleichen Angebot erstellen. Diese Pläne (auch als SKUs bezeichnet) können sich in Bezug auf Version, Monetarisierung und Dienstebenen unterscheiden. Ausführliche Anleitungen zu Plänen finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](./plans-pricing.md).

## <a name="create-a-plan"></a>Erstellen von Plänen

1. Wählen Sie auf der Registerkarte **Planübersicht** Ihres Angebots im Partner Center **+ Neuen Plan erstellen** aus.
2. Geben Sie im angezeigten Dialogfeld im Feld **Plan-ID** eine eindeutige Plan-ID ein. Verwenden Sie bis zu 50 Kleinbuchstaben, Ziffern, Bindestriche und Unterstriche. Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben. Diese ID ist für Ihre Kunden sichtbar.
3. Geben Sie im Feld **Planname** einen eindeutigen Namen für diesen Plan ein. Es sind maximal 50 Zeichen zulässig. Dieser Name ist für Ihre Kunden sichtbar.
4. Klicken Sie auf **Erstellen**.

## <a name="define-the-plan-listing"></a>Definieren des Planlistings

Definieren Sie auf der Registerkarte **Planlisten** den Plannamen und die Beschreibung so, wie sie im kommerziellen Marketplace angezeigt werden sollen.

1. Im Feld **Planname** wird der Name angezeigt, den Sie zuvor für diesen Plan angegeben haben. Sie können diesen jederzeit ändern. Dieser Name wird als Titel des Plans für Ihr Angebot im kommerziellen Marketplace angezeigt.
2. Geben Sie im Feld **Planzusammenfassung** eine kurze Beschreibung Ihres Plans ein, die in den Marketplace-Suchergebnissen verwendet werden kann.
3. Erläutern Sie im Feld **Planbeschreibung**, was diesen Plan einzigartig macht und von anderen Plänen in Ihrem Angebot unterscheidet.
4. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren.

## <a name="define-pricing-and-availability"></a>Definieren von Preis und Verfügbarkeit

Das einzige Preismodell, das für Angebote für den verwalteten Dienst verfügbar ist, ist **Bring your own License (BYOL)** . Dies bedeutet, dass Sie Ihren Kunden Kosten im Zusammenhang mit diesem Angebot direkt berechnen, und dass Microsoft Ihnen keine Gebühren berechnet.

Sie können festlegen, dass jeder Plan für alle Benutzer (öffentlich) oder nur für eine bestimmte Zielgruppe (privat) angezeigt werden soll.

> [!NOTE]
> Private Pläne werden bei Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt.

> [!IMPORTANT]
> Nachdem ein Plan als öffentlich veröffentlicht wurde, kann er nicht mehr in privat geändert werden. Um zu steuern, welche Kunden Ihr Angebot annehmen und Ressourcen delegieren können, verwenden Sie einen privaten Plan. Mit einem öffentlichen Plan können Sie die Verfügbarkeit nicht auf bestimmte Kunden oder auf eine bestimmte Anzahl von Kunden beschränken, obgleich Sie den Verkauf des Plans vollständig beenden können, wenn Sie sich dafür entscheiden. Sie können den Zugriff auf eine Delegierung nur entfernen, nachdem ein Kunde ein Angebot akzeptiert hat, wenn Sie beim Veröffentlichen des Angebots eine Autorisierung mit der auf Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste festgelegten Rollendefinition eingeschlossen haben. Sie können sich auch an den Kunden wenden und ihn bitten, Ihren Zugriff zu entfernen.

## <a name="make-your-plan-public"></a>Festlegen eines Plans als öffentlich

1. Wählen Sie unter **Sichtbarkeit des Plans** die Option **Öffentlich** aus.
2. Wähen Sie **Entwurf speichern** aus. Um zur Registerkarte „Planübersicht“ zurückzukehren, wählen Sie in der oberen linken Ecke **Planübersicht** aus.
3. Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie auf der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus.

## <a name="make-your-plan-private"></a>Festlegen Ihres Plans als privat

Sie gewähren Zugriff auf einen privaten Plan mithilfe von Azure-Abonnement-IDs. Sie können maximal 10 Abonnement-IDs manuell eingeben oder über eine CSV-Datei bis zu 10.000 Abonnement-IDs angeben.

So fügen Sie bis zu 10 Abonnement-IDs manuell hinzu:

1. Wählen Sie unter **Sichtbarkeit des Plans** die Option **Privat** aus.
2. Geben Sie die Azure-Abonnement-ID der Zielgruppe ein, der Sie Zugriff gewähren möchten.
3. Geben Sie optional im Feld **Beschreibung** eine Beschreibung der Zielgruppe ein.
4. Wählen Sie zum Hinzufügen einer weiteren ID **ID hinzufügen (max. 10)** .
5. Wenn Sie die gewünschten IDs hinzugefügt haben, wählen Sie **Entwurf speichern** aus.

So fügen Sie bis zu 10.000 Abonnement-IDs mit einer CSV-Datei manuell hinzu:

1. Wählen Sie unter **Sichtbarkeit des Plans** die Option **Privat** aus.
2. Wählen Sie den Link **Zielgruppe exportieren (CSV)** aus. Damit wird eine CSV-Datei heruntergeladen.
3. Öffnen Sie die CSV-Datei. Geben Sie in der Spalte **Id** die Azure-Abonnement-IDs ein, denen Sie Zugriff gewähren möchten.
4. In der Spalte  **Beschreibung**  können Sie optional eine Beschreibung für jeden Eintrag hinzufügen.
5. Fügen Sie in der Spalte **Typ**  jeder Zeile mit einer ID  **SubscriptionId**  hinzu.
6. Speichern Sie die Datei als CSV-Datei.
7. Wählen Sie im Partner Center den Link **Zielgruppe importieren (CSV)** aus.
8. Wählen Sie im Dialogfeld  **Bestätigen**  die Option **Ja** aus, und laden Sie dann die CSV-Datei hoch.
9. Wählen Sie  **Entwurf speichern** aus.

## <a name="technical-configuration"></a>Technische Konfiguration

In diesem Abschnitt wird ein Manifest mit Autorisierungsinformationen zum Verwalten von Kundenressourcen erstellt. Diese Informationen werden benötigt, um die [delegierte Azure-Ressourcenverwaltung](../lighthouse/concepts/azure-delegated-resource-management.md) zu aktivieren.

Unter [Mandanten, Rollen und Benutzer in Azure Lighthouse-Szenarien](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) erfahren Sie, welche Rollen unterstützt werden und welche bewährten Methoden für das Definieren von Autorisierungen gelten.

> [!NOTE]
> Die Benutzer und Rollen in Ihren Autorisierungseinträgen werden auf jeden Kunden angewendet, der den Plan aktiviert. Wenn Sie den Zugriff auf einen bestimmten Kunden eingrenzen möchten, müssen Sie einen privaten Plan für dessen ausschließliche Nutzung veröffentlichen.

### <a name="manifest"></a>Manifest

1. Geben Sie unter **Manifest** eine **Version** für das Manifest an. Verwenden Sie das Format n.n.n (z. B. 1.2.5).
2. Geben Sie Ihre **Mandanten-ID** ein. Dabei handelt es sich um eine GUID, die der Azure Active Directory (Azure AD)-Mandanten-ID Ihrer Organisation zugeordnet ist. Dies ist der Verwaltungsmandant, über den Sie auf Ressourcen Ihrer Kunden zugreifen. Wenn Sie diese nicht zur Hand haben, können Sie sie anzeigen, indem Sie rechts oben im Azure-Portal auf Ihren Kontonamen zeigen, oder indem Sie **Verzeichnis wechseln** auswählen.

Wenn Sie eine neue Version Ihres Angebots veröffentlichen und ein aktualisiertes Manifest erstellen müssen, wählen Sie **+ Neues Manifest** aus. Achten Sie darauf, dass die Versionsnummer einen höheren Wert als die vorherige Manifestversion aufweist.

### <a name="authorizations"></a>Authorizations

Durch Autorisierungen werden in Ihrem Verwaltungsmandanten die Entitäten definiert, die auf Ressourcen und Abonnements für Kunden zugreifen können, die den Plan kaufen. Jeder dieser Entitäten wird eine integrierte Rolle zugewiesen, mit der bestimmte Zugriffsebenen gewährt werden.

Sie können für jeden Plan bis zu 20 Autorisierungen erstellen.

> [!TIP]
> In den meisten Fällen sollten Sie Rollen einer Azure AD-Benutzergruppe oder einem -Dienstprinzipal zuweisen, anstatt einer Reihe einzelner Benutzerkonten. Auf diese Weise können Sie den Zugriff für einzelne Benutzer hinzufügen oder entfernen, ohne den Plan aktualisieren und erneut veröffentlichen zu müssen, wenn sich ihre Zugriffsanforderungen ändern. Wenn Sie Azure AD-Gruppen Rollen zuweisen, sollte der [Gruppentyp auf „Sicherheit“ und nicht „Office 365“ festgelegt werden](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Zusätzliche Empfehlungen finden Sie unter [Mandanten, Rollen und Benutzer in Azure Lighthouse-Szenarien](../lighthouse/concepts/tenants-users-roles.md).

Geben Sie für jede „Autorisierung“ die folgenden Informationen an. Sie können dann so oft wie nötig **+ Autorisierung hinzufügen** auswählen, um weitere Benutzer und Rollendefinitionen hinzuzufügen.

* **AAD-Objekt-ID**: Der Azure AD-Bezeichner eines Benutzers, einer Benutzergruppe oder Anwendung, denen bestimmte Berechtigungen (wie in der Rollendefinition festgelegt) für die Ressourcen Ihrer Kunden gewährt werden.
* **AAD-Objektanzeigename**: Ein Anzeigename, der dem Kunden helfen soll, den Zweck dieser Autorisierung zu verstehen. Dem Kunde wird dieser Name beim Delegieren von Ressourcen angezeigt.
* **Rollendefinition**: Wählen Sie eine der verfügbaren integrierten Azure AD-Rollen aus der Liste aus. Diese Rolle legt die Berechtigungen fest, über die der Benutzer im Feld **Prinzipal-ID** für Ressourcen Ihrer Kunden verfügt. Beschreibungen dieser Rollen finden Sie unter [Integrierte Rollen](../role-based-access-control/built-in-roles.md) und [Rollenunterstützung für Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> Wenn Azure geeignete neue integrierte Rollen hinzugefügt werden, sind diese hier verfügbar. Es kann jedoch zu Verzögerungen kommen, bevor sie angezeigt werden.

* **Zuweisbare Rollen**: Diese Option wird nur angezeigt, wenn Sie in der **Rollendefinition** für diese Autorisierung „Benutzerzugriffsadministrator“ ausgewählt haben. Wenn dies der Fall ist, müssen Sie hier mindestens eine zuweisbare Rollen hinzufügen. Der Benutzer im Feld **Objekt-ID von Azure AD** kann diese Rollen verwalteten Identitäten zuweisen. Dies ist erforderlich, um [Richtlinien bereitzustellen, die korrigiert werden können](../lighthouse/how-to/deploy-policy-remediation.md). Für diesen Benutzer gelten keine anderen Berechtigungen, die normalerweise der Rolle „Benutzerzugriffsadministrator“ zugeordnet sind.

> [!TIP]
> Um sicherzustellen, dass Sie den [Zugriff auf eine Delegierung im Bedarfsfall entfernen](../lighthouse/how-to/remove-delegation.md) können, schließen Sie eine **Autorisierung** mit der auf [Rolle zum Löschen der Registrierungszuweisung für verwaltete Dienste](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) festgelegten **Rollendefinition** ein. Wenn diese Rolle nicht zugewiesen wird, können delegierte Ressourcen nur durch einen Benutzer im Kundenmandanten entfernt werden.

Nachdem Sie alle Abschnitte für Ihren Plan bearbeitet haben, können Sie **+ Neuen Plan erstellen** auswählen, um zusätzliche Pläne zu erstellen. Wählen Sie anschließend **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="next-steps"></a>Nächste Schritte

* [Überprüfen und veröffentlichen](review-publish-offer.md)
