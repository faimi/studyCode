#

`yarn add 包名称`

#

删除 node_modules 文件
`rm -rf node_modules/`

#

版本锁定

pnpm 可以直接替代 npm，但是不能替代 yarn。如果项目本来是基于 yarn 的，使用 pnpm 可能会 install 失败，提示缺少各种包。即使手动把 yarn 需要的包补全了，如果 install 命令内包含了 yarn build，则也可能会失败。

#

同样都是`` isModuleDeclaration` has been deprecated, please migrate to `isImportOrExportDeclaration ``但 pnpm 报错，yarn 运行
