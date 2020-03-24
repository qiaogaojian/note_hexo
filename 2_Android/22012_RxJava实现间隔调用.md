# RxJava实现间隔调用

## 代码

``` java
    Observable.interval(1, TimeUnit.SECONDS, Schedulers.trampoline())
    .take(6)
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
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

如果涉及到对UI的操作 需要设置好线程

``` java
 Observable.interval(1, TimeUnit.SECONDS, Schedulers.trampoline())
                  .take(59)
                  .subscribeOn(Schedulers.io())                 // io线程
                  .observeOn(AndroidSchedulers.mainThread())    // 主线程
                  .subscribe(new Observer<Long>()
                  {
                      @Override
                      public void onCompleted()
                      {
                          if (activityRegBinding.etPhoneNumber.getText().length() == 11)
                          {
                              mainView.setSendBtnState(2);
                          } else
                          {
                              mainView.setSendBtnState(1);
                          }
                      }

                      @Override
                      public void onError(Throwable e)
                      {

                      }

                      @Override
                      public void onNext(Long aLong)
                      {
                          activityRegBinding.tvSendCode.setText(String.format("(%ss)后重发", (59 - aLong) + ""));
                      }
                  });
```