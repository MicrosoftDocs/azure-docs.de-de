---
title: Connectors der Benutzeroberfläche von Azure AD Synchronization Service Manager | Microsoft Docs
description: Grundlegende Informationen zur Registerkarte „Connectors“ in Synchronization Service Manager für Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae932191c7b76590ea217386dfd729add5566f87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60384108"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Verwenden von Connectors mit dem Azure AD Connect Synchronization Service Manager

![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

Die Registerkarte „Connectors“ wird verwendet, um alle Systeme zu verwalten, mit denen das Synchronisierungsmodul verbunden ist.

## <a name="connector-actions"></a>Connectoraktionen
| Aktion | Comment |
| --- | --- |
| Erstellen |Nicht verwenden. Verwenden Sie den Installations-Assistenten zum Verbinden mit weiteren Active Directory-Gesamtstrukturen. |
| Eigenschaften |Wird zum Filtern von Domänen und Organisationseinheiten verwendet. |
| [Löschen](#delete) |Wird zum Löschen der Daten im Connectorbereich oder zum Löschen der Verbindung mit einer Gesamtstruktur verwendet. |
| [Ausführungsprofile konfigurieren](#configure-run-profiles) |Hier muss nur das Filtern von Domänen konfiguriert werden. Sie können diese Aktion verwenden, um bereits konfigurierte Ausführungsprofile anzuzeigen. |
| Ausführen |Wird zum Starten einer einmaligen Ausführung eines Profils verwendet. |
| Beenden |Beendet einen Connector, der gerade ein Profil ausführt. |
| Connector exportieren |Nicht verwenden. |
| Connector importieren |Nicht verwenden. |
| Connector aktualisieren |Nicht verwenden. |
| Schema aktualisieren |Aktualisiert das zwischengespeicherte Schema. Es wird empfohlen, die Option im Installations-Assistenten zu verwenden, da damit auch die Synchronisierungsregeln aktualisiert werden. |
| [Connectorbereich durchsuchen](#search-connector-space) |Wird verwendet, um Objekte zu finden und um ein Objekt und dessen Daten durch das System zu verfolgen. |

### <a name="delete"></a>Löschen
Die Löschaktion wird für zwei verschiedene Dinge verwendet.  
![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

Mit der Option **Delete connector space only** (Nur Connectorbereich löschen) werden alle Daten entfernt, die Konfiguration bleibt aber erhalten.

Mit der Option **Delete Connector and connector space** (Connector und Connectorbereich löschen) werden sowohl die Daten als auch die Konfiguration entfernt. Diese Option wird verwendet, wenn Sie mit einer Gesamtstruktur keine Verbindung mehr herstellen möchten.

Mit beiden Optionen werden alle Objekte synchronisiert und die Metaverse-Objekte aktualisiert. Bei dieser Aktion handelt es sich um einen Vorgang mit langer Ausführungsdauer.

### <a name="configure-run-profiles"></a>Ausführungsprofile konfigurieren
Mit dieser Option können Sie die für einen Connector konfigurierten Ausführungsprofile anzeigen.

![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Connectorbereich durchsuchen
Die Aktion zum Durchsuchen des Connectorbereichs ist nützlich, um nach Objekten zu suchen und Probleme zu behandeln.

![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Wählen Sie zuerst einen **Bereich**aus. Sie können basierend auf Daten (RDN, DN, Anker, Teilstruktur) oder des Zustands des Objekts (alle anderen Optionen) suchen.  
![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
Wenn Sie beispielsweise eine Unterstruktur durchsuchen, erhalten Sie alle Objekte in einer Organisationseinheit.  
![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
In dieser Tabelle können Sie ein Objekt auswählen, **Eigenschaften** auswählen und das Objekt aus dem Quellconnectorbereich durch das Metaverse bis zum Zielconnectorbereich [verfolgen](tshoot-connect-object-not-syncing.md).

### <a name="changing-the-ad-ds-account-password"></a>Ändern des AD DS-Kontokennworts
Wenn Sie das Kontokennwort ändern, kann Synchronisierungsdienst Änderungen an einem lokalen AD nicht mehr importieren oder exportieren.   Folgendes könnte angezeigt werden:

- Der Schritte zum Importieren/Exportieren für den AD-Connector löst den Fehler „no-start-credentials“ aus.
- In der Windows-Ereignisanzeige enthält das Anwendungsereignisprotokoll einen Fehler mit der Ereignis-ID 6000 und der Meldung „Der Verwaltungs-Agent „contoso.com“ konnte nicht ausgeführt werden, da die Anmeldeinformationen ungültig waren“.

Um das Problem zu beheben, aktualisieren Sie das AD DS-Benutzerkonto wie folgt:


1. Starten Sie Synchronization Service Manager („START“ > „Synchronization Service“).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Wechseln Sie zur Registerkarte **Connectors**.
3. Wählen Sie den AD-Connector aus, der für das Verwenden des AD DS-Kontos konfiguriert ist.
4. Wählen Sie unter „Aktionen“ die Option **Eigenschaften** aus.
5. Wählen Sie im Popup-Dialogfeld „Mit Active Directory-Gesamtstruktur verbinden“ aus:
6. Der Name der Gesamtstruktur gibt das zugehörige lokale AD an.
7. Der Benutzername gibt das AD DS-Konto an, das für die Synchronisierung verwendet wird.
8. Geben Sie das neue Kennwort des AD DS-Kontos in das Textfeld „Kennwort“ im ![Hilfsprogramm für den Verschlüsselungsschlüssel für die Azure AD Connect-Synchronisierung](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png) ein.
9. Klicken Sie auf „OK“, um das neue Kennwort zu speichern, und starten Sie den Synchronisierungsdienst neu, um das alte Kennwort aus dem Speichercache zu entfernen.



## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
