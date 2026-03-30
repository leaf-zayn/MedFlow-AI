# MedFlow-AI

## 1. 项目简介

MedFlow-AI 是一个面向医疗问诊与健康管理场景的智能辅助系统，基于开源项目进行二次开发，结合大模型问答、医疗知识检索、多轮会话记忆和预约挂号工具调用能力，提供一套可运行、可扩展、适合展示 AI 应用落地能力的医疗智能问诊 Demo。

项目整体采用前后端分离架构：

- 前端基于 Vue 3 + Vite + Element Plus，提供对话式交互界面
- 后端基于 Spring Boot + LangChain4j，负责大模型调用、RAG 检索、会话记忆和工具执行
- 数据层结合 MySQL、MongoDB、Pinecone，分别承担业务数据、会话上下文和向量检索能力

该项目适合用于以下场景：

- 作为医疗 AI 助手方向的项目展示
- 作为 LangChain4j + Spring Boot 的实践样例
- 作为 Agent + RAG + Tool Calling 的入门与二次开发底座
- 作为简历中的 AI 应用落地项目进行包装和延展

---

## 2. 项目定位

MedFlow-AI 的目标不是替代医生，而是作为智能导诊、问诊辅助和信息整理工具，帮助用户更高效地完成以下任务：

- 基于用户输入进行基础健康问答
- 结合知识库提供医院、科室、医学常识等信息查询
- 在多轮上下文中保持连续对话体验
- 根据用户提供的信息执行预约挂号或取消预约
- 通过流式响应提升交互实时性

当前版本更偏向工程演示和能力集成，重点体现：

- 大模型能力接入
- AI Agent 编排
- RAG 知识检索
- Tool 调用与业务系统联动
- 前后端分离与流式交互体验

---

## 3. 核心能力

### 3.1 智能问诊对话

用户可以通过前端输入自然语言问题，例如：

- 我最近头疼应该挂什么科
- 神经内科有哪些医生
- 帮我预约明天下午的门诊
- 取消我之前的预约

后端通过 LangChain4j 构建医疗问诊 Agent，对用户问题进行识别、理解和回复。

### 3.2 多轮会话记忆

系统使用会话 ID 维护上下文，支持连续追问，例如：

- 用户先问“我想挂神经内科”
- 再补充“明天下午”
- 再补充“帮我找一个医生”

系统可以在多轮对话中继承上下文，避免每次都重新描述完整信息。

### 3.3 RAG 医疗知识检索

项目内置 `rag文档` 目录，可用于维护医院信息、科室说明、基础医学知识等文档。后端会结合向量检索能力，在用户提问时召回相关知识片段，为大模型生成结果提供上下文支撑。

当前仓库中已包含示例文档，例如：

- 医院信息
- 科室信息
- 神经内科
- 人工智能相关资料

这部分能力适合扩展为：

- 医院知识库
- 科室介绍知识库
- 医疗 FAQ 知识库
- 患者教育内容知识库

### 3.4 预约挂号工具调用

项目后端集成了工具调用能力，支持：

- 预约挂号
- 取消预约
- 查询是否有号源

当前逻辑中，大模型会在识别到用户意图后，调用后端工具方法完成业务执行。预约数据存储在 MySQL 中，便于和常规业务系统衔接。

### 3.5 流式响应输出

项目对话接口采用流式返回，前端通过增量接收和实时渲染，实现类似聊天助手的连续输出效果，交互体验比一次性完整返回更自然。

### 3.6 多模型与多后端兼容

当前项目已配置多种模型接入方式：

- OpenAI Compatible 协议接入
- 阿里云百炼 DashScope 接入
- Ollama 本地模型接入

同时支持向量检索与本地知识增强，便于根据不同部署环境进行扩展。

---

## 4. 技术架构

### 4.1 前端技术栈

- Vue 3
- Vite 5
- Element Plus
- Axios
- UUID

前端核心职责：

- 提供聊天窗口 UI
- 管理用户输入和会话展示
- 调用后端流式接口
- 维护本地 `memoryId`
- 支持新建会话和页面滚动跟随

### 4.2 后端技术栈

- Java 17
- Spring Boot 3.2.6
- LangChain4j 1.0.0-beta3
- Spring Web
- Spring WebFlux
- MyBatis-Plus
- MongoDB
- MySQL

后端核心职责：

- 提供对话接口
- 管理会话上下文
- 组装 AI Agent
- 执行工具调用
- 对接向量检索
- 管理业务数据

### 4.3 AI 与数据相关组件

- DashScope / 通义千问
- OpenAI Compatible 模型接入
- Ollama 本地模型
- DashScope Embedding
- Pinecone 向量数据库
- MongoDB Chat Memory Store

### 4.4 系统分层说明

从职责划分上，可以将项目理解为以下几层：

1. 表现层  
   前端页面负责用户交互、消息展示和流式接收。

2. 接口层  
   Spring Boot Controller 对外暴露 HTTP 接口。

3. Agent 编排层  
   通过 LangChain4j 的 `@AiService` 定义系统消息、模型、记忆、工具和检索器。

4. 工具与业务层  
   预约挂号、取消预约、号源查询等能力通过工具类接入。

5. 数据与知识层  
   MySQL 存储业务数据，MongoDB 存储历史消息，Pinecone 存储向量索引，`rag文档` 目录提供知识语料来源。

---

## 5. 主要业务流程

### 5.1 智能问答流程

1. 用户在前端输入问题
2. 前端将 `memoryId` 与消息一起提交给后端
3. 后端 Agent 结合系统提示词、历史会话和知识检索结果生成回复
4. 如果识别到需要执行业务动作，则进一步触发工具调用
5. 回复内容以流式方式返回前端
6. 前端增量渲染内容，展示完整答案

### 5.2 预约挂号流程

1. 用户表达预约意图
2. Agent 识别出预约所需参数，例如科室、日期、时间、医生
3. 先调用号源查询工具判断是否可预约
4. 在用户确认信息后执行预约工具
5. 预约结果持久化到数据库
6. 最终将预约结果反馈给用户

### 5.3 知识检索流程

1. 用户提出医疗知识或医院信息相关问题
2. 系统将问题转为向量检索请求
3. 从 Pinecone 中召回相关文本片段
4. 将召回片段作为上下文交给大模型
5. 大模型生成更具针对性的回答

---

## 6. 仓库结构

```text
Medical
├── README.md
├── rag文档
│   ├── 医院信息.md
│   ├── 科室信息.md
│   ├── 神经内科.md
│   └── ...
├── 前端代码
│   └── xiaozhi-ui
│       ├── src
│       ├── package.json
│       └── vite.config.js
├── 后端代码
│   └── java-ai-langchain4j
│       ├── src/main/java
│       ├── src/main/resources
│       └── pom.xml
└── 课件
```

### 6.1 目录说明

- `前端代码/xiaozhi-ui`  
  前端项目目录，负责页面渲染、聊天交互和流式消息展示。

- `后端代码/java-ai-langchain4j`  
  后端项目目录，负责模型接入、Agent 编排、RAG 检索和业务逻辑处理。

- `rag文档`  
  RAG 知识库示例文档目录，可作为向量化数据源。

- `课件`  
  原项目学习资料与配套文档目录，便于理解项目背景。

---

## 7. 核心代码说明

以下文件是理解项目主流程的关键入口：

- [XiaozhiController.java](./后端代码/java-ai-langchain4j/src/main/java/com/atguigu/java/ai/langchain4j/controller/XiaozhiController.java)  
  对外暴露对话接口 `/xiaozhi/chat`，以流式方式返回模型输出。

- [XiaozhiAgent.java](./后端代码/java-ai-langchain4j/src/main/java/com/atguigu/java/ai/langchain4j/assistant/XiaozhiAgent.java)  
  定义 AI Agent，配置系统提示词、流式模型、会话记忆、工具调用和内容检索器。

- [AppointmentTools.java](./后端代码/java-ai-langchain4j/src/main/java/com/atguigu/java/ai/langchain4j/tools/AppointmentTools.java)  
  封装预约挂号、取消预约、号源查询等业务工具。

- [EmbeddingStoreConfig.java](./后端代码/java-ai-langchain4j/src/main/java/com/atguigu/java/ai/langchain4j/config/EmbeddingStoreConfig.java)  
  配置 Pinecone 向量存储，用于支撑 RAG 检索。

- [application.properties](./后端代码/java-ai-langchain4j/src/main/resources/application.properties)  
  后端核心配置文件，包含服务端口、模型参数、数据库连接和日志配置。

- [ChatWindow.vue](./前端代码/xiaozhi-ui/src/components/ChatWindow.vue)  
  前端聊天窗口组件，负责消息渲染、请求发送、流式接收和会话管理。

---

## 8. 运行环境要求

在本地启动项目前，建议准备以下环境：

### 8.1 基础环境

- JDK 17
- Maven 3.9 及以上
- Node.js 18 及以上
- npm 9 及以上

### 8.2 数据与中间件

- MySQL 8.x
- MongoDB 6.x 或兼容版本
- Pinecone 账号与索引

### 8.3 可选依赖

- Ollama  
  如果希望在本地运行模型，可安装并拉取对应模型。

- DashScope API Key  
  如果使用阿里云百炼和通义千问模型，需要准备对应密钥。

---

## 9. 配置说明

后端核心配置位于：

- [application.properties](./后端代码/java-ai-langchain4j/src/main/resources/application.properties)

### 9.1 服务端口

```properties
server.port=8080
```

默认后端服务运行在 `8080` 端口。

### 9.2 模型配置

当前项目同时配置了以下模型接入：

#### OpenAI Compatible 方式

```properties
langchain4j.open-ai.chat-model.base-url=https://dashscope.aliyuncs.com/compatible-mode/v1
langchain4j.open-ai.chat-model.api-key=${DASH_SCOPE_API_KEY}
langchain4j.open-ai.chat-model.model-name=deepseek-v3
```

#### Ollama 本地模型

```properties
langchain4j.ollama.chat-model.base-url=http://localhost:11434
langchain4j.ollama.chat-model.model-name=deepseek-r1:1.5b
langchain4j.ollama.chat-model.temperature=0.8
langchain4j.ollama.chat-model.timeout=PT60S
```

#### DashScope 对话与流式模型

```properties
langchain4j.community.dashscope.chat-model.api-key=${DASH_SCOPE_API_KEY}
langchain4j.community.dashscope.chat-model.model-name=qwen-max

langchain4j.community.dashscope.streaming-chat-model.api-key=${DASH_SCOPE_API_KEY}
langchain4j.community.dashscope.streaming-chat-model.model-name=qwen-plus
```

#### DashScope 向量模型

```properties
langchain4j.community.dashscope.embedding-model.api-key=${DASH_SCOPE_API_KEY}
langchain4j.community.dashscope.embedding-model.model-name=text-embedding-v3
```

### 9.3 MongoDB 配置

```properties
spring.data.mongodb.uri=mongodb://localhost:27017/chat_memory_db
```

MongoDB 主要用于存储聊天记忆和上下文数据。

### 9.4 MySQL 配置

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/guiguxiaozhi?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

MySQL 主要用于存储预约挂号相关业务数据。

### 9.5 Pinecone 配置

Pinecone 的关键配置来自代码中的环境变量：

```java
.apiKey(System.getenv("PINECONE_API_KEY"))
```

请在本地环境中设置：

```bash
export PINECONE_API_KEY=your_pinecone_api_key
export DASH_SCOPE_API_KEY=your_dashscope_api_key
```

---

## 10. 本地启动指南

### 10.1 启动后端

进入后端目录：

```bash
cd 后端代码/java-ai-langchain4j
```

安装依赖并启动：

```bash
mvn spring-boot:run
```

或先打包再运行：

```bash
mvn clean package
java -jar target/java-ai-langchain4j-1.0-SNAPSHOT.jar
```

启动成功后，默认监听：

```text
http://localhost:8080
```

### 10.2 启动前端

进入前端目录：

```bash
cd 前端代码/xiaozhi-ui
```

安装依赖：

```bash
npm install
```

启动开发服务：

```bash
npm run dev
```

通常会启动在：

```text
http://localhost:5173
```

### 10.3 前后端联调

前端通过 Vite 代理将 `/api` 请求转发到后端服务。也就是说，前端请求：

```text
/api/xiaozhi/chat
```

在本地开发时会被转发到后端：

```text
http://localhost:8080/xiaozhi/chat
```

---

## 11. API 说明

### 11.1 对话接口

**请求路径**

```text
POST /xiaozhi/chat
```

**请求头**

```text
Content-Type: application/json
```

**请求参数**

```json
{
  "memoryId": 100001,
  "message": "我想预约明天下午的神经内科"
}
```

字段说明：

- `memoryId`：会话 ID，用于关联上下文
- `message`：用户输入内容

**返回格式**

接口返回 `text/stream;charset=utf-8`，前端按流式内容逐步拼接和展示。

**适用场景**

- 问诊咨询
- 医疗知识问答
- 医院/科室信息查询
- 预约挂号意图识别与执行

---

## 12. RAG 知识库说明

项目根目录中的 `rag文档` 目录可作为知识库原始语料来源，适合放置以下内容：

- 医院介绍
- 科室职能说明
- 医生擅长方向
- 常见疾病科普内容
- 就诊须知与流程说明

建议维护方式：

1. 按领域拆分 Markdown 文档
2. 控制单篇文档主题聚焦，避免过于杂乱
3. 对医学信息保持准确、规范和可追溯
4. 通过嵌入模型将文档向量化后写入 Pinecone

如果后续继续扩展，建议按如下维度建设知识库：

- 医院知识库
- 科室知识库
- 医生知识库
- 症状到科室映射知识库
- 预约规则知识库

---

## 13. 前端说明

前端当前以一个简洁的聊天窗口为核心，适合快速验证 AI 助手交互流程。

### 13.1 已实现能力

- Logo 与项目名称展示
- 新建会话
- 用户与机器人消息分栏展示
- 流式消息输出
- 自动滚动到底部
- 本地持久化会话 ID
- 移动端基础适配

### 13.2 交互特点

- 首次打开会自动发送一条“你好”作为初始化问候
- 每个用户在本地浏览器中维护自己的会话 ID
- 新会话会重置本地 UUID

### 13.3 后续可扩展方向

- 会话列表管理
- Markdown 渲染
- 问诊卡片与结构化结果展示
- 预约结果确认弹窗
- 医疗免责声明展示
- 用户登录与历史记录查看

---

## 14. 后端说明

后端是整个项目的核心编排层，承载了模型接入、工具调用和数据管理。

### 14.1 已接入能力

- 基于 LangChain4j 的 AI Agent
- 系统提示词配置
- 流式模型输出
- Chat Memory 会话记忆
- Pinecone 向量检索
- Appointment Tool 工具调用
- MongoDB 聊天记忆存储
- MyBatis-Plus 业务持久化

### 14.2 当前工具能力

当前后端工具主要包括：

- `预约挂号`
- `取消预约挂号`
- `查询是否有号源`

其中号源查询逻辑目前仍是示例性质，后续可以进一步对接真实排班与库存系统。

### 14.3 后续可增强能力

- 用户身份认证
- 就诊人管理
- 医生排班与号源管理
- 风险词过滤与医疗合规提示
- Prompt 模块化配置
- 工具调用监控和审计日志
- 多租户知识库隔离

---

## 15. 技术亮点总结

从项目展示角度看，MedFlow-AI 具备以下亮点：

- 将大模型能力与医疗业务场景结合
- 同时覆盖 Agent、RAG、Memory、Tool Calling 等关键能力
- 实现了前后端分离和流式交互
- 支持多模型接入，具备较强扩展空间
- 具备面向简历展示和二次开发的良好基础

---

## 16. 适合简历中的项目描述方向

如果你计划将本项目用于简历展示，可以围绕以下关键词展开：

- 医疗 AI 助手
- 智能问诊
- RAG 知识库检索
- LangChain4j Agent 开发
- Spring Boot + Vue 前后端分离
- 工具调用与业务系统联动
- 流式响应与多轮会话记忆

示例描述方向：

> 基于 Spring Boot、LangChain4j 和 Vue 3 实现医疗 AI 辅助问诊系统，集成多轮对话记忆、RAG 医疗知识检索、预约挂号工具调用和流式响应能力，完成医疗问答场景下的 AI 应用工程化落地。

---

## 17. 已知限制

当前项目更偏演示和教学实践，仍存在以下限制：

- 医疗建议不能替代专业医生诊断
- 预约排班逻辑较简化，未接入真实医院系统
- 安全、鉴权、权限控制尚未完善
- 知识库规模和召回策略仍有较大优化空间
- 前端交互形态较轻量，尚未达到产品级复杂度

---

## 18. 后续规划建议

如果需要继续将该项目打磨成更完整的作品，建议按以下方向演进：

### 18.1 业务侧

- 引入真实医生、科室、排班和号源数据
- 增加就诊人档案管理
- 支持预约确认、改约和退号
- 支持导诊、复诊提醒、检查项目说明

### 18.2 AI 侧

- 优化 Prompt 设计
- 补充结构化输出
- 引入症状分诊规则
- 增加问题分类和意图识别
- 提升知识召回准确率

### 18.3 工程侧

- 增加环境隔离与配置分层
- 增加日志与监控
- 增加统一异常处理
- 增加接口文档和部署脚本
- 增加容器化部署能力

---

## 19. 免责声明

本项目仅用于 AI 应用开发学习、技术研究和工程实践展示，不构成任何医疗诊断、治疗建议或正式临床依据。对于涉及健康、疾病、用药和诊疗决策的问题，请以专业医生意见为准。

---

## 20. 致谢

本项目基于开源项目进行二次开发，并结合医疗问诊业务场景进行了功能整合与结构整理。感谢原项目作者及相关开源生态在 Spring Boot、Vue、LangChain4j、大模型接入和向量检索领域提供的基础能力支持。
