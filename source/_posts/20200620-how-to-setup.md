---
title: 使用 Hexo + NexT + Valine 搭建 github 博客
date: 2020-06-20 23:51:20
tags:
- Hexo
- NexT
- Valine
- LeanCloud
categories:
- [关于本站, 搭建过程]
---

## 安装 [Hexo](https://hexo.io/docs/index.html)

#### 前提
- 安装 git
- 安装 nodejs

#### 安装 Hexo
```
$ npm install -g hexo-cli
$ npm install hexo
```

## 创建 project

#### 创建博客项目
```bash
$ hexo init blog
$ cd blog
$ npm install
```

#### 启动
```bash
$ hexo g  # hexo generate
$ hexo s  # hexo server
```
启动之后通过 http://localhost:4000/ 访问

## 创建 post
- 首先，_config.yml 里定义 post 的文件命名格式
```yml
new_post_name: :year:month:day-:title.md 
```

- 创建一篇 title 为 'mypost' 的 post。这会在 source/_posts 下生成一个按自定义命名格式生成的文件，如: 20200618-mypost.md
```bash
$ hexo new post mypost
```

文件头部自带 Front-matter。title 默认为 'mypost'，可编辑替换成中文
```
---
title: 我的博文
date: 2020-06-18 15:20:51
tags:
categories: 
---
```


## [集成 github](https://hexo.io/docs/one-command-deployment) 

1. github 上创建名称为 gavfu.github.io 的 repository，并添加 README.md 文件

2. 安装 hexo-deploy-git
```bash
$ npm install hexo-deployer-git --save
```

3. 编辑 _.config.yml
```yml
deploy:
  type: git
  repo: https://github.com/gavfu/gavfu.github.io.git
  branch: master
```

4. 部署
```bash
$ hexo clean && hexo deploy
```

## 选择安装 [NexT](http://theme-next.iissnan.com/getting-started.html) 主题
``` bash
$ cd blog
$ git clone https://github.com/iissnan/hexo-theme-next themes/next

或者，作为一个子模块添加进来
$ cd blog/themes
$ git submodule add https://github.com/iissnan/hexo-theme-next
```
编辑 _.config.yml，切换到 next 主题，并设置 language
```yml
theme: next

language: zh-Hans
```

编辑 themes\next\\_.config.yml，选择 Mist scheme
```yml
scheme: Mist
```

### 设置 next 菜单

- 编辑 themes\next\\_.config.yml，设置显示的菜单及 font awesome 图标
```yml
menu:
  home: /|| home
  about: /about|| user
  tags: /tags|| tags
  categories: /categories|| th
  archives: /archives|| archive
```

- 编辑 themes\next\languages\zh-Hans.yml，设置中文菜单名
```yml
menu:
  home: 首页
  archives: 归档
  categories: 分类
  tags: 标签
  about: 关于
```

- 编辑 themes\next\\_.config.yml，打开菜单对应的 font awesome 图标
```yml
menu_icons:
  enable: true
```

- 生成菜单文件夹 (不然点击菜单会出现 404)

  - 生成 tags 标签页
  
    ```bash
    $ hexo new page tags
    ```
  - 编辑生成的 source/tags/index.md
    ```yml
    ---
    date: 2020-06-18 15:43:10
    type: "tags"
    comments: false
    ---
    ```
   
   - 生成 categories 分类页
    ```bash
    $ hexo new page categories
    ```
   - 编辑生成的 source/categories/index.md
   ```yml
    ---
    date: 2020-06-18 15:38:13
    type: "categories"
    comments: false
    ---
   ```
   
   - 生成 about 页面
    ```bash
    $ hexo new page about
    ```
   - 编辑生成的 source/about/index.md
   ```yml
    ---
    title: 关于我
    date: 2020-06-18 15:39:20
    type: "about"
    comments: false
    ---
    
    缠非缠，禅非禅，枯木龙吟照大千
   ```

### 设置社交链接
- 编辑 themes\next\\_.config.yml
  ```yml
  social:
    github: https://github.com/gavfu || github
    email: mailto:gavfu@outlook.com || envelope
  ```

### 开启打赏功能
- 编辑 themes\next\\_.config.yml
  ```yml
  reward_comment: 请作者喝杯咖啡
    wechatpay: /images/wechat-reward-image.png
    alipay: /images/alipay-reward-image.jpeg
  ```


## 绑定域名
- 添加 CNAME 解析

  以阿里云万网域名为例，添加一个 CNAME 解析，指向 gavfu.github.io

- 添加 CNAME 文件
  在 source 下面新建 CNAME 文件，文件内容为上一步添加的域名

## 备份 source 到 github
#### 思路
- hexo deploy 命令将编译好的博客文件推送到 github 的 master 分支
- 同一个 repository 下新建 source 分支，用来保存 .md 等源文件
- 注意: 作为一个 sub-repository 或 submodule (subtree)，themes\next 相关的配置改动并不会被提交

### 步骤
- github -> [username].github.io，新建 source 分支

- github -> [username].github.io -> settings -> Branches，将 source 设为默认分支

- 本地 project git初始化
  ```bash
  $ cd blog
  $ git init
  $ git remote add origin https://github.com/gavfu/gavfu.github.io.git
  ```

- 编辑 .gitignore
  ```
    .DS_Store
    Thumbs.db
    db.json
    *.log
    node_modules/
    public/
    .deploy*/
  ```

- 提交
  ```bash
  $ rm -rf public # 删除编译生成的博客文件
  $ git add .
  $ git commit -m 'initial checkin'
  $ git push origin master:source  # 将本地 master 分支上的提交推送到远端的source分支。 第一次推送有冲突，可以添加 -f 参数强制推送
  ```

## 集成 [Valine](https://valine.js.org/) 评论系统

- 注册 [LeanCloud](https://www.leancloud.cn/) 帐号，创建应用，并设置

  - 打开应用，点击 “存储” -> “结构化数据”，创建 Comment 和 Counter，分别存储评论和文章阅读量。

  - 打开 “设置” -> “安全中心”，将所有服务都关闭，只保留 “数据存储”；同时在 “Web 安全域名” 填写 “https://gavfu.com”

  - 打开 “应用 Keys”，获取 AppID 和 AppKey

- 在 themes\next\\_.config.yml 配置 Valine

    ```yml
    valine:
      enable: true
      appid:  ### # your leancloud application appid
      appkey:  ### # your leancloud application appkey
      notify: false # mail notifier , https://github.com/xCss/Valine/wiki
      verify: false # Verification code
      placeholder: 我有话说 # comment box placeholder
      avatar: mm # gravatar style
      guest_info: nick,mail,link # custom comment header
      pageSize: 10 # pagination size
      language: zh-CN
      visitor: true
      comment_count: true
    ```
    
    

