```shell
pip install uv  #安装uv

uv python list  #所有支持python版本
uv python install <python版本>  #安装python版本

uv run -p <python版本> main.py  #以特定版本运行脚本
uv run -p <python版本> python  #python交互命令行

uv add <库名>  #添加库
uv remove <库名>  #移除库
uv tree  #依赖树
uv sync  #自动读取pyproject.toml文件搭建环境
```