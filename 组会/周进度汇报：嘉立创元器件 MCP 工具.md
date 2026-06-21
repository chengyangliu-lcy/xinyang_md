# 周进度汇报：嘉立创元器件 MCP 工具

## 一、关键进展

### 1\. **芯阳 WebUI** 平台接入与部署

  * 已完成 **芯阳 WebUI 平台** 的接入与测试
  * 已在公司服务器完成部署，支持团队内部使用
  * 实现了三个核心工具：


  * `lcsc_search`：通过关键词搜索元器件，返回 C 编号、型号、厂商、分类、库存、阶梯价格
  * `lcsc_detail`：根据 C 编号查询完整详情，包括技术参数、数据手册链接
  * `lcsc_datasheet`：快速获取元器件数据手册 PDF 下载链接


![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1780486314567-825fc084-185c-4ce2-90d2-6d873a58020f.png)

![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1780565821939-1d1f43db-a341-4629-8caa-4133b901d203.png)

![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1780565844390-9341d89f-e05b-4a22-803d-234d669788ab.png)

![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1780566825915-75e3962b-6a0f-4c2b-bc10-fe93a8c4c04e.png)

![image.png](https://cdn.nlark.com/yuque/0/2026/png/43223830/1780566856616-8b5ff364-c427-407f-816b-f0bc659418a5.png)

### 2\. 技术方案亮点

  * **双站兼容** ：同时支持立创商城中文站（szlcsc.com）和英文站（lcsc.com/jlcpcb）
  * **数据手册优化** ：优先使用英文 JLCPCB 接口获取 PDF 链接，避免中文站附件域名的 403 问题
  * **稳定性保障** ：内置基础限速（3 秒间隔）+ 403/429 自动冷却重试机制


### 3\. MCP 服务端正在开发中

  * 基础框架已搭建，采用 Node.js + TypeScript
  * 支持 MCP 协议，可对接 Claude Code、Codex、OpenCode 等主流 AI 工具
  * 核心功能与 WebUI 工具保持一致


* * *

## 二、风险与问题

风险项| 影响  
---|---  
立创页面结构变更| 解析逻辑可能失效  
403/429 限流| 频繁查询触发拦截  
  
* * *

## 三、下周计划

  1. **完成 MCP 服务端开发** ：


  * 完善三个工具的 MCP 接口定义
  * 适配 Claude Code、Codex、OpenCode 客户端


  2. **稳定性优化** ：


  * 增加更多元器件品类的兼容性测试
  * 优化数据手册获取的成功率


  3. **继续优化芯阳大模型问答体验，提高回答质量**
