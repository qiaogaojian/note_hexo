# Unity 中 Awake( )和 Start( )的小区别

## Awake

### 绑定对象 active

### 实例化后不论脚本是否 enable

### 只会调用一次

## Start

### 绑定对象active

### 脚本 enable

### 只会 调用一次

### 在第一次 Update 前调用,可能没有想象中的那么快,在这里初始化要想好调用顺序
