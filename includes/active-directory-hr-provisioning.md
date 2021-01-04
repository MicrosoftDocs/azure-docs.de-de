---
author: billmath
ms.service: active-directory
ms.subservice: governance
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: be33e492f44f6926f2ae2d133cf112245fba15f1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134949"
---
## <a name="cloud-hr-application-to-azure-active-directory-user-provisioning"></a>HR-Cloudanwendung für die Azure Active Directory-Benutzerbereitstellung

In der Vergangenheit haben sich IT-Mitarbeiter zum Erstellen, Aktualisieren und Löschen von Mitarbeitern auf manuelle Methoden verlassen. Zum Synchronisieren von Mitarbeiterdaten wurden Methoden wie das Hochladen von CSV-Dateien oder benutzerdefinierte Skripts verwendet. Diese Bereitstellungsprozesse sind fehleranfällig, unsicher und schwer zu verwalten.

Zum Verwalten der Identitätszyklen von Mitarbeitern, Anbietern oder vorübergehend Beschäftigten bietet der [Azure Active Directory (Azure AD)-Benutzerbereitstellungsdienst](../articles/active-directory/app-provisioning/user-provisioning.md) die Integration mit cloudbasierten Anwendungen für das Personalwesen (Human Resources, HR). Als Beispiele sind hier Workday oder SuccessFactors zu nennen.

Azure AD nutzt diese Integration, um die folgenden Workflows für HR-Cloudanwendungen (Apps) zu ermöglichen:

- **Bereitstellen von Benutzern in Azure Active Directory:** Ausgewählte Benutzergruppen aus einer HR-Cloud-App werden in einer oder mehreren Azure Active Directory-Domänen bereitgestellt.
- **Bereitstellen reiner Cloudbenutzer in Azure AD:** In den Fällen, in denen Azure Active Directory nicht verwendet wird, werden Benutzer direkt aus der HR-Cloud-App in Azure AD bereitgestellt.
- **Rückschreiben in die HR-Cloud-App** : E-Mail-Adressen und Benutzernamenattribute werden aus Azure AD zurück in die HR-Cloud-App geschrieben.


## <a name="enabled-hr-scenarios"></a>Unterstützte HR-Szenarien

Der Azure AD-Benutzerbereitstellungsdienst ermöglicht die Automatisierung der folgenden HR-basierten Identity Lifecycle Management-Szenarien:

- **Einstellen neuer Mitarbeiter:** Wenn Sie einen neuen Mitarbeiter zur HR-Cloud-App hinzufügen, wird in Azure Active Directory und Azure AD automatisch ein Benutzerkonto mit der Option erstellt, E-Mail-Adress- und Benutzernamenattribute in die HR-Cloud-App rückzuschreiben.
- **Aktualisieren von Mitarbeiterattributen und -profilen:** Wenn Sie in der HR-Cloud-App einen Mitarbeiterdatensatz (z. B. Name, Titel oder Vorgesetzter) aktualisieren, wird das entsprechende Benutzerkonto automatisch in Azure Active Directory und Azure AD aktualisiert.
- **Kündigen von Mitarbeitern:** Wenn Sie das Arbeitsverhältnis eines Mitarbeiters in der HR-Cloud-App beenden, wird das entsprechende Benutzerkonto in Azure Active Directory und Azure AD automatisch deaktiviert.
- **Erneutes Einstellen von Mitarbeitern:** Wenn Sie einen Mitarbeiter in der HR-Cloud-App erneut einstellen, kann sein altes Konto in Azure Active Directory und Azure AD automatisch reaktiviert bzw. erneut bereitgestellt werden.

## <a name="who-is-this-integration-best-suited-for"></a>Für wen ist diese Integration am besten geeignet?

Die Integration der HR-Cloud-App mit der Azure AD-Benutzerbereitstellung ist ideal für Unternehmen geeignet, für die Folgendes gilt:

- Sie wünschen sich eine vorgefertigte, cloudbasierte Lösung für die HR-Benutzerbereitstellung in der Cloud.
- Benutzer müssen direkt aus der HR-Cloud-App in Azure Active Directory oder Azure AD bereitgestellt werden.
- Benutzer müssen mithilfe von Daten bereitgestellt werden, die aus der HR-Cloud-App abgerufen werden.
- Benutzer müssen bei Einstellungen, Wechseln und Kündigungen nur auf Grundlage von Änderungsinformationen, die in der HR-Cloud-App erkannt werden, mit einer oder mehreren Azure Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden.
- Sie verwenden Office 365 für E-Mail-Funktionen.


### <a name="key-benefits"></a>Hauptvorteile

Diese Funktion der vom Personalwesen (HR) gesteuerten IT-Bereitstellung bietet erhebliche Geschäftsvorteile, wie nachfolgend aufgeführt:

- **Produktivitätssteigerung:** Sie können jetzt die Zuweisung von Benutzerkonten und Office 365-Lizenzen automatisieren und den Zugriff auf Schlüsselgruppen ermöglichen. Durch die Automatisierung von Zuweisungen haben Neueinstellungen sofortigen Zugriff auf ihre Arbeitstools, und die Produktivität erhöht sich.
- **Risikomanagement:** Sie können die Sicherheit erhöhen, indem Sie Änderungen anhand von Mitarbeiterstatus oder Gruppenmitgliedschaft mit Daten, die aus der HR-Cloud-App einfließen, automatisieren. Die Automatisierung von Änderungen stellt sicher, dass Benutzeridentitäten und der Zugriff auf wichtige Anwendungen automatisch aktualisiert werden, wenn Benutzer innerhalb des Unternehmen wechseln oder es verlassen.
- **Umsetzen von Compliance und Governance** : Azure AD unterstützt native Überwachungsprotokolle für Benutzerbereitstellungsanforderungen, die von Apps aus dem Quell- und Zielsystem erfolgen. Durch die Überwachung können Sie über einen einzigen Bildschirm nachverfolgen, wer Zugriff auf die Apps hat.
- **Kostenmanagement:** Durch Vermeiden von Ineffizienzen und menschlichen Fehlern, die bei einer manuellen Bereitstellung entstehen, reduziert die automatische Benutzerbereitstellung die Kosten. Sie reduziert auch den Bedarf an benutzerdefiniert entwickelten Lösungen für die Benutzerbereitstellung, die im Laufe der Zeit mithilfe älterer und veralteter Plattformen erstellt wurden.
