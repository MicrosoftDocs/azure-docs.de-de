---
title: Massendownload der Gruppenmitgliedschaftsliste – Azure Active Directory-Portal | Microsoft-Dokumentation
description: Fügen Sie Benutzer in einem Massenvorgang im Azure Admin Center hinzu.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4055fb3bfdbcfd7e4a74d9290017a6193578146
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547695"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Massendownload von Mitgliedern einer Gruppe in Azure Active Directory

Im Azure Active Directory-Portal (Azure AD) können Sie die Mitglieder einer Gruppe in Ihrer Organisation per Massenvorgang in eine CSV-Datei (Comma-Separated Values, durch Trennzeichen getrennte Werte) herunterladen.

## <a name="to-bulk-download-group-membership"></a>So führen Sie einen Massendownload der Gruppenmitgliedschaft durch

1. Melden Sie sich mit dem Konto eines Benutzeradministrators in der Organisation beim [Azure-Portal](https://portal.azure.com) an. Gruppenbesitzer können Mitglieder von Gruppen, die ihnen gehören, ebenfalls in einem Massenvorgang herunterladen.
1. Wählen Sie in Azure AD **Gruppen** > **Alle Gruppen** aus.
1. Öffnen Sie die Gruppe, deren Mitgliedschaft Sie herunterladen möchten, und wählen Sie dann **Mitglieder** aus.
1. Wählen Sie auf der Seite **Mitglieder** die Option **Mitglieder herunterladen** aus, um eine CSV-Datei mit der Auflistung der Gruppenmitglieder herunterzuladen.

   ![Der Befehl „Mitglieder herunterladen“ ist auf der Profilseite für die Gruppe zu finden.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Überprüfen des Downloadstatus

Sie können den Status aller Ihrer ausstehenden Massenanforderungen auf der Seite **Ergebnisse von Massenvorgängen** anzeigen.

[![Überprüfen des Status auf der Seite „Ergebnisse von Massenvorgängen“](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Grenzwerte für den Massendownloaddienst

Jede Massenaktivität zum Herunterladen einer Liste von Gruppenmitgliedern kann bis zu einer Stunde dauern. Auf diese Weise können Sie eine Liste von mindestens 500.000 Mitgliedern herunterladen.

## <a name="next-steps"></a>Nächste Schritte

- [Massenimport von Gruppenmitgliedern ausführen](groups-bulk-import-members.md)
- [Massenentfernung von Gruppenmitgliedern](groups-bulk-download-members.md)
