# はじめに

[protractorチュートリアル](https://github.com/angular/protractor/blob/master/docs/tutorial.md)

[Protractorチュートリアル日本語訳](http://qiita.com/weed/items/30098f7be2f753580f63)


[非AngularサイトでのProtractorテスト](http://ng-learn.org/2014/02/Protractor_Testing_With_Angular_And_Non_Angular_Sites/)

```javascript
  it('non angular site', function(){
    browser.driver.get('http://www.yahoo.co.jp/');

    browser.driver.sleep(3000);
    browser.driver.findElement(by.name('p')).sendKeys('protractor');
    browser.driver.findElement(by.id('srchbtn')).click();
    browser.driver.sleep(5000);

    // duty huck for protractor option browser.ignoreSynchronization = true
    // see: http://ng-learn.org/2014/02/Protractor_Testing_With_Angular_And_Non_Angular_Sites/
    
  });
```
