---
sidebar_position: 0
---
# Autopilot Observability: The Intelligent Revolution in Observability

Just as Docker revolutionized cloud-native technology through containerization, we believe that AI-powered workflows built on data and expert knowledge will revolutionize the field of observability.

## The AI-Driven Observability Revolution

APO (Autopilot Observability) is dedicated to transforming AI agents into the intelligent core of observability systems, completely changing how we approach troubleshooting, performance optimization, and root cause analysis. With autonomous agents, observability systems are no longer passive tools but active problem-solving partners.

When an alert is triggered, these intelligent agents take over—automatically analyzing the root cause and providing solutions. Imagine a scenario where a microservice's database queries are performing poorly. The autonomous agent not only notifies you immediately but also delivers a detailed incident report with precise recommendations. You can even have a natural conversation with it:

"Why is my application running slow?"

"Service A's database query is causing a full table scan, resulting in increased latency. I recommend optimizing your indexes."

![agent-future](/img/agent-future.gif)

While fully autonomous AI agents remain APO's ultimate vision, Agentic workflows—the core feature of this release—represent the critical first step toward this revolutionary future.
<!-- truncate -->
## Agentic Workflows: Your Experience, the Agent's Intelligence

General-purpose LLMs are powerful but lack specialized IT knowledge. IT systems are complex and varied, making it difficult to address operational challenges with general knowledge alone.

**APO's breakthrough innovation lies in injecting your operational wisdom—your specific experience—into the core of these intelligent agents**. Ordinary AI solutions follow preset logic, completely ignoring your years of valuable experience. APO changes this fundamentally by allowing you to build Agentic workflows that feed your professional knowledge directly into the system, making it the driving force behind agent decisions.

Think about that experienced IT operations expert on your team who always quickly identifies the root cause of system problems. When they're on vacation, others might take several times longer to solve the same issues. APO's Agentic workflows digitize this expertise, allowing everyone to solve problems with the same efficiency.

**APO comes with predefined Agentic workflows for various scenarios, allowing you to experience intelligent operations immediately:**

+ **Intelligent Alert Analysis**: When database CPU suddenly spikes, traditional alerts simply notify you that "CPU usage exceeds 90%." APO launches an analysis workflow, automatically collecting related SQL queries, connection counts, and I/O information. It identifies that "this is caused by a full table scan due to missing indexes in the newly deployed user registration API" and even suggests SQL optimization recommendations.

+ **Automated Operations Inspection**: On Monday mornings, you no longer need to check weekend system performance individually. APO automatically executes inspection workflows, checking all service health statuses, resource usage trends, and abnormal logs. It generates a concise weekend system report: "User service experienced three brief CPU spikes early Sunday morning, but user experience was unaffected; storage space is predicted to reach warning levels in 14 days, expansion recommended before Thursday."

+ **Quick Diagnosis of Complex Issues**: For complicated problems like "intermittent user login failures," traditional methods require team collaboration and checking potential causes one by one. APO's workflow systematically collects evidence: first checking the user service itself (finding no abnormalities), then examining dependent services (discovering occasional timeouts in the authentication service), and finally tracing the issue to increased packet loss rates on specific network paths during peak periods. The entire diagnostic process is reduced from hours to minutes.

Each system environment is unique, and predefined workflows cannot cover all scenarios. Therefore, APO has built a **workflow orchestration platform** specifically for observability. Based on the popular and user-friendly AI tool Dify, APO provides a series of **data query and anomaly detection tools** that can be directly dragged and dropped, allowing anyone to easily turn their experience into workflows, laying the foundation for future autonomous agents.

For example, if you have your own "ten-step method for troubleshooting database performance issues," you can simply drag and drop these steps through an intuitive visual interface, add conditional logic like "if there are more than 10 slow queries, collect index usage information," and let the large language model decide the next steps based on the actual situation. This transforms your expertise into automated workflows.

APO's predefined workflows are built on this platform, and each workflow can be modified according to actual scenarios, truly integrating workflows into your operational processes.

With these workflows, your team no longer panics when key members take vacation, because their experience has been encoded as workflows, becoming part of APO's intelligent agents, serving the team 24/7. As workflows accumulate and improve, you're essentially training an AI assistant that increasingly understands your systems, becoming the most reliable "member" of your team.

## Data Foundation: Providing Solid Support for Intelligent Agents

The core capabilities of **Agentic workflows** depend on effective data utilization. Currently, observability data primarily includes metrics, logs, and traces, with emerging data like continuous profiling flame graphs becoming mainstream. However, this data is diverse and massive; directly inputting it to general-purpose LLMs is inefficient and limited by context windows. Therefore, APO has designed a data plane specifically optimized for LLMs, built from two dimensions:

+ **Horizontal View**: Like an intelligent map, it intuitively displays the service relationships and health status of the entire system. For example, when the order service experiences delays, you can immediately see which dependent service is causing problems—the payment service response time is abnormal, marked in red.

+ **Vertical Correlation**: The system automatically correlates data from different levels. When you click on the abnormal payment service, you can clearly see which container and node it's running on, what resources it's using, what log anomalies exist, and which API calls are timing out—all information available at your fingertips without switching between multiple systems.

The horizontal design of this data foundation allows AI Agents to quickly grasp the system overview and immediately locate abnormal components. The vertical design enables them to dig deeper along problem clues, tracing back to root causes. For example, when service latency is detected, AI Agents can use the horizontal view to identify the problematic service, then use vertical data to trace the specific cause, analyzing layer by layer from container to host, from application to middleware, until reaching the core of the problem.

APO provides built-in collection tools that can build this data plane through **one-click installation and deployment**. For users already using other collection tools, APO also **supports seamless integration with existing data sources**, allowing users to use this revolutionary data plane without modifying their systems. This innovative design provides intelligent agents with an efficient, structured data foundation, enabling AI to truly understand system behavior and make precise decisions.

![data-integration](/img/data-integration.png)
## Ecosystem Building: Enabling Continuous Agent Evolution

After integrating large language models, data foundations, and specialized knowledge, APO has taken a critical step toward realizing its vision. APO understands that richer system observability leads to more capable intelligent agents. However, existing data often cannot cover all blind spots, and predefined workflows struggle to address complex operational scenarios. Therefore, APO has created an open collaborative ecosystem focused on two core areas—**data ecosystem** and **workflow ecosystem**—helping AI Agents adapt to diverse environments and continuously evolve.

### Data Ecosystem: Multi-dimensional Data Collaboration

As an open-source project, APO has built an open platform. Troubleshooting requires multidimensional data, and this platform supports vendors and experts in seamlessly providing various new data types, enriching the observability resources available to intelligent agents. This data is integrated into **natural language query tools**, providing powerful support for intelligent agents in troubleshooting and performance optimization.

For proprietary data, vendors and experts can embed their domain knowledge into specific workflows. When users use these workflows, they can directly experience the value of the data without additional learning or experience accumulation. For example, vendor Cloud Observer provides Polaris metrics based on eBPF implementation and troubleshooting workflows, while the Dragonfly Community System Operations SIG provides SysOM's CPU & GPU continuous profiling large-scale data and AI flame graph analysis.

### Agentic Workflow Ecosystem: Collective Intelligence Sharing

APO's Agentic workflow ecosystem facilitates the gathering of collective wisdom:

+ **Essence of Workflows**: Experts' unique wisdom and experience, transformed into repeatedly executable intelligent agents through workflows and LLM nodes
+ **Experience Sharing**: Experts can share their wisdom and experience for others to use, more effectively than through blog posts
+ **Collective Wisdom Approaching Autonomous AI Agents**: As more workflows cover different scenarios, fully autonomous intelligent agents become increasingly achievable

Through sharing and collaboration, every user can benefit from the most advanced observability intelligent agents.

## Creating an Intelligent Operations Future Together

APO is committed to completely revolutionizing the observability field through AI power and community capabilities. We firmly believe that open collaboration will accelerate the realization of intelligent agents, allowing operations work to completely break free from tedious inefficiency and enter a new intelligent era. Whether you're a data provider, operations expert, or technology enthusiast, APO invites you to join this transformation and shape the future of observability intelligent agents together!

Start by experiencing APO's Agentic workflows and take your first step toward intelligent operations today!