---
title:  "Windows安装Jekyll以及若干问题的解决办法"
date:   2013-07-26 09:17:59
tags:
  - Jekyll
  - Windows
author:
  nick: LV主唱大人
  github_name: mamboer

---

Jekyll 官方网站是以外链的形式给出的 [Windows 下安装 Jekyll 方法](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)。这个方法总体上是可行的，但是这篇文章写于 2011 年，并且没有考虑中文环境下的问题，所以安装过程中会碰到种种问题。

---

一，软件版本问题。建议在 Windwos 7/8 64位中下载安装以下版本 Ruby 和 DevKit：

* [Python 2.7.5 Windows X86-64 Installer](http://www.python.org/ftp/python/2.7.5/python-2.7.5.amd64.msi)
* [Ruby 2.0.0-p247 (x64)](http://dl.bintray.com/oneclick/rubyinstaller/rubyinstaller-2.0.0-p247-x64.exe?direct)
* [DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe](http://files.rubyforge.vm.bytemark.co.uk/rubyinstaller/DevKit-mingw64-64-4.7.2-20130224-1432-sfx.exe)

首先安装Python，设置Python.exe所在的目录到环境变量Path中;

接着安装Ruby（默认：C:\Ruby200-x64），然后解压Devkit到_install目录（C:\Ruby200-x64\_install\DevKit），在命令行工具中运行:

``` bash
cd C:\Ruby200-x64\_install\DevKit
ruby dk.rb init
ruby dk.rb install
```

安装完Ruby和Devkit后就可以安装jekyll了:

```bash
gem install jekyll
```

注：如果提示无法下载数据，请给Ruby.exe设置代理。

---

二，进入网站所在的目录，如：`E:\GitHub\oxox.github.io` ，然后运行：`jekyll serve` 即可。

运行 `jekyll serve` 时显示

	Liquid Exception: No such file or directory - /bin/sh in ...

这个是语法高亮插件 Pygments 引起。解决方法是卸载最新版本的 Pygments （0.5.1+），重新安装 0.5.0 版本的 Pygments:

``` bash
gem uninstall pygments.rb --version "=0.5.2"
gem install pygments.rb --version "=0.5.0"
```

这里注意，pygments.rb的版本可能已经不是0.5.2了，所以需要先查看pygments版本，简单的方法，可以先在ruby文件夹中搜索pygments.rb 会看到以版本名称命名的文件夹，如pygments.rb-0.5.4，然后再运行

``` bash
gem uninstall pygments.rb --version "=0.5.4"
gem install pygments.rb --version "=0.5.0"
```

即可解决。

---

三，和第二个问题类似，提示

	Liquid Exception: No such file or directory - python ...

原因是没将 Python 的路径添加为 PATH 环境变量。

---

四，运行 `jekyll serve` 不成功，显示

	 ... invalid byte sequence in GBK  ...

这个是中文编码引起的问题。一旦 post 中有中文字符就会出现这样的问题。当然这个是 Windows 下特有的问题。解决方法是将 `C:\Ruby200-x64\lib\ruby\gems\2.0.0\gems\jekyll-1.1.2\lib\jekyll\convertible.rb` 文件 （路径可能与此不同）中的

``` ruby
self.content = File.read(File.join(base, name))
```

改为

``` ruby
self.content = File.read(File.join(base, name), :encoding => "utf-8")
```

hugo edit：

以上方法不适应新版jekyll-1.3.0了，这句代码已经被修改。最简单的方法是直接在`cmd`里输入

``` ruby
chcp 65001
```

即可解决问题。
	...参考：[https://github.com/twbs/bootstrap](https://github.com/twbs/bootstrap)
Windows users: run chcp 65001 first to change the command prompt's character encoding (code page) to UTF-8 so Jekyll runs without errors. ...

五，如果你的_config.yml中配置了rdiscount做为markdown的解析器，别忘了安装rdiscount

``` bash
gem install rdiscount
```

六，至此，jekyll的安装已经全部完成了，应该能正常运行。如果cmd那显示 `Server address：http://0.0.0.0:4000` 请不必理会，在浏览器中输入`http://localhost:4000` 即可访问。

## References

* <http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html>
* <http://stackoverflow.com/questions/17364028/jekyll-on-windows-pygments-not-working>
* <http://chxt6896.github.io/blog/2011/12/01/blog-pygments.html>
* <http://sstude.com/blog/2013/02/12/how-to-install-jekyll-on-windows/>
