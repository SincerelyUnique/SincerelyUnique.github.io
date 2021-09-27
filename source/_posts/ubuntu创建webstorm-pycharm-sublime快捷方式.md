---
title: ubuntu创建webstorm-pycharm-sublime快捷方式
date: 2020-06-12 11:10:47
tags:
- ubuntu
categories:
---

```
[Desktop Entry]
Categories=Development;
Comment[zh_CN]=
Comment=
Exec=/home/jalen/Jalen/tools/webstorm/WebStorm-2019.3.1/WebStorm-193.5662.54/bin/webstorm.sh
GenericName[zh_CN]=IDE
GenericName=IDE
Icon=/home/jalen/Jalen/tools/webstorm/WebStorm-2019.3.1/WebStorm-193.5662.54/bin/webstorm.png
MimeType=
Name[zh_CN]=WebStorm
Name=WebStorm
Path=
StartupNotify=true
Terminal=false
Type=Application
X-DBUS-ServiceName=
X-DBUS-StartupType=
X-KDE-SubstituteUID=false
X-KDE-Username=owen
 
 
 
[Desktop Entry]
Type=Application
Name=Pycharm
GenericName=Pycharm3
Comment=Pycharm3:The Python IDE
Exec="/home/jalen/Jalen/tools/pycharm/pycharm-professional-2019.3.1/pycharm-2019.3.1/bin/pycharm.sh" %f
Icon=/home/jalen/Jalen/tools/pycharm/pycharm-professional-2019.3.1/pycharm-2019.3.1/bin/pycharm.png
Terminal=pycharm
Categories=Pycharm;
Terminal = false Startup
 
 
 
[Desktop Entry]
Version=1.0
Type=Application
Name=Sublime Text
GenericName=Text Editor
Comment=Sophisticated text editor for code, markup and prose
Exec=/home/jalen/Jalen/tools/sublime/sublime_text_3_build_3211_x64/sublime_text_3/sublime_text %F
Terminal=false
MimeType=text/plain;
Icon=/home/jalen/Jalen/tools/sublime/sublime_text_3_build_3211_x64/sublime_text_3/Icon/48x48/sublime-text.png
Categories=TextEditor;Development;
StartupNotify=true
Actions=Window;Document;
 
[Desktop Action Window]
Name=New Window
Exec=/home/jalen/Jalen/tools/sublime/sublime_text_3_build_3211_x64/sublime_text_3/sublime_text -n
OnlyShowIn=Unity;
 
[Desktop Action Document]
Name=New File
Exec=/home/jalen/Jalen/tools/sublime/sublime_text_3_build_3211_x64/sublime_text_3/sublime_text --command new_file
OnlyShowIn=Unity;
```

```shell script
jalen@jalen-UX410UQK:~$ sudo su
root@jalen-UX410UQK:/home/jalen# cd /usr/share/applications/
root@jalen-UX410UQK:/usr/share/applications# touch webstorm.desktop
root@jalen-UX410UQK:/usr/share/applications# vi webstorm.desktop
# copy webstorm 'desktop entry' to webstorm.desktop (Esc-> :q!)
 
root@jalen-UX410UQK:/usr/share/applications# exit
jalen@jalen-UX410UQK:/usr/share/applications$ sudo chmod ug+x webstorm.desktop 
jalen@jalen-UX410UQK:/usr/share/applications$ cp webstorm.desktop /home/jalen/Desktop/
jalen@jalen-UX410UQK:/usr/share/applications$ sudo chmod ug+x /home/jalen/Desktop/webstorm.desktop
# double click webstorm.desktop from desktop, click Trust and launch
```
