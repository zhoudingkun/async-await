本文借鉴参考了边城先生的《理解 JavaScript 的 async/await》如作者觉得不妥，请随时联系我将之撤下。
# async-await
async/await以及promise之间的关系

async和await

        随着node版本的更新，越来越多人开始研究、使用async和await，我对这个不是很了解，所以决定研究一下，然后水一帖。
        JavaScript 的 async/await 实现，也离不开 Promise。

async和await到底是干什么的？
        任何一个代码单词，它的名称都是有意义的，先看英文单词，async是’异步’的简写，await可以看成 async wait 等待异步，所以可以理解async用于声明一个function是异步的，而await就是等待一个异步方法的执行完成。
async 起什么作用？

        这个问题的关键是async函数是怎么处理返回值的。我们来写一个例子
Async function test1 () {
    return ‘hello async'
}
console.log(test1());


可以看出结果是输出了一个promise 对象，那么就可以得到这样的信息，async函数会返回一个promise对象，如果在函数中直接return，async会把这个直接量通过promise.resolve()    封装成promise对象，所以如果在最外层不能用await的情况下，我们要用.then()链来处理promise对象从而获取返回值
test1().then(v => {console.log(v)})

这个时候获取了promise对象，而async函数没有了返回值，所以最后返回一个undefind；

联想一下 Promise 的特点——无等待，所以在没有 await 的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且，绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数并无二致。

await在等啥玩意呢？

一般来说，await在等一个async函数的完成。不过按语法说明，await等待的是一个表达式，但表达式结果没有特殊限定。

async返回一个promise对象，await可以用于等待async的返回值，注意⚠️await不仅仅用于等peomise对象，还可以等任意表达式的结果，所以await后面可以接普通函数或者直接量的。具体如下面例子


再举一个例子
async function f() {

  let promise = new Promise((resolve, reject) => {
    setTimeout(() => resolve("done!"), 1000)
  });

  let result = await promise; // 等待至promise获得结果 (*)

  alert(result); // "done!"
}

f();

该函数在运行至await时，执行了“pauses”操作，直至promise执行完毕后重新执行接下来的代码。所以该段代码将在一秒后显示“done!”。
让我们强调一遍：await将顺序使得JavaScript的运行时等待promise执行完毕，而后继续运行余下代码。等待时的操作并不会消耗任何CPU资源，因为此时的运行时可以同时执行其他操作：执行其他的代码，处理事件逻辑等。
这仅仅是一项相对promise.than更为优雅的语法来获取promise的运行结果，更容易阅读和编写代码而已。


await等到了要等的，然后呢

await等到promise时，它会阻塞后面的代码，等着promise对象resolve，然后得到resolve的值，作为await的结果。这也是为啥await必须在async函数中执行，async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。 

async/await的作用，或者说它干了啥
 举例，用setimeout模拟耗时的异步操作

Function old-time(){return new Promise(resolve => {setTimeout(()=>resolve(‘value’),1000)})}

old-time().then(v=>{console.log(‘got’, v)})

改成async/await

async old-time(){return new Promise(resolve => {setTimeout(()=>resolve(‘value’),1000)})};
Async test(){ const v = await old-time(),console.log(‘v’, v)}

test();


Async/await的优势在于处理then链

async function doit() {
    console.time(‘doit’);
Const time1 =300;
Const time2 = await step1(time1);
Const time3 = await step2(time1,time2);
const result = await step3(time1, time2, time3);
console.log(`result is ${result}`);
console.timeEnd("doIt");
}
Doit();
