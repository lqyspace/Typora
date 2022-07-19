# 无法恢复到tensorflow1.5.0

安装时报错：

> pip install tensorflow==1.5.0

```python
Note: you may need to restart the kernel to use updated packages.
ERROR: Could not find a version that satisfies the requirement tensorflow==1.5.0 (from versions: 2.2.0rc1, 2.2.0rc2, 2.2.0rc3, 2.2.0rc4, 2.2.0, 2.2.1, 2.2.2, 2.2.3, 2.3.0rc0, 2.3.0rc1, 2.3.0rc2, 2.3.0, 2.3.1, 2.3.2, 2.3.3, 2.4.0rc0, 2.4.0rc1, 2.4.0rc2, 2.4.0rc3, 2.4.0rc4, 2.4.0, 2.4.1, 2.4.2, 2.5.0rc0, 2.5.0rc1, 2.5.0rc2, 2.5.0rc3, 2.5.0, 2.6.0rc0)
ERROR: No matching distribution found for tensorflow==1.5.0
```

> 回答

```
使用python3.6（支持tensorflow 1.5）创建一个虚拟环境并安装tensorflow。您可能安装了更新版本的python(e.g.-python3.8支持tensorflow 2.2或更高版本，python3.9支持tensorflow 2.5或更高版本）。
```

```python
conda create -n py36 python=3.6
conda activate py36
pip install tensorflow==1.5.0
```

