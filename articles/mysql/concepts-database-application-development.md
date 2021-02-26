---
title: Entwickeln von Anwendungen – Azure Database for MySQL
description: Einführung in Entwurfsüberlegungen, die Entwickler beim Schreiben des Anwendungscodes zum Herstellen einer Verbindung zu Azure-Datenbank für MySQL befolgen sollten
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830684"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Entwicklung von Anwendungen für Azure-Datenbank für MySQL – Überblick 
In diesem Artikel werden Entwurfsüberlegungen besprochen, die Entwickler beim Schreiben des Anwendungscodes zum Herstellen einer Verbindung mit Azure Database for MySQL befolgen sollten. 

> [!TIP]
> Ein Tutorial, das die Erstellung eines Servers, Erstellung einer serverbasierten Firewall, Anzeige von Servereigenschaften, Erstellung einer Datenbank, Herstellung einer Verbindung mithilfe von Workbench und „mysql.exe“ und Abfrage derselbigen zeigt, finden Sie unter [Entwerfen Ihrer ersten Azure Database for MySQL-Datenbank](tutorial-design-database-using-portal.md).

## <a name="language-and-platform"></a>Sprache und Plattform
Für eine Vielzahl von Programmiersprachen und Plattformen sind Codebeispiele verfügbar. Die Links zu den Codebeispielen finden Sie in folgendem Thema: [Konnektivitätsbibliotheken zur Verbindung mit Azure-Datenbank für MySQL](concepts-connection-libraries.md)

## <a name="tools"></a>Tools
Azure Database for MySQL verwendet die MySQL Community-Version, die mit allgemeinen MySQL-Verwaltungstools wie Workbench oder MySQL-Hilfsprogrammen (z. B. mysql.exe, [phpMyAdmin](https://www.phpmyadmin.net/), [Navicat](https://www.navicat.com/products/navicat-for-mysql), [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/)) kompatibel ist. Sie können auch über das Azure-Portal, die Azure CLI und REST-APIs mit dem Datenbankdienst interagieren.

## <a name="resource-limitations"></a>Ressourcenbeschränkungen
Azure Database for MySQL verwaltet die für einen Server verfügbaren Ressourcen anhand von zwei verschiedenen Mechanismen: 
- Ressourcenkontrolle
- Durchsetzung von Grenzwerten

## <a name="security"></a>Sicherheit
Azure Database for MySQL stellt Ressourcen zum Einschränken des Zugriffs, Schützen von Daten, Konfigurieren von Benutzern und Rollen sowie Überwachen von Aktivitäten in einer MySQL-Datenbank bereit.

## <a name="authentication"></a>Authentication
Azure Database for MySQL unterstützt die Serverauthentifizierung von Benutzern und Anmeldungen.

## <a name="resiliency"></a>Resilienz
Wenn beim Herstellen einer Verbindung mit einer MySQL-Datenbank ein vorübergehender Fehler auftritt, sollte Ihr Code den Aufruf wiederholen. Die Wiederholungslogik sollte Backoff-Logik verwenden, damit die SQL-Datenbank nicht unnötig überlastet wird, wenn mehrere Clients die Wiederholung gleichzeitig durchführen.

- Codebeispiele: Codebeispiele, die die Wiederholungslogik veranschaulichen, finden Sie in den Beispielen für die Sprache Ihrer Wahl: [Konnektivitätsbibliotheken zum Herstellen einer Verbindung mit Azure-Datenbank für MySQL](concepts-connection-libraries.md).

## <a name="managing-connections"></a>Verwalten von Verbindungen
Datenbankverbindungen stellen eine im begrenzten Umfang verfügbare Ressource dar. Daher sollten Verbindungen beim Zugriff auf Ihre MySQL-Datenbank sinnvoll eingesetzt werden, um eine bessere Leistung zu erzielen.
- Greifen Sie anhand von Verbindungspooling oder persistenten Verbindungen auf die Datenbank zu.
- Greifen Sie über eine kurze Verbindungsdauer auf die Datenbank zu. 
- Verwenden Sie beim Verbindungsversuch die Wiederholungslogik in Ihrer Anwendung, um Fehler durch Überschreitung der maximal zulässigen Anzahl an gleichzeitigen Verbindungen abzufangen. Legen Sie in der Wiederholungslogik eine kurze Verzögerung fest, und warten Sie dann eine gewisse Zeit, bevor Sie weitere Verbindungsversuche unternehmen.
