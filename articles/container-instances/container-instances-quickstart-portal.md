---
title: "クイックスタート - Azure ポータルでの最初の Azure Container Instances コンテナーの作成"
description: "Azure Container Instances でのデプロイおよび作業の開始"
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 823d06d8524a937a8d2098262cf97f868672f4d0
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成

Azure Container Instances を使用すると、Azure でコンテナーを簡単に作成して管理できます。 このクイックスタートでは、Azure でコンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、Azure ポータルを使用することによって完結します。 わずか数回のクリックで、お使いのブラウザーに次のように表示されます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-portal-07]

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-container-instance"></a>コンテナー インスタンスの作成

**[New] (新規)** > **[Containers] (コンテナー)** > **[Azure Container Instances (preview)] (Azure Container Instances (プレビュー))** を選択します。

![Azure ポータルで新しいコンテナー インスタンスの作成を開始][aci-portal-01]

**[Container name] (コンテナー名)**、**[Container image] (コンテナー イメージ)**、および **[Resource group] (リソース グループ)** テキスト ボックスに次の値を入力します。 その他の値は既定値のままにして、**[OK]** をクリックします。

* [Container name] (コンテナー名): `mycontainer`
* [Container image] (コンテナー イメージ): `microsoft/aci-helloworld`
* リソース グループ: `myResourceGroup`

![Azure ポータルで新しいコンテナー インスタンスの基本設定を構成][aci-portal-03]

Azure Container Instances では、Windows コンテナーと Linux コンテナーの両方を作成できます。 前のステップで Linux ベースのコンテナー (`microsoft/aci-helloworld`) を指定したので、このクイックスタートでは **Linux** の既定の設定は変更しません。

**[Configuration] (構成)** のその他の設定は既定値のままにして **[OK]** をクリックし、構成を検証します。

![Azure ポータルでの新しいコンテナー インスタンスの構成][aci-portal-04]

検証が完了したら、コンテナー設定の概要が表示されます。 **[OK]** を選択して、コンテナーのデプロイ要求を送信します。

![Azure ポータルでの新しいコンテナー インスタンスの設定概要][aci-portal-05]

デプロイが開始すると、デプロイの進捗状況を示すタイルがポータル ダッシュボードに配置されます。 デプロイが完了するとタイルが更新され、新しい **mycontainer-myc1** コンテナー グループが表示されます。

![Azure ポータルでの新しいコンテナー インスタンスの作成進捗状況][aci-portal-08]

**mycontainer-myc1** コンテナー グループを選択して、コンテナー グループのプロパティを表示します。 コンテナー グループの **[Ip address] (IP アドレス)** と、コンテナーの **[STATE] (状態)** をメモします。

![Azure ポータルでのコンテナー グループ概要][aci-portal-06]

コンテナーが **[Running] (稼働中)** 状態になったら、前のステップでメモした IP アドレスに移動して、新しいコンテナーでホストされているアプリケーションを表示します。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-portal-07]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png

## <a name="next-steps"></a>次の手順

このクイック スタートでは、パブリックの Docker Hub リポジトリ内のイメージから Azure コンテナー インスタンスを作成しました。 Azure Container Registry を使用してコンテナーのビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)