## 1. 入门
RxJava是一个在JVM上使用可观测序列来组成异步的，基于事件的程序的库。

我们可以把它和Handler、AyncTask归为一类，都是用来处理异步事件的库。

它的特点是：
1. 逻辑简洁
2. 使用简单
3. 实现优雅

## 2. 原理解析
被观察者通过订阅产生一系列事件，观察者按顺序接收事件并按顺序做出反应。
## 3. 基础使用
步骤：

1. 创建被观察者(Observerable)
2. 创建观察者(Observer)
3. 通过订阅(subscribe)链接观察者和被观察者

### 创建观察者

    		// create 最基础的创建被观察者的方法
            Observable<Integer> observable = Observable.create(new ObservableOnSubscribe<Integer>() {
                @Override
                public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {

                }
            });

            // just
            Observable<String> observable_just = Observable.just("a", "b", "c");

            // from
            Observable observable1 = Observable.fromArray(new String[]{"1", "2", "3"});

#### 创建被观察者

   		 // observer
            Observer<String> observer = new Observer<String>() {
                @Override
                public void onSubscribe(Disposable d) {

                }

                @Override
                public void onNext(String s) {

                }

                @Override
                public void onError(Throwable e) {

                }

                @Override
                public void onComplete() {

                }
            };

            // subscriber implement observer
            Subscriber<String> subscriber = new Subscriber<String>() {
                @Override
                public void onSubscribe(Subscription s) {

                }

                @Override
                public void onNext(String s) {

                }

                @Override
                public void onError(Throwable t) {

                }

                @Override
                public void onComplete() {

                }
            }
            
#### 订阅

	observerable.subscribe(observer);
    // 或者
    observerable.subscribe(subscriber);

## 4. 操作符，英文好的话建议直接阅读[官方文档](http://reactivex.io/documentation/operators.html#filtering)， 文档中有实例动画，参考便于理解
#### 创建observables
###### create
###### just
###### from
![](http://reactivex.io/documentation/operators/images/from.c.png)
###### defer，每次被订阅的时候才会去创建observable，不订阅不创建
![](http://reactivex.io/documentation/operators/images/defer.c.png)
###### range
###### empty/never/error
###### interval,创建一个observable,按指定的周期发送一串整形数字
###### timer，类似interval，但是只有两个参数，不能指定周期
###### repeat 复制另一个observable对象,obserable.repeat();
###### start,创建一个observable，发射一个方法的返回值

#### 转换observable
###### map，将一个函数应用到observable的每一项，并把结果发送出去
###### flatmap，使用传递过来的事件对象创建了一个observable对象，并不发送这个observable而是将他激活，于是它开始发送事件，每一个创建出来的observable发送的事件都被汇入同一个observable
###### buffer(count,skip)，用buffer集合收集数据，并把这些buffer集合发送出去
###### scan,将一个方法应用到每个项，并发射每个结果({1,2,3} -> sub(a+b) -> {1,3,6})，与reduce类似，但reduce只发射最后一项的结果
###### window，用observable收集数据，最后将这些observable收集起来发送出去
###### groupby，用于分类

#### 过滤 Observeables
###### debounce(当时间跨度内没有再发出项目，则发送该项目，否则不发送)
###### distinct(去重)
###### elementAt(只发出第n项)
###### filter(只发送通过测试的哪些项)
###### first(只发出第一项或者满足条件的第一项)
###### ignoreelements(不发出任何项目)
###### last(只发出最后一项或者满足条件的最后一项)
###### take(只发出observable发出的前n项)
###### takelast(只发出observable发出的最后几项)
###### sample (在周期性的间隔中发出observable发出的最新项目)
###### skip(抑制observable发出项目的前几项)
###### skiplast(抑制observable发出项目的后几项)

#### 组合 observable
###### and/then/when ([unknown]通过Pattern和Plan中间人组合两个或多个Observable发出的项目集)
###### combinelatest （当两个Observable中的任何一个发出一个项目时，通过指定的函数组合每个Observable发出的最新项目，并根据此函数的结果发出项目）
###### join (在根据另一个Observable发出的项目定义的时间窗口期间发出一个Observable中的项目时，组合两个Observable发出的项目)
###### merge (通过合并排放，将多个observable合并为1个)
###### startwith (将另外一个observable加到一个observable的开头)
###### switch 
###### zip (通过指定的功能将多个observable的排列组合在一起，并根据此功能的结果为每个组合发送单个项目)
## 5. 线程调度
## 6. 应用实践
## 7. 与第三方库的混用