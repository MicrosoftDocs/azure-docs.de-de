---
title: Schützen des Zugriffs auf Azure Data Catalog
description: In diesem Artikel wird erläutert, wie Data Catalog und seine Datenobjekte geschützt werden.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6c09b509399647f4cacbc96427200da5a1b00ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61000752"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Schützen des Zugriffs auf Data Catalog und Datenobjekte
> [!IMPORTANT]
> Dieses Feature ist nur in der Standard Edition von Azure Data Catalog verfügbar.

Azure Data Catalog ermöglicht Ihnen das Angeben, wer Zugriff auf den Datenkatalog hat und welche Vorgänge (Registrieren, Anmerkungen hinzufügen, Besitz übernehmen) auf die Metadaten im Katalog angewendet werden dürfen. 

## <a name="catalog-users-and-permissions"></a>Katalogbenutzer und -berechtigungen
So gewähren Sie einem Benutzer oder einer Gruppe Zugriff auf einen Datenkatalog und werden Berechtigungen festgelegt:

1. Klicken Sie auf der [Startseite Ihres Datenkatalogs](https://www.azuredatacatalog.com) auf der Symbolleiste auf **Einstellungen**.

    ![Datenkatalog: Einstellungen](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. Erweitern Sie auf der Seite „Einstellungen“ den Abschnitt **Katalogbenutzer**.
    ![Katalogbenutzer: Hinzufügen](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. Klicken Sie auf **Hinzufügen**.
4. Geben Sie den vollständig qualifizierten **Benutzernamen** oder den Namen der **Sicherheitsgruppe** in Azure Active Directory (AAD) ein, die dem Katalog zugeordnet ist. Wenn Sie mehrere Benutzer oder Gruppen hinzufügen, verwenden Sie Kommas als Trennzeichen.
    ![Katalogbenutzer: Benutzer oder Gruppen](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. Drücken Sie die **EINGABETASTE** oder **TAB-TASTE**, um das Textfeld zu verlassen. 
6.  Überprüfen Sie, ob alle Berechtigungen (**Kommentieren**, **Registrieren** und **Besitz übernehmen**) diesen Benutzern oder Gruppen standardmäßig zugewiesen sind. Der Benutzer oder die Gruppe kann dann [Datenobjekte registrieren]( data-catalog-how-to-register.md), [kommentieren]( data-catalog-how-to-annotate.md) und [den Besitz von Datenobjekten übernehmen]( data-catalog-how-to-manage.md). 
    ![Katalogbenutzer: Standardberechtigungen](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  Um einen Benutzer oder eine Gruppe lediglich Lesezugriff auf den Katalog zu gewähren, deaktivieren Sie die Option **Kommentieren** für diesen Benutzer oder diese Gruppe. Wenn Sie dies tun, kann der Benutzer oder die Gruppe Datenobjekte im Katalog nicht kommentieren, sondern nur anzeigen. 
8.  Um einem Benutzer oder einer Gruppe das Registrieren von Datenobjekten zu verweigern, deaktivieren Sie die Option **Registrieren** für diesen Benutzer oder diese Gruppe.
9.  Um einem Benutzer oder einer Gruppe das Übernehmen des Besitzes eines Datenobjekts zu verweigern, deaktivieren Sie die Option **Besitz übernehmen** für diesen Benutzer oder diese Gruppe. 
10. Zum Löschen eines Benutzers oder einer Gruppe aus den Katalogbenutzern klicken Sie unten in der Liste für diesen Benutzer oder diese Gruppe auf das **x**. 
    ![Katalogbenutzer: Benutzer löschen](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > Es wird empfohlen, Katalogbenutzer Sicherheitsgruppen hinzuzufügen anstatt Benutzer direkt hinzuzufügen und Berechtigungen zuzuweisen. Fügen Sie Benutzer den Sicherheitsgruppen hinzu, die ihren Rollen und ihrem benötigten Zugriff auf den Katalog entsprechen.

## <a name="special-considerations"></a>Besondere Überlegungen

- Die Sicherheitsgruppen zugewiesenen Berechtigungen sind additiv. Angenommen, ein Benutzer gehört zu zwei Gruppen. Eine Gruppe hat die Berechtigung „Kommentieren“, die andere nicht. Außerdem hat der Benutzer die Berechtigung „Kommentieren“. 
- Die einem Benutzer explizit zugewiesenen Berechtigungen überschreiben die Berechtigungen für Gruppen, denen der Benutzer angehört. Angenommen, Sie haben im vorherigen Beispiel den Benutzer den Katalogbenutzern explizit hinzugefügt und nicht die Berechtigung „Kommentieren“ zugewiesen. Der Benutzer kann Datenobjekte nicht kommentieren, obwohl er Mitglied einer Gruppe ist, die die Berechtigung „Kommentieren“ hat.

## <a name="next-steps"></a>Nächste Schritte
- [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md)

