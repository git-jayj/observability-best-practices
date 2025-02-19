# OpenTelemetry を使用した Lambda ベースのサーバーレスアプリケーションのオブザーバビリティ

このガイドでは、AWS X-RayやAmazon CloudWatchなどのネイティブなAWSモニタリングサービスとともに、マネージドなオープンソースのツールとテクノロジーを使用して、Lambda ベースのサーバーレスアプリケーションのオブザーバビリティを設定するためのベストプラクティスをカバーします。 [AWS Distro for OpenTelemetry(ADOT)](https://aws-otel.github.io/docs/introduction)、[AWS X-Ray](https://aws.amazon.com/xray)、[Amazon Managed Service for Prometheus(AMP)](https://aws.amazon.com/prometheus/)などのツールと、これらのツールを使用してサーバーレスアプリケーションについての実行可能な洞察を得たり、問題をトラブルシューティングしたり、アプリケーションのパフォーマンスを最適化する方法について説明します。

## **カバーする主要トピック**

このオブザーバビリティのベストプラクティスガイドのセクションでは、次のトピックを深掘りします。

* AWS Distro for OpenTelemetry(ADOT) と ADOT Lambda Layer の概要
* ADOT Lambda Layer を使用した Lambda 関数の自動計装
* ADOT Collector のカスタム構成のサポート
* Amazon Managed Service for Prometheus(AMP) との統合
* ADOT Lambda Layer 使用の長所と短所
* ADOT 使用時のコールドスタート待ち時間の管理

## **AWS Distro for OpenTelemetry(ADOT)の概要**

[AWS Distro for OpenTelemetry(ADOT)](https://aws-otel.github.io/docs/introduction) は、Cloud Native Computing Foundation(CNCF)の[OpenTelemetry(OTel)](https://opentelemetry.io/) プロジェクトのセキュアで実稼働準備が整った AWS サポート付きディストリビューションです。ADOT を使用することで、アプリケーションに一度だけインスツルメンテーションを適用し、相関したメトリクスとトレースを複数のモニタリングソリューションに送信できます。 

AWS のマネージド [OpenTelemetry Lambda Layer](https://aws-otel.github.io/docs/getting-started/lambda) は、[OpenTelemetry Lambda Layer](https://github.com/open-telemetry/opentelemetry-lambda) を利用して、AWS Lambda から非同期的にテレメトリデータをエクスポートします。AWS Lambda 関数をラップし、OpenTelemetry ランタイム固有の SDK、ADOT コレクターのトリムダウンバージョンをパッケージ化することで、プラグアンドプレイなユーザーエクスペリエンスを提供します。このレイヤーには AWS Lambda 関数の自動インスツルメンテーションのためのすぐに使える設定が付属しています。ADOT Lambda Layer コレクターコンポーネントである Receivers、Exporters、Extensions は、Amazon CloudWatch、Amazon OpenSearch Service、Amazon Managed Service for Prometheus、AWS X-Ray などとの統合をサポートしています。完全なリストは[こちら](https://github.com/aws-observability/aws-otel-lambda)をご覧ください。ADOT は[パートナーソリューション](https://aws.amazon.com/otel/partners)との統合もサポートしています。

ADOT Lambda Layer は、Python、NodeJS、Java 向けの自動インスツルメンテーションと、特定のライブラリや SDK セットのカスタムインスツルメンテーションの両方をサポートしています。自動インスツルメンテーションでは、デフォルトで Lambda Layer がトレースを AWS X-Ray にエクスポートするように構成されています。カスタムインスツルメンテーションの場合は、対応するランタイムインスツルメンテーションライブラリを各 [OpenTelemetry ランタイムインスツルメンテーションレポジトリ](https://github.com/open-telemetry) から含め、関数内での初期化のためにコードを変更する必要があります。

## **AWS Lambda での ADOT Lambda レイヤーを使用した自動計装**

コード変更なしで、ADOT Lambda レイヤーを使用して Lambda 関数の自動計装を簡単に有効にできます。 既存の Java ベースの Lambda 関数に ADOT Lambda レイヤーを追加し、CloudWatch で実行ログとトレースを表示する例を見ていきましょう。

1. `runtime`、`region`、`arch type` に基づいて、[ドキュメント](https://aws-otel.github.io/docs/getting-started/lambda) の Lambda レイヤーの ARN を選択します。 Lambda レイヤーは Lambda 関数と同じリージョンで使用するようにしてください。 たとえば、Java の自動計装用 Lambda レイヤーは `arn:aws:lambda:us-east-1:901920570463:layer:aws-otel-java-agent-x86_64-ver-1-28-1:1` です。
2. コンソールまたは IaC を使用して、Lambda 関数にレイヤーを追加します。
    * コンソールを使用する場合は、[手順](https://docs.aws.amazon.com/lambda/latest/dg/adding-layers.html) に従って Lambda 関数にレイヤーを追加します。 ARN を指定 に上記で選択したレイヤー ARN を貼り付けます。
    * IaC オプションを使用する場合、Lambda 関数の SAM テンプレートは次のようになります。
    ```
    Layers:
    - !Sub arn:aws:lambda:${AWS::Region}:901920570463:layer:aws-otel-java-agent-arm64-ver-1-28-1:1
    ```
3. Node.js または Java の場合は `AWS_LAMBDA_EXEC_WRAPPER=/opt/otel-handler`、Python の場合は `AWS_LAMBDA_EXEC_WRAPPER=/opt/otel-instrument` という環境変数を Lambda 関数に追加します。
4. Lambda 関数でアクティブトレースを有効にします。 **`Note`** デフォルトでは、レイヤーはトレースを AWS X-Ray にエクスポートするように構成されています。 Lambda 関数の実行ロールに必要な AWS X-Ray アクセス許可があることを確認してください。 AWS Lambda の AWS X-Ray アクセス許可の詳細については、[AWS Lambda ドキュメント](https://docs.aws.amazon.com/lambda/latest/dg/services-xray.html#services-xray-permissions) を参照してください。
    * `Tracing: Active`
5. Lambda レイヤー構成、環境変数、X-Ray トレースを使用した SAM テンプレートの例は次のようになります。
```
Resources:
  ListBucketsFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: com.example.App::handleRequest
      ...
      ProvisionedConcurrencyConfig:
        ProvisionedConcurrentExecutions: 1
      Policies:
        - AWSXrayWriteOnlyAccess
        - AmazonS3ReadOnlyAccess
      Environment:
        Variables:
          AWS_LAMBDA_EXEC_WRAPPER: /opt/otel-handler
      Tracing: Active
      Layers:
        - !Sub arn:aws:lambda:${AWS::Region}:901920570463:layer:aws-otel-java-agent-amd64-ver-1-28-1:1
      Events:
        HelloWorld:
          Type: Api
          Properties:
            Path: /listBuckets
            Method: get
```
6. AWS X-Ray でのトレースのテストと可視化
Lambda 関数を直接または API (トリガーとして構成されている場合) 経由で呼び出します。 たとえば、API 経由で Lambda 関数を呼び出す ( `curl` を使用) と、次のようなログが生成されます。
```
curl -X GET https://XXXXXX.execute-api.us-east-1.amazonaws.com/Prod/listBuckets
```
Lambda 関数のログ: 
<pre><code>
INIT_START Runtime Version: java:11.v23 Runtime Version ARN: arn:aws:lambda:us-east-1::runtime:d8547b2b4b9c3a87d99a42a8197838256f11e0afe5f7ce2d58e4574f4e6cf50d
<b>{level:info,msg:Launching OpenTelemetry Lambda extension,version:v0.32.0}</b> 
{level:info,logger:telemetryAPI.Listener,msg:Listening for requests,address:sandbox:53612}
{level:info,logger:telemetryAPI.Client,msg:Subscribing,baseURL:http://127.0.0.1:9001/2022-07-01/telemetry}  
<b>{level:info,logger:telemetryAPI.Client,msg:Subscription success,response:\OK\}</b>
{level:info,caller:service/telemetry.go:84,msg:Setting up own telemetry...} 
{level:info,caller:service/telemetry.go:201,msg:Serving Prometheus metrics,address:localhost:8888,level:Basic}
{level:info,caller:exporter@v0.82.0/exporter.go:275,msg:Development component. May change in the future.,kind:exporter,data_type:metrics,name:logging}
{level:info,caller:service/service.go:132,msg:Starting aws-otel-lambda...,Version:v0.32.0,NumCPU:2}  
<b>{level:info,caller:extensions/extensions.go:30,msg:Starting extensions...}</b>
{level:info,caller:otlpreceiver@v0.82.0/otlp.go:83,msg:Starting GRPC server,kind:receiver,name:otlp,data_type:traces,endpoint:localhost:4317} 
{level:info,caller:otlpreceiver@v0.82.0/otlp.go:101,msg:Starting HTTP server,kind:receiver,name:otlp,data_type:traces,endpoint:localhost:4318}
{level:info,caller:service/service.go:149,msg:Everything is ready. Begin running and processing data.}
<b>Picked up JAVA_TOOL_OPTIONS: -javaagent:/opt/opentelemetry-javaagent.jar</b>  
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended
[otel.javaagent 2023-09-24 15:28:16:862 +0000] [main] INFO io.opentelemetry.javaagent.tooling.VersionLogger - opentelemetry-javaagent - version: 1.28.0-adot-lambda1-aws  
EXTENSION Name: collector State: Ready Events: [INVOKE, SHUTDOWN]  
START RequestId: ed8f8444-3c29-40fe-a4a1-aca7af8cd940 Version: 3
...
Event### {resource: /listBuckets,path: /listBuckets/,httpMethod: GET,headers: {Accept=*/*, CloudFront-Forwarded-Proto=https, CloudFront-Is-Desktop-Viewer=true, CloudFront-Is-Mobile-Viewer=false, CloudFront-Is-SmartTV-Viewer=false, CloudFront-Is-Tablet-Viewer=false, CloudFront-Viewer-ASN=7224, CloudFront-Viewer-Country=US, Host=yf0ukeu0sg.execute-api.us-east-1.amazonaws.com, User-Agent=curl/8.1.2, Via=2.0 31341771a4bfa40d7b1f61883ffb56c6.cloudfront.net (CloudFront), X-Amz-Cf-Id=ooZO2eehCAwGzPW0nq6NKOwKLQGBkmhtuFCzsRhyCISbnex6c45Jcw==, X-Amzn-Trace-Id=Root=1-65105691-384f7da75714148655fa631b, X-Forwarded-For=15.248.0.124, 15.158.50.42, X-Forwarded-Port=443, X-Forwarded-Proto=https},multiValueHeaders: {Accept=[*/*], CloudFront-Forwarded-Proto=[https], CloudFront-Is-Desktop-Viewer=[true], CloudFront-Is-Mobile-Viewer=[false], CloudFront-Is-SmartTV-Viewer=[false], CloudFront-Is-Tablet-Viewer=[false], CloudFront-Viewer-ASN=[7224], CloudFront-Viewer-Country=[US], Host=[yf0ukeu0sg.execute-api.us-east-1.amazonaws.com], User-Agent=[curl/8.1.2], Via=[2.0 31341771a4bfa40d7b1f61883ffb56c6.cloudfront.net (CloudFront)], X-Amz-Cf-Id=[ooZO2eehCAwGzPW0nq6NKOwKLQGBkmhtuFCzsRhyCISbnex6c45Jcw==], X-Amzn-Trace-Id=[Root=1-65105691-384f7da75714148655fa631b], X-Forwarded-For=[15.248.0.124, 15.158.50.42], X-Forwarded-Port=[443], X-Forwarded-Proto=[https]},requestContext: {accountId: 681808143105,resourceId: 7xxz9z,stage: Prod,requestId: 56c8c9ad-3d8d-4f20-aed1-bcef03b5e8e4,identity: {sourceIp: 15.248.0.124,userAgent: curl/8.1.2,},resourcePath: /listBuckets,httpMethod: GET,apiId: yf0ukeu0sg,path: /Prod/listBuckets/,},isBase64Encoded: false}  
...
END RequestId: ed8f8444-3c29-40fe-a4a1-aca7af8cd940
REPORT RequestId: ed8f8444-3c29-40fe-a4a1-aca7af8cd940 Duration: 5144.38 ms Billed Duration: 5145 ms Memory Size: 1024 MB Max Memory Used: 345 MB Init Duration: 27769.64 ms  
<b>XRAY TraceId: 1-65105691-384f7da75714148655fa631b SegmentId: 2c52a147021ebd20 Sampled: true</b>
</code></pre>

ログからわかるように、OpenTelemetry Lambda 拡張機能は opentelemetry-javaagent を使用して Lambda 関数の計装とリスニングを開始し、AWS X-Ray でトレースを生成します。

上記の Lambda 関数呼び出しからのトレースを表示するには、AWS X-Ray コンソールに移動し、[Traces] の下のトレース ID を選択します。 次のようなトレースマップとセグメントタイムラインが表示されるはずです。
![Lambda Insights](../../../images/Serverless/oss/xray-trace.png)

## **ADOT Collector のカスタム設定のサポート**

ADOT Lambda Layer は、OpenTelemetry SDK と ADOT Collector のコンポーネントを組み合わせています。ADOT Collector の設定は OpenTelemetry 標準に従います。デフォルトでは、ADOT Lambda Layer は [config.yaml](https://github.com/aws-observability/aws-otel-lambda/blob/main/adot/collector/config.yaml) を使用し、これはテレメトリデータを AWS X-Ray にエクスポートします。ただし、ADOT Lambda Layer は他のエクスポーターもサポートしているため、メトリクスとトレースを他のデスティネーションに送信することもできます。カスタム設定でサポートされている利用可能なコンポーネントの完全なリストは[こちら](https://github.com/aws-observability/aws-otel-lambda/blob/main/README.md#adot-lambda-layer-available-components) を参照してください。

## **Amazon Managed Service for Prometheus(AMP) との統合**

カスタムコレクター設定を使用して、Lambda 関数からメトリクスを Amazon Managed Prometheus(AMP)にエクスポートできます。

1. 自動計測のステップに従って、Lambda レイヤーを構成し、環境変数 `AWS_LAMBDA_EXEC_WRAPPER` を設定します。
2. Lambda 関数がメトリクスを送信する先となる、AWS アカウント内に [Amazon Manager Prometheus ワークスペースを作成](https://docs.aws.amazon.com/ja_jp/prometheus/latest/userguide/AMP-onboard-create-workspace.html) します。AMP ワークスペースから `Endpoint - remote write URL` をメモします。これは、ADOT コレクター設定で設定する必要があります。
3. Lambda 関数のルートディレクトリに、前のステップの AMP エンドポイントリモートライト URL の詳細を含むカスタム ADOT コレクター設定ファイル(collector.yaml など)を作成します。設定ファイルは S3 バケットから読み込むこともできます。
ADOT コレクター設定ファイルのサンプル:
```
#collector.yaml in the root directory
#Set an environemnt variable 'OPENTELEMETRY_COLLECTOR_CONFIG_FILE' to '/var/task/collector.yaml'

extensions:
  sigv4auth:
    service: "aps"
    region: "<workspace_region>"

receivers:
  otlp:
    protocols:
      grpc:
      http:

exporters:
  logging:
  prometheusremotewrite:
    endpoint: "<workspace_remote_write_url>"
    namespace: test
    auth:
      authenticator: sigv4auth

service:
  extensions: [sigv4auth]
  pipelines:
    traces:
      receivers: [otlp]
      exporters: [awsxray]
    metrics:
      receivers: [otlp]
      exporters: [logging, prometheusremotewrite]
```
Prometheus リモートライトエクスポーターは、再試行とタイムアウトの設定でも構成できます。詳細は、[ドキュメント](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/prometheusremotewriteexporter/README.md)を参照してください。 **`注`** sigv4auth 拡張機能のサービス値は aps (Amazon Prometheus Service) である必要があります。また、Lambda 関数の実行ロールに必要な AMP アクセス許可があることを確認してください。AMP で AWS Lambda に必要なアクセス許可とポリシーの詳細については、Amazon Managed Service for Prometheus の[ドキュメント](https://docs.aws.amazon.com/ja_jp/prometheus/latest/userguide/AMP-and-IAM.html#AMP-IAM-policies-built-in)を参照してください。

4. 環境変数 `OPENTELEMETRY_COLLECTOR_CONFIG_FILE` を追加し、設定ファイルへのパスを値として設定します。例: /var/task/`<config file="" path="">`.yaml。これにより、Lambda レイヤー拡張機能がコレクター設定を見つける場所が指示されます。
```
Function:
    Type: AWS::Serverless::Function
    Properties:
      ...
      Environment:
        Variables:
          OPENTELEMETRY_COLLECTOR_CONFIG_FILE: /var/task/collector.yaml
```
5. Lambda 関数のコードを更新して、OpenTelemetry メトリクス API を使用してメトリクスを追加します。こちらの例を確認してください。
```
// get meter
Meter meter = GlobalOpenTelemetry.getMeterProvider()
    .meterBuilder("aws-otel")
    .setInstrumentationVersion("1.0")
    .build();

// Build counter e.g. LongCounter
LongCounter counter = meter
    .counterBuilder("processed_jobs")
    .setDescription("Processed jobs")
    .setUnit("1")
    .build();

// It is recommended that the API user keep a reference to Attributes they will record against
Attributes attributes = Attributes.of(stringKey("Key"), "SomeWork");

// Record data
counter.add(123, attributes);
```

</config></workspace_remote_write_url></workspace_region>

## **ADOT Lambdaレイヤーの使用のメリットとデメリット**

Lambda関数からAWS X-Rayにトレースを送信する場合、[X-Ray SDK](https://docs.aws.amazon.com/xray/latest/devguide/xray-sdk-nodejs.html) または [AWS Distro for OpenTelemetry (ADOT) Lambdaレイヤー](https://aws-otel.github.io/docs/getting-started/lambda) を使用できます。X-Ray SDKは、さまざまなAWSサービスの計装を容易にサポートしていますが、トレースをX-Rayにのみ送信できます。一方、Lambdaレイヤーの一部として含まれているADOTコレクターは、各言語の多数のライブラリの計装をサポートしています。これを使用して、メトリクスとトレースをAWS X-RayやAmazon CloudWatch、Amazon OpenSearch Service、Amazon Managed Service for Prometheusなどの他のモニタリングソリューションに収集および送信できます。 

ただし、ADOTが提供する柔軟性のため、Lambda関数で追加のメモリが必要になり、コールドスタートのレイテンシに顕著な影響が生じる可能性があります。したがって、低レイテンシのLambda関数を最適化していて、OpenTelemetryの高度な機能を必要としていない場合は、ADOTよりもAWS X-Ray SDKを使用する方が適している場合があります。比較と適切なトレースツールの選択のためのガイダンスの詳細については、ADOTとX-Ray SDKの[選択に関するAWSドキュメント](https://docs.aws.amazon.com/xray/latest/devguide/xray-instrumenting-your-app.html#xray-instrumenting-choosing)を参照してください。

## **ADOT を使用する際のコールドスタート待ち時間の管理**

Java 用の ADOT Lambda Layer はエージェントベースであるため、自動計測を有効にすると、Java エージェントは OTel で[サポートされている](https://github.com/open-telemetry/opentelemetry-java-instrumentation/tree/main/instrumentation)すべてのライブラリを計測しようとします。これにより、Lambda 関数のコールドスタート待ち時間が大幅に増加します。したがって、アプリケーションで使用されているライブラリ/フレームワークのみ自動計測を有効にすることをおすすめします。

特定の計測のみを有効にするには、次の環境変数を使用できます:

* `OTEL_INSTRUMENTATION_COMMON_DEFAULT_ENABLED`: false に設定すると、Layer の自動計測が無効になり、各計測を個別に有効にする必要があります。
* `OTEL_INSTRUMENTATION_<name>_ENABLED`: 特定のライブラリまたはフレームワークの自動計測を有効にするには true に設定します。<name> を有効にする計測で置き換えます。使用可能な計測のリストについては、特定のエージェント計測の抑制を参照してください。

たとえば、Lambda と AWS SDK の自動計測のみを有効にするには、次の環境変数を設定します:

```
OTEL_INSTRUMENTATION_COMMON_DEFAULT_ENABLED=false
OTEL_INSTRUMENTATION_AWS_LAMBDA_ENABLED=true
OTEL_INSTRUMENTATION_AWS_SDK_ENABLED=true
```

</name></name>

## **その他のリソース**

* [OpenTelemetry](https://opentelemetry.io)
* [AWS Distro for OpenTelemetry (ADOT)](https://aws-otel.github.io/docs/introduction)
* [ADOT Lambda Layer](https://aws-otel.github.io/docs/getting-started/lambda)

## **まとめ**

このオブザーバビリティのベストプラクティスガイドでは、オープンソーステクノロジを使用した AWS Lambda ベースのサーバレスアプリケーションのためのものです。AWS Distro for OpenTelemetry(ADOT) と Lambda レイヤーの使用方法と、AWS Lambda 関数へのインスツルメンテーションの方法をカバーしました。自動インスツルメンテーションの有効化と ADOT コレクターのカスタマイズの方法をシンプルな設定で説明し、複数のデスティネーションへのオブザーバビリティ シグナルの送信ができることを示しました。ADOT の利点と欠点、Lambda 関数のコールドスタート待ち時間への影響、コールドスタート時間の管理のベストプラクティスを強調しました。これらのベストプラクティスを採用することで、アプリケーションを一度だけインスツルメントして、ログ、メトリクス、トレースをベンダーに依存しない形で複数のモニタリングソリューションに送信できます。

より深い解析のために、[AWS One Observability ワークショップ](https://catalog.workshops.aws/observability/ja-JP) の AWS マネージド オープンソース Observability モジュールを実践することを強くおすすめします。
