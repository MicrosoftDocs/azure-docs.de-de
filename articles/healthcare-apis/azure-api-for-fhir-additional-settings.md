---
title: Zusätzliche Einstellungen für Azure API for FHIR
description: Hier finden Sie eine Übersicht über die zusätzlichen Einstellungen, die Sie für Azure API for FHIR festlegen können.
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.reviewer: matjazl
ms.author: cavoeg
author: CaitlinV39
ms.date: 11/22/2019
ms.openlocfilehash: b9949ab4cfa42553e5a8d28244b8f621f09334d1
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398163"
---
# <a name="additional-settings-for-azure-api-for-fhir"></a>Zusätzliche Einstellungen für Azure API for FHIR

In diesem Leitfaden werden die zusätzlichen Einstellungen beschrieben, die Sie in Azure API for FHIR festlegen können. Es stehen zusätzliche Seiten mit noch ausführlicheren Details bereit.

## <a name="configure-database-settings"></a>Konfigurieren von Datenbankeinstellungen

Azure API for FHIR verwendet eine Datenbank zum Speichern der Daten. Die Leistung der zugrunde liegenden Datenbank hängt von der Anzahl der Anforderungseinheiten (Request Unit, RU) ab, die während der Dienstbereitstellung oder nach der Dienstbereitstellung in den Datenbankeinstellungen ausgewählt wurde.

Durchsatz muss bereitgestellt werden, um zu gewährleisten, dass jederzeit genügend Systemressourcen für Ihre Datenbank zur Verfügung stehen. Die erforderliche Anzahl von RUs für Ihre Anwendung hängt von den ausgeführten Vorgängen ab. Vorgänge können von einfachen Lese- und Schreibvorgängen bis hin zu komplexeren Abfragen reichen.

Weitere Informationen zum Ändern der Standardeinstellungen finden Sie unter [Konfigurieren von Datenbankeinstellungen](configure-database.md).

## <a name="access-control"></a>Zugriffssteuerung

Azure API for FHIR gestattet nur autorisierten Benutzern den Zugriff auf die FHIR-API. Sie können autorisierte Benutzer mithilfe von zwei verschiedenen Methoden konfigurieren. Die primäre und empfohlene Methode zum Konfigurieren der Zugriffssteuerung ist die Verwendung der [rollenbasierten Zugriffssteuerung von Azure](../role-based-access-control/index.yml), auf die über das Blatt **Zugriffssteuerung (IAM)** zugegriffen werden kann. Azure RBAC funktioniert nur, wenn Sie den Zugriff auf die Datenebene mithilfe des Azure Active Directory-Mandanten sichern möchten, der Ihrem Abonnement zugeordnet ist. Wenn Sie einen anderen Mandanten verwenden möchten, bietet Azure API for FHIR einen Mechanismus zur lokalen Steuerung des Zugriffs auf die FHIR-Datenebene. Die Konfigurationsoptionen sind bei Verwendung des lokalen RBAC-Mechanismus weniger umfangreich. Zum Anzeigen weiterer Details wählen Sie eine der folgenden Optionen aus:

* [Azure RBAC für FHIR-Datenebene](configure-azure-rbac.md). Dies ist die bevorzugte Option, wenn Sie den Azure Active Directory-Mandanten verwenden, der Ihrem Abonnement zugeordnet ist.
* [Lokale Steuerung des Zugriffs auf die FHIR-Datenebene](configure-local-rbac.md). Verwenden Sie diese Option nur, wenn Sie einen externen Azure Active Directory-Mandanten für die Steuerung des Zugriffs auf die Datenebene verwenden müssen. 

## <a name="enable-diagnostic-logging"></a>Aktivieren der Diagnoseprotokollierung
Möglicherweise möchten Sie die Diagnoseprotokollierung als Teil des Setups aktivieren, damit Sie den Dienst überwachen und präzise Berichte für Compliancezwecke erstellen können. Ausführliche Informationen zum Einrichten der Diagnoseprotokollierung finden Sie im entsprechenden [Leitfaden](enable-diagnostic-logging.md). Dort sind auch einige Beispielabfragen enthalten. 

## <a name="use-custom-headers-to-add-data-to-audit-logs"></a>Hinzufügen von Daten zu Überwachungsprotokollen mithilfe benutzerdefinierter Header
Möglicherweise möchten Sie in Azure API for FHIR zusätzliche Informationen in die Protokolle aufnehmen, die aus dem aufrufenden System stammen. Zum Einbeziehen dieser Informationen können Sie benutzerdefinierte Header verwenden.

Mit benutzerdefinierten Headern können Sie verschiedene Arten von Informationen erfassen. Beispiel:

* Identitäts- und Autorisierungsinformationen
* Ursprung des Aufrufers
* Ursprungsorganisation
* Details zum Clientsystem (elektronische Patientenakte, Patientenportal)

Informationen zum Hinzufügen dieser Daten zu den Überwachungsprotokollen finden Sie im Leitfaden [Hinzufügen von Daten zu Überwachungsprotokollen mithilfe benutzerdefinierter HTTP-Header](use-custom-headers.md).

## <a name="next-steps"></a>Nächste Schritte

Mithilfe dieses Leitfadens richten Sie zusätzliche Einstellungen für Azure API for FHIR ein.

Sehen Sie sich als Nächstes die Reihe von Tutorials zum Erstellen einer Webanwendung an, die FHIR-Daten liest.

>[!div class="nextstepaction"]
>[Bereitstellen einer JavaScript-Anwendung](tutorial-web-app-fhir-server.md)