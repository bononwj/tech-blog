开始安装flutter

``` bash
cd ~/Library  
mkdir Flutter && cd Flutter  

//github  
git clone -b dev https://github.com/flutter/flutter.git    

//gitee（我同步github的库）  
git clone -b dev https://gitee.com/kikt/flutter.git   

cd flutter

git remote set-url origin https://github.com/flutter/flutter.git  

git remote -v  
```


配置环境

``` bash
export FLUTTER_HOME=/Users/xxxxxx/Library/Flutter/flutter  
export PATH=$JAVA_HOME/bin:$ANDROID_HOME/platform-tools:$FLUTTER_HOME/bin:$PATH  

cd ~  
source .bash_profile  
flutter doctor -v 
```

更新依赖
`flutter pub upgrade`