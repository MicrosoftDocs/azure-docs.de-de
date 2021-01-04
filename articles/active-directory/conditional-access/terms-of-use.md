---
title: Nutzungsbedingungen – Azure Active Directory | Microsoft-Dokumentation
description: Eine Einführung in die Verwendung der Azure Active Directory-Nutzungsbedingungen zum Anzeigen von Informationen für Mitarbeiter oder Gäste, bevor diese Zugriff erhalten.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c1b07534c702e509b2b664fbee585aa2cff69f6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837599"
---
# <a name="azure-active-directory-terms-of-use"></a>Nutzungsbedingungen für Azure Active Directory

Mit den Azure AD-Nutzungsbedingungen können Organisationen komfortabel Informationen für Endbenutzer anzeigen. Dadurch wird sichergestellt, dass Benutzern relevante Haftungsausschlüsse angezeigt werden, um rechtliche oder compliancebezogene Anforderungen zu erfüllen. Dieser Artikel enthält eine Einführung in die Verwendung der Nutzungsbedingungen.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Übersichtsvideos

Das folgende Video enthält eine kurze Übersicht über die Nutzungsbedingungen.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Weitere Videos finden Sie unter:
- [How to deploy Terms of use in Azure Active Directory (Bereitstellen der Nutzungsbedingungen in Azure Active Directory)](https://www.youtube.com/embed/N4vgqHO2tgY)
- [How to roll out Terms of use in Azure Active Directory (Rollout der Nutzungsbedingungen in Azure Active Directory)](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Welche Möglichkeiten bieten die Nutzungsbedingungen?

Für die Azure AD-Nutzungsbedingungen können Sie folgendes festlegen:

- Mitarbeiter oder Gäste müssen Ihren Nutzungsbedingungen zustimmen, bevor ihnen Zugriff gewährt wird.
- Mitarbeiter oder Gäste müssen Ihren Nutzungsbedingungen auf jedem Gerät zustimmen, bevor ihnen Zugriff gewährt wird.
- Mitarbeiter oder Gäste müssen Ihren Nutzungsbedingungen gemäß einer Zeitplanserie zustimmen.
- Mitarbeiter oder Gäste müssen Ihre Nutzungsbedingungen vor dem Registrieren von Sicherheitsinformationen in Azure AD Multi-Factor Authentication (MFA) akzeptieren.
- Mitarbeiter müssen Ihre Nutzungsbedingungen vor dem Registrieren von Sicherheitsinformationen in der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure AD akzeptieren.
- Anzeigen allgemeiner Nutzungsbedingungen für alle Benutzer in Ihrer Organisation.
- Anzeigen spezifischer Nutzungsbedingungen auf der Grundlage von Benutzerattributen (beispielsweise Ärzte/Pflegepersonal oder Mitarbeiter im Inland/Ausland unter Verwendung [dynamischer Gruppen](../enterprise-users/groups-dynamic-membership.md))
- Anzeigen spezifischer Nutzungsbedingungen beim Zugriff auf besonders geschäftsrelevante Anwendungen (beispielsweise Salesforce).
- Anzeigen von Nutzungsbedingungen in verschiedenen Sprachen.
- Auflisten, wer den Nutzungsbedingungen bereits bzw. noch nicht zugestimmt hat.
- Leisten eines Beitrags zur Erfüllung von Datenschutzbestimmungen
- Anzeigen eines Protokolls zur Aktivität der Nutzungsbedingungen in Bezug auf Compliance und Überwachung.
- Erstellen und Verwalten von Nutzungsbedingungen für die Verwendung von [Microsoft Graph-APIs](/graph/api/resources/agreement?view=graph-rest-beta) (derzeit in der Vorschau).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Nutzungsbedingungen verwenden und konfigurieren zu können, benötigen Sie Folgendes:

- Eines der folgenden Abonnements: Azure AD Premium P1, P2, EMS E3 oder EMS E5.
   - Sollten Sie über keines dieser Abonnements verfügen, können Sie [Azure AD Premium beziehen](../fundamentals/active-directory-get-started-premium.md) oder [die Azure AD Premium-Testversion aktivieren](https://azure.microsoft.com/trial/get-started-active-directory/).
- Eines der folgenden Administratorkonten für das Verzeichnis, das Sie konfigurieren möchten:
   - Globaler Administrator
   - Sicherheitsadministrator
   - Administrator für den bedingten Zugriff

## <a name="terms-of-use-document"></a>Dokument mit Nutzungsbedingungen

Der Inhalt von Azure AD-Nutzungsbedingungen wird im PDF-Format bereitgestellt. Die PDF-Datei kann für beliebige Inhalte verwendet werden (etwa für vorhandene Vertragsdokumente) und ermöglicht es, bei der Benutzeranmeldung die Zustimmung von Endbenutzern einzuholen. Zur Unterstützung von Benutzern auf mobilen Geräten wird für PDF-Inhalte der Schriftgrad 24 empfohlen.

## <a name="add-terms-of-use"></a>Hinzufügen von Nutzungsbedingungen

Gehen Sie nach Fertigstellung Ihres Dokuments mit Nutzungsbedingungen wie folgt vor, um sie hinzuzufügen.

1. Melden Sie sich bei Azure als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie unter [https://aka.ms/catou](https://aka.ms/catou) zu **Nutzungsbedingungen**.

   ![Blatt „Bedingter Zugriff – Nutzungsbedingungen“](./media/terms-of-use/tou-blade.png)

1. Klicken Sie auf **Neue Bedingungen**.

   ![Bereich „Neue Nutzungsbedingungen“ zum Festlegen der Einstellungen für die Nutzungsbedingungen](./media/terms-of-use/new-tou.png)

1. Geben Sie im Feld **Name** einen Namen für die Nutzungsbedingungen zur Verwendung im Azure-Portal ein.
1. Geben Sie im Feld **Anzeigename** einen Titel ein, der Benutzern bei der Anmeldung angezeigt wird.
1. Navigieren Sie im Feld für das **Dokument mit Nutzungsbedingungen** zu Ihrer PDF-Datei mit den fertig gestellten Nutzungsbedingungen, und wählen Sie die Datei aus.
1. Wählen Sie die Sprache für Ihr Dokument mit Nutzungsbedingungen aus. Die Sprachoption ermöglicht das Hochladen mehrerer Nutzungsbedingungen in verschiedenen Sprachen. Die Version der Nutzungsbedingungen, die einem Endbenutzer angezeigt wird, basiert auf den Browsereinstellungen.
1. Legen Sie **Benutzer müssen die Nutzungsbedingungen erweitern** auf **Ein** fest, damit die Endbenutzer die Nutzungsbedingungen anzeigen müssen, bevor sie diesen zustimmen können.
1. Wenn die Endbenutzer Ihren Nutzungsbedingungen auf jedem Gerät zustimmen sollen, über das der Zugriff erfolgt, legen Sie **Zustimmung der Benutzer auf jedem Gerät erforderlich** auf **Ein** fest. Wenn diese Option aktiviert ist, müssen Benutzer möglicherweise weitere Anwendungen installieren. Weitere Informationen finden Sie unter [Nutzungsbedingungen pro Gerät](#per-device-terms-of-use).
1. Wenn die Zustimmungen zu Nutzungsbedingungen nach einem Zeitplan ablaufen sollen, legen Sie **Ablauf für Einwilligungen** auf **Ein** fest. Wenn diese Option aktiviert ist, werden zwei zusätzliche Zeitplaneinstellungen angezeigt.

   ![Einstellungen von „Ablauf für Einwilligungen“ zum Festlegen des Startdatums, der Häufigkeit und Dauer](./media/terms-of-use/expire-consents.png)

1. Verwenden Sie die Einstellungen **Ablauf startet am** und **Häufigkeit**, um den Zeitplan für den Ablauf der Nutzungsbedingungen anzugeben. In der folgenden Tabelle wird das Ergebnis für ein paar Beispieleinstellungen aufgezeigt:

   | Ablauf startet am | Häufigkeit | Ergebnis |
   | --- | --- | --- |
   | Heutiges Datum  | Monatlich | Ab heute müssen die Benutzer den Nutzungsbedingungen zustimmen und diesen dann jeden Monat erneut zustimmen. |
   | Datum in der Zukunft  | Monatlich | Ab heute müssen die Benutzer den Nutzungsbedingungen zustimmen. Wenn das Datum in der Zukunft eintritt, laufen die Zustimmungen ab, und die Benutzer müssen dann den Nutzungsbedingungen jeden Monat erneut zustimmen.  |

   Wenn Sie beispielsweise das Datum für „Ablauf startet am“ auf **1. Jan.** und die Häufigkeit auf **Monatlich** festlegen, können sich für zwei Benutzer die folgenden Ablauftermine ergeben:

   | Benutzer | Erstes Zustimmungsdatum | Erstes Ablaufdatum | Zweites Ablaufdatum | Drittes Ablaufdatum |
   | --- | --- | --- | --- | --- |
   | Alina | 1\. Januar | 1\. Februar | 1\. März | 1\. April |
   | Bernd | 15. Januar | 1\. Februar | 1\. März | 1\. April |

1. Verwenden Sie die Einstellung **Zeitraum bis erneute Zustimmung erforderlich ist (Tage)** , um die Anzahl von Tagen anzugeben, nach denen der Benutzer den Nutzungsbedingungen spätestens erneut zustimmen muss. Dies ermöglicht es den Benutzern, ihrem eigenen Zeitplan zu folgen. Wenn Sie beispielsweise den Zeitraum auf **30** Tage festlegen, können sich für zwei Benutzer die folgenden Ablauftermine ergeben:

   | Benutzer | Erstes Zustimmungsdatum | Erstes Ablaufdatum | Zweites Ablaufdatum | Drittes Ablaufdatum |
   | --- | --- | --- | --- | --- |
   | Alina | 1\. Januar | 31. Januar | 2\. März | 1\. April |
   | Bernd | 15. Januar | 14. Februar | 16. März | 15. April |

   Es ist möglich, die beiden Einstellungen – **Ablauf für Einwilligungen** und **Zeitraum bis erneute Zustimmung erforderlich ist (Tage)** – zusammen zu verwenden. In der Regel wird jedoch jeweils nur eine dieser Einstellungen verwendet.

1. Verwenden Sie unter **Bedingter Zugriff** die Liste **Mit Richtlinienvorlagen für bedingten Zugriff erzwingen**, um die Vorlage zum Erzwingen der Nutzungsbedingungen auszuwählen.

   ![Dropdownliste für bedingten Zugriff zum Auswählen einer Richtlinienvorlage](./media/terms-of-use/conditional-access-templates.png)

   | Vorlage | BESCHREIBUNG |
   | --- | --- |
   | **Zugriff auf Cloud-Apps für alle Gäste** | Es wird eine Richtlinie für bedingten Zugriff für alle Gäste und alle Cloud-Apps erstellt. Diese Richtlinie hat Auswirkungen auf das Azure-Portal. Nach der Erstellung dieser Richtlinie müssen Sie sich möglicherweise ab- und anmelden. |
   | **Zugriff auf Cloud-Apps für alle Benutzer** | Es wird eine Richtlinie für bedingten Zugriff für alle Benutzer und alle Cloud-Apps erstellt. Diese Richtlinie hat Auswirkungen auf das Azure-Portal. Nach der Erstellung dieser Richtlinie müssen Sie sich ab- und anmelden. |
   | **Benutzerdefinierte Richtlinie** | Wählen Sie die Benutzer, Gruppen und Apps aus, für die diese Nutzungsbedingungen gelten sollen. |
   | **Richtlinie für bedingten Zugriff später erstellen** | Diese Nutzungsbedingungen werden in der Zuweisungssteuerungsliste angezeigt, wenn Sie eine Richtlinie für bedingten Zugriff erstellen. |

   >[!IMPORTANT]
   >Die Richtliniensteuerelemente für den bedingten Zugriff (einschließlich Nutzungsbedingungen) unterstützen die Erzwingung für Dienstkonten nicht. Es wird empfohlen, alle Dienstkonten von der Richtlinie für bedingten Zugriff auszuschließen.

    Benutzerdefinierte Richtlinien für bedingten Zugriff ermöglichen präzise Nutzungsbedingungen bis auf die Ebene einer spezifischen Cloudanwendung oder Benutzergruppe. Weitere Informationen finden Sie unter [Quickstart: Fordern der Annahme von Nutzungsbedingungen vor dem Zugreifen auf Cloud-Apps](require-tou.md).

1. Klicken Sie auf **Erstellen**.

   Wenn Sie eine benutzerdefinierte Richtlinienvorlage für bedingten Zugriff ausgewählt haben, wird ein neuer Bildschirm angezeigt, auf dem Sie die benutzerdefinierte Richtlinie für bedingten Zugriff erstellen können.

   ![Bedingter Zugriff, Bereich „Neu“, der angezeigt wird, wenn Sie die benutzerdefinierte Richtlinienvorlage für bedingten Zugriff ausgewählt haben](./media/terms-of-use/custom-policy.png)

   Ihre neuen Nutzungsbedingungen werden angezeigt.

   ![Auf dem Blatt für Nutzungsbedingungen aufgeführte neue Nutzungsbedingungen](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Anzeigen des Berichts über abgelehnte und akzeptierte Nutzungsbedingungen

Auf dem Blatt für die Nutzungsbedingungen wird angezeigt, wie viele Benutzer die Bedingungen akzeptiert bzw. abgelehnt haben. Diese Werte sowie die Angabe, wer die Bedingungen akzeptiert/abgelehnt hat, werden für die Lebensdauer der Nutzungsbedingungen gespeichert.

1. Melden Sie sich unter [https://aka.ms/catou](https://aka.ms/catou) bei Azure an, und navigieren Sie zu **Nutzungsbedingungen**.

   ![Blatt für Nutzungsbedingungen mit der Anzahl der Benutzer, die zugestimmt und abgelehnt haben](./media/terms-of-use/view-tou.png)

1. Klicken Sie neben dem Namen der Nutzungsbedingungen auf die Zahl unter **Akzeptiert** oder **Abgelehnt**, um den aktuellen Status für Benutzer anzuzeigen.

   ![Bereich „Zustimmungen zu Nutzungsbedingungen“ mit einer Auflistung der Benutzer, die zugestimmt haben](./media/terms-of-use/accepted-tou.png)

1. Um den Verlauf für einen einzelnen Benutzer anzuzeigen, klicken Sie auf die Auslassungspunkte ( **...** ) und dann auf **Verlauf anzeigen**.

   ![Kontextmenü „Verlauf anzeigen“ für einen Benutzer](./media/terms-of-use/view-history-menu.png)

   Im Bereich „Verlauf anzeigen“ wird der Verlauf aller Zustimmungen, Ablehnungen und Ablauftermine angezeigt.

   ![Der Bereich „Verlauf anzeigen“ enthält den Verlauf aller Zustimmungen, Ablehnungen und Ablauftermine für einen Benutzer.](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Anzeigen der Azure AD-Überwachungsprotokolle

Für den Fall, dass Sie zusätzliche Aktivitäten anzeigen möchten, enthalten die Azure AD-Nutzungsbedingungen auch Überwachungsprotokolle. Jede Benutzerzustimmung löst ein Ereignis in den Überwachungsprotokollen aus, die **30 Tage** lang gespeichert werden. Diese Protokolle können im Portal angezeigt oder als CSV-Datei heruntergeladen werden.

Gehen Sie wie folgt vor, um mit der Verwendung von Azure AD-Überwachungsprotokollen zu beginnen:

1. Melden Sie sich unter [https://aka.ms/catou](https://aka.ms/catou) bei Azure an, und navigieren Sie zu **Nutzungsbedingungen**.
1. Wählen Sie die gewünschten Nutzungsbedingungen aus.
1. Klicken Sie auf **Überwachungsprotokolle anzeigen**.

   ![Blatt für Nutzungsbedingungen, auf dem die Option „Überwachungsprotokolle anzeigen“ hervorgehoben ist](./media/terms-of-use/audit-tou.png)

1. Auf dem Bildschirm mit den Azure AD-Überwachungsprotokollen können Sie die Informationen mithilfe der bereitgestellten Listen nach bestimmten Überwachungsprotokollinformationen filtern.

   Sie können auch auf **Herunterladen** klicken, um die Informationen zur in einer CSV-Datei herunterzuladen und lokal zu verwenden.

   ![Bildschirm der Azure AD-Überwachungsprotokolle mit Angaben zu Datum, Zielrichtlinie, „Initiiert von (Akteur)“ und Aktivität](./media/terms-of-use/audit-logs-tou.png)

   Wenn Sie auf ein Protokoll klicken, wird ein Bereich mit weiteren Aktivitätsdetails angezeigt.

   ![Aktivitätsdetails für ein Protokoll mit den Angaben „Aktivität“, „Aktivitätsstatus“, „Initiiert von (Akteur)“ und „Ziel(e)“](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Darstellung der Nutzungsbedingungen für Benutzer

Wenn Nutzungsbedingungen erstellt wurden und erzwungen werden, wird den betroffenen Benutzern bei der Anmeldung folgender Bildschirm angezeigt.

![Beispiel für Nutzungsbedingungen, die angezeigt werden, wenn sich ein Benutzer anmeldet](./media/terms-of-use/user-tou.png)

Benutzer können die Nutzungsbedingungen anzeigen und bei Bedarf die Schaltflächen zum Vergrößern und Verkleinern verwenden.

![Ansicht der Nutzungsbedingungen mit Zoomschaltflächen](./media/terms-of-use/zoom-buttons.png)

Der folgende Bildschirm veranschaulicht die Darstellung der Nutzungsbedingungen auf mobilen Geräten:

![Beispiel für Nutzungsbedingungen, die angezeigt werden, wenn sich ein Benutzer auf einem mobilen Gerät anmeldet](./media/terms-of-use/mobile-tou.png)

Benutzer müssen die Nutzungsbedingungen nur einmal akzeptieren. Bei nachfolgenden Anmeldungen werden diese Bedingungen nicht mehr angezeigt.

### <a name="how-users-can-review-their-terms-of-use"></a>Möglichkeiten zum Lesen der Nutzungsbedingungen für die Benutzer

Benutzer können die Nutzungsbedingungen, die sie akzeptiert haben, wie folgt lesen und anzeigen:

1. Melden Sie sich bei [https://myapps.microsoft.com](https://myapps.microsoft.com) an.
1. Klicken Sie in der rechten oberen Ecke auf Ihren Namen, und wählen Sie **Profil** aus.

   ![MyApps-Website mit dem geöffneten Bereich des Benutzers](./media/terms-of-use/tou14.png)

1. Klicken Sie in Ihrem Profil auf **Nutzungsbedingungen lesen**.

   ![Profilseite für einen Benutzer mit dem Link „Nutzungsbedingungen lesen“](./media/terms-of-use/tou13a.png)

1. Hier können Sie sich die akzeptierten Nutzungsbedingungen ansehen.

## <a name="edit-terms-of-use-details"></a>Bearbeiten der Details der Nutzungsbedingungen

Sie können einige Details der Nutzungsbedingungen bearbeiten, aber kein vorhandenes Dokument ändern. Im Folgenden wird das Verfahren zum Bearbeiten von Details beschrieben.

1. Melden Sie sich unter [https://aka.ms/catou](https://aka.ms/catou) bei Azure an, und navigieren Sie zu **Nutzungsbedingungen**.
1. Wählen Sie die Nutzungsbedingungen aus, die Sie bearbeiten möchten.
1. Klicken Sie auf **Bedingungen bearbeiten**.
1. Ändern Sie im Bereich „Bedingungen bearbeiten“ den Namen und Anzeigenamen, oder fordern Sie von Benutzern, Werte zu erweitern.

   Wenn Sie andere Einstellungen ändern möchten, z. B. das PDF-Dokument, die Optionen „Zustimmung der Benutzer auf jedem Gerät erforderlich“, „Ablauf für Einwilligungen“ und „Zeitraum bis erneute Zustimmung erforderlich ist (Tage)“ oder die Richtlinie für bedingten Zugriff, müssen Sie neue Nutzungsbedingungen erstellen.

   ![Bereich „Nutzungsbedingungen bearbeiten“ mit Optionen für den Namen und zum Erweitern der Nutzungsbedingungen](./media/terms-of-use/edit-tou.png)

1. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

   Nachdem Sie die Änderungen gespeichert haben, müssen die Benutzer diesen Änderungen nicht erneut zustimmen.

## <a name="add-a-terms-of-use-language"></a>Hinzufügen einer Sprache der Nutzungsbedingungen

Im Folgenden wird das Verfahren zum Hinzufügen einer Sprache der Nutzungsbedingungen beschrieben.

1. Melden Sie sich unter [https://aka.ms/catou](https://aka.ms/catou) bei Azure an, und navigieren Sie zu **Nutzungsbedingungen**.
1. Wählen Sie die Nutzungsbedingungen aus, die Sie bearbeiten möchten.
1. Klicken Sie im Detailbereich auf die Registerkarte **Sprachen**.

   ![Ausgewählte Nutzungsbedingungen und hervorgehobene Registerkarte „Sprachen“ im Detailbereich](./media/terms-of-use/languages-tou.png)

1. Klicken Sie auf **Sprache hinzufügen**.
1. Laden Sie in den Bereich „Sprache der Nutzungsbedingungen hinzufügen“ Ihre lokalisierte PDF-Datei hoch, und wählen Sie die Sprache aus.

   ![Bereich „Sprache der Nutzungsbedingungen hinzufügen“ mit Optionen zum Hochladen lokalisierter PDF-Dateien](./media/terms-of-use/language-add-tou.png)

1. Klicken Sie auf **Hinzufügen**, um die Sprache hinzuzufügen.

## <a name="per-device-terms-of-use"></a>Nutzungsbedingungen pro Gerät

Mit der Einstellung **Zustimmung der Benutzer auf jedem Gerät erforderlich** können Sie erzwingen, dass die Endbenutzer Ihren Nutzungsbedingungen auf jedem Gerät zustimmen müssen, über das der Zugriff erfolgt. Die Endbenutzer müssen ihre Geräte in Azure AD registrieren. Nach der Geräteregistrierung wird die Geräte-ID verwendet, um die Nutzungsbedingungen auf dem jeweiligen Gerät zu erzwingen.

Nachstehend finden Sie eine Liste der unterstützten Plattformen und Softwareanwendungen.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Andere |
> | --- | --- | --- | --- | --- |
> | **Native App** | Ja | Ja | Ja |  |
> | **Microsoft Edge** | Ja | Ja | Ja |  |
> | **Internet Explorer** | Ja | Ja | Ja |  |
> | **Chrome (mit Erweiterung)** | Ja | Ja | Ja |  |

Für Nutzungsbedingungen pro Gerät gelten die folgenden Einschränkungen:

- Ein Gerät kann nur mit einem Mandanten verknüpft werden.
- Der Benutzer muss über Berechtigungen zum Einbinden des Geräts verfügen.
- Die Intune-Registrierungs-App wird nicht unterstützt. Stellen Sie sicher, dass sie in jeder Richtlinie für bedingten Zugriff, die Nutzungsbedingungen erfordern, ausgeschlossen wird.
- Azure AD B2B-Benutzer werden nicht unterstützt.

Wenn das Gerät des Benutzers nicht eingebunden ist, erhält er eine Meldung, dass er das Gerät einbinden muss. Die Benutzeroberfläche ist von der Plattform und Software abhängig.

### <a name="join-a-windows-10-device"></a>Einbinden eines Windows 10-Geräts

Wenn ein Benutzer Windows 10 und Microsoft Edge verwendet, wird eine Meldung zum [Einbinden des Geräts](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device) angezeigt, die in etwa wie folgt aussieht.

![Windows 10 und Microsoft Edge – Meldung, in der Sie darauf hingewiesen werden, dass Ihr Gerät registriert werden muss](./media/terms-of-use/per-device-win10-edge.png)

Bei Verwendung von Chrome werden die Benutzer aufgefordert, die [Erweiterung „Windows 10 Accounts“](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) zu installieren.

### <a name="register-an-ios-device"></a>Registrieren eines iOS-Geräts

Wenn ein Benutzer ein iOS-Gerät verwendet, wird er aufgefordert, die [Microsoft Authenticator-App](https://apps.apple.com/us/app/microsoft-authenticator/id983156458) zu installieren.

### <a name="register-an-android-device"></a>Registrieren eines Android-Geräts

Wenn ein Benutzer ein Android-Gerät verwendet, wird er aufgefordert, die [Microsoft Authenticator-App](https://play.google.com/store/apps/details?id=com.azure.authenticator) zu installieren.

### <a name="browsers"></a>Browser

Wenn ein Benutzer einen nicht unterstützten Browser verwendet, wird er aufgefordert, einen anderen Browser zu verwenden.

![Meldung, in der Sie darauf hingewiesen werden, dass Ihr Gerät registriert werden muss, aber der Browser nicht unterstützt wird](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Löschen von Nutzungsbedingungen

Veraltete Nutzungsbedingungen können wie folgt gelöscht werden:

1. Melden Sie sich unter [https://aka.ms/catou](https://aka.ms/catou) bei Azure an, und navigieren Sie zu **Nutzungsbedingungen**.
1. Wählen Sie die Nutzungsbedingungen aus, die Sie entfernen möchten.
1. Klicken Sie auf **Bedingungen löschen**.
1. Klicken Sie in der Bestätigungsmeldung auf **Ja**.

   ![Meldung, in der Sie aufgefordert werden, das Löschen der Nutzungsbedingungen zu bestätigen](./media/terms-of-use/delete-tou.png)

   Ihre Nutzungsbedingungen werden nicht mehr angezeigt.

## <a name="deleted-users-and-active-terms-of-use"></a>Gelöschte Benutzer und aktive Nutzungsbedingungen

Ein gelöschter Benutzer befindet sich in Azure AD standardmäßig 30 Tage lang in einem gelöschten Zustand. In diesem Zeitraum kann er von einem Administrator wiederhergestellt werden, sofern erforderlich. Nach 30 Tagen wird dieser Benutzer endgültig gelöscht. Darüber hinaus kann ein globaler Administrator über das Azure Active Directory-Portal explizit [einen kürzlich gelöschten Benutzer endgültig löschen](../fundamentals/active-directory-users-restore.md), bevor dieser Zeitraum abgelaufen ist. Wurde ein Benutzer endgültig gelöscht, werden nachfolgende Daten für diesen Benutzer aus den aktiven Nutzungsbedingungen entfernt. Überwachungsinformationen zu gelöschten Benutzern verbleiben im Überwachungsprotokoll.

## <a name="policy-changes"></a>Richtlinienänderungen

Richtlinien für bedingten Zugriff werden sofort wirksam. In diesem Fall werden dem Administrator Cloudprobleme oder Azure AD-Tokenprobleme angezeigt. Der Administrator muss sich ab- und dann erneut anmelden, um die neue Richtlinie zu erfüllen.

> [!IMPORTANT]
> Betroffene Benutzer müssen sich zur Erfüllung einer neuen Richtlinie ab- und wieder anmelden, wenn Folgendes zutrifft:
>
> - Für eine Nutzungsbedingung ist eine Richtlinie für bedingten Zugriff aktiviert
> - Eine zweite Nutzungsbedingung wird erstellt.

## <a name="b2b-guests-preview"></a>B2B-Gäste (Vorschau)

Die meisten Organisationen haben für ihre Mitarbeiter einen Prozess zur Zustimmung zu den Nutzungsbedingungen und Datenschutzbestimmungen ihrer Organisation eingerichtet. Aber wie können Sie die gleichen Zustimmungen für Azure AD B2B-Gäste erzwingen, wenn sie über SharePoint oder Teams hinzugefügt werden? Mithilfe von bedingtem Zugriff und Nutzungsbedingungen können Sie eine Richtlinie direkt für B2B-Gastbenutzer erzwingen. Beim Vorgang zum Einlösen der Einladung werden dem Benutzer die Nutzungsbedingungen angezeigt. Diese Unterstützung befindet sich derzeit in der Vorschauphase.

Die Nutzungsbedingungen werden nur angezeigt, wenn der Benutzer über ein Gastkonto in Azure AD verfügt. SharePoint Online verfügt aktuell über eine [Freigabeoberfläche für externe Ad-hoc-Empfänger](/sharepoint/what-s-new-in-sharing-in-targeted-release) zum Freigeben von Dokumenten oder Ordnern, für die der Benutzer nicht über ein Gastkonto verfügen muss. In diesem Fall werden die Nutzungsbedingungen nicht angezeigt.

![Bereich „Benutzer und Gruppen“ – Registerkarte „Einschließen“, auf der die Option „Sämtliche Gastbenutzer (Vorschau)“ aktiviert ist](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Unterstützung für Cloud-Apps (Vorschau)

Nutzungsbedingungen können für verschiedene Cloud-Apps wie Azure Information Protection und Microsoft Intune verwendet werden. Diese Unterstützung befindet sich derzeit in der Vorschauphase.

### <a name="azure-information-protection"></a>Azure Information Protection

Sie können für die App Azure Information Protection eine Richtlinie für bedingten Zugriff konfigurieren und einen Benutzer auffordern, den Nutzungsbedingungen zuzustimmen, wenn er auf ein geschütztes Dokument zugreift. Dadurch wird das Anzeigen der Nutzungsbedingungen ausgelöst, bevor ein Benutzer zum ersten Mal auf ein geschütztes Dokument zugreifen kann.

![Bereich „Cloud-Apps“ mit ausgewählter Microsoft Azure Information Protection-App](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune-Registrierung

Sie können für die App Microsoft Intune-Registrierung eine Richtlinie für bedingten Zugriff konfigurieren und einen Benutzer vor der Registrierung eines Geräts in Intune auffordern, den Nutzungsbedingungen zuzustimmen. Weitere Informationen finden Sie im [Blogbeitrag](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) über das Auswählen der richtigen Lösung für Nutzungsbedingungen für Ihre Organisation.

![Bereich „Cloud-Apps“ mit ausgewählter Microsoft Intune-App](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Die Intune-Registrierungs-App wird für [Nutzungsbedingungen pro Gerät](#per-device-terms-of-use) nicht unterstützt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie sehe ich, wann bzw. ob ein Benutzer die Nutzungsbedingungen akzeptiert hat?**<br />
A: Klicken Sie auf dem Blatt für Nutzungsbedingungen auf die Zahl unter **Akzeptiert**. Sie können die Zustimmungsaktivität auch in den Azure AD-Überwachungsprotokollen anzeigen oder suchen. Weitere Informationen finden Sie unter „Anzeigen des Berichts über abgelehnte und akzeptierte Nutzungsbedingungen“ und [Anzeigen der Azure AD-Überwachungsprotokolle](#view-azure-ad-audit-logs).

**F: Wie lange werden die Informationen gespeichert?**<br />
A: Die Benutzeranzahl im Bericht zu den Nutzungsbedingungen sowie die Angabe, wer die Bedingungen akzeptiert/abgelehnt hat, werden für die Lebensdauer der Nutzungsbedingungen gespeichert. Die Azure AD-Überwachungsprotokolle werden 30 Tage lang gespeichert.

**F: Warum sehe ich eine andere Anzahl von Zustimmungen im Bericht zu den Nutzungsbedingungen als in den Azure AD-Überwachungsprotokollen?**<br />
A: Der Bericht zu den Nutzungsbedingungen wird für die Lebensdauer dieser Nutzungsbedingungen gespeichert, während die Azure AD-Überwachungsprotokolle (nur) 30 Tage lang gespeichert werden. Darüber hinaus wird im Bericht zu den Nutzungsbedingungen nur der aktuelle Zustimmungsstatus der Benutzer angezeigt. Lehnt ein Benutzer z.B. die Nutzungsbedingungen ab und akzeptiert sie aber später, enthält der Bericht zu den Nutzungsbedingungen nur die Zustimmung. Wenn Sie den Verlauf anzeigen möchten, können Sie in den Azure AD-Überwachungsprotokollen nachsehen.

**F: Müssen Benutzer den Nutzungsbedingungen erneut zustimmen, wenn ich die Details der Nutzungsbedingungen bearbeite?**<br />
A: Nein. Wenn ein Administrator die Details der Nutzungsbedingungen (den Namen, den Anzeigenamen, die Option „Benutzer müssen die Nutzungsbedingungen erweitern“ oder „Sprache hinzufügen“) bearbeitet, müssen die Benutzer den neuen Bedingungen nicht erneut zustimmen.

**F: Kann ich ein vorhandenes Dokument mit Nutzungsbedingungen aktualisieren?**<br />
A: Derzeit können Sie ein vorhandenes Dokument mit Nutzungsbedingungen nicht aktualisieren. Um ein Dokument mit Nutzungsbedingungen zu ändern, müssen Sie eine neue Instanz der Nutzungsbedingungen erstellen.

**F: Wenn das PDF-Dokument mit den Nutzungsbedingungen Links enthält, können Benutzer darauf klicken?**<br />
A: Ja, Endbenutzer können Links zu weiteren Seiten auswählen. Links zu Abschnitten innerhalb des Dokuments werden hingegen nicht unterstützt. Außerdem funktionieren Links im PDF-Dokument mit den Nutzungsbedingungen nicht, wenn Sie aus dem Azure AD-Portal „Meine Apps“/„Mein Konto“ darauf zugreifen.

**F: Können Nutzungsbedingungen mehrere Sprachen unterstützen?**<br />
A: Ja. Derzeit sind 108 verschiedene Sprachen verfügbar, die ein Administrator für eine einzelne Version der Nutzungsbedingungen konfigurieren kann. Ein Administrator kann mehrere PDF-Dokumente hochladen und diese Dokumente mit einer unterstützten Sprache (bis zu 108) kennzeichnen. Wenn sich Endbenutzer anmelden, wird die Spracheinstellung des Browsers überprüft und das entsprechende Dokument angezeigt. Wenn es keine Übereinstimmung gibt, wird das Standarddokument angezeigt. Dies ist das erste Dokument, das hochgeladen wurde.

**F: Wann werden die Nutzungsbedingungen ausgelöst?**<br />
A: Die Nutzungsbedingungen werden bei der Anmeldung ausgelöst.

**F: Für welche Anwendungen kann ich Nutzungsbedingungen verwenden?**<br />
A: Sie können eine Richtlinie für bedingten Zugriff für Unternehmensanwendungen mit moderner Authentifizierung erstellen. Weitere Informationen finden Sie unter [Anzeigen aller von Ihnen verwaltbaren Unternehmens-Apps in Azure Active Directory](./../manage-apps/view-applications-portal.md).

**F: Kann ich einem bestimmten Benutzer oder einer bestimmten App mehrere Nutzungsbedingungen hinzufügen?**<br />
A: Ja. Erstellen Sie hierzu mehrere Richtlinien für bedingten Zugriff, und richten Sie sie auf die entsprechenden Gruppen oder Anwendungen aus. Wenn für einen Benutzer mehrere Nutzungsbedingungen gelten, muss er jeweils den einzelnen Nutzungsbedingungen zustimmen.

**F: Was passiert, wenn ein Benutzer die Nutzungsbedingungen ablehnt?**<br />
A: Der Benutzer kann nicht auf die Anwendung zugreifen. Wenn der Benutzer auf die Anwendung zugreifen möchte, muss er sich erneut anmelden und den Nutzungsbedingungen zustimmen.

**F: Ist es möglich, die Zustimmung zu zuvor akzeptierten Nutzungsbedingungen zu widerrufen?**<br />
A: Sie können [zuvor akzeptierte Nutzungsbedingungen überprüfen](#how-users-can-review-their-terms-of-use), aber zurzeit besteht keine Möglichkeit, die Zustimmung zu widerrufen.

**F: Was geschieht, wenn ich auch Intune-Geschäftsbedingungen verwende?**<br />
A: Wenn Sie sowohl Azure AD-Nutzungsbedingungen als auch [Intune-Nutzungsbedingungen](/intune/terms-and-conditions-create) konfiguriert haben, muss der Benutzer beide akzeptieren. Weitere Informationen finden Sie im Blogbeitrag [Choosing the right Terms solution for your organization](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) (Auswählen der richtigen Lösung für Bestimmungen für Ihre Organisation).

**F: Welche Endpunkte werden vom Dienst für die Nutzungsbedingungen zur Authentifizierung verwendet?**<br />
A: Der Dienst für die Nutzungsbedingungen verwendet die folgenden Endpunkte zur Authentifizierung: https://tokenprovider.termsofuse.identitygovernance.azure.com und https://account.activedirectory.windowsazure.com. Wenn Ihre Organisation über eine Liste zulässiger Registrierungs-URLs verfügt, müssen Sie die Endpunkte zusammen mit den Azure AD-Endpunkten für die Anmeldung dieser Liste hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Fordern der Annahme von Nutzungsbedingungen vor dem Zugreifen auf Cloud-Apps](require-tou.md)