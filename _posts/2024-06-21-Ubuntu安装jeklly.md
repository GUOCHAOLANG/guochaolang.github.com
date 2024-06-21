---
title: Ubuntu安装jeklly
author: Guocl
date: 2024-06-21
category: 软件
layout: post
mermaid: true
---

- 使用 rbenv 安装 Ruby 3.3.3
    - **安装依赖项**
      collapsed:: true
        - 首先，确保您的系统拥有构建Ruby所需的依赖项。
        -
        ```
        bash
        
        复制代码
        
        sudo apt update
        sudo apt install -y build-essential libssl-dev libreadline-dev zlib1g-dev
        ```
    - **安装 rbenv 和 ruby-build**
      collapsed:: true
        - 接下来，安装`rbenv`和`ruby-build`插件。`ruby-build`插件提供了安装不同Ruby版本的功能。
        -
        ```
        bash
        
        复制代码
        
        # 克隆 rbenv 仓库到 ~/.rbenv
        git clone https://github.com/rbenv/rbenv.git ~/.rbenv
        
        # 添加 rbenv 到路径
        echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
        echo 'eval "$(rbenv init -)"' >> ~/.bashrc
        
        # 重新加载 shell
        source ~/.bashrc
        
        # 克隆 ruby-build 仓库到 rbenv 插件目录
        git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
        
        # 安装 ruby-build
        ~/.rbenv/plugins/ruby-build/install.sh
        ```
    - **安装 Ruby 3.3.3**
      collapsed:: true
        - 使用`rbenv`安装指定版本的Ruby。
        -
        ```
        bash
        
        复制代码
        
        rbenv install 3.3.3
        ```
    - **设置全局Ruby版本**
      collapsed:: true
        - 您可以将安装的Ruby版本设置为系统的全局默认版本。
        -
        ```
        bash
        
        复制代码
        
        rbenv global 3.3.3
        ```
    - **验证安装**
      collapsed:: true
        - 验证Ruby安装是否成功。
        -
        ```
        bash
        
        复制代码
        
        ruby -v
        # 输出: ruby 3.3.3pXXX (根据具体的版本信息)
        ```