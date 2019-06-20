---
title: SSL-Konnektivität für Azure Database for MySQL
description: Informationen zum Konfigurieren von Azure-Datenbank für MySQL und zugehörigen Anwendungen für die richtige Verwendung von SSL-Verbindungen
author: JasonMAnderson
ms.author: janders
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 129f90d495627edb25dfafdeb1b274aa2c4c71cb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525818"
---
# <a name="ssl-connectivity-in-azure-database-for-mysql"></a>SSL-Konnektivität in Azure-Datenbank für MySQL
Azure-Datenbank für MySQL unterstützt die Verbindung Ihres Datenbankservers mit Clientanwendungen, die Secure Sockets Layer (SSL) verwenden. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird.

## <a name="default-settings"></a>Standardeinstellungen
Der Datenbankdienst sollte standardmäßig so konfiguriert sein, dass beim Herstellen einer Verbindung mit MySQL SSL-Verbindungen erforderlich sind.  Es wird empfohlen, die SSL-Option wenn möglich nicht zu deaktivieren. 

Bei der Bereitstellung eines neuen Azure-Datenbank für MySQL-Servers über das Azure-Portal und die CLI wird die Erzwingung von SSL-Verbindungen standardmäßig aktiviert. 

Verbindungszeichenfolgen für verschiedene Programmiersprachen werden im Azure-Portal angezeigt. Diese Verbindungszeichenfolgen schließen die erforderlichen SSL-Parameter für die Verbindung mit Ihrer Datenbank ein. Wählen Sie im Azure-Portal Ihren Server aus. Wählen Sie unter der Überschrift **Einstellungen** die Option **Verbindungszeichenfolgen** aus. Der SSL-Parameter variiert je nach Connector, z.B. „ssl=true“ oder „sslmode=require“ oder „sslmode=required“ und weitere Variationen.

Informationen zum Aktivieren oder Deaktivieren von SSL-Verbindungen bei der Anwendungsentwicklung finden Sie unter [Konfigurieren von SSL](howto-configure-ssl.md). 

## <a name="next-steps"></a>Nächste Schritte
[Datenverbindungsbibliotheken für Azure-Datenbank für MySQL](concepts-connection-libraries.md)
