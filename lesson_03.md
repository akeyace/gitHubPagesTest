# デザインパターン入門 第三回 Proxy パターンと Decorator パターン

今回はデザインパターンから、 Proxy パターンと Decorator パターンを習得しましょう！

## 事前準備
今回は UML を扱うのに ディファクトスタンダードな、Astah Community を使います。
各自 DL をお願い致します。
http://astah.change-vision.com/ja/product/astah-community.html

デザインパターンをより解りやすくするため、PE-BANK の業務を UML化！
こちらを開いて下さい。
[PE-BANK UML図 初期状態](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_01/lessons/lesson_03)

## PE-BANK クラス図について
PE-BANK の組織をクラス図として表しています。

### interface について
OPP の概念として **interface を使う**事が**非常に重要**。

- interface を使わない場合  
  ```java
  class Tanaka {
       // 担当 PE社員毎にメンバ変数を持つ必要が生じる
       private Nagata nagata;
       private Ueda ueda;
  
       public String askToEmployee(QuestionType qType) {
           // 担当 PE社員毎に処理を実装する必要があり、新しい担当 PE が増える毎に修正が必要になる
           if (nagata != null) {
               return nagata.ask(qType);
           } else if (ueda != null){
               return ueda.ask(qType);
          }
      }
  }
  ```

- interface を使う場合  
  ```java
  class Tanaka {
      // interface をメンバ変数に定義する
      private PeEmployee currentPeEmployee;
  
      public String askToEmployee(QuestionType qType) {
          // 条件分岐が不要で、且つ担当 PE が増えても修正は不要
          return currentPeEmployee.ask(qType);
      }
  }
  ```

この様に OPP の仕様をより理解することで開発効率の大幅な向上が見込めます。

## PE-BANK クラス図に実際の業務を追加
それでは、PE-BANK クラス図を拡張していきましょう。

今回は……
- PE と PE社員で契約を結べるようにする
- PE から PE社員に質問が出来るようにする
- PE社員間で案件情報を交換出来るようにする

### 案件処理を追加
- PE と PE社員 を結ぶ契約処理を追加
  - PE社員に、案件紹介メソッドを追加
  - PE に案件受取メソッドを追加
  - PE に案件承認メソッドを追加
- 案件紹介の元となるプロジェクトを追加
  - プロジェクトクラスを追加
  - プロジェクトに状態を追加
    - プロジェクトステータス enum を追加
  - プロジェクトに状態設定メソッドを追加
  - プロジェクトに状態取得メソッドを追加
- プロジェクト参画処理を追加
  - PE社員に PE 参画メソッドを追加
  - プロジェクトに PE 参画メソッドを追加

### 仕事情報を追加
- PE社員に仕事情報取得処理、受け渡し処理を追加
- 仕事情報を追加
  - 仕事情報クラスを追加
  - 仕事情報に関するメソッドを追加

### 質問、回答を追加
- 質問を追加
  - 質問クラスを追加
  - 質問Dao を追加
  - 質問タイプを追加
- PE社員に質問メソッドを追加
- PE に質問メソッドを追加

それでは、修正されたクラス図を見てみましょう。
[PE-BANK UML図 上記修正分を追加](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_02/lessons/lesson_03)

## ソースコードを確認
上記の処理のソースコードはこちら

[PE-BANK UML図 ソースコード](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_03/lessons/lesson_03/java/src/introductiontodesignpatterns)

ソースコードを clone して 実行してみましょう！

```bash
// 新しく clone する場合
git clone -b lesson_03_03 https://github.com/akeyace/IntoroductoinToDesignPatterns.git
// 既に clone 済みの場合
git checkout lesson_03_03
```

### Eclipse で開く
1. Eclipse で 『ファイル』→ 『新規』 → 『Java プロジェクト』を実行
1. プロジェクト名に『IntroductionToDesignPatterns』 を指定
1. ロケーションにclone したディレクトリ  (IntoroductoinToDesignPatterns/lessons/lesson_03/java/) を指定
1. プロジェクトを開いたら、『実行』 → 『Java アプリケーション』 を実行
1. コンソール ウィンドウに 「Hello World!!」 と表示されたら成功

### Main.java に処理を追加
#### 田中さんに案件を渡してみる
```java
// 田中さんのインスタンス生成
Tanaka tanaka = new Tanaka();

// 永田さん、新規案件を田中さんに提案
Nagata.call().proposalProject(tanaka, new JavaProject());
// 案件の状態確認
System.out.println(tanaka.getCurrentProject().getStatus());

// 案件に同意
tanaka.agreeProject();
// 案件の状態確認
System.out.println(tanaka.getCurrentProject().getStatus());
```
実行すると……！？

#### 質問を行う
```java
// 田中さん、永田さんに質問を行う
System.out.println(tanaka.askToEmployee(QuestionType.ADDRESS_OSAKA));
System.out.println(tanaka.askToEmployee(QuestionType.ADDRESS_TOKYO));
System.out.println(tanaka.askToEmployee(QuestionType.STUDY_MEETING));
```
実行すると……！？

#### 営業さんに案件情報を渡してみる
```java

// 案件情報をセット
Nagata.call().setWorkInformation(1, "java案件", "Spring フレームワーク");
// 案件情報を取得
System.out.println(Nagata.call().getWorkInfo(1));

// 案件情報をセット
Ueda.call().setWorkInformation(2, "python案件", "機械学習");
// 案件情報を確認
System.out.println(Ueda.call().getWorkInfo(2));

// 案件を受け渡す
Nagata.call().passWorkInfo(1, Ueda.call());
// 受け渡された案件を確認
System.out.println(Ueda.call().getWorkInfo(1));
```

実行すると……！？

無事、クラス図通りに動作させる事が出来ました。
それでは、このクラス図、ソースコードを元にいよいよデザインパターンを学んで参りましょう！

## 今回学ぶデザインパターン
今回は、Proxy パターン、Decorator パターンを学びます。
それぞれ、改修時に強みを発揮するパターンになります。
是非覚えて参りましょう！

## Proxy パターン
- 目的
  - あるオブジェクトへのアクセスを制御するために、そのオブジェクトの代理、または入れ物を提供する
- 別名
  - Surrogate
- 動機（意訳)
  - ある生成コストの高い処理、オブジェクトが存在する時に、その処理をすべて読み込むのではなく、Proxy オブジェクトを呼び出された時に初めて高コストのオブジェクトを生成することで処理コストを軽減したい。  
また重い処理の代わりに、より軽い処理で要求を返したい。  
まさに所謂 Proxy として振る舞いを期待する。
- 適用可能性
  - コストの高いオブジェクトを要求があり次第生成したい (virtual proxy)
  - 実オブジェクトへのアクセスを制御したい (protection proxy)
- クラス図  
![800px-proxy_pattern_diagram svg](https://user-images.githubusercontent.com/1944039/29357586-0846f9f2-82b3-11e7-8b0b-e70051cfcf5b.png)
  - wikipedia  より
- 構成要素
  - Proxy クラス
    - RealSubject クラスにアクセスするための参照を保持する
    - RealSubject へのアクセスを制御し、時にRealSubject の処理を代行する
  - Subject クラス
    - RealSubject クラスを利用する際に Proxy オブジェクトを利用出来るようにするための RealSubject クラスと Proxy クラスの共通インタフェース
  - RealSubject クラス
    - Proxy オブジェクトが代理を務めることになる実オブジェクト
- 結果
  - Proxy パターンを用いることで、実オブジェクトへの参照が間接的になり、必要な処理が生じた場合のみ実オブジェクト生成、処理の追加、より低コストの処理を行う等が可能になる

### Proxy パターンを適用する
それでは、Proxy パターンを適用してみましょう。
今回適用出来そうな場所はどこでしょう？

感の良い方なら、解るはず！




そう！質問(Answer) クラス です！

#### クラス図修正
それでは、質問(Answer) クラスを修正していきましょう。
- 修正内容
  - 質問 クラスのステレオタイプにインタフェースを追加
  - 質問(Real)を追加し、質問インタフェースを実現する
  - 質問(Proxy) クラスを追加

修正後のクラス図はこちらになります。
[PE-BANK UML図 Proxy パターンを適用](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_04/lessons/lesson_03)


#### ソースコード修正
- 修正内容
  - Answer クラスの名前を RealAnswer に変更し、Answer インタフェースをimplements する
  - Answer インタフェースを追加
    - RealAnswerクラス のメソッドを追加
      - インタフェースなのでメソッドを 「public String getCompany();」のように記述すること
  - AnswerProxy クラスを追加
      ```java
      package introductiontodesignpatterns.answer;
      
      public class AnswerProxy implements Answer{
          private Answer answer;
      
          private Answer getAnswer() {
              if (answer == null) {
                  answer = new RealAnswer();
              }
              return answer;
          }
      
          public String getCompany() {
              return "PE-BANKやで";
          }
      
          public String getAddressTokyo() {
              return getAnswer().getAddressTokyo();
          }
      
          public String getAddressOsaka() {
              return "北浜やで";
          }
      
          public String getHolyday() {
              return "8月は山の日が休みやったで";
          }
      
          public String getEvents() {
              return getAnswer().getEvents();
          }
      
          public String getStudyMeeting() {
              return "今日 Java の勉強会あるで";
          }
      }
      ````
  - 永田クラスのコンストラクタの Answer インスタンスを AnswerProxy に変更  
    ```diff
    private Nagata() {
    	System.out.println("--- 永田さんインスタンス生成 ---");
    -	answer = new Answer();
    +	answer = new AnswerProxy();
    }
    ```
  - 上田クラスのコンストラクタの Answer インスタンスを RealAnswer に変更
  - PEEmployee クラスの Answer インスタンス生成箇所を RealAnswer  に変更

実行してログを確認すると、AnswerDao が生成されるタイミングが「System.out.println(tanaka.askToEmployee(QuestionType.ADDRESS_TOKYO));」になっている事が解ります。

### Proxy パターン まとめ
この様にインスタンス生成タイミングを処理が呼ばれた時に遅延させる事や、一部処理を書き換えて AnswerDao を経由せずに返答する事(処理コストの低減)が可能になります。

修正後のソースコードはこちらになります。
[PE-BANK ソースコード Proxy パターンを適用](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_05/lessons/lesson_03/java/src/introductiontodesignpatterns)

## Decorator パターン
- 目的
  - オブジェクトに責任(responsibility)を動的に追加する。Decorator パターンは、サブクラス化よりも柔軟な機能拡張方法を提供する
- 別名
  - wrapper
- 動機(意訳)
  - あるクラス全体にではなく、部分的に機能を追加、修正したい
    - 例えば肥大化したクラスの部分的な追加・修正を行ないたいが影響範囲を抑えたい
  - 単純に継承を行いサブクラスを作る方法もあるが、不要にサブクラスが量産される事を回避したい
    - 例えばよくある方法は……   
      ```java
      // 通常の処理
      class Process() {
        public function doAction() {
          // 何らかの処理
        }
      }
      
      // 特定状況下で処理を変えたい
      class ProcessOtherCase extends Process {
        @override
        public function doAction() {
          // 処理内容が異なる
        }
      }
      ```
- 適用可能性
  - 個々のオブジェクトに処理を動的、かつ影響範囲を小さく処理を追加する場合
  - 処理の取り外しがある場合
  - サブクラスの肥大化等により、サブクラスによる拡張が困難な場合
- クラス図  
  ![960px-decorator_uml_class_diagram svg](https://user-images.githubusercontent.com/1944039/29361022-9a72b7f0-82c0-11e7-9b04-1860b4f703ac.png)
  - wikipedia より
- 構成要素
  - Component クラス
    - 処理の動的追加が出来るようにインタフェースを定義する
  - ConcreteComponent クラス
    - 処理追加が行われるの実態オブジェクト。Component インタフェースを実現する
  - Decorator クラス
    - 実態オブジェクト(ConcereteComponent) への参照を保持する。Component インタフェースを実現する
  - ConcreteDecorator クラス
    - Component クラスに処理を追加するオブジェクト
- 結果
  - 2つのメリット、2つのデメリットを持つ
    - メリット
      - サブクラスによる実装よりも、柔軟な実装が可能
      - 実装する際の影響範囲を小さくすることが出来る
    - デメリット
      - ConcreteDecorater オブジェクト、Component オブジェクトの同一性がない
        - 実装時に注意が必要
      - エンジニアが Decorator パターンを理解していないと混乱が生じる可能性がある

### Decorator パターンを適用する

それでは、Decorator パターンを適用してみましょう。
今回適用出来そうな場所はどこでしょう？

今回は適用可能な箇所が複数あるやも。

今回は、案件情報(WorkInformation) に適用してみましょう！

#### クラス図修正
それでは、案件情報(WorkInformation) クラスを修正していきましょう。
- 修正内容
  - 案件情報クラスを拡張しやすくするため、操作: getOptions, setOptions を追加 
  - 案件情報 クラスのステレオタイプにインタフェースを追加
  - 案件情報(実態) クラスを追加し、案件情報インタフェースを実現する
  - 案件情報(Decorator)を追加し、抽象クラスとして案件情報インタフェースを実現する
  - 案件情報(メモ)を追加し、案件情報(Decorator)を継承する
  - 案件情報(単価)を追加し、案件情報(Decorator)を継承する
  - 案件情報(永田)を追加し、案件情報(Decorator)を継承する
    - 操作に getDetail, upPrice を追加

修正後のクラス図はこちらになります。
[PE-BANK UML図 Decorator パターンを適用](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_06/lessons/lesson_03)

#### ソースコード修正
- 修正内容
  - WorkInformation クラスの名前を ConcreteWorkInformation に変更し、WorkInformation インタフェースをimplements する
    - メンバ変数に options, メソッドにgetOptions, setOptions を追加する
      ```diff
        String detail;
      + HashMap<String, String> options = new HashMap<>();
     
         public void setDetail(String detail) {
           this.detail = detail;
          }
      
      + public HashMap<String, String> getOptions() {
      +   return options;
      + }
      
      + public void setOptions(String key, String value) {
      +   options.put(key, value);
      + }
      ```
  - WorkInformation インタフェースを追加
    - インタフェースなのでメソッドを 「void setWorkInformation(String workName, String detail)」のように記述すること
  - WorkInformationDecorator クラスを追加
    ```java
    package introductiontodesignpatterns.workinformation;
    
    import java.util.HashMap;
    
    abstract class WorkInformationDecorator implements WorkInformation {
      protected WorkInformation workInformation;
    
      public void setWorkInformation(String workName, String detail) {
        workInformation.setWorkInformation(workName, detail);
      }
    
      public String getWorkName() {
        return workInformation.getWorkName();
      }
    
      public void setWorkName(String workName) {
        workInformation.setWorkName(workName);
      }
    
      public String getDetail() {
        return workInformation.getDetail();
      }
    
      public void setDetail(String detail) {
        workInformation.setDetail(detail);
      }
    
      public HashMap<String, String> getOptions() {
        return workInformation.getOptions();
      }
    
      public void setOptions(String key, String value) {
        workInformation.setOptions(key, value);
      }
    }
    ````
  - WorkInformationWithMemo クラスを追加
    ```java
    package introductiontodesignpatterns.workinformation;
    
    public class WorkInformationWithMemo extends WorkInformationDecorator {
      public WorkInformationWithMemo(WorkInformation workInformation, String memo) {
        this.workInformation = workInformation;
        setOptions("memo", memo);
    
      }
    }
    ```
  - WorkInformationWithPrice クラスを追加
    ```java
    package introductiontodesignpatterns.workinformation;
    
    public class WorkInformationWithPrice extends WorkInformationDecorator {
      public WorkInformationWithPrice(WorkInformation workInformation, Number price) {
        this.workInformation = workInformation;
        setOptions("単価", price.toString());
      }
    
      public String getDetail() {
        if (Integer.parseInt(workInformation.getOptions().get("単価")) >= 600000) {
          return workInformation.getDetail() + " 高単価案件です";
        } else {
          return workInformation.getDetail();
        }
      }
    }
    ```
  - WorkInformationNagata クラスを追加
    ```java
    package introductiontodesignpatterns.workinformation;
      
    public class WorkInformationNagata extends WorkInformationDecorator {
      
      public WorkInformationNagata(WorkInformation workInformation) {
        this.workInformation = workInformation;
        this.workInformation.setWorkName(workInformation.getWorkName() + "[永田案件]");
      }
    
      public WorkInformation priceUp(Double priceUpRate) {
        this.workInformation.setOptions("単価", String
            .valueOf(Math.round(Integer.parseInt(this.workInformation.getOptions().get("単価")) * priceUpRate)));
        return this;
      }
    
      public String getDetail() {
        return workInformation.getDetail() + " オススメの案件です！";
      }
    }
    ```

   - PEEmployee クラスを修正
     ```diff
     
     public void setWorkInformation(Number id, String workName, String detail) {
     -  workInformations.put(id, new WorkInformation(workName, detail));
     +  workInformations.put(id, new ConcreteWorkInformation(workName, detail));
     }

     public ArrayList<String> getWorkInfo(Number id) {
        WorkInformation workInformation = workInformations.get(id);
     -  return new ArrayList<String>(Arrays.asList(workInformation.getWorkName(), workInformation.getDetail()));
     +  return new ArrayList<String>(Arrays.asList(workInformation.getWorkName(), workInformation.getDetail(),
        workInformation.getOptions().toString()));
      }
     ```

これで実行してみると……
ログにの案件情報に「{}」が追加されたのが解ります。
Decorator パターンは影響範囲を限定出来るのがメリット。
それでは、Decorator パターンの威力を見てみましょう

- 永田クラスでsetWorkInformationをオーバーライド
  ```java
  public void setWorkInformation(Number id, String workName, String detail, String memo, Number price) {
     workInformations.put(id,
       new WorkInformationWithPrice(
          new WorkInformationWithMemo(new ConcreteWorkInformation(workName, detail), memo), price));
  }
  ```
- Main.java の次の行を修正
  ```diff
  - Nagata.call().setWorkInformation(1, "java案件", "Spring フレームワーク");
  + Nagata.call().setWorkInformation(1, "java案件", "Spring フレームワーク", "働き易い案件", 600000);
  ```

実行してみると……。
ログの案件情報に、options の項目が追加され、しかもdetail 欄に「高単価案件」と表示されるようになりました。
さらに永田クラスを修正すると……

- 永田クラスのsetWorkInformation を書き換え
  ```java
  public void setWorkInformation(Number id, String workName, String detail, String memo, Number price) {
    workInformations.put(id,
      new WorkInformationNagata(new WorkInformationWithPrice(
        new WorkInformationWithMemo(new ConcreteWorkInformation(workName, detail), memo), price))
          .priceUp(1.2));
  }
  ```

上記、実行結果は是非ともお試しあれ。

### Proxy パターン まとめ
この様にインスタンス生成時に ConcreteDecorator を使い分ける事で処理内容が変更される事がわかりました。
これで修正時に元のクラスへの変更せず、新規クラスの追加による処理変更が行われるようになりました。

修正後のソースコードはこちらになります。
[PE-BANK ソースコード Decorator パターンを適用](https://github.com/akeyace/IntoroductoinToDesignPatterns/tree/lesson_03_07/lessons/lesson_03/java/src/introductiontodesignpatterns)

## 第三回まとめ
今回は、オブジェクトの処理をより効果的に修正するパターンを学びました。
デザインパターンはスパゲッティになりがちな設計、ソースコードをより解りやすく、より透明にします。
一見すると難しいですが、何回も繰り返し実装し、不明な点は検索して探し出す事でより深い理解へと辿り着くことが可能です。
是非ともデザインパターンを習得していきましょう！
