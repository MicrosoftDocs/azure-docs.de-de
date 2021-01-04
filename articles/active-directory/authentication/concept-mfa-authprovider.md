---
title: Azure Multi-Factor Authentication-Anbieter – Azure Active Directory
description: Wann sollten Sie einen Auth-Anbieter mit Azure MFA verwenden?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c8454d2ca83d4f406149e7eb73feb19ce59554f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744124"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Erste Schritte mit einem Azure Multi-Factor Authentication-Anbieter

> [!IMPORTANT]
> Ab dem 1. September 2018 können keine neuen Authentifizierungsanbieter mehr erstellt werden. Vorhandene Authentifizierungsanbieter können ggf. weiterhin verwendet und aktualisiert werden, aber Migration ist nicht mehr möglich. Die mehrstufige Authentifizierung ist in Azure AD Premium-Lizenzen weiterhin als Feature verfügbar.

Die zweistufige Überprüfung ist standardmäßig für globale Administratoren, die mit Azure Active Directory arbeiten, und für Microsoft 365-Benutzer verfügbar. Falls Sie die [erweiterten Funktionen](howto-mfa-mfasettings.md) nutzen möchten, müssen Sie die Vollversion von Azure Multi-Factor Authentication (MFA) erwerben.

Ein Azure Multi-Factor Authentication-Anbieter wird verwendet, um Features zu nutzen, die von Azure MFA für Benutzer bereitgestellt werden, die **keine Lizenzen haben**.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Einschränkungen im Zusammenhang mit dem Azure MFA SDK

Beachten Sie, dass das SDK veraltet ist und nur noch bis zum 14. November 2018 funktioniert. Nach diesem Zeitpunkt führen Aufrufe an das SDK zu Fehlern.

## <a name="what-is-an-mfa-provider"></a>Was ist ein MFA-Anbieter?

Es gibt zwei Arten von Authentifizierungsanbietern. Der Unterschied besteht darin, wie Ihr Azure-Abonnement in Rechnung gestellt wird. Bei der Option pro Authentifizierung wird die Anzahl der Authentifizierungen berechnet, die für Ihren Mandanten in einem Monat ausgeführt werden. Diese Option ist am besten geeignet, wenn sich mehrere Benutzer nur gelegentlich authentifizieren. Bei der Option pro Benutzer wird die Anzahl der Personen in Ihrem Mandanten berechnet, die in einem Monat die zweistufige Überprüfung ausführen. Diese Option empfiehlt sich, wenn Sie über einige Benutzer mit Lizenzen verfügen, MFA aber auf weitere Benutzer außerhalb Ihrer Lizenzierungsgrenzwerte erweitern müssen.

## <a name="manage-your-mfa-provider"></a>Verwalten Ihres MFA-Anbieters

Das Nutzungsmodell (pro aktiviertem Benutzer oder pro Authentifizierung) kann nach der Erstellung eines MFA-Anbieters nicht mehr geändert werden.

Sofern die Anzahl erworbenen Lizenzen für alle Benutzer ausreicht, die für MFA aktiviert sind, können Sie den MFA-Anbieter auch ganz löschen.

Falls Ihr MFA-Anbieter nicht mit einem Azure AD-Mandanten verknüpft ist oder Sie den neuen MFA-Anbieter mit einem anderen Azure AD-Mandanten verknüpfen, werden Benutzereinstellungen und Konfigurationsoptionen nicht übernommen. Darüber hinaus müssen vorhandene Azure MFA-Server unter Verwendung von Aktivierungsanmeldeinformationen des MFA-Anbieters erneut aktiviert werden.

### <a name="removing-an-authentication-provider"></a>Entfernen eines Authentifizierungsanbieters

> [!CAUTION]
> Beim Löschen eines Authentifizierungsanbieters wird keine Bestätigung angezeigt. **Löschen** ist ein endgültiger Prozess.

Authentifizierungsanbieter finden Sie hier: **Azure-Portal** > **Azure Active Directory** > **Sicherheit** > **MFA** > **Anbieter**. Klicken Sie auf einen aufgeführten Anbieter, um die zugehörigen Details und Konfigurationen anzuzeigen.

Überprüfen Sie vor dem Entfernen eines Authentifizierungsanbieters zunächst alle benutzerdefinierten Einstellungen, die ggf. für Ihren Anbieter konfiguriert wurden. Überlegen Sie sich, welche Einstellungen von Ihrem Anbieter zu allgemeinen MFA-Einstellungen migriert werden müssen, und migrieren Sie sie. 

Mit Anbietern verknüpfte MFA-Server müssen unter Verwendung der Anmeldeinformationen reaktiviert werden, die unter **Azure-Portal** > **Azure Active Directory** > **Sicherheit** > **MFA** > **Servereinstellungen** generiert wurden. Vor der Reaktivierung müssen auf Azure MFA-Servern in Ihrer Umgebung folgende Dateien aus dem Verzeichnis `\Program Files\Multi-Factor Authentication Server\Data\` gelöscht werden:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Löschen eines Authentifizierungsanbieters über das Azure-Portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

Wenn Sie sich vergewissert haben, dass alle Einstellungen migriert wurden, können Sie unter **Azure-Portal** > **Azure Active Directory** > **Sicherheit** > **MFA** > **Anbieter** die Auslassungspunkte **...** und anschließend **Löschen** auswählen.

> [!WARNING]
> Beim Löschen eines Authentifizierungsanbieters werden alle diesem Anbieter zugeordneten Berichtsinformationen gelöscht. Es empfiehlt sich gegebenenfalls, Aktivitätsberichte vor dem Löschen des Anbieters zu speichern.

> [!NOTE]
> Benutzer mit älteren Versionen von Microsoft Authenticator-App und Azure MFA-Server müssen ihre App möglicherweise neu registrieren.

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren von Azure Multi-Factor Authentication-Einstellungen](howto-mfa-mfasettings.md)
