---
title: "Sysdig による Azure コンテナー サービス クラスターの監視"
description: "Sysdig を使って Azure コンテナー サービス クラスターを監視します。"
services: container-service
author: sauryadas
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d694744665ef6399560fc12c6976c2d88d232148
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-sysdig"></a>Sysdig による Azure コンテナー サービス クラスターの監視

この記事では、Azure コンテナー サービス クラスター内のすべてのエージェント ノードに Sysdig エージェントをデプロイします。 この構成を行うためには、Sysdig のアカウントが必要です。 

## <a name="prerequisites"></a>前提条件
Azure Container Service によって構成されたクラスターを[デプロイ](container-service-deployment.md)して[接続](../container-service-connect.md)してください。 [Marathon UI](container-service-mesos-marathon-ui.md)の詳細を確認してください。 [http://app.sysdigcloud.com](http://app.sysdigcloud.com) にアクセスして Sysdig クラウド アカウントをセットアップしてください。 

## <a name="sysdig"></a>Sysdig
Sysdig は監視サービスです。クラスター内のコンテナーを監視することができます。 Sysdig は、トラブルシューティングに役立つことで知られていますが、その一方で CPU やネットワーク、メモリ、I/O の基本的な監視メトリックも備えています。 Sysdig を利用することで、負荷の最も高い (基本的にメモリと CPU の使用率が最も高い) コンテナーを簡単に見分けることができます。 次の画面は [Overview (概要)] セクションにあります (現在はベータ版)。 

![Sysdig UI](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Marathon を使用した Sysdig のデプロイの構成
以降、Marathon を使用して Sysdig アプリケーションを構成し、クラスターにデプロイする方法について説明します。 

[http://localhost:80/](http://localhost:80/) を使用して DC/OS の UI にアクセスします。DC/OS の UI が表示されたら、左下にある "Universe" に移動し、"Sysdig" を探します。

![Sysdig in DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig1.png)

この構成作業には、Sysdig クラウド アカウントまたは無料試用版アカウントが必要です。 Sysdig クラウドの Web サイトへのログイン後に自分のユーザー名をクリックすると、該当する "アクセス キー" がそのページに表示されます。 

![Sysdig API key](./media/container-service-monitoring-sysdig/sysdig2.png) 

次に、DC/OS Universe の Sysdig 構成に自分のアクセス キーを入力します。 

![Sysdig configuration in the DC/OS Universe](./media/container-service-monitoring-sysdig/sysdig3.png)

インスタンス数は「10000000」に設定してください。これでクラスターに新しいノードが追加されると、その新しいノードに対し、Sysdig によって自動的にエージェントがデプロイされます。 これは、クラスターに新しく追加されたすべてのエージェントを確実にデプロイ対象とするための暫定的な措置です。 

![Sysdig configuration in the DC/OS Universe-instances](./media/container-service-monitoring-sysdig/sysdig4.png)

パッケージをインストールしたら、Sysdig の UI に戻ります。クラスター内のコンテナーの使用状況に関する各種メトリックを詳しく調査できる状態になっています。 

[新しいダッシュボード ウィザード](https://app.sysdigcloud.com/#/dashboards/new)を使用して、Mesos と Marathon 固有のダッシュボードをインストールすることもできます。
