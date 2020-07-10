软件下载 

> https://zh-cn.libreoffice.org/download/libreoffice/

语言包下载

> https://www.libreoffice.org/download/download/?lang=pick

安装：

```
$ wget https://mirror.labkom.id/tdf/libreoffice/stable/6.4.4/deb/x86_64/LibreOffice_6.4.4_Linux_x86-64_deb.tar.gz
$ tar -zxvf LibreOffice_6.4.4_Linux_x86-64_deb.tar.gz
$ cd LibreOffice_6.4.4.2_Linux_x86-64_deb/DEBS/
$ sudo dpkg -i *.deb
```

启动：

```
$ /opt/libreoffice6.4/program/soffice --headless --accept="socket,host=127.0.0.1,port=8100;urp;" --nofirststartwizard &
```



