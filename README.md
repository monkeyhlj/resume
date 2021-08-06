# 动态简历

> 本项目源于：https://github.com/jirengu-inc/animating-resume。向作者表示深深的敬意。

简历样例预览：https://monkeyhlj.github.io/resume/public/
## 使用方法

1. 安装Node.js环境：https://nodejs.org/

2. 安装git: https://git-scm.com/

## 执行以下命令

``` bash
git clone https://github.com/monkeyhlj/resume.git
cd resume
npm install
npm run dev
```

## 部署方法


1. 编辑 `config/index.js`，修改第 10 行的 `assetsPublicPath`，值为 `项目名/public/`。如果你没有修改项目名resume，则可跳过此步骤。

2. 编译、上传
    ``` bash
    npm run build
    git add .
    git commit -m "提交信息"
    git push
    ```

3. 开启 GitHub Pages 功能, 或者，把生成的目标文件```public/*```放在你的web服务器上。

4. 访问地址：https://你的github用户名.github.io/resume/public。

