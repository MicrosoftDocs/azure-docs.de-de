---
title: Verwenden von Zugriffsüberprüfungen zum Verwalten von Benutzern, die aus Richtlinien für den bedingten Zugriff ausgeschlossen sind – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Active Directory-Zugriffsüberprüfungen (Azure AD-Zugriffsüberprüfungen) Benutzer verwalten, die aus Richtlinien für den bedingten Zugriff ausgeschlossen wurden.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4169b15304afe1ecc4af9c5354798b29ad9dba38
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571357"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Verwenden von Azure AD-Zugriffsüberprüfungen zum Verwalten von Benutzern, die aus Richtlinien für den bedingten Zugriff ausgeschlossen sind

Im Idealfall halten sich alle Benutzer an die Zugriffsrichtlinien, um den Zugriff auf die Ressourcen Ihrer Organisation zu schützen. Es kann aber auch Geschäftsszenarien geben, in denen Ausnahmen erforderlich sind. In diesem Artikel werden einige Beispiele beschrieben, für die ggf. der Ausschluss von Benutzern erforderlich ist. Außerdem erfahren Sie, wie Sie als IT-Administrator diese Aufgabe verwalten, das Übersehen von Richtlinienausnahmen vermeiden und gegenüber Prüfern nachweisen können, dass diese Ausnahmen regelmäßig überprüft werden, indem Azure AD-Zugriffsüberprüfungen (Azure Active Directory) durchgeführt werden.

> [!NOTE]
> Eine gültige Azure AD Premium P2-, kostenpflichtige Enterprise Mobility + Security E5- oder Testlizenz ist erforderlich, um Azure AD-Zugriffsüberprüfungen verwenden zu können. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Welche Gründe gibt es, Benutzer von Richtlinien auszuschließen?

Als IT-Administrator nutzen Sie ggf. den [bedingten Azure AD-Zugriff](../conditional-access/overview.md), um für Benutzer die Durchführung einer mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) oder die Anmeldung über ein vertrauenswürdiges Netzwerk oder Gerät zu erzwingen. Während der Bereitstellungsplanung ermitteln Sie, dass einige dieser Anforderungen nicht von allen Benutzern erfüllt werden können. Beispielsweise kann es Benutzer an einem Remotestandort geben, der nicht Teil Ihres internen Netzwerks ist, oder eine leitende Person nutzt ein altes Telefon, das nicht unterstützt wird. Da es für das Unternehmen erforderlich ist, dass sich diese Benutzer anmelden und Ihre Arbeit erledigen können, werden sie aus den Richtlinien für den bedingten Zugriff ausgeschlossen.

Ein weiteres Beispiel: Unter Umständen nutzen Sie im Rahmen des bedingten Zugriffs [benannte Orte](../conditional-access/location-condition.md), um eine Reihe von Ländern und Regionen zu konfigurieren, für die Sie verhindern möchten, dass Benutzer auf ihren Mandanten zugreifen.

![Benannte Orte](./media/conditional-access-exclusion/named-locations.png)

In einigen Fällen kann es aber sein, dass Benutzer einen guten Grund für die Anmeldung aus diesen blockierten Ländern/Regionen haben. Beispielsweise können Benutzer aus geschäftlichen oder persönlichen Gründen unterwegs sein. In diesem Beispiel kann die Richtlinie für bedingten Zugriff, mit der diese Länder/Regionen blockiert werden, über eine dedizierte Cloudsicherheitsgruppe für die Benutzer verfügen, die aus der Richtlinie ausgeschlossen sind. Benutzer, die unterwegs Zugriff benötigen, können sich der Gruppe durch das [Einrichten von Azure Active Directory für die Self-Service-Gruppenverwaltung](../users-groups-roles/groups-self-service-management.md) selbst hinzufügen.

In einem weiteren Beispiel wird ggf. eine Richtlinie für bedingten Zugriff verwendet, mit der die [Legacyauthentifizierung für den Großteil Ihrer Benutzer blockiert wird](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). Microsoft empfiehlt dringend, dass Sie die Nutzung von Legacyprotokollen in Ihrem Mandanten blockieren, um Ihren Sicherheitsstatus zu verbessern. Es kann auch sein, dass Sie über einige Benutzer verfügen, die die Methoden der Legacyauthentifizierung unbedingt benötigen, um über Office 2010 oder IMAP/SMTP/POP-basierte Clients auf Ihre Ressourcen zuzugreifen. In diesem Fall können Sie diese Benutzer aus der Richtlinie ausschließen, mit der die Methoden der Legacyauthentifizierung blockiert werden.

## <a name="why-are-exclusions-challenging"></a>Warum stellen Ausschlüsse eine Herausforderung dar?

In Azure AD können Sie den Bereich für eine Richtlinie für bedingten Zugriff auf eine Gruppe von Benutzern festlegen. Sie können auch einige dieser Benutzer ausschließen, indem Sie Azure AD-Rollen, einzelne Benutzer oder Gäste von Benutzern auswählen. Es ist wichtig, Folgendes zu beachten: Wenn diese Ausschlüsse konfiguriert sind, kann die Absicht der Richtlinie für die betreffenden Benutzer nicht erzwungen werden. Falls diese Ausschlüsse entweder als Liste mit einzelnen Benutzern oder über eine lokale Legacysicherheitsgruppe konfiguriert werden, werden die Sichtbarkeit dieser Ausschlussliste (Benutzer wissen davon ggf. nichts) und die Kontrolle des IT-Administrators darüber (Benutzer können der Sicherheitsgruppe beitreten, um die Richtlinie zu umgehen) eingeschränkt. Außerdem kann es sein, dass Benutzer, die ursprünglich für den Ausschluss qualifiziert waren, diesen nicht mehr benötigen oder dafür nicht mehr qualifiziert sind.

Zu Beginn eines Ausschlusses ist die Liste mit den Benutzern, die die Richtlinie umgehen können, erst einmal sehr kurz. Im Laufe der Zeit werden immer mehr Benutzer ausgeschlossen, und die Liste wird umfangreicher. An einem bestimmten Punkt muss für die Liste eine Überprüfung durchgeführt und bestätigt werden, dass die einzelnen Benutzer weiterhin ausgeschlossen sein sollen. Die Verwaltung der Liste aus technischer Sicht kann relativ einfach sein, aber wer trifft die geschäftlichen Entscheidungen, und wie stellen Sie sicher, dass alles überwachbar ist?

Wenn Sie den Ausschluss für die Richtlinie für bedingten Zugriff aber über eine Azure AD-Gruppe konfigurieren, können Sie Zugriffsüberprüfungen als ausgleichendes Kontrollelement nutzen, um die Sichtbarkeit zu erhöhen und die Anzahl von Benutzern mit Ausnahmen zu reduzieren.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Erstellen einer Ausschlussgruppe in einer Richtlinie für bedingten Zugriff

Führen Sie diese Schritte aus, um eine neue Azure AD-Gruppe und eine Richtlinie für bedingten Zugriff zu erstellen, die für diese Gruppe nicht gilt.

### <a name="create-an-exclusion-group"></a>Erstellen einer Ausschlussgruppe

1. Melden Sie sich beim Azure-Portal an.

1. Klicken Sie im linken Navigationsbereich auf **Azure Active Directory** und dann auf **Gruppen**.

1. Klicken Sie im oberen Menü auf **Neue Gruppe**, um den Gruppenbereich zu öffnen.

1. Wählen Sie in der Liste **Gruppentyp** die Option **Sicherheit**. Geben Sie einen Namen und eine Beschreibung an.

1. Stellen Sie sicher, dass Sie den Typ **Mitgliedschaft** auf **Zugewiesen** festlegen.

1. Wählen Sie die Benutzer aus, die Teil dieser Ausschlussgruppe sein sollen, und klicken Sie anschließend auf **Erstellen**.

    ![Bereich „Neue Gruppe“](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Erstellen einer Richtlinie für bedingten Zugriff, für die die Gruppe ausgeschlossen ist

Sie können jetzt eine Richtlinie für bedingten Zugriff erstellen, für die diese Ausschlussgruppe verwendet wird.

1. Klicken Sie im linken Navigationsbereich auf **Azure Active Directory** und dann auf **Bedingter Zugriff**, um das Blatt **Richtlinien** zu öffnen.

1. Klicken Sie auf **Neue Richtlinie**, um den Bereich **Neu** zu öffnen.

1. Geben Sie einen Namen an.

1. Klicken Sie unter „Zuweisungen“ auf **Benutzer und Gruppen**.

1. Wählen Sie auf der Registerkarte **Einschließen** die Option **Alle Benutzer**.

1. Fügen Sie auf der Registerkarte **Ausschließen** unter **Benutzer und Gruppen** ein Häkchen hinzu, und klicken Sie dann auf **Ausgeschlossene Benutzer auswählen**.

1. Wählen Sie die Ausschlussgruppe aus, die Sie erstellt haben.

    > [!NOTE]
    > Die empfohlene bewährte Methode besteht darin, beim Testen mindestens ein Administratorkonto aus der Richtlinie auszuschließen, um sicherzustellen, dass Sie nicht aus Ihrem Mandanten ausgesperrt werden.

1. Fahren Sie mit dem Einrichten der Richtlinie für bedingten Zugriff basierend auf den Anforderungen Ihrer Organisation fort.

    ![Auswählen von ausgeschlossenen Benutzern](./media/conditional-access-exclusion/select-excluded-users.png)

Im Folgenden werden zwei Beispiele beschrieben, in denen Sie Zugriffsüberprüfungen nutzen können, um Ausschlüsse in Richtlinien für bedingten Zugriff zu verwalten.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Beispiel 1: Zugriffsüberprüfung für Benutzer, die aus blockierten Ländern/Regionen zugreifen

Angenommen, Sie verfügen über eine Richtlinie für bedingten Zugriff, die den Zugriff aus bestimmten Ländern/Regionen blockiert. Sie enthält eine Gruppe, die von der Richtlinie ausgeschlossen ist. Hier ist eine empfohlene Zugriffsüberprüfung angegeben, mit der die Mitglieder der Gruppe überprüft werden.

> [!NOTE]
> Für die Erstellung von Zugriffsüberprüfungen ist die Rolle „Globaler Administrator“ oder „Benutzeradministrator“ erforderlich.

1. Die Überprüfung wird wöchentlich durchgeführt.

2. Sie endet nie, um sicherzustellen, dass Sie diese Ausschlussgruppe immer auf dem aktuellen Stand halten.

3. Alle Mitglieder dieser Gruppe werden in die Überprüfung einbezogen.

4. Jeder Benutzer muss selbst nachweisen, dass er weiterhin Zugriff aus diesen blockierten Ländern/Regionen benötigt und deshalb ein Mitglied der Gruppe bleiben muss.

5. Wenn der Benutzer nicht auf die Überprüfungsanforderung reagiert, wird er automatisch aus der Gruppe entfernt und kann nicht mehr auf den Mandanten zugreifen, wenn er in diesen Ländern/Regionen unterwegs ist.

6. Aktivieren Sie E-Mail-Benachrichtigungen, damit die Benutzer über den Beginn und den Abschluss der Zugriffsüberprüfung informiert sind.

    ![Erstellen einer Zugriffsüberprüfung](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Beispiel 2: Zugriffsüberprüfung für Benutzer, die per Legacyauthentifizierung zugreifen

Angenommen, Sie verfügen über eine Richtlinie für bedingten Zugriff, mit der der Zugriff für Benutzer mit Legacyauthentifizierung und älteren Clientversionen blockiert wird. Sie enthält eine Gruppe, die von der Richtlinie ausgeschlossen ist. Hier ist eine empfohlene Zugriffsüberprüfung angegeben, mit der die Mitglieder der Gruppe überprüft werden.

1. Bei dieser Überprüfung muss es sich um eine wiederkehrende Überprüfung handeln.

2. Alle Mitglieder der Gruppe müssen überprüft werden.

3. Sie kann so konfiguriert werden, dass die „Besitzer“ von Geschäftseinheiten als ausgewählte Prüfer aufgeführt werden.

4. Verwenden Sie die automatische Anwendung der Ergebnisse, und entfernen Sie Benutzer, die noch nicht genehmigt wurden, damit diese weiterhin die Methoden für die Legacyauthentifizierung nutzen können.

5. Es kann vorteilhaft sein, die Empfehlungen zu aktivieren, damit für die Prüfer großer Gruppen das Treffen von Entscheidungen vereinfacht wird.

6. Aktivieren Sie E-Mail-Benachrichtigungen, damit die Benutzer über den Beginn und den Abschluss der Zugriffsüberprüfung informiert sind.

    ![Erstellen einer Zugriffsüberprüfung](./media/conditional-access-exclusion/create-access-review-2.png)

**Profi-Tipp**: Wenn Sie über viele Ausschlussgruppen verfügen und deshalb mehrere Zugriffsüberprüfungen erstellen müssen, können Sie am Microsoft Graph Beta-Endpunkt jetzt eine API nutzen, um die Erstellung und Verwaltung programmgesteuert durchzuführen. Informationen zu den ersten Schritten finden Sie unter [Azure AD access reviews API reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) (Azure AD-Zugriffsüberprüfungen – API-Referenz) und [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Beispiel für das Abrufen von Azure AD-Zugriffsüberprüfungen mit Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Zugreifen auf Überprüfungsergebnisse und Überwachungsprotokolle

Nachdem Sie nun alles eingerichtet haben (Gruppe, Richtlinie für bedingten Zugriff und Zugriffsüberprüfungen), können Sie die Ergebnisse der Überprüfungen überwachen und nachverfolgen.

1. Öffnen Sie im Azure-Portal das Blatt **Zugriffsüberprüfungen**.

1. Öffnen Sie das Steuerelement und das Programm, das Sie für die Verwaltung der Ausschlussgruppe erstellt haben.

1. Klicken Sie auf **Ergebnisse**, um anzuzeigen, für wen der Verbleib auf der Liste genehmigt und wer entfernt wurde.

    ![Ergebnisse der Zugriffsüberprüfungen](./media/conditional-access-exclusion/access-reviews-results.png)

1. Klicken Sie anschließend auf **Überwachungsprotokolle**, um die Aktionen anzuzeigen, die während der Überprüfung durchgeführt wurden.

    ![Überwachungsprotokolle von Zugriffsüberprüfungen](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Als IT-Administrator wissen Sie, dass sich die Verwaltung von Ausschlussgruppen für Ihre Richtlinien manchmal nicht verhindern lässt. Mit Azure AD-Zugriffsüberprüfungen können die Wartung dieser Gruppen, die regelmäßigen Überprüfungen durch den Besitzer der Geschäftseinheit oder die Benutzer selbst und die Überprüfung dieser Änderungen aber deutlich vereinfacht werden.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)
- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)
