#Factory 工廠模式
Factory的目的是建立物件，通常實作為一個class，或class內的靜態方法。作用是：

- 對於建立相似的物件以進行重複性的工作，達到程式碼精簡的目的
- 提供一個無需事先知道(即編譯時期)明確型別的建立物件的方式

##實作方法
由Factory的class或method所建立的物件，會被設計成繼承同一個父物件，這些具體的subclass物件則各自實作專門的功能。有時這個公共的父物件也是容納factor方法的class。  

	//公共的父建構式
	function CarMaker(){}; 
	
	CarMaker.prototype.drive = function(){
		return 'Vroom, I have ' + this.doors + ' doors';
	};
	
	//CarMaker的靜態方法，用來建立car物件
	CarMaker.factory = function(type){
		var constr = type,
			newcar;
	
		if(typeof CarMaker[constr] !== 'function'){
			throw {
				name: 'Error',
				message: constr + ' does not exist'
			};
		}
	
		if(typeof CarMaker[constr].prototype.drive !== 'function'){
			CarMaker[constr].prototype = new CarMaker();
		}
	
		newcar = new CarMaker[constr]();
		return newcar;
	};
	
	//繼承自 CarMaker ，且被定義為 CarMaker 的靜態屬性
	CarMaker.Compact = function(){
		this.doors = 4;
	};
	
	CarMaker.Convertible = function(){
		this.doors = 2;
	};
	
	CarMaker.SUV = function(){
		this.doors = 24;
	};
	
	var corolla = CarMaker.factory('Compact');
	var soltice = CarMaker.factory('Convertible');
	var cherokee = CarMaker.factory('SUV');
	
	console.log(corolla.drive()); //Vroom, I have 4 doors
	console.log(soltice.drive()); //Vroom, I have 2 doors
	console.log(cherokee.drive()); //Vroom, I have 24 doors

[看程式碼](factory.html)。  

###說明
CarMaker是一個公共的父建構式，因此我們將可重複使用的方法放到CarMaker.factory中。factory()是CarMaker的靜態方法，根據不同的type輸入，來建立car物件。建構式 CarMaker.Compatc 、 CarMaker.SUV 、 CarMaker.Convertible ，他們都繼承自 CarMaker ，且被定義為 CarMaker 的靜態屬性。

##真實世界中的Factory - Object建構式
Object建構式的行為也是factory，它會根據輸入而產生不同的物件。  

	var o = new Object(),
		n = new Object(1),
		s = new Object('1'),
		b = new Object(true);
	
	console.log(o.constructor === Object); //true
	console.log(n.constructor === Number); //true
	console.log(s.constructor === String); //true
	console.log(b.constructor === Boolean); //true

---
####推薦閱讀 / 參考文件
- [用JS設計模式-開軍火工廠(Factory)](http://www.checkme.tw/wordpress/js-foctory)：實際範例 + 生活化解說，如果對特效有興趣的可以參考這一篇。[看Demo](https://dl.dropboxusercontent.com/u/64049607/fly_factory/index.html)。
- [JavaScript 模式之工廠模式(Factory)應用介紹](http://codex.wiki/post/180062-949)：內有範例。
- [JavaScript Design Pattern - Factory 工廠模式](http://cythilya.blogspot.tw/2015/06/javascript-design-pattern-factory.html)：網誌版。