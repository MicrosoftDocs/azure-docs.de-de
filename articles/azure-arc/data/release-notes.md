---
title: Azure Arc-fähige Datendienste – Versionshinweise
description: Aktuelle Versionshinweise
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: 94074c2c5e11187252084832e5a20a197f6723fd
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359815"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Versionshinweise – Azure Arc-fähige Datendienste (Vorschauversion)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Oktober 2020 

Versionsnummer der Azure Data CLI (`azdata`): 20.2.3. Download: [https://aka.ms/azdata](https://aka.ms/azdata).

### <a name="breaking-changes"></a>Aktuelle Änderungen

Dieses Release enthält folgende Breaking Changes: 

* In der benutzerdefinierten PostgreSQL-Ressourcendefinition (Custom Resource Definition, CRD) wird der Begriff `shards` in `workers` umbenannt. Dieser Begriff (`workers`) entspricht dem Namen des Befehlszeilenparameters.

* Von `azdata arc postgres server delete` wird vor dem Löschen einer Postgres-Instanz eine Bestätigung angefordert.  Verwenden Sie `--force`, um die Aufforderung zu überspringen.

### <a name="additional-changes"></a>Weitere Änderungen

* `azdata arc postgres server create` wurde ein neuer optionaler Parameter namens `--volume-claim mounts` hinzugefügt. Der Wert ist eine durch Trennzeichen getrennte Liste mit Volumeanspruchseinbindungen. Bei einer Volumeanspruchseinbindung handelt es sich um ein Paar aus Volumetyp und PVC-Name. Derzeit wird ausschließlich der Volumetyp `backup` unterstützt.  Wenn der Volumetyp in PostgreSQL `backup` lautet, wird das PVC in `/mnt/db-backups` eingebunden.  Dies ermöglicht die gemeinsame Nutzung von Sicherungen zwischen PostgresSQL-Instanzen, sodass die Sicherung einer PostgresSQL-Instanz in einer anderen Instanz wiederhergestellt werden kann.

* Neuer Kurzname für benutzerdefinierte PostgresSQL-Ressourcendefinitionen: 

  * `pg11` 

  * `pg12`

* Telemetrieupload bietet dem Benutzer Folgendes:

   * Anzahl von Punkten, die in Azure hochgeladen wurden

     oder 

   * Aufforderung zum erneuten Versuch, falls keine Daten in Azure geladen wurden.

* `azdata arc dc debug copy-logs` liest nun auch aus dem Ordner `/var/opt/controller/log` und sammelt PostgreSQL-Engineprotokolle unter Linux.

*   Anzeige eines Funktionsindikators beim Erstellen und Wiederherstellen einer Sicherung mit PostgreSQL Hyperscale.

* `azdata arc postrgres backup list` enthält jetzt Informationen zur Sicherungsgröße.

* Die Administratornamenseigenschaft von SQL Managed Instance wurde der rechten Spalte des Übersichtsblatts im Azure-Portal hinzugefügt.

* Azure Data Studio unterstützt das Konfigurieren der Anzahl von Workerknoten sowie von virtuellen Kernen und Arbeitsspeichereinstellungen für PostgreSQL Hyperscale. 

* In der Vorschauversion werden Sicherungen/Wiederherstellungen für die Postgres-Versionen 11 und 12 unterstützt.

## <a name="september-2020"></a>September 2020

Azure Arc-fähige Datendienste sind für die öffentliche Vorschauversion freigegeben. Mit Arc-fähigen Datendiensten können Sie Datendienste an jedem Ort verwalten.

- Verwaltete SQL-Instanz
- PostgreSQL Hyperscale

Anweisungen finden Sie unter [Was sind Azure Arc-fähige Datendienste?](overview.md)

## <a name="known-limitations-and-issues"></a>Bekannte Einschränkungen und Probleme

- Instanznamen dürfen maximal 13 Zeichen umfassen.
- Für Azure Arc-Datencontroller oder -Datenbankinstanzen sind keine direkten Upgrades verfügbar.
- Containerimages von Arc-fähigen Datendiensten sind nicht signiert.  Möglicherweise müssen Sie die Kubernetes-Knoten so konfigurieren, dass das Pullen nicht signierter Containerimages zulässig ist.  Wenn Sie z. B. Docker als Containerruntime verwenden, können Sie die Umgebungsvariable DOCKER_CONTENT_TRUST=0 festlegen und neu starten.  Andere Containerruntimes bieten ähnliche Optionen beispielsweise in [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Es können keine Azure Arc-fähigen SQL Managed Instance-Instanzen oder PostgreSQL Hyperscale-Servergruppen über das Azure-Portal erstellt werden.
- Bei Verwendung von NFS muss `allowRunAsRoot` in der Bereitstellungsprofildatei vorerst auf `true` festgelegt werden, bevor der Azure Arc-Datencontroller erstellt wird.
- Es wird nur die SQL- und PostgreSQL-Anmeldeauthentifizierung unterstützt.  Azure Active Directory oder Active Directory werden nicht unterstützt.
- Die Erstellung eines Datencontrollers in OpenShift erfordert gelockerte Sicherheitseinschränkungen.  Einzelheiten dazu finden Sie der Dokumentation.
- Wenn Sie die Azure Kubernetes Service Engine (AKS-Engine) in Azure Stack Hub mit Azure Arc-Datencontrollern und -Datenbankinstanzen verwenden, wird kein Upgrade auf eine neuere Kubernetes-Version unterstützt. Deinstallieren Sie den Azure Arc-Datencontroller und alle Datenbankinstanzen, bevor Sie den Kubernetes-Cluster aktualisieren.
- AKS-Cluster (Azure Kubernetes Service), die [mehrere Verfügbarkeitszonen](../../aks/availability-zones.md) umfassen, werden für Datendienste mit Azure Arc-Unterstützung derzeit nicht unterstützt. Löschen Sie zur Vermeidung dieses Problems alle Zonen aus dem Auswahlsteuerelement, wenn Sie den AKS-Cluster im Azure-Portal erstellen und eine Region mit verfügbaren Zonen auswählen. Sehen Sie sich die folgende Abbildung an:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Deaktivieren der Kontrollkästchen für die einzelnen Zonen, um keine Zone anzugeben":::


### <a name="known-issues-for-azure-arc-enabled-postgresql-hyperscale"></a>Bekannte Probleme für PostgreSQL Hyperscale mit Azure Arc-Unterstützung   

- In der Vorschauversion werden keine Sicherungen/Wiederherstellungen für die Version 11 der PostgreSQL-Engine unterstützt. Es werden nur Sicherungen/Wiederherstellungen für die PostgreSQL-Version 12 unterstützt.
- Von `azdata arc dc debug copy-logs` werden keine PostgreSQL-Engineprotokolle unter Windows gesammelt.
- Es kann vorkommen, dass beim erneuten Erstellen einer Servergruppe mit dem Namen einer gerade erst gelöschten Servergruppe Fehler auftreten oder keine Reaktion erfolgt. 
   - **Problemumgehung:** Verwenden Sie beim erneuten Erstellen einer Servergruppe nicht den gleichen Namen, oder warten Sie auf den Lastenausgleich/externen Dienst der zuvor gelöschten Servergruppe. Wenn der Name der gelöschten Servergruppe also beispielsweise `postgres01` lautet und sie im Namespace `arc` gehostet wurde, warten Sie vor dem erneuten Erstellen einer Servergruppe gleichen Namens, bis `postgres01-external-svc` nicht mehr in der Ausgabe des kubectl-Befehls `kubectl get svc -n arc` erscheint.
 - Die Übersichtsseite und die Konfigurationsseite „Compute + Speicher“ in Azure Data Studio werden nur langsam geladen. 



## <a name="next-steps"></a>Nächste Schritte
  
> **Möchten Sie es selbst ausprobieren?**  
> Mit dem [Azure Arc-Schnelleinstieg](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte unternehmen.

[Installieren der Clienttools](install-client-tools.md)

[Erstellen des Azure Arc-Datencontrollers](create-data-controller.md) (erfordert zuvor die Installation der Clienttools)

[Erstellen einer Azure SQL Managed Instance-Instanz in Azure Arc](create-sql-managed-instance.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)

[Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)
