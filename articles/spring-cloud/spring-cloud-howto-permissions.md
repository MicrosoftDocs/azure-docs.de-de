---
title: Verwenden von Berechtigungen in Azure Spring Cloud
description: In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen für Berechtigungen in Azure Spring Cloud erstellen.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498641"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Verwenden von Berechtigungen in Azure Spring Cloud
In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen erstellen, die Berechtigungen an Azure Spring Cloud-Ressourcen vergeben. Benutzerdefinierte Rollen erweitern [integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md) um verschiedene Bestandsberechtigungen.

Die folgenden benutzerdefinierten Rollen werden implementiert:

* **Entwicklerrolle**: 
    * Bereitstellen
    * Testen
    * Apps neu starten
    * Kann App-Konfigurationen im Git-Repository anwenden und ändern
    * Kann den Protokolldatenstrom abrufen
* **Ops – Websitezuverlässigkeits-Techniker (SRE)** : 
    * Apps neu starten
    * Protokolldatenströme abrufen
    * Kann keine Änderungen an Apps oder Konfigurationen vornehmen
* **Azure Pipelines/Jenkins/Github Actions-Rolle**:
    * Kann Erstell-, Lese-, Aktualisierungs- und Löschvorgänge ausführen
    * Kann alles in der Azure Spring Cloud und in den Apps innerhalb der Dienstinstanz erstellen und konfigurieren: Azure Pipelines, Jenkins oder GitHub Actions mit TERRAFORM- oder ARM-Vorlagen

## <a name="define-developer-role"></a>Entwicklerrolle definieren

Die Entwicklerrolle umfasst die Berechtigungen, Apps neu zu starten und ihre Protokolldatenströme anzuzeigen, kann aber keine Änderungen an Apps oder der Konfiguration vornehmen.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>In Abonnement- und Ressourcengruppen-Zugriffssteuerung (IAM) navigieren

Führen Sie diese Schritte aus, um mit dem Definieren einer Rolle zu beginnen.

1. Öffnen Sie im Azure-Portal das Abonnement und die Ressourcengruppe, in dem bzw. in der es möglich sein soll, die benutzerdefinierte Rolle zuzuweisen.
2. Öffnen Sie **Zugriffssteuerung (IAM)** .
3. Klicken Sie auf **+ Hinzufügen**.
4. Klicken Sie auf **Benutzerdefinierte Rolle hinzufügen**.
5. Klicken Sie auf **Weiter**.

   ![Erstellen einer benutzerdefinierten Rolle](media/spring-cloud-permissions/create-custom-role.png)

6. Klicken Sie a **Berechtigungen hinzufügen**.

   ![Berechtigungen hinzufügen – Starten](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Suchen Sie nach Azure Spring Cloud-Berechtigungen:
7. Suchen Sie im Suchfeld nach *Microsoft.app*.
Wählen Sie *Microsoft Azure Spring Cloud* aus.

   ![Azure Spring Cloud auswählen](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Wählen Sie die Berechtigungen für die Rolle „Entwickler“ aus:

Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
* Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren
* Lesen: Azure Spring Cloud-Dienstinstanz abrufen
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
* Lesen: Microsoft Azure Spring Cloud-Anwendung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/domains** Folgendes aus:
* Lesen: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne lesen

Wählen Sie unter **Microsoft.AppPlatform/Spring/certificates** Folgendes aus:
* Lesen: Microsoft Azure Spring Cloud-Zertifikat lesen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
* Lesen: Vorgangsergebnis lesen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
* Lesen: Vorgangsstatus lesen

    [ ![Erstellen von Entwicklerberechtigungen](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Klicken Sie auf **Hinzufügen**.

10. Berechtigungen überprüfen.

11. Klicken Sie auf **Überprüfen und erstellen**.

## <a name="define-devops-engineer-role"></a>DevOps-Entwickler-Rolle definieren
Diese Prozedur definiert eine Rolle mit Berechtigungen zum Bereitstellen, Testen und Neustarten von Azure Spring Cloud-Apps.

1. Wiederholen Sie das Verfahren zum Navigieren in Abonnement, Ressourcengruppe und Zugriffssteuerung (IAM).
2. Wählen Sie die Berechtigungen für die Rolle „DevOps-Entwickler“ aus:

Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
* Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren
* Löschen: Azure Spring Cloud-Dienstinstanz löschen
* Lesen: Azure Spring Cloud-Dienstinstanz abrufen
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt aktivieren
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt deaktivieren
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel neu erstellen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendung schreiben
* Löschen: Microsoft Azure Spring Cloud-Anwendung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen
* Sonstiges: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne überprüfen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendungsbindung schreiben
* Löschen: Microsoft Azure Spring Cloud-Anwendungsbindung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben
* Löschen: Azure Spring Cloud-Anwendungsbereitstellung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments/skus** Folgendes aus:
* Lesen: Auflisten der verfügbaren SKUs für die Anwendungsbereitstellung

Wählen Sie unter **Microsoft.AppPlatform/locations** Folgendes aus:
* Sonstiges: Verfügbarkeit des Namens prüfen

Wählen Sie unter „Microsoft.AppPlatform/locations/operationResults/Spring“ Folgendes aus: Lesen: Vorgangsergebnis lesen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
* Lesen: Vorgangsstatus lesen

Wählen Sie unter **Microsoft.AppPlatform/skus** Folgendes aus:
* Lesen: Verfügbare SKUs auflisten

   [ ![Dev/Op-Berechtigungen](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Klicken Sie auf **Hinzufügen**.

4. Berechtigungen überprüfen.

5. Klicken Sie auf **Überprüfen und erstellen**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definieren der Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“
Diese Prozedur definiert eine Rolle mit Berechtigungen zum Bereitstellen, Testen und Neustarten von Azure Spring Cloud-Apps.

1. Wiederholen Sie das Verfahren zum Navigieren in Abonnement, Ressourcengruppe und Zugriffssteuerung (IAM).

2. Wählen Sie die Berechtigungen für die Rolle „Ops – Websitezuverlässigkeits-Techniker (SRE)“ aus:

Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
* Lesen: Azure Spring Cloud-Dienstinstanz abrufen
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
* Lesen: Microsoft Azure Spring Cloud-Anwendung lesen

Wählen Sie unter **Microsoft.AppPlatform/apps/deployments** Folgendes aus:
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten

Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
* Lesen: Vorgangsergebnis lesen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
* Lesen: Vorgangsstatus lesen

   [ ![Ops/SRE-Berechtigungen](media/spring-cloud-permissions/ops-sre-permissions.png) ](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Klicken Sie auf **Hinzufügen**.

4. Berechtigungen überprüfen.

5. Klicken Sie auf **Überprüfen und erstellen**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definieren der Rolle „Azure Pipelines/Bereitstellung“
Diese Rolle für Jenkins/GitHub Actions kann alles in der Azure Spring Cloud und in den Apps innerhalb einer Dienstinstanz erstellen und konfigurieren. Diese Rolle dient zum Freigeben oder Bereitstellen von Code.

1. Wiederholen Sie das Verfahren zum Navigieren in Abonnement, Ressourcengruppe und Zugriffssteuerung (IAM).

2. Öffnen Sie die Optionen für **Berechtigungen**.

3. Wählen Sie die Berechtigungen für die Rolle „Azure Pipelines/Bereitstellung“ aus:
  
Wählen Sie unter **Microsoft.AppPlatform/Spring** Folgendes aus:
* Schreiben: Azure Spring Cloud-Dienstinstanz erstellen oder aktualisieren
* Löschen: Azure Spring Cloud-Dienstinstanz löschen
* Lesen: Azure Spring Cloud-Dienstinstanz abrufen
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt aktivieren
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testendpunkt deaktivieren
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel auflisten
* Sonstiges: Azure Spring Cloud-Dienstinstanz-Testschlüssel neu erstellen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendung schreiben
* Löschen: Microsoft Azure Spring Cloud-Anwendung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsressourcen-Upload-URL abrufen
* Sonstiges: Benutzerdefinierte Microsoft Azure Spring Cloud-Anwendungsdomäne überprüfen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/bindings** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendungsbindung schreiben
* Löschen: Microsoft Azure Spring Cloud-Anwendungsbindung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbindung lesen

Wählen Sie unter **Microsoft.AppPlatform/Spring/apps/deployments** Folgendes aus:
* Schreiben: Microsoft Azure Spring Cloud-Anwendungsbereitstellung schreiben
* Löschen: Azure Spring Cloud-Anwendungsbereitstellung löschen
* Lesen: Microsoft Azure Spring Cloud-Anwendungsbereitstellung lesen
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung beenden
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellung neu starten
* Sonstiges: Microsoft Azure Spring Cloud-Anwendungsbereitstellungs-Protokolldatei-URL abrufen

Wählen Sie unter **Microsoft.AppPlatform/skus** Folgendes aus:
* Lesen: Verfügbare SKUs auflisten

Wählen Sie unter **Microsoft.AppPlatform/locations** Folgendes aus:
* Sonstiges: Verfügbarkeit des Namens prüfen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationResults/Spring** Folgendes aus:
* Lesen: Vorgangsergebnis lesen

Wählen Sie unter **Microsoft.AppPlatform/locations/operationStatus/operationId** Folgendes aus:
* Lesen: Vorgangsstatus lesen

Wählen Sie unter **Microsoft.AppPlatform/skus** Folgendes aus:
* Lesen: Verfügbare SKUs auflisten

   [ ![Pipelines-Berechtigungen](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Klicken Sie auf **Hinzufügen**.

5. Berechtigungen überprüfen.

6. Klicken Sie auf **Überprüfen und erstellen**.


## <a name="see-also"></a>Weitere Informationen
* [Erstellen oder Aktualisieren benutzerdefinierter Azure-Rollen über das Azure-Portal](../role-based-access-control/custom-roles-portal.md)

Weitere Informationen zu drei Methoden, mit denen benutzerdefinierte Berechtigungen definiert werden, finden Sie unter:
* [Klonen einer Rolle](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Ganz von vorn beginnen](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Verwenden einer JSON-Datei](../role-based-access-control/custom-roles-portal.md#start-from-json)