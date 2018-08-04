# Unity 中的碰撞

## 碰撞的两种形式

### Collision:造成物理碰撞,触发 OnCollision 事件.

### Trigger:没有物理碰撞效果,触发 OnTrigger 事件.

## 碰撞的条件

### Collision 碰撞

#### 1. 双方都有碰撞体
  
#### 2. 撞的一方必须有刚体

#### 3. 双方不可同时勾选 Kinematic 选项

#### 4. 双方都不可勾选 Trigger 触发器

### Trigger 触发器

#### 1. 双发都有碰撞体

#### 2. 撞的一方必要有刚体

#### 3. 至少有一方勾选 Trigger 触发器

## 碰撞的事件

### OnTriggerEnter() OnTriggerStay() OnTriggerExit()

### OnCollisionEnter() OnCollisionStay() OnCollisionExit()

### 1. Enter 事件表示两物体接触的瞬间,会执行一次

### 2. Stay 事件表示两物体持续接触,会不断执行

### 3. Exit 事件表示当两物体分开瞬间,会执行一次
