整理自AWS Blog https://aws.amazon.com/blogs/database/key-components-of-a-data-driven-agentic-ai-application/

Agentic AI能够提升生产效率，解决传统软件不能解决的问题，简化集成，找到之前不可见的答案。 Agentic AI系统自动决定如何以及调用何种action（动作）来完成一个任务，并能随着更多信息来逐步调整计划。但是，这里还需要人工的操作，需要人工通过自然语言给出提示词，并进行管控。人类聚焦在目标上，agent聚焦在找到完成目标需要执行的细节上。当人类应用接口发生变化时，AgentAI系统可以仍然依赖于相同的后端来完成任务。以买玩具汽车为例，Agentic AI系统能够调用购买、库存更新、物流以及订单确认的工作流，在这些工作流中需要调用相同的后端服务和数据库。但问题是：Agentic AI系统是否能够直接和数据库来交互来拿到库存信息活着调用信息卡网络API来付费？也就是：Agentic AI系统通过什么方式与数据交互？

这篇博客中，作者探讨了直接数据库访问来替代智能体AI服务的成本、收益和缺点，包含在生产中验证过的有效的方式，以及还需要构建的服务。

**Agentic AI 系统的原子性**

Agentic AI应用的核心其实是一个循环（loop).用户让系统完成一项任务时，工作流就进入了一个event的loop，这个loop中会不停迭代循环，直至自己认为完成了这项任务或回答了用户的问题。系统然后会回馈给用户来寻求更多的信息。这个loop，实际上就是Reason+Act（ReAct）loop，是Agentic AI系统的最流行的设计模式。

![Uploading DBBLOG-5187-2.png…]()

