---
title: Erneutes Ausführen des Azure AD Connect-Installations-Assistenten | Microsoft-Dokumentation
description: Erläutert die Funktionsweise des Installations-Assistenten, wenn Sie ihn ein zweites Mal ausführen.
keywords: Beim erneuten Ausführen des Azure AD Connect-Installations-Assistenten haben Sie die Möglichkeit, die Wartungseinstellungen zu konfigurieren.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff2caae7cb387f4f0d88cf059d01ad28861b9ad
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348404"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect-Synchronisierung: Erneutes Ausführen des Installations-Assistenten
Beim erstmaligen Ausführen des Azure AD Connect-Installations-Assistenten werden Sie durch die Konfiguration Ihrer Installation geführt. Beim erneuten Ausführen des Installations-Assistenten werden Ihnen Wartungsoptionen angeboten.

Sie finden den Installations-Assistenten im Startmenü unter der Bezeichnung **Azure AD Connect**.

![Startmenü](./media/how-to-connect-installation-wizard/startmenu.png)

Wenn Sie den Installations-Assistenten starten, wird Ihnen eine Seite mit folgenden Optionen angezeigt:

![Seite mit einer Liste zusätzlicher Aufgaben](./media/how-to-connect-installation-wizard/additionaltasks.png)

Wenn Sie AD FS mit Azure AD Connect installiert haben, stehen Ihnen noch weitere Optionen zur Verfügung. Die zusätzlichen Optionen für AD FS sind unter [AD FS-Verwaltung](how-to-connect-fed-management.md#manage-ad-fs)dokumentiert.

Wählen Sie eine der Aufgaben aus, und klicken Sie auf **Weiter** , um fortzufahren.

> [!IMPORTANT]
> Solange der Installations-Assistent geöffnet ist, werden alle Vorgänge im Synchronisierungsmodul angehalten. Stellen Sie sicher, dass Sie den Installations-Assistenten schließen, sobald Sie Ihre Konfigurationsänderungen abgeschlossen haben.
>
>

## <a name="view-current-configuration"></a>Aktuelle Konfiguration anzeigen
Über diese Option erhalten Sie eine Schnellansicht Ihrer aktuell konfigurierten Optionen.

![Seite mit einer Liste aller Optionen und deren Status](./media/how-to-connect-installation-wizard/viewconfig.png)

Um zurückzugehen, klicken Sie auf **Zurück** . Mit einem Klick auf **Beenden**schließen Sie den Installations-Assistenten.

## <a name="customize-synchronization-options"></a>Synchronisierungsoptionen anpassen
Mit dieser Option nehmen Sie Änderungen an der Synchronisierungskonfiguration vor. Ihnen wird eine Teilmenge von Optionen im Installationspfad der Konfiguration angezeigt. Diese Option wird Ihnen auch dann angezeigt, wenn Sie zunächst die Expressinstallation verwendet haben.

* [Hinzufügen weiterer Verzeichnisse](how-to-connect-install-custom.md#connect-your-directories). Informationen zum Entfernen eines Verzeichnisses finden Sie unter [Löschen eines Connectors](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Ändern der Filterung von Domänen und Organisationseinheiten](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Entfernen gruppenspezifischer Filterregeln.
* [Ändern optionaler Features](how-to-connect-install-custom.md#optional-features).

Die weiteren Optionen aus der anfänglichen Installation können nicht geändert werden und sind nicht verfügbar. Die Optionen sind:

* Ändern des Attributs zur Verwendung für userPrincipalName und sourceAnchor.
* Ändern der Verknüpfungsmethode für Objekte aus einer anderen Gesamtstruktur.
* Aktivieren der gruppenbasierten Filterung.

## <a name="refresh-directory-schema"></a>Aktualisieren des Verzeichnisschemas
Diese Option wird verwendet, wenn Sie das Schema in einer Ihrer lokalen AD DS-Gesamtstrukturen verändert haben. Zum Beispiel haben Sie möglicherweise Exchange installiert oder ein Upgrade auf ein Windows Server 2012-Schema mit Geräteobjekten ausgeführt. In diesem Fall müssen Sie Azure AD Connect anweisen, das Schema erneut aus den AD DS zu lesen und den Cache zu aktualisieren. Diese Aktion generiert auch die Synchronisierungsregeln neu. Wenn Sie beispielsweise das Exchange-Schema hinzufügen, werden der Konfiguration die Synchronisierungsregeln für Exchange hinzugefügt.

Wenn Sie diese Option auswählen, werden alle Verzeichnisse in Ihrer Konfiguration aufgeführt. Sie können die Standardeinstellung beibehalten und alle Gesamtstrukturen aktualisieren oder die Auswahl einiger Gesamtstrukturen aufheben.

![Seite mit einer Liste aller Verzeichnisse in der Umgebung](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Stagingmodus konfigurieren
Mit dieser Option können Sie den Stagingmodus auf dem Server aktivieren und deaktivieren. Weitere Informationen zum Stagingmodus und dessen Verwendung finden Sie unter [Vorgänge](how-to-connect-sync-staging-server.md).

Die Option zeigt an, ob Staging derzeit aktiviert oder deaktiviert ist:  
![Option, die auch den aktuellen Status des Stagingmodus anzeigt](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Um den Status zu ändern, wählen Sie diese Option aus, und aktivieren bzw. deaktivieren Sie das Kontrollkästchen.  
![Option, die auch den aktuellen Status des Stagingmodus anzeigt](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Benutzeranmeldung ändern
Mit dieser Option können Sie zwischen Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund als Benutzeranmeldemethode wechseln. Ein Wechsel zu **Nicht konfigurieren**ist nicht möglich.

Weitere Informationen zu dieser Option finden Sie unter [Benutzeranmeldung](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu dem von Azure AD Connect verwendeten Konfigurationsmodell finden Sie unter [Understanding Declarative Provisioning](concept-azure-ad-connect-sync-declarative-provisioning.md)(Grundlegendes zur deklarativen Bereitstellung).

**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
