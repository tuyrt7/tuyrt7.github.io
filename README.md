# tuyrt7.github.io
个人博客  

## 新环境时：

``` 
新建文件夹tuyrt7.github.io
git clone git@github.com:tuyrt7/tuyrt7.github.io.git 
cd tuyrt7.github.io
npm install hexo
npm install hexo-deployer-git -save

// hexo环境配置好后，继续像之前一样
hexo new post_name
... 

确保hexo deploy推送的是master分支，hexo目录下的_config.yml文件通常会配置deploy推送的目标地址，这个一般在最初使用hexo时，就会配置为master，不用改动：

```
