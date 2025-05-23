|命令|说明|
|---|---|
|`npm init`|初始化一个新的 `package.json` 文件，交互式输入信息。|
|`npm init -y`|快速创建带有默认设置的 `package.json` 文件。|
|`npm install package-name`|本地安装指定包。|
|`npm install -g package-name`|全局安装指定包，使其在系统范围内可用。|
|`npm install`|安装 `package.json` 中列出的所有依赖。|
|`npm install package-name --save-dev`|安装包并添加到 `devDependencies`。|
|`npm update package-name`|更新指定的依赖包。|
|`npm uninstall package-name`|卸载指定的依赖包。|
|`npm uninstall -g package-name`|全局卸载指定的包。|
|`npm list`|查看当前项目的已安装依赖包列表。|
|`npm list -g --depth=0`|查看全局已安装的依赖包列表（不展开依赖树）。|
|`npm info package-name`|查看包的详细信息，包括版本和依赖等。|
|`npm login`|登录到 NPM 账号。|
|`npm publish`|发布当前包到 NPM 注册表。|
|`npm unpublish package-name`|从 NPM 注册表中撤销发布的包（一般限 24 小时内）。|
|`npm cache clean --force`|清理 NPM 缓存。|
|`npm audit`|检查项目依赖中的安全漏洞。|
|`npm audit fix`|自动修复已知的漏洞。|
|`npm run script-name`|运行 `package.json` 中定义的脚本，例如 `npm run start`。|
|`npm start`|运行 `start` 脚本（等同于 `npm run start`）。|
|`npm test`|运行 `test` 脚本。|
|`npm build`|运行 `build` 脚本。|
|`npm outdated`|列出项目中有可更新版本的依赖包。|
|`npm version patch/minor/major`|更新 `package.json` 中的版本号，自动更新版本。|
|`npm ci`|使用 `package-lock.json` 快速安装依赖，适用于 CI/CD 环境。|