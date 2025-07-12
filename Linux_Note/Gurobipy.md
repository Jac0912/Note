环境变量：
vim ~/.bashrc
```txt
export GUROBI_HOME="/home/sdn/gurobi1202/linux64"
export PATH="${PATH}:${GUROBI_HOME}/bin"
export LD_LIBRARY_PATH="${LD_LIBRARY_PATH}:${GUROBI_HOME}/lib"
export GRB_LICENSE_FILE="/home/sdn/gurobi1202/gurobi.lic"
```
source ~/.bashrc
cd 到 gurobi/linux64/bin 文件夹下激活
把 license file 保存到/home/sdn/gurobi1202/