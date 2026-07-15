# ROADMAP

## 当前阶段

依赖维护与构建稳定性验证。

## 已完成

- 将已弃用的 `hexo-renderer-jade` 替换为 `hexo-renderer-pug@3.0.0`。
- 将 `hexo-renderer-stylus` 升级至 `3.0.1`，移除旧版 Stylus 引入的 `urix`、`source-map-url`、`source-map-resolve` 和 `resolve-url`。
- 使用官方 npm Registry 重建 `package-lock.json`，移除失效的淘宝与 NLark 镜像地址。
- 完成干净依赖安装和 Hexo 构建验证。

## 进行中

无。

## 待办

- 单独评估其余上游弃用警告，避免为消除提示而强制覆盖间接依赖版本。
- 单独审查 `npm audit` 报告的 9 个漏洞，再决定兼容性升级方案。

## 阻塞

无。

## 最近验证

- 2026-07-15：`npm ci --dry-run --ignore-scripts --offline` 成功。
- 2026-07-15：`npm run build` 成功，无插件加载或资源渲染错误，生成 34 个页面文件。
- 2026-07-15：锁文件版本为 3，没有空版本条目，也没有旧镜像地址。
- 2026-07-15：`transformers`、`hexo-renderer-jade`、`jade`、`core-js`、`urix`、`source-map-url`、`source-map-resolve` 和 `resolve-url` 均不在有效依赖树中。
