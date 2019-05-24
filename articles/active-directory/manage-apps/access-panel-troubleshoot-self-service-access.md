---
title: Probleme beim Self-Service-Anwendungszugriff | Microsoft-Dokumentation
description: Beheben von Problemen in Zusammenhang mit dem Self-Service-Anwendungszugriff
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784280"
---
# <a name="problem-using-self-service-application-access"></a>Probleme beim Self-Service-Anwendungszugriff

Der Self-Service-Anwendungszugriff bietet die Möglichkeit, dass Benutzer Anwendungen selbst ermitteln können und die entsprechende Geschäftseinheit den Zugriff auf diese Anwendungen optional genehmigen kann. Sie können der Geschäftseinheit ermöglichen, die Anmeldeinformationen zu verwalten, die Benutzern zugewiesen wurden, damit diese über ihren Zugriffsbereich direkt auf Anwendungen mit einmaligem Anmelden per Kennwort zugreifen können.

Damit Ihre Benutzer über ihren Zugriffsbereich Anwendungen selbst ermitteln können, müssen Sie den **Self-Service-Anwendungszugriff** auf alle Anwendungen aktivieren, die Benutzer selbst ermitteln und für die sie den Zugriff anfordern können sollen.

## <a name="general-issues-to-check-first"></a>Allgemeine Probleme, die zuerst überprüft werden sollten

-   Stellen Sie sicher, dass der Self-Service-Anwendungszugriff ordnungsgemäß konfiguriert ist. Informationen hierzu finden Sie unter „Konfigurieren des Self-Service-Anwendungszugriffs“.

-   Stellen Sie sicher, dass der Benutzer oder die Gruppe für die Anforderung des Self-Service-Anwendungszugriffs aktiviert wurde.

-   Stellen Sie sicher, dass die Benutzer die richtige Stelle für den Self-Service-Anwendungszugriff verwenden. Benutzer können in ihrem [Anwendungszugriffsbereich](https://myapps.microsoft.com/) auf die Schaltfläche **+Hinzufügen** klicken und die Apps suchen, für die Sie den Self-Service-Zugriff aktiviert haben.

-   Wenn der Self-Service-Anwendungszugriff erst kürzlich konfiguriert wurde, sollte sich der Benutzer vom Zugriffsbereich abmelden und nach einigen Minuten wieder anmelden, um festzustellen, ob die Änderungen am Self-Service-Zugriff angezeigt werden.

## <a name="how-to-configure-self-service-application-access"></a>Konfigurieren des Self-Service-Anwendungszugriffs

Führen Sie die folgenden Schritte aus, um den Self-Service-Anwendungszugriff auf eine Anwendung zu aktivieren:

1. Melden Sie sich beim [**Azure-Portal**](https://portal.azure.com/) als **Globaler Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie in der Liste die Anwendung aus, für die Sie den Self-Service-Zugriff aktivieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Self-Service**.

8. Um den Self-Service-Anwendungszugriff auf die Anwendung zu aktivieren, legen Sie **Benutzern das Anfordern des Zugriffs auf diese Anwendung erlauben?** auf **Ja** fest.

9. Um dann die Gruppe auszuwählen, zu der Benutzer, die Zugriff auf diese Anwendung anfordern, hinzugefügt werden sollen, klicken Sie auf das Auswahlfeld neben **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?**, und wählen Sie eine Gruppe aus.

10. **Optional**: Wenn eine Genehmigung des Unternehmens erforderlich sein soll, damit Benutzer Zugriff erhalten, legen Sie **Genehmigung anfordern, bevor Zugriff auf diese Anwendung gewährt wird?** auf **Ja** fest.

11. **Optional: nur für Anwendungen mit einmaligem Anmelden per Kennwort:** Wenn Sie möchten, dass die genehmigenden Personen des Unternehmens die für genehmigte Benutzer an die Anwendung gesendeten Kennwörter angeben können, legen Sie **Genehmigenden Personen das Festlegen von Benutzerkennwörtern für diese Anwendung gestatten?** auf **Ja** fest.

12. **Optional**: Um die genehmigenden Personen des Unternehmens anzugeben, die den Zugriff auf die Anwendung genehmigen können, klicken Sie auf die Auswahl neben **Wer darf den Zugriff auf diese Anwendung genehmigen?**. Hier können Sie bis zu 10 genehmigende Personen auswählen.

    >[!NOTE]
    > Gruppen werden nicht unterstützt.
    >
    >

13. **Optional**: Wenn Sie **für Anwendungen, die Rollen verfügbar machen**, den für den Self-Service genehmigten Benutzern eine Rolle zuweisen möchten, klicken Sie auf die Auswahl neben **Welcher Rolle sollen Benutzer in dieser Anwendung zugewiesen werden?**, und wählen Sie die Rolle aus, die den Benutzern zugewiesen werden soll.

14. Klicken Sie abschließend oben auf dem Blatt auf die Schaltfläche **Speichern**.

Nachdem Sie die Self-Service-Anwendungskonfiguration abgeschlossen haben, können Benutzer in ihrem [Anwendungszugriffsbereich](https://myapps.microsoft.com/) auf die Schaltfläche **+Hinzufügen** klicken und die Apps suchen, für die Sie den Self-Service-Zugriff aktiviert haben. Den genehmigenden Personen des Unternehmens wird im [Zugriffsbereich](https://myapps.microsoft.com/) zudem eine Benachrichtigung angezeigt. Sie können festlegen, dass sie in einer E-Mail darüber benachrichtigt werden, dass ein Benutzer den Zugriff auf eine Anwendung angefordert hat, der zu genehmigen ist. 

Diese Genehmigungen unterstützen nur Workflows mit einzelnen Genehmigungen. Das bedeutet, dass bei der Angabe mehrerer genehmigender Personen jede einzelne genehmigende Person den Zugriff auf die Anwendung genehmigen kann.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Wenn das Problem mit diesen Problembehandlungsschritten nicht behoben wird 

Öffnen Sie ein Supportticket mit den folgenden Informationen, sofern verfügbar:

-   Fehlerkorrelations-ID

-   UPN (E-Mail-Adresse des Benutzers)

-   Mandanten-ID

-   Browsertyp

-   Zeitzone und Uhrzeit/Zeitraum des Auftretens des Fehlers

-   Fiddler-Ablaufverfolgungen

## <a name="next-steps"></a>Nächste Schritte
[Einrichten von Azure Active Directory zur Self-Service-Gruppenverwaltung](../users-groups-roles/groups-self-service-management.md)
