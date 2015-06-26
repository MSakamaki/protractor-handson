
# protractor のデバッグ


### [参考文献](https://github.com/angular/protractor/blob/master/docs/debugging.md)

protractorのデバッグ方法は大きく分けて２つあります。

`browser.pause();`

を使う方法と

`browser.debugger();`

を使う方法です。

##`browser.pause();`でのデバッギング

この機能は、画面を一時停止したいときに使います。

停止を開場させたい場合は`Ctrl+C`で止める事が出来ます。

用途としては、Protractorのテストで一時的に止めて、ブラウザ上の振る舞いを確認したいときに使います。

テストコードのデバックはできません。

### 実行コマンド

```sh
protractor conf.js
```

### spec.js

```javascript
describe('Protractor Demo App', function() {
  it('should add one and two', function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
    element(by.model('first')).sendKeys(3);
    element(by.model('second')).sendKeys(2);

    //ここで一時とまる。
    browser.pause();

    element(by.id('gobutton')).click();

    expect(element(by.binding('latest')).getText()).
        toEqual('5');
  });
});
```

---

## `browser.debugger();`でのデバッグ

次に紹介するのは、protractorのテストコードを含めてデバッグする手法です。

[node debugger](https://nodejs.org/api/debugger.html)を使い、デバッグしています。

画面、テストコードを含めての詳細なデバッグで使用します。

### 実行コマンド

```sh
protractor debug conf.js
```

### spec.js

```javascript
describe('Protractor Demo App', function() {
  it('should add one and two', function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
    element(by.model('first')).sendKeys(3);
    element(by.model('second')).sendKeys(2);
    
    // ここでブレークポイントが貼られる。
    browser.debugger();

    element(by.id('gobutton')).click();

    expect(element(by.binding('latest')).getText()).
        toEqual('5'); // This is wrong!
  });
});
```


### debug中のコマンド

#### Stepping#

 + cont, c - Continue execution
 + next, n - Step next
 + step, s - Step in
 + out, o - Step out
 + pause - Pause running code (like pause button in Developer Tools)

#### `c`,`cont`を入力する事で、次のbreakpontまで移動する。

#### `n`,`next`を入力する事で、次のbreakpontまで移動する。

#### `repl`でscriptの実行を行う

この状態では、`Ctrl+C`で抜ける事が出来る。

#### `quit`でデバッグを強制終了させる事が出来る。


## デバッグ操作チュートリアル

最初にprotractorをdebugモードで実行します。

```sh
protractor debug  conf.js
```

デバックモードで実行されると、以下のようにprotractorにアタッチされた時点でブレークポイントが貼られます。

```sh
Using the selenium server at http://localhost:4444/wd/hub
[launcher] Running 1 instances of WebDriver
Starting debugger agent.
Debugger listening on port 5858
< Debugger listening on port 5858
connecting to port 5858... ok
break in timers.js:94
 92 }
 93 
>94 function listOnTimeout() {
 95   var msecs = this.msecs;
 96   var list = this;
< module.js:338
<     throw err;
<           ^
< Error: Cannot find module '/Users/msakamaki/project/FrontDevelopment/protTest/localhost:5858'
<     at Function.Module._resolveFilename (module.js:336:15)
<     at Function.Module._load (module.js:278:25)
<     at Module.runMain [as _onTimeout] (module.js:501:10)
<     at Timer.listOnTimeout (timers.js:133:15)
< Failed to open socket on port 5858, waiting 1000 ms before retrying
debug> 
```

ここはまだ、テスト中のブレークポイントでは無い為、`c`を入力して次のブレークポイントに進んでみましょう。

```sh
debug> c
break in /Users/msakamaki/.nodebrew/node/v0.11.14/lib/node_modules/protractor/lib/protractor.js:602
 600   // jshint debug: true
 601   this.driver.executeScript(clientSideScripts.installInBrowser);
>602   webdriver.promise.controlFlow().execute(function() { debugger; },
 603                                           'add breakpoint to control flow');
 604 };
debug> 
```

そうすると、上記のように`webdriver.promise.controlFlow().execute(function() { debugger`という箇所で処理がとまり、
画面でも、想定した位置で処理がとまっていると思います。

続いて、`repl`と言うコマンドを実行する事で、テストコードの参照・編集が可能になります。

左側の数値に、1を追加してみましょう

```sh
debug> repl
Press Ctrl + C to leave debug repl
>element(by.model('first')).sendKeys(1)
```

`repl`モードの状態で、コマンドを入力したら`Ctrl+C`で`repl`モードを抜けます。

その後、`c`を入力しテストを続行させると

元々`3`の入力されていたエリアに`1`が追加され`31`となってしまい、テストが失敗しました。

このように`repl`を使用する事で、テストの最中に新しく操作を加える事が可能になります。


---

##`node-inspector`を使ったGUIデバッギング

ここまでは、コマンドラインを使ったデバッグを試してきました。

しかし、コマンドラインでのデバッグは癖が強く、操作に慣れない方も多いでしょう。

その操作を多少行いやすくする為のツールに node-inspectorを使用したデバッグがあります。

ブレークポイントの設定にはテスト実行時には`browser.debugger()`を使用します。

(`debugger`句を使用してしまうと、テストコードの評価前に止まってしまいます。)

### 実行コマンド

それぞれ別のウィンドウで実行します。

```sh
# Webdriverサーバーを立ち上げる
webdriver-manager start
# node-inspectorを立ち上げる
node-inspector
# プロトラクタをデバッグモードで立ち上げる(mac/linux)
node --debug-brk `which protractor` conf.js 
# プロトラクタをデバッグモードで立ち上げる(wind)
node --debug-brk [protractor.exeのパス] conf.js 
```

上のコマンドを実行し終わったらnode-inspectorのURL [http://127.0.0.1:8080/debug?port=5858](http://127.0.0.1:8080/debug?port=5858)にアクセスしてみてください。

デバッグ実行がGUIで可能になります。

### spec.js 

```javascript
describe('Protractor Demo App', function() {
  it('should add one and two', function() {
    browser.get('http://juliemr.github.io/protractor-demo/');
    element(by.model('first')).sendKeys(3);
    element(by.model('second')).sendKeys(2);
    
    browser.debugger()

    element(by.id('gobutton')).click();

    expect(element(by.binding('latest')).getText()).
        toEqual('5');
  });
});
```






