# 翻译 Purpose-Driven Microservice Design

> [原文](https://betterprogramming.pub/purpose-driven-microservice-design-c25fa2a3ad0e)
>
> 创建目标驱动的微服务应该一直作为最终目标，了解[Render Blueprints]如何提供一个可重复构建的策略。

在我的职业生涯初期流行语并不是我期望的那样。在如今，大部分科技新闻来自周刊杂志，比如InformationWeek和Netowrk World。我记得我当时的想法：“你们在一遍又一遍的使用同样的词”。

一直以来人们都在使用流行语。以前，我最喜欢的两个流行语是将互联网引用为"world wide web"和"information superhighway"。我总是担心哪天出现了新词“超级超级告诉公路”。

然而，现在，我注意到流行语被用到了毫无意义的地方。像微服务、事件驱动架构、AI、ML等被使用在上下文中，我觉得大部分人们不理解这些。我也没想到会这样。

想象这个关于误解问题的简单的对话

> 朋友#1：你什么时候做飞机离开？
>
> 朋友#2：大概年底。

朋友#2提供了一个不算错误的答案，但是这个回答无法给朋友#1提供任何有用的信息。

同样，迁移至微服务也遇到类似的挑战。通常，我的客户和合作公司想要做微服务结果做成了单体大应用。基本上，单体大应用被替换成了一个大RESTful API。

关于以上结论，我觉得有必要演示下如何正确创建目标驱动微服务会挺有趣的。

## 目标驱动微服务设计

一个目标驱动的微服务可以自建立，当需要时可以包含指定的持久化工具。目标驱动，微服务将提供一组重点的信息，并成为关联API中的系统记录。

通过采用目标驱动微服务方法，用户可以添加额外的节点和缩减已存在的节点，根据时间满足流量。

作为一个案例，目标驱动微服务关注于收入税，可能最高使用在前半年，后半年比较少的运行示例。

让我们看一个非常简单的创建目标驱动微服务的例子。

## 创建一个Docker微服务

[中国性别预测指标]是一个预测孩子性别的系统，由母亲的怀孕年龄和月份来判断。

> 传言这是清朝时期家庭依赖相同的系统来选择男孩，男孩可以为家庭赚钱和延续香火。

下面是它的示例图

![实例图](https://miro.medium.com/max/1400/1*boU8hXND22rqE3eBaOwXag.png)

举个例子，一个18岁的母亲在1月怀孕会生下一个女孩。

为此，我们将创建一个目标驱动微服务，它能返回性别预测基于相同的理论。请求结果实例如下：

```json
{
    "month": 1,
    "age": 18,
    "gender": "female",
    "errorMessage": null
}
```

该微服务将使用Java和SpringBoot，而且会使用一个多阶段Dockerfile来编译和构建Docker镜像来提供API能力。

源码在此：[https://gitlab.com/johnjvester/birth-predictor](https://gitlab.com/johnjvester/birth-predictor)

## 使用[Render Blueprints]创建Reproducible Pattern

我写过一些关于[Render]平台的文章：

- [Using Render and Go For the First Time](https://betterprogramming.pub/how-to-create-a-web-service-using-render-and-go-75d211421a00)
- [Under the Hood:Render Unified Cloud](https://betterprogramming.pub/render-unified-cloud-under-the-hood-940d097cede8)

为了让我个人的实例跑在[Render]上，我用过Go，静态网页和Postgres实例。这次，我写了Java/SpringBoot的服务。

Java现在还不支持native，所以依赖Docker容器来执行Java。

所以这次用了Dockfile，我正好想要在Render平台上发布Docker服务有多容易，当然我注意到有个[文档](https://render.com/docs/infrastructure-as-code)能帮助我了解该平台是如何工作的。

## 什么是Blueprint？

组织管理需要发布的服务，服务的配置信息写在render.yaml中，是一种Infrastructure as Code (IaC)

## 使用BluePrint Redner

[官方文档](https://render.com/docs/blueprint-spec#sample-blueprint-spec)

我的SpringBoot项目用的配置

```yaml
services:
  - type: web
    name: restful-api-spring-boot
    env: docker
    region: ohio # optional (defaults to oregon)
    plan: free # optional (defaults to starter)
    branch: master # optional (uses repo default)
    numInstances: 1 # optional (defaults to 1)
    healthCheckPath: /actuator/health
    envVars:
      - key: SERVER_PORT
        value: 443
```

文件存在项目的根目录下的`render.yaml`中，提交代码后可以自动创建容器

下面都是些介绍如何使用Blueprint的，就不想翻译了。 

## 翻译总结

作者解释了Purpose-Driven Microservice是一个流行语，由一些周刊杂志创造的。个人感觉这就是一个微服务，就像作者说的互联网也有很多别称：“world wide web” “information superhighway”，这些词很容易造成人们的误解。我很赞同作者的观点，明明是一个意思，为什么要创造那么多词，可能是为了流量？为了Money？

本文重心是在介绍了用[Render Blueprints]快速构建一个无状态的服务，并且能被外部访问。推广[Render Blueprints]的一个软文，像一个CD工具。





[Render Blueprints]: <https://dashboard.render.com/select-repo?type=blueprint>
[ 中国性别预测指标 ]: https://www.thebump.com/chinese-gender-chart
[ Render ]: https://render.com/
