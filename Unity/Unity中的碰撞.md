# Unity 中的碰撞

## 碰撞的两种形式

- Collision:造成物理碰撞,触发 OnCollision 事件.
- Trigger:没有物理碰撞效果,触发 OnTrigger 事件.

## 碰撞的条件

- Collision 碰撞:
  1.  双方都有碰撞体
  2.  撞的一方必须有刚体
  3.  双方不可同时勾选 Kinematic 选项
  4.  双方都不可勾选 Trigger 触发器

## 碰撞的事件
