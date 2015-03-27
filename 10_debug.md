
# protractor のデバッグ

protractorのデバッグ方法は大きく分けて２つあります。

`protractor.pause();`

を使う方法と

`protractor.debugger();`

を使う方法です。

##`browser.pause();`でのデバッギング

この機能は、画面を一時停止したいときに使います。

停止を開場させたい場合は`Ctrl+C`で止める事が出来ます。

用途としては、Protractorのテストで一時的に止めて、ブラウザ上の振る舞いを確認したいときに使います。

テストコードのデバックはできません。

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

    //ここで一時とまる。
    browser.pause();

    element(by.id('gobutton')).click();

    expect(element(by.binding('latest')).getText()).
        toEqual('5');
  });
});
```

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






