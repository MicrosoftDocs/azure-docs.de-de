---
title: 'Schnellstart: Ablaufrichtlinie für Office 365-Gruppen: Azure Active Directory | Microsoft-Dokumentation'
description: 'Ablauf für Office 365-Gruppen: Azure Active Directory'
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01cedadc115496fcf00df986b4ad4b9c5aab5139
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606175"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Schnellstart: Festlegen des Ablaufs von Office 365-Gruppen in Azure Active Directory

In dieser Schnellstartanleitung legen Sie die Ablaufrichtlinie für Ihre Office 365-Gruppen fest. Wenn Benutzer eigene Gruppen einrichten können, entstehen unter Umständen zahlreiche ungenutzte Gruppen. Um das zu vermeiden, kann beispielsweise ein Ablaufdatum für diese Gruppen konfiguriert werden, um den Aufwand für das manuelle Löschen von Gruppen zu verringern.

Eine Ablaufrichtlinie ist einfach:

* Gruppenbesitzer werden benachrichtigt, eine ablaufende Gruppe zu verlängern.
* Nicht verlängerte Gruppen werden gelöscht.
* Eine gelöschte Office 365-Gruppe kann innerhalb von 30 Tagen durch einen Gruppenbesitzer oder einen Azure AD-Administrator wiederhergestellt werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisite"></a>Voraussetzung

 Die zum Einrichten des Gruppenablaufs erforderliche Rolle mit den geringsten Berechtigungen ist die Rolle „Benutzeradministrator“ für die Organisation.

## <a name="turn-on-user-creation-for-groups"></a>Aktivieren der Benutzererstellung für Gruppen

1. Melden Sie sich mit einem Benutzeradministratorkonto beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Gruppen** und anschließend auf **Allgemein**.
  
   ![Seite „Self-Service-Gruppeneinstellungen“](./media/groups-quickstart-expiration/self-service-settings.png)

3. Legen Sie **Benutzer können Office 365-Gruppen erstellen** auf **Ja** fest.

4. Klicken Sie auf **Speichern**, um die Gruppeneinstellungen zu speichern, wenn Sie fertig sind.

## <a name="set-group-expiration"></a>Festlegen des Gruppenablaufs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Azure Active Directory** > **Gruppen** > **Ablauf** aus, um die Ablaufeinstellungen zu öffnen.
  
   ![Seite „Ablaufeinstellungen“ für die Gruppe](./media/groups-quickstart-expiration/expiration-settings.png)

2. Legen Sie das Ablaufintervall fest. Wählen Sie einen vordefinierten Wert aus, oder geben Sie einen benutzerdefinierten Wert ein, der mehr als 31 Tage beträgt. 

3. Geben Sie eine E-Mail-Adresse an, an die die Verlängerungsbenachrichtigungen gesendet werden sollen, wenn eine Gruppe keinen Besitzer hat.

4. Legen Sie für diese Schnellstartanleitung **Ablauf für diese Office 365-Gruppen aktivieren** auf **Alle** fest.

5. Klicken Sie auf **Speichern**, um die Ablaufeinstellungen zu speichern, wenn Sie fertig sind.

Das ist alles! In dieser Schnellstartanleitung haben Sie die Ablaufrichtlinie für die ausgewählten Office 365-Gruppen festgelegt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="to-remove-the-expiration-policy"></a>So entfernen Sie die Ablaufrichtlinie

1. Vergewissern Sie sich, dass Sie beim [Azure-Portal](https://portal.azure.com) über ein Konto angemeldet sind, das als globaler Administrator für Ihren Mandanten konfiguriert ist.
2. Klicken Sie auf **Azure Active Directory** > **Gruppen** > **Ablauf**.
3. Legen Sie **Ablauf für diese Office 365-Gruppen aktivieren** auf **Keine** fest.

### <a name="to-turn-off-user-creation-for-groups"></a>So deaktivieren Sie die Benutzererstellung für Gruppen

1. Klicken Sie auf **Azure Active Directory** > **Gruppen** > **Allgemein**. 
2. Legen Sie **Benutzer können Office 365-Gruppen in Azure-Portalen erstellen.** auf **Nein** fest.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Ablauf einschließlich der PowerShell-Anweisungen und technischen Einschränkungen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Konfigurieren der Ablaufrichtlinie für Office 365-Gruppen](groups-lifecycle.md)
