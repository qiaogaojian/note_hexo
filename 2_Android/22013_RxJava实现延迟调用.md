# RxJava实现延迟调用

## 代码

``` java
    Observable.timer(1, TimeUnit.SECONDS).subscribe(new Observer<Long>() {
        @Override
        public void onSubscribe(Disposable d) {
            System.out.println("onSubscribe");
        }

        @Override
        public void onNext(Long value) {
            System.out.println("延迟了:" + value + "秒.");
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