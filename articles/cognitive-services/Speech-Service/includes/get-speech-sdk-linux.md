---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135635"
---
:::row:::
    :::column span="3":::
        Das Speech SDK unterstützt bei Verwendung mit Linux nur **Ubuntu 16.04/18.04/20.04** , **Debian 9/10** , **Red Hat Enterprise Linux (RHEL) 7/8** und **CentOS 7/8** auf den folgenden Zielarchitekturen:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für die C++-Entwicklung
- x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für Java
- x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für .NET Core
- x64 für Python

> [!IMPORTANT]
> Für C# unter Linux ARM64 ist .NET Core 3.x (Paket „dotnet-sdk-3.x“) erforderlich.

### <a name="system-requirements"></a>Systemanforderungen

Bei einer nativen Anwendung basiert das Speech SDK auf `libMicrosoft.CognitiveServices.Speech.core.so`. Stellen Sie sicher, dass die Zielarchitektur (x86, x64) mit der Anwendung übereinstimmt. Abhängig von der Linux-Version können zusätzliche Abhängigkeiten erforderlich sein.

- Freigegebene Bibliotheken der GNU C-Bibliothek (einschließlich der POSIX Threads Programming-Bibliothek `libpthreads`)
- OpenSSL-Bibliothek (`libssl.so.1.0.0` oder `libssl.so.1.0.2`)
- Freigegebene Bibliothek für ALSA-Anwendungen (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> Wenn „libssl1.0.x“ nicht verfügbar ist, installieren Sie stattdessen „libssl1.1“.

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> Wenn „libssl1.0.x“ nicht verfügbar ist, installieren Sie stattdessen „libssl1.1“.

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 und CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - Befolgen Sie in RHEL/CentOS 7 die Anweisungen zum [Konfigurieren von RHEL/CentOS 7 für das Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Befolgen Sie unter RHEL/CentOS 8 die Anweisungen zum [Konfigurieren von OpenSSL für Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
