### Promise

**同步任务**：`只有前一个任务执行完毕，才能执行后一个任务`。还记得大学饭堂排队打饭例子吗，作为一个优秀的当代大学生，排队打饭遵循先来先打后来排队原则，只有前面那一个同学打完饭，你才能打。

**异步任务**：`由JavaScript 委托给宿主环境进行执行`。当异步任务执行完成后，会通知JavaScript 主线程执行异步任务的回调函数。还记得铁板烧吗，说实际的，铁板烧确实不错，细心的你有没有发现，老板里有多个锅，不可能只有一个锅，每一份铁板烧都需要时间，不然让顾客等待得花儿都谢了，你下次也不会来了，所以多个锅就代表多个任务，不需要等待一个锅烧完才去重新烧，也就是说不需要等待当前任务结束（**这个任务没有那么快完成，未来某个时间点才结束，它就是异步任务**），为节省时间或能耗，可以继续去执行其他任务。

**宏任务**：JavaScript自身发起。如setTimeout 、setInterval MessageChannel I/O、setImmediate（Node环境）、script（整体代码块）

**微任务**：是由宿主（浏览器、Node）发起的。MutationObserver（浏览器环境）、promise.[ then/catch/finally ]、事件队列 process.[nextTick](https://link.juejin.cn?target=https%3A%2F%2Fso.csdn.net%2Fso%2Fsearch%3Fq%3DnextTick%26spm%3D1001.2101.3001.7020)（Node环境

**Promise**：异步编程的一种解决方案，可以通俗把它当作一个容器，内部存储着某个未来才会结束的事件（通常是一个异步操作）的结果，从语法上来讲它就是一个对象，有两个特点：

* 有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
* 对象状态不受外界影响。
* 状态一旦改变就不会在变，也就是说任何时候Promise都只有一种状态。

#### 1. **Promise没有resolve前都是同步任务**。 Promise是微任务，但是Promise构造器内有同步任务，js线程会先把同步任务执行完，再去执行resolve回调。

```javascript
const promise = new Promise((resolve, reject) => {
    console.log(1);
    resolve(2)
    console.log(3);
});
promise.then((res) => {
    console.log(res);
});
console.log(4, promise);// 4 fulfilled
// 1 3 4 2

```

下面是输出的解释：

1. **`console.log(1);`** 执行，输出 `1`。
2. **`resolve(2)`** 执行，但这时`promise.then`中的回调函数不会立即执行。`resolve`仅标记了Promise状态的改变，并将结果2传递给任何`.then()`处理程序。在JavaScript中，Promises的回调（如`.then()`中的函数）是在当前执行堆栈清空后的事件循环中异步执行的。
3. **`console.log(3);`** 执行，输出 `3`。注意，虽然`resolve(2)`已经被调用，但由于JavaScript的事件循环和Promise的异步特性，`.then()`中的内容尚未执行。
4. **`console.log(4, promise);`** 执行，输出 `4` 和当前Promise的状态。这时，Promise已经被解决（fulfilled），所以它显示为 `Promise { <fulfilled>: 2 }`。
5. 最后，事件循环处理到promise的`.then()`回调，输出 `2`。

#### 2. **当函数返回的是一个Promise实例**。

```javascript
const fn = () => (new Promise((resolve, reject) => {
        console.log(1);
        resolve(2)
}))
fn().then(res => {
   console.log(res)
})
console.log(3)
// 1 3 2

```

1. 首先，立即执行函数表达式(fn)被调用。它会立即打印出1。
2. 紧接着，立即执行函数表达式返回一个Promise对象，并且通过resolve(2)将Promise标记为已完成状态。因此，Promise的then回调会在后续的事件循环中执行。
3. 下一行的console.log(3)会立即执行，打印出3。
4. 在事件循环的下一个循环中，promise的回调函数会被执行，由于之前的resolve(2)，所以res的值为2，打印出2。

#### 3. **宏任务与微任务执行顺序**。 定时器是宏任务，Promise是微任务，其他都是同步任务，

```javascript
console.log(1)
setTimeout(() => {
     console.log(2)
})
Promise.resolve().then(() => {
     console.log(3)
})
console.log(4)
// 1 4 3 2

```

1. 首先，console.log(1)会立即执行，打印出1。
2. 然后，setTimeout会被添加到事件队列中，但是不会立即执行，因为它是异步的。所以程序继续执行下一行代码。
3. Promise.resolve().then()会被添加到微任务队列中。由于微任务比宏任务优先级高，所以会立即执行，打印出3。
4. 然后，console.log(4)会立即执行，打印出4。
5. 由于之前的setTimeout，setTimeout的回调函数被添加到宏任务队列中，等待事件循环的下一个循环才会执行。而此时微任务队列已经为空了，那么程序就会去执行宏任务队列中的回调函数。因此，setTimeout的回调函数会被执行，打印出2。

#### 4. **当微任务中嵌套宏任务、宏任务嵌套微任务、宏任务嵌套宏任务、微任务嵌套微任务时**。

##### 4.1 **当微任务中嵌套宏任务时**：由于构造器中除了resolve执行回调之外，还有其他同步任务、宏任务。

```javascript
// 当微任务中嵌套宏任务
const promise = new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        console.log(2);
        resolve(3);
        console.log(4);
    }, 0);
    console.log(5);
});
promise.then((res) => {
    console.log(res);
});
console.log(6);
// 1 5 6 2 4 3

```

1. 首先，Promise被创建并且立即执行，打印出1和5。
2. 接着，setTimeout被设置并添加到事件队列中，但是它被设置为0秒后执行，所以不会立即执行，程序继续执行下一行代码。
3. promise.then()被添加到微任务队列中，但是微任务队列不会立即执行，程序继续执行下一行代码。
4. console.log(6)立即执行，打印出6。
5. 事件循环的第一轮完成，开始执行微任务队列中的任务，因此promise.then()回调函数被执行，打印出3。
6. 接着，setTimeout()的回调函数被添加到宏任务队列中，等待下一轮事件循环执行。这时候，setTimeout()的回调函数开始执行，打印出2和4。

##### 4.2 **当宏任务嵌套宏任务时**：当发生嵌套任务时，优先处理同层级，因为程序是自上而下的，setTimeout是异步任务中的宏任务，同层级的比嵌套的先入队，所以先执行同层级。

```javascript
// 宏任务嵌套宏任务
setTimeout(() => {
    console.log(1);
    setTimeout(() => {
       console.log(2)
    }, 0)
}, 0)
setTimeout(() => {
    console.log(3)
}, 0)
console.log(4)
// 4 1 3 2

```

1. 首先，console.log(4)会立即执行，打印出4。
2. 然后，两个setTimeout()被添加到事件队列中，并且它们都设置为0秒后执行。由于它们的定时器时间相同，所以它们就按照它们被定义的顺序来执行。因此，第一个setTimeout()中的回调函数会首先执行，打印出1。
3. 接着，第二个setTimeout()中的回调函数被添加到宏任务队列中，等待下一轮事件循环执行。
4. 在第一个setTimeout()的回调函数中，又有一个嵌套的setTimeout()被添加到事件队列中，并且它的定时器时间也是0秒。由于这个嵌套setTimeout()在第二个setTimeout()之前添加到事件队列中，所以它会先执行，打印出2。
5. 最后，第二个setTimeout()的回调函数被执行，打印出3。

##### 4.3 **当宏任务嵌套微任务时**：

```javascript
// 微任务链接微任务
Promise.resolve().then(() => {
        console.log(1)
        return 2;
}).then((res) => {
        console.log(res);
})
Promise.resolve().then(() => {
        console.log(3)
})
// 1 3 2

// 微任务嵌套微任务
Promise.resolve().then(() => {
        console.log(1)
        Promise.resolve().then(() => {
                console.log(2)
        })
        return 3
}).then((res) => {
        console.log(res);
})
Promise.resolve().then(() => {
        console.log(4)
})
// 1 4 2 3

```

微任务链接微任务：

1. 首先，Promise.resolve()被调用，并且立即执行，打印出1。
2. 接着，返回值2被传递给后面的then()方法，然后打印出2。
3. 然后，第二个Promise.resolve()被调用，并且立即执行，打印出3。
4. 由于没有再继续调用then()方法，所以没有其他输出。

微任务嵌套微任务：

1. 首先，Promise.resolve()被调用，并且立即执行。然后，第一个then()方法中的回调函数被添加到微任务队列中，但是微任务队列不会立即执行。
2. 接着，第二个Promise.resolve()被调用，并且立即执行。然后，第二个then()方法中的回调函数被添加到微任务队列中，但是微任务队列不会立即执行。
3. 然后，console.log(4)被执行，打印出4。
4. 事件循环的第一轮完成，开始执行微任务队列中的任务，因此第一个then()方法中的回调函数被执行，打印出1。
5. 在第一个then()方法的回调函数中，又有一个嵌套的Promise.resolve().then()被调用，并且立即执行。然后，嵌套的then()方法中的回调函数被添加到微任务队列中，但是微任务队列不会立即执行。
6. 接着，返回值3被传递给后面的then()方法，然后打印出3。
7. 事件循环的第二轮开始执行微任务队列中的任务，因此嵌套的then()方法中的回调函数被执行，打印出2。

#### **结合微任务和宏任务，灵活理解Promise三种状态**。

```javascript
const promise1 = new Promise((resolve, reject) => {
   setTimeout(() => {
           resolve("success");
           console.log(1);
   }, 1000);
   console.log(2);
});
const promise2 = promise1.then(() => {
       throw new Error("error!!!");
});
console.log(3, promise1);// pending
console.log(4, promise2);// pending
setTimeout(() => {
   console.log(5);
   console.log(6, promise1);// fufilled
   console.log(7, promise2);// rejected
}, 2000);
// 2 3 4 1 抛出error! 5 6 7

```

1. 首先，创建了promise1对象，并且立即执行。在promise1的构造函数中，setTimeout()被调用并设置为1秒后执行。同时，在setTimeout()之前，console.log(2)也会被执行，打印出2。
2. 然后，console.log(3, promise1)会被执行，打印出3和promise1的状态为pending。
3. 接着，promise1的定时器时间到达，resolve("success")被调用，promise1的状态变为fulfilled，并且console.log(1)会被执行，打印出1。
4. 然后，promise1的then()方法被调用，并且抛出一个错误"error!!!"。此时，promise2被创建，并且其状态为pending。
5. 接着，console.log(4, promise2)会被执行，打印出4和promise2的状态为pending。
6. 2秒后，setTimeout()中的回调函数开始执行。首先，console.log(5)会被执行，打印出5。
7. 然后，console.log(6, promise1)会被执行，打印出6和promise1的状态为fulfilled。
8. 接着，由于promise1的状态为fulfilled，所以promise2的状态变为rejected，并且console.log(7, promise2)会被执行，打印出7和promise2的状态为rejected。
9. 最后，由于promise2被拒绝并且没有处理该错误，所以控制台会显示一个未捕获的错误，错误消息为"Error: error!!!"。

#### **Promise中构造函数中的resolve或reject只有第一次执行有效。**

```javascript
const promise = new Promise((resolve, reject) => {
    resolve(1);
    reject("error");
    resolve(2);
});
promise.then(res => {
    console.log("then: ", res);
}).catch(err => {
    console.log("catch: ", err);
})
// then：1

```

1. 首先，创建了promise对象，并且立即执行。在promise的构造函数中，resolve(1)被调用，将promise的状态设置为fulfilled，并且传递了值1。
2. 然后，reject("error")被调用，但由于promise的状态已经被resolve()设置为fulfilled，所以该调用被忽略。
3. 接着，resolve(2)被调用，但同样被忽略，因为promise的状态已经被设置为fulfilled。
4. 最后，promise的then()方法被调用，传入一个回调函数(res => { console.log("then: ", res); })。由于promise的状态是fulfilled，并且传递的值是1，所以该回调函数被执行，打印出"then: 1"。

#### **Promise对象中的catch无视链接位置，都能捕获上层未捕捉过的错误**，then3会执行因为catch会返回一个Promise，且由于这个Promise没有返回值，所以打印出来的是undefined。

```javascript
const promise = new Promise((resolve, reject) => {
        reject("error");
        resolve(1);
});
promise.then(res => {
        console.log("then1: ", res);
}).then(res => {
        console.log("then2: ", res);
}).catch(err => {
        console.log("catch: ", err);
}).then(res => {
        console.log("then3: ", res);
})
// catch:  error
// then3:  undefined

```

1. 首先，创建了promise对象，并且立即执行。在promise的构造函数中，reject("error")被调用，将promise的状态设置为rejected，并且传递了错误信息"error"。
2. 然后，resolve(1)被调用，但由于promise的状态已经被reject()设置为rejected，所以该调用被忽略。
3. 接着，promise的第一个then()方法被调用，传入回调函数(res => { console.log("then1: ", res); })。由于promise的状态是rejected，所以该回调函数被跳过，不会被执行。
4. 然后，promise的第二个then()方法被调用，传入回调函数(res => { console.log("then2: ", res); })。由于前一个then()方法没有返回值，所以该回调函数被传递undefined值，打印出"then2: undefined"。
5. 接着，promise的catch()方法被调用，传入回调函数(err => { console.log("catch: ", err); })。由于promise的状态是rejected，并且传递的错误信息是"error"，所以该回调函数被执行，打印出"catch: error"。
6. 最后，promise的第三个then()方法被调用，传入回调函数(res => { console.log("then3: ", res); })。由于前一个catch()方法返回undefined值，所以该回调函数被传递undefined值，打印出"then3: undefined"。

#### **Promise对象的链式调用的执行顺序**

```javascript
Promise.resolve(1)
.then(res => {
        console.log(res);
        return 2;
})
.catch(err => {
        return 3;
})
.then(res => {
        console.log(res);
});
// 1 2

```

1. `Promise.resolve(1)`会返回一个已经处于fulfilled状态的Promise对象，其结果为1。然后，使用`.then()`方法注册回调函数，在Promise对象resolve后执行该回调函数，输出结果1并返回2。由于没有发生错误，所以不会执行`.catch()`方法。
2. 使用第二个`.then()`方法注册回调函数，在前一个回调函数返回的结果上继续操作。在这种情况下，前一个回调函数返回的结果为2，因此输出结果2。

#### **注意then的第二参数错误处理与catch的区别。**

```javascript
Promise.reject('err!!!')
    .then((res) => {
            console.log('success', res)
    }, (err) => {
            console.log('error', err)
    }).catch(err => {
            console.log('catch', err)
    })
// error err!!!


Promise.resolve()
.then(() => {
  throw new Error('error!!!');
})
.then(
  function success(res) {},
  function fail1(err) {
    console.log('fail1', err);
  }
)
.catch(function fail2(err) {
  console.log('fail2', err);
});
// fail1 Error: error!!!

```

第一个代码段： 

在`Promise.reject('err!!!')`之后，`.then()`方法的第一个回调函数被忽略，而是执行了`.catch()`方法注册的回调函数。因此，输出错误信息`err!!!`。

第二个代码段：

在`Promise.resolve()`之后，使用`.then()`方法注册的第一个回调函数抛出了一个错误，这导致Promise对象进入rejected状态。然后，第二个参数是一个错误处理函数，它会被调用并且接收到这个错误信息。因此，输出错误信息`Error: error!!!`。