##  git 操作

### 1、 设置git的代理

#### 1.1 全局代理设置

```bash
git config --global http.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```

### 1.2 取消代理

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 1.3  查看已有配置

```bash
git config --global -l
```



## 2、忽略 Git Hooks   Husky  等代码格式提交的限制

```bash
# 在提交后面 增加 --no-verify
git commit -m 'V6' --no-verify
```

