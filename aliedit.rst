解决安装支付宝插件aliedit仍不能正常登录的问题
=============================================

::

    mkdir ~/.mozilla/plugins
    cd ~/.mozilla/plugins
    bash ...aliedit.sh
    ldd libaliedit32.so|grep 'not'
    sudo ln -s /usr/lib/libpng.so /usr/lib/libpng12.so.0
    sudo mv libaliedit32.so /usr/lib/mozilla/plugins/
