---
layout: post
title: "Gentoo KDE5 桌面安装"
description: "准备工作 qt5安装"
category: Gentoo
tags: [Gentoo, kde5, qt5]
---


准备用三篇文章来介绍一下Gentoo上KDE5的安装，官方[wiki](https://wiki.gentoo.org/wiki/KDE)介绍。


KDE桌面依赖Qt，所以先来安装Qt， Gentoo官方Qt [wiki](https://wiki.gentoo.org/wiki/Qt)。


由于Qt整个包比较大自成体系，并且重要的一点并非是必须的软件，
Qt并未包含在官方发布Gentoo stage3中，
而是被单独放在一个官方的layman overlay中。

首先安装layman, 并且在layman中添加Qt的overlay

    emerge -av layman
    layman -a qt


查看/etc/portage/make.config

    #this sources the PORTDIR_OVERLAY variable defined by layman.
    #however, the variable expanded by layman was empty
    source /var/lib/layman/make.conf
    #now the layman defined overlays take precedence,
    #but the user defined overlay still has the lowest priority
    PORTDIR_OVERLAY="
    ${PORTDIR} 
    ${PORTDIR_OVERLAY}"


这几行配置是把layman的overlay源加入到portage软件源中。


在官方overlay中有以下几个软件集合

    $ls /var/lib/layman/qt/sets/
    lxqt-live  qt4  qt5-addons  qt5-essentials  qt5-tools



按官方的说法按装Qt4只要


    emerge -av @qt4

Qt5

    emerge -av @q5-essentials @qt-addons


实际中在安装Qt5的时候出现软件冲突

    
    # emerge -av @qt5-essentials @qt5-addons

    These are the packages that would be merged, in order:

    Calculating dependencies... done!
    [ebuild  N     ] dev-libs/libyaml-0.1.6::gentoo  USE="-doc -examples -static-libs {-test}" 492 KiB
    [ebuild  N     ] sys-libs/libunwind-1.1:7::gentoo  USE="-debug -debug-frame -libatomic -lzma -static-libs" 1,073 KiB
    [ebuild  N     ] dev-util/google-perftools-2.0-r2:0/4::gentoo  USE="debug -largepages -minimal -static-libs {-test}" 1,232 KiB
    [ebuild  N     ] app-arch/snappy-1.1.2-r1::gentoo  USE="-static-libs" ABI_X86="(64) -32 (-x32)" 1,451 KiB
    [ebuild  N     ] dev-util/ragel-6.7-r1::gentoo  USE="-vim-syntax" 1,156 KiB
    [ebuild  N     ] dev-libs/leveldb-1.18-r1::gentoo  USE="snappy tcmalloc -static-libs" 205 KiB
    [ebuild     U ~] media-libs/freetype-2.6.2:2::gentoo [2.5.5:2::gentoo] USE="X adobe-cff bindist bzip2 png -debug -doc -fontforge -harfbuzz -infinality -static-libs -utils (-auto-hinter%)" ABI_X86="(64) -32 (-x32)" 1,727 KiB
    [ebuild  N     ] app-eselect/eselect-ruby-20131227::gentoo  2 KiB
    [ebuild  N     ] dev-qt/qtcore-5.4.2:5::gentoo  USE="icu -debug -systemd {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtcore-5.6.0_beta:5/5.6::qt  USE="icu -debug -systemd {-test}" 59,321 KiB
    [ebuild  N     ] dev-qt/qtxml-5.4.2:5::gentoo  USE="-debug {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtnetwork-5.6.0_beta:5/5.6::qt  USE="bindist ssl -connman -debug -libproxy -networkmanager {-test}" 0 KiB
    [ebuild  N     ] dev-qt/qtscript-5.4.2-r1:5::gentoo  USE="jit -debug -scripttools {-test}" 2,531 KiB
    [ebuild  N    ~] dev-qt/qttest-5.6.0_beta:5/5.6::qt  USE="-debug {-test}" 0 KiB
    [ebuild  N     ] dev-qt/qtconcurrent-5.4.2:5::gentoo  USE="-debug {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtxmlpatterns-5.6.0_beta:5/5.6::qt  USE="-debug {-test}" 1,861 KiB
    [ebuild  N     ] dev-lang/ruby-2.1.7:2.1::gentoo  USE="berkdb gdbm ipv6 ncurses rdoc readline ssl -debug -doc -examples -rubytests -socks5 -xemacs" 9,154 KiB
    [ebuild  N     ] dev-lang/ruby-2.0.0_p647-r1:2.0::gentoo  USE="berkdb gdbm ipv6 ncurses rdoc readline ssl -debug -doc -examples -rubytests -socks5 -xemacs" CPU_FLAGS_X86="sse2" 8,110 KiB
    [ebuild  N     ] dev-ruby/rubygems-2.2.5-r1::gentoo  USE="-server {-test}" RUBY_TARGETS="ruby20 ruby21 (-ruby19)" 408 KiB
    [ebuild  N     ] virtual/rubygems-10::gentoo  RUBY_TARGETS="ruby20 ruby21 (-ruby19)" 0 KiB
    [ebuild  N     ] dev-ruby/rake-0.9.6-r1::gentoo  USE="-doc {-test}" RUBY_TARGETS="ruby20 ruby21 (-ruby19)" 121 KiB
    [ebuild  N     ] dev-ruby/json-1.8.2-r1::gentoo  USE="-doc {-test}" RUBY_TARGETS="ruby20 ruby21 (-ruby19)" 149 KiB
    [ebuild  N     ] dev-ruby/racc-1.4.11::gentoo  USE="-doc {-test}" RUBY_TARGETS="ruby20 ruby21 (-ruby19)" 111 KiB
    [ebuild  N     ] dev-ruby/rdoc-4.2.0::gentoo  USE="-doc {-test}" RUBY_TARGETS="ruby20 ruby21 (-ruby22)" 718 KiB
    [ebuild  N     ] dev-qt/qtsql-5.4.2:5::gentoo  USE="mysql sqlite -debug -freetds -oci8 -odbc -postgres {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtserialport-5.6.0_beta:5/5.6::qt  USE="-debug {-test}" 321 KiB
    [ebuild     U ~] media-libs/harfbuzz-1.1.3:0/0.9.18::gentoo [0.9.41:0/0.9.18::gentoo] USE="cairo glib graphite introspection truetype -fontconfig -icu -static-libs {-test}" ABI_X86="(64) -32 (-x32)" 1,265 KiB
    [ebuild  N     ] dev-qt/qtdbus-5.4.2:5::gentoo  USE="-debug {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtdbus-5.6.0_beta:5/5.6::qt  USE="-debug {-test}" 0 KiB
    [ebuild  N     ] dev-qt/qtgui-5.4.2-r1:5::gentoo  USE="dbus gif harfbuzz jpeg png udev xcb -accessibility -debug -egl -eglfs -evdev -gles2 -gtkstyle -ibus {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtgui-5.6.0_beta:5/5.6::qt  USE="dbus gif jpeg png udev xcb -accessibility -debug -egl -eglfs -evdev -gles2 -gtkstyle -ibus -libinput {-test} -tslib -tuio" 0 KiB
    [ebuild  N     ] media-libs/freeglut-3.0.0::gentoo  USE="-debug -static-libs" ABI_X86="(64) -32 (-x32)" 410 KiB
    [ebuild  N     ] dev-qt/qtwidgets-5.4.2:5::gentoo  USE="png xcb -debug -gles2 -gtkstyle {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtwidgets-5.6.0_beta:5/5.6::qt  USE="png xcb -debug -gles2 -gtkstyle {-test}" 0 KiB
    [ebuild  N     ] media-libs/jasper-1.900.1-r9::gentoo  USE="jpeg opengl -static-libs" ABI_X86="(64) -32 (-x32)" 1,409 KiB
    [ebuild  N     ] media-libs/libwebp-0.4.0:0/5::gentoo  USE="gif jpeg opengl png tiff -experimental -static-libs -swap-16bit-csp" ABI_X86="(64) -32 (-x32)" 868 KiB
    [ebuild  N    ~] dev-qt/qtdeclarative-5.6.0_beta:5/5.6::qt  USE="jit widgets xml -debug -gles2 -localstorage {-test}" 21,749 KiB
    [ebuild  N     ] dev-qt/qtopengl-5.4.2:5::gentoo  USE="-debug -gles2 {-test}" 0 KiB
    [ebuild  N     ] dev-qt/qtx11extras-5.4.2:5::gentoo  USE="-debug {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtimageformats-5.6.0_beta:5/5.6::qt  USE="-debug {-test}" 2,547 KiB
    [ebuild  N     ] dev-qt/qtsvg-5.4.2:5::gentoo  USE="-debug {-test}" 1,752 KiB
    [ebuild  N     ] dev-qt/qtmultimedia-5.4.2-r1:5::gentoo  USE="alsa gstreamer opengl qml widgets -debug -openal -pulseaudio {-test}" 0 KiB
    [ebuild  N     ] dev-qt/qtquickcontrols-5.4.2:5::gentoo  USE="widgets -debug {-test}" 0 KiB
    [ebuild  N    ~] dev-qt/qtpositioning-5.6.0_beta:5/5.6::qt  USE="qml -debug -geoclue {-test}" 3,426 KiB
    [ebuild  N     ] dev-qt/qtquick1-5.4.2:5::gentoo  USE="opengl xml -debug -designer {-test} -webkit" 15,647 KiB
    [ebuild  N     ] dev-qt/qtwebkit-5.4.2:5::gentoo  USE="opengl qml udev -debug -gstreamer -gstreamer010 -multimedia -printsupport {-test} -webp" 32,677 KiB
    [ebuild  N    ~] dev-qt/qtwebsockets-5.6.0_beta:5/5.6::qt  USE="qml -debug {-test}" 201 KiB
    [ebuild  N     ] dev-qt/qtgraphicaleffects-5.4.2:5::gentoo  USE="-debug {-test}" 13,989 KiB
    [ebuild  N    ~] dev-qt/qtsensors-5.6.0_beta:5/5.6::qt  USE="qml -debug {-test}" 2,151 KiB
    [ebuild  N     ] dev-qt/qtprintsupport-5.4.2:5::gentoo  USE="cups -debug -gles2 {-test}" 0 KiB

    Total: 50 packages (2 upgrades, 48 new), Size of downloads: 188,219 KiB

    !!! Multiple package instances within a single package slot have been pulled
    !!! into the dependency graph, resulting in a slot conflict:

    dev-qt/qtcore:5

      (dev-qt/qtcore-5.4.2:5/5::gentoo, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtcore-5.4.2 required by (dev-qt/qtconcurrent-5.4.2:5/5::gentoo, ebuild scheduled for merge)
        ^              ^^^^^                                                                                                                                       
        (and 8 more with the same problem)

      (dev-qt/qtcore-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtcore-5.6.0_beta required by (dev-qt/qtwebsockets-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge)
        ^              ^^^^^^^^^^                                                                                                                                          
        (and 11 more with the same problem)

    dev-qt/qtwidgets:5

      (dev-qt/qtwidgets-5.4.2:5/5::gentoo, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtwidgets-5.4.2[gles2=] required by (dev-qt/qtprintsupport-5.4.2:5/5::gentoo, ebuild scheduled for merge)
        ^                 ^^^^^                                                                                                                                                 
        (and 2 more with the same problem)

      (dev-qt/qtwidgets-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtwidgets-5.6.0_beta[gles2=] required by (dev-qt/qtdeclarative-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge)
        ^                 ^^^^^^^^^^                                                                                                                                                   

    dev-qt/qtgui:5

      (dev-qt/qtgui-5.4.2-r1:5/5::gentoo, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtgui-5.4.2[gles2=,gtkstyle=,png=,xcb?] required by (dev-qt/qtwidgets-5.4.2:5/5::gentoo, ebuild scheduled for merge)
        ^             ^^^^^                                                                                                                                                                
        (and 3 more with the same problem)

      (dev-qt/qtgui-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtgui-5.6.0_beta[gles2=,gtkstyle=,png=,xcb?] required by (dev-qt/qtwidgets-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge)
        ^             ^^^^^^^^^^                                                                                                                                                                   
        (and 2 more with the same problem)

    dev-qt/qtdbus:5

      (dev-qt/qtdbus-5.4.2:5/5::gentoo, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtdbus-5.4.2 required by (dev-qt/qtgui-5.4.2-r1:5/5::gentoo, ebuild scheduled for merge)
        ^              ^^^^^                                                                                                                                   

      (dev-qt/qtdbus-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge) pulled in by
        ~dev-qt/qtdbus-5.6.0_beta required by (dev-qt/qtgui-5.6.0_beta:5/5.6::qt, ebuild scheduled for merge)
        ^              ^^^^^^^^^^                                                                                                                                   

    NOTE: Use the '--verbose-conflicts' option to display parents omitted above

    It may be possible to solve this problem by using package.mask to
    prevent one of those packages from being selected. However, it is also
    possible that conflicting dependencies exist such that they are
    impossible to satisfy simultaneously.  If such a conflict exists in
    the dependencies of two different packages, then those packages can
    not be installed simultaneously. You may want to try a larger value of
    the --backtrack option, such as --backtrack=30, in order to see if
    that will solve this conflict automatically.

    For more information, see MASKED PACKAGES section in the emerge man
    page or refer to the Gentoo Handbook.


    The following keyword changes are necessary to proceed:
     (see "package.accept_keywords" in the portage(5) man page for more details)
    # required by @qt5-addons (argument)
    =dev-qt/qtpositioning-5.6.0_beta ~amd64
    # required by x11-libs/pango-1.36.8-r1::gentoo
    # required by x11-libs/gtk+-2.24.28-r1::gentoo
    # required by gnome-base/libglade-2.6.4-r2::gentoo
    # required by dev-python/pygtk-2.24.0-r4::gentoo
    # required by dev-vcs/git-2.4.10::gentoo[python,gtk]
    # required by @selected
    # required by @world (argument)
    =media-libs/harfbuzz-1.1.3 ~amd64
    # required by dev-qt/qtdeclarative-5.6.0_beta::qt
    # required by dev-qt/qtquickcontrols-5.4.2::gentoo
    # required by @qt5-essentials (argument)
    =dev-qt/qtgui-5.6.0_beta ~amd64
    # required by dev-qt/qtpositioning-5.6.0_beta::qt[qml]
    # required by @qt5-addons (argument)
    =dev-qt/qtnetwork-5.6.0_beta ~amd64
    # required by dev-qt/qtdeclarative-5.6.0_beta::qt[-test]
    # required by dev-qt/qtquickcontrols-5.4.2::gentoo
    # required by @qt5-essentials (argument)
    =dev-qt/qttest-5.6.0_beta ~amd64
    # required by @qt5-addons (argument)
    =dev-qt/qtwebsockets-5.6.0_beta ~amd64
    # required by @qt5-addons (argument)
    =dev-qt/qtsensors-5.6.0_beta ~amd64
    # required by app-text/ghostscript-gpl-9.15-r1::gentoo
    # required by net-print/cups-filters-1.5.0::gentoo
    # required by net-print/cups-2.0.3::gentoo
    # required by dev-qt/qtprintsupport-5.4.2::gentoo[cups]
    # required by @qt5-addons (argument)
    =media-libs/freetype-2.6.2 ~amd64
    # required by @qt5-addons (argument)
    =dev-qt/qtimageformats-5.6.0_beta ~amd64
    # required by dev-qt/qtgui-5.6.0_beta::qt[dbus]
    # required by dev-qt/qtdeclarative-5.6.0_beta::qt
    # required by dev-qt/qtquickcontrols-5.4.2::gentoo
    # required by @qt5-essentials (argument)
    =dev-qt/qtdbus-5.6.0_beta ~amd64
    # required by @qt5-addons (argument)
    =dev-qt/qtserialport-5.6.0_beta ~amd64
    # required by dev-qt/qtdeclarative-5.6.0_beta::qt[xml]
    # required by dev-qt/qtquickcontrols-5.4.2::gentoo
    # required by @qt5-essentials (argument)
    =dev-qt/qtxmlpatterns-5.6.0_beta ~amd64
    # required by dev-qt/qtdeclarative-5.6.0_beta::qt[widgets]
    # required by dev-qt/qtquickcontrols-5.4.2::gentoo
    # required by @qt5-essentials (argument)
    =dev-qt/qtwidgets-5.6.0_beta ~amd64
    # required by dev-qt/qtsensors-5.6.0_beta::qt
    # required by @qt5-addons (argument)
    =dev-qt/qtcore-5.6.0_beta ~amd64
    # required by dev-qt/qtwebsockets-5.6.0_beta::qt[qml]
    # required by @qt5-addons (argument)
    =dev-qt/qtdeclarative-5.6.0_beta ~amd64


`~`的软件表示不完全测试通过。
查看后发现Qt5的开发组已经开发了一个Qt5.6.0的beta版本 查看/var/lib/layman/qt/sets/qt5-addons 

    # set for Qt5 Add-on modules
    # http://doc.qt.io/qt-5/qtmodules.html

    #dev-qt/qt3d:5
    dev-qt/qtconcurrent:5
    dev-qt/qtdbus:5
    dev-qt/qtgraphicaleffects:5
    dev-qt/qtimageformats:5
    #dev-qt/qtlocation:5
    dev-qt/qtopengl:5
    dev-qt/qtpositioning:5
    dev-qt/qtprintsupport:5
    dev-qt/qtquick1:5
    dev-qt/qtscript:5
    dev-qt/qtsensors:5
    dev-qt/qtserialport:5
    dev-qt/qtsvg:5
    dev-qt/qtwayland:5
    dev-qt/qtwebkit:5
    dev-qt/qtwebsockets:5
    dev-qt/qtx11extras:5
    dev-qt/qtxml:5
    dev-qt/qtxmlpatterns:5


各个软甲只指定了大版本 如果有更新的版本则会安装最新的版本， 我们要手动指定安装Qt 5.5.1。

在Gentoo中指定安装特定版本的软件需要在/etc/portage/package.keywords中添加相应项


    vim /etc/portage/package.keywords/qt5


    =dev-qt/assistant-5.5.1 
    =dev-qt/designer-5.5.1-r1 
    =dev-qt/linguist-5.5.1-r1 
    =dev-qt/linguist-tools-5.5.1-r1 
    =dev-qt/pixeltool-5.5.1-r1 
    =dev-qt/qdbus-5.5.1-r1 
    =dev-qt/qdbusviewer-5.5.1-r1 
    =dev-qt/qdoc-5.5.1 
    =dev-qt/qt-docs-5.5.1_p0 
    =dev-qt/qtbluetooth-5.5.1-r1 
    =dev-qt/qtconcurrent-5.5.1-r1 
    =dev-qt/qtcore-5.5.1 
    =dev-qt/qtdbus-5.5.1 
    =dev-qt/qtdeclarative-5.5.1-r1 
    =dev-qt/qtdiag-5.5.1-r1 
    =dev-qt/qtgraphicaleffects-5.5.1-r1 
    =dev-qt/qtgui-5.5.1 
    =dev-qt/qthelp-5.5.1-r1 
    =dev-qt/qtimageformats-5.5.1-r1 
    =dev-qt/qtmultimedia-5.5.1-r1 
    =dev-qt/qtnetwork-5.5.1 
    =dev-qt/qtopengl-5.5.1 
    =dev-qt/qtpaths-5.5.1-r1 
    =dev-qt/qtpositioning-5.5.1-r1 
    =dev-qt/qtprintsupport-5.5.1 
    =dev-qt/qtquick1-5.5.1-r1 
    =dev-qt/qtquickcontrols-5.5.1-r1 
    =dev-qt/qtscript-5.5.1-r1 
    =dev-qt/qtsensors-5.5.1-r1 
    =dev-qt/qtserialport-5.5.1-r1 
    =dev-qt/qtsql-5.5.1 
    =dev-qt/qtsvg-5.5.1-r1 
    =dev-qt/qttest-5.5.1 
    =dev-qt/qttranslations-5.5.1-r1 
    =dev-qt/qtwayland-5.5.1-r1 
    =dev-qt/qtwebchannel-5.5.1-r1 
    =dev-qt/qtwebkit-5.5.1-r1 
    =dev-qt/qtwebsockets-5.5.1-r1 
    =dev-qt/qtwidgets-5.5.1-r1 
    =dev-qt/qtx11extras-5.5.1 
    =dev-qt/qtxml-5.5.1 
    =dev-qt/qtxmlpatterns-5.5.1-r1 

其实这个列表获取很简单 `eix dev-qt/*` 就可以获取所有qt的包， 这里我只修改了qt5相关的包。

然后问题解决了 用`emerge -av @q5-essentials @qt-addons` 安装Qt5。

这个keywords是一个个尝试安装Qt5的软件 在自己机器上试出来的不保证以后一直能用，
这里是提供一个排除错误的思路。


