---
title: Installieren von Azure AD Connect mit delegierten SQL-Administratorberechtigungen | Microsoft-Dokumentation
description: In diesem Thema wird ein Update für Azure AD Connect beschrieben, das die Installation mit einem Konto ermöglicht, das lediglich über SQL-dbo-Berechtigungen verfügt.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60243547"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Installieren von Azure AD Connect mit delegierten SQL-Administratorberechtigungen
Vor dem neuesten Azure AD Connect-Build wurde das Delegieren von Administratorrechten bei der Bereitstellung von Konfigurationen, die SQL erforderten, nicht unterstützt.  Benutzer, die Azure AD Connect installieren wollten, benötigten Serveradministratorberechtigungen („SA“) auf dem SQL-Server.

Mit dem neuesten Release von Azure AD Connect kann der SQL-Administrator nun eine Out-of-Band-Datenbankbereitstellung ausführen, sodass die Datenbank anschließend vom Azure AD Connect-Administrator mit Datenbankbesitzerrechten installiert werden kann.

## <a name="before-you-begin"></a>Voraussetzungen
Um dieses Feature verwenden zu können, müssen Sie beachten, dass es mehrere variable Komponenten gibt, für die jeweils ein anderer Administrator in Ihrer Organisation verantwortlich sein kann.  In der folgenden Tabelle werden die einzelnen Rollen und ihre jeweiligen Aufgaben bei der Bereitstellung von Azure AD Connect mit diesem Feature zusammengefasst.

|Rolle|BESCHREIBUNG|
|-----|-----|
|AD-Administrator der Domäne oder Gesamtstruktur|Erstellt das Dienstkonto auf Domänenebene, das von Azure AD Connect zum Ausführen des Synchronisierungsdiensts verwendet wird.  Weitere Informationen zu Dienstkonten finden Sie unter [Konten und Berechtigungen](reference-connect-accounts-permissions.md).
|SQL-Administrator|Erstellt die ADSync-Datenbank und gewährt dem Azure AD Connect-Administrator und dem Dienstkonto, das vom Administrator der Domäne/Gesamtstruktur erstellt wurde, Anmelde- und dbo-Berechtigungen|
Azure AD Connect-Administrator|Installiert Azure AD Connect und gibt während der benutzerdefinierten Installation das Dienstkonto an

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Schritte zum Installieren von Azure AD Connect mit delegierten SQL-Berechtigungen
Gehen Sie zum Out-of-Band-Bereitstellen der Datenbank und Installieren von Azure AD Connect mit Besitzerberechtigungen für die Datenbank wie folgt vor.

>[!NOTE]
>Obwohl es nicht erforderlich ist, wird **dringend empfohlen**, beim Erstellen der Datenbank als Sortierung Latin1_General_CI_AS auszuwählen.


1. Lassen Sie die ADSync-Datenbank durch den SQL-Administrator mit einer Sortierreihenfolge unter Berücksichtigung der Groß-/Kleinschreibung **(Latin1_General_CI_AS)** erstellen.  Die Datenbank muss den Namen **ADSync** tragen.  Wiederherstellungsmodell, Kompatibilitätsgrad und Einschlusstyp werden auf die richtigen Werte festgelegt, wenn Azure AD Connect installiert wird.  Die Sortierreihenfolge muss jedoch vom SQL-Administrator ordnungsgemäß festgelegt werden, da andernfalls Azure AD Connect die Installation blockiert.  Zum Wiederherstellen muss der Dienstadministrator die Datenbank löschen und neu erstellen.
 
   ![Collation](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Gewähren Sie dem Azure AD Connect-Administrator und dem Domänendienstkonto die folgenden Berechtigungen:
   - SQL-Anmeldung 
   - **Datenbankbesitzerrechte (dbo)**
 
   ![Berechtigungen](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect unterstützt keine Anmeldungen mit einer geschachtelten Mitgliedschaft.  Dies bedeutet, dass Ihr Azure AD Connect-Administratorkonto und Ihr Domänendienstkonto mit einem Login verknüpft sein müssen, dem dbo-Rechte gewährt werden.  Es kann nicht einfach ein Mitglied einer Gruppe sein, die einer Anmeldung mit dbo-Rechten zugewiesen ist.

3. Senden Sie eine E-Mail mit den Namen für den SQL-Server und die Instanz, die bei der Installation von Azure AD Connect verwendet werden sollen, an den Azure AD Connect-Administrator.

## <a name="additional-information"></a>Zusätzliche Informationen
Nachdem die Datenbank bereitgestellt wurde, kann der Azure AD Connect-Administrator die lokale Synchronisierung nach Bedarf installieren und konfigurieren.

Wenn der SQL-Administrator die ADSync-Datenbank aus einer früheren Azure AD Connect-Sicherung wiederhergestellt hat, müssen Sie den neuen Azure AD Connect-Server unter Verwendung einer vorhandenen Datenbank installieren. Weitere Informationen zur Installation von Azure AD Connect mit einer vorhandenen Datenbank finden Sie unter [Installieren von Azure AD Connect mit einer vorhandenen ADSync-Datenbank](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>Nächste Schritte
- [Erste Schritte mit Azure AD Connect mit Expresseinstellungen](how-to-connect-install-express.md)
- [Benutzerdefinierte Installation von Azure AD Connect](how-to-connect-install-custom.md)
- [Installieren von Azure AD Connect mithilfe einer vorhandenen ADSync-Datenbank](how-to-connect-install-existing-database.md)  
