# Android使用VideoView播放视频

## 布局

``` xml
    <android.support.constraint.ConstraintLayout>

        <ImageView
            android:id="@+id/test_button"
            android:layout_width="100dp"
            android:layout_height="60dp"
            android:src="@color/colorAccent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            android:layout_marginRight="80dp" />

        <VideoView
            android:id="@+id/test_video"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:visibility="gone"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent" />

    </android.support.constraint.ConstraintLayout>
```

## 代码

``` java
RxView.clicks(gamehallBinding.testButton)
        .compose(mMainView.getActivity().<Void>bindUntilEvent(ActivityEvent.DESTROY))
        .throttleFirst(1000,TimeUnit.MILLISECONDS )
        .subscribe(new Action1<Void>()
        {
            @Override
            public void call(Void aVoid)
            {
                gamehallBinding.testVideo.setVisibility(View.VISIBLE);
                gamehallBinding.testVideo.setVideoURI(Uri.parse("android.resource://"+WerewolfApplication.getInstance().getPackageName()+"/"+R.raw.day2night)); // 内部视频
//                      gamehallBinding.testVideo.setVideoURI(Uri.parse("http://www.test.com/day2night.mp4"));  // 远程视频
//                      gamehallBinding.testVideo.setVideoPath(Environment.getExternalStorageDirectory()+"/001/day2night.mp4"); // 外部视频
                gamehallBinding.testVideo.seekTo(0);
                gamehallBinding.testVideo.requestFocus();
                gamehallBinding.testVideo.start();
                gamehallBinding.testVideo.setOnCompletionListener(new MediaPlayer.OnCompletionListener()
                {
                    @Override
                    public void onCompletion(MediaPlayer mp)
                    {
                        gamehallBinding.testVideo.setVisibility(View.GONE);
                    }
                });
            }
        });
```