---
title: Anpassen eine Synchronisierungsregel – Azure AD Connect | Microsoft-Dokumentation
description: Dieses Thema enthält Schritte zum Behandeln von Problemen bei der Installation von Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a65d4c477d0e3aa9d5feea53e3e667ece651c83f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60351047"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Anpassen einer Synchronisierungsregel

## <a name="recommended-steps"></a>**Empfohlene Schritte**

Sie können den Synchronisierungsregel-Editor verwenden, um eine Synchronisierungsregel zu bearbeiten oder eine neue Regel zu erstellen. Um Änderungen an Synchronisierungsregeln vornehmen zu können, müssen Sie ein fortgeschrittener Benutzer sein. Falsche Änderungen können zur Löschung von Objekten in Ihrem Zielverzeichnis führen. Lesen Sie noch einmal [Empfohlene Dokumente](#recommended-documents), um sich das für Synchronisierungsregeln notwendige Fachwissen anzueignen. Führen Sie die folgenden Schritte aus, um eine Synchronisierungsregel zu ändern:

* Starten Sie den Synchronisierungsregel-Editor im Anwendungsmenü auf dem Desktop, wie unten dargestellt:

    ![Menü „Synchronisierungsregel-Editor“](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Um eine Standardsynchronisierungsregel anzupassen, klonen Sie die vorhandene Regel, indem Sie im Synchronisierungsregel-Editor auf die Schaltfläche „Bearbeiten“ klicken. Dadurch wird eine Kopie der Standardregel erstellt und diese deaktiviert. Speichern Sie die geklonte Regel mit einer Priorität unter 100.  Die Priorität bestimmt im Fall eines Attributflusskonflikts, welche Regel bei der Konfliktlösung den Vorrang hat (d.h. welche Regel den niedrigeren numerischen Wert hat).

    ![Synchronisierungsregel-Editor](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Wenn Sie ein bestimmtes Attribut ändern, sollten Sie im Idealfall nur das geänderte Attribut in der geklonten Regel beibehalten.  Aktivieren Sie dann die Standardregel so, dass das geänderte Attribut aus der geklonten Regel und andere Attribute aus der Standardregel bezogen werden. 

* Dabei ist Folgendes zu beachten: Wenn der berechnete Wert des geänderten Attributs in der geklonten Regel NULL und in der Standardregel NICHT NULL beträgt, hat der NICHT-NULL-Wert Vorrang und ersetzt den NULL-Wert. Wenn ein NULL-Wert nicht durch einen NICHT-NULL-Wert ersetzt werden soll, weisen Sie dem Wert in der geklonten Regel „AuthoritativeNull“ zu.

* Wenn Sie eine **ausgehende** Regel ändern möchten, ändern Sie den Filter im Synchronisierungsregel-Editor.

## <a name="recommended-documents"></a>**Empfohlene Dokumente**
* [Azure AD Connect-Synchronisierung: Technische Konzepte](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Azure AD Connect-Synchronisierung: Grundlagen der Architektur](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Azure AD Connect-Synchronisierung: Grundlegendes zur deklarativen Bereitstellung](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect-Synchronisierung: Grundlegendes zu Ausdrücken für die deklarative Bereitstellung](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Azure AD Connect-Synchronisierung: Grundlegendes zur Standardkonfiguration](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Azure AD Connect-Synchronisierung: Grundlegendes zu Benutzern, Gruppen und Kontakten](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect-Synchronisierung: Schattenattribute](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md)
- [Was ist eine Hybrididentität?](whatis-hybrid-identity.md)
