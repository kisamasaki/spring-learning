- Springはアノテーションの種類や用途を押さえることがポイント
- Springの中心はSpring Framework、Spring Security、Spring Boot
- Spring Frameworkが提供する機能はDIコンテナ、Spring MVC、Spring JDBC(Java Database Connectivity(接続))、宣言的トランザクション、テストサポート
- Springが提供する機能だけでアプリを作ることは可能だが、DBへのアクセスだけSpring以外のフレームワークを使うことが可能。
- インタフェースを実装(implementation)したServiceクラス名の末尾には慣例的に「Impl」を付けることが多い。
- Bean
    - DIコンテナが管理しているオブジェクト
- Bean定義
    - Beanを定義する情報のこと。代表的なものは3つ
    - ステレオタイプアノテーション
        - Beanとして管理してほしい具象クラスに付けるアノテーション。一番使用頻度が多い。
        - 代表的なステレオタイプアノテーション
            - @Service
                - 付加機能無し
            - @Repository
                - 付加機能としてDBアクセス周りの例外をSpringが提供する例外に変換可能
            - @Controller
                - 付加機能としてSpring MVC機能を利用可能
            - @Component
                - 役割を表さない汎用的なステレオアノテーション。付加機能無し。具象クラスの役割が上記に当てはまらない場合に付ける
        - @Serviceと@Componentでは前者の方が可読性が良い。
        - DIコンテナ生成時にそれぞれのクラスのコンストラクタがDIコンテナによって呼び出され、オブジェクトが生成されてBeanとして管理される。
    - @Beanメソッド
        - @Beanを付けたメソッドのこと。DIコンテナに管理してほしいオブジェクトを戻り値で返す。
    - beanタグ
        - XMLファイルに記述する。Beanとして管理してほしい具象クラスをclass属性で指定し、XMLファイルをDIコンテナに読み込ませる。
- コンフィグレーション
    - DIコンテナに読み込ませる情報。JavaConfigというJavaのクラスのように記述できるSpringの機能がある。
    - JavaConfigクラスを作成するには任意のクラスの上に@Configurationを付ける
- Application Context
    - DIコンテナの別名
- DIコンテナにインジェクションをお願いするには@Autowiredを使用する
- 具象クラスのコンストラクタに@Autowiredを付けるが、コンストラクタが一つしか定義されていない場合は省略可能。
- ステレオタイプアノテーションを具象クラスに付けた後にコンポーネントスキャンの処理が必要。
    - ステレオタイプアノテーションが付いた具象クラスを探すDIコンテナの行為。
    - コンポーネントスキャンを指示するためにはJavaConfigクラスに@ComponentScanを付ける。付けることで、クラスが所属するパッケージを起点にして、サブパッケージも含めてステレオタイプアノテーションが付いた具象クラスをDIコンテナが探す。
- DIコンテナの生成にはAnnotationConfigApplicationContextクラスをnewする。
- Beanを取得するためにはgetBeanメソッドを使う
- インジェクションの方法の種類は「コンストラクタインジェクション」「Setterインジェクション」「フィールドインジェクション」がある
    - 推奨されるのはコンストラクタインジェクション。理由はフィールドにfinal修飾子を付けることが出来るから。finalが付いたフィールドはコンストラクタ以外の場所では中身を設定できない。つまり、不用意にフィールドの中身を変更できなくする。
- 本番環境とステージング環境を切り替えたい場合、DIコンテナの機能であるプロファイルを使用する
    - 実際の開発プロジェクトでは環境ごとのコンフィグレーションをプロファイルでグルーピングする
    - コンフィグレーションをグルーピングするには@Profileを使用する
    - 使い方としてステレオタイプアノテーションを付けた具象クラスに@Profileを付けて()の中にプロファイル名を指定
    - プロファイルを指定する方法としてjavaコマンドによるシステムプロパティでの指定と環境変数での指定がある
- @Beanメソッドが呼び出される順番はDIコンテナが適切に決める
- Java標準のDBアクセスの仕組み
- Spring Data
    - Springのプロジェクトの一つ
    - リレーショナルデータベースだけでなく、NoSQLに対しても便利な機能を提供
    - Repositoryの具象クラス自動生成も行う
- Spring JDBC
    - Spring Frameworkが提供するJava標準のJDBCをより簡単に使用できる仕組み
    - JDBCを直接使用したソースコードとSpring JDBCを使用したコードでは冗長な記述が無くなる
    - Spring JDBCで主要なクラスはJdbcTemplateクラスでDBにSQLを発行するための様々なメソッドを提供している
- 検索系の処理
    - 1カラムを取得する場合
        - queryForObjectメソッドを使う
        - 複数レコードの1カラムを取得する場合はqueryForListメソッドを使う
    - レコードをMapオブジェクトに変換して取得する場合
        - queryForMapメソッドを使う
        - 複数件のレコードを取得する場合はqueryForListメソッドを使う
    - レコードをEntityオブジェクトに変換して取得する場合
        - カラム名がスネークケース、フィールド名がキャメルケースであっても紐付け可能。
        - 1レコードを取得してEntityオブジェクトに変換する場合はqueryForObjectメソッドを使用。
            - 第1引数にSELECT文の文字列を指定し、第2引数にはDataClassRowMapperオブジェクトを指定
        - 複数件取得したレコードを複数のEntityオブジェクトに変換する場合はqueryメソッドを使用。
- Springの宣言的トランザクション
    - メソッドに@Transactionalを付けると自動的にトランザクション制御が行われる
    - クラスに付けた場合はクラスが持っている全てのメソッドでトランザクション制御が行われる
- Spring Bootは「@Configuration」「@EnableAutoConfiguration」「@ComponentScan」の3つのアノテーションを含んだ@SpringBootApplicationを提供している