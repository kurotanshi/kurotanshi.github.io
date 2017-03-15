---
layout: post
title: '淺談 ECMAScript 5 嚴格模式 (Strict Mode) '
date: 2011-11-28 02:43
comments: true
categories: [ECMAScript]
tags: [javascript, ecmascript]
---
自 ECMAScript 5 開始，增加了一個 嚴格模式 (Strict Mode) 的新特性。

ECMAScript 5 雖然可以跟前一版的 ECMAScript 3 相容 (ECMAScript 4 已廢棄)，但是，當我們宣告為 "Strict Mode" 後，那些 ECMAScript 5 不再建議使用的 ECMAScript 3 的舊語法會被全面禁止。
一旦出現，便會出現錯誤或拋出異常 (Exception)。

Strict Mode 的宣告方式有兩種：

若要在全域範圍內宣告使用 Strict Mode，只需在程式碼的第一行加上下面敘述，如：

``` javascript
"use strict";
```

也可以在指定的函數內宣告使用 Strict Mode，在函數的第一行加上下面敘述，如：

``` javascript
// Non-strict code...
function func_UseStrictMode() {
  "use strict";
   // ... your code ...
}
```

因為宣告使用 Strict Mode 時只需要加入一段 "use strict"; 敘述式，所以不會對些舊時的瀏覽器造成任何相容性的問題。 再來簡單介紹一下 Strict Mode 與非 Strict Mode 的差異：

**變數：**
JavaScript 使用變數的時候不一定要先宣告 (若直接使用未宣告的變數會自動變成全域變數，強烈不建議!) ，但在 Strict Mode 下，變數使用前必須要先用 var 宣告後才能拿來用，否則會出現錯誤。

``` javascript
"use strict";

try {
  i = 1;
}
catch (err) {
  alert(err);    // throw exception !
}
```

另外，在 Strict Mode 下刪除全域變數、函數，或是函數內的參數都會被認為是錯誤的語法。
如下：

``` javascript
"use strict";

var i = 1;
var myfunc = function () { };
delete i;         // Error !
delete myfunc;    // Error !

function myfunc2(arg) {
    delete arg;    // Error !
}
```

**屬性**：
在定義物件的屬性時，屬性名稱不可重複，同一個物件內不能重複定義相同屬性，否則會出現異常：

``` javascript
"use strict";
{
  foo: true,
  foo: false;     // Error
}
```

**函數與參數**：
在 Strict Mode 下，函數的參數 (arguments) 不能有相同名稱的變數，如下：

``` javascript
function func1(arg1, arg2) { }    // OK
function func2(arg, arg) { }      // Error
```

同時，函數的 arguments 屬性在 Strict Mode 下也是唯讀的：

``` javascript
"use strict";

function func(arg) {
  arguments = ["..."];    // not allow, Error.
}
```

另外，對 arguments.caller 和 arguments.callee 的存取會出現錯誤。

``` javascript
"use strict";

function test(){
	function inner(){
  	// Don't exist, either
    test.arguments = ...;		// Error
    inner.caller = ...;			// Error
  }
}
```

因此，任何需要用到的匿名函數都必須先命名，例如：

``` javascript
setTimeout(function later(){
	// do stuff...
	setTimeout( later, 1000 );
}, 1000 );
```

最後，當使用 null 或者 undefined 作為 Function.prototype.call 或 Function.prototype.apply 方法的第一個參數時，函數內部的 this 將會指向 global object。

而 Strict mode 將會阻止其執行並拋出異常：

``` javascript
(function(){ ... }).call( null );		// Exception
```

**eval**：
eval 是保留的關鍵字，不能作為變數名、函數名、物件的屬性，甚至是變數都不行。
所以，以下的程式碼**全都是錯誤**的：

``` javascript
// All generate errors...
obj.eval = ...
obj.foo = eval;
var eval = ...;

for ( var eval in ... ) {}
function eval(){}
function test(eval){}
function(eval){}
new Function("eval")
```

另外，在 Strict mode 透過 eval 引入的變數也會無效，如下：

``` javascript
eval("var a = false;");
print( typeof a );			// undefined.
```

**with() { }**：
在 Strict mode 下沒有這個東西了，如果使用的話會被認為語法錯誤。

**其他**：
在 Strict mode 下，不再支援 8 進位數字。
實際在 Firefox 8 測試的結果，會出現 "octal literals and octal escape sequences are deprecated" 的錯誤：在非 Strict mode 下，010 則是會 alert 出 8 這個數字。

