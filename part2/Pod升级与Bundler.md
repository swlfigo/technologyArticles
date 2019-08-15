

# Pod 升级



假设已经安装好 `Cocoapod`

## 1. 查看当前版本

```shell
pod --version

1.5.3 //本机安装版本
```



## 2.升级前可以看当前安装的Ruby源

```shell
gem source -l

*** CURRENT SOURCES ***

http://rubygems.org/
https://gems.ruby-china.org/
https://ruby.taobao.org/

//上面已经添加的源
```



## 3. 移除淘宝的Ruby源，添加一个新的源

```shell
// 移除旧的源
gem sources --remove https://ruby.taobao.org/
https://ruby.taobao.org/ removed from sources

// 添加新的源
gem sources -a https://gems.ruby-china.org/
// 因为已经添加过了
source https://gems.ruby-china.org/ already present in the cache

```



## 4.查看新的源是否添加成功



```shell
gem source -l

*** CURRENT SOURCES ***

https://gems.ruby-china.org/
```



## 5. 升级

```shell
sudo gem install cocoapods
```





# Bundler 使用

团队成员拥有不同的 `cocoapods gem` 的安装版本，并且当有人运行 `pod install` 时 , 由于不同版本的pod在执行 `pod install`或者 `pod update`的时候会改变 `.xcodeproj` 的格式 或为 `xml` 或者 `json`。这样就会造成很难解决的冲突问题。其中一个做法就是指定全组人用同一个 `Cocoapod` 版本 。

为了解决这个不便问题，可以使用 Bundler 来管理 `Cocoapod`. 这样就可以对别人的电脑依赖环境进行**"强制更新"**来达到所有团队成员一致的开发环境



```shell
gem install bundler
```



如果出现问题如:

```shell
'find_spec_for_exe': can't find gem bundler (>= 0.a) with executable bundle (Gem::GemNotFoundException)
```



需要升级系统的 gem , (10.14需要升级到 3.0 或以上)

```shell
gem update --system '3.0'
```



安装成功完，在项目文件夹中创建 `Gemfile` ，与 `.xcworkspace` 同级

```shell
source "https://rubygems.org"

gem 'cocoapods'
//可指定版本,如 1.5.3 版本:
gem 'cocoapod','1.5.3'

```



设置完之后，团队每个成员确保执行这句命令，生成依赖

```shell
bundle install
```



那么以后使用 pod 时候，需要用到以下命令

```shell
bundle exec pod install
```



`bundle` 可以理解为一个虚拟环境容器，执行后就会根据 `Gemfile` 指定的 `Cocoapod` 版本进行项目管理了



