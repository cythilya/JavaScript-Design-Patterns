#Iterator 迭代器模式
Iterator (迭代器模式) 是一種提供簡單操作介面，而不用暴露資料來源的方法或更動它。也就是說，我們提供一個界面，讓其他人利用這個介面對資料元素做操作。

##簡單實作範例
我們將資料儲存在一個private變數 `data` 裡面，並用另外一個private變數 `index` 指向下一個元素。關於資料的操作，我們使用next()、hasNext()、rewind()和current()做存取，而不會直接觸碰到資料本身。

	var agg = (function(){
		var index = 0,
			data = [1, 2, 3, 4, 5],
			length = data.length;
	
		return {
			next: function(){
				var element;
				if(!this.hasNext()){
					return null;
				}
				element = data[index];
				index = index + 1;
				return element;
			},
			hasNext: function(){
				return index < length;
			},
			rewind: function(){
				//重新設定索引指向資料的開頭
				index = 0;
			},
			current: function(){
				//用來取得當前的元素
				return data[index];
			}
		}
	}());
	
	while(agg.hasNext()){
		console.log(agg.next()); //1, 2, 3, 4, 5
	};
	
	agg.rewind();
	console.log(agg.current()); //1

[看原始碼](iterator.html)。  

##實例 - jQuery的應用
jQuery中有一個非常有名的迭代實作 - $.each方法。透過$.each我們可以傳入資料和額外的function，來對這個資料中的元素進行操作。如下範例 (參考 [深入理解JavaScript系列（35）：設計模式之迭代器模式- 湯姆大叔- 博客園](http://www.cnblogs.com/tomxu/archive/2012/03/09/2358903.html))：

	$.each(['Apple', 'Bob', 'Cat', 'Dog'], function (index, value) {
	    console.log(index + ': ' + value);
	});
	
或是
	
	$('li').each(function (index) {
	    console.log(index + ': ' + $(this).text());
	});


##總結
使用Iterator (迭代器模式)的時機在於  

- 資料集合內部結構可能經常變動
- 不想公開資料集合的內部結構
- 必須讓外面的程式碼操作資料集合內的元素

我們就可以使用這樣的方法。

---
####推薦閱讀 / 參考文件
- [深入理解JavaScript系列 - 湯姆大叔- 博客園](http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)：深入理解JavaScript系列文章。
- [深入理解JavaScript系列（35）：設計模式之迭代器模式- 湯姆大叔- 博客園](http://www.cnblogs.com/tomxu/archive/2012/03/09/2358903.html)：迭代器模式的解說與範例。
- [JavaScript Design Pattern - Iterator 迭代器模式](http://cythilya.blogspot.tw/2015/06/javascript-design-pattern-iterator.html)：網誌版。