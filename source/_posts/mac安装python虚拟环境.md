Mac X Yosemite默认python是2.7版本，但总所周知的是，现在python有2.x，也有新的3.x版本。而在windows安装多版本python很简单，下载后，将lib路径加载到系统的path路径里就可以了。

1 Python多版本工具pydev

1）安装homebrew

可搜索如何安装使用命令brew

2）安装pyenv，以及pyenv-virtualenv

brew install pyenv
brew install pyenv-virtualenv
成功后，使用命令pyenv install --list查看可用的版本，最新是3.5.1

3）安装对应的版本

比如要安装3.5.1，则使用pyenv install 3.5.1
安装过程中，可能会出现ImportError: No module named 'zlib' ，这个是zlib没有正确被引入。再网上搜索，先使用brew安装zlib，注意在安装过程中，显示需安装homebrew/dupes/zlib而不是zlib，按照指示安装对应zlib即可。
安装后使用pyenv install 3.5.1还是报错，安装帖子Mac OSX 下使用pythonbrew安装zlib 报ImportError: No module named 'zlib' 解决方案]解决。

4）切换Python版本

安装后可以使用pyenv versions查看安装的版本

5）设定环境，在.bashrc(.bash_profile)增加：

if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
注意：保存后，source生效下对应的文件

6）使用pyenv virtualenv创建虚拟的python35环境

pyenv virtualenv 3.5.1 python35
然后切换(就像virtualenv使用一样，active是生效，deactivate是取消恢复默认)：
pyenv activate python35

7) (补充)在进入虚拟环境后，如何安装第三方包

当安装pip和easy_install后，安装过程中，mac会提示 "PermissionError",此时要小心，如果用sudo去执行，由于sudo用户还是使用的默认的2.7版本，所以安装不是在虚拟Python环境中进行的，正确的做法是给“/Users/Yourdir/.pyenv/”赋予当前用户可以读写执行的权限，然后执行pip install即可