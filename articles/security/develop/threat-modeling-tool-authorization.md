---
title: Autorisierung – Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über mögliche Maßnahmen zur Risikominderung im Bereich der Autorisierung im Threat Modeling Tool. Nachfolgend finden Sie eine Liste mit potenziellen Bedrohungen und Anweisungen zur Risikominderung.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: b2ad38e518fa4b924992355990ea3eb06a338ebe
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693157"
---
# <a name="security-frame-authorization--mitigations"></a>Sicherheitsrahmen: Autorisierung | Risikominderung 
| Produkt/Dienst | Artikel |
| --------------- | ------- |
| **Computer-Vertrauensstellungsgrenze** | <ul><li>[Stellen Sie sicher, dass geeignete ACLs konfiguriert sind, um nicht autorisierten Zugriff auf Daten auf dem Gerät zu beschränken.](#acl-restricted-access)</li><li>[Stellen Sie sicher, dass sensible benutzerspezifische Anwendungsinhalte im Benutzerprofilverzeichnis gespeichert werden.](#sensitive-directory)</li><li>[Stellen Sie sicher, dass die bereitgestellten Anwendungen mit geringstmöglichen Berechtigungen ausgeführt werden.](#deployed-privileges)</li></ul> |
| **Web Application** | <ul><li>[Erzwingen Sie bei der Verarbeitung von Geschäftslogikflows die Einhaltung einer sequenziellen Reihenfolge.](#sequential-logic)</li><li>[Implementieren Sie einen Ratenbegrenzungsmechanismus, um Enumerationen zu verhindern.](#rate-enumeration)</li><li>[Stellen Sie sicher, dass eine geeignete Autorisierung eingerichtet ist und dass das Prinzip der geringstmöglichen Berechtigungen angewendet wird.](#principle-least-privilege)</li><li>[Geschäftslogik und Entscheidungen hinsichtlich der Ressourcenzugriffsautorisierung dürfen nicht auf eingehenden Anforderungsparametern basieren.](#logic-request-parameters)</li><li>[Stellen Sie sicher, dass Inhalte und Ressourcen nicht aufzählbar sind und dass auf sie nicht mittels Forceful Browsing zugegriffen werden kann.](#enumerable-browsing)</li></ul> |
| **Datenbank** | <ul><li>[Stellen Sie sicher, dass beim Herstellen einer Verbindung mit dem Datenbankserver Konten mit geringstmöglichen Berechtigungen verwendet werden.](#privileged-server)</li><li>[Implementieren Sie Sicherheit auf Zeilenebene (Row Level Security, RLS), um zu verhindern, dass Mandanten auf Daten anderer Mandanten zugreifen.](#rls-tenants)</li><li>[Die Rolle „SysAdmin“ darf nur gültige, erforderliche Benutzer enthalten.](#sysadmin-users)</li></ul> |
| **IoT-Cloudgateway** | <ul><li>[Verwenden Sie beim Herstellen einer Verbindung mit dem Cloudgateway Token mit geringstmöglichen Berechtigungen.](#cloud-least-privileged)</li></ul> |
| **Azure Event Hub** | <ul><li>[Verwenden Sie einen auf Sendeberechtigungen beschränkten SAS-Schlüssel, um Gerätetoken zu generieren.](#sendonly-sas)</li><li>[Verwenden Sie keine Zugriffstoken, die direkten Zugriff auf den Event Hub ermöglichen.](#access-tokens-hub)</li><li>[Verwenden Sie beim Herstellen einer Verbindung mit dem Event Hub SAS-Schlüssel, die über die erforderlichen Mindestberechtigungen verfügen.](#sas-minimum-permissions)</li></ul> |
| **Azure DocumentDB** | <ul><li>[Verwenden Sie nach Möglichkeit Ressourcentoken, um eine Verbindung mit Azure Cosmos DB herzustellen.](#resource-docdb)</li></ul> |
| **Azure-Vertrauensstellungsgrenze** | <ul><li>[Ermöglichen Sie mithilfe von Azure RBAC eine differenzierte Zugriffsverwaltung für das Azure-Abonnement.](#grained-rbac)</li></ul> |
| **Service Fabric-Vertrauensstellungsgrenze** | <ul><li>[Beschränken Sie mithilfe von Azure RBAC den Clientzugriff auf Clustervorgänge.](#cluster-rbac)</li></ul> |
| **Dynamics CRM** | <ul><li>[Führen Sie die Sicherheitsmodellierung durch, und verwenden Sie bei Bedarf die Sicherheit auf Feldebene.](#modeling-field)</li></ul> |
| **Dynamics CRM-Portal** | <ul><li>[Führen Sie die Sicherheitsmodellierung für Portalkonten durch, und bedenken Sie dabei, dass sich das Sicherheitsmodell für das Portal vom restlichen CRM unterscheidet.](#portal-security)</li></ul> |
| **Azure Storage (in englischer Sprache)** | <ul><li>[Gewähren Sie differenzierte Berechtigungen für eine Reihe von Entitäten in Azure Table Storage.](#permission-entities)</li><li>[Aktivieren Sie mithilfe von Azure Resource Manager die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) für das Azure-Speicherkonto.](#rbac-azure-manager)</li></ul> |
| **Mobiler Client** | <ul><li>[Implementieren Sie eine implizite Jailbreak- oder Rooting-Erkennung.](#rooting-detection)</li></ul> |
| **WCF** | <ul><li>[Schwache Klassenreferenz in WCF](#weak-class-wcf)</li><li>[WCF – Implementieren Sie eine Autorisierungskontrolle.](#wcf-authz)</li></ul> |
| **Web-API** | <ul><li>[Implementieren Sie einen geeigneten Autorisierungsmechanismus in der ASP.NET-Web-API.](#authz-aspnet)</li></ul> |
| **IoT-Gerät** | <ul><li>[Führen Sie Autorisierungsprüfungen auf dem Gerät aus, falls es verschiedene Aktionen unterstützt, die unterschiedliche Berechtigungsstufen erfordern.](#device-permission)</li></ul> |
| **Zwischengeschaltetes IoT-Gateway** | <ul><li>[Führen Sie Autorisierungsprüfungen für das zwischengeschaltete Gateway aus, falls es verschiedene Aktionen unterstützt, die unterschiedliche Berechtigungsstufen erfordern.](#field-permission)</li></ul> |

## <a name="ensure-that-proper-acls-are-configured-to-restrict-unauthorized-access-to-data-on-the-device"></a><a id="acl-restricted-access"></a>Stellen Sie sicher, dass geeignete ACLs konfiguriert sind, um nicht autorisierten Zugriff auf Daten auf dem Gerät zu beschränken.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass geeignete ACLs konfiguriert sind, um nicht autorisierten Zugriff auf Daten auf dem Gerät zu beschränken.|

## <a name="ensure-that-sensitive-user-specific-application-content-is-stored-in-user-profile-directory"></a><a id="sensitive-directory"></a>Stellen Sie sicher, dass sensible benutzerspezifische Anwendungsinhalte im Benutzerprofilverzeichnis gespeichert werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass sensible benutzerspezifische Anwendungsinhalte im Benutzerprofilverzeichnis gespeichert werden. Dadurch wird verhindert, dass mehrere Benutzer eines Computers auf Daten der anderen Benutzer zugreifen.|

## <a name="ensure-that-the-deployed-applications-are-run-with-least-privileges"></a><a id="deployed-privileges"></a>Stellen Sie sicher, dass die bereitgestellten Anwendungen mit geringstmöglichen Berechtigungen ausgeführt werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Computer-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass die bereitgestellte Anwendung mit geringstmöglichen Berechtigungen ausgeführt wird. |

## <a name="enforce-sequential-step-order-when-processing-business-logic-flows"></a><a id="sequential-logic"></a>Erzwingen Sie bei der Verarbeitung von Geschäftslogikflows die Einhaltung einer sequenziellen Reihenfolge.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Um sicherzustellen, dass diese Phase von einem echten Benutzer durchlaufen wurde, erzwingen Sie, dass Geschäftslogikflows von der Anwendung ausschließlich in sequenzieller Reihenfolge verarbeitet werden. Dabei müssen alle Schritte in für Menschen realistischer Zeit und in der korrekten Reihenfolge verarbeitet werden. Außerdem dürfen keine Schritte übersprungen, keine Schritte eines anderen Benutzers verarbeitet und Transaktionen nicht zu schnell übermittelt werden.|

## <a name="implement-rate-limiting-mechanism-to-prevent-enumeration"></a><a id="rate-enumeration"></a>Implementieren Sie einen Ratenbegrenzungsmechanismus, um Enumerationen zu verhindern.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Stellen Sie sicher, dass sensible Bezeichner nach dem Zufallsprinzip festgelegt werden. Implementieren Sie auf anonymen Seiten ein CAPTCHA-Steuerelement. Stellen Sie sicher, dass Fehler und Ausnahmen keine spezifischen Daten offenlegen.|

## <a name="ensure-that-proper-authorization-is-in-place-and-principle-of-least-privileges-is-followed"></a><a id="principle-least-privilege"></a>Stellen Sie sicher, dass eine geeignete Autorisierung eingerichtet ist und dass das Prinzip der geringstmöglichen Berechtigungen angewendet wird.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Bei diesem Prinzip werden einem Benutzerkonto nur die Berechtigungen gewährt, die für die Arbeit des jeweiligen Benutzers erforderlich sind. Ein Sicherungsbenutzer muss beispielsweise keine Software installieren und ist daher nur zum Ausführen von Sicherungen und sicherungsbezogenen Anwendungen berechtigt. Andere Berechtigungen (beispielsweise zum Installieren neuer Software) werden blockiert. Das Prinzip gilt auch für einen PC-Benutzer, der in der Regel unter einem normalen Benutzerkonto arbeitet und nur dann ein privilegiertes, kennwortgeschütztes Konto (Superuser) öffnet, wenn es sich nicht vermeiden lässt. </p><p>Dieses Prinzip lässt sich auch auf Ihre Webanwendungen übertragen. Anstatt sich ausschließlich auf rollenbasierte Authentifizierungsmethoden mit Sitzungen zu verlassen, empfiehlt es sich, Benutzern mithilfe eines datenbankbasierten Authentifizierungssystems Berechtigungen zuzuweisen. Wir verwenden zwar weiterhin Sitzungen, um zu ermitteln, ob der Benutzer ordnungsgemäß angemeldet wurde, aber anstatt diesem Benutzer eine bestimmte Rolle zuzuweisen, weisen wir ihm Berechtigungen zu, um zu überprüfen, zu welchen Aktionen er im System berechtigt ist. Ein großer Vorteil dieser Methode: Wenn einem Benutzer weniger Berechtigungen zugewiesen werden müssen, werden die Änderungen umgehend angewendet, da die Zuweisung nicht von der Sitzung abhängt, die andernfalls zuerst ablaufen muss.</p>|

## <a name="business-logic-and-resource-access-authorization-decisions-should-not-be-based-on-incoming-request-parameters"></a><a id="logic-request-parameters"></a>Geschäftslogik und Entscheidungen hinsichtlich der Ressourcenzugriffsautorisierung dürfen nicht auf eingehenden Anforderungsparametern basieren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Wenn Sie überprüfen, ob ein Benutzer nur bestimmte Daten betrachten darf, müssen die Zugriffsbeschränkungen serverseitig verarbeitet werden. Die Benutzer-ID muss bei der Anmeldung in einer Sitzungsvariablen gespeichert und zum Abrufen von Benutzerdaten aus der Datenbank verwendet werden. |

### <a name="example"></a>Beispiel
```SQL
SELECT data 
FROM personaldata 
WHERE userID=:id < - session var 
```
Nun kann ein potenzieller Angreifer den Anwendungsbetrieb nicht mehr manipulieren und verändern, da die ID zum Abrufen der Daten serverseitig behandelt wird.

## <a name="ensure-that-content-and-resources-are-not-enumerable-or-accessible-via-forceful-browsing"></a><a id="enumerable-browsing"></a>Stellen Sie sicher, dass Inhalte und Ressourcen nicht aufzählbar sind und dass auf sie nicht mittels Forceful Browsing zugegriffen werden kann.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Webanwendung. | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Sensible statische Dateien und Konfigurationsdateien dürfen nicht in Webroot gespeichert werden. Inhalte, die nicht öffentlich sein müssen, müssen entweder mit einer geeigneten Zugriffssteuerung versehen oder entfernt werden.</p><p>Forceful Browsing wird zudem meist mit Brute-Force-Techniken kombiniert, um Informationen zu sammeln, indem versucht wird, auf möglichst viele URLs zuzugreifen und Verzeichnisse und Dateien auf einem Server aufzuzählen. Angreifer können nach sämtlichen Varianten von üblicherweise vorhandenen Dateien suchen. Bei einer Suche nach einer Kennwortdatei wird also beispielsweise nach Dateien wie „psswd.txt“, „password.htm“, „password.dat“ und anderen Varianten gesucht.</p><p>Als Gegenmaßnahme müssen Funktionen zur Erkennung von Brute-Force-Angriffsversuchen einbezogen werden.</p>|

## <a name="ensure-that-least-privileged-accounts-are-used-to-connect-to-database-server"></a><a id="privileged-server"></a>Stellen Sie sicher, dass beim Herstellen einer Verbindung mit dem Datenbankserver Konten mit geringstmöglichen Berechtigungen verwendet werden.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Berechtigungshierarchie (Datenbank-Engine)](/sql/relational-databases/security/permissions-hierarchy-database-engine), [Sicherungsfähige Elemente](/sql/relational-databases/security/securables) |
| **Schritte** | Beim Herstellen einer Verbindung mit der Datenbank müssen Konten mit möglichst geringen Berechtigungen verwendet werden. Die Anwendungsanmeldung muss in der Datenbank eingeschränkt sein und darf nur bestimmte gespeicherte Prozeduren ausführen. Die Anmeldung der Anwendung darf nicht über direkten Tabellenzugriff verfügen. |

## <a name="implement-row-level-security-rls-to-prevent-tenants-from-accessing-each-others-data"></a><a id="rls-tenants"></a>Implementieren Sie Sicherheit auf Zeilenebene (Row Level Security, RLS), um zu verhindern, dass Mandanten auf Daten anderer Mandanten zugreifen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | SQL Azure, lokal |
| **Attribute**              | SQL-Version: V12, SQL-Version: MsSQL2016 |
| **Referenzen**              | [Sicherheit auf Zeilenebene](/sql/relational-databases/security/row-level-security) |
| **Schritte** | <p>Bei der Sicherheit auf Zeilenebene können Kunden den Zugriff auf Zeilen in einer Datenbanktabelle basierend auf den Merkmalen des Benutzers steuern, der eine Abfrage ausführt (z.B. Gruppenmitgliedschaft oder Ausführungskontext).</p><p>Eine zeilenbasierte Sicherheit vereinfacht den Entwurf und die Sicherheitscodierung in Ihrer Anwendung. Mit RLS können Sie den Zugriff auf Datenzeilen einschränken. So können Sie beispielsweise sicherstellen, dass Mitarbeiter nur auf Datenzeilen zugreifen können, die für ihre Abteilung relevant sind, oder den Datenzugriff von Kunden auf Daten beschränken, die für das Unternehmen des jeweiligen Kunden relevant sind.</p><p>Die Datenbeschränkungszugriffslogik befindet sich auf der Datenbankebene, statt fern der Daten auf einer anderen Anwendungsebene. Das Datenbanksystem wendet die Zugriffsbeschränkungen bei jedem Zugriffsversuch auf Daten aus einer beliebigen Ebene an. Dadurch wird das Sicherheitssystem kompakter, zuverlässiger und robuster.</p><p>|

Beachten Sie, dass RLS als vorgefertigtes Datenbankfeature nur für SQL Server-Versionen ab 2016, Azure SQL-Datenbank und verwaltete SQL-Instanzen verfügbar ist. Wenn das vorgefertigte RLS-Feature nicht implementiert wird, muss der Datenzugriff mithilfe von Sichten und Prozeduren beschränkt werden.

## <a name="sysadmin-role-should-only-have-valid-necessary-users"></a><a id="sysadmin-users"></a>Die Rolle „SysAdmin“ darf nur gültige, erforderliche Benutzer enthalten.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Datenbank | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Berechtigungshierarchie (Datenbank-Engine)](/sql/relational-databases/security/permissions-hierarchy-database-engine), [Sicherungsfähige Elemente](/sql/relational-databases/security/securables) |
| **Schritte** | Die feste Serverrolle „SysAdmin“ darf nur sehr wenige Mitglieder und niemals Konten enthalten, die von Anwendungen verwendet werden.  Überprüfen Sie die Benutzerliste der Rolle, und entfernen Sie alle nicht benötigten Konten.|

## <a name="connect-to-cloud-gateway-using-least-privileged-tokens"></a><a id="cloud-least-privileged"></a>Verwenden Sie beim Herstellen einer Verbindung mit dem Cloudgateway Token mit geringstmöglichen Berechtigungen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Cloudgateway | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Wahl des Gateways: Azure IoT Hub |
| **Referenzen**              | [Entwicklungsleitfaden für Azure IoT Hub](../../iot-hub/iot-hub-devguide.md) |
| **Schritte** | Gewähren Sie verschiedenen Komponenten, die eine Verbindung mit dem Cloudgateway (IoT Hub) herstellen, möglichst wenige Berechtigungen. Ein typisches Beispiel: Die Komponente für die Geräteverwaltung/-bereitstellung verwendet „RegistryRead/Write“, der Ereignisprozessor (ASA) verwendet Service Connect. Einzelne Geräte stellen eine Verbindung unter Verwendung von Geräteanmeldeinformationen her.|

## <a name="use-a-send-only-permissions-sas-key-for-generating-device-tokens"></a><a id="sendonly-sas"></a>Verwenden Sie einen auf Sendeberechtigungen beschränkten SAS-Schlüssel, um Gerätetoken zu generieren.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Event Hub | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)](../../event-hubs/authenticate-shared-access-signature.md) |
| **Schritte** | Ein SAS-Schlüssel dient zum Generieren individueller Gerätetoken. Verwenden Sie einen auf Sendeberechtigungen beschränkten SAS-Schlüssel, wenn Sie das Gerätetoken für einen bestimmten Herausgeber generieren.|

## <a name="do-not-use-access-tokens-that-provide-direct-access-to-the-event-hub"></a><a id="access-tokens-hub"></a>Verwenden Sie keine Zugriffstoken, die direkten Zugriff auf den Event Hub ermöglichen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Event Hub | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)](../../event-hubs/authenticate-shared-access-signature.md) |
| **Schritte** | Das Gerät darf kein Token erhalten, das direkten Zugriff auf den Event Hub gewährt. Verwenden Sie für das Gerät stattdessen ein Token mit geringstmöglichen Berechtigungen, das nur einem Herausgeber Zugriff gewährt. Dadurch lassen sich gefährliche oder kompromittierte Geräte leichter identifizieren und sperren.|

## <a name="connect-to-event-hub-using-sas-keys-that-have-the-minimum-permissions-required"></a><a id="sas-minimum-permissions"></a>Verwenden Sie beim Herstellen einer Verbindung mit dem Event Hub SAS-Schlüssel, die über die erforderlichen Mindestberechtigungen verfügen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Event Hub | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Event Hubs-Authentifizierung und -Sicherheitsmodell (Übersicht)](../../event-hubs/authenticate-shared-access-signature.md) |
| **Schritte** | Gewähren Sie verschiedenen Back-End-Anwendungen, die eine Verbindung mit dem Event Hub herstellen, möglichst wenige Berechtigungen. Generieren Sie für jede Back-End-Anwendung einen separaten SAS-Schlüssel, und gewähren Sie den Anwendungen nur die erforderlichen Sende-, Empfangs- oder Verwaltungsberechtigungen.|

## <a name="use-resource-tokens-to-connect-to-cosmos-db-whenever-possible"></a><a id="resource-docdb"></a>Verwenden Sie nach Möglichkeit Ressourcentoken, um eine Verbindung mit Cosmos DB herzustellen.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure DocumentDB | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Ein Ressourcentoken ist einer Azure Cosmos DB-Berechtigungsressource zugeordnet und erfasst die Beziehung zwischen dem Benutzer einer Datenbank und der Berechtigung, über die der Benutzer für eine bestimmte Azure Cosmos DB-Anwendungsressource (z. B. Sammlung, Dokument) verfügt. Verwenden Sie für den Zugriff auf Azure Cosmos DB immer ein Ressourcentoken, falls die Verarbeitung von Hauptschlüsseln oder Schlüsseln mit Leseberechtigung nicht bedenkenlos dem Client überlassen werden kann (z. B. im Falle einer Endbenutzeranwendung wie einem mobilen Client oder einem Desktopclient). Verwenden Sie Hauptschlüssel oder Schlüssel mit Leseberechtigung von Back-End-Anwendungen, die diese Schlüssel sicher speichern können.|

## <a name="enable-fine-grained-access-management-to-azure-subscription-using-azure-rbac"></a><a id="grained-rbac"></a>Ermöglichen Sie mithilfe von Azure RBAC eine differenzierte Zugriffsverwaltung für das Azure-Abonnement.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Hinzufügen oder Entfernen von Rollenzuweisungen zum Verwalten von Azure-Abonnementressourcen](../../role-based-access-control/role-assignments-portal.md)  |
| **Schritte** | Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure ermöglicht eine präzise Zugriffsverwaltung für Azure. Mit Azure RBAC können Sie den Benutzern nur die Zugriffsrechte gewähren, die diese zum Ausführen ihrer Aufgaben benötigen.|

## <a name="restrict-clients-access-to-cluster-operations-using-service-fabric-rbac"></a><a id="cluster-rbac"></a>Beschränken Sie mithilfe von Service Fabric RBAC den Clientzugriff auf Clustervorgänge.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Service Fabric-Vertrauensstellungsgrenze | 
| **SDL-Phase**               | Bereitstellung |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | Umgebung: Azure |
| **Referenzen**              | [Rollenbasierte Zugriffssteuerung in Service Fabric für Service Fabric-Clients](../../service-fabric/service-fabric-cluster-security-roles.md) |
| **Schritte** | <p>Azure Service Fabric unterstützt zwei unterschiedliche Zugriffsberechtigungstypen für Clients, die mit einem Service Fabric-Cluster verbunden sind: Administrator und Benutzer. Zugriffssteuerung ermöglicht es dem Clusteradministrator, den Zugriff auf bestimmte Clustervorgänge für verschiedene Gruppen von Benutzern einzuschränken, wodurch die Sicherheit des Clusters erhöht wird.</p><p>Administratoren haben vollständigen Zugriff auf Verwaltungsfunktionen (einschließlich Lese-/Schreibzugriff). Benutzer haben standardmäßig nur Lesezugriff auf Verwaltungsfunktionen (z. B. Abfragefunktionen) sowie die Möglichkeit, Anwendungen und Dienste aufzulösen.</p><p>Sie geben die beiden Clientrollen (Administrator und Client) zum Zeitpunkt der Clustererstellung an, indem Sie für jede separate Zertifikate bereitstellen.</p>|

## <a name="perform-security-modeling-and-use-field-level-security-where-required"></a><a id="modeling-field"></a>Führen Sie die Sicherheitsmodellierung durch, und verwenden Sie bei Bedarf die Sicherheit auf Feldebene.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Führen Sie die Sicherheitsmodellierung durch, und verwenden Sie bei Bedarf die Sicherheit auf Feldebene.|

## <a name="perform-security-modeling-of-portal-accounts-keeping-in-mind-that-the-security-model-for-the-portal-differs-from-the-rest-of-crm"></a><a id="portal-security"></a>Führen Sie die Sicherheitsmodellierung für Portalkonten durch, und bedenken Sie dabei, dass sich das Sicherheitsmodell für das Portal vom restlichen CRM unterscheidet.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Dynamics CRM-Portal | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Führen Sie die Sicherheitsmodellierung für Portalkonten durch, und bedenken Sie dabei, dass sich das Sicherheitsmodell für das Portal vom restlichen CRM unterscheidet.|

## <a name="grant-fine-grained-permission-on-a-range-of-entities-in-azure-table-storage"></a><a id="permission-entities"></a>Gewähren Sie differenzierte Berechtigungen für eine Reihe von Entitäten in Azure Table Storage.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | StorageType: Tabelle |
| **Referenzen**              | [Delegieren des Zugriffs auf Objekte in Ihrem Konto mithilfe von SAS und gespeicherter Zugriffsrichtlinien](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Schritte** | In bestimmten Geschäftsszenarien müssen in Azure Table Storage unter Umständen sensible Daten für unterschiedliche Parteien gespeichert werden. Ein Beispiel wären etwa sensible Daten für verschiedene Länder/Regionen. In solchen Fällen können SAS-Signaturen unter Angabe der Partition und Zeilenschlüsselbereiche erstellt werden, sodass ein Benutzer auf spezifische Daten für ein bestimmtes Land bzw. eine bestimmte Region zugreifen kann.| 

## <a name="enable-azure-role-based-access-control-azure-rbac-to-azure-storage-account-using-azure-resource-manager"></a><a id="rbac-azure-manager"></a>Aktivieren Sie mithilfe von Azure Resource Manager die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) für das Azure-Speicherkonto.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Azure Storage | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | [Sichern des Speicherkontos mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](../../storage/blobs/security-recommendations.md) |
| **Schritte** | <p>Bei der Erstellung eines neuen Speicherkontos haben Sie die Wahl zwischen dem klassischen Bereitstellungsmodell und dem Azure Resource Manager-Bereitstellungsmodell. Das klassische Modell der Ressourcenerstellen in Azure lässt nur den uneingeschränkten Zugriff auf das Abonnement und damit auf das Speicherkonto zu.</p><p>Beim Azure Resource Manager-Modell platzieren Sie das Speicherkonto in einer Ressourcengruppe und steuern den Zugriff auf die Verwaltungsebene dieses bestimmten Speicherkontos mithilfe von Azure Active Directory. Beispielsweise können Sie bestimmten Benutzern den Zugriff auf die Speicherkontoschlüssel gewähren, während andere Benutzer Informationen über das Speicherkonto anzeigen, jedoch nicht auf die Speicherkontoschlüssel zugreifen können.</p>|

## <a name="implement-implicit-jailbreak-or-rooting-detection"></a><a id="rooting-detection"></a>Implementieren Sie eine implizite Jailbreak- oder Rooting-Erkennung.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Mobiler Client | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Die Anwendung muss ihre eigenen Konfigurations- und Benutzerdaten für den Fall schützen, dass das Smartphone mittels Jailbreak oder Rooting manipuliert wird. Rooting/Jailbreak impliziert einen nicht autorisierten Zugriff und wird von normalen Benutzern nicht auf dem eigenen Smartphone durchgeführt. Die Anwendung muss daher beim Anwendungsstart mithilfe einer impliziten Erkennungslogik ermitteln, ob das Smartphone mittels Rooting manipuliert wurde.</p><p>Die Erkennungslogik kann einfach auf Dateien zugreifen, auf die normalerweise nur Root-Benutzer Zugriff haben. Beispielsweise:</p><ul><li>/system/app/Superuser.apk</li><li>/sbin/su</li><li>/system/bin/su</li><li>/system/xbin/su</li><li>/data/local/xbin/su</li><li>/data/local/bin/su</li><li>/system/sd/xbin/su</li><li>/system/bin/failsafe/su</li><li>/data/local/su</li></ul><p>Falls die Anwendung auf eine dieser Dateien zugreifen kann, wird die Anwendung als Root-Benutzer ausgeführt.</p>|

## <a name="weak-class-reference-in-wcf"></a><a id="weak-class-wcf"></a>Schwache Klassenreferenz in WCF

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Schritte** | <p>Das System verwendet eine schwache Klassenreferenz, wodurch Angreifer unter Umständen nicht autorisierten Code ausführen können. Das Programm verweist auf eine benutzerdefinierte Klasse, die nicht eindeutig identifiziert wird. Wenn .NET diese schwach identifizierte Klasse lädt, durchsucht das CLR-Typladeprogramm an die folgenden Speicherorte in der angegebenen Reihenfolge nach der Klasse:</p><ol><li>Wenn die Assembly des Typs bekannt ist, durchsucht das Ladeprogramm die Umleitungsorte der Konfigurationsdatei, den GAC, die aktuelle Assembly mit Konfigurationsinformationen und das Anwendungsbasisverzeichnis.</li><li>Ist die Assembly nicht bekannt, durchsucht das Ladeprogramm die aktuelle Assembly, „mscorlib“ und den vom TypeResolve-Ereignishandler zurückgegebenen Ort.</li><li>Diese CLR-Suchreihenfolge kann mit Hooks wie dem Typweiterleitungsmechanismus und dem AppDomain.TypeResolve-Ereignis geändert werden.</li></ol><p>Wenn sich ein Angreifer die CLR-Suchreihenfolge zunutze macht, indem er eine alternative Klasse mit dem gleichen Namen erstellt und sie an einem alternativen Speicherort platziert, den die CLR zuerst lädt, führt die CLR ungewollt den Code des Angreifers aus.</p>|

### <a name="example"></a>Beispiel
Das Element `<behaviorExtensions/>` der folgenden WCF-Konfigurationsdatei weist WCF an, einer bestimmten WCF-Erweiterung eine Klasse mit benutzerdefiniertem Verhalten hinzuzufügen.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""MyBehavior"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```
Die Verwendung vollqualifizierter (sicherer) Namen ermöglicht die eindeutige Identifizierung eines Typs und trägt zur Verbesserung der Systemsicherheit bei. Verwenden Sie vollqualifizierte Assemblynamen, wenn Sie Typen in den Dateien „machine.config“ und „app.config“ registrieren.

### <a name="example"></a>Beispiel
Das Element `<behaviorExtensions/>` der folgenden WCF-Konfigurationsdatei weist WCF an, einer bestimmten WCF-Erweiterung eine stark referenzierte Klasse mit benutzerdefiniertem Verhalten hinzuzufügen.
```
<system.serviceModel>
    <extensions>
        <behaviorExtensions>
            <add name=""myBehavior"" type=""Microsoft.ServiceModel.Samples.MyBehaviorSection, MyBehavior,
            Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"" />
        </behaviorExtensions>
    </extensions>
</system.serviceModel>
```

## <a name="wcf-implement-authorization-control"></a><a id="wcf-authz"></a>WCF – Implementieren Sie eine Autorisierungskontrolle.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | WCF | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, .NET Framework 3 |
| **Attribute**              | –  |
| **Referenzen**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference) |
| **Schritte** | <p>Dieser Dienst verwendet keine Autorisierungskontrolle. Wenn ein Client einen bestimmten WCF-Dienst aufruft, stellt WCF verschiedene Autorisierungsschemas bereit, um zu überprüfen, ob der Aufrufer zum Ausführen der Dienstmethode auf dem Server berechtigt ist. Ohne Autorisierungskontrolle für WCF-Dienste kann ein authentifizierter Benutzer eine Berechtigungsausweitung bewirken.</p>|

### <a name="example"></a>Beispiel
Die folgende Konfiguration weist WCF an, bei der Dienstausführung die Autorisierungsstufe des Clients nicht zu überprüfen:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""None"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Verwenden Sie ein Dienstautorisierungsschema, um zu überprüfen, ob der Aufrufer zum Aufrufen der Dienstmethode autorisiert ist. WCF bietet zwei Modi und ermöglicht die Verwendung eines benutzerdefinierten Authentifizierungsschemas. Im UseWindowsGroups-Modus werden Windows-Rollen und -Benutzer verwendet. Im UseAspNetRoles-Modus erfolgt die Authentifizierung anhand eines ASP.NET-Rollenanbieters (beispielsweise SQL Server).

### <a name="example"></a>Beispiel
Die folgende Konfiguration weist WCF an, vor der Ausführung des Add-Diensts zu überprüfen, ob der Client der Administratorgruppe angehört:
```
<behaviors>
    <serviceBehaviors>
        <behavior>
            ...
            <serviceAuthorization principalPermissionMode=""UseWindowsGroups"" />
        </behavior>
    </serviceBehaviors>
</behaviors>
```
Der Dienst wird dann wie folgt deklariert:
```
[PrincipalPermission(SecurityAction.Demand,
Role = ""Builtin\\Administrators"")]
public double Add(double n1, double n2)
{
double result = n1 + n2;
return result;
}
```

## <a name="implement-proper-authorization-mechanism-in-aspnet-web-api"></a><a id="authz-aspnet"></a>Implementieren Sie einen geeigneten Autorisierungsmechanismus in der ASP.NET-Web-API.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Web-API | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein, MVC5 |
| **Attribute**              | N/V, Identitätsanbieter: ADFS, Identitätsanbieter: Azure AD |
| **Referenzen**              | [Authentication and Authorization in ASP.NET Web API](https://www.asp.net/web-api/overview/security/authentication-and-authorization-in-aspnet-web-api) (Authentifizierung und Autorisierung in der ASP.NET-Web-API) |
| **Schritte** | <p>Rolleninformationen für die Anwendungsbenutzer können von Azure AD- oder ADFS-Ansprüchen abgeleitet werden, wenn die Anwendung sie als Identitätsanbieter nutzt, oder sie können von der Anwendung selbst bereitgestellt werden. In beiden Fällen müssen die Benutzerrolleninformationen von der benutzerdefinierten Autorisierungsimplementierung überprüft werden.</p><p>Rolleninformationen für die Anwendungsbenutzer können von Azure AD- oder ADFS-Ansprüchen abgeleitet werden, wenn die Anwendung sie als Identitätsanbieter nutzt, oder sie können von der Anwendung selbst bereitgestellt werden. In beiden Fällen müssen die Benutzerrolleninformationen von der benutzerdefinierten Autorisierungsimplementierung überprüft werden.</p>

### <a name="example"></a>Beispiel
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
public class ApiAuthorizeAttribute : System.Web.Http.AuthorizeAttribute
{
        public async override Task OnAuthorizationAsync(HttpActionContext actionContext, CancellationToken cancellationToken)
        {
            if (actionContext == null)
            {
                throw new Exception();
            }

            if (!string.IsNullOrEmpty(base.Roles))
            {
                bool isAuthorized = ValidateRoles(actionContext);
                if (!isAuthorized)
                {
                    HandleUnauthorizedRequest(actionContext);
                }
            }

            base.OnAuthorization(actionContext);
        }

public bool ValidateRoles(actionContext)
{
   //Authorization logic here; returns true or false
}

}
```
Alle zu schützenden Controller und Aktionsmethoden müssen mit dem obigen Attribut versehen werden.
```csharp
[ApiAuthorize]
public class CustomController : ApiController
{
     //Application code goes here
}
```

## <a name="perform-authorization-checks-in-the-device-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="device-permission"></a>Führen Sie Autorisierungsprüfungen auf dem Gerät aus, falls es verschiedene Aktionen unterstützt, die unterschiedliche Berechtigungsstufen erfordern.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | IoT-Gerät | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | <p>Das Gerät muss den Aufrufer autorisieren, um zu überprüfen, ob er zum Ausführen der angeforderten Aktion berechtigt ist. Ein Beispiel: Angenommen, bei dem Gerät handelt es sich um ein intelligentes Türschloss, das über die Cloud überwacht und unter anderem per Remotezugriff verriegelt werden kann.</p><p>Das intelligente Türschloss macht die Entriegelungsfunktion nur verfügbar, wenn sich jemand physisch und mit einer Karte der Tür nähert. In diesem Fall müssen Remotebefehl und -steuerung so implementiert werden, dass sie keine Funktion zum Entriegeln der Tür bereitstellen, da das Cloudgateway nicht autorisiert ist, einen Befehl zum Entriegeln der Tür zu senden.</p>|

## <a name="perform-authorization-checks-in-the-field-gateway-if-it-supports-various-actions-that-require-different-permission-levels"></a><a id="field-permission"></a>Führen Sie Autorisierungsprüfungen für das zwischengeschaltete Gateway aus, falls es verschiedene Aktionen unterstützt, die unterschiedliche Berechtigungsstufen erfordern.

| Titel                   | Details      |
| ----------------------- | ------------ |
| **Komponente**               | Zwischengeschaltetes IoT-Gateway | 
| **SDL-Phase**               | Entwickeln |  
| **Zutreffende Technologien** | Allgemein |
| **Attribute**              | –  |
| **Referenzen**              | –  |
| **Schritte** | Das zwischengeschaltete Gateway muss den Aufrufer autorisieren, um zu überprüfen, ob er zum Ausführen der angeforderten Aktion berechtigt ist. Beispielsweise müssen für eine Administratorbenutzeroberfläche/-API, die zum Konfigurieren eines zwischengeschalteten Gateways verwendet wird, und für Geräte, die eine Verbindung mit dem Gateway herstellen, unterschiedliche Berechtigungen verwendet werden.|