# MOSN 架构

MOSN 的组成由Net/IO、Protocol、Stream、Proxy 四层组成，具体请参见
[architecture](architecture.md)

## MOSN 工作流程

Following pic shows how mosn works as a sidecar; You can assign the protocol used for downstream and upstream
![WorkFlow](./resource/MosnWorkFlow.png)

## MOSN 模块划分

Following pic shows the modules forming mosn, we will describe each part in detail [here](MosnModulesDescribe.md)
![modules](./resource/MosnModules.png)

## MOSN 数据流转

As described above, data flow transfered in follow pic
![DataFLow](./resource/MosnDataFlow.png)
