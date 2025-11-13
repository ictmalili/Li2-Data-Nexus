整理自AWS系列博客： Agent互操作性开放协议
1. [第一部分：MCP上的智能体间通信](https://aws.amazon.com/blogs/opensource/open-protocols-for-agent-interoperability-part-1-inter-agent-communication-on-mcp/)
2. [第二部分：MCP上的身份验证](https://aws.amazon.com/blogs/opensource/open-protocols-for-agent-interoperability-part-2-authentication-on-mcp/)
3. [第三部分：Strands Agents MCP](https://aws.amazon.com/blogs/opensource/open-protocols-for-agent-interoperability-part-3-strands-agents-mcp/)
4. [第四部分：A2A上的智能体间通信](https://aws.amazon.com/blogs/opensource/open-protocols-for-agent-interoperability-part-4-inter-agent-communication-on-a2a/)

随着AI智能体数量的快速增长，智能体之间的沟通和交互变得越来越重要。AWS通过支持两个主要的开放协议——模型上下文协议(MCP)和智能体到智能体协议(A2A)，为构建可互操作的智能体生态系统提供了完整的解决方案。

## MCP机制：工具集成的标准化协议

### 核心设计理念
MCP在设计时就考虑了灵活性和可扩展性，创建了一个从工具集成扩展到智能体协作的基础。MCP已经提供了智能体相互通信所需的核心基础设施，包括对多种通信机制、身份验证/授权、能力协商和上下文共享的支持。

### Streamable HTTP灵活通信
多智能体通信可以根据交互智能体的复杂性和设计采用各种不同的模式。MCP的流式HTTP实现为开发者提供了丰富的交互模式调色板，无需重新发明轮子。

#### 通信模式
• **无状态请求/响应流**：适用于智能体间简单的一次性交互

• **有状态会话管理**：通过持久ID在多次交换中保持上下文，适用于更复杂的对话

• **实时数据交换**：流式HTTP传输使用服务器发送事件(SSE)实现响应流式传输

#### 高级功能
通过SSE，MCP支持以下功能，这些功能非常适合构建需要相互共享持续更新的长时间运行智能体：

• **进度通知**：实时反馈任务执行进度

• **请求取消**：支持中途取消长时间运行的请求

• **响应缓冲**：在客户端断开连接期间缓冲响应

### MCP能力发现和协商
为了有效协作，每个智能体都需要了解其他智能体的能力。MCP通过其能力发现功能解决了这一需求。

#### 协商机制

• **协议特性协商**：智能体连接时，可以协商会话期间可用的协议功能

• **服务器级别决定**：这种确定发生在服务器级别，但扩展到智能体相互暴露的各个工具或技能

• **详细能力声明**：每个智能体可以声明其能力的详细描述（建模为工具）以及它们接受的参数

#### 动态生态系统
工具通知系统允许智能体在新能力可用或现有能力发生变化时得到通知。这种通知创建了一个动态生态系统，智能体可以发现和利用彼此不断发展的技能集，形成随时间适应和变得更有能力的网络。

### MCP安全机制
信任和安全构成了有效智能体协作的基础。MCP在传输层实现基于OAuth 2.0/2.1的身份验证和授权，确保智能体可以验证彼此的身份并维护适当的访问控制。

### 上下文共享
像人类一样，智能体需要可靠的机制来相互共享上下文（文件、应用程序状态、智能体记忆）。

#### 资源能力
MCP使智能体能够使用其资源能力共享各种数据：

• **上下文信息共享**：智能体可以共享有关其可用上下文的信息

• **上下文检索**：允许其他智能体检索该上下文

• **资源变更订阅**：智能体可以订阅资源变更通知，使开发者能够实现智能体间具有依赖关系的复杂工作流

#### 采样功能
智能体还可以相互共享提示，甚至可以共享LLM。这种能力称为采样。采样支持智能体工作流，例如提供交互式协助，工作方式如下：

1. 服务器发送采样请求：向客户端发送采样请求
2. 客户端审查请求：客户端审查请求并可以修改它
3. 客户端采样：客户端从LLM采样
4. 客户端审查完成：客户端审查完成结果
5. 返回结果：客户端将结果返回给服务器

## A2A：专为智能体间通信设计的协议

### Agent Cards智能体卡片
智能体发现和理解的基础：

• **能力描述**：捕获智能体的语义含义、工作偏好和擅长的任务类型

• **智能决策支持**：帮助其他智能体决定何时以及如何协作

• **认证要求**：描述智能体的身份验证和授权需求

• **渐进式能力暴露**：通过认证扩展智能体卡片逐步增加暴露的能力

### 结构化任务执行

• **任务组织**：将消息组织成携带上下文、跟踪进度和存储输出工件的智能工作单元

• **依赖管理**：理解工作流中任务间的依赖关系，支持顺序和并行执行

• **可追溯性**：通过任务和上下文ID跟踪任务来源，提供完整的对话历史

• **工作流建模**：为开发者提供建模真实业务流程的灵活性

### 多样化传输选项
A2A支持三种等效的核心协议：

• **JSON-RPC 2.0**：轻量级的远程过程调用

• **gRPC**：高性能的RPC框架

• **REST**：基于HTTP的架构风格

增强功能：

• **服务器发送事件(SSE)**：用于流式传输和实时更新

• **Webhook推送通知**：用于长时间运行操作的异步通知

### A2A安全机制

• **多种认证方案**：支持OAuth 2.0、OpenID Connect和mTLS

• **企业集成**：与现有身份基础设施集成

• **细粒度控制**：技能特定的授权元数据和二级认证支持

• **零信任架构**：将每个智能体视为独立的安全边界

• **审计支持**：为全面的安全监控和合规报告提供基础

## Strands Agents与MCP的集成
### 架构概述
![HR智能体架构示例](https://d2908q01vomqb2.cloudfront.net/ca3512f4dfa95a03169c5a670a4c91a19b3077b4/2025/07/10/HR-agent-architecture.png)

### 实现步骤详解

#### 1. 创建员工数据MCP服务器
首先构建一个基础的MCP服务器来暴露员工数据：
```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("employee-server", stateless_http=True, host="0.0.0.0", port=8002)

@mcp.tool()
def get_skills() -> set[str]:
    """获取所有员工可能拥有的技能列表 - 使用此列表找出相关技能"""
    return SKILLS

@mcp.tool()
def get_employees_with_skill(skill: str) -> list[dict]:
    """获取拥有指定技能的员工列表 - 输出包括全名(名 姓)和他们的技能"""
    skill_lower = skill.lower()
    return [employee for employee in EMPLOYEES if any(s.lower() == skill_lower for s in employee["skills"])]

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

#### 2. 创建员工信息智能体
使用Strands Agents连接到MCP服务器并创建智能体：

```python
EMPLOYEE_INFO_URL = os.environ.get("EMPLOYEE_INFO_URL", "http://localhost:8002/mcp/")
employee_mcp_client = MCPClient(lambda: streamablehttp_client(EMPLOYEE_INFO_URL))

bedrock_model = BedrockModel(
    model_id="amazon.nova-micro-v1:0",
    region_name="us-east-1",
)

def employee_agent(question: str):
    with employee_mcp_client:
        tools = employee_mcp_client.list_tools_sync()
        agent = Agent(
            model=bedrock_model,
            tools=tools,
            system_prompt="you must abbreviate employee first names and list all their skills",
            callback_handler=None
        )
        return agent(question)
```

多轮推理工作流程：
这个示例展示了Amazon Bedrock和Nova Micro模型的多轮推理能力。多轮推理是指AI智能体在循环中多次调用AI模型，通常涉及调用工具来获取或更新数据，在初始任务完成或发生错误时结束。

典型的多轮推理流程：
1. 用户询问"列出具有AI相关技能的员工"
2. LLM发现它可以访问员工技能列表，指示智能体调用该工具
3. 智能体调用get_skills工具并将技能返回给LLM
4. LLM确定哪些技能与AI相关，发现可以使用get_employees_with_skill工具获取每个技能的员工
5. 智能体获取每个技能的员工并返回给LLM
6. LLM汇总具有AI相关技能的完整员工列表并返回

这种多轮交互跨越多个LLM调用和多个工具调用，封装到单个agent(question)调用中，展示了Strands Agents执行智能体循环以完成提供任务的强大能力。这个示例还展示了员工智能体如何在底层工具之上添加额外指
令，通过系统提示实现自定义行为。

#### 3. 将智能体暴露为MCP服务器
为了让其他智能体能够与员工信息智能体交互，我们将其包装为MCP服务器：

```python
mcp = FastMCP("employee-agent", stateless_http=True, host="0.0.0.0", port=8001)

@mcp.tool()
def inquire(question: str) -> list[str]:
    """回答与员工相关的问题"""
    return [
        content["text"]
        for content in employee_agent(question).message["content"]
        if "text" in content
    ]

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

#### 4. 创建HR智能体REST API
HR智能体暴露为REST API，以便可以从Web应用程序或其他服务调用：

```python
EMPLOYEE_AGENT_URL = os.environ.get("EMPLOYEE_AGENT_URL", "http://localhost:8001/mcp/")
hr_mcp_client = MCPClient(lambda: streamablehttp_client(EMPLOYEE_AGENT_URL))

bedrock_model = BedrockModel(
    model_id="amazon.nova-micro-v1:0",
    region_name="us-east-1",
    temperature=0.9,
)

app = FastAPI(title="HR Agent API")

class QuestionRequest(BaseModel):
    question: str

@app.get("/health")
def health_check():
    return {"status": "healthy"}

@app.post("/inquire")
async def ask_agent(request: QuestionRequest):
    async def generate():
        with hr_mcp_client:
            tools = hr_mcp_client.list_tools_sync()
            agent = Agent(model=bedrock_model, tools=tools, callback_handler=None)
            async for event in agent.stream_async(request.question):
                if "data" in event:
                    yield event["data"]

    return StreamingResponse(
        generate(),
        media_type="text/plain"
    )
```

### 关键特性说明

#### 微服务架构
暴露为MCP服务器的智能体提供了类似微服务的架构，将智能体彼此解耦，利用MCP作为通信的通用协议。

#### 流式响应
HR智能体支持流式响应，可以实时返回处理结果，提供更好的用户体验。

## Strands Agents与A2A的集成

### 架构设计
A2A协议专门为智能体间通信而设计，Strands Agents的内置A2A支持使得智能体可以轻松地作为A2A服务器暴露，并与其他A2A智能体通信。

![A2A智能体间通信架构](https://d2908q01vomqb2.cloudfront.net/ca3512f4dfa95a03169c5a670a4c91a19b3077b4/2025/08/20/mermaid-diagram-2025-07-24-131022.png)

### 实现详解

#### 1. 员工信息智能体基础配置
```python
employee_agent = Agent(
    model=bedrock_model,
    name="Employee Agent",
    description="Answers questions about employees",
    tools=tools,  # 来自MCP客户端的工具
    system_prompt="you must abbreviate employee first names and list all their skills"
)
```

#### 2. 暴露为A2A服务器
使用Strands Agents将智能体暴露为A2A服务器只需要几行代码：

```python
a2a_server = A2AServer(
    agent=employee_agent,
    host=urlparse(EMPLOYEE_AGENT_URL).hostname,
    port=urlparse(EMPLOYEE_AGENT_URL).port
)

if __name__ == "__main__":
    a2a_server.serve(host="0.0.0.0", port=8001)
```

注意：通过环境变量传递EMPLOYEE_AGENT_URL，这样了解端点URL的基础设施定义可以设置A2A智能体卡片中使用的主机和端口（用于A2A客户端的智能体发现）。

#### 3. 创建HR智能体A2A客户端
python
provider = A2AClientToolProvider(known_agent_urls=[EMPLOYEE_AGENT_URL])
agent = Agent(model=bedrock_model, tools=provider.tools)


### A2A通信流程详解

当REST请求发出时，会发生以下流程：

1. 用户查询：用户（可能通过Web或移动应用）发送查询，如"列出具有AI相关技能的员工"

2. HR智能体处理：HR智能体使用Amazon Nova模型理解用户查询，决定需要将查询发送给员工信息智能体

3. A2A通信：使用A2A协议将查询发送到员工信息智能体

4. 员工智能体处理：员工信息智能体使用Amazon Nova模型理解查询，决定需要调用员工数据MCP服务器

5. MCP工具调用：员工信息智能体调用员工数据MCP服务器，查询员工数据库并将数据返回给Nova模型

6. 结果格式化：根据系统提示缩写员工名字，模型获取员工列表，格式化并返回文本给员工信息智能体

7. 响应返回：员工信息智能体将文本返回给HR智能体，HR智能体在REST响应中返回结果

### 对等通信的优势

#### 智能体作为对等体
与MCP中智能体主要作为工具使用不同，A2A中的智能体作为对等体进行通信，这种区别至关重要，因为它使智能体能够：
• 进行复杂的来回交互
• 协商任务需求
• 在朝着共同目标工作时保持独立的决策能力

#### 企业级特性

• **安全智能体发现**：通过标准化智能体卡片

• **身份验证和授权**：用于受控访问的机制

• **多种通信模式**：支持文本、表单、媒体

• **长时间运行任务**：智能体可以在不暴露内部状态或实现细节的情况下协作

## MCP的权限管理
### OAuth增强实现
AWS对MCP身份验证的贡献基于成熟的OAuth标准：

• **自动发现**：基于RFC 8414的授权服务器元数据发现

• **动态注册**：自动客户端注册，无需手动配置

• **安全保护**：通过RFC 8707防止凭据盗用和跨服务器滥用

### 企业级部署
• **集中式授权**：单个授权服务器可为多个MCP服务器处理身份验证

• **SSO集成**：与现有单点登录基础设施无缝集成

• **分层认证**：支持前端OAuth与后端现有认证系统的结合

### 未来发展方向

• **自主智能体支持**：JWT断言替代客户端密钥

• **多跳场景**：支持复杂的智能体协作链

• **工作负载身份**：与SPIFFE等现代标准集成


## AWS对MCP的其他贡献

### Human In the Loop Interactions人机交互循环

• **引出功能**：MCP服务器可以在运行时请求用户提供额外信息

• **动态交互**：根据运行时特征确定是否需要更多输入

• **用户体验优化**：在自动化和人工干预之间找到平衡

### Streaming Partial Results流式部分结果

• **实时反馈**：在处理长时间运行请求时提供部分结果

• **用户体验**：避免长时间等待，提供进度感知

• **性能优化**：通过SSE支持中间结果传输

### Enhanced Capability Discovery增强能力发现

• **输出模式声明**：工具可以声明其输出模式

• **类型安全**：支持更安全的工具输出到类型化数据的转换

• **元数据丰富**：为工具和智能体技能提供额外的能力元数据

### Asynchronous Communication异步通信

• **简化抽象**：为异步通信提供更简单的抽象

• **共享状态**：支持智能体间的状态共享

• **状态检查**：客户端轮询驱动的状态检查机制

## 技术选择指南

### MCP vs A2A选择标准
| 场景 | 推荐协议 | 原因 |
|------|----------|------|
| 工具集成 | MCP | 专为工具连接设计，成熟稳定 |
| 智能体协作 | A2A | 专为智能体间通信优化 |
| 混合场景 | MCP + A2A | 发挥各自优势，灵活组合 |
| 企业部署 | 两者皆可 | 根据现有基础设施选择 |

### AWS部署
两种集成方式都可以在AWS上使用多种运行时部署：

• **Amazon EKS**：Kubernetes容器编排

• **Amazon ECS**：容器服务

• **Amazon Bedrock AgentCore**：托管智能体服务

• **AWS Lambda**：无服务器计算

![AWS部署架构图](https://d2908q01vomqb2.cloudfront.net/ca3512f4dfa95a03169c5a670a4c91a19b3077b4/2025/08/21/mermaid-diagram-2025-08-20-181650.png)


## 总结

本文基于AWS智能体互操作性开放协议系列博客，全面梳理了MCP和A2A两大协议的技术特性、实现方式和应用场景。

### 主要收获

• **协议互补**：MCP擅长工具集成，A2A专注智能体协作，两者结合可构建强大的智能体生态

• **开发简化**：Strands Agents SDK大幅降低了智能体开发门槛，几行代码即可实现复杂交互

• **企业就绪**：完善的身份验证、安全机制和AWS云服务支持，满足生产环境需求

• **标准化价值**：开放协议避免厂商锁定，促进整个行业的创新和发展

### 技术启示
智能体间的标准化通信协议正在成为AI应用架构的重要基础设施，类似于互联网协议对网络应用的意义。AWS通过积极参与开源社区和规范制定，展现了对开放生态的承诺。

### 实践建议
对于企业和开发者而言，及早了解和采用这些开放协议，将有助于构建更灵活、可扩展的AI应用系统，为未来的智能体协作时代做好准备。
