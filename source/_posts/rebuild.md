---
title: Rebuild Blog
date: 2019-02-25 23:30:14
tags: blog
categories: hexo blog
---

# 从头开始

非常悲催,由于一次误操作,导致了我的Blog源文件丢失.然后由于本人懒散 :grinning: ,一直拖到现在才重建Blog. 

# 新科技

1. 为了上次经验教训采用了github保存blog源文件
2. 采用了 Travis-CI 自动化部署 (:smirk:)

# 具体流程

tarvis-ci服务器不断地监听blog主线开发的变更内容， 一旦有任何变更（可以理解为 git commit ）就自动调用测试和部署脚本。然后我的blog就自动更新了.
(好处就是可以直接在github web页面写文章了,而且也不需要担心多设备同步问题.2333 黑科技不? :wink:
