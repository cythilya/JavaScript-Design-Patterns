#JavaScript Design Pattern - Strategy 策略模式
JavaScript Design Pattern 「Strategy 策略模式」 筆記。 策略模式將各個方法封裝起來，在執行期間再選擇適當的演算法。  

##範例
假設我們想驗證一些資料，其中想驗證各欄位

- 是否為空(isNonEmpty)
- 是否只填數字(isNumber)
- 是否只填英數(isAlphaNum)
- 是否為Email(isEmail)
- 字串大小是否在最小範圍內，設定為3個characters以上(minSize)
- 字串大小是否在最大範圍內，設定為10個characters以內(maxSize)

###程式碼解說

	var validator = {
		types: {}, //所有的驗證規則皆會存放於此，稍後會個別定義
		messages: [], //個別驗證類型的錯誤訊息
		config: {}, //使用者資料各欄位需要被驗證的類型
		validate: function (data) { // 使用者資料 - 欄位:值
			var i,
				msg,
				type,
				checker,
				result_ok;
			this.messages = []; //清空所有的錯誤信息
	
			for(i in data) {
				if(data.hasOwnProperty(i)) { //判斷使用者欄位是否需要被驗證
					type = this.config[i]; //如果需要被驗證，則取出相對應的驗證規則
					checker = this.types[type];
					if(!type) {
						continue; //如果驗證規則不存在，則不處理
					}
					if(!checker) { //如果驗證規則類不存在，拋出異常
						throw {
							name: "ValidationError",
							message: "No handler to validate type " + type
						};
					}
					result_ok = checker.validate(data[i]); //驗證
					if(!result_ok) { //取得錯誤訊息
						msg = "Invalid value for *" + i + "*, " + checker.instructions;
						this.messages.push(msg);
					}
				}
			}
			return this.hasErrors();
		},
		//helper
		hasErrors:function () {
			return this.messages.length !== 0;
		}
	};
	
	//checks for non-empty values
	validator.types.isNonEmpty = {
		validate:function (value) {
			return value !== "";
		},
		instructions: "the value cannot be empty"
	};
	
	//checks if a value is a number
	validator.types.isNumber = {
		validate:function (value) {
			return !isNaN(value);
		},
		instructions: "the value can only be a valid number, e.g. 1, 3.14 or 2010"
	};
	
	//checks if the value contains only letters and numbers
	validator.types.isAlphaNum = {
		validate:function (value) {
			return !/[^a-z0-9]/i.test(value);
		},
		instructions: "the value can only contain characters and numbers, no special symbols"
	};
	
	validator.types.isEmail = {
		validate: function(value){
			var re = /^([\w-]+(?:\.[\w-]+)*)@((?:[\w-]+\.)*\w[\w-]{0,66})\.([a-z]{2,6}(?:\.[a-z]{2})?)$/i;
	    	return re.test(value);		
		},
		instructions: 'use valid email format, e.g. @'
	};
	
	validator.types.minSize = {
		validate: function(value){
	    	return value.length >= 3;		
		},
		instructions: 'min size is 3 characters'
	};
	
	validator.types.maxSize = {
		validate: function(value){
			return value.length <= 10;
		},
		instructions: 'max size is 10 characters'
	};
	
	//test
	var data = {
		first_name: 'Super',
		last_name: 'Man',
		age: 'unknown',
		username: 'o_O',
		email: 'example@gmail.com',
		confirm_email: 'invalid_email_sample',
		password: '12'
	};
	
	validator.config = {
		first_name: 'isNonEmpty',
		last_name: 'maxSize',
		age: 'isNumber',
		username: 'isAlphaNum',
		email: 'isEmail',
		confirm_email: 'isEmail',
		password: 'minSize'	
	};
	
	validator.validate(data);
	if (validator.hasErrors()) {
		console.log(validator.messages.join("\n"));
	}

[看程式碼](strategy.html)。

###Demo
console視窗顯示訊息如下：  

	Invalid value for *age*, the value can only be a valid number, e.g. 1, 3.14 or 2010
	Invalid value for *username*, the value can only contain characters and numbers, no special symbols
	Invalid value for *confirm_email*, use valid email format, e.g. @
	Invalid value for *password*, min size is 3 characters

---
####推薦閱讀
- [深入理解JavaScript系列（33）：設計模式之策略模式- 湯姆大叔- 博客園](http://www.cnblogs.com/TomXu/archive/2012/03/05/2358552.html)