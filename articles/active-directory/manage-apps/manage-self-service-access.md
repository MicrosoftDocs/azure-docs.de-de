---
title: Konfigurieren der Self-Service-Anwendungszuweisung | Microsoft-Dokumentation
description: Aktivieren des Self-Service-Anwendungszugriffs, um Benutzern die Suche ihrer eigenen Anwendungen zu ermöglichen
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfa7530617830de99d752edcf4545300525ddbe8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997459"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Konfigurieren der Self-Service-Anwendungszuweisung

Damit Ihre Benutzer Anwendungen über „Meine Apps“ selbst ermitteln können, müssen Sie den **Self-Service-Anwendungszugriff** auf alle Anwendungen aktivieren, die Benutzer selbst ermitteln und für die sie den Zugriff anfordern können sollen. Diese Funktion ist für Anwendungen verfügbar, die aus dem [Azure AD-Katalog](./add-application-portal.md), [Azure AD-Anwendungsproxy](./application-proxy.md) oder über [Benutzer- oder Administratoreinwilligung](../develop/application-consent-experience.md) hinzugefügt wurden. 

Diese Funktion ermöglicht Ihnen als IT-Abteilung, Zeit und Geld zu sparen, und empfiehlt sich unbedingt als Bestandteil einer modernen Anwendungsbereitstellung mit Azure Active Directory.

Mit dieser Funktion können Sie die folgenden Schritte ausführen:

-   Sie können es Benutzern ermöglichen, Anwendungen über den [Zugriffsbereich „Meine Apps“](https://myapps.microsoft.com/) ohne Zutun der IT-Abteilung selbst zu ermitteln.

-   Diese Benutzer einer vorkonfigurierten Gruppe hinzufügen, sodass Sie sehen können, welcher Benutzer den Zugriff angefordert hat, sowie den Zugriff aufheben und die den Benutzern zugewiesenen Rollen verwalten können

-   Optional festlegen, dass eine genehmigende Person des Unternehmens Anforderungen für den Anwendungszugriff genehmigt, sodass dies nicht durch die IT-Abteilung erfolgen muss

-   Optional bis zu 10 Personen konfigurieren, die den Zugriff auf eine Anwendung genehmigen können

-   Sie können optional angeben, dass eine genehmigende Person aus einer Geschäftsabteilung direkt über den Bereich [Meine Apps](https://myapps.microsoft.com/) die Kennwörter festlegen kann, die Benutzer zum Anmelden bei der Anwendung verwenden können.

-   Optional Benutzer mit Self-Service-Zuweisung automatisch direkt einer Anwendungsrolle zuweisen

> [!NOTE]
> Eine Azure Active Directory Premium-Lizenz (P1 oder P2) ist erforderlich, damit Benutzer den Beitritt einer Self-Service-App anfordern und Besitzer Anforderungen genehmigen oder verweigern können. Ohne eine Azure Active Directory Premium-Lizenz können Benutzer keine Self-Service-Apps hinzufügen.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivieren des Self-Service-Anwendungszugriffs, um Benutzern die Suche ihrer eigenen Anwendungen zu ermöglichen

Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Für SSO-Anwendungen mit Kennwort können Sie auch der Geschäftseinheit ermöglichen, die Anmeldeinformationen zu verwalten, die diesen Benutzern in ihrem Zugriffsbereich „Meine Apps“ zugewiesen wurden.

Führen Sie die folgenden Schritte aus, um den Self-Service-Anwendungszugriff auf eine Anwendung zu aktivieren:

1. Melden Sie sich als globaler Administrator am [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Azure Active Directory** aus. Wählen Sie im linken Navigationsmenü **Unternehmensanwendungen** aus.

3. Wählen Sie die Anwendung in der Liste aus. Wenn die Anwendung nicht angezeigt wird, beginnen Sie mit der Eingabe ihres Namens in das Suchfeld. Oder verwenden Sie die Filtersteuerelemente, um den Anwendungstyp, Status oder die Sichtbarkeit auszuwählen, und wählen Sie dann **Übernehmen** aus.

4. Wählen Sie im Navigationsmenü auf der linken Seite **Self-Service** aus.

5. Um den Self-Service-Anwendungszugriff auf die Anwendung zu aktivieren, legen Sie **Benutzern das Anfordern des Zugriffs auf diese Anwendung erlauben?** auf **Ja** fest.

6. Klicken Sie neben **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?** auf **Gruppe auswählen**. Wählen Sie eine Gruppe, und klicken Sie auf **Auswählen**. Wenn die Anforderung eines Benutzers genehmigt wird, wird er dieser Gruppe hinzugefügt. Wenn Sie die Mitgliedschaft dieser Gruppe prüfen, können Sie erkennen, wem der Zugriff auf die Anwendung über einen Self-Service-Zugriff gewährt wurde.
  
    > [!NOTE]
    > Diese Einstellung unterstützt keine Gruppen, die lokal synchronisiert werden.

7. **Optional:** Um eine Genehmigung des Unternehmens anzufordern, ehe Benutzer Zugriff erhalten, legen Sie **Genehmigung anfordern, bevor Zugriff auf diese Anwendung gewährt wird?** auf **Ja** fest.

8. **Optional: nur für Anwendungen mit einmaligem Anmelden per Kennwort:** Wenn Sie möchten, dass die genehmigenden Personen des Unternehmens die für genehmigte Benutzer an die Anwendung gesendeten Kennwörter angeben können, legen Sie **Genehmigenden Personen das Festlegen von Benutzerkennwörtern für diese Anwendung gestatten?** auf **Ja** fest.

9. **Optional:** Um die genehmigenden Personen des Unternehmens anzugeben, die den Zugriff auf die Anwendung genehmigen können, klicken Sie auf neben **Wer darf den Zugriff auf diese Anwendung genehmigen?** auf **Genehmigende Person auswählen**. Hier können Sie bis zu 10 genehmigende Personen des Unternehmens auswählen. Klicken Sie dann auf **Auswählen**.

    >[!NOTE]
    >Gruppen werden nicht unterstützt. Sie können bis zu 10 genehmigende Personen des Unternehmens auswählen. Wenn Sie mehrere genehmigende Personen angeben, kann jede einzelne genehmigende Person eine Zugriffsanforderung genehmigen.

10. **Optional:** Wenn Sie **für Anwendungen, die Rollen verfügbar machen**, den für den Self-Service genehmigten Benutzern eine Rolle zuweisen möchten, klicken Sie neben **Welcher Rolle sollen Benutzer in dieser Anwendung zugewiesen werden?** auf **Rolle auswählen**, und wählen Sie dann die Rolle aus, die diesen Benutzern zugewiesen werden soll. Klicken Sie dann auf **Auswählen**.

11. Klicken Sie abschließend oben im Bereich auf die Schaltfläche **Speichern**.

Nachdem Sie die Self-Service-Anwendungskonfiguration abgeschlossen haben, können Benutzer in ihrem Bereich [Meine Apps](https://myapps.microsoft.com/) auf die Schaltfläche **Self-Service-Apps hinzufügen** klicken und die Apps suchen, die für den Self-Service-Zugriff aktiviert sind. Genehmigenden Personen aus einer Geschäftsabteilung wird in [Meine Apps](https://myapps.microsoft.com/) zudem eine Benachrichtigung angezeigt. Sie können festlegen, dass sie in einer E-Mail darüber benachrichtigt werden, dass ein Benutzer den Zugriff auf eine Anwendung angefordert hat, der zu genehmigen ist.

## <a name="next-steps"></a>Nächste Schritte
[Einrichten von Azure Active Directory zur Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md)