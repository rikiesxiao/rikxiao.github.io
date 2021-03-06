---
layout: post
title: "用技术管理团队"
subtitle: '和人打交道、还是需要一些制度'
author: "叉叉敌"
header-style: text
tags:
  - 管理团队
---



今天遇到一个问题，就是如何用`技术`来管理团队？

我自己想到一个点，一个是人的软性管理，`一个硬性可以度量的管理`。

# 人的管理

用对的人做对的事情。

就是找的这个人，他特长点是什么，然后去做对应的事情，比如他擅长开发，比较仔细，那可能去做一些效率提升开发相关的；对于对风险识别比较高的，那可以做一些小项目的管理。

其实找到每个人的特长，然后用他们的特长去做他们更擅长的事情。

以及一些团队建设，包括文档、氛围的建设，这些都是因人而异，不是刻板半数可以说出来的，但是可以`借鉴参考一些优秀的活动`，比如团建，做一些户外活动，提高团队凝聚力等。

# 可以度量

就那测试来说，就需要从`测试质量和效率提升`，这2个目标来展开。

## 测试质量

需求质量、设计质量、系统质量、用户文档质量

测试计划：用例数、工作量、风险覆盖率，以及需求覆盖率、功能覆盖率、代码覆盖率、纳入可复用的测试用例%

测试执行：缺陷趋势曲线， 以及需求、设计、代码缺陷发现率，和系统测试缺陷发现率

测试脚本质量：运行失败率、测试覆盖率

测试环境：与真实环境的差异项(加权计算)、可靠性(平均失效时间)、可用性(可执行/计划用例数)、环境配置组合覆盖率

缺陷报告：误报率、描述不清楚、被打回的缺陷数

上线质量：缺陷遗漏率-> 设置遗漏、执行遗漏


## 测试效率

测试分析：风险列表个数、功能点、场景

测试设计：用例数、场景数、脚本行数

执行：用例数、自动化比重、回归测试自动化比重、bug数、安全性漏洞数、性能易用等测试工作

缺陷处理和跟踪：reopen次数、bug验证周期

变更引起的返工：需求、设计


# readmore

https://blog.csdn.net/weixin_39608063/article/details/110904028
https://cloud.tencent.com/developer/article/1614700
http://www.woshipm.com/user-research/728330.html
https://www.zhihu.com/question/25061626