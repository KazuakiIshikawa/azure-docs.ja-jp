---
title: "Azure Machine Learning の ALM | Microsoft Docs"
description: "Azure Machine Learning Studio でのアプリケーション ライフサイクル管理のベスト プラクティスの適用"
keywords: "ALM、AML、Azure ML、アプリケーション ライフ サイクル管理、バージョン管理"
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 1be6577d-f2c7-425b-b6b9-d5038e52b395
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: haining
ms.openlocfilehash: 9d1fcc761115c64fafb811d6ca1c2389babfdc15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio でのアプリケーション ライフサイクル管理
Azure Machine Learning Studio は、Azure クラウド プラットフォームで運用できる機械学習の実験を開発するツールです。 Visual Studio IDE に似ており、1 つのプラットフォームにマージされているスケーラブルなクラウド サービスです。 さまざまな資産のバージョン管理から、自動化された実行とデプロイまで、標準的なアプリケーション ライフサイクル管理 (ALM) プラクティスを Azure Machine Learning Studio に組み込むことができます。 この記事では、一部のオプションとアプローチについて説明します。

## <a name="versioning-experiment"></a>実験のバージョン管理
実験のバージョン管理に推奨される方法は 2 つあります。 組み込みの実行履歴に依存する方法と、JavaScript Object Notation (JSON) 形式で実験をエクスポートして外部で管理する方法です。 それぞれのアプローチには長所と短所があります。

### <a name="experiment-snapshots-using-run-history"></a>実行履歴を使用した実験のスナップショット
Azure Machine Learning Studio の学習実験の実行モデルでは、実験エディターで **[実行]** ボタンをクリックするたびに、実験の変更不可スナップショットがジョブ スケジューラに送信されます。 このスナップショットの一覧は、実験エディター ビューのコマンド バーで、**[実行履歴]** ボタンをクリックして表示できます。

![[実行履歴] ボタン](./media/version-control/runhistory.png)

実験が実行のために送信されてスナップショットが取得された時点の実験の名前をクリックすると、スナップショットをロック モードで開くことができます。 現在の実験を表す、一覧の最初の項目のみが編集できる状態になります。 また、各スナップショットは、[Finished (Partial run) (完了 (部分的に実行))]、[失敗]、[Failed (Partial run) (失敗 (部分的に実行))]、[ドラフト] などのさまざまな状態になります。

![Run History list](./media/version-control/runhistorylist.png)

開いたら、スナップショットの実験を新しい実験として保存し、それを変更できます。 実験のスナップショットにトレーニング済みのモデル、変換、データセットなど、更新バージョンがある資産が含まれている場合、スナップショットにはスナップショット取得時の元のバージョンへの参照が保持されます。 ロックされているスナップショットを新しい実験として保存する場合は、Azure Machine Learning Studio によってこれらの資産の新しいバージョンの存在が検出され、新しい実験に自動的に更新されます。

実験を削除すると、その実験でのすべてのスナップショットが削除されます。

### <a name="exportimport-experiment-in-json-format"></a>JSON 形式で実験のエクスポートとインポート
実行履歴のスナップショットは、実行のために送信されるたびに実験の変更不可バージョンを Azure Machine Learning Studio に保持します。 実験のローカル コピーを保存し、Team Foundation Server などのお気に入りのソース管理システムにチェックインして、後でそのローカル ファイルから実験を再作成することもできます。 [Azure Machine Learning PowerShell](http://aka.ms/amlps) コマンドレット [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) と [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) を使用して、それを実行できます。

JSON ファイルは実験グラフのテキスト表現ですが、データセットやトレーニング済みのモデルなど、ワークスペース内の資産への参照を含む場合があります。 その資産のシリアル化されたバージョンは含みません。 このため、JSON ドキュメントをワークスペースにもう一度インポートしようとする場合、これらの参照先資産は実験で参照されているのと同じ資産 ID で既に存在する必要があります。 そうでない場合はインポートされた実験にアクセスできません。

## <a name="versioning-trained-model"></a>トレーニング済みのモデルのバージョン管理
Azure Machine Learning のトレーニング済みのモデルは .iLearner ファイルと呼ばれる形式にシリアル化され、ワークスペースに関連付けられている Azure Blob Storage アカウントに格納されます。 .iLearner ファイルのコピーを取得する 1 つの方法は、再トレーニング API を使用することです。 この[記事](retrain-models-programmatically.md)では、再トレーニング API の動作について説明します。 手順の概要は次のとおりです。

1. トレーニング実験を設定します。
2. モデルのトレーニング モジュール、またはモデル ハイパーパラメーターの調整や R モデルの作成など、トレーニング済みのモデルを生成するモジュールに、Web サービス出力ポートを追加します。
3. トレーニング実験を実行し、モデルのトレーニング Web サービスとしてデプロイします。
4. トレーニング Web サービスの BES エンドポイントを呼び出し、必要な .iLearner ファイル名とそれを格納する Blob Storage アカウントの場所を指定します。
5. BES の呼び出しが終了した後で、生成された .iLearner ファイルを収集します。

.ILearner ファイルを取得するもう 1 つの方法は、PowerShell コマンドレット [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) を使用することです。 モデルをプログラムによって再トレーニングする必要なしに iLearner ファイルのコピーを取得する場合には、このほうが簡単になります。

トレーニング済みのモデルを含む .iLearner ファイルを作成したら、独自のバージョン管理戦略を採用できます。 接頭辞/接尾辞を名前付け規則として適用して Blob Storage に .iLearner ファイルを残したり、バージョン管理システムにコピー/インポートしたりといった簡単な戦略にできます。

保存されている .iLearner ファイルは、デプロイされた Web サービスを通じてスコアリングに使用できます。

## <a name="versioning-web-service"></a>バージョン管理 Web サービス
Azure Machine Learning の実験から、2 種類の Web サービスをデプロイできます。 従来の Web サービスは、実験およびワークスペースと緊密に結合されています。 新しい Web サービスは Azure Resource Manager のフレームワークを活用しており、元の実験とワークスペースのどちらとも結合されなくなりました。

### <a name="classic-web-service"></a>従来の Web サービス
従来の Web サービスをバージョン管理するには、Web サービス エンドポイントのコンストラクトを利用できます。 典型的なフローを次に示します。

1. 予測実験から、既定のエンドポイントを含む新しい従来の Web サービスをデプロイします。
2. 現在のバージョンの実験/トレーニング済みのモデルを公開する ep2 という名前の新しいエンドポイントを作成します。
3. 戻って予測実験とトレーニング済みのモデルを更新します。
4. 予測実験を再デプロイすると、それによって既定のエンドポイントが更新されます。 しかし、ep2 は変更されません。
5. ep3 という名前の追加のエンドポイントを作成します。新しいバージョンの実験とトレーニング済みのモデルを公開するエンドポイントです。
6. 必要に応じて手順 3 に戻ります。

時間の経過と共に多くのエンドポイントが同じ Web サービスで作成される場合があります。 それぞれが特定の時点のバージョンのトレーニング済みのモデルを含む特定の時点の実験のコピーを表します。 次に、外部のロジックを使用して、呼び出すエンドポイントを決定します。これは事実上、スコア付けの実行用のトレーニング済みのモデルのバージョンを選択することを意味します。

また、多くの同じ Web サービス エンドポイントを作成し、異なるバージョンの .iLearner ファイルのパッチをエンドポイントに適用して、同様の効果を実現することもできます。 この[記事](create-models-and-endpoints-with-powershell.md)では、これを実現する方法について詳しく説明します。

### <a name="new-web-service"></a>新しい Web サービス
新しい Azure Resource Manager ベースの Web サービスを作成する場合は、エンドポイント コンストラクトを使用できなくなります。 代わりに、[Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) PowerShell コマンドレットを使用するか、デプロイされた Resource Manager ベースの Web サービスから [*Export-AzureRmMlWebservice*](https://msdn.microsoft.com/library/azure/mt767935.aspx) PowerShell コマンドレットを使用して、予測実験から Web サービス定義 (WSD) ファイルを JSON 形式で生成できます。

WSD ファイルをエクスポートしてそれをバージョン管理した後で、別の Azure リージョンで別の Web サービス プランの新しい Web サービスとして WSD をデプロイすることもできます。 適切なストレージ アカウント構成だけではなく、新しい Web サービス プラン ID を指定してください。 異なる .iLearner ファイルにパッチを適用するには、WSD ファイルを変更し、トレーニング済みのモデルの場所の参照を更新して、新しい Web サービスとしてそれをデプロイします。

## <a name="automate-experiment-execution-and-deployment"></a>実験の実行とデプロイの自動化
ALM の重要な側面は、アプリケーションの実行とデプロイのプロセスを自動化できるようにすることです。 Azure Machine Learning では、[PowerShell モジュール](http://aka.ms/amlps)を使用してこれを実現できます。 ここでは、[Azure Machine Learning Studio PowerShell モジュール](http://aka.ms/amlps)を使用することによる、標準的な ALM 自動化実行/デプロイ プロセスに関連するエンド ツー エンドの手順の例を示します。 各手順は、その手順の実行に使用できる 1 つまたは複数の PowerShell コマンドレットにリンクされています。

1. [データセットをアップロード](https://github.com/hning86/azuremlps#upload-amldataset)します。
2. [ワークスペース](https://github.com/hning86/azuremlps#copy-amlexperiment)または[ギャラリー](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery)からワークスペースにトレーニング実験をコピーするか、[エクスポート](https://github.com/hning86/azuremlps#export-amlexperimentgraph)された実験をローカル ディスクから[インポート](https://github.com/hning86/azuremlps#import-amlexperimentgraph)します。
3. トレーニング実験で[データセットを更新](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)します。
4. [トレーニング実験を実行](https://github.com/hning86/azuremlps#start-amlexperiment)します。
5. [トレーニング済みのモデルを昇格](https://github.com/hning86/azuremlps#promote-amltrainedmodel)させます。
6. ワークスペースに[予測実験をコピー](https://github.com/hning86/azuremlps#copy-amlexperiment)します。
7. 予測実験で[トレーニング済みのモデルを更新](https://github.com/hning86/azuremlps#update-amlexperimentuserasset)します。
8. [予測実験を実行](https://github.com/hning86/azuremlps#start-amlexperiment)します。
9. 予測実験から [Web サービスをデプロイ](https://github.com/hning86/azuremlps#new-amlwebservice)します。
10. Web サービス [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) または [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint) エンドポイントをテストします。

## <a name="next-steps"></a>次のステップ
* [Azure Machine Learning Studio PowerShell](http://aka.ms/amlps) モジュールをダウンロードし、ALM タスクの自動化を開始します。
* PowerShell と再トレーニング API を通じて、[1 つの実験を使用して多数の ML モデルを作成、管理](create-models-and-endpoints-with-powershell.md)する方法を説明します。
* [Azure Machine Learning Web サービスのデプロイ](publish-a-machine-learning-web-service.md)の詳細について説明します。
