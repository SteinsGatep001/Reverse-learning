
# 工具
cotinuing
## install old gdb(用pwndbg的话就可以略过):
    sudo apt-get remove gdb
    wget http://security.ubuntu.com/ubuntu/pool/main/g/gdb/gdb_7.4-2012.02-0ubuntu2_amd64.deb
    sudo dpkg -i ./gdb_7.4-2012.02-0ubuntu2_amd64.deb
## install peda:
    - Download
        $ wget http://ropshell.com/peda/peda.tar.gz
    - Unpack to HOME directory
        $ tar zxvf peda.tar.gz
    - Append a line to ~/.gdbinit to load PEDA when GDB starts
        $ echo "source ~/peda/peda.py" >> ~/.gdbinit

## pwndbg
这个比peda好用一点。推荐

## GDB debug
##自定义hook(这里自定制用，其实pwngdb就完全足够了)
define hook-stop
>info registers 
>x/24wx $esp
>x/2i $eip
>end
### checksec
    CANARY    : 覆盖返回地址基本不可利用
    FORTIFY   : 一种安全机制
    NX        : 堆栈不可执行
    PIE       : 攻击时需要泄露地址
    RELRO     : Partial: 不可修改strtab
                Full: 程序装载时填充got表

## metsploit:msfconsole
    msf >
    show payloads: 显示所有渗透模块
    use linux/x64/exec: 使用linux x64 模块
    set cmd /bin/sh
    generate -t py -b "/x00":产生shellcode /xXX的形式

## redare2(更多使用用的话再积累 IDA静态很多时候就够了)
aaa
[x] Analyze all flags starting with sym. and entry0 (aa)<br>
[x] Analyze len bytes of instructions for references (aar)<br>
[x] Analyze function calls (aac)<br>
s sym.main:运行到main处<br>
pdf:打印汇编<br>
odd [strings]: 给参数来运行程序<br>
dc: 运行程序<br>
db: 下断点<br>
dr: 查看所有寄存器信息<br>
VV: 查看调用关系视图<br>
afvn [name] [name]: 重命名，类似ida的n<br>

echo disass main | gdb ./[program]  利用管道来调试<br>
"\41\xffABCD".encode('hex')<br>
set disassembly-flavor intel    设置为x86汇编显示<br>

# Tips
attention!!!!
## 连接
ssh user@192.168.47.143 用来连接目标主机<br>
scp user@192.168.47.143 [filename] [dir]<br>
scp -P2222 col@pwnable.kr:/home/passcode<br>
## 追踪程序
ltrace<br>
strace<br>
## 本机测试
socat tcp-listen:12345 exec:./stack_overflow 把程序放到本机运行<br>
while true ; do socat tcp-listen:12345 exec:./readme.bin ; sleep 1; done    保持程序一直执行<br>
nc 127.0.0.1 12345 本地测试<br>
## 加载信息
info proc map 查看各个库加载信息然后寻找 "/bin/sh" 字符串<br>
strings: 查看文件中可见字符串<br>
strings -a -t x /lib32/libc.so.6 | grep "/bin/sh"<br>
objdump -d stack7 | grep "ret" 可以用来查找ret指令<br>
objdump -x [filename] 打印头文件信息以及区段信息<br>
objdump -T libc.so | grep gets <br>
## 查找gadgets
ROPgadget --binary level4 --only "pop|ret" <br>
ROPgadget --binary libc.so.6 --only "pop|ret" | grep rdi<br>
objdump -d ./level5<br>
__libc_csu_init这个函数里找 ROP<br>
objdump -d -j.plt pwn | grep write 查找write函数地址<br>

# Konwledge
QAQ
栈主要就是找溢出和rop等
## 姿势
### 1.覆盖x86_64 ret libc
执行call操作时栈内已经存放了传递的变量，call将当前地址压入栈中，作为返回地址，然后执行jmp到指定函数位置。构造call system时可以利用这个先存放一个地址，然后跳转。
### 2.dl-resolve
高端玩家姿势，读取内存。<br>
to be continued

## 容易犯的错误
orz
### ret & call
ret 后面必须是 .plt<br>
__libc_init 里用call来必须是 .got的(具体LCTF pwn-100那个)
### 其他的坑
to be continued

# resources

##[pwnable](http://pwnable.kr/play.php)
主要linux elf为主 比较适合开拓思路和入门
## [io](http://io.netgarage.org/)
还没来得及看，主要是有arm的

#一些大佬的blog&net
---------------------
## [LiveOverFlow](https://www.youtube.com/channel/UClcE-kVhqyiHCcjYwcpfj9w)
youtube有相关视频 从他的视频学了不少
## [CTF writeup](https://github.com/ctfs)
各种writeup (大多国外)
## [DEF CON](https://www.defcon.org/#)
知名hacker团队
## [tisiphone](https://tisiphone.net/)
推送多 有心得系列文章可以看看
## [muhe](http://o0xmuhe.me/)
CTFer 二进制
## [Icemakr](http://0byjwzsf.me/)
LCTF2016 pwn出题大大
## [uaf](http://uaf.io/)
国外一位，各种ctf wp都有他的身影

## [另外 hardware](http://www.sp3ctr3.me/hardware-security-resources/)
下面的一些资源= = 嗯。。有时间看看