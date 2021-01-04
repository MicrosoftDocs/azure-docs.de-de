---
title: Aktivieren von Azure AD Multi-Factor Authentication
description: In diesem Tutorial erfahren Sie, wie Sie Azure AD Multi-Factor Authentication für eine Gruppe von Benutzern aktivieren und die Anforderung des zweiten Faktors während eines Anmeldeereignisses testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32228e90e3cfc064cd5be9cd0655f321ab3e2809
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741149"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication

Bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) wird vom Benutzer im Rahmen eines Anmeldeereignisses eine zusätzliche Art der Identifizierung angefordert. Dabei kann es sich beispielsweise um die Eingabe eines Codes auf dem Smartphone oder um einen Fingerabdruckscan handeln. Wenn Sie ein zweites Authentifizierungsverfahren erzwingen, wird die Sicherheit erhöht, weil dieses zusätzliche Verfahren von einem Angreifer nicht ohne Weiteres nachvollzogen bzw. dupliziert werden kann.

Mithilfe von Azure AD Multi-Factor Authentication und Richtlinien für bedingten Zugriff kann MFA für Benutzer flexibel während bestimmter Anmeldeereignisse aktiviert werden.

> [!IMPORTANT]
> In diesem Tutorial wird für Administratoren veranschaulicht, wie Azure AD Multi-Factor Authentication aktiviert wird.
>
> Wenn Ihr IT-Team die Verwendung von Azure AD Multi-Factor Authentication nicht aktiviert hat oder Sie Probleme mit der Anmeldung haben, wenden Sie sich an Ihren Helpdesk.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Richtlinie für bedingten Zugriff, um Azure AD Multi-Factor Authentication für eine Gruppe von Benutzern zu aktivieren
> * Konfigurieren der Richtlinienbedingungen zum Initiieren der MFA
> * Testen des MFA-Prozesses als Benutzer

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Einen funktionierenden Azure AD-Mandanten mit mindestens einer aktivierten Azure AD Premium P1- oder -Testlizenz.
    * Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein Konto mit Berechtigungen vom Typ *Globaler Administrator*.
* Ein Benutzer ohne Administratorrechte mit einem Ihnen bekannten Kennwort, wie z. B. *testuser*. Das Konto wird in diesem Tutorial verwendet, um Azure AD Multi-Factor Authentication als Endbenutzer zu testen.
    * Wenn Sie einen Benutzer erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) weiter.
* Eine Gruppe, der der Benutzer ohne Administratorrechte angehört (beispielsweise *MFA-Test-Group*). In diesem Tutorial wird Azure AD Multi-Factor Authentication für diese Gruppe aktiviert.
    * Wenn Sie eine Gruppe erstellen müssen, finden Sie weitere Informationen unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-conditional-access-policy"></a>Erstellen der Richtlinie für bedingten Zugriff

Es empfiehlt sich, Azure AD Multi-Factor Authentication mit Richtlinien für bedingten Zugriff zu aktivieren. Bedingter Zugriff ermöglicht das Erstellen und Definieren von Richtlinien, die auf Anmeldeereignisse reagieren und zusätzliche Aktionen anfordern, bevor einem Benutzer der Zugriff auf eine Anwendung oder einen Dienst gewährt wird.

![Übersichtsdiagramm: Funktionsweise des bedingten Zugriffs zum Schutz des Anmeldevorgangs](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Richtlinien für bedingten Zugriff können präzise und spezifisch sein, damit Benutzer jederzeit und überall produktiv arbeiten können, gleichzeitig aber auch Ihre Organisation geschützt ist. In diesem Tutorial wird eine einfache Richtlinie für bedingten Zugriff erstellt, um die MFA zu initiieren, wenn sich ein Benutzer beim Azure-Portal anmeldet. In einem späteren Tutorial dieser Reihe wird Azure AD Multi-Factor Authentication mithilfe einer risikobasierten Richtlinie für bedingten Zugriff konfiguriert.

Erstellen Sie zunächst eine Richtlinie für bedingten Zugriff, und weisen Sie Ihre Benutzertestgruppe zu:

1. Melden Sie sich mit dem Konto mit den Berechtigungen vom Typ *Globaler Administrator* beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie anschließend im Menü auf der linken Seite die Option **Sicherheit** aus.
1. Wählen Sie **Bedingter Zugriff** und anschließend **+ Neue Richtlinie** aus.
1. Geben Sie einen Namen für die Richtlinie ein (beispielsweise *MFA Pilot*).
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** und anschließend das Optionsfeld **Benutzer und Gruppen auswählen** aus.
1. Aktivieren Sie das Kontrollkästchen für **Benutzer und Gruppen**, und wählen Sie anschließend **Auswählen** aus, um die verfügbaren Azure AD-Benutzer und -Gruppen zu durchsuchen.
1. Navigieren Sie zu Ihrer Azure AD-Gruppe (etwa *MFA-Test-Group*), und wählen Sie die Gruppe und anschließend **Auswählen** aus.

    [ ![Auswählen Ihrer Azure AD-Gruppe für die Richtlinie für bedingten Zugriff](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Wählen Sie **Fertig** aus, um die Richtlinie für bedingten Zugriff auf die Gruppe anzuwenden.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>Konfigurieren der Bedingungen die mehrstufige Authentifizierung

Nachdem Sie die Richtlinie für bedingten Zugriff erstellt und eine Benutzertestgruppe zugewiesen haben, müssen als Nächstes die Cloud-Apps oder Aktionen zum Auslösen der Richtlinie definiert werden. Bei diesen Cloud-Apps oder Aktionen handelt es sich um die Szenarien, die eine zusätzliche Verarbeitung erfordern (etwa in Form einer MFA-Aufforderung). So können Sie beispielsweise festlegen, dass für den Zugriff auf eine Finanzanwendung oder für die Verwendung von Verwaltungstools eine zusätzliche Überprüfung erforderlich ist.

Konfigurieren Sie die Richtlinie für bedingten Zugriff in diesem Tutorial so, dass die MFA initiiert wird, wenn sich ein Benutzer beim Azure-Portal anmeldet.

1. Wählen Sie **Cloud-Apps oder -aktionen** aus. Sie können auswählen, ob die Richtlinie für bedingten Zugriff auf alle Cloud-Apps (*Alle Cloud-Apps*) oder nur auf bestimmte Apps (*Apps auswählen*) angewendet werden soll. Sie haben auch die Möglichkeit, bestimmte Apps aus der Richtlinie auszuschließen.

    Wählen Sie für dieses Tutorial auf der Seite *Einschließen* das Optionsfeld **Apps auswählen** aus.

1. Wählen Sie **Auswählen** aus, und durchsuchen Sie die Liste der verfügbaren Anmeldeereignisse, die verwendet werden können.

    Wählen Sie für dieses Tutorial die Option **Microsoft Azure Management** (Microsoft Azure-Verwaltung) aus, sodass die Richtlinie für Anmeldeereignisse im Zusammenhang mit dem Azure-Portal gilt.

1. Wählen Sie zum Anwenden der ausgewählten Apps **Auswählen** und anschließend **Fertig** aus.

    ![Auswählen der App „Microsoft Azure Management“ (Microsoft Azure-Verwaltung), um sie in die Richtlinie für bedingten Zugriff einzuschließen](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Mithilfe von Zugriffssteuerungen können Sie die Voraussetzungen definieren, die erfüllt sein müssen, damit einem Benutzer Zugriff gewährt wird (beispielsweise die Verwendung einer genehmigten Client-App oder eines in Azure AD Hybrid eingebundenen Geräts). Konfigurieren Sie die Zugriffssteuerungen in diesem Tutorial so, dass die MFA im Rahmen eines Anmeldeereignisses für das Azure-Portal erforderlich ist.

1. Wählen Sie unter *Zugriffssteuerung* die Option **Gewähren** aus, und vergewissern Sie sich, dass das Optionsfeld **Zugriff gewähren** aktiviert ist.
1. Aktivieren Sie das Kontrollkästchen **Multi-Factor Authentication erforderlich**, und wählen Sie anschließend **Auswählen** aus.

Richtlinien für bedingten Zugriff können auf *Nur Bericht* festgelegt werden, wenn Sie ermitteln möchten, welche Auswirkungen die Konfiguration auf die Benutzer hätte, oder auf *Aus*, wenn Sie die Richtlinie nicht sofort verwenden möchten. Da in diesem Tutorial eine Benutzertestgruppe als Zielgruppe verwendet wird, aktivieren wir als Nächstes die Richtlinie und testen anschließend Azure AD Multi-Factor Authentication.

1. Legen Sie die Umschaltfläche *Richtlinie aktivieren* auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie für bedingten Zugriff anzuwenden.

## <a name="test-azure-ad-multi-factor-authentication"></a>Testen von Azure AD Multi-Factor Authentication

In diesem Abschnitt sehen wir uns die Richtlinie für bedingten Zugriff sowie Azure AD Multi-Factor Authentication in Aktion an. Melden Sie sich zunächst bei einer Ressource an, für die keine MFA erforderlich ist:

1. Öffnen Sie ein neues Browserfenster im InPrivate- oder Inkognitomodus, und navigieren Sie zu [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
1. Melden Sie sich mit Ihrem Testbenutzer ohne Administratorrechte an (beispielsweise *testuser*). Es wird keine MFA-Aufforderung angezeigt.
1. Schließen Sie das Browserfenster.

Melden Sie sich nun beim Azure-Portal an. Da die Richtlinie für bedingten Zugriff so konfiguriert wurde, dass für das Azure-Portal eine zusätzliche Überprüfung erforderlich ist, wird eine Azure AD Multi-Factor Authentication-Aufforderung angezeigt.

1. Öffnen Sie ein neues Browserfenster im InPrivate- oder Inkognitomodus, und navigieren Sie zu [https://portal.azure.com](https://portal.azure.com).
1. Melden Sie sich mit Ihrem Testbenutzer ohne Administratorrechte an (beispielsweise *testuser*). Sie müssen sich für Azure AD Multi-Factor Authentication registrieren und die mehrstufige Authentifizierung verwenden. Befolgen Sie die Anweisungen, um den Prozess abzuschließen, und vergewissern Sie sich, dass die Anmeldung beim Azure-Portal erfolgreich ist.

    ![Befolgen der Anweisungen im Browser und anschließend der Anweisungen in Ihrer registrierten MFA-Aufforderung, um die Anmeldung durchzuführen](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Schließen Sie das Browserfenster.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Richtlinie für bedingten Zugriff, die im Rahmen dieses Tutorials zum Aktivieren von Azure AD Multi-Factor Authentication konfiguriert wurde, nicht mehr benötigen, löschen Sie die Richtlinie wie folgt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie anschließend im Menü auf der linken Seite die Option **Sicherheit** aus.
1. Wählen Sie **Bedingter Zugriff** und anschließend die erstelle Richtlinie aus (beispielsweise *MFA Pilot*).
1. Wählen Sie **Löschen** aus, und bestätigen Sie, dass Sie die Richtlinie löschen möchten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Azure AD Multi-Factor Authentication mithilfe von Richtlinien für bedingten Zugriff für eine ausgewählte Benutzergruppe aktiviert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Richtlinie für bedingten Zugriff, um Azure AD Multi-Factor Authentication für eine Gruppe von Azure AD-Benutzern zu aktivieren
> * Konfigurieren der Richtlinienbedingungen zum Initiieren der MFA
> * Testen des MFA-Prozesses als Benutzer

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren des Kennwortrückschreibens](./tutorial-enable-sspr-writeback.md)