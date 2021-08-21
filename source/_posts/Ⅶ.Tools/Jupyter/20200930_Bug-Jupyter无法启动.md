# Bug-Jupyter无法启动

## 报错

Cannot start jupyter notebook, DLL load failed

## 解决方法

把 Anaconda3\Library\bin 添加到系统环境变量中

一定要确保jupyter_notebook_config.py中自己配置的工作路径是存在的,不存在也会启动不了