# Android页面跳转的几种方法

## 一般跳转

```java
Intent intent = new Intent(FirstActivity.this,SecondAcvity.class);
startActivity(intent);
```

## 跳转到新的Activity并传递数据

Person实现了Serialable接口,所以所有的Person对象都是可序列化的,这时我们就可以使用Intent来传递Person对象了

```java
Person person = new Person("Michael","123");
Intent intent = new Intent(FirstActivity.this,SecondActivity.class);
intent.putExtra("person_data",person);
startActivity(intent);
```

SecondActivity获取Person数据方法

```java
Person person = (Person) getIntent().getSerializableExtra("person_data");
```

## 销毁当前的Activity并跳转

```java
Intent intent = new Intent(this,SecondActivity.class);
intent.putExtra("isBoy", true);
FirstActivity.this.finish();
startActivity(intent);
```

## 返回之前未关闭的一个Activity

因为Activity是堆栈存储的,finish掉当前的Activity就可以返回之前的Activity

```java
FirstActivity.this.finish();
```