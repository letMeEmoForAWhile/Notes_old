# 下载特定分支

-b 

# 递归下载

##### 使用时机：

仓库中有`.gitmodules`文件，说明该仓库包含子模块，可以使用`--recursive`选项，递归地初始化和更新所有子模块。

##### 用法：

1. 克隆包含子模块的仓库

   ```bash
   git clone --recursive [repository_url]
   ```

   该命令会克隆主仓库并递归地初始化和更新所有子模块。

2. 初始化和更新子模块：

   如果已经克隆了一个包含子模块的仓库，但没有使用`--recursive`选项，可以使用以下命令初始化和更新子模块：

   ```bash
   git submodule update --init --recursive
   ```

##### `.gitmodules`文件

```ini
[submodule "submodule_name"]
    path = path/to/submodule
    url = https://github.com/example/submodule.git
```

- `submodule_name`：子模块的名称。
- `path`：子模块相对于主仓库的路径。
- `url`：子模块的远程仓库地址。
