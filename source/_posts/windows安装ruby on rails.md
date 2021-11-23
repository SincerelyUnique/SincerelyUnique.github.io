---
title: windows10安装使用ruby
date: 2021-11-22 14:34:43
tags: ruby, ruby on rails
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

安装使用ruby

OS： windows10

1.下载Rails Installer
https://rubyinstaller.org/

2.配置系统环境变量
$ ruby -v

3.安装rails
$ gem install rails
$ rails --version

4.创建blog应用
$ rails new blog
$ cd blog

5.安装yarn
$ npm install --global yarn

6.Since Rails 6, Webpacker is the default JavaScript compiler
$ rails webpacker:install
$ yarn install --check-files
$ rails s

7.访问
http://localhost:3000/



8.参考
https://ruby-china.github.io/rails-guides/getting_started.html
https://runebook.dev/zh-CN/docs/rails/guides/getting_started
https://stackoverflow.com/questions/57891751/webpacker-configuration-file-not-found-rails-6-0-0
https://progressbar.tw/posts/122
