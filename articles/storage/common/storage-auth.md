---
title: Autorisierung des Zugriffs auf Azure Storage | Microsoft-Dokumentation
description: Weitere Informationen über die verschiedenen Methoden zum Autorisieren des Zugriffs auf Azure Storage, z.B. Azure Active Directory, Authentifizierung mit gemeinsam verwendetem Schlüssel oder Shared Access Signatures (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9e8c9755c444ca7b81891f5f83164bc51aa694eb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147068"
---
# <a name="authorizing-access-to-azure-storage"></a>Autorisierung des Zugriffs auf Azure Storage

Immer dann, wenn Sie auf Daten in Ihrem Speicherkonto zugreifen, sendet der Client eine Anforderung über HTTP/HTTPS an Azure Storage. Jede Anforderung an eine sichere Ressource muss autorisiert sein, damit der Dienst sicherstellt, dass der Client über die erforderlichen Berechtigungen zum Zugriff auf die Daten verfügt. Azure Storage bietet diese Optionen für die Autorisierung des Zugriffs auf sichere Ressourcen:

- **Integration von Azure Active Directory (Azure AD)** für Blobs und Warteschlangen. Azure AD bietet rollenbasierte Zugriffssteuerung (RBAC, Role-Based Access Control) für eine präzise Steuerung des Zugriffs eines Clients auf Ressourcen in einem Speicherkonto. Weitere Informationen finden Sie unter [Authentifizieren des Zugriffs auf Azure Storage mit Azure Active Directory](storage-auth-aad.md).
- **Autorisierung mit gemeinsam verwendetem Schlüssel** für Blobs, Dateien, Warteschlangen und Tabellen. Ein Client mit gemeinsam verwendetem Schlüssel übergibt mit jeder Anforderung einen Header, der mit dem Speicherkonto-Zugriffsschlüssel signiert wird. Weitere Informationen finden Sie unter [Authentifizieren mit gemeinsam verwendetem Schlüssel](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **Shared Access Signatures** für Blobs, Dateien, Warteschlangen und Tabellen. Shared Access Signatures (SAS) ermöglichen den begrenzten delegierten Zugriff auf Ressourcen in einem Speicherkonto. Einschränkungen des Zeitintervalls, für das die Signatur gültig ist, oder von Berechtigungen, die sie gewährt, bieten Flexibilität beim Verwalten des Zugriffs. Weitere Informationen finden Sie unter [Verwenden von Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Anonymer öffentlicher Lesezugriff** für Container und Blobs. Autorisierung ist nicht erforderlich. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../blobs/storage-manage-access-to-resources.md).  

Standardmäßig werden alle Ressourcen in Azure Storage gesichert und stehen nur dem Kontobesitzer zur Verfügung. Obwohl Sie jede der oben beschriebenen Autorisierungsstrategien verwenden können, um Clients Zugriff auf Ressourcen in Ihrem Speicherkonto zu gewähren, empfiehlt Microsoft im Sinne optimaler Sicherheit und Benutzerfreundlichkeit nach Möglichkeit die Verwendung von Azure AD. 



