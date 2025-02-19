# Amazon CloudWatch - FAQ

**なぜ Amazon CloudWatch を選択する必要がありますか?**

Amazon CloudWatch は、AWS のクラウドネイティブサービスで、単一のプラットフォーム上で AWS クラウドリソースと AWS 上で実行するアプリケーションの統合されたオブザーバビリティを提供します。Amazon CloudWatch は、[ログ](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html)、[メトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)、[イベント](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)の形式でモニタリングと運用データを収集し、[アラーム](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) を設定するために使用できます。また、AWS 上および [オンプレミスのサーバー](https://aws.amazon.com/blogs/mt/how-to-monitor-hybrid-environment-with-aws-services/)で実行されている AWS リソース、アプリケーション、サービスの [統合ビュー](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) も提供します。Amazon CloudWatch は、リソースの利用状況、アプリケーションのパフォーマンス、ワークロードの運用状態のシステム全体の可視性を高めるのに役立ちます。Amazon CloudWatch は、AWS、ハイブリッド、オンプレミスのアプリケーションとインフラストラクチャリソースの [アクショナブルなインサイト](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Insights-Sections.html) を提供します。 [クロスアカウントのオブザーバビリティ](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Unified-Cross-Account.html) は、CloudWatch の統合されたオブザーバビリティ機能の追加です。

**どの AWS サービスが Amazon CloudWatch および Amazon CloudWatch Logs とネイティブに統合されていますか?**

Amazon CloudWatch は、70 を超える AWS サービスとネイティブに統合されており、お客様はアクションなしでインフラストラクチャメトリクスを収集し、モニタリングとスケーラビリティを簡素化できます。サポートされている [CloudWatch メトリクスをパブリッシュする AWS サービスの完全なリスト](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html)はドキュメントをご確認ください。現在、30 を超える AWS サービスが CloudWatch にログをパブリッシュしています。サポートされている [CloudWatch Logs にログをパブリッシュする AWS サービスの完全なリスト](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/aws-services-sending-logs.html)はドキュメントをご確認ください。

**すべての AWS サービスから Amazon CloudWatch にパブリッシュされるメトリクスのリストはどこで入手できますか?**

[メトリクスをパブリッシュするすべての AWS サービスのリスト](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/aws-services-cloudwatch-metrics.html)は、AWS ドキュメントにあります。

**Amazon CloudWatch へのメトリクスの収集とモニタリングを開始するにはどうすればよいですか?**

[Amazon CloudWatch は](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)、さまざまな AWS サービスからメトリクスを収集します。これらは [AWS Management Console、AWS CLI、API](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/viewing_metrics_with_cloudwatch.html) を通じて表示できます。 Amazon CloudWatch は、Amazon EC2 インスタンスの [利用可能なメトリクス](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/viewing_metrics_with_cloudwatch.html)を収集します。カスタムメトリクスの場合、統合 CloudWatch エージェントを使用して収集とモニタリングを行うことができます。

> 関連する AWS Observability ワークショップ: [メトリクス](https://catalog.workshops.aws/observability/ja-JP/aws-native/metrics)

**私の Amazon EC2 インスタンスには非常に細かいレベルのモニタリングが必要です。どうすればいいですか?**

デフォルトでは、Amazon EC2 はインスタンスの基本モニタリングとして 5 分ごとにメトリックデータを CloudWatch に送信します。インスタンスのメトリックデータを 1 分ごとに CloudWatch に送信するには、インスタンスで [詳細モニタリング](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-cloudwatch-new.html) を有効にできます。

**アプリケーションの独自のメトリクスをパブリッシュするオプションはありますか?**

お客様は、API または CLI を使用して、標準の分解能で 1 分の粒度、または高分解能の粒度を 1 秒間隔まで下げて、独自の [カスタムメトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/publishingMetrics.html) を CloudWatch にパブリッシュすることもできます。

統合された CloudWatch エージェントは、Linux で動作する Elastic Network Adapter (ENA) を使用する Amazon EC2 インスタンス上の [ネットワークパフォーマンスメトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-network-performance.html) や Linux サーバーの [NVIDIA GPU メトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-NVIDIA-GPU.html)、Linux と Windows サーバーの個々のプロセスの [プロセスメトリクス](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-procstat-process-metrics.html) を収集するなど、特殊なシナリオのカスタムメトリクスを収集することもサポートしています。

> 関連する AWS Observability ワークショップ: [カスタムメトリクスのパブリッシュ](https://catalog.workshops.aws/observability/ja-JP/aws-native/metrics/publishmetrics)

**Amazon CloudWatch エージェントを使用したカスタムメトリクスの収集で、どのようなサポートが利用できますか?**

アプリケーションやサービスからのカスタムメトリクスは、[StatsD](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-statsd.html) または [collectd](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html) プロトコルをサポートする統合 CloudWatch エージェントを使用して取得できます。StatsD は、幅広いアプリケーションからメトリクスを収集できる一般的なオープンソースソリューションです。StatsD は、Linux および Windows ベースのサーバーの両方をサポートしており、独自のメトリクスを計装するのに特に便利です。collectd プロトコルは、幅広いアプリケーションのシステム統計を収集できる Linux サーバーでのみサポートされている一般的なオープンソースソリューションです。

**私のワークロードには多数の短期的なリソースが含まれており、高基数のログが生成されています。メトリクスとログを収集および測定するための推奨アプローチは何ですか?**

[CloudWatch 埋め込みメトリックフォーマット](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch_Embedded_Metric_Format.html) を使用すると、お客様はログの形式で複雑な高基数アプリケーションデータを取り込み、Lambda 関数やコンテナなどの短期的なリソースから実行可能なメトリクスを生成できます。これにより、お客様は個別のコードを計装または維持することなく、詳細なログイベントデータとともにカスタムメトリクスを埋め込むことができ、ログデータに対する強力な分析機能を得ると同時に、CloudWatch がカスタムメトリクスを自動的に抽出してデータの視覚化とリアルタイムのインシデント検出のためのアラームの設定を支援できます。

> 関連する AWS Observability ワークショップ: [Embedded Metric Format](https://catalog.workshops.aws/observability/ja-JP/aws-native/metrics/emf)

**Amazon CloudWatch Logs へのログの収集とモニタリングを開始するにはどうすればよいですか?**

[Amazon CloudWatch Logs](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/WhatIsCloudWatchLogs.html) は、既存のシステム、アプリケーション、カスタムログファイルを使用して、システムとアプリケーションのトラブルシューティングとモニタリングをほぼリアルタイムで行うのに役立ちます。 お客様は[統合 CloudWatch エージェント](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/logs/CWL_GettingStarted.html)をインストールして、[Amazon EC2 インスタンスとオンプレミスのサーバーからのログを CloudWatch に収集](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)できます。

> 関連する AWS Observability ワークショップ: [Log Insights](https://catalog.workshops.aws/observability/ja-JP/aws-native/logs/logsinsights)

**CloudWatch エージェントとは何ですか。なぜそれを使用する必要がありますか?**

[統合 CloudWatch エージェント](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) は、MIT ライセンスの下でオープンソースソフトウェアであり、x86-64 および ARM64 アーキテクチャを利用したほとんどのオペレーティングシステムをサポートしています。CloudWatch エージェントは、オペレーティングシステム全体で Amazon EC2 インスタンスとハイブリッド環境のオンプレミスサーバーからシステムレベルのメトリクスを収集し、アプリケーションやサービスからカスタムメトリクスを取得し、Amazon EC2 インスタンスとオンプレミスサーバーからのログを収集するのに役立ちます。

**環境にはさまざまな規模のインストールが必要です。CloudWatch エージェントは通常どのようにインストールされますか?自動化はどうでしょうか?**

Linux と Windows サーバーを含むすべてのサポートされているオペレーティングシステムで、お客様は [コマンドライン](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/installing-cloudwatch-agent-commandline.html) を使用して CloudWatch エージェントをダウンロードおよび[インストール](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-EC2-Instance.html)したり、AWS [Systems Manager](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/installing-cloudwatch-agent-ssm.html) を使用したり、AWS [CloudFormation テンプレート](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent-New-Instances-CloudFormation.html)を使用したりできます。また、モニタリングのためにオンプレミスサーバーに [CloudWatch エージェントをインストール](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-on-premise.html) することもできます。

**組織には複数の AWS アカウントとリージョンがあります。Amazon CloudWatch はこれらのシナリオで機能しますか。**

Amazon CloudWatch は、[クロスアカウントのオブザーバビリティ](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Unified-Cross-Account.html)を提供し、リージョン内の複数のアカウントにまたがるリソースとアプリケーションの正常性を監視およびトラブルシューティングするのに役立ちます。Amazon CloudWatch は、[クロスアカウントおよびクロスリージョンのダッシュボード](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Cross-Account-Cross-Region.html)も提供します。この機能により、お客様はマルチアカウントおよびマルチリージョンのリソースとワークロードの可視性と洞察力を得ることができます。

**Amazon CloudWatch の自動化サポートは何がありますか?**

AWS Management Console を通じて Amazon CloudWatch にアクセスする以外に、お客様は API、[AWS コマンドラインインターフェイス (CLI)](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/getting-started-install.html)、[AWS SDK](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/sdk-general-information-section.html) を介してサービスにアクセスすることもできます。 メトリクスとダッシュボードの [CloudWatch API](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/APIReference/Welcome.html) は、[AWS CLI](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/cli/Welcome.html) を介して、またはソフトウェア/製品と統合することで、リソースとアプリケーションの管理または管理に費やす時間を減らすことができる自動化を支援します。 ログ用の [CloudWatch API](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatchLogs/latest/APIReference/Welcome.html) と [AWS CLI](https://docs.aws.amazon.com/ja_jp/cli/latest/reference/logs/index.html) も個別に利用できます。 追加のリファレンスのために、お客様向けの [CloudWatch で AWS SDK を使用したコード例](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/service_code_examples.html) が用意されています。

**リソースのモニタリングをすぐに開始したいのですが、推奨されるアプローチは何ですか?**

CloudWatch の自動ダッシュボードは、すべての AWS パブリックリージョンで利用可能であり、すべての AWS リソースの正常性とパフォーマンスの集約ビューを提供します。これにより、お客様はすぐにモニタリングを開始し、メトリクスとアラームに基づいてリソースベースのビューを取得し、パフォーマンスの問題の根本原因を簡単に特定できます。 [自動ダッシュボード](https://docs.aws.amazon.com/ja_jp/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/cloudwatch-dashboards-visualizations.html) は、AWS サービスの推奨ベストプラクティスに基づいて構築されており、リソースを認識したままで、重要なパフォーマンスメトリクスの最新の状態を反映するように動的に更新されます。

関連する AWS Observability ワークショップ: [自動ダッシュボード](https://catalog.workshops.aws/observability/ja-JP/aws-native/dashboards/autogen-dashboard)

**CloudWatch でモニタリングしたいものをカスタマイズするには、推奨されるアプローチは何ですか?**

[カスタムダッシュボード](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/create_dashboard.html)を使用すると、お客様はさまざまなウィジェットを使用して必要な数の追加のダッシュボードを作成し、必要に応じてカスタマイズできます。 カスタムダッシュボードを作成するとき、選択とカスタマイズのために使用できるさまざまなウィジェットタイプがあります。

関連する AWS Observability ワークショップ: [ダッシュボード](https://catalog.workshops.aws/observability/ja-JP/aws-native/ec2-monitoring/dashboarding)

**カスタムダッシュボードをいくつか構築しました。共有する方法はありますか?**

はい、[CloudWatch ダッシュボードの共有](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/cloudwatch-dashboard-sharing.html) が可能です。共有には 3 つの方法があります。 リンクへのアクセス権を持つすべてのユーザーがダッシュボードを表示できるように、単一のダッシュボードをパブリックに共有します。 ダッシュボードを表示できる人の電子メールアドレスを指定して、単一のダッシュボードをプライベートに共有します。 サードパーティのシングルサインオン (SSO) プロバイダーを指定して、アカウント内のすべての CloudWatch ダッシュボードを共有します。

> 関連する AWS Observability ワークショップ: [CloudWatch ダッシュボードの共有](https://catalog.workshops.aws/observability/ja-JP/aws-native/dashboards/sharingdashboard)

**AWS リソースを含むアプリケーションの可視性を向上させるにはどうすればよいですか?**

[Amazon CloudWatch Application Insights](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/cloudwatch-application-insights.html) は、SQL Server データベース、.Net ベースの Web (IIS) スタック、アプリケーションサーバー、OS、ロードバランサーなど、アプリケーションと基盤となる AWS リソースの可視性を容易にします。 アプリケーションリソースとテクノロジスタック全体の主要なメトリクスとログを特定および設定するのに役立ちます。 これにより、平均修復時間 (MTTR) が短縮され、アプリケーションの問題をより迅速にトラブルシューティングできます。

> 追加の詳細は FAQ をご覧ください: [AWS リソースとカスタムメトリクスのモニタリング](https://aws.amazon.com/jp/cloudwatch/faqs/#AWS_resource_.26_custom_metrics_monitoring)

**組織はオープンソース中心で、Amazon CloudWatch を使用してオープンソーステクノロジーを通じてモニタリングと可視性をサポートしています。**

メトリクスとトレースの収集のために、[AWS Distro for OpenTelemetry (ADOT) Collector](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-open-telemetry.html) と CloudWatch エージェントを Amazon EC2 インスタンスに並行してインストールし、OpenTelemetry SDK を使用して Amazon EC2 インスタンス上で実行されているワークロードからアプリケーショントレースとメトリクスを収集できます。

Amazon CloudWatch で OpenTelemetry メトリクスをサポートするには、[OpenTelemetry Collector 用の AWS EMF Exporter](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/exporter/awsemfexporter) が OpenTelemetry 形式のメトリクスを CloudWatch Embedded Metric Format(EMF) に変換します。これにより、OpenTelemetry メトリクスと統合されたアプリケーションが高基数の [アプリケーションメトリクスを CloudWatch に送信](https://aws-otel.github.io/docs/getting-started/adot-eks-add-on/config-cloudwatch) できるようになります。

ログの場合、Fluent Bit は、ログの保持と分析のために Amazon CloudWatch を含む AWS サービスに [Amazon EC2 からのログをストリーミング](https://docs.fluentbit.io/manual/pipeline/outputs/cloudwatch) するための簡単な拡張ポイントを作成します。 新しくリリースされた [Fluent Bit プラグイン](https://github.com/aws/amazon-cloudwatch-logs-for-fluent-bit#new-higher-performance-core-fluent-bit-plugin) は、Amazon CloudWatch にログをルーティングできます。

ダッシュボードの場合、Amazon Managed Grafana に [Amazon CloudWatch をデータソース](https://docs.aws.amazon.com/ja_jp/grafana/latest/userguide/using-amazon-cloudwatch-in-AMG.html)として追加できます。これは、Grafana ワークスペースコンソールの AWS データソース構成オプションを使用することによって実現されます。 この機能により、既存の CloudWatch アカウントを検出し、CloudWatch へのアクセスに必要な認証情報の構成を管理することで、CloudWatch をデータソースとして追加するプロセスが簡略化されます。

**ワークロードには Prometheus からのメトリクス収集がすでに構築されています。同じ方法論を使用し続けることができますか。**

お客様は、可観測性のニーズのために完全なオープンソースのセットアップを選択できます。 これについては、AWS Distro for OpenTelemetry (ADOT) Collector を、Prometheus で計装されたアプリケーションからスクレイピングし、メトリクスを Prometheus Server または Amazon Managed Prometheus に送信するように構成できます。

EC2 インスタンス上の CloudWatch エージェントは、インストールおよび構成して、[Prometheus からメトリクスをスクレイプ](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-PrometheusEC2.html) して CloudWatch でモニタリングできます。 これは、EC2 上のコンテナワークロードを好み、オープンソースの Prometheus モニタリングと互換性のあるカスタムメトリクスが必要なお客様に役立ちます。

CloudWatch の [Prometheus 用コンテナインサイトのモニタリング](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/ContainerInsights-Prometheus.html) は、コンテナ化されたシステムとワークロードからの Prometheus メトリクスの自動検出を自動化します。 Prometheus メトリクスの検出は、Amazon Elastic Container Service (Amazon ECS)、Amazon Elastic Kubernetes Service (Amazon EKS)、および Amazon EC2 インスタンス上で実行されている Kubernetes クラスターでサポートされています。

**ワークロードにはマイクロサービスコンピューティング、特に EKS/Kubernetes 関連のコンテナが含まれています。環境への洞察を得るために Amazon CloudWatch をどのように使用できますか?**

お客様は、[CloudWatch Container Insights](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/ContainerInsights.html) を使用して、[Amazon Elastic Kubernetes Service (Amazon EKS)](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html) または Amazon EC2 上の Kubernetes プラットフォームで実行されているコンテナ化されたアプリケーションとマイクロサービスからメトリクスとログを収集、集約、要約できます。 [Container Insights](https://aws.amazon.com/jp/cloudwatch/faqs/#Container_Monitoring) は、Amazon EKS 用 Fargate でデプロイされたクラスターからのメトリクスの収集もサポートしています。 CloudWatch は、CPU、メモリ、ディスク、ネットワークなど、多くのリソースのメトリクスを自動的に [収集](https://docs.aws.amazon.com/ja_jp/AmazonCloudWatch/latest/monitoring/Container-Insights-metrics.html) し、コンテナの再起動失敗などの診断情報を提供して、問題を隔離および迅速に解決するのに役立ちます。

> 関連する AWS Observability ワークショ
