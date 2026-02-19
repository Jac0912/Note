```shell
./mill mill.idea.GenIdea/idea
taskkill /F /IM java.exe
./mill mill.bsp.BSP/install
chmod +x mill

> curl.exe -L https://repo1.maven.org/maven2/com/lihaoyi/mill-dist/1.1.0-RC4/mill-dist-1.1.0-RC4-mill.bat -o mill.bat
> curl -L https://repo1.maven.org/maven2/com/lihaoyi/mill-dist/1.1.0/mill-dist-1.1.0-mill.sh -o mill
> ./mill version
1.1.0-RC4
//wsl.localhost/Ubuntu-24.04/home/jac0912/ChiselProjects/zcu216_chisel/project
C:/Jac0912Data/ChiselProjects/zcu216_chisel
-DemitVcd=1
> curl -L https://repo1.maven.org/maven2/com/lihaoyi/mill-dist/0.12.17/mill-dist-0.12.17-mill.sh -o mill
> chmod +x mill
> echo 0.12.17 > .mill-version
gtkwave trace.vcd
```