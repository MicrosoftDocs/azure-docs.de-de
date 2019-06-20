---
title: Problembehandlung bei fehlerhafter Attributsynchronisierung in Azure AD Connect | Microsoft-Dokumentation
description: Dieses Thema enthält Schritte zum Beheben von Problemen bei der Attributsynchronisierung mithilfe der Aufgaben zur Problembehandlung.
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
ms.openlocfilehash: a639b14c9313179816f6376aa0c5642a645ea344
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60455970"
---
# <a name="troubleshoot-an-attribute-not-synchronizing-in-azure-ad-connect"></a>Problembehandlung bei fehlerhafter Attributsynchronisierung in Azure AD Connect

## <a name="recommended-steps"></a>**Empfohlene Schritte**

Vor dem Untersuchen von Attributsynchronisierungsproblemen ist es wichtig, den Synchronisierungsprozess in **Azure AD Connect** zu verstehen:

  ![Azure AD Connect-Synchronisierungsprozess](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connectorbereich, eine Tabelle in der Datenbank.
* **MV:** Metaverse, eine Tabelle in der Datenbank.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synchronisierungsschritte**

* Aus AD importieren: Active Directory-Objekte werden in den AD-Connectorbereich (AD CS) eingefügt.

* Aus AAD importieren: Azure Active Directory-Objekte werden in den AAD-Connectorbereich (AAD CS) eingefügt.

* Synchronisierung: Es werden **eingehende Synchronisierungsregeln** und ausgehende Synchronisierungsregeln in der Reihenfolge der Priorität von niedrig zu hoch ausgeführt. Informationen zu den Synchronisierungsregeln finden Sie im **Synchronisierungsregel-Editor** in der Desktopanwendungen. Die **eingehenden Synchronisierungsregeln** übertragen den Daten aus CS zu MV. Die **ausgehenden Synchronisierungsregeln** verschieben Daten aus MV zu CS.

* In AD exportieren: Nach der Synchronisierung werden Objekte aus AD CS in das **Active Directory** exportiert.

* In AAD exportieren: Nach der Synchronisierung werden Objekte aus AAD CS in das **Azure Active Directory** exportiert.

### <a name="step-by-step-investigation"></a>**Schrittweise Untersuchung**

* Zunächst beginnen Sie die Suche im **Metaverse** und sehen sich die Attributzuordnung von der Quelle zum Ziel an.

* Starten Sie **Synchronization Service Manager** aus den Desktopanwendungen, wie unten gezeigt:

  ![Starten von Synchronization Service Manager](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/startmenu.png)

* Wählen Sie in **Synchronization Service Manager** die Option **Metaverse-Suche** und dann **Bereich nach Objekttyp** aus, wählen Sie dann mithilfe eines Attributs das Objekt aus, und klicken Sie auf die Schaltfläche **Suchen**.

  ![Metaverse Search](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvsearch.png)

* Doppelklicken Sie auf das Objekt, das über die **Metaverse-Suche** gefunden wurde, um alle zugehörigen Attribute anzuzeigen. Sie können auf die Registerkarte **Connectors** klicken, um das entsprechende Objekt in allen **Connectorbereichen** anzuzeigen.

  ![Metaverse-Objekt-Connectors](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvattributes.png)

* Doppelklicken Sie auf **Active Directory-Connector**, um die Attribute im **Connectorbereich** anzuzeigen. Klicken Sie auf die Schaltfläche **Vorschau**, und klicken Sie im folgenden Dialogfeld auf die Schaltfläche **Vorschau generieren**.

  ![Attribute im Connectorbereich](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/csattributes.png)

* Klicken Sie nun auf **Import-Attributfluss**. Dadurch wird der Fluss der Attribute vom **Active Directory-Connectorbereich** zum **Metaverse** angezeigt. Aus der Spalte **Synchronisierungsregel** geht hervor, welche **Synchronisierungsregel** zu diesem Attribut geführt hat. In der Spalte **Datenquelle** werden die Attribute aus dem **Connectorbereich** angezeigt. Die Spalte **Metaverse-Attribut** enthält die Attribute im **Metaverse**. Hier können Sie nach dem Attribut suchen, das nicht synchronisiert wird. Wenn Sie das Attribut hier nicht finden, ist es nicht zugeordnet, und Sie müssen eine neue benutzerdefinierte **Synchronisierungsregel** erstellen, um das Attribut zuzuordnen.

  ![Attribute im Connectorbereich](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/cstomvattributeflow.png)

* Klicken Sie im linken Bereich auf **Export-Attributfluss**, um mithilfe der **Ausgehenden Synchronisierungsregeln** den Attributfluss vom **Metaverse** zurück zum **Active Directory-Connectorbereich** anzuzeigen.

  ![Attribute im Connectorbereich](media/tshoot-connect-attribute-not-syncing/tshoot-connect-attribute-not-syncing/mvtocsattributeflow.png)

* Auf vergleichbare Weise können Sie das Objekt im **Azure Active Directory-Connectorbereich** anzeigen und die **Vorschau** generieren, um den Attributfluss vom **Metaverse** zum **Connectorbereich** und umgekehrt anzuzeigen. Auf diese Weise können Sie untersuchen, warum ein Attribut nicht synchronisiert wird.

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
