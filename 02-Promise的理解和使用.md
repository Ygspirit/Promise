# 一、Promise是什么

## 1. 理解

### 1、抽象表达

- ###### Promise是JS中进行异步编程的新的解决方案

### 2、具体表达

- ###### 语法：Promise是一个构造函数

- ###### 功能：Promise对象用来封装一个异步操作并可以获取其结果

## 2. Promise的状态和状态改变

### 1、Promise的三种状态

- ###### peding（初始化/等待）、resolved/fulfilled（成功）、rejected（失败）

### 2、Promise的两种状态改变

- ###### peding变为resolved

- ###### peding变为rejected

- 注意：只有着两种，且一个Promise对象只能改变一次；无论成功或失败，都会有一个数据结果；成功的数据结果称为Value，失败称为Reason。

### 3、Promise的基本流程

- 成功

new promise()【peding状态】=>  执行异步任务  =>  成功了，执行resolve()  =>  promise对象【resolved状态】=>  使用.then，回调onResolved()  =>  新的promise对象

- 失败

new promise()【peding状态】=>  执行异步任务  =>  失败了，执行reject()  =>  promise对象【rejected状态】=>  使用.then/.catch，回调onRejected()  =>  新的promise对象

### 4、Promise的基本使用

```js
     const p = new Promise((resolve, reject) => {
        //执行器函数（同步回调）
        setTimeout(() => {
          const time = Date.now();
          if (time % 2 == 0) {
            resolve("成功的数据，time" + time);
          } else {
            reject("失败的数据，time" + time);
          }
        });
      });
      p.then(
        (value) => {
          //接收成功的数据，onResolved()
          console.log("成功的回调", value);
        },
        (reason) => {
          //接收失败的数据，onRejected()
          console.log("失败的回调", reason);
        }
      );
```

# 二、为什么要使用Promise

## 1. 指定回调函数的方式更加灵活

### 1、旧的指定方式

- 必须在启动异步任务之前指定

### 2、promise的指定方式

- 启动异步任务=>返回promise对象=>给promise对象绑定回调函数（甚至可以在异步任务结束后指定）

## 2. 支持链式调用，解决回调地狱

### 1、什么是回调地狱

- 回调函数的嵌套调用，外部回调函数异步执行的结果是嵌套的回调函数执行的条件（必须上一个成功，下一个才可以执行）

### 2、回调地狱的缺点

- 不便于阅读，不便于异常处理

### 3、解决方案

- promise链式调用

### 4、终极方案

- async/await

# 三、如何使用Promise

## 1. Promise的API

### 1、Promise构造函数：Promise(excutor){}

- excutor函数：同步执行 (resolve,reject)=>{}
- resolve函数：内部定义成功时调用的函数 (value)=>{}
- reject函数：内部定义失败时调用的函 (reject)=>{}
- 说明：excutor会在Promise内部立即同步回调，异步操作在执行器中执行

### 2、Promise.prototype.then方法：(onResolved,onRejected)=>{}

- onResolved函数：成功的回调函数  (value)=>{}
- onRejected函数：失败的回调函数   (reason)=>{}
- 说明：指定用于得到成功value的成功回调和用于得到失败reason的失败回调
- 说明：返回一个新的promise对象

### 3、Promise.prototype.catch方法：(onRejected)=>{}

- onRejected函数：失败的回调函数   (reason)=>{}
- 说明：then()的语法糖，相当于：then(undefined,onRejected)

### 4、Promise.resolve方法：(value)=>{}

- value：成功的数据或者Promise对象
- 说明：返回一个成功/失败的Promise对象

### 5、Promise.reject方法：(reason)=>{}

- reason：失败的原因
- 说明：返回一个失败的Promise对象

### 6、Promise.all方法：(promises)=>{}

- promises：包含n个promise的数组
- 说明：返回一个新的promise，只要所有的promise都成功才成功，只有有一个失败了就直接失败

### 7、Promise.race方法：(promises)=>{}

- promises：包含n个promise的数组
- 说明：返回一个新的Promise，第一个完成的Promise的结果状态就是最终的结果状态

## 2. PromiseAPI的使用

### 1、Promise构造函数、then方法、catch方法的使用

```js
new Promise((resolve, reject) => {
   setTimeout(() => {
      resolve("成功的数据");
      //   reject("失败的调用");
    }, 1000);
})
   .then((value) => {
      console.log("onResolve", value);
    })
    .catch((reason) => {
      console.log("onReject", reason);
    });
```

### 2、语法糖：resolve方法和reject方法的使用

```js
 //需求：产生一个成功值为1和失败值为2的Promise对象
   //正常写法
   const p1 = new Promise((resolve, reject) => {resolve(1)});
   //语法糖的写法
   const p2 = Promise.resolve(1);
   const p3 = Promise.reject(2);
   
   p1.then((value) => {console.log(value)});
   p2.then((value) => {console.log(value)});
   p3.catch((reason) => {console.log(reason)});
```

### 3、Promise.all方法的使用

```js
const pAll = Promise.all([p1,p2,p3])
pAll.then(
  values=>{console.log("all onResolved()",values)},
  reason=>{console.log("all onReason()",reason)})
```

- 说明：成功了回调all onResolved()函数，values是一个数组
- 说明：失败了回调all onReason()函数，reason是失败的那个Promise的数据

### 4、Promise.race方法的使用

```js
const pRace = Promise.race([p1, p2, p3]);
pRace.then(
  (value) => {console.log("race onResolved()", value)},
  (reason) => {console.log("race onReason()", reason)});
```

- 说明：哪个promise函数先执行完，就接收谁的数据

# 四、Promise的几个关键问题

## 1. 如何改变Promise的状态？

- resolve(value)：如果当前是pendding就会变为resolved
- reject(reason)：如果当前是pendding就会变为rejected
  - 抛出异常：如果当前是pendding就会变为rejected（reason为抛出的error）m,.

```js
const p1 = new Promise((resolve, reject) => {
   resolve(1);  //promise变为resolved成功状态
   reject(2);  //promise变为rejected失败状态
   throw new Error("出错了");  //抛出异常，promise变为rejected失败状态，reason为抛出的error
   throw 3;  //抛出异常，promise变为rejected失败状态，reason为抛出的3
});
```

## 2、一个promise指定多个失败/成功的回调函数，都会执行吗？

- 当promise改变为对应状态时都会调用

## 3、改变Promise状态和指定回调函数谁先谁后？

-  都有可能，正常情况下是先指定回调后改变状态

```js
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        resolve(1);  //后改变状态（同时指定数据），异步执行回调函数
    }, 1000);
}).then(  //先指定回调函数，保存当前指定的回调函数
    value => {console.log(value)},
    reason => {console.log(reason)}
);
```

- 如何先改状态后指定回调

  - 在执行器中直接调用resolve()或者reject()

  -  延长更长的时间调用then()

```js
const p1 = new Promise((resolve, reject) => {
    resolve(1);  //先改变状态（同时指定数据）
}).then(  //后指定回调函数，异步执行回调函数
    value => {console.log(value)},
    reason => {console.log(reason)}
);
```

- 什么时候才能得到数据？

  - 如果先指定回调，那当状态发生改变时，回调函数就会调用，得到数据

  - 如果先改变状态，那当指定回调时，回调函数就会调用，得到数据

## 4、Promise.then()返回的新promise的结果状态由什么决定？

- 由then()指定的回调函数执行的结果决定

- 说明：

  - 如果抛出异常，新promise变为rejected，reason为抛出的异常

  - 如果返回的是非Promise的任意值，新promise变为resolved，value为返回的值

  - 如果返回的是另一个新promise，此promise的结果就会成为新的promise的结果

```js
   new Promise((resolve, reject) => {
     resolve(1);
     //   reject(2);
   }).then(
      (value) => {
       console.log("onResolved1()", value);
       // return 2;
       // return Promise.resolve(3);
       // return Promise.reject(4);
       throw 5;
      },
      (reason) => {
          console.log("onRejected1()", reason);
          // return 2;
          // return Promise.resolve(3);
          // return Promise.reject(4);
          throw 5;
     }
     ).then(
      (value) => {console.log("onResolved1()", value);},
      (reason) => {console.log("onRejected1()", reason);}
     );
```

## 5、Promise如何串联多个任务？

- promise的then()会返回一个新的promise，可以开成then()的链式调用
- 通过then()的链式调用串联多个同步或异步任务  

```js
   new Promise((resolve, reject) => {
      setTimeout(() => {
        console.log("执行异步任务1");
        resolve(1);
     }, 1000);
   })
   .then((value) => {
      console.log("任务1的结果", value);
      console.log("执行同步任务2");
      return 2;
    })
    .then((value) => {
      console.log("任务2的结果", value);
      console.log("执行异步任务3");
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve(3);
        }, 1000);
      });
     })
     .then((value) => { 
       console.log("任务3的结果", value);
     });
```

## 6、promise异常穿/传透？

- 当使用promise的then链式调用时，可以在最后指定失败的回调
- 前面的任何操作出现了异常，都会传到最后失败的回调中处理

```js
      new Promise((resolve, reject) => {
        // resolve(1);
        reject(1);
      })
        .then(
          (value) => {
            console.log("onResolved1()", value);
            return 2;
          },
          (reason) => {throw reason;}  //一般被省略不写，但是默认存在，
        )
        .then((value) => {
          console.log("onResolved2()", value);
          return 3;
        }，
          (reason) => {throw reason;}   //一般被省略不写，但是默认存在，
        )
        .then((value) => {
          console.log("onResolved3()", value);
        }，
          (reason) => {throw reason;}   //一般被省略不写，但是默认存在，
        )
        .catch((reason) => {
          console.log("onRejected()", reason);
        });
```

## 7、中断Promise链？

- 当使用promise的then链式调用时，在中间中短，不再调用后面的回调函数
- 办法：在回调函数中返回一个pedding状态的promise对象

```js
      new Promise((resolve, reject) => {
        // resolve(1);
        reject(1);
      })
        .then(
          (value) => {
            console.log("onResolved1()", value);
            return 2;
          },
          (reason) => {
            throw reason;
          }
        )
        .then((value) => {
          console.log("onResolved2()", value);
          return 3;
        })
        .then((value) => {
          console.log("onResolved3()", value);
        })
        .catch((reason) => {
          console.log("onRejected()", reason);
          // throw reason
          // return new Promis.reject(reason)
          return new Promise(() => {}); //返回一个pedding的promise
        })
        .then((value) => {
          console.log("onResolved()", value);
        },
        (reason)=>{   
          console.log("onRejected()", reason);
        })
```

# 五、自定义Promise

## 1、定义整体结构   

```js
/*
自定义Promise函数模块:IIFE
*/
(function (window) {
    /*
    Promise构造函数
    excutor:执行器函数(同步执行)
    */
    function Promise(excutor) {
    }
 
    /*
    Promise原型对象的then()
    指定成功和失败的回调函数
    返回一个新的Promise对象
    */
    Promise.prototype.then = function (onResolved, onRejected) {
    }

    /*
    Promise原型对象的catch()
    指定失败的回调函数
    返回一个新的Promise对象
    */
    Promise.prototype.catch = function (onRejected) {
    }

    /*
    Promise函数对象的resolve方法
    返回一个指定结果的成功的Promisre
    */
    Promise.resolve = function (value) { }

    /*
    Promise函数对象的reject方法
    返回一个指定reason的失败的Promise
    */
    Promise.reject = function (reason) { }

    /*
    Promise函数对象的all方法
    返回一个promise，只有所有的promise都成功才成功，否则失败
    */
    Promise.all = function (promises) { }

    /*
    Promise函数对象的race方法
    返回一个promise，其结果由第一个完成的promise决定
    */
   Promise.race = function (promises) { }

    //向外暴露Promise函数
    window.Promise = Promise
})(window)
```

## 2、Promise构造函数的实现



## 3、Promise.then()/.catch()的实现

## 4、Promise.resolve()/.reject()的实现

## 5、Promise.all/.race的实现

## 6、Promise.resolveDelay()/.rejectDelay()的实现

## 7、ES5 function完整版本

## 8、ES6 class完整版本