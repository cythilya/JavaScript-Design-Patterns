#Decorator 裝飾者模式

> A Decorator is an object which adds functionality to another object dynamically. It can be used to enhance the behavior of an object without requiring the author to reopen its class. While Decorators might feel a little weird to implement in static languages they're extremely simple in JavaScript due to the ease with which JS passes around functions and handles dynamic types. - [JavaScript Design Patterns: Decorator](http://robdodson.me/javascript-design-patterns-decorator)

<!-- more -->

## 為什麼會有Decorator？

假設我們要買一台12吋的MacBook，原始價格是$41900，會隨著加上不同的加購條件而提升價格，例如增加記憶體需多增$10000，增加刻字服務多增$1000，增加保固需多增$8590，如果三項都要(增加記憶體、刻字、保固)則會增加$19590。在這個例子中，不論是合併特性或新增一個特性(例如之後想要新增「購買轉接線」)，就得多寫一個function，於是管理和維護變得非常困難，而Decorator就是為了擴充性應運而生。

    var MacBook = function(){
        this.cost = function(){return 41900};
        this.screenSize = function(){return 12}
    };

    var mb = new MacBook();

    //增加記憶體需多增$10000
    var MacBookWithLargeMemory = function(){
        MacBook.call(this);
        this.cost = this.cost() + 10000;
    };

    //增加刻字服務多增$1000
    var MacBookWithEngraving = function(){
        MacBook.call(this);
        this.cost = this.cost() + 1000;
    };

    //增加保固需多增$8590
    var MacBookWithInsurance = function(){
        MacBook.call(this);
        this.cost = this.cost() + 8590;
    };

    //增加記憶體、刻字、保固 - 雖然只是將前面的條件合併，卻需要多寫一個function
    var MacBookWithLargeEngravingInsurance = function(){
        MacBook.call(this);
        this.cost = this.cost() + 10000 + 1000 + 8590;
    };

    MacBookWithLargeMemory.prototype = Object.create(MacBook.prototype);

    //增加記憶體需多增$10000
    var mbWithLargeMemory = new MacBookWithLargeMemory();
    console.log(mbWithLargeMemory); //51900

    //增加刻字服務多增$1000
    var mbWithEngraving = new MacBookWithEngraving();
    console.log(mbWithEngraving); //42900

    //增加保固需多增$8590
    var mbWithInsurance = new MacBookWithInsurance();
    console.log(mbWithInsurance); //50490

    //增加記憶體、刻字、保固
    var mbWithLargeEngravingInsurance = new MacBookWithLargeEngravingInsurance();
    console.log(mbWithLargeEngravingInsurance); //61490

## Decorator怎麼用？

Decorator接受function作為其參數，並使用匿名函數(anonymous function)包裝起來做執行，然後將執行結果回傳回去。

### 簡單範例

    function decorator(func) {
        return function() {
            return func.apply(this, arguments);
        }
    }

    function sum(a, b) {
        return a + b;
    }

    var getSum = decorator(sum);

    console.log( getSum(1,2) ); //3
    console.log( getSum(2,3) ); //5

### 改寫上述購買MacBook的例子

    function MacBook(){
        this.cost = function(){return 41900};
        this.screenSize = function(){return 12}
    }

    //Decorator 1 - 增加記憶體需多增$10000
    function Memory(mackbook){
        var v = mackbook.cost();
        mackbook.cost = function(){
            return v + 10000;
        }
    }

    //Decorator 2 - 增加刻字服務多增$1000
    function Engraving(mackbook){
        var v = mackbook.cost();
        mackbook.cost = function(){
            return v + 1000;
        }
    }

    //Decorator 3 - 增加保固需多增$8590
    function Insurance(mackbook){
        var v = mackbook.cost();
        mackbook.cost = function(){
            return v + 8590;
        }
    }

    var mb = new MacBook();

    //增加記憶體、刻字、保固
    Memory(mb);
    Engraving(mb);
    Insurance(mb);
    console.log(mb.cost()); //61490
    console.log(mb.screenSize()); //12

Decorator的優點就如同上例所示 - 可重覆利用(reuse)、彈性(flexibility)和擴充性(expandability)。但其缺點為若管理不慎可能造成結構複雜的問題。

* * *

#### 推薦閱讀

*   [Decorators](http://javascript.info/tutorial/decorators)
*   [Learning JavaScript Design Patterns](http://it-ebooks.info/book/724)
*   [JavaScript Design Patterns: Decorator](http://robdodson.me/javascript-design-patterns-decorator)
*   [深入理解JavaScript系列（29）：設計模式之裝飾者模式- 湯姆大叔- 博客園](http://www.cnblogs.com/TomXu/archive/2012/02/24/2353434.html)