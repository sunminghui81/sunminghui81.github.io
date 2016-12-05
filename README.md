# 说明

本博客的设计来源于[codepiano](https://github.com/codepiano/codepiano.github.com)提供的模版，多谢提供源代码！

# 个人博客

[mathsyouth](https://mathsyouth.github.io)


## 使用的工具

1. 图标库         [Font-Awesome](http://fortawesome.github.io/Font-Awesome)

1. 静态页面服务   [Github pages](http://pages.github.com)

1. 博客生成工具   [Jekyll](https://github.com/mojombo/jekyll)

1. 博客生成工具   [Jekyll-Bootstrap](http://jekyllbootstrap.com/)

1. 前端工具库     [JQuery](http://jquery.com/)

1. 表格控件       [DataTables](http://www.datatables.net/)

1. 前端框架       [Twitter Bootstrap](http://twitter.github.io/bootstrap)

1. 前端排版样式表 [Typo.css](http://typo.sofish.de)

1. 开发工具       [Vim](http://www.vim.org/)

## 使用方法

### 通过clean分支（建议）

提供了一个clean分支，直接检出clean分支修改，然后merge回master分支即可

1. git checkout master
1. git merge clean

所有的个性化变量都被替换为字符串"site"

建议使用该种方式，这样不用再清理文件，修改设置后可以直接使用

### 通过fork

1. fork我的博客，修改fork后的项目名称为：你的username.github.io
1. 执行如下命令以创建一个github仓库的克隆版本：git clone <你的仓库地址>
1. 参见清理文件并修改配置


### 清理文件并修改配置

清理文件步骤

1. 删除\_post目录下所有文件
1. 删除pages目录
1. 删除CNAME文件，如果你需要自定义域名，可以修改CNAME文件
1. 修改config.yml中的设置，需要自定义的地方已经加了注释，建议把那个文件看一遍，对设置有个大概的了解
1. 我使用我的id作为了一些设置的属性名、文件名、目录名，如果你想修改，最好使用替换工具，把所有文件中的"mathsyouth"，替换为你想要的名字 然后重命名以我的id为名的所有文件和目录
1. config.yml中的comments和analytics必须修改，配置上你自己的账号，如果不想配置，请置provider为false
1. 出现问题，建议自己google，有很多详细的教程，或者直接参考官方文档 [jekyll](http://jekyllrb.com)
1. 有好的建议或者要求，欢迎提issue或者发邮件交流
