---
title: 'Bedingter Zugriff: Erzwingen von Nutzungsbedingungen – Azure Active Directory'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit dem bedingten Zugriff mit Azure Active Directory erzwingen können, dass Ihre Nutzungsbedingungen akzeptiert werden, bevor der Zugriff auf ausgewählte Cloud-Apps gewährt wird.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077896"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Schnellstart: Fordern der Annahme von Nutzungsbedingungen vor dem Zugreifen auf Cloud-Apps

Bevor Sie den Zugriff auf bestimmte Cloud-Apps in Ihrer Umgebung gewähren, möchten Sie eventuell, dass Benutzer Ihre Nutzungsbedingungen akzeptieren. Der bedingte Zugriff mit Azure Active Directory (Azure AD) bietet Folgendes:

- Eine einfache Methode zum Konfigurieren der Nutzungsbedingungen
- Die Option zum Fordern der Annahme Ihrer Nutzungsbedingungen über eine Richtlinie für bedingten Zugriff  

Dieser Schnellstart veranschaulicht das Konfigurieren einer [Azure AD-Richtlinie für bedingten Zugriff](./overview.md), durch die für eine ausgewählte Cloud-App in Ihrer Umgebung erforderlich ist, dass Nutzungsbedingungen akzeptiert werden.

:::image type="content" source="./media/require-tou/5555.png" alt-text="Screenshot des Azure-Portals. Ein Bereich, in dem eine Richtlinie mit dem Namen „Nutzungsbedingungen für Isabella erforderlich“ angezeigt wird" border="false":::

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung des Szenarios im Rahmen dieses Schnellstarts benötigen Sie Folgendes:

- **Zugriff auf eine Azure AD Premium-Edition** : Der bedingte Zugriff von Azure AD ist eine Funktion, die in Azure AD Premium enthalten ist.
- **Ein Testkonto unter dem Namen Isabella Simonsen** : Wenn Sie nicht wissen, wie Sie ein Testkonto erstellen, lesen Sie [Hinzufügen von cloudbasierten Benutzern](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Testen Ihrer Anmeldung

Das Ziel dieses Schritts besteht darin, den Anmeldevorgang ohne eine Richtlinie für bedingten Zugriff zu überprüfen.

**So testen Sie Ihre Anmeldung**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Isabella Simonsen an.
1. Melden Sie sich ab.

## <a name="create-your-terms-of-use"></a>Erstellen Ihrer Nutzungsbedingungen

Dieser Abschnitt enthält die Schritte, die Sie zum Erstellen von Beispielnutzungsbedingungen ausführen müssen. Wenn Sie Nutzungsbedingungen erstellen, wählen Sie einen Wert für **Mit Richtlinienvorlagen für bedingten Zugriff erzwingen** aus. Nach dem Auswählen von **Benutzerdefinierte Richtlinie** wird das Dialogfeld zum Erstellen einer neuen Richtlinie für bedingten Zugriff geöffnet, sobald Ihre Nutzungsbedingungen erstellt wurden.

**So erstellen Sie Ihre Nutzungsbedingungen**

1. Erstellen Sie in Microsoft Word ein neues Dokument.
1. Geben Sie **Meine Nutzungsbedingungen** ein, und speichern Sie das Dokument unter **mytou.pdf** auf Ihrem Computer.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Klicken Sie im Azure-Portal auf der linken Navigationsleiste auf **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Klicken Sie auf der Seite **Azure Active Directory** im Abschnitt **Sicherheit** auf **Bedingter Zugriff**.

   ![Bedingter Zugriff](./media/require-tou/03.png)

1. Klicken Sie im Bereich **Verwalten** auf **Nutzungsbedingungen**.

   :::image type="content" source="./media/require-tou/04.png" alt-text="Screenshot: Abschnitt „Verwalten“ der Seite „Azure Active Directory“. Das Element „Nutzungsbedingungen“ ist hervorgehoben." border="false":::

1. Klicken Sie oben im Menü auf **Neue Bedingungen**.

   :::image type="content" source="./media/require-tou/05.png" alt-text="Screenshot: Menü auf der Seite „Azure Active Directory“. Das Element „Neue Bedingungen“ ist hervorgehoben." border="false":::

1. Gehen Sie auf der Seite **Neue Nutzungsbedingungen** folgendermaßen vor:

   :::image type="content" source="./media/require-tou/112.png" alt-text="Screenshot: Seite „Neue Nutzungsbedingungen“, auf der die Felder für Name, Anzeigename, Dokument, Sprache, bedingten Zugriff und die Umschaltfläche zum Erweitern der Nutzungsbedingungen hervorgehoben sind" border="false":::

   1. Geben Sie im Textfeld **Name** den Text **Meine Nutzungsbedingungen** ein.
   1. Geben Sie im Textfeld **Anzeigename** den Text **Meine Nutzungsbedingungen** ein.
   1. Laden Sie Ihre Nutzungsbedingungen als PDF-Datei hoch.
   1. Wählen Sie unter **Sprache** die Option **Englisch** aus.
   1. Wählen Sie für **Benutzer müssen die Nutzungsbedingungen erweitern** die Option **Ein** aus.
   1. Wählen Sie für **Mit Richtlinienvorlagen für bedingten Zugriff erzwingen** die Option **Benutzerdefinierte Richtlinie** aus.
   1. Klicken Sie auf **Erstellen**.

## <a name="create-your-conditional-access-policy"></a>Erstellen Ihrer Richtlinie für bedingten Zugriff

In diesem Abschnitt wird gezeigt, wie Sie die erforderliche Richtlinie für bedingten Zugriff erstellen. Für das Szenario in diesem Schnellstart wird Folgendes verwendet:

- Azure-Portal als Platzhalter für eine Cloud-App, für die Ihre Nutzungsbedingungen akzeptiert werden müssen 
- Beispielbenutzer zum Testen der Richtlinie für bedingten Zugriff  

Legen Sie in Ihrer Richtlinie Folgendes fest:

| Einstellung | Wert |
| --- | --- |
| Benutzer und Gruppen | Isabella Simonsen |
| Cloud-Apps | Microsoft Azure Management |
| Gewähren von Zugriff | Meine Nutzungsbedingungen |

:::image type="content" source="./media/require-tou/1234.png" alt-text="Screenshot: Bereich im Azure-Portal, in dem eine Richtlinie definiert wird. Pfeile geben an, dass die Richtlinie den Zugriff auf „Meine Nutzungsbedingungen“ gewährt und einen Benutzer und eine App enthält" border="false":::

**So konfigurieren Sie die Richtlinie für bedingten Zugriff**

1. Geben Sie auf der Seite **Neu** im Textfeld **Name** den Text **Nutzungsbedingungen für Isabella erforderlich** ein.

   ![Name](./media/require-tou/71.png)

1. Klicken Sie im Abschnitt **Zuweisung** auf **Benutzer und Gruppen**.

   :::image type="content" source="./media/require-tou/06.png" alt-text="Screenshot: Abschnitt „Zuweisungen“ im Azure-Portalbereich, in dem eine Richtlinie definiert wird. Das Element „Benutzer und Gruppen“ ist sichtbar, und es ist nichts ausgewählt." border="false":::

1. Gehen Sie auf der Seite **Benutzer und Gruppen** folgendermaßen vor:

   :::image type="content" source="./media/require-tou/24.png" alt-text="Screenshot: Registerkarte „Einschließen“ der Seite „Benutzer und Gruppen“. „Benutzer und Gruppen auswählen“ und „Benutzer und Gruppen“ sind ausgewählt. „Auswählen“ ist hervorgehoben." border="false":::

   1. Klicken Sie auf **Benutzer und Gruppen auswählen** , und wählen Sie dann **Benutzer und Gruppen** aus.
   1. Klicken Sie auf **Auswählen**.
   1. Wählen Sie auf der Seite **Auswählen** den Benutzer **Isabella Simonsen** aus, und klicken Sie dann auf **Auswählen**.
   1. Klicken Sie auf der Seite **Benutzer und Gruppen** auf **Fertig**.
1. Klicken Sie auf **Cloud-Apps**.

   :::image type="content" source="./media/require-tou/08.png" alt-text="Screenshot: Abschnitt „Zuweisungen“ im Azure-Portalbereich, in dem eine Richtlinie definiert wird. Das Element „Cloud-Apps“ ist sichtbar, und es ist nichts ausgewählt." border="false":::

1. Gehen Sie auf der Seite **Cloud-Apps** folgendermaßen vor:

   ![Auswählen der Cloud-Apps](./media/require-tou/26.png)

   1. Klicken Sie auf **Apps auswählen**.
   1. Klicken Sie auf **Auswählen**.
   1. Wählen Sie auf der Seite **Auswählen** die Option **Microsoft Azure Management** aus, und klicken Sie dann auf **Auswählen**.
   1. Klicken Sie auf der Seite **Cloud-Apps** auf **Fertig**.
1. Klicken Sie im Abschnitt **Zugriffssteuerungen** auf **Gewähren**.

   ![Zugriffssteuerung](./media/require-tou/10.png)

1. Gehen Sie auf der Seite **Gewähren** folgendermaßen vor:

   ![Erteilen](./media/require-tou/111.png)

   1. Wählen Sie **Zugriff gewähren** aus.
   1. Wählen Sie **Meine Nutzungsbedingungen** aus.
   1. Klicken Sie auf **Auswählen**.
1. Klicken Sie im Abschnitt **Richtlinie aktivieren** auf **Ein**.

   ![Richtlinie aktivieren](./media/require-tou/18.png)

1. Klicken Sie auf **Erstellen**.

## <a name="evaluate-a-simulated-sign-in"></a>Auswerten einer simulierten Anmeldung

Nachdem Sie nun Ihre Richtlinie für bedingten Zugriff konfiguriert haben, möchten Sie wahrscheinlich wissen, ob sie erwartungsgemäß funktioniert. Verwenden Sie im ersten Schritt das What-If-Richtlinientool des bedingten Zugriffs, um eine Anmeldung für Ihren Testbenutzer zu simulieren. Die Simulation schätzt die Auswirkungen dieser Anmeldungen auf Ihre Richtlinien ab und generiert einen Simulationsbericht.  

Legen Sie zum Initialisieren des **What If** -Richtlinienauswertungstools Folgendes fest:

- Benutzer: **Isabella Simonsen**
- Cloud-App: **Microsoft Azure Management**

Durch Klicken auf **What If** wird ein Simulationsbericht mit folgenden Informationen erstellt:

- **Nutzungsbedingungen für Isabella erforderlich** unter **Anzuwendende Richtlinien**
- **Meine Nutzungsbedingungen** als **Steuerelemente zur Rechteerteilung**.

![What If-Richtlinientool](./media/require-tou/79.png)

**So werten Sie die Richtlinie für bedingten Zugriff aus:**

1. Klicken Sie auf der Seite [Bedingter Zugriff – Richtlinien](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) im Menü am oberen Rand auf **What If**.  

   ![What If](./media/require-tou/14.png)

1. Klicken Sie auf **Benutzer** , wählen Sie **Isabella Simonsen** aus, und klicken Sie dann auf **Auswählen**.

   ![Benutzer](./media/require-tou/15.png)

1. So wählen Sie eine Cloud-App aus

   :::image type="content" source="./media/require-tou/16.png" alt-text="Screenshot: Abschnitt „Cloud-Apps“. Text gibt an, dass eine App ausgewählt ist." border="false":::

   1. Klicken Sie auf **Cloud-Apps**.
   1. Klicken Sie auf der Seite **Cloud-Apps** auf **Apps auswählen**.
   1. Klicken Sie auf **Auswählen**.
   1. Wählen Sie auf der Seite **Auswählen** die Option **Microsoft Azure Management** aus, und klicken Sie dann auf **Auswählen**.
   1. Klicken Sie auf der Seite „Cloud-Apps“ auf **Fertig**.
1. Klicken Sie auf **What If**.

## <a name="test-your-conditional-access-policy"></a>Testen der Richtlinie für bedingten Zugriff

Im vorherigen Abschnitt haben Sie gelernt, wie Sie eine simulierte Anmeldung auswerten. Zusätzlich zu einer Simulation sollten Sie auch Ihre Richtlinie für bedingten Zugriff testen, um sicherzustellen, dass sie wie erwartet funktioniert.

Um Ihre Richtlinie zu testen, versuchen Sie, sich im [Azure-Portal](https://portal.azure.com) mit dem Testkonto **Isabella Simonsen** anzumelden. Es sollte ein Dialogfeld angezeigt werden, in dem Sie Ihre Nutzungsbedingungen akzeptieren müssen.

:::image type="content" source="./media/require-tou/57.png" alt-text="Screenshot: Dialogfeld mit dem Titel „Nutzungsbedingungen für Identitätssicherheit und -schutz“ sowie den Schaltflächen „Ablehnen“, „Akzeptieren“ und „Meine Nutzungsbedingungen“" border="false":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den Testbenutzer, wenn er nicht mehr benötigt wird, und löschen Sie auch die Richtlinie für bedingten Zugriff:

- Wenn Sie nicht wissen, wie ein Azure AD-Benutzer gelöscht wird, lesen Sie [Löschen von Benutzern aus Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Zum Löschen Ihrer Richtlinie wählen Sie die Richtlinie aus, und klicken Sie dann in der Symbolleiste für den Schnellzugriff auf **Löschen**.

    :::image type="content" source="./media/require-tou/33.png" alt-text="Screenshot: Richtlinie „MFA für Azure-Portalbenutzer erforderlich“. Das Kontextmenü ist sichtbar, in dem „Löschen“ hervorgehoben ist." border="false":::

- Um Ihre Nutzungsbedingungen zu löschen, wählen Sie sie aus, und klicken Sie dann auf der Symbolleiste oben auf **Bedingungen löschen**.

    :::image type="content" source="./media/require-tou/29.png" alt-text="Screenshot: Teil einer Tabelle, die Dokumente mit Nutzungsbedingungen enthält. Das Dokument „Meine Nutzungsbedingungen“ ist sichtbar. Im Menü ist die Option „Bedingungen löschen“ hervorgehoben." border="false":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Vorschreiben der Verwendung der MFA für bestimmte Apps](../authentication/tutorial-enable-azure-mfa.md)