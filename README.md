# vuejs-2.0 翻译计划


## 翻译字典

先参考 [vuejs-1.0 中文文档](http://cn.vuejs.org/)，如有需要再建立一个额外的文档。

## 协作形式

翻译者：

* 请从标记为 `待领取` 的 issues 中领取任务，写清楚预估的 DDL。
* 如果超过预估时间无法完成请在 issue 中 at 协调员，说明接替者信息或请协调员重新分配该任务；
* 完成任务后提交 PR
* 若 PR 被标记为 `待修改`，根据修改一件修改后删除标记，分配给协调员

协调员
* 将无状态的 PR 分配给校对员，标记为待校对
* 若 `待合并` 的 PR 可以合并，则直接合并。合并后在 README 更新进度表，关闭相关的 issues
* 若 `待合并` 的 PR 不能合并，分配给 PR 提交者并将 PR 标记为 `待修改`

校对员:
* 对 `待校对` 的 PR 进行校对
* 校对通过则将 PR 改为`待合并`，并分配给协调员
* 校对不通过则将 PR 改为 `待修改`，并分配给 PR 提交者

#vuejs.org

This site is built with [hexo](http://hexo.io/). Site content is written in Markdown format located in `src`. Pull requests welcome!

## Developing

Start a dev server at `localhost:4000`:

```
$ npm install -g hexo-cli
$ npm install
$ hexo server
```
