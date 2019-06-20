---
title: Verwalten von Benutzerdaten in Azure Active Directory B2C | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Benutzerdaten in Azure AD B2C löschen und exportieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eb99cd94dca2a463c275dd5d4fab540b4fc55318
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510448"
---
# <a name="manage-user-data-in-azure-active-directory-b2c"></a>Verwalten von Benutzerdaten in Azure Active Directory B2C

 Dieser Artikel beschreibt, wie Sie Benutzerdaten in Azure Active Directory (Azure AD) B2C mit den Vorgängen der [Azure Active Directory-Graph-API](/previous-versions/azure/ad/graph/api/api-catalog) verwalten. Beim Verwalten von Benutzerdaten werden u.a. Daten aus Überwachungsprotokollen gelöscht oder exportiert.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="delete-user-data"></a>Löschen von Benutzerdaten

Benutzerdaten werden im Azure AD B2C-Verzeichnis und in den Überwachungsprotokollen gespeichert. Alle Daten zur Benutzerüberwachung werden in Azure AD B2C 30 Tage lang aufbewahrt. Falls Sie Benutzerdaten innerhalb dieser 30-Tage-Frist löschen möchten, können Sie den Vorgang [Einen Benutzer löschen](/previous-versions/azure/ad/graph/api/users-operations#DeleteUser) verwenden. Ein DELETE-Vorgang ist für alle Azure AD B2C-Mandanten erforderlich, auf denen sich Daten befinden können. 

Jedem Benutzer in Azure AD B2C wird eine Objekt-ID zugewiesen. Mit der Objekt-ID erhalten Sie einen eindeutigen Bezeichner, den Sie zum Löschen von Benutzerdaten in Azure AD B2C verwenden können. Je nach Architektur kann die Objekt-ID auch ein nützlicher Korrelationsbezeichner für andere Dienste sein, z.B. Datenbanken in den Bereichen Finanzen, Marketing und Kundenbeziehungsmanagement. 

Der präziseste Ansatz zum Beschaffen der Objekt-ID für einen Benutzer ist das Abrufen im Rahmen eines Authentifizierungsablaufs mit Azure AD B2C. Wenn Sie eine gültige Datenanforderung von einem Benutzer über andere Methoden erhalten, ist unter Umständen ein Offlineprozess erforderlich, wie eine Suche nach einem Kundendienst-Agent, um den Benutzer zu finden und die zugeordnete Objekt-ID zu erhalten. 

Im folgenden Beispiel ist ein möglicher Ablauf zum Löschen von Daten dargestellt:

1. Der Benutzer meldet sich an und wählt **Meine Daten löschen**.
2. Die Anwendung enthält eine Option zum Löschen der Daten in einem Verwaltungsabschnitt.
3. Die Anwendung erzwingt eine Authentifizierung über Azure AD B2C. Azure AD B2C stellt für die Anwendung ein Token mit der Objekt-ID des Benutzers bereit. 
4. Die Anwendung empfängt das Token, und die Objekt-ID wird verwendet, um die Benutzerdaten über einen Aufruf der Azure AD-Graph-API zu löschen. Die Azure AD-Graph-API löscht die Benutzerdaten und gibt den Statuscode „200 OK“ zurück.
5. Die Anwendung orchestriert das Löschen der Benutzerdaten in anderen Organisationssystemen nach Bedarf, indem die Objekt-ID oder andere Bezeichner verwendet werden.
6. Die Anwendung bestätigt das Löschen der Daten und stellt die nächsten Schritte für den Benutzer bereit.

## <a name="export-customer-data"></a>Exportieren von Kundendaten

Der Prozess zum Exportieren von Kundendaten aus Azure AD B2C ähnelt dem Löschvorgang.

Azure AD B2C-Benutzerdaten sind auf folgende Daten beschränkt:

- **In Azure Active Directory gespeicherte Daten**: Sie können Daten über die User Journey einer Azure AD B2C-Authentifizierung abrufen, indem Sie die Objekt-ID oder einen beliebigen Anmeldenamen wie beispielsweise die E-Mail-Adresse oder den Benutzernamen verwenden. 
- **Benutzerspezifischer Bericht der Überwachungsereignisse**: Sie können Daten durch Verwendung der Objekt-ID indizieren.

Im folgenden Beispiel für einen Exportdatenfluss lassen sich die Schritte, die für die Anwendung beschrieben werden, entweder per Back-End-Prozess oder von einem Benutzer mit einer Administratorrolle im Verzeichnis ausführen:

1. Der Benutzer meldet sich an der Anwendung an. Azure AD B2C erzwingt bei Bedarf die Azure Multi-Factor Authentication.
2. Die Anwendung verwendet die Anmeldeinformationen des Benutzers, um einen Vorgang der Azure AD-Graph-API zum Abrufen von Benutzerattributen aufzurufen. Die Azure AD-Graph-API stellt die Attributdaten im JSON-Format bereit. Je nach Schema können Sie den ID-Tokeninhalt so festlegen, dass er alle persönlichen Daten eines Benutzers enthält.
3. Die Anwendung ruft die Überwachungsaktivitäten für den Endbenutzer ab. Die Azure AD-Graph-API stellt die Ereignisdaten für die Anwendung bereit.
4. Die Anwendung aggregiert die Daten und stellt sie für den Benutzer zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen dazu, wie Benutzer auf Ihre Anwendung zugreifen können, finden Sie unter [Verwalten des Benutzerzugriffs](manage-user-access.md).




