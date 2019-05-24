---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: b6e0e57881154f5885e9f518363eda3c5b1169a0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66171269"
---
### <a name="install-via-composer"></a>Installation mithilfe von Composer
1. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json** , und fügen Sie zu dieser den folgenden Code hinzu:
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. Laden Sie **[composer.phar][composer-phar]** in das Stammverzeichnis Ihres Projekts herunter.
3. Öffnen Sie eine Eingabeaufforderung und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus
   
    ```
    php composer.phar install
    ```

Navigieren Sie alternativ zur [Azure Storage PHP-Clientbibliothek][php-sdk-github] auf GitHub, um den Quellcode zu klonen.

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar
