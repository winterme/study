# vue-cli.md

准备
在使用vue-cli新建项目前。请确保你已经安装了Node.js，vue和vue-cli。

步骤：
cd 目录(你要把项目放在哪个目录)；
vue init webpack pname(你的项目名字)；
? Project description (A Vue.js project) vue-cli新建项目(项目描述)；
? Author (xhdx <zhuming3834@sina.com>) ；zhuming3834@sina.com(项目作者)；
? Vue build
❯ Runtime + Compiler: recommended for most users
Runtime-only: about 6KB lighter min+gzip, but templates (or any Vue-specific HTML) are ONLY allowed in .vue files - render functions are required elsewhere
这里选择Runtime + Compiler: recommended for most users；
? Install vue-router? (Y/n) y 是否使用vue-router，这里根据需求选择；
? Use ESLint to lint your code? (Y/n) y 是否使用ESLint，这里根据需求选择；
? Pick an ESLint preset (Use arrow keys)
❯ Standard (https://github.com/feross/standard)
Airbnb (https://github.com/airbnb/javascript) none (configure it yourself) 这里选择Standard (https://github.com/feross/standard)
? Setup unit tests with Karma + Mocha? (Y/n) n 是否需要单元测试，这里根据需求选择；
Setup e2e tests with Nightwatch? (Y/n) n是否需要单元测试，这里根据需求选择；
cd pname(项目目录)；
npm install 安装依赖；
npm run dev 本地运行项目