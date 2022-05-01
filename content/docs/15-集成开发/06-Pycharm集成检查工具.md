---
title: 06-Pycharm集成检查工具
date: 2022-04-30 16:55:47
tags: my-notes
---
pylint

```python

Program: C:\Users\Administrator\AppData\Local\Programs\Python\Python36-32\Scripts\pylint.exe
Arguments: -rn --msg-template="{abspath}:{line}: [{msg_id}({symbol}), {obj}] {msg}" $FilePath$
Working directory: $FileDir$

Output filters:
```



flake8

```python

Program: $PyInterpreterDirectory$/python
  C:\Users\zhang\AppData\Local\Programs\Python\Python36\Scripts\flake8.exe
Arguments: -m flake8 --show-source --statistics $ProjectFileDir$
Working directory: $ProjectFileDir$

Output filters:
```



autopep8

```python

Program: C:\Users\Administrator\AppData\Local\Programs\Python\Python36-32\Scripts\autopep8.exe
Arguments: --in-place --aggressive --aggressive $FilePath$
Working directory: $ProjectFileDir$

Output filters: $FILE_PATH$\:$LINE$\:$COLUMN$\:.*
```