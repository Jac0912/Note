pip install +包名

```shell
python -m venv venv #创建虚拟环境
source activate    #激活虚拟环境
deactivate         #退出虚拟环境
pip list           #列出所有包的情况
pip freeze > requirements.txt  #导出安装的包表
pip install -r requirements.txt  #别人安装相同的包
python.exe -m pip install --upgrade pip  #windows下更新pip
python "C:\Program Files\IBM\ILOG\CPLEX_Studio221\python\setup.py" install
```