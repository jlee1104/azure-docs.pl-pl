---
title: Samouczek dotyczący usługi Kubernetes na platformie Azure — wdrażanie klastra
description: W tym samouczku dotyczącym usługi Azure Kubernetes Service (AKS) utworzysz klaster usługi AKS i nawiążesz połączenie z węzłem głównym usługi Kubernetes za pomocą narzędzia kubectl.
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: bc31a4197b08cbeb1a99820d7ff490f20147c7bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78191269"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Samouczek: wdrażanie klastra usługi Azure Kubernetes Service (AKS)

Usługa Kubernetes zapewnia rozproszoną platformę dla konteneryzowanych aplikacji. Za pomocą usługi AKS można szybko utworzyć klaster Kubernetes gotowy do użycia w środowisku produkcyjnym. W tym samouczku (część trzecia z siedmiu) w usłudze AKS jest wdrażany klaster Kubernetes. Omawiane kwestie:

> [!div class="checklist"]
> * Wdrażanie klastra usługi AKS usługi Kubernetes, który może być uwierzytelniony w rejestrze kontenerów platformy Azure
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

W dodatkowych samouczkach aplikacja Azure Vote jest wdrażana w klastrze, skalowana i aktualizowana.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W poprzednich samouczkach utworzono obraz kontenera i przekazano go do wystąpienia usługi Azure Container Registry. Jeśli nie wykonano tych kroków, a chcesz kontynuować pracę, zacznij od części [Samouczek 1 — tworzenie obrazów kontenera][aks-tutorial-prepare-app].

Ten samouczek wymaga, aby uruchomić platformę Azure CLI w wersji 2.0.75 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Tworzenie klastra Kubernetes

Klastry usługi AKS mogą używać kontroli dostępu opartej na rolach (RBAC) rozwiązania Kubernetes. Te kontrolki umożliwiają zdefiniowanie dostępu do zasobów na podstawie ról przypisanych użytkownikom. Uprawnienia są łączone, jeśli użytkownikowi przypisano wiele ról, a zakres uprawnień można ograniczyć do jednej przestrzeni nazw lub do całego klastra. Domyślnie interfejs wiersza polecenia platformy Azure automatycznie włącza kontrolę dostępu opartą na rolach podczas tworzenia klastra usługi AKS.

Utwórz klaster usługi AKS za pomocą polecenia [az aks create][]. W poniższym przykładzie tworzony jest klaster o nazwie *myAKSCluster* w grupie zasobów o nazwie *myResourceGroup*. Ta grupa zasobów została utworzona w ramach [poprzedniego samouczka][aks-tutorial-prepare-acr]. Aby zezwolić klastrowi AKS na interakcję z innymi zasobami platformy Azure, jest tworzony automatycznie podmiot usługi Azure Active Directory, ponieważ nie określono jednego. W tym miejscu ten podmiot usługi ma [prawo do ściągania obrazów][container-registry-integration] z wystąpienia usługi Azure Container Registry (ACR), które zostało utworzone w poprzednim samouczku.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Można również ręcznie skonfigurować jednostkę usługi do ściągania obrazów z usługi ACR. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie usługi ACR za pomocą podmiotów usługi](../container-registry/container-registry-auth-service-principal.md) lub [Uwierzytelnianie z kubernetes z kluczem tajnym ściągania](../container-registry/container-registry-auth-kubernetes.md).

Po kilku minutach wdrażanie zostanie zakończone i zwróci informacje o wdrożeniu usługi AKS w formacie JSON.

> [!NOTE]
> Aby upewnić się, że klaster działa niezawodnie, należy uruchomić co najmniej 2 (dwa) węzły.

## <a name="install-the-kubernetes-cli"></a>Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes

Aby nawiązać połączenie z klastrem Kubernetes z komputera lokalnego, należy użyć narzędzia [kubectl][kubectl], czyli klienta wiersza polecenia usługi Kubernetes.

Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Możesz także zainstalować je lokalnie za pomocą polecenia [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Nawiązywanie połączenia z klastrem przy użyciu narzędzia kubectl

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][]. Poniższy przykład umożliwia pobranie poświadczeń dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Aby zweryfikować połączenie z klastrem, uruchom polecenie [kubectl get nodes,][kubectl-get] aby zwrócić listę węzłów klastra:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono klaster Kubernetes w usłudze AKS i skonfigurowano narzędzie `kubectl` w celu nawiązania z nim połączenia. W tym samouczku omówiono:

> [!div class="checklist"]
> * Wdrażanie klastra usługi AKS usługi Kubernetes, który może być uwierzytelniony w rejestrze kontenerów platformy Azure
> * Instalowanie interfejsu wiersza polecenia rozwiązania Kubernetes (kubectl)
> * Konfigurowanie narzędzia kubectl w celu nawiązania połączenia z klastrem AKS

Przejdź do następnego samouczka, aby dowiedzieć się, jak wdrożyć aplikację w klastrze.

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w rozwiązaniu Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
