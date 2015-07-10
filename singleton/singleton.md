#Singleton 單體模式
Singleton的概念就是同一個class只能建立唯一一個實體物件。當第二次使用同一個class建立新物件的時候，我們會得到和第一次建立時同一個物件。一般來說，當我們利用同一個class建立實體物件時，每次建立得到的物件都會是不同的，例如：    

	var obj = {
		myProp: 'myValue'
	};
	
	var obj2 = {
		myProp: 'myValue'
	};
	
	console.log(obj === obj2); //false
	console.log(obj == obj2); //false

而我們預期Singleton可以得到以下的效果...  

	function Universe(){
		//no-op
	};
	
	var uni = new Universe();
	var uni2 = new Universe();
	console.log(uni === uni2); //true

###實作方法
- 儲存在靜態屬性中的實體
- 儲存在Closure中的實體

####儲存在靜態屬性中的實體
將快取建立在建構式的靜態屬性中(Instance in a Static Property)，意即，建立類似 `Universe.instance` 的屬性，並將物件實體儲存於此。缺點是instance屬性可被public存取，很可能會被修改，而失去應有的正確性。  

	function Universe(){
		
		if(typeof Universe.instance === 'object'){
			return Universe.instance;
		}
	
		this.start_time = 0;
		this.bang = 'Big';
	
		Universe.instance = this;
	};
	
	var uni = new Universe();
	var uni2 = new Universe();
	console.log('uni === uni2');
	console.log(uni === uni2); //true

我們先檢查這個物件是否有被建立過，由於這個物件是存在屬性 `Universe.instance` 中，因此檢查這個屬性是否有值且存入一個物件。如果有，那就回傳已經建立過的物件；如果沒有，則建立新物件，並存在屬性 `Universe.instance`裡面，然後回傳這個新物件。由於函式會預設回傳目前的物件，因此我們不見得要寫return這個指令。在上面這個範例中，我們看到uni和uni2是相等的。  

但這樣的實作方式會有個缺點，由於instance屬性是公開的，大家都可以修改，如果有其他程式碼修改了，例如下面的範例，修改值為null，那麼uni3就會收到一個新物件，而讓uni不等於uni3了。  
	
	Universe.instance = null; //公開屬性，被修改了...
	var uni3 = new Universe();
	console.log(uni === uni3); //false

####儲存在Closure中的實體
為了解決上面不被公開修改的缺點，我們改將這份實體包在一個closure裡面(Instance in a Closure)，保持其為private的狀態，無法在建構式之外被修改，就能保證為唯一。  

	function UniverseC(){
		var instance = this;
	
		this.start_time = 0;
		this.bang = 'Big';
	
		//重新定義建構式
		UniverseC = function(){
			return instance;
		};
	};
	
	var uni4 = new UniverseC();
	var uni5 = new UniverseC();
	console.log('uni4 === uni5');
	console.log(uni4 === uni5); //true


原本的建構是在第一次才會呼叫做初始化的動作，即產生一份實體物件，而之後呼叫就只是回傳這份實體物件而已。這個觀念即是「Self-Defining-Function」(自我定義函式)，可參考[程式碼](https://github.com/cythilya/Code_Snippets/blob/master/functional_snippet/self-defining-function.html)。 但缺點是，重新定義的函式會失去在重新定義後加上去的屬性，即我們失去了inEverything。  

	//測試是否遺失之後加上去的屬性
	UniverseC.prototype.inEverything = true;
	var uni6 = new UniverseC();
	
	console.log(uni4.inNothing); //true
	console.log(uni4.inEverything); //undefined
	console.log(uni6.inNothing); //true
	console.log(uni6.inEverything); //undefined

另外，我們也可以觀察到，一個有趣的現象... `uni4.constructor` 和 UniverseC居然是不一樣的。  

	console.log(uni4.constructor.name); //UniverseC
	console.log(uni4.constructor === UniverseC); //false
	
	console.log(uni4.constructor); //最初定義的UniverseC
	console.log(UniverseC); //重新定義的UniverseC

####修改儲存在Closure中的實體
Sol 1與Sol 2解決了「儲存在Closure中的實體」的問題：

- 重新定義的函式會失去在重新定義後加上去的屬性
- 建構式與自我重新定義後是不同的

#####Sol 1

	UniverseM.prototype.inNothing = true;
	function UniverseM(){
		var instance;
	
		UniverseM = function UniverseM(){
			return instance;
		};
	
		UniverseM.prototype = this;
		instance = new UniverseM();
		instance.constructor = UniverseM();
	
		instance.start_time = 0;
		instance.bang = 'Big';
	
		return instance;
	};
	
	var uni7 = new UniverseM();
	var uni8 = new UniverseM();
	
	console.log(uni7 === uni8); //true
	
	UniverseM.prototype.inEverything = true;
	console.log(uni7.constructor === UniverseM); //true

#####Sol 2
用立即函式包住建構式和實體，當第一次呼叫的時候，建立一個private物件並回傳；第二次之後的呼叫，只會回傳這份private物件。

	var UniverseN;
	(function(){
		var instance;
		UniverseN = function UniverseN(){
			if(instance){
				return instance;
			}
			instance = this;
	
			this.start_time = 0;
			this.bang = 'Big';
		};
	}());
	
	var uni9 = new UniverseN();
	var uni10 = new UniverseN();
	
	console.log(uni9 === uni10); //true
	
	UniverseN.prototype.inEverything = true;
	console.log(uni9.constructor === UniverseN); //true

[看程式碼](singleton.html)。  

###總結
總結各方法的實作方式和優缺點：  

- 儲存在靜態屬性中的實體
	- 實作方法：建立類似 `Universe.instance` 的屬性，並將物件實體儲存於此。
	- 優點：簡單，容易實作。
	- 缺點：instance屬性可被public存取，很可能會被修改，而失去應有的正確性。解法：儲存在Closure中的實體。
- 儲存在Closure中的實體
	- 實作方法：將物件實體包在一個closure裡面，保持其為private的狀態，使其無法在建構式之外被修改。
	- 優點：不會被建構式之外的程式碼修改，具有private特性。
	- 缺點：重新定義的函式會失去在重新定義後加上去的屬性，且建構式與自我重新定義後是不同的，這在修改後的Sol 1和Sol 2可以獲得解決。我自己比較喜歡Sol 2，簡短、簡單易懂。

---
####推薦閱讀 / 參考文件
- [從實例學Design Patterns](http://slides.com/jaceju/design-patterns-by-examples/#/)