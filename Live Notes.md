[TOC]

# 函数柯里化（curry）

柯里化函数：用于固定某个函数的某些参数，得到另一个函数，该函数接收剩余参数

## 柯里化通用式

```js
function curry(func) {
    // 获取剩余擦书
    var args = Array.from(arguments).slice(1); // 转化为真数组并截取要固定的函数
    
    // 返回柯里化的函数，由于返回函数中使用了curry函数中的变量，因此使用了闭包的特性
    return function () {
        var subArgs = Array.from(arguments); // 获取剩余参数
        var newArgs = args.concat(subArgs);	 // 总参数=固定参数+剩余参数
        if (newArgs.length >= func.length) { // func.length为函数的形参个数
            // 总参数数量已经足够，将总参数作为实参列表传入func进行执行，得到函数执行结果
            return func.apply(null, newArgs);
        }else {
            // 总参数数量不够，将不再返回函数执行结果，而是按照总参数进行柯里化
            newArgs.unshift(func);
            return curry.apply(null, newArgs);
        }
    }
}
```

简化写法：

```js
function curry(func) {
    var args = Array.from(arguments).slice(1);
    return function () {
        var newArgs = Array.from(arguments).concat(args);
        if (newArgs.length >= func.length) {
            return func.apply(null, newArgs);
        }else {
            newArgs.unshift(func);
            return curry.apply(null, newArgs);
        }
    }
}
```

使用ES6写法：

```js
function curry(func, ...fixedArgs) {
    return function (...restArgs){
        var totalArgs = fixedArgs.concat(restArgs);
        if (totalArgs.length >= func.length) {
            return func(...totalArgs);
        }else {
            return curry(func, ...totalArgs);
        }
    }
}
```



### 柯里化函数的使用

#### 1. 固定参数

```js
function sum(a, b, c) {
    return a + b + c;
}

// 使用柯里化函数固定第一个参数为2
var sumCurried = curry(sum, 2);

// 给柯里化后的sum函数sumCurried传入剩余的参数
// 如果传入完整的剩余参数，则返回得到sum函数的执行结果
var res = sumCurried(3, 4);
console.log(res);	// 9

// 如果传入的剩余参数不完整，表明不需要得到函数执行结果，而是继续固定参数并返回得到进一步柯里化的函数
var sumCurriedCurried = sumCurried(3);
console.log(sumCurriedCurried);		// function () {/*...*/}
console.log(sumCurriedCurried(4));	// 9
```

#### 2. 函数的连续调用

```js
function sum(a, b, c) {
    return a + b + c;
}

// 将函数sum进行柯里化
var sumCurried = curry(sum);

// 实现函数的连续调用
sumCurried(1)(2)(3);	// 6
```

