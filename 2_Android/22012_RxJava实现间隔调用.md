# RxJava实现间隔调用

## 代码

``` java
    Observable.interval(1, TimeUnit.SECONDS, Schedulers.trampoline())
    .take(6)
    .subscribe(new Observer<Long>() {
        @Override
        public void onSubscribe(Disposable d) {
            System.out.println("onSubscribe");
        }

        @Override
        public void onNext(Long value) {
            System.out.println("onNext:" + value);
        }

        @Override
        public void onError(Throwable e) {
            System.out.println("onError");
        }

        @Override
        public void onComplete() {
            System.out.println("onComplete");
        }
    });
```