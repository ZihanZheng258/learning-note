### 依赖管理

1. dependencies：存储项目运行所需的依赖。
``` json
"dependencies": {
  "express": "^4.17.1"
}
```
安装依赖时使用：
> npm install express

2. devDependencies：存储项目开发期间需要的依赖。
```json
"devDependencies": {
  "nodemon": "^2.0.20"
}
```
安装依赖时使用：
> npm install nodemon --save-dev


### scripts 字段

scripts 字段用于定义可通过 npm run \<script> 执行的命令。常见的脚本包括：

``` json
"scripts": {
  "start": "node app.js",
  "test": "jest",
  "build": "webpack --mode production",
  "dev": "nodemon app.js"
}
```

运行脚本示例：

> npm run dev   # 执行 "nodemon app.js"
> npm start     # 等同于 "node app.js"，可以直接用 npm start 运行

### 版本号中的符号说明

- **`^`**（插入符）：表示安装与当前主版本兼容的最新版本。例如，`^4.17.1` 会安装 `4.x.x` 中的最新版本。
- **`~`**（波浪号）：表示安装与当前次版本兼容的最新版本。例如，`~4.17.1` 会安装 `4.17.x` 中的最新版本。

### 常用命令

1. 初始化 package.json 文件：

>    npm init

2. 使用 npm init -y 可以快速生成默认的 package.json 文件。

3. 查看项目依赖：

> npm list --depth=0

4. 更新依赖：

> npm update package-name

### 注意事项

- 保持 `package.json` 文件的简洁和准确，避免不必要的字段。
- 使用 `package-lock.json` 来锁定依赖的版本，以确保项目在不同环境中的一致性。
- 定期更新依赖，以利用最新的功能和安全修复。