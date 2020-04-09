## 开发

- 本地开发

```sh
git clone https://github.com/zqlu/notes.git
cd notes
npm install hexo-cli -g
yarn
yarn run server
```

- 写作

`hexo new post <title>`

- 部署

直接提交，部署操作由[deploy action](./.github/workflows/deploy.yml)执行

## 升级 Hexo

直接全新 hexo init，然后把 source 迁移过去
