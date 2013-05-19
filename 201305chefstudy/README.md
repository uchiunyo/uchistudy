CPS chefハンズオン
======================
社内chefハンズオンの作業手順です。以下環境を想定しています。

------
### 事前準備
EC2(t1.micro)を用意して、以下コマンドを実行しgitとRuby 1.9.3をインストールする。

    $ sudo yum install git
    $ curl -L https://get.rvm.io | bash -s stable --ruby=1.9.3 --autolibs=enabled

------
### chefをインストールしてみよう ###

    $ sudo su -
    # gem install chef
    # exit
    
### レポジトリを作ってみよう ###

    $ git clone git://github.com/opscode/chef-repo.git
    $ cd chef-repo
    
### HelloWorldするレシピを作ってみよう ###

    $ knife cookbook create helloworld -o cookbooks
    $ vi cookbooks/helloworld/recipes/default.rb
    $ cat cookbooks/helloworld/recipes/default.rb
      # Cookbook Name:: helloworld
      # Recipe:: default
      #
      # Copyright 2013, YOUR_COMPANY_NAME
      #
      # All rights reserved - Do Not Redistribute
      #
      log "Hello World!!"
    
    
### 実行してみよう

    $ vi ec2.json
    $ cat ec2.json
      {
        "run_list":[
          "recipe[helloworld]"
        ]
      }
    $ vi solo.rb
    $ cat solo.rb
      file_cache_path "/tmp/chef-solo"
      cookbook_path ["/home/ec2-user/chef-repo/cookbooks"]
    $ sudo su
    # chef-solo -c solo.rb -j ec2.json
      Starting Chef Client, version 11.4.4
      Compiling Cookbooks...
      Converging 1 resources
      Recipe: helloworld::default
        * log[Hello World!!] action write
      Chef Client finished, 1 resources updated
      
------
### chefでmysqlを入れてみよう

    $ cd ~
    $ git clone https://github.com/uchiunyo/uchistudy.git
    $ mv uchistudy/201305chefstudy/mysql chef-repo/cookbooks/
    $ cd chef-repo
    $ vi ec2.json
    $ cat ec2.json
      {
        "run_list":[
          "recipe[helloworld]",
          "recipe[mysql]"
        ]
      }
    $ sudo su
    # chef-solo -c solo.rb -j ec2.json
