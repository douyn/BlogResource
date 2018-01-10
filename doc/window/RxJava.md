# RxJava
## 简介 
RxJava是RectiveX的java VM实现，用来使用可观察序列来编写异步和基于事件的程序的库。

主要是用来实现异步任务，和事件总线的功能

## 使用
1. 创建观察者,有两种实现方式

	1. 创建Observer
		
			Observer<String> observer = new Observer<String>() {
		        @Override
		        public void onSubscribe(Disposable d) {
		            Log.i(TAG, "onSubscribe: ");
		        }
		
		        @Override
		        public void onNext(String s) {
		            Log.i(TAG, "onNext: ");
		        }
		
		        @Override
		        public void onError(Throwable e) {
		            Log.i(TAG, "onError: ");
		        }
		
		        @Override
		        public void onComplete() {
		            Log.i(TAG, "onComplete: ");
		        }
		    };
	2. 创建Subscriber
		
			Subscriber subscriber = new Subscriber() {
		        @Override
		        public void onSubscribe(Subscription s) {
		            Log.i(TAG, "onSubscribe: ");
		        }
		
		        @Override
		        public void onNext(Object o) {
		            Log.i(TAG, "onNext: ");
		        }
		
		        @Override
		        public void onError(Throwable t) {
		            Log.i(TAG, "onError: ");
		        }
		
		        @Override
		        public void onComplete() {
		            Log.i(TAG, "onComplete: ");
		        }
	   	 	};
		
2. 创建被观察者，有三种实现方式
	
	1. Observable.create
		
			
			Observable.create(new ObservableOnSubscribe<String>() {
	            @Override
	            public void subscribe(ObservableEmitter<String> e) throws Exception {
	                // 使用该方法创建observable，需要手动调用onNext, onError, onComplete方法，而onSubscribe是自动调用的
	                e.onNext("observable.create");
	            }
        	}).subscribe(observer);
	2. Observable.just
	
			
			 // 使用该方法创建observable，会自动调用onSubscribe，onNext，onError，onComplete
        	Observable.just("observable.just").subscribe(observer);
	3. Observable.fromArray

			
			// 使用该方法创建的observable，会自动调用onSubscribe，onNext，onError，onComplete
	        String[] quotations = {"observable.fromArray"};
	        Observable.fromArray(quotations).subscribe(observer);
		
3. 创建被观察之后，通过subscribe方法关联观察者
	
	observable.subscribe(observer)

4. 观察者的方法

	onError: 表示事件队列异常，队列终止，下一步会回调onComplete
	
	onSubscribe: 当观察者订阅被观察者的时候，会回调该方法

	onNext: 普通事件，可以在该方法中做一些业务逻辑

	onComplete: 表示事件队列完结

5. 操作符，包括普通操作符，变换操作符，过滤操作符，组合操作符，辅助操作符，辅助处理操作符，条件操作符，布尔操作符，转换操作符

	
6. 普通操作符，例如，interval，intervalRange

	
		 // 普通操作符
        // 用于周期执行
        Observable.interval(3, TimeUnit.SECONDS).just("observable.interval").subscribe(observer);

        // 用于延迟执行并且定期执行
        Observable.intervalRange(0, 6, 0, 3, TimeUnit.SECONDS).create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> e) throws Exception {
                Log.i(TAG, "subscribe: ");
            }
        }).subscribe(observer);
7. 变换操作符，例如，map，flatmap，cast，concatmap
	
	
		// 变换操作符
		// map，指定一个Function对象，将一个Observable转换成另一个Observable对象。
        Observable.just("observable.map").map(new Function<String, String>() {
            @Override
            public String apply(String s) throws Exception {
                return s + "[map]";
            }
        }).subscribe(observer);

		// flatmap，将一个observable对象转换成另一个observable集合，然后将这些observable对象发射的数据平坦的放进一个单独的observable。
		Student[] students = {};
        Observable.fromArray(students).flatMap(new Function<Student, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(Student student) throws Exception {
                return Observable.fromArray(student.classes);
            }
        }).subscribe(observer);

		// cast负责将observable中的所有数据转换成指定类型
        Student[] stus = {};
        Observable.fromArray(stus).flatMap(new Function<Student, ObservableSource<?>>() {
            @Override
            public ObservableSource<?> apply(Student student) throws Exception {
                return null;
            }
        }).cast(String.class).subscribe(observer);
8. 过滤操作符，包括，skip，take，element，fileter等


		// 过滤操作符
        // filter,对Observable产生的结果自定义规则进行过滤，只有满足条件的结果才提交给订阅者。
        Observable.just("你好", "中国", "世界").filter(new Predicate<String>() {
            @Override
            public boolean test(String s) throws Exception {
                return s.equals("你好");
            }
        }).subscribe(new Observer<String>() {
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
        });

        // skip，过滤掉前n项
        Observable.range(0,10).skip(2).subscribe(new Observer<Integer>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(Integer integer) {
                Log.i(TAG, "onNext: " + integer);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });

        // take, 取前n项
        Observable.range(0, 10).take(5).subscribe(new Observer<Integer>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(Integer integer) {
                Log.i(TAG, "onNext: " + integer);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });

        // throttleWithTimeout：如果在限定的时间内，源Observable有新的数据发射出来，该数据就会被丢弃，同时throttleWithTimeout重新开始计时，如果每次都是在计时结束前发射数据，那么这个限流就会走向极端（只会发射最后一个数据）
        Observable.just("hell", "o," "worl", "d").throttleWithTimeout(200, TimeUnit.MILLISECONDS).subscribe(observer);


        // ditinct, 去重
        Observable.just("hh", "hh", "hhh", "h").distinct().subscribe(new Observer<String>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(String s) {
                Log.i(TAG, "onNext: " + s);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });


9. 组合操作符， merge，startWidth，concat，jion，switch，zip


		// 组合操作符
        // merge将多个observable合并到一个observable中进行发射
        Observable<String> observable1 = Observable.just("1", "2");
        Observable<String> observable2 = Observable.just("3", "4");
        Observable.merge(observable1, observable2).subscribe(observer);

        // concat将多个observable合并并按队列顺序发射，前一个没发射完无法进行下一个发射
        Observable.concat(observable1, observable2).subscribe(observer);

        // zip, 合并两个或者多个Obserable发射出的数据项，根据指定的函数变换它们，并发射一个新值。	
	

10. 辅助操作符，DO、delay、observeOn、timeout、timeInterval、timestamp、subscribeOn、meterialize和to等。

		
		// delay：延迟执行发射数据
		Observable<String> observable1 = Observable.just("你好", "hello World");
		Observable<String> observable2 = Observable.just("new obj", "mergeobj");
		Observable.concat(observable1, observable2).delay(5, TimeUnit.SECONDS).subscribe(observer);

		// subscribeOn：指定Obserable自身在那个线程上运行。
		// observeOn：指定Obserable发射出的数据在那个线程运行。

11. 错误操作符，包括catch和retry

	catch能够拦截原始Observable的onError通知，将它替换为其他数据项或者数据序列，让产生的Observable能够正常终止或者根本不终止。

	catch实现分为三个不同的操作符：

	1、onErrorReturn：返回原有Observable行为的备用Observable， 备用的Observable忽略原有的Observable的onError调用，即不会将错误传递给观察者，而是发射一个特殊的项，以及调用观察者的onCompleted。

	2、onErrorResumeNext：跟onErrorReturn一样返回备用的Observable，不会调用原有的Observable的onError，它会发射备用的Observable数据。

	3、onExceptionResumeNext：如果onError收到的Throwable不是一个Exception，它会将错误传递给观察者的onError方法，不会使用备用的Observable。

	retry：不会将原有的Observable的onError通知传递给观察者，这会订阅这个Observable，再给它一次机会无错误地完成其数据序列，而它总会传递onNext通知给观察者。该操作符有可能造成数据重复，因为重新订阅。如果超过了重新订阅的次数，就不会再次订阅了，而是把最新的一个onError通知传递给观察者。

12. 条件操作符,包括：defaultEmpty、skipUntil、amb、skipWhile、takeUtil、takeWhile


	defaultEmpty：如果原有的Observable没有发射数据，就发射一个默认的数据。

	skipUntil：订阅原始的Observable，但是忽略它的发射物，直到第二个Observable发射了一项数据那一刻，它开始发射原始Observable。

13. 布尔操作符,包括：all、isEmpty、contains、exists和sequenceEqual。

	关于条件操作符和布尔操作符，读者可以关注[《RxJava操作符(08-条件和布尔操作) 》](https://link.juejin.im/?target=http%3A%2F%2Fblog.csdn.net%2Fxmxkf%2Farticle%2Fdetails%2F51671826)这篇文章，文章地址：

14. 转换操作符,转换操作符能够将Observable转换为另一个对象或者数据结构，其中转换操作符包括：toMap、toMultiMap、toList、toSortedList、nest和getIterator等。

		// toMap：将原始的Observable发射的所有数据项集合到一个Map中，然后发射该Map。
		String s1 = "你好";
        String s2 = "hello world";
        String s3 = "lalala";

        Observable.just(s1,s2,s3).toMap(new Function<String, String>() {
            @Override
            public String apply(@NonNull String s) throws Exception {
                return s;
            }
        }).subscribe(new SingleObserver<Map<String, String>>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onSuccess(@NonNull Map<String, String> stringStringMap) {
                Log.i("test",""+stringStringMap);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }
        });

		// toMultiMap：类似于toMap，不同的地方在于map的value是一个集合。

		// toList：将发射的数据组成一个List。
		String s1 = "你好";
        String s2 = "hello world";
        String s3 = "lalala";

        Observable.just(s1,s2,s3).toList().subscribe(new SingleObserver<List<String>>() {
            @Override
            public void onSubscribe(@NonNull Disposable d) {

            }

            @Override
            public void onSuccess(@NonNull List<String> strings) {
                Log.i("test",""+strings);
            }

            @Override
            public void onError(@NonNull Throwable e) {

            }
        });

	关于其他操作符，读者可以参考[《RxJava操作符大全》](http://blog.csdn.net/maplejaw_/article/details/52396175)这篇文章。

15. buffer操作符 


		// buffer操作符能收集多个结果到列表中，订阅后会自动清空结果，也可以周期性收集结果
        Observable.range(0, 5).buffer(2).subscribe(new Observer<List<Integer>>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(List<Integer> integers) {
                Log.i(TAG, "onNext: " + integers);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });

        Observable.just("你好", "中国", "我爱", "我家").buffer(3).subscribe(new Observer<List<String>>() {
            @Override
            public void onSubscribe(Disposable d) {

            }

            @Override
            public void onNext(List<String> strings) {
                Log.i(TAG, "onNext: " + strings);
            }

            @Override
            public void onError(Throwable e) {

            }

            @Override
            public void onComplete() {

            }
        });

16. 线程调度

	前面讲解辅助操作符的时候，我们知道使用subscribeOn可以指定Obserable自身在那个线程上运行。使用observeOn可以指定Obserable发射出的数据在那个线程运行。RxJava默认线程是在调用subcribe方法的线程上进行回调，但是如果想切换线程，就需要使用Scheduler。

	在RxJava中内置了以下几个Scheduler：

	1、Scheduler.immediate()：运行在当前线程，是timeout、timestamp和timeInterval操作符的默认调度器。

	2、Scheduler.io()：I/O操作使用的Scheduler。

	3、Scheduler.newThread()：开启一个新的线程执行操作。

	> 2和3的区别就是：2的内部实现了一个无数量上限的线程池，重用空闲的线程，因此2具有更高的效率

	4、Scheduler.trampoline()：可以将任务通过trampoline方法加入队列，该调度器会按顺序处理队列的任务，是repeat和retry操作符的默认调度器。

	5、Scheduler.computation()：计算所使用的调度器，它具有固定的线程池，大小为cpu核数，注意不要将io操作放到computation中，否则io操作的等待时间会浪费cpu。该调度器是buffer、delay、sample、debounce、interval和skip的默认调度器。

	6、AndroidSchedulers.mainThread()：表示在主线程中运行，该调度器是RxAndroid提供的。

		Observable.just("你好","hello world")
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(observer);
