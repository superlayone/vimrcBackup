vimrcbackup
===============================================


主要是Ubuntu12.04上配置YouCompleteMe(以后简称YCM)的总结，其他的通过安装Vundle和备份vimrc就很好搞定，不多说了。

安装vim
---------------------------------

首先，这个东西对Vim的版本有要求( Vim 7.3.584及以上)，在apt-get下的vim版本太低，所以需要重新编译vim安装，
关于这个可以[参考这里][vim-build]，基本照做就行。

安装Vundle
-----------------------------------------

这个东西是管理vim插件的一个插件。具体办法不用多说，直接上官方README[Vundle][vundle]，注意下vimrc的配置就好，
用起来非常好使，直接在vimrc添加所需的插件的地方就好，比如我要安装YouCompleteMe那么在vimrc中首先添加:

    Bundle "Valloric/YouCompleteMe"

然后进入vim，运行':BundleInstall',然后就行了(当然YouCompleteMe也是需要这个的，但是折腾的地方主要不在这)

安装YCM
-----------------------------------------

一般vim插件的安装基本上到上面就基本结束了，但这个要麻烦点，我们得继续安装YCM，主要是编译下ycm_core. 
准备工作:

    sudo apt-get install build-essential cmake
    sudo apt-get install python-dev

如果要带上是C/C++的话:

    cd ~/.vim/bundle/YouCompleteMe
    ./install.sh --clang-completer

没有C/C++的话就比较简单:

    cd ~/.vim/bundle/YouCompleteMe
    ./install.sh

理论上这样应该就可以的，但是我这边不行，主要是没有libclang.so这个东西，这个libclang.so貌似系统
没有，我locate libclang.so 是不行的，apt-cache search llvm的最高版本貌似没有3.3，所以我手动装了.
Ubuntu上详细的安装可以见这里,[这个][install-llvm-clang]还有libc++的配置，不过因为要翻墙，所以在这里把我用到的备份下.
准备工作:

    sudo apt-get install g++ subversion

接着下载llvm的源码安装即可，我把源码放在~下了,当然可以时随意的地方:

    cd ~
    mkdir Clang && cd Clang
    svn co http://llvm.org/svn/llvm-project/llvm/trunk llvm
    cd llvm/tools
    svn co http://llvm.org/svn/llvm-project/cfe/trunk clang

然后编译就好了，注意下路径就好:

    cd ..
    cd ..
    mkdir build && cd build
    ../llvm/configure --prefix=/usr/clang_3_3 --enable-optimized --enable-targets=host
    make -j 8

最后安装Clang:

    sudo make install

配置下环境变量再~/.bansh下加这么一句就好:

    export PATH=/usr/clang_3_3/bin:$PATH

最后的工作比较简单:

    cd ~
    mkdir ycm_build
    cd ycm_build

生成Makefile:

    cmake -G "Unix Makefiles" . ~/.vim/bundle/YouCompleteMe/cpp

到这不要C/C++其实就好了，不过折腾了那么久就是为了C/C++，所以还得继续。

     cmake -G "Unix Makefiles" -DEXTERNAL_LIBCLANG_PATH=/usr/clang_3_3/lib/libclang.so .
     ~/.vim/bundle/YouCompleteMe/cpp

这个我是指定的动态库的地址，这样configure就不会说缺少这个库了。最后:

    make ycm_core

最后，注意下配置那个vimrc的配置就好了，全文收工。







[Clang]: http://clang.llvm.org/
[vundle]: https://github.com/gmarik/vundle#about
[pathogen]: https://github.com/tpope/vim-pathogen#pathogenvim
[clang-download]: http://llvm.org/releases/download.html#3.3
[brew]: http://mxcl.github.com/homebrew/
[cmake-download]: http://www.cmake.org/cmake/resources/software.html
[macvim]: http://code.google.com/p/macvim/#Download
[vimrc]: http://vimhelp.appspot.com/starting.txt.html#vimrc
[gpl]: http://www.gnu.org/copyleft/gpl.html
[vim]: http://www.vim.org/
[syntastic]: https://github.com/scrooloose/syntastic
[flags_example]: https://github.com/Valloric/YouCompleteMe/blob/master/cpp/ycm/.ycm_extra_conf.py
[compdb]: http://clang.llvm.org/docs/JSONCompilationDatabase.html
[subsequence]: http://en.wikipedia.org/wiki/Subsequence
[listtoggle]: https://github.com/Valloric/ListToggle
[vim-build]: https://github.com/Valloric/YouCompleteMe/wiki/Building-Vim-from-source
[tracker]: https://github.com/Valloric/YouCompleteMe/issues?state=open
[issue18]: https://github.com/Valloric/YouCompleteMe/issues/18
[delimitMate]: https://github.com/Raimondi/delimitMate
[completer-api]: https://github.com/Valloric/YouCompleteMe/blob/master/python/ycm/completers/completer.py
[win-wiki]: https://github.com/Valloric/YouCompleteMe/wiki/Windows-Installation-Guide
[eclim]: http://eclim.org/
[jedi]: https://github.com/davidhalter/jedi
[ultisnips]: https://github.com/SirVer/ultisnips/blob/master/doc/UltiSnips.txt
[exuberant-ctags]: http://ctags.sourceforge.net/
[ctags-format]: http://ctags.sourceforge.net/FORMAT
[vundle-bug]: https://github.com/gmarik/vundle/issues/48
[ycm-users]: https://groups.google.com/forum/?hl=en#!forum/ycm-users
[install-llvm-clang]:http://solarianprogrammer.com/2013/01/17/building-clang-libcpp-ubuntu-linux/
