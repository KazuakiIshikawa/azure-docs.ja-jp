---
title: "Azure Stack 統合システムの計画に関する考慮事項 | Microsoft Docs"
description: "マルチノードの Azure Stack について今すぐ計画し、準備するためにできることについて説明します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 6ba6bed8321e1ffde8bc8959443682725da36827
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Azure Stack 統合システムの計画に関する考慮事項
Azure Stack 統合システムに関心がある場合は、デプロイや、このシステムがデータセンターにどのように適合するかに関する計画のいくつかの主な考慮事項を理解する必要があります。 この記事では、Azure Stack マルチノード システムに関するインフラストラクチャの重要な決定を行うときに役立つこれらの考慮事項の概要について説明します。 これらの考慮事項を理解していると、OEM ハードウェア ベンダーと協力して Azure Stack をデータセンターにデプロイする場合に役立ちます。  

> [!NOTE]
> Azure Stack マルチノード システムは、承認されたハードウェア ベンダーからのみ購入できます。 

Azure Stack をデプロイするには、Azure Stack を環境に正しく統合するために行う必要のある一連の決定が存在します。 計画プロセス中にこれらの情報をソリューション プロバイダーに提供し、デプロイが開始される前にハードウェア ベンダーへの準備を整えて、このプロセスが迅速かつ円滑に進むようにする必要があります。

必要な情報はネットワーク、セキュリティ、および ID に関する情報全般にわたり、多くの重要な決定にはさまざまな領域の知識と意思決定者が必要になる可能性があります。 そのため、デプロイが開始される前に必要なすべての情報を確実に準備するために、組織内の複数のチームからメンバーを集めることが必要になるかもしれません。 ハードウェア ベンダーは意思決定に役立つアドバイスを持っている可能性があるため、これらの情報を収集しているときにそれらのベンダーに伝えると有効な場合があります。

必要な情報を調査および収集している間に、ネットワーク環境へのデプロイ前の構成変更がいくつか必要になることがあります。 これには、Azure Stack ソリューションのための IP アドレス領域の予約や、新しい Azure Stack ソリューション スイッチへの接続を準備するためのルーター、スイッチ、およびファイアウォールの構成が含まれる可能性があります。 計画の策定に役立つサブジェクト領域のエキスパートを確保するようにしてください。

## <a name="management-considerations"></a>管理の考慮事項
Azure Stack は、アクセス許可とネットワークの両方の観点から制限されたインフラストラクチャを持つシールド システムです。 ネットワーク アクセス制御リスト (ACL) を適用することで、承認されていないすべての受信トラフィックと、インフラストラクチャ コンポーネント間のすべての不要な通信をブロックします。 そのため、承認されていないユーザーがシステムにアクセスすることは困難です。

日常の管理と運用のために管理者がインフラストラクチャに無制限にアクセスすることはできません。 Azure Stack オペレーターは、管理者ポータルまたは Azure Resource Manager から (PowerShell または REST API を使用して) システムを管理する必要があります。 Hyper-V マネージャーやフェールオーバー クラスター マネージャーなどの他の管理ツールでシステムにアクセスすることはできません。 システム保護の目的で、サード パーティのソフトウェア (エージェントなど) を Azure Stack インフラストラクチャのコンポーネント内部にインストールすることはできないようになっています。 PowerShell または REST API を介して、管理とセキュリティ用の外部ソフトウェアとの相互運用性が提供されます。

アラート修正の手順を使用して解決できない問題のトラブルシューティングを行うために上位レベルのアクセス権が必要な場合は、サポートに問い合わせる必要があります。 サポートを通して、より高度な操作を実行するために、システムに対する一時的な管理者のフル権限を得る方法があります。 

## <a name="identity-considerations"></a>ID に関する考慮事項

### <a name="choose-identity-provider"></a>ID プロバイダーの選択
Azure Stack のデプロイに Azure AD と AD FS のどちらの ID プロバイダーを使用するかを検討する必要があります。 デプロイ後に ID プロバイダーを切り替えるには、システム全体を再デプロイする必要があります。

ID プロバイダーの選択は、テナントの仮想マシン、ID システム、使用するアカウント、Active Directory ドメインに参加できるかどうかなどには関係しません。この選択とは独立しています。

[Azure Stack 統合システムのデプロイの決定の記事](.\azure-stack-deployment-decisions.md)では、ID プロバイダーの選択に関する詳細を学習できます。

### <a name="ad-fs-and-graph-integration"></a>AD FS と Graph の統合
ID プロバイダーとして AD FS を使用して Azure Stack をデプロイする場合は、フェデレーションの信頼関係を介して Azure Stack 上の AD FS インスタンスと既存の AD FS インスタンスを統合する必要があります。 これにより、既存の Active Directory フォレスト内の ID を Azure Stack 内のリソースに対して認証できます。

Azure Stack の Graph サービスを既存の Active Directory と統合することもできます。 これにより、Azure Stack でロールベースのアクセス制御 (RBAC) を管理できます。 リソースへのアクセスが委任されると、Graph のコンポーネントは LDAP プロトコルを使用して、既存の Active Directory フォレストでユーザー アカウントを検索します。

次の図は、統合された AD FS と Graph のトラフィック フローを示しています。
![AD FS と Graph のトラフィック フローの図](media/azure-stack-deployment-planning/ADFSIntegration.PNG)

## <a name="licensing-model"></a>ライセンス モデル

使用するライセンス モデルを決める必要があります。 接続されたデプロイの場合、従量制ライセンスか容量ベースのライセンスを選択できます。 従量制では、Azure との通信を通じて課金される使用量を報告するために Azure に接続する必要があります。 インターネットに接続していないデプロイでは、容量ベースのライセンスのみがサポートされます。 ライセンス モデルの詳細については、「[Microsoft Azure Stack packaging and pricing](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)」 (Microsoft Azure Stack のパッケージと料金) を参照してください。

## <a name="naming-decisions"></a>名前付けの決定事項

Azure Stack の名前空間 (特にリージョン名) と外部のドメイン名を計画する方法について検討する必要があります。 Azure Stack のデプロイで公開されたエンドポイントの完全修飾ドメイン名 (FQDN) は、&lt;*region*&gt;&lt;*external_FQDN*&gt; という 2 つの名前の組み合わせです (たとえば *east.cloud.fabrikam.com*)。この例で Azure Stack ポータルは、次の URL で使用可能になります。

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

次の表は、これらのドメイン名前付けの決定事項をまとめたものです。

| Name | [説明] | 
| -------- | ------------- | 
|リージョン名 | 最初の Azure Stack リージョンの名前。 この名前は、Azure Stack によって管理されるパブリック仮想 IP アドレス (VIP) の FQDN の一部として使用されます。 通常、リージョン名は、データセンターの場所などの物理的な場所の識別子になります。 | 
| 外部ドメイン名 | 外部接続 VIP を持つエンドポイントのドメイン ネーム システム (DNS) ゾーンの名前。 パブリック VIP の FQDN に使用します。 | 
| プライベート (内部) ドメイン名 | インフラストラクチャ管理のために Azure Stack 上に作成されるドメイン (および内部の DNS ゾーン) の名前。 
| | |

## <a name="certificate-requirements"></a>証明書の要件

デプロイに使用するために、公開されたエンドポイントの Secure Sockets Layer (SSL) 証明書を提供する必要があります。 大まかに言うと、証明書には以下の要件があります。

- 1 つのワイルドカード証明書を使用するか、専用証明書のセットを使用することができます。記憶域や Key Vault などのエンドポイントに対してのみワイルドカードを使用できます。
- 証明書は、信頼された公的証明機関 (CA) または顧客によって管理される CA が発行できます。

Azure Stack をデプロイするために必要な PKI 証明書、およびその取得方法の詳細については、「[Azure Stack 公開キー インフラストラクチャ証明書の要件](azure-stack-pki-certs.md)」を参照してください。  


> [!IMPORTANT]
> 提供されている PKI 証明書の情報は、一般的なガイダンスとして使用してください。 Azure Stack のいずれかの PKI 証明書を取得する前に、OEM ハードウェア パートナーに連絡してください。 証明書のガイダンスと要件の詳細が提供されます。



## <a name="time-synchronization"></a>時刻同期
Azure Stack を同期するために使用される特定のタイム サーバーを選択する 必要があります。  時間の記号化は、内部サービスを互いに認証するために使用される Kerberos チケットの生成に使用されるため、Azure Stack とそのインフラストラクチャ ロールにとって重要です。

時刻同期サーバーの IP を指定する必要があります。インフラストラクチャ内のほとんどのコンポーネントは URL を解決できますが、一部は IP アドレスしかサポートできません。 切断されたデプロイのオプションを使用している場合は、Azure Stack のインフラストラクチャ ネットワークから確実に到達できる企業ネットワーク上のタイム サーバーを指定する必要があります。


## <a name="network-connectivity"></a>ネットワーク接続
このセクションでは、Azure Stack をどのようにして既存のネットワーク環境に最適に統合するかに関する重要な決定を行うときに役立つ Azure Stack ネットワーク インフラストラクチャの情報を提供します。 

> [!NOTE]
> Azure Stack から外部の DNS 名を解決するには (たとえば www.bing.com)、Azure Stack が権限のない DNS 要求の転送に使用できる DNS サーバーを提供する必要があります。 Azure Stack の DNS の要件の詳細については、「[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)」を参照してください。

### <a name="physical-network-design"></a>物理ネットワークの設計
Azure Stack ソリューションには、その操作やサービスをサポートするための回復性と可用性の高い物理インフラストラクチャが必要です。 次の図は、推奨される設計を示しています。

![推奨される Azure Stack ネットワークの設計](media/azure-stack-deployment-planning/recommended-design.png)


### <a name="logical-networks"></a>論理ネットワーク
論理ネットワークは、基礎となる物理ネットワーク インフラストラクチャの抽象化を表します。 これらは、ホスト、仮想マシン、およびサービスへのネットワーク割り当てを整理および簡略化するために使用されます。 論理ネットワーク作成の一部として、それぞれの物理的な場所にある論理ネットワークに関連付けられた仮想ローカル エリア ネットワーク (VLAN)、IP サブネット、および IP サブネット/VLAN ペアを定義するためのネットワーク サイトが作成されます。
Azure Stack のネットワーク インフラストラクチャは、スイッチ上に構成された次の論理ネットワークを使用します。

### <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ
Azure Stack のネットワーク インフラストラクチャは、スイッチ上に構成されたいくつかの論理ネットワークから成ります。 それらの論理ネットワークを次の図に示し、各論理ネットワークと TOR (top-of-rack)、ベースボード管理コントローラー (BMC)、境界 (カスタマー ネットワーク) スイッチを統合する方法を説明します。

![論理ネットワーク図とスイッチ接続](media/azure-stack-deployment-planning/NetworkDiagram.png)

#### <a name="bmc-network"></a>BMC ネットワーク
このネットワークは、すべてのベースボード管理コントローラー (サービス プロセッサとも呼ばれます。iDRAC、iLO、iBMC など) の管理ネットワークへの接続専用です。 存在する場合は、ハードウェア ライフサイクル ホスト (HLH) がこのネットワーク上に配置され、ハードウェアのメンテナンスや監視のための OEM 固有のソフトウェアを提供する可能性があります。 

#### <a name="private-network"></a>プライベート ネットワーク
この /24 (254 のホスト IP) ネットワークは Azure Stack リージョンに対してプライベートであり (Azure Stack リージョンの境界スイッチ デバイスを超えて拡張されません)、2 つのサブネットに分割されます。

- **ストレージ ネットワーク**。 スペース ダイレクトおよびサーバー メッセージ ブロック (SMB) ストレージ トラフィックの使用や仮想マシンのライブ マイグレーションをサポートするために使用される /25 (126 のホスト IP) ネットワーク。 
- **内部仮想 IP ネットワーク**。 ソフトウェア ロード バランサーのための内部のみの VIP 専用の /25 ネットワーク。

#### <a name="azure-stack-infrastructure-network"></a>Azure Stack インフラストラクチャ ネットワーク
この /24 ネットワークは、内部の Azure Stack コンポーネントが互いにデータを通信したり交換したりできるように、これらのコンポーネント専用です。 このサブネットはルーティング可能な IP アドレスを必要としますが、アクセス制御リスト (ACL) を使用してソリューションに対してプライベートに保持されます。これらのサービスの一部が外部リソースやインターネットにアクセスする必要があるときに利用する /27 ネットワークと同等のサイズである非常に小さな範囲を除き、境界スイッチを超えてルーティングされることは想定されていません。 

#### <a name="public-infrastructure-network"></a>パブリック インフラストラクチャ ネットワーク
この /27 ネットワークは、前に説明した Azure Stack インフラストラクチャ サブネットからの非常に小さな範囲であり、パブリック IP アドレスは必要ありませんが、NAT または透過プロキシ経由のインターネット アクセスが必要です。 このネットワークは、緊急回復コンソール システム (ERCS) に割り当てられます。ERCS VM は Azure への登録中にインターネット アクセスを必要とし、トラブルシューティングのために管理ネットワークにルーティング可能である必要があります。

#### <a name="public-vip-network"></a>パブリック VIP ネットワーク
パブリック VIP ネットワークは、Azure Stack 内のネットワーク コントローラーに割り当てられます。 これはスイッチ上の論理ネットワークではありません。 SLB はアドレスのプールを使用して、テナント ワークロードに /32 ネットワークを割り当てます。 スイッチのルーティング テーブルでは、これらの /32 IP は BGP 経由で使用可能なルートとしてアドバタイズされます。 このネットワークには外部からアクセス可能な、つまりパブリック IP アドレスが含まれています。 Azure Stack インフラストラクチャは、このパブリック VIP ネットワークの少なくとも 8 個のアドレスを使用し、残りはテナント VM によって使用されます。 このサブネット上のネットワーク サイズは、最小 /26 (64 のホスト) から最大 /22 (1022 のホスト) までの範囲があり、/24 ネットワークを計画することをお勧めします。

#### <a name="switch-infrastructure-network"></a>スイッチ インフラストラクチャ ネットワーク
この /26 ネットワークは、ルーティング可能なポイント ツー ポイント IP /30 (2 つのホスト IP) サブネット、およびインバンド スイッチ管理と BGP ルーター ID に専用の /32 サブネットであるループバックを含むサブネットです。 この範囲の IP アドレスは Azure Stack ソリューションの外部のデータセンターにルーティング可能である必要があり、プライベート IP またはパブリック IP のどちらでもかまいません。

#### <a name="switch-management-network"></a>スイッチ管理ネットワーク
この /29 (6 つのホスト IP) ネットワークは、スイッチの管理ポートの接続専用です。 これにより、デプロイ、管理、およびトラブルシューティングのためのアウトオブバンド アクセスが可能になります。 これは、上で説明したスイッチ インフラストラクチャ ネットワークから計算されます。

### <a name="transparent-proxy"></a>透過プロキシ
Azure Stack ソリューションは、通常の Web プロキシをサポートしていません。 データセンターですべてのトラフィックがプロキシを使用する必要がある場合は、ラックからのすべてのトラフィックをポリシーに従って処理し、ネットワーク上のゾーン間でトラフィックを分離する透過プロキシを構成する必要があります。 透過プロキシ (インターセプト、インライン、または強制プロキシとも呼ばれます) は、特殊なクライアント構成を必要とすることなく、通常の通信をネットワーク レイヤーでインターセプトします。 クライアントがプロキシの存在を意識する必要はありません。

### <a name="publish-azure-stack-services"></a>Azure Stack サービスの公開

Azure Stack の外部のユーザーに対して Azure Stack サービスを使用可能にする必要があります。 Azure Stack は、さまざまなエンドポイントをそのインフラストラクチャ ロールに応じて設定します。 それらのエンドポイントには、パブリック IP アドレス プールから VIP が割り当てられます。 デプロイ時に指定した外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。 たとえば、ユーザー ポータルに portal.*&lt;region>.&lt;fqdn>* の DNS ホスト エントリが割り当てられます。

#### <a name="ports-and-urls"></a>ポートと URL

Azure Stack サービス (ポータル、Azure Resource Manager、DNS など) を外部ネットワークに対して使用可能にするには、特定の URL、ポート、プロトコルに対して、これらのエンドポイントへの受信トラフィックを許可する必要があります。
 
透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、特定のポートと URL に外部への通信を許可する必要があります。 これには、ID、マーケットプレース シンジケーション、パッチと更新プログラム、登録、使用状況データに使用するポートと URL が該当します。

詳細については、「
- [受信用のポートとプロトコル](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [送信用のポートと URL](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Azure への Azure Stack の接続

ハイブリッド クラウド シナリオでは、Azure Stack を Azure に接続する方法を計画する必要があります。 Azure Stack 内の仮想ネットワークを Azure 内の仮想ネットワークに接続する方法は 2 つあります。 
- **サイト間**。 IPsec (IKE v1 および IKE v2) 経由の仮想プライベート ネットワーク (VPN) 接続。 この種の接続には、VPN デバイスまたは RRAS (ルーティングとリモート アクセス サービス) が必要です。 Azure での VPN Gateway の詳細については、「[VPN Gateway について](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)」を参照してください。 このトンネル経由の通信は暗号化されており、安全です。 ただし、帯域幅はトンネルの最大スループット (100 - 200 Mbps) によって制限されます。
- **送信 NAT**。 既定では、Azure Stack 内のすべての仮想マシンが送信 NAT を経由して外部ネットワークに接続できます。 Azure Stack 内に作成される各仮想ネットワークには、パブリック IP アドレスが割り当てられます。 仮想マシンにパブリック IP アドレスが直接割り当てられているか、パブリック IP アドレスを持つロード バランサーを介しているかにかかわらず、仮想ネットワークの VIP を使用して送信 NAT 経由の送信アクセスが可能です。 これは、仮想マシンによって開始され、外部ネットワーク (インターネットまたはイントラネット) を接続先とする通信についてのみ有効です。 外部から仮想マシンと通信するために使用することはできません。

#### <a name="hybrid-connectivity-options"></a>ハイブリッド接続のオプション

ハイブリッド接続に関して重要なことは、提供するデプロイの種類とデプロイ先の場所です。 ネットワーク トラフィックをテナントごとに分離する必要があるかどうか、イントラネットまたはインターネットをデプロイするかどうかを検討する必要があります。

- **シングル テナントの Azure Stack**。 少なくともネットワークの観点からは 1 つのテナントと見なされる Azure Stack のデプロイです。 多数のテナント サブスクリプションが存在できますが、イントラネット サービスと同様に、すべてのトラフィックが同じネットワークを経由して送信されます。 1 つのサブスクリプションからのネットワーク トラフィックは、別のサブスクリプションと同じネットワーク接続を経由して送信されます。暗号化されたトンネルを使用して分離する必要はありません。

- **マルチテナントの Azure Stack**。 テナント サブスクリプションごとに、Azure Stack の外部ネットワークを送信先とするトラフィックを他のテナントのネットワーク トラフィックから分離する必要がある Azure Stack のデプロイです。
 
- **イントラネット デプロイ**。 一般的に 1 つ以上のファイアウォールの内側にあるプライベート IP アドレス空間である企業のイントラネット上に配置される Azure Stack のデプロイです。 パブリック IP アドレスは、パブリック インターネット経由で直接ルーティングできないため、実際にはパブリックではありません。

- **インターネット デプロイ**。 パグリック インターネットに接続し、インターネットでルーティング可能なパブリック IP アドレスをパブリック VIP 範囲として使用する Azure Stack のデプロイです。 このデプロイはファイアウォールの内側への配置が可能でありながら、パブリック VIP 範囲はパブリック インターネットおよび Azure から直接到達可能です。
 
次の表は、ハイブリッド接続のシナリオの長所と短所、ユース ケースをまとめたものです。

| シナリオ | 接続方法 | 長所 | 短所 | 適した用途 |
| -- | -- | --| -- | --|
| シングル テナントの Azure Stack、イントラネット デプロイ | 送信 NAT | 帯域幅向上による転送の高速化。 実装が簡単。ゲートウェイは必要ありません。 | トラフィックは暗号化されません。TOR より先の分離や暗号化が行われません。 | すべてのテナントが平等に信頼されているエンタープライズ デプロイ。<br><br>Azure への Azure ExpressRoute 回線を所有している企業。 |
| マルチテナントの Azure Stack、イントラネット デプロイ | サイト間 VPN | テナント VNet から送信先へのトラフィックはセキュリティで保護されます。 | 帯域幅がサイト間 VPN トンネルによって制限されます。<br><br>仮想ネットワークにゲートウェイが、また接続先ネットワークに VPN デバイスが必要です。 | 一部のテナント トラフィックを他のテナントから分離して保護する必要があるエンタープライズ デプロイ。 |
| シングル テナントの Azure Stack、インターネット デプロイ | 送信 NAT | 帯域幅向上による転送の高速化。 | トラフィックは暗号化されません。TOR より先の分離や暗号化が行われません。 | テナント専用に Azure Stack をデプロイし、Azure Stack 環境への専用回線を使用するホスティングのシナリオ。 例として、ExpressRoute とマルチプロトコル ラベル スイッチング (MPLS) があります。
| マルチテナントの Azure Stack、インターネット デプロイ | サイト間 VPN | テナント VNet から送信先へのトラフィックはセキュリティで保護されます。 | 帯域幅がサイト間 VPN トンネルによって制限されます。<br><br>仮想ネットワークにゲートウェイが、また接続先ネットワークに VPN デバイスが必要です。 | プロバイダーがマルチテナント クラウドを提供する必要があるホスティング シナリオ。テナント相互の信頼関係がないため、トラフィックを暗号化する必要があります。
|  |  |  |  |  |

#### <a name="using-expressroute"></a>ExpressRoute の使用

シングル テナントのイントラネットとマルチテナントの両方のシナリオで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) を経由して Azure Stack を Azure に接続することができます。 [接続プロバイダー](https://docs.microsoft.com/azure/expressroute/expressroute-locations)経由でプロビジョニングされている ExpressRoute 回線が必要です。

次の図は、シングル テナント シナリオの ExpressRoute ("顧客の接続" が ExpressRoute 回線) を示しています。

![シングル テナントの ExpressRoute シナリオを示した図](media/azure-stack-deployment-planning/ExpressRouteSingleTenant.PNG)

次の図は、マルチテナント シナリオの ExpressRoute を示しています。

![マルチテナントの ExpressRoute シナリオを示した図](media/azure-stack-deployment-planning/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>外部の監視
Azure Stack のデプロイとデバイスからすべてのアラートを 1 つのビューとして取得し、チケット発行用の既存の IT サービス管理ワークフローにアラートを統合するために、外部のデータセンター監視ソリューションと Azure Stack を統合することができます。

Azure Stack ソリューションに含まれているハードウェア ライフサイクル ホストは、Azure Stack の外部にあるコンピューターで、ハードウェアを対象にした OEM ベンダー提供の管理ツールを実行しています。 それらのツールを使用することも、データセンター内の既存の監視ソリューションに直接統合されたソリューションを使用することもできます。

次の表は、現在、使用可能なオプションの一覧です。

| 領域 | 外部の監視ソリューション |
| -- | -- |
| Azure Stack ソフトウェア | - [Operations Manager 用 Azure Stack 管理パック](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios プラグイン](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>- REST ベースの API 呼び出し | 
| 物理サーバー (IPMI 経由 BMC) | - Operations Manager ベンダー管理パック<br>- OEM ハードウェア ベンダー提供のソリューション<br>- ハードウェア ベンダーの Nagios プラグイン | OEM パートナーがサポートしている監視ソリューション (含まれている) | 
| ネットワーク デバイス (SNMP) | - Operations Manager ネットワーク デバイスの検出<br>- OEM ハードウェア ベンダー提供のソリューション<br>- Nagios スイッチ プラグイン |
| テナント サブスクリプションの正常性の監視 | - [Windows Azure 用 System Center 管理パック](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

以下の要件に注意してください。
- 使用するソリューションは、エージェントレスである必要があります。 Azure Stack コンポーネント内にサード パーティ製エージェントをインストールすることはできません。 
- System Center Operations Manager を使用する場合は、Operations Manager 2012 R2 または Operations Manager 2016 が必要です。

## <a name="backup-and-disaster-recovery"></a>バックアップと障害復旧

バックアップとディザスター リカバリーの計画では、次の両方を計画に含める必要があります。つまり、基になる Azure Stack インフラストラクチャ (IaaS 仮想マシンと PaaS サービスをホストする) と、テナント アプリケーションとデータです。 これらの計画は個別に行う必要があります。

### <a name="protect-infrastructure-components"></a>インフラストラクチャ コンポーネントの保護

Azure Stack では、指定した共有にインフラストラクチャのコンポーネントがバックアップされます。

- 既存の Windows ベース ファイル サーバーまたはサード パーティのデバイス上に、外部 SMB ファイル共有が必要です。
- この同じ共有を、ネットワーク スイッチとハードウェア ライフサイクル ホストのバックアップに使用する必要があります。 これらのコンポーネントは Azure Stack の外部にあるため、これらのコンポーネントのバックアップと復元については、OEM ハードウェア ベンダーに問い合わせてください。 OEM ベンダーの推奨に基づいたバックアップ ワークフローを実行することは、お客様の責任となります。

致命的なデータ消失が発生した場合は、インフラストラクチャのバックアップを使用してデプロイ データを再生成します。デプロイ データには、デプロイの入力データや ID、サービス アカウント、CA ルート証明書、フェデレーション リソース (切断モードでのデプロイ時)、プラン、オファー、サブスクリプション、クォータ、RBAC ポリシー、ロール割り当て、Key Vault のシークレットなどが含まれます。
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>IaaS 仮想マシン上のテナント アプリケーションの保護

Azure Stack でテナント アプリケーションとデータはバックアップされません。 Azure Stack の外部にあるターゲットに対するバックアップとディザスター リカバリーの保護を計画する必要があります。 テナントの保護は、テナント ドリブン アクティビティです。 IaaS 仮想マシンに対しては、テナントはゲスト内テクノロジを使用して、ファイル フォルダー、アプリケーション データ、システム状態を保護できます。 ただし、エンタープライズまたはサービス プロバイダーの場合は、同じデータセンター内または外部のクラウドでバックアップと復元のソリューションを提供することができます。

Linux または Windows の IaaS 仮想マシンをバックアップするには、ゲスト オペレーティング システムへのアクセス権を持つバックアップ製品を使用して、ファイル、フォルダー、オペレーティング システムの状態、アプリケーション データを保護する必要があります。 Azure Backup、System Center Data Center Protection Manager、またはサポートされているサード パーティ製品を使用できます。

セカンダリ ロケーションにデータをレプリケートし、障害発生時のアプリケーションのフェールオーバーを調整するには、Azure Site Recovery またはサポートされているサード パーティの製品を使用することができます。 (統合システムの最初のリリースで、Azure Site Recovery でフェールバックはサポートされません。 ただし、手動プロセスでフェールバックを実現できます。)また、ネイティブ レプリケーション (Microsoft SQL Server など) をサポートするアプリケーションは、アプリケーションが実行されている別の場所にデータをレプリケートできます。

> [!IMPORTANT]
> 統合システムの最初のリリースで、IaaS 仮想マシンのゲスト レベルで機能する保護テクノロジがサポートされます。 基になるインフラストラクチャ サーバーにエージェントをインストールすることはできません。

## <a name="next-steps"></a>次の手順

- ユース ケース、購入、パートナー、OEM ハードウェア ベンダーの詳細については、[Azure Stack](https://azure.microsoft.com/overview/azure-stack/) の製品ページを参照してください。
- Azure Stack 統合システムのロードマップと地理的な可用性については、ホワイト ペーパー「[Azure Stack: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)」 (Azure Stack: Azure の拡張機能) を参照してください。 
