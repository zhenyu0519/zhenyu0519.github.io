---
layout: post
title: MAC Mojave下，gem安装package(jekyll)出现的问题以及解决方法/Trouble shooting gem install packages (jekyll) under Mac Mojave(中文/英语)
categories: TroubleShoot
description: MAC Mojave下，gem安装jekyll出现的问题以及解决方法
keywords: MAC Mojave, jekyll, Github Pages, Failed to build gem native extension, Gem, Permission
---

### 今天踩坑了，用gem安装jekyll的时候各种报错，网上的一些解决方法，要不就是行不通，要不就是零散破碎不完整，花了点时间把这个问题搞定，整理完之后顺便分享一下，帮助大家节省一点时间。

### When using gem to install or update some packages, there may be some wired problems and messages pop-up. If you met same problems like me, here are the reason and solution.
---

## 起因(Start)

构建Github Pages免不了要和jekyll打交道，至于它是什么，[看这里](https://jekyllrb.com/)。网上有个不错的教程，可惜仅限中文，跟着教程走，在安装jekyll的时候出了问题。偏偏又是我，好吧已经习惯了。先看看是什么问题：

When I try to build up my github pages, I need to install jekyll, (what is it? [click here](https://jekyllrb.com/)) and when I install jekyll, the below message pop-up. Apparentlly, something wrong!

```
sudo gem install jekyll

Building native extensions. This could take a while...
ERROR:  Error installing jekyll:
	ERROR: Failed to build gem native extension.

current directory: /Library/Ruby/Gems/2.3.0/gems/http_parser.rb-0.6.0/ext/ruby_http_parser
/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/bin/ruby -I /Library/Ruby/Site/2.3.0 -r ./siteconf20191121-70645-4kcf1p.rb extconf.rb
mkmf.rb can't find header files for ruby at /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/include/ruby.h

extconf failed, exit code 1

Gem files will remain installed in /Library/Ruby/Gems/2.3.0/gems/http_parser.rb-0.6.0 for inspection.
Results logged to /Library/Ruby/Gems/2.3.0/extensions/universal-darwin-18/2.3.0/http_parser.rb-0.6.0/gem_make.out

```
上面说了一长串其实重点就是can't find header files for ruby。网上建议安装xcode command line tool:

The point is acctually 'can't find header files for ruby'. Someone suggest to install xcode command line tool:
```
sudo xcode-select --install
sudo xcodebuild -license accept
```
安装完之后，然并卵（虽然上面的没有解决这个问题还是建议大家安装一下xcode command line tool）。做了些search后发现，找不到header file的根本原因是header file的地址变了。重新分配一下地址可能会起到作用。

Thank you and it does not work! (xcode command line tool is necessary so please install anyway). After some searches, I found the true reason is header file location changed. re-assign the location may work.

```
On Xcode 11 / macOS Catalina, the header files are no longer in the old location:
/Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg
Instead, the headers are now installed to the /usr/include directory of the current SDK path:
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include
```
这个方法我没有试过，嫌麻烦，考虑到变更地址，重新配置，我决定直接安装新版ruby 2.6 因为:

However, I will not do that, change exsiting location may make trouble easily. So I decide to install new version of ruby because:

```
Xcode 11 ships the macOS 10.15 SDK which includes headers for ruby2.6 but not for macOS 10.14's ruby2.3.
```
就是说 **Xcode 11 里面的SDK包含了ruby 2.6的头文件但是不包含ruby 2.3的头文件**。那好办，那就装ruby 2.6好了，之所以选择这样做还有其他原因。使用系统自带ruby 2.3的gem安装或者更新package的时候经常会出现权限问题，例如：

**Xcode 11 SDK include header files of ruby 2.6 but not for ruby 2.3**. Another reason is when you use gem to update or install packages, there are sometimes permission problems happend due to ruby 2.3 which comes with system, for example:

```
gem update --system

Updating rubygems-update
Fetching rubygems-update-3.0.6.gem

ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.3.0 directory.
```
又或者
or

```
gem install bundler

Fetching bundler-2.0.2.gem
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.3.0 directory.
```
原因即字面意思, 就是 gem 要往某个神奇的目录写文件但是你的权限不够。 因为你使用的是Apple家自带的ruby, 在尝试往 Apple自家的库中塞东西。所以就算你用sudo也不行，网上有人提供一些方法帮助做强制修改的，但是**个人不推荐**，因为更新系统的时候很容易出现兼容性问题。

The reason is Apple does not allow you to change its own libraries even using sudo. Well there are some articles will help you force to change but it is not recommand because when you upgrade system, there may be compatiable problems happend. So, **don't do that!**

## 划重点(Solutions)

好了说这么多就直接说一个合理可行的方案：
* 额外安装一个新的ruby版本（默认最新就行）这样也可以隔离开系统自带的ruby版本，以免对原版本造成破坏，进而触发依赖或者兼容性问题 ```brew install ruby```
* 然后需要把新版本ruby的路径添加的路径文件里面，方便命令行操作。打开bash_profile ``` nano ~/.bash_profile```
* 添加路径到bash_profile里面 ```export PATH="/usr/local/lib/ruby/gems/2.6.0/bin:$PATH"```
* 添加完成后需要reload一下 ```. ~/.bash_profile```
* 确认是否产生变化 ```echo $PATH```，结果显示 ```/usr/local/lib/ruby/gems/2.6.0/bin```
* 如果用的terminal终端是zsh不是bash那么你需要 ```echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc```
* 接下来需要让编译器找到ruby ```export LDFLAGS="-L/usr/local/opt/ruby/lib"```,  ```export CPPFLAGS="-I/usr/local/opt/ruby/include"```
* 确认ruby路径,重启一下终端 ```which ruby```,结果显示 ```/usr/local/opt/ruby/bin/ruby```
* 确认ruby版本，```ruby -v```,结果显示 ```ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-darwin18]```

Here is the solution：
* install new version ruby（defaule to be latest）it will seperate system ruby version to avoid unexpected problem ```brew install ruby```
* then add path. open bash_profile ``` nano ~/.bash_profile```
* add path to bash_profile ```export PATH="/usr/local/lib/ruby/gems/2.6.0/bin:$PATH"```
* reload after adding ```. ~/.bash_profile```
* confirm effection ```echo $PATH```，display ```/usr/local/lib/ruby/gems/2.6.0/bin```
* if you use zsh not bash you need ```echo 'export PATH="/usr/local/opt/ruby/bin:$PATH"' >> ~/.zshrc```
* let compliers find ruby ```export LDFLAGS="-L/usr/local/opt/ruby/lib"```,  ```export CPPFLAGS="-I/usr/local/opt/ruby/include"```
* relaunch the terminal and confirm ruby path ```which ruby```,display ```/usr/local/opt/ruby/bin/ruby```
* confirm ruby version，```ruby -v```, display ```ruby 2.6.5p114 (2019-10-01 revision 67812) [x86_64-darwin18]```

接下来可以愉快的安装jekyll啦：
done!

```
sudo gem install jekyll

Fetching jekyll-sass-converter-2.0.1.gem
Fetching jekyll-4.0.0.gem
Fetching kramdown-2.1.0.gem
Fetching kramdown-parser-gfm-1.1.0.gem
Fetching sassc-2.2.1.gem
Building native extensions. This could take a while...
Successfully installed sassc-2.2.1
Successfully installed jekyll-sass-converter-2.0.1
Successfully installed kramdown-2.1.0
Successfully installed kramdown-parser-gfm-1.1.0
```

希望对你们有点帮助！
I hope it helps!


