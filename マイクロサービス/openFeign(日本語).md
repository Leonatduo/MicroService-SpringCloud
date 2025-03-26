# OpenFeign リモート呼び出し簡略化ソリューション

## 背景問題
前のセクションでは、Nacosを使用してサービスガバナンスを実現し、RestTemplateを使用してサービスのリモート呼び出しを実装しました。しかし、以下の問題が存在します：
- リモート呼び出しのコードが複雑すぎる
- ローカルメソッド呼び出しとの差異が大きく、プログラミング体験が統一されていない

## OpenFeignソリューション
OpenFeignは宣言型HTTPクライアントを通じて、リモート呼び出しをローカルメソッド呼び出しのように簡単にします。その核心は4つのキーポイントを処理することです：
1. リクエスト方式
2. リクエストパス
3. リクエストパラメータ
4. 戻り値の型

OpenFeignはSpringMVCアノテーションを使用してこれらのパラメータを宣言し、動的プロキシに基づいてリモート呼び出しコードを生成します

## 4.1 クイックスタート

### 4.1.1 依存関係の導入
`cart-service`の`pom.xml`に追加：

```xml
<!-- openFeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

<!-- ロードバランサー -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

### 4.1.2 OpenFeignの有効化
Spring Bootアプリケーションの起動クラスに`@EnableFeignClients`アノテーションを追加：

```java
@SpringBootApplication
@EnableFeignClients  // OpenFeign機能を有効化
public class CartServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(CartServiceApplication.class, args);
    }
}
```

### 4.1.3 OpenFeignクライアントの作成

#### 基本使用方法
Feignクライアントを作成するには、サービス呼び出し用のインターフェースを定義します。実装クラスは不要で、インターフェースの宣言だけで機能します。

```java
@FeignClient("item-service")  // 呼び出すサービス名を指定
public interface ItemClient {
    
    @GetMapping("/items")  // GETメソッドとエンドポイントURLを指定
    List<ItemDTO> queryItemsByIds(@RequestParam("ids") Collection<Long> ids);
}
```

このインターフェースでは、メソッドの実装は不要で、以下の重要な情報を宣言するだけで構いません：
- @FeignClient("item-service") ：呼び出すサービスの登録名を指定
- @GetMapping ：HTTP GETメソッドを使用することを宣言
- @GetMapping("/items") ：呼び出すAPIのエンドポイントパスを定義
- @RequestParam("ids") Collection<Long> ids ：呼び出すAPIのエンドポイントパスを定義
- List<ItemDTO> ：呼び出すAPIのエンドポイントパスを定義



