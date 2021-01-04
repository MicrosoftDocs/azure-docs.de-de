---
title: Benutzerdefinierte Steuerelemente beim bedingtem Zugriff in Azure AD
description: Hier erfahren Sie, wie benutzerdefinierte Steuerelemente beim bedingten Zugriff in Azure Active Directory funktionieren.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837582"
---
# <a name="custom-controls-preview"></a>Benutzerdefinierte Steuerelemente (Vorschau)

„Benutzerdefinierte Steuerelemente“ ist eine Vorschaufunktion von Azure Active Directory. Wenn Sie benutzerdefinierte Steuerelemente verwenden, werden Ihre Benutzer zu kompatiblen Diensten umgeleitet, um Authentifizierungsanforderungen außerhalb von Azure Active Directory zu genügen. Um die Bedingungen dieses Steuerelements zu erfüllen, wird der Browser eines Benutzers an den externen Dienst umgeleitet, die erforderliche Authentifizierung wird durchgeführt, dann wird der Browser wieder an Azure Active Directory umgeleitet. Azure Active Directory überprüft die Antwort, und wenn der Benutzer erfolgreich authentifiziert oder überprüft wurde, verbleibt der Benutzer im Vorgangsfluss des bedingten Zugriffs.

> [!NOTE]
> Weitere Informationen zu den geplanten Änderungen an der Funktion „Benutzerdefiniertes Steuerelement“ finden Sie im [Archiv für Neuerungen in Azure Active Directory](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls) für Februar 2020.

## <a name="creating-custom-controls"></a>Erstellen von benutzerdefinierten Steuerelementen

„Benutzerdefinierte Steuerelemente“ kann mit einer begrenzten Anzahl genehmigter Authentifizierungsanbieter verwendet werden. Um ein benutzerdefiniertes Steuerelement zu erstellen, sollten Sie sich zuerst an den Anbieter wenden, den Sie verwenden möchten. Jeder Nicht-Microsoft-Anbieter hat seine eigenen Prozesse und Anforderungen für das Registrieren, das Abonnieren, oder um auf andere Weise Teil des Diensts zu werden, und um anzugeben, dass Sie sich in den bedingten Zugriff integrieren möchten. Zu diesem Zeitpunkt wird der Anbieter einen Block von Daten im JSON-Format für Sie bereitstellen. Diese Daten ermöglichen dem Anbieter und dem bedingten Zugriff, für Ihren Mandanten zusammenzuarbeiten. Diese Daten erstellen das neue Steuerelement und definieren, wie der bedingte Zugriff feststellen kann, ob Ihre Benutzer die Überprüfung mit dem Anbieter erfolgreich ausgeführt haben.

Kopieren Sie die JSON-Daten, und fügen Sie sie in das entsprechende Textfeld ein. Verändern Sie JSON-Daten nicht, es sei denn, Sie verstehen explizit die Änderungen, die Sie vornehmen möchten. Jede Änderung kann die Verbindung zwischen dem Anbieter und Microsoft unterbrechen und möglicherweise den Zugriff auf Ihre Konten für Sie und Ihre Benutzer sperren.

Die Option zum Erstellen eines benutzerdefinierten Steuerelements befindet sich im Abschnitt **Verwalten** der Seite **Bedingter Zugriff**.

![Schnittstelle für benutzerdefinierte Steuerelemente beim bedingtem Zugriff](./media/controls/custom-controls-conditional-access.png)

Wenn Sie auf **New custom control** (Neues benutzerdefiniertes Steuerelement) klicken, wird ein Blatt geöffnet und ein Textfeld für die JSON-Daten des Steuerelements angezeigt.  

![Neues benutzerdefiniertes Steuerelement](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Löschen von benutzerdefinierten Steuerelementen

Um ein benutzerdefiniertes Steuerelement zu löschen, müssen Sie zunächst sicherstellen, dass es in keiner der Richtlinien für bedingten Zugriff verwendet wird. Ist der Vorgang einmal abgeschlossen:

1. Wechseln Sie zur Liste der benutzerdefinierten Steuerelemente
1. Klicken Sie auf „...“  
1. Klicken Sie auf **Löschen**.

## <a name="editing-custom-controls"></a>Bearbeiten von benutzerdefinierten Steuerelementen

Um ein benutzerdefiniertes Steuerelement zu bearbeiten, müssen Sie das aktuelle Steuerelement löschen und ein neues Steuerelement mit den aktualisierten Informationen erstellen.

## <a name="known-limitations"></a>Bekannte Einschränkungen

Es ist nicht möglich, benutzerdefinierte Steuerelemente mit der Identity Protection-Automatisierung zu verwenden, für die Azure AD Multi-Factor Authentication erforderlich ist. Sie können auch nicht mit der Self-Service-Kennwortzurücksetzung (SSPR) in Azure AD, bei der Anspruchsanforderungen der mehrstufigen Authentifizierung erfüllt werden, zum Heraufstufen von Rollen in Privileged Identity Management (PIM) oder im Rahmen der Registrierung von Intune-Geräten oder beim Einbinden von Geräten in Azure AD eingesetzt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)

- [Modus „Nur Bericht“](concept-conditional-access-report-only.md)

- [Simulieren des Anmeldeverhaltens mit dem Was-wäre-wenn-Tool für den bedingten Zugriff](troubleshoot-conditional-access-what-if.md)
