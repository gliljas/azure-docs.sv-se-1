---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77594015"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Hämta och installera Istio istioctl-klientens binärfil

Använd `curl` för att hämta Istio-versionen i ett bash-baserat gränssnitt på Linux-eller [Windows-undersystem för Linux][install-wsl]och extrahera `tar` sedan med enligt följande:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl` Klientens binärfil körs på klient datorn och gör att du kan interagera med Istio-tjänstens nät. Använd följande kommandon för att installera Istio `istioctl` -klientens binärfil i ett bash-baserat gränssnitt på Linux-eller Windows- [undersystem för Linux][install-wsl]. Dessa kommandon kopierar `istioctl` klientens binärfil till standard platsen för användar program i `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Om du vill att kommando rads komplettering ska slutföras `istioctl` för Istio-klientens binärfil, så gör du det på följande sätt:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10