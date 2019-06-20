---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244629"
---
| Entität                                       | Konventionen   |
|----------------------------------------------|-----------------------------------------------------|
| Containernamen für Blockblob und Seitenblob | Es muss sich um einen gültigen DNS-Namen mit einer Länge von 3 bis 63 Zeichen handeln. <br>  Sie muss mit einem Buchstaben oder einer Zahl beginnen. <br> Nur Kleinbuchstaben, Zahlen und Bindestriche (-) sind zulässig. <br> Vor und nach jedem Bindestrich (-) muss unmittelbar ein Buchstabe oder eine Ziffer stehen. <br> Aufeinanderfolgende Bindestriche sind in Namen nicht zulässig. |
| Freigabenamen für Azure-Dateien                  | Wie oben                                          |
| Verzeichnis- und Dateinamen für Azure-Dateien     |<li> Groß-/Kleinschreibung wird beibehalten, aber nicht berücksichtigt, maximal 255 Zeichen. </li><li> Schrägstrich (/) als letztes Zeichen nicht erlaubt. </li><li>Falls eingegeben, erfolgt eine automatische Entfernung. </li><li> Folgende Zeichen sind nicht zulässig: <code>" \\ / : \| < > * ?</code></li><li> Reservierte URL-Zeichen müssen korrekt mit Escapezeichen versehen sein. </li><li> Ungültige Zeichen im URL-Pfad sind unzulässig. Codepunkte wie „\\uE000“ sind keine gültigen Unicode-Zeichen. Einige ASCII- und Unicode-Zeichen, z. B. Steuerzeichen (0x00 bis 0x1F, \\u0081 usw.), sind ebenfalls unzulässig. Regeln für Unicode-Zeichenfolgen in HTTP/1.1 finden Sie unter „RFC 2616, Section 2.2: Basic Rules“ und „RFC 3987“. </li><li> Die folgenden Dateinamen sind unzulässig: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, Punkt (.) und zwei Punkte (..).</li>|
| Blobnamen für Blockblobs und Seitenblobs      | </li><li>Für Blobnamen wird die Groß-/Kleinschreibung beachtet, und sie können eine beliebige Kombination von Zeichen enthalten. </li><li>Ein Blobname muss zwischen 1 und 1.024 Zeichen lang sein. </li><li>Reservierte URL-Zeichen müssen korrekt mit Escapezeichen versehen sein. </li><li>Die Anzahl von Pfadsegmenten, aus denen der Blobname besteht, darf 254 nicht überschreiten. Ein Pfadsegment ist die Zeichenfolge zwischen aufeinanderfolgenden Trennzeichen (z.B. der Schrägstrich (/)), die für den Namen eines virtuellen Verzeichnisses steht.</li> |
